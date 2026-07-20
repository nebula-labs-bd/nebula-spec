# Module 007 — Units

| Field | Value |
|-------|-------|
| Module ID | MOD-007 |
| Name | Units |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

The Units module provides the standardized system of measurement used throughout Nebula ERP.

It defines units of measure (UoM), conversion rules, packaging units, decimal precision, and measurement consistency across Products, Inventory, Purchasing, Sales, POS, Manufacturing, and Reporting.

All quantity-based calculations within the ERP rely on this module.

---

# 2. Objectives

The Units module must:

- Maintain standardized units of measure
- Support multiple measurement systems
- Support automatic unit conversion
- Support packaging units
- Support decimal precision
- Prevent invalid conversions
- Support custom organization units
- Ensure consistent quantity calculations
- Support future manufacturing requirements

---

# 3. Scope

This module manages:

- Units of Measure (UoM)
- Unit Groups
- Conversion Rules
- Packaging Units
- Decimal Precision
- Unit Status

This module does **not** manage:

- Products
- Inventory Quantities
- Pricing
- Warehouse Stock

Operational modules reference units but do not define them.

---

# 4. Business Objectives

Organizations should be able to define measurement systems that accurately represent their products and operations while ensuring all quantity calculations remain consistent and reliable.

---

# 5. Actors

Primary actors:

- Organization Administrator
- Inventory Manager
- Product Manager

Secondary actors:

- Purchasing Officer
- Warehouse Manager

Operational users reference units during daily activities but rarely modify them.

---

# 6. Functional Requirements

The module shall allow users to:

- Create units
- Update units
- Archive units
- Restore archived units
- Define conversion rules
- Create unit groups
- Configure decimal precision
- Search units
- Export units

---

# 7. Unit Types

Nebula ERP supports measurement units including:

- Quantity
- Length
- Weight
- Volume
- Area
- Time
- Packaging
- Custom

Examples:

Quantity

- Piece
- Dozen
- Pair

Weight

- Gram
- Kilogram
- Ton

Volume

- Milliliter
- Liter

Length

- Millimeter
- Centimeter
- Meter

Packaging

- Box
- Carton
- Bundle
- Pallet

Organizations may define additional custom units.

---

# 8. Unit Information

Each unit stores:

- Unit ID (UUID)
- Unit Code
- Unit Name
- Short Name
- Symbol
- Unit Group
- Base Unit
- Decimal Precision
- Status

Optional fields:

- Description
- Display Order
- Notes

---

# 9. Unit Groups

Units belong to logical groups.

Examples:

Quantity

- Piece
- Box
- Carton

Weight

- Gram
- Kilogram
- Ton

Volume

- Milliliter
- Liter

Length

- Millimeter
- Centimeter
- Meter

Only units within the same group may be converted.

---

# 10. Base Units

Each unit group has one Base Unit.

Examples:

Weight

Base Unit:

Gram

Length

Base Unit:

Millimeter

Volume

Base Unit:

Milliliter

Quantity

Base Unit:

Piece

All conversions are calculated relative to the Base Unit.

---

# 11. Unit Status

Supported unit states:

- Draft
- Active
- Inactive
- Archived

Only Active units may be assigned to new products.

Archived units remain available for historical transactions and reporting.

---

# 12. Business Rules

The Units module enforces the following rules.

## BR-001

Every unit must belong to exactly one organization.

System-defined units are shared across all organizations and cannot be modified.

---

## BR-002

Every unit must belong to exactly one Unit Group.

---

## BR-003

Each Unit Group must have exactly one Base Unit.

The Base Unit cannot be archived while dependent units exist.

---

## BR-004

Unit conversions are only permitted between units belonging to the same Unit Group.

Example:

```
Kilogram → Gram ✅

Liter → Milliliter ✅

Kilogram → Liter ❌
```

---

## BR-005

Conversion factors must always be greater than zero.

Negative or zero conversion values are invalid.

---

## BR-006

Units already referenced by products or transactions cannot be permanently deleted.

They must be archived instead.

---

## BR-007

Changing a conversion factor does not modify historical inventory, purchasing, sales, or accounting transactions.

Updated conversion factors apply only to future transactions.

---

## BR-008

Only Active units may be assigned to new products.

Inactive and Archived units remain available only for historical records.

---

## BR-009

Packaging units must ultimately resolve to the Base Unit through valid conversion rules.

---

# 13. Unit Conversion Engine

Nebula ERP performs all quantity calculations through the Base Unit.

Example:

```
1 Carton

↓

10 Boxes

↓

120 Pieces
```

Example configuration:

```
Base Unit

Piece

1 Box = 12 Pieces

1 Carton = 10 Boxes

Result:

1 Carton = 120 Pieces
```

The conversion engine must support chained conversions while preventing circular conversion paths.

---

# 14. Packaging Units

Packaging units describe how products are grouped for purchasing, storage, and sales.

Examples:

- Packet
- Box
- Bundle
- Carton
- Sack
- Drum
- Roll
- Pallet
- Container

Packaging units use conversion rules to relate back to the Base Unit.

Example:

```
1 Pallet

↓

50 Cartons

↓

500 Boxes

↓

6000 Pieces
```

---

# 15. Decimal Precision

Each unit defines its supported decimal precision.

Examples:

| Unit | Decimal Places |
|------|----------------:|
| Piece | 0 |
| Box | 0 |
| Kilogram | 3 |
| Gram | 0 |
| Liter | 3 |
| Meter | 2 |

Operational modules should respect the configured precision during calculations and data entry.

---

