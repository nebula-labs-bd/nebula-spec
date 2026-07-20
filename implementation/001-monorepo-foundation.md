# Implementation Document 001 — Monorepo Foundation

| Field | Value |
|-------|-------|
| Document ID | IMP-001 |
| Name | Monorepo Foundation |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

This document defines the engineering foundation for the Nebula ERP codebase.

It establishes the repository structure, workspace management, package organization, shared tooling, coding environment, and development workflow.

Everything implemented afterwards will inherit these standards.

---

# 2. Objectives

The repository must:

- Support long-term scalability
- Minimize duplicated code
- Share types across applications
- Share UI components
- Share configurations
- Simplify dependency management
- Enable independent deployments
- Support local development
- Support CI/CD
- Remain IDE friendly

---

# 3. Technology Stack

## Runtime

- Node.js 22 LTS

## Package Manager

- pnpm

## Monorepo

- Turborepo

## Language

- TypeScript

## Frontend

- React
- Vite
- Tailwind CSS
- shadcn/ui

## Backend

- NestJS

## Database

- PostgreSQL

## Cache / Queue

- Redis

## ORM

- Prisma

## Testing

- Vitest
- Playwright

---

# 4. Repository Structure

```text
nebula-erp/

├── apps/
│
│   ├── web/
│   ├── api/
│   └── worker/
│
├── packages/
│
│   ├── ui/
│   ├── types/
│   ├── utils/
│   ├── config/
│   ├── eslint-config/
│   ├── tsconfig/
│   └── api-client/
│
├── infrastructure/
│
│   ├── docker/
│   ├── nginx/
│   ├── monitoring/
│   └── scripts/
│
├── docs/
│
│   ├── business/
│   ├── architecture/
│   ├── implementation/
│   └── api/
│
├── .github/
│
├── turbo.json
├── package.json
├── pnpm-workspace.yaml
├── pnpm-lock.yaml
├── README.md
└── .gitignore
```

---

# 5. Applications

## apps/web

Responsibilities:

- React frontend
- Dashboard
- Authentication
- ERP Modules
- AI Interface

---

## apps/api

Responsibilities:

- REST API
- Authentication
- Business Logic
- AI Gateway
- RBAC
- File Uploads

---

## apps/worker

Responsibilities:

- Queue Processing
- Scheduled Jobs
- Email
- Reports
- AI Background Tasks
- Imports
- Notifications

Applications must remain independent.

---

# 6. Shared Packages

Shared code belongs inside `/packages`.

Current packages:

## ui

Reusable React UI components.

Examples:

- Button
- Dialog
- Card
- Table
- Charts

---

## types

Shared TypeScript types.

Examples:

- DTOs
- API Models
- Shared Interfaces
- Enums

---

## utils

Pure utility functions.

Examples:

- Date helpers
- Currency
- Validation
- Formatting

---

## config

Shared configuration.

Examples:

- Environment schema
- Constants
- Feature flags

---

## eslint-config

Shared lint configuration.

---

## tsconfig

Shared TypeScript configuration.

---

## api-client

Shared HTTP client used by the frontend.

No package may depend on an application.

Applications may depend on packages.

---

# 7. Dependency Rules

Allowed dependency direction:

```text
apps

↓

packages

↓

external libraries
```

Forbidden:

```text
package

↓

app
```

Packages must never import from applications.

---

# 8. Workspace Management

Workspace manager:

- pnpm

Workspace definition:

```yaml
packages:

  - apps/*
  - packages/*
```

Benefits:

- Fast installs
- Shared dependencies
- Efficient caching
- Workspace linking

---

# 9. Turbo Pipeline

Turborepo coordinates builds.

Pipeline stages:

```text
Lint

↓

Type Check

↓

Unit Tests

↓

Build

↓

E2E Tests
```

Each package should define:

- build
- dev
- lint
- test
- typecheck

Turbo cache should be enabled for all deterministic tasks.

---

# 10. Root Configuration

