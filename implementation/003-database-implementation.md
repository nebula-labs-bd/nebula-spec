# Implementation Document 003 — Database Implementation

| Field | Value |
|-------|-------|
| Document ID | IMP-003 |
| Name | Database Implementation |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

This document defines how Nebula ERP implements its database layer using PostgreSQL and Prisma.

The goal is to create a scalable, maintainable, secure, and high-performance data architecture that supports multi-tenancy, auditing, AI integration, and future platform growth.

---

# 2. Objectives

The database implementation must:

- Support multi-tenancy
- Maintain referential integrity
- Scale to millions of records
- Minimize redundant data
- Optimize read and write performance
- Support AI features
- Enable comprehensive auditing
- Support soft deletion
- Allow zero-downtime schema evolution
- Be fully version controlled

---

# 3. Technology Stack

| Component | Technology |
|-----------|------------|
| Database | PostgreSQL 16+ |
| ORM | Prisma ORM |
| Migration Tool | Prisma Migrate |
| Seed Tool | Prisma Seed |
| Query Language | SQL / Prisma Client |
| Schema Language | Prisma Schema |

---

# 4. Database Directory Structure

```text
apps/api/

└── prisma/

    ├── schema.prisma

    ├── migrations/

    ├── seed.ts

    ├── seeds/

    │   ├── roles.ts
    │   ├── permissions.ts
    │   ├── countries.ts
    │   ├── currencies.ts
    │   └── demo.ts

    └── README.md
```

The Prisma directory contains all schema definitions, migrations, and seed logic.

---

# 5. Schema Organization

Models should be grouped by business domain.

Recommended organization:

```text
Core

Identity

Organization

Inventory

Sales

Purchasing

Finance

CRM

HR

AI

System
```

Each domain should remain logically independent while using shared references where required.

---

# 6. Naming Conventions

## Tables

- Singular model names in Prisma
- Snake_case database table names

Example:

```text
User

↓

users
```

---

## Columns

Use snake_case.

Examples:

```text
created_at

updated_at

deleted_at

organization_id

customer_id
```

---

## Primary Keys

Every table uses:

```text
id UUID
```

UUID v7 is recommended when available for improved index locality. UUID v4 is acceptable if v7 support is unavailable.

---

## Foreign Keys

Foreign keys follow:

```text
organization_id

product_id

invoice_id

purchase_order_id
```

---

## Indexes

Index names:

```text
idx_users_email

idx_products_sku

idx_sales_created_at
```

---

## Constraints

Constraint naming:

```text
pk_users

fk_products_category

uq_users_email

ck_inventory_quantity
```

Consistent naming simplifies maintenance and debugging.

---

# 7. Common Model Fields

Every business entity should include the following fields where applicable:

```text
id

organization_id

created_at

updated_at

deleted_at

created_by

updated_by

deleted_by
```

Benefits:

- Auditability
- Soft deletion
- Tenant isolation
- Ownership tracking

---

# 8. Multi-Tenant Strategy

Nebula ERP uses a shared database with logical tenant isolation.

```text
Database

↓

Organization

↓

Departments

↓

Users

↓

Business Records
```

Rules:

- Every tenant-owned table includes `organization_id`.
- Queries must always be scoped by organization.
- Cross-tenant access is prohibited unless explicitly authorized.
- Super Admin functionality bypasses tenant filtering only through controlled services.

---

# 9. Relationship Standards

Relationship types:

- One-to-One
- One-to-Many
- Many-to-Many

Examples:

```text
Organization

↓

Users

↓

Sales Orders

↓

Invoices
```

Many-to-many relationships should use explicit join tables with their own primary keys and audit fields.

Avoid implicit relationships that cannot be extended later.

---

# 10. Migration Strategy

Schema changes must be managed exclusively through Prisma migrations.

Migration principles:

- One logical change per migration
- Never modify applied migrations
- Use descriptive migration names
- Review generated SQL before applying
- Test migrations in staging before production

Examples:

```text
20260720_init_database

20260721_add_inventory_tables

20260723_create_sales_module
```

Production migrations should be executed as part of the deployment pipeline.

---

# 11. Seed Strategy

Seed data should initialize essential system records.

Required seed categories:

| Category | Purpose |
|----------|---------|
| Roles | Default system roles |
| Permissions | RBAC permissions |
| Countries | Country reference data |
| Currencies | Currency reference data |
| Tax Rates | Optional defaults |
| Units | Measurement units |
| Demo Data | Development and testing |

Seed scripts must be:

- Idempotent
- Version controlled
- Environment aware

Production environments should exclude demo data by default.

---

# 12. Indexing Strategy

Indexes should be created based on application query patterns rather than added indiscriminately.

### Primary Indexes

Every table must include:

- Primary Key
- Required Unique Constraints
- Foreign Key Indexes

---

### Frequently Indexed Columns

Recommended indexes:

| Column | Reason |
|---------|--------|
| organization_id | Tenant filtering |
| created_at | Sorting |
| updated_at | Synchronization |
| deleted_at | Soft delete filtering |
| status | Workflow filtering |
| sku | Product lookup |
| email | Authentication |
| username | Login |
| invoice_number | Search |
| order_number | Search |

---

### Composite Indexes

Examples:

```text
organization_id + created_at

organization_id + status

organization_id + customer_id

organization_id + product_id
```

Composite indexes should reflect real application queries.

---

### Full Text Search

Use PostgreSQL Full Text Search where appropriate.

Applicable modules:

- Products
- Customers
- Suppliers
- Documents
- CRM Notes
- Knowledge Base

