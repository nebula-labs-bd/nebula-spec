# Nebula ERP System Architecture

| Field | Value |
|-------|-------|
| Document ID | DOC-002 |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

This document defines the architectural blueprint of Nebula ERP. It establishes the technical direction, system boundaries, architectural principles, communication patterns, and major system components that guide the implementation of the platform.

This document is the authoritative reference for all future backend, frontend, database, API, infrastructure, and deployment decisions.

---

# 2. Architecture Vision

Nebula ERP is designed as a modern, modular, enterprise-grade ERP platform capable of serving businesses ranging from a single retail shop to large multi-branch organizations.

The architecture prioritizes:

- Scalability
- Maintainability
- Security
- Performance
- Developer Experience
- User Experience
- Extensibility

Every architectural decision must support long-term product evolution without requiring major rewrites.

---

# 3. Architectural Principles

## 3.1 Modular by Design

Every major business capability is implemented as an independent module.

Examples include:

- Authentication
- Organization
- Products
- Inventory
- Purchasing
- Sales
- POS
- Warehouse
- CRM
- Accounting
- Reports
- Repair
- Warranty

Each module owns its own business logic while exposing functionality through clearly defined interfaces.

---

## 3.2 API First

Every business capability must be accessible through documented REST APIs.

Benefits include:

- Mobile support
- Third-party integrations
- Automation
- Future microservices
- AI integrations

The web application itself consumes the same APIs as external systems.

---

## 3.3 Cloud Native

Nebula ERP is designed for cloud deployment while remaining fully functional in self-hosted environments.

Supported deployment targets include:

- Docker
- Virtual Machines
- Bare Metal Servers
- Kubernetes (future)

---

## 3.4 Security by Default

Security is never optional.

The architecture must provide:

- JWT Authentication
- Refresh Tokens
- Role-Based Access Control (RBAC)
- Permission-Based Authorization
- Secure Password Hashing
- HTTPS Everywhere
- Input Validation
- Audit Logging
- Secure File Storage

---

## 3.5 Performance First

Performance is considered a feature.

Every component should minimize:

- Database queries
- API latency
- Rendering time
- Memory usage
- Bundle size

Caching should be applied where appropriate without sacrificing data consistency.

---

## 3.6 AI Ready

Every module should expose structured data suitable for AI-powered features.

Future AI capabilities include:

- Smart Search
- Business Insights
- Inventory Forecasting
- Purchase Suggestions
- Financial Analysis
- Natural Language Queries
- Automated Report Generation

---

## 3.7 Event Driven

Business events should trigger downstream processes.

Examples:

Purchase Approved

↓

Inventory Updated

↓

Supplier Balance Updated

↓

Accounting Journal Created

↓

Audit Log Recorded

↓

Notifications Sent

This minimizes coupling between modules.

---

# 4. High-Level Architecture

Nebula ERP follows a layered architecture.

```
┌──────────────────────────────────────────────┐
│                Web Browser                   │
└──────────────────────────────────────────────┘
                    │
                    ▼
┌──────────────────────────────────────────────┐
│           React Frontend (Vite)              │
└──────────────────────────────────────────────┘
                    │
                HTTPS / REST
                    │
                    ▼
┌──────────────────────────────────────────────┐
│             NestJS Backend API               │
└──────────────────────────────────────────────┘
        │           │            │
        ▼           ▼            ▼
 PostgreSQL      Redis        File Storage
        │           │            │
        ▼           ▼            ▼
 Audit Logs   Cache / Jobs   Documents

```

---

# 5. System Layers

Nebula ERP consists of multiple logical layers.

## Presentation Layer

Responsibilities:

- User Interface
- Navigation
- Forms
- Dashboards
- Data Visualization

Technology:

- React
- TypeScript
- Tailwind CSS
- shadcn/ui

---

## Application Layer

Responsibilities:

- Business Logic
- Validation
- Authorization
- API Endpoints
- Workflow Coordination

Technology:

- NestJS

---

## Domain Layer

Contains:

- Business Rules
- Services
- Use Cases
- Domain Events

No UI logic should exist in this layer.

---

## Data Access Layer

Responsible for:

- Database Access
- Transactions
- Queries
- Repositories

Technology:

- Prisma ORM

---

## Infrastructure Layer

Provides:

- Authentication
- File Storage
- Email
- Logging
- Background Jobs
- Notifications
- External Integrations

---

