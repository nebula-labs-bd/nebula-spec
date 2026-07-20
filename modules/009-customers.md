# Module 009 — Customers

| Field | Value |
|-------|-------|
| Module ID | MOD-009 |
| Name | Customers |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

The Customers module manages all customer information used throughout Nebula ERP.

It provides a centralized customer master database for Sales, POS, CRM, Accounting, Payments, Warranty, Repair, and Reporting while maintaining customer history, pricing, loyalty, and communication preferences.

Every sales transaction should reference a customer from this module.

---

# 2. Objectives

The Customers module must:

- Maintain customer master records
- Support individual and business customers
- Support multiple contacts
- Support multiple addresses
- Support customer groups
- Support credit limits
- Support payment terms
- Support tax information
- Support customer pricing
- Support loyalty programs
- Support customer communication preferences
- Support future customer portal integration

---

# 3. Scope

This module manages:

- Customer Profiles
- Customer Contacts
- Customer Addresses
- Customer Groups
- Credit Information
- Payment Terms
- Tax Information
- Loyalty Information
- Customer Status

This module does **not** manage:

- Sales Orders
- POS Transactions
- Customer Payments
- Accounting Entries
- CRM Activities

Operational modules reference customers but manage their own transactions.

---

# 4. Business Objectives

Organizations should maintain complete and accurate customer information to improve sales, customer service, financial management, and long-term customer relationships.

The system should support both small businesses with a few customers and enterprises managing millions of customer records.

---

# 5. Actors

Primary actors:

- Organization Administrator
- Sales Manager
- Sales Executive
- Customer Service Representative

Secondary actors:

- Cashier
- Accountant
- Finance Manager

Future versions may allow customers to access limited information through a Customer Portal.

---

# 6. Functional Requirements

The module shall allow users to:

- Create customers
- Update customers
- Archive customers
- Restore archived customers
- Search customers
- Filter customers
- Import customers
- Export customers
- Assign customer groups
- Configure payment terms
- Configure credit limits
- Configure tax information
- Record contacts
- Record addresses
- Configure communication preferences
- Upload customer documents

---

# 7. Customer Types

Nebula ERP supports multiple customer types.

Examples:

- Individual
- Business
- Corporate
- Government
- Educational Institution
- Non-Profit Organization
- Distributor
- Dealer
- Reseller

Organizations may define additional customer types if required.

---

# 8. Customer Information

Each customer stores:

- Customer ID (UUID)
- Customer Code
- Customer Type
- Customer Name
- Legal Name
- Display Name
- Customer Group
- Tax Identification Number (TIN/VAT)
- Business Registration Number (where applicable)
- Website
- Status

Optional fields:

- Date of Birth (Individuals)
- Company Logo
- Internal Notes

Customer Codes must be unique within an organization.

---

# 9. Contact Information

Each customer may have multiple contacts.

Each contact stores:

- Full Name
- Designation
- Department
- Email Address
- Mobile Number
- Office Number

One contact may be designated as the Primary Contact.

---

# 10. Customer Addresses

A customer may have multiple addresses.

Supported address types:

- Billing Address
- Shipping Address
- Home Address
- Office Address
- Branch Address

Each address stores:

- Country
- State / Division
- City
- Postal Code
- Full Address

Organizations may designate one default Billing Address and one default Shipping Address.

---

# 11. Financial Information

Each customer may define:

- Default Currency
- Payment Terms
- Credit Limit
- Credit Period
- Preferred Payment Method
- Default Price List

Supported payment methods:

- Cash
- Bank Transfer
- Cheque
- Mobile Financial Service
- Online Payment

These settings are referenced by Sales, POS, Accounting, and Payments.

---

# 12. Business Rules

The Customers module enforces the following rules.

## BR-001

Every customer belongs to exactly one organization.

Customer data is isolated between organizations.

---

## BR-002

Customer Codes must be unique within an organization.

Archived customer codes remain reserved and cannot be reused unless explicitly released by system configuration.

---

## BR-003

A customer may have multiple contacts.

Only one contact may be designated as the Primary Contact at any given time.

---

## BR-004

A customer may have multiple addresses.

Only one default Billing Address and one default Shipping Address may exist.

---

## BR-005

