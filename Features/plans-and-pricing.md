# Plans & Pricing

## Overview

Notelian offers tiered subscription plans applied per workspace. Plans control feature access and usage limits. Plan configuration — pricing, limits, feature flags, and landing-page copy — is fully managed from **Orbit Admin** with no code deployment required.

**Three surfaces this module covers:**

| Surface | Description |
|---------|-------------|
| Orbit Admin — Plan Config | Platform team configures plans, pricing, and limits |
| Landing Page — Pricing Section | Public-facing pricing page shown to visitors |
| App — Plan Enforcement | Limits enforced inside the app per workspace's active plan |

---

## Plans

### Default Plans

| Plan | Target | Monthly Price | Annual Price |
|------|--------|--------------|-------------|
| **Free** | Individuals and solo creators | $0 | $0 |
| **Pro** | Small teams and power users | $12 / workspace | $9 / workspace ($108/yr) |
| **Business** | Larger teams and organizations | $25 / workspace | $20 / workspace ($240/yr) |

Plans are applied per **workspace** — not per user. All members of a workspace share the same plan.

> All default values are configurable from Orbit Admin — they are not hardcoded.

---

## Plan Limits

Each plan has configurable limits enforced server-side on every relevant action.

| Limit | Free | Pro | Business |
|-------|------|-----|----------|
| Workspaces per user | 1 | 3 | Unlimited |
| Members per workspace | 5 | 25 | Unlimited |
| Guests per workspace | 2 | 10 | Unlimited |
| Storage per workspace | 500 MB | 10 GB | 100 GB |
| Max file upload size | 5 MB | 25 MB | 100 MB |
| Custom templates per workspace | 5 | Unlimited | Unlimited |
| Page version history | 7 days | 90 days | 1 year |
| Trash retention | 30 days | 90 days | 1 year |
| API requests per day | — | 1,000 | 10,000 |
| Saved filters per user | 5 | 25 | Unlimited |

---

## Feature Flags

Some features are gated entirely (on/off) rather than limited by count.

| Feature | Free | Pro | Business |
|---------|------|-----|----------|
| Table view | ✅ | ✅ | ✅ |
| Board view | ✅ | ✅ | ✅ |
| Calendar view | ❌ | ✅ | ✅ |
| Gallery view | ❌ | ✅ | ✅ |
| API access | ❌ | ✅ | ✅ |
| Page analytics | ❌ | ✅ | ✅ |
| Advanced analytics | ❌ | ❌ | ✅ |
| Priority support | ❌ | ❌ | ✅ |
| Dedicated CSM | ❌ | ❌ | ✅ |
| SSO / SAML *(Phase 3)* | ❌ | ❌ | ✅ |

> All feature flags are configurable from Orbit Admin — not hardcoded.

---

## Orbit Admin — Plan Configuration

Platform team manages plans at `/orbit/plans`.

### Plan List Screen

Shows all plans in a table:
- Plan name, display name, monthly price, annual price, status (Active / Hidden)
- Actions: Edit / Hide

### Edit Plan Screen

**Pricing:**
- Monthly price (USD) — number input
- Annual price (USD) — number input
- Currency (USD only for MVP)
- Show annual savings badge (toggle) — e.g. `"Save 25%"`

**Display (for pricing page):**
- Plan display name (e.g. `Pro`)
- Tagline (e.g. `"For small teams"`)
- Highlighted / recommended (toggle) — shows `"Most Popular"` badge on pricing page
- CTA button label (e.g. `"Start Free Trial"`)
- CTA button action (enum: `signup` | `contact_sales`)
- Feature bullet list — ordered list of highlights shown on the pricing card

**Limits:** All 10 configurable limit keys with number or `"Unlimited"` toggle.

**Feature Flags:** Toggle for each of the 10 feature flag keys.

**Visibility:** `Active` (shown on pricing page) | `Hidden` (legacy, invisible to new signups, still active for existing workspaces)

---

## Public Pricing Page (`/pricing`)

