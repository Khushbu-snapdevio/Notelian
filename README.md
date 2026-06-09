# Notelian — Structured Workspace for Small Teams

## Product Vision

Notelian is an opinionated, pre-structured workspace for **small teams (3–15 people) who tried Notion, found it overwhelming to set up, and just want to get working.** Instead of a blank canvas with infinite options, Notelian ships with the structure those teams actually need — a fast note editor, a shared wiki, a Kanban board, and good search — ready to use on day one.

**Who it's for:**
- A 3–15 person startup that needs a structured team wiki + lightweight project tracking
- Teams who want Notion's core value without its setup overhead or power-user complexity
- *Not* power users chasing formulas, rollups, or API-driven automation — those are deliberately out of scope

**Positioning (honest):** Notelian is not "Notion but faster." It is **Notion's core, pre-assembled** — a smaller, opinionated surface that trades configurability for time-to-value. The bet is on *fewer decisions*, not on out-engineering a 30M-user incumbent on speed.

**Goals:**
- Everything is a block — flexible, composable content
- Fast, distraction-free writing experience
- Powerful databases that feel like documents
- Opinionated defaults so a team is productive in minutes, not hours

---

## Product Structure

```
Workspace
  └── Page
        ├── Blocks (text, media, tasks, code, etc.)
        └── Subpages
              └── Database
                    └── Entry (is itself a full Page)
```

---

## Table of Contents

