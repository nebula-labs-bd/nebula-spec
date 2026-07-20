# Implementation Document 006 — Authentication & RBAC

| Field | Value |
|-------|-------|
| Document ID | IMP-006 |
| Name | Authentication & RBAC |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

Authentication is the security foundation of Nebula ERP.

Every module depends on a secure identity system that guarantees users can only access organizations, resources, and operations they are explicitly authorized to use.

This document defines:

- Identity architecture
- Authentication
- Authorization
- RBAC
- Tenant isolation
- Session management
- Security policies
- Audit logging
- Backend implementation
- Frontend implementation

---

# 2. Objectives

The authentication system must:

- Support multi-tenancy
- Support multiple organizations
- Scale to enterprise deployments
- Support millions of users
- Remain stateless
- Support horizontal scaling
- Minimize attack surface
- Support future SSO integration
- Support MFA
- Support API authentication
- Provide complete auditability

---

# 3. Core Principles

Authentication must follow:

- Zero Trust
- Least Privilege
- Defense in Depth
- Secure by Default
- Explicit Authorization
- Immutable Audit Trail

Authorization decisions are never made on the frontend.

The backend is always authoritative.

---

# 4. Authentication Architecture

```text
Browser

↓

Login

↓

API

↓

Authentication Service

↓

Prisma

↓

PostgreSQL

↓

JWT Issued

↓

Frontend Stores Tokens

↓

Authenticated Requests
```

Every protected request follows:

```text
Access Token

↓

JWT Validation

↓

Organization Resolution

↓

Permission Resolution

↓

Business Logic

↓

Response
```

---

# 5. Identity Model

Identity hierarchy:

```text
Platform

↓

Organization

↓

Department

↓

Role

↓

User
```

Definitions:

Platform
: Entire Nebula ERP deployment.

Organization
: Independent tenant.

Department
: Logical subdivision.

Role
: Collection of permissions.

User
: Authenticated identity.

---

# 6. User Lifecycle

```text
Invitation

↓

Registration

↓

Email Verification

↓

Active

↓

Password Changes

↓

Role Updates

↓

Suspended

↓

Archived
```

User states:

| State | Description |
|--------|-------------|
| Pending | Awaiting activation |
| Active | Can authenticate |
| Locked | Temporary security lock |
| Suspended | Administrative action |
| Archived | Historical record only |

---

# 7. User Entity

Core fields:

```text
id

organization_id

email

username

password_hash

first_name

last_name

status

email_verified

last_login

failed_login_attempts

locked_until

created_at

updated_at
```

Passwords are never stored.

Only password hashes.

---

# 8. Organization Membership

Users belong to organizations.

```text
Organization

↓

Members

↓

Roles

↓

Permissions
```

A user may belong to multiple organizations.

Example:

```text
John

↓

Company A

↓

Inventory Manager

---------------------

John

↓

Company B

↓

Sales Manager
```

Permissions are evaluated independently per organization.

---

# 9. Authentication Methods

Supported methods:

- Email + Password
- Username + Password
- API Tokens
- Service Accounts

Future:

- Microsoft OAuth
- Google OAuth
- GitHub OAuth
- Azure AD
- LDAP
- SAML
- Passkeys

The architecture must remain provider-agnostic.

---

# 10. Password Policy

Minimum requirements:

Length

```text
12 characters
```

Must contain:

- Uppercase
- Lowercase
- Number
- Special Character

Cannot contain:

- Email
- Username
- Company name
- Common passwords

Password history:

```text
Last 5 passwords
```

Expiration:

Optional.

Configured per organization.

---

# 11. Password Hashing

Algorithm:

```text
Argon2id
```

Reasons:

- Memory hard
- GPU resistant
- OWASP recommendation

Never use:

- MD5
- SHA1
- Unsalted SHA256

Password verification flow:

```text
Password

↓

Argon2 Verify

↓

Success

↓

Issue Tokens
```

