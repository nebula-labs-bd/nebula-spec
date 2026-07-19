# Module 002 — Authentication

| Field | Value |
|-------|-------|
| Module ID | MOD-002 |
| Name | Authentication |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

The Authentication module is responsible for verifying user identity and securely granting access to Nebula ERP.

It provides login, logout, session management, password policies, multi-factor authentication, device management, and account security.

This module only answers one question:

**"Who is the user?"**

Authorization (what the user can do) is handled separately by the Users & Roles module.

---

# 2. Objectives

The Authentication module must:

- Secure user authentication
- Support email and username login
- Support secure password storage
- Support JWT authentication
- Support Refresh Tokens
- Support Session Management
- Support Multi-Factor Authentication (MFA)
- Support Password Reset
- Support Remember Me
- Support Device Management
- Protect against brute-force attacks
- Support future SSO integration

---

# 3. Scope

This module manages:

- Login
- Logout
- Password Verification
- Password Reset
- Email Verification
- Session Management
- Token Management
- MFA
- Device Trust
- Security Policies

This module does **not** manage:

- Roles
- Permissions
- User Profiles
- Organizations
- Branches

---

# 4. Business Objectives

Authentication should provide a secure, fast, and reliable login experience while protecting organizational data from unauthorized access.

Security always takes priority over convenience.

---

# 5. Actors

Primary actors:

- User
- Organization Administrator
- Super Administrator

Secondary actors:

- Support Engineer

---

# 6. Functional Requirements

The module shall allow users to:

- Login
- Logout
- Change Password
- Reset Forgotten Password
- Verify Email Address
- Enable MFA
- Disable MFA
- View Active Sessions
- Revoke Sessions
- Trust Device
- Remove Trusted Device

---

# 7. Authentication Methods

Nebula ERP supports:

- Email + Password
- Username + Password

Future support:

- Microsoft Entra ID
- Google Workspace
- LDAP
- SAML
- OpenID Connect
- OAuth2

---

# 8. Login Process

A successful login requires:

- Valid identifier
- Valid password
- Active account
- Active organization
- Active branch access
- Successful MFA (if enabled)

If all conditions are met, the system issues:

- Access Token
- Refresh Token
- Session Record

---

# 9. Password Policy

Default password requirements:

- Minimum 12 characters
- At least one uppercase letter
- At least one lowercase letter
- At least one number
- At least one special character

Passwords must never be stored in plain text.

Passwords must be hashed using Argon2id.

---

# 10. Session Management

Each successful login creates a new session.

Each session stores:

- Session ID
- User
- Organization
- Device
- Browser
- Operating System
- IP Address
- Login Time
- Last Activity
- Expiration Time

Users may terminate individual sessions without affecting others.

---

# 11. Multi-Factor Authentication (MFA)

Supported methods:

- TOTP Authenticator App (Preferred)
- Recovery Codes

Future methods:

- Email OTP
- SMS OTP
- Security Keys (WebAuthn/FIDO2)
- Passkeys

MFA should be configurable at the organization level and enforceable through security policies.

---

# 12. Business Rules

The Authentication module enforces the following rules.

## BR-001

Only active users belonging to an active organization may authenticate.

---

## BR-002

A user account must have a unique email address within an organization.

---

## BR-003

Passwords must be hashed using Argon2id before being stored.

---

## BR-004

Access Tokens shall be short-lived.

Default expiration: **15 minutes**.

---

## BR-005

Refresh Tokens shall be securely stored, revocable, and rotated after every successful refresh.

---

## BR-006

A user may maintain multiple active sessions unless restricted by organization security policies.

---

## BR-007

Accounts are temporarily locked after multiple consecutive failed login attempts.

Default policy:

- Maximum Failed Attempts: 5
- Lock Duration: 15 minutes

Organization administrators may configure stricter policies.

---

## BR-008

Password reset links must expire after 30 minutes and become invalid immediately after use.

---

## BR-009

Email verification is required before accessing protected resources unless explicitly disabled by organization policy.

---

## BR-010

All authentication events must be recorded in the Audit Log.

---

# 13. Authentication Flow

```
User

↓

Enter Email / Username

↓

Validate Credentials

↓

Account Active?

↓

Organization Active?

↓

MFA Enabled?

↓

Verify MFA

↓

Generate Access Token

↓

Generate Refresh Token

↓

Create Session

↓

Grant Access
```

Authentication should fail immediately if any required validation fails.

---

# 14. JWT Strategy

Nebula ERP uses JSON Web Tokens (JWT).

Two token types are issued:

## Access Token

Purpose:

- Authenticate API requests

Default Lifetime:

- 15 Minutes

Contains:

- User ID
- Organization ID
- Branch Context
- Session ID
- Token Version

---

## Refresh Token

Purpose:

- Obtain new Access Tokens

Default Lifetime:

- 30 Days

Requirements:

- Rotated after each use
- Stored securely
- Revocable
- Invalidated upon logout or password reset

---

# 15. Account Lockout Policy

The system shall protect against brute-force attacks.

Default behavior:

| Failed Attempts | Action |
|-----------------|--------|
| 1–4 | Reject login |
| 5 | Lock account for 15 minutes |
| Continued attempts | Extend lock duration based on security policy |

Administrators may manually unlock accounts.

---

# 16. Trusted Devices

Users may optionally trust frequently used devices.

Each trusted device stores:

- Device ID
- Device Name
- Browser
- Operating System
- IP Address (Last Known)
- Trusted Date
- Last Used
- Expiration Date

Organizations may disable trusted devices through security policies.

---

# 17. Database Design

## Primary Tables