# 6. Core System Components

The architecture is composed of the following major components.

## Frontend

Responsibilities:

- User Interface
- Authentication
- Routing
- State Management
- API Communication
- Theme Management
- Localization

---

## Backend API

Responsibilities:

- Authentication
- Business Logic
- Validation
- Authorization
- Database Access
- Background Processing

---

## PostgreSQL Database

Stores:

- Business Data
- Users
- Transactions
- Products
- Inventory
- Accounting
- Audit Logs

---

## Redis

Responsibilities:

- Caching
- Session Storage (if required)
- Queue Management
- Rate Limiting

---

## File Storage

Stores:

- Documents
- Product Images
- Reports
- Attachments
- Exports
- Import Files

---

# 7. Frontend Architecture

The frontend is responsible for delivering a fast, responsive, and intuitive user experience while remaining independent of backend implementation details.

## Responsibilities

- Authentication
- Routing
- Form Handling
- Dashboard Rendering
- API Communication
- State Management
- Theme Management
- Localization
- Error Handling

## Technology Stack

- React 19
- TypeScript
- Vite
- Tailwind CSS v4
- shadcn/ui
- TanStack Router
- TanStack Query
- Zustand
- React Hook Form
- Zod
- Framer Motion
- Lucide Icons

---

# Frontend Design Principles

The frontend should remain:

- Component Driven
- Responsive
- Accessible
- Keyboard Friendly
- Lazy Loaded
- Modular
- Testable

Business logic should never live inside UI components.

---

# 8. Backend Architecture

The backend provides all business capabilities through REST APIs.

## Responsibilities

- Authentication
- Authorization
- Business Logic
- Validation
- Reporting
- Background Processing
- Notifications
- File Management
- Audit Logging

Technology

- NestJS
- Prisma ORM
- PostgreSQL
- Redis
- BullMQ

---

# Backend Module Structure

Each module should contain:

```
module/

controller/

service/

repository/

dto/

entities/

validators/

events/

guards/

interceptors/

tests/
```

Every module should remain independent whenever possible.

---

# Dependency Direction

```
Controller

↓

Service

↓

Repository

↓

Database
```

Controllers must never access the database directly.

---

# 9. Authentication Architecture

Authentication is centralized.

Supported authentication methods:

- Email & Password
- Username & Password
- Refresh Tokens

Future support:

- Google OAuth
- Microsoft OAuth
- GitHub OAuth
- LDAP
- SAML
- Two-Factor Authentication

---

# Authentication Flow

```
Login

↓

Credential Validation

↓

JWT Access Token

↓

Refresh Token

↓

Authenticated Requests

↓

Token Refresh

↓

Logout
```

---

# Authorization

Nebula ERP uses:

- Role-Based Access Control (RBAC)
- Permission-Based Authorization

Example

```
Super Admin

↓

Admin

↓

Manager

↓

Staff

↓

Viewer
```

Permissions are evaluated before every protected operation.

---

# 10. Database Architecture

PostgreSQL is the primary relational database.

The database is designed using:

- Normalized Tables
- Foreign Keys
- Transactions
- Constraints
- Indexes
- UUID Primary Keys

Prisma ORM acts as the abstraction layer.

---

# Database Design Principles

- No duplicated data
- Soft Deletes where appropriate
- Immutable Audit Logs
- Explicit Relationships
- Transaction Safety
- Predictable Naming

Every table must be documented inside the Database Specification.

---

# 11. API Architecture

All APIs follow REST principles.

Example

```
GET

POST

PUT

PATCH

DELETE
```

Standard response format

```json
{
  "success": true,
  "message": "",
  "data": {},
  "meta": {}
}
```

Standard error format

```json
{
  "success": false,
  "message": "",
  "errors": []
}
```

---

# API Versioning

Initial version:

```
/api/v1/
```

Future versions:

```
/api/v2/

/api/v3/
```

Breaking changes require a new API version.

---

# Validation

Every API request must validate:

- Authentication
- Permissions
- Required Fields
- Business Rules
- Data Types
- Relationships

Invalid requests should never reach business logic.

---

# 12. Event Architecture

Business modules communicate using domain events whenever practical.

Examples

```
Purchase Approved

↓

Inventory Updated

↓

Supplier Balance Updated

↓

Accounting Entry Created

↓

Audit Log Recorded

↓

Notification Sent
```

Events reduce coupling and improve scalability.