---

# 12. JWT Architecture

Two-token model.

```text
Access Token

+

Refresh Token
```

Access Token:

Purpose

```text
Authentication
```

Lifetime:

```text
15 minutes
```

Refresh Token

Purpose:

```text
Session Renewal
```

Lifetime:

```text
30 days
```

Tokens contain:

```text
User ID

Organization ID

Role IDs

Permission Version

Session ID
```

Never include:

- Password
- Secrets
- Personal sensitive information

---

# 13. Session Management

Each login creates a unique session.

Session entity:

```text
Session ID

↓

Device

↓

IP

↓

Browser

↓

Issued At

↓

Last Activity

↓

Refresh Token

↓

Expires
```

Users can view active sessions.

Users may revoke sessions individually.

Administrators may revoke all sessions.

---

# 14. Refresh Token Rotation

Every refresh generates:

```text
Old Refresh Token

↓

Invalidated

↓

New Refresh Token

↓

Stored

↓

Returned
```

Benefits:

- Replay protection
- Stolen token detection
- Better auditing

Compromised refresh tokens invalidate the entire session.

---

# 15. Login Flow

```text
User

↓

Email + Password

↓

Validation

↓

Rate Limit Check

↓

Password Verify

↓

Account Status

↓

Organization Resolution

↓

Generate Tokens

↓

Create Session

↓

Audit Log

↓

Return Response
```

Successful response:

```json
{
  "accessToken": "...",
  "refreshToken": "...",
  "expiresIn": 900,
  "user": {},
  "organization": {}
}
```

---

# 16. Logout Flow

```text
Client

↓

Logout

↓

Delete Refresh Token

↓

Invalidate Session

↓

Audit Event

↓

Success
```

Logout from:

- Current device
- All devices
- Selected devices

must all be supported.

---

# 17. Password Reset

Flow:

```text
Forgot Password

↓

Email Token

↓

Verification

↓

New Password

↓

Invalidate Sessions

↓

Login Again
```

Reset tokens:

- Single use
- Short expiration
- Cryptographically random
- Stored hashed

---

# 18. Email Verification

Flow:

```text
Register

↓

Verification Email

↓

Signed Token

↓

Click Link

↓

Verify

↓

Activate Account
```

Verification tokens expire automatically.

Unverified users have restricted access until activation.

---

# 19. Account Lockout

Protection against brute-force attacks.

Policy:

```text
5 failed attempts

↓

Lock Account

↓

15 minutes
```

Administrative unlock is supported.

Repeated lockouts should generate security alerts.

---

# 20. Brute Force Protection

Protection layers:

- IP rate limiting
- Account rate limiting
- CAPTCHA (optional)
- Progressive delays
- Audit logging
- Threat monitoring

Authentication endpoints should have stricter limits than general API endpoints.

---

# 21. Multi-Factor Authentication (MFA)

Nebula ERP must be designed with MFA support from the beginning, even if it is not enabled by default.

Supported methods:

- TOTP (Authenticator Apps)
- Email OTP
- Recovery Codes

Future support:

- WebAuthn
- Passkeys
- Hardware Security Keys (YubiKey)
- Biometric Authentication

Authentication flow:

```text
Login

↓

Password Verified

↓

MFA Required?

↓

Yes

↓

Verify OTP

↓

Issue JWT

↓

Authenticated
```

MFA should be configurable at multiple levels:

- Platform-wide
- Organization
- Individual User

---

# 22. Role-Based Access Control (RBAC)

Nebula ERP uses Role-Based Access Control as its primary authorization mechanism.

Hierarchy:

```text
Organization

↓

Roles

↓

Permissions

↓

Users
```

Users never receive permissions directly unless explicitly supported for exceptional cases.

Roles contain collections of permissions.

Users inherit permissions from assigned roles.

---

# 23. Built-in Roles

Every organization is created with default system roles.

