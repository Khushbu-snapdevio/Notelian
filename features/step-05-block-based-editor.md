# Step 5 — Block-Based Editor

## What Is a Block?

A block is the fundamental unit of content in Notelian. Every element on a page — whether it is a paragraph of text, a heading, an image, a to-do checkbox, a code snippet, a divider, or an entire embedded database — is a block. Blocks are independent, movable, and transformable.

This approach means content is never locked into a fixed layout. Users compose pages by stacking, nesting, and rearranging blocks freely. The entire page is a vertical stack of blocks, and each block can optionally contain other blocks nested inside it.

---

## The Slash Command Menu

The primary way to insert a new block is by typing `/` (forward slash) anywhere on a page. This immediately opens the **Block Command Menu** — a floating, searchable popup that lists every available block type.

### How It Works

- The user places their cursor on an empty line or at the end of any line and types `/`
- The command menu appears instantly, showing all block categories and types
- The user can continue typing after `/` to filter the list in real time — for example, typing `/image` narrows results to image-related blocks
- Pressing `Enter` or clicking a result inserts the selected block at the cursor position
- Pressing `Escape` or clicking away closes the menu without inserting anything

### Why This Matters

The slash command eliminates toolbar hunting. Users keep their hands on the keyboard, think in terms of what they want to express, and insert the right block type without breaking their writing flow.

---

## Block Categories & Types

Notelian organizes all blocks into logical categories inside the slash command menu for easy discovery.

### Text Blocks

These are the most commonly used blocks for writing and organizing content.

- **Text (Paragraph)** — Standard body text. The default block type when a user starts typing. Supports all inline formatting.
- **Heading 1** — Large, prominent heading. Used for major page sections. Rendered significantly larger than body text.
- **Heading 2** — Medium heading. Used for subsections within a Heading 1 area.
- **Heading 3** — Small heading. Used for detailed sub-subsections.
- **Bulleted List** — An unordered list item with a round bullet point. Supports unlimited nesting through indentation.
- **Numbered List** — An ordered list item. Numbers auto-increment and automatically adjust when items are reordered or nested.
- **Toggle List** — A collapsible block with a clickable triangle arrow. When closed, the nested content inside is hidden. When opened, the nested blocks are revealed. Ideal for FAQs, outlines, and long reference material.
- **Quote** — A styled block for pull quotes or notable excerpts. Displayed with a distinct left border accent and slightly different typography to set it apart from body text.
- **Callout** — A highlighted box with an optional leading icon (any emoji). Used to draw attention to tips, warnings, important notes, or key information. The background color of the callout can be customized.
- **Divider** — A full-width horizontal rule that creates a visual break between sections of a page. Has no content — it is purely structural.

### Task Block

- **To-Do** — A checkbox item for task tracking. The checkbox is clickable — clicking it marks the task as done, visually striking through the text. Unchecked and checked states are tracked and stored.

### Media Blocks

Blocks that display rich media content inline within a page.

- **Image** — Upload an image file from the device, paste an image URL, or drag and drop an image directly onto the page. Supports a caption beneath the image. Resize handles allow adjusting the display width. Images are stored and served by Notelian's own file storage. **Max upload size: 10 MB.** Supported formats: JPEG, PNG, GIF, WebP, SVG.
- **Video** — Embed a video by pasting a URL (YouTube, Vimeo, and similar platforms are supported) or by uploading a video file. The video player is rendered inline and plays directly on the page without navigating away. **Max direct upload size: 200 MB.** URL embeds have no size limit.
- **Audio** — Embed an audio file by URL or direct file upload. Displays an inline audio player with play, pause, and seek controls. **Max upload size: 50 MB.** Supported formats: MP3, WAV, OGG, M4A.
- **File** — Attach any file type (PDF, spreadsheet, ZIP archive, presentation, etc.) as a downloadable attachment. The block displays the file name, type icon, and file size. Clicking downloads the file. **Max upload size: 100 MB per file.**

### Organizational Blocks

Blocks used to structure and navigate long pages.

