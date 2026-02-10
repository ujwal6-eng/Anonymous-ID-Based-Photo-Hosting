```md
# 🎥 Automated Video Upload & Website Integration System

## Overview

This repository contains a **fully automated video processing pipeline** that connects a website, Google Drive, GitHub Actions, and YouTube.

The system uploads videos from a website, processes them automatically, uploads them to YouTube as **private or unlisted**, and embeds them back on the website without any manual steps.

---

## System Flow

Website Upload  
↓  
Google Drive (pending)  
↓  
GitHub Actions  
↓  
YouTube (Private / Unlisted)  
↓  
Website Embed  

---

## Google Drive Folder Structure

```

/video-system
├── pending/        # New uploads (queue)
├── processing/     # Currently processing
├── uploaded/       # Successfully uploaded
└── failed/         # Failed uploads

```

Google Drive folders act as a queue and status tracker.

---

## Step 1: Website → Google Drive Upload

- User uploads video from the website  
- Upload goes directly from browser to Google Drive  
- Google OAuth 2.0 is used (`drive.file` scope)  
- File is placed in the `pending/` folder  

This avoids server load and supports large file uploads.

---

## Step 2: Pending Folder = Queue

- `pending/` contains videos waiting for processing  
- Automation **only reads from `pending/`**  
- Prevents duplicate processing  

---

## Step 3: GitHub Actions Automation

GitHub Actions acts as a serverless worker.

Automation tasks:

1. Read video from `pending/`
2. Move file to `processing/`
3. Upload video to YouTube
4. Generate metadata
5. Notify website backend
6. Move file to `uploaded/` or `failed/`

---

## Step 4: YouTube Upload

- Privacy: `private` or `unlisted`
- OAuth 2.0 authentication
- YouTube handles all streaming and bandwidth

Generated link:

```

[https://www.youtube.com/watch?v=VIDEO_ID](https://www.youtube.com/watch?v=VIDEO_ID)

````

---

## Step 5: Metadata Generation

After upload, metadata is generated.

Metadata includes:

- video_id
- youtube_url
- original_filename
- drive_file_id
- upload_time
- status

Metadata is the single source of truth.

---

## Step 6: Website Integration

- Automation sends metadata to website backend
- Website stores metadata
- Video is embedded automatically

Embed example:

```html
<iframe src="https://www.youtube.com/embed/VIDEO_ID" allowfullscreen></iframe>
````

---

## Step 7: Finalization

* Success → move to `uploaded/`
* Failure → move to `failed/`

Folders show state.
Metadata shows truth.

---

## Security

* No API keys in frontend
* OAuth tokens stored as GitHub Secrets
* Limited Google Drive scope
* Videos are not public by default

---

## Tech Stack

* Website: Any frontend
* Upload Queue: Google Drive
* Automation: GitHub Actions
* Video CDN: YouTube
* Metadata: API / Database / JSON

---

## Notes

This project forms the base of a **private video distribution system**.

Simple design.
Automated flow.
No unnecessary complexity.

```
```

