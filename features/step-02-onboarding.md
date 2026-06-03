# Step 2 — Onboarding & First-Time Experience

## Overview

Onboarding is the first impression Notelian makes on every new user. A well-designed onboarding flow reduces time-to-value — getting users from "just signed up" to "doing something useful" as quickly as possible, without overwhelming them. The goal is for every new user to complete their first meaningful action within 2 minutes of creating their account.

Notelian's onboarding consists of three stages:
1. **Stage 1 — Setup Wizard** (guided steps immediately after sign-up)
2. **Stage 2 — Welcome Screen** with an optional tooltip tour (first workspace visit)
3. **Stage 3 — Contextual Hints** (non-intrusive prompts that appear as users encounter features for the first time)

---

## Stage 1 — Setup Wizard

The setup wizard runs immediately after a new user creates their account and verifies their email. It is a modal overlay shown on first login. It cannot be accessed again after completion (though all settings changed in the wizard can be updated later in Account Settings and Workspace Settings).

### Screen 1 — Profile Setup

The user is asked to personalize their account before doing anything else.

**Fields:**
- **Display Name** (required) — the name shown to teammates in mentions, comments, and member lists. Pre-filled with the name from Google OAuth if available.
- **Profile Avatar** (optional) — the user can upload an image from their device. If skipped, a color-coded initial avatar is automatically generated from the user's display name.
- **Role / Title** (optional) — a short descriptive string like "Designer", "Engineering Manager", or "Freelancer". Shown next to the user's name in member directories. Maximum 50 characters.

**Actions:**
- **"Next"** — saves the profile and advances to Screen 2
- No "Skip" option — display name is required; other fields default gracefully

---

### Screen 2 — Create or Join a Workspace

When this screen appears, the user does not yet belong to any workspace — the wizard is what creates it. Every Notelian account must belong to at least one workspace before entering the product.

**Option A — Create a New Workspace**
1. User clicks **"Create a new workspace"**
2. User enters a **workspace name** (required, max 100 characters). Examples: "My Notes", "Acme Corp", "Side Projects"
3. User selects a **workspace icon** (emoji picker or upload a custom image). Defaults to a generic workspace emoji if skipped.
4. User clicks **"Create Workspace"** → advances to Screen 3

**Option B — Join an Existing Workspace**
1. User clicks **"Join an existing workspace"**
2. User pastes an **invite link** (a full URL of the form `notelian.app/invite/[token]`) shared by a workspace admin. The visible invite "code" is the token portion of this URL — they are the same thing. Both the full URL and just the token are accepted.
3. If valid, the workspace name and icon are shown as a preview: "You're joining: [Workspace Name]"
4. If the invite link has expired or been deactivated, an error is shown: "This invite link is no longer active. Ask the workspace admin for a new one."
5. User clicks **"Join Workspace"** → skips to Screen 4 (no need to invite teammates to an existing workspace)

---

### Screen 3 — Invite Teammates (Optional)

Shown only for users who created a new workspace.

The user can invite teammates now or skip and do it later from Workspace Settings.

**Invite by email:**
- User types one or more email addresses (comma or Enter-separated)
- User selects the role for all invitees: Editor (default) or Viewer
- Invitation emails are queued and sent after the wizard is complete

**"Skip for now"** — advances to Screen 4 without sending invites

---

### Screen 4 — Choose a Starting Template

The user selects how to begin their workspace. They see four or five recommended starter templates and a blank page option.

**Starter Template Options:**
- **Meeting Notes** — pre-structured notes page with agenda, attendees, decisions, and action items sections
- **Project Tracker** — a database with Board and Table views, and properties for status, assignee, and due date
- **Personal Journal** — a dated entry template for daily notes and reflections
- **Team Wiki** — a nested page structure with onboarding, processes, and guidelines sections
- **Blank Page** — starts with a completely empty page

**How it works:**
1. User clicks a template card (highlighting it) or clicks "Blank Page"
2. User clicks **"Get Started"**
3. The wizard closes, the workspace loads, and the selected template page opens — ready to use

**Note on template placeholder text:** All starter templates use placeholder text to guide the user — styled in a lighter color to signal "replace this." This is the same placeholder convention described in the Templates feature (Step 6). The user edits or replaces placeholder content to make the page their own.

---

## Stage 2 — Welcome Screen & Tooltip Tour

