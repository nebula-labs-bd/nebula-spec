# Module 003 — Users & Roles

| Field | Value |
|-------|-------|
| Module ID | MOD-003 |
| Name | Users & Roles |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

The Users & Roles module manages user accounts, organizational roles, permissions, branch assignments, departments, and access control throughout Nebula ERP.

Authentication determines **who a user is**.

This module determines **what a user is allowed to do**.

Nebula ERP implements Role-Based Access Control (RBAC) with optional direct permission overrides.

---

# 2. Objectives

The Users & Roles module must:

- Manage user accounts
- Manage roles
- Manage permissions
- Assign users to branches
- Assign users to departments
- Control access to modules
- Support custom roles
- Support permission inheritance
- Support account activation and suspension
- Maintain a complete audit trail

---

# 3. Scope

This module manages:

- Users
- Roles
- Permissions
- Departments
- Job Titles
- Branch Assignments
- User Status
- User Preferences

This module does **not** manage:

- Authentication
- Organization Settings
- Payroll
- Attendance

---

# 4. Business Objectives

The system shall provide secure, flexible, and scalable access control while minimizing administrative effort.

Administrators should be able to configure access without modifying application code.

---

# 5. Actors

Primary actors:

- Super Administrator
- Organization Administrator
- Branch Manager

Secondary actors:

- Department Manager
- Human Resources

End users interact with the system according to the permissions assigned to them.

---

# 6. Functional Requirements

The module shall allow administrators to:

- Create users
- Update user information
- Suspend users
- Activate users
- Archive users
- Assign roles
- Create custom roles
- Assign permissions
- Assign departments
- Assign branches
- Reset passwords
- Force password changes
- View user activity

---

# 7. User Profile

Each user stores:

- Employee ID
- First Name
- Last Name
- Display Name
- Email Address
- Username
- Phone Number
- Profile Photo
- Job Title
- Department
- Organization
- Default Branch
- Time Zone
- Language
- Status

Optional fields:

- National ID
- Date of Birth
- Emergency Contact
- Notes

---

# 8. User Status

Supported user states:

- Pending Invitation
- Active
- Suspended
- Locked
- Archived

Archived users cannot authenticate and remain available only for historical reporting and audit purposes.

---

# 9. Roles

A role is a collection of permissions.

Examples:

- Super Administrator
- Organization Administrator
- Branch Manager
- Warehouse Manager
- Sales Manager
- Cashier
- Accountant
- Inventory Officer
- Purchasing Officer
- Customer Support
- Auditor
- Read-Only User

Organizations may create additional custom roles.

---

# 10. Permissions

Permissions are granular capabilities granted through roles.

Examples:

- Create Product
- Edit Product
- Delete Product
- View Inventory
- Approve Purchase Order
- Create Sales Invoice
- Process Refund
- Export Reports
- Manage Users
- Manage Organization

Permissions should follow a consistent naming convention and be reusable across modules.

---

# 11. Branch & Department Assignment

A user may belong to:

- One organization
- One or more branches
- One department
- Multiple roles (if enabled by organization policy)

Each user must have exactly one default branch.

Branch assignments determine which operational data the user can access.

Department assignments are used for reporting, workflow routing, and future HR integrations.

---

# 12. Business Rules

The Users & Roles module enforces the following rules.

## BR-001

Every user must belong to exactly one organization.

---

## BR-002

Every user must have at least one assigned role.

---

## BR-003

Every user must have exactly one default branch.

---

## BR-004

Only users with the appropriate permission may create, modify, suspend, archive, or delete user accounts.

---

## BR-005

Permissions are inherited through assigned roles.

Direct permission overrides may be enabled by organization policy.

---

## BR-006

A suspended or archived user cannot access the system.

---

## BR-007

Users cannot modify roles or permissions that grant privileges equal to or higher than their own.

---

## BR-008

The organization must always have at least one active Organization Administrator.

---

## BR-009

Deleting users is not permitted.

Users are archived to preserve historical records and audit integrity.

---

## BR-010

Changes to roles and permissions take effect immediately for new requests.

Existing sessions may be revoked according to organization security policy.

---

# 13. RBAC Model

Nebula ERP uses Role-Based Access Control (RBAC).

```
Organization

↓

Roles

↓

Permissions

↓

Users

↓

Authorized Actions
```

Permissions are assigned to roles.

Roles are assigned to users.

Users inherit all permissions granted by their assigned roles.

---

# 14. Permission Matrix

| Action | Super Admin | Org Admin | Branch Manager | Staff |
|----------|:----------:|:---------:|:--------------:|:-----:|
| View Users | ✓ | ✓ | ✓ | ✗ |
| Create Users | ✓ | ✓ | ✗ | ✗ |
| Edit Users | ✓ | ✓ | Limited | ✗ |
| Suspend Users | ✓ | ✓ | ✗ | ✗ |
| Archive Users | ✓ | ✓ | ✗ | ✗ |
| Manage Roles | ✓ | ✓ | ✗ | ✗ |
| Assign Permissions | ✓ | ✓ | ✗ | ✗ |
| Reset Password | ✓ | ✓ | Limited | ✗ |
| View Audit History | ✓ | ✓ | Limited | ✗ |

"Limited" indicates access is restricted to users within the manager's assigned branches or scope.

---

# 15. User Lifecycle

```
Invite User

↓

Pending Invitation

↓

Accept Invitation

↓

Set Password

↓

Activate Account

↓

Daily Usage

↓

Suspend (Optional)

↓

Reactivate

↓

Archive
```

Archived users remain in the system for reporting and auditing purposes.

---

# 16. Database Design

## Primary Tables

