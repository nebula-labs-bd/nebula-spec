# Module 012 — Purchasing

| Field | Value |
|-------|-------|
| Module ID | MOD-012 |
| Name | Purchasing |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

The Purchasing module manages the complete procurement lifecycle within Nebula ERP.

It supports purchase requisitions, requests for quotation (RFQ), supplier quotations, purchase orders, goods receipts, purchase returns, landed costs, supplier invoicing, approval workflows, and procurement analytics.

Every procurement activity should be traceable from the initial request through final payment.

---

# 2. Objectives

The Purchasing module must:

- Manage purchase requisitions
- Support approval workflows
- Support Requests for Quotation (RFQ)
- Record supplier quotations
- Generate purchase orders
- Receive goods
- Handle partial deliveries
- Process purchase returns
- Allocate landed costs
- Support supplier invoice matching
- Maintain procurement history
- Generate purchasing analytics

---

# 3. Scope

This module manages:

- Purchase Requisitions
- RFQs
- Supplier Quotations
- Purchase Orders
- Goods Receipts (GRN)
- Purchase Returns
- Landed Costs
- Supplier Invoice Matching
- Procurement Approvals

This module does **not** manage:

- Supplier Master Records
- Inventory Valuation
- Accounting Journal Entries
- Supplier Payments

These responsibilities belong to their respective modules.

---

# 4. Business Objectives

Organizations should be able to purchase goods efficiently while maintaining full visibility into procurement activities, supplier performance, inventory receipts, and financial obligations.

The system should support organizations ranging from small businesses with simple purchasing requirements to enterprises operating complex procurement workflows.

---

# 5. Actors

Primary actors:

- Organization Administrator
- Purchasing Manager
- Purchasing Officer

Secondary actors:

- Warehouse Manager
- Inventory Manager
- Accountant
- Finance Manager
- Department Manager (Approver)

Future versions may support supplier self-service participation in procurement workflows.

---

# 6. Functional Requirements

The module shall allow users to:

- Create Purchase Requisitions
- Approve or Reject Requisitions
- Generate RFQs
- Receive Supplier Quotations
- Compare Quotations
- Generate Purchase Orders
- Approve Purchase Orders
- Receive Goods
- Process Partial Receipts
- Process Purchase Returns
- Allocate Landed Costs
- Match Supplier Invoices
- Search Procurement Records
- Export Procurement Reports

---

# 7. Procurement Workflow

A standard procurement workflow consists of:

```
Purchase Requisition

↓

Approval

↓

Request for Quotation (RFQ)

↓

Supplier Quotations

↓

Quotation Evaluation

↓

Purchase Order

↓

Approval

↓

Goods Receipt (GRN)

↓

Supplier Invoice

↓

Three-Way Matching

↓

Payment
```

Organizations may enable or disable workflow stages according to operational policies.

---

# 8. Purchase Requisition

A Purchase Requisition (PR) represents an internal request to procure goods or services.

Each requisition stores:

- Requisition Number
- Requesting Department
- Requestor
- Priority
- Required Date
- Requested Items
- Justification
- Approval Status

Supported priorities:

- Low
- Normal
- High
- Urgent

Requisitions do not affect inventory until converted into approved purchase orders.

---

# 9. Request for Quotation (RFQ)

RFQs are issued to one or more suppliers to request pricing and commercial terms.

Each RFQ stores:

- RFQ Number
- Related Requisition
- Suppliers Invited
- RFQ Issue Date
- Closing Date
- Requested Items
- Terms & Conditions
- Status

One RFQ may receive multiple supplier quotations.

---

# 10. Supplier Quotations

Supplier quotations contain commercial offers.

Each quotation stores:

- Supplier
- RFQ Reference
- Quotation Number
- Unit Prices
- Discounts
- Taxes
- Delivery Time
- Valid Until
- Payment Terms
- Attachments

Purchasing staff may compare quotations before selecting the preferred supplier.

---

# 11. Purchase Orders

Purchase Orders (PO) represent approved purchasing commitments.

Each Purchase Order stores:

- PO Number
- Supplier
- Warehouse
- Currency
- Ordered Items
- Ordered Quantities
- Unit Prices
- Taxes
- Discounts
- Expected Delivery Date
- Payment Terms
- Approval Status

Supported Purchase Order statuses:

- Draft
- Pending Approval
- Approved
- Partially Received
- Fully Received
- Closed
- Cancelled

Approved Purchase Orders become eligible for Goods Receipt processing.

---

# 12. Business Rules

The Purchasing module enforces the following rules.

## BR-001

Every procurement document belongs to exactly one organization.

Procurement data is isolated between organizations.

---

## BR-002

Every Purchase Requisition must be created by an authorized user and belong to a department or branch.

Requisition numbering must be unique within the organization.

---

## BR-003

Purchase Orders may only be created from:

- Approved Purchase Requisitions
- Approved Supplier Quotations
- Direct Purchases (if permitted by organization policy)

Organizations may disable Direct Purchases.

---

## BR-004

Purchase Orders requiring approval cannot proceed to Goods Receipt until fully approved.

Approval workflows follow organization-defined authorization rules.

