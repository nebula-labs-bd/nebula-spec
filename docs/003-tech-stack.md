# Nebula ERP Technology Stack

| Field | Value |
|-------|-------|
| Document ID | DOC-003 |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

This document defines the official technology stack for Nebula ERP. It establishes the approved technologies, their responsibilities, selection rationale, versioning policy, and architectural boundaries.

Only technologies documented in this specification should be used in production unless an Architecture Decision Record (ADR) explicitly approves an alternative.

---

# 2. Technology Philosophy

Nebula ERP adopts technologies that satisfy the following principles:

- Long-term support
- Strong community adoption
- Excellent documentation
- Enterprise readiness
- Type safety
- Performance
- Scalability
- Developer productivity
- Active maintenance

Technology selection prioritizes stability over trends.

---

# 3. Core Technology Stack

| Layer | Technology |
|--------|------------|
| Frontend | React 19 |
| Language | TypeScript |
| Build Tool | Vite |
| Styling | Tailwind CSS v4 |
| UI Components | shadcn/ui |
| Icons | Lucide React |
| Animation | Framer Motion |
| Routing | TanStack Router |
| Data Fetching | TanStack Query |
| State Management | Zustand |
| Forms | React Hook Form |
| Validation | Zod |
| Backend | NestJS |
| ORM | Prisma |
| Database | PostgreSQL |
| Cache | Redis |
| Queue | BullMQ |
| Authentication | JWT + Refresh Tokens |
| Package Manager | pnpm |
| Monorepo | Turborepo |
| Containerization | Docker |
| Version Control | Git + GitHub |

---

# 4. Frontend Stack

## React

Purpose

- Component-based UI
- Large ecosystem
- Excellent performance
- Strong TypeScript support

Reasons for Selection

- Industry standard
- Massive community
- Mature ecosystem
- Long-term stability

---

## TypeScript

Purpose

- Static typing
- Better maintainability
- Improved developer experience
- Safer refactoring

Required

All production frontend and backend code must use TypeScript.

---

## Vite

Purpose

- Fast development server
- Fast builds
- Modern tooling
- Excellent React support

Reasons

- Superior developer experience
- Faster than traditional bundlers
- Simple configuration

---

## Tailwind CSS

Purpose

Utility-first styling framework.

Reasons

- Consistent design system
- Rapid UI development
- Small production bundle
- Easy customization

---

## shadcn/ui

Purpose

Reusable UI components.

Reasons

- Accessible by default
- Modern design
- Fully customizable
- No runtime dependency

---

## Lucide React

Purpose

Application icon system.

Reasons

- Lightweight
- Consistent
- Tree-shakeable
- Open source

---

## Framer Motion

Purpose

Animations and transitions.

Used For

- Page transitions
- Dialog animations
- Loading states
- Dashboard interactions

Animations should enhance usability rather than distract from it.

---

# 5. Frontend Libraries

## TanStack Router

Purpose

Type-safe routing.

Benefits

- Route generation
- Nested layouts
- Data loading
- Type safety

---

## TanStack Query

Purpose

Server state management.

Responsibilities

- API caching
- Background refetching
- Optimistic updates
- Error handling

---

## Zustand

Purpose

Client-side state.

Examples

- Sidebar state
- Theme
- Current organization
- UI preferences

Business data should not be stored here unless appropriate.

---

## React Hook Form

Purpose

Form management.

Benefits

- Excellent performance
- Minimal re-renders
- Strong TypeScript support

---

## Zod

Purpose

Schema validation.

Responsibilities

- Form validation
- API validation
- Shared validation schemas

Validation logic should be reusable across the application.

---

# 6. Frontend Standards

The frontend must be:

- Responsive
- Accessible
- Component-based
- Lazy loaded
- Keyboard friendly
- Type-safe
- Performance optimized

Business logic belongs in services or hooks, not presentation components.

---

# 7. Backend Stack

## NestJS

### Purpose

NestJS is the official backend framework for Nebula ERP.

### Responsibilities

- REST APIs
- Authentication
- Authorization
- Business Logic
- Module Management
- Validation
- Background Job Coordination
- Event Handling

### Why NestJS

- Enterprise architecture
- Dependency Injection
- Modular structure
- TypeScript first
- Excellent testing support
- Long-term maintainability

---

## Prisma ORM

### Purpose

Prisma is the official Object Relational Mapper (ORM).

### Responsibilities

