# FollowUpHub Database Schema

**Document Type:** Database Design & Schema Documentation  
**Project Code:** FUH-2025  
**Database:** PostgreSQL 14+  
**ORM:** Prisma 5+  
**Version:** 1.0.0  
**Status:** Active  
**Timestamp:** 2025-10-18 22:15:41 UTC

---

## Copyright Notice

© 2025 GURDIAN-X - Costantine George Mpanda. All Rights Reserved.

---

## Table of Contents

1. [Database Overview](#database-overview)
2. [Entity Relationship Diagram](#entity-relationship-diagram)
3. [Table Definitions](#table-definitions)
4. [Relationships](#relationships)
5. [Indexes](#indexes)
6. [Migrations](#migrations)
7. [Data Types & Constraints](#data-types--constraints)
8. [Security Considerations](#security-considerations)
9. [Backup & Recovery](#backup--recovery)

---

## Database Overview

### Database Information

| **Attribute** | **Value** |
|--------------|-----------|
| **Database Type** | PostgreSQL |
| **Minimum Version** | 14.0 |
| **Character Set** | UTF-8 |
| **Collation** | en_US.UTF-8 |
| **Total Tables** | 9 core tables |
| **Naming Convention** | snake_case |

### Design Principles

1. **Normalization:** Database is in 3rd Normal Form (3NF)
2. **Soft Deletes:** Use `deletedAt` instead of hard deletes
3. **Audit Trail:** Immutable `audit_logs` table for all actions
4. **UUID Primary Keys:** For security and scalability
5. **Timestamps:** All tables have `createdAt` and `updatedAt`
6. **Foreign Keys:** Enforce referential integrity
7. **Indexes:** Strategic indexing for performance

---

## Entity Relationship Diagram

```
┌─────────────────┐
│  organizations  │
│  (id, name)     │
└────────┬────────┘
         │ 1
         │
         │ N
         ├────────────────────────────────────┐
         │                                     │
         │                                     │
┌────────▼────────┐              ┌────────────▼──────┐
│      users      │              │     meetings      │
│  (id, email)    │              │   (id, title)     │
└────────┬────────┘              └────────┬──────────┘
         │ 1                              │ 1
         │                                │
         │ N                              │ N
         │                        ┌───────▼───────────┐
         │                        │  resolution_types │
         │                        │   (id, name_sw)   │
         │                        └───────┬───────────┘
         │                                │ 1
         │                                │
         │                                │ N
         │                        ┌───────▼───────────┐
         │                        │   resolutions     │
         ├────────────────────────┤ (id, description) │
         │                        └───────┬───────────┘
         │ 1                              │ 1
         │                                │
         │ N                              │ N
┌────────▼────────┐              ┌───────▼───────────┐
│   assignments   │              │     comments      │
│ (id, deadline)  │              │  (id, content)    │
└────────┬────────┘              └───────────────────┘
         │ N
         │
         │ 1
┌────────▼────────┐
│     alerts      │
│ (id, message)   │
└─────────────────┘

┌─────────────────┐
│   audit_logs    │
│  (id, action)   │  (Tracks all actions across tables)
└─────────────────┘
```

---

## Table Definitions

### 1. users

**Purpose:** Store user account information and authentication data.

**Prisma Schema:**
```prisma
model User {
  id                String         @id @default(uuid())
  email             String         @unique
  password          String         // bcrypt hashed (12 rounds)
  name              String
  phone             String?
  role              Role           @default(USER)
  organizationId    String
  organization      Organization   @relation(fields: [organizationId], references: [id])
  language          Language       @default(EN)
  theme             Theme          @default(LIGHT)
  isActive          Boolean        @default(true)
  lastLoginAt       DateTime?
  emailVerified     Boolean        @default(false)
  emailVerifiedAt   DateTime?
  passwordChangedAt DateTime?
  failedLoginCount  Int            @default(0)
  lockedUntil       DateTime?
  createdAt         DateTime       @default(now())
  updatedAt         DateTime       @updatedAt
  deletedAt         DateTime?
  
  // Relations
  createdResolutions Resolution[]  @relation("CreatedResolutions")
  assignments        Assignment[]
  comments           Comment[]
  auditLogs          AuditLog[]
  
  @@index([organizationId])
  @@index([email])
  @@index([role])
  @@map("users")
}

enum Role {
  OWNER
  ADMIN
  MANAGER
  USER
  VIEWER
}

enum Language {
  EN
  SW
}

enum Theme {
  LIGHT
  DARK
}
```

**Columns:**

| **Column** | **Type** | **Constraints** | **Description** |
|-----------|----------|----------------|-----------------|
| id | UUID | PRIMARY KEY | Unique user identifier |
| email | VARCHAR(255) | UNIQUE, NOT NULL | User email address |
| password | VARCHAR(255) | NOT NULL | Bcrypt hashed password |
| name | VARCHAR(255) | NOT NULL | User full name |
| phone | VARCHAR(20) | NULLABLE | Phone number |
| role | ENUM | NOT NULL, DEFAULT 'USER' | User role (RBAC) |
| organizationId | UUID | FOREIGN KEY, NOT NULL | Organization reference |
| language | ENUM | NOT NULL, DEFAULT 'EN' | Preferred language |
| theme | ENUM | NOT NULL, DEFAULT 'LIGHT' | UI theme preference |
| isActive | BOOLEAN | NOT NULL, DEFAULT TRUE | Account active status |
| lastLoginAt | TIMESTAMP | NULLABLE | Last successful login |
| emailVerified | BOOLEAN | NOT NULL, DEFAULT FALSE | Email verification status |
| emailVerifiedAt | TIMESTAMP | NULLABLE | Email verification timestamp |
| passwordChangedAt | TIMESTAMP | NULLABLE | Last password change |
| failedLoginCount | INTEGER | NOT NULL, DEFAULT 0 | Failed login attempts |
| lockedUntil | TIMESTAMP | NULLABLE | Account lock expiration |
| createdAt | TIMESTAMP | NOT NULL, DEFAULT NOW() | Record creation time |
| updatedAt | TIMESTAMP | NOT NULL, AUTO UPDATE | Last update time |
| deletedAt | TIMESTAMP | NULLABLE | Soft delete timestamp |

---

### 2. organizations

**Purpose:** Store organization/company information for multi-tenancy support.

**Prisma Schema:**
```prisma
model Organization {
  id              String       @id @default(uuid())
  name            String
  description     String?
  email           String?
  phone           String?
  address         String?
  website         String?
  logo            String?      // Cloudinary URL
  settings        Json?        // Organization-specific settings
  isActive        Boolean      @default(true)
  createdAt       DateTime     @default(now())
  updatedAt       DateTime     @updatedAt
  deletedAt       DateTime?
  
  // Relations
  users           User[]
  meetings        Meeting[]
  resolutions     Resolution[]
  
  @@map("organizations")
}
```

**Columns:**

| **Column** | **Type** | **Constraints** | **Description** |
|-----------|----------|----------------|-----------------|
| id | UUID | PRIMARY KEY | Unique organization identifier |
| name | VARCHAR(255) | NOT NULL | Organization name |
| description | TEXT | NULLABLE | Organization description |
| email | VARCHAR(255) | NULLABLE | Contact email |
| phone | VARCHAR(20) | NULLABLE | Contact phone |
| address | TEXT | NULLABLE | Physical address |
| website | VARCHAR(255) | NULLABLE | Website URL |
| logo | VARCHAR(255) | NULLABLE | Logo URL (Cloudinary) |
| settings | JSONB | NULLABLE | Organization settings |
| isActive | BOOLEAN | NOT NULL, DEFAULT TRUE | Active status |
| createdAt | TIMESTAMP | NOT NULL, DEFAULT NOW() | Creation timestamp |
| updatedAt | TIMESTAMP | NOT NULL, AUTO UPDATE | Last update |
| deletedAt | TIMESTAMP | NULLABLE | Soft delete timestamp |

---

### 3. meetings

**Purpose:** Store meeting information (optional, for context).

**Prisma Schema:**
```prisma
model Meeting {
  id              String       @id @default(uuid())
  title           String
  description     String?
  meetingDate     DateTime
  location        String?
  organizationId  String
  organization    Organization @relation(fields: [organizationId], references: [id])
  minuteHubId     String?      // External reference to MinuteHub
  isActive        Boolean      @default(true)
  createdAt       DateTime     @default(now())
  updatedAt       DateTime     @updatedAt
  deletedAt       DateTime?
  
  // Relations
  resolutions     Resolution[]
  
  @@index([organizationId])
  @@index([meetingDate])
  @@map("meetings")
}
```

**Columns:**

| **Column** | **Type** | **Constraints** | **Description** |
|-----------|----------|----------------|-----------------|
| id | UUID | PRIMARY KEY | Unique meeting identifier |
| title | VARCHAR(255) | NOT NULL | Meeting title |
| description | TEXT | NULLABLE | Meeting description |
| meetingDate | TIMESTAMP | NOT NULL | Meeting date and time |
| location | VARCHAR(255) | NULLABLE | Meeting location |
| organizationId | UUID | FOREIGN KEY, NOT NULL | Organization reference |
| minuteHubId | VARCHAR(255) | NULLABLE | MinuteHub external ID |
| isActive | BOOLEAN | NOT NULL, DEFAULT TRUE | Active status |
| createdAt | TIMESTAMP | NOT NULL, DEFAULT NOW() | Creation timestamp |
| updatedAt | TIMESTAMP | NOT NULL, AUTO UPDATE | Last update |
| deletedAt | TIMESTAMP | NULLABLE | Soft delete timestamp |

---

### 4. resolution_types

**Purpose:** Store resolution type definitions (ILIELEKEZA/ILISHAURIWA).

**Prisma Schema:**
```prisma
model ResolutionType {
  id          String       @id @default(uuid())
  nameSw      String       @unique  // Swahili name
  nameEn      String       @unique  // English name
  code        String       @unique  // ILIELEKEZA or ILISHAURIWA
  description String?
  isMandatory Boolean      @default(true)
  priority    Int          @default(1)
  createdAt   DateTime     @default(now())
  updatedAt   DateTime     @updatedAt
  
  // Relations
  resolutions Resolution[]
  
  @@map("resolution_types")
}
```

**Default Data:**

| **code** | **nameSw** | **nameEn** | **isMandatory** | **priority** |
|---------|-----------|-----------|----------------|-------------|
| ILIELEKEZA | Ilielekeza | Directive | TRUE | 1 |
| ILISHAURIWA | Ilishauriwa | Recommendation | FALSE | 2 |

---

### 5. resolutions

**Purpose:** Store meeting resolutions and decisions.

**Prisma Schema:**
```prisma
model Resolution {
  id                 String           @id @default(uuid())
  description        String
  resolutionTypeId   String
  resolutionType     ResolutionType   @relation(fields: [resolutionTypeId], references: [id])
  organizationId     String
  organization       Organization     @relation(fields: [organizationId], references: [id])
  meetingId          String?
  meeting            Meeting?         @relation(fields: [meetingId], references: [id])
  status             ResolutionStatus @default(PENDING)
  priority           Priority         @default(MEDIUM)
  deadline           DateTime?
  completedAt        DateTime?
  createdBy          String
  createdByUser      User             @relation("CreatedResolutions", fields: [createdBy], references: [id])
  notes              String?
  attachments        Json?            // Array of Cloudinary URLs
  externalRef        String?          // MinuteHub reference
  createdAt          DateTime         @default(now())
  updatedAt          DateTime         @updatedAt
  deletedAt          DateTime?
  
  // Relations
  assignments        Assignment[]
  comments           Comment[]
  
  @@index([organizationId])
  @@index([status])
  @@index([createdBy])
  @@index([deadline])
  @@map("resolutions")
}

enum ResolutionStatus {
  PENDING
  IN_PROGRESS
  COMPLETED
  CANCELLED
  DEFERRED
}

enum Priority {
  LOW
  MEDIUM
  HIGH
  URGENT
}
```

**Columns:**

| **Column** | **Type** | **Constraints** | **Description** |
|-----------|----------|----------------|-----------------|
| id | UUID | PRIMARY KEY | Unique resolution identifier |
| description | TEXT | NOT NULL | Resolution description |
| resolutionTypeId | UUID | FOREIGN KEY, NOT NULL | Type reference |
| organizationId | UUID | FOREIGN KEY, NOT NULL | Organization reference |
| meetingId | UUID | FOREIGN KEY, NULLABLE | Meeting reference |
| status | ENUM | NOT NULL, DEFAULT 'PENDING' | Current status |
| priority | ENUM | NOT NULL, DEFAULT 'MEDIUM' | Priority level |
| deadline | TIMESTAMP | NULLABLE | Completion deadline |
| completedAt | TIMESTAMP | NULLABLE | Completion timestamp |
| createdBy | UUID | FOREIGN KEY, NOT NULL | Creator user ID |
| notes | TEXT | NULLABLE | Additional notes |
| attachments | JSONB | NULLABLE | File URLs |
| externalRef | VARCHAR(255) | NULLABLE | MinuteHub ID |
| createdAt | TIMESTAMP | NOT NULL, DEFAULT NOW() | Creation timestamp |
| updatedAt | TIMESTAMP | NOT NULL, AUTO UPDATE | Last update |
| deletedAt | TIMESTAMP | NULLABLE | Soft delete timestamp |

---

### 6. assignments

**Purpose:** Assign resolutions to users with deadlines and tracking.

**Prisma Schema:**
```prisma
model Assignment {
  id                String             @id @default(uuid())
  resolutionId      String
  resolution        Resolution         @relation(fields: [resolutionId], references: [id])
  assigneeId        String
  assignee          User               @relation(fields: [assigneeId], references: [id])
  status            AssignmentStatus   @default(PENDING)
  deadline          DateTime
  completedAt       DateTime?
  notes             String?
  progress          Int                @default(0)  // 0-100%
  createdAt         DateTime           @default(now())
  updatedAt         DateTime           @updatedAt
  deletedAt         DateTime?
  
  // Relations
  alerts            Alert[]
  
  @@index([resolutionId])
  @@index([assigneeId])
  @@index([status])
  @@index([deadline])
  @@map("assignments")
}

enum AssignmentStatus {
  PENDING
  IN_PROGRESS
  COMPLETED
  OVERDUE
  CANCELLED
}
```

**Columns:**

| **Column** | **Type** | **Constraints** | **Description** |
|-----------|----------|----------------|-----------------|
| id | UUID | PRIMARY KEY | Unique assignment identifier |
| resolutionId | UUID | FOREIGN KEY, NOT NULL | Resolution reference |
| assigneeId | UUID | FOREIGN KEY, NOT NULL | Assigned user ID |
| status | ENUM | NOT NULL, DEFAULT 'PENDING' | Assignment status |
| deadline | TIMESTAMP | NOT NULL | Completion deadline |
| completedAt | TIMESTAMP | NULLABLE | Completion timestamp |
| notes | TEXT | NULLABLE | Assignment notes |
| progress | INTEGER | NOT NULL, DEFAULT 0 | Progress percentage (0-100) |
| createdAt | TIMESTAMP | NOT NULL, DEFAULT NOW() | Creation timestamp |
| updatedAt | TIMESTAMP | NOT NULL, AUTO UPDATE | Last update |
| deletedAt | TIMESTAMP | NULLABLE | Soft delete timestamp |

---

### 7. comments

**Purpose:** Allow users to comment on resolutions for collaboration.

**Prisma Schema:**
```prisma
model Comment {
  id             String     @id @default(uuid())
  content        String
  resolutionId   String
  resolution     Resolution @relation(fields: [resolutionId], references: [id])
  userId         String
  user           User       @relation(fields: [userId], references: [id])
  parentId       String?    // For nested comments (future)
  parent         Comment?   @relation("CommentReplies", fields: [parentId], references: [id])
  replies        Comment[]  @relation("CommentReplies")
  attachments    Json?      // Array of file URLs
  createdAt      DateTime   @default(now())
  updatedAt      DateTime   @updatedAt
  deletedAt      DateTime?
  
  @@index([resolutionId])
  @@index([userId])
  @@map("comments")
}
```

**Columns:**

| **Column** | **Type** | **Constraints** | **Description** |
|-----------|----------|----------------|-----------------|
| id | UUID | PRIMARY KEY | Unique comment identifier |
| content | TEXT | NOT NULL | Comment content |
| resolutionId | UUID | FOREIGN KEY, NOT NULL | Resolution reference |
| userId | UUID | FOREIGN KEY, NOT NULL | Comment author |
| parentId | UUID | FOREIGN KEY, NULLABLE | Parent comment (replies) |
| attachments | JSONB | NULLABLE | File URLs |
| createdAt | TIMESTAMP | NOT NULL, DEFAULT NOW() | Creation timestamp |
| updatedAt | TIMESTAMP | NOT NULL, AUTO UPDATE | Last update |
| deletedAt | TIMESTAMP | NULLABLE | Soft delete timestamp |

---

### 8. audit_logs (IMMUTABLE)

**Purpose:** Immutable audit trail for all system actions (7-year retention).

**Prisma Schema:**
```prisma
model AuditLog {
  id             String      @id @default(uuid())
  userId         String?
  user           User?       @relation(fields: [userId], references: [id])
  action         AuditAction
  resourceType   String      // 'Resolution', 'User', 'Assignment', etc.
  resourceId     String?
  oldValues      Json?       // Before state (for updates)
  newValues      Json?       // After state (for creates/updates)
  ipAddress      String
  userAgent      String?
  sessionId      String?
  timestamp      DateTime    @default(now())  // IMMUTABLE
  
  @@index([userId])
  @@index([action])
  @@index([resourceType])
  @@index([timestamp])
  @@map("audit_logs")
}

enum AuditAction {
  // Authentication
  LOGIN
  LOGOUT
  LOGIN_FAILED
  PASSWORD_CHANGED
  
  // Authorization
  PERMISSION_GRANTED
  PERMISSION_DENIED
  
  // CRUD Operations
  CREATE_RESOLUTION
  UPDATE_RESOLUTION
  DELETE_RESOLUTION
  CREATE_ASSIGNMENT
  UPDATE_ASSIGNMENT
  DELETE_ASSIGNMENT
  CREATE_COMMENT
  UPDATE_COMMENT
  DELETE_COMMENT
  
  // User Management
  CREATE_USER
  UPDATE_USER
  DELETE_USER
  ACTIVATE_USER
  DEACTIVATE_USER
  
  // Data Export
  EXPORT_DATA
  GENERATE_REPORT
  
  // System
  CONFIG_CHANGED
  ALERT_TRIGGERED
}
```

**Columns:**

| **Column** | **Type** | **Constraints** | **Description** |
|-----------|----------|----------------|-----------------|
| id | UUID | PRIMARY KEY | Unique log identifier |
| userId | UUID | FOREIGN KEY, NULLABLE | User who performed action |
| action | ENUM | NOT NULL | Action type |
| resourceType | VARCHAR(50) | NOT NULL | Resource affected |
| resourceId | UUID | NULLABLE | Resource identifier |
| oldValues | JSONB | NULLABLE | Previous values (updates) |
| newValues | JSONB | NULLABLE | New values (creates/updates) |
| ipAddress | VARCHAR(45) | NOT NULL | User IP address |
| userAgent | TEXT | NULLABLE | User agent string |
| sessionId | UUID | NULLABLE | Session identifier |
| timestamp | TIMESTAMP | NOT NULL, DEFAULT NOW() | Action timestamp (IMMUTABLE) |

**Important:** This table is IMMUTABLE. No UPDATE or DELETE operations allowed.

---

### 9. alerts

**Purpose:** Store system-generated alerts and notifications.

**Prisma Schema:**
```prisma
model Alert {
  id             String      @id @default(uuid())
  assignmentId   String?
  assignment     Assignment? @relation(fields: [assignmentId], references: [id])
  type           AlertType
  severity       AlertSeverity @default(INFO)
  message        String
  messageSw      String      // Swahili message
  messageEn      String      // English message
  isRead         Boolean     @default(false)
  readAt         DateTime?
  sentAt         DateTime?
  createdAt      DateTime    @default(now())
  
  @@index([assignmentId])
  @@index([type])
  @@index([isRead])
  @@map("alerts")
}

enum AlertType {
  DEADLINE_APPROACHING
  TASK_OVERDUE
  TASK_COMPLETED
  ASSIGNMENT_CREATED
  COMMENT_ADDED
  STATUS_CHANGED
  PERFORMANCE_DEGRADATION
  SECURITY_BREACH
  DATA_INTEGRITY
  COMPLIANCE_VIOLATION
}

enum AlertSeverity {
  INFO
  WARNING
  ERROR
  CRITICAL
}
```

**Columns:**

| **Column** | **Type** | **Constraints** | **Description** |
|-----------|----------|----------------|-----------------|
| id | UUID | PRIMARY KEY | Unique alert identifier |
| assignmentId | UUID | FOREIGN KEY, NULLABLE | Related assignment |
| type | ENUM | NOT NULL | Alert type |
| severity | ENUM | NOT NULL, DEFAULT 'INFO' | Alert severity |
| message | TEXT | NOT NULL | Alert message (generic) |
| messageSw | TEXT | NOT NULL | Swahili message |
| messageEn | TEXT | NOT NULL | English message |
| isRead | BOOLEAN | NOT NULL, DEFAULT FALSE | Read status |
| readAt | TIMESTAMP | NULLABLE | Read timestamp |
| sentAt | TIMESTAMP | NULLABLE | Email sent timestamp |
| createdAt | TIMESTAMP | NOT NULL, DEFAULT NOW() | Creation timestamp |

---

## Relationships

### Primary Relationships

| **Parent Table** | **Child Table** | **Relationship** | **Foreign Key** |
|-----------------|----------------|-----------------|----------------|
| organizations | users | 1:N | users.organizationId |
| organizations | meetings | 1:N | meetings.organizationId |
| organizations | resolutions | 1:N | resolutions.organizationId |
| users | resolutions | 1:N | resolutions.createdBy |
| users | assignments | 1:N | assignments.assigneeId |
| users | comments | 1:N | comments.userId |
| users | audit_logs | 1:N | audit_logs.userId |
| meetings | resolutions | 1:N | resolutions.meetingId |
| resolution_types | resolutions | 1:N | resolutions.resolutionTypeId |
| resolutions | assignments | 1:N | assignments.resolutionId |
| resolutions | comments | 1:N | comments.resolutionId |
| assignments | alerts | 1:N | alerts.assignmentId |
| comments | comments | 1:N | comments.parentId (self-ref) |

### Cascade Rules

**ON DELETE:**
- users → SET NULL (audit_logs, assignments, comments)
- organizations → CASCADE (users, meetings, resolutions)
- resolutions → CASCADE (assignments, comments)
- assignments → CASCADE (alerts)

**ON UPDATE:**
- CASCADE for all foreign keys

---

## Indexes

### Primary Indexes

All tables have automatic primary key indexes on `id` column (UUID).

### Foreign Key Indexes

```sql
-- users table
CREATE INDEX idx_users_organization_id ON users(organization_id);
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_role ON users(role);

-- meetings table
CREATE INDEX idx_meetings_organization_id ON meetings(organization_id);
CREATE INDEX idx_meetings_meeting_date ON meetings(meeting_date);

-- resolutions table
CREATE INDEX idx_resolutions_organization_id ON resolutions(organization_id);
CREATE INDEX idx_resolutions_status ON resolutions(status);
CREATE INDEX idx_resolutions_created_by ON resolutions(created_by);
CREATE INDEX idx_resolutions_deadline ON resolutions(deadline);

-- assignments table
CREATE INDEX idx_assignments_resolution_id ON assignments(resolution_id);
CREATE INDEX idx_assignments_assignee_id ON assignments(assignee_id);
CREATE INDEX idx_assignments_status ON assignments(status);
CREATE INDEX idx_assignments_deadline ON assignments(deadline);

-- comments table
CREATE INDEX idx_comments_resolution_id ON comments(resolution_id);
CREATE INDEX idx_comments_user_id ON comments(user_id);

-- audit_logs table
CREATE INDEX idx_audit_logs_user_id ON audit_logs(user_id);
CREATE INDEX idx_audit_logs_action ON audit_logs(action);
CREATE INDEX idx_audit_logs_resource_type ON audit_logs(resource_type);
CREATE INDEX idx_audit_logs_timestamp ON audit_logs(timestamp);

-- alerts table
CREATE INDEX idx_alerts_assignment_id ON alerts(assignment_id);
CREATE INDEX idx_alerts_type ON alerts(type);
CREATE INDEX idx_alerts_is_read ON alerts(is_read);
```

### Composite Indexes (Performance Optimization)

```sql
-- For pagination queries
CREATE INDEX idx_resolutions_org_created ON resolutions(organization_id, created_at DESC);
CREATE INDEX idx_assignments_user_status ON assignments(assignee_id, status);

-- For filtering and sorting
CREATE INDEX idx_resolutions_status_deadline ON resolutions(status, deadline);
CREATE INDEX idx_assignments_status_deadline ON assignments(status, deadline);
```

---

## Migrations

### Migration Strategy

**Tool:** Prisma Migrate

**Commands:**
```bash
# Create migration
npx prisma migrate dev --name initial_schema

# Apply migration (production)
npx prisma migrate deploy

# Generate Prisma Client
npx prisma generate

# Reset database (development only)
npx prisma migrate reset
```

### Initial Migration Script

```sql
-- Create extensions
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

-- Create enums
CREATE TYPE "Role" AS ENUM ('OWNER', 'ADMIN', 'MANAGER', 'USER', 'VIEWER');
CREATE TYPE "Language" AS ENUM ('EN', 'SW');
CREATE TYPE "Theme" AS ENUM ('LIGHT', 'DARK');
CREATE TYPE "ResolutionStatus" AS ENUM ('PENDING', 'IN_PROGRESS', 'COMPLETED', 'CANCELLED', 'DEFERRED');
CREATE TYPE "Priority" AS ENUM ('LOW', 'MEDIUM', 'HIGH', 'URGENT');
CREATE TYPE "AssignmentStatus" AS ENUM ('PENDING', 'IN_PROGRESS', 'COMPLETED', 'OVERDUE', 'CANCELLED');
CREATE TYPE "AlertType" AS ENUM ('DEADLINE_APPROACHING', 'TASK_OVERDUE', 'TASK_COMPLETED', 'ASSIGNMENT_CREATED', 'COMMENT_ADDED', 'STATUS_CHANGED', 'PERFORMANCE_DEGRADATION', 'SECURITY_BREACH', 'DATA_INTEGRITY', 'COMPLIANCE_VIOLATION');
CREATE TYPE "AlertSeverity" AS ENUM ('INFO', 'WARNING', 'ERROR', 'CRITICAL');
CREATE TYPE "AuditAction" AS ENUM ('LOGIN', 'LOGOUT', 'LOGIN_FAILED', 'PASSWORD_CHANGED', 'PERMISSION_GRANTED', 'PERMISSION_DENIED', 'CREATE_RESOLUTION', 'UPDATE_RESOLUTION', 'DELETE_RESOLUTION', 'CREATE_ASSIGNMENT', 'UPDATE_ASSIGNMENT', 'DELETE_ASSIGNMENT', 'CREATE_COMMENT', 'UPDATE_COMMENT', 'DELETE_COMMENT', 'CREATE_USER', 'UPDATE_USER', 'DELETE_USER', 'ACTIVATE_USER', 'DEACTIVATE_USER', 'EXPORT_DATA', 'GENERATE_REPORT', 'CONFIG_CHANGED', 'ALERT_TRIGGERED');

-- Create tables in order (respecting dependencies)
-- 1. organizations
-- 2. users
-- 3. meetings
-- 4. resolution_types
-- 5. resolutions
-- 6. assignments
-- 7. comments
-- 8. audit_logs
-- 9. alerts

-- (Full SQL DDL provided in actual migration files)
```

### Seed Data Script

```javascript
// prisma/seed.js
const { PrismaClient } = require('@prisma/client');
const prisma = new PrismaClient();

async function main() {
  // Create default organization
  const org = await prisma.organization.create({
    data: {
      name: 'GURDIAN-X',
      description: 'FollowUpHub Development Organization',
      email: 'contact@gurdianx.com',
      isActive: true
    }
  });

  // Create resolution types
  await prisma.resolutionType.createMany({
    data: [
      {
        code: 'ILIELEKEZA',
        nameSw: 'Ilielekeza',
        nameEn: 'Directive',
        description: 'Mandatory resolution that must be implemented',
        isMandatory: true,
        priority: 1
      },
      {
        code: 'ILISHAURIWA',
        nameSw: 'Ilishauriwa',
        nameEn: 'Recommendation',
        description: 'Optional resolution for consideration',
        isMandatory: false,
        priority: 2
      }
    ]
  });

  console.log('Seed data created successfully');
}

main()
  .catch(e => {
    console.error(e);
    process.exit(1);
  })
  .finally(async () => {
    await prisma.$disconnect();
  });
```

---

## Data Types & Constraints

### UUID Usage

**Why UUIDs:**
- Security: Not sequential, harder to guess
- Scalability: No auto-increment conflicts
- Distribution: Can generate offline
- Merging: No ID collisions

**Implementation:**
```sql
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";
id UUID PRIMARY KEY DEFAULT uuid_generate_v4()
```

### JSONB Usage

**Fields Using JSONB:**
- `organizations.settings` - Organization configuration
- `resolutions.attachments` - File URLs array
- `comments.attachments` - File URLs array
- `audit_logs.oldValues` - Previous state
- `audit_logs.newValues` - New state

**Benefits:**
- Flexible schema
- Indexable (GIN indexes)
- Query support
- Type validation

### Timestamp Handling

**All timestamps are:**
- Timezone-aware (TIMESTAMPTZ)
- UTC stored
- Auto-generated (`createdAt`, `updatedAt`)
- Immutable (`audit_logs.timestamp`)

### Soft Deletes

**Implementation:**
```prisma
deletedAt DateTime?

// Query active records only
where: {
  deletedAt: null
}

// Soft delete
update({
  where: { id },
  data: { deletedAt: new Date() }
})
```

---

## Security Considerations

### Row-Level Security (Future)

**PostgreSQL RLS:**
```sql
-- Enable RLS on sensitive tables
ALTER TABLE resolutions ENABLE ROW LEVEL SECURITY;

-- Policy: Users can only see their organization's data
CREATE POLICY org_isolation ON resolutions
  FOR ALL
  TO authenticated_users
  USING (organization_id = current_setting('app.current_org_id')::uuid);
```

### Audit Log Immutability

**Trigger to prevent modifications:**
```sql
CREATE OR REPLACE FUNCTION prevent_audit_log_changes()
RETURNS TRIGGER AS $$
BEGIN
  IF TG_OP = 'UPDATE' OR TG_OP = 'DELETE' THEN
    RAISE EXCEPTION 'Audit logs are immutable and cannot be modified or deleted';
  END IF;
  RETURN NEW;
END;
$$ LANGUAGE plpgsql;

CREATE TRIGGER immutable_audit_logs
BEFORE UPDATE OR DELETE ON audit_logs
FOR EACH ROW EXECUTE FUNCTION prevent_audit_log_changes();
```

### Database User Permissions

**Application User:**
```sql
CREATE USER followuphub_app WITH PASSWORD 'secure_password';
GRANT CONNECT ON DATABASE followuphub TO followuphub_app;
GRANT SELECT, INSERT, UPDATE ON ALL TABLES IN SCHEMA public TO followuphub_app;
GRANT DELETE ON ALL TABLES IN SCHEMA public TO followuphub_app;
REVOKE DELETE ON audit_logs FROM followuphub_app;  -- Cannot delete audit logs
```

**Read-Only User (Reports):**
```sql
CREATE USER followuphub_readonly WITH PASSWORD 'secure_password';
GRANT CONNECT ON DATABASE followuphub TO followuphub_readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO followuphub_readonly;
```

---

## Backup & Recovery

### Backup Strategy

**Daily Full Backups:**
```bash
pg_dump -U postgres -F c -b -v -f "backup_$(date +%Y%m%d).dump" followuphub
```

**Retention:**
- Daily backups: 30 days
- Weekly backups: 1 year
- Monthly backups: 7 years (audit compliance)

### Recovery Procedures

**Full Restore:**
```bash
pg_restore -U postgres -d followuphub -v "backup_20251018.dump"
```

**Point-in-Time Recovery:**
```bash
# Requires WAL archiving enabled
pg_basebackup -U postgres -D /backup/base -Fp -Xs -P
```

**Testing:**
- Monthly recovery drills
- Verify data integrity
- Document RTO/RPO metrics

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
📄 [API_DOCUMENTATION.md](./API_DOCUMENTATION.md) - API endpoints  
📄 [SECURITY_POLICY.md](./SECURITY_POLICY.md) - Security policies  
📄 [README.md](./README.md) - Project overview  

---

**Document Status:** Active  
**Last Updated:** 2025-10-18 22:15:41 UTC  
**Version:** 1.0.0  

**#ComplianceKwanza** 🛡️ | **Made in Tanzania** 🇹🇿

© 2025 GURDIAN-X - Costantine George Mpanda. All Rights Reserved.
