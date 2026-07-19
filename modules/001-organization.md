# Module 001 — Organization

| Field | Value |
|-------|-------|
| Module ID | MOD-001 |
| Name | Organization |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

The Organization module is the foundation of Nebula ERP.

Every record stored in the system belongs to an organization. It provides tenant isolation, company configuration, licensing, branding, and global settings.

No business module should exist independently of an organization.

---

# 2. Objectives

The Organization module must:

- Support multiple organizations.
- Isolate organization data.
- Store company information.
- Store global configuration.
- Support multiple branches.
- Support future SaaS deployments.
- Support self-hosted deployments.
- Provide branding settings.
- Manage subscription and licensing information.

---

# 3. Scope

This module manages:

- Company Profile
- Organization Settings
- Branding
- Localization
- Currency
- Time Zone
- Fiscal Year
- Business Information
- License Information
- Feature Flags

This module does **not** manage:

- Users
- Authentication
- Products
- Inventory
- Sales
- Accounting

Those are handled by their respective modules.

---

# 4. Business Objectives

The system should allow an administrator to configure the organization once and have those settings automatically apply throughout the ERP.

Examples include:

- Company name
- Logo
- Currency
- Date format
- Time zone
- Language
- Tax configuration

---

# 5. Actors

Primary actors:

- Super Administrator
- Organization Administrator

Secondary actors:

- System Administrator
- Support Engineer (optional)

Regular employees should not modify organization settings unless granted explicit permissions.

---

# 6. Functional Requirements

The module shall allow:

- Create an organization.
- View organization details.
- Update organization information.
- Upload company logo.
- Configure localization.
- Configure financial defaults.
- Configure regional settings.
- Configure business identity.
- Enable or disable optional features.

---

# 7. Organization Profile

Each organization stores:

- Legal Name
- Display Name
- Registration Number
- Tax Identification Number
- Business Type
- Industry
- Email
- Phone
- Website
- Address
- Country
- State / Division
- City
- Postal Code

---

# 8. Branding

Organization branding includes:

- Logo
- Favicon
- Primary Color
- Secondary Color
- Company Seal (future)
- Email Signature (future)

Branding should automatically appear throughout the application where applicable.

---

# 9. Localization

Each organization configures:

- Language
- Time Zone
- Currency
- Date Format
- Time Format
- Number Format

All users inherit these settings by default unless personal preferences override them.

---

# 10. Fiscal Configuration

Organization-wide financial settings include:

- Fiscal Year Start
- Fiscal Year End
- Accounting Method
- Default Tax Rules
- Invoice Number Prefix
- Purchase Number Prefix
- Sales Number Prefix
- Quotation Prefix

These values are referenced by Accounting, Purchasing, and Sales modules.

---

# 11. Dependencies

This module is required before:

- Authentication
- Branches
- Warehouses
- Products
- Inventory
- Sales
- Purchasing
- Accounting

Without an organization, the ERP cannot function.

---

# 12. Business Rules

The Organization module enforces the following rules.

## BR-001

Every record in Nebula ERP must belong to exactly one organization.

---

## BR-002

An organization cannot be permanently deleted while business records exist.

---

## BR-003

Organization settings affect all branches unless explicitly overridden.

---

## BR-004

The organization's base currency cannot be changed after financial transactions have been recorded unless a controlled migration process is performed.

---

## BR-005

Fiscal year settings affect Accounting, Sales, Purchasing, Inventory Valuation, and Financial Reports.

---

## BR-006

Only Organization Administrators may modify organization settings.

---

## BR-007

Feature Flags determine whether optional modules are available.

---

## BR-008

An organization must always have at least one active administrator.

---

# 13. Permission Matrix

| Action | Super Admin | Org Admin | Manager | Staff |
|----------|:----------:|:---------:|:-------:|:-----:|
| View Organization | ✓ | ✓ | ✓ | ✗ |
| Edit Organization | ✓ | ✓ | ✗ | ✗ |
| Upload Logo | ✓ | ✓ | ✗ | ✗ |
| Configure Currency | ✓ | ✓ | ✗ | ✗ |
| Configure Fiscal Year | ✓ | ✓ | ✗ | ✗ |
| Manage Feature Flags | ✓ | ✓ | ✗ | ✗ |
| Manage License | ✓ | ✗ | ✗ | ✗ |

---

# 14. Multi-Tenancy Model

Nebula ERP is designed as a multi-tenant application.

Each tenant represents one organization.

```
Platform

├── Organization A

│   ├── Branch 1

│   ├── Branch 2

│   └── Branch 3

│

├── Organization B

│   ├── Branch 1

│   └── Branch 2

│

└── Organization C
```

Each organization's data is completely isolated.

No organization should be able to access another organization's data.

---

# 15. Organization Lifecycle

```
Create Organization

↓

Configure Settings

↓

Create Branches

↓

Invite Administrators

↓

Configure Modules

↓

Go Live

↓

Operate Business

↓

Archive (optional)
```

Organizations should never skip the initial configuration process.

---

# 16. Database Design

## Primary Table

```
organizations
```

Main fields include:

```
id

legal_name

display_name

registration_number

tax_number

business_type

industry

email

phone

website

logo_url

primary_color

secondary_color

currency_code

language_code

timezone

date_format

time_format

fiscal_year_start

fiscal_year_end

is_active

created_at

updated_at

created_by

updated_by
```

