# NOTELIAN — Master Product Document

## Overview

Notelian is a modern, all-in-one workspace tool that combines notes, documents, databases, and project management into a single unified platform. Designed as a next-generation productivity tool, Notelian draws inspiration from the best of Notion, Coda, AppFlowy, and Anytype — while building its own identity through a clean, fast, and intuitive experience that works for individuals, teams, and organizations of every size.

Every piece of content in Notelian lives as a modular, flexible building block. Pages are first-class citizens. Databases are deeply integrated. Views adapt to how you think. And collaboration is woven into every layer of the product.

---

## Vision

To become the most intuitive and powerful all-in-one workspace — where thinking, planning, and executing all happen in one place, without friction.

---

## Mission

Empower individuals and teams to organize their knowledge, manage their work, and collaborate in real time — using a single, beautifully designed tool that scales from personal notes to full company wikis.

---

## Target Users

- **Individuals** — students, writers, researchers, and knowledge workers organizing personal notes and ideas
- **Small Teams** — startups, creative agencies, and small businesses needing a shared workspace
- **Product & Engineering Teams** — teams needing wikis, roadmaps, sprint boards, and bug trackers in one tool
- **Freelancers & Consultants** — professionals managing multiple clients and projects simultaneously

---

## Product Philosophy

1. **Everything is a Block** — All content is a modular block. Blocks can be moved, converted, nested, and combined without constraints.
2. **Pages are Universal** — A page can be a document, a database, a wiki, a project board, or all of the above at once.
3. **Databases are Documents** — Databases live inside pages, can be viewed in multiple ways, and every row is itself a full page.
4. **Collaboration is Native** — Sharing, commenting, mentioning, and real-time co-editing are built into the core product.

---

## Tech Stack

### Frontend Framework — Next.js

Notelian is built on **Next.js** (App Router) — providing server-side rendering, file-based routing, API routes, and React Server Components. Next.js enables fast page loads, SEO-friendly rendering, and a unified full-stack codebase without a separate backend server.

### Database — PostgreSQL

All application data — users, workspaces, pages, blocks, databases, and permissions — is stored in **PostgreSQL**. PostgreSQL is chosen for its reliability, ACID compliance, native full-text search (used for global search indexing), and JSON support for storing flexible block content structures.

### ORM — Drizzle ORM

Database queries and schema management are handled through **Drizzle ORM** — a TypeScript-native, type-safe ORM with excellent PostgreSQL support. Drizzle provides compile-time query safety, schema-based migrations, and a clean query-builder API without the overhead of heavier ORMs.

### Background Jobs — pg-boss

Asynchronous background work — including transactional email delivery, email digest batching, and other deferred tasks — is managed by **pg-boss**, a PostgreSQL-backed job queue. pg-boss keeps background processing inside the same PostgreSQL instance, eliminating the need for a separate queue service (e.g., Redis) in Phase 1.

### Authentication — Better Auth (with Admin Plugin)

User authentication (sign-up, login, OAuth, session management, password reset, email verification) is handled by **Better Auth** — a framework-agnostic, TypeScript-first authentication library with first-class Next.js support. The **Better Auth Admin Plugin** extends the core library to provide platform-level user management: session inspection, forced logout, account suspension, and audit controls — all surfaced through the Orbit Admin panel.

### Admin Panel — Orbit Admin

Internal platform administration is handled through **Orbit Admin** — an admin dashboard wired to Better Auth's admin plugin. Orbit Admin gives the Notelian operations team a UI to manage users, workspaces, sessions, and platform settings without building a custom internal tool. Orbit Admin is an internal operations tool — it is not accessible to end users.

### Block Editor — TipTap

Notelian's editor is powered by **TipTap** (built on ProseMirror) — chosen for its headless design, extension-based architecture, and first-class support for real-time collaborative editing via Yjs CRDT (used in Phase 2).

### Configuration & Environment

Notelian does not rely on external feature flag services, remote config platforms, or third-party CMS systems. All configuration is managed internally through standard environment variable patterns.

