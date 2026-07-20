# Document 002 — Database Architecture

| Field | Value |
|-------|-------|
| Document ID | DOC-002 |
| Name | Database Architecture |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

This document defines the database architecture for Nebula ERP.

It establishes standards for schema design, entity relationships, naming conventions, indexing, transactions, auditing, migrations, performance optimization, and long-term maintainability.

The goal is to ensure that every module follows a consistent, scalable, and performant data model.

---

# 2. Objectives

The database architecture must:

- Support multi-tenancy
- Ensure data integrity
- Provide high performance
- Scale with organizational growth
- Maintain referential consistency
- Support auditing
- Enable efficient reporting
- Simplify migrations
- Minimize downtime
- Allow future expansion

---

# 3. Database Platform

Primary database:

**PostgreSQL**

Reasons for selection:

- Excellent ACID compliance
- Mature ecosystem
- Strong indexing capabilities
- Native JSON support
- Full-text search
- Advanced partitioning
- Extensions (PostGIS, pg_trgm, UUID)
- High reliability
- Proven enterprise scalability

Future support may include read replicas and managed PostgreSQL services.

---

# 4. Design Principles

Nebula ERP databases follow these principles:

- Normalize transactional data
- Denormalize only where justified
- Avoid duplicated business data
- Prefer explicit relationships
- Enforce constraints at the database level
- Keep schemas readable
- Optimize for correctness first, performance second
- Never compromise tenant isolation

---

# 5. Multi-Tenant Strategy

Nebula ERP uses a **Shared Database, Shared Schema** model.

Every tenant-owned table must include:

```sql
organization_id UUID NOT NULL
```

Organization isolation is mandatory.

Every query must filter by:

```text
organization_id
```

Tenant ownership cannot be optional.

Global reference tables (example: countries, currencies, languages) do not require `organization_id`.

---

# 6. Primary Key Strategy

All business entities use UUIDs.

Example:

```sql
id UUID PRIMARY KEY
```

Reasons:

- Globally unique identifiers
- Safe distributed generation
- No predictable sequences
- Easier replication
- Better integration support

Auto-increment integers may be used internally for logging or analytics where external exposure is unnecessary.

---

# 7. Standard Audit Columns

Every business table should contain the following fields unless explicitly exempted.

| Column | Type |
|---------|------|
| id | UUID |
| organization_id | UUID |
| created_at | TIMESTAMP WITH TIME ZONE |
| updated_at | TIMESTAMP WITH TIME ZONE |
| created_by | UUID |
| updated_by | UUID |
| deleted_at | TIMESTAMP WITH TIME ZONE (nullable) |

Optional fields:

- archived_at
- archived_by
- restored_at
- restored_by

These fields provide consistency across all modules.

---

# 8. Naming Conventions

## Tables

Use:

- lowercase
- plural
- snake_case

Examples:

```text
users

products

purchase_orders

inventory_transactions

sales_invoices
```

---

## Columns

Use:

```text
snake_case
```

Examples:

```text
product_name

purchase_date

created_at

organization_id
```

---

## Foreign Keys

Use:

```text
<entity>_id
```

Examples:

```text
customer_id

supplier_id

warehouse_id

product_id
```

---

## Constraints

Recommended prefixes:

```text
pk_

fk_

idx_

uq_

chk_
```

Example:

```text
pk_products

fk_products_category

idx_products_name

uq_users_email
```

---

# 9. Relationship Standards

Supported relationships:

- One-to-One
- One-to-Many
- Many-to-Many

Many-to-many relationships should always use explicit junction tables.

Example:

```
users

↓

user_roles

↓

roles
```

Avoid storing comma-separated identifiers.

---

# 10. Indexing Strategy

Every table should have:

- Primary Key Index
- Foreign Key Indexes
- Frequently Filtered Columns
- Frequently Joined Columns

Composite indexes should follow query patterns.

Examples:

```sql
(organization_id, status)

(organization_id, created_at)

(organization_id, product_code)

(organization_id, customer_id)
```