Customers referenced by sales orders, invoices, POS transactions, warranty claims, repairs, payments, or accounting entries cannot be permanently deleted.

They must be archived instead.

---

## BR-006

Inactive or Archived customers cannot be selected for new sales or POS transactions unless system configuration explicitly allows it.

Historical transactions remain available.

---

## BR-007

Changes to customer payment terms, credit limits, or pricing affect only future transactions.

Historical invoices and payments remain unchanged.

---

## BR-008

Customer loyalty balances must be automatically maintained by the system.

Manual adjustments require appropriate permissions and are recorded in the audit log.

---

## BR-009

Customer communication preferences must be respected by all notification and marketing modules.

---

# 13. Customer Groups

Organizations may classify customers into groups.

Examples:

- Retail
- Wholesale
- Corporate
- VIP
- Dealer
- Distributor
- Government
- Educational
- Employee

Customer Groups may define default:

- Price Lists
- Discount Policies
- Credit Limits
- Payment Terms
- Loyalty Rules

Organizations may create custom customer groups.

---

# 14. Loyalty Program

The system supports configurable customer loyalty programs.

Supported features include:

- Reward Points
- Cashback
- Membership Levels
- Promotional Bonuses
- Birthday Rewards
- Seasonal Campaigns

Example membership levels:

- Bronze
- Silver
- Gold
- Platinum

Each loyalty transaction should record:

- Earned Points
- Redeemed Points
- Expiration Date
- Related Sales Transaction
- Remaining Balance

Future versions may support multiple loyalty programs per organization.

---

# 15. Customer Pricing

Customers may receive customized pricing.

Supported pricing methods:

- Standard Pricing
- Customer Group Pricing
- Customer-Specific Pricing
- Contract Pricing
- Promotional Pricing
- Quantity-Based Pricing

Priority order:

1. Customer-Specific Price
2. Contract Price
3. Customer Group Price
4. Promotional Price
5. Standard Price

Sales and POS modules should automatically determine the correct selling price using this priority.

---

# 16. Database Design

## Primary Tables

```
customers

customer_contacts

customer_addresses

customer_groups

customer_loyalty_accounts

customer_loyalty_transactions

customer_price_lists

customer_documents
```

Relationships:

- Customer → Contacts (1:N)
- Customer → Addresses (1:N)
- Customer Group → Customers (1:N)
- Customer → Loyalty Transactions (1:N)
- Customer → Price Lists (1:N)
- Customer → Documents (1:N)

Future versions may introduce:

```
customer_portal_users

customer_preferences

customer_memberships
```

---

# 17. Validation Rules

| Field | Validation |
|--------|------------|
| Customer Name | Required, 2–200 characters |
| Customer Code | Required, unique within organization |
| Customer Type | Required |
| Customer Group | Required |
| Email | Valid email format |
| Mobile Number | Valid international format |
| Website | Valid URL |
| Credit Limit | Zero or greater |
| Credit Period | Zero or greater |
| Currency | Valid ISO currency code |
| Status | Valid enum value |

Validation must occur on both the client and server.

---

# 18. Security Policies

The Customers module shall enforce:

- Organization ownership validation
- Role-based access control
- Sensitive financial data protection
- Customer privacy protection
- Secure document access
- Audit logging
- Data isolation between organizations

Only authorized users may create, update, archive, restore, or modify customer records.

---

# 19. Audit Events

The following actions generate audit records:

- Customer Created
- Customer Updated
- Customer Archived
- Customer Restored
- Contact Added
- Contact Updated
- Contact Removed
- Address Added
- Address Updated
- Address Removed
- Customer Group Changed
- Credit Limit Updated
- Payment Terms Updated
- Loyalty Adjustment
- Price List Assigned
- Document Uploaded
- Document Deleted

Each audit record should include:

- User performing the action
- Organization
- Customer
- Timestamp
- Previous Value
- New Value
- IP Address (where available)
- Device Information (where available)

---

# 20. API Summary

The Customers module exposes the following primary endpoints.