```
┌──────────────────────────────────────────────────────────┐
│           Simple, transparent pricing                    │
│      [Monthly]  ●────  [Annual — Save 25%]               │
└──────────────────────────────────────────────────────────┘

┌────────────────┐  ┌──────────────────┐  ┌─────────────────┐
│      Free      │  │  ★  Pro          │  │    Business     │
│    $0 / mo     │  │  $12 / mo        │  │   $25 / mo      │
│                │  │  Most Popular    │  │                 │
│ For individuals│  │ For small teams  │  │ For large orgs  │
│                │  │                  │  │                 │
│ ✅ 1 workspace │  │ ✅ 3 workspaces  │  │ ✅ Unlimited    │
│ ✅ 5 members   │  │ ✅ 25 members    │  │ ✅ Unlimited    │
│ ✅ 500 MB      │  │ ✅ 10 GB storage │  │ ✅ 100 GB       │
│ ❌ Calendar    │  │ ✅ Calendar view  │  │ ✅ All views    │
│ ❌ API access  │  │ ✅ API access     │  │ ✅ API access   │
│                │  │                  │  │                 │
│ [Get Started]  │  │ [Start Free Trial│  │ [Contact Us]    │
└────────────────┘  └──────────────────┘  └─────────────────┘

              All plans include:
  ✅ Unlimited pages        ✅ Block-based editor
  ✅ Table & Board views    ✅ Public page sharing
  ✅ Global search          ✅ Comments & mentions
```

**Key behaviors:**
- Monthly/Annual billing toggle at top — switches all prices; state persists in `localStorage`
- Annual plans show a savings badge (e.g. `"Save $36/yr"`)
- `GET /api/plans` (public, unauthenticated, 60-second CDN cache) — no hardcoded content
- FAQ section below cards — configurable from Orbit Admin

### Default FAQ Items

| Question | Answer |
|----------|--------|
| Can I switch plans later? | Yes. Upgrades take effect immediately; downgrades at end of billing period. |
| What happens when I hit a limit? | The action is blocked and you see an upgrade prompt. No data is lost. |
| Is there a free trial? | Pro includes a 14-day trial with no credit card required. |
| Can I cancel anytime? | Yes. Your workspace stays on the plan until the period ends, then moves to Free. |
| Nonprofit or student discounts? | Yes — contact us at pricing@notelian.com. |

---

## In-App Plan Enforcement

All limits are enforced **server-side**. The frontend reflects limits but is never the sole enforcement layer.

### Enforcement Points

| Action | What happens when limit is hit |
|--------|-------------------------------|
| Invite member (over limit) | Blocked. Upgrade prompt shown. |
| Invite guest (over limit) | Blocked. Upgrade prompt shown. |
| Create workspace (over limit) | Blocked. Upgrade prompt shown. |
| Upload file (over size or storage limit) | Blocked. Shows remaining storage. Upgrade prompt shown. |
| Use Calendar view (Free plan) | Blocked. Feature upgrade overlay shown. |
| Use Gallery view (Free plan) | Blocked. Feature upgrade overlay shown. |
| Call API (Free plan or over rate limit) | 401 response with upgrade message. |
| Access version history past retention limit | Older entries shown greyed out with lock icon and upgrade prompt inline. |

### Upgrade Prompt Modal

```
┌───────────────────────────────────────────────┐
│  This feature is on the Pro plan              │
│                                               │
│  Upgrade to unlock:                           │
│  ✅ Calendar & Gallery views                  │
│  ✅ Up to 25 members                          │
│  ✅ 10 GB storage                             │
│  ✅ API access                                │
│  ✅ 90-day version history                    │
│                                               │
│         [Upgrade to Pro →]                   │
│         [Maybe later]                         │
└───────────────────────────────────────────────┘
```

- **Workspace Admin** sees the upgrade button → links to `/settings/billing`
- **Editors and Viewers** see: `"Contact your workspace admin to upgrade."`
- Bullet list is driven by `plan_bullets` from the database — not hardcoded

### Soft Usage Warnings

When a workspace reaches **80%** of a limit, an amber inline notice appears in **Workspace Settings → Plan & Usage**:

> "20 of 25 members used. Upgrade to Business for unlimited members."

No blocking modal below 100%. Warnings turn red at 95%+.

---

## Workspace Plan & Usage (`/settings/plan`)

Available to workspace Admin only.

- Current plan name, billing cycle, next renewal date
- `"Change Plan"` link → `/pricing`
- Usage bars for all metered limits (amber at 80%, red at 95%+)
- Billing history table — date, amount, plan, download PDF link

---

## Data Model

