# Nebula ERP Folder Structure

| Field | Value |
|-------|-------|
| Document ID | DOC-006 |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

This document defines the official folder structure for the Nebula ERP source code repository. A consistent directory structure improves maintainability, scalability, onboarding, and collaboration while reducing duplication and technical debt.

---

# 2. Design Principles

The project structure should:

- Be easy to navigate.
- Separate concerns clearly.
- Encourage code reuse.
- Support modular development.
- Scale as the product grows.
- Keep configuration centralized.
- Minimize coupling between modules.

---

# 3. Repository Structure

```
nebula-erp/

├── apps/
├── packages/
├── infrastructure/
├── scripts/
├── tools/
├── docs/
├── tests/
├── docker/
├── .github/
├── .vscode/
├── package.json
├── pnpm-workspace.yaml
├── turbo.json
├── README.md
└── LICENSE
```

---

# 4. apps/

The `apps` directory contains executable applications.

```
apps/

├── web/
├── api/
├── admin/      (future)
├── mobile/     (future)
└── desktop/    (future)
```

Each application should remain independently buildable whenever practical.

---

# 5. apps/web

Contains the React frontend.

```
apps/web/

src/

public/

assets/

components/

features/

hooks/

layouts/

pages/

router/

services/

stores/

styles/

types/

utils/

tests/
```

### Directory Responsibilities

**components/**

Reusable UI components shared across multiple features.

**features/**

Business feature modules such as Inventory, Sales, Purchasing, CRM, and Accounting.

**hooks/**

Reusable React hooks.

**services/**

API communication and business services.

**stores/**

Global client-side state.

**utils/**

Helper functions and shared utilities.

---

# 6. apps/api

Contains the NestJS backend.

```
apps/api/

src/

modules/

common/

config/

database/

events/

jobs/

middleware/

guards/

filters/

interceptors/

decorators/

tests/
```

### modules/

Every business module should remain independent whenever practical.

Example:

```
inventory/

controller/

service/

repository/

dto/

entities/

validators/

events/

tests/
```

---

# 7. packages/

Shared code belongs inside packages.

```
packages/

ui/

shared/

types/

config/

eslint-config/

tsconfig/

utils/
```

Packages should avoid dependencies on application-specific business logic.

---

# 8. infrastructure/

Infrastructure resources.

```
infrastructure/

docker/

kubernetes/

nginx/

terraform/

monitoring/

backups/
```

Infrastructure code should be version-controlled alongside application code.

---

# 9. scripts/

Automation and maintenance scripts.

Examples:

- Database seeding
- Backup scripts
- Deployment helpers
- Data migration
- Code generation
- Maintenance utilities

Scripts should be repeatable and safe to execute multiple times.

---

# 10. tools/

Developer tooling.

Examples:

- Internal CLI
- Documentation generators
- Code generators
- Utility scripts

Developer tools should simplify repetitive tasks without affecting production code.

---

# 11. docs/

The `docs` directory contains project documentation that supports development and operations.

```
docs/

architecture/

deployment/

development/

operations/

user-guide/

release-notes/
```

Documentation should remain synchronized with implementation changes.

---

# 12. tests/

The `tests` directory contains shared testing resources that are not tied to a single application.

```
tests/

fixtures/

helpers/

integration/

e2e/

performance/
```

Testing resources should be reusable and independent of application-specific code where practical.

---

# 13. .github/

The `.github` directory stores GitHub-specific configuration.

```
.github/

workflows/

ISSUE_TEMPLATE/

PULL_REQUEST_TEMPLATE.md/

CODEOWNERS
```

Typical contents include:

- GitHub Actions workflows
- Issue templates
- Pull request templates
- Repository configuration

---

# 14. Configuration Files

Project configuration files remain at the repository root.

```
package.json

pnpm-workspace.yaml

turbo.json

tsconfig.json

.prettierrc

.eslintrc

.gitignore

.env.example
```

Configuration should be centralized whenever possible.

---

# 15. Naming Conventions

## Directories

- lowercase
- kebab-case

Examples

```
sales-orders

purchase-management

customer-service
```

---

## Files

Use descriptive names.

Examples

```
inventory.service.ts

product.controller.ts

sales.repository.ts

customer.dto.ts
```

Avoid vague names such as:

```
helper.ts

utils2.ts

new.ts
```

---

# 16. Module Organization

Every business module should follow a consistent internal structure.

```
module/

controller/

service/

repository/

dto/

entities/

validators/

events/

tests/
```

Each module should own its business logic and expose functionality through well-defined interfaces.

---

# 17. Import Guidelines

Imports should follow this order:

1. External libraries
2. Internal packages
3. Shared utilities
4. Local modules
5. Relative imports

Example:

```typescript
import { Controller } from "@nestjs/common";

import { PrismaService } from "@nebula/shared";

import { InventoryService } from "../service/inventory.service";
```

Avoid circular dependencies between modules.

---

# 18. File Organization Principles

Files should be:

- Small
- Focused
- Easy to locate
- Cohesive

General recommendations:

- One primary responsibility per file.
- Prefer composition over large utility files.
- Keep related files together.
- Delete obsolete files promptly.

---

# 19. Future Expansion

The folder structure should support future additions such as:

- Mobile application
- Desktop application
- Public SDK
- Plugin system
- Marketplace
- AI services
- Microservices (if adopted)

New top-level directories should only be introduced when there is a clear architectural need.

---

# 20. AI Context Summary

## Summary

This document defines the standard directory layout for Nebula ERP. Following this structure ensures consistency, scalability, and maintainability across all applications and shared packages.

## Related Documents

- DOC-002 System Architecture
- DOC-003 Technology Stack
- DOC-005 Development Workflow
- DOC-007 Coding Standards

## Related Standards

- API Standards
- Database Standards
- UI Guidelines

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial folder structure specification |