# Database Properties

## Overview

Properties are typed columns that define the structure of a database. Every database entry has a title plus any number of properties that store structured data — dates, numbers, assignees, links to other databases, and more. Properties are defined once on the database and apply to all entries.

---

## User Stories

- As a user, I want to add a Status property so I can track the state of each entry.
- As a team lead, I want a Person property so I can assign entries to team members.
- As a user, I want a Relation property to link tasks to their parent projects across databases.
- As a user, I want to set default values so new entries are pre-filled sensibly.
- As a user, I want to hide properties I don't need so the view stays clean.

---

## Managing Properties

**Add a property:**
- In Table view: click the `+` button at the right edge of the column headers
- In entry detail view: click `"+ Add a property"`
- Choose a property type, enter a name, configure options

**Edit a property:**
- Click the property column header → `"Edit property"`
- Change name, type, format, or options
- Changes apply immediately across all entries

**Rename a property:** Click the header → rename inline.

**Change type:** Click header → `"Edit property"` → change type. Existing values are converted where possible (see conversion table below).

**Reorder:** Drag the column header in Table view, or drag the property row in the entry detail panel.

**Hide:** Toggle visibility per view via the view's `"Properties"` settings. Hidden properties still exist — they just aren't shown in that view. Entry detail always shows all properties.

**Delete:** Removing a property permanently deletes all values stored in that property across all entries. Requires confirmation.

**Max properties:** 50 user-created properties per database.

---

## Property Types

### Text

- Plain text, multi-line supported
- Searchable in global search
- **Filters:** Contains, Does not contain, Is, Is not, Starts with, Ends with, Is empty, Is not empty

---

### Number

- Stores numeric values (integer or decimal)
- **Format options:**

| Format | Example |
|--------|---------|
| Number (plain) | 1,234.56 |
| Currency — USD | $1,234.56 |
| Currency — EUR | €1,234.56 |
| Percentage | 75% |
| Scientific | 1.23e3 |

- **Filters:** `=`, `≠`, `<`, `>`, `≤`, `≥`, Is empty, Is not empty

---

### Select

- Single option chosen from a predefined list
- Options are color-coded badges
- Add new options inline by typing a new value in any entry
- Reorder, rename, or change option colors from property settings
- Deleting an option removes it from all entries that had that value

**Filters:** Is, Is not, Is any of, Is none of, Is empty, Is not empty

---

### Multi-Select

- Multiple options chosen from a predefined list
- Displayed as multiple color-coded badges per entry
- Add, rename, reorder options the same as Select
- Deleting an option removes it from all entries that included it

**Filters:** Contains, Does not contain, Is empty, Is not empty

---

### Date

- Stores a single date or a date range (start + end)
- Optional time component (hour + minute)
- **Date formats:** Month/Day/Year, Day/Month/Year, Year/Month/Day
- **Time format:** 12-hour or 24-hour
- Timezone: stored in UTC, displayed in user's local timezone
- Relative display: Today, Yesterday, Tomorrow, "in 3 days", "3 days ago"

**Filters:** Is, Is before, Is after, Is between, Is empty, Is not empty

---

### Checkbox

- Boolean toggle (checked / unchecked)
- Clickable directly from Table view or Board card
- Default value: unchecked

**Filters:** Is checked, Is not checked

---

### URL

- Stores a web URL; displayed as a clickable link
- Optional validation (checks for valid URL format on entry)
- Opens in a new tab

**Filters:** Contains, Does not contain, Is, Is not, Is empty, Is not empty

---

### Email

- Stores an email address; displayed as a `mailto:` link
- Clicking opens the user's default email client

**Filters:** Contains, Does not contain, Is, Is not, Is empty, Is not empty

---

### Phone

- Stores a phone number; displayed as a `tel:` link
- No formatting enforced — any string accepted
- Clicking opens the phone dialer on mobile

**Filters:** Contains, Does not contain, Is, Is not, Is empty, Is not empty

---

### Person

- References workspace members by name / avatar
- Multiple members can be selected per entry
- Special value: `@me` — resolves to the current user at the time the entry is created (useful as a default for "Assignee")
- Displays member avatar + name; clicking opens their profile

**Filters:** Is, Is not, Is any of, Includes me (`@me`), Is empty, Is not empty

---

### Relation

Creates a bidirectional link between entries across databases (or within the same database).

