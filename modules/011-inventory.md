# Module 011 — Inventory

| Field | Value |
|-------|-------|
| Module ID | MOD-011 |
| Name | Inventory |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

The Inventory module is the operational core of Nebula ERP.

It manages real-time stock quantities, inventory valuation, stock movements, serial and batch tracking, reservations, adjustments, cycle counting, physical stock verification, and inventory costing across multiple warehouses.

Every inventory-affecting transaction within the ERP must be recorded through this module.

---

# 2. Objectives

The Inventory module must:

- Maintain real-time inventory balances
- Record every stock movement
- Support multiple warehouses
- Support storage location inventory
- Support serial number tracking
- Support batch and lot tracking
- Support inventory reservations
- Support stock transfers
- Support inventory adjustments
- Support physical stock counting
- Support cycle counting
- Support multiple valuation methods
- Prevent inventory inconsistencies
- Maintain a complete audit trail

---

# 3. Scope

This module manages:

- Stock Ledger
- Inventory Balances
- Stock Movements
- Serial Numbers
- Batch & Lot Tracking
- Inventory Reservations
- Inventory Adjustments
- Physical Stock Counts
- Cycle Counts
- Inventory Valuation
- Inventory Cost Layers

This module does **not** manage:

- Product Definitions
- Purchase Orders
- Sales Orders
- Accounting Journals
- Manufacturing Orders

Operational modules create inventory events, while this module records and validates them.

---

# 4. Business Objectives

Organizations should always know:

- What inventory they own
- Where inventory is stored
- How much inventory is available
- Inventory valuation
- Inventory movement history
- Inventory ownership
- Inventory traceability

The system must support businesses ranging from small retailers to enterprise-scale distribution and manufacturing environments.

---

# 5. Actors

Primary actors:

- Organization Administrator
- Inventory Manager
- Warehouse Manager
- Stock Controller

Secondary actors:

- Purchasing Officer
- Sales Manager
- Accountant
- Auditor

Future versions may support handheld warehouse devices and automated inventory systems.

---

# 6. Functional Requirements

The module shall allow users to:

- View inventory balances
- Record inventory movements
- Reserve inventory
- Release reservations
- Perform stock adjustments
- Perform warehouse transfers
- Record physical stock counts
- Execute cycle counts
- Track serial numbers
- Track batches
- Track lots
- Search inventory
- Export inventory reports

Inventory balances should update automatically from approved operational transactions.

---

# 7. Inventory Model

Inventory is tracked using the following dimensions:

- Organization
- Branch
- Warehouse
- Storage Location
- Product
- Variant
- Batch
- Lot
- Serial Number
- Ownership (Future)
- Inventory Status

Each inventory record uniquely represents stock at a specific location.

---

# 8. Inventory Status

Supported inventory statuses include:

- Available
- Reserved
- Allocated
- In Transit
- Quarantine
- Damaged
- Returned
- Expired
- Lost

Status determines whether inventory may be sold, transferred, or consumed.

---

# 9. Stock Ledger

The Stock Ledger is the immutable record of all inventory movements.

Each ledger entry stores:

- Transaction ID
- Transaction Type
- Product
- Warehouse
- Storage Location
- Quantity In
- Quantity Out
- Unit Cost
- Valuation Amount
- Reference Document
- Transaction Date
- Created By

Ledger entries must never be modified after posting.

Corrections require reversal transactions.

---

# 10. Inventory Balance

Inventory balances are calculated from the Stock Ledger.

Each balance stores:

- On Hand Quantity
- Available Quantity
- Reserved Quantity
- Allocated Quantity
- Damaged Quantity
- In Transit Quantity
- Quarantine Quantity

Balances are maintained automatically by the system.

Users cannot edit balances directly.

---

# 11. Stock Movement Types

Supported movement types include:

Inbound

- Purchase Receipt
- Sales Return
- Customer Return
- Production Output (Future)
- Stock Adjustment Increase
- Transfer Receipt

Outbound

