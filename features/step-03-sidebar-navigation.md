# Step 3 — Sidebar Navigation

## Overview

The sidebar is the primary navigation hub of the Notelian workspace. It runs along the left side of the application and is always visible during normal use. It shows the user's full workspace structure, provides quick access to key pages and features, and allows for intuitive organization through drag-and-drop.

The sidebar is designed to scale from a single personal workspace with a handful of pages to a large team workspace with hundreds of nested pages — without becoming overwhelming.

---

## Sidebar Layout Structure

The sidebar is divided into distinct vertical sections, each serving a specific purpose. From top to bottom:

### 1. Workspace Header

At the very top of the sidebar is the **Workspace Header**. It displays:
- The workspace name and icon
- A clickable area that opens a **Workspace Switcher** dropdown — allowing users who belong to multiple workspaces to switch between them instantly
- An indication of which workspace is currently active

### 2. Quick Action Buttons

A row of icon buttons immediately below the workspace header for the most frequently accessed global actions:

- **Search** — Opens the global search dialog (same as the keyboard shortcut `Ctrl+K` / `Cmd+K`)
- **New Page** — Creates a new blank page at the top level of the workspace
- **Notifications** — Opens the notification panel (mentions, comments, updates)
- **Settings** — Opens workspace and account settings

### 3. Favorites Section

A collapsible section that shows pages the current user has marked as favorites. This section is personal — each user has their own favorites list and favoriting a page does not affect how others see the sidebar.

Favorites appear as flat list items, regardless of where the page actually sits in the workspace hierarchy. This allows quick access to frequently visited pages without navigating deep into the tree.

If no pages have been favorited, the Favorites section is hidden automatically.

### 4. Page Tree (Main Navigation)

The largest section of the sidebar. It displays the full hierarchical structure of the workspace as a collapsible tree of pages.

- Each page appears as a row in the tree showing its icon and title
- Pages with subpages have a collapse/expand arrow (chevron) on the left side of their row
- Clicking the chevron expands or collapses that page's subtree of subpages
- Clicking the page title navigates to that page
- The currently active page is highlighted with a distinct background color

Pages at the top level of the workspace are shown at the root of the tree. Their subpages appear indented beneath them when expanded.

### 5. Shared vs. Private Sections

The Page Tree is optionally divided into two subsections:

- **Shared** — Pages and databases that are accessible to other workspace members based on their permissions
- **Private** — Pages that are visible only to the current user. Private pages are not indexed in workspace-wide search for other users and are not visible to admins unless explicitly shared.

Users can move pages between Shared and Private sections by dragging or through the page options menu.

### 6. Trash

At the very bottom of the sidebar is a **Trash** link. Clicking it opens the Trash view, which shows all pages deleted in this workspace. From Trash, pages can be restored to their original location or permanently deleted.

---

## Page Tree Interactions

### Hover Actions

When the user hovers over any page row in the sidebar, two additional action icons appear on the right side of the row:

- **`+` (Add Subpage)** — Clicking this immediately creates a new untitled subpage nested under the hovered page
- **`···` (Options Menu)** — Opens a context menu for that specific page with options including: Open in new tab, Rename, Add to Favorites, Move To, Duplicate, Delete, Copy Link, and Lock Page

### Drag and Drop Reordering

Any page in the sidebar can be dragged to a new position:

- **Reorder at the same level** — Drag a page above or below its siblings to change the order within that section of the tree
- **Change parent (reparent)** — Drag a page and drop it onto another page to make it a subpage of that page
- **Move to top level** — Drag a subpage all the way to the left (to the root indentation level) to remove it from its parent and place it at the workspace root

A blue insertion indicator appears during dragging to show exactly where the page will land when dropped.

### Rename in Place

Double-clicking a page title in the sidebar makes it editable inline. The user types the new name and presses `Enter` or clicks away to confirm the rename. The page title updates everywhere it appears — in the sidebar, in breadcrumbs, in search results, and in any mentions of that page.

---

## Sidebar Width & Collapse

### Resizing

The sidebar width can be adjusted by clicking and dragging the right edge of the sidebar (the resize handle). The sidebar remembers its width setting between sessions.