The repository root owns:

- package.json
- turbo.json
- workspace configuration
- shared scripts
- git hooks
- CI configuration

Applications should not redefine shared tooling unless justified.

---

# 11. Development Environment

Required software:

| Software | Version |
|----------|---------|
| Node.js | 22 LTS |
| pnpm | Latest Stable |
| Docker | Latest Stable |
| Git | Latest Stable |
| PostgreSQL | 16+ |
| Redis | 7+ |

Recommended IDE:

- Visual Studio Code

Recommended extensions:

- ESLint
- Prettier
- Prisma
- Tailwind CSS IntelliSense
- GitLens
- Error Lens

---

# 12. Environment Variables

Configuration must be externalized from application code.

Environment file hierarchy:

```text
.env

.env.local

.env.development

.env.staging

.env.production
```

Each application should validate its required environment variables during startup.

Example variables:

```text
NODE_ENV

APP_NAME

API_PORT

WEB_PORT

DATABASE_URL

REDIS_URL

JWT_SECRET

JWT_REFRESH_SECRET

STORAGE_ENDPOINT

STORAGE_ACCESS_KEY

STORAGE_SECRET_KEY

OPENAI_API_KEY

SMTP_HOST

SMTP_PORT

SMTP_USER

SMTP_PASSWORD
```

Rules:

- Never commit secrets.
- Provide `.env.example` files for every application.
- Validate required variables before application initialization.
- Document all supported variables.

---

# 13. Docker Development Stack

Local development should run through Docker Compose.

Core services:

```text
Frontend

↓

Backend API

↓

Worker

↓

PostgreSQL

↓

Redis

↓

MinIO

↓

Mailpit
```

Service responsibilities:

| Service | Purpose |
|----------|---------|
| PostgreSQL | Primary database |
| Redis | Cache & queues |
| MinIO | S3-compatible object storage |
| Mailpit | Local email testing |

Development containers should support:

- Hot reload
- Persistent volumes
- Health checks
- Automatic restart
- Named networks

---

# 14. Local Development Workflow

Standard workflow:

```text
Clone Repository

↓

Install Dependencies

↓

Start Infrastructure

↓

Run Database Migrations

↓

Seed Development Data

↓

Start Applications

↓

Begin Development
```

Recommended commands:

```bash
pnpm install

docker compose up -d

pnpm db:migrate

pnpm db:seed

pnpm dev
```

Every developer should be able to bootstrap a working environment with minimal manual configuration.

---

# 15. Git Hooks

Git hooks should enforce baseline quality before code reaches the repository.

Recommended hooks:

### pre-commit

- ESLint
- Prettier
- Type checking (staged files where practical)

### commit-msg

- Conventional Commit validation

### pre-push

- Unit tests
- Build validation (optional for large repositories)

Suggested tools:

- Husky
- lint-staged
- commitlint

Hooks should complete quickly to avoid slowing development unnecessarily.

---

# 16. Linting & Formatting

Code formatting must be automated.

Linting:

- ESLint

Formatting:

- Prettier

Rules:

- Automatic formatting on save
- Zero lint errors before merge
- Shared configuration through workspace packages
- Consistent import ordering
- Remove unused imports automatically where supported

Formatting should be deterministic across all development environments.

---

# 17. Continuous Integration

Every Pull Request should trigger automated validation.

Pipeline stages:

```text
Install

↓

Lint

↓

Type Check

↓

Unit Tests

↓

Build

↓

Integration Tests

↓

Artifact Creation
```

CI requirements:

- Dependency caching
- Turbo remote/local cache support
- Build artifacts
- Test reports
- Coverage reports

A failing CI pipeline blocks merges into protected branches.

---

# 18. Repository Standards

Repository requirements:

- Single source of truth
- Clear folder ownership
- Protected main branch
- CODEOWNERS (recommended)
- Pull request templates
- Issue templates
- Security policy
- Contributing guide
- Changelog

Recommended root files:

```text
README.md

CONTRIBUTING.md

SECURITY.md

CHANGELOG.md

LICENSE
```

Repository documentation should be updated alongside significant architectural changes.

---

# 19. Bootstrap Scripts

The repository should provide standardized bootstrap scripts.

Recommended scripts:

| Script | Purpose |
|---------|----------|
| `pnpm dev` | Start all development services |
| `pnpm build` | Build entire workspace |
| `pnpm lint` | Run linting |
| `pnpm test` | Execute tests |
| `pnpm typecheck` | TypeScript validation |
| `pnpm format` | Format source code |
| `pnpm db:migrate` | Apply database migrations |
| `pnpm db:seed` | Seed development database |
| `pnpm clean` | Remove build artifacts |
| `pnpm reset` | Reset local development environment |

Scripts should provide a consistent interface across all applications and packages.

---

# 20. Workspace Quality Gates

Before merging code, the following conditions must be satisfied:

- All dependencies install successfully.
- Type checking passes.
- Linting passes with zero errors.
- Required tests pass.
- Build succeeds.
- Documentation is updated where applicable.
- No unresolved merge conflicts exist.
- No secrets are committed.

These quality gates apply to all applications and shared packages.

---

# 21. Release & Versioning

The monorepo should follow Semantic Versioning (SemVer).

Version format:

```text
MAJOR.MINOR.PATCH
```

Examples:

```text
0.1.0

0.2.0

0.2.1

1.0.0
```

Versioning principles:

- MAJOR — Breaking architectural or API changes
- MINOR — Backward-compatible features
- PATCH — Bug fixes and maintenance

Every production release must:

- Be tagged in Git
- Have release notes
- Reference the associated milestone
- Preserve changelog history

---

# 22. Initial Repository Checklist

The repository is considered correctly initialized when all of the following are complete.

## Repository

- Git repository initialized
- Protected `main` branch
- `develop` branch created
- Repository description configured
- License added
- README completed

---

## Workspace

- pnpm workspace configured
- Turborepo configured
- Shared TypeScript configuration
- Shared ESLint configuration
- Shared Prettier configuration

---

## Applications

- React application initialized
- NestJS API initialized
- Worker service initialized

---

## Packages

- ui
- types
- utils
- config
- api-client
- eslint-config
- tsconfig

---

## Infrastructure

- Docker Compose
- PostgreSQL
- Redis
- MinIO
- Mailpit

---

## CI/CD

- GitHub Actions
- Pull Request workflow
- Build workflow
- Test workflow
- Release workflow (future)

---

## Documentation

- Business Specifications
- Architecture Specifications
- Implementation Specifications
- API Documentation
- Contributing Guide
- Security Policy

---

# 23. Acceptance Criteria

The Monorepo Foundation is complete when:

- Repository structure follows the approved architecture.
- Applications are separated from shared packages.
- Workspace management is configured.
- Turborepo pipeline is operational.
- Shared TypeScript configuration is established.
- Shared ESLint and Prettier configurations are applied.
- Docker development environment is functional.
- Environment variables are validated.
- Local development workflow is documented.
- Git hooks enforce repository standards.
- Continuous Integration validates every Pull Request.
- Repository documentation is complete.
- Bootstrap scripts function consistently across the workspace.

---

# 24. AI Context Summary

## Summary

The Monorepo Foundation establishes the engineering baseline for Nebula ERP. It defines repository organization, workspace management, shared packages, development tooling, environment configuration, Docker infrastructure, Git workflow, CI/CD integration, and quality gates. All future implementation documents build upon this foundation.

## Dependencies

- DOC-001 — System Architecture
- DOC-003 — Backend Architecture
- DOC-004 — Frontend Architecture
- DOC-005 — Design System
- DOC-006 — Infrastructure
- DOC-009 — Development Standards

## Referenced By

- All implementation documents
- CI/CD configuration
- Developer onboarding
- Deployment pipeline

---

# 25. Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial Monorepo Foundation implementation specification |