---

## Platform & Browser Support

**Phase 1 target:** Desktop web browsers only. No native mobile app in Phase 1 (planned for Phase 5).

| Browser | Minimum Version | Support Level |
|---|---|---|
| **Chrome** | 110+ | Full — primary target |
| **Firefox** | 115+ | Full |
| **Safari** | 16.4+ | Full |
| **Edge** | 110+ | Full |
| **Mobile browsers** | Any | Read-only view; editing not supported in Phase 1 |

**Screen resolution minimum:** 1280 × 720px for the full editing experience.

The sidebar, editor, and database views are optimized for desktop viewports. Responsive layout for tablet and mobile editing is a Phase 2 item.

---

## MVP Scope — Phase 1

Phase 1 delivers the complete core experience across **12 steps** of the user journey: account creation, onboarding, navigation, pages, block editing, templates, databases, properties, search, collaboration, permissions, and notifications. Every feature must work reliably and feel polished before Phase 2 begins.

---

## Project Structure

```
Notelian/
├── NOTELIAN.md                                   ← This master document
├── README.md                                     ← Project README
└── features/                                     ← Individual feature specification files
    ├── step-01-user-auth-and-workspaces.md       ← Step 1: Sign Up, Login, Workspaces, Roles
    ├── step-02-onboarding.md                     ← Step 2: Setup Wizard, Profile, First Page
    ├── step-03-sidebar-navigation.md             ← Step 3: Page Tree, Favorites, Workspace Switcher
    ├── step-04-pages-and-hierarchy.md            ← Step 4: Pages, Subpages, Breadcrumbs, Trash
    ├── step-05-block-based-editor.md             ← Step 5: Block Types, Slash Command, Formatting
    ├── step-06-templates.md                      ← Step 6: Template Gallery, Custom Templates, Template Button
    ├── step-07-databases-and-views.md            ← Step 7: Table, Board, Calendar, Gallery Views
    ├── step-08-database-properties.md            ← Step 8: Property Types, Relations, Default Values
    ├── step-09-global-search.md                  ← Step 9: Full-Text Search, Filters, Quick Navigation
    ├── step-10-comments-and-mentions.md          ← Step 10: Block Comments, Threads, @Mentions
    ├── step-11-permissions-and-sharing.md        ← Step 11: Access Levels, Public Links, Guest Access
    └── step-12-notifications.md                  ← Step 12: Notification Center, Toasts, Email Digests
```

---

## Phase 1 — User Journey (12 Steps)

The 12 features below are ordered by when a real user first encounters them — from first visit to advanced daily use.

---

### Step 1 — Sign Up & Account Creation
**File:** [features/step-01-user-auth-and-workspaces.md](features/step-01-user-auth-and-workspaces.md)

