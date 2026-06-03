# Step 4 — Pages & Hierarchy

## What Is a Page?

A page is the primary container in Notelian. Everything the user creates lives on a page or is itself a page. A page holds an ordered stack of content blocks — text, images, databases, media, and more — and can also contain other pages nested inside it (called subpages).

Pages in Notelian serve as documents, wikis, dashboards, project boards, and databases all at once. There is no distinction between a "document" and a "page" — every piece of content the user creates is a page.

---

## Page Anatomy

Every page in Notelian consists of the following elements:

### Page Icon
An emoji or custom image displayed at the top of the page, next to the title, and in the sidebar navigation. Icons make pages visually distinguishable at a glance. The user can click the icon area to open a picker and choose any emoji, upload a custom image, or remove the icon entirely.

### Page Cover
A wide banner image displayed at the very top of the page, above the title. Covers give pages personality and visual identity. Users can:
- Upload a custom image from their device
- Choose from a built-in gallery of curated cover images (organized by color, landscape, abstract, etc.)
- Paste a URL to use an image from the web
- Reposition the focal point of the cover by dragging it up or down
- Remove the cover to return to a plain header

### Page Title
The large heading at the top of the page that serves as its name. The title is displayed in the sidebar, in search results, in breadcrumb navigation, and when the page is linked to or mentioned elsewhere. Titles can be any text — there is no length restriction.

### Page Content Area
The main body of the page below the title. This is the block editor canvas where all content blocks are added, arranged, and edited. The content area has no height limit — pages can be as long as needed.

---

## Creating Pages

There are multiple ways to create a new page in Notelian:

- **From the sidebar** — Clicking the `+` (New Page) button in the sidebar creates a new top-level page in the user's workspace
- **From within a page** — Typing `/page` in the slash command menu or clicking the "Add a page" option inserts a subpage block inside the current page
- **From the keyboard** — A keyboard shortcut opens a new blank page instantly
- **From a template** — Users can create a new page pre-filled with a template structure (see the Templates feature)
- **From the quick-add button** — Hovering next to any page in the sidebar reveals a `+` button that creates a subpage directly beneath it

---

## Subpages & Nesting

Notelian supports **unlimited page nesting**. Any page can contain other pages as subpages, and those subpages can contain further subpages — creating a deep hierarchy of content organized exactly as the user thinks.

### How Subpages Work

- A subpage is a block on the parent page that renders as a clickable page card
- The subpage card shows the subpage's icon (if set) and its title
- Clicking the card navigates to the subpage
- The subpage card can be moved anywhere on the parent page by dragging it, just like any other block
- Subpages appear in the sidebar under their parent page in a collapsible tree structure

### Moving Pages

Users can reorganize their hierarchy at any time:
- **Drag and drop in the sidebar** — Drag a page from one location in the sidebar tree to another
- **Move To action** — Right-clicking a page or using the page options menu provides a "Move To" option that opens a search dialog to find the destination
- A page moved to a new parent becomes a subpage of that parent automatically

### Duplicating Pages

Any page can be duplicated from its options menu (`···`) → **"Duplicate"**. Duplicating a page:
- Creates an exact copy of the page including all blocks, subpages, icon, and cover
- Places the duplicate immediately below the original in the sidebar (same parent level)
- Names the copy **"[Original Title] (copy)"** — the user can rename it immediately
- All block content, database entries, and nested subpages are copied
- The duplicate is fully independent — changes to it do not affect the original

### Opening Pages in a New Tab

Any page can be opened in a new browser tab from:
- The sidebar options menu (`···`) → **"Open in new tab"**
- Middle-clicking (scroll-click) the page title in the sidebar
- Right-clicking any page link or mention in the editor → **"Open in new tab"**

This is useful when comparing two pages side by side or referencing a page while writing in another.

### Deleting Pages

Deleted pages are moved to a **Trash** area — they are not immediately destroyed. Pages in Trash can be:
- **Restored** — Returned to their original location in the hierarchy
- **Permanently deleted** — Removed completely from the workspace (irreversible)

