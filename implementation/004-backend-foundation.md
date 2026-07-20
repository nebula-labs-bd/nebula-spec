# Implementation Document 004 — Backend Foundation

| Field | Value |
|-------|-------|
| Document ID | IMP-004 |
| Name | Backend Foundation |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

This document defines the foundational architecture of the Nebula ERP backend using NestJS. It establishes application structure, dependency management, configuration, middleware, validation, logging, API standards, and testing practices that every backend module must follow.

---

# 2. Objectives

The backend foundation must:

- Be modular and maintainable
- Support domain-driven development
- Be fully type-safe
- Enforce consistent validation
- Provide centralized configuration
- Enable horizontal scaling
- Support multi-tenancy
- Integrate with AI services
- Be observable and testable
- Support future microservice extraction

---

# 3. Technology Stack

| Component | Technology |
|-----------|------------|
| Framework | NestJS |
| Language | TypeScript |
| ORM | Prisma |
| Validation | class-validator + class-transformer |
| Authentication | JWT |
| API Documentation | Swagger / OpenAPI |
| Queue | BullMQ |
| Cache | Redis |
| Configuration | @nestjs/config |
| Logging | Pino |

---

# 4. Project Structure

```text
apps/api/

src/

├── main.ts
├── app.module.ts
│
├── common/
│   ├── decorators/
│   ├── dto/
│   ├── exceptions/
│   ├── filters/
│   ├── guards/
│   ├── interceptors/
│   ├── middleware/
│   ├── pipes/
│   ├── utils/
│   └── constants/
│
├── config/
│
├── database/
│
├── modules/
│   ├── auth/
│   ├── users/
│   ├── organizations/
│   ├── inventory/
│   ├── sales/
│   ├── purchasing/
│   ├── finance/
│   ├── crm/
│   ├── ai/
│   └── system/
│
├── queue/
│
├── integrations/
│
└── shared/
```

Business modules must remain isolated and communicate through well-defined services.

---

# 5. Module Architecture

Each module follows a consistent structure.

Example:

```text
inventory/

├── controllers/
├── services/
├── repositories/
├── dto/
├── entities/
├── interfaces/
├── mappers/
├── validators/
├── events/
└── inventory.module.ts
```

Responsibilities:

- Controllers expose APIs.
- Services implement business logic.
- Repositories access the database.
- DTOs validate requests.
- Mappers transform data.
- Events publish domain changes.

---

# 6. Dependency Injection Strategy

NestJS Dependency Injection is the only supported mechanism for service composition.

Rules:

- Depend on interfaces where practical.
- Avoid direct instantiation using `new`.
- Register shared services as providers.
- Keep providers stateless unless state is intentional.
- Prefer constructor injection.

Dependency flow:

```text
Controller

↓

Service

↓

Repository

↓

Prisma
```

Circular dependencies should be eliminated through refactoring rather than relying on `forwardRef()` whenever possible.

---

# 7. Configuration Management

Application configuration is centralized.

Configuration sources:

```text
Environment Variables

↓

Config Module

↓

Application Services
```

Configuration categories:

- Application
- Database
- Redis
- Authentication
- Storage
- Email
- AI Providers
- Logging
- Feature Flags

All configuration should be validated during startup. The application must fail fast if required values are missing.

---

# 8. Global Middleware

Global middleware applies to all requests unless explicitly excluded.

Recommended middleware:

- Request ID generation
- Request logging
- CORS
- Helmet security headers
- Compression
- Rate limiting
- Tenant resolution
- Request timing

Execution order:

```text
Incoming Request

↓

Middleware

↓

Guards

↓

Interceptors

↓

Pipes

↓

Controller

↓

Service

↓

Response
```

Middleware should remain lightweight and avoid business logic.

---

# 9. Validation Pipeline

All incoming requests must be validated before reaching business logic.

Validation pipeline:

```text
HTTP Request

↓

DTO

↓

Validation Pipe

↓

Transformation

↓

Business Logic
```

Validation rules:

- Whitelist allowed properties.
- Reject unknown properties.
- Automatically transform types.
- Return consistent validation errors.
- Never trust client input.

Validation should be enabled globally.

---

# 10. Exception Handling