New users sign up via **email + password** (with email verification — link expires in 24 hours) or **Google OAuth** (one click, no password needed; Google's verified email serves as verification). Returning users log in the same way. Forgotten passwords are reset via a time-limited, single-use link (expires in 1 hour). Each account can belong to multiple workspaces. Workspace members are assigned a role — **Admin**, **Editor**, or **Viewer** — that sets their baseline capabilities. Users can permanently **delete their own account** from Account Settings.

---

### Step 2 — Onboarding & First Workspace Setup
**File:** [features/step-02-onboarding.md](features/step-02-onboarding.md)

A one-time **4-screen Setup Wizard** runs immediately after sign-up:
1. Profile setup (display name, avatar, title)
2. Create a new workspace or join an existing one via invite link
3. Optionally invite teammates by email
4. Choose a starter template or start with a blank page

After the wizard, a **5-step tooltip tour** highlights the key UI areas (sidebar, new page, slash command, search). Contextual hints appear once per feature as users encounter them for the first time.

---

### Step 3 — Workspace & Sidebar Navigation
**File:** [features/step-03-sidebar-navigation.md](features/step-03-sidebar-navigation.md)

The left sidebar is the navigation hub. It contains the **Workspace Switcher** (top), **Quick Action Buttons** (search, new page, notifications, settings), a **Favorites** section, the full **Page Tree** (collapsible, drag-and-drop, hover actions), **Shared / Private** subsections, and **Trash** at the bottom. The sidebar is resizable, collapsible (`Ctrl+\`), and has an inline page-tree filter for quick lookup.

---

### Step 4 — Creating & Organizing Pages
**File:** [features/step-04-pages-and-hierarchy.md](features/step-04-pages-and-hierarchy.md)

Pages are the primary container in Notelian. Each page has a **title**, optional **icon** (emoji or image), optional **cover** (banner image), and a block editor canvas. Pages support unlimited **subpage nesting**. Pages can be moved by drag-and-drop in the sidebar or via "Move To." Deleted pages go to **Trash** (restorable or permanently deletable). **Breadcrumbs** show the full hierarchy path. Pages can be **favorited**, **locked**, **exported** (Markdown / PDF), and directly linked via URL.

---

### Step 5 — Writing & Editing with Blocks
**File:** [features/step-05-block-based-editor.md](features/step-05-block-based-editor.md)

Every piece of content is a **block**. Blocks are inserted via the **`/` slash command menu**. Block categories:

| Category | Types |
|---|---|
| Text | Paragraph, H1, H2, H3, Bullet List, Numbered List, Toggle, Quote, Callout, Divider |
| Tasks | To-Do (checkbox) |
| Media | Image, Video, Audio, File |
| Structure | Table of Contents, Simple Table, Column Layout |
| Code & Math | Code Block (syntax highlighting), Equation (LaTeX) |
| References | Linked Page, Inline Database |

Inline formatting (Bold, Italic, Underline, Strikethrough, Code, Color, Highlight, Link, Comment) is applied via a floating toolbar on text selection. **Markdown shortcuts** auto-convert as the user types. Blocks support drag-and-drop reorder, nesting (`Tab` / `Shift+Tab`), multi-select, and a context menu with Delete, Duplicate, Turn Into, Move To, Copy Link, and Color.

---

### Step 6 — Using Templates
**File:** [features/step-06-templates.md](features/step-06-templates.md)

Templates let users start with a pre-built structure. The **Template Gallery** is organized into categories: Personal, Productivity, Project Management, Team & Knowledge, and Personal CRM. Any page can be **saved as a custom template** for workspace-wide reuse. A **Template Button** block inserts a repeatable block structure on demand — ideal for recurring logs (daily standups, meeting notes, sprint entries).

---

### Step 7 — Building Databases & Views
**File:** [features/step-07-databases-and-views.md](features/step-07-databases-and-views.md)

Databases are structured collections of entries where every entry is a full page. One database can display in **four view types** without data loss:

| View | Best For |
|---|---|
| **Table** | Structured data entry, spreadsheet layout |
| **Board (Kanban)** | Workflow stages (To Do / In Progress / Done) |
| **Calendar** | Time-based data, deadlines, editorial schedules |
| **Gallery** | Visual browsing, photo/asset collections |

All views support **Filtering** (per property, AND/OR logic), **Sorting** (multi-level), and **Grouping**. Entries open in a **Side Panel** (database stays visible) or **Full Page** mode. Multiple named views can exist on the same database simultaneously.

---

### Step 8 — Database Properties & Structure
**File:** [features/step-08-database-properties.md](features/step-08-database-properties.md)

Properties are the typed columns that define a database's structure.

| Type | Stores |
|---|---|
| Text | Freeform plain text |
| Number | Numeric values (plain, currency, percentage) |
| Select | One option from a color-badged list |
| Multi-Select | Multiple options simultaneously |
| Date | Calendar date + optional time + optional date range |
| Checkbox | True / False toggle |
| URL | Clickable web address |
| Email | Clickable mailto link |
| Phone | Clickable tel link |
| Person | Workspace member(s) — avatar + name |
| Relation | Cross-database entry links (bidirectional) |

Properties can be added, renamed, reordered, hidden per view, and given **default values**. Max 50 properties per database in Phase 1.

---

### Step 9 — Global Search & Navigation
**File:** [features/step-09-global-search.md](features/step-09-global-search.md)

`Ctrl+K` / `Cmd+K` opens the search dialog from anywhere in the app. Results appear in real time as the user types, ranked by relevance (title matches first, then recency). Filters: location (All / Shared / Private / Specific Page), content type (Pages / Databases / Entries / Comments), date range, and author. Before typing, the dialog shows **Recently Visited Pages** — making it a one-key navigation launcher as well as a search tool. Full-text indexing is powered by **PostgreSQL's native full-text search** (`tsvector` / `tsquery`).

---

### Step 10 — Comments, Mentions & Collaboration
**File:** [features/step-10-comments-and-mentions.md](features/step-10-comments-and-mentions.md)

**Block comments** are attached to specific blocks; **text-level comments** are anchored to selected text ranges (like Google Docs). All comments are threaded (with replies), editable, deletable, and **resolvable** (resolved threads are hidden but preserved and can be reopened). `@name` mentions people (sends a notification), `@page` inserts a live page link, and `@date` inserts a formatted date reference. A **Page Comment** area at the top of each page handles general page-level feedback.

---

### Step 11 — Permissions & Sharing
**File:** [features/step-11-permissions-and-sharing.md](features/step-11-permissions-and-sharing.md)

Access control works in two layers: **Workspace Roles** (Admin / Editor / Viewer) as a baseline, and **Page-Level Permissions** (Full Access / Can Edit / Can Comment / Can View) for fine-grained control. Permissions inherit top-down from parent to subpage; any subpage can override with its own custom settings. **Public Links** let anyone read (or comment on) a page without a Notelian account. **Guest Access** lets external users access specific pages via email invite without joining the full workspace.

---

### Step 12 — Notification Center
**File:** [features/step-12-notifications.md](features/step-12-notifications.md)

The `🔔` bell icon in the sidebar opens the Notification Center panel. Notifications are triggered by: @mentions, comment replies in participated threads, new comments on created pages, thread resolutions/reopens, and new page access grants. Each notification links directly to the source context. **In-app toasts** appear for real-time events. Email frequency is configurable per user: real-time, daily digest, weekly digest, or off. Notifications are retained for 90 days.

---

## Master Keyboard Shortcuts Reference

| Action | Windows/Linux | Mac |
|---|---|---|
| Open Global Search | `Ctrl+K` | `Cmd+K` |
| New Page | `Ctrl+N` | `Cmd+N` |
| Toggle Sidebar | `Ctrl+\` | `Cmd+\` |
| Open Notifications | `Ctrl+Shift+N` | `Cmd+Shift+N` |
| Undo | `Ctrl+Z` | `Cmd+Z` |
| Redo | `Ctrl+Shift+Z` | `Cmd+Shift+Z` |
| Bold | `Ctrl+B` | `Cmd+B` |
| Italic | `Ctrl+I` | `Cmd+I` |
| Underline | `Ctrl+U` | `Cmd+U` |
| Slash Command | `/` | `/` |
| Indent Block | `Tab` | `Tab` |
| Outdent Block | `Shift+Tab` | `Shift+Tab` |
| Move Block Up | `Ctrl+Shift+↑` | `Cmd+Shift+↑` |
| Move Block Down | `Ctrl+Shift+↓` | `Cmd+Shift+↓` |
| Select All Blocks | `Ctrl+A` | `Cmd+A` |
| Submit Comment | `Ctrl+Enter` | `Cmd+Enter` |
| Navigate Search Results | `↑` / `↓` | `↑` / `↓` |
| Open Search Result | `Enter` | `Enter` |
| Close Modal / Dialog | `Escape` | `Escape` |
| Keyboard Shortcuts Help | `Ctrl+/` | `Cmd+/` |

---

## Competitive Differentiation

| Competitor | What Notelian Learns From It | Where Notelian Does Better |
|---|---|---|
| **Notion** | Block editor, database views, templates, hierarchical pages | Faster, cleaner UI, less overwhelming for new users |
| **Coda** | Interactive elements, formula-powered documents | Simpler onboarding, more intuitive block model |
| **AppFlowy** | Open architecture, multiple database views, offline support | More polished UI, richer template gallery |
| **Anytype** | Object-based model, privacy-first thinking | Gentler learning curve, better team collaboration |

---

## Design Principles for MVP

- **Speed First** — Every interaction under 200ms perceived response.
- **No Hidden Complexity** — Features are discoverable without a tutorial.
- **Keyboard-Friendly** — Every action reachable via keyboard shortcut.
- **Consistent Block Model** — Blocks behave identically everywhere in the product.
- **Progressive Disclosure** — Simple by default, powerful when needed.
- **Onboarding Without Friction** — New users reach their first useful moment within 2 minutes.
- **Auto-Save Always** — All content is saved automatically and continuously. There is no manual save action and no "Save" button. Users never lose work.

---

## Product Roadmap

| Phase | Focus | Key Features |
|---|---|---|
| **Phase 1 — MVP** | Core experience | Auth, onboarding, editor, pages, databases, search, templates, collaboration, permissions, notifications |
| **Phase 2 — Collaboration** | Real-time & history | Multiplayer cursors, version history, activity log, Slack/email integrations, timeline (Gantt) view |
| **Phase 3 — Power** | Advanced data | Formula properties, rollup aggregations, synced blocks, bidirectional relations, API access |
| **Phase 4 — Intelligence** | AI layer | AI writing assistant, AI database fill, semantic search, AI summaries |
| **Phase 5 — Platform** | Integrations & mobile | Public API, webhooks, GitHub/Jira/Figma integrations, native iOS & Android apps |

---

## Glossary

| Term | Definition |
|---|---|
| **Next.js** | The React framework powering the Notelian frontend and API layer (App Router). |
| **PostgreSQL** | The relational database storing all Notelian data — pages, blocks, users, workspaces. |
| **Drizzle ORM** | The TypeScript-native ORM used for type-safe PostgreSQL queries and schema migrations. |
| **pg-boss** | The PostgreSQL-backed job queue used for async background work — email delivery, digest scheduling. |
| **Better Auth** | The authentication library handling sign-up, login, OAuth, sessions, and password reset. |
| **Better Auth Admin Plugin** | An extension to Better Auth that enables platform-level user and session management for Orbit Admin. |
| **Orbit Admin** | The internal admin dashboard (not user-facing) for managing users, workspaces, and sessions at the platform level. |
| **Block** | The smallest unit of content. Every text, image, or embed is a block. |
| **Page** | A container holding blocks and optional subpages. |
| **Subpage** | A page nested inside another page, forming a parent-child hierarchy. |
| **Database** | A structured collection of entries (rows) with typed properties (columns). |
| **View** | A visual representation of a database — Table, Board, Calendar, or Gallery. |
| **Property** | A typed column in a database (text, date, select, person, relation, etc.). |
| **Entry** | A single row in a database. Each entry is a full page with properties and block content. |
| **Workspace** | The top-level organizational unit. A user can belong to multiple workspaces. |
| **Template** | A pre-built page or database structure duplicated for reuse. |
| **Template Button** | A block that creates a copy of a predefined block structure when clicked. |
| **Slash Command** | Typing `/` in the editor opens the block insertion menu. |
| **Mention** | Using `@` to reference a teammate, page, or date inline in content. |
| **Guest** | An external user with access to specific pages but not the full workspace. |
| **Permission Inheritance** | Subpages automatically adopt the permission settings of their parent page. |
| **Trash** | A holding area for deleted pages — restorable or permanently deletable. |
| **Breadcrumb** | The navigation trail showing the full path from workspace root to current page. |
| **Toggle Block** | A collapsible block that hides or reveals nested content when clicked. |
| **Relation** | A database property linking entries across two databases bidirectionally. |

---

*Last Updated: Phase 1 MVP Planning — June 2026*
*Tool: Notelian — Your All-in-One Workspace*
