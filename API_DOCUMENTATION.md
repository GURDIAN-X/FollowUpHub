# FollowUpHub API Documentation

**Document Type:** RESTful API Documentation  
**Project Code:** FUH-2025  
**API Version:** v1.0.0  
**Base URL:** `https://api.followuphub.com/api/v1`  
**Status:** Active  
**Timestamp:** 2025-10-18 22:15:41 UTC

---

## Copyright Notice

© 2025 GURDIAN-X - Costantine George Mpanda. All Rights Reserved.

---

## Table of Contents

1. [API Overview](#api-overview)
2. [Authentication](#authentication)
3. [Error Handling](#error-handling)
4. [Rate Limiting](#rate-limiting)
5. [Pagination](#pagination)
6. [Endpoints](#endpoints)
   - [Authentication](#authentication-endpoints)
   - [Users](#users-endpoints)
   - [Organizations](#organizations-endpoints)
   - [Resolutions](#resolutions-endpoints)
   - [Assignments](#assignments-endpoints)
   - [Comments](#comments-endpoints)
   - [Audit Logs](#audit-logs-endpoints)
   - [Alerts](#alerts-endpoints)
   - [Webhooks](#webhooks-endpoints)
7. [Response Format](#response-format)
8. [Status Codes](#status-codes)

---

## API Overview

### General Information

| **Attribute** | **Value** |
|--------------|-----------|
| **Protocol** | HTTPS only (TLS 1.3) |
| **Format** | JSON |
| **Character Set** | UTF-8 |
| **Versioning** | URL path (`/api/v1/`) |
| **Authentication** | JWT Bearer Token |
| **Rate Limiting** | 100 requests/15 min |

### Base URL

**Production:** `https://api.followuphub.com/api/v1`  
**Staging:** `https://staging-api.followuphub.com/api/v1`  
**Development:** `http://localhost:3000/api/v1`

### Request Headers

**Required Headers:**
```http
Content-Type: application/json
Accept: application/json
Authorization: Bearer <access_token>
```

**Optional Headers:**
```http
Accept-Language: en|sw
X-Client-Version: 1.0.0
```

---

## Authentication

### Overview

FollowUpHub uses **JWT (JSON Web Tokens)** for authentication with access and refresh token mechanism.

**Token Types:**
- **Access Token:** Short-lived (30 minutes), used for API requests
- **Refresh Token:** Long-lived (7 days), used to obtain new access tokens

**Storage:**
- Access tokens: httpOnly cookie or Authorization header
- Refresh tokens: httpOnly cookie only (secure)

### Authentication Flow

```
1. Login → Receive access + refresh tokens
2. Use access token for API requests
3. When access token expires → Use refresh token to get new access token
4. When refresh token expires → Login again
```

---

## Error Handling

### Error Response Format

```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message",
    "details": []
  },
  "meta": {
    "timestamp": "2025-10-18T22:15:41.000Z"
  }
}
```

### Common Error Codes

| **Code** | **HTTP Status** | **Description** |
|---------|----------------|-----------------|
| `VALIDATION_ERROR` | 400 | Request validation failed |
| `UNAUTHORIZED` | 401 | Authentication required |
| `FORBIDDEN` | 403 | Insufficient permissions |
| `NOT_FOUND` | 404 | Resource not found |
| `CONFLICT` | 409 | Resource already exists |
| `UNPROCESSABLE` | 422 | Business logic error |
| `RATE_LIMITED` | 429 | Too many requests |
| `SERVER_ERROR` | 500 | Internal server error |

---

## Rate Limiting

### Limits

| **Endpoint Type** | **Limit** | **Window** |
|------------------|-----------|-----------|
| Global | 100 requests | 15 minutes |
| Authentication | 5 requests | 15 minutes |
| Per User | 100 requests | 15 minutes |

### Rate Limit Headers

```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1697654141
```

### Rate Limit Response

```json
{
  "success": false,
  "error": {
    "code": "RATE_LIMITED",
    "message": "Too many requests. Please try again later.",
    "retryAfter": 900
  }
}
```

---

## Pagination

### Query Parameters

```http
GET /api/v1/resolutions?page=1&perPage=20&sortBy=createdAt&sortOrder=desc
```

| **Parameter** | **Type** | **Default** | **Description** |
|--------------|----------|------------|-----------------|
| `page` | integer | 1 | Page number (min: 1) |
| `perPage` | integer | 20 | Items per page (min: 1, max: 100) |
| `sortBy` | string | createdAt | Sort field |
| `sortOrder` | string | desc | Sort order (asc/desc) |

### Pagination Response

```json
{
  "success": true,
  "data": [...],
  "pagination": {
    "page": 1,
    "perPage": 20,
    "total": 150,
    "totalPages": 8,
    "hasNext": true,
    "hasPrev": false
  }
}
```

---

## Endpoints

## Authentication Endpoints

### POST /auth/register

**Description:** Register a new user account.

**Authentication:** None required

**Request Body:**
```json
{
  "name": "John Doe",
  "email": "john@example.com",
  "password": "SecureP@ssw0rd",
  "phone": "+255714755686",
  "organizationId": "uuid",
  "language": "EN"
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "id": "user-uuid",
    "name": "John Doe",
    "email": "john@example.com",
    "role": "USER",
    "organizationId": "org-uuid",
    "language": "EN",
    "emailVerified": false
  },
  "meta": {
    "timestamp": "2025-10-18T22:15:41.000Z"
  }
}
```

---

### POST /auth/login

**Description:** Authenticate user and receive tokens.

**Authentication:** None required

**Rate Limit:** 5 requests per 15 minutes

**Request Body:**
```json
{
  "email": "john@example.com",
  "password": "SecureP@ssw0rd"
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "user": {
      "id": "user-uuid",
      "name": "John Doe",
      "email": "john@example.com",
      "role": "USER",
      "organizationId": "org-uuid",
      "language": "EN"
    },
    "accessToken": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": 1800
  },
  "meta": {
    "timestamp": "2025-10-18T22:15:41.000Z"
  }
}
```

**Cookies Set:**
```http
Set-Cookie: accessToken=...; HttpOnly; Secure; SameSite=Strict; Max-Age=1800
Set-Cookie: refreshToken=...; HttpOnly; Secure; SameSite=Strict; Max-Age=604800
```

---

### POST /auth/refresh

**Description:** Refresh access token using refresh token.

**Authentication:** Refresh token (cookie)

**Request Body:** Empty

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "accessToken": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
    "expiresIn": 1800
  }
}
```

---

### POST /auth/logout

**Description:** Logout user and invalidate tokens.

**Authentication:** Required

**Request Body:** Empty

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "message": "Logged out successfully"
  }
}
```

---

### POST /auth/forgot-password

**Description:** Request password reset email.

**Authentication:** None required

**Request Body:**
```json
{
  "email": "john@example.com"
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "message": "Password reset email sent"
  }
}
```

---

### POST /auth/reset-password

**Description:** Reset password with token from email.

**Authentication:** None required

**Request Body:**
```json
{
  "token": "reset-token-from-email",
  "password": "NewSecureP@ssw0rd"
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "message": "Password reset successfully"
  }
}
```

---

## Users Endpoints

### GET /users

**Description:** Get all users (with pagination).

**Authentication:** Required (Admin+)

**Query Parameters:**
- `page` (default: 1)
- `perPage` (default: 20, max: 100)
- `role` (filter by role)
- `isActive` (filter by active status)
- `search` (search by name or email)

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "id": "user-uuid",
      "name": "John Doe",
      "email": "john@example.com",
      "role": "USER",
      "organizationId": "org-uuid",
      "isActive": true,
      "createdAt": "2025-10-18T22:15:41.000Z"
    }
  ],
  "pagination": {
    "page": 1,
    "perPage": 20,
    "total": 50,
    "totalPages": 3
  }
}
```

---

### GET /users/:id

**Description:** Get user by ID.

**Authentication:** Required

**URL Parameters:**
- `id` (UUID) - User ID

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "id": "user-uuid",
    "name": "John Doe",
    "email": "john@example.com",
    "phone": "+255714755686",
    "role": "USER",
    "organizationId": "org-uuid",
    "language": "EN",
    "theme": "LIGHT",
    "isActive": true,
    "lastLoginAt": "2025-10-18T22:15:41.000Z",
    "createdAt": "2025-10-18T22:15:41.000Z",
    "updatedAt": "2025-10-18T22:15:41.000Z"
  }
}
```

---

### PUT /users/:id

**Description:** Update user information.

**Authentication:** Required (Self or Admin+)

**URL Parameters:**
- `id` (UUID) - User ID

**Request Body:**
```json
{
  "name": "John Updated",
  "phone": "+255714755687",
  "language": "SW",
  "theme": "DARK"
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "id": "user-uuid",
    "name": "John Updated",
    "email": "john@example.com",
    "phone": "+255714755687",
    "language": "SW",
    "theme": "DARK",
    "updatedAt": "2025-10-18T22:15:41.000Z"
  }
}
```

---

### DELETE /users/:id

**Description:** Soft delete user.

**Authentication:** Required (Admin+)

**URL Parameters:**
- `id` (UUID) - User ID

**Response:** `204 No Content`

---

## Resolutions Endpoints

### GET /resolutions

**Description:** Get all resolutions (with pagination and filters).

**Authentication:** Required

**Query Parameters:**
- `page`, `perPage` (pagination)
- `status` (PENDING, IN_PROGRESS, COMPLETED, etc.)
- `type` (ILIELEKEZA, ILISHAURIWA)
- `priority` (LOW, MEDIUM, HIGH, URGENT)
- `createdBy` (UUID)
- `search` (search description)

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "id": "resolution-uuid",
      "description": "Implement new security features",
      "resolutionType": {
        "code": "ILIELEKEZA",
        "nameSw": "Ilielekeza",
        "nameEn": "Directive"
      },
      "status": "IN_PROGRESS",
      "priority": "HIGH",
      "deadline": "2025-11-01T00:00:00.000Z",
      "createdBy": {
        "id": "user-uuid",
        "name": "John Doe"
      },
      "assignmentCount": 3,
      "commentCount": 5,
      "createdAt": "2025-10-18T22:15:41.000Z"
    }
  ],
  "pagination": {...}
}
```

---

### GET /resolutions/:id

**Description:** Get resolution by ID with full details.

**Authentication:** Required

**URL Parameters:**
- `id` (UUID) - Resolution ID

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "id": "resolution-uuid",
    "description": "Implement new security features",
    "resolutionType": {
      "code": "ILIELEKEZA",
      "nameSw": "Ilielekeza",
      "nameEn": "Directive",
      "isMandatory": true
    },
    "status": "IN_PROGRESS",
    "priority": "HIGH",
    "deadline": "2025-11-01T00:00:00.000Z",
    "completedAt": null,
    "createdBy": {
      "id": "user-uuid",
      "name": "John Doe",
      "email": "john@example.com"
    },
    "meeting": {
      "id": "meeting-uuid",
      "title": "Q4 2025 Strategic Planning",
      "meetingDate": "2025-10-18T14:00:00.000Z"
    },
    "notes": "Additional context and notes",
    "attachments": [
      {
        "url": "https://res.cloudinary.com/...",
        "filename": "requirements.pdf",
        "size": 245678
      }
    ],
    "assignments": [
      {
        "id": "assignment-uuid",
        "assignee": {
          "id": "user-uuid",
          "name": "Jane Smith"
        },
        "status": "IN_PROGRESS",
        "deadline": "2025-10-25T00:00:00.000Z",
        "progress": 60
      }
    ],
    "comments": [
      {
        "id": "comment-uuid",
        "content": "Working on implementation",
        "user": {
          "id": "user-uuid",
          "name": "Jane Smith"
        },
        "createdAt": "2025-10-19T10:30:00.000Z"
      }
    ],
    "createdAt": "2025-10-18T22:15:41.000Z",
    "updatedAt": "2025-10-19T15:20:00.000Z"
  }
}
```

---

### POST /resolutions

**Description:** Create new resolution.

**Authentication:** Required (Manager+)

**Request Body:**
```json
{
  "description": "Implement new security features",
  "resolutionTypeId": "type-uuid",
  "meetingId": "meeting-uuid",
  "priority": "HIGH",
  "deadline": "2025-11-01T00:00:00.000Z",
  "notes": "Additional context",
  "attachments": [
    {
      "url": "https://res.cloudinary.com/...",
      "filename": "requirements.pdf"
    }
  ]
}
```

**Response:** `201 Created`
```json
{
  "success": true,
  "data": {
    "id": "resolution-uuid",
    "description": "Implement new security features",
    "status": "PENDING",
    "priority": "HIGH",
    "createdAt": "2025-10-18T22:15:41.000Z"
  }
}
```

---

### PUT /resolutions/:id

**Description:** Update resolution.

**Authentication:** Required (Manager+ or Creator)

**URL Parameters:**
- `id` (UUID) - Resolution ID

**Request Body:**
```json
{
  "description": "Updated description",
  "status": "IN_PROGRESS",
  "priority": "URGENT",
  "deadline": "2025-10-25T00:00:00.000Z"
}
```

**Response:** `200 OK`

---

### DELETE /resolutions/:id

**Description:** Soft delete resolution.

**Authentication:** Required (Admin+ or Creator)

**Response:** `204 No Content`

---

## Assignments Endpoints

### GET /assignments

**Description:** Get all assignments (with filters).

**Authentication:** Required

**Query Parameters:**
- `page`, `perPage` (pagination)
- `resolutionId` (filter by resolution)
- `assigneeId` (filter by assignee)
- `status` (filter by status)
- `overdue` (boolean, show only overdue)

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "id": "assignment-uuid",
      "resolution": {
        "id": "resolution-uuid",
        "description": "Implement security features"
      },
      "assignee": {
        "id": "user-uuid",
        "name": "Jane Smith"
      },
      "status": "IN_PROGRESS",
      "deadline": "2025-10-25T00:00:00.000Z",
      "progress": 60,
      "createdAt": "2025-10-18T22:15:41.000Z"
    }
  ],
  "pagination": {...}
}
```

---

### POST /assignments

**Description:** Create new assignment.

**Authentication:** Required (Manager+)

**Request Body:**
```json
{
  "resolutionId": "resolution-uuid",
  "assigneeId": "user-uuid",
  "deadline": "2025-10-25T00:00:00.000Z",
  "notes": "Please complete by deadline"
}
```

**Response:** `201 Created`

---

### PUT /assignments/:id

**Description:** Update assignment.

**Authentication:** Required (Assignee or Manager+)

**Request Body:**
```json
{
  "status": "COMPLETED",
  "progress": 100,
  "completedAt": "2025-10-24T16:30:00.000Z",
  "notes": "Completed successfully"
}
```

**Response:** `200 OK`

---

## Comments Endpoints

### GET /resolutions/:resolutionId/comments

**Description:** Get all comments for a resolution.

**Authentication:** Required

**URL Parameters:**
- `resolutionId` (UUID) - Resolution ID

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "id": "comment-uuid",
      "content": "Working on implementation",
      "user": {
        "id": "user-uuid",
        "name": "Jane Smith"
      },
      "attachments": [],
      "createdAt": "2025-10-19T10:30:00.000Z",
      "updatedAt": "2025-10-19T10:30:00.000Z"
    }
  ]
}
```

---

### POST /resolutions/:resolutionId/comments

**Description:** Add comment to resolution.

**Authentication:** Required

**Request Body:**
```json
{
  "content": "I have a question about this resolution",
  "attachments": [
    {
      "url": "https://res.cloudinary.com/...",
      "filename": "screenshot.png"
    }
  ]
}
```

**Response:** `201 Created`

---

### PUT /comments/:id

**Description:** Update comment.

**Authentication:** Required (Comment author)

**Request Body:**
```json
{
  "content": "Updated comment content"
}
```

**Response:** `200 OK`

---

### DELETE /comments/:id

**Description:** Soft delete comment.

**Authentication:** Required (Comment author or Admin+)

**Response:** `204 No Content`

---

## Audit Logs Endpoints

### GET /audit-logs

**Description:** Get audit logs (read-only).

**Authentication:** Required (Admin+ or Compliance Officer)

**Query Parameters:**
- `page`, `perPage` (pagination)
- `userId` (filter by user)
- `action` (filter by action type)
- `resourceType` (filter by resource)
- `startDate`, `endDate` (date range)

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "id": "log-uuid",
      "user": {
        "id": "user-uuid",
        "name": "John Doe"
      },
      "action": "CREATE_RESOLUTION",
      "resourceType": "Resolution",
      "resourceId": "resolution-uuid",
      "ipAddress": "192.168.1.1",
      "timestamp": "2025-10-18T22:15:41.000Z"
    }
  ],
  "pagination": {...}
}
```

---

## Alerts Endpoints

### GET /alerts

**Description:** Get user alerts.

**Authentication:** Required

**Query Parameters:**
- `isRead` (boolean, filter read/unread)
- `type` (filter by alert type)
- `severity` (filter by severity)

**Response:** `200 OK`
```json
{
  "success": true,
  "data": [
    {
      "id": "alert-uuid",
      "type": "DEADLINE_APPROACHING",
      "severity": "WARNING",
      "message": "Task deadline in 2 days",
      "messageSw": "Tarehe ya kumalizika ni siku 2 kuelekea",
      "messageEn": "Task deadline in 2 days",
      "isRead": false,
      "createdAt": "2025-10-18T22:15:41.000Z"
    }
  ]
}
```

---

### PUT /alerts/:id/read

**Description:** Mark alert as read.

**Authentication:** Required

**Response:** `200 OK`

---

## Webhooks Endpoints

### POST /webhooks/minutehub

**Description:** Receive webhook from MinuteHub.

**Authentication:** HMAC signature verification

**Headers:**
```http
X-MinuteHub-Signature: sha256=...
Content-Type: application/json
```

**Request Body:**
```json
{
  "event": "meeting.concluded",
  "meetingId": "minutehub-meeting-id",
  "meetingTitle": "Q4 2025 Strategic Planning",
  "meetingDate": "2025-10-18T14:00:00.000Z",
  "resolutions": [
    {
      "description": "Implement new security features",
      "type": "ILIELEKEZA",
      "assignedTo": "john@example.com",
      "deadline": "2025-11-01T00:00:00.000Z",
      "priority": "HIGH"
    }
  ]
}
```

**Response:** `200 OK`
```json
{
  "success": true,
  "data": {
    "received": true,
    "processedResolutions": 1
  }
}
```

---

## Response Format

### Success Response

```json
{
  "success": true,
  "data": {
    // Response data
  },
  "meta": {
    "timestamp": "2025-10-18T22:15:41.000Z"
  }
}
```

### Error Response

```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Error description",
    "details": []
  },
  "meta": {
    "timestamp": "2025-10-18T22:15:41.000Z"
  }
}
```

---

## Status Codes

| **Code** | **Meaning** | **Description** |
|---------|------------|-----------------|
| 200 | OK | Request successful |
| 201 | Created | Resource created successfully |
| 204 | No Content | Request successful, no content returned |
| 400 | Bad Request | Invalid request parameters |
| 401 | Unauthorized | Authentication required |
| 403 | Forbidden | Insufficient permissions |
| 404 | Not Found | Resource not found |
| 409 | Conflict | Resource already exists |
| 422 | Unprocessable Entity | Business logic error |
| 429 | Too Many Requests | Rate limit exceeded |
| 500 | Internal Server Error | Server error |
| 503 | Service Unavailable | Service temporarily unavailable |

---

## Contact Information

**Project Owner:** Costantine George Mpanda (GURDIAN-X)  
**Institution:** University of Dodoma (UDOM)  
**Location:** Dodoma, Tanzania 🇹🇿

**Emails:**
- kapipocostantine@gmail.com
- costantine.mpanda@udom.ac.tz
- contact@gurdianx.com

**Phones:**
- +255714755686
- +255752999417

**Repository:** https://github.com/GURDIAN-X/FollowUpHub

---

## Related Documentation

📄 [PROJECT_CHARTER.md](./PROJECT_CHARTER.md) - Strategic framework  
📄 [TECHNICAL_SPECS.md](./TECHNICAL_SPECS.md) - Technical architecture  
📄 [DATABASE_SCHEMA.md](./DATABASE_SCHEMA.md) - Database design  
📄 [SECURITY_POLICY.md](./SECURITY_POLICY.md) - Security policies  
📄 [README.md](./README.md) - Project overview  

---

**Document Status:** Active  
**Last Updated:** 2025-10-18 22:15:41 UTC  
**Version:** 1.0.0  

**#ComplianceKwanza** 🛡️ | **Made in Tanzania** 🇹🇿

© 2025 GURDIAN-X - Costantine George Mpanda. All Rights Reserved.
