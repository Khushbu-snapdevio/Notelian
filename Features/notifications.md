# Notifications

## Overview

Notifications keep users informed about activity that involves them — @mentions, replies to their comments, and changes to pages they care about. Notelian delivers notifications in-app in real time and via email on a configurable schedule.

**Powered by:** pg-boss (PostgreSQL-backed job queue) — no Redis or separate message broker needed.

---

## User Stories

- As a team member, I want to be notified when someone @mentions me so I can respond quickly.
- As a comment author, I want to know when someone replies to my thread.
- As a page creator, I want to be alerted when a teammate leaves a comment on my page.
- As a user, I want to choose between real-time email and a daily digest so I'm not overwhelmed.
- As a user, I want to see all my unread notifications in one place without leaving the page.

---

## Notification Triggers

| Event | Who is notified |
|-------|----------------|
| @mention in a comment | Mentioned user |
| @mention in page content | Mentioned user |
| New comment on a page you created | Page creator |
| Reply in a thread you participated in | All prior thread participants |
| Comment thread resolved | All prior thread participants |
| Comment thread reopened | All prior thread participants |
| You are added to a workspace | New member |
| You are granted access to a page | The user who was granted access |
| A guest invite you sent is accepted | The inviting user |

---

## In-App Notification Center

**Opening:**
- Click the 🔔 bell icon in the sidebar
- Keyboard shortcut: `Ctrl+Shift+N` / `Cmd+Shift+N`
- Panel slides in from the right side of the screen

**Panel layout:**
```
┌─────────────────────────────────────────────┐
│ Notifications            [Mark all read]    │
│ [All ▾]  [Mentions]  [Comments]  [Updates]  │
├─────────────────────────────────────────────┤
│ ● [Avatar] Sarah mentioned you              │
│   Meeting Notes · Engineering               │
│   "...review the @You section before..."    │
│   2h ago                          [✓ Read] │
├─────────────────────────────────────────────┤
│   [Avatar] Tom replied to your comment      │
│   Sprint Board · Product                    │
│   "Sounds good, let's go with that."        │
│   Yesterday                       [✓ Read] │
├─────────────────────────────────────────────┤
│   [Avatar] System                           │
│   You've been added to Engineering Space    │
│   3 days ago                      [✓ Read] │
└─────────────────────────────────────────────┘
```

---

## Notification Card

Each notification shows:
- **Sender avatar** + name (or "System" for automated events)
- **Summary line** — one sentence describing what happened
- **Location path** — breadcrumb to the page (e.g., `Engineering > Sprint Board`)
- **Content snippet** — up to 100 characters of relevant content (highlighted mention or comment text)
- **Timestamp** — relative (e.g., "2h ago", "Yesterday", "Jun 3")
- **Unread indicator** — blue dot on the left for unread notifications
- **Hover actions** — `"Go to source"` (navigates and highlights the relevant block), `"Mark as read"`

---

## Notification Filters

| Filter | Shows |
|--------|-------|
| All | All notifications (default) |
| Mentions | Only @mention notifications |
| Comments | Comment, reply, and resolution events |
| Updates | Access grants, workspace invites |

---

## Read / Unread State

- Notifications are **unread by default** (blue dot)
- Clicking a notification marks it as read and navigates to the source
- `"Mark as read"` button marks individually without navigating
- `"Mark all as read"` button in the header clears all unread in one click
- The bell icon badge shows unread count:
  - 0 → no badge
  - 1–99 → exact number
  - 100+ → `99+`

---

## Real-Time Toast

When a notification arrives while the user is active in the app, a toast appears in the bottom-right corner.

```
┌────────────────────────────────────────────┐
│ [Avatar] Sarah — Meeting Notes             │
│ "Can you review the intro section @You"    │
│                              [View →]      │
└────────────────────────────────────────────┘
```

- Auto-dismisses after **5 seconds**
- Multiple notifications within 5 seconds are batched: `"3 new notifications"`
- Clicking `"View →"` opens the notification center and the source page

Real-time delivery via **Server-Sent Events (SSE)** while the user is active. If the user is offline or the tab is inactive, notifications are queued and delivered on next notification center open.

---

## Email Notifications

### Frequency Options

| Option | Description |
|--------|-------------|
| Real-time | One email per event, sent immediately |
| Daily digest | One digest email each morning (default) |
| Weekly digest | One digest email on a configurable day |
| Off | No email — in-app and toast only |

**Default:** Daily digest.

### Timezone

- Digest emails are delivered at 8:00 AM in the user's local timezone
- Timezone is set in Account Settings or auto-detected from the browser on first sign-up
- Changes take effect from the next scheduled delivery

### Daily / Weekly Digest Format

