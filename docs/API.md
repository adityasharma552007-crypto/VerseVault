# VerseVault — API Specification

**Version:** 1.0
**Status:** Planning
**API Style:** REST
**Base URL:** `/api/v1`

---

# 1. Purpose

This document defines the contract between the VerseVault frontend and backend.

It specifies:

* Available endpoints
* HTTP methods
* Authentication requirements
* Request formats
* Response formats
* Error formats
* Pagination
* Authorization rules
* Resource relationships
* Common API conventions

The frontend and backend should follow this contract.

---

# 2. API Architecture

```text
                    VerseVault Web
                         │
                         │ HTTPS
                         ▼
                  ┌───────────────┐
                  │   REST API    │
                  │ /api/v1/...  │
                  └───────┬───────┘
                          │
                 ┌────────┴────────┐
                 │                 │
                 ▼                 ▼
              Services          Validation
                 │
                 ▼
              MongoDB
```

The frontend should never communicate directly with MongoDB.

---

# 3. Base URL

All API endpoints use:

```text
/api/v1
```

Example:

```text
/api/v1/auth/login
```

In development, the frontend may communicate with:

```text
http://localhost:<api-port>/api/v1
```

The exact development port will be decided during project initialization.

---

# 4. HTTP Methods

VerseVault follows standard REST conventions.

| Method   | Purpose                         |
| -------- | ------------------------------- |
| `GET`    | Retrieve data                   |
| `POST`   | Create data / perform an action |
| `PATCH`  | Partially update data           |
| `DELETE` | Delete data                     |

Example:

```text
GET     /works/:id
POST    /works
PATCH   /works/:id
DELETE  /works/:id
```

---

# 5. Authentication

Protected requests use the authenticated user's secure HTTP-only cookie.

The browser automatically sends the cookie with appropriate requests.

The frontend should not manually store authentication secrets in:

```text
localStorage
sessionStorage
```

The API determines the current user from the authenticated session.

---

# 6. Authentication States

A request is either:

```text
Public
```

or:

```text
Authenticated
```

or:

```text
Authenticated + Authorized
```

Example:

```text
GET /works/some-story
```

may be public.

While:

```text
PATCH /works/:id
```

requires authentication **and ownership**.

---

# 7. Standard Success Response

Successful responses should follow:

```json id="f7k0tr"
{
  "success": true,
  "data": {}
}
```

For collections:

```json id="9m8x0q"
{
  "success": true,
  "data": [],
  "pagination": {
    "nextCursor": null,
    "hasMore": false
  }
}
```

---

# 8. Standard Error Response

Errors should follow:

```json id="6f8k9v"
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable message."
  }
}
```

The API should not expose:

* Stack traces
* Database credentials
* Internal implementation details
* Sensitive server information

---

# 9. Common HTTP Status Codes

| Status | Meaning                                  |
| -----: | ---------------------------------------- |
|  `200` | Successful request                       |
|  `201` | Resource created                         |
|  `204` | Successful request with no response body |
|  `400` | Invalid request                          |
|  `401` | Not authenticated                        |
|  `403` | Authenticated but not authorized         |
|  `404` | Resource not found                       |
|  `409` | Conflict                                 |
|  `422` | Validation failure                       |
|  `429` | Rate limit exceeded                      |
|  `500` | Internal server error                    |

---

# 10. Authentication API

## Register

```http
POST /api/v1/auth/register
```

### Request

```json id="w2f5r4"
{
  "username": "aditya",
  "name": "Aditya Sharma",
  "email": "aditya@example.com",
  "password": "********"
}
```

### Server responsibilities

* Validate input
* Normalize email
* Normalize username
* Check username uniqueness
* Check email uniqueness
* Hash password
* Create user
* Create authenticated session
* Set secure HTTP-only cookie

### Response

```json id="h9zz3b"
{
  "success": true,
  "data": {
    "user": {
      "id": "...",
      "username": "aditya",
      "name": "Aditya Sharma"
    }
  }
}
```

The response must never include `passwordHash`.

---

# 11. Login

```http
POST /api/v1/auth/login
```

### Request

```json id="3y2u6c"
{
  "email": "aditya@example.com",
  "password": "********"
}
```

### Response

```json id="p8e5e4"
{
  "success": true,
  "data": {
    "user": {
      "id": "...",
      "username": "aditya",
      "name": "Aditya Sharma"
    }
  }
}
```

The server sets the authenticated HTTP-only cookie.