```
users

roles

permissions

role_permissions

user_roles

user_permissions

departments

job_titles

user_branches
```

Additional relationship tables may be introduced as new modules require more granular access control.

---

# 17. Validation Rules

| Field | Validation |
|--------|------------|
| First Name | Required, 2–100 characters |
| Last Name | Required, 2–100 characters |
| Email | Required, valid email format |
| Username | Required, unique within organization |
| Phone | Valid international format |
| Department | Must exist |
| Branch | Must exist and belong to organization |
| Role | Must exist and belong to organization |

Validation must be enforced on both the client and server.

---

# 18. Security Policies

The Users & Roles module shall support:

- Least Privilege Principle
- Separation of Duties
- Role Inheritance (future)
- Permission Caching
- Session Revocation after privilege changes
- Organization-level security policies
- Branch-level access restrictions

Sensitive administrative actions require appropriate authorization.

---

# 19. Audit Events

The following actions generate audit records:

- User Created
- User Updated
- User Invited
- User Activated
- User Suspended
- User Reactivated
- User Archived
- Role Created
- Role Updated
- Role Deleted
- Permission Assigned
- Permission Removed
- Branch Assignment Changed
- Department Changed

Each audit record should include:

- User performing the action
- Affected user
- Organization
- Timestamp
- Previous value
- New value
- IP Address (where available)
- Device Information (where available)
---

# 20. API Summary

The Users & Roles module exposes the following primary endpoints.

| Method | Endpoint | Description |
|---------|----------|-------------|
| GET | /users | List users |
| POST | /users | Create user |
| GET | /users/{id} | Get user details |
| PATCH | /users/{id} | Update user |
| DELETE | /users/{id} | Archive user |
| POST | /users/{id}/activate | Activate user |
| POST | /users/{id}/suspend | Suspend user |
| POST | /users/{id}/reset-password | Send password reset |
| GET | /roles | List roles |
| POST | /roles | Create role |
| PATCH | /roles/{id} | Update role |
| DELETE | /roles/{id} | Archive role |
| GET | /permissions | List permissions |
| POST | /roles/{id}/permissions | Assign permissions |
| DELETE | /roles/{id}/permissions/{permissionId} | Remove permission |
| POST | /users/{id}/roles | Assign roles |
| DELETE | /users/{id}/roles/{roleId} | Remove role |
| GET | /departments | List departments |
| POST | /departments | Create department |

All endpoints require authentication and appropriate authorization.

---

# 21. User Interface

The Users & Roles module consists of the following screens.

## Users

Displays:

- Employee ID
- Name
- Email
- Username
- Department
- Default Branch
- Assigned Roles
- Status
- Last Login

Supports:

- Search
- Filtering
- Sorting
- Bulk Actions

---

## User Details

Displays:

- Personal Information
- Contact Information
- Organization
- Branch Assignments
- Department
- Roles
- Permissions
- Security Information
- Login History

---

## Roles

Allows administrators to:

- Create Roles
- Edit Roles
- Archive Roles
- Assign Permissions
- View Assigned Users

---

## Permissions

Displays all available permissions grouped by module.

Supports:

- Search
- Filtering
- Bulk Assignment
- Bulk Removal

---

## Departments

Allows administrators to manage:

- Departments
- Department Managers
- Department Status

---

# 22. Workflow

## User Invitation

```
Create User

↓

Assign Branch

↓

Assign Department

↓

Assign Role(s)

↓

Send Invitation

↓

User Accepts Invitation

↓

Set Password

↓

Account Activated
```

---

## Role Assignment

```
Select User

↓

Select Role(s)

↓

Validate Permissions

↓

Save Changes

↓

Permissions Updated

↓

(Optional) Revoke Existing Sessions
```

---

# 23. Notifications

Examples of notifications generated by this module:

- User Invitation Sent
- Invitation Accepted
- User Activated
- User Suspended
- User Archived
- Role Assigned
- Role Removed
- Permission Updated
- Department Changed

Notifications may be delivered through:

- In-app notifications
- Email
- Push notifications (future)

---

# 24. Error Scenarios

| Scenario | Expected Result |
|----------|-----------------|
| Duplicate email | Validation error |
| Duplicate username | Validation error |
| Invalid role | Validation error |
| Invalid department | Validation error |
| Branch not assigned to organization | Validation error |
| Unauthorized role modification | 403 Forbidden |
| User not found | 404 Not Found |
| Attempt to remove last Organization Administrator | Operation rejected |

Error messages should clearly explain the issue without exposing sensitive information.

---

# 25. Acceptance Criteria

The Users & Roles module is complete when:

- Users can be created, updated, suspended, activated, and archived.
- Custom roles can be created.
- Permissions can be assigned through roles.
- Branch assignments are enforced.
- Department assignments function correctly.
- RBAC rules are consistently applied.
- Administrative actions generate audit records.
- APIs comply with project standards.
- Documentation is complete.

---

# 26. Future Enhancements

Potential future capabilities:

- Team-based permissions
- Dynamic permission policies
- Temporary role assignments
- Approval workflows for privilege escalation
- Delegated administration
- Organizational charts
- HR system integration
- Payroll integration
- Automatic role assignment rules
- Time-based permission expiration

---

# 27. AI Context Summary

## Summary

The Users & Roles module manages user accounts, organizational roles, permissions, departments, and branch assignments. It implements Role-Based Access Control (RBAC) to ensure users can only access the resources and actions authorized for their role.

## Dependencies

- Organization
- Authentication

## Dependent Modules

- Branches
- Products
- Inventory
- Purchasing
- Sales
- POS
- Accounting
- CRM
- Reports
- Notifications

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial users & roles module specification |