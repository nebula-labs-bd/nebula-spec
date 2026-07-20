# Module 013 — Sales

| Field | Value |
|-------|-------|
| Module ID | MOD-013 |
| Name | Sales |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

The Sales module manages the complete sales lifecycle within Nebula ERP.

It supports quotations, sales orders, deliveries, invoicing, returns, credit notes, customer pricing, promotions, fulfillment, approvals, and customer sales history while integrating seamlessly with Inventory, Warehouses, CRM, Accounting, Payments, and POS.

Every revenue-generating transaction should be managed through this module.

---

# 2. Objectives

The Sales module must:

- Manage sales quotations
- Manage sales orders
- Support approval workflows
- Support customer-specific pricing
- Support discounts and promotions
- Support multi-warehouse fulfillment
- Support partial deliveries
- Support backorders
- Generate delivery orders
- Generate invoices
- Process sales returns
- Generate credit notes
- Maintain complete sales history
- Generate sales analytics

---

# 3. Scope

This module manages:

- Sales Quotations
- Sales Orders
- Delivery Orders
- Sales Invoices
- Sales Returns
- Credit Notes
- Customer Pricing
- Promotions
- Sales Approvals
- Order Fulfillment

This module does **not** manage:

- Customer Master Records
- Inventory Valuation
- Accounting Journal Entries
- Payment Collection

These responsibilities belong to their respective modules.

---

# 4. Business Objectives

Organizations should efficiently convert customer demand into fulfilled sales while maintaining accurate pricing, inventory availability, financial control, and complete customer history.

The system should support businesses ranging from retail stores to enterprise distributors with complex fulfillment operations.

---

# 5. Actors

Primary actors:

- Organization Administrator
- Sales Manager
- Sales Executive

Secondary actors:

- Warehouse Manager
- Delivery Staff
- Customer Service Representative
- Accountant
- Finance Manager

Future versions may support customer self-service ordering through an online portal.

---

# 6. Functional Requirements

The module shall allow users to:

- Create sales quotations
- Approve quotations
- Convert quotations into sales orders
- Create sales orders directly
- Approve sales orders
- Reserve inventory
- Allocate inventory
- Generate delivery orders
- Record deliveries
- Generate invoices
- Process partial deliveries
- Process backorders
- Process sales returns
- Issue credit notes
- Search sales records
- Export sales reports

---

# 7. Sales Workflow

A standard sales workflow consists of:

```
Sales Quotation

↓

Customer Approval

↓

Sales Order

↓

Approval

↓

Inventory Reservation

↓

Delivery Order

↓

Shipment

↓

Sales Invoice

↓

Payment

↓

Order Closed
```

Organizations may enable or disable workflow stages according to operational requirements.

---

# 8. Sales Quotation

A Sales Quotation represents a commercial offer provided to a customer.

Each quotation stores:

- Quotation Number
- Customer
- Quotation Date
- Expiration Date
- Currency
- Quoted Items
- Unit Prices
- Discounts
- Taxes
- Payment Terms
- Delivery Terms
- Status

Supported quotation statuses:

- Draft
- Sent
- Accepted
- Rejected
- Expired
- Cancelled

Accepted quotations may be converted into Sales Orders.

---

# 9. Sales Order

A Sales Order represents a confirmed customer purchase.

Each Sales Order stores:

- Sales Order Number
- Customer
- Warehouse
- Currency
- Ordered Items
- Ordered Quantities
- Reserved Quantities
- Unit Prices
- Discounts
- Taxes
- Expected Delivery Date
- Payment Terms
- Approval Status

Supported Sales Order statuses:

- Draft
- Pending Approval
- Approved
- Partially Fulfilled
- Fulfilled
- Closed
- Cancelled

Approved Sales Orders become eligible for fulfillment.

---

# 10. Delivery Order

Delivery Orders authorize warehouse personnel to fulfill customer orders.

Each Delivery Order stores:

- Delivery Number
- Sales Order Reference
- Warehouse
- Picking Location
- Packed Quantities
- Delivered Quantities
- Carrier
- Tracking Number
- Delivery Date
- Delivery Status

Supported delivery statuses:

- Draft
- Picking
- Packed
- Shipped
- Delivered
- Failed Delivery
- Returned

Delivery Orders automatically update inventory upon shipment according to organization policy.

---

# 11. Sales Invoice

Sales Invoices record the financial obligation created by a completed sale.

Each invoice stores:

- Invoice Number
- Customer
- Sales Order Reference
- Delivery Reference
- Invoice Date
- Due Date
- Currency
- Invoice Items
- Taxes
- Discounts
- Total Amount
- Outstanding Balance
- Invoice Status

Supported invoice statuses:

- Draft
- Posted
- Partially Paid
- Paid
- Overdue
- Cancelled

Posted invoices become available to the Payments and Accounting modules.

---

# 12. Business Rules

The Sales module enforces the following rules.

## BR-001

Every sales document belongs to exactly one organization.

Sales data is isolated between organizations.

---

## BR-002

Every Sales Order must reference an active customer.

Inactive or archived customers cannot be used for new sales unless explicitly permitted by organization policy.

---

## BR-003

Sales Orders may be created from:

- Accepted Sales Quotations
- Direct Sales Orders (if enabled)

Organizations may disable Direct Sales Orders.

---

## BR-004

Sales Orders requiring approval cannot proceed to fulfillment until fully approved.

Approval workflows follow organization-defined authorization rules.

---

## BR-005

Inventory must be available according to the organization's inventory policy before shipment.

Supported inventory policies:

- Reserve on Order
- Reserve on Approval
- Reserve on Picking
- No Automatic Reservation

---

## BR-006

Partial deliveries are supported.

A Sales Order remains open until:

- Fully delivered
- Closed manually
- Cancelled

---

## BR-007

Backorders are automatically created when ordered quantities exceed immediately available inventory, provided the organization has enabled backorder functionality.

---

## BR-008

Sales Returns may only reference previously invoiced or delivered items.

Returned quantities cannot exceed previously delivered quantities.

---

## BR-009

Credit Notes may only reference approved Sales Returns or authorized invoice corrections.

Credit Notes cannot exceed the remaining invoice value.

---

## BR-010

Posted Sales Invoices are immutable.

Corrections require Credit Notes or authorized reversal workflows.

---

# 13. Pricing & Discounts

Nebula ERP supports multiple pricing strategies.

Supported pricing sources include:

- Standard Price
- Customer Price
- Customer Group Price
- Contract Price
- Promotional Price
- Quantity Break Pricing

Discounts may be applied as:

- Line Discount
- Document Discount
- Percentage
- Fixed Amount

Pricing priority follows the Customer Pricing rules defined in the Customers module.

Every pricing adjustment should be traceable.

---

# 14. Promotions

Organizations may configure promotional campaigns.

Supported promotion types include:

- Percentage Discount
- Fixed Discount
- Buy One Get One (BOGO)
- Bundle Pricing
- Free Gift
- Quantity Discount
- Seasonal Promotion
- Coupon Code

Promotion conditions may include:

- Customer Group
- Product Category
- Product
- Order Value
- Quantity
- Sales Channel
- Date Range

When multiple promotions are applicable, the system applies organization-defined stacking rules.

---

# 15. Partial Deliveries & Backorders

Sales Orders may be fulfilled over multiple deliveries.

Each fulfillment records:

- Delivery Number
- Delivered Quantity
- Remaining Quantity
- Delivery Date
- Warehouse
- Carrier
- Tracking Information

If sufficient inventory is unavailable:

- Remaining quantities may become Backorders.
- Backorders remain linked to the original Sales Order.
- Future inventory receipts may automatically allocate stock according to organization policy.

---

# 16. Sales Returns & Credit Notes

Sales Returns record inventory returned by customers.