1. [Authentication](#1-authentication)
2. [Workspace](#2-workspace)
3. [Onboarding](#3-onboarding)
4. [Navigation](#4-navigation)
5. [Pages](#5-pages)
6. [Editor](#6-editor)
7. [Templates](#7-templates)
8. [Databases](#8-databases)
9. [Database Properties](#9-database-properties)
10. [Search](#10-search)
11. [Comments & Mentions](#11-comments--mentions)
12. [Permissions & Sharing](#12-permissions--sharing)
13. [Notifications](#13-notifications)
14. [Admin Panel](#14-admin-panel)
15. [File Storage](#15-file-storage)
16. [MVP Scope](#16-mvp-scope)
17. [Tech Stack](#17-tech-stack)

---

## 1. Authentication

User identity and session management.

**Powered by:** Better Auth with Magic Link Plugin + Admin Plugin

**Features:**
- Passwordless sign in / sign up via magic link (the only auth method — no passwords, no OAuth/social)
- Sign out
- Database-backed session management
- Multi-device session list and revocation
- Ban / unban users (via Admin Plugin)
- Impersonate users (via Admin Plugin)

> **Note:** Reconciled with [Features/authentication.md](Features/authentication.md), which defines auth as passwordless magic-link only. The product audit assumed email/password + Google OAuth — that assumption is now stale; the magic-link model supersedes it.

---

## 2. Workspace

Top-level organizational container. Every user belongs to at least one workspace.

**Example:** `Acme Inc`, `Personal`, `My Team`

**Features:**
- Create, edit, delete workspace
- Switch between multiple workspaces (workspace switcher)
- Invite members via email or invite link
- Manage members and roles
- Transfer ownership
- Workspace settings page

**Roles:**

| Role | Description |
|------|-------------|
| Admin | Full control — settings, members, all non-private content |
| Editor | Create and edit content; subject to page-level restrictions |
| Viewer | Read-only access to pages they have been granted access to |

---

## 3. Onboarding

Guided setup for new users.

**Stages:**
1. **Setup Wizard** — Profile → Create/Join Workspace → Invite teammates → Choose starting template
2. **Tooltip Tour** — 5-step walkthrough of key UI elements
3. **Contextual Hints** — One-time, non-blocking in-app hints as new features are encountered

---

## 4. Navigation

Sidebar and workspace navigation.

**Features:**
- Workspace switcher (multiple workspaces per account)
- Collapsible sidebar with resizable width
- Hierarchical page tree with drag-and-drop reordering
- Favorites section (personal per-user)
- Recently visited pages (last 10)
- Trash (30-day retention)
- Sidebar filter (filter visible pages by name)

---

## 5. Pages

Universal content container — everything lives on a page.

**Features:**
- Page icon (emoji or image) and cover banner
- Unlimited subpage nesting
- Breadcrumb navigation
- Move, duplicate, delete, restore from trash
- Favorites and page lock
- Export (Markdown, PDF, HTML)
- Page version history
- Public link sharing

---

## 6. Editor

Block-based rich text editor. Every element is a block.

**Block Types:**

| Category | Blocks |
|----------|--------|
| Text | Paragraph, H1, H2, H3, Bulleted List, Numbered List, Toggle, Quote, Callout, Divider |
| Task | To-Do (checkbox) |
| Media | Image, Video, Audio, File |
| Structure | Table of Contents, Simple Table, Columns (2–3 column layout) |
| Code & Math | Code Block (syntax highlighting), Equation (LaTeX) |
| Reference | Linked Page, Inline Database |

**Editor Features:**
- `/` slash command to insert any block
- Floating toolbar for inline formatting (bold, italic, code, link, comment, color)
- Markdown shortcuts (auto-convert as you type)
- Block drag-and-drop reordering
- Multi-block selection and bulk operations
- Continuous auto-save with offline queue
- 200-step undo history per session

---

## 7. Templates

Reusable page structures.

**Types:**
- **Built-in** — Curated library by category: Personal, Productivity, Project Management, Team & Knowledge, Personal CRM
- **Custom** — Save any page as a template; workspace-scoped, available to all members

**Template Button Block:** A block that creates a copy of a predefined block structure on click — ideal for logs, meeting notes, and journals.

---

## 8. Databases

Structured data collections where each entry is a full page.

**View Types:**

| View | Description |
|------|-------------|
| Table | Spreadsheet-style rows and columns |
| Board | Kanban columns grouped by a Select property |
| Calendar | Entries placed on a month grid by a Date property |
| Gallery | Visual card grid with cover images |

**Features:**
- Full-page or inline database (embedded in a page)
- Multiple named views per database (all share the same data)
- Filters with AND/OR logic, persistent per view
- Up to 5 stacked sort rules
- Grouping by Select property

---

## 9. Database Properties

Typed columns that define database structure.

| Type | Description |
|------|-------------|
| Text | Plain text |
| Number | Numeric with format options (plain, currency, %, scientific) |
| Select | Single option from a list (color-coded) |
| Multi-Select | Multiple options (color-coded tags) |
| Date | Date with optional time and date range |
| Checkbox | Boolean toggle |
| URL | Clickable hyperlink |
| Email | Clickable mailto link |
| Phone | Clickable tel link |
| Person | Workspace members (supports `@me` as dynamic default) |
| Relation | Bidirectional link to entries in another database (auto-created back-relation) |

**System Properties (auto-generated):** Created Time, Last Edited Time, Created By, Last Edited By

> **Limit:** 50 user-created properties per database.

---

## 10. Search

Global search across all workspace content.

**Features:**
- `Ctrl+K` / `Cmd+K` to open
- Real-time results — searches page titles, content, database entries, property values, comments
- Results filtered by user permissions (private pages never surface for others)
- Filters: location, content type, date range, author
- Recently visited pages shown before typing

**Powered by:** PostgreSQL full-text search (`tsvector` / `tsquery`)

---

## 11. Comments & Mentions

In-context collaboration on pages and blocks.

**Comments:**
- Block comments — attached to any block, threaded (one level deep)
- Text-level comments — anchored to a selected text range within a block
- Page-level comments — general feedback at the bottom of the page
- Resolve and reopen threads

**Mentions:**
- `@name` — mention a workspace member (sends notification)
- `@page` — insert a live page link (auto-updates on rename)
- `@date` — insert a formatted, clickable date reference

---

## 12. Permissions & Sharing

Two-layer access control: workspace roles + page-level permissions.

**Page Permissions:**

| Level | Can do |
|-------|--------|
| Full Access | Read, edit, manage permissions, share externally |
| Can Edit | Read and edit content, comment |
| Can Comment | Read and leave comments |
| Can View | Read only |

**Sharing:** Public link (no login required), guest access (invite external users to specific pages by email), subpage permission inheritance with per-page override.

---

## 13. Notifications

**In-App:** Bell icon with unread badge. Triggers: @mentions, comment replies, thread resolutions, page access granted, workspace invites, trash auto-deletion warnings. Real-time delivery via **Server-Sent Events (SSE)**; toast auto-dismisses after 5s.

**Email:** Real-time, Daily digest (default), Weekly digest, or Off. Timezone-aware delivery.

**Retention:** 90 days. Powered by pg-boss job queue.

---

## 14. Admin Panel

**Orbit Admin** — internal operations tool for the Notelian platform team. Not accessible to end users.

**Capabilities:**
- User management (view, ban/unban, impersonate, revoke sessions)
- Workspace management (view all workspaces, members)
- Template management (built-in templates via `/orbit/templates`)
- Platform analytics
- Audit trail (append-only log of all platform-team actions)

---

## 15. File Storage

Binary uploads stored in S3-compatible object storage and served via a CDN. Covers page cover images, page icons, user avatars, workspace icons, and all media blocks (Image, Video, Audio, File).

**Provider:** Any S3-compatible object storage

**Upload flow:** Pre-signed PUT URLs — files upload directly to the storage bucket, never transiting the app server.

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

## 16. MVP Scope

### Included in MVP

- [x] Authentication (passwordless magic link) + session management
- [x] Workspaces (multiple per account, switcher, create, invite, roles)
- [x] Onboarding (wizard, tour, hints)
- [x] Sidebar navigation with page tree
- [x] Pages (unlimited hierarchy, icon, cover, export)
- [x] Block-based editor (all block types, including Columns)
- [x] Templates (built-in + custom)
- [x] Databases (Table, Board, Calendar, Gallery views)
- [x] Database Properties (all types, including Relation + system properties)
- [x] Global Search (PostgreSQL FTS)
- [x] Comments & Mentions (block, text-level, and page-level)
- [x] Permissions & Sharing (roles, page permissions, public links, guest access)
- [x] In-app and Email Notifications
- [x] Orbit Admin (user + workspace + template management)
- [x] File Storage (S3-compatible object storage + CDN, pre-signed direct uploads)

### Excluded from MVP (Post-MVP)

- [ ] Real-time collaboration / multiplayer cursors (Phase 2)
- [ ] API access for third-party integrations (Phase 3)
- [ ] SSO / SAML (Phase 3)
- [ ] Mobile app — iOS/Android (Phase 3)
- [ ] AI features — summarize, draft, smart search (Phase 4)
- [ ] Whiteboard (Phase 4)
- [ ] Custom domains (Phase 4)
- [ ] Integrations — Slack, GitHub, Zapier (Phase 5)
- [ ] Plugin marketplace (Phase 5)
- [ ] Dark mode / appearance settings (Post-MVP)
- [ ] Language / localization (Post-MVP)

---

## 17. Tech Stack

| Layer | Technology | Purpose |
|-------|-----------|---------|
| Frontend | Next.js (App Router) | UI, routing, server components |
| Backend | Next.js API Routes / Server Actions | API layer |
| Database | PostgreSQL | Primary data store + full-text search |
| ORM | Drizzle ORM | Type-safe database access |
| Auth | Better Auth + Magic Link Plugin + Admin Plugin | Database-backed sessions, passwordless magic-link sign-in, user management |
| Editor | TipTap (ProseMirror-based) | Block-based rich text editor |
| Job Queue | pg-boss | Notifications, email digests, cleanup jobs |
| File Storage | S3-compatible object storage + CDN | Images, covers, file attachments |
| Email | Nodemailer (SMTP) | Transactional email delivery |
| Admin | Orbit Admin | Internal ops dashboard |

---

## 18. Phase-1 Architecture Decisions

Decisions that are **hard or impossible to change after data exists** — they must be settled before any pages, blocks, or permissions are persisted.

| # | Decision | Commitment for MVP | Why it can't wait |
|---|----------|--------------------|-------------------|
| 1 | **Page hierarchy storage** | **Closure table** (not a plain adjacency list) | "Get all descendant pages" is needed for permissions, search scoping, and bulk ops. Adjacency lists make this slow and are extremely hard to migrate once pages exist. |
| 2 | **Block content storage** | **JSONB with an explicit `schema_version` field from day one** | Adding or reshaping block types later requires migrating existing block JSON. Without versioning, block shapes become unmaintainable. |
| 3 | **Permission resolution** | **Single recursive CTE** that walks parents to the first explicit permission or workspace root | Computing effective permissions in application code is an N+1 query trap. Must be one query, designed up front. |
| 4 | **Search index maintenance** | **PostgreSQL triggers that fire on block-content changes, not just page-title changes** | Block content lives separately from the title; a title-only trigger silently produces stale search results. Test triggers under bulk ops (import, mass delete). |
| 5 | **Permission filtering location** | **Enforced at the SQL query level**, never in application code after fetch | App-level filtering after a broad query is a BOLA vulnerability — restricted rows leave the database before they're filtered. |
| 6 | **Real-time delivery (SSE)** | Host the `GET /api/notifications/stream` route on a **persistent-connection target (Railway / long-lived Node server, PM2 / Docker)**. If deploying the app on Vercel, that one route can't be a standard serverless function — but the client's `EventSource` auto-reconnect + polling fallback keeps the Notification Center correct, so Vercel remains viable with degraded liveness. | Serverless function timeouts kill long-lived SSE connections. The deploy target for this route is a prerequisite for choosing SSE; settle it before building real-time delivery. (Consistent with [development-plan.md](Features/development-plan.md) and [notifications.md](Features/notifications.md).) |

---

## 19. Accessibility (MVP baseline)

Accessibility is not addressed in the original specs and is cheapest to build in from the start. Minimum bar for Phase 1:

- **Keyboard accessibility** for all interactive elements (Tab, Enter, Escape); logical Tab order in complex views (database Table, comment panel)
- **ARIA labels** on the sidebar tree, database cells, and comment panels
- **Color is never the only indicator** — badges, callouts, and highlights pair color with an icon or text
- **WCAG AA contrast** (4.5:1 for text) enforced in the color palette

---

*Last updated: 2026-06-09*