Trash is accessible from the bottom of the sidebar. Deleted subpages are visible in Trash along with their parent path, so users can identify what they deleted and from where.

**Trash Retention Policy:**
- Pages in Trash are automatically and permanently deleted after **30 days**
- A warning badge appears on pages that are within 7 days of automatic deletion: "Will be deleted in X days"
- Workspace Admins can permanently delete any page from Trash at any time, regardless of the 30-day window
- Permanent deletion cannot be undone — content is removed from all storage

---

## Breadcrumb Navigation

When a user is viewing a deeply nested subpage, a **breadcrumb trail** appears at the top of the page (above the cover and title area). The breadcrumb shows the full path from the workspace root to the current page.

Example: `Workspace → Projects → Q3 Planning → Sprint 7 → Bug List`

Each element in the breadcrumb is a clickable link that navigates to that ancestor page. This allows users to quickly jump up the hierarchy without using the sidebar.

---

## Page Settings & Options

Every page has a settings panel accessible from the top-right area of the page. This panel contains:

### Customization
- Change or remove the page icon
- Change, reposition, or remove the page cover
- Toggle full-width layout (expands the content area to span the full browser width, removing the default centered column constraint)
- Toggle small text mode (reduces the base font size for denser content display)

### Page Information
- **Created by** — Shows which workspace member created the page and the creation date
- **Last edited** — Shows who last edited the page and when
- **Word count** — Displays the number of words currently on the page

### Export
- Export the page as a Markdown file
- Export the page as a PDF

---

## Linking Between Pages

Pages can reference each other through inline mentions. When a user types `@` followed by the name of a page, a popover appears showing matching pages. Selecting one inserts a **page mention** — a clickable inline link that navigates to the referenced page when clicked.

Page mentions are live — if the referenced page is renamed, the mention text updates automatically to reflect the new name.

---

## Favorites

Users can mark any page as a **Favorite** to pin it for quick access. Favorited pages appear in a dedicated "Favorites" section at the top of the sidebar, always visible regardless of the page's position in the hierarchy.

To favorite a page:
- Hover over the page in the sidebar and click the star icon that appears
- Or open the page options menu and select "Add to Favorites"

Favorites are per-user — favoriting a page does not affect how other workspace members see the sidebar.

---

## Page Lock

A page can be **locked** to prevent accidental edits. When a page is locked:
- The content area becomes read-only — no blocks can be added, edited, or deleted
- A visual indicator shows that the page is locked
- Only users with Full Access permission on the page can unlock it

This is useful for finalized documents, published wikis, or reference pages that should not be accidentally modified.

---

## Templates for Pages

Any page can be saved as a custom template for future reuse. New pages can also be created from the built-in template gallery. See the Templates feature file for full details.

---

## Page URL Structure

Every page in Notelian has its own unique URL. The URL format is:

```
https://notelian.app/[workspace-slug]/[page-id]
```

Example:
```
https://notelian.app/acme-corp/a1b2c3d4
```

- `workspace-slug` — a URL-safe version of the workspace name, set when the workspace is created (e.g., "Acme Corp" → `acme-corp`). Admins can customize the slug in Workspace Settings.
- `page-id` — a short unique identifier generated for each page (8–12 characters, alphanumeric).
- Page titles do NOT appear in the URL — renaming a page never breaks existing links.
- Users can copy a page's URL from the browser address bar or via the page options menu → "Copy Link." Recipients with the appropriate access are taken directly to that page when they open the link.

### Duplicate Page and Deep Recursion

When a page with deeply nested subpages is duplicated, **all descendant pages are duplicated recursively** — the full subtree is copied, not just the top-level page. There is no nesting depth limit on duplication. For very large page trees (50+ subpages), duplication runs as a background operation and the user is notified when it completes.

---

*Feature File: step-04-pages-and-hierarchy.md*
*Step: 4 of 12 — Creating & Organizing Pages*
*Phase: MVP — Phase 1*
