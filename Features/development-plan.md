# Development Plan

## Overview

This document covers Notelian's technical architecture, development phases, and key engineering decisions. It is the primary reference for developers building the platform.

---

## Tech Stack

| Layer | Technology | Version / Notes |
|-------|-----------|----------------|
| Framework | Next.js (App Router) | v15+ — server components, server actions, API routes |
| Language | TypeScript | Strict mode enabled |
| Database | PostgreSQL | v16+ — primary store, full-text search, job queue |
| ORM | Drizzle ORM | Type-safe queries, migrations, schema-as-code |
| Auth | Better Auth + Admin Plugin | Database-backed sessions, OAuth, impersonation |
| Editor | TipTap | ProseMirror-based, extensible block editor |
| Job Queue | pg-boss | PostgreSQL-backed — notifications, email, cleanup |
| File Storage | AWS S3 | Page covers, media blocks, file attachments |
| Email | Nodemailer (SMTP) | Transactional email delivery via SMTP |
| Admin | Orbit Admin | Custom internal ops dashboard (`/orbit`) |
| Styling | Tailwind CSS | Utility-first CSS framework |
| Math Rendering | KaTeX | LaTeX equation rendering in editor blocks |
| Testing | Vitest + Playwright | Unit + integration + E2E |
| Deployment | Vercel / Railway | Next.js hosting + managed PostgreSQL |

---

## Repository Structure

```
notelian/
├── app/                        # Next.js App Router pages
│   ├── (auth)/                 # Sign up, sign in, forgot password
│   ├── (app)/[workspace]/      # Main workspace UI
│   │   ├── [pageId]/           # Page editor
│   │   └── settings/           # Workspace settings
│   ├── orbit/                  # Orbit Admin (platform team only)
│   └── api/                    # API route handlers
├── components/                 # Shared UI components
│   ├── editor/                 # TipTap editor and block components
│   ├── database/               # Database views (Table, Board, Calendar, Gallery)
│   └── ui/                     # Design system primitives
├── lib/                        # Business logic and utilities
│   ├── auth/                   # Better Auth configuration
│   ├── db/                     # Drizzle schema and queries
│   └── notifications/          # Notification triggers and pg-boss jobs
├── drizzle/                    # Database migrations
└── public/                     # Static assets
```

---

## Development Phases

### Phase 1 — MVP (Current)

**Goal:** Core workspace, writing, and collaboration features.

**Scope:**
- Authentication (Email + Google OAuth, Better Auth)
- Workspace creation, members, and roles
- Onboarding wizard, tooltip tour, contextual hints
- Sidebar navigation with page tree and drag-and-drop
- Pages — unlimited hierarchy, icon, cover, version history, export
- Block-based editor — all block types, slash command, auto-save
- Templates — built-in gallery + custom workspace templates
- Databases — all 4 views (Table, Board, Calendar, Gallery), filters, sorting, grouping
- Database properties — all 11 types + system properties
- Global search — PostgreSQL full-text search
- Comments — block, text-level, and page-level; resolve/reopen threads
- Mentions — @name, @page, @date
- Permissions — workspace roles + page-level access, public links, guests
- Notifications — in-app (SSE), email digest (pg-boss)
- Orbit Admin — user, workspace, and template management
- File Storage (AWS S3, pre-signed direct uploads)

**Estimated timeline:** 16–20 weeks

---

### Phase 2 — Collaboration & Views

**Goal:** Real-time multiplayer editing and advanced database views.

**Scope:**
- Real-time collaborative editing (Yjs CRDT + WebSocket server)
- Multiplayer cursors and presence indicators
- Page analytics (view count, unique visitors, traffic referrers)
- Version history improvements (named versions, compare diffs)
- Password-protected public links
- Granular notification preferences (per-event-type toggles)
- Mobile web — read-only optimized experience
- Dark mode / appearance settings

**Estimated timeline:** 12–16 weeks after Phase 1

---

### Phase 3 — Integrations & Scale

**Goal:** External integrations, enterprise features, and mobile.

**Scope:**
- Public REST API + API keys
- Webhooks for page and database events
- SSO / SAML (enterprise identity providers)
- Native iOS app
- Native Android app
- GitHub OAuth provider
- Import from Notion (page structure and content)

**Estimated timeline:** 16–20 weeks after Phase 2

---

### Phase 4 — AI & Advanced Features

**Goal:** AI-powered writing and knowledge management.

**Scope:**
- AI writing assistant (autocomplete, summarize, improve writing)
- AI-powered search (semantic / natural language)
- Smart database autofill (AI-suggested property values)
- Whiteboard (freehand drawing canvas)
- Custom domains for workspace public pages

**Estimated timeline:** TBD

---

### Phase 5 — Ecosystem

**Goal:** Platform extensibility and integrations marketplace.

