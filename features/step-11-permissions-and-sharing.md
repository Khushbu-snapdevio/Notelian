# Step 11 — Permissions & Sharing

## Overview

Notelian's permissions system controls who can see and do what within a workspace. Permissions operate at two levels: the **workspace level** (roles assigned to workspace members) and the **page level** (access settings on individual pages and databases). Together, these two layers give workspace admins and page owners precise control over access — no more, no less.

Sharing extends access beyond the workspace to external users through public links and guest invitations.

---

## Permission Layers

### Layer 1 — Workspace Roles

Every workspace member has a role that defines their baseline capabilities across the entire workspace. See the User Authentication & Workspaces feature for full role definitions. In summary:

- **Admin** — Full access to all workspace content and settings
- **Editor** — Can create and edit content; subject to page-level restrictions
- **Viewer** — Read-only access to content they have been granted access to

Workspace roles are the floor of what a member can do. Page-level permissions can further restrict (but never expand) what an Editor or Viewer can do on a specific page.

### Layer 2 — Page-Level Permissions

Each page and database in Notelian has its own access settings that control what different members can do specifically on that page.

---

## Page Access Levels

When setting permissions on a page, four access levels are available:

### Full Access
The member can do everything on the page:
- Read all content
- Edit and delete all blocks
- Add and delete subpages
- Manage the page's own permission settings (add/remove members, change access levels)
- Share the page externally
- Delete the page itself

### Can Edit
The member can:
- Read all content
- Edit existing blocks and add new ones
- Add comments
- Cannot manage permissions or share the page externally

### Can Comment
The member can:
- Read all content
- Leave comments on blocks
- Reply to existing comment threads
- Cannot edit any page content or manage permissions

### Can View
The member can:
- Read all content on the page
- Cannot comment, edit, or manage permissions

---

## Permission Inheritance

Notelian uses a **top-down inheritance model** for page permissions.

When a page's permissions are set, all subpages nested under it **inherit** those permissions by default. This means:
- If a parent page is set to "Can View" for a member, all its subpages are also "Can View" for that member — unless the subpage has its own overriding permissions set
- Changing the parent page's permissions automatically applies down the tree to all subpages that have not had their own custom permissions explicitly set

### Overriding Inherited Permissions

Any subpage can have its own independent permission settings that override what it would inherit from its parent. When a subpage has custom permissions set:
- A visual indicator shows that this page has custom permissions (not using inherited settings)
- The subpage's permissions are completely independent of its parent's — changing the parent no longer affects it
- Custom permissions on a subpage can be removed ("reset to inherited") to go back to inheriting from the parent

This model makes it easy to have a large workspace with sensible defaults (e.g., everything is shared with the full team) while still creating isolated sections for sensitive content (e.g., a Finance subpage with access restricted to only the Finance team).

---

## Setting Page Permissions

Page permissions are managed through the **Share & Permissions panel**, accessible from:
- The "Share" button in the top-right area of any page
- The page options menu (`···`) → "Permissions"

### The Share & Permissions Panel

The panel shows:
- The current permission settings for all members who have explicit access to this page
- An "Invite" input to search for and add workspace members with a specified access level
- The current public sharing status (link sharing on or off)
- An inheritance status indicator (inherited from parent vs. custom)

### Adding a Member to a Page
1. The user opens the Share & Permissions panel
2. Types the member's name or email in the invite field
3. Selects the access level from a dropdown (Full Access, Can Edit, Can Comment, Can View)
4. Clicks "Invite" or presses `Enter`
5. The member immediately gains access and receives a notification that they have been granted access to the page

### Changing a Member's Access Level
In the permissions panel, each listed member shows their current access level as a dropdown. Clicking the dropdown and selecting a new level updates their access immediately.

### Removing a Member's Access
Next to each listed member is a remove button. Clicking it revokes that member's explicit access to the page. If the page uses inheritance, the member reverts to the inherited permission level. If the page does not inherit (i.e., it is in a restricted section), removing them gives them no access to that page.

---

## Workspace-Wide Defaults

Workspace admins can set a **default permission level** for new pages created in the workspace. Options:
- **Private by default** — New pages are only visible to their creator until explicitly shared
- **Shared by default** — New pages are visible and editable by all workspace members

The default can be changed at any time and affects only newly created pages — existing pages are not modified.

---

## Private Pages

Any page can be set to **Private**, meaning only the creator (and explicitly invited members) can see or access it.

Private pages:
- Do not appear in the sidebar for other workspace members
- Are not returned in workspace-wide search results for other members — **including workspace Admins.** Admin role does not bypass the private page search exclusion; Admins cannot discover private pages through in-product search.
- Workspace Admins can view a list of private page **titles** (not content) through the **Orbit Admin** internal dashboard for compliance and audit purposes. This access is limited to the Notelian operations team and is not an in-product Admin capability.
- If a workspace Admin needs access to a specific private page (e.g., for a content dispute or offboarding), this is handled via Orbit Admin tooling — not through the normal product UI.

