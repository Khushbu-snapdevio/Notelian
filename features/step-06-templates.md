# Step 6 — Templates

## What Are Templates?

Templates in Notelian are pre-built page and database structures that users can apply with a single click to start a new page with useful content already in place. Instead of building a meeting notes format, a project tracker, or a personal journal layout from scratch every time, users select a template and a fully structured page is created for them — ready to fill in.

Templates save time, establish consistency, and lower the barrier to entry for users who are new to the tool or exploring a new use case.

---

## Types of Templates

Notelian has two kinds of templates:

### Built-In Templates (System Templates)
A curated gallery of templates created and maintained by the Notelian team. These cover the most common use cases and are available to every user in every workspace. They are tested, well-structured, and designed to be immediately useful without modification.

### Custom Templates (User Templates)
Any page or database that a user saves as a template. These are stored within the user's workspace and available to all members of that workspace. Custom templates allow teams to create standardized formats for their specific workflows, naming conventions, and content structures.

---

## The Template Gallery

The template gallery is a browsable collection of all available built-in templates. It is accessible from:
- The "New Page" dialog — when creating a new page, the user can select "Start with a template" to open the gallery
- A dedicated "Templates" section in the sidebar (accessible via the quick actions area)
- The slash command menu within a page (`/template`)

### Gallery Layout

The gallery displays templates organized into categories. Each template is represented by a card showing:
- Template title
- A short description of what it is used for
- A visual thumbnail preview of the template structure
- The category it belongs to

Users can browse categories or search the gallery by keyword.

### Gallery Categories

Templates are organized by use case category:

**Personal**
- Daily Journal — Dated entry for personal reflection, mood tracking, and daily notes
- Weekly Planner — Seven-day planning layout with goals, tasks, and notes for the week
- Reading List — Database tracking books to read, currently reading, and completed reads with rating and notes properties
- Habit Tracker — Database for tracking daily habits with checkbox entries and streak counting

**Productivity**
- Meeting Notes — Standard meeting structure with attendees, agenda, discussion points, decisions, and action items
- Task List — Simple to-do page with grouped tasks by status and a checkbox property
- Project Brief — Structured document covering project overview, goals, scope, timeline, stakeholders, and risks
- OKR Tracker — Objectives and Key Results database with progress tracking properties

**Project Management**
- Project Tracker — Full-page database with entries for tasks, a Board view for Kanban, and a Table view for status management
- Sprint Board — Kanban board database with columns for Backlog, To Do, In Progress, In Review, and Done
- Bug Tracker — Database with properties for severity, status, reporter, assignee, and reproduction steps
- Content Calendar — Calendar view database for planning and scheduling content with properties for type, channel, status, and author

**Team & Knowledge**
- Team Wiki — Nested page structure for team documentation with sections for onboarding, processes, tools, and guidelines
- Company Handbook — Top-level page with subpages for mission, values, policies, benefits, and team structure
- Meeting Agenda — Pre-structured agenda template with numbered items, time allocations, and owner fields
- Decision Log — Database for tracking important decisions with rationale, alternatives considered, and date properties

**Personal CRM**
- Contact Database — People database with properties for company, role, relationship strength, last contact date, and notes
- Company Tracker — Company database with properties for industry, size, relationship type, and linked contacts

---

## Using a Built-In Template

1. The user opens the template gallery through any of the access points described above
2. The user browses categories or searches for a keyword
3. The user clicks on a template card to see a full preview of the template structure
4. Clicking "Use Template" creates a new page in the workspace pre-filled with all the template content
5. The new page appears in the sidebar and is immediately editable — the user fills in the placeholder content, renames sections, or adjusts the structure as needed

The original template is not modified. The user receives a completely independent copy.

---

## Creating Custom Templates

Any page in Notelian can be saved as a custom template. This is useful for:
- Repeatable meeting notes formats specific to a team's style
- Standard database structures the team uses for every project
- Onboarding document skeletons for new team members
- Report templates with pre-filled section headings and guiding text

### How to Save a Page as a Template

1. The user builds and structures the page exactly as they want the template to appear — including headings, placeholder text, block structure, database columns, property types, and any guiding notes
2. The user opens the page options menu (from the `···` button in the top right of the page)
3. Selecting "Save as Template" opens a dialog where the user:
   - Names the template
   - Writes a short description
   - Assigns it to a category (from the workspace's defined categories)
4. Confirming saves the template to the workspace's Custom Templates section

### Accessing Custom Templates

Custom templates appear in the template gallery in a dedicated "Custom" section, clearly distinguished from built-in templates. They are available to all members of the workspace, not just the user who created them.

### Managing Custom Templates

Workspace admins (and the template's creator) can:
- **Edit the template** — Changes to a template's structure only affect pages created from it **in the future**. Pages already created from this template are fully independent copies and are not updated retroactively.
- **Rename or re-describe the template** — Only affects how the template is displayed in the gallery; does not affect existing pages created from it.
- **Delete the template** — Removes it from the gallery. Pages already created from the template are not deleted or modified in any way.

**Template versioning:** There is no versioning system for templates in Phase 1. Each edit to a custom template is immediately live for future use. If teams need to track changes to a template structure, the recommended approach is to duplicate the template before editing (creating a new version manually).

---

## The Template Button

Separate from gallery templates, Notelian supports a **Template Button** block — a special interactive block that can be placed on any page. When clicked, the Template Button creates a new copy of a predefined block structure immediately below the button on the same page.

### Use Case

A meeting notes page might have a Template Button labeled "Add Meeting Entry." Each time the user clicks it, a new dated section appears below with the standard meeting note format — attendees field, agenda, discussion, action items — pre-structured and ready to fill in.

### How It Works

- The user inserts a Template Button block via the slash command (`/template button`)
- Clicking the button opens an editor where the user defines what content should be duplicated each time the button is clicked — this can include any combination of blocks: headings, checklists, tables, database entries, etc.
- The user names the button with a clear action label (e.g., "Add Week", "Log Entry", "New Sprint")
- The user selects the **insertion location** for generated content from two options:
  - **Below the button** (default) — new content appears immediately below the Template Button block each time it is clicked
  - **Bottom of page** — new content is always appended at the very end of the page, regardless of where the button sits in the page
- Once saved, clicking the button on the page creates a fresh copy of the defined content at the configured insertion location

**Note on custom templates in onboarding:** Custom templates created by workspace members are available in the Template Gallery within the workspace immediately after creation. They do NOT appear as options in the onboarding wizard's "Choose a Starting Template" screen (Screen 4) — only built-in system templates are offered during onboarding.

Template Buttons are particularly powerful for log-style pages where a consistent structure needs to be repeated over time — daily standups, weekly reviews, client check-ins, expense reports.

---

## Template Placeholders

Built-in templates and well-designed custom templates use **placeholder text** to guide the user. Placeholder text is visible content that describes what should go in a particular place. It is styled differently from real content (lighter color, italicized) and is meant to be replaced.

Examples of placeholder text in a meeting notes template:
- *Type the meeting objective here*
- *List attendees separated by commas*
- *Record key decisions made during the meeting*

Placeholder text is simply regular text that the user edits or replaces — there is no special placeholder mechanism required.

---

*Feature File: step-06-templates.md*
*Step: 6 of 12 — Using Templates*
*Phase: MVP — Phase 1*