- Sales Delivery
- Purchase Return
- Supplier Return
- Production Consumption (Future)
- Stock Adjustment Decrease
- Transfer Dispatch

Internal

- Warehouse Transfer
- Location Transfer
- Reservation
- Reservation Release
- Cycle Count Adjustment
- Physical Count Adjustment

Every movement must generate one or more immutable stock ledger entries.

---

# 12. Business Rules

The Inventory module enforces the following rules.

## BR-001

Every inventory record belongs to exactly one organization.

Inventory data is isolated between organizations.

---

## BR-002

Every inventory movement must reference:

- Organization
- Warehouse
- Product
- Quantity
- Movement Type

Additional references such as Storage Location, Batch, Lot, or Serial Number are required when configured for the product.

---

## BR-003

Inventory balances are system-calculated.

Users cannot directly edit inventory quantities.

Any correction must occur through an approved inventory transaction.

---

## BR-004

Stock Ledger entries are immutable.

Posted inventory transactions cannot be modified or deleted.

Corrections require reversal transactions followed by replacement transactions.

---

## BR-005

Products configured for Serial Number Tracking require one unique serial number for every individual unit.

Duplicate serial numbers are not permitted within the same organization.

---

## BR-006

Products configured for Batch or Lot Tracking must reference a valid Batch or Lot Number during every inventory movement.

Batch quantities must always equal the total quantity represented by their associated inventory records.

---

## BR-007

Inventory reservations reduce Available Quantity but do not reduce On Hand Quantity.

Reservations remain active until:

- Released
- Fulfilled
- Expired
- Cancelled

---

## BR-008

Negative inventory is controlled by organization policy.

Supported policies:

- Never Allow
- Allow with Warning
- Allow with Authorization
- Always Allow

The selected policy applies consistently across all inventory-affecting modules.

---

## BR-009

Inventory movements between warehouses must follow the Warehouse Transfer workflow.

Direct relocation between warehouses is prohibited.

---

## BR-010

Inventory valuation methods are organization-level settings.

Changing the valuation method affects only future inventory transactions unless a controlled migration process is executed.

---

# 13. Serial, Batch & Lot Management

Nebula ERP supports three inventory tracking methods.

## Serial Number Tracking

Used for uniquely identifiable items.

Examples:

- CCTV Cameras
- DVR/NVR
- Laptops
- Routers
- Smartphones

Each serial number stores:

- Serial Number
- Product
- Warehouse
- Storage Location
- Status
- Purchase Reference
- Sales Reference
- Warranty Status
- Current Owner (Future)

---

## Batch Tracking

Used for products manufactured or received in production batches.

Each batch stores:

- Batch Number
- Product
- Manufacturing Date
- Expiry Date
- Quantity
- Warehouse

---

## Lot Tracking

Used for traceability where production grouping is required.

Each lot stores:

- Lot Number
- Product
- Supplier
- Purchase Reference
- Manufacturing Date
- Expiry Date
- Quality Status

Organizations may enable any combination of Serial, Batch, or Lot tracking depending on product requirements.

---

# 14. Inventory Valuation

Nebula ERP supports multiple inventory valuation methods.

Supported methods:

- FIFO (First In, First Out)
- LIFO (Last In, First Out)
- Weighted Average Cost
- Standard Cost
- Moving Average Cost

The selected valuation method determines:

- Inventory Value
- Cost of Goods Sold (COGS)
- Inventory Cost Layers

Historical valuation records remain immutable after posting.

---

# 15. Reservation Engine

Inventory reservations temporarily allocate stock for operational processes.

Supported reservation sources:

- Sales Orders
- POS Layaway
- Manufacturing Orders (Future)
- Service Jobs
- Manual Reservation

Reservation information includes:

- Reservation ID
- Product
- Warehouse
- Storage Location
- Reserved Quantity
- Reserved By
- Expiration Date
- Source Document
- Status

The system automatically updates Available Quantity when reservations are created, modified, fulfilled, or released.

---

# 16. Physical Stock Count & Cycle Counting

