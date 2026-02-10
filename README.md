# Anonymous ID-Based Photo Hosting

## Overview

This project implements an anonymous photo hosting system that removes traditional user accounts entirely.
Instead of emails or passwords, users are identified by a randomly generated Space ID that directly maps to a storage bucket.
The system exists to simplify access control, reduce personal data handling, and support low-friction uploads.

The core idea is capability-based access.
Possession of a Space ID grants full control over the associated photos.
There is no identity verification, recovery flow, or user database, which keeps the system intentionally minimal and predictable.

## Architecture

```
User Browser
    |
    v
Web Interface
    |
    v
Backend API
    |
    v
Static Storage (Buckets)
    |
    v
Public Image URLs
```

The browser interacts with a simple web interface for creating spaces and uploading photos.
The backend API is responsible for generating Space IDs, creating buckets, and committing files.
Static storage serves images directly to clients through public URLs, reducing backend responsibility.

## Repository / Resource Structure

The repository structure represents the entire system state.
Each folder corresponds to one anonymous user space and contains only that user’s uploaded files.

```
/buckets
├── pht_A1B2C3D4/
│   ├── img_001.webp
│   └── img_002.webp
├── pht_X9Y8Z7Q6/
│   └── img_001.webp
```

The presence of a folder defines ownership and validity of a Space ID.
No external database is required because state is derived directly from deterministic folder paths.
File naming conventions are used to avoid collisions and simplify listing operations.

## Workflow

1. A user visits the site and requests a new photo space.
2. The backend generates a cryptographically strong random Space ID.
3. A bucket folder is created for that Space ID.
4. The user uploads one or more photos into the bucket.
5. Each photo is stored as a static file and assigned a public URL.
6. On return, the user enters the same Space ID to access previous uploads.

## Automation Logic

Automation is triggered by user actions such as space creation and photo upload.
Each upload is processed individually to avoid partial failures and to simplify retries.
Rate limits, file size limits, and file type checks are enforced to maintain stability.
All write operations are idempotent, ensuring repeated requests do not corrupt state.

## External Integrations

Static storage is used to persist and serve image files.
A source control API is used for authenticated read and write access to storage.
A CDN layer provides caching and efficient delivery of image content.

## State & Metadata Handling

The system does not maintain a separate metadata database.
State is inferred from folder existence and file listings within each bucket.
Progress and validity checks are performed by verifying known paths rather than stored records.
Duplicate processing is avoided through deterministic naming and existence checks before writes.

## Security Considerations

No personal or identifying information is collected or stored.
Access control relies on unguessable Space IDs with sufficient entropy.
API tokens are stored server-side and scoped to the minimum required permissions.
Public access is restricted to static read-only file delivery.

## Tech Stack

* HTML, CSS, JavaScript
* Backend service (Node.js or Python)
* Git-based static storage
* CDN-backed static hosting

## Roadmap

* Automatic expiration and cleanup of inactive buckets
* Optional read-only sharing links
* Per-space upload quotas
* Lightweight indexing for faster file listing

## Final Notes

This system prioritizes simplicity, anonymity, and deterministic behavior.
It avoids overengineering by deriving state from structure rather than databases.
The design favors reliability through stateless operations and static delivery.