### Collapsing the Sidebar

Users can collapse the sidebar entirely to maximize the content area for focused writing. When collapsed:
- The sidebar is hidden from view
- A small chevron icon (`›`) appears at the top-left corner of the screen
- **Hovering** over the icon (or the left edge of the screen) temporarily reveals the sidebar in "peek" mode — the sidebar slides in and stays visible as long as the cursor remains over it
- **Clicking away** (moving the cursor off the sidebar) dismisses peek mode and the sidebar slides back out
- **Clicking the chevron icon** while the sidebar is peeked permanently expands the sidebar (equivalent to toggling it open with `Ctrl+\`)
- The collapsed/expanded state is saved per user — it persists across sessions and page refreshes

### Keyboard Toggle

A keyboard shortcut (`Ctrl+\` / `Cmd+\`) toggles the sidebar open and closed without using the mouse.

---

## Search Within the Sidebar

At the top of the page tree section, there is a **sidebar filter input** — a small search field that filters the visible pages in the sidebar in real time as the user types. This is distinct from the global search and is useful for quickly locating a specific page in a large workspace tree without navigating through collapsed sections.

- **Matching pages:** Shown normally with their title and icon. If a match is a subpage, its parent pages are also shown (collapsed, not expanded) to preserve hierarchy context.
- **Non-matching pages:** **Completely hidden** from the sidebar while the filter is active — not dimmed, not shown at all. This keeps results clean and uncluttered.
- **Drag-and-drop:** Drag-and-drop reordering is disabled while the filter is active. The user must clear the filter first.
- **Clearing the filter:** Pressing `Escape` or clearing the input restores the full tree view with all pages visible in their original positions.

---

## Recently Visited Pages

A **Recent** section shows the pages the current user has visited most recently within this workspace. This helps users pick up where they left off after switching contexts.

- **Ordering:** Most recently visited first (reverse-chronological). The list updates in real time as the user navigates — opening a page moves it to the top.
- **Scope:** Shows the last **10 pages** visited in the current workspace. Older entries drop off as new ones are added.
- **Display:** Each entry shows the page icon, title, and a relative timestamp ("2 hours ago", "Yesterday").
- **Time window:** No minimum age — even a page visited 3 years ago appears if it is within the most recent 10. The list is purely recency-ranked, not time-capped.

---

## Sidebar Customization

Users can right-click on section headers within the sidebar to toggle which sections are visible. For example, a user who never uses Private pages can hide that section to keep the sidebar cleaner.

Section visibility settings are per-user and per-workspace, so each team member can configure the sidebar to match their working style.

---

## Workspace Switcher

Clicking the workspace name at the top of the sidebar opens the **Workspace Switcher** panel, which:
- Lists all workspaces the current user is a member of
- Shows each workspace's name, icon, and the user's role in it
- Allows one-click switching between workspaces
- Has a "Create Workspace" option to start a new workspace
- Has a "Join a Workspace" option to enter an invite code

Switching workspaces updates the entire sidebar and main content area to reflect the selected workspace. Each workspace has its own completely separate page tree, members, and settings.

---

## Sidebar Bottom Section

At the very bottom of the sidebar, below the Trash link, sits a persistent footer area with two items always visible:

### Account & Profile

A clickable row showing the current user's avatar and display name. Clicking it opens **Account Settings** — the same settings panel accessible from the workspace header. This provides a quick path to:
- Edit profile (name, avatar, title)
- Change email or password
- Toggle Light / Dark / System appearance mode
- Configure notification preferences
- Manage active sessions
- Log out

### Help & Resources

A **"?"** icon button that opens the **Help Panel** — a slide-in panel containing:
- Link to documentation / help center
- **Keyboard Shortcuts** reference (`Ctrl+/` / `Cmd+/` also opens this from anywhere)
- Link to the Template Gallery
- Link to submit feedback or report a bug

The Help Panel is non-blocking — it opens without navigating away from the current page.

---

*Feature File: step-03-sidebar-navigation.md*
*Step: 3 of 12 — Workspace & Sidebar Navigation*
*Phase: MVP — Phase 1*
