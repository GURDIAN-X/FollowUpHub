# FollowUpHub Technical Specifications

**Document Type:** Technical Architecture & Specifications  
**Project Code:** FUH-2025  
**Version:** 1.0.0  
**Status:** Active  
**Timestamp:** 2025-10-18 22:15:41 UTC

---

## Copyright Notice

© 2025 GURDIAN-X - Costantine George Mpanda. All Rights Reserved.

---

## Table of Contents

1. [System Overview](#system-overview)
2. [Architecture](#architecture)
3. [Technology Stack](#technology-stack)
4. [System Components](#system-components)
5. [API Architecture](#api-architecture)
6. [Database Architecture](#database-architecture)
7. [Security Architecture](#security-architecture)
8. [Integration Architecture](#integration-architecture)
9. [Deployment Architecture](#deployment-architecture)
10. [Performance Specifications](#performance-specifications)
11. [Scalability Considerations](#scalability-considerations)
12. [Monitoring & Logging](#monitoring--logging)

---

## System Overview

### Purpose

FollowUpHub is an enterprise-grade compliance-focused resolution tracking system designed to help organizations track and implement meeting resolutions with full audit trails, bilingual support, and MinuteHub integration.

### Key Characteristics

- **Type:** Web-based SaaS application
- **Architecture:** Three-tier (Presentation, Application, Data)
- **Deployment:** Cloud-native (containerized)
- **Scalability:** Horizontal scaling capable
- **Availability Target:** 99.5% uptime
- **Performance Target:** <200ms API response, <2s page load

### System Boundaries

**In Scope:**
- Web application (responsive design)
- RESTful API backend
- PostgreSQL database
- Authentication & authorization
- Audit logging system
- Alert & notification system
- MinuteHub integration
- Email service integration

**Out of Scope:**
- Native mobile applications
- Real-time chat/messaging
- Video conferencing
- Blockchain integration
- Payment processing

---

## Architecture

### High-Level Architecture Diagram

```
┌─────────────────────────────────────────────────────────────────┐
│                         PRESENTATION LAYER                       │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │   Web App    │  │  Mobile Web  │  │  MinuteHub   │         │
│  │   (React)    │  │  (Responsive)│  │  Integration │         │
│  └──────┬───────┘  └──────┬───────┘  └──────┬───────┘         │
│         │                  │                  │                  │
└─────────┼──────────────────┼──────────────────┼─────────────────┘
          │                  │                  │
          ▼                  ▼                  ▼
┌─────────────────────────────────────────────────────────────────┐
│                       APPLICATION LAYER                          │
│  ┌──────────────────────────────────────────────────────────┐  │
│  │                   API Gateway (Express)                   │  │
│  │  - Rate Limiting  - CORS  - Security Headers             │  │
│  └────────┬──────────────────────────────────────────┬───────┘  │
│           │                                            │          │
│  ┌────────▼────────┐  ┌──────────────┐  ┌───────────▼──────┐  │
│  │  Authentication  │  │   Business   │  │   Integration    │  │
│  │  & Authorization │  │     Logic    │  │     Services     │  │
│  │   (JWT, RBAC)   │  │   Services   │  │  (MinuteHub API) │  │
│  └─────────────────┘  └──────┬───────┘  └──────────────────┘  │
│                               │                                  │
└───────────────────────────────┼─────────────────────────────────┘
                                │
                                ▼
┌─────────────────────────────────────────────────────────────────┐
│                          DATA LAYER                              │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐         │
│  │  PostgreSQL  │  │    Redis     │  │  Cloudinary  │         │
│  │   Database   │  │    Cache     │  │    (Files)   │         │
│  └──────────────┘  └──────────────┘  └──────────────┘         │
└─────────────────────────────────────────────────────────────────┘
```

### Component Interaction Flow

```
User Request Flow:
1. User → React App (UI interaction)
2. React App → API Gateway (AJAX request)
3. API Gateway → Authentication Middleware (JWT verification)
4. Authentication → Authorization Middleware (RBAC check)
5. Authorization → Business Logic (route handler)
6. Business Logic → Database (Prisma ORM)
7. Database → Business Logic (query results)
8. Business Logic → Audit Log (record action)
9. Business Logic → API Gateway (response)
10. API Gateway → React App (JSON response)
11. React App → User (UI update)
```

---

## Technology Stack

### Frontend

| **Component** | **Technology** | **Version** | **Purpose** |
|--------------|---------------|------------|-------------|
| Framework | React | 18+ | UI component library |
| Build Tool | Vite | 5+ | Fast development and building |
| Styling | Tailwind CSS | 3+ | Utility-first CSS framework |
| Routing | React Router | 6+ | Client-side routing |
| State Management | Zustand | 4+ | Lightweight state management |
| Forms | React Hook Form | 7+ | Form validation and handling |
| HTTP Client | Axios | 1.6+ | API communication |
| Internationalization | i18next | 23+ | Bilingual support (SW/EN) |
| Icons | Lucide React | Latest | Icon library |
| Date/Time | date-fns | 3+ | Date manipulation |
| Charts | Recharts | 2+ | Data visualization |
| Testing | React Testing Library | 14+ | Component testing |

### Backend

| **Component** | **Technology** | **Version** | **Purpose** |
|--------------|---------------|------------|-------------|
| Runtime | Node.js | 18 LTS | JavaScript runtime |
| Framework | Express | 4+ | Web application framework |
| ORM | Prisma | 5+ | Database ORM |
| Authentication | jsonwebtoken | 9+ | JWT token generation/verification |
| Password Hashing | bcrypt | 5+ | Password encryption (12 rounds) |
| Validation | Joi | 17+ | Input validation |
| Security Headers | helmet | 7+ | HTTP security headers |
| Rate Limiting | express-rate-limit | 7+ | API rate limiting |
| CORS | cors | 2.8+ | Cross-origin resource sharing |
| Logging | winston | 3+ | Application logging |
| Email | nodemailer | 6+ | Email notifications |
| Caching | ioredis | 5+ | Redis client |
| Testing | Jest + Supertest | 29+ | API testing |
| Process Manager | PM2 | 5+ | Production process management |

### Database

| **Component** | **Technology** | **Version** | **Purpose** |
|--------------|---------------|------------|-------------|
| Database | PostgreSQL | 14+ | Primary data storage |
| Cache | Redis | 7+ | Session and data caching |
| Migrations | Prisma Migrate | 5+ | Database schema versioning |
| Backup | pg_dump | 14+ | Database backups |

### Infrastructure

| **Component** | **Technology** | **Version** | **Purpose** |
|--------------|---------------|------------|-------------|
| Containerization | Docker | 24+ | Application containerization |
| Orchestration | Docker Compose | 2.20+ | Multi-container orchestration |
| Web Server | Nginx | 1.24+ | Reverse proxy, SSL termination |
| SSL/TLS | Let's Encrypt | Latest | Free SSL certificates |
| CI/CD | GitHub Actions | N/A | Continuous integration/deployment |
| Monitoring | Prometheus + Grafana | Latest | Metrics and visualization |
| File Storage | Cloudinary | N/A | Image and file uploads |

### Development Tools

| **Component** | **Technology** | **Version** | **Purpose** |
|--------------|---------------|------------|-------------|
| Package Manager | npm | 10+ | Dependency management |
| Code Quality | ESLint | 8+ | JavaScript linting |
| Formatting | Prettier | 3+ | Code formatting |
| Version Control | Git | 2.40+ | Source control |
| API Testing | Postman/Insomnia | Latest | API development and testing |

---

## System Components

### 1. Frontend Application (React)

**Directory Structure:**
```
frontend/
├── public/
│   ├── index.html
│   └── favicon.ico
├── src/
│   ├── assets/          # Images, fonts
│   ├── components/      # Reusable UI components
│   │   ├── common/      # Buttons, inputs, modals
│   │   ├── layout/      # Header, footer, sidebar
│   │   └── resolutions/ # Resolution-specific components
│   ├── pages/           # Page components
│   │   ├── Dashboard.jsx
│   │   ├── Resolutions.jsx
│   │   ├── Assignments.jsx
│   │   └── Profile.jsx
│   ├── hooks/           # Custom React hooks
│   ├── services/        # API service functions
│   ├── store/           # Zustand state management
│   ├── utils/           # Utility functions
│   ├── locales/         # Translation files (en.json, sw.json)
│   ├── App.jsx          # Root component
│   └── main.jsx         # Entry point
├── .env.example
├── vite.config.js
├── tailwind.config.js
├── package.json
└── README.md
```

**Key Features:**
- Server-side rendering (SSR) not required initially
- Code splitting for optimal loading
- Lazy loading for routes
- Dark/light theme support
- Responsive design (mobile-first)
- Accessibility (WCAG 2.1 AA)
- Bilingual support (SW/EN toggle)

### 2. Backend API (Express)

**Directory Structure:**
```
backend/
├── src/
│   ├── config/          # Configuration files
│   │   ├── database.js
│   │   ├── jwt.js
│   │   └── email.js
│   ├── middleware/      # Express middleware
│   │   ├── auth.js      # Authentication
│   │   ├── authorize.js # Authorization (RBAC)
│   │   ├── validate.js  # Input validation
│   │   ├── errorHandler.js
│   │   └── rateLimiter.js
│   ├── routes/          # API routes
│   │   ├── auth.js
│   │   ├── users.js
│   │   ├── organizations.js
│   │   ├── resolutions.js
│   │   ├── assignments.js
│   │   ├── comments.js
│   │   └── webhooks.js
│   ├── controllers/     # Route handlers
│   │   ├── authController.js
│   │   ├── resolutionController.js
│   │   └── ...
│   ├── services/        # Business logic
│   │   ├── authService.js
│   │   ├── resolutionService.js
│   │   ├── emailService.js
│   │   └── auditService.js
│   ├── utils/           # Utility functions
│   │   ├── jwt.js
│   │   ├── logger.js
│   │   └── validators.js
│   ├── prisma/          # Prisma ORM
│   │   ├── schema.prisma
│   │   └── migrations/
│   ├── tests/           # Test files
│   │   ├── unit/
│   │   └── integration/
│   ├── app.js           # Express app setup
│   └── server.js        # Server entry point
├── .env.example
├── package.json
├── jest.config.js
└── README.md
```

**Key Features:**
- RESTful API design
- JWT authentication (access + refresh tokens)
- RBAC authorization (5 roles)
- Comprehensive input validation
- Rate limiting (per user and global)
- CORS configuration
- Security headers (helmet)
- Audit logging (all actions)
- Error handling middleware
- API versioning (/api/v1/)

### 3. Database (PostgreSQL)

**Schema Overview:**
- 9 core tables (see DATABASE_SCHEMA.md)
- Foreign key relationships
- Indexes on frequently queried fields
- Soft delete support (deletedAt column)
- Audit trail table (immutable)
- Timestamp columns (createdAt, updatedAt)

**Key Features:**
- ACID compliance
- Row-level security (future consideration)
- Automatic backups (daily)
- Point-in-time recovery
- Replication for high availability (production)
- Connection pooling

### 4. Cache Layer (Redis)

**Use Cases:**
- Session storage (JWT refresh tokens)
- Rate limiting counters
- Frequently accessed data (user profiles, organization settings)
- Temporary data (email verification codes)
- Background job queue (future)

**Key Features:**
- In-memory data structure store
- TTL support (automatic expiration)
- Pub/sub for real-time features (future)
- Persistence options (RDB + AOF)

---

## API Architecture

### RESTful Principles

**Resource-Based URLs:**
- `/api/v1/resolutions` - Resolution collection
- `/api/v1/resolutions/:id` - Single resolution
- `/api/v1/resolutions/:id/assignments` - Nested resource

**HTTP Methods:**
- `GET` - Retrieve resource(s)
- `POST` - Create new resource
- `PUT` - Update entire resource
- `PATCH` - Partial update
- `DELETE` - Delete resource (soft delete)

**Status Codes:**
- `200 OK` - Successful GET/PUT/PATCH
- `201 Created` - Successful POST
- `204 No Content` - Successful DELETE
- `400 Bad Request` - Validation error
- `401 Unauthorized` - Authentication required
- `403 Forbidden` - Insufficient permissions
- `404 Not Found` - Resource not found
- `409 Conflict` - Duplicate resource
- `422 Unprocessable Entity` - Business logic error
- `429 Too Many Requests` - Rate limit exceeded
- `500 Internal Server Error` - Server error

### API Response Format

**Success Response:**
```json
{
  "success": true,
  "data": {
    "id": "uuid",
    "description": "Resolution description",
    "type": "ILIELEKEZA",
    "status": "IN_PROGRESS",
    "createdAt": "2025-10-18T22:15:41.000Z"
  },
  "meta": {
    "timestamp": "2025-10-18T22:15:41.000Z"
  }
}
```

**Error Response:**
```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input data",
    "details": [
      {
        "field": "description",
        "message": "Description must be at least 10 characters"
      }
    ]
  },
  "meta": {
    "timestamp": "2025-10-18T22:15:41.000Z"
  }
}
```

**Pagination Response:**
```json
{
  "success": true,
  "data": [...],
  "pagination": {
    "page": 1,
    "perPage": 20,
    "total": 150,
    "totalPages": 8
  },
  "meta": {
    "timestamp": "2025-10-18T22:15:41.000Z"
  }
}
```

### Authentication Flow

**Login:**
```
1. POST /api/v1/auth/login { email, password }
2. Server validates credentials
3. Server generates access token (30min) + refresh token (7d)
4. Server returns tokens in httpOnly cookies
5. Client stores user data in state/localStorage
```

**Authenticated Request:**
```
1. Client includes access token in Authorization header
2. Server verifies token signature and expiration
3. Server extracts user ID from token
4. Server loads user from database
5. Server checks RBAC permissions
6. Server processes request and returns response
```

**Token Refresh:**
```
1. POST /api/v1/auth/refresh (with refresh token cookie)
2. Server validates refresh token
3. Server generates new access token
4. Server returns new access token
5. Client updates stored token
```

### Rate Limiting Strategy

**Global Limits:**
- 100 requests per 15 minutes per IP address

**Authentication Endpoints:**
- 5 requests per 15 minutes per IP address
- Prevents brute force attacks

**Per-User Limits:**
- 100 requests per 15 minutes per authenticated user
- Prevents abuse by compromised accounts

**Implementation:**
```javascript
const rateLimit = require('express-rate-limit');

const globalLimiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100,
  message: 'Too many requests from this IP'
});

const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,
  message: 'Too many authentication attempts'
});
```

---

## Database Architecture

### Connection Pooling

**Prisma Configuration:**
```javascript
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

// Connection pool settings
connection_limit = 20  // Maximum connections
timeout = 30s          // Connection timeout
```

**Best Practices:**
- Use connection pooling in production
- Monitor active connections
- Set appropriate timeout values
- Close connections properly

### Indexing Strategy

**Primary Indexes:**
- Primary keys (UUID) - automatic
- Foreign keys - manual creation

**Secondary Indexes:**
- Email addresses (users table)
- Status fields (resolutions, assignments)
- Date fields (createdAt, deadline)
- Organization ID (all multi-tenant tables)

**Composite Indexes:**
- (organizationId, createdAt) for pagination
- (userId, status) for user-specific queries

### Backup Strategy

**Automated Backups:**
- Daily full backups (retained 30 days)
- Hourly incremental backups (retained 7 days)
- Weekly backups (retained 1 year)

**Backup Storage:**
- Primary: Cloud storage (encrypted)
- Secondary: Off-site storage (disaster recovery)

**Recovery Procedures:**
- Point-in-time recovery capability
- Testing recovery monthly
- RTO: 4 hours
- RPO: 1 hour

---

## Security Architecture

### Authentication Security

**JWT Implementation:**
- Algorithm: RS256 (RSA with SHA-256)
- Access token expiry: 30 minutes
- Refresh token expiry: 7 days
- Token rotation on refresh
- Secure token storage (httpOnly cookies)

**Password Security:**
- Bcrypt hashing (12 rounds)
- Minimum length: 8 characters
- Complexity requirements enforced
- Password history (prevent reuse)
- Account lockout after 5 failed attempts

### Authorization (RBAC)

**Role Hierarchy:**
```
Owner (Level 5)
  ├─ Full system access
  ├─ Manage all organizations
  └─ Assign any role

Admin (Level 4)
  ├─ Manage organization
  ├─ Manage users (except Owner)
  └─ View all data

Manager (Level 3)
  ├─ Create/edit resolutions
  ├─ Assign tasks
  └─ View team data

User (Level 2)
  ├─ View assigned resolutions
  ├─ Update own assignments
  └─ Add comments

Viewer (Level 1)
  ├─ Read-only access
  └─ View resolutions and reports
```

**Permission Check Implementation:**
```javascript
const authorize = (requiredRole) => {
  return async (req, res, next) => {
    const roleHierarchy = {
      Owner: 5,
      Admin: 4,
      Manager: 3,
      User: 2,
      Viewer: 1
    };
    
    const userLevel = roleHierarchy[req.user.role] || 0;
    const requiredLevel = roleHierarchy[requiredRole] || 0;
    
    if (userLevel < requiredLevel) {
      return res.status(403).json({
        error: 'Insufficient permissions'
      });
    }
    
    next();
  };
};
```

### Data Encryption

**In Transit:**
- TLS 1.3 required
- Strong cipher suites only
- HSTS enabled (max-age: 1 year)
- Certificate pinning (mobile apps)

**At Rest:**
- Database: AES-256 encryption
- Backups: AES-256 encryption
- File storage: Cloudinary encryption
- Sensitive fields: Application-level encryption

### Security Headers

**Helmet Configuration:**
```javascript
app.use(helmet({
  contentSecurityPolicy: {
    directives: {
      defaultSrc: ["'self'"],
      styleSrc: ["'self'", "'unsafe-inline'"],
      scriptSrc: ["'self'"],
      imgSrc: ["'self'", "data:", "https://res.cloudinary.com"]
    }
  },
  hsts: {
    maxAge: 31536000,
    includeSubDomains: true,
    preload: true
  },
  frameguard: { action: 'deny' },
  noSniff: true,
  referrerPolicy: { policy: 'no-referrer' }
}));
```

### Input Validation & Sanitization

**Validation Strategy:**
- Server-side validation (all inputs)
- Schema-based validation (Joi)
- Type checking
- Length constraints
- Format validation (email, UUID, etc.)

**XSS Prevention:**
- Input sanitization
- Output encoding
- Content-Security-Policy headers
- React's built-in XSS protection

**SQL Injection Prevention:**
- Parameterized queries (Prisma ORM)
- No raw SQL (unless necessary)
- Input validation
- Least privilege database user

---

## Integration Architecture

### MinuteHub Integration

**Integration Methods:**

1. **Webhook Integration (Push):**
   - MinuteHub → FollowUpHub
   - Event: Meeting concluded
   - Endpoint: `POST /api/v1/webhooks/minutehub`
   - Authentication: HMAC signature verification

2. **REST API Integration (Pull):**
   - FollowUpHub → MinuteHub
   - Endpoint: `GET /api/v1/minutehub/meetings/:id`
   - Authentication: API key

3. **Status Sync (Bidirectional):**
   - Resolution status updates
   - Real-time or scheduled sync
   - Conflict resolution strategy

**Webhook Implementation:**
```javascript
// Webhook signature verification
const verifySignature = (payload, signature, secret) => {
  const hmac = crypto.createHmac('sha256', secret);
  const expectedSignature = hmac.update(payload).digest('hex');
  return crypto.timingSafeEqual(
    Buffer.from(signature),
    Buffer.from(expectedSignature)
  );
};

// Webhook handler
app.post('/api/v1/webhooks/minutehub', async (req, res) => {
  const signature = req.headers['x-minutehub-signature'];
  const payload = JSON.stringify(req.body);
  
  if (!verifySignature(payload, signature, process.env.MINUTEHUB_SECRET)) {
    return res.status(401).json({ error: 'Invalid signature' });
  }
  
  // Process webhook data
  await processMinuteHubWebhook(req.body);
  
  res.status(200).json({ success: true });
});
```

### Email Integration

**SMTP Configuration:**
- Provider: Configurable (Gmail, SendGrid, AWS SES)
- TLS/SSL required
- Authentication: Username/password or API key

**Email Types:**
- Welcome email (new user registration)
- Password reset
- Task assignment notifications
- Deadline reminders (3 days, 1 day before)
- Overdue task alerts
- Resolution status updates

**Email Template System:**
- HTML templates (responsive)
- Bilingual support (SW/EN)
- Variable substitution
- Unsubscribe link (required)

---

## Deployment Architecture

### Development Environment

**Local Setup:**
```
Docker Compose:
  - PostgreSQL container
  - Redis container
  - Backend API (port 3000)
  - Frontend dev server (port 5173)
```

**Configuration:**
- Environment variables (.env)
- Hot reload enabled
- Debug logging
- No caching

### Staging Environment

**Infrastructure:**
- Cloud provider (DigitalOcean, AWS, Azure)
- Single server or small cluster
- PostgreSQL managed database
- Redis managed cache

**Configuration:**
- Production-like settings
- Reduced resources
- Comprehensive logging
- Performance monitoring

### Production Environment

**Infrastructure:**
```
┌─────────────────────────────────────┐
│        Load Balancer (Nginx)        │
│     (SSL Termination, Rate Limit)   │
└──────────┬──────────────────────────┘
           │
    ┌──────┴──────┐
    │             │
┌───▼────┐   ┌───▼────┐
│  API   │   │  API   │  (Multiple instances)
│Server 1│   │Server 2│
└───┬────┘   └───┬────┘
    │             │
    └──────┬──────┘
           │
    ┌──────▼──────────────┐
    │                     │
┌───▼─────────┐   ┌──────▼──────┐
│ PostgreSQL  │   │    Redis    │
│  (Primary)  │   │   Cluster   │
└─────┬───────┘   └─────────────┘
      │
┌─────▼───────┐
│ PostgreSQL  │
│  (Replica)  │
└─────────────┘
```

**Configuration:**
- Horizontal scaling (API servers)
- Database replication
- Redis cluster
- Automated backups
- CDN for static assets
- Log aggregation
- Monitoring and alerting

### Containerization (Docker)

**Backend Dockerfile:**
```dockerfile
FROM node:18-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

RUN npx prisma generate

EXPOSE 3000

CMD ["node", "src/server.js"]
```

**Frontend Dockerfile:**
```dockerfile
FROM node:18-alpine AS build

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
COPY nginx.conf /etc/nginx/nginx.conf

EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
```

### CI/CD Pipeline (GitHub Actions)

**Workflow:**
```yaml
name: CI/CD Pipeline

on:
  push:
    branches: [main, develop]
  pull_request:
    branches: [main, develop]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm ci
      - run: npm run lint
      - run: npm test
      - run: npm run test:coverage

  build:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: docker/build-push-action@v4
        with:
          context: .
          push: true
          tags: followuphub:latest

  deploy:
    needs: build
    runs-on: ubuntu-latest
    if: github.ref == 'refs/heads/main'
    steps:
      - name: Deploy to production
        run: |
          # Deployment script
```

---

## Performance Specifications

### Response Time Targets

| **Endpoint Type** | **Target** | **Maximum** |
|------------------|-----------|------------|
| Authentication | <100ms | 200ms |
| Simple GET | <50ms | 100ms |
| Complex GET (joins) | <150ms | 300ms |
| POST/PUT/PATCH | <100ms | 200ms |
| DELETE | <50ms | 100ms |
| Search queries | <200ms | 400ms |
| Reports | <500ms | 1000ms |

### Database Query Optimization

**Strategies:**
- Use indexes on frequently queried fields
- Avoid N+1 queries (use includes)
- Select only needed fields
- Implement pagination
- Use database views for complex queries
- Cache frequently accessed data

**Example Optimized Query:**
```javascript
// ❌ Bad (N+1 query)
const resolutions = await prisma.resolution.findMany();
for (const res of resolutions) {
  res.assignments = await prisma.assignment.findMany({
    where: { resolutionId: res.id }
  });
}

// ✅ Good (single query with join)
const resolutions = await prisma.resolution.findMany({
  include: {
    assignments: true,
    createdByUser: {
      select: { id: true, name: true, email: true }
    }
  }
});
```

### Caching Strategy

**Redis Caching:**
```javascript
// Cache user data (30 minutes TTL)
const getUserById = async (userId) => {
  const cached = await redis.get(`user:${userId}`);
  if (cached) return JSON.parse(cached);
  
  const user = await prisma.user.findUnique({
    where: { id: userId }
  });
  
  await redis.setex(`user:${userId}`, 1800, JSON.stringify(user));
  return user;
};
```

**Cache Invalidation:**
- On data update/delete
- TTL expiration
- Manual invalidation (admin action)

### Load Testing

**Tools:**
- Apache JMeter
- k6
- Artillery

**Test Scenarios:**
- Concurrent users: 100, 500, 1000
- Requests per second: 100, 500, 1000
- Sustained load (30 minutes)
- Spike testing
- Stress testing (to failure)

---

## Scalability Considerations

### Horizontal Scaling

**API Servers:**
- Stateless design (JWT, no sessions)
- Load balancer distribution
- Auto-scaling based on CPU/memory
- Health checks and failover

**Database:**
- Read replicas for read-heavy operations
- Connection pooling
- Query optimization
- Partitioning (future consideration)

### Vertical Scaling

**Server Resources:**
- CPU: 2-4 cores (initial), 8-16 cores (growth)
- RAM: 4-8GB (initial), 16-32GB (growth)
- Storage: SSD, 50GB (initial), 500GB+ (growth)

### Microservices Migration (Future)

**Candidates for Extraction:**
- Email service
- Notification service
- Audit logging service
- Report generation service
- MinuteHub integration service

---

## Monitoring & Logging

### Application Logging

**Winston Configuration:**
```javascript
const winston = require('winston');

const logger = winston.createLogger({
  level: process.env.LOG_LEVEL || 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  transports: [
    new winston.transports.File({
      filename: 'logs/error.log',
      level: 'error'
    }),
    new winston.transports.File({
      filename: 'logs/combined.log'
    })
  ]
});

if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: winston.format.simple()
  }));
}
```

**Log Levels:**
- `error`: Application errors
- `warn`: Warning conditions
- `info`: Informational messages
- `debug`: Debug information (dev only)

### Performance Monitoring

**Metrics to Track:**
- API response times (per endpoint)
- Database query times
- Error rates
- Request rates
- CPU/memory usage
- Cache hit/miss rates

**Tools:**
- Prometheus (metrics collection)
- Grafana (visualization)
- New Relic or Datadog (APM)

### Error Tracking

**Sentry Integration:**
- Automatic error capture
- Stack traces
- User context
- Breadcrumbs (user actions before error)
- Email alerts for critical errors

### Health Checks

**Endpoints:**
- `/health` - Basic health check
- `/health/db` - Database connectivity
- `/health/redis` - Redis connectivity

**Implementation:**
```javascript
app.get('/health', (req, res) => {
  res.status(200).json({
    status: 'healthy',
    uptime: process.uptime(),
    timestamp: new Date().toISOString()
  });
});

app.get('/health/db', async (req, res) => {
  try {
    await prisma.$queryRaw`SELECT 1`;
    res.status(200).json({ status: 'healthy' });
  } catch (error) {
    res.status(503).json({ status: 'unhealthy' });
  }
});
```

---

## Contact Information

**Project Owner:** Costantine George Mpanda (GURDIAN-X)  
**Organization:** GURDIAN-X  
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

📄 [PROJECT_CHARTER.md](./PROJECT_CHARTER.md) - Strategic framework and requirements  
📄 [README.md](./README.md) - Project overview and quick start  
📄 [DATABASE_SCHEMA.md](./DATABASE_SCHEMA.md) - Database design and schema  
📄 [API_DOCUMENTATION.md](./API_DOCUMENTATION.md) - API endpoints and usage  
📄 [SECURITY_POLICY.md](./SECURITY_POLICY.md) - Security policies and procedures  
📄 [CONTRIBUTING.md](./CONTRIBUTING.md) - Contribution guidelines  
📄 [CHANGELOG.md](./CHANGELOG.md) - Version history and changes  
📄 [.cursorrules](./.cursorrules) - AI assistant guidelines  

---

**Document Status:** Active  
**Last Updated:** 2025-10-18 22:15:41 UTC  
**Next Review:** 2026-01-18  
**Version:** 1.0.0  

**#ComplianceKwanza** 🛡️ | **Made in Tanzania** 🇹🇿

© 2025 GURDIAN-X - Costantine George Mpanda. All Rights Reserved.