| Role | Purpose |
|------|----------|
| Owner | Full control |
| Administrator | Organization administration |
| Manager | Department management |
| Employee | Standard user |
| Viewer | Read-only access |

Platform-only roles:

- Super Admin
- Platform Support
- Platform Auditor

These roles cannot exist inside tenant organizations.

---

# 24. Permission Model

Permission naming follows a consistent convention.

```
module.action
```

Examples:

```
users.read
users.create
users.update
users.delete

inventory.read
inventory.create
inventory.update
inventory.delete

sales.read
sales.create
sales.cancel

purchase.approve

finance.approve

crm.update

hr.manage

reports.export

ai.use

system.admin
```

Permissions must remain human-readable and version controlled.

---

# 25. Permission Categories

Permissions are grouped logically.

Example:

```text
Users
├── Read
├── Create
├── Update
└── Delete

Inventory
├── Read
├── Receive
├── Transfer
├── Adjust
└── Delete

Sales
├── Create
├── Invoice
├── Refund
└── Cancel
```

Grouping improves administration and auditing.

---

# 26. Permission Resolution

Authorization follows this sequence:

```text
Authenticated?

↓

Organization Valid?

↓

User Active?

↓

Role Loaded?

↓

Permission Exists?

↓

Allowed?
```

Access is denied immediately when any validation fails.

---

# 27. Authorization Strategy

Authorization is always enforced on the backend.

The frontend may hide UI elements for usability, but backend APIs remain the source of truth.

Every protected endpoint performs:

- Identity validation
- Session validation
- Tenant validation
- Permission validation

before business logic executes.

---

# 28. Multi-Tenant Authorization

Nebula ERP is a strict multi-tenant platform.

Users may belong to multiple organizations.

Example:

```text
Alice

↓

Company A

↓

Finance Manager

---------------------

Alice

↓

Company B

↓

Inventory Viewer
```

Permissions are isolated by organization.

No request may access another organization's data unless executed by a Platform Super Admin.

---

# 29. Tenant Isolation

Every business record must contain:

```
organization_id
```

Every query must filter by:

```
organization_id
```

Example:

```sql
SELECT *
FROM inventory_items
WHERE organization_id = ?
```

No API may return records from another tenant.

This rule applies to:

- Inventory
- Sales
- CRM
- HR
- Finance
- AI Memory
- Notifications
- Files

Everything is tenant scoped.

---

# 30. Authentication Guards

NestJS Guards enforce authorization.

Examples:

```
JwtAuthGuard

RefreshTokenGuard

PermissionGuard

OrganizationGuard

SuperAdminGuard
```

Execution order:

```text
JWT Guard

↓

Organization Guard

↓

Permission Guard

↓

Controller
```

---

# 31. Custom Decorators

Custom decorators simplify permission checking.

Examples:

```typescript
@RequirePermission("inventory.read")

@RequirePermission("sales.create")

@Organization()

@CurrentUser()
```

Decorators should avoid duplicated authorization logic.

---

# 32. API Authentication

Protected requests use:

```
Authorization: Bearer <AccessToken>
```

Access token validation includes:

- Signature verification
- Expiration
- Session validation
- Organization validation
- Permission version

Invalid tokens return:

```
401 Unauthorized
```

Insufficient permissions return:

```
403 Forbidden
```

---

# 33. Security Events

The authentication service must generate events for:

- Login Success
- Login Failure
- Password Changed
- Password Reset
- Email Verified
- MFA Enabled
- MFA Disabled
- Account Locked
- Account Unlocked
- Session Created
- Session Revoked
- Token Refreshed

These events feed the audit log and monitoring systems.

---

# 34. Audit Logging

Every authentication action creates an immutable audit record.

Audit fields:

```
User ID

Organization ID

Action

Timestamp

IP Address

Device

Browser

Success

Failure Reason
```

Audit records are append-only.

They cannot be modified by application users.

---

# 35. Session Tracking

Each login session stores:

