# Step 7 — Databases & Views

## What Is a Database in Notelian?

A database in Notelian is a structured collection of entries, where each entry is its own full page. Databases bring the power of a spreadsheet or data table directly into the page-and-block world of Notelian.

Every entry (row) in a database:
- Has a **title** (the main name or identifier of the entry)
- Has any number of **typed properties** (columns) — dates, dropdowns, numbers, people, relations, and more
- Is a **full Notelian page** — clicking an entry opens it as a page where the user can write content blocks, attach files, add subpages, and do everything a regular page can do

The same database can be displayed in multiple **views** — Table, Board, Calendar, or Gallery — and the user switches between views without any data loss. Views are just different visual lenses on the same underlying data.

---

## Creating a Database

There are two ways to place a database in Notelian:

### Full-Page Database
The database occupies its own dedicated page. The user creates it by selecting "New Database" from the sidebar or converting an existing page. A full-page database fills the entire content area and is accessed from the sidebar like any other page.

### Inline Database
The database is embedded as a block inside an existing page, sitting alongside other content blocks. The user inserts it via the slash command menu (`/database` or `/table`). Multiple databases can be embedded on the same page.

---

## The Four Database Views

### 1. Table View

The Table view is the most structured and data-dense way to look at a database. It is a traditional row-and-column spreadsheet layout.

**How it works:**
- Each row is one database entry (linked to its own page)
- Each column is one property type (title, date, select, etc.)
- The first column is always the entry title — it is bold and acts as a link to open that entry's full page
- Additional property columns appear to the right
- A `+` button at the bottom of the list adds a new entry as a new row
- A `+` button at the right edge of the header row adds a new property as a new column

**Interactions:**
- Clicking a cell allows inline editing of that property's value
- Clicking the entry title opens the entry as a full page
- Column headers can be clicked to sort the table by that property (ascending or descending)
- Column widths can be adjusted by dragging the edge of any column header
- Rows can be reordered by dragging the drag handle on the left side of each row (when no sort is applied)

**Best for:** Structured data entry, project tracking, CRM records, inventory, issue tracking — anywhere a spreadsheet layout makes sense.

---

### 2. Board View (Kanban)

The Board view displays database entries as cards arranged in vertical columns. Each column represents a group of entries that share the same value in a **select-type property** (for example, a "Status" property with values like To Do, In Progress, and Done).

**How it works:**
- The user selects which Select property the board groups by
- Each unique value of that property becomes a column on the board
- Entries appear as cards within their respective column
- The card shows the entry title and optionally a few key property values (date, assignee, priority) configured by the user
- A `+` button at the bottom of each column adds a new entry that automatically has that column's group value pre-set

**Drag & Drop:**
- Entries (cards) can be dragged from one column to another
- Dropping a card into a different column automatically updates the grouped property value on that entry
- Within a column, cards can be reordered by dragging

**Column Management:**
- New columns can be created by adding a new value to the grouped select property
- Columns can be hidden without deleting the underlying data
- Columns can be collapsed to show only the column header, saving screen space when one group is not the current focus

**Best for:** Project management (To Do / In Progress / Done), content publishing pipeline (Draft / Review / Published), hiring pipeline (Applied / Screened / Interview / Offer), or any workflow with defined stages.

---

### 3. Calendar View

The Calendar view displays database entries on a calendar grid, placing each entry on the date it corresponds to. This requires the database to have at least one **date-type property**.

**How it works:**
- The user selects which date property to use for placement (for example, "Due Date" or "Event Date")
- The calendar displays a month grid by default (rows for weeks, columns for days of the week)
- Each entry appears as a small card on its corresponding date
- If multiple entries share the same date, they stack vertically on that day cell
- Clicking an entry card opens that entry's full page

**Navigation:**
- Arrow buttons or keyboard shortcuts move forward and backward through months
- A "Today" button jumps the calendar back to the current month and highlights today's date
- Entries that span a date range appear as a horizontal bar spanning the relevant days on the calendar grid

**Multi-day span setup:**
- To enable multi-day spans, the database must have **two separate Date properties** — one for start date and one for end date
- In the Calendar view settings, the user selects which property is the "Start Date" and which is the "End Date"
- Spans render correctly across week rows and can span month boundaries
- If only one date property is configured, entries appear as single-day points on the calendar

**Adding Entries:**
- Clicking on any day cell creates a new entry with that day pre-filled as the date property value
- The new entry title is editable inline before confirming

**Best for:** Editorial calendars, event planning, deadline tracking, scheduling, and any time-based data.

---

### 4. Gallery View

The Gallery view displays database entries as visual cards arranged in a grid. It is the most visual of the four views and works best when entries have an associated image or cover.

**How it works:**
- Each entry is shown as a card in a grid layout
- The card displays a cover image at the top (if the entry has one) and the title below
- Additional properties can be configured to appear on the card face (date, status, tags, etc.)
- Clicking a card opens that entry's full page

**Card Covers:**
- The cover image shown on a gallery card comes from the page cover of the entry itself
- If no cover is set on the entry, the card shows a **solid color background** — the color is deterministically derived from the entry's ID, so it is always the same color for that entry (not random on each load). This ensures visual consistency across sessions.
- Users can configure which image property (if any) to use as the card cover instead of the page cover

