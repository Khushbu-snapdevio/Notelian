# Security Model

Notelian's security model, consolidated from the feature specs. Read this before working on anything touching auth, permissions, sharing, or file uploads. As the surface grows this file can split into a `docs/security/` folder (per-topic), as krova does.

The single most important principle: **access control is enforced in the database query, never in application code after a broad fetch.** That is [Phase-1 decision #5](../README.md#phase-1-architecture-decisions) and Rule 3 in [CLAUDE.md](../CLAUDE.md#rules).

## Authentication

- **Passwordless, magic-link only** (Better Auth). No passwords, no OAuth/social in the MVP — fewer credential-handling surfaces to secure.
- **Magic-link tokens are short-lived and single-use.** A token is deleted the moment it's consumed; expired/unused token expiry is handled by Better Auth's own lifecycle. A delivered link that's already been used must fail closed.

- **Sessions are database-backed** and revocable. Users can list active devices and revoke any session; expired-session cleanup is handled by Better Auth, not a custom job.
- **The Better Auth ↔ schema column mapping is locked once** in `lib/auth/` (see the mapping note in [DATABASE-PLAN.md](../DATABASE-PLAN.md)). Never rename auth columns after sessions/accounts exist.

## Authorization (the core)

Two layers: **workspace role** (Admin / Editor / Viewer) and **page-level permission** (Full Access / Can Edit / Can Comment / Can View).

- **Effective permission is resolved by a single recursive CTE** that walks `parent_id` up to the first explicit `page_permissions` row, then falls back to `workspaces.default_page_access`. One query — never an N+1 walk in application code (Phase-1 decision #3).
- **Private-page short-circuit.** When `pages.is_private = true`, inheritance and the workspace default are skipped — only the page creator and explicit grants apply, **and workspace Admins are denied too.** Honor this in the resolver, not as a special case sprinkled across callers. The one exception: **Platform Admins** (`is_platform_admin = true`) can see private page **titles only** (never content) via Orbit Admin (`/orbit`) for compliance and moderation — this is enforced by a separate Orbit-specific query that returns only `pages.title` and never exposes `blocks` or `property_values`. This capability is not available to workspace Admins through any in-product path.
- **Filtering happens in SQL.** Every list/search/tree query joins the permission resolution so restricted rows never leave the database. Fetching broadly and filtering in JS is a **BOLA (Broken Object-Level Authorization)** vulnerability — the restricted rows have already left the trust boundary.
- **Use the shared auth helpers** in the documented order: `requireSession` → `requireWorkspaceMember` → `requirePagePermission`. Never reimplement a permission check inline (Rule 3).

## Sharing & external access

- **Public links** grant `can_view` or `can_comment` with **no login required.** The public render path must expose only the shared page and its permitted descendants — re-resolve permissions per request; never trust a client-supplied page id alone.
- **Guest access** invites an external email to specific pages. Guests are scoped to exactly the pages they're granted — they are not workspace members and must never see the workspace tree, search across it, or other pages.
- **Subpage inheritance with per-page override** — a child inherits the parent's sharing unless it has its own explicit grant. The override must be evaluated by the same resolver, not bolted on.

## File uploads

- **Pre-signed direct-to-S3 PUT URLs.** Files upload straight to the bucket; bytes never transit the app server.
- **Validate before issuing the URL** — check the authenticated user's permission on the target, the declared content type, and the per-type size limit (page cover 5 MB, icons/avatars 1 MB, image 10 MB, video/audio 50 MB, file 100 MB) **server-side** before signing. A signed URL is a capability; only mint it for a validated request.
- **Serve via CDN**; treat user-uploaded files as untrusted content (correct `Content-Type`, no inline execution of arbitrary types).
- **Orphan sweep** — the `cleanup-orphaned-media` job deletes objects no longer referenced by any block/page (and `cleanup-stale-uploads` removes never-confirmed uploads) so abandoned uploads can't accumulate.

## Notifications & real-time

- **The SSE stream is per-user.** `GET /api/notifications/stream` must authenticate the session and only ever emit the requesting user's own notifications.
- **Notification enqueue is transactional** — only inside the transaction that saved the triggering event, so a rolled-back action never produces a phantom notification.

## Platform admin (Orbit)

- **Orbit is not reachable by end users** — gate every `/orbit` route and API on `users.is_platform_admin`.
- **Every Orbit mutation writes an append-only `platform_audit_log` row** (actor, action, target, metadata). Admins cannot escalate or modify their own admin status through the user-edit path.

## Forward-looking (post-MVP, document now so it's not forgotten)

- **Outbound webhooks (Phase 3)** — sign payloads (HMAC), guard against **SSRF** by re-resolving the destination URL on every delivery and blocking private/loopback/link-local ranges, and auto-disable flapping endpoints.
- **Public API + API keys (Phase 3)** — scoped keys, the same SQL-level permission enforcement as the web app.

---

*Last updated: 2026-06-09*
