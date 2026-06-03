# Step 10 — Comments & Mentions

## Overview

Comments and mentions are the primary tools for communication and collaboration within Notelian's content. Rather than switching to a separate messaging tool, team members can leave feedback, ask questions, assign tasks, and call each other's attention directly within the relevant page or block — keeping context tightly coupled to the content it refers to.

Comments are threaded, block-level, and resolvable. Mentions use the `@` symbol to reference people, pages, and dates — creating live connections between content and collaborators.

---

## Inline Block Comments

### What Is a Block Comment?

A block comment is a comment attached to a specific block on a page. It creates a tight link between the feedback and the exact piece of content it refers to — so there is never any ambiguity about what the comment is about.

### How to Add a Comment on a Block

There are two ways to open the comment panel for a block:

1. **Hover and click** — When hovering over any block, a comment icon appears in the block's left action bar. Clicking it opens the comment thread panel for that block.
2. **Right-click context menu** — Right-clicking any block shows the context menu, which includes a "Comment" option.
3. **Select text and comment** — The user selects a specific portion of text within a text block. The floating format toolbar appears, which includes a "Comment" button. This creates a comment anchored to the selected text range (displayed with a subtle underline on the text).

### Writing and Submitting a Comment

Once the comment panel is open:
- A text input appears for composing the comment
- The user types their message — @mentions and text formatting (bold, italic, inline code) are supported within comment text
- Pressing `Ctrl+Enter` / `Cmd+Enter` (or clicking a Send button) submits the comment
- The comment appears in the thread panel attached to that block

### Comment Visibility

- Comments are visible to all workspace members who have at least "Can Comment" access to the page
- Comments appear as a highlighted indicator on the block (a small icon or underline) — the comment text is not displayed inline to avoid cluttering the reading experience
- The full thread is shown in the comment panel that appears when the comment icon is clicked

---

## Comment Threads

### Replies

Any comment can receive replies, forming a thread. Replying to a comment:
- Keeps the conversation organized in one place
- Notifies all previous participants in the thread
- Allows an ongoing discussion without creating separate comment instances on the same block

To reply, the user clicks "Reply" beneath any existing comment in the thread, types their response, and submits.

**Thread depth:** Comment threads are **one level deep only** — replies can be added to any top-level comment, but replies cannot have replies themselves. This keeps threads readable and prevents deeply nested discussions. If a user wants to reply to a reply, they reply to the thread at the top level (the thread structure groups all replies together under the original comment).

### Editing Comments

A comment's author can edit their own comment after submitting — **at any time, with no time limit**. Edited comments display an "Edited" indicator next to the timestamp so readers know the content was changed after original posting.

### Deleting Comments

A comment's author can delete their own comment. Workspace admins can delete any comment. Deleting the first comment in a thread while replies exist shows a placeholder ("Comment deleted") rather than removing the thread entirely — preserving the context of the replies.

### Resolving Comments

When a comment thread has been addressed — the question answered, the feedback incorporated, the issue resolved — any participant (or any user with edit access to the page) can **resolve** the thread.

**What happens when a comment is resolved:**
- The comment thread is marked as resolved and visually collapsed
- The highlight indicator on the block is removed (the block looks clean again)
- Resolved comments are not deleted — they are moved to a "Resolved" section visible via a toggle in the comment panel
- Resolved comments can be reopened if needed (clicking "Reopen")

This resolved/unresolved system allows teams to use comments as an asynchronous review workflow — reviewers leave comments, authors address them one by one and resolve each thread, until all comments are resolved and the page is approved.

---

## Text-Level Comments

Beyond block-level comments, users can leave comments on a specific selected range of text within a text block. These **text-level comments** work similarly to Google Docs suggestions:

- Selected text is highlighted with a colored underline
- The comment thread is attached to that exact text span
- If the text is later edited or deleted, the comment anchor adjusts to remain attached to the closest remaining text (or becomes unanchored if the text is fully removed)
- Multiple different text ranges on the same block can each have their own comment thread

---

## Mentions

Mentions use the `@` symbol to create live references to people, pages, and dates — within any text block or within comment text.

### People Mentions (`@name`)

Typing `@` followed by a teammate's name in any text block or comment opens a member picker showing matching workspace members and guests. **Guest visibility in the picker is context-sensitive:**
- On pages a guest has been explicitly granted access to, that guest appears in the `@` mention suggestions for that page and its comments
- On pages the guest does NOT have access to, they do not appear in suggestions at all
- Workspace members (Admin / Editor / Viewer) always appear in suggestions on all pages they can access

Selecting a member inserts a mention that:

- Displays as the person's name with a subtle visual treatment (highlighted or bold)
- Sends a notification to the mentioned user
- In comments specifically, the mentioned person receives a notification that they were called out in a specific discussion on a specific page

People mentions are the primary way to direct a teammate's attention to something without leaving the content.

### Page Mentions (`@page name`)

Typing `@` also searches page titles in addition to member names. Selecting a page inserts a **page mention** — a clickable inline link that opens that page when clicked.

Page mentions differ from a plain hyperlink in that they are **live references**:
- If the linked page is renamed, the mention text automatically updates to reflect the new name
- If the linked page is deleted, the mention displays a "Page not found" indicator

Page mentions are used to cross-reference related content — linking a task database entry to the relevant project brief, or linking a meeting notes page to the relevant project tracker.

### Date Mentions (`@date`)

Typing `@` followed by a date (or a relative term like `@today`, `@tomorrow`, `@next Monday`) inserts a **date mention**. Date mentions:
- Display as a formatted, clickable date
- Can open a date context (showing nearby entries or events on that date — Phase 2 feature)
- Serve as in-context date anchors in documents (e.g., "The deadline is @December 15, 2025")

---

## Page-Level Comments

Separate from block-level comments, pages have a **Page Comment** section — a general comment thread not attached to any specific block.

**Location:** The Page Comment section appears **at the bottom of the page**, below all content blocks. It is always visible when scrolling to the end of a page. It is also accessible from a "Comments" button in the top-right page action bar, which scrolls the user down to the section.

Page-level comments are useful for:
- General feedback about the page as a whole
- Approval requests or status updates
- Questions that refer to the page overall rather than a specific section

---

## Notifications Triggered by Comments & Mentions

Every comment and mention action automatically creates a notification for the relevant user. Events that trigger notifications from this feature:

- An **@mention** of a person in any page or comment
- A **reply** added to a thread the user participated in
- A **new comment** added to a page the user created
- A comment thread the user is part of being **resolved or reopened**

The full Notification Center spec — panel layout, card format, read/unread state, in-app toasts, and email digest settings — is in:

→ **[Step 12 — Notification Center](step-12-notifications.md)**

---

*Feature File: step-10-comments-and-mentions.md*
*Step: 10 of 12 — Comments, Mentions & Collaboration*
*Phase: MVP — Phase 1*