- Session ID
- User
- Organization
- Device Name
- Browser
- Operating System
- IP Address
- Country (optional)
- Created At
- Last Activity
- Expires At

Users can review active sessions from their profile.

---

# 36. Concurrent Sessions

Organizations may configure:

- Unlimited sessions
- Maximum concurrent sessions
- Single active session only

When the limit is exceeded:

```text
Newest Login

↓

Oldest Session Revoked
```

or

```
Reject Login
```

depending on organization policy.

---

# 37. Forced Logout

Administrators may revoke:

- One session
- All user sessions
- Entire organization sessions

Triggers include:

- Password reset
- Employee termination
- Security incident
- Device compromise

Session revocation invalidates all refresh tokens immediately.

---

# 38. Inactive Timeout

Organizations configure inactivity timeout.

Example:

```
15 minutes

30 minutes

1 hour

4 hours
```

After timeout:

```text
Inactive

↓

Access Token Expired

↓

Re-authentication Required
```

Sensitive operations may require password confirmation regardless of session age.

---

# 39. Authentication APIs

Core endpoints:

```
POST   /auth/login

POST   /auth/logout

POST   /auth/refresh

POST   /auth/register

POST   /auth/forgot-password

POST   /auth/reset-password

POST   /auth/change-password

POST   /auth/verify-email

GET    /auth/me

GET    /auth/sessions

DELETE /auth/sessions/:id
```

All responses follow the standardized API response format defined in previous implementation documents.

---

# 40. Error Handling

Common authentication errors:

| Status | Description |
|---------|-------------|
|400|Validation error|
|401|Invalid credentials|
|401|Expired token|
|401|Invalid refresh token|
|403|Permission denied|
|403|Organization disabled|
|404|User not found|
|409|Email already exists|
|423|Account locked|
|429|Too many login attempts|

Error responses must never expose sensitive implementation details.

---

# 41. Authentication Testing Strategy

Authentication is a critical security component and must achieve high test coverage.

## Unit Tests

Test the following:

- Password hashing
- Password verification
- JWT generation
- JWT validation
- Refresh token generation
- Refresh token rotation
- Permission resolution
- Role inheritance
- Session creation
- Session revocation
- Lockout logic
- Rate limiter

Target Coverage:

```
95%+
```

---

## Integration Tests

Validate complete workflows:

- Register → Verify Email
- Login → Access Protected Route
- Login → Refresh Token
- Login → Logout
- Forgot Password → Reset Password
- Password Change → Session Revocation
- Organization Switching
- Role Assignment
- Permission Changes
- MFA Verification

---

## Security Tests

Validate protection against:

- SQL Injection
- XSS
- CSRF
- JWT Tampering
- Expired Tokens
- Invalid Signatures
- Replay Attacks
- Refresh Token Reuse
- Session Hijacking
- Credential Stuffing
- Brute Force Attacks

---

# 42. Performance Targets

Authentication should remain fast even under heavy load.

| Operation | Target |
|-----------|---------|
| Login | < 300 ms |
| Refresh Token | < 150 ms |
| JWT Validation | < 20 ms |
| Permission Resolution | < 10 ms |
| Session Lookup | < 30 ms |

System must support:

- 10,000+ concurrent users
- Horizontal scaling
- Stateless API nodes
- Shared session persistence

---

# 43. Security Best Practices

The authentication system must follow OWASP recommendations.

## Passwords

- Argon2id hashing
- Strong password policy
- Password history
- Password reset tokens
- No plaintext passwords

## Tokens

- Short-lived access tokens
- Rotating refresh tokens
- Token revocation
- Secure signing keys
- Key rotation support

## Cookies

If cookies are used:

- HttpOnly
- Secure
- SameSite=Lax or Strict

For SPA deployments, Authorization Bearer tokens are preferred.

---

# 44. Logging & Monitoring

Authentication metrics should be exported.

Metrics include:

- Successful logins
- Failed logins
- Locked accounts
- Active sessions
- Refresh token usage
- Token revocations
- MFA usage
- Average authentication latency

These metrics feed the monitoring stack defined in previous implementation documents.

---

# 45. Future Enhancements

The architecture should support future authentication methods without major redesign.

Planned enhancements:

- Passkeys (WebAuthn)
- FIDO2
- Microsoft Entra ID
- Google Workspace
- GitHub OAuth
- LDAP
- Active Directory
- SAML 2.0
- SCIM User Provisioning
- API Keys
- Machine-to-Machine Authentication
- Single Sign-On (SSO)

---

# 46. Implementation Checklist

## Backend

- [ ] Authentication Module
- [ ] User Module
- [ ] Organization Module
- [ ] Session Module
- [ ] JWT Strategy
- [ ] Refresh Strategy
- [ ] Password Service
- [ ] Email Verification
- [ ] Password Reset
- [ ] Permission Service
- [ ] Role Service
- [ ] Audit Logger
- [ ] Security Events
- [ ] Rate Limiter
- [ ] MFA Service (Scaffold)

---

## Database

- [ ] Users Table
- [ ] Organizations Table
- [ ] Roles Table
- [ ] Permissions Table
- [ ] Role Permissions
- [ ] User Roles
- [ ] Sessions
- [ ] Refresh Tokens
- [ ] Audit Logs
- [ ] Email Verification Tokens
- [ ] Password Reset Tokens

---

## Frontend

- [ ] Login Page
- [ ] Register Page
- [ ] Forgot Password
- [ ] Reset Password
- [ ] Verify Email
- [ ] Session Manager
- [ ] Profile Page
- [ ] Change Password
- [ ] Route Guards
- [ ] Permission Guards
- [ ] Organization Switcher
- [ ] Token Refresh Interceptor
- [ ] Logout Handling

---

# 47. Validation Checklist

Before marking this implementation complete, verify:

- [ ] Users can register
- [ ] Email verification works
- [ ] Login succeeds
- [ ] Invalid credentials are rejected
- [ ] Access tokens expire correctly
- [ ] Refresh token rotation works
- [ ] Sessions can be revoked
- [ ] Password reset works
- [ ] Locked accounts cannot authenticate
- [ ] RBAC permissions are enforced
- [ ] Tenant isolation is maintained
- [ ] Audit logs are generated
- [ ] Authentication APIs return standardized responses
- [ ] Frontend automatically refreshes expired access tokens
- [ ] Sensitive routes require authorization
- [ ] Security events are logged

---

# 48. Acceptance Criteria

Authentication & RBAC implementation is considered complete when:

- Stateless JWT authentication is operational.
- Refresh token rotation is implemented.
- Multi-tenant organization isolation is enforced.
- Role-Based Access Control is fully functional.
- Permission-based authorization is applied across all protected APIs.
- Sessions can be managed and revoked.
- Password recovery and email verification are functional.
- Audit logs capture all authentication events.
- Frontend authentication flow is fully integrated.
- All security and validation tests pass.

---

# 49. AI Context Summary

This document establishes the authentication and authorization foundation for Nebula ERP.

Key architectural decisions include:

- Stateless JWT authentication
- Refresh token rotation
- Argon2id password hashing
- Organization-scoped multi-tenancy
- Role-Based Access Control (RBAC)
- Immutable audit logging
- Session tracking and revocation
- MFA-ready architecture
- Backend-enforced authorization
- Future-ready SSO and OAuth integration

All subsequent implementation documents assume this authentication framework is in place.

---

# 50. Revision History

| Version | Date | Author | Notes |
|----------|------|--------|------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial implementation specification |

---

# Git Commit

Do **not** commit after this document.

Authentication & RBAC must be implemented alongside the Core Platform (IMP-007) because users, organizations, roles, and permissions are tightly coupled.

Proceed to:

**IMP-007 — Core Platform**