---

# 12. Logout

```http
POST /api/v1/auth/logout
```

Authentication required.

### Response

```json id="9b7z5r"
{
  "success": true,
  "data": null
}
```

The server invalidates the session/authentication state.

---

# 13. Current User

```http
GET /api/v1/auth/me
```

Authentication required.

### Response

```json id="2x7x5r"
{
  "success": true,
  "data": {
    "user": {
      "id": "...",
      "username": "aditya",
      "name": "Aditya Sharma",
      "bio": "...",
      "avatarUrl": "..."
    }
  }
}
```

---

# 14. Profile API

## Get Public Profile

```http
GET /api/v1/users/:username
```

Public.

Example:

```text
GET /api/v1/users/aditya
```

Response:

```json id="e4x4c5"
{
  "success": true,
  "data": {
    "user": {
      "id": "...",
      "username": "aditya",
      "name": "Aditya Sharma",
      "bio": "...",
      "avatarUrl": "..."
    },
    "stats": {
      "works": 12,
      "followers": 120,
      "following": 85
    }
  }
}
```

---

# 15. Update Own Profile

```http
PATCH /api/v1/users/me
```

Authentication required.

### Request

```json id="s0gq1r"
{
  "name": "Aditya",
  "bio": "Writer and developer.",
  "website": "https://example.com"
}
```

Users may only modify their own profile.

---

# 16. Folder API

Folders are primarily private workspace organization tools.

---

## Create Folder

```http
POST /api/v1/folders
```

Authentication required.

### Request

```json id="a9z9s1"
{
  "name": "Story Draft 1",
  "description": "My first long-form story.",
  "coverImageUrl": "..."
}
```

### Response

```json id="c4m7jk"
{
  "success": true,
  "data": {
    "folder": {}
  }
}
```

---

# 17. Get My Folders

```http
GET /api/v1/folders
```

Authentication required.

Returns folders owned by the current user.

---

# 18. Get Folder

```http
GET /api/v1/folders/:id
```

Authentication required.

The API must verify that the current user owns the folder.

---

# 19. Update Folder

```http
PATCH /api/v1/folders/:id
```

Authentication required.

### Request

```json id="z6br8s"
{
  "name": "Story Draft 1 — Revised",
  "description": "Updated description.",
  "coverImageUrl": "..."
}
```

---

# 20. Delete Folder

```http
DELETE /api/v1/folders/:id
```

Authentication required.

Deleting a folder should **not automatically delete its works**.

Works should become:

```text
folderId = null
```

unless a different product rule is introduced later.

---

# 21. Get Folder Works

```http
GET /api/v1/folders/:id/works
```

Authentication required.

Returns works belonging to the folder.

Supports pagination.

Example:

```text
GET /api/v1/folders/123/works?limit=20&cursor=...
```

---

# 22. Work API

Works are the core content resource.

---

## Create Work

```http
POST /api/v1/works
```

Authentication required.

### Request

```json id="3bq8y4"
{
  "title": "Chapter 1",
  "content": "Once upon a time...",
  "folderId": "..."
}
```

`folderId` is optional.

### Server responsibilities

* Validate content
* Verify folder ownership if supplied
* Create work
* Assign author from authenticated user
* Set initial status to `draft`

The client must not supply:

```text
authorId
createdAt
updatedAt
status
```

These are server-controlled.

---

# 23. Get Work

```http
GET /api/v1/works/:slug
```

Public if the work is public.

Private/draft works require ownership authentication.

Example:

```text
GET /api/v1/works/the-last-evening
```

---

# 24. Update Work

```http
PATCH /api/v1/works/:id
```

Authentication required.

Only the work owner can modify it.

### Request

```json id="2g3z4s"
{
  "title": "Chapter 1 — The Beginning",
  "content": "Updated content...",
  "folderId": "..."
}
```

---

# 25. Move Work

A work may be moved between folders.

```http
PATCH /api/v1/works/:id
```

Example:

```json id="s7w2j0"
{
  "folderId": "new-folder-id"
}
```

The backend must verify that the target folder belongs to the current user.

A work may also be removed from a folder:

```json id="3v2p4z"
{
  "folderId": null
}
```

---

# 26. Delete Work

```http
DELETE /api/v1/works/:id
```

Authentication required.

Only the owner may delete the work.

Deletion behavior will follow the rules defined in `SECURITY.md`.

---

# 27. Publish Work