Nebula ERP supports two inventory verification methods.

## Physical Stock Count

A complete verification of inventory within one or more warehouses.

Features:

- Count Sessions
- Freeze Options
- Variance Detection
- Approval Workflow
- Adjustment Posting

---

## Cycle Counting

Partial inventory verification performed on scheduled intervals.

Cycle count scheduling may be based on:

- ABC Classification
- Product Category
- Warehouse
- Storage Location
- Last Count Date
- Custom Schedule

Count variances require review and approval before inventory adjustments are posted.

---

# 17. Database Design

## Primary Tables

```
inventory_balances

stock_ledger

inventory_movements

inventory_reservations

inventory_serials

inventory_batches

inventory_lots

inventory_adjustments

physical_stock_counts

cycle_counts

inventory_cost_layers
```

Relationships:

- Product → Inventory Balances (1:N)
- Warehouse → Inventory Balances (1:N)
- Stock Ledger → Inventory Movements (1:N)
- Product → Serials (1:N)
- Product → Batches (1:N)
- Product → Lots (1:N)
- Physical Count → Adjustments (1:N)

Future versions may introduce:

```
inventory_ownership

inventory_consignment

inventory_quality_checks
```

---

# 18. Validation Rules

| Field | Validation |
|--------|------------|
| Product | Required, active product |
| Warehouse | Required, active warehouse |
| Storage Location | Required if location tracking is enabled |
| Quantity | Greater than zero |
| Serial Number | Required for serial-tracked products, unique within organization |
| Batch Number | Required for batch-tracked products |
| Lot Number | Required for lot-tracked products |
| Movement Type | Valid enum value |
| Valuation Method | Valid organization setting |

Validation must occur on both the client and server.

---

# 19. Security Policies

The Inventory module shall enforce:

- Organization ownership validation
- Warehouse access permissions
- Inventory movement authorization
- Negative stock policy enforcement
- Immutable stock ledger
- Inventory valuation protection
- Audit logging

Only authorized users may approve inventory adjustments, warehouse transfers, or stock count postings.

---

# 20. Audit Events

The following actions generate audit records:

- Inventory Movement Posted
- Inventory Adjustment Created
- Inventory Adjustment Approved
- Reservation Created
- Reservation Released
- Warehouse Transfer Posted
- Physical Count Started
- Physical Count Completed
- Cycle Count Started
- Cycle Count Completed
- Batch Created
- Lot Created
- Serial Registered
- Inventory Valuation Updated

Each audit record should include:

- User performing the action
- Organization
- Warehouse
- Product
- Transaction Reference
- Timestamp
- Previous Value
- New Value
- IP Address (where available)
- Device Information (where available)

---

# 21. API Summary

The Inventory module exposes the following primary endpoints.

| Method | Endpoint | Description |
|---------|----------|-------------|
| GET | /inventory | List inventory balances |
| GET | /inventory/{productId} | View inventory by product |
| GET | /stock-ledger | View stock ledger |
| GET | /inventory/movements | View inventory movements |
| POST | /inventory/adjustments | Create inventory adjustment |
| POST | /inventory/reservations | Create reservation |
| PATCH | /inventory/reservations/{id} | Update reservation |
| DELETE | /inventory/reservations/{id} | Release reservation |
| POST | /inventory/transfers | Create warehouse transfer |
| POST | /inventory/physical-counts | Start physical count |
| POST | /inventory/cycle-counts | Start cycle count |
| GET | /inventory/serials | Search serial numbers |
| GET | /inventory/batches | Search batches |
| GET | /inventory/lots | Search lots |
| GET | /inventory/export | Export inventory |

All endpoints require authentication and appropriate authorization.

---

# 22. User Interface

The Inventory module consists of the following screens.

## Inventory Dashboard

Displays:

- Total Inventory Value
- Total Stock Quantity
- Low Stock Items
- Out-of-Stock Items
- Reserved Stock
- Inventory by Warehouse
- Recent Inventory Movements

---

## Inventory List

Displays:

- Product
- SKU
- Warehouse
- Storage Location
- On Hand Quantity
- Available Quantity
- Reserved Quantity
- Unit Cost
- Inventory Value

Supports:

- Search
- Filtering
- Sorting
- Export

---

## Product Inventory Details

Displays:

- Inventory Summary
- Warehouse Balances
- Storage Locations
- Serial Numbers
- Batch Information
- Lot Information
- Reservation Summary
- Cost Layers
- Stock Ledger
- Audit History

---

## Inventory Adjustment

Allows authorized users to:

- Increase Stock
- Decrease Stock
- Record Damaged Stock
- Record Lost Stock
- Record Found Stock
- Record Write-Offs

All adjustments require a reason code and may require approval depending on organization policy.

---

## Physical Count Workspace

Supports:

- Count Sessions
- Barcode Scanning
- Variance Review
- Approval Workflow
- Adjustment Posting

---

# 23. Search & Filtering

Inventory should support searching by:

- Product Name
- SKU
- Barcode
- Warehouse
- Storage Location
- Serial Number
- Batch Number
- Lot Number

Filters should include:

- Warehouse
- Inventory Status
- Product Category
- Low Stock
- Out of Stock
- Expiring Batch
- Negative Inventory
- Created Date
- Updated Date

Navigation should support:

- Quick Search
- Saved Filters
- Pagination
- Column Selection

---

# 24. Reports

Example reports:

- Inventory Valuation Report
- Stock Ledger Report
- Stock Movement Report
- Inventory Aging Report
- Low Stock Report
- Out-of-Stock Report
- Inventory Adjustment Report
- Inventory Reservation Report
- Batch Expiration Report
- Serial Number Report
- Warehouse Inventory Report
- Physical Count Variance Report

Reports should support:

- PDF Export
- Excel Export
- CSV Export
- Printing

---

# 25. Error Scenarios

| Scenario | Expected Result |
|----------|-----------------|
| Insufficient available inventory | Operation rejected according to organization policy |
| Duplicate serial number | Validation error |
| Invalid batch or lot number | Validation error |
| Negative inventory not permitted | Operation rejected |
| Warehouse not found | 404 Not Found |
| Storage location not found | 404 Not Found |
| Inventory record not found | 404 Not Found |
| Reservation exceeds available quantity | Validation error |
| Unauthorized inventory adjustment | 403 Forbidden |
| Invalid valuation method | Operation rejected |

Error messages should clearly describe the issue while avoiding disclosure of internal implementation details.

---

# 26. Acceptance Criteria

The Inventory module is complete when:

- Real-time inventory balances are maintained.
- Every inventory movement generates immutable stock ledger entries.
- Serial, batch, and lot tracking function correctly.
- Reservations update available inventory accurately.
- Physical and cycle counts reconcile inventory correctly.
- Inventory valuation methods calculate correctly.
- Warehouse transfers update inventory appropriately.
- Search and filtering operate correctly.
- Reports generate successfully.
- Administrative actions generate audit records.
- APIs comply with project standards.

---

# 27. Future Enhancements

Potential future capabilities:

- AI demand forecasting
- Predictive replenishment
- ABC inventory optimization
- FEFO (First Expired, First Out) support
- Barcode and RFID automation
- IoT warehouse sensors
- Consignment inventory
- Vendor Managed Inventory (VMI)
- Blockchain inventory traceability
- Autonomous warehouse integration

---

# 28. AI Context Summary

## Summary

The Inventory module is the operational core of Nebula ERP. It provides real-time inventory management through immutable stock ledgers, warehouse balances, serial/batch/lot tracking, inventory reservations, valuation methods, stock counting, and audit trails. All inventory-affecting modules depend on this module for accurate and traceable stock management.

## Dependencies

- Organization
- Products
- Units
- Warehouses
- Users & Roles

## Dependent Modules

- Purchasing
- Sales
- POS
- Accounting
- Manufacturing (Future)
- Warranty
- Repair
- Reports

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial inventory module specification |