All exceptions should be processed by a global exception filter.

Error categories:

| Category | Example |
|----------|---------|
| Validation | Invalid request payload |
| Authentication | Invalid credentials |
| Authorization | Insufficient permissions |
| Business | Stock unavailable |
| Database | Constraint violation |
| External Service | AI provider unavailable |
| Internal | Unexpected server error |

Standard response:

```json
{
  "success": false,
  "statusCode": 400,
  "message": "Validation failed",
  "errors": [],
  "timestamp": "2026-07-20T12:00:00Z",
  "requestId": "req_123456"
}
```

Internal implementation details must never be exposed to API consumers.

---

# 11. Logging Foundation

Structured logging should be used throughout the application.

Required fields:

- Timestamp
- Request ID
- User ID (if authenticated)
- Organization ID
- Log Level
- Module
- Action
- Message

Log levels:

```text
TRACE

DEBUG

INFO

WARN

ERROR

FATAL
```

Logs should be structured for centralized aggregation and correlation across distributed services.

---

# 12. Authentication & Authorization Foundation

Authentication verifies identity.

Authorization determines access rights.

Authentication flow:

```text
Client

↓

JWT Access Token

↓

JWT Guard

↓

Current User

↓

Permission Guard

↓

Controller
```

Authorization layers:

- Authentication
- Organization Membership
- Role-Based Access Control (RBAC)
- Permission Checks
- Resource Ownership
- Business Rule Validation

Core guards:

| Guard | Purpose |
|--------|----------|
| JwtAuthGuard | Verify JWT |
| OrganizationGuard | Resolve tenant |
| RolesGuard | Role validation |
| PermissionsGuard | Permission validation |

Every protected endpoint must require authentication unless explicitly marked as public.

---

# 13. API Versioning

All public APIs must be versioned.

Recommended format:

```text
/api/v1/
```

Examples:

```text
GET /api/v1/users

POST /api/v1/products

GET /api/v1/inventory

POST /api/v1/auth/login
```

Versioning rules:

- Never introduce breaking changes without a new version.
- Maintain backward compatibility where practical.
- Deprecate endpoints before removal.
- Document all supported versions.

---

# 14. OpenAPI / Swagger

The backend should automatically generate API documentation.

Documentation includes:

- Endpoints
- DTOs
- Authentication requirements
- Request examples
- Response examples
- Error responses

Swagger endpoint:

```text
/api/docs
```

Documentation standards:

- Every endpoint must have a summary.
- DTO properties require descriptions.
- Authentication requirements must be declared.
- Response status codes must be documented.

Swagger documentation should always reflect the current implementation.

---

# 15. Interceptors

Interceptors provide cross-cutting functionality.

Recommended interceptors:

| Interceptor | Purpose |
|--------------|----------|
| LoggingInterceptor | Request logging |
| ResponseInterceptor | Standardize responses |
| TimeoutInterceptor | Request timeout |
| CacheInterceptor | Read caching |
| MetricsInterceptor | Performance metrics |

Execution flow:

```text
Request

↓

Interceptor

↓

Controller

↓

Service

↓

Interceptor

↓

Response
```

Business logic should not be implemented inside interceptors.

---

# 16. Caching Strategy

Caching reduces unnecessary database queries and improves response times.

Primary cache provider:

```text
Redis
```

Suitable cache targets:

- User permissions
- System configuration
- Reference data
- Dashboard summaries
- Frequently accessed reports

Rules:

- Apply sensible TTL values.
- Invalidate caches after relevant updates.
- Do not cache sensitive user-specific data unless properly isolated.
- Monitor cache hit rates.

Caching should improve performance without compromising data consistency.

---

# 17. Queue Integration

Background processing should use BullMQ with Redis.

Queue categories:

| Queue | Purpose |
|--------|----------|
| Email | Email delivery |
| Notifications | Push & SMS |
| Reports | Report generation |
| Imports | Bulk imports |
| Exports | Data exports |
| AI | AI processing |
| Maintenance | Scheduled jobs |

Workflow:

```text
API Request

↓

Queue

↓

Worker

↓

Background Processing

↓

Completion Event
```

Long-running operations should be processed asynchronously whenever possible.

---

# 18. Event Architecture

