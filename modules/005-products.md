# Module 005 — Products

| Field | Value |
|-------|-------|
| Module ID | MOD-005 |
| Name | Products |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

The Products module is the master catalog of all items that can be purchased, stocked, manufactured, sold, rented, repaired, or serviced within Nebula ERP.

Every operational module—including Inventory, Purchasing, Sales, POS, CRM, Accounting, Warranty, and Reporting—depends on this module.

The Product Catalog acts as the single source of truth for all product-related information.

---

# 2. Objectives

The Products module must:

- Maintain a centralized product catalog
- Support multiple product types
- Support product variants
- Support product attributes
- Support multiple units of measure
- Support multiple barcodes
- Support product images and documents
- Support pricing strategies
- Support tax configuration
- Support serial and batch tracking
- Support product lifecycle management
- Support future e-commerce integrations

---

# 3. Scope

This module manages:

- Products
- Product Variants
- Product Attributes
- Product Images
- Product Documents
- Product Pricing
- Product Barcodes
- Product Tax Profiles
- Product Status
- Product Classification

This module does **not** manage:

- Inventory Quantities
- Purchase Orders
- Sales Orders
- Warehouse Stock
- Accounting Entries

These are managed by their respective modules.

---

# 4. Business Objectives

The product catalog should provide a flexible and scalable foundation capable of supporting organizations ranging from small retailers to multi-branch enterprises.

Every product should be uniquely identifiable and consistently represented across the entire ERP.

---

# 5. Actors

Primary actors:

- Organization Administrator
- Inventory Manager
- Purchasing Officer
- Sales Manager

Secondary actors:

- Warehouse Staff
- Cashier
- Customer Support

Most users will consume product information but only authorized personnel may modify it.

---

# 6. Functional Requirements

The module shall allow users to:

- Create products
- Update products
- Archive products
- Restore archived products
- Clone products
- Import products
- Export products
- Search products
- Filter products
- Assign categories
- Assign units
- Assign taxes
- Upload images
- Upload documents
- Manage variants
- Configure pricing
- Configure tracking options

---

# 7. Product Types

Nebula ERP supports multiple product types.

Supported types include:

- Physical Product
- Digital Product
- Service
- Raw Material
- Finished Goods
- Semi-Finished Goods
- Bundle / Kit
- Asset
- Rental Item
- Repair Item
- Warranty Item

Future product types may be added without modifying the core architecture.

---

# 8. Product Identification

Each product stores:

- Product ID (UUID)
- SKU
- Product Code
- Barcode
- QR Code (optional)
- Product Name
- Display Name
- Short Description
- Long Description

SKU must be unique within an organization.

Multiple barcodes may be associated with a single product.

---

# 9. Product Classification

Every product may belong to:

- Category
- Subcategory
- Brand
- Manufacturer
- Collection
- Product Line
- Department

Products may also be assigned searchable tags for reporting and filtering.

---

# 10. Units of Measure

Each product defines:

- Base Unit
- Purchase Unit
- Sales Unit
- Conversion Rules

Example:

Base Unit:

Piece

Purchase Unit:

Box

Sales Unit:

Piece

Conversion:

1 Box = 12 Pieces

Unit conversions are maintained automatically by the ERP.

---

# 11. Product Status

Supported product states:

- Draft
- Active
- Inactive
- Discontinued
- Archived

Only Active products may participate in purchasing, inventory, sales, or POS operations.

Archived products remain available for historical reporting and audit purposes.

---

# 12. Business Rules

The Products module enforces the following rules.

## BR-001

Every product must belong to exactly one organization.

---

## BR-002

Every product must have exactly one Base Unit.

---

## BR-003

Every product must have a unique SKU within the organization.

---

## BR-004

Archived or discontinued products cannot be used in new operational transactions.

Historical transactions must remain unchanged.

---

## BR-005

Changing product information must not alter historical purchase orders, sales invoices, inventory transactions, or accounting records.

---

## BR-006

A product may belong to multiple categories only if the organization enables multi-category support.

Otherwise, each product belongs to exactly one category.

---

## BR-007

Price changes affect only future transactions.