---

## BR-005

Goods Receipts cannot exceed the remaining quantity on the Purchase Order unless authorized by organization policy.

Over-receipt policies include:

- Never Allow
- Allow with Warning
- Allow with Authorization

---

## BR-006

Partial deliveries are supported.

A Purchase Order remains open until:

- Fully received
- Closed manually
- Cancelled

---

## BR-007

Purchase Returns may only reference previously received inventory.

Returned quantities cannot exceed the quantity originally received.

---

## BR-008

Supplier invoices may only be approved after successful Three-Way Matching unless organization policy permits exceptions.

---

## BR-009

Posted Goods Receipts update Inventory automatically.

Inventory adjustments must never be performed manually for normal procurement receipts.

---

## BR-010

Posted procurement documents are immutable.

Corrections require reversal documents or authorized adjustment workflows.

---

# 13. Goods Receipt (GRN)

Goods Receipt Notes (GRNs) record the physical receipt of purchased goods.

Each GRN stores:

- GRN Number
- Purchase Order Reference
- Supplier
- Warehouse
- Storage Location
- Received Quantities
- Rejected Quantities
- Batch Information
- Serial Numbers
- Receiving User
- Receipt Date

Receiving inventory automatically creates inventory movement records.

Partial receipts are fully supported.

---

# 14. Purchase Returns

Purchase Returns record inventory sent back to suppliers.

Supported return reasons include:

- Damaged Goods
- Incorrect Item
- Incorrect Quantity
- Expired Product
- Quality Failure
- Supplier Error
- Other

Each Purchase Return stores:

- Return Number
- Supplier
- Related GRN
- Returned Items
- Return Reason
- Return Quantity
- Return Date
- Approval Status

Approved returns automatically reduce inventory.

---

# 15. Three-Way Matching

Nebula ERP supports automated Three-Way Matching.

Matching compares:

Purchase Order

↓

Goods Receipt

↓

Supplier Invoice

Validation includes:

- Supplier
- Product
- Quantity
- Unit Price
- Discounts
- Taxes
- Currency

Matching outcomes:

- Matched
- Matched with Tolerance
- Mismatch
- Rejected

Tolerance percentages are configurable at the organization level.

---

# 16. Landed Costs

Landed Costs allocate additional procurement expenses to inventory.

Supported landed cost categories include:

- Freight
- Customs Duty
- Insurance
- Import Tax
- Port Charges
- Handling Fees
- Transportation
- Other Charges

Allocation methods:

- By Quantity
- By Weight
- By Volume
- By Item Value
- Equal Distribution

Allocated landed costs increase inventory valuation according to the organization's costing policy.

---

# 17. Database Design

## Primary Tables

```
purchase_requisitions

purchase_requisition_items

rfqs

rfq_suppliers

supplier_quotations

supplier_quotation_items

purchase_orders

purchase_order_items

goods_receipts

goods_receipt_items

purchase_returns

purchase_return_items

landed_costs

supplier_invoices

three_way_matching
```

Relationships:

- Supplier → Purchase Orders (1:N)
- Purchase Requisition → RFQ (1:N)
- RFQ → Supplier Quotations (1:N)
- Purchase Order → Goods Receipts (1:N)
- Goods Receipt → Purchase Returns (1:N)
- Purchase Order → Supplier Invoice (1:N)

Future versions may introduce:

```
supplier_contracts

blanket_purchase_orders

purchase_forecasts
```

---

# 18. Validation Rules

| Field | Validation |
|--------|------------|
| Supplier | Required, active supplier |
| Warehouse | Required, active warehouse |
| Purchase Order | Required for GRN |
| Quantity | Greater than zero |
| Unit Price | Zero or greater |
| Currency | Valid ISO currency code |
| Delivery Date | Valid date |
| Approval Status | Valid workflow state |
| Document Status | Valid enum value |

Validation must occur on both the client and server.

---

# 19. Security Policies

The Purchasing module shall enforce:

- Organization ownership validation
- Supplier access validation
- Role-based procurement permissions
- Approval workflow enforcement
- Warehouse authorization
- Financial data protection
- Audit logging

Only authorized users may approve requisitions, purchase orders, goods receipts, purchase returns, and supplier invoices.

---

# 20. Audit Events

The following actions generate audit records:

- Purchase Requisition Created
- Purchase Requisition Approved
- Purchase Requisition Rejected
- RFQ Issued
- Supplier Quotation Received
- Purchase Order Created
- Purchase Order Approved
- Goods Receipt Posted
- Purchase Return Created
- Purchase Return Approved
- Landed Cost Allocated
- Supplier Invoice Matched
- Three-Way Match Completed

Each audit record should include:

- User performing the action
- Organization
- Supplier
- Document Reference
- Timestamp
- Previous Value
- New Value
- IP Address (where available)
- Device Information (where available)

---

# 21. API Summary

The Purchasing module exposes the following primary endpoints.

