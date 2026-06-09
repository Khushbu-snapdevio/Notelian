# Background Jobs & Queues

Notelian runs all asynchronous work through **pg-boss**, a job queue that lives
inside the same PostgreSQL database as the application data. There is no Redis
and no separate message broker — one database is the single source of truth for
both app state and the queue.

This is the **one place** that lists every job the system runs. The job names
here are the canonical ones, drawn from the feature specs under
[../../Features/](../../Features/) and mirrored in
[GETTING-STARTED.md § 9](../../GETTING-STARTED.md). When you add, rename, or
reschedule a job, define it in its **feature spec first**, then update this
catalog and GETTING-STARTED so all three stay in sync (CLAUDE.md Rule 1).

> **Modeled on Krova.** Krova (a VM-hosting platform) runs dozens of pg-boss jobs
> for cube provisioning, snapshots, billing, and server lifecycle. Notelian is a
> document workspace, so almost none of those infra jobs apply. What we *did*
> adopt is Krova's **discipline**: a single catalog, `policy: "exclusive"` on
> every cron job, at-least-once delivery with idempotent handlers, and a
> retry/expiry budget declared per queue. The list below is Notelian's own.

---

## Why a job queue at all

Some work must not happen inside the HTTP request that triggers it — it is too
slow, must retry on failure, or must run on a schedule with no user present:

- **Slow / external work** — sending email, talking to S3, cascading deletes.
- **Scheduled work** — daily digests, nightly trash auto-delete, storage sweeps.
- **Retryable work** — anything that hits an external service (SMTP, S3) and can
  fail transiently.

The web process stays fast by **enqueuing a job and returning immediately**; the
worker process does the slow part.

---

## Two-process model

Notelian runs as **two processes against one database** (see
[backend-overview.md](backend-overview.md) for the full picture):

| Process | Command | Responsibility |
| --- | --- | --- |
| **Web** | `pnpm dev` / `next start` | Serves the UI + API; **enqueues** jobs, never runs slow work inline |
| **Worker** | `pnpm worker` | Boots pg-boss, registers handlers, runs jobs (scheduled + on-demand) |

The worker is a separate deployable. It is safe to scale to multiple replicas —
pg-boss claims each job with Postgres `FOR UPDATE SKIP LOCKED`, so two replicas
can never run the same job.

---

## Job catalog

