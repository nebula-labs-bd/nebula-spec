# Module 016 — Payments

| Field | Value |
|-------|-------|
| Module ID | MOD-016 |
| Name | Payments |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

The Payments module manages all incoming and outgoing monetary transactions across Nebula ERP.

It records customer receipts, supplier payments, refunds, payment allocations, bank and cash transactions, multi-currency settlements, reconciliation activities, and payment references while integrating tightly with Sales, Purchasing, POS, Accounting, and Expenses.

Every movement of money into or out of the organization should be managed through this module.

---

# 2. Objectives

The Payments module must:

- Record customer receipts
- Record supplier payments
- Support multiple payment methods
- Support payment allocation
- Support partial payments
- Support advance payments
- Process refunds
- Support bank accounts
- Support cash accounts
- Support payment reconciliation
- Support multi-currency payments
- Maintain complete payment history
- Generate payment analytics

---

# 3. Scope

This module manages:

- Customer Receipts
- Supplier Payments
- Payment Allocation
- Refunds
- Payment Methods
- Payment Terms
- Bank Accounts
- Cash Accounts
- Payment Reconciliation
- Multi-Currency Payments

This module does **not** manage:

- Sales Orders
- Purchase Orders
- Journal Entries
- Bank API Integrations

These responsibilities belong to their respective modules.

---

# 4. Business Objectives

Organizations should accurately record every payment transaction while maintaining complete financial traceability, minimizing reconciliation effort, and supporting multiple payment channels.

The system should support businesses ranging from small retailers using cash to enterprises processing high-volume electronic payments.

---

# 5. Actors

Primary actors:

- Cashier
- Accountant
- Accounts Receivable Officer
- Accounts Payable Officer

Secondary actors:

- Finance Manager
- Branch Manager
- Organization Administrator

Future versions may support customer and supplier self-service payment portals.

---

# 6. Functional Requirements

The module shall allow users to:

- Record customer receipts
- Record supplier payments
- Allocate payments to invoices
- Process advance payments
- Process partial payments
- Process refunds
- Configure payment methods
- Configure payment terms
- Manage bank accounts
- Manage cash accounts
- Perform payment reconciliation
- Search payment history
- Export payment reports

---

# 7. Payment Workflow

A standard payment workflow consists of:

```
Invoice

↓

Payment Received / Payment Issued

↓

Payment Allocation

↓

Accounting Update

↓

Bank Reconciliation

↓

Payment Closed
```

Organizations may customize payment approval workflows according to financial policies.

---

# 8. Customer Receipts

Customer Receipts record payments received from customers.

Each receipt stores:

- Receipt Number
- Customer
- Receipt Date
- Currency
- Exchange Rate
- Payment Method
- Payment Reference
- Received Amount
- Allocated Amount
- Remaining Balance
- Status

Supported statuses:

- Draft
- Pending Approval
- Posted
- Partially Allocated
- Fully Allocated
- Cancelled

Receipts may be allocated to one or multiple invoices.

---

# 9. Supplier Payments

Supplier Payments record payments issued to suppliers.

Each payment stores:

- Payment Number
- Supplier
- Payment Date
- Currency
- Exchange Rate
- Payment Method
- Payment Reference
- Paid Amount
- Allocated Amount
- Remaining Balance
- Status

Supported statuses:

- Draft
- Pending Approval
- Posted
- Partially Allocated
- Fully Allocated
- Cancelled

Supplier payments may settle one or multiple supplier invoices.

---

# 10. Payment Methods

Organizations may configure supported payment methods.

Examples include:

- Cash
- Cheque
- Credit Card
- Debit Card
- Bank Transfer
- Mobile Banking
- Digital Wallet
- Store Credit
- Gift Card

Each payment method stores:

- Name
- Type
- Default Account
- Status
- Processing Fee (Optional)

Only active payment methods may be used in new transactions.

---

# 11. Bank & Cash Accounts

The Payments module manages organizational financial accounts.

Each account stores:

- Account Name
- Account Type
- Bank Name
- Branch
- Account Number
- Currency
- Opening Balance
- Current Balance
- Status

Supported account types:

- Bank Account
- Cash Account
- Petty Cash

Account balances are updated automatically through posted payment transactions.

---

# 12. Business Rules

The Payments module enforces the following rules.

## BR-001

Every payment transaction belongs to exactly one organization.

Payment records are completely isolated between organizations.

---

## BR-002

Posted payments are immutable.

Corrections require:

- Payment Reversal
- Refund
- Adjustment Entry

Direct modification of posted payments is prohibited.

---

## BR-003

Payments may only be allocated to valid outstanding invoices.

Allocated amounts cannot exceed the invoice's outstanding balance unless overpayment is permitted by organization policy.

---

## BR-004

Partial payments are fully supported.

Invoices remain open until their outstanding balance reaches zero.

---

## BR-005