**Setup:**
1. Add a Relation property → select the **target database**
2. Optionally name the back-relation (the property that appears in the linked database pointing back to this one)
3. The back-relation is automatically created in the target database as a read-only Relation property

**Display:** Shows linked entry titles as clickable chips. Click a chip to open that entry.

**Behavior:**
- Adding a relation on entry A automatically adds A to the back-relation on entry B (both sides written in one transaction)
- If the target entry is **permanently deleted** (hard delete), the delete transaction must also scrub the entry's id from all `property_values` rows of type `relation` across all databases — both sides are cleaned in the same atomic transaction so counts and filters stay correct. If the target entry is **trashed** (soft-deleted), the relation chip shows `"Entry in Trash"` instead; the id is scrubbed only when `auto-delete-expired-trash` permanently removes the entry (same logic as hard delete, executed in the job's transaction)
- Back-relation property is read-only — cannot be renamed or edited from the target database; deleting the source Relation property removes the back-relation and both sides' values in one transaction

**Filters:** Contains (entry), Does not contain, Is empty, Is not empty

---

## Property Type Conversion Rules

When changing a property's type, existing values are converted where possible.

| From → To | Result |
|-----------|--------|
| Text → Number | Numeric strings converted; non-numeric values cleared |
| Text → Select | Each unique text value becomes a new option; entry gets that option |
| Text → Date | Dates in ISO format parsed; others cleared |
| Number → Text | Number formatted as string |
| Select → Multi-Select | Single option becomes a one-item multi-select value |
| Multi-Select → Select | First selected option is kept; additional options cleared |
| Select → Text | Option name becomes text value |
| Date → Text | Date formatted as string |
| Checkbox → Select | `true` → "Yes", `false` → "No" options created |
| Any → Relation | All existing values cleared (no conversion possible) |
| Any → Person | All existing values cleared |

---

## Default Values

Properties can have a default value that is pre-filled when a new entry is created.

| Property Type | Default value options |
|--------------|----------------------|
| Text | Any static text |
| Number | Any number |
| Select | Any existing option |
| Multi-Select | Any existing options |
| Date | Today, Tomorrow, or a fixed date |
| Checkbox | Checked or Unchecked |
| Person | `@me` (current user) or a fixed member |

---

## System Properties

Auto-generated, read-only properties that exist on every database entry.

| Property | Description |
|----------|-------------|
| Created Time | Timestamp when the entry was first created |
| Last Edited Time | Timestamp of the most recent edit |
| Created By | The user who created the entry |
| Last Edited By | The user who made the most recent edit |

System properties are hidden by default. Show them in the entry detail view via `"Properties"` → toggle on.
They can be used in filters and sorts.

---

## Data Model

> **Title is not in this table.** The entry title is `pages.title` (entries are pages) and is surfaced as a virtual position-1 property — see business rule 1. `database_properties` holds only user-created properties plus auto-generated system / back-relation properties.

```
DatabaseProperty
├── id                  (uuid, primary key)
├── database_id         (foreign key → Database)
├── name                (string, required)
├── type                (enum: text | number | select | multi_select | date |
│                        checkbox | url | email | phone | person | relation)
├── config              (jsonb — type-specific options:
│                        number: { format }
│                        select/multi_select: { options: [{ id, name, color }] }
│                        date: { include_time, date_format, time_format }
│                        relation: { target_database_id, back_relation_id, back_relation_name }
│                        person: { allow_multiple })
├── default_value       (jsonb, nullable)
├── is_hidden           (boolean, default: false — default visibility at property creation;
│                        per-view override stored in database_views.hidden_property_ids)
├── is_system           (boolean, default: false — true for Created Time, Last Edited Time,
│                        Created By, Last Edited By; system properties don't count toward the 50 limit)
├── is_back_relation    (boolean, default: false — true for auto-created reverse side of a
│                        Relation property; back-relations are read-only and don't count toward the limit)
├── order_index         (integer)
├── created_at          (timestamp)
└── updated_at          (timestamp)

PropertyValue
├── id                  (uuid, primary key)
├── entry_id            (foreign key → DatabaseEntry)
├── property_id         (foreign key → DatabaseProperty)
├── value               (jsonb — type-specific value storage)
└── updated_at          (timestamp)
```

---

## API Endpoints

