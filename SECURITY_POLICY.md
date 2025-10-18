# FollowUpHub Security Policy

**Document Type:** Security Policy & Procedures  
**Project Code:** FUH-2025  
**Version:** 1.0.0  
**Status:** Active  
**Timestamp:** 2025-10-18 22:15:41 UTC

---

## Copyright Notice

© 2025 GURDIAN-X - Costantine George Mpanda. All Rights Reserved.

---

## Table of Contents

1. [Security Philosophy](#security-philosophy)
2. [Reporting Security Vulnerabilities](#reporting-security-vulnerabilities)
3. [Authentication & Authorization](#authentication--authorization)
4. [Data Protection](#data-protection)
5. [Network Security](#network-security)
6. [Application Security](#application-security)
7. [Infrastructure Security](#infrastructure-security)
8. [Incident Response](#incident-response)
9. [Security Compliance](#security-compliance)
10. [Security Best Practices](#security-best-practices)

---

## Security Philosophy

### #ComplianceKwanza Approach

At FollowUpHub, security is not an afterthought—it's the foundation of everything we build. Our **#ComplianceKwanza** (Compliance First) philosophy ensures that:

1. **Security by Design:** Security is integrated from the start
2. **Defense in Depth:** Multiple layers of security controls
3. **Zero Trust:** Verify everything, trust nothing
4. **Least Privilege:** Minimum necessary access
5. **Audit Everything:** Comprehensive logging and monitoring
6. **Continuous Improvement:** Regular security assessments

### Security Principles

✅ **Confidentiality:** Protect sensitive data from unauthorized access  
✅ **Integrity:** Ensure data accuracy and prevent unauthorized modification  
✅ **Availability:** Maintain system availability and resilience  
✅ **Accountability:** Track and audit all actions  
✅ **Compliance:** Meet all regulatory requirements  

---

## Reporting Security Vulnerabilities

### Responsible Disclosure Policy

We take security vulnerabilities seriously and appreciate responsible disclosure from the security research community.

### How to Report

**DO NOT** publicly disclose security vulnerabilities.

**Contact Information:**
- **Primary Email:** security@gurdianx.com
- **Alternative Email:** kapipocostantine@gmail.com
- **PGP Key:** Available on request

### What to Include

Please provide the following information:
1. **Description:** Detailed vulnerability description
2. **Impact:** Potential impact and severity assessment
3. **Steps to Reproduce:** Clear reproduction steps
4. **Proof of Concept:** Code/screenshots (if applicable)
5. **Suggested Fix:** Recommended remediation (if available)
6. **Contact Information:** Your name and email for follow-up

### Response Timeline

| **Severity** | **Initial Response** | **Fix Timeline** | **Disclosure** |
|-------------|---------------------|-----------------|----------------|
| **Critical** | Within 24 hours | Within 7 days | 30 days after fix |
| **High** | Within 48 hours | Within 14 days | 45 days after fix |
| **Medium** | Within 1 week | Within 30 days | 60 days after fix |
| **Low** | Within 2 weeks | Within 60 days | 90 days after fix |

### Bug Bounty Program

Currently, we do not have a formal bug bounty program. However, we recognize and appreciate security researchers who help us improve our security posture. Public acknowledgment (with permission) will be provided in our security advisories.

---

## Authentication & Authorization

### Password Security

**Requirements:**
- Minimum 8 characters
- At least one uppercase letter
- At least one lowercase letter
- At least one number
- At least one special character
- No common passwords (dictionary check)
- No password reuse (last 5 passwords)

**Storage:**
- Bcrypt hashing (12 rounds)
- No plain text storage
- Secure random salt per password

**Password Reset:**
- Time-limited reset tokens (1 hour expiry)
- One-time use tokens
- Email verification required
- Audit logging of reset attempts

### JWT Token Security

**Access Tokens:**
- Algorithm: RS256 (RSA with SHA-256)
- Expiry: 30 minutes
- Claims: userId, role, organizationId, iat, exp
- Signature verification required

**Refresh Tokens:**
- Expiry: 7 days
- Stored in httpOnly cookies
- Rotation on each use
- Revocation support
- One refresh token per device/session

**Token Storage:**
- **Frontend:** httpOnly cookies (preferred) or Authorization header
- **Backend:** Redis (refresh tokens), Database (revocation list)
- **Never:** localStorage, sessionStorage (XSS risk)

### Multi-Factor Authentication (MFA)

**Support (Future):**
- Time-based One-Time Password (TOTP)
- SMS verification (backup)
- Email verification (backup)
- Recovery codes (10 one-time codes)

### Role-Based Access Control (RBAC)

**Roles Hierarchy:**
```
Owner (Level 5) - Full system access
  ├─ Admin (Level 4) - Organization management
  │   ├─ Manager (Level 3) - Resolution management
  │   │   ├─ User (Level 2) - Task execution
  │   │   │   └─ Viewer (Level 1) - Read-only access
```

**Permission Checks:**
- Every protected endpoint verifies authentication
- Every protected resource verifies authorization
- Role hierarchy enforced
- Resource ownership respected
- Audit logging of authorization failures

### Session Management

**Session Security:**
- Secure session IDs (cryptographically random)
- Session fixation prevention (regenerate on login)
- Session timeout: 30 minutes inactivity
- Absolute timeout: 24 hours
- Logout invalidates session immediately

**Concurrent Sessions:**
- Multiple sessions allowed per user
- Session tracking in Redis
- Ability to revoke specific sessions
- "Logout all devices" functionality

---

## Data Protection

### Encryption

**Data in Transit:**
- TLS 1.3 required (minimum TLS 1.2)
- Strong cipher suites only:
  - TLS_AES_256_GCM_SHA384
  - TLS_CHACHA20_POLY1305_SHA256
  - TLS_AES_128_GCM_SHA256
- Certificate from trusted CA (Let's Encrypt)
- HTTP Strict Transport Security (HSTS) enabled
- Perfect Forward Secrecy (PFS)

**Data at Rest:**
- Database: AES-256-GCM encryption
- Backups: AES-256 encryption
- Sensitive fields: Application-level encryption
- File storage: Cloudinary encryption
- Key management: Environment variables (production: AWS KMS)

**Key Management:**
- Secure key generation (cryptographically random)
- Key rotation schedule (every 90 days)
- No hardcoded keys in source code
- Separate keys per environment
- Key access audit logging

### Personal Data Protection (GDPR-Inspired)

**Data Minimization:**
- Collect only necessary data
- Regular data cleanup procedures
- User consent for optional data

**User Rights:**
- **Right to Access:** Export personal data (JSON format)
- **Right to Rectification:** Update inaccurate data
- **Right to Erasure:** Delete account and associated data
- **Right to Portability:** Export data in portable format
- **Right to Object:** Opt-out of non-essential processing

**Data Retention:**
- User data: Retained while account active
- Audit logs: 7 years minimum (compliance requirement)
- Deleted accounts: 30-day grace period, then permanent deletion
- Backup retention: 30 days for daily, 1 year for weekly

### Audit Logging

**What We Log:**
- Authentication events (login, logout, failures)
- Authorization failures
- Data modifications (CREATE, UPDATE, DELETE)
- Permission changes
- Configuration changes
- Data exports and reports
- Security events and alerts

**Log Security:**
- Immutable logs (no modification/deletion)
- Tamper detection (hash verification)
- Separate log storage (write-only access)
- Encrypted log transmission
- 7-year retention (minimum)

**Log Access:**
- Restricted to authorized personnel only
- Audit trail of log access
- No PII in logs (unless necessary)

---

## Network Security

### Firewall Configuration

**Inbound Rules:**
- Port 443 (HTTPS): Public access
- Port 80 (HTTP): Redirect to HTTPS
- Port 22 (SSH): Restricted IP addresses only
- Port 5432 (PostgreSQL): Internal network only
- Port 6379 (Redis): Internal network only
- All other ports: Blocked

**Outbound Rules:**
- Allow necessary external services:
  - Email (SMTP): Port 587/465
  - DNS: Port 53
  - NTP: Port 123
  - External APIs (MinuteHub, Cloudinary): HTTPS only
- Block unnecessary outbound traffic

### DDoS Protection

**Strategies:**
- Rate limiting (API level)
- Connection limits (server level)
- CDN with DDoS protection (Cloudflare/AWS CloudFront)
- Geographic filtering (if applicable)
- Automated alerting on traffic spikes

### IP Whitelisting

**Administrative Access:**
- SSH: Whitelisted IPs only
- Database: Internal network only
- Admin panel: IP restriction (optional)

**API Access:**
- Public endpoints: Global access with rate limiting
- Webhook endpoints: IP whitelist (MinuteHub)

---

## Application Security

### Input Validation & Sanitization

**Validation Rules:**
- Server-side validation (always, never trust client)
- Schema-based validation (Joi/Yup/Zod)
- Type checking (string, number, boolean, etc.)
- Length constraints (min/max)
- Format validation (email, UUID, phone, etc.)
- Whitelist approach (allow known good, not block known bad)

**Sanitization:**
- HTML/JavaScript sanitization (DOMPurify)
- SQL injection prevention (parameterized queries)
- NoSQL injection prevention (query validation)
- Command injection prevention (no shell execution with user input)
- Path traversal prevention (validate file paths)

### SQL Injection Prevention

**Strategies:**
- ✅ Use ORM (Prisma) - parameterized queries
- ✅ Input validation
- ✅ Least privilege database user
- ❌ Never concatenate user input into queries

**Example (Secure):**
```javascript
// ✅ Good: Parameterized query
const user = await prisma.user.findUnique({
  where: { email: userInput }
});

// ❌ Bad: String concatenation
const query = `SELECT * FROM users WHERE email = '${userInput}'`;
```

### XSS Prevention

**Strategies:**
- Output encoding (HTML entities)
- Content Security Policy (CSP) headers
- React's built-in XSS protection (JSX escaping)
- Sanitize rich text content (DOMPurify)
- HTTPOnly cookies (prevent JavaScript access)

**Content Security Policy:**
```http
Content-Security-Policy: 
  default-src 'self';
  script-src 'self';
  style-src 'self' 'unsafe-inline';
  img-src 'self' data: https://res.cloudinary.com;
  font-src 'self';
  connect-src 'self' https://api.followuphub.com;
  frame-ancestors 'none';
```

### CSRF Prevention

**Strategies:**
- SameSite cookies (Strict)
- CSRF tokens (for state-changing requests)
- Custom headers (X-Requested-With)
- Origin/Referer validation

### Clickjacking Prevention

**Headers:**
```http
X-Frame-Options: DENY
Content-Security-Policy: frame-ancestors 'none'
```

### Security Headers

**Required Headers:**
```http
Strict-Transport-Security: max-age=31536000; includeSubDomains; preload
X-Content-Type-Options: nosniff
X-Frame-Options: DENY
X-XSS-Protection: 1; mode=block
Referrer-Policy: no-referrer
Permissions-Policy: geolocation=(), microphone=(), camera=()
```

### Rate Limiting

**Limits:**
- Global: 100 requests per 15 minutes per IP
- Authentication: 5 requests per 15 minutes per IP
- Per user: 100 requests per 15 minutes

**Implementation:**
- Redis-backed rate limiting
- Sliding window algorithm
- Graceful degradation
- Clear error messages

### File Upload Security

**Validation:**
- File type whitelist (PDF, PNG, JPG, DOCX)
- File size limits (max 10MB)
- Filename sanitization
- Virus scanning (ClamAV)
- Store in secure location (Cloudinary)

**Prevention:**
- No execution permissions on upload directories
- Serve files from different domain/CDN
- Content-Type validation
- File signature verification

---

## Infrastructure Security

### Server Hardening

**Operating System:**
- Keep OS up to date (security patches)
- Disable unnecessary services
- Configure firewall (iptables/ufw)
- Secure SSH configuration:
  - Disable root login
  - Key-based authentication only
  - Change default port (optional)
  - Fail2ban for brute force protection

**Application Server:**
- Run as non-root user
- Limit file system permissions
- Disable directory listing
- Hide server version information

### Database Security

**Configuration:**
- Strong password (generated, rotated)
- Listen on localhost only (internal network)
- Encrypted connections (SSL/TLS)
- Regular backups (encrypted)
- Least privilege user accounts

**Access Control:**
- Application user: SELECT, INSERT, UPDATE, DELETE (no DROP, TRUNCATE)
- Read-only user: SELECT only (for reports)
- Admin user: Full access (restricted IP)

### Container Security

**Docker Best Practices:**
- Use official base images
- Minimize image layers
- No secrets in images
- Run as non-root user
- Regular image updates
- Vulnerability scanning (Trivy, Snyk)

**Docker Compose Security:**
- Isolated networks
- Resource limits (CPU, memory)
- Read-only file systems (where possible)
- Secret management (Docker secrets or env files)

### Secrets Management

**Never in Code:**
- Database credentials
- API keys
- JWT secrets
- Encryption keys
- Third-party service credentials

**Storage:**
- Development: `.env` file (gitignored)
- Staging/Production: Environment variables or AWS Secrets Manager
- Rotate regularly (quarterly minimum)

---

## Incident Response

### Security Incident Types

1. **Data Breach:** Unauthorized access to sensitive data
2. **Service Disruption:** DDoS, outage, performance degradation
3. **Malware/Ransomware:** Malicious software infection
4. **Unauthorized Access:** Account compromise, privilege escalation
5. **Data Integrity:** Data corruption, manipulation
6. **Compliance Violation:** Regulatory non-compliance

### Incident Response Plan

#### Phase 1: Detection & Analysis (0-2 hours)
- Automated alerting (security events)
- Manual detection (user reports, audits)
- Initial assessment (severity, scope, impact)
- Incident classification
- Notification to incident response team

#### Phase 2: Containment (2-4 hours)
- Short-term containment:
  - Isolate affected systems
  - Revoke compromised credentials
  - Block malicious IPs
  - Disable affected accounts
- Long-term containment:
  - Apply security patches
  - Implement additional controls
  - Monitor for further activity

#### Phase 3: Eradication (4-8 hours)
- Remove malware/backdoors
- Close vulnerabilities
- Strengthen security controls
- Verify system integrity

#### Phase 4: Recovery (8-24 hours)
- Restore from clean backups
- Verify system functionality
- Re-enable services gradually
- Monitor for recurrence
- Communicate with users (if applicable)

#### Phase 5: Post-Incident (1-7 days)
- Root cause analysis
- Document lessons learned
- Update security policies
- Implement preventive measures
- Compliance reporting (if required)

### Notification Requirements

**Internal:**
- Incident response team: Immediate
- Management: Within 1 hour (critical), 4 hours (high)
- Technical team: Within 2 hours

**External:**
- Users: Within 24 hours (if data breach)
- Regulatory authorities: Within 72 hours (TDPA 2022)
- Law enforcement: If criminal activity suspected

**Communication:**
- Clear, factual information
- Impact assessment
- Remediation steps taken
- User action required (if any)
- Contact information for questions

---

## Security Compliance

### OWASP Top 10 (2021) Mitigation

| **Risk** | **Mitigation** |
|---------|---------------|
| **A01: Broken Access Control** | RBAC, authorization checks, audit logging |
| **A02: Cryptographic Failures** | TLS 1.3, AES-256, bcrypt, secure key management |
| **A03: Injection** | Parameterized queries, input validation, ORM |
| **A04: Insecure Design** | Threat modeling, security by design, code review |
| **A05: Security Misconfiguration** | Hardening, security headers, least privilege |
| **A06: Vulnerable Components** | Dependency scanning, regular updates, SCA |
| **A07: Authentication Failures** | Strong passwords, JWT, MFA (future), rate limiting |
| **A08: Software & Data Integrity** | Code signing, CI/CD security, audit trails |
| **A09: Logging Failures** | Comprehensive logging, immutable audit logs, monitoring |
| **A10: SSRF** | Input validation, network segmentation, allowlist |

### Tanzania Data Protection Act 2022

**Compliance Requirements:**
- Data controller registration
- User consent management
- Data subject rights (access, correction, deletion)
- Data breach notification (72 hours)
- Cross-border data transfer safeguards
- Data protection impact assessment (DPIA)
- Privacy by design and default

### ISO 27001 Alignment

**Controls Implemented:**
- Information security policies
- Risk assessment and treatment
- Access control
- Cryptography
- Physical and environmental security
- Operations security
- Communications security
- System acquisition and development
- Supplier relationships
- Incident management
- Business continuity
- Compliance

---

## Security Best Practices

### For Developers

1. ✅ Follow secure coding guidelines (.cursorrules)
2. ✅ Never commit secrets to version control
3. ✅ Use parameterized queries (ORM)
4. ✅ Validate all inputs (server-side)
5. ✅ Implement proper error handling
6. ✅ Add audit logging for sensitive operations
7. ✅ Write security tests
8. ✅ Keep dependencies up to date
9. ✅ Use security linters (ESLint security plugin)
10. ✅ Participate in code reviews

### For Users

1. ✅ Use strong, unique passwords
2. ✅ Enable MFA (when available)
3. ✅ Keep software up to date
4. ✅ Be cautious of phishing attempts
5. ✅ Report suspicious activity immediately
6. ✅ Use secure networks (avoid public Wi-Fi)
7. ✅ Regularly review account activity
8. ✅ Logout when finished
9. ✅ Don't share credentials
10. ✅ Use password manager

### For Administrators

1. ✅ Follow principle of least privilege
2. ✅ Regularly review access permissions
3. ✅ Monitor audit logs
4. ✅ Keep systems patched and updated
5. ✅ Perform regular security assessments
6. ✅ Test backup and recovery procedures
7. ✅ Maintain incident response plan
8. ✅ Conduct security training
9. ✅ Document security procedures
10. ✅ Stay informed about security threats

---

## Security Contact

**Report Security Issues:**
- **Email:** security@gurdianx.com
- **Alternative:** kapipocostantine@gmail.com
- **Phone:** +255714755686 / +255752999417

**Project Owner:** Costantine George Mpanda (GURDIAN-X)  
**Institution:** University of Dodoma (UDOM)  
**Location:** Dodoma, Tanzania 🇹🇿

**Other Contacts:**
- costantine.mpanda@udom.ac.tz
- contact@gurdianx.com

**Repository:** https://github.com/GURDIAN-X/FollowUpHub

---

## Related Documentation

📄 [PROJECT_CHARTER.md](./PROJECT_CHARTER.md) - Strategic framework  
📄 [TECHNICAL_SPECS.md](./TECHNICAL_SPECS.md) - Technical architecture  
📄 [DATABASE_SCHEMA.md](./DATABASE_SCHEMA.md) - Database design  
📄 [API_DOCUMENTATION.md](./API_DOCUMENTATION.md) - API reference  
📄 [CONTRIBUTING.md](./CONTRIBUTING.md) - Contribution guidelines  

---

**Document Status:** Active  
**Last Updated:** 2025-10-18 22:15:41 UTC  
**Version:** 1.0.0  
**Next Review:** 2026-01-18  

**#ComplianceKwanza** 🛡️ | **Made in Tanzania** 🇹🇿

© 2025 GURDIAN-X - Costantine George Mpanda. All Rights Reserved.