A page's private status is shown with a lock icon in the sidebar.

---

## Sharing via Public Link

Any page can be shared externally via a **public link** — a URL that does not require the recipient to have a Notelian account.

### Enabling Public Link Sharing

In the Share & Permissions panel, there is a "Public Link" section with a toggle. When turned on:
- A unique URL is generated for the page
- Anyone with the URL can access the page according to the configured public access level

### Public Link Access Levels

- **Can View** — External visitors can read the page but cannot comment or edit
- **Can Comment** — External visitors can read and leave comments but cannot edit

There is no public "Can Edit" option — editing always requires a Notelian account and workspace membership.

**Password protection:** Public links do not support password protection in Phase 1. All public links are accessible to anyone with the URL. Password-protected public links are a Phase 2 feature.

### Disabling Public Link Sharing

Turning the public link toggle off immediately revokes access. The previously generated URL no longer works. If the link is re-enabled later, a new unique URL is generated (the old URL remains permanently inactive).

### Public Link Behavior

- Public links display the page content as a clean, read-only web view (no sidebar, no workspace navigation)
- The page title and icon are visible
- Subpages of a shared page are NOT automatically made public — they require their own public link to be enabled
- Public link access cannot override private subpage restrictions

---

## Guest Access

Guests are external users (outside the workspace's regular membership) who are given access to specific pages via email invitation, without becoming full workspace members.

### How Guest Access Works

- An editor or admin with Full Access on a page can invite an external email address to that page
- The external user receives an invitation email with a link
- **The invitation link expires after 7 days.** If the guest does not accept within 7 days, the inviting user must resend the invitation from the page's permission panel.
- They create a Notelian account (if they don't have one) or log in
- They gain access only to the specific page(s) they were invited to — the rest of the workspace is invisible to them
- Their access level is set at invitation time (Can View, Can Comment, or Can Edit)

### Guest Limitations

- Guests cannot see the workspace sidebar or navigate to any pages they were not explicitly invited to
- Guests cannot create new pages in the workspace
- Guests cannot be mentioned using @mention in workspace-wide contexts (they only appear in @mention suggestions on pages they have access to)
- Guests are listed separately from full members in the workspace Members settings

### Revoking Guest Access

The page owner or admin removes the guest from the page's permission list. The guest immediately loses access and receives no notification (to avoid confusion). They cannot log in to access that page anymore.

---

## Permission Summary Matrix

**Note:** "Admin" and "Editor" refer to workspace-level roles. "Full Access", "Can Edit", "Can Comment", and "Can View" refer to page-level permission settings. "Guest" refers to external users granted page-specific access (not full workspace members).

| Action | Workspace Admin | Workspace Editor | Page: Full Access | Page: Can Edit | Page: Can Comment | Page: Can View | Guest (Edit) | Guest (Comment) | Guest (View) |
|---|---|---|---|---|---|---|---|---|---|
| Read page content | Yes | Yes | Yes | Yes | Yes | Yes | Yes | Yes | Yes |
| Edit blocks | Yes | Yes | Yes | Yes | No | No | Yes | No | No |
| Add comments | Yes | Yes | Yes | Yes | Yes | No | Yes | Yes | No |
| Manage page permissions | Yes | No | Yes | No | No | No | No | No | No |
| Share via public link | Yes | No | Yes | No | No | No | No | No | No |
| Delete page | Yes | Yes | Yes | No | No | No | No | No | No |
| Create subpages | Yes | Yes | Yes | Yes | No | No | No | No | No |
| Access workspace settings | Yes | No | No | No | No | No | No | No | No |
| Appear in workspace sidebar | Yes | Yes | Yes | Yes | Yes | Yes | No | No | No |

---

## Platform-Level Administration — Orbit Admin

Beyond what workspace Admins can do within the end-user product, Notelian operates an internal **Orbit Admin** dashboard powered by the **Better Auth Admin Plugin**. Orbit Admin is not accessible to end users — it is an internal operations tool for the Notelian engineering and operations team.

Through Orbit Admin, the platform team can:

| Action | Purpose |
|---|---|
| View all registered user accounts | Platform-wide user audit and support |
| Inspect active sessions for any user | Diagnose account access issues |
| Force-revoke sessions (force-logout any user across all devices) | Security incident response |
| Disable / suspend user accounts | Policy enforcement, abuse prevention |
| View workspace membership and ownership | Cross-workspace visibility for compliance |
| Override workspace admin roles | Emergency access for critical support scenarios |

These capabilities exist at the platform layer and are entirely separate from the workspace Admin role that end users hold. A workspace Admin has elevated permissions within their workspace — the platform team has cross-workspace visibility via Orbit Admin.

---

*Feature File: step-11-permissions-and-sharing.md*
*Step: 11 of 12 — Permissions & Sharing*
*Phase: MVP — Phase 1*