**Scope:**
- Plugin / extension API
- Marketplace for third-party blocks and integrations
- Slack integration (notifications)
- GitHub integration (link PRs and issues to database entries)
- Zapier / Make connectors
- Jira import

**Estimated timeline:** TBD

---

## Key Architecture Decisions

### Why PostgreSQL for full-text search?

Notelian uses PostgreSQL's native `tsvector` / `tsquery` for search rather than a dedicated search service like Elasticsearch or Typesense. This reduces operational complexity for the MVP — one database to manage instead of two services. The tradeoff is lower relevance tuning flexibility. If search quality becomes a limitation at scale, migrating to a dedicated search service is a clean upgrade path.

---

### Why pg-boss for notifications?

pg-boss is a job queue built on PostgreSQL. It handles notification delivery, email digest scheduling, and cleanup jobs without introducing Redis or a separate message broker. Jobs are transactional — if the triggering action (a comment save) fails, the notification job is never created. The tradeoff is that pg-boss is less efficient than Redis pub/sub at very high message throughput, which is acceptable for an MVP-scale product.

---

### Why Drizzle ORM over Prisma?

Drizzle provides a more direct mapping to SQL, better TypeScript inference, and lighter runtime overhead than Prisma. Schema changes require writing explicit migration files rather than relying on an ORM to infer them, which gives more control in production environments. The tradeoff is a steeper initial setup for developers new to Drizzle.

---

### Why TipTap over Slate or Lexical?

TipTap is built on ProseMirror, which is the most mature and battle-tested rich text editing foundation available. It has strong TypeScript support, a growing ecosystem of extensions, and Yjs integration for Phase 2 real-time collaboration. The tradeoff is that custom block types require writing ProseMirror node specs, which has a learning curve.

---

### Block storage — JSONB in PostgreSQL

Each block's content is stored as a `content` JSONB column in the `blocks` table. This allows flexible, schema-less storage of different block types without requiring a separate table per block type. Full-text indexing is maintained separately via a `search_vector` column updated by triggers.

---

## Database Schema Overview

```
Core tables:
  users, sessions, accounts, verification_tokens
  workspaces, workspace_members
  pages (covers both pages and database entries)
  blocks

Database features:
  databases (extends pages)
  database_views
  database_properties
  property_values

Collaboration:
  comments
  notifications
  notification_preferences

User preferences:
  user_preferences
  user_hint_states
  user_favorites
  user_recently_visited

Admin:
  platform_audit_log
```

---

## Environment Variables

| Variable | Description |
|----------|-------------|
| `DATABASE_URL` | PostgreSQL connection string |
| `BETTER_AUTH_SECRET` | Secret for Better Auth session signing |
| `GOOGLE_CLIENT_ID` | Google OAuth client ID |
| `GOOGLE_CLIENT_SECRET` | Google OAuth client secret |
| `S3_BUCKET` | AWS S3 bucket name |
| `S3_REGION` | AWS region (e.g. `us-east-1`) |
| `S3_ACCESS_KEY_ID` | AWS IAM access key ID |
| `S3_SECRET_ACCESS_KEY` | AWS IAM secret access key |
| `CDN_URL` | CloudFront CDN base URL for serving uploaded files |
| `EMAIL_FROM` | Sender address (e.g. `noreply@notelian.app`) |
| `SMTP_HOST` | SMTP server hostname (e.g. `smtp.sendgrid.net`) |
| `SMTP_PORT` | SMTP port (`587` for STARTTLS, `465` for TLS) |
| `SMTP_USER` | SMTP authentication username |
| `SMTP_PASSWORD` | SMTP authentication password |
| `SMTP_SECURE` | `true` for TLS (port 465), `false` for STARTTLS (port 587) |
| `NEXT_PUBLIC_APP_URL` | Public base URL (e.g. `https://notelian.app`) |

---

## Local Development

```bash
# Install dependencies
npm install

# Set up environment
cp .env.example .env.local
# Fill in DATABASE_URL and other required variables

# Run database migrations
npm run db:migrate

# Start development server
npm run dev
```

**Prerequisites:** Node.js v20+, PostgreSQL v16+

---

## Testing Strategy

| Layer | Tool | Coverage target |
|-------|------|----------------|
| Unit tests | Vitest | Business logic, utilities |
| Integration tests | Vitest + real PostgreSQL | API routes, database queries |
| E2E tests | Playwright | Critical user flows (sign up, create page, invite member) |

---

## Deployment

**Production stack:**
- Next.js app: Vercel (or Railway)
- PostgreSQL: Supabase / Neon / Railway managed PostgreSQL
- File storage: AWS S3 + CloudFront CDN
- Email: Nodemailer (SMTP)

**CI/CD:**
- Push to `main` → automated tests → deploy to production
- Push to feature branch → automated tests → preview deployment

---

*Last updated: 2026-06-04*
