# Step 12 — Notification Center

## Overview

The Notification Center is the central hub for all activity that is directed at or relevant to the current user. It ensures that no @mention, comment reply, or access grant goes unnoticed — without forcing users to rely on email as the primary communication loop.

Notifications in Notelian are:
- **Contextual** — each notification links directly to the exact page and block where the activity happened
- **Actionable** — users can mark read, dismiss, or navigate from the notification itself
- **Configurable** — each user controls their own email notification frequency independently

---

## Opening the Notification Center

There are two ways to open the Notification Center:

1. **Sidebar bell icon** `🔔` — always visible in the Quick Action Buttons section below the workspace header. A red badge shows the count of unread notifications when there are any.
2. **Keyboard shortcut** — `Ctrl+Shift+N` / `Cmd+Shift+N` opens the Notification Center panel from anywhere in the app.

The Notification Center opens as a **slide-in panel from the right side** — it does not navigate away from the current page. The user can read and act on notifications while remaining in their current context.

---

## Notification Triggers

A notification is created and delivered to the relevant user when any of the following events occur:

### Direct Mentions
- A workspace member types `@[their name]` in any page, block, or comment
- Triggers: an immediate in-app notification + email (based on their email preference setting)

### Comment Activity
- **New comment on a page they created** — when someone leaves the first comment on a page the current user created
- **Reply in a thread they participated in** — when someone replies to a comment thread the user previously commented in (including threads they started)
- **Comment on a page they are watching** — when comments are added to pages the user has explicitly marked as "watching" (Phase 2 feature — in Phase 1, this only applies to pages the user created)

### Thread Status Changes
- **Thread resolved** — when a comment thread the user is part of is marked as resolved
- **Thread reopened** — when a previously resolved thread is reopened

### Access & Sharing
- **New page access granted** — when someone shares a page with the user and grants them access (any access level)
- **Guest invite accepted** — when a guest the user invited accepts and joins (shown to the inviting user)

### Workspace Events
- **Added to workspace** — when an admin adds the user to a workspace via email invitation and the user accepts

---

## The Notification Center Panel

### Layout

The panel has three sections:

**Header**
- Title: "Notifications"
- **"Mark all as read"** button — clears all unread badges at once
- Filter dropdown — see Filter section below

**Notification List**
- A reverse-chronological list of all notifications (newest at the top)
- Each entry is a notification card (see Notification Card below)
- Infinite scroll — older notifications load as the user scrolls down
- Empty state message when there are no notifications: "You're all caught up — no new notifications."

**Footer**
- Link to **Notification Settings** (opens Account Settings → Notifications tab)

---

### Notification Card

Each notification in the list is displayed as a card with the following elements:

- **Avatar** — the profile image of the person who triggered the notification
- **Summary line** — a short human-readable description of the action. Examples:
  - "**Jamie Chen** mentioned you in a comment on *Q3 Planning*"
  - "**Alex Rivera** replied to your comment in *Sprint 7 → Bug List*"
  - "**Morgan Lee** shared *Marketing Brief* with you — Can Edit"
  - "**Taylor Kim** resolved a thread in *Product Roadmap*"
- **Page path** — the breadcrumb location of the relevant page (e.g., `Projects → Q3 → Marketing`)
- **Content snippet** — a short preview of the comment or relevant content (up to 100 characters), truncated with "…"
- **Timestamp** — relative time (e.g., "2 minutes ago", "Yesterday", "Jun 1")
- **Unread indicator** — a blue dot on the left edge of the card for unread notifications

**Card Actions (on hover):**
- **"Go to source"** (or clicking the card) — navigates to the exact page and scrolls to the relevant block or comment, highlighting it briefly
- **"Mark as read"** — dismisses the unread indicator without navigating away (appears as an `×` or check icon on hover)

---

## Notification Filters

Users can filter the notification list to focus on a specific type:

| Filter | What It Shows |
|---|---|
| **All** | Every notification, regardless of type (default) |
| **Mentions** | Only @mention notifications |
| **Comments** | Only new comment and reply notifications |
| **Updates** | Thread resolutions, reopens, access grants |

The selected filter is remembered for the current session.

---

## Read / Unread State

- **Unread** — Notifications are unread by default when they arrive. Indicated by a blue dot on the card.
- **Read** — A notification becomes read when the user clicks the card to navigate to it, or manually marks it as read.
- **Mark as Read** — Individual notifications can be marked read by hovering and clicking the check/dismiss icon.
- **Mark All as Read** — A single button in the header marks every notification as read at once.
- The bell icon badge count reflects the number of unread notifications. When all are read, the badge disappears.

---

## In-App Notification Toast (Real-Time)

When the user is actively using the app and a new notification arrives, a **toast notification** briefly appears in the bottom-right corner of the screen. It shows:
- The sender's avatar
- A summary line (same as the notification card)
- A "View" button that opens the Notification Center or navigates directly to the source

The toast auto-dismisses after 5 seconds. Clicking "View" navigates immediately. Clicking elsewhere on the page dismisses the toast early.

**Toast batching:** If multiple notifications arrive within a **5-second window**, they are collapsed into a single batched toast showing the count: "You have 3 new notifications — click to view." The batching window resets after each batch is shown.

