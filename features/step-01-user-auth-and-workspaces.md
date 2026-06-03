# Step 1 — User Authentication & Workspaces

## Overview

User authentication and workspaces form the structural foundation of Notelian. Authentication ensures that each user has a secure, personal account. Workspaces provide the organizational containers where content lives, members collaborate, and permissions are enforced.

Every user has exactly one Notelian account. That account can be a member of multiple workspaces — one for personal use, one for a company, one for a freelance client project, and so on. Switching between workspaces is instant, and each workspace is completely isolated from the others in terms of content and membership.

---

## Account Creation

### Sign Up Methods

New users can create a Notelian account using either of two methods:

**Email and Password**
- The user provides their email address and a password
- A verification email is sent to the provided address
- The user clicks the verification link in the email to confirm ownership of the address and activate the account
- The verification link expires after **24 hours**. If it expires, the user can request a new one from the login screen
- If a user attempts to sign up with an email that already has a Notelian account, the sign-up form shows: "An account with this email already exists — try logging in instead." No verification email is sent
- **Password requirements:** Minimum 8 characters, at least 1 uppercase letter, 1 lowercase letter, 1 number, and 1 special character (`!@#$%^&*` etc.). Requirements are shown as real-time validation hints beneath the password field

**Google OAuth (Sign in with Google)**
- The user clicks "Continue with Google"
- They are redirected to Google's standard OAuth consent screen
- After granting consent, they are returned to Notelian with their account created
- The account is associated with their Google account's email address
- No separate password is required — login is handled by Google's authentication
- Google OAuth accounts are considered pre-verified — no separate email verification step is required, as Google has already confirmed ownership of the email address
- If the user's Google account email matches an existing Notelian email/password account, the OAuth account is automatically linked to the existing account. The user can then log in with either method

### Profile Setup After Sign Up

After creating an account for the first time, the user is guided through a brief profile setup:
- Set their **display name** — how they appear to teammates in comments, mentions, and member lists
- Upload a **profile avatar** — an image that represents them throughout the workspace
- Set their **role/title** — optional, displayed next to their name in member directories (e.g., "Product Designer", "Engineer")

---

## Login

### Email and Password Login
- The user enters their registered email and password
- On success, they are taken directly to their last active workspace

### Google Login
- The user clicks "Continue with Google"
- Google authenticates them and returns them to Notelian
- If the Google account is already linked to a Notelian account, the user is logged in directly

### Session Management
- Login sessions persist for **30 days** by default (sliding window — activity resets the expiry)
- Users can explicitly log out from any device via the Account Settings
- Users can view and revoke active sessions from multiple devices from their account security settings
- **Session revocation is immediate** — the revoked session token is invalidated server-side at the moment the user clicks "Revoke." The next request from that device (or page refresh) will redirect to the login screen
- When a session is revoked from another device, the user on that device is not notified in real time — they simply get redirected to login on their next action
- Changing the account password automatically invalidates all active sessions on all devices except the current one

### Password Reset
- Users who forget their password click "Forgot Password" on the login screen
- They enter their email address and receive a password reset link
- The link is time-limited and single-use for security
- After clicking the link, the user sets a new password and is logged in

---

## Workspaces

### What Is a Workspace?

A workspace is the top-level organizational unit in Notelian. All pages, databases, and content live within a workspace. All members of a team share a single workspace. Permissions, settings, and templates are all scoped to a workspace.

A workspace has:
- A **name** — displayed in the sidebar header and in the workspace switcher
- An **icon** — an emoji or custom image that represents the workspace visually
- A **member list** — all users who have been invited and accepted membership
- A **settings panel** — where admins can manage the workspace configuration

### Creating a Workspace

During the initial onboarding flow after account creation, the user is prompted to either:
1. **Create a new workspace** — Names it, sets an icon, and optionally invites teammates
2. **Join an existing workspace** — If the user has an invite link or email invitation, they can join an existing workspace directly

After initial setup, additional workspaces can be created at any time from the Workspace Switcher in the sidebar.

### Workspace Settings

Workspace settings are accessible from the sidebar (click the workspace name → Settings) and are divided into sections:

**General**
- Change the workspace name
- Change the workspace icon
- Set the workspace's default language

**Members** (covered in the Members section below)

**Plans & Billing** (Phase 2 — not in MVP scope)

**Export**
- Export all workspace content as a ZIP archive of Markdown files and media assets
- Useful for backup, migration, or offline archiving

**Danger Zone**
- Delete Workspace — Permanently deletes the workspace and all its content (requires admin confirmation and a secondary confirmation step to prevent accidents)

---

## Workspace Members

### Inviting Members

A workspace admin can invite new members in two ways:

