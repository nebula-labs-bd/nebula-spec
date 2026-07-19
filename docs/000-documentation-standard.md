# Nebula ERP Documentation Standard

| Field | Value |
|-------|-------|
| Document ID | DOC-000 |
| Version | 1.0.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

This document defines the documentation standards used throughout the Nebula ERP Engineering Handbook. Every specification, module, API, workflow, database table, business rule, and design document must follow these standards to ensure consistency, maintainability, and AI-assisted development.

---

# 2. Document Status

Every document in the Nebula ERP Engineering Handbook must have one of the following statuses.

| Status | Meaning |
|---------|---------|
| Draft | Initial work in progress |
| Review | Ready for technical review |
| Approved | Accepted as the current source of truth |
| Deprecated | No longer recommended but kept for historical reference |
| Archived | Retained for historical purposes only |

---

# 3. Versioning

All documentation in the Nebula ERP Engineering Handbook follows Semantic Versioning.

| Version | Meaning |
|---------|---------|
| 0.x.x | Work in progress |
| 1.0.0 | Initial approved version |
| Major | Breaking or significant changes |
| Minor | New sections or features added |
| Patch | Corrections, formatting, or clarification only |

Example:

- 1.0.0 → Initial approved document
- 1.1.0 → New section added
- 1.1.1 → Grammar or formatting fix
- 2.0.0 → Major restructuring
---

# 4. Document ID Convention

Every document in the Nebula ERP Engineering Handbook must have a unique identifier.

| Prefix | Purpose | Example |
|---------|---------|---------|
| DOC | General Documents | DOC-001 |
| MOD | Modules | MOD-001 |
| DB | Database Tables | DB-001 |
| API | API Specifications | API-001 |
| BR | Business Rules | BR-001 |
| WF | Workflows | WF-001 |
| UI | UI Screens | UI-001 |
| CMP | UI Components | CMP-001 |
| ADR | Architecture Decision Records | ADR-001 |

Rules:

- IDs are permanent.
- IDs must never be reused.
- IDs are assigned sequentially.
- Cross-reference documents using their IDs whenever possible.

---

# 5. File Naming Convention

To keep the repository organized and predictable, all files must follow a consistent naming convention.

## Rules

- Use lowercase letters only.
- Separate words with hyphens (`-`).
- Prefix specification documents with a three-digit sequence number.
- Avoid spaces and special characters.
- Keep file names descriptive and concise.

## Examples

| Correct | Incorrect |
|---------|-----------|
| 000-documentation-standard.md | Documentation Standard.md |
| 001-product-vision.md | ProductVision.md |
| 002-system-architecture.md | System Architecture.md |
| 003-tech-stack.md | techStack.md |
| inventory-module.md | Inventory_Module.md |

---

# 6. Folder Structure Standard

Each top-level folder in the repository has a single responsibility.

| Folder | Purpose |
|--------|---------|
| docs | Core engineering handbook and standards |
| modules | ERP module specifications |
| database | Database design and schema |
| api | API specifications |
| business-rules | Business rules |
| workflows | Business process workflows |
| templates | Reusable document templates |
| diagrams | ERDs, flowcharts, architecture diagrams |
| context | AI context and project summaries |
| decisions | Architecture Decision Records (ADRs) |
| changelog | Historical project changes |

---

# 7. Writing Standards

All documentation must follow these writing principles.

## Language

- Use clear and concise English.
- Avoid ambiguous terms.
- Prefer active voice over passive voice.
- Use "must" for mandatory requirements.
- Use "should" for recommendations.
- Use "may" for optional behavior.

## Formatting

- Use Markdown headings consistently.
- Use tables for structured data.
- Use bullet lists for short items.
- Keep paragraphs focused on a single topic.
- Include examples where they improve clarity.

## Cross References

When referring to another specification, always reference its Document ID.

Example:

- See **MOD-001** for Product Management.
- Refer to **API-005** for Product Search.
- Business rule **BR-012** applies.