```
sessions

refresh_tokens

password_resets

email_verifications

trusted_devices

mfa_recovery_codes
```

User credentials remain in the `users` table defined by the Users & Roles module.

---

# 18. Validation Rules

| Field | Validation |
|--------|------------|
| Email | Valid email format |
| Username | 3–50 characters |
| Password | Meets password policy |
| Reset Token | Valid and not expired |
| MFA Code | Valid TOTP code |
| Refresh Token | Valid, active, not revoked |

Validation must occur on both the client and server.

---

# 19. Security Controls

The Authentication module shall implement:

- Argon2id password hashing
- HTTPS-only authentication
- Secure HTTP-only cookies (where applicable)
- CSRF protection for cookie-based authentication
- Rate limiting
- Brute-force protection
- Token rotation
- Session revocation
- Secure password reset workflow
- Audit logging

Sensitive information must never be exposed in authentication responses.

---

# 20. Audit Events

The following events generate audit records:

- Login Successful
- Login Failed
- Logout
- Password Changed
- Password Reset Requested
- Password Reset Completed
- Email Verified
- MFA Enabled
- MFA Disabled
- Session Revoked
- Trusted Device Added
- Trusted Device Removed
- Account Locked
- Account Unlocked

Each audit entry should include:

- User
- Organization
- Timestamp
- IP Address
- Device Information
- Result (Success / Failure)
- Failure Reason (if applicable)

---

# 21. API Summary

The Authentication module exposes the following primary endpoints.

| Method | Endpoint | Description |
|---------|----------|-------------|
| POST | /auth/login | Authenticate user |
| POST | /auth/logout | Logout current session |
| POST | /auth/refresh | Refresh access token |
| GET | /auth/me | Get current authenticated user |
| POST | /auth/change-password | Change current password |
| POST | /auth/forgot-password | Request password reset |
| POST | /auth/reset-password | Reset password |
| POST | /auth/verify-email | Verify email address |
| POST | /auth/resend-verification | Resend verification email |
| POST | /auth/mfa/enable | Enable MFA |
| POST | /auth/mfa/disable | Disable MFA |
| POST | /auth/mfa/verify | Verify MFA code |
| GET | /auth/sessions | List active sessions |
| DELETE | /auth/sessions/{id} | Revoke a session |
| GET | /auth/trusted-devices | List trusted devices |
| DELETE | /auth/trusted-devices/{id} | Remove trusted device |

All endpoints, except login, password reset, and email verification, require authentication.

---

# 22. User Interface

The Authentication module consists of the following screens.

## Login

Displays:

- Email or Username
- Password
- Remember Me
- Sign In
- Forgot Password

---

## Multi-Factor Authentication

Displays:

- TOTP Code Input
- Recovery Code Option

---

## Forgot Password

Allows users to request a password reset link.

---

## Reset Password

Allows users to securely set a new password after validating the reset token.

---

## Active Sessions

Displays:

- Current Device
- Other Devices
- Browser
- Operating System
- IP Address
- Last Activity
- Login Time

Users can revoke any active session except the current one if organization policy allows.

---

## Trusted Devices

Displays:

- Device Name
- Browser
- Operating System
- Trusted Date
- Last Used

Users may remove trusted devices at any time.

---

# 23. Workflow

```
User Opens Login

↓

Enter Credentials

↓

Validate Credentials

↓

MFA Required?

↓

Yes → Verify MFA

↓

Issue Tokens

↓

Create Session

↓

Redirect to Dashboard
```

Password Reset Flow

```
Forgot Password

↓

Receive Email

↓

Open Reset Link

↓

Validate Token

↓

Enter New Password

↓

Password Updated

↓

Invalidate Previous Sessions (optional by policy)

↓

Login
```

---

# 24. Notifications

Examples of notifications generated by this module:

- Successful Login (optional)
- Login From New Device
- Password Changed
- Password Reset Requested
- Password Reset Completed
- MFA Enabled
- MFA Disabled
- Account Locked
- Account Unlocked

Notifications may be delivered through:

- In-app notifications
- Email
- Push notifications (future)

---

# 25. Error Scenarios

| Scenario | Expected Result |
|----------|-----------------|
| Invalid username/password | Authentication failed |
| Account locked | Inform user of lock duration |
| Account disabled | Access denied |
| Organization inactive | Access denied |
| Invalid MFA code | Authentication failed |
| Expired reset token | Request new reset link |
| Invalid refresh token | Re-authentication required |
| Revoked session | Force login |

Error messages should not reveal whether the username or password was incorrect.

---

# 26. Acceptance Criteria

The Authentication module is complete when:

- Users can securely log in and log out.
- Passwords are stored using Argon2id.
- Access and Refresh Tokens function correctly.
- MFA can be enabled and verified.
- Password reset works securely.
- Email verification functions correctly.
- Session management is available.
- Trusted devices are supported.
- Audit events are generated.
- Authentication APIs comply with project standards.

---

# 27. Future Enhancements

Potential future capabilities:

- Passkeys (WebAuthn)
- Biometric Authentication
- Microsoft Entra ID
- Google Workspace SSO
- LDAP Authentication
- SAML Authentication
- OAuth2 Providers
- Risk-Based Authentication
- Adaptive MFA
- Device Compliance Checks

---

# 28. AI Context Summary

## Summary

The Authentication module securely verifies user identity, manages authentication sessions, issues tokens, enforces security policies, and protects access to Nebula ERP.

## Dependencies

- Organization

## Dependent Modules

- Users & Roles
- Branches
- Dashboard
- Inventory
- Purchasing
- Sales
- POS
- Accounting
- CRM

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial authentication module specification |