Indexes should be reviewed periodically to remove unused or redundant indexes.

---

# 11. Constraints & Integrity

The database should enforce:

- Primary Keys
- Foreign Keys
- Unique Constraints
- Check Constraints
- NOT NULL Constraints

Examples:

```sql
price >= 0

quantity >= 0

discount_percentage BETWEEN 0 AND 100
```

Business rules that can be safely enforced by the database should be implemented as constraints where appropriate.

---

# 12. Schema Organization

Nebula ERP uses a logical schema organization while maintaining a shared application schema.

## Default Schema

```
public
```

Stores:

- Core ERP tables
- Business entities
- Reference data
- Transactions

---

## Future Schemas

The following schemas may be introduced if operational requirements justify separation:

```
audit

reporting

integration

analytics

system
```

Schema separation should not increase application complexity unnecessarily.

---

# 13. Transaction Management

All business operations requiring consistency shall execute within database transactions.

Examples include:

- Purchase Approval
- Sales Invoice Creation
- Stock Adjustment
- Payment Posting
- Inventory Transfer
- Customer Balance Update

Transactions must follow ACID principles.

---

## Transaction Rules

- Keep transactions as short as possible.
- Never perform external API calls inside database transactions.
- Avoid long-running locks.
- Roll back completely on failure.
- Use optimistic concurrency where appropriate.

---

# 14. Soft Delete Strategy

Business records should not be permanently deleted under normal operation.

Soft deletion uses:

```sql
deleted_at TIMESTAMPTZ NULL
```

Optional:

```sql
deleted_by UUID
```

Queries should exclude soft-deleted records by default.

Permanent deletion should be limited to:

- Data retention policies
- Administrative cleanup
- Legal compliance requirements

---

# 15. Partitioning Strategy

Partitioning should be introduced only when data volume requires it.

Candidate tables include:

- audit_logs
- integration_logs
- notifications
- inventory_transactions
- sales_transactions
- payment_transactions

Recommended partition strategy:

- Monthly partitions
- Quarterly partitions
- Yearly partitions

Partition keys should align with query patterns.

---

# 16. JSON Usage

PostgreSQL JSONB may be used for flexible metadata that does not justify dedicated relational columns.

Suitable examples:

- External API payloads
- Connector configurations
- User preferences
- Dynamic settings
- Integration responses

JSON should **not** replace relational modeling for core transactional data.

Avoid storing searchable business attributes exclusively in JSON.

---

# 17. Full-Text Search

Where appropriate, PostgreSQL Full-Text Search should be used.

Candidate entities include:

- Products
- Customers
- Suppliers
- Documents
- Notes
- CRM Activities

Future enhancements may include:

- Trigram search
- Fuzzy matching
- Language-aware stemming
- AI-assisted semantic search

---

# 18. Database Functions & Procedures

Database functions should be used sparingly.

Appropriate use cases include:

- Aggregate calculations
- Reporting helpers
- Data validation
- Maintenance routines
- Scheduled cleanup

Core business logic should remain within the application layer.

This improves portability, testing, and maintainability.

---

# 19. Performance Standards

Target performance objectives:

| Operation | Target |
|-----------|--------|
| Simple Lookup | < 50 ms |
| Standard CRUD | < 100 ms |
| Paginated Lists | < 300 ms |
| Dashboard Queries | < 500 ms |
| Report Generation | Asynchronous for large datasets |

Performance guidelines:

- Use `EXPLAIN ANALYZE` during optimization.
- Avoid `SELECT *` in production queries.
- Fetch only required columns.
- Batch updates where practical.
- Monitor slow query logs.
- Regularly vacuum and analyze tables.

---

# 20. Backup Strategy

Recommended backup approach:

### Full Backup

- Daily

### Incremental Backup

- Every hour (where supported)

### WAL Archiving

Enable continuous Write-Ahead Log archiving to support Point-in-Time Recovery (PITR).

### Backup Storage

