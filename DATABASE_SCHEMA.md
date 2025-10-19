# DATABASE_SCHEMA.md

© 2025 GURDIAN-X - Costantine George Mpanda. All Rights Reserved.
Timestamp: 2025-10-18 22:15:41 UTC
Contact: kapipocostantine@gmail.com, costantine.mpanda@udom.ac.tz, contact@gurdianx.com
Phones: +255714755686 / +255752999417
Institution: UDOM
#ComplianceKwanza

## Database: PostgreSQL

### Table: users
- id (PK)
- name
- email (unique)
- phone
- password_hash
- role
- status
- created_at
- updated_at

### Table: meetings
- id (PK)
- title
- date
- location
- status
- created_by
- created_at
- updated_at

### Table: resolutions
- id (PK)
- meeting_id (FK)
- title
- description
- status
- assigned_to (FK)
- deadline
- created_by
- created_at
- updated_at

### Table: comments
- id (PK)
- resolution_id (FK)
- user_id (FK)
- content
- created_at

### Table: alerts
- id (PK)
- user_id (FK)
- type
- message
- read (bool)
- created_at

### Table: audit_logs
- id (PK)
- user_id (FK)
- action
- entity
- entity_id
- timestamp
- details

### Table: minutehub_imports
- id (PK)
- source_url
- imported_by (FK)
- status
- created_at

### Table: minutehub_exports
- id (PK)
- target_url
- exported_by (FK)
- status
- created_at

### Table: files
- id (PK)
- user_id (FK)
- filename
- url
- type
- uploaded_at

---

## Indexing & Performance
- All FKs indexed
- created_at indexed
- status indexed
- email unique index

## Security & Compliance
- Encrypted passwords
- Audit trail for all changes
- Soft delete via status
- GDPR, Tanzanian Data Protection Act, ISO 27001

## Related Docs
- [API_DOCUMENTATION.md](./API_DOCUMENTATION.md)
- [TECHNICAL_SPECS.md](./TECHNICAL_SPECS.md)

#ComplianceKwanza 🛡️ | Made in Tanzania 🇹🇿