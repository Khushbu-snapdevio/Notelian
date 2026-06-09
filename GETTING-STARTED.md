# Notelian — Getting Started & Project Blueprint

> **One-file reference.** Everything you need to start development: the full project inventory (every feature and every option included), the complete tech stack, all required credentials, and the local setup steps.
>
> For deep per-feature specs, see the [Features/](Features/) folder. For phases and architecture decisions, see [development-plan.md](Features/development-plan.md).

---

## 0. What Notelian Is

A Notion-style all-in-one workspace: notes, documents, databases, and project management in one place. **Everything is a block**, and **every database entry is itself a full page**.

```
Workspace
  └── Page
        ├── Blocks (text, media, tasks, code, etc.)
        └── Subpages
              └── Database
                    └── Entry (is itself a full Page)
```

---

## 1. Prerequisites (install before you start)

| Tool | Version | Why |
|------|---------|-----|
| Node.js | v20+ | Runs Next.js |
| PostgreSQL | v16+ | Primary data store, full-text search, AND job queue (pg-boss) |
| pnpm | latest | Package manager |
| Git | any | Version control |
| Headless Chromium | bundled | PDF export renders via **Puppeteer** (see §4). **pnpm v10+ blocks dependency build scripts by default**, so Puppeteer's Chromium download is skipped until you approve it (`pnpm approve-builds`, or add `puppeteer` to `onlyBuiltDependencies` in `package.json`). On Linux you also need its system libraries (`libnss3`, `libatk`, `libgbm`, etc. — `pnpm exec puppeteer browsers install chrome` reports what's missing) |

You do **not** need Redis, Elasticsearch, or a separate message broker — PostgreSQL covers search (tsvector) and the job queue (pg-boss).

> **Deployment note:** PDF export needs a real Chromium process. On serverless platforms (e.g. Vercel) the bundled Chromium does not fit the function size limit — use `@sparticuz/chromium` + `puppeteer-core`, or run export on a long-lived server (Railway). Decide this before building the export feature.

---

## 2. Credentials & Accounts to Obtain Before Coding

Set these up first — development is blocked without them. Each maps to one or more environment variables (Section 3).

