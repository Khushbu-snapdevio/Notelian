# Notelian — All-in-One Workspace (Notion-style)

## Product Vision

Build a modern all-in-one workspace where individuals and teams can write, plan, and collaborate — combining notes, documents, databases, and project management in one place.

**Goals:**
- Everything is a block — flexible, composable content
- Fast, distraction-free writing experience
- Powerful databases that feel like documents
- Native collaboration without switching tools

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

**Powered by:** Better Auth with Admin Plugin

**Features:**
- Sign up (Email + Password)
- Sign in / Sign out
- OAuth login (Google)
- Forgot password / Reset password
- Email verification
- Database-backed session management
- Multi-device session list and revocation
- Ban / unban users (via Admin Plugin)
- Impersonate users (via Admin Plugin)

---

## 2. Workspace

Top-level organizational container. Every user belongs to at least one workspace.

**Example:** `Acme Inc`, `Personal`, `My Team`

**Features:**
- Create, edit, delete workspace
- Switch between multiple workspaces
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
- Collapsible sidebar with resizable width
- Hierarchical page tree with drag-and-drop reordering
- Favorites section (personal per-user)
- Workspace switcher (top of sidebar)
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
| Structure | Table of Contents, Simple Table, Columns |
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
| Calendar | Entries placed on a calendar by date property |
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
| Relation | Bidirectional link to entries in another database |

**System Properties (auto-generated):** Created Time, Last Edited Time, Created By, Last Edited By

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
- Text-level comments — anchored to a selected text range
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

**Sharing:** Public link (no login required), guest invite by email, subpage permission inheritance with per-page override.

---

## 13. Notifications

**In-App:** Bell icon with unread badge. Triggers: @mentions, comment replies, thread resolutions, page access granted, workspace invites. Real-time delivery via **Server-Sent Events (SSE)**; toast auto-dismisses after 5s.

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

Binary uploads stored in Cloudflare R2 and served via Cloudflare CDN. Covers page cover images, page icons, and all media blocks (Image, Video, Audio, File).

**Provider:** Cloudflare R2 (S3-compatible, zero egress fees)

**Upload flow:** Pre-signed PUT URLs — files upload directly to R2, never transiting the app server.

**Per-type size limits:**

| Type | Max size |
|------|---------|
| Page cover image | 5 MB |
| Page icon | 1 MB |
| Image block | 10 MB |
| Video / Audio block | 50 MB |
| File block | 100 MB |

---

## 16. MVP Scope

### Included in MVP

- [x] Authentication (Email + Google OAuth)
- [x] Workspace (create, invite, roles)
- [x] Onboarding (wizard, tour, hints)
- [x] Sidebar navigation with page tree
- [x] Pages (unlimited hierarchy, icon, cover, export)
- [x] Block-based editor (all block types)
- [x] Templates (built-in + custom)
- [x] Databases (Table, Board, Calendar, and Gallery views)
- [x] Database Properties (all 11 types + system properties)
- [x] Global Search (PostgreSQL FTS)
- [x] Comments & Mentions
- [x] Permissions & Sharing (roles, page permissions, public links, guests)
- [x] In-app and Email Notifications
- [x] Orbit Admin (user + workspace + template management)
- [x] File Storage (Cloudflare R2, pre-signed direct uploads)

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
| Auth | Better Auth + Admin Plugin | Sessions, OAuth, user management |
| Editor | TipTap (ProseMirror-based) | Block-based rich text editor |
| Job Queue | pg-boss | Notifications, email digests, cleanup jobs |
| File Storage | S3 / Cloudflare R2 | Images, covers, file attachments |
| Email | Resend | Transactional email delivery |
| Admin | Orbit Admin | Internal ops dashboard |

---

*Last updated: 2026-06-04*
