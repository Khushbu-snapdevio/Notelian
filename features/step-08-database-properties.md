# Step 8 — Database Properties

## What Are Properties?

Properties are the typed fields (columns) that define the structure of a database entry in Notelian. Every database entry (row) has a title — which is the primary identifier — plus any number of additional properties that hold structured data about that entry.

Properties are the reason Notelian databases are more powerful than simple lists or notes. They turn a collection of pages into a structured, filterable, sortable, and viewable dataset.

Each property has:
- A **name** (what the column is called, e.g., "Due Date", "Status", "Assignee")
- A **type** (what kind of data it stores, e.g., date, select, person)
- A **value** for each entry (the actual data stored for that row and column combination)

---

## Managing Properties

### Adding a Property
In Table view, clicking the `+` button at the right end of the column header row opens the "Add Property" menu. The user names the property and selects its type. The new property column immediately appears for all existing entries (with empty values by default).

### Editing a Property
Clicking any column header opens the property settings panel. From here the user can:
- Rename the property
- Change the property type (with a warning if existing data may be affected — see conversion rules below)
- Configure type-specific options (like choices for a Select property)
- Set a default value for new entries
- Delete the property (removes it and all its values from all entries — this action is confirmed before executing)

**Property Type Conversion Rules:**
When a user changes a property's type, existing values are converted according to these rules:

| From Type | To Type | What Happens to Existing Data |
|---|---|---|
| Text | Number | Non-numeric values become empty; numeric strings are parsed as numbers |
| Text | Select | Each unique text value becomes a new Select option; multiple unique values each get their own option |
| Number | Text | Values are converted to their string representation (e.g., `42` → `"42"`) |
| Select | Multi-Select | Each entry's single selected option becomes the sole selected option in multi-select |
| Multi-Select | Select | Only the **first** selected option is kept; additional options are discarded |
| Date | Text | Date values are converted to ISO 8601 string format (`YYYY-MM-DD`) |
| Any type | Checkbox | Non-empty / non-zero values become checked; empty / zero values become unchecked |
| Checkbox | Any type | Checked → `true` (text), `1` (number); Unchecked → `false` (text), `0` (number) |
| Any type | Relation | Existing values are cleared — Relation cannot be populated from other types |

A confirmation dialog shows a preview of the conversion impact (e.g., "15 of 40 entries will have their values cleared") before the change is applied.

### Reordering Properties
Column headers can be dragged left or right to change the order in which properties appear in Table view. Property order in the entry detail view follows the same order.

### Hiding Properties
Individual properties can be hidden from the current view without deleting them or their data. Hidden properties are accessible from the entry detail view. This allows cleaner, focused views that only show the most relevant columns for a given context.

---

## Property Types

### Text
Stores freeform, plain text. Used for notes, descriptions, links, IDs, or any unstructured string data.
- Supports multi-line text
- No formatting is applied — it is stored and displayed as plain text
- Searchable via the global search

### Number
Stores a numeric value. Used for quantities, scores, prices, percentages, counts, and any measurable data.
- Users can configure the number format: plain number, currency (with currency symbol), percentage (displays with `%` suffix), or scientific notation
- Supports decimal values
- Can be used in sort operations (smallest to largest, or largest to smallest)

### Select
Stores a single value chosen from a predefined list of options. Each option has a name and a color.
- When a user clicks a select cell, a dropdown appears showing all defined options
- Selecting an option sets it as the value; only one option can be selected at a time
- New options can be added directly from the dropdown without leaving the current entry
- Options can be renamed, recolored, and deleted from the property settings
- Options appear as colored badges/tags in the cell

### Multi-Select
Stores multiple values from a predefined list of options. Works the same as Select but allows any number of options to be active simultaneously.
- Useful for tags, labels, categories, skills, or any dimension where an entry can belong to multiple groups
- Each selected option appears as a colored badge
- Options are shared with the property definition — any option added in one entry is available in all entries for that property

### Date
Stores a calendar date and optionally a time.
- Clicking the cell opens a calendar picker to choose the date visually
- Supports an optional time component (hour and minute)
- Supports an optional end date (for date ranges — "from X to Y")
- Dates can include time zone information
- Used extensively in Calendar view (entries are placed on the calendar by their date property value)
- Displays in a configurable date format (YYYY-MM-DD, MM/DD/YYYY, etc.)

### Checkbox
Stores a boolean value — either checked (true) or unchecked (false).
- Renders as a checkbox icon in the cell — clicking toggles between checked and unchecked
- Useful for completion flags, boolean toggles, approval states, and yes/no fields
- Can be used in filters (filter by "is checked" to show only completed entries)