Advance payments may exist without invoice allocation.

Advance balances remain available until allocated or refunded.

---

## BR-006

Refunds may only reference previously posted customer receipts unless organization policy explicitly permits standalone refunds.

---

## BR-007

Every payment must reference a valid payment method and financial account.

Inactive payment methods or closed accounts cannot be used for new transactions.

---

## BR-008

Multi-currency payments shall record:

- Transaction Currency
- Base Currency
- Exchange Rate
- Converted Amount

Historical exchange rates remain unchanged after posting.

---

## BR-009

Bank reconciliation must never modify original payment records.

Differences are resolved using reconciliation adjustments or accounting entries.

---

## BR-010

Posting a payment automatically updates:

- Accounts Receivable or Accounts Payable
- General Ledger
- Bank or Cash Account Balance
- Audit Log

---

# 13. Payment Allocation

Payments may be allocated across one or more invoices.

Supported allocation scenarios include:

- Single Payment → Single Invoice
- Single Payment → Multiple Invoices
- Multiple Payments → Single Invoice
- Partial Allocation
- Advance Allocation

Each allocation stores:

- Allocation Number
- Payment Reference
- Invoice Reference
- Allocated Amount
- Allocation Date
- Remaining Balance
- User

Allocation history must remain fully traceable.

---

# 14. Refunds

Refunds reverse previously received customer payments.

Supported refund reasons include:

- Order Cancellation
- Product Return
- Duplicate Payment
- Overpayment
- Pricing Adjustment
- Service Cancellation
- Other

Each refund stores:

- Refund Number
- Original Payment
- Customer
- Refund Method
- Refund Amount
- Refund Date
- Reason
- Approval Status

Approved refunds automatically reduce customer balances and update financial accounts.

---

# 15. Payment Terms & Reconciliation

## Payment Terms

Organizations may configure payment terms such as:

- Cash on Delivery
- Immediate Payment
- Net 7
- Net 15
- Net 30
- Net 45
- Net 60
- Custom Terms

Each payment term stores:

- Name
- Due Days
- Early Payment Discount (Optional)
- Late Payment Penalty (Optional)
- Status

---

## Payment Reconciliation

Reconciliation compares internal payment records against external bank or cash statements.

Each reconciliation stores:

- Reconciliation Number
- Account
- Statement Period
- Opening Balance
- Closing Balance
- Matched Transactions
- Unmatched Transactions
- Variance
- Status

Supported statuses:

- Draft
- In Progress
- Completed
- Approved

Reconciliation history must be retained for audit purposes.

---

# 16. Database Design

## Primary Tables

```
customer_receipts

supplier_payments

payment_allocations

payment_methods

payment_terms

refunds

bank_accounts

cash_accounts

payment_reconciliation

payment_reconciliation_items
```

Relationships:

- Customer Receipt → Payment Allocations (1:N)
- Supplier Payment → Payment Allocations (1:N)
- Payment → Refunds (1:N)
- Bank Account → Reconciliation Sessions (1:N)
- Payment Method → Payments (1:N)

Future versions may introduce:

```
payment_gateway_transactions

bank_statement_imports

scheduled_payments

direct_debits
```

---

# 17. Validation Rules

| Field | Validation |
|--------|------------|
| Customer/Supplier | Required, active record |
| Payment Method | Required, active method |
| Financial Account | Required, active account |
| Payment Amount | Greater than zero |
| Currency | Valid ISO currency code |
| Exchange Rate | Greater than zero for foreign currency |
| Allocation Amount | Cannot exceed available balance |
| Payment Reference | Unique where required by payment method |
| Payment Date | Valid accounting date |

Validation must occur on both the client and server.

---

# 18. Security Policies

The Payments module shall enforce:

- Organization ownership validation
- Role-based payment permissions
- Payment approval authorization
- Refund authorization
- Reconciliation authorization
- Financial account authorization
- Audit logging

Only authorized users may:

- Post payments
- Reverse payments
- Approve refunds
- Complete reconciliations
- Configure payment methods
- Manage financial accounts

---

# 19. Audit Events

The following actions generate audit records:

- Customer Receipt Created
- Supplier Payment Created
- Payment Posted
- Payment Reversed
- Payment Allocated
- Payment Allocation Removed
- Refund Created
- Refund Approved
- Bank Account Created
- Cash Account Updated
- Reconciliation Started
- Reconciliation Completed
- Payment Method Updated

Each audit record should include:

- User performing the action
- Organization
- Payment Reference
- Related Customer or Supplier
- Timestamp
- Previous Value
- New Value
- IP Address (where available)
- Device Information (where available)

---

# 20. API Summary

The Payments module exposes the following primary endpoints.

