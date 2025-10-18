# FollowUpHub

**Enterprise-Grade Resolution Tracking System with Comprehensive Audit Trails**

[![License](https://img.shields.io/badge/License-Proprietary-red.svg)](./LICENSE)
[![Version](https://img.shields.io/badge/Version-0.1.0-blue.svg)](./CHANGELOG.md)
[![Made in Tanzania](https://img.shields.io/badge/Made_in-Tanzania_🇹🇿-green.svg)](https://www.udom.ac.tz)
[![#ComplianceKwanza](https://img.shields.io/badge/%23ComplianceKwanza-🛡️-orange.svg)](./PROJECT_CHARTER.md)

---

## Project Overview

FollowUpHub is an enterprise-grade, compliance-focused meeting resolution tracking system designed to help organizations efficiently track and implement decisions from meetings with full transparency, security, and regulatory compliance.

**Project Code:** FUH-2025  
**Owner:** Costantine George Mpanda (GURDIAN-X)  
**Institution:** University of Dodoma (UDOM)  
**Location:** Dodoma, Tanzania 🇹🇿  
**Status:** Documentation Phase (v0.1.0)  
**Target Launch:** Q2 2026

---

## Copyright Notice

© 2025 GURDIAN-X - Costantine George Mpanda. All Rights Reserved.

This is proprietary software. See [LICENSE](./LICENSE) for details.

---

## Mission & Vision

### Mission Statement

**Swahili (SW):**
> Kuwezesha mashirika kufuatilia na kutekeleza maamuzi yaliyotokana na mikutano kwa ufanisi, uwazi, na uangalifu wa sheria, huku tukitumia teknolojia ya kisasa na mfumo wa ukaguzi usioweza kubadilishwa.

**English (EN):**
> To empower organizations to track and implement meeting resolutions with efficiency, transparency, and legal compliance, using modern technology and immutable audit systems.

### Vision Statement

**Swahili (SW):**
> Kuwa mfumo wa kwanza wa Afrika wa kufuatilia maamuzi ya kikao wenye viwango vya kimataifa vya uangalifu wa sheria na usalama wa data.

**English (EN):**
> To become Africa's leading meeting resolution tracking system with international standards of legal compliance and data security.

---

## Core Philosophy: #ComplianceKwanza 🛡️

**Compliance First** - Every decision prioritizes legal and regulatory compliance, security, and data protection. This is not negotiable.

**Key Principles:**
1. **Security by Design** - Security integrated from the start
2. **Defense in Depth** - Multiple layers of security controls
3. **Zero Trust** - Verify everything, trust nothing
4. **Least Privilege** - Minimum necessary access
5. **Audit Everything** - Comprehensive logging and monitoring
6. **Continuous Improvement** - Regular security assessments

---

## Key Features

### Resolution Tracking
✅ **ILIELEKEZA** (Directive/Mandatory) - Resolutions that MUST be implemented  
✅ **ILISHAURIWA** (Recommendation/Optional) - Resolutions for consideration  
✅ **Status Tracking** - Real-time resolution and task status monitoring  
✅ **Deadline Management** - Automatic deadline tracking and reminders  
✅ **Priority Levels** - Low, Medium, High, Urgent priorities

### Compliance & Audit
✅ **Immutable Audit Trails** - 7-year retention, tamper-proof logging  
✅ **GDPR-Inspired** - Data protection principles applied  
✅ **Tanzania Data Protection Act 2022** - Full compliance  
✅ **ISO 27001 Alignment** - Information security standards  
✅ **OWASP Top 10** - Security vulnerability mitigation  
✅ **WCAG 2.1 AA** - Accessibility compliance

### User Experience
✅ **Bilingual Interface** - Swahili/English language toggle  
✅ **Dark/Light Mode** - User preference support  
✅ **Responsive Design** - Mobile-friendly interface  
✅ **Intuitive Navigation** - User-centric design  
✅ **Accessibility** - WCAG 2.1 AA compliant

### Integration & Automation
✅ **MinuteHub Integration** - Automatic resolution import via webhooks  
✅ **Email Notifications** - Automated alerts and reminders  
✅ **Alert System** - Overdue tasks, performance, security alerts  
✅ **RESTful API** - Comprehensive API for integrations  
✅ **Webhook Support** - Real-time event notifications

### Security & Authentication
✅ **JWT Authentication** - Secure token-based authentication  
✅ **RBAC** - 5 roles (Owner, Admin, Manager, User, Viewer)  
✅ **TLS 1.3 Encryption** - Data in transit security  
✅ **AES-256 Encryption** - Data at rest security  
✅ **Rate Limiting** - DDoS protection and abuse prevention  
✅ **Input Validation** - XSS and SQL injection prevention

---

## Technology Stack

### Frontend
- **React 18+** - UI component library
- **Tailwind CSS 3+** - Utility-first CSS framework
- **Vite** - Fast build tool
- **React Router 6+** - Client-side routing
- **Zustand** - State management
- **i18next** - Internationalization (SW/EN)

### Backend
- **Node.js 18 LTS** - JavaScript runtime
- **Express 4+** - Web application framework
- **Prisma 5+** - Database ORM
- **PostgreSQL 14+** - Primary database
- **Redis 7+** - Cache and session store
- **JWT** - Authentication tokens
- **bcrypt** - Password hashing (12 rounds)

### Infrastructure
- **Docker** - Containerization
- **Nginx** - Reverse proxy, SSL termination
- **PM2** - Process management
- **GitHub Actions** - CI/CD pipeline
- **Cloudinary** - File storage

### Security & Compliance
- **Helmet** - Security headers
- **express-rate-limit** - Rate limiting
- **Joi** - Input validation
- **Winston** - Logging
- **Jest + Supertest** - Testing (≥80% coverage)

---

## Database Schema

**9 Core Tables:**
1. **users** - User accounts and authentication
2. **organizations** - Multi-tenancy support
3. **meetings** - Meeting records
4. **resolution_types** - ILIELEKEZA/ILISHAURIWA definitions
5. **resolutions** - Meeting resolutions/decisions
6. **assignments** - Task assignments with deadlines
7. **comments** - Collaboration and discussion
8. **audit_logs** - Immutable audit trail (7-year retention)
9. **alerts** - System notifications and alerts

See [DATABASE_SCHEMA.md](./DATABASE_SCHEMA.md) for complete details.

---

## API Overview

**Base URL:** `https://api.followuphub.com/api/v1`

**Core Endpoints:**
- `POST /auth/login` - User authentication
- `GET /resolutions` - List resolutions (with pagination)
- `POST /resolutions` - Create new resolution
- `GET /assignments` - List user assignments
- `PUT /assignments/:id` - Update assignment status
- `POST /comments` - Add comment to resolution
- `GET /audit-logs` - View audit trail (Admin+)
- `POST /webhooks/minutehub` - MinuteHub webhook

See [API_DOCUMENTATION.md](./API_DOCUMENTATION.md) for complete API reference.

---

## Installation Guide

### Prerequisites
- Node.js 18+ and npm 10+
- PostgreSQL 14+
- Redis 7+
- Git

### Quick Start

```bash
# Clone repository
git clone https://github.com/GURDIAN-X/FollowUpHub.git
cd FollowUpHub

# Backend setup
cd backend
npm install
cp .env.example .env
# Edit .env with your configuration
npx prisma migrate dev
npm run dev

# Frontend setup (new terminal)
cd ../frontend
npm install
cp .env.example .env
# Edit .env with your configuration
npm run dev
```

**Access:**
- Frontend: http://localhost:5173
- Backend API: http://localhost:3000/api/v1

See [CONTRIBUTING.md](./CONTRIBUTING.md) for detailed setup instructions.

---

## Documentation

### Strategic & Planning
📄 **[PROJECT_CHARTER.md](./PROJECT_CHARTER.md)** - Complete strategic framework, objectives, KPIs, compliance requirements, governance, risk management, quality standards, and AI agent instructions

### Technical Documentation
📄 **[TECHNICAL_SPECS.md](./TECHNICAL_SPECS.md)** - System architecture, technology stack, component design, deployment specifications, and performance requirements

📄 **[DATABASE_SCHEMA.md](./DATABASE_SCHEMA.md)** - Database design with 9 tables, relationships, indexes, migrations, and backup strategies

📄 **[API_DOCUMENTATION.md](./API_DOCUMENTATION.md)** - Complete API reference with all endpoints, request/response formats, authentication, and error handling

### Security & Compliance
📄 **[SECURITY_POLICY.md](./SECURITY_POLICY.md)** - Comprehensive security policies, vulnerability reporting, authentication, encryption, incident response, and compliance (OWASP, TDPA 2022, ISO 27001)

### Development & Contribution
📄 **[CONTRIBUTING.md](./CONTRIBUTING.md)** - Contribution guidelines, code standards, testing requirements, pull request process, and issue reporting

📄 **[.cursorrules](./.cursorrules)** - AI assistant compliance guidelines for secure code generation

### Project Management
📄 **[CHANGELOG.md](./CHANGELOG.md)** - Version history and release notes

📄 **[LICENSE](./LICENSE)** - Copyright and licensing information (Proprietary)

### Configuration
📄 **[.gitignore](./.gitignore)** - Security-focused version control exclusions

📄 **[.env.example](./.env.example)** - Environment variable template

---

## Key Performance Indicators (KPIs)

| **Category** | **Metric** | **Target** |
|-------------|-----------|-----------|
| **Code Quality** | Test Coverage | ≥80% |
| **Code Quality** | Code Quality Score | ≥85% |
| **Performance** | API Response Time | <200ms |
| **Performance** | Page Load Time | <2s |
| **Performance** | System Uptime | ≥99.5% |
| **Security** | Security Compliance | 100% |
| **Security** | Vulnerability Remediation | <24h |
| **User Adoption** | User Adoption Rate | ≥70% |
| **User Adoption** | User Satisfaction | ≥4.0/5.0 |

---

## Roadmap

### Phase 1: Foundation (Q4 2025)
- [x] Repository setup and documentation ✅
- [ ] Database schema implementation
- [ ] Backend API development
- [ ] Security framework implementation
- [ ] Initial test coverage (≥60%)

### Phase 2: Core Features (Q1 2026)
- [ ] Frontend React application
- [ ] Resolution and assignment management
- [ ] Audit logging system
- [ ] Alert mechanisms
- [ ] Test coverage increase (≥70%)

### Phase 3: Integration & Enhancement (Q2 2026)
- [ ] MinuteHub integration
- [ ] Advanced reporting features
- [ ] Performance optimization
- [ ] Accessibility compliance verification
- [ ] Test coverage target (≥80%)

### Phase 4: Launch & Deployment (Q2 2026)
- [ ] Production environment setup
- [ ] Security penetration testing
- [ ] User acceptance testing
- [ ] Documentation finalization
- [ ] Beta user onboarding

---

## Contributing

We welcome contributions from developers, security experts, compliance specialists, and users who share our vision. Please read [CONTRIBUTING.md](./CONTRIBUTING.md) for:

- Code of conduct
- Development setup
- Coding standards
- Testing requirements
- Pull request process
- Issue reporting

**Key Requirements:**
- Follow #ComplianceKwanza philosophy
- Maintain ≥80% test coverage
- Follow coding standards (ESLint, Prettier)
- Ensure accessibility (WCAG 2.1 AA)
- Support bilingual (SW/EN)
- Security first, always

---

## Security

Security is our top priority. For security vulnerabilities:

**DO NOT** create public GitHub issues.

**Contact:**
- **Email:** security@gurdianx.com
- **Alternative:** kapipocostantine@gmail.com

See [SECURITY_POLICY.md](./SECURITY_POLICY.md) for:
- Responsible disclosure policy
- Security features and controls
- Incident response procedures
- Compliance requirements

---

## License

**Copyright © 2025 GURDIAN-X - Costantine George Mpanda. All Rights Reserved.**

This is proprietary software licensed for personal, educational, and evaluation use. Commercial use requires explicit permission. See [LICENSE](./LICENSE) for complete terms.

---

## Contact Information

**Project Owner:** Costantine George Mpanda (GURDIAN-X)  
**Organization:** GURDIAN-X  
**Institution:** University of Dodoma (UDOM)  
**Location:** Dodoma, Tanzania 🇹🇿

**Email Addresses:**
- kapipocostantine@gmail.com
- costantine.mpanda@udom.ac.tz
- contact@gurdianx.com

**Phone Numbers:**
- Primary: +255714755686
- Secondary: +255752999417

**Repository:** https://github.com/GURDIAN-X/FollowUpHub

---

## Acknowledgments

This project was developed at the **University of Dodoma (UDOM)**, Dodoma, Tanzania, as part of the commitment to building world-class, compliance-focused software solutions in Africa.

**Special Thanks:**
- University of Dodoma (UDOM) for institutional support
- The open-source community for excellent tools and libraries
- Contributors and early adopters for feedback and suggestions

---

## Project Status

**Current Version:** 0.1.0 (Documentation Phase)  
**Last Updated:** 2025-10-18 22:15:41 UTC  
**Status:** ✅ Documentation Complete - Ready for Development Phase  

**Next Milestone:** Database Implementation (v0.2.0)

---

**#ComplianceKwanza** 🛡️ | **Made in Tanzania** 🇹🇿

© 2025 GURDIAN-X - Costantine George Mpanda. All Rights Reserved.