Additional configuration tables may be introduced as the platform evolves.

---

# 17. Validation Rules

Examples of validation:

| Field | Validation |
|--------|------------|
| Legal Name | Required, 3–200 characters |
| Display Name | Required |
| Email | Valid email format |
| Phone | Valid international format |
| Website | Valid URL |
| Currency | Supported ISO 4217 code |
| Language | Supported language code |
| Time Zone | Valid IANA time zone |

Validation should occur both client-side and server-side.

---

# 18. Feature Flags

Organizations may enable or disable optional capabilities.

Examples:

- CRM
- Repair Management
- Warranty
- AI Assistant
- Mobile Access
- Customer Portal
- Supplier Portal
- API Access

Disabled modules should not appear in navigation or allow access through APIs.

---

# 19. License Management

The organization stores licensing information.

Fields may include:

- License Type
- License Key
- Subscription Plan
- Maximum Users
- Maximum Branches
- Maximum Warehouses
- Storage Limit
- Expiration Date

Future SaaS editions may synchronize license information with a central licensing service.

---

# 20. Audit Events

The following actions should generate audit records:

- Organization Created
- Organization Updated
- Branding Changed
- Currency Changed
- Fiscal Year Updated
- Feature Flag Changed
- License Updated
- Organization Archived

Audit logs should include:

- User
- Timestamp
- Action
- Previous Value
- New Value
- IP Address (where available)
- Device Information (where available)

---

# 21. API Summary

The Organization module exposes the following primary endpoints.

| Method | Endpoint | Description |
|---------|----------|-------------|
| GET | /organizations | List organizations *(Super Admin only)* |
| POST | /organizations | Create organization |
| GET | /organizations/{id} | Get organization details |
| PATCH | /organizations/{id} | Update organization |
| DELETE | /organizations/{id} | Archive organization |
| POST | /organizations/{id}/logo | Upload organization logo |
| GET | /organizations/{id}/settings | Retrieve organization settings |
| PATCH | /organizations/{id}/settings | Update organization settings |
| GET | /organizations/{id}/features | Retrieve feature flags |
| PATCH | /organizations/{id}/features | Update feature flags |

Authentication is required for every endpoint except the initial setup process.

---

# 22. User Interface

The Organization module consists of the following screens.

## Organization Dashboard

Displays:

- Organization Profile
- Subscription Status
- Active Branches
- Active Users
- Enabled Modules
- Recent Activity

---

## Organization Profile

Allows administrators to manage:

- Company Information
- Contact Information
- Address
- Registration Details
- Tax Information

---

## Branding

Allows administrators to configure:

- Logo
- Favicon
- Primary Color
- Secondary Color

A live preview should be displayed before saving.

---

## Localization

Allows configuration of:

- Language
- Currency
- Time Zone
- Date Format
- Number Format

---

## License & Subscription

Displays:

- Current Plan
- Expiration Date
- Usage Limits
- Storage Usage
- User Count
- Branch Count

---

# 23. Workflow

```
Create Organization

↓

Complete Company Profile

↓

Configure Localization

↓

Configure Fiscal Settings

↓

Upload Branding

↓

Create First Branch

↓

Invite Administrator

↓

Enable Required Modules

↓

Organization Ready
```

The onboarding wizard should guide administrators through this workflow during the first login.

---

# 24. Notifications

Examples of notifications generated by this module:

- Organization Created
- Organization Updated
- License Expiring Soon
- License Expired
- Subscription Renewed
- Feature Enabled
- Feature Disabled

Notifications may be delivered through:

- In-app notifications
- Email
- Push notifications (future)

---

# 25. Reports

Example reports:

- Organization Summary
- License Usage
- Branch Summary
- User Allocation
- Storage Utilization
- Feature Usage

Reports should support:

- PDF Export
- Excel Export
- CSV Export
- Printing

---

# 26. Error Scenarios

Examples:

| Scenario | Expected Result |
|----------|-----------------|
| Duplicate organization name | Validation error |
| Invalid currency code | Validation error |
| Invalid timezone | Validation error |
| Unauthorized update | 403 Forbidden |
| Organization not found | 404 Not Found |
| Expired license | Restricted access according to license policy |

Errors should provide clear, actionable messages without exposing internal implementation details.

---

# 27. Acceptance Criteria

The Organization module is complete when:

- Organizations can be created and updated.
- Company information is validated.
- Branding settings are applied throughout the application.
- Localization settings affect the user interface.
- Feature flags correctly enable or disable modules.
- Permissions are enforced.
- Audit events are recorded.
- APIs comply with project standards.
- Documentation is complete.

---

# 28. Future Enhancements

Potential future capabilities:

- Multiple legal entities
- Organization hierarchy
- White-label branding
- Custom domains
- Multi-currency organizations
- Organization templates
- Automated onboarding wizard
- Organization cloning
- Centralized SaaS licensing server

---

# 29. AI Context Summary

## Summary

The Organization module is the foundation of Nebula ERP. It defines tenant configuration, branding, localization, licensing, and global business settings used throughout the platform.

## Dependencies

None

## Dependent Modules

- Authentication
- Users & Roles
- Branches
- Products
- Inventory
- Purchasing
- Sales
- Accounting
- CRM
- Reporting

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial organization module specification |