| Method | Endpoint | Description |
|---------|----------|-------------|
| GET | /customers | List customers |
| POST | /customers | Create customer |
| GET | /customers/{id} | Get customer details |
| PATCH | /customers/{id} | Update customer |
| DELETE | /customers/{id} | Archive customer |
| POST | /customers/{id}/restore | Restore archived customer |
| GET | /customers/{id}/contacts | List customer contacts |
| POST | /customers/{id}/contacts | Add customer contact |
| GET | /customers/{id}/addresses | List customer addresses |
| POST | /customers/{id}/addresses | Add customer address |
| GET | /customers/{id}/loyalty | View loyalty account |
| POST | /customers/{id}/loyalty/adjust | Adjust loyalty balance |
| GET | /customers/export | Export customers |
| POST | /customers/import | Import customers |

All endpoints require authentication and appropriate authorization.

---

# 21. User Interface

The Customers module consists of the following screens.

## Customer List

Displays:

- Customer Code
- Customer Name
- Customer Type
- Customer Group
- Primary Contact
- Outstanding Balance
- Loyalty Level
- Status

Supports:

- Search
- Filtering
- Sorting
- Bulk Actions
- Export

---

## Customer Details

Displays:

- General Information
- Contacts
- Addresses
- Financial Information
- Loyalty Account
- Pricing Rules
- Sales History
- Warranty History
- Uploaded Documents
- Audit History

---

## Customer Editor

Allows authorized users to:

- Create Customers
- Update Customers
- Archive Customers
- Restore Customers
- Manage Contacts
- Manage Addresses
- Configure Credit Limits
- Configure Payment Terms
- Configure Pricing
- Upload Documents

---

## Loyalty Dashboard

Displays:

- Current Membership Level
- Available Points
- Lifetime Points
- Redemption History
- Reward Expiration
- Promotional Rewards

The dashboard should update automatically as new sales transactions occur.

---

# 22. Search & Filtering

Customers should support searching by:

- Customer Name
- Customer Code
- Contact Name
- Mobile Number
- Email Address
- Tax ID
- Business Registration Number

Filters should include:

- Customer Type
- Customer Group
- Status
- Loyalty Level
- Credit Status
- Country
- Created Date
- Updated Date

Navigation should support:

- Quick Search
- Saved Filters
- Pagination
- Column Selection

---

# 23. Reports

Example reports:

- Customer Directory
- Customer Sales Summary
- Customer Outstanding Balance
- Customer Credit Limit Report
- Loyalty Activity Report
- Customer Group Analysis
- Top Customers
- Inactive Customers
- Archived Customers

Reports should support:

- PDF Export
- Excel Export
- CSV Export
- Printing

---

# 24. Error Scenarios

| Scenario | Expected Result |
|----------|-----------------|
| Duplicate customer code | Validation error |
| Duplicate tax registration number (if uniqueness is enforced) | Validation error |
| Invalid email address | Validation error |
| Invalid website URL | Validation error |
| Credit limit exceeded during sales | Operation rejected or approval workflow triggered according to business policy |
| Customer not found | 404 Not Found |
| Unauthorized modification | 403 Forbidden |
| Attempt to archive customer with pending sales orders | Operation rejected or warning displayed according to business policy |

Error messages should clearly describe the issue while avoiding disclosure of internal implementation details.

---

# 25. Acceptance Criteria

The Customers module is complete when:

- Customers can be created, updated, archived, and restored.
- Multiple contacts and addresses are supported.
- Customer groups function correctly.
- Credit limits and payment terms are configurable.
- Loyalty programs operate correctly.
- Customer-specific pricing is supported.
- Search and filtering operate correctly.
- Reports generate successfully.
- Administrative actions generate audit records.
- APIs comply with project standards.

---

# 26. Future Enhancements

Potential future capabilities:

- Customer Self-Service Portal
- Online Account Management
- AI Customer Segmentation
- Predictive Customer Lifetime Value
- Marketing Automation Integration
- Subscription Management
- Referral Programs
- Customer Satisfaction Surveys
- Digital Contract Management

---

# 27. AI Context Summary

## Summary

The Customers module provides centralized customer management for Nebula ERP. It maintains customer profiles, contacts, addresses, financial settings, loyalty programs, and pricing while serving as the foundation for Sales, POS, CRM, Accounting, Payments, Warranty, Repair, and Reporting.

## Dependencies

- Organization
- Users & Roles

## Dependent Modules

- Sales
- POS
- CRM
- Accounting
- Payments
- Warranty
- Repair
- Reports

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial customers module specification |