Completed transactions retain the original transaction price.

---

## BR-008

Serial-tracked products require one unique serial number per inventory unit.

Duplicate serial numbers are not permitted within the same organization.

---

## BR-009

Batch-tracked products require a valid batch number before stock can be received.

---

## BR-010

Products cannot be permanently deleted after they have been referenced by operational transactions.

They must be archived instead.

---

# 13. Product Variants

Variants represent different versions of the same product.

Common variant attributes include:

- Size
- Color
- Capacity
- Memory
- Material
- Length
- Width
- Model
- Edition

Example:

```
T-Shirt

├── Small / Black

├── Medium / Black

├── Large / Black

├── Small / White

└── Large / White
```

Each variant may have its own:

- SKU
- Barcode
- Price
- Images
- Weight
- Dimensions

Variants inherit information from the parent product unless overridden.

---

# 14. Product Attributes

Attributes describe product characteristics.

Examples:

- Brand
- Model
- Color
- Size
- Weight
- Voltage
- Processor
- Memory
- Storage
- Material
- Country of Origin

Organizations may create custom attributes.

Attributes should be searchable and filterable.

---

# 15. Pricing

Each product supports multiple pricing models.

Supported prices include:

- Purchase Cost
- Standard Cost
- Average Cost (calculated)
- Selling Price
- Wholesale Price
- Dealer Price
- Distributor Price
- Promotional Price

Future enhancements:

- Customer Group Pricing
- Branch Pricing
- Region Pricing
- Dynamic Pricing
- Time-Based Pricing

Price history should be retained for auditing and reporting.

---

# 16. Tax Configuration

Each product may be associated with:

- Tax Category
- VAT Rate
- Sales Tax Rate
- Purchase Tax Rate
- Tax Exemption Status

Tax calculations are performed by the Accounting module using the product's assigned tax profile.

---

# 17. Serial & Batch Tracking

Products may use one of the following inventory tracking methods:

| Tracking Method | Description |
|-----------------|-------------|
| None | Quantity only |
| Serial Number | Every item has a unique serial number |
| Batch Number | Items are grouped into production batches |
| Serial + Batch | Supports both serial and batch tracking |

Tracking mode is selected when the product is created and should not be changed once inventory transactions exist.

---

# 18. Database Design

## Primary Tables

```
products

product_variants

product_images

product_documents

product_attributes

product_attribute_values

product_barcodes

product_prices

product_tax_profiles

product_tags
```

Relationships:

- Product → Variants (1:N)
- Product → Images (1:N)
- Product → Documents (1:N)
- Product → Prices (1:N)
- Product → Barcodes (1:N)
- Product → Attributes (N:N)

---

# 19. Validation Rules

| Field | Validation |
|--------|------------|
| Product Name | Required, 3–200 characters |
| SKU | Required, unique within organization |
| Product Code | Optional, unique if provided |
| Barcode | Unique if provided |
| Base Unit | Required |
| Category | Required |
| Selling Price | Must be zero or greater |
| Purchase Cost | Must be zero or greater |
| Product Type | Required |
| Tracking Method | Valid enum value |

Validation must occur on both the client and server.

---

# 20. Audit Events

The following actions generate audit records:

- Product Created
- Product Updated
- Product Archived
- Product Restored
- Product Cloned
- Variant Created
- Variant Updated
- Price Changed
- Barcode Added
- Barcode Removed
- Tax Profile Changed
- Tracking Method Changed
- Image Uploaded
- Document Uploaded

Each audit record should include:

- User performing the action
- Organization
- Product
- Timestamp
- Previous Value
- New Value
- IP Address (where available)
- Device Information (where available)

---

# 21. API Summary

The Products module exposes the following primary endpoints.

| Method | Endpoint | Description |
|---------|----------|-------------|
| GET | /products | List products |
| POST | /products | Create product |
| GET | /products/{id} | Get product details |
| PATCH | /products/{id} | Update product |
| DELETE | /products/{id} | Archive product |
| POST | /products/{id}/restore | Restore archived product |
| POST | /products/{id}/clone | Clone product |
| GET | /products/{id}/variants | List product variants |
| POST | /products/{id}/variants | Create variant |
| PATCH | /variants/{id} | Update variant |
| DELETE | /variants/{id} | Archive variant |
| POST | /products/import | Import products |
| GET | /products/export | Export products |
| POST | /products/{id}/images | Upload product image |
| DELETE | /products/{id}/images/{imageId} | Remove product image |
| POST | /products/{id}/documents | Upload product document |

