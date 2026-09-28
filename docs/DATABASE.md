# VerseVault — Database Design

**Version:** 1.0
**Status:** Planning
**Database:** MongoDB
**ODM:** Mongoose
**Primary Goal:** Define VerseVault's data model, relationships, indexes, constraints, and query patterns before implementation.

---

# 1. Database Philosophy

VerseVault uses MongoDB as the primary source of truth for application data.

The database should prioritize:

* Clear ownership
* Data integrity
* Efficient common queries
* Simple relationships
* Scalable collections
* Minimal duplication
* Easy querying
* Safe deletion
* Future extensibility

The database should not attempt to model every possible future feature.

Start with the data required by the current product and expand when actual requirements appear.

---

# 2. Core Collections

The initial database will contain:

```text
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
```

Future collections may include:

```text
sessions
analytics
tags
categories
auditLogs
```

These are intentionally excluded from the initial implementation unless required.

---

# 3. Relationship Overview

```text
                         ┌─────────────┐
                         │    USER     │
                         └──────┬──────┘
                                │
             ┌──────────────────┼──────────────────┐
             │                  │                  │
             ▼                  ▼                  ▼
          FOLDERS             WORKS             FOLLOWS
             │                  │
             │                  ├──── COMMENTS
             │                  ├──── LIKES
             │                  ├──── BOOKMARKS
             │                  ├──── REPOSTS
             │                  └──── REPORTS
             │
             └──────── contains ──────── WORKS

USER ─────────────── NOTIFICATIONS
```

Most relationships will use MongoDB `ObjectId` references.

---

# 4. Users

Collection:

```text
users
```

A user represents an account and public creator identity.

## Schema

```text
User
├── _id
├── username
├── name
├── email
├── phone
├── passwordHash
├── bio
├── avatarUrl
├── website
├── role
├── status
├── createdAt
└── updatedAt
```

### Fields

| Field          | Type     | Required | Notes                              |
| -------------- | -------- | -------: | ---------------------------------- |
| `_id`          | ObjectId |      Yes | MongoDB identifier                 |
| `username`     | String   |      Yes | Unique public identifier           |
| `name`         | String   |      Yes | Display name                       |
| `email`        | String   |      Yes | Unique account email               |
| `phone`        | String   |       No | Optional                           |
| `passwordHash` | String   |      Yes | Never store raw password           |
| `bio`          | String   |       No | Public profile description         |
| `avatarUrl`    | String   |       No | Profile image reference            |
| `website`      | String   |       No | Optional external link             |
| `role`         | String   |      Yes | `user` / `admin`                   |
| `status`       | String   |      Yes | `active` / `suspended` / `deleted` |
| `createdAt`    | Date     |      Yes | Account creation                   |
| `updatedAt`    | Date     |      Yes | Last update                        |

---

# 5. User Identity Rules

### Username

Username must be:

* Unique
* Case-normalized
* URL-safe
* Immutable or carefully changeable

Example:

```text
/aditya
```

A unique MongoDB index should be created.

### Email

Email should also have a unique index.

Emails should be normalized before storage.

Example:

```text
ADITYA@EMAIL.COM
```

becomes:

```text
aditya@email.com
```

---

# 6. Password Security

The database must never contain:

```text
password: "mypassword123"
```

Instead:

```text
passwordHash: "$argon2id$..."
```

Password hashing occurs in the backend before persistence.

The frontend must never receive `passwordHash`.

---

# 7. Folders

Collection:

```text
folders
```

Folders are organizational containers for a creator's private workspace.

A folder does NOT define the type of writing.

Examples:

```text
Story Draft 1
song32
Story Draft 4
College Ideas
Random Thoughts
```

## Schema

```text
Folder
├── _id
├── ownerId
├── name
├── description
├── coverImageUrl
├── visibility
├── createdAt
└── updatedAt
```

### Fields

| Field           | Type     | Required | Notes                |
| --------------- | -------- | -------: | -------------------- |
| `_id`           | ObjectId |      Yes | Folder identifier    |
| `ownerId`       | ObjectId |      Yes | References User      |
| `name`          | String   |      Yes | Folder name          |
| `description`   | String   |       No | Optional description |
| `coverImageUrl` | String   |       No | Folder artwork       |
| `visibility`    | String   |      Yes | Initially `private`  |
| `createdAt`     | Date     |      Yes | Creation time        |
| `updatedAt`     | Date     |      Yes | Last update          |