```http
POST /api/v1/works/:id/publish
```

Authentication required.

Only the owner can publish.

### Server responsibilities

* Verify ownership
* Validate publishing requirements
* Set status to `published`
* Set visibility to `public`
* Generate/confirm slug
* Set `publishedAt`

### Response

```json id="9w5q3e"
{
  "success": true,
  "data": {
    "work": {}
  }
}
```

---

# 28. Unpublish Work

```http
POST /api/v1/works/:id/unpublish
```

Authentication required.

The work returns to:

```text
draft
```

or another explicitly defined private state.

It should no longer appear in public discovery.

---

# 29. Archive Work

```http
POST /api/v1/works/:id/archive
```

Authentication required.

Archived works remain associated with the owner but are excluded from normal active views.

---

# 30. Explore API

```http
GET /api/v1/explore
```

Public.

Returns discoverable public works.

Possible query parameters:

```text
limit
cursor
sort
```

Initial sorting options may include:

```text
latest
popular
```

Additional ranking systems can be introduced later.

---

# 31. Search API

```http
GET /api/v1/search
```

Public.

Example:

```text
GET /api/v1/search?q=love
```

Possible parameters:

```text
q
type
limit
cursor
```

Possible `type` values:

```text
works
users
all
```

---

# 32. Likes

## Like Work

```http
POST /api/v1/works/:id/like
```

Authentication required.

Response:

```json id="x5j3j6"
{
  "success": true,
  "data": {
    "liked": true
  }
}
```

Duplicate likes must be prevented by both:

* Backend logic
* Database unique index

---

# 33. Unlike Work

```http
DELETE /api/v1/works/:id/like
```

Authentication required.

Response:

```json id="8xq6m2"
{
  "success": true,
  "data": {
    "liked": false
  }
}
```

---

# 34. Like Status

When needed:

```http
GET /api/v1/works/:id/like
```

Authentication required.

Response:

```json id="c4q3h9"
{
  "success": true,
  "data": {
    "liked": true
  }
}
```

For feeds containing many works, the API should eventually avoid making one request per work.

---

# 35. Bookmarks

## Bookmark Work

```http
POST /api/v1/works/:id/bookmark
```

Authentication required.

---

## Remove Bookmark

```http
DELETE /api/v1/works/:id/bookmark
```

Authentication required.

---

## My Bookmarks

```http
GET /api/v1/bookmarks
```

Authentication required.

Supports:

```text
limit
cursor
```

---

# 36. Reposts

## Quote Repost

```http
POST /api/v1/works/:id/repost
```

Authentication required.

### Request

```json id="0xqv3x"
{
  "quote": "This is exactly what I've been trying to say."
}
```

The quote is optional only if the product later supports plain reposts.

For the current design, quote reposts are the intended behavior.

---

## Remove Repost

```http
DELETE /api/v1/works/:id/repost
```

Authentication required.

---

## Reposts of a Work

```http
GET /api/v1/works/:id/reposts
```

Public for public works.

---

# 37. Comments

## Create Comment

```http
POST /api/v1/works/:id/comments
```

Authentication required.

### Request

```json id="h8v8cc"
{
  "content": "This was beautiful."
}
```

---

# 38. Reply to Comment

```http
POST /api/v1/comments/:commentId/replies
```

Authentication required.

### Request

```json id="t8i6ay"
{
  "content": "I thought so too!"
}
```

The backend verifies that the parent comment exists.

---

# 39. Get Comments

```http
GET /api/v1/works/:id/comments
```

Public for public works.

Supports:

```text
limit
cursor
sort
```

---

# 40. Update Comment

```http
PATCH /api/v1/comments/:id
```

Authentication required.

Only the comment author may edit it.

### Request

```json id="v8d5t0"
{
  "content": "Updated comment."
}
```

---

# 41. Delete Comment

```http
DELETE /api/v1/comments/:id
```

Authentication required.

Only:

* Comment author
* Authorized moderator/admin

may delete or hide a comment.

---

# 42. Following

## Follow User

```http
POST /api/v1/users/:id/follow
```

Authentication required.

The backend must reject:

```text
following yourself
```

---

## Unfollow User

```http
DELETE /api/v1/users/:id/follow
```

Authentication required.

---

## Followers

```http
GET /api/v1/users/:id/followers
```

Public.

Supports pagination.

---

## Following

```http
GET /api/v1/users/:id/following
```

Public.

Supports pagination.

---

# 43. Following Feed