- **Table of Contents** — Automatically reads all Heading 1, Heading 2, and Heading 3 blocks on the current page and generates a linked outline. Each entry in the table of contents is a clickable link that scrolls to the corresponding heading. Updates dynamically as headings are added, removed, or renamed.
- **Simple Table** — A basic, inline table for displaying structured information in rows and columns. This is not a full database — it has no filtering, sorting, property types, or views. It is suitable for simple comparison grids or structured reference data.
- **Column Layout** — Allows users to arrange blocks side by side. Users can create two-column, three-column, or more layouts on a page. Any block type can be placed inside a column.

### Code & Math Blocks

- **Code** — A dedicated block for displaying formatted source code. Supports syntax highlighting for a wide range of programming languages (the user selects the language from a dropdown). Includes a copy-to-clipboard button in the top right corner. Content inside a code block is not treated as rich text — it is displayed monospaced and verbatim.
- **Equation** — Renders a mathematical equation using LaTeX notation. The user types the LaTeX expression and the block displays the rendered, typeset formula. Used for technical and academic documentation.

### Reference Blocks

- **Linked Page** — Creates a reference link to another page within the workspace. Displayed as a styled card showing the linked page's title and icon. Clicking navigates to that page.
- **Database** — Embeds a full Notelian database inline on any page. The database can be displayed in Table, Board, Calendar, or Gallery view. Creating a database block either creates a new database or links an existing one. See the Databases & Views feature file for full details.

---

## Inline Text Formatting

Within any text block, users select text to reveal a floating **Format Toolbar** that appears above the selection. The following inline formats are available:

- **Bold** — Makes selected text heavier. Keyboard shortcut: `Ctrl+B` / `Cmd+B`
- **Italic** — Tilts selected text. Keyboard shortcut: `Ctrl+I` / `Cmd+I`
- **Underline** — Underlines selected text. Keyboard shortcut: `Ctrl+U` / `Cmd+U`
- **Strikethrough** — Draws a horizontal line through selected text. Used to show deleted or cancelled content.
- **Inline Code** — Displays selected text in a monospaced font inside a subtle rounded background. Used for technical terms, file paths, or short code snippets within a sentence.
- **Text Color** — Changes the color of selected text from a predefined palette of accessible colors.
- **Highlight** — Applies a background color behind selected text from a predefined color palette. Useful for marking important phrases.
- **Hyperlink** — Converts selected text into a clickable link. Opens a URL input popover. The link opens in a new tab by default.
- **Turn Into** — Converts the current block to a different block type while preserving the text content.
- **Comment** — Attaches an inline comment thread to the selected text. A subtle underline indicates commented text.

---

## Markdown Shortcuts

Notelian automatically converts common Markdown patterns into their corresponding block types as the user types. This allows Markdown-fluent users to compose content at full speed without using the slash command menu.

| Typed Pattern | Result |
|---|---|
| `# ` + Space | Heading 1 |
| `## ` + Space | Heading 2 |
| `### ` + Space | Heading 3 |
| `- ` or `* ` + Space | Bulleted List |
| `1. ` + Space | Numbered List |
| `> ` + Space | Quote block |
| `---` + Enter | Divider |
| `[ ]` + Space | To-Do (unchecked) |
| `[x]` + Space | To-Do (checked) |
| `**text**` | Bold inline |
| `*text*` | Italic inline |
| `` `text` `` | Inline Code |

---

## Block Operations

Every block has a set of standard operations. These become accessible by hovering over a block, which reveals a **drag handle** (six-dot icon) and an **options button** on its left side.

### Drag & Reorder

Users click and hold the drag handle to drag any block to a new position. While dragging, a blue insertion line appears to show where the block will land. Blocks can be:
- Moved above or below any other block on the same page
- Moved into a column layout
- Moved into or out of a toggle block or nested list

### Block Context Menu

Clicking the options button or right-clicking any block opens a context menu with the following actions:

- **Delete** — Permanently removes the block from the page. Can be undone with `Ctrl+Z`.
- **Duplicate** — Creates an exact copy of the block, including all nested content, immediately below the original.
- **Turn Into** — Opens a submenu listing all compatible block types. Converts the block without losing text content. For example, a Paragraph can become a Heading 1, a Bulleted List can become a Numbered List, a Heading can become a Quote.
- **Copy Block Link** — Copies a direct URL to this specific block. Pasting this URL in any browser navigates directly to the block on its page.
- **Move To** — Opens a page search dialog. The user selects a destination page and the block (along with all nested blocks) is moved there.
- **Comment** — Opens the inline comment thread panel for the block.
- **Color** — Opens color picker options for the block's text color and background color.

### Nesting Blocks

Certain block types can hold other blocks nested inside them:

- **Toggle List** — Content nested inside is revealed or hidden when the toggle is opened or closed
- **Bulleted List, Numbered List** — Nested list items create visual indentation for sub-items
- **Callout** — Other blocks (including other callouts) can be nested inside
- **Column Layout** — Any block type can be placed inside each column

To nest a block, the user presses `Tab` while the cursor is inside it — this indents the block into the one above it. Pressing `Shift+Tab` outdents it back to the parent level.

---

## Multi-Block Selection

Users can select multiple blocks at once to perform bulk operations.

- **Click and drag** across blocks to select a range
- **Click one block, then Shift+Click another** to select everything between them
- Once multiple blocks are selected, the user can: Delete all, Duplicate all, Move all to another page, or change Color for all simultaneously

---

## Keyboard Navigation & Shortcuts

Notelian's editor is designed for full keyboard operation.

| Action | Shortcut |
|---|---|
| New block below | `Enter` |
| Delete empty block | `Backspace` |
| Indent block (nest) | `Tab` |
| Outdent block | `Shift+Tab` |
| Undo | `Ctrl+Z` / `Cmd+Z` |
| Redo | `Ctrl+Shift+Z` / `Cmd+Shift+Z` |
| Open slash command | `/` |
| Select all on page | `Ctrl+A` / `Cmd+A` |
| Move block up | `Ctrl+Shift+↑` / `Cmd+Shift+↑` |
| Move block down | `Ctrl+Shift+↓` / `Cmd+Shift+↓` |

---

## Auto-Save

Notelian saves all content **automatically and continuously**. There is no "Save" button and no manual save action. Every keystroke, block change, and property edit is persisted to the server within milliseconds.

- A subtle **"Saving…" → "Saved"** status indicator appears in the top-right area of the page when a write is in progress and when it completes. It is hidden once saved — it does not linger.
- If the user loses internet connectivity, edits are held in a local queue and synced automatically when the connection is restored. A "Offline — changes will sync when reconnected" banner appears during disconnection.
- There is no draft state — every edit is the live saved state.

## Undo & Redo

`Ctrl+Z` / `Cmd+Z` undoes the most recent action. `Ctrl+Shift+Z` / `Cmd+Shift+Z` redoes it.

- **Undo history is per-session and per-page** — up to **200 steps** are tracked per page while the current session is open.
- Undo history is **not persisted** across sessions — closing and reopening a page resets the undo stack. (Version history across sessions is a Phase 2 feature.)
- Pressing Undo at the very beginning of the undo stack (nothing left to undo) has no effect — there is no looping behavior and no error is shown.

## File Block — Deletion & Replacement

For the **File**, **Image**, **Video**, and **Audio** blocks, the uploaded file is attached to the block. To remove or replace the uploaded content:
- **Delete block** — Deleting the block removes the block and its uploaded file from the page. The file is removed from Notelian's storage.
- **Replace file** — Hovering the block reveals a "Replace" button that opens the file picker again, replacing the current file with a new upload. The old file is deleted from storage.
- Renaming a file block's caption does not affect the underlying file.

## Block Consistency Principle

A core design rule of Notelian: **blocks behave identically everywhere.** Whether a block is inside a page, inside a database entry's detail view, inside a template, or inside a toggle — the editing experience, keyboard shortcuts, slash command menu, and context menu are always the same.

This consistency means users learn the editor once and apply that knowledge in every context across the product.

---

*Feature File: step-05-block-based-editor.md*
*Step: 5 of 12 — Writing & Editing with Blocks*
*Phase: MVP — Phase 1*