# 16. Database Design

## Primary Tables

```
units

unit_groups

unit_conversions
```

Relationships:

- Unit Group → Units (1:N)
- Base Unit → Conversion Rules (1:N)
- Unit → Products (1:N)

Future versions may introduce:

```
unit_templates

unit_localizations
```

---

# 17. Validation Rules

| Field | Validation |
|--------|------------|
| Unit Name | Required, 2–100 characters |
| Unit Code | Required, unique within organization |
| Symbol | Required |
| Unit Group | Required |
| Base Unit | Must exist within the same Unit Group |
| Conversion Factor | Greater than zero |
| Decimal Precision | Integer between 0 and 6 |
| Status | Valid enum value |

Validation must occur on both the client and server.

---

# 18. Security Policies

The Units module shall enforce:

- Organization ownership validation
- Unit Group integrity
- Conversion integrity
- Immutable historical references
- Permission-based access
- Audit logging

Only authorized users may create or modify units and conversion rules.

---

# 19. Audit Events

The following actions generate audit records:

- Unit Created
- Unit Updated
- Unit Archived
- Unit Restored
- Unit Group Created
- Unit Group Updated
- Conversion Rule Created
- Conversion Rule Updated
- Conversion Rule Deleted
- Decimal Precision Changed

Each audit record should include:

- User performing the action
- Organization
- Unit
- Timestamp
- Previous Value
- New Value
- IP Address (where available)
- Device Information (where available)

---

# 20. API Summary

The Units module exposes the following primary endpoints.

| Method | Endpoint | Description |
|---------|----------|-------------|
| GET | /units | List units |
| POST | /units | Create unit |
| GET | /units/{id} | Get unit details |
| PATCH | /units/{id} | Update unit |
| DELETE | /units/{id} | Archive unit |
| POST | /units/{id}/restore | Restore archived unit |
| GET | /unit-groups | List unit groups |
| POST | /unit-groups | Create unit group |
| PATCH | /unit-groups/{id} | Update unit group |
| GET | /unit-conversions | List conversion rules |
| POST | /unit-conversions | Create conversion rule |
| PATCH | /unit-conversions/{id} | Update conversion rule |
| DELETE | /unit-conversions/{id} | Remove conversion rule |

All endpoints require authentication and appropriate authorization.

---

# 21. User Interface

The Units module consists of the following screens.

## Unit List

Displays:

- Unit Code
- Unit Name
- Symbol
- Unit Group
- Base Unit
- Decimal Precision
- Status

Supports:

- Search
- Filtering
- Sorting
- Bulk Actions

---

## Unit Details

Displays:

- General Information
- Unit Group
- Conversion Rules
- Decimal Precision
- Products Using This Unit
- Audit History

---

## Unit Groups

Allows administrators to:

- Create Unit Groups
- Define Base Units
- View Group Members
- Configure Conversion Rules

---

## Conversion Rule Editor

Allows administrators to:

- Create Conversion Rules
- Modify Conversion Rules
- Test Conversions
- View Conversion Chains

The interface should prevent invalid or circular conversions.

---

# 22. Conversion Examples

Example 1

```
1 Box = 12 Pieces

Purchase:

5 Boxes

↓

Inventory:

60 Pieces
```

---

Example 2

```
1 Carton = 24 Bottles

Receive:

8 Cartons

↓

Inventory:

192 Bottles
```

---

Example 3

```
1 Kilogram = 1000 Grams

Inventory:

2.75 Kilograms

↓

2750 Grams
```

All operational modules must use the same conversion engine to ensure consistency.

---

# 23. Reports

Example reports:

- Unit List
- Unit Group Summary
- Conversion Rule Report
- Products by Unit
- Archived Units
- Unit Usage Analysis

Reports should support:

- PDF Export
- Excel Export
- CSV Export
- Printing

---

# 24. Error Scenarios

| Scenario | Expected Result |
|----------|-----------------|
| Duplicate unit code | Validation error |
| Duplicate symbol within the same unit group (if uniqueness is enforced) | Validation error |
| Invalid conversion factor | Validation error |
| Cross-group conversion attempt | Operation rejected |
| Circular conversion detected | Operation rejected |
| Unit not found | 404 Not Found |
| Unauthorized modification | 403 Forbidden |
| Attempt to archive a base unit while dependent units exist | Operation rejected |

Error messages should clearly describe the issue without exposing internal implementation details.

---

# 25. Acceptance Criteria

The Units module is complete when:

- Units can be created, updated, archived, and restored.
- Unit groups function correctly.
- Base units are enforced.
- Conversion rules are validated.
- Packaging units convert correctly.
- Decimal precision is respected throughout the system.
- Reports generate successfully.
- Administrative actions generate audit records.
- APIs comply with project standards.

---

# 26. Future Enhancements

Potential future capabilities:

- Industry-specific unit templates
- AI-assisted unit suggestions
- Formula-based conversions
- Density-based conversions
- Temperature conversions
- Regional measurement profiles
- Multi-language unit names
- GS1 packaging integration
- Manufacturing-specific measurement systems

---

# 27. AI Context Summary

## Summary

The Units module provides the standardized measurement system for Nebula ERP. It manages unit groups, base units, conversion rules, decimal precision, and packaging units to ensure consistent quantity calculations across Products, Inventory, Purchasing, Sales, POS, Manufacturing, and Reporting.

## Dependencies

- Organization
- Products

## Dependent Modules

- Inventory
- Warehouses
- Purchasing
- Sales
- POS
- Manufacturing (Future)
- Reporting

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial units module specification |