Modules should communicate using domain events instead of tight coupling.

Example:

```text
Sales Order Created

↓

Inventory Updated

↓

Invoice Generated

↓

Notification Sent

↓

Audit Recorded
```

Benefits:

- Loose coupling
- Better scalability
- Easier testing
- Independent module evolution

Events should be idempotent and carry only the information required by subscribers.

---

# 19. Testing Strategy

Testing pyramid:

```text
E2E Tests

↓

Integration Tests

↓

Unit Tests
```

Testing objectives:

| Test Type | Purpose |
|------------|----------|
| Unit | Business logic |
| Integration | Module interaction |
| API | Endpoint validation |
| E2E | Complete workflows |

Requirements:

- Mock external services.
- Test business rules.
- Validate authorization.
- Verify validation failures.
- Cover success and failure scenarios.

Testing should be automated through the CI pipeline.

---

# 20. Backend Coding Standards

General principles:

- One responsibility per class.
- Keep controllers thin.
- Place business logic in services.
- Repositories only access data.
- Avoid duplicated logic.
- Prefer composition over inheritance.
- Favor explicit code over hidden behavior.

Code quality requirements:

- Strict TypeScript mode enabled.
- No `any` type unless justified.
- No unused imports.
- Consistent naming conventions.
- Self-documenting code.
- Comprehensive inline documentation only where complexity warrants it.

All backend modules must follow these standards to ensure consistency across the platform.

---

# 21. Performance & Scalability

The backend should be designed for predictable performance under increasing load.

Performance principles:

- Stateless application services
- Horizontal scaling
- Connection pooling
- Efficient database queries
- Background job processing
- Response caching
- Lazy loading where appropriate
- Resource monitoring

Performance targets:

| Operation | Target |
|-----------|--------|
| Authentication | < 150 ms |
| Standard CRUD | < 200 ms |
| Dashboard API | < 500 ms |
| AI Request Submission | < 300 ms |
| File Upload Initialization | < 300 ms |

Heavy operations should always be delegated to background workers.

---

# 22. Backend Validation Checklist

The backend foundation is considered complete when the following are verified.

## Application

- NestJS application starts successfully.
- Configuration validation passes.
- Environment variables load correctly.

## API

- Versioned routes are operational.
- Swagger documentation is generated.
- Health endpoint responds successfully.

## Security

- JWT authentication functions correctly.
- Authorization guards enforce permissions.
- CORS is configured.
- Helmet security headers are enabled.
- Rate limiting is operational.

## Validation

- Global validation pipe is enabled.
- Invalid requests return standardized errors.
- Unknown properties are rejected.

## Logging

- Structured logs are generated.
- Request IDs are included.
- Errors are captured consistently.

## Infrastructure

- Prisma connects successfully.
- Redis connection is established.
- Queue processing is operational.

## Testing

- Unit tests execute successfully.
- Integration tests pass.
- API smoke tests complete successfully.

---

# 23. Acceptance Criteria

The Backend Foundation implementation is complete when:

- Project structure follows the approved architecture.
- Module organization is standardized.
- Dependency injection is consistently applied.
- Configuration management is centralized.
- Global middleware is configured.
- Validation pipeline is enabled.
- Global exception handling is implemented.
- Structured logging is operational.
- Authentication and authorization foundations are established.
- API versioning is implemented.
- Swagger documentation is generated.
- Queue and caching integrations are configured.
- Testing strategy is documented.
- Performance guidelines are established.
- Backend validation checklist passes.

---

# 24. AI Context Summary

## Summary

The Backend Foundation establishes the architectural standards for all backend development within Nebula ERP. It defines module organization, dependency injection, configuration management, middleware, validation, authentication, authorization, logging, caching, queues, API documentation, testing, and scalability practices.

## Dependencies

- IMP-001 — Monorepo Foundation
- IMP-002 — Infrastructure Bootstrap
- IMP-003 — Database Implementation
- DOC-003 — Backend Architecture
- DOC-007 — Security

## Referenced By

- Authentication & RBAC
- Inventory Module
- Sales Module
- Purchasing Module
- Finance Module
- CRM Module
- AI Platform
- Integration Services

---

# 25. Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial Backend Foundation implementation specification |