---

# 8. Folder Artwork

Folder artwork is used for visual identification and presentation.

MongoDB stores:

```text
coverImageUrl
```

The actual image file is stored in external object/file storage.

MongoDB should not store large image binaries for this feature.

---

# 9. Works

Collection:

```text
works
```

A Work is the fundamental writing/content unit of VerseVault.

A work can represent anything the creator wants:

```text
Chapter 1
Character Notes
Backstory
Poem
Song Verse
Essay
Article
Journal Entry
Full Story
Random Idea
```

VerseVault does not force a content type.

---

## Schema

```text
Work
├── _id
├── authorId
├── folderId
├── title
├── slug
├── content
├── excerpt
├── coverImageUrl
├── status
├── visibility
├── publishedAt
├── createdAt
└── updatedAt
```

### Fields

| Field           | Type     | Required | Notes                              |
| --------------- | -------- | -------: | ---------------------------------- |
| `_id`           | ObjectId |      Yes | Work identifier                    |
| `authorId`      | ObjectId |      Yes | References User                    |
| `folderId`      | ObjectId |       No | Optional Folder                    |
| `title`         | String   |      Yes | Work title                         |
| `slug`          | String   |      Yes | URL-friendly identifier            |
| `content`       | String   |      Yes | Main content                       |
| `excerpt`       | String   |       No | Public preview                     |
| `coverImageUrl` | String   |       No | Optional cover                     |
| `status`        | String   |      Yes | `draft` / `published` / `archived` |
| `visibility`    | String   |      Yes | `private` / `public`               |
| `publishedAt`   | Date     |       No | Set when published                 |
| `createdAt`     | Date     |      Yes | Creation                           |
| `updatedAt`     | Date     |      Yes | Last update                        |

---

# 10. Work Lifecycle

A work follows:

```text
                    ┌────────────┐
                    │   DRAFT    │
                    └─────┬──────┘
                          │
                       Publish
                          │
                          ▼
                    ┌────────────┐
                    │ PUBLISHED  │
                    └─────┬──────┘
                          │
                       Archive
                          │
                          ▼
                    ┌────────────┐
                    │  ARCHIVED  │
                    └────────────┘
```

A work can also remain permanently as a draft.

---

# 11. Folder ↔ Work Relationship

A work may belong to:

```text
zero or one folder
```

Therefore:

```text
folderId = null
```

is valid.

Example:

```text
Work A → Story Draft 1
Work B → Story Draft 1
Work C → no folder
```

The database should not store a large `works[]` array inside the Folder document.

Instead:

```text
works.folderId → folders._id
```

This allows efficient querying:

```text
Find all works where folderId = X
```

---

# 12. Why Works Reference Folders

This avoids constantly modifying the Folder document whenever a work is:

* Added
* Removed
* Moved
* Archived
* Deleted

It also prevents the folder document from growing unnecessarily large.

---

# 13. Comments

Collection:

```text
comments
```

Comments belong to works.

They support replies.

## Schema

```text
Comment
├── _id
├── workId
├── authorId
├── parentCommentId
├── content
├── status
├── createdAt
└── updatedAt
```

### Fields

| Field             | Type     | Required | Notes                           |
| ----------------- | -------- | -------: | ------------------------------- |
| `_id`             | ObjectId |      Yes | Comment identifier              |
| `workId`          | ObjectId |      Yes | Target work                     |
| `authorId`        | ObjectId |      Yes | Comment author                  |
| `parentCommentId` | ObjectId |       No | Parent comment for replies      |
| `content`         | String   |      Yes | Comment text                    |
| `status`          | String   |      Yes | `active` / `deleted` / `hidden` |
| `createdAt`       | Date     |      Yes | Creation                        |
| `updatedAt`       | Date     |      Yes | Last update                     |

---

# 14. Nested Comment Structure

A top-level comment has:

```text
parentCommentId = null
```

A reply has:

