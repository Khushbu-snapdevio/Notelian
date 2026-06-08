# Authentication

## Overview

Authentication handles user identity — who you are, how you prove it, and how your session is maintained. Notelian uses **Better Auth** with the **Admin Plugin**, integrated directly into Next.js App Router.

**Powered by:** [Better Auth](https://better-auth.com)

**Why Better Auth:**
- Built for Next.js (API Routes + Server Actions)
- Database-backed sessions (more secure than stateless JWT)
- Admin Plugin provides ban, impersonation, and session revocation out of the box
- Works natively with Drizzle ORM + PostgreSQL

---

## Auth Flows

| Flow | Description |
|------|-------------|
| Sign Up | New user creates account with email + password |
| Sign In | Existing user signs in with email + password, magic link, or Google OAuth |
| Magic Link | Passwordless sign in / sign up via a one-time email link |
| OAuth | Sign in / sign up via Google |
| Email Verification | Verify email address after sign up |
| Forgot Password | Request password reset link via email |
| Reset Password | Set new password using the reset link |
| Sign Out | End the current session |
| Session Management | View and revoke active sessions across devices |

---

## 1. Sign Up (Email + Password)

### Flow

1. User visits `/sign-up`
2. Fills in: Full Name (required), Email (required), Password (required)
3. Submits form
4. Better Auth creates the user record and a session
5. Verification email is sent to the provided address
6. User is redirected to `/onboarding`
7. Banner shown: `"Please verify your email. Check your inbox."` — app accessible but invite capability locked until verified

### Validation

| Field | Rules |
|-------|-------|
| Full Name | Required, 2–100 characters |
| Email | Required, valid format, unique across platform |
| Password | Required, min 8 characters, at least one uppercase, one lowercase, one number, one special character |

### On duplicate email

- Already registered: `"An account with this email already exists. Sign in instead?"`
- Registered via Google OAuth: `"This email is linked to a Google account. Sign in with Google instead."`

---

## 2. Sign In (Email + Password)

### Flow

1. User visits `/sign-in`
2. Enters email + password
3. On success: session created, redirected to last active workspace or workspace switcher
4. On failure: `"Invalid email or password."` (generic — does not reveal which field is wrong)

### Rate Limiting

- **No rate limit on sign in** — failed sign-in attempts are not throttled.

### Session Duration

- Default: **7 days** sliding window (TTL resets on each authenticated request)
- `"Remember me"` toggle: extends to **30 days**

---

## 3. Magic Link (Passwordless)

Users can sign in or sign up without a password by requesting a one-time link sent to their email.

### Flow

1. User enters their email on `/sign-in` and chooses `"Email me a sign-in link"`
2. **Always shows:** `"If an account exists with this email, a sign-in link has been sent."` (prevents email enumeration)
3. Better Auth sends a magic link with a token valid for **15 minutes**, single-use
4. User clicks the link → `GET /api/auth/magic-link/verify?token=:token`
5. Better Auth verifies the token:
   - **New email** (not in DB): account auto-created (email marked verified — the link proves ownership), redirected to `/onboarding`
   - **Existing user**: session created, redirected to last active workspace
6. The link is invalidated immediately after use

> A successful magic-link sign-in counts as email verification — clicking the link proves the user controls the address.

---

## 4. OAuth — Google

Users can sign up or sign in using their Google account. No password required.

### Flow

1. User clicks `"Continue with Google"` on sign-in or sign-up page
2. Browser redirects to Google OAuth consent screen
3. User approves → redirected back to `/api/auth/callback/google`
4. Better Auth handles the callback:
   - **New user** (email not in DB): account auto-created, redirected to `/onboarding`
   - **Existing user** (email matches password account): accounts are linked — user can now use both methods
   - **Existing OAuth user**: session created, redirected to app
5. No email verification required (Google has already verified the email)

| Provider | Scope |
|----------|-------|
| Google | `email`, `profile` |

---

## 5. Email Verification

Every user who signs up with email + password must verify their email.

### Flow

1. Verification email sent on sign up with a token link valid for **24 hours**
2. User clicks link → `GET /api/auth/verify-email?token=:token`
3. On success: email marked verified, redirected to app with success toast
4. On expired/invalid: `"This link has expired. Request a new verification email."`

### Unverified Restrictions

- Can access app and complete onboarding
- Cannot send member invites until verified — invites entered during the onboarding wizard are **queued** and sent automatically once the email is verified
- Persistent banner shown until verified

### Resend

- Available from banner or `/settings/account`
- **No rate limit** — resends are not throttled

---

## 6. Forgot Password

### Flow

1. User visits `/forgot-password`, enters email
2. **Always shows:** `"If an account exists with this email, a reset link has been sent."` (prevents email enumeration)
3. If email exists: reset email sent with token valid for **1 hour**
4. User clicks link → `/reset-password?token=:token`
5. Enters new password + confirm
6. On success: password updated, all sessions revoked, redirected to `/sign-in`

### Security

- Reset token is **single-use** — invalidated immediately after use
- Changing password revokes all active sessions

---

## 7. Session Management

### Access

`/settings/sessions`

### Session List

Each active session shows:
- Device type (Desktop / Mobile)
- Browser (Chrome, Safari, Firefox, etc.)
- Approximate location (city, country — IP-based, best-effort)
- Last active timestamp
- `"Current session"` badge on the active device

### Actions

| Action | Description |
|--------|-------------|
| Revoke session | End a specific session (log out that device) |
| Revoke all other sessions | End all sessions except the current one |

---

## 8. Account Settings

Available at `/settings/account`

**Profile:** Update display name, avatar (upload or use initials as default)

**Password:** Change password (requires current password). Changing password revokes all other sessions.

**Connected Accounts:** Shows linked Google account. Can link Google to an email account. Cannot unlink if it is the only auth method.

**Danger Zone — Delete Account:**
- If the user is the Admin of any workspace, they must transfer ownership first
- Shown: `"You are the Admin of X workspace(s). Transfer ownership before deleting your account."`
- Requires typing email address to confirm deletion

**What happens to content on deletion:**

| Content type | Outcome |
|--------------|---------|
| Pages created in shared workspaces | Remain in the workspace; `created_by` is set to null and displayed as `"Former Member"` |
| Comments | Remain visible; author shown as `"Former Member"` |
| Private pages (visible only to the deleted user) | **Permanently deleted** — no other user has access to them, so they cannot be recovered or reassigned |
| Uploaded files on private pages | Deleted from object storage; workspace `bytes_used` decremented |
| Workspace memberships | Removed from all workspaces the user belonged to |

Private page deletion is queued as a pg-boss job (`delete-user-private-pages`) that runs immediately on account deletion confirmation.

---

## 9. Better Auth — Admin Plugin Features

Used by the Notelian platform team via **Orbit Admin** — not exposed to customers.

| Feature | Description |
|---------|-------------|
| Ban user | Immediately revokes all sessions. User cannot sign in. |
| Unban user | Restores sign-in access. |
| Impersonate user | Platform admin logs in as any user for support. Opens a separate marked session. |
| Revoke sessions | Revoke any user's sessions individually or all at once. |
| List sessions | View all active sessions for any user. |

---

## Background Jobs (pg-boss)

| Job | Trigger | Description |
|-----|---------|-------------|
| `delete-user-private-pages` | On account deletion confirmation | Permanently deletes all private pages owned by the deleted user and queues object-storage file deletion for any uploaded files on those pages. Runs immediately (not on a schedule). |

---

## Data Model

```
User
├── id                  (uuid, primary key)
├── name                (string)
├── email               (string, unique)
├── email_verified      (boolean, default: false)
├── image               (string — avatar URL, nullable)
├── is_platform_admin   (boolean, default: false)
├── banned              (boolean, default: false)
├── banned_reason       (string, nullable)
├── created_at          (timestamp)
└── updated_at          (timestamp)

Session
├── id                  (uuid, primary key)
├── user_id             (foreign key → User)
├── token               (string, unique — hashed)
├── expires_at          (timestamp)
├── ip_address          (string, nullable)
├── user_agent          (string, nullable)
├── impersonated_by     (uuid, nullable)
└── created_at          (timestamp)

Account
├── id                  (uuid, primary key)
├── user_id             (foreign key → User)
├── provider            (enum: credential | google)
├── provider_account_id (string, nullable — for OAuth)
└── password            (string, nullable — hashed, credential only)

Verification
├── id                  (uuid, primary key)
├── identifier          (string — email or user id)
├── value               (string — hashed token)
└── expires_at          (timestamp)
```

---

## API Endpoints

Better Auth exposes a unified handler at `/api/auth/[...all]`.

| Method | Endpoint | Description |
|--------|----------|-------------|
| POST | `/api/auth/sign-up/email` | Register with email + password |
| POST | `/api/auth/sign-in/email` | Sign in with email + password |
| POST | `/api/auth/sign-in/magic-link` | Request a magic-link sign-in email |
| GET | `/api/auth/magic-link/verify?token=` | Verify magic link and create session |
| GET | `/api/auth/sign-in/social?provider=google` | Initiate Google OAuth |
| GET | `/api/auth/callback/google` | Google OAuth callback |
| POST | `/api/auth/sign-out` | Sign out current session |
| POST | `/api/auth/forget-password` | Request password reset email |
| POST | `/api/auth/reset-password` | Reset password with token |
| GET | `/api/auth/verify-email?token=` | Verify email address |
| POST | `/api/auth/send-verification-email` | Resend verification email |
| GET | `/api/auth/get-session` | Get current session + user |
| GET | `/api/auth/list-sessions` | List all active sessions |
| POST | `/api/auth/revoke-session` | Revoke a specific session |
| POST | `/api/auth/revoke-other-sessions` | Revoke all sessions except current |

---

## UI Screens

| Screen | Route | Access |
|--------|-------|--------|
| Sign Up | `/sign-up` | Unauthenticated |
| Sign In | `/sign-in` | Unauthenticated |
| Forgot Password | `/forgot-password` | Unauthenticated |
| Reset Password | `/reset-password?token=` | Unauthenticated |
| Email Verification | `/verify-email?token=` | Unauthenticated |
| Onboarding | `/onboarding` | Authenticated (new user) |
| Account Settings | `/settings/account` | Authenticated |
| Session Management | `/settings/sessions` | Authenticated |

---

## Security Considerations

| Concern | Mitigation |
|---------|-----------|
| Brute force | No sign-in rate limit (disabled per product decision) — mitigated by the strong password policy and immediate session revocation on ban |
| Email enumeration | Forgot password and magic-link requests always return the same message |
| Session hijacking | Database-backed sessions; token hashed in DB |
| Token reuse | Reset + verification tokens are single-use |
| Password change | Revokes all sessions on change |
| Banned users | Sessions revoked immediately on ban |
| Impersonation | Logged to audit trail; session marked with `impersonated_by` |
| Account deletion | Requires email confirmation; ownership transfer enforced |

---

## Business Rules

1. Email addresses are unique across the platform — one account per email.
2. OAuth accounts do not require email verification — Google has already verified it.
3. If a user signs up via email and later signs in via Google with the same email, the accounts are linked — not duplicated.
4. A banned user's sessions are revoked immediately and cannot re-authenticate until unbanned.
5. Password reset invalidates all existing sessions — the user must log in again on all devices.
6. Reset tokens and verification tokens are single-use and expire (1 hour for reset, 24 hours for verification).
7. A user cannot delete their account if they are the sole Admin of any workspace — ownership must be transferred first.
8. Verification email resends are not rate-limited.
9. Forgot password always returns the same response regardless of whether the email exists — prevents account enumeration.
10. Sessions use sliding expiry — TTL resets on each authenticated request, keeping active users logged in.
11. On account deletion: shared-workspace content (pages, comments) is retained with `"Former Member"` attribution. Private pages owned exclusively by the deleted user are permanently and irreversibly deleted — they are inaccessible to anyone else and cannot be recovered.

---

## Out of Scope (MVP)

- Two-factor authentication (2FA / TOTP)
- SSO / SAML (enterprise identity providers)
- Passkeys / WebAuthn
- Email address change (requires re-verification flow — Phase 2)
- GitHub OAuth
- Account merge across different emails