### URL
Stores a valid web URL as text.
- Displays as a clickable hyperlink that opens the URL in a new tab
- Can be validated to ensure the value starts with `http://` or `https://`
- Useful for linking to external resources, project pages, design files, or documentation

### Email
Stores an email address.
- Displays as a clickable `mailto:` link that opens the user's default mail client when clicked
- Useful for contact databases, CRM entries, or team member directories

### Phone
Stores a phone number as text.
- Displays as a clickable `tel:` link that opens the phone app on mobile devices
- No formatting is enforced — international numbers, extensions, and custom formats are all stored as entered

### Person
Stores a reference to one or more workspace members (users who have access to the workspace).
- Clicking the cell opens a member picker showing all workspace members
- Multiple members can be assigned to the same person property (for fields like "Assignees" or "Reviewers")
- Each selected person is displayed with their avatar and name
- In Board view, databases can be grouped by a Person property to see what is assigned to each member
- Can be filtered to show only entries assigned to the current user ("Assigned to me")

**Default value — current user (`@me`):**
When setting a default value for a Person property, the user can select **"@me (Current User)"** as a special dynamic value. When a new entry is created, `@me` resolves to whichever workspace member is creating the entry at that moment — not a fixed person. This makes it ideal for "Created By" or "Assignee" fields where the default should always be the person doing the creating. `@me` is a special system token; it is not the same as selecting a specific named member as the default.

### Relation
Creates a link between entries in two different databases (or two entries within the same database). This is the most structurally powerful property type.

**How it works:**
- When a Relation property is created, the user selects which other database to relate to
- In each entry's cell for a Relation property, the user searches for and selects one or more entries from the related database
- Once linked, the related entries appear as clickable mentions — clicking opens that entry's detail view
- Relations are bidirectional by default: when Entry A in Database X is linked to Entry B in Database Y, Entry B also automatically shows Entry A in its corresponding back-relation

**Bidirectional backlink — how to access it:**
When a Relation property is created on Database X pointing to Database Y, Notelian automatically creates a corresponding **back-relation property** on Database Y. This back-relation property is named "← [Database X name]" by default and appears in Database Y's property list. It is read-only — entries in Database Y cannot manually add back-links; the back-relation is populated automatically based on what Database X entries have linked.

For example: A "Tasks" database has a Relation property pointing to "Projects." Notelian automatically adds a "← Tasks" back-relation property to each Project entry, showing all Tasks currently linked to that Project.

**Example use case:**
A "Projects" database and a "Tasks" database. Each Task has a Relation property pointing to its parent Project. Looking at a Project entry, the automatically created "← Tasks" back-relation shows all linked Tasks. Looking at a Task entry shows which Project it belongs to.

---

## Property Visibility & Layout in Entry Detail View

When a user opens an entry's detail view (the full page view of a database row), all properties are listed in a vertical column on the left or top of the page — depending on the layout configuration. The content body of the entry occupies the main area.

Users can:
- Reorder properties in the detail view by dragging
- Hide specific properties from the detail view (they remain in Table view)
- Show all hidden properties from a "Show all properties" toggle at the bottom of the property list

Properties that have no value set display a faint placeholder prompt to invite the user to fill them in.

---

## Default Values

For any property, the user can set a **default value** that is automatically pre-filled when a new entry is created. This reduces repetitive data entry.

Examples:
- A "Status" Select property might default to "To Do"
- A "Assignee" Person property might default to the currently logged-in user
- A "Priority" Select property might default to "Medium"
- A "Checkbox" property for "Is Active" might default to checked

Default values are defined in the property settings and apply to all new entries created in any view of that database.

---

## System Properties (Auto-Generated, Read-Only)

In addition to user-created properties, every database entry automatically has four **system properties** that are read-only and cannot be deleted or manually edited. They are managed entirely by Notelian.

| System Property | What It Stores |
|---|---|
| **Created Time** | The exact date and time the entry was created — set once, never changes |
| **Last Edited Time** | The date and time of the most recent edit to any property or block content |
| **Created By** | The workspace member who created the entry (avatar + name) |
| **Last Edited By** | The workspace member who made the most recent edit (avatar + name) |

**Visibility:** System properties are hidden from table columns and card views by default. Users can make them visible from the "Properties" toggle in the view toolbar. They are always visible inside the entry detail view.

**In Filters & Sorts:** System properties work the same as regular properties in filters and sorts — for example: "Filter: Created By is me" or "Sort: Last Edited Time descending."

---

## Property Count Limits (MVP)

In Phase 1, each database supports up to **50 user-created properties** (system properties do not count against this limit). This limit is more than sufficient for any real-world use case and is set to ensure consistent performance in the initial version.

---

*Feature File: step-08-database-properties.md*
*Step: 8 of 12 — Database Properties & Structure*
*Phase: MVP — Phase 1*