Supported return reasons include:

- Damaged Product
- Incorrect Product
- Wrong Quantity
- Warranty Claim
- Customer Cancellation
- Shipping Damage
- Other

Each Sales Return stores:

- Return Number
- Customer
- Invoice Reference
- Delivery Reference
- Returned Items
- Return Reason
- Return Quantity
- Return Date
- Approval Status

Approved returns automatically generate inventory movements according to return policy.

Credit Notes reduce customer balances and are forwarded to the Accounting module.

---

# 17. Database Design

## Primary Tables

```
sales_quotations

sales_quotation_items

sales_orders

sales_order_items

delivery_orders

delivery_order_items

sales_invoices

sales_invoice_items

sales_returns

sales_return_items

credit_notes

promotions

promotion_rules
```

Relationships:

- Customer → Sales Orders (1:N)
- Sales Quotation → Sales Orders (1:N)
- Sales Order → Delivery Orders (1:N)
- Delivery Order → Sales Invoices (1:N)
- Sales Invoice → Sales Returns (1:N)
- Sales Return → Credit Notes (1:N)

Future versions may introduce:

```
sales_contracts

subscription_orders

recurring_invoices
```

---

# 18. Validation Rules

| Field | Validation |
|--------|------------|
| Customer | Required, active customer |
| Warehouse | Required, active warehouse |
| Product | Required, active product |
| Quantity | Greater than zero |
| Unit Price | Zero or greater |
| Currency | Valid ISO currency code |
| Delivery Date | Valid date |
| Approval Status | Valid workflow state |
| Document Status | Valid enum value |

Validation must occur on both the client and server.

---

# 19. Security Policies

The Sales module shall enforce:

- Organization ownership validation
- Customer access validation
- Warehouse authorization
- Inventory reservation authorization
- Pricing permission control
- Discount approval rules
- Audit logging

Only authorized users may approve quotations, sales orders, deliveries, returns, invoices, and credit notes.

---

# 20. Audit Events

The following actions generate audit records:

- Sales Quotation Created
- Sales Quotation Approved
- Sales Order Created
- Sales Order Approved
- Inventory Reserved
- Delivery Order Created
- Delivery Completed
- Sales Invoice Posted
- Sales Return Created
- Sales Return Approved
- Credit Note Issued
- Promotion Applied

Each audit record should include:

- User performing the action
- Organization
- Customer
- Document Reference
- Timestamp
- Previous Value
- New Value
- IP Address (where available)
- Device Information (where available)

---

# 21. API Summary

The Sales module exposes the following primary endpoints.

| Method | Endpoint | Description |
|---------|----------|-------------|
| GET | /sales-quotations | List sales quotations |
| POST | /sales-quotations | Create sales quotation |
| PATCH | /sales-quotations/{id} | Update sales quotation |
| POST | /sales-quotations/{id}/approve | Approve quotation |
| POST | /sales-quotations/{id}/convert | Convert quotation to sales order |
| GET | /sales-orders | List sales orders |
| POST | /sales-orders | Create sales order |
| POST | /sales-orders/{id}/approve | Approve sales order |
| GET | /delivery-orders | List delivery orders |
| POST | /delivery-orders | Create delivery order |
| POST | /delivery-orders/{id}/ship | Ship delivery |
| GET | /sales-invoices | List sales invoices |
| POST | /sales-invoices | Generate sales invoice |
| POST | /sales-returns | Create sales return |
| POST | /credit-notes | Issue credit note |
| GET | /sales/export | Export sales data |

All endpoints require authentication and appropriate authorization.

---

# 22. User Interface

The Sales module consists of the following screens.

## Sales Dashboard

Displays:

- Sales Today
- Monthly Revenue
- Pending Quotations
- Open Sales Orders
- Pending Deliveries
- Outstanding Invoices
- Top Customers
- Sales Performance

---

## Sales Quotation

Allows users to:

- Create Quotations
- Select Customer
- Add Products
- Configure Pricing
- Apply Discounts
- Apply Promotions
- Submit for Approval
- Generate PDF
- Email Quotation

Displays quotation status and expiration information.

---

## Sales Order

Displays:

- Customer Information
- Ordered Items
- Reserved Inventory
- Pricing
- Taxes
- Discounts
- Delivery Schedule
- Approval Status

Supports:

- Approval
- Reservation Status
- Fulfillment Tracking
- Printing
- PDF Generation

---

## Delivery Workspace

Allows warehouse users to:

- Pick Inventory
- Pack Orders
- Generate Delivery Orders
- Record Shipments
- Track Deliveries
- Register Delivery Exceptions

Supports barcode scanning for picking and packing in future versions.

---

## Sales Invoice

Displays:

- Customer Information
- Invoice Details
- Payment Status
- Outstanding Balance
- Credit Notes
- Payment History

Supports:

- Printing
- PDF Generation
- Email Delivery

---

# 23. Search & Filtering

Sales should support searching by:

- Sales Order Number
- Quotation Number
- Invoice Number
- Delivery Number
- Customer
- Product
- SKU
- Tracking Number

Filters should include:

- Customer
- Warehouse
- Salesperson
- Order Status
- Invoice Status
- Payment Status
- Date Range
- Currency

Navigation should support:

- Quick Search
- Saved Filters
- Pagination
- Column Selection

---

# 24. Reports

Example reports:

- Sales Summary
- Revenue Analysis
- Sales by Customer
- Sales by Product
- Sales by Salesperson
- Outstanding Invoices
- Delivery Performance
- Sales Return Report
- Credit Note Report
- Promotion Effectiveness
- Gross Margin Analysis
- Backorder Report

Reports should support:

- PDF Export
- Excel Export
- CSV Export
- Printing

---

# 25. Error Scenarios

| Scenario | Expected Result |
|----------|-----------------|
| Customer not found | 404 Not Found |
| Insufficient inventory | Operation rejected or backorder created according to organization policy |
| Invalid warehouse | Validation error |
| Sales Order not approved | Fulfillment rejected |
| Duplicate document number | Validation error |
| Unauthorized approval | 403 Forbidden |
| Sales Return exceeds delivered quantity | Validation error |
| Credit Note exceeds invoice value | Validation error |
| Invalid pricing rule | Validation error |
| Promotion not applicable | Promotion rejected |

Error messages should clearly describe the issue while avoiding disclosure of internal implementation details.

---

# 26. Acceptance Criteria

The Sales module is complete when:

- Sales Quotations function correctly.
- Sales Orders support configurable approval workflows.
- Inventory reservations operate correctly.
- Delivery Orders support partial fulfillment and backorders.
- Sales Invoices integrate with Accounting and Payments.
- Sales Returns update inventory according to policy.
- Credit Notes correctly adjust customer balances.
- Pricing and promotions calculate accurately.
- Search and filtering operate correctly.
- Reports generate successfully.
- Administrative actions generate audit records.
- APIs comply with project standards.

---

# 27. Future Enhancements

Potential future capabilities:

- AI sales forecasting
- AI product recommendations
- Customer self-service ordering
- Subscription billing
- Sales territory management
- Route planning for deliveries
- Electronic signatures
- Customer order tracking portal
- Marketplace integration
- Omnichannel order management

---

# 28. AI Context Summary

## Summary

The Sales module manages the complete order-to-cash lifecycle within Nebula ERP. It provides quotations, sales orders, inventory reservation, fulfillment, delivery management, invoicing, returns, credit notes, promotions, and pricing while integrating closely with Customers, Inventory, Warehouses, CRM, Accounting, Payments, and POS.

## Dependencies

- Organization
- Users & Roles
- Customers
- Products
- Inventory
- Warehouses
- Pricing

## Dependent Modules

- POS
- CRM
- Accounting
- Payments
- Reports
- Notifications

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial sales module specification |