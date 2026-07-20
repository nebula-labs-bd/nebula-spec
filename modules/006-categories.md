# Module 006 — Categories

| Field | Value |
|-------|-------|
| Module ID | MOD-006 |
| Name | Categories |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

The Categories module organizes products into a structured hierarchy, making them easier to manage, search, report, and analyze.

Categories provide logical classification across Inventory, Purchasing, Sales, POS, Reporting, CRM, and future E-Commerce integrations.

A well-designed category structure improves usability, reporting accuracy, and product discoverability.

---

# 2. Objectives

The Categories module must:

- Organize products into logical groups
- Support unlimited category hierarchy
- Support parent-child relationships
- Support category metadata
- Support category images
- Support category status management
- Support future e-commerce SEO
- Support reporting and analytics
- Prevent invalid hierarchy structures

---

# 3. Scope

This module manages:

- Categories
- Subcategories
- Parent Categories
- Category Images
- Category Metadata
- Category Display Order
- Category Status

This module does **not** manage:

- Products
- Brands
- Units
- Pricing
- Inventory

Products reference categories but remain independent entities.

---

# 4. Business Objectives

Organizations should be able to classify products using a flexible hierarchy that supports businesses of any size, from small retailers to enterprise distributors.

The hierarchy should remain intuitive, maintainable, and scalable.

---

# 5. Actors

Primary actors:

- Organization Administrator
- Inventory Manager
- Product Manager

Secondary actors:

- Purchasing Officer
- Sales Manager

Most users consume category information, while only authorized users may modify it.

---

# 6. Functional Requirements

The module shall allow users to:

- Create categories
- Edit categories
- Archive categories
- Restore categories
- Reorder categories
- Move categories
- Assign parent categories
- Upload category images
- Configure category metadata
- Search categories
- Export categories

---

# 7. Category Structure

Categories are organized as a hierarchy.

Example:

```
Electronics

├── Computers

│   ├── Laptops

│   ├── Desktops

│   └── Accessories

│

├── Networking

│   ├── Routers

│   ├── Switches

│   └── Access Points

│

└── CCTV

    ├── Cameras

    ├── DVRs

    ├── NVRs

    └── Accessories
```

There is no fixed limit on hierarchy depth, although organizations should keep structures practical for usability.

---

# 8. Category Information

Each category stores:

- Category ID (UUID)
- Category Code
- Category Name
- Display Name
- Parent Category
- Description
- Image
- Display Order
- Status

Optional fields:

- Icon
- Banner Image
- Internal Notes

---

# 9. Category Status

Supported states:

- Draft
- Active
- Inactive
- Archived

Only Active categories should be available for assigning to new products.

Archived categories remain available for historical reporting.

---

# 10. Category Metadata

Each category may define:

- Meta Title
- Meta Description
- Keywords
- URL Slug
- Search Keywords

These fields support future web catalog, marketplace, and e-commerce integrations.

---

# 11. Category Display

Categories should support:

- Tree View
- Expand / Collapse
- Drag-and-Drop Reordering
- Alphabetical Sorting
- Custom Display Order

The user interface should efficiently handle large category hierarchies without compromising performance.

---

# 12. Business Rules

The Categories module enforces the following rules.

## BR-001

Every category must belong to exactly one organization.

---

## BR-002

Category Codes must be unique within an organization.

---

## BR-003

A category may have only one parent category.

---

## BR-004

Circular references are not permitted.

Example:

```
Electronics

↓

Networking

↓

Routers

↓

Electronics ❌
```

The system must reject any operation that creates a circular hierarchy.

---

## BR-005

A category cannot be archived while it contains active child categories unless the operation explicitly archives the entire hierarchy.

---

## BR-006

Organizations may choose whether products can belong to:

- Exactly one category (default)
- Multiple categories

This behavior is controlled through organization settings.

---

## BR-007

Deleting categories is not permitted once they have been assigned to products.

Categories must be archived instead.

---

## BR-008

Changing a category affects only future product assignments.

Historical transactions remain unchanged.

---

## BR-009

Category names should be unique under the same parent category.

Duplicate names under different parents are permitted.

Example:

```
Electronics

└── Accessories

Furniture

└── Accessories
```

---

# 13. Category Relationships

Categories participate in the following relationships:

```
Organization

↓

Categories

↓

Products

↓

Inventory

↓

Sales

↓

Purchasing

↓

Reports
```

Relationships:

- Organization → Categories (1:N)
- Category → Child Categories (1:N)
- Category → Products (1:N or N:N depending on configuration)

---

# 14. Hierarchy Validation

The system shall validate:

- Circular references
- Invalid parent assignments
- Duplicate sibling names
- Maximum hierarchy depth (configurable)
- Organization ownership

Hierarchy validation must occur before every create, update, or move operation.

---

# 15. Database Design

## Primary Tables

```
categories

category_products

category_metadata

category_images
```

Relationships:

- Category → Parent Category (Self Reference)
- Category → Child Categories (Self Reference)
- Category → Products

Future versions may introduce:

```
category_permissions

category_attributes

category_templates
```

---

# 16. Validation Rules

| Field | Validation |
|--------|------------|
| Category Name | Required, 2–150 characters |
| Category Code | Required, unique within organization |
| Parent Category | Must belong to the same organization |
| Display Order | Integer, zero or greater |
| URL Slug | Unique if provided |
| Status | Valid enum value |

Validation must be enforced on both the client and server.

---

# 17. Security Policies

The Categories module shall enforce:

- Organization ownership validation
- Permission-based access
- Hierarchy integrity
- Safe archival process
- Immutable historical references
- Audit logging

Only authorized users may modify category structures.

---

# 18. Audit Events

The following actions generate audit records:

- Category Created
- Category Updated
- Category Archived
- Category Restored
- Category Moved
- Parent Category Changed
- Display Order Changed
- Image Uploaded
- Metadata Updated

Each audit record should include:

- User performing the action
- Organization
- Category
- Timestamp
- Previous Value
- New Value
- IP Address (where available)
- Device Information (where available)
---

# 19. API Summary

The Categories module exposes the following primary endpoints.

| Method | Endpoint | Description |
|---------|----------|-------------|
| GET | /categories | List categories |
| POST | /categories | Create category |
| GET | /categories/{id} | Get category details |
| PATCH | /categories/{id} | Update category |
| DELETE | /categories/{id} | Archive category |
| POST | /categories/{id}/restore | Restore archived category |
| POST | /categories/{id}/move | Move category to another parent |
| GET | /categories/tree | Retrieve category hierarchy |
| POST | /categories/reorder | Update display order |
| POST | /categories/{id}/image | Upload category image |
| DELETE | /categories/{id}/image | Remove category image |
| GET | /categories/export | Export category list |

All endpoints require authentication and appropriate authorization.

---

# 20. User Interface

The Categories module consists of the following screens.

## Category List

Displays:

- Category Code
- Category Name
- Parent Category
- Product Count
- Status
- Display Order

Supports:

- Search
- Filtering
- Sorting
- Bulk Actions

---

## Category Tree

Displays:

- Expand / Collapse hierarchy
- Drag-and-Drop organization
- Product Count
- Status indicators

Allows administrators to move categories within the hierarchy.

---

## Category Details

Displays:

- General Information
- Parent Category
- Child Categories
- Metadata
- Images
- Product Count
- Audit History

---

## Category Editor

Allows administrators to:

- Create Categories
- Edit Categories
- Archive Categories
- Restore Categories
- Upload Images
- Configure Metadata
- Reorder Categories

---

# 21. Search & Navigation

Categories should support searching by:

- Category Name
- Category Code
- Description
- Keywords
- URL Slug

Filters should include:

- Status
- Parent Category
- Created Date
- Updated Date

Navigation should support:

- Breadcrumbs
- Expand / Collapse Tree
- Keyboard Navigation
- Quick Search

---

# 22. Reports

Example reports:

- Category Hierarchy
- Products by Category
- Empty Categories
- Category Usage
- Archived Categories
- Category Growth Report

Reports should support:

- PDF Export
- Excel Export
- CSV Export
- Printing

---

# 23. Error Scenarios

| Scenario | Expected Result |
|----------|-----------------|
| Duplicate category code | Validation error |
| Duplicate sibling category name | Validation error |
| Circular hierarchy detected | Operation rejected |
| Parent category not found | Validation error |
| Attempt to archive category with active child categories | Operation rejected unless recursive archive is selected |
| Category not found | 404 Not Found |
| Unauthorized modification | 403 Forbidden |

Error messages should be descriptive while avoiding disclosure of internal implementation details.

---

# 24. Acceptance Criteria

The Categories module is complete when:

- Categories can be created, updated, archived, restored, and reordered.
- Parent-child relationships function correctly.
- Circular references are prevented.
- Category metadata and images are supported.
- Tree navigation performs efficiently.
- Search and filtering operate correctly.
- Reports generate successfully.
- Administrative actions generate audit records.
- APIs comply with project standards.

---

# 25. Future Enhancements

Potential future capabilities:

- AI-assisted category suggestions
- Automatic product classification
- Category templates
- Category-specific attributes
- Category access permissions
- Multi-language category names
- Category analytics dashboard
- E-commerce navigation optimization
- Marketplace category mapping

---

# 26. AI Context Summary

## Summary

The Categories module provides hierarchical classification for products within Nebula ERP. It supports scalable category trees, metadata, reporting, and future e-commerce integrations while maintaining hierarchy integrity and organizational isolation.

## Dependencies

- Organization
- Users & Roles
- Products

## Dependent Modules

- Inventory
- Purchasing
- Sales
- POS
- CRM
- Reporting
- E-Commerce (Future)

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial categories module specification |