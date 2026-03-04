# Project Constitution

## YouTube Downloader – Local MVP

This document defines the **engineering principles, constraints, and decision rules** governing this project.

It serves as the single source of truth for architectural discipline and scope control.

---

# 1. Project Identity

**Type:** Educational Local Tool  
**Architecture:** Stateless  
**Environment:** Local Development Only  
**Deployment Model:** Docker-based multi-container setup  

This project is intentionally small, controlled, and purpose-built for personal use.

---

# 2. Core Principles

## 2.1 Simplicity First (MVP Discipline)

The system must always favor:

- Fewer dependencies
- Clear logic
- Minimal abstraction
- Readable structure
- Predictable behavior

If a feature increases complexity without clear value, it must be rejected.

---

## 2.2 Stateless by Design

The application MUST:

- Not use a database
- Not persist user data
- Not store metadata long-term
- Not maintain sessions
- Not rely on external caching systems

All operations must be self-contained per request.

---

## 2.3 Temporary File Policy

Files may only exist inside `/tmp`.

Rules:

1. Files must be uniquely named.
2. Files must be deleted immediately after streaming completes.
3. No leftover files are acceptable.
4. Cleanup must be guaranteed using safe mechanisms (try/finally or background task).

Zero persistent storage is allowed.

---

## 2.4 No External Services

The system must not depend on:

- Redis
- Message queues
- Background workers
- Cloud storage
- External APIs (except YouTube via yt-dlp)

The application must run fully with:

- Docker
- FastAPI
- Next.js
- yt-dlp
- ffmpeg

Nothing more.

---

# 3. Scope Boundaries

## 3.1 In Scope

- Single video download
- Playlist download
- Video formats (including 4K if available)
- Audio extraction
- Thumbnail preview
- Title and duration display
- Dynamic format listing

---

## 3.2 Out of Scope

The following are strictly prohibited unless constitution is amended:

- Authentication system
- User accounts
- Database integration
- Analytics
- Monetization
- Public production hosting
- Rate limiting
- Job queue systems
- Background async workers
- SaaS features

This project is not a startup foundation. It is a clean engineering exercise.

---

# 4. Architectural Rules

## 4.1 Backend Rules

- Must use FastAPI
- Must call yt-dlp via subprocess
- Must return structured JSON responses
- Must stream files using StreamingResponse
- Must handle errors explicitly
- Must not block indefinitely

No hidden background logic.

---

## 4.2 Frontend Rules

- Must use Next.js 14 (App Router)
- Must remain simple and minimal
- No complex state management libraries
- No UI frameworks unless strictly necessary
- Direct API communication with backend container

UI complexity must never exceed backend complexity.

---

## 4.3 Docker Rules

- Must use docker-compose
- Separate containers for frontend and backend
- Shared internal Docker network
- No unnecessary services
- Rebuild required when yt-dlp updates

The system must be runnable with a single command.

---

# 5. Error Handling Doctrine

Errors must be:

- Explicit
- Human-readable
- Returned as structured JSON
- Never swallowed silently

Common error categories:

- Invalid URL
- Unsupported format
- yt-dlp execution failure
- Empty playlist
- File processing error

---

# 6. Code Quality Standards

All code must follow:

- Clear naming
- Small functions
- Single responsibility principle
- Logical folder structure
- No dead code
- No commented-out legacy logic

If code becomes confusing, it must be refactored.

---

# 7. Security Posture

This project is local-only.

Therefore:

- No authentication required
- No rate limiting required
- No bot protection required

However:

- No arbitrary shell injection must be possible
- All subprocess calls must be safely constructed
- Inputs must be validated

Local does not mean careless.

---

# 8. Change Control

Any feature that introduces:

- State persistence
- Background task systems
- External services
- Public hosting

Requires explicit revision of this constitution.

Scope creep must be resisted.

---

# 9. Operational Discipline

The system must:

1. Build successfully via Docker
2. Start both containers cleanly
3. Allow local access via browser
4. Perform full download cycle without manual intervention
5. Leave no residual temporary files

If any of these fail, the build is not acceptable.

---

# 10. Educational Intent

This project exists to:

- Practice clean backend architecture
- Practice Docker-based development
- Understand request/response lifecycle
- Learn file streaming mechanics
- Learn subprocess management

It is not intended for commercial deployment.

---

# 11. Amendment Rule

This constitution may be modified only if:

- The project goal changes explicitly
- A new architectural direction is agreed upon
- Complexity increase is justified

Otherwise, simplicity prevails.

---

# Final Statement

This project values:

Clarity over cleverness.  
Simplicity over scalability.  
Control over expansion.  

The system must remain small, understandable, and disciplined.