- Database schema management
- Type-safe database access
- Migrations
- Query generation

### Why Prisma

- Type safety
- Excellent developer experience
- Migration support
- Strong PostgreSQL integration

---

# 8. Database

## PostgreSQL

### Purpose

Primary relational database for Nebula ERP.

### Responsibilities

- Business data
- Financial records
- Inventory
- Sales
- Purchasing
- CRM
- Audit logs

### Why PostgreSQL

- Enterprise-grade
- ACID compliance
- High reliability
- Excellent indexing
- JSON support
- Large ecosystem

---

# 9. Cache & Queue

## Redis

### Purpose

High-speed in-memory data store.

### Responsibilities

- API cache
- Session storage (if required)
- Rate limiting
- Queue backend
- Temporary application data

---

## BullMQ

### Purpose

Background job processing.

### Responsibilities

- Email sending
- Report generation
- Data imports
- Data exports
- Scheduled jobs
- Notifications

Long-running tasks must never block API requests.

---

# 10. Authentication

Nebula ERP uses:

- JWT Access Tokens
- Refresh Tokens
- Password Hashing (Argon2)
- Role-Based Access Control (RBAC)
- Permission-Based Authorization

Future support:

- Google OAuth
- Microsoft OAuth
- GitHub OAuth
- LDAP
- SAML
- Multi-Factor Authentication (MFA)

---

# 11. Infrastructure

## Docker

Purpose

Containerized deployments for development and production.

Benefits

- Consistent environments
- Simplified deployment
- Easier scaling
- Environment isolation

---

## Git & GitHub

Purpose

Version control and collaboration.

Standards

- Feature branches
- Pull Requests
- Code Reviews
- Protected main branch
- Semantic commit messages

---

# 12. Development Tools

The recommended development environment includes:

| Tool | Purpose |
|------|---------|
| VS Code | Code Editor |
| Git | Version Control |
| GitHub | Repository Hosting |
| Docker Desktop | Container Runtime |
| PostgreSQL | Database |
| DBeaver | Database Administration |
| Bruno | API Testing |
| Node.js LTS | JavaScript Runtime |
| pnpm | Package Management |

---

# 13. Version Policy

Nebula ERP follows these versioning policies.

## Node.js

Use the current Active LTS release.

## React

Upgrade after stable releases have proven compatibility.

## NestJS

Remain within the latest supported major version unless compatibility concerns exist.

## PostgreSQL

Support the current major LTS version and the previous major version during transition periods.

---

# 14. Dependency Management

Dependencies must follow these principles:

- Prefer stable releases.
- Avoid unnecessary libraries.
- Remove unused dependencies promptly.
- Evaluate security advisories before upgrades.
- Lock dependency versions for reproducible builds.

---

# 15. Upgrade Policy

Technology upgrades should be:

- Planned
- Tested
- Documented
- Backward compatible where practical

Major upgrades require:

- Architecture review
- Regression testing
- Documentation updates

---

# 16. Approved Technology List

Only the following core technologies are approved for production use unless superseded by an approved ADR.

| Category | Approved Technology |
|----------|---------------------|
| Frontend | React |
| Language | TypeScript |
| Styling | Tailwind CSS |
| UI | shadcn/ui |
| Backend | NestJS |
| ORM | Prisma |
| Database | PostgreSQL |
| Cache | Redis |
| Queue | BullMQ |
| Forms | React Hook Form |
| Validation | Zod |
| State | Zustand |
| Data Fetching | TanStack Query |
| Routing | TanStack Router |
| Icons | Lucide |
| Animation | Framer Motion |
| Package Manager | pnpm |
| Monorepo | Turborepo |
| Containers | Docker |

---

# 17. Technology Decision Summary

Nebula ERP selects technologies based on:

- Stability
- Maintainability
- Scalability
- Performance
- Security
- Type Safety
- Community Support
- Developer Experience

Technology choices are intended to minimize technical debt while enabling long-term product growth.

---

# 18. AI Context Summary

## Summary

This document defines the official technology stack for Nebula ERP and serves as the authoritative reference for all implementation work.

## Related Documents

- DOC-001 Product Vision
- DOC-002 System Architecture
- DOC-004 Design Principles

## Related Standards

- Coding Standards
- Database Standards
- API Standards
- UI Standards

## Notes

Any deviation from this technology stack requires an approved Architecture Decision Record (ADR).

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial technology stack specification |