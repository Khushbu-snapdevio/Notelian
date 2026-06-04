# File Storage

## Overview

File Storage handles all binary uploads in Notelian — page cover images, page icons, and media blocks (Image, Video, Audio, File). Every uploaded file is stored in object storage (S3-compatible) and served through a CDN.

**Storage provider:** Cloudflare R2 (S3-compatible, zero egress fees)
**CDN:** Cloudflare CDN (automatic — R2 public buckets are served via Cloudflare's edge network)

---

## Upload Flow

All uploads use **pre-signed URLs** — the client receives a signed URL from the API and uploads directly to R2, bypassing the Next.js server. This avoids streaming large files through the app server.

### Step-by-step

```
Client                     API Server                      Cloudflare R2
  │                              │                                │
  │── POST /api/uploads/sign ───►│                                │
  │   { type, size, mimeType }   │                                │
  │                              │── generate pre-signed PUT URL ►│
  │                              │◄─ signed URL + objectKey ──────│
  │◄─ { uploadUrl, objectKey } ──│                                │
  │                              │                                │
  │── PUT {uploadUrl} ──────────────────────────────────────────►│
  │   (file bytes, direct to R2)                                  │
  │◄─ 200 OK ────────────────────────────────────────────────────│
  │                              │                                │
  │── POST /api/uploads/confirm ►│                                │
  │   { objectKey }              │── verify object exists ───────►│
  │                              │── update storage usage ─────── DB
  │◄─ { fileUrl } ───────────────│                                │
```

### API Endpoints

| Method | Endpoint | Description | Access |
|--------|----------|-------------|--------|
| POST | `/api/uploads/sign` | Request a pre-signed PUT URL | Authenticated member |
| POST | `/api/uploads/confirm` | Confirm upload complete, record usage | Authenticated member |
| DELETE | `/api/uploads/:objectKey` | Delete a stored file | System (on block delete) |

---

## Upload Rules

### Allowed MIME Types

| Category | Allowed Types |
|----------|--------------|
| Image | `image/jpeg`, `image/png`, `image/webp`, `image/gif` |
| Video | `video/mp4`, `video/webm`, `video/quicktime` |
| Audio | `audio/mpeg`, `audio/ogg`, `audio/wav`, `audio/mp4` |
| File | Any MIME type (generic file block) |

### Per-File Size Limits

| Block Type | Max Size |
|------------|---------|
| Page cover image | 5 MB |
| Page icon (custom image) | 1 MB |
| Image block | 10 MB |
| Video block | 50 MB |
| Audio block | 50 MB |
| File block | 100 MB |

These limits are enforced at the `/api/uploads/sign` step — if the declared size exceeds the limit, the API returns a 400 error before any upload occurs.

---

## Storage Tracking

Storage usage is calculated as the sum of all file sizes stored in the workspace. Database content (text, block metadata) does not count toward storage.

## Storage Usage Enforcement

### Pre-upload check

Before issuing a pre-signed URL, the API checks:

1. Current workspace storage usage (from `WorkspaceStorageUsage.bytes_used`)
2. Requested file size

### Usage tracking

- `WorkspaceStorageUsage.bytes_used` is incremented on upload confirm
- `WorkspaceStorageUsage.bytes_used` is decremented when a file is deleted (block delete, page permanent delete, workspace delete)
- Usage is updated atomically to prevent race conditions

---

## Storage Usage UI

Shown in **Workspace Settings → General** (visible to all members, admin can see full detail):

```
┌────────────────────────────────────────────────┐
│ Storage                                         │
│                                                 │
│  [████████░░░░░░░░░░░░░░] 4.2 GB used           │
│                                                 │
└────────────────────────────────────────────────┘
```

- Progress bar turns amber at 90% used
- Progress bar turns red at 100% used (new uploads blocked)

---

## File Deletion

Files are deleted from R2 when:

| Trigger | Behavior |
|---------|---------|
| Block deleted from editor | Delete file from R2; decrement usage |
| Page permanently deleted | Delete all file blocks on the page from R2; decrement usage |
| Workspace deleted | Delete all workspace files from R2; usage record dropped |
| Upload aborted / not confirmed within 30 minutes | Object is cleaned up by a pg-boss job (stale upload cleanup) |

Deletes from R2 are async — queued as a pg-boss job so the UI is not blocked.

---

## Object Key Structure

```
{workspaceId}/{pageId}/{uuid}.{ext}
```

Example: `wsp_abc123/pg_xyz789/img_k3j9x2a.webp`

- Never publicly guessable — UUIDs are random
- CDN public URL: `https://cdn.notelian.app/{objectKey}`

---

## Data Model

```
FileUpload
├── id                  (uuid, primary key)
├── workspace_id        (foreign key → Workspace)
├── page_id             (foreign key → Page, nullable — for page cover/icon)
├── block_id            (foreign key → Block, nullable — for media blocks)
├── object_key          (string — R2 object key, unique)
├── file_url            (string — CDN URL)
├── mime_type           (string)
├── file_size_bytes     (integer)
├── uploaded_by         (user_id, foreign key)
├── confirmed_at        (timestamp, nullable — null until upload confirmed)
└── created_at          (timestamp)

WorkspaceStorageUsage
├── workspace_id        (foreign key → Workspace, primary key)
├── bytes_used          (bigint, default: 0)
└── updated_at          (timestamp)
```

---

## Background Jobs (pg-boss)

| Job | Schedule | Description |
|-----|----------|-------------|
| `cleanup-stale-uploads` | Every 30 minutes | Delete R2 objects for uploads not confirmed within 30 minutes (abandoned uploads) |
| `sync-storage-usage` | Daily | Reconcile `bytes_used` against actual R2 objects for drift correction |

---

## Business Rules

1. Files are uploaded directly to R2 via pre-signed URL — they never transit the Next.js app server.
2. Pre-signed PUT URLs expire after **15 minutes** — the client must complete the upload within this window.
3. An upload is not recorded until `/api/uploads/confirm` is called with the object key.
4. Storage quota is checked before issuing a pre-signed URL — a quota breach blocks the upload before any bytes are sent.
5. File deletion from R2 is always async via pg-boss — the UI reflects deletion immediately but R2 cleanup happens in the background.
6. Deleting a file block decrements the workspace's `bytes_used` immediately on confirm.
7. Workspace storage usage is shown to all members.
8. CDN URLs are stable — a file's public URL does not change after upload.
9. Abandoned uploads (not confirmed within 30 minutes) are cleaned up by the stale-upload job.

---

## Out of Scope (MVP)

- Image resizing / optimization on upload (Phase 2)
- Video transcoding (embed via URL is the MVP path for large videos)
- Per-member storage breakdown
- Storage usage history / graph
- Custom storage provider (bring your own S3)
