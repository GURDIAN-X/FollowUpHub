# API_DOCUMENTATION.md

© 2025 GURDIAN-X - Costantine George Mpanda. All Rights Reserved.
Timestamp: 2025-10-19 15:08:17 UTC
Contact: kapipocostantine@gmail.com, costantine.mpanda@udom.ac.tz, contact@gurdianx.com
Phones: +255714755686 / +255752999417
Institution: University of Dodoma (UDOM)

## #ComplianceKwanza

## API Overview

All API endpoints are RESTful, use JSON, require HTTPS, and are protected with JWT+RBAC. All requests and responses must be logged for audit.

### Base URLs

| Environment | URL                      |
|-------------|--------------------------|
| Production  | https://api.followuphub.co.tz |
| Staging     | https://staging-api.followuphub.co.tz |
| Local Dev   | http://localhost:4000    |

---

## Authentication

### POST /auth/register
- Registers a new user.
- Request: `{ "email": "...", "password": "...", "name": "...", ... }`
- Response: `{ "user": {...}, "token": "...", ... }`

### POST /auth/login
- Logs in a user.
- Request: `{ "email": "...", "password": "..." }`
- Response: `{ "user": {...}, "token": "...", ... }`

### POST /auth/refresh
- Refreshes access token.

---

## Users

### GET /users/me
- Returns current user's profile.

### PUT /users/me
- Updates current user's profile.

### GET /users
- List users (admin only).

### GET /users/:id
- Gets user by id.

---

## Resolutions (ILIELEKEZA/ILISHAURIWA)

### GET /resolutions
- List all resolutions (with filters).

### POST /resolutions
- Create new resolution.

### PUT /resolutions/:id
- Update a resolution.

### DELETE /resolutions/:id
- Archive a resolution (soft delete).

---

## Meetings

### GET /meetings
- List all meetings.

### POST /meetings
- Create new meeting.

---

## Audit Logs

### GET /audit-logs
- List audit logs (filters: user, date, action).

---

## Alerts

### GET /alerts
- List alerts for current user.

### PATCH /alerts/:id/read
- Mark alert as read.

### PATCH /alerts/read-all
- Mark all alerts as read.

---

## Comments

### POST /resolutions/:id/comments
- Add comment to a resolution.

### GET /resolutions/:id/comments
- List comments for a resolution.

---

## MinuteHub Integration

### POST /minutehub/import
- Import minutes from MinuteHub.

### GET /minutehub/export
- Export resolutions to MinuteHub.

### POST /minutehub/webhook
- Webhook for real-time sync.

---

## Error Handling & Rate Limiting

- All responses use standard format:
  - `{ "status": "success", "data": {...} }`
  - `{ "status": "error", "message": "...", "code": 400, "details": {...} }`
- Rate limits: 100 requests / 15min / IP (customizable per role).
- Bilingual error messages (Swahili/English).

---

## Related Documentation

- [README.md](./README.md)
- [DATABASE_SCHEMA.md](./DATABASE_SCHEMA.md)
- [TECHNICAL_SPECS.md](./TECHNICAL_SPECS.md)
- [SECURITY_POLICY.md](./SECURITY_POLICY.md)
- [CONTRIBUTING.md](./CONTRIBUTING.md)

---

#ComplianceKwanza 🛡️ | Made in Tanzania 🇹🇿