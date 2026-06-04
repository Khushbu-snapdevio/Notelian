# Admin Panel (Orbit Admin)

## Overview

**Orbit Admin** is Notelian's internal operations dashboard — an exclusive tool for the platform team to manage users, workspaces, subscriptions, and platform health. It is **not accessible to any end user**, including workspace Admins. All capabilities here exist at the platform layer, separate from in-product workspace Admin features.

**Powered by:** Better Auth Admin Plugin + custom Orbit Admin interface.

---

## Access

- Route: `/orbit` (requires `is_platform_admin = true` on the user record)
- Platform admin status is set directly in the database — there is no UI to self-assign this role
- Impersonation sessions are logged to the audit trail

---

## Dashboard

**Overview metrics:**

| Metric | Description |
|--------|-------------|
| Total users | All registered accounts |
| Active workspaces | Workspaces with at least one login in the past 30 days |
| New signups (7d / 30d) | Signup trend |
| Plan distribution | Number of workspaces on Free / Pro / Business |
| Active sessions | Current active sessions across all users |

---

## User Management (`/orbit/users`)

| Action | Description |
|--------|-------------|
| List all users | Paginated table with name, email, join date, plan, last active |
| Search users | Filter by name, email |
| View user detail | Profile, linked workspaces, active sessions, account status |
| Ban user | Immediately revokes all sessions. User cannot sign in. Optionally set a ban reason. |
| Unban user | Restores sign-in access |
| Impersonate user | Opens a separate admin session logged in as the user — for support troubleshooting |
| Revoke all sessions | Signs the user out of all devices |
| View session list | All active sessions for a user with device, IP, and last active |

**Impersonation:** Marked with `impersonated_by` on the session record. Always logged to the audit trail. The impersonation session has a maximum duration of 2 hours.

---

## Workspace Management (`/orbit/workspaces`)

| Action | Description |
|--------|-------------|
| List all workspaces | Paginated table with name, plan, member count, created date |
| Search workspaces | Filter by name or ID |
| View workspace detail | Members list, page count, storage usage, current plan |
| View workspace plan | Current paid plan, any active override, billing cycle, period end date |
| Force delete workspace | Permanently delete a workspace and all its content (requires confirmation) |
| Impersonate workspace | View the workspace as an Admin (uses user impersonation for a workspace member) |

---

## Plan & Subscription Management (`/orbit/plans`)

All plan configuration — pricing, limits, feature flags, and display content — is managed here. See the [Plans & Pricing](./plans-and-pricing.md) document for the full specification.

**Summary of capabilities:**

| Action | Description |
|--------|-------------|
| List all plans | Active and hidden plans |
| Edit plan | Pricing, display, limits, feature flags, bullets |
| Hide / activate plan | Toggle plan visibility on the pricing page |
| Reorder plans | Drag to set display order on the pricing page |
| Edit pricing FAQ | Add, edit, remove FAQ items shown on `/pricing` |
| Edit "All plans include" | Universal bullet list below pricing cards |

### Workspace Plan Override

From any workspace's detail page:
- Apply a plan override — assign any plan to a workspace regardless of payment
- Set an optional expiry date — the workspace automatically reverts to its paid plan on expiry
- Remove an existing override
- Override is logged to the audit trail

**Use cases:** Free trials, enterprise pilot deals, support compensations, grace periods after failed payments.

When an override is active, the workspace Admin sees a notice in their Settings: `"Your workspace is on a special plan arrangement. Contact us at support@notelian.com for details."` The specific override reason is not shown to end users.

---

## Platform Analytics (`/orbit/analytics`)

| Metric | Description |
|--------|-------------|
| Signups over time | Daily/weekly/monthly signup chart |
| Activation rate | % of new users who create a page within 7 days |
| Plan conversion | Free → Pro and Pro → Business conversion rates |
| Feature usage | How many workspaces are using each feature (databases, comments, etc.) |
| Notification open rate | % of email notifications that are opened |
| Search usage | Search queries per day, no-result rate |

