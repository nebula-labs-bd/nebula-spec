# Nebula ERP Database Standards

| Field | Value |
|-------|-------|
| Document ID | DOC-010 |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

This document defines the official database standards for Nebula ERP. It establishes rules for schema design, table naming, relationships, indexing, migrations, auditing, and data integrity.

These standards apply to every database object created for Nebula ERP.

---

# 2. Design Principles

The database should be:

- Consistent
- Normalized
- Scalable
- Secure
- Performant
- Auditable
- Maintainable

The database is the single source of truth for business data.

---

# 3. Database Platform

Official database:

- PostgreSQL

Database access:

- Prisma ORM

Every schema change must be tracked using Prisma Migrations.

---

# 4. Naming Conventions

## Tables

Use:

- lowercase
- snake_case
- plural nouns

Examples:

```
products

customers

sales_orders

purchase_orders
```

Avoid abbreviations unless they are universally understood.

---

## Columns

Use:

- lowercase
- snake_case

Examples:

```
created_at

updated_at

branch_id

supplier_id
```

Column names should clearly describe their purpose.

---

## Primary Keys

Every table must contain a primary key.

Preferred format:

```
id UUID PRIMARY KEY
```

UUIDs provide uniqueness across distributed systems and simplify future integrations.

---

## Foreign Keys

Foreign key columns should follow this convention:

```
customer_id

product_id

branch_id

supplier_id
```

Each foreign key must reference an existing primary key and enforce referential integrity.

---

# 5. Required Audit Columns

Unless there is a justified exception, business tables should include:

```
id

created_at

updated_at

created_by

updated_by
```

Optional where appropriate:

```
deleted_at

deleted_by
```

Audit fields improve traceability and support operational reporting.

---

# 6. Relationships

Supported relationship types:

- One-to-One
- One-to-Many
- Many-to-Many

Guidelines:

- Use foreign keys.
- Enforce referential integrity.
- Document cascading behavior.
- Avoid orphaned records.

Relationship decisions should prioritize data integrity over convenience.

---

# 7. Constraints

Use database constraints wherever possible.

Examples:

- PRIMARY KEY
- FOREIGN KEY
- UNIQUE
- CHECK
- NOT NULL

Business rules should not rely solely on application logic when they can be enforced by the database.

---

# 8. Indexing

Indexes improve query performance.

Create indexes for:

- Foreign keys
- Frequently filtered columns
- Frequently sorted columns
- Unique values

Avoid excessive indexing, as it increases write overhead.

Indexes should be reviewed periodically based on query performance.

---

# 9. Transactions

Multiple related database operations should execute within a transaction.

Transactions should guarantee:

- Atomicity
- Consistency
- Isolation
- Durability (ACID)

Never leave data in a partially updated state.

---

# 10. Soft Deletes

Business entities that may require recovery should support soft deletion.

Recommended columns:

```
deleted_at

deleted_by
```

Soft-deleted records should be excluded from application queries by default.

Permanent deletion should be reserved for exceptional cases.

---

# 11. Migration Standards

All database schema changes must be managed through Prisma Migrations.

## Rules

- Never modify the production database manually.
- Every schema change must generate a migration.
- Migrations must be committed to version control.
- Each migration should have a clear purpose.
- Test migrations before deployment.

Migration files should never be edited after they have been applied to production.

---

# 12. Seed Data

Seed data should be used only for predictable initial system data.

Examples:

- System Roles
- Default Permissions
- Countries
- Currencies
- Tax Types
- Measurement Units

Business data such as customers, products, or invoices should never be included in seed files.

---

# 13. Query Performance

Database queries should be designed for efficiency.

Guidelines:

- Select only required columns.
- Avoid unnecessary joins.
- Use indexes effectively.
- Limit returned rows.
- Use pagination for collections.
- Review slow queries regularly.

Performance improvements should be based on measurement rather than assumptions.

---

# 14. Security

Database security is mandatory.

Requirements:

- Parameterized queries only.
- Least-privilege database users.
- Encrypted connections.
- Secure credential storage.
- Access logging where appropriate.

Sensitive information should never be stored in plain text.

---

# 15. Backup & Recovery

Regular backups are required.

Backup strategy should include:

- Scheduled full backups
- Incremental backups (where supported)
- Backup verification
- Restore testing
- Off-site storage

Recovery procedures should be documented and tested periodically.

---

# 16. Data Retention

Data retention policies should comply with legal, contractual, and operational requirements.

Guidelines:

- Archive historical data when appropriate.
- Retain audit logs according to policy.
- Permanently delete data only when permitted.
- Document retention periods for each data category.

---

# 17. Prisma Standards

Prisma is the official ORM for Nebula ERP.

Guidelines:

- Keep the schema organized.
- Use meaningful model names.
- Define relationships explicitly.
- Prefer generated types over custom duplicates.
- Review generated migrations before applying them.

Schema changes should be documented in the relevant specification documents.

---

# 18. Database Review Checklist

Before deploying schema changes, verify:

- Naming conventions are followed.
- Relationships are correct.
- Constraints are defined.
- Required indexes exist.
- Audit fields are included.
- Migrations execute successfully.
- Rollback procedures are understood.
- Documentation has been updated.

---

# 19. Database Design Summary

Every database object should be:

- Consistent
- Normalized
- Secure
- Auditable
- Performant
- Scalable
- Well documented

Database integrity should always take precedence over implementation convenience.

---

# 20. AI Context Summary

## Summary

This document defines the official database standards for Nebula ERP, covering schema design, naming conventions, relationships, indexing, migrations, security, and operational best practices.

## Related Documents

- DOC-002 System Architecture
- DOC-003 Technology Stack
- DOC-007 Coding Standards
- DOC-008 API Standards

## Related Standards

- Development Workflow
- Folder Structure
- UI Guidelines

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial database standards specification |