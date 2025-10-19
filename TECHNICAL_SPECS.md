# TECHNICAL_SPECS.md

© 2025 GURDIAN-X - Costantine George Mpanda. All Rights Reserved.
Timestamp: 2025-10-18 22:15:41 UTC
Contact: kapipocostantine@gmail.com, costantine.mpanda@udom.ac.tz, contact@gurdianx.com
Phones: +255714755686 / +255752999417
Institution: UDOM
#ComplianceKwanza

## Technical Specifications

### Platform
- Backend: Node.js (Express)
- Frontend: React.js (planned)
- Database: PostgreSQL
- Authentication: JWT, bcrypt
- Audit Trail: All user actions logged
- API: RESTful, JSON

### Security
- HTTPS enforced for all environments
- JWT-based authentication + RBAC
- Passwords hashed (bcrypt)
- Rate limiting: 100 requests/15min/IP
- Data encrypted at rest and in transit
- Regular vulnerability scans: Snyk, npm audit, Dependabot

### Compliance
- GDPR, Tanzania Data Protection Act 2022, ISO 27001, WCAG 2.1 AA, OWASP Top 10

### Integrations
- MinuteHub (import/export, webhook)
- Email & SMS notifications (planned)
- Audit logs and alerts for compliance

### Testing & CI/CD
- Testing: Jest, React Testing Library
- Coverage: 80%+
- CI/CD: GitHub Actions, Docker
- Linting: ESLint, Prettier

### Documentation
- All endpoints documented in [API_DOCUMENTATION.md](./API_DOCUMENTATION.md)
- Database schema in [DATABASE_SCHEMA.md](./DATABASE_SCHEMA.md)
- Security policy in [SECURITY_POLICY.md](./SECURITY_POLICY.md)
- Changelog in [CHANGELOG.md](./CHANGELOG.md)
- Compliance notes in all docs

### Accessibility
- WCAG 2.1 AA standards for all user interfaces
- Bilingual: Swahili & English

### Contact & Support
- For technical support: kapipocostantine@gmail.com
- For compliance: costantine.mpanda@udom.ac.tz
- Phone: +255714755686 / +255752999417

#ComplianceKwanza 🛡️ | Made in Tanzania 🇹🇿