| Method | Endpoint | Description |
|---------|----------|-------------|
| GET | /customer-receipts | List customer receipts |
| POST | /customer-receipts | Record customer receipt |
| GET | /supplier-payments | List supplier payments |
| POST | /supplier-payments | Record supplier payment |
| GET | /payment-allocations | List payment allocations |
| POST | /payment-allocations | Allocate payment |
| DELETE | /payment-allocations/{id} | Remove allocation |
| GET | /refunds | List refunds |
| POST | /refunds | Create refund |
| GET | /payment-methods | List payment methods |
| POST | /payment-methods | Create payment method |
| GET | /bank-accounts | List bank accounts |
| POST | /bank-accounts | Create bank account |
| GET | /payment-reconciliation | List reconciliations |
| POST | /payment-reconciliation | Create reconciliation |
| POST | /payment-reconciliation/{id}/complete | Complete reconciliation |
| GET | /payments/export | Export payment data |

All endpoints require authentication and appropriate authorization.

---

# 21. User Interface

The Payments module consists of the following screens.

## Payments Dashboard

Displays:

- Cash Position
- Bank Balances
- Customer Receipts
- Supplier Payments
- Outstanding Receivables
- Outstanding Payables
- Pending Reconciliations
- Recent Transactions

---

## Customer Receipts

Allows users to:

- Record Receipt
- Allocate Payment
- Record Advance Payment
- View Allocation History
- Print Receipt
- Email Receipt

Displays:

- Customer
- Outstanding Invoices
- Allocation Status
- Remaining Balance

---

## Supplier Payments

Allows users to:

- Record Supplier Payment
- Allocate Payment
- Record Advance Payment
- Print Payment Voucher
- View Payment History

Displays supplier balances and outstanding invoices.

---

## Payment Reconciliation

Allows users to:

- Select Financial Account
- Match Transactions
- Resolve Variances
- Complete Reconciliation
- Review Reconciliation History

Supports manual reconciliation and future automated reconciliation.

---

## Financial Accounts

Allows users to:

- Manage Bank Accounts
- Manage Cash Accounts
- Configure Payment Methods
- Configure Payment Terms
- Review Account Balances

---

# 22. Search & Filtering

Payments should support searching by:

- Receipt Number
- Payment Number
- Refund Number
- Customer
- Supplier
- Payment Reference
- Bank Account
- Transaction ID

Filters should include:

- Payment Method
- Currency
- Payment Status
- Allocation Status
- Reconciliation Status
- Branch
- Date Range

Navigation should support:

- Quick Search
- Saved Filters
- Pagination
- Column Selection

---

# 23. Reports

Example reports:

- Customer Receipt Report
- Supplier Payment Report
- Outstanding Receivables
- Outstanding Payables
- Payment Allocation Report
- Refund Report
- Bank Reconciliation Report
- Cash Account Report
- Payment Method Analysis
- Daily Cash Summary
- Multi-Currency Payment Report

Reports should support:

- PDF Export
- Excel Export
- CSV Export
- Printing

---

# 24. Error Scenarios

| Scenario | Expected Result |
|----------|-----------------|
| Customer or supplier not found | 404 Not Found |
| Inactive payment method | Validation error |
| Closed financial account | Validation error |
| Allocation exceeds invoice balance | Validation error |
| Invalid exchange rate | Validation error |
| Duplicate payment reference | Validation error where uniqueness is required |
| Unauthorized payment approval | 403 Forbidden |
| Reconciliation already completed | Operation rejected |
| Refund exceeds original payment | Validation error |
| Invalid accounting period | Posting rejected |

Error messages should clearly describe the issue while avoiding disclosure of internal implementation details.

---

# 25. Acceptance Criteria

The Payments module is complete when:

- Customer receipts are recorded correctly.
- Supplier payments are processed correctly.
- Payment allocations support partial and full settlements.
- Advance payments are supported.
- Refunds correctly update customer balances.
- Bank and cash account balances update automatically.
- Payment reconciliation functions correctly.
- Multi-currency payments calculate accurately.
- Reports generate successfully.
- Administrative actions generate audit records.
- APIs comply with project standards.

---

# 26. Future Enhancements

Potential future capabilities:

- Online payment gateway integration
- Automatic bank statement import
- Open Banking integration
- Scheduled recurring payments
- Direct debit support
- QR code payments
- AI fraud detection
- AI cash flow prediction
- Payment approval via mobile app
- Virtual bank accounts

---

# 27. AI Context Summary

## Summary

The Payments module centralizes all incoming and outgoing financial transactions within Nebula ERP. It manages customer receipts, supplier payments, payment allocations, refunds, financial accounts, reconciliation, and multi-currency settlements while integrating with Sales, Purchasing, POS, Accounting, Expenses, and future banking integrations.

## Dependencies

- Organization
- Users & Roles
- Customers
- Suppliers
- Sales
- Purchasing
- POS
- Accounting

## Dependent Modules

- Expenses
- Reports
- Notifications
- Audit Log
- Integrations

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial payments module specification |