Large-scale search features may later migrate to Elasticsearch or OpenSearch without changing the application domain model.

---

# 13. Query Optimization

Database queries should prioritize efficiency.

Guidelines:

- Select only required columns.
- Avoid `SELECT *`.
- Prevent N+1 query patterns.
- Use pagination for large datasets.
- Limit nested joins.
- Cache frequently accessed reference data.
- Batch writes when appropriate.

Prisma best practices:

- Use `select` to limit fields.
- Use `include` only when necessary.
- Prefer transactions for related writes.
- Reuse Prisma Client instance.

---

# 14. Soft Delete Strategy

Business records should be soft deleted whenever recovery or auditability is important.

Standard fields:

```text
deleted_at

deleted_by
```

Deletion lifecycle:

```text
Active

↓

Soft Deleted

↓

Archived (optional)

↓

Permanent Removal
```

Rules:

- Default queries exclude soft-deleted records.
- Administrative tools may restore records.
- Permanent deletion should be restricted and logged.

Reference tables (for example, countries or currencies) should generally not support soft deletion.

---

# 15. Audit Logging

Critical business actions must be traceable.

Audit fields:

```text
created_by

updated_by

deleted_by

created_at

updated_at

deleted_at
```

Events that should generate audit records include:

- User authentication
- Permission changes
- Product modifications
- Inventory adjustments
- Financial transactions
- Configuration changes
- Data imports
- Data exports

Audit records should be immutable.

---

# 16. Transactions

Database transactions must protect business consistency.

Use transactions for:

- Sales creation
- Purchase processing
- Inventory movements
- Financial posting
- Stock adjustments
- Payment processing

Typical workflow:

```text
Validate

↓

Begin Transaction

↓

Execute Changes

↓

Commit

↓

Success
```

On failure:

```text
Rollback

↓

Return Error
```

Partial updates should never leave business data in an inconsistent state.

---

# 17. Data Validation

Validation should occur at multiple layers.

Validation hierarchy:

```text
Client

↓

API DTO Validation

↓

Business Rules

↓

Database Constraints
```

Validation categories:

- Required fields
- Data types
- Length limits
- Numeric ranges
- Foreign key existence
- Unique constraints
- Business rules

Database constraints provide the final enforcement layer and should not be replaced by application-only validation.

---

# 18. Backup Compatibility

The schema should support reliable backup and restore operations.

Requirements:

- Stable primary keys
- Foreign key integrity
- Deterministic migrations
- Timestamp consistency
- No hard-coded environment values

Backups should support:

- Full database restoration
- Point-in-time recovery (where configured)
- Tenant-wide export
- Individual record recovery through application features where practical

---

# 19. Performance Guidelines

Performance objectives:

| Operation | Target |
|-----------|--------|
| Simple lookup | < 50 ms |
| Standard list query | < 200 ms |
| Dashboard queries | < 500 ms |
| Large report generation | Background job |
| Bulk import | Queue processing |

General recommendations:

- Monitor slow queries.
- Review execution plans regularly.
- Add indexes only when justified.
- Archive historical data where appropriate.
- Optimize high-volume tables periodically.

Performance testing should be part of pre-production validation.

---

# 20. Database Security

The database layer must enforce security through multiple defensive controls.

Security principles:

- Least privilege access
- Encrypted connections (TLS in production)
- Strong authentication
- Parameterized queries
- Secret management
- Role-based access
- Continuous auditing

Database users:

| User | Permissions |
|------|-------------|
| app_user | CRUD on application tables |
| migration_user | Schema migrations |
| readonly_user | Read-only reporting |
| backup_user | Backup operations |

Administrative accounts must never be used by application services.

Sensitive data such as passwords, API keys, and tokens must never be stored in plain text.

---

# 21. Schema Validation Checklist

Before a schema is accepted, verify the following:

## Structure

- All models use UUID primary keys.
- Naming conventions are followed.
- Relationships are explicitly defined.
- Foreign keys are indexed.

## Multi-Tenancy

- Tenant-owned tables include `organization_id`.
- Queries enforce tenant isolation.
- Cross-tenant access is prevented.

## Auditing

- Audit fields are present.
- Soft delete fields exist where required.
- Immutable audit records are maintained.

## Performance

- Frequently queried columns are indexed.
- Composite indexes match query patterns.
- Pagination is implemented for list endpoints.

## Integrity

- Required constraints exist.
- Foreign key relationships validate successfully.
- Seed data executes without conflicts.

---

# 22. Acceptance Criteria

The Database Implementation is complete when:

- Prisma schema is organized by domain.
- Database naming conventions are consistently applied.
- Migration strategy is documented.
- Seed strategy is implemented.
- Multi-tenancy is enforced.
- Audit fields are standardized.
- Soft deletion strategy is defined.
- Indexing strategy is documented.
- Query optimization guidelines are established.
- Transaction handling is standardized.
- Database security controls are documented.
- Validation checklist passes.

---

# 23. AI Context Summary

## Summary

The Database Implementation defines the persistence layer for Nebula ERP using PostgreSQL and Prisma. It establishes schema organization, naming conventions, migration workflows, multi-tenancy, auditing, indexing, transactions, validation, performance optimization, and security standards.

## Dependencies

- IMP-001 — Monorepo Foundation
- IMP-002 — Infrastructure Bootstrap
- DOC-002 — Database Architecture
- DOC-007 — Security

## Referenced By

- Backend services
- Authentication & RBAC
- Business modules
- AI platform
- Reporting engine
- Data import/export services

---

# 24. Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|----------------------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial Database Implementation specification |