| Method | Endpoint | Description | Access |
|--------|----------|-------------|--------|
| GET | `/api/databases/:id/properties` | List all properties | Can View+ |
| POST | `/api/databases/:id/properties` | Create a new property | Can Edit+ |
| PATCH | `/api/databases/:id/properties/:propId` | Update property (name, type, config) | Can Edit+ |
| DELETE | `/api/databases/:id/properties/:propId` | Delete a property and all its values | Can Edit+ |
| PATCH | `/api/databases/:id/properties/reorder` | Reorder properties | Can Edit+ |
| GET | `/api/entries/:id/property-values` | Get all property values for an entry | Can View+ |
| PATCH | `/api/entries/:id/property-values/:propId` | Set/update a property value | Can Edit+ |

---

## UI Screens

| Screen | Route / Location | Access |
|--------|----------------|--------|
| Property Manager | Table view header → edit property | Can Edit+ |
| Entry Detail — Properties Panel | Side panel or full-page entry | Can Edit+ |
| Property Type Selector | Modal (when creating/changing type) | Can Edit+ |

---

## Business Rules

1. Every database has a built-in **Title** property that is always visible at column position 1 and cannot be deleted or reordered. **Title is virtual** — it is backed by `pages.title` (each entry is a page), **not** stored as a `database_properties` row or in `property_values`. It does not count toward the 50-property limit, and filters/sorts on Title resolve directly against `pages.title`. Only user-created properties live in `database_properties`. **`GET /api/databases/:id/properties` never returns Title** — clients must synthesize it as a read-only column 1 from the page title separately.
2. A database can have a maximum of 50 user-created properties. System properties and back-relation properties do not count toward this limit. Attempting to create a 51st property returns `400 { error: "property_limit_exceeded", limit: 50 }`. The UI should disable the "Add Property" button and display: *"This database has reached the 50-property limit."*
3. Deleting a property permanently deletes all values for that property across all entries. This cannot be undone.
4. Property type changes that cannot convert values (e.g., any type → Relation, any type → Person) clear all existing values, but only after explicit user confirmation. **API + UX flow:** the initial `PATCH /api/databases/:id/properties/:propId` with a destructive new type (where `affectedValueCount > 0`) returns `400 { error: "destructive_conversion", affectedValueCount: N }`. The UI shows a confirmation dialog: *"Changing to [type] will permanently delete N existing values. This cannot be undone."* with a **Delete N values and change type** button. Re-sending the request with `{ confirmDestructive: true }` executes the type change and `DELETE FROM property_values WHERE property_id = :propId` in a **single atomic transaction**. If `affectedValueCount = 0`, the type change proceeds without a confirmation step.
5. Back-relation properties are auto-created and read-only — they cannot be renamed, reordered, or deleted directly. Deleting the source Relation property removes the back-relation too.
6. Deleting a property that is the active `group_by_property_id` on a Board view is rejected with `400 { error: "property_in_use_as_grouping" }` — the user must change or remove the view's grouping first. Deleting a property referenced in active filter or sort rules removes it from those rules atomically in the same transaction.
7. The `@me` default for Person properties resolves at entry creation time — it is stored as the actual user ID, not as a dynamic reference. **Resolution happens exclusively server-side** in the entry creation handler (`POST /api/databases/:id/entries` or the equivalent server action): the handler reads the property's `default_value` config, substitutes `@me` with the authenticated session's `user_id`, and writes the concrete UUID to `property_values`. Client code must never resolve `@me` — only the server resolves it using the verified session identity.
8. Select option deletion removes that value from all entries holding it. The deleted option cannot be recovered.
9. System properties (Created Time, Last Edited Time, Created By, Last Edited By) are always present and cannot be modified.
10. Hidden properties are hidden per-view — they still store and accept values.
11. Property ordering is shared across all views of the same database (except hidden/visible state, which is per-view).
12. The `back_relation_name` is stored in the source Relation property's `config`. When the back-relation property is auto-created, it uses this name as its `name` field. If `back_relation_name` is null, the back-relation is named `"← [source database name]"` by default.

---

## Out of Scope (MVP)

- Formula property (computed values based on other properties)
- Rollup property (aggregations across Relation)
- File / attachment property (distinct from the File block in entry content)
- Unique ID property (auto-incrementing record numbers)
- Created by / Last edited by as standalone person pickers (currently system-only)
- Property groups / sections for organizing large property lists
