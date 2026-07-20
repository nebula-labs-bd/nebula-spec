# Document 003 — Backend Architecture

| Field | Value |
|-------|-------|
| Document ID | DOC-003 |
| Name | Backend Architecture |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

This document defines the backend architecture of Nebula ERP.

It establishes engineering standards for project structure, module organization, dependency injection, validation, business logic, persistence, authentication, observability, testing, and scalability.

The objective is to ensure that every backend component follows a consistent architecture that is maintainable, testable, secure, and scalable.

---

# 2. Objectives

The backend architecture must:

- Follow modular design
- Enforce separation of concerns
- Support multi-tenancy
- Ensure high maintainability
- Promote reusable business logic
- Support asynchronous processing
- Enable comprehensive testing
- Provide observability
- Scale horizontally
- Remain framework-consistent

---

# 3. Technology Stack

| Layer | Technology |
|--------|------------|
| Framework | NestJS |
| Language | TypeScript |
| ORM | Prisma ORM |
| Validation | Zod |
| Authentication | JWT + Refresh Tokens |
| Authorization | RBAC + Permission Guards |
| Queue | BullMQ |
| Cache | Redis |
| Logging | Pino |
| API Documentation | OpenAPI (Swagger) |
| Testing | Vitest + Supertest |
| Package Manager | pnpm |

Future support may include gRPC and GraphQL gateways where appropriate.

---

# 4. Architectural Principles

The backend follows these principles:

- Domain-driven module organization
- Dependency Injection
- SOLID principles
- Single Responsibility Principle
- Explicit dependencies
- Stateless request handling
- API-first design
- Testability by default
- Security by default

Business logic must never be duplicated across modules.

---

# 5. Project Structure

Recommended directory structure:

```text
src/

├── app/
├── common/
├── config/
├── database/
├── modules/
├── infrastructure/
├── integrations/
├── jobs/
├── shared/
├── types/
├── utils/
├── main.ts
```

Each directory has a clearly defined responsibility.

---

# 6. Module Structure

Each business module should follow a consistent layout.

Example:

```text
modules/products/

├── controllers/
├── services/
├── repositories/
├── dto/
├── entities/
├── validators/
├── mappers/
├── events/
├── guards/
├── policies/
├── interfaces/
├── constants/
├── module.ts
```

Every module should remain self-contained and expose only its public interfaces.

---

# 7. Layered Architecture

Each request flows through clearly defined layers.

```
Controller

↓

Validation

↓

Service

↓

Repository

↓

Prisma ORM

↓

PostgreSQL
```

Responsibilities:

### Controller

- Receive HTTP requests
- Validate request shape
- Delegate work
- Return responses

Controllers should remain thin.

---

### Service

Responsible for:

- Business rules
- Domain logic
- Permission enforcement
- Event publishing
- Transaction orchestration

---

### Repository

Responsible for:

- Database interaction
- Query optimization
- Persistence
- Data mapping

Repositories should contain no business logic.

---

# 8. Dependency Injection

All services should use NestJS dependency injection.

Allowed dependencies:

- Repository
- Logger
- Cache
- Event Publisher
- Queue
- Configuration

Avoid circular dependencies.

If unavoidable, redesign module boundaries before using framework workarounds.

---

# 9. Configuration Management

Configuration should be centralized.

Configuration sources:

- Environment Variables
- Secret Manager
- Database Configuration
- Feature Flags

Configuration categories:

- Application
- Database
- Redis
- Queue
- Authentication
- Storage
- Integrations
- Monitoring

Configuration values should never be hardcoded.

---

# 10. DTO Standards

Every API request uses explicit DTOs.

Rules:

- Immutable DTOs
- Strict validation
- No business logic
- No persistence logic
- Clear documentation

Separate DTOs for:

- Create
- Update
- Response
- Search
- Filter
- Bulk Operations

DTOs should never expose internal database implementation details.

---

# 11. Validation Standards

All external input must be validated.

Validation includes:

- Required fields
- Data types
- Length constraints
- Numeric ranges
- Enum validation
- UUID validation
- Date validation
- Business-specific validation

Validation occurs before business logic execution.

Never trust client-provided data.

---

# 12. Repository Pattern

Repositories are responsible only for data persistence.

Responsibilities include:

- CRUD operations
- Query construction
- Pagination
- Filtering
- Sorting
- Database transactions
- Optimized data retrieval