```
Plan
├── id                    (uuid, primary key)
├── name                  (string — internal slug, e.g. "pro")
├── display_name          (string — public name, e.g. "Pro")
├── tagline               (string)
├── monthly_price_usd     (integer — cents, e.g. 1200 = $12.00)
├── annual_price_usd      (integer — full annual in cents, e.g. 10800)
├── is_highlighted        (boolean — shows "Most Popular" badge)
├── cta_label             (string)
├── cta_action            (enum: signup | contact_sales)
├── status                (enum: active | hidden)
├── order_index           (integer)
├── created_at            (timestamp)
└── updated_at            (timestamp)

PlanLimit
├── plan_id               (foreign key → Plan)
├── limit_key             (string — e.g. "max_members", "storage_mb")
└── limit_value           (integer — -1 = unlimited)

PlanFeatureFlag
├── plan_id               (foreign key → Plan)
├── feature_key           (string — e.g. "calendar_view", "api_access")
└── is_enabled            (boolean)

PlanBullet
├── plan_id               (foreign key → Plan)
├── text                  (string)
├── is_included           (boolean — true = ✅, false = ❌)
└── order_index           (integer)

Workspace (additions)
├── plan_id               (foreign key → Plan)
├── plan_override_id      (foreign key → Plan, nullable)
├── plan_override_expires_at (timestamp, nullable)
├── billing_cycle         (enum: monthly | annual)
└── current_period_ends_at (timestamp)
```

**Effective plan resolution:**
```
effectivePlan = workspace.plan_override_id
  && workspace.plan_override_expires_at > now()
    ? plans[workspace.plan_override_id]
    : plans[workspace.plan_id]
```

---

## API Endpoints

### Public

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/plans` | All active plans with limits, flags, bullets, FAQ |

### Authenticated

| Method | Endpoint | Description | Access |
|--------|----------|-------------|--------|
| GET | `/api/workspaces/:id/plan` | Current effective plan + usage | Member+ |
| GET | `/api/workspaces/:id/usage` | Usage vs limits for all metered limits | Member+ |

### Orbit Admin

| Method | Endpoint | Description |
|--------|----------|-------------|
| GET | `/api/orbit/plans` | List all plans (including hidden) |
| GET | `/api/orbit/plans/:id` | Plan detail with limits, flags, bullets |
| PATCH | `/api/orbit/plans/:id` | Update pricing and display fields |
| PATCH | `/api/orbit/plans/:id/limits` | Update all limit values |
| PATCH | `/api/orbit/plans/:id/flags` | Update all feature flags |
| PATCH | `/api/orbit/plans/:id/bullets` | Add, remove, reorder bullets |
| GET / PATCH | `/api/orbit/plans/faq` | FAQ items |
| GET / PATCH | `/api/orbit/plans/universal` | "All plans include" bullets |
| POST | `/api/orbit/workspaces/:id/plan-override` | Apply a plan override |
| DELETE | `/api/orbit/workspaces/:id/plan-override` | Remove a plan override |

---

## UI Screens

| Screen | Route | Access |
|--------|-------|--------|
| Public Pricing Page | `/pricing` | Public |
| Pricing section on landing page | `/` (section) | Public |
| Workspace Plan & Usage | `/settings/plan` | Admin |
| Orbit Admin — Plan List | `/orbit/plans` | Platform team |
| Orbit Admin — Edit Plan | `/orbit/plans/:id/edit` | Platform team |
| Orbit Admin — Pricing FAQ | `/orbit/plans/faq` | Platform team |

---

## Business Rules

1. Plans are applied per workspace — not per user. All workspace members share the same plan.
2. All limit checks are enforced server-side — the frontend never serves as the sole guard.
3. Free plan has a 14-day trial for Pro features — after the trial, the workspace downgrades automatically if not upgraded.
4. A workspace with an active plan override ignores its paid plan for enforcement purposes until the override expires.
5. When an override expires, the workspace reverts to its paid plan (or Free if none is set).
6. Hidden plans cannot be selected by new workspaces but remain active for existing workspaces on them.
7. All plan content (pricing, limits, feature flags, bullets) is fetched from the database — changing it in Orbit Admin immediately affects new API responses.
8. The public `/api/plans` response is CDN-cached for 60 seconds — pricing changes may take up to 60 seconds to appear on the pricing page.
9. Workspace Admins are shown their effective plan (override if active) in Settings, not their paid plan.
10. The upgrade prompt bullet list is driven by database `plan_bullets` records — not hardcoded text.

---

## Out of Scope (MVP)

- Self-service billing / Stripe checkout (managed via Orbit Admin for now)
- Proration calculations for mid-cycle plan changes
- Team-seat-based pricing (current model: per-workspace flat rate)
- Coupon / discount codes
- Invoicing for annual plans
- Automated dunning (payment failure retries — managed manually)