```
Subject: Your Notelian activity for Wednesday, Jun 4

== Meeting Notes (Engineering) ==
  → Sarah: "@You can you review before Friday?"
  → Tom replied to your comment: "Will do, thanks!"

== Sprint Board (Product) ==
  → Alex mentioned you: "Assigned to @You per yesterday's discussion"

───────────────────────────────────
View all notifications →  |  Manage email settings →
```

**Rules:**
- Only **unread** notifications are included
- Notifications already read in-app are excluded from the digest
- Empty digests (no unread activity) are not sent
- Notifications are grouped by page

### Real-time Email

- Sent immediately when the triggering event occurs
- One email per notification (not batched)
- Uses the same content as the digest but with a single notification

---

## Notification Preferences

Available at `/settings/notifications`

| Setting | Options |
|---------|---------|
| Email frequency | Real-time / Daily digest / Weekly digest / Off |
| Weekly digest day | Monday / Tuesday / Wednesday / Thursday / Friday / Saturday / Sunday |

Phase 1: global frequency setting only. Granular per-event-type control is a Phase 2 feature.

---

## Notification Retention

- Notifications are retained for **90 days**
- Read notifications older than 30 days are shown greyed out / muted in the panel
- Nightly pg-boss cleanup job permanently deletes notifications older than 90 days
- No manual export or archive in Phase 1

---

## Delivery Reliability

All notification jobs are managed by **pg-boss** within the same PostgreSQL database:

- Notification writes are **transactional** — if the triggering event (comment, @mention) fails to save, no notification is enqueued
- Failed delivery jobs retry up to **3 times** with exponential backoff (1min, 5min, 25min)
- Failed jobs after 3 retries are logged to the error monitoring system
- Email sending uses a transactional email provider (e.g., Resend, Postmark)

---

## Data Model

```
Notification
├── id                  (uuid, primary key)
├── workspace_id        (foreign key → Workspace)
├── recipient_id        (foreign key → User)
├── sender_id           (foreign key → User, nullable — null for system events)
├── type                (enum: mention | comment | reply | resolved | reopened |
│                        access_granted | workspace_invite | guest_accepted)
├── page_id             (foreign key → Page)
├── source_id           (uuid — ID of the comment, block, etc. that triggered it)
├── content_snippet     (string — up to 100 chars of relevant content)
├── is_read             (boolean, default: false)
├── read_at             (timestamp, nullable)
├── created_at          (timestamp)

NotificationPreference
├── id                  (uuid, primary key)
├── user_id             (foreign key → User, unique)
├── email_frequency     (enum: realtime | daily | weekly | off, default: daily)
├── weekly_digest_day   (integer — 0=Sunday, 1=Monday ... 6=Saturday, default: 1)
└── updated_at          (timestamp)
```

---

## API Endpoints

| Method | Endpoint | Description | Access |
|--------|----------|-------------|--------|
| GET | `/api/notifications` | List notifications for current user | Authenticated |
| PATCH | `/api/notifications/:id/read` | Mark a specific notification as read | Authenticated |
| POST | `/api/notifications/read-all` | Mark all notifications as read | Authenticated |
| GET | `/api/user/notification-preferences` | Get notification preferences | Authenticated |
| PATCH | `/api/user/notification-preferences` | Update email frequency / digest day | Authenticated |
| GET | `/api/notifications/stream` | SSE stream for real-time notifications | Authenticated |

---

## UI Screens

| Screen | Route / Location | Access |
|--------|----------------|--------|
| Notification Center | Sidebar panel (bell icon) | All members |
| Notification Settings | `/settings/notifications` | All members |

---

## Business Rules

1. Notification delivery is transactional — a notification is only enqueued if the triggering action (comment, @mention) is successfully saved.
2. A user does not receive notifications for their own actions (you cannot @mention yourself and receive a notification).
3. Notifications are scoped to the workspace — activity in Workspace A does not generate notifications visible in Workspace B.
4. Email digests only include unread notifications — activity already seen in-app is excluded.
5. Empty digests are not sent. A daily digest is skipped if there are no unread notifications.
6. Notification retention is 90 days. Notifications older than 90 days are permanently deleted.
7. Changing email frequency takes effect from the next scheduled delivery cycle.
8. Real-time SSE delivery is best-effort. Notifications are always available in the Notification Center regardless of SSE delivery status.
9. Failed email delivery jobs retry up to 3 times before being logged as failed.

---

## Out of Scope (MVP)

- Granular per-event-type notification control (Phase 2 — e.g., "only @mentions, no comment replies")
- Muting a specific page or thread
- Browser push notifications (PWA)
- Mobile push notifications (Phase 3 with mobile app)
- Notification grouping by sender or page in the panel
- Slack / Teams integration for notifications (Phase 5)