Conventions: **Trigger** is `On demand` (enqueued by app code) or a cadence
(registered with `boss.schedule()`). All scheduled jobs use `policy: "exclusive"`
so a slow run can never overlap the next tick (see
[Idempotency & safety](#idempotency--safety)). The **Source** column points to
the feature spec that owns the job's behavior.

### File Storage — [file-storage.md](../../Features/file-storage.md)

| Job | Trigger | Purpose |
| --- | --- | --- |
| `cleanup-stale-uploads` | Every 30 min | Delete storage objects for uploads that were signed but never confirmed |
| `cleanup-orphaned-media` | Daily | Delete stored files no longer referenced by any active block — or by a page version within the 7-day window (preserves undo / Version History) |
| `sync-storage-usage` | Daily | Reconcile each workspace's `bytes_used` against the actual storage objects |

### Auth — [authentication.md](../../Features/authentication.md)

| Job | Trigger | Purpose |
| --- | --- | --- |
| `delete-user-private-pages` | On account-deletion confirm | Purge a deleted user's private pages and their files |

> Session and magic-link **token** expiry are handled by Better Auth's own
> lifecycle, not a custom pg-boss job — don't invent one. (See
> [docs/security.md](../security.md).)

### Pages — [pages.md](../../Features/pages.md)

| Job | Trigger | Purpose |
| --- | --- | --- |
| `auto-delete-expired-trash` | Daily 02:00 UTC | Permanently remove trashed pages past the 30-day retention (cascades to subpages + files) |
| `warn-expiring-trash` | Daily 02:00 UTC | Flag pages ≤7 days from auto-deletion for the warning banner; at 3 days send a one-time in-app + email warning to the page's deleter and creator |
| `auto-delete-expired-versions` | Daily | Prune page versions outside the retention window |

### Notifications — [notifications.md](../../Features/notifications.md)

| Job | Trigger | Purpose |
| --- | --- | --- |
| `send-notification-email` | On event (realtime frequency) | Deliver a per-event notification email (mention, comment reply, access granted) via Nodemailer/SMTP; retries up to 3× with exponential backoff (1 min → 5 min → 25 min) |
| `send-email-digest` | Daily 08:00 (user TZ) / weekly | Send the daily or weekly digest of unread notifications; skips users with nothing unread |
| `cleanup-old-notifications` | Nightly | Permanently delete notification rows older than 90 days |

---

## Scheduled jobs at a glance

| Job | Cadence |
| --- | --- |
| `cleanup-stale-uploads` | Every 30 min |
| `cleanup-orphaned-media` | Daily |
| `sync-storage-usage` | Daily |
| `auto-delete-expired-trash` | Daily 02:00 UTC |
| `warn-expiring-trash` | Daily 02:00 UTC |
| `auto-delete-expired-versions` | Daily |
| `send-email-digest` | Daily 08:00 (user TZ) / weekly |
| `cleanup-old-notifications` | Nightly |

On-demand (not scheduled): `delete-user-private-pages`, `send-notification-email`.
All scheduled jobs use `policy: "exclusive"`.

---

## How jobs are registered

Job definitions live in `lib/jobs/` (notification triggers in
`lib/notifications/`), per the repo structure in
[development-plan.md](../../Features/development-plan.md). The intended layout:

```
lib/jobs/
├── job-names.ts        # const map of every queue name (single source of truth)
├── register.ts         # boss.work() handler registration + boss.schedule() crons
├── enqueue.ts          # typed enqueue helpers used by web + worker
└── handlers/           # one file per job — the actual business logic
worker/
└── index.ts            # `pnpm worker` entry point: start pg-boss → register → drain on SIGTERM
```

- **Names** are declared once in `job-names.ts` and imported everywhere — no
  string literals scattered across the codebase.
- **Enqueue** from app code via a typed helper, never by hand-building payloads.
- **Schedules** are registered at worker boot, e.g.
  `await boss.schedule(JOB.AUTO_DELETE_EXPIRED_TRASH, "0 2 * * *")`.
- Each queue declares its retry/expiry budget when its handler is registered
  (`retryLimit`, `retryDelay`, `expireInSeconds`, `policy`).

This mirrors the **Job Registry** piece in
[backend-overview.md](backend-overview.md#registry-pieces): one definition per
job, so this catalog reflects code rather than drifting from it.

---

## Idempotency & safety

pg-boss guarantees **at-least-once** delivery, not exactly-once. A job can run
twice (retry after a worker crash, a duplicate enqueue). Every handler must be
safe to run more than once. The patterns Notelian uses:

1. **Atomic status transition** — flip a status-bearing row and act only if the
   flip succeeded (`UPDATE … WHERE status = 'queued' RETURNING id` → 0 rows means
   another run already took it; exit).
2. **Transactional enqueue** — a notification job is only created inside the same
   transaction that saved the triggering comment/mention. If the save rolls back,
   the job never exists (a rule in
   [notifications.md](../../Features/notifications.md)).
3. **Check-then-act for external side effects** — deleting an S3 object that is
   already gone is a no-op; re-sending an email keys off an idempotency marker so
   a retry can't double-send.
4. **`policy: "exclusive"` on every cron job** — pg-boss inserts each tick with a
   per-queue singleton key, so if a run takes longer than its interval the next
   tick is rejected at enqueue rather than running in parallel. Idempotent
   handlers self-heal on the next interval — an occasional skipped tick is
   acceptable; parallel execution is not.

### Retry policy

| Concern | Default |
| --- | --- |
| `retryLimit` | 3 for delivery jobs (email), 5 for cleanup/cron |
| `retryDelay` | Exponential backoff (notifications: 1 min → 5 min → 25 min, per [notifications.md](../../Features/notifications.md)) |
| Exhausted retries | Logged to error monitoring |
| `localConcurrency` | `1` per queue per replica — scale out with more replicas, not higher concurrency |

---

## What is **not** a background job

To keep the boundary clear:

- **Real-time notification delivery** is **Server-Sent Events (SSE)**, not a job —
  the in-app toast/badge is pushed over `GET /api/notifications/stream`. pg-boss
  only handles the **email** side and cleanup. (See
  [notifications.md](../../Features/notifications.md) and Phase-1 decision #6 in
  the [README](../../README.md).)
- **Search indexing** is maintained by **PostgreSQL triggers** on
  block-content / property-value / comment changes — there is no reindex job.
  Bulk operations (import, mass edit) must batch their writes so the trigger
  isn't fired row-by-row thousands of times in one transaction.
- **Permission resolution** is a synchronous recursive CTE in the request path,
  never deferred to a job.
- **Session / magic-link token expiry** is handled by Better Auth, not a job.

---

*Last updated: 2026-06-09*