All endpoints require authentication and appropriate authorization.

---

# 22. User Interface

The Products module consists of the following screens.

## Product List

Displays:

- SKU
- Product Name
- Category
- Brand
- Product Type
- Selling Price
- Available Status
- Tracking Method

Supports:

- Search
- Filtering
- Sorting
- Bulk Actions
- Column Customization

---

## Product Details

Displays:

- General Information
- Images
- Pricing
- Categories
- Attributes
- Variants
- Tax Profile
- Tracking Configuration
- Documents
- Audit History

---

## Product Editor

Allows authorized users to:

- Create Products
- Edit Products
- Archive Products
- Clone Products
- Upload Images
- Upload Documents
- Configure Variants
- Configure Prices
- Configure Tracking

---

## Variant Manager

Displays:

- Variant Name
- SKU
- Barcode
- Selling Price
- Status

Allows administrators to add, edit, archive, and restore variants.

---

# 23. Import & Export

Supported import formats:

- Excel (.xlsx)
- CSV

Supported export formats:

- Excel (.xlsx)
- CSV
- PDF (Catalog)
- JSON (API Integration)

Import validation should identify:

- Duplicate SKUs
- Invalid Categories
- Invalid Units
- Missing Required Fields
- Invalid Prices

The system should provide a detailed import summary including successful, failed, and skipped records.

---

# 24. Search & Filtering

Products should support searching by:

- Product Name
- SKU
- Barcode
- Product Code
- Brand
- Manufacturer
- Category
- Tag

Filters should include:

- Product Type
- Status
- Category
- Brand
- Tracking Method
- Price Range
- Created Date
- Updated Date

Search results should support pagination and configurable sorting.

---

# 25. Reports

Example reports:

- Product Catalog
- Product Pricing
- Product List by Category
- Product List by Brand
- Product Status Summary
- New Products
- Archived Products
- Product Attribute Report

Reports should support:

- PDF Export
- Excel Export
- CSV Export
- Printing

---

# 26. Error Scenarios

| Scenario | Expected Result |
|----------|-----------------|
| Duplicate SKU | Validation error |
| Duplicate barcode | Validation error |
| Invalid category | Validation error |
| Invalid base unit | Validation error |
| Invalid tracking method | Validation error |
| Attempt to delete product with transactions | Operation rejected |
| Product not found | 404 Not Found |
| Unauthorized modification | 403 Forbidden |

Error messages should clearly explain the problem while avoiding disclosure of sensitive implementation details.

---

# 27. Acceptance Criteria

The Products module is complete when:

- Products can be created, updated, archived, restored, and cloned.
- Product variants function correctly.
- Product attributes are configurable.
- Multiple pricing models are supported.
- Product images and documents can be managed.
- Serial and batch tracking options are enforced.
- Import and export functions operate correctly.
- Product search and filtering perform efficiently.
- Administrative actions generate audit records.
- APIs comply with project standards.

---

# 28. Future Enhancements

Potential future capabilities:

- AI-assisted product creation
- Automatic barcode generation
- GS1 integration
- Product versioning
- Product lifecycle workflows
- Digital asset management
- Supplier-specific product catalogs
- E-commerce synchronization
- Marketplace integration
- Product recommendation engine

---

# 29. AI Context Summary

## Summary

The Products module serves as the centralized product catalog for Nebula ERP. It manages product master data, variants, pricing, tax configuration, attributes, tracking methods, media assets, and classification. Operational modules reference products but do not modify the master catalog directly.

## Dependencies

- Organization
- Users & Roles
- Branches
- Categories
- Units

## Dependent Modules

- Inventory
- Warehouses
- Purchasing
- Sales
- POS
- CRM
- Warranty
- Repair
- Reporting
- Accounting

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial products module specification |