| Method | Endpoint | Description |
|---------|----------|-------------|
| GET | /purchase-requisitions | List purchase requisitions |
| POST | /purchase-requisitions | Create purchase requisition |
| PATCH | /purchase-requisitions/{id} | Update purchase requisition |
| POST | /purchase-requisitions/{id}/approve | Approve requisition |
| POST | /purchase-requisitions/{id}/reject | Reject requisition |
| GET | /rfqs | List RFQs |
| POST | /rfqs | Create RFQ |
| GET | /supplier-quotations | List supplier quotations |
| POST | /supplier-quotations | Record supplier quotation |
| GET | /purchase-orders | List purchase orders |
| POST | /purchase-orders | Create purchase order |
| POST | /purchase-orders/{id}/approve | Approve purchase order |
| POST | /goods-receipts | Record goods receipt |
| POST | /purchase-returns | Create purchase return |
| POST | /supplier-invoices | Record supplier invoice |
| GET | /purchasing/export | Export purchasing data |

All endpoints require authentication and appropriate authorization.

---

# 22. User Interface

The Purchasing module consists of the following screens.

## Procurement Dashboard

Displays:

- Open Requisitions
- Pending Approvals
- Open RFQs
- Purchase Orders
- Goods Awaiting Receipt
- Supplier Performance
- Procurement Spend
- Recent Activities

---

## Purchase Requisition

Allows users to:

- Create Requisitions
- Add Requested Items
- Select Priority
- Attach Supporting Documents
- Submit for Approval

Displays approval history and workflow status.

---

## RFQ Workspace

Supports:

- Supplier Selection
- RFQ Generation
- Quotation Collection
- Quotation Comparison
- Supplier Recommendation

Users may compare quotations side-by-side before creating a Purchase Order.

---

## Purchase Order

Displays:

- Supplier Information
- Ordered Items
- Pricing
- Taxes
- Discounts
- Delivery Schedule
- Approval Status
- Receipt Progress

Supports:

- Approval
- Printing
- PDF Generation
- Email to Supplier

---

## Goods Receipt

Allows warehouse users to:

- Receive Inventory
- Record Partial Deliveries
- Register Serial Numbers
- Register Batch Numbers
- Register Lot Numbers
- Reject Damaged Goods
- Record Variances

Approved receipts automatically update Inventory.

---

# 23. Search & Filtering

Purchasing should support searching by:

- Purchase Order Number
- Requisition Number
- RFQ Number
- GRN Number
- Supplier
- Product
- Warehouse
- Invoice Number

Filters should include:

- Supplier
- Warehouse
- Approval Status
- Document Status
- Date Range
- Department
- Purchaser
- Currency

Navigation should support:

- Quick Search
- Saved Filters
- Pagination
- Column Selection

---

# 24. Reports

Example reports:

- Purchase Order Report
- Procurement Spend Analysis
- Supplier Performance Report
- Goods Receipt Report
- Purchase Return Report
- Outstanding Purchase Orders
- RFQ Comparison Report
- Procurement Approval Report
- Landed Cost Report
- Three-Way Matching Exception Report

Reports should support:

- PDF Export
- Excel Export
- CSV Export
- Printing

---

# 25. Error Scenarios

| Scenario | Expected Result |
|----------|-----------------|
| Supplier not found | 404 Not Found |
| Purchase Order not approved | Goods Receipt rejected |
| Goods Receipt exceeds ordered quantity | Operation rejected or warning according to organization policy |
| Invalid warehouse | Validation error |
| Supplier invoice mismatch | Three-Way Match failed |
| Duplicate document number | Validation error |
| Unauthorized approval | 403 Forbidden |
| Purchase Return exceeds received quantity | Validation error |
| Invalid landed cost allocation | Validation error |

Error messages should clearly describe the issue while avoiding disclosure of internal implementation details.

---

# 26. Acceptance Criteria

The Purchasing module is complete when:

- Purchase Requisitions operate correctly.
- RFQs and supplier quotations function correctly.
- Purchase Orders support approval workflows.
- Goods Receipts update inventory automatically.
- Purchase Returns function correctly.
- Three-Way Matching validates procurement accurately.
- Landed Costs allocate correctly.
- Search and filtering operate correctly.
- Reports generate successfully.
- Administrative actions generate audit records.
- APIs comply with project standards.

---

# 27. Future Enhancements

Potential future capabilities:

- AI supplier recommendation
- Predictive procurement planning
- Blanket Purchase Orders
- Vendor Self-Service Portal
- Electronic Data Interchange (EDI)
- Automatic RFQ generation
- Contract-based purchasing
- Procurement budget forecasting
- Mobile procurement approvals
- Digital supplier onboarding

---

# 28. AI Context Summary

## Summary

The Purchasing module manages the complete procurement lifecycle within Nebula ERP, from Purchase Requisitions through RFQs, Supplier Quotations, Purchase Orders, Goods Receipts, Purchase Returns, Landed Costs, Supplier Invoices, and Three-Way Matching. It integrates tightly with Suppliers, Inventory, Warehouses, Accounting, and Payments while maintaining complete procurement traceability.

## Dependencies

- Organization
- Users & Roles
- Suppliers
- Products
- Inventory
- Warehouses

## Dependent Modules

- Inventory
- Accounting
- Payments
- Reports
- Notifications

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial purchasing module specification |