Repositories shall **not** contain:

- Business rules
- Permission checks
- Validation logic
- HTTP concerns

Each repository should expose clear interfaces.

Example:

```typescript
interface ProductRepository {
  create(data: CreateProductInput): Promise<Product>;
  update(id: UUID, data: UpdateProductInput): Promise<Product>;
  delete(id: UUID): Promise<void>;
  findById(id: UUID): Promise<Product | null>;
  search(filters: ProductFilters): Promise<PaginatedResult<Product>>;
}
```

---

# 13. Exception Handling

All application errors shall use standardized exception types.

Categories include:

| Category | HTTP Status |
|----------|-------------|
| Validation Error | 400 |
| Authentication Error | 401 |
| Authorization Error | 403 |
| Resource Not Found | 404 |
| Conflict | 409 |
| Business Rule Violation | 422 |
| Rate Limited | 429 |
| Internal Server Error | 500 |

Error response format:

```json
{
  "success": false,
  "error": {
    "code": "PRODUCT_NOT_FOUND",
    "message": "Requested product does not exist.",
    "details": {}
  },
  "timestamp": "2026-07-20T12:30:45Z",
  "requestId": "req_123456789"
}
```

Internal exception details must never be exposed to clients.

---

# 14. Middleware

Middleware executes before route handlers.

Common middleware includes:

- Request ID generation
- Request logging
- Correlation ID
- Tenant resolution
- Localization
- Security headers
- Request timing
- Compression

Middleware should remain lightweight and framework-agnostic where practical.

---

# 15. Guards

Guards determine whether a request may proceed.

Examples:

- JWT Authentication Guard
- Refresh Token Guard
- Role Guard
- Permission Guard
- Organization Guard
- Branch Access Guard
- API Key Guard

Guard evaluation order:

```
Authentication

↓

Organization

↓

Role

↓

Permission

↓

Route Access
```

Unauthorized requests shall terminate immediately.

---

# 16. Pipes

Pipes transform and validate request data.

Typical uses:

- UUID parsing
- Integer conversion
- Date parsing
- Enum validation
- Pagination normalization
- Search parameter validation

Pipes should avoid business logic.

---

# 17. Interceptors

Interceptors wrap request execution.

Common responsibilities:

- Response transformation
- Execution timing
- Logging
- Cache handling
- Serialization
- Audit event publishing

Response format:

```json
{
  "success": true,
  "data": {},
  "meta": {},
  "timestamp": "2026-07-20T12:30:45Z"
}
```

Interceptors provide a consistent API response structure.

---

# 18. Authentication & Authorization Flow

Authentication flow:

```
Login Request

↓

Credential Validation

↓

JWT Issued

↓

Refresh Token Stored

↓

Authenticated Request

↓

JWT Verification

↓

Organization Resolution

↓

Permission Evaluation

↓

Controller
```

Authorization uses Role-Based Access Control (RBAC) with fine-grained permissions.

Every request evaluates:

- User Status
- Organization Membership
- Branch Access
- Assigned Roles
- Effective Permissions

---

# 19. Background Jobs

Asynchronous tasks execute through BullMQ workers.

Examples include:

- Email Sending
- SMS Sending
- File Processing
- OCR Jobs
- Report Generation
- Inventory Recalculation
- Integration Synchronization
- Scheduled Maintenance
- Notification Delivery

Job lifecycle:

```
Queued

↓

Processing

↓

Completed

↓

Archived
```

Failed jobs should support configurable retries with exponential backoff.

---

# 20. Event Handling

Business modules communicate through domain events.

Examples:

- ProductCreated
- ProductUpdated
- PurchaseApproved
- InvoicePaid
- PaymentRecorded
- CustomerCreated
- UserInvited
- FileUploaded

Event processing principles:

- Events represent completed business actions.
- Consumers should be independent.
- Event handlers should be idempotent.
- Failed handlers should retry where appropriate.
- Long-running processing should remain asynchronous.

---

## ✅ End of Part 2

**Don't commit yet.**

**Part 3** will include:

- Logging & Observability
- API Standards
- Testing Strategy
- Performance Guidelines
- Scalability
- Acceptance Criteria
- AI Context Summary
- Revision History
- Git Commit
- Progress Tracker

---

# 21. Logging & Observability