---

# Event Principles

Events should be:

- Immutable
- Descriptive
- Traceable
- Idempotent

Every event must be logged.

---

# 13. Caching Strategy

Redis is used to improve application performance and reduce unnecessary database queries.

## Cached Data

- Application Settings
- User Permissions
- Frequently Accessed Reference Data
- Dashboard Statistics
- Session Data (if required)

## Cache Principles

- Cache only when beneficial.
- Never cache sensitive information.
- Always invalidate stale data after updates.
- Prefer short-lived cache entries unless explicitly required.

---

# 14. Background Job Processing

Time-consuming operations must execute asynchronously.

Examples include:

- Email Sending
- Report Generation
- Data Import
- Data Export
- Notification Delivery
- Scheduled Tasks
- Inventory Recalculation

Technology:

- BullMQ
- Redis

---

# 15. Logging & Monitoring

Every significant system action must be logged.

## Application Logs

- Startup
- Shutdown
- Errors
- Warnings
- Background Jobs

## Audit Logs

Business-critical actions include:

- User Login
- User Logout
- Password Change
- Product Created
- Product Updated
- Product Deleted
- Purchase Approved
- Sales Completed
- Inventory Adjustments
- Permission Changes

Audit logs must be immutable and searchable.

---

# 16. File Storage Architecture

The system supports secure storage for business documents and media.

## Supported Files

- Product Images
- Company Logos
- Purchase Documents
- Sales Invoices
- Warranty Files
- Repair Attachments
- Reports
- Import Files
- Export Files

Storage providers may include:

- Local Storage
- Amazon S3
- S3-Compatible Object Storage
- Azure Blob Storage (future)

---

# 17. Deployment Architecture

Nebula ERP supports multiple deployment models.

## Supported Deployments

- Local Development
- Self-Hosted Server
- Docker
- Virtual Machine
- Cloud VPS
- Kubernetes (future)

Deployment environments:

- Development
- Testing
- Staging
- Production

Each environment should maintain separate configuration and secrets.

---

# 18. Scalability Strategy

The architecture is designed to scale both vertically and horizontally.

Scalability goals:

- Support multiple organizations
- Support multiple branches
- Handle increasing transaction volume
- Allow independent module growth
- Minimize performance degradation

Future improvements may include:

- Read Replicas
- Distributed Caching
- CDN Integration
- Microservice Extraction (where justified)

---

# 19. Security Architecture

Security is a foundational requirement.

## Authentication

- JWT Access Tokens
- Refresh Tokens
- Secure Password Hashing

## Authorization

- Role-Based Access Control (RBAC)
- Permission-Based Authorization

## Data Protection

- HTTPS
- Input Validation
- Output Encoding
- Secure Headers
- SQL Injection Prevention
- XSS Protection
- CSRF Protection (where applicable)
- Rate Limiting

Sensitive configuration must never be stored in source code.

---

# 20. Disaster Recovery

Business continuity is essential.

The platform should support:

- Automated Database Backups
- Point-in-Time Recovery (where supported)
- Backup Verification
- Restore Procedures
- Configuration Backups
- File Storage Backups

Recovery procedures should be documented separately.

---

# 21. Technology Boundaries

To maintain consistency, Nebula ERP standardizes its core technologies.

## Frontend

- React
- TypeScript
- Vite
- Tailwind CSS
- shadcn/ui

## Backend

- NestJS
- Prisma ORM

## Database

- PostgreSQL

## Cache & Queue

- Redis
- BullMQ

Alternative technologies require an approved Architecture Decision Record (ADR).

---

# 22. Architecture Decision Summary

The architecture follows these guiding principles:

- Modular Architecture
- API First
- Cloud Native
- Self-Host Friendly
- Security by Default
- Performance First
- AI Ready
- Event Driven
- Scalable
- Maintainable

These principles govern all future development decisions.

---

# 23. AI Context Summary

## Summary

This document defines the overall architecture of Nebula ERP and serves as the primary technical reference for all implementation work.

## Related Documents

- DOC-001 Product Vision
- DOC-003 Tech Stack
- DOC-004 Design Principles

## Related Modules

- Authentication
- Organization
- Products
- Inventory
- Purchasing
- Sales
- POS
- Warehouse
- Accounting
- CRM
- Reporting

## Related Standards

- API Standards
- Database Standards
- UI Standards
- Coding Standards

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial architecture draft |