- Encrypted
- Off-site
- Versioned
- Regularly tested

Retention policies should align with organizational compliance requirements.

---

# 21. Restore Strategy

Database recovery procedures should support:

- Full database restore
- Point-in-Time Recovery
- Single table restoration (where feasible)
- Backup verification

Recovery testing should be performed on a scheduled basis to validate backup integrity and recovery objectives.

---

## ✅ End of Part 2

**Don't commit yet.**

**Part 3** will include:

- Migration Strategy
- Data Archiving
- Monitoring & Maintenance
- API Summary
- Acceptance Criteria
- AI Context Summary
- Revision History
- Git Commit
- Progress Tracker

---

# 22. Migration Strategy

Nebula ERP uses version-controlled database migrations.

Migration principles:

- Every schema change must be tracked.
- Migrations are immutable after deployment.
- Each migration must be reversible where practical.
- Production migrations should be tested in staging before release.
- Schema and application versions should remain compatible during deployment.

Migration workflow:

```
Development

↓

Migration Created

↓

Code Review

↓

Testing

↓

Staging Deployment

↓

Production Deployment
```

Recommended practices:

- One logical change per migration.
- Avoid mixing schema and data migrations when possible.
- Use transactional migrations where supported.
- Never edit previously applied migrations.

---

# 23. Data Archiving

Large historical datasets should be archived according to retention policies.

Candidate tables:

- audit_logs
- notifications
- integration_logs
- inventory_transactions
- payment_transactions
- sales_transactions

Archive principles:

- Preserve referential integrity.
- Maintain read-only access where required.
- Record archive timestamps.
- Support restoration where business requirements permit.

Archived data should not impact operational query performance.

---

# 24. Monitoring & Maintenance

Database health should be monitored continuously.

Key metrics include:

- Active Connections
- Query Latency
- Slow Queries
- Deadlocks
- Lock Wait Time
- Index Usage
- Cache Hit Ratio
- Table Bloat
- Disk Utilization
- Replication Lag (Future)

Routine maintenance includes:

- VACUUM
- ANALYZE
- REINDEX (when necessary)
- Statistics updates
- Backup verification
- Storage capacity review

Automated alerts should notify administrators when defined thresholds are exceeded.

---

# 25. Security Considerations

The database layer shall enforce:

- TLS encryption for client connections
- Least-privilege database roles
- Encrypted backups
- Strong authentication
- Parameterized queries through the ORM
- Protection against SQL injection
- Secure secret management
- Restricted administrative access

Direct production database access should be limited to authorized administrators.

---

# 26. Database Standards Summary

All modules shall follow these standards:

- UUID primary keys
- `organization_id` on tenant-owned tables
- Standard audit columns
- Snake_case naming
- Foreign key enforcement
- Indexed query paths
- Soft deletes by default
- Transactional consistency
- Version-controlled migrations
- Performance monitoring

These standards apply across every Nebula ERP module.

---

# 27. Acceptance Criteria

The database architecture is complete when:

- Multi-tenant isolation is consistently enforced.
- Naming conventions are standardized.
- Referential integrity is maintained.
- Audit columns exist where required.
- Soft delete behavior is implemented.
- Indexing follows documented guidelines.
- Migrations are version-controlled.
- Backup and recovery procedures are documented.
- Monitoring and maintenance practices are established.
- Database design supports future scalability.

---

# 28. AI Context Summary

## Summary

The Database Architecture defines the structural foundation of Nebula ERP using PostgreSQL. It establishes standards for multi-tenancy, UUID identifiers, schema organization, constraints, indexing, transactions, soft deletes, migrations, backups, monitoring, and long-term scalability. These conventions ensure consistency, integrity, and performance across every business module.

## Dependencies

- DOC-001 — System Architecture
- Business Specification (Modules 001–024)

## Referenced By

- Backend Architecture
- Frontend Architecture
- Infrastructure
- Security
- AI Architecture
- Development Standards

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial Database Architecture specification |