Nebula ERP shall provide structured, centralized logging for all backend services.

Logging objectives:

- Trace requests
- Diagnose failures
- Monitor performance
- Support auditing
- Correlate distributed events
- Enable operational analytics

Every log entry should include:

- Timestamp
- Log Level
- Request ID
- Organization ID (if applicable)
- User ID (if authenticated)
- Module
- Service
- Operation
- Execution Time
- Result

Supported log levels:

- TRACE
- DEBUG
- INFO
- WARN
- ERROR
- FATAL

Sensitive information must never be written to logs.

Examples include:

- Passwords
- Tokens
- Secrets
- Encryption Keys
- Payment Credentials

---

# 22. API Standards

All APIs shall follow a consistent contract.

## REST Principles

- Resource-oriented endpoints
- Stateless requests
- Standard HTTP methods
- Standard HTTP status codes
- JSON request/response bodies
- UTC timestamps using ISO 8601
- UUID identifiers

Example endpoint:

```text
GET /api/v1/products/{id}
```

---

## Response Format

Successful response:

```json
{
  "success": true,
  "data": {},
  "meta": {
    "page": 1,
    "pageSize": 25,
    "total": 120
  },
  "timestamp": "2026-07-20T12:30:45Z"
}
```

Error response:

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request.",
    "details": {}
  },
  "timestamp": "2026-07-20T12:30:45Z",
  "requestId": "req_abc123"
}
```

---

## Pagination

Supported query parameters:

```
?page=1
&pageSize=25
&sort=name
&order=asc
```

Maximum page size should be configurable.

---

# 23. Testing Strategy

Testing is mandatory for all production code.

## Unit Tests

Cover:

- Services
- Validators
- Utilities
- Domain Logic

Target:

- Fast execution
- High isolation
- Deterministic results

---

## Integration Tests

Cover:

- Repositories
- Database interactions
- External services (mocked where appropriate)
- Transactions

---

## End-to-End Tests

Validate:

- Authentication
- Authorization
- CRUD workflows
- Multi-tenant isolation
- API contracts
- Error handling

---

## Performance Tests

Measure:

- Request latency
- Throughput
- Database performance
- Queue processing
- Concurrent users

---

# 24. Performance Guidelines

Backend services should adhere to these principles:

- Avoid N+1 database queries.
- Use pagination for large datasets.
- Load only required fields.
- Cache frequently accessed data.
- Execute long-running tasks asynchronously.
- Optimize indexes based on query patterns.
- Reuse database connections.
- Minimize serialization overhead.

Recommended response targets:

| Operation | Target |
|-----------|--------|
| Read Request | < 100 ms |
| Write Request | < 200 ms |
| Authentication | < 150 ms |
| Background Job Dispatch | < 50 ms |

Large exports should execute asynchronously.

---

# 25. Scalability

The backend is designed for horizontal scaling.

Stateless application servers allow multiple instances behind a load balancer.

Scalable components include:

- API Servers
- Background Workers
- Redis
- Object Storage
- Read Replicas (Future)

Scalability principles:

- Stateless services
- Externalized session storage where required
- Distributed queues
- Configurable worker concurrency
- Event-driven processing

Future deployments may split modules into microservices if operational requirements justify the added complexity.

---

# 26. Acceptance Criteria

The backend architecture is complete when:

- Modules follow a consistent structure.
- Dependency injection is used throughout.
- Validation occurs before business logic.
- Repositories contain only persistence logic.
- Authentication and authorization are centralized.
- Background jobs are asynchronous.
- Domain events support loose coupling.
- Logging is structured and traceable.
- APIs follow documented standards.
- Testing strategy covers unit, integration, and end-to-end testing.
- Backend services support horizontal scaling.

---

# 27. AI Context Summary

## Summary

The Backend Architecture defines how Nebula ERP is implemented using NestJS and TypeScript. It standardizes project organization, layered architecture, dependency injection, validation, repositories, authentication, authorization, event handling, background processing, logging, testing, and scalability to ensure a maintainable and enterprise-ready backend.

## Dependencies

- DOC-001 — System Architecture
- DOC-002 — Database Architecture
- Business Specification (Modules 001–024)

## Referenced By

- Frontend Architecture
- Infrastructure
- Security
- AI Architecture
- Development Standards

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial Backend Architecture specification |