---

## Email Notifications

Each user controls how often they receive email notifications from Notelian. This is configured in **Account Settings → Notifications**.

### Email Frequency Options

| Setting | Behavior |
|---|---|
| **Real-time** | An email is sent for every notification event immediately as it occurs |
| **Daily digest** | All activity from the past 24 hours is bundled into one email, delivered at a consistent time each morning (configurable time in settings) |
| **Weekly digest** | All activity from the past 7 days is bundled into one email, delivered on a chosen day of the week |
| **Off** | No email notifications. In-app notifications and toasts only. |

Default for new users: **Daily digest**.

**Timezone handling for digest delivery:**
- Daily and weekly digests are delivered at the scheduled time in the **user's local timezone**, as configured in Account Settings → Appearance → Timezone.
- If no timezone is set by the user, the server uses the timezone detected from the user's browser at signup (stored as the default).
- Users can update their timezone at any time — the next digest delivery will reflect the updated timezone immediately.

### Email Digest Format

The digest email contains:
- Subject line: "Your Notelian notifications — [date or date range]"
- A grouped summary by page: each page with activity is listed once, with all notifications for that page grouped underneath
- Each entry has: sender avatar, summary text, and a direct link to the relevant page/block
- A footer with a one-click link to adjust notification settings

### Email Notification Rules

- Emails are only sent for notifications that have not yet been read in the app (read in-app notifications are excluded from the digest)
- If the user has already navigated to the source of a notification, that notification is considered read and excluded from the next email
- If the digest period has no new unread notifications, no email is sent (no empty digests)

---

## Notification Preferences (Granular Control — Phase 2)

In Phase 1, email frequency is a global setting. In Phase 2, users will be able to configure notifications per event type:

| Event Type | Configurable In Phase 2 |
|---|---|
| @mentions | On / Off |
| Comment replies | On / Off |
| New comments on my pages | On / Off |
| Thread resolved | On / Off |
| Access granted | On / Off |
| Workspace events | On / Off |

Phase 1 ships with global on/off per delivery channel (in-app always on; email configurable by frequency).

---

## Notification Retention

- Notifications are retained for **90 days** in Phase 1
- After 90 days, notifications are automatically removed from the Notification Center by a nightly pg-boss cleanup job (the underlying activity — the comment, the mention — still exists on the page; only the notification record is removed)
- Read notifications older than 30 days are displayed with a muted visual style to help users distinguish recent from older activity
- **Export / archive:** There is no notification export or archive feature in Phase 1. Notifications older than 90 days are permanently deleted with no historical record. Users who need to reference old discussions should consult the relevant page's comment history directly.

## Notification Delivery Reliability

Email notifications are delivered via pg-boss job queue:
- Each email notification job is written to the PostgreSQL job queue transactionally — if the notification record is saved, the email job is guaranteed to be queued (no phantom jobs or silent losses)
- Failed email jobs are retried up to **3 times** with exponential backoff before being marked as failed
- Failed jobs are logged for monitoring; the Notelian team is alerted on repeated delivery failures
- In-app notifications are delivered via server-sent events (SSE) while the user is active. If the user is offline when a notification is created, it is retrieved the next time they open the Notification Center

---

## Notification Bell Badge Behavior

| State | Badge Display |
|---|---|
| 0 unread notifications | No badge shown |
| 1–9 unread | Shows the exact count (e.g., `3`) |
| 10–99 unread | Shows the exact count (e.g., `47`) |
| 100+ unread | Shows `99+` |

The badge updates in real time — as new notifications arrive while the user is using the app, the count increases without requiring a page refresh.

---

## Technical Implementation — Background Job Processing

Email notification delivery and digest batching are handled asynchronously using **pg-boss**, a PostgreSQL-backed job queue. All background work runs within the existing PostgreSQL infrastructure — no separate Redis or external queue service is required in Phase 1.

### How pg-boss Is Used

**Transactional email (real-time setting):**
- When a notification event is created (mention, reply, access grant), a job is immediately enqueued in pg-boss
- A background worker picks up the job and delivers the email via the configured transactional email provider
- Failed email jobs are retried with exponential backoff (up to 3 retries before being marked as failed)

**Daily digest processing:**
- A recurring pg-boss scheduled job runs at the user's configured digest delivery time
- The job queries all users with Daily Digest enabled, collects their unread notifications from the past 24 hours, composes the digest email, and sends it
- Users with no unread notifications in the period are skipped — no empty emails are sent

**Weekly digest processing:**
- Same pattern as daily digest — runs once per week at the user's configured delivery day and time

**Notification cleanup:**
- A scheduled pg-boss job runs nightly to remove notifications older than 90 days from the database, keeping the notifications table lean

### Why pg-boss

pg-boss stores job state directly in PostgreSQL, giving the team:
- Job persistence across server restarts (no lost jobs)
- Visibility into queued, running, completed, and failed jobs from the same database tooling
- Transactional job enqueuing — a notification job is only enqueued if the notification record was successfully written (no phantom jobs from failed writes)
- No additional infrastructure to operate in Phase 1

---

*Feature File: step-12-notifications.md*
*Step: 12 of 12 — Notification Center*
*Phase: MVP — Phase 1*