**Grid Density:**
- Users can adjust the card size between small, medium, and large to control how many cards appear per row
- Small cards show more items per row with minimal detail; large cards show fewer but with more visible information

**Best for:** Photo galleries, design asset libraries, recipe collections, product catalogs, portfolio items, or any database where visual browsing is the primary use case.

---

## Switching Between Views

Each database can have multiple views of different types. A view selector panel appears at the top of the database. Users can:

- Click any view name to switch to that view instantly
- Click `+` (Add a View) to create an additional view of any type
- Give each view a custom name (for example, "Sprint Board" for a Board view, "This Month" for a Calendar view)
- Reorder views by dragging their tabs
- Delete views without deleting the underlying data

All views display the same entries. Creating an entry in one view makes it appear in all other views immediately.

---

## Filtering

Any database view can have one or more **filters** applied. Filters hide entries that do not match the criteria, showing only the relevant subset.

### Adding a Filter
The user opens the Filter panel from the view toolbar and clicks "Add Filter." They then select:
1. Which property to filter on
2. The filter condition (depends on property type — see table below)
3. The filter value to compare against

### Filter Conditions by Property Type

| Property Type | Available Conditions |
|---|---|
| Text | Contains, Does not contain, Is, Is not, Is empty, Is not empty |
| Number | Equals, Does not equal, Greater than, Less than, Is empty, Is not empty |
| Select | Is, Is not, Is empty, Is not empty |
| Multi-Select | Contains, Does not contain, Is empty, Is not empty |
| Date | Is, Is before, Is after, Is between, Is empty, Is not empty |
| Checkbox | Is checked, Is not checked |
| Person | Contains, Does not contain, Is empty, Is not empty |

### Multiple Filters
When multiple filters are added, the user chooses whether to combine them with **AND** (all conditions must match) or **OR** (any condition must match). This allows for complex, nuanced filtering of large databases.

### Filter Persistence
Filters can be saved as part of a named view (so they always apply when that view is selected) or applied temporarily just for the current session.

---

## Sorting

Any database view can be sorted by one or more properties. Sorting controls the order in which entries appear.

- The user opens the Sort panel and selects a property to sort by
- For each sort rule, the user selects Ascending or Descending order
- Multiple sort rules can be stacked — up to **5 sort rules** per view
- Sort rules are evaluated in order: if the first sort property produces a tie, the second rule is applied; if the second also ties, the third is applied, and so on through all stacked rules
- If all sort rules produce a tie across entries, the final tiebreaker is **entry creation time (oldest first)** — ensuring a stable and predictable order at all times
- Entries with **empty values** for the sort property are placed at the bottom when sorting ascending, and at the top when sorting descending

---

## Grouping

In Board view, grouping is the core mechanic (entries are grouped by a select property). In Table and Gallery views, optional grouping can be applied to organize entries into collapsible sections within the view, grouped by any select-type property.

For example, grouping a Table view by "Priority" creates three collapsible sections: High, Medium, and Low — each containing the relevant entries.

---

## Database Title and Description

Each database has:
- A **title** — displayed at the top of the database in full-page mode and in the sidebar
- An optional **description** — a short text description shown beneath the title to explain the purpose or context of the database

---

## Card Display Settings (Board & Gallery Views)

Each view has a **"Properties"** control in the view toolbar that lets users choose which property values appear directly on each card — without opening the entry detail view.

### Board View Card Properties
Users select which properties are shown as labels on each Kanban card. Recommended properties to display:
- Due Date (so deadlines are visible at a glance)
- Assignee/Person (shows avatar on the card)
- Priority (colored badge)
- Any other Select or Multi-Select property

Cards always show the entry title. All other properties are opt-in via the Properties toggle.

### Gallery View Card Properties
Users select which properties appear below the title on each gallery card. The card cover image (from the entry's page cover) appears at the top. Common choices:
- Status badge
- Date
- Tags (Multi-Select)
- Author (Person)

### Table View Column Visibility
In Table view, the "Properties" control hides or shows specific columns in the current view. Hidden columns still exist on the entry and are visible in the entry detail view.

---

## Inline Database Search

Within any database view, a **search bar** is accessible from the view toolbar (a search icon or "Filter" area). Typing in it filters the visible entries in real time by their **title property**.

- This is not a full-text search — it matches only the entry's title/name column
- Clearing the search field restores the full list
- For full-text property search, use Global Search (`Ctrl+K`)

---

## Entry Detail View

Clicking on any entry in any view opens that entry's **detail view** — a full-page editing experience where the user can:
- Edit the entry's title
- Edit all of its property values
- Write full block content in the body of the entry (the same block editor used on regular pages)
- Add comments and mentions
- View the entry's full-page URL for direct linking

The detail view can be opened in two modes:
- **Side panel** (default) — The entry opens as a panel sliding in from the right side, keeping the database view visible behind it. This is the default for all views.
- **Full page** — The entry opens as a full-page view, hiding the database view (accessible via breadcrumb navigation back). This can be triggered by holding `Ctrl` / `Cmd` when clicking the entry, or by clicking "Open as full page" from the side panel header.

The default open mode (side panel vs. full page) is configurable per view in View Settings and is saved as part of the view configuration.

---

*Feature File: step-07-databases-and-views.md*
*Step: 7 of 12 — Building Databases & Views*
*Phase: MVP — Phase 1*