```text
parentCommentId = commentId
```

Example:

```text
Comment A
│
├── Reply B
│   └── Reply C
│
└── Reply D
```

The UI may limit nesting depth even if the database supports deeper relationships.

---

# 15. Likes

Collection:

```text
likes
```

A Like represents:

```text
User X liked Work Y
```

## Schema

```text
Like
├── _id
├── userId
├── workId
└── createdAt
```

A compound unique index must prevent duplicate likes:

```text
(userId, workId)
```

Therefore:

```text
User A → Work X → Like
```

can exist only once.

---

# 16. Bookmarks

Collection:

```text
bookmarks
```

A Bookmark represents a user's saved work.

## Schema

```text
Bookmark
├── _id
├── userId
├── workId
└── createdAt
```

Unique compound index:

```text
(userId, workId)
```

A user can save a work only once.

---

# 17. Reposts

Collection:

```text
reposts
```

VerseVault supports **quote reposts**.

A repost does not duplicate the original work.

## Schema

```text
Repost
├── _id
├── userId
├── workId
├── quote
└── createdAt
```

Example:

```text
User A
   │
   └── Reposts Work X
          │
          └── "This is exactly what I wanted to say."
```

The original work remains the source of truth.

A compound unique index should prevent the same user from reposting the same work multiple times.

---

# 18. Following

Collection:

```text
follows
```

A Follow represents:

```text
User A follows User B
```

## Schema

```text
Follow
├── _id
├── followerId
├── followingId
└── createdAt
```

A unique compound index should exist on:

```text
(followerId, followingId)
```

A user cannot follow the same person twice.

A user should also not be allowed to follow themselves.

This should be enforced in the backend.

---

# 19. Notifications

Collection:

```text
notifications
```

Notifications inform users about relevant activity.

Examples:

```text
Someone liked your work
Someone commented on your work
Someone replied to your comment
Someone followed you
Someone reposted your work
```

## Schema

```text
Notification
├── _id
├── recipientId
├── actorId
├── type
├── workId
├── commentId
├── read
└── createdAt
```

Possible types:

```text
like
comment
reply
follow
repost
```

Optional references remain `null` when irrelevant.

---

# 20. Reports

Collection:

```text
reports
```

Reports allow users to flag inappropriate content.

## Schema

```text
Report
├── _id
├── reporterId
├── workId
├── commentId
├── reason
├── description
├── status
├── reviewedBy
├── reviewedAt
└── createdAt
```

A report may target either:

```text
Work
```

or:

```text
Comment
```

but not both simultaneously.

---

# 21. Report Status

Initial states:

```text
pending
reviewed
resolved
dismissed
```

Example:

```text
User reports Work
        ↓
     pending
        ↓
      Admin
        ↓
 ┌──────┴──────┐
 ▼             ▼
resolved     dismissed
```

---

# 22. Soft Deletion

Certain user-generated content should not immediately disappear from the database.

For example:

```text
Comment
status = deleted
```

rather than immediately removing the document.

This allows:

* Moderation
* Auditability
* Referential integrity
* Safer recovery

Permanent deletion policies will be defined separately in the security/privacy documentation.

---

# 23. Index Strategy

Indexes should be created around actual query patterns.

### Users

```text
username — unique
email — unique
```

### Works

```text
authorId
folderId
slug
status
visibility
publishedAt
createdAt
```

### Comments

```text
workId
parentCommentId
createdAt
```

### Likes

```text
(userId, workId) — unique
workId
```

### Bookmarks

```text
(userId, workId) — unique
userId
createdAt
```

### Reposts

```text
(userId, workId) — unique
workId
createdAt
```

### Follows

```text
(followerId, followingId) — unique
followingId
followerId
```

### Notifications

```text
recipientId
recipientId + read
recipientId + createdAt
```

### Reports

```text
status
workId
commentId
createdAt
```

Indexes should be measured and refined as query patterns become clearer.

---

# 24. Pagination

VerseVault should avoid loading huge collections at once.

Examples:

```text
Feed
Comments
Bookmarks
Followers
Following
Notifications
Search results
```

should use pagination.

The initial implementation may use:

```text
limit + cursor
```