**Email Invitation**
- The admin goes to the Members section in Workspace Settings
- Enters one or more email addresses
- Selects the role to assign to the invited user(s)
- Clicks "Send Invite"
- An invitation email is sent to each address containing a link
- The recipient clicks the link, creates or logs into their Notelian account, and is immediately added to the workspace

**Invite Link**
- The admin generates a shareable invite link from the Members settings
- Copying and sharing the link allows anyone with the link to join the workspace
- The link can be configured with a role (everyone who joins via this link gets a specific role)
- The link can be deactivated at any time to stop new members from joining via it

### Member Roles

Every member of a workspace is assigned one of three roles. Roles determine what actions a member can perform at the workspace level.

**Admin**
- Full control over the workspace
- Can invite, remove, and change roles of members
- Can change workspace name, icon, and settings
- Can delete the workspace
- Can access all pages and databases regardless of individual page permissions
- Can unlock locked pages
- There must always be at least one Admin in a workspace

**Editor**
- Can create, edit, and delete pages and databases within the workspace
- Can invite others to specific pages (but cannot manage workspace-level membership)
- Cannot access workspace settings
- Subject to individual page-level permissions (if a specific page is restricted, an Editor cannot access it unless granted access)

**Viewer**
- Can read pages and databases they have been granted access to
- Cannot create or edit any content
- Cannot leave comments (comment access is controlled at the page level)
- Suitable for stakeholders, clients, or external reviewers who need read-only access

### Removing Members

Admins can remove a member from the workspace at any time from the Members settings. After removal:
- The member immediately loses access to all workspace content
- Content created by the removed member remains in the workspace and is not deleted
- The removed member's name appears as "Former Member" in any places their account was previously referenced (comments, property values, etc.)

---

## Multiple Workspaces Per Account

A single Notelian account can be a member of multiple workspaces simultaneously. This is useful for:
- Separating personal notes from work content
- Belonging to multiple companies or teams
- Managing client projects in isolated workspaces

### Switching Workspaces

The Workspace Switcher is accessible by clicking the workspace name in the sidebar header. It shows all workspaces the current user belongs to, with the active one highlighted. Clicking any workspace in the list switches to it immediately — the sidebar, content area, and all navigation update to reflect the selected workspace.

---

## Account Settings

Accessible from the user avatar or profile area in the sidebar. Contains:

- **Profile** — Edit display name, avatar, and title
- **Email & Password** — Change email address or password (for email/password accounts)
- **Connected Accounts** — Manage Google OAuth connection
- **Notifications** — Configure notification preferences (in-app and email notifications for mentions, comments, and updates)
- **Appearance** — Toggle between Light, Dark, or System mode; set language preference
- **Active Sessions** — View and revoke active login sessions on other devices
- **Log Out** — Log out from the current session
- **Delete Account** — Permanently deletes the user's Notelian account and all associated data. Before deletion:
  1. The user must type their email address to confirm intent
  2. A final warning lists what will be deleted: profile, account settings, and membership in all workspaces
  3. **Content the user created inside workspaces is NOT deleted** — it remains in the workspace and is reassigned to "Former Member" attribution
  4. If the user is the sole Admin of any workspace, they must first transfer ownership or delete that workspace before account deletion proceeds
  5. After confirmation, the account is deleted immediately and all sessions are revoked. The action is irreversible.

---

## Technical Implementation Notes

### Authentication Library — Better Auth

User authentication is implemented using **Better Auth**, a TypeScript-first, framework-agnostic authentication library with native Next.js support.

Better Auth handles:
- Email/password sign-up with bcrypt-based secure password hashing
- Google OAuth 2.0 integration (authorization code flow)
- Email verification with time-limited, single-use tokens
- Password reset via time-limited, single-use signed links
- HTTP-only cookie-based session tokens (prevents XSS token theft)
- Session persistence with configurable expiry per account type
- Multi-device session tracking and per-device revocation

### Better Auth Admin Plugin

The **Admin Plugin** is enabled alongside the core Better Auth setup. It exposes server-side admin APIs that power the **Orbit Admin** dashboard:
- View and search all registered user accounts
- Inspect active sessions across all devices for any user
- Force-revoke sessions (force-logout a user from all devices)
- Disable or suspend user accounts at the platform level
- Promote or demote workspace-level admin roles

Admin plugin endpoints are protected and only callable by authenticated Orbit Admin sessions — they are never exposed to regular end users.

### Session Storage

Sessions are stored in the **PostgreSQL** database via Better Auth's built-in database session adapter (using Drizzle ORM). Each session record includes: user ID, session token hash, device info, creation time, and expiry time. Sessions are invalidated server-side on logout and on password change.

---

*Feature File: step-01-user-auth-and-workspaces.md*
*Step: 1 of 12 — Sign Up & Account Creation*
*Phase: MVP — Phase 1*