Analytics data is aggregated and anonymized — individual user activity is not surfaced in charts.

---

## Audit Trail (`/orbit/audit`)

A read-only log of all platform-team actions:

| Column | Description |
|--------|-------------|
| Timestamp | When the action occurred |
| Actor | Platform admin who performed the action |
| Action | What was done (e.g., `user.banned`, `workspace.plan_overridden`, `session.impersonated`) |
| Target | The user or workspace affected |
| Notes | Optional reason or context |

Audit trail is append-only — records cannot be deleted.

---

## Data Model

```
PlatformAuditLog
├── id                  (uuid, primary key)
├── actor_id            (foreign key → User — platform admin)
├── action              (string — e.g. "user.banned", "plan.override_applied")
├── target_type         (enum: user | workspace | plan)
├── target_id           (uuid — ID of the affected entity)
├── metadata            (jsonb — additional context, e.g. ban reason, override plan)
└── created_at          (timestamp)
```

---

## API Endpoints

All Orbit Admin endpoints require `is_platform_admin = true` on the authenticated user.

### User Management

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/orbit/users` | List all users (paginated) |
| GET | `/api/orbit/users/:id` | Get user detail + sessions |
| POST | `/api/orbit/users/:id/ban` | Ban a user |
| POST | `/api/orbit/users/:id/unban` | Unban a user |
| POST | `/api/orbit/users/:id/impersonate` | Start an impersonation session |
| POST | `/api/orbit/users/:id/revoke-sessions` | Revoke all sessions |

### Workspace Management

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/orbit/workspaces` | List all workspaces (paginated) |
| GET | `/api/orbit/workspaces/:id` | Get workspace detail |
| DELETE | `/api/orbit/workspaces/:id` | Force delete workspace |
| GET | `/api/orbit/workspaces/:id/plan` | Get plan info + usage |
| POST | `/api/orbit/workspaces/:id/plan-override` | Apply plan override |
| DELETE | `/api/orbit/workspaces/:id/plan-override` | Remove plan override |

### Audit

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/orbit/audit` | List audit trail entries (paginated, filterable) |

---

## UI Screens

| Screen | Route | Access |
|--------|-------|--------|
| Orbit Admin Dashboard | `/orbit` | Platform admin |
| User List | `/orbit/users` | Platform admin |
| User Detail | `/orbit/users/:id` | Platform admin |
| Workspace List | `/orbit/workspaces` | Platform admin |
| Workspace Detail | `/orbit/workspaces/:id` | Platform admin |
| Plan List | `/orbit/plans` | Platform admin |
| Edit Plan | `/orbit/plans/:id/edit` | Platform admin |
| Pricing FAQ | `/orbit/plans/faq` | Platform admin |
| Analytics | `/orbit/analytics` | Platform admin |
| Audit Trail | `/orbit/audit` | Platform admin |

---

## Business Rules

1. Orbit Admin is accessible only to users with `is_platform_admin = true` in the database — this flag cannot be set from any in-product UI.
2. Every action performed in Orbit Admin is logged to the audit trail — the log is append-only.
3. Impersonation sessions are marked with `impersonated_by` on the session record and have a maximum 2-hour TTL.
4. Banning a user immediately revokes all their active sessions — the ban takes effect with no grace period.
5. Force-deleting a workspace permanently removes all its data — this cannot be undone. Requires explicit text confirmation.
6. Plan overrides are logged to the audit trail including the reason and expiry date.
7. Platform Admins cannot see private page content — Orbit Admin only exposes private page titles for compliance.
8. Analytics data shown in Orbit Admin is aggregated — individual user browsing or content is not surfaced.

---

## Out of Scope (MVP)

- Customer support ticket management (external tool, e.g., Linear or Intercom)
- Automated churn alerts and dunning management
- Revenue dashboards (Stripe integration — Phase 3)
- Feature flag management for product experiments (separate from plan feature flags)
- Role-based access within Orbit Admin (all platform admins have full access)
- Multi-region data management