After the wizard closes, the user lands inside the workspace for the first time. A **Welcome Banner** appears at the top of the first page with a brief message:

> "Welcome to [Workspace Name]! Here's a quick tour to get you started."

Clicking "Show me around" activates a **5-step tooltip tour** that highlights key UI areas one by one. Users can click "Next" to advance or "Skip Tour" to dismiss at any point.

**Tour Steps:**

**Step 1 — The Sidebar**
> A tooltip appears over the sidebar panel.
> "Your workspace lives here. Pages are organized in a tree — click any page to open it, or click + to create a new one."

**Step 2 — New Page Button**
> Tooltip highlights the "New Page" `+` button.
> "Click here to create a new page. You can create as many pages as you need — organized however makes sense to you."

**Step 3 — Slash Command**
> Tooltip appears inside the editor area over the content placeholder.
> "Type / anywhere in a page to add content — headings, images, lists, tables, databases, and more. Try it now."

**Step 4 — Global Search**
> Tooltip highlights the Search button or the keyboard shortcut hint.
> "Press Ctrl+K (or Cmd+K) to instantly search and jump to any page in your workspace."

**Step 5 — Done**
> "You're all set! Start building — or explore templates to get a head start."
> Button: **"Start Building"** — dismisses the tour

After the tour ends (or is skipped), the Welcome Banner is dismissed and does not reappear.

---

## Stage 3 — Contextual Hints

After onboarding, Notelian shows **contextual hints** the first time a user encounters a feature they haven't used yet. These are subtle, non-blocking prompts — a small tooltip or a ghost placeholder message — that guide the user without interrupting them.

### Examples of Contextual Hints

**Empty page content area:**
> A ghost text placeholder reads: "Press Enter to write, or type / to add something."
> This disappears the moment the user starts typing.

**First time hovering a page in the sidebar:**
> A small tooltip appears briefly over the `+` and `···` icons: "Click + to add a subpage. Click ··· for more options."
> Dismissed after 3 seconds or when clicked.

**First time creating a database:**
> A hint appears at the top of the database: "Click + to add an entry. Click the column header to add a property."

**First time opening global search with no results typed:**
> "Search your workspace — or use ↑↓ to navigate to a recent page."

**Rules for Contextual Hints:**
- Each hint appears only once per user account (not once per session — once ever)
- Hints are dismissible with a single click or key press
- Hints never block the user's action — they are always non-modal and non-blocking
- Hints are stored per-user so they are not shown again after being dismissed

---

## Empty State Design

When a new workspace has no pages yet, the main content area shows an **empty state** screen with:
- A brief headline: "Your workspace is ready"
- A sub-message: "Create your first page or start from a template"
- Two buttons: **"New Page"** and **"Browse Templates"**
- A subtle illustration or graphic (not blocking the UI)

This empty state replaces the tooltip tour for users who skip it — ensuring there is always a clear call-to-action.

---

## Re-Accessing Onboarding Resources

After initial onboarding, users can access help resources from:
- **"?" Help button** in the bottom-left of the sidebar → opens a help panel with links to documentation, keyboard shortcuts, and template gallery
- **Keyboard Shortcuts Reference** — accessible via `Ctrl+/` (`Cmd+/`) from anywhere in the app — shows a modal with all available shortcuts

---

## Onboarding Data & Tracking (Internal)

The following events are tracked during onboarding for product analytics:

| Event | When It Fires |
|---|---|
| `onboarding_started` | Setup wizard appears for the first time |
| `profile_completed` | User completes Screen 1 and clicks Next |
| `workspace_created` | User creates a new workspace |
| `workspace_joined` | User joins via invite link |
| `teammates_invited` | User sends invites on Screen 3 |
| `teammates_skipped` | User skips Screen 3 |
| `template_selected` | User picks a starter template |
| `blank_page_selected` | User picks blank page |
| `onboarding_completed` | Wizard closes and workspace loads |
| `tour_started` | User clicks "Show me around" |
| `tour_skipped` | User clicks "Skip Tour" |
| `tour_completed` | User completes all 5 tour steps |

These events feed a funnel report that tracks where users drop off during onboarding so the flow can be improved over time.

---

*Feature File: step-02-onboarding.md*
*Step: 2 of 12 — Onboarding & First Workspace Setup*
*Phase: MVP — Phase 1*