| # | Credential / Account | Where to get it | Used for |
|---|----------------------|-----------------|----------|
| 1 | **PostgreSQL database** | Local install, or managed: Supabase / Neon / Railway | App data, search, job queue |
| 2 | **Better Auth secret** | Generate yourself: `openssl rand -base64 32` | Signing sessions |
| 3 | **S3-compatible storage bucket** | Any S3-compatible object storage provider ([MinIO](https://min.io) for local dev) → create a bucket | Store uploaded files |
| 4 | **Storage access keys** | In your provider's console, create an access key + secret with put/get/head/delete on the bucket | App writes/reads/deletes objects |
| 5 | **CDN distribution** | A CDN in front of the bucket → note the public base URL | Serve files from the edge |
| 6 | **SMTP provider** | e.g. SendGrid, Amazon SES, Mailgun, Postmark → host, port, username, password | Magic-link sign-in & digest emails (via Nodemailer) |
| 7 | **GeoIP source** *(optional)* | [MaxMind GeoLite2](https://www.maxmind.com) license key, or skip | Approximate device location in the session list ([authentication.md](Features/authentication.md) §2) — best-effort; the feature degrades gracefully without it |

> **Tip:** Items 3–5 (storage bucket + access keys + CDN) all come from one S3-compatible storage provider — the bucket, an access key/secret pair, and the public base URL of a CDN in front of it. For local dev you can run [MinIO](https://min.io) and skip the CDN entirely. Item 6 is any SMTP-capable mail provider.
>
> **Local email tip:** You don't need a real SMTP provider to develop. Point the SMTP variables at a local catcher like [Mailpit](https://mailpit.axllent.org) or an [Ethereal](https://ethereal.email) test inbox to see verification, reset, invite, and digest emails without sending anything externally.


### ⭐ What to request from your admin : 

> [!IMPORTANT]
> **These 3 are the only credentials that must come from outside** — request them from whoever owns the project's cloud accounts. Everything else you set up yourself locally.
>
> | ✅ Request | Env var(s) | Notes |
> |-----------|-----------|-------|
> | **🗄️ S3-compatible storage** — endpoint, region, bucket, access key + secret | `S3_ENDPOINT`, `S3_BUCKET`, `S3_REGION`, `S3_ACCESS_KEY_ID`, `S3_SECRET_ACCESS_KEY` | Any S3-compatible provider — admin's choice |
> | **🌐 CDN URL** — public base URL in front of the bucket | `CDN_URL` | Comes with the storage provider |
> | **✉️ SMTP** — host, port, username, password | `SMTP_HOST`, `SMTP_PORT`, `SMTP_USER`, `SMTP_PASSWORD` | Powers the **magic-link sign-in** and digest emails |

> [!TIP]
> **Handle yourself — no need to ask:** PostgreSQL (run locally → `DATABASE_URL`) · Better Auth secret (`openssl rand -base64 32` → `BETTER_AUTH_SECRET`) · storage & email for local dev ([MinIO](https://min.io) + [Mailpit](https://mailpit.axllent.org)) · the optional MaxMind key.



---

## 3. Environment Variables (`.env.local`)

Copy `.env.example` → `.env.local` and fill these in.

```bash
# --- Database ---
DATABASE_URL=postgresql://user:password@localhost:5432/notelian

# --- Auth (Better Auth) ---
BETTER_AUTH_SECRET=<openssl rand -base64 32>
BETTER_AUTH_URL=http://localhost:3000      # base URL Better Auth builds magic-link URLs against

# --- File Storage (S3-compatible) ---
S3_ENDPOINT=                            # leave blank for the region's default S3 endpoint; set a full URL for a custom S3-compatible endpoint
S3_BUCKET=notelian-uploads
S3_REGION=us-east-1                      # use `auto` if your S3-compatible provider requires it
S3_ACCESS_KEY_ID=<access key id>
S3_SECRET_ACCESS_KEY=<secret access key>
CDN_URL=https://cdn.notelian.app        # CDN base URL in front of the bucket

# --- Email (Nodemailer / SMTP) ---
EMAIL_FROM=noreply@notelian.app
SMTP_HOST=smtp.sendgrid.net
SMTP_PORT=587                            # 587 = STARTTLS, 465 = TLS
SMTP_USER=<smtp username>
SMTP_PASSWORD=<smtp password>
SMTP_SECURE=false                        # true for port 465, false for 587

# --- App ---
NEXT_PUBLIC_APP_URL=http://localhost:3000

# --- Optional ---
MAXMIND_LICENSE_KEY=<optional — session-list geolocation>
```

| Variable | Description |
|----------|-------------|
| `DATABASE_URL` | PostgreSQL connection string |
| `BETTER_AUTH_SECRET` | Secret for Better Auth session signing |
| `BETTER_AUTH_URL` | Base URL Better Auth uses to build magic-link URLs (matches `NEXT_PUBLIC_APP_URL`) |
| `S3_ENDPOINT` | S3-compatible endpoint — leave blank for the region default, or set a custom endpoint URL |
| `S3_BUCKET` / `S3_REGION` | Storage bucket + region |
| `S3_ACCESS_KEY_ID` / `S3_SECRET_ACCESS_KEY` | Storage programmatic credentials |
| `CDN_URL` | CDN base URL used to build public file URLs |
| `EMAIL_FROM` | Sender address |
| `SMTP_HOST` / `SMTP_PORT` / `SMTP_USER` / `SMTP_PASSWORD` / `SMTP_SECURE` | SMTP delivery |
| `NEXT_PUBLIC_APP_URL` | Public base URL (used in links/emails) |
| `MAXMIND_LICENSE_KEY` | *Optional* — GeoIP lookup for the session list; omit to disable location display |

---

## 4. Tech Stack

| Layer | Technology | Notes |
|-------|-----------|-------|
| Framework | **Next.js (App Router)** v15+ | Server components, server actions, API routes |
| Language | **TypeScript** | Strict mode |
| Database | **PostgreSQL** v16+ | Data store + full-text search + job queue |
| ORM | **Drizzle ORM** | Type-safe, schema-as-code, explicit migrations |
| Auth | **Better Auth** + Magic Link Plugin + Admin Plugin | Database-backed sessions, passwordless magic-link sign-in, ban/impersonate |
| Editor | **TipTap** (ProseMirror) | Block-based rich text |
| Job Queue | **pg-boss** | Notifications, email digests, cleanup (runs in a worker — see §8) |
| Real-time | **Server-Sent Events (SSE)** | In-app notification push — native, no extra library |
| File Storage | **S3-compatible** object storage + **CDN** | Pre-signed direct uploads |
| Email | **Nodemailer** (SMTP) | Transactional email |
| Styling | **Tailwind CSS** | Utility-first |
| Math | **KaTeX** | LaTeX equation blocks |
| PDF Export | **Puppeteer** (headless Chromium) | Server-side render of pages to PDF |
| Code blocks | **Shiki / lowlight** | Syntax highlighting (50+ languages) |
| Emoji picker | **emoji-mart** (or similar) | Page/workspace icon emoji selection |
| Offline queue | **IndexedDB** (via `idb`) | Editor offline edit buffer + auto-sync |
| Dates | **date-fns** + **chrono-node** | Formatting/timezones + `@date` natural-language parsing |
| Short IDs | **nanoid** | 7-char public page IDs (`short_id`) |
| Admin | **Orbit Admin** | Custom internal ops dashboard (`/orbit`) |
| Testing | **Vitest** + **Playwright** | Unit/integration + E2E |
| Deployment | **Vercel / Railway** + managed PostgreSQL | Hosting |

> Library choices marked "or similar" (emoji picker, syntax highlighter) are recommendations — swap freely, but lock the choice before building that feature.

---

## 5. Complete Feature Inventory (everything included in the MVP)

### 5.1 Authentication — *Better Auth + Magic Link Plugin + Admin Plugin*
- **Passwordless only** — magic link is the sole sign-in / sign-up method (no passwords, no OAuth / social)
- Magic link sign in / sign up (token valid **15 minutes**, single-use)
- A successful magic-link sign-in marks the email verified — clicking the link proves ownership, so there is no separate verification step
- Sign out
- Database-backed sessions; multi-device session list + revocation
- Admin plugin: ban/unban users, impersonate users

### 5.2 Workspace
- Create / edit / delete workspace; switch between multiple
- Invite members by email or invite link (`/invite/:token`)
- Manage members & roles; transfer ownership; settings page
- **Roles:** `Admin` (full control) · `Editor` (create/edit) · `Viewer` (read-only)

### 5.3 Onboarding
1. Setup Wizard — Profile → Create/Join workspace → Invite teammates → Choose template
2. Tooltip Tour — 5-step UI walkthrough
3. Contextual Hints — one-time non-blocking in-app hints

### 5.4 Navigation
- Collapsible, resizable sidebar
- Hierarchical page tree with drag-and-drop reordering
- Favorites (per-user) · Workspace switcher · Recently visited (last 10)
- Trash (30-day retention) · Sidebar filter by name

### 5.5 Pages
- Page icon (emoji or image) + cover banner
- Unlimited subpage nesting · breadcrumbs
- Move / duplicate / delete / restore from trash
- Layout options: full-width toggle · small text · font family (default / serif / mono)
- Favorites · page lock · version history
- Export to **Markdown, PDF, HTML** · public link sharing

### 5.6 Editor (block-based — every element is a block)

| Category | Blocks |
|----------|--------|
| Text | Paragraph, H1, H2, H3, Bulleted List, Numbered List, Toggle, Quote, Callout, Divider |
| Task | To-Do (checkbox) |
| Media | Image, Video, Audio, File |
| Structure | Table of Contents, Simple Table, Columns |
| Code & Math | Code Block (syntax highlighting), Equation (LaTeX / KaTeX) |
| Reference | Linked Page, Inline Database |

**Editor features:** `/` slash command · floating inline-format toolbar (bold, italic, code, link, comment, color) · Markdown shortcuts · block drag-and-drop · multi-block selection · continuous auto-save with offline queue · 200-step undo history.

### 5.7 Templates
- **Built-in** library by category: Personal, Productivity, Project Management, Team & Knowledge, Personal CRM
- **Custom** — save any page as a workspace-scoped template
- **Template Button block** — clones a predefined block structure on click

### 5.8 Databases (each entry is a full page)

**View types (all 4 in MVP):**

| View | Description |
|------|-------------|
| **Table** | Spreadsheet-style rows & columns |
| **Board** | Kanban columns grouped by a Select property |
| **Calendar** | Entries placed by a Date property |
| **Gallery** | Visual card grid with cover images |

- Full-page or inline (embedded) database
- Multiple named views per database (shared data)
- Filters with AND/OR logic, persistent per view
- Up to **5 stacked sort rules**
- Grouping by Select property

### 5.9 Database Properties

**Typed columns (all 11 types in MVP):**

| Type | Description |
|------|-------------|
| Text | Plain text |
| Number | Formats: plain, currency, %, scientific |
| Select | Single color-coded option |
| Multi-Select | Multiple color-coded tags |
| Date | Optional time + date range |
| Checkbox | Boolean toggle |
| URL | Clickable hyperlink |
| Email | Clickable mailto link |
| Phone | Clickable tel link |
| Person | Workspace members (`@me` dynamic default) |
| Relation | Bidirectional link to another database |

**System properties (auto-generated):** Created Time, Last Edited Time, Created By, Last Edited By.

### 5.10 Search — *PostgreSQL full-text search (`tsvector`/`tsquery`)*
- `Ctrl+K` / `Cmd+K` to open
- Searches page titles, content, database entries, property values, comments
- Permission-filtered (private pages never surface for others)
- Filters: location, content type, date range, author
- Shows recently visited before typing

### 5.11 Comments & Mentions
- **Comments:** block-level (threaded one level), text-range, page-level; resolve/reopen
- **Mentions:** `@name` (member → notification) · `@page` (live link, auto-updates on rename) · `@date` (formatted clickable date)

### 5.12 Permissions & Sharing (two layers: workspace role + page permission)

| Page permission | Can do |
|-----------------|--------|
| Full Access | Read, edit, manage permissions, share externally |
| Can Edit | Read, edit, comment |
| Can Comment | Read, comment |
| Can View | Read only |

- Workspace role is the **ceiling**; page permissions restrict but never expand it (a Viewer is read-only on every page)
- Public link (no login) · guest invite by email · subpage inheritance with per-page override

### 5.13 Notifications
- **In-app:** bell + unread badge; real-time via **Server-Sent Events (SSE)**; toast auto-dismisses 5s. Triggers: @mentions, comment replies, thread resolutions, page access granted, workspace invites, trash auto-deletion warnings
- **Email:** Real-time / Daily digest (default) / Weekly digest / Off; timezone-aware
- **Retention:** 90 days · powered by **pg-boss**

### 5.14 Orbit Admin (internal platform-team tool, not for end users — `/orbit`)
- User management (view, ban/unban, impersonate, revoke sessions)
- Workspace management (view all, members)
- Built-in template management (`/orbit/templates`)
- Platform analytics · append-only audit trail

### 5.15 File Storage — *where uploads go*
- **Stored in:** S3-compatible object storage · **Served via:** a CDN in front of the bucket
- Covers: page cover images, page icons, **user avatars, workspace icons**, and all media blocks (Image, Video, Audio, File)
- **Workspace storage quota** is **5 GB per workspace**, tracked per workspace (`WorkspaceStorageUsage`) and enforced before each upload; the bar turns amber at 90% (4.5 GB) and blocks uploads at 100% (5 GB).
- **Upload flow:** pre-signed PUT URLs — client uploads **directly to the storage bucket**, never through the app server
  - `POST /api/uploads/sign` → get signed URL + objectKey
  - `PUT {uploadUrl}` → upload bytes straight to the bucket
  - `POST /api/uploads/confirm` → verify object exists, record usage, return `fileUrl`
  - `DELETE /api/uploads/:objectKey` → on block delete
- Public URL pattern: `https://cdn.notelian.app/{objectKey}` (stable, never changes)

**Per-type size limits:**

| Type | Max size |
|------|---------|
| Page cover image | 5 MB |
| Page icon | 1 MB |
| User avatar | 1 MB |
| Workspace icon | 1 MB |
| Image block | 10 MB |
| Video / Audio block | 50 MB |
| File block | 100 MB |

---

## 6. Database Schema Overview

```
Core:           users, sessions, accounts, verifications
                workspaces, workspace_members
                pages (covers both pages AND database entries)
                page_closure, page_versions, blocks (content stored as JSONB)

Database:       database_views, database_properties, property_values
                (a database is a "pages" row where kind='database' —
                 there is no separate "databases" table)

Sharing:        page_permissions, public_links, guest_invitations

Files:          file_uploads, workspace_storage_usage

Search:         search_index (denormalized FTS rows for pages, entries,
                comments; tsvector + GIN)

Templates:      templates

Collaboration:  comments, notifications, notification_preferences

User prefs:     user_preferences, user_hint_states,
                user_favorites, user_recently_visited

Admin:          platform_audit_log
```

> This overview lists the main tables by area; some columns and helper tables are defined in the per-feature specs under [Features/](Features/).

**Key decisions:** block content as JSONB (schema-less per block type) · search via a dedicated `search_index` table (`search_vector` + GIN) kept current on save · PostgreSQL for both search and the job queue to avoid extra infra.

---

## 7. Repository Structure

```
notelian/
├── app/                        # Next.js App Router
│   ├── (auth)/                 # Sign in / sign up (magic link), magic-link verify
│   ├── (app)/[workspace]/      # Main workspace UI
│   │   ├── [pageId]/           # Page editor
│   │   └── settings/           # Workspace settings
│   ├── orbit/                  # Orbit Admin (platform team only)
│   └── api/                    # API route handlers
├── components/
│   ├── editor/                 # TipTap editor + block components
│   ├── database/               # Table, Board, Calendar, Gallery views
│   └── ui/                     # Design system primitives
├── lib/
│   ├── auth/                   # Better Auth config
│   ├── db/
│   │   ├── schema/             # Drizzle schema — one file per domain + types.ts + index.ts barrel
│   │   ├── index.ts            # Drizzle client
│   │   └── queries/            # Reusable query helpers
│   ├── jobs/                   # pg-boss job definitions + registration
│   ├── notifications/          # Notification triggers
│   └── storage/                # S3 pre-signed URL helpers
├── worker/                     # pg-boss worker entry point (pnpm worker)
├── drizzle/                    # Migrations
└── public/                     # Static assets
```

---

## 8. Local Development Steps

```bash
# 1. Install dependencies
pnpm install
#    → run `pnpm approve-builds` so Puppeteer downloads Chromium (pnpm v10+ blocks build scripts by default)

# 2. Set up environment
cp .env.example .env.local
#    → fill in every variable from Section 3

# 3. Generate + apply database migrations
pnpm db:generate          # build migration SQL from the Drizzle schema
pnpm db:migrate           # apply migrations to the database

# 4. Start the dev server
pnpm dev                  # → http://localhost:3000

# 5. Start the background-job worker (separate terminal)
pnpm worker               # pg-boss — notifications, email digests, cleanup jobs
```

> **Important:** `pnpm dev` alone does **not** process background jobs. pg-boss work (email digests, stale-upload cleanup, trash auto-delete, private-page deletion, version pruning) runs in the **worker** process. Run `pnpm worker` in a second terminal during development, or features that depend on jobs will appear to silently do nothing.

### Expected package scripts

| Script | Purpose |
|--------|---------|
| `dev` | Next.js dev server |
| `build` / `start` | Production build / serve |
| `worker` | pg-boss job worker (long-running) |
| `db:generate` | Generate migration SQL from the Drizzle schema |
| `db:migrate` | Apply pending migrations |
| `db:studio` | Open Drizzle Studio to inspect data |
| `lint` | ESLint / type-check |
| `test` | Vitest unit + integration |
| `test:e2e` | Playwright end-to-end |

### Dev bootstrap notes

- **Accessing Orbit Admin (`/orbit`):** there is no UI to grant platform-admin. To test Orbit locally, set `is_platform_admin = true` on your user row directly (`pnpm db:studio` or `UPDATE users SET is_platform_admin = true WHERE email = '...';`).
- **Built-in templates are empty on a fresh DB:** they are authored through Orbit Admin (`/orbit/templates`), not via seed scripts. The 18 launch templates won't exist locally until you create them — or until a seed script is added. Expect an empty template gallery on first run.
- **First page render:** a brand-new workspace has no content; you'll land on the empty-state, not an error.

**First-run checklist:**
- [ ] PostgreSQL v16+ running and reachable via `DATABASE_URL`
- [ ] `BETTER_AUTH_SECRET` generated + `BETTER_AUTH_URL` set
- [ ] Storage bucket + access keys + CDN URL ready (or MinIO running locally)
- [ ] SMTP provider (or local Mailpit) credentials in `.env.local` — required so magic-link sign-in emails send
- [ ] Migrations generated + applied (`db:generate` → `db:migrate`)
- [ ] Worker running (`pnpm worker`) so jobs process
- [ ] (To test Orbit) your user set to `is_platform_admin = true`

---

## 9. Background Jobs (pg-boss)

All async/scheduled work runs through pg-boss in the **worker** process (§8). Collected from across the feature specs so nothing is forgotten:

| Job | Schedule / Trigger | Source feature |
|-----|--------------------|----------------|
| `cleanup-stale-uploads` | Every 30 min | File Storage — delete storage objects for uploads never confirmed |
| `cleanup-orphaned-media` | Daily | File Storage — delete stored files no longer referenced by any active block or by a page version within the 7-day window (preserves undo / Version History) |
| `sync-storage-usage` | Daily | File Storage — reconcile `bytes_used` against actual storage objects |
| `delete-user-private-pages` | On account-deletion confirm | Auth — purge private pages + their files for a deleted user |
| `auto-delete-expired-trash` | Daily 02:00 UTC | Pages — permanently remove trashed pages past retention (cascades to subpages + files) |
| `warn-expiring-trash` | Daily 02:00 UTC | Pages — flag pages ≤7 days from auto-deletion for the warning banner; at 3 days send a one-time in-app + email warning to the page's deleter and creator |
| `auto-delete-expired-versions` | Daily | Pages — prune page versions outside the retention window |
| `send-email-digest` | Daily 08:00 (user TZ) / weekly | Notifications — send daily/weekly digest of unread items |
| `cleanup-old-notifications` | Nightly | Notifications — permanently delete notifications older than 90 days |
| `send-notification-email` | On event / scheduled | Notifications — deliver per-event emails; retries up to 3× with exponential backoff |

> Job definitions live in `lib/jobs/` (notification triggers in `lib/notifications/`) and must be registered on worker boot. There is no separate broker — the queue tables live in the same PostgreSQL database.

---

## 10. Limits (MVP)

Fixed limits to enforce in code, collected from across the feature specs:

| Limit | Value | Source |
|-------|-------|--------|
| Trash retention | 30 days | Pages, Navigation |
| Page version history | 7 days | Pages |
| Custom templates / workspace | 5 | Templates |
| Workspace storage quota | 5 GB per workspace | File Storage |
| Properties per database | 50 | Database Properties |
| Stacked sort rules per view | 5 | Databases |
| Recently visited | 10 recent (Favorites are uncapped) | Navigation |
| Undo history | 200 steps (per session) | Editor |
| Magic-link requests | **3 / 15 min per email · 10 / hour per IP** (throttled; same generic response when limited) | Auth |
| Magic-link token validity | 15 minutes, single-use | Auth |

---

## 11. Build Order (suggested sequence)

A pragmatic dependency-ordered path through the MVP:

1. **Foundation** — Next.js + TypeScript + Tailwind + Drizzle + PostgreSQL connection + **pg-boss worker harness** (stub the worker process early so jobs have a home)
2. **Auth** — Better Auth passwordless magic link, database-backed sessions
3. **Workspace + members + roles** — the container everything hangs off
4. **Navigation + Pages** — sidebar tree, page CRUD, icons/covers; **export (Markdown/HTML, then PDF via Puppeteer)** + version history
5. **Editor** — TipTap, all block types, slash command, auto-save + IndexedDB offline queue
6. **File Storage** — S3-compatible pre-signed uploads (needed by media blocks + covers)
7. **Databases + Properties + Views** — Table → Board → Calendar → Gallery
8. **Search** — PostgreSQL FTS + `search_vector` triggers
9. **Comments & Mentions**
10. **Permissions & Sharing** — page-level access, public links, guests
11. **Notifications** — SSE in-app + pg-boss email digests
12. **Templates** — built-in gallery + custom
13. **Orbit Admin** — user/workspace/template management
14. **Testing** — Vitest unit/integration + Playwright E2E

---

## 12. Testing & Deployment

| Layer | Tool | Coverage |
|-------|------|----------|
| Unit | Vitest | Business logic, utilities |
| Integration | Vitest + real PostgreSQL | API routes, queries |
| E2E | Playwright | Sign up, create page, invite member |

**Production:** Next.js on Vercel/Railway · managed PostgreSQL (Supabase/Neon/Railway) · S3-compatible object storage + CDN · Nodemailer SMTP. CI/CD: push to `main` → tests → deploy; feature branch → tests → preview.

> **SSE hosting note:** The notification SSE stream (`/api/notifications/stream`) needs a persistent-connection host (Railway / long-lived Node server) — Vercel serverless functions cap stream duration and drop the connection. The client auto-reconnects via `EventSource` and falls back to polling, so notifications still work on Vercel, just without a steady live stream. See [notifications.md](Features/notifications.md).

---

## 13. Excluded from MVP (don't build yet)

Real-time multiplayer (P2) · public REST API & webhooks (P3) · SSO/SAML (P3) · mobile apps (P3) · AI features (P4) · whiteboard (P4) · custom domains (P4) · Slack/GitHub/Zapier integrations & plugin marketplace (P5) · dark mode · localization.

---

*Single-file development reference for Notelian. Last updated: 2026-06-09.*