rather than returning unlimited records.

Cursor-based pagination should eventually be preferred for continuously growing feeds.

---

# 25. Common Query Patterns

The database design should support queries such as:

### Get public work

```text
Find work by slug
```

### Get author's works

```text
Find published works where authorId = X
```

### Get folder contents

```text
Find works where folderId = X
```

### Get user's drafts

```text
Find works where authorId = X
AND status = draft
```

### Get bookmarks

```text
Find bookmarks where userId = X
Then retrieve referenced works
```

### Get comments

```text
Find comments where workId = X
Sort by createdAt
```

### Get replies

```text
Find comments where parentCommentId = X
```

### Check whether user liked work

```text
Find like where userId = X
AND workId = Y
```

### Check whether user follows creator

```text
Find follow where followerId = X
AND followingId = Y
```

---

# 26. MongoDB References vs Embedding

VerseVault will primarily use references for relationships that can grow independently.

For example:

```text
User
   ↓
Work
```

using:

```text
authorId
```

rather than embedding the entire user.

Similarly:

```text
Work
   ↓
Comments
```

will use a separate collection.

Embedding will be considered only for small, tightly coupled data where it provides a clear benefit.

---

# 27. Data Integrity Rules

The backend must enforce:

### User

* Username unique
* Email unique
* Valid role
* Valid account status

### Work

* Author must exist
* Folder must belong to the same author
* Published work must satisfy publishing requirements
* Only owner can edit/delete/publish

### Like

* User must exist
* Work must exist
* One like per user/work

### Bookmark

* One bookmark per user/work

### Repost

* One repost per user/work
* Original work must exist

### Follow

* One follow per user/following pair
* User cannot follow themselves

### Comment

* Work must exist
* Parent comment must belong to the same work

### Report

* Reporter must exist
* Target must exist
* Target must be valid

---

# 28. Folder Ownership Rule

A particularly important rule:

A user cannot attach a work to another user's folder.

For example:

```text
User A
 ├── Work A
 └── Folder A

User B
 └── Folder B
```

User A must not be able to set:

```text
Work A → Folder B
```

The API must verify folder ownership before changing `folderId`.

---

# 29. Public vs Private Data

### Public

Potentially visible:

```text
username
name
bio
avatar
published works
public interactions
```

### Private

Must remain protected:

```text
passwordHash
email
phone
draft works
private folders
private works
internal moderation information
```

The API should explicitly control which fields are returned.

---

# 30. Work Visibility Rules

A work may have:

```text
status:
draft
published
archived
```

and:

```text
visibility:
private
public
```

Examples:

```text
draft + private
```

Normal private writing.

```text
published + public
```

Normal public VerseVault work.

The backend must prevent private works from appearing in public feeds/search results.

---

# 31. Folder Visibility

Initially:

```text
private
```

Folders are primarily personal organization tools.

A future version may introduce:

```text
private
public
```

for creators who want to present a collection of related works.

---

# 32. Analytics

Basic counters such as:

```text
likes
comments
reposts
views
```

should not automatically be stored as the only source of truth.

For example:

```text
likes collection
```

is the source of truth for likes.

A cached counter may later be added to `works` for performance:

```text
likeCount
commentCount
repostCount
```

but these must be treated as derived values.

---

# 33. Search

The initial database should support basic search through appropriate indexes.

Searchable work information may include:

```text
title
excerpt
content
tags
author
```

More advanced search can later use MongoDB Atlas Search.

Search implementation should not complicate the MVP database prematurely.

---

# 34. Deletion Rules

Deleting a user requires careful handling.

Potential future strategy:

```text
User
 ↓
status = deleted
```

rather than immediately deleting everything.

Related content may require:

* Anonymization
* Ownership reassignment
* Soft deletion
* Permanent deletion
* Retention for moderation/legal requirements

These rules will be finalized in `SECURITY.md`.

---

# 35. Database Transactions

MongoDB transactions should only be used when multiple writes must succeed or fail together.

Examples may include:

```text
Complex account deletion
Moderation actions
Multi-document state changes
```

Simple operations such as:

```text
Like a work
Bookmark a work
Follow a user
```

should not automatically use transactions.