```http
GET /api/v1/feed/following
```

Authentication required.

Returns public works from users the current user follows.

Supports:

```text
limit
cursor
```

---

# 44. Notifications

## Get Notifications

```http
GET /api/v1/notifications
```

Authentication required.

Supports:

```text
limit
cursor
```

---

# 45. Mark Notification as Read

```http
PATCH /api/v1/notifications/:id/read
```

Authentication required.

The notification must belong to the current user.

---

# 46. Mark All Notifications as Read

```http
POST /api/v1/notifications/read-all
```

Authentication required.

---

# 47. Reports

## Report Work

```http
POST /api/v1/works/:id/report
```

Authentication required.

### Request

```json id="2h8v4k"
{
  "reason": "harassment",
  "description": "Additional context..."
}
```

---

# 48. Report Comment

```http
POST /api/v1/comments/:id/report
```

Authentication required.

### Request

```json id="z1f3l6"
{
  "reason": "spam",
  "description": "Additional context..."
}
```

---

# 49. Moderation API

Admin-only.

## Get Reports

```http
GET /api/v1/admin/reports
```

Supports filtering:

```text
status
type
limit
cursor
```

---

## Review Report

```http
PATCH /api/v1/admin/reports/:id
```

Example:

```json id="f5s2o1"
{
  "status": "resolved"
}
```

The backend records:

```text
reviewedBy
reviewedAt
```

---

# 50. API Authorization Matrix

| Endpoint            | Public | Auth | Owner/Admin |
| ------------------- | :----: | :--: | :---------: |
| View public work    |    ✅   |      |             |
| Create work         |        |   ✅  |             |
| Edit work           |        |      |      ✅      |
| Publish work        |        |      |      ✅      |
| Delete work         |        |      |      ✅      |
| Like                |        |   ✅  |             |
| Bookmark            |        |   ✅  |             |
| Repost              |        |   ✅  |             |
| Comment             |        |   ✅  |             |
| Edit own comment    |        |      |      ✅      |
| Follow              |        |   ✅  |             |
| View public profile |    ✅   |      |             |
| View own drafts     |        |   ✅  |             |
| View private folder |        |      |    Owner    |
| Create report       |        |   ✅  |             |
| Review report       |        |      |    Admin    |

---

# 51. Pagination

Collection endpoints should support cursor pagination.

Example:

```text
GET /api/v1/explore?limit=20&cursor=abc123
```

Response:

```json id="8n7z9k"
{
  "success": true,
  "data": [],
  "pagination": {
    "nextCursor": "xyz789",
    "hasMore": true
  }
}
```

Default limit:

```text
20
```

Maximum limit:

```text
50
```

The exact values can be adjusted during implementation.

---

# 52. Filtering and Sorting

Where applicable:

```text
?sort=latest
?sort=popular
?status=draft
?limit=20
?cursor=...
```

Only documented parameters should be accepted.

Unknown or invalid parameters should not silently alter query behavior.

---

# 53. Slugs

Public works use human-readable slugs.

Example:

```text
/api/v1/works/the-last-evening
```

Slugs should be:

* URL-safe
* Stable
* Unique
* Generated server-side

If a title changes, the system should decide whether to preserve the existing slug or generate a new one.

For the MVP, preserving the existing public slug is preferred once published.

---

# 54. Idempotency and Duplicate Actions

The API must protect against repeated actions.

Examples:

```text
Like
Bookmark
Repost
Follow
```

Repeated requests should not create duplicate records.

Database unique indexes provide the final integrity layer.

---

# 55. Request Validation

Every endpoint receiving user input must validate the request.

Validation should happen before business logic.

```text id="u5r0la"
Request
   ↓
Schema validation
   ↓
Authentication
   ↓
Authorization
   ↓
Business logic
   ↓
Database
```

Zod will be used for request schemas.

---

# 56. Ownership Verification

The backend must never trust an ID supplied by the client.

Example:

```json id="8v7s0m"
{
  "folderId": "some-folder-id"
}
```

The server must verify:

```text
Does this folder exist?
        ↓
Does it belong to the authenticated user?
        ↓
Is it valid for this operation?
```

Only then should the operation continue.

---

# 57. Rate Limiting

Rate limiting should eventually be applied more aggressively to:

```text
login
register
comments
reports
follows
likes
reposts
search
```

The exact limits will be configured during implementation and deployment.

---

# 58. API Security Rules

The API must:

* Validate every user-controlled input
* Authenticate protected requests
* Authorize resource ownership
* Sanitize user-generated content where required
* Avoid exposing private fields
* Avoid exposing internal errors
* Apply rate limits
* Use secure cookies
* Never expose secrets
* Never directly expose database credentials

---

# 59. API Feature Modules

Backend modules should map approximately to:

```text id="2j5r8e"
auth
users
folders
works
comments
likes
bookmarks
reposts
follows
notifications
reports
search
```

Some smaller resources may later be combined into an `interactions` module.

---

# 60. Frontend API Layer

The frontend should not scatter raw `fetch()` calls throughout components.

Instead:

```text id="g0v3ak"
components
     ↓
feature API functions
     ↓
central API client
     ↓
VerseVault API
```

Example:

```text id="d6h8r4"
features/works/api.ts
```

could expose functions such as:

```text
createWork()
getWork()
updateWork()
publishWork()
deleteWork()
```

The UI should not need to know the raw endpoint structure.

---

# 61. API Error Codes

Initial error codes:

```text id="09j7sk"
VALIDATION_ERROR
INVALID_CREDENTIALS
AUTHENTICATION_REQUIRED
FORBIDDEN
USER_NOT_FOUND
WORK_NOT_FOUND
FOLDER_NOT_FOUND
COMMENT_NOT_FOUND
ALREADY_LIKED
ALREADY_BOOKMARKED
ALREADY_REPOSTED
ALREADY_FOLLOWING
CANNOT_FOLLOW_SELF
INVALID_FOLDER
DUPLICATE_USERNAME
DUPLICATE_EMAIL
REPORT_NOT_FOUND
RATE_LIMITED
INTERNAL_ERROR
```

These codes allow the frontend to handle predictable cases.

---

# 62. API Design Principles

### 1. The API is the backend contract

Frontend and backend should communicate through defined interfaces.

### 2. Never trust the client

The backend validates and authorizes every sensitive operation.

### 3. Keep controllers thin

Business logic belongs in services.

### 4. Keep responses predictable

Use consistent response structures.

### 5. Don't expose unnecessary data

Return only fields required by the client.

### 6. Prevent duplicate interactions

Use application checks and database constraints.

### 7. Design around real user actions

Endpoints should map naturally to VerseVault workflows.

### 8. Keep the MVP simple

Don't create endpoints for features that don't exist.

---

# 63. Core User Journey — API Flow

A user creating and publishing a work:

```text id="n3yd5b"
POST /auth/register
        ↓
POST /works
        ↓
PATCH /works/:id
        ↓
POST /works/:id/publish
        ↓
GET /works/:slug
        ↓
POST /works/:id/like
        ↓
POST /works/:id/bookmark
        ↓
POST /works/:id/comments
        ↓
POST /works/:id/repost
```

Another user:

```text id="9db7r0"
GET /works/:slug
        ↓
POST /users/:id/follow
        ↓
POST /works/:id/like
        ↓
POST /works/:id/bookmark
        ↓
POST /works/:id/comments
```

This forms the core VerseVault interaction loop.

---

# 64. API Implementation Order

Backend implementation should follow dependency order:

```text id="n6f5y4"
1. Server setup
        ↓
2. MongoDB connection
        ↓
3. Error handling
        ↓
4. Validation
        ↓
5. Authentication
        ↓
6. Users
        ↓
7. Folders
        ↓
8. Works
        ↓
9. Comments
        ↓
10. Likes
        ↓
11. Bookmarks
        ↓
12. Reposts
        ↓
13. Follows
        ↓
14. Notifications
        ↓
15. Reports
        ↓
16. Search
```

---

# 65. Definition of API Completion

The API layer is considered ready for frontend integration when:

* Authentication works
* Protected routes enforce authentication
* Ownership checks work
* CRUD operations work
* Work publishing works
* Folder organization works
* Comments/replies work
* Likes work
* Bookmarks work
* Reposts work
* Following works
* Notifications work
* Reports work
* Validation is implemented
* Errors use standard responses
* Pagination works
* API tests cover critical paths

---

# 66. Next Document

The next planning document is:

```text
docs/SECURITY.md
```

It will define:

* Authentication security
* Password handling
* Cookies
* Authorization
* Input validation
* XSS prevention
* CSRF considerations
* Rate limiting
* File uploads
* Environment secrets
* MongoDB security
* User privacy
* Moderation
* Account deletion
* Security testing
