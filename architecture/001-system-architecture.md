---

# 12. Multi-Tenant Architecture

Nebula ERP follows a **Shared Database, Shared Schema** multi-tenant model.

Each business record belongs to exactly one organization through an immutable `organization_id`.

## Tenant Isolation

Every request must resolve the active organization before accessing business data.

Isolation is enforced at:

- Authentication
- Authorization
- Service Layer
- Repository Layer
- Database Queries
- Cache Keys
- Background Jobs
- File Storage
- API Access
- Audit Logs

No query shall access records without tenant filtering.

---

## Organization Context

Every authenticated request contains:

- User ID
- Organization ID
- Branch ID (optional)
- Role
- Permission Set
- Session ID
- Locale
- Time Zone

This context is injected into every service request.

---

# 13. Request Lifecycle

Every incoming request follows the same processing pipeline.

```
HTTP Request

↓

Reverse Proxy

↓

NestJS Application

↓

Authentication

↓

Organization Resolution

↓

Permission Validation

↓

Request Validation

↓

Business Service

↓

Repository

↓

Database

↓

Audit Log

↓

Response
```

Every successful or failed business operation should be eligible for auditing according to configured policies.

---

# 14. Background Processing

Long-running operations are executed asynchronously.

Examples include:

- Email Delivery
- SMS Delivery
- Notification Processing
- Report Generation
- File Preview Generation
- Thumbnail Generation
- OCR Processing
- Scheduled Backups
- Inventory Recalculation
- Integration Synchronization

Workers should be stateless and horizontally scalable.

---

# 15. Internal Event Bus

Modules communicate through domain events rather than direct coupling where asynchronous behavior is appropriate.

Example events:

- ProductCreated
- ProductUpdated
- StockAdjusted
- PurchaseApproved
- InvoicePaid
- CustomerCreated
- FileUploaded
- UserCreated

Consumers subscribe without creating circular dependencies.

Benefits include:

- Loose Coupling
- Better Scalability
- Easier Testing
- Future Microservice Compatibility

---

# 16. Caching Strategy

Caching improves performance while maintaining consistency.

Recommended cache targets:

- User Permissions
- Organization Settings
- Product Categories
- Tax Configuration
- Dashboard Widgets
- Frequently Accessed Reports
- API Tokens
- Feature Flags

Rules:

- Cache keys must include `organization_id`.
- Sensitive data should never be cached in shared contexts.
- Cache invalidation occurs after relevant updates.

---

# 17. Storage Architecture

Nebula ERP separates storage responsibilities.

## Database

Stores:

- Business Data
- Configuration
- Metadata
- Audit Records

---

## Object Storage

Stores:

- Uploaded Documents
- Product Images
- Reports
- Attachments
- Backups
- Generated Files

---

## Cache

Stores:

- Temporary Data
- Session Data (if configured)
- Frequently Requested Objects

---

## Search Index (Future)

Stores optimized indexes for:

- Global Search
- Full-Text Search
- AI Retrieval
- Document Search

---

# 18. Configuration Management

Configuration is divided into four levels.

## System Configuration

Applies globally.

Examples:

- SMTP
- Redis
- Storage Provider
- Logging

---

## Organization Configuration

Examples:

- Currency
- Fiscal Year
- Invoice Settings
- Taxes
- Branding

---

## User Preferences

Examples:

- Theme
- Language
- Dashboard Layout
- Notification Preferences

---

## Runtime Configuration

Examples:

- Feature Flags
- Temporary Overrides
- Maintenance Mode

Configuration changes should be audited.

---

# 19. Performance Principles

The architecture follows these principles:

- Minimize database round trips.
- Prefer indexed queries.
- Avoid N+1 query patterns.
- Paginate large datasets.
- Stream large exports.
- Compress responses where appropriate.
- Execute heavy tasks asynchronously.
- Use caching for frequently accessed data.
- Optimize file delivery through object storage.

Performance targets should be measured continuously using monitoring tools.

---

## ✅ End of Part 2

**Don't commit yet.**

**Part 3** will include:

- Technology Stack
- Development Standards
- Deployment Topology
- Scalability Strategy
- Disaster Recovery
- API Summary
- Acceptance Criteria
- AI Context Summary
- Revision History
- Git Commit

---

# 20. Technology Stack

Nebula ERP is built using a modern TypeScript-first architecture.

| Layer | Technology |
|--------|------------|
| Frontend | React 19 + Vite + TypeScript |
| UI Framework | Tailwind CSS v4 |
| Component Library | shadcn/ui |
| Icons | Lucide React |
| Charts | Recharts |
| State Management | TanStack Query + Zustand |
| Forms | React Hook Form + Zod |
| Backend | NestJS |
| Language | TypeScript |
| ORM | Prisma ORM |
| Database | PostgreSQL |
| Cache | Redis |
| Queue | BullMQ |
| Object Storage | S3-Compatible Storage |
| Authentication | JWT + Refresh Tokens |
| API | REST (GraphQL optional future) |
| Monitoring | OpenTelemetry + Prometheus + Grafana |
| Logging | Pino |
| Containerization | Docker |
| Reverse Proxy | Nginx |
| CI/CD | GitHub Actions |

### Design Principles

- Type-safe end-to-end
- API-first development
- Modular architecture
- Dependency Injection
- Domain-driven module boundaries
- Testability by design

---

# 21. Deployment Topology

## Development

```
Developer

↓

React Dev Server

↓

NestJS

↓

PostgreSQL

↓

Redis

↓

Local Object Storage
```

---

## Production

```
Internet

↓

Load Balancer

↓

Nginx

↓

NestJS Application

↓

Redis

↓

PostgreSQL Primary

↓

Read Replicas (Future)

↓

Object Storage

↓

Monitoring Stack
```

Production deployments should support horizontal scaling for stateless services.

---

# 22. Scalability Strategy

Nebula ERP is designed to scale incrementally.

### Vertical Scaling

Suitable for:

- Small businesses
- Pilot deployments
- Single-server installations

Increase:

- CPU
- Memory
- Storage
- Network throughput

---

### Horizontal Scaling

Suitable for:

- Multi-branch organizations
- High transaction volumes
- SaaS deployments

Scale:

- API servers
- Background workers
- Redis
- Object storage gateways
- Read replicas

Future versions may adopt microservices where operational complexity is justified.

---

# 23. Disaster Recovery

Recovery objectives should be defined per deployment.

Recommended practices:

- Automated database backups
- Point-in-time recovery (PITR)
- Encrypted off-site backup storage
- Daily backup verification
- Object storage versioning
- Infrastructure-as-Code
- Periodic disaster recovery drills

Suggested targets:

| Metric | Target |
|---------|--------|
| RPO (Recovery Point Objective) | ≤ 15 minutes |
| RTO (Recovery Time Objective) | ≤ 2 hours |

Organizations may define stricter requirements.

---

# 24. Development Standards

Engineering standards include:

- Strict TypeScript mode
- ESLint enforcement
- Prettier formatting
- Conventional Commits
- Pull Request reviews
- Automated testing
- API documentation
- Code ownership
- Security scanning
- Dependency vulnerability checks

Code should favor readability, maintainability, and explicitness over clever implementations.

---

# 25. Architecture Decision Records (ADR)

Major technical decisions should be documented as Architecture Decision Records.

Each ADR should include:

- Title
- Status
- Context
- Decision
- Alternatives Considered
- Consequences
- Date
- Author

Example ADRs:

- ADR-001: Shared Database Multi-Tenancy
- ADR-002: NestJS as Backend Framework
- ADR-003: PostgreSQL as Primary Database
- ADR-004: Prisma ORM Selection
- ADR-005: Redis for Distributed Caching

---

# 26. Acceptance Criteria

The system architecture is considered complete when:

- All modules follow consistent architectural patterns.
- Tenant isolation is enforced across the platform.
- Authentication and authorization are centralized.
- Background processing is asynchronous where appropriate.
- Event-driven communication is supported.
- Storage responsibilities are clearly separated.
- Configuration hierarchy is defined.
- Performance principles are documented.
- Deployment architecture supports scaling.
- Disaster recovery guidance is established.

---

# 27. AI Context Summary

## Summary

The Nebula ERP System Architecture establishes the foundational engineering principles for the platform. It defines a modular, TypeScript-first, multi-tenant architecture with shared database isolation, asynchronous processing, event-driven communication, scalable deployment strategies, and standardized technology choices to support long-term maintainability and growth.

## Dependencies

- Business Specification (Modules 001–024)

## Referenced By

- Database Architecture
- Backend Architecture
- Frontend Architecture
- Design System
- Infrastructure
- Security
- AI Architecture
- Development Standards
- Implementation Roadmap

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial System Architecture specification |