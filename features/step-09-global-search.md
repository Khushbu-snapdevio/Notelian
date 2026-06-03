# Step 9 — Global Search

## Overview

Global Search in Notelian allows users to find any piece of content across their entire workspace — instantly. Whether searching for a page title, a piece of text buried inside a document, a database entry name, or a comment — global search surfaces it within milliseconds.

For large workspaces with hundreds of pages, search is often the fastest way to navigate. Rather than clicking through the sidebar tree, users type a few characters and jump directly to what they need. Notelian is designed so that search is a first-class navigation tool, not just a find-in-page utility.

---

## Opening Global Search

There are three ways to open the global search dialog:

1. **Keyboard shortcut** — `Ctrl+K` on Windows/Linux or `Cmd+K` on Mac. This is the fastest and most common way. The shortcut works from anywhere in the application — while writing, while viewing a database, while reading a page.
2. **Sidebar button** — A search icon button in the top section of the left sidebar, beneath the workspace header.
3. **Quick access bar** — An accessible search entry point at the top of the application on mobile viewports.

---

## The Search Dialog

When opened, the global search dialog appears as a centered modal overlay on top of the current view. It does not navigate away from the current page — it floats above it so the user can quickly check something and dismiss the dialog to return exactly where they were.

### Dialog Anatomy

- **Search input field** — Focused automatically when the dialog opens. The user starts typing immediately.
- **Results list** — Appears below the input as the user types, showing matching results in real time.
- **Filter controls** — Optional filters the user can apply to narrow results by type, section, or date.
- **Recent pages** — Shown by default before the user types anything — the last several pages the user visited in this workspace, allowing instant return to recent work.

---

## How Search Works

### Instant Results

As the user types each character, the results list updates in real time without needing to press `Enter`. Results appear within the time the user types the first two or three characters.

### What Is Searched

Global search indexes the following content across the workspace:

- **Page titles** — The title of every page and subpage
- **Page content** — The text content of all blocks on every page (paragraphs, headings, list items, callouts, code, etc.)
- **Database entry titles** — The title/name property of every database entry
- **Database property values** — The values stored in text, select, URL, email, and phone properties
- **Comments** — The text content of comments left on pages and blocks

### What Is Not Searched

- Content inside files and attachments (PDFs, spreadsheets, images) — only the file name is indexed
- Content in Private pages of other users — search only returns results the current user has permission to view
- Archived or permanently deleted content in Trash

### Search Permissions Scope

Search results are strictly permission-scoped:
- **Regular members (Editor / Viewer):** Cannot find Private pages belonging to other users, even if they are a workspace Admin. Admins have elevated workspace privileges but private pages are excluded from search for all users except the owner.
- **Workspace Admins:** Can access any page once they know its URL or location, but global search does NOT surface other users' Private pages in Admin search results. This is intentional — it preserves the privacy expectation of Private pages. Admins can view the list of private pages (title only) through the Orbit Admin panel for compliance purposes, but not via in-product search.
- **Guests:** Search results include only pages they have been explicitly granted access to.

---

## Search Results

### Result Limits

- The search dialog shows a maximum of **50 results** per query. If more than 50 matches exist, only the top 50 by relevance score are shown.
- A note at the bottom of the results list reads "Showing top 50 results — refine your search to narrow down" when the result count is at or near the limit.
- There is no pagination — if 50 results are insufficient, the user should apply filters or use a more specific query.

### Result Format

Each result in the search list shows:
- **Page icon** — The emoji or image icon of the page
- **Page title** — The primary name of the page or entry
- **Location path** — The breadcrumb showing where the page lives (e.g., `Projects → Q3 → Marketing`)
- **Content preview snippet** — A short excerpt of the matching content with the matching words highlighted in bold, giving context for why the result matched
- **Last edited** — A relative timestamp (e.g., "2 hours ago", "Yesterday", "Jun 1")

### Result Ordering

By default, results are ordered by **relevance** — an internal scoring that weighs:
- Exact title matches rank higher than content matches
- Recently edited pages rank higher than older ones when relevance scores are similar
- Pages the current user has recently visited rank higher

Users can change the sort order to:
- **Last edited** — Most recently modified pages appear first
- **Created date** — Newest pages appear first

---

## Filtering Results

Users can optionally apply filters to narrow the result set. Filters appear as a row of toggle buttons or a filter dropdown below the search input.

### Filter by Location
Restrict results to a specific section of the workspace:
- **All** — Search the entire workspace (default)
- **Shared** — Search only shared workspace content
- **Private** — Search only the current user's private pages
- **Specific Page** — Search only within a selected page and all of its subpages (useful when the user knows the content is somewhere within a particular section)

### Filter by Content Type
- **Pages** — Return only page-level results (excludes database entries shown separately)
- **Databases** — Return only database pages
- **Database Entries** — Return only individual database row entries
- **Comments** — Return only comment matches

### Filter by Date (Last Edited)
- Any time (default)
- Past 24 hours
- Past 7 days
- Past 30 days
- Custom date range (date picker)

### Filter by Author
- Any author (default)
- Created by me
- Last edited by me
- Created by a specific member (select from member list)

---

## Quick Navigation

Beyond finding specific content, global search doubles as a **quick navigation launcher**. After typing and seeing results, users can:

- **Click** any result to navigate to that page or entry immediately
- **Keyboard arrow keys** (`↑` and `↓`) to move the selection up and down the results list without using the mouse
- **Press `Enter`** on the highlighted result to navigate to it
- **Press `Escape`** to close the dialog and return to the current page without navigating

This keyboard-first design means power users can find and jump to any page in the workspace entirely without touching the mouse.

---

## Recent Pages (No Query State)

When the search dialog is opened but no text has been typed yet, it shows the **Recently Visited Pages** list — the last several pages the current user opened, in reverse chronological order.

This makes the search shortcut (`Ctrl+K` / `Cmd+K`) useful not just for searching but as a general navigation launcher — opening it and immediately pressing `Enter` to re-open the most recent page, or scanning the recent list to jump back to a previous context.

---

## Title-Only Search Toggle

When searching, users can toggle **"Search page titles only"** to restrict results to title matches only. This is useful when the user remembers the page name but global content search is returning too many results from body text.

- **Default state: OFF** — full-text search across titles and content is active by default.
- The toggle state is remembered for the current session only — it resets to OFF when the search dialog is closed and reopened.

---

## Search in Databases

Databases also have their own **inline search** separate from the global search. Within any database view, a search input (or filter icon) allows filtering entries by their title property. This is a quick, in-context filter — not a full-text search across property values — and is separate from global search.

For full-text search across a database's property values, global search is the right tool.

---

## Keyboard Shortcuts Summary

| Action | Shortcut |
|---|---|
| Open Global Search | `Ctrl+K` / `Cmd+K` |
| Navigate results | `↑` / `↓` |
| Open selected result | `Enter` |
| Close search dialog | `Escape` |
| Clear search input | `Ctrl+A` + `Backspace` |

---

## Technical Implementation — Full-Text Search

Global search is powered by **PostgreSQL's native full-text search** using `tsvector` and `tsquery`.

### How It Works

- Each indexable content record (page title, block text, database entry title, property values, comment text) has a corresponding `tsvector` column that is kept up to date via PostgreSQL triggers on insert and update
- Search queries are converted to `tsquery` and matched against the `tsvector` column using the `@@` operator
- Results are ranked using `ts_rank` weighted by content type: title matches receive higher weight than body text matches
- Recency and user-visit history are applied as secondary ranking signals after the full-text score
- The search index is scoped by workspace and filtered by the current user's permission set before results are returned — users never see content they do not have access to

### Why PostgreSQL Full-Text Search

Using PostgreSQL's native FTS keeps the search infrastructure within the same database that stores all other data — no Elasticsearch, Typesense, or external search service is required in Phase 1. PostgreSQL FTS is sufficient for Phase 1 workspace sizes and eliminates operational complexity of a separate search cluster.

---

*Feature File: step-09-global-search.md*
*Step: 9 of 12 — Global Search & Navigation*
*Phase: MVP — Phase 1*