---

# 36. Example User Workspace

A real user could have:

```text
USER: Aditya
│
├── FOLDERS
│   │
│   ├── Story Draft 1
│   │   ├── Chapter 1
│   │   ├── Chapter 2
│   │   └── Chapter 3
│   │
│   ├── song32
│   │   └── Verse
│   │
│   └── Story Draft 4
│       ├── Characters
│       ├── Backstory
│       └── Opening Song
│
└── WORKS
    ├── Chapter 1
    ├── Random Idea
    └── Published Poem
```

A work does not have to belong to a folder.

---

# 37. Example Public Interaction

A public work might produce:

```text
WORK
 │
 ├── 125 LIKES
 ├── 12 COMMENTS
 ├── 7 REPOSTS
 │
 ├── COMMENTS
 │    ├── Comment
 │    ├── Reply
 │    └── Reply
 │
 └── REPOSTS
      ├── User A + quote
      └── User B + quote
```

These interactions remain separate documents.

---

# 38. Initial Collection Summary

| Collection      | Purpose                     |
| --------------- | --------------------------- |
| `users`         | Accounts and profiles       |
| `folders`       | Personal organization       |
| `works`         | Writing/content             |
| `comments`      | Comments and replies        |
| `likes`         | Work likes                  |
| `bookmarks`     | Saved works                 |
| `reposts`       | Quote reposts               |
| `follows`       | Creator relationships       |
| `notifications` | User activity notifications |
| `reports`       | Moderation reports          |

---

# 39. Future Collections

Only introduce these when the feature is actually required:

```text
sessions
tags
categories
analytics
auditLogs
```

They are intentionally not part of the first database implementation.

---

# 40. Database Design Principles

VerseVault's database should follow these rules:

### 1. MongoDB is the source of truth

### 2. Don't duplicate large objects unnecessarily

### 3. Use references for independently growing relationships

### 4. Use indexes based on real queries

### 5. Enforce uniqueness at the database level where necessary

### 6. Validate input at the API boundary

### 7. Enforce authorization at the backend

### 8. Never store plaintext passwords

### 9. Never trust frontend validation

### 10. Keep derived counters separate from source-of-truth data

### 11. Don't optimize before measuring

### 12. Keep the schema understandable

---

# 41. Database Implementation Order

When implementation begins:

```text
1. MongoDB connection
        ↓
2. User model
        ↓
3. Folder model
        ↓
4. Work model
        ↓
5. Authentication
        ↓
6. Comments
        ↓
7. Likes
        ↓
8. Bookmarks
        ↓
9. Reposts
        ↓
10. Follows
        ↓
11. Notifications
        ↓
12. Reports
        ↓
13. Index optimization
```

This order follows the dependency structure of the product.

---

# 42. Final Data Model

The initial VerseVault database is conceptually:

```text
                         ┌─────────────┐
                         │    USERS    │
                         └──────┬──────┘
                                │
              ┌─────────────────┼──────────────────┐
              │                 │                  │
              ▼                 ▼                  ▼
           FOLDERS            WORKS             FOLLOWS
              │                 │                  │
              │                 │                  │
              └───────┐    ┌────┼─────┐            │
                      │    │    │     │            │
                      ▼    ▼    ▼     ▼            ▼
                   COMMENTS LIKES BOOKMARKS      USERS
                      │
                      │
                   REPLIES

                         WORKS
                           │
                  ┌────────┼─────────┐
                  ▼        ▼         ▼
                LIKES   REPOSTS    REPORTS

                         USERS
                           │
                           ▼
                     NOTIFICATIONS
```

This model provides the foundation for the first implementation while leaving room for VerseVault to evolve.

---

# 43. Next Document

The next planning document is:

```text
docs/API.md
```

It will translate this database and product design into actual application operations:

```text
POST   /auth/register
POST   /auth/login
POST   /works
GET    /works/:slug
PATCH  /works/:id
POST   /works/:id/publish
POST   /works/:id/like
POST   /works/:id/bookmark
POST   /works/:id/repost
POST   /works/:id/comments
POST   /users/:id/follow
...
```

The API specification will be the contract between the VerseVault frontend and backend.
