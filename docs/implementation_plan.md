# YouTube Downloader (Local MVP)

## 1. Project Overview

This project is a **local, stateless YouTube Downloader application** built using:

- **Frontend:** Next.js 14 (App Router)
- **Backend:** FastAPI (Python)
- **Downloader Engine:** yt-dlp
- **Containerization:** Docker + docker-compose

### Core Principles

- Stateless architecture
- No database
- No authentication
- No external services (Redis, queues, etc.)
- Temporary file storage in `/tmp`
- Immediate file deletion after response
- Local development only
- Clean, simple MVP implementation

---

# 2. High-Level Architecture

```
User (Browser)
      │
      ▼
Next.js Frontend (Container)
      │  HTTP (internal docker network)
      ▼
FastAPI Backend (Container)
      │
      ▼
yt-dlp (subprocess CLI)
      │
      ▼
/tmp (temporary file storage)
      │
      ▼
StreamingResponse → Browser Download
      │
      ▼
File Deleted Immediately
```

---

# 3. Functional Requirements

## 3.1 Supported Features

- Download single video
- Download full playlist
- Download video (all available qualities including 4K)
- Download audio
- Dynamic quality detection from YouTube
- Display:
  - Title
  - Thumbnail
  - Duration
  - Available formats

---

# 4. Backend Design (FastAPI)

## 4.1 Folder Structure

```
backend/
│
├── app/
│   ├── main.py
│   ├── routers/
│   │    └── download.py
│   ├── services/
│   │    └── ytdlp_service.py
│   ├── schemas/
│   │    └── video.py
│   └── utils/
│        └── file_manager.py
│
├── requirements.txt
├── Dockerfile
```

---

## 4.2 Core Backend Responsibilities

### 1️⃣ Fetch Video/Playlist Info
- Call yt-dlp with `--dump-json`
- Parse response
- Extract:
  - title
  - duration
  - thumbnail
  - formats
- Return structured JSON

### 2️⃣ Download Video/Audio
- Receive `url`, `format_id`, `mode`
- Generate unique filename in `/tmp`
- Execute yt-dlp via subprocess
- Wait for completion
- Stream file via `StreamingResponse`
- Delete file after streaming finishes

---

## 4.3 API Contract

### POST /api/info

Request:

```json
{
  "url": "string",
  "type": "video" | "playlist"
}
```

Response:

```json
{
  "title": "string",
  "duration": 123,
  "thumbnail": "string",
  "formats": [
    {
      "format_id": "string",
      "resolution": "string",
      "ext": "string",
      "filesize": 12345678
    }
  ]
}
```

---

### POST /api/download

Request:

```json
{
  "url": "string",
  "format_id": "string",
  "mode": "video" | "audio"
}
```

Response:
- Streaming file download
- Auto delete after completion

---

# 5. Playlist Strategy

For playlists:

- yt-dlp downloads videos sequentially
- Files saved temporarily in `/tmp`
- All files compressed into single ZIP file
- ZIP streamed to browser
- All temporary files deleted immediately

---

# 6. Audio Strategy

- Use yt-dlp audio extraction mode
- Include ffmpeg in Docker image
- Support MP3 output

---

# 7. Frontend Design (Next.js 14)

## 7.1 Folder Structure

```
frontend/
│
├── app/
│   ├── page.tsx
│   ├── components/
│   │    ├── UrlForm.tsx
│   │    ├── VideoPreview.tsx
│   │    ├── QualitySelector.tsx
│   │    └── DownloadButton.tsx
│
├── services/
│   └── api.ts
│
├── Dockerfile
```

---

## 7.2 UI Flow

1. User enters URL
2. Click "Fetch Info"
3. Show:
   - Thumbnail
   - Title
   - Duration
   - Quality dropdown
4. User selects format
5. Click "Download"
6. Browser starts direct download

No progress bar (MVP simplicity).

---

# 8. Docker Strategy

## 8.1 Services

- frontend
- backend

Both connected via internal Docker network.

Frontend calls backend using:

```
http://backend:8000
```

---

## 8.2 Backend Docker Requirements

- Python base image
- Install:
  - yt-dlp
  - ffmpeg
- Expose port 8000

---

## 8.3 Frontend Docker Requirements

- Node base image
- Install dependencies
- Run Next.js dev server
- Expose port 3000

---

# 9. Error Handling Strategy

Handle:

- Invalid URL
- yt-dlp failure
- Unsupported format
- Empty playlist
- Download interruption

Return clear JSON error messages.

---

# 10. Security Considerations (Minimal – Local Only)

- No public exposure
- No authentication
- No rate limiting
- Internal Docker network communication only

---

# 11. File Lifecycle Management

1. Create file in `/tmp`
2. Stream to client
3. Ensure deletion using:
   - try/finally
   - background task cleanup

Prevent orphan files.

---

# 12. Development Phases

## Phase 1 – Backend Core
- Setup FastAPI
- Implement /api/info
- Implement /api/download (single video)
- Test locally

## Phase 2 – Playlist + ZIP
- Implement playlist detection
- ZIP compression
- Cleanup logic

## Phase 3 – Frontend
- Build URL form
- Integrate API
- Render preview
- Trigger download

## Phase 4 – Dockerization
- Backend Dockerfile
- Frontend Dockerfile
- docker-compose.yml
- Test full local stack

---

# 13. Non-Goals (Out of Scope)

- Authentication system
- Database
- Cloud deployment
- Rate limiting
- Background job queue
- Monetization
- Analytics

---

# 14. Future Improvements (Optional)

- Progress tracking
- Better format filtering
- UI improvements
- Automatic yt-dlp update script
- Domain + HTTPS via Cloudflare

---

# 15. Final Notes

This implementation prioritizes:

- Simplicity
- Local execution
- Clean architecture
- Docker-first development
- Minimal dependencies

The system is intentionally designed as a small, controlled, educational MVP tool for personal use only.

