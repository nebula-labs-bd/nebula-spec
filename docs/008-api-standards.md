# Nebula ERP API Standards

| Field | Value |
|-------|-------|
| Document ID | DOC-008 |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

This document defines the official API design standards for Nebula ERP. It ensures every API is consistent, secure, predictable, and easy to maintain.

These standards apply to every REST endpoint developed within Nebula ERP.

---

# 2. Design Principles

Every API should be:

- Consistent
- Predictable
- Versioned
- Secure
- Stateless
- Well documented
- Easy to consume
- Backward compatible whenever possible

---

# 3. API Architecture

Nebula ERP uses a RESTful API architecture.

General principles:

- Resource-oriented endpoints
- JSON request/response format
- Stateless communication
- HTTPS only
- Standard HTTP methods
- Consistent status codes

---

# 4. Base URL

Development

```
http://localhost:3000/api/v1
```

Production

```
https://api.example.com/api/v1
```

Every endpoint must begin with:

```
/api/v1/
```

Future breaking changes require a new version.

---

# 5. HTTP Methods

| Method | Purpose |
|---------|---------|
| GET | Retrieve resources |
| POST | Create resources |
| PUT | Replace resources |
| PATCH | Partially update resources |
| DELETE | Remove resources |

Use the correct HTTP method for each operation.

---

# 6. Resource Naming

Use plural nouns.

Good:

```
/products

/customers

/invoices

/purchase-orders
```

Avoid:

```
/getProducts

/createCustomer

/deleteInvoice
```

Resource names should be lowercase and use kebab-case when multiple words are required.

---

# 7. URL Structure

Examples:

```
GET /products

GET /products/{id}

POST /products

PATCH /products/{id}

DELETE /products/{id}
```

Nested resources:

```
GET /customers/{id}/addresses

GET /orders/{id}/items
```

Avoid deeply nested URLs.

---

# 8. Request Format

All requests must use JSON unless uploading files.

Example:

```json
{
  "name": "Wireless Mouse",
  "price": 1500,
  "categoryId": "cat_001"
}
```

---

# 9. Response Format

Successful responses follow a consistent structure.

```json
{
  "success": true,
  "message": "Product created successfully.",
  "data": {},
  "meta": {}
}
```

---

# 10. Error Response Format

Errors should be predictable.

Example:

```json
{
  "success": false,
  "message": "Validation failed.",
  "errors": [
    {
      "field": "price",
      "message": "Price must be greater than zero."
    }
  ]
}
```

Error messages should help API consumers resolve issues.

---

# 11. HTTP Status Codes

Use standard status codes.

| Code | Meaning |
|------|---------|
| 200 | OK |
| 201 | Created |
| 204 | No Content |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 409 | Conflict |
| 422 | Validation Error |
| 429 | Too Many Requests |
| 500 | Internal Server Error |

Avoid using `200 OK` for failed operations.

---

# 12. Authentication

Protected endpoints require authentication.

Authentication uses:

- JWT Access Token
- Refresh Token

Clients must send:

```
Authorization: Bearer <access_token>
```

Public endpoints should be explicitly documented.

# Nebula ERP API Standards

| Field | Value |
|-------|-------|
| Document ID | DOC-008 |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

This document defines the official API design standards for Nebula ERP. It ensures every API is consistent, secure, predictable, and easy to maintain.

These standards apply to every REST endpoint developed within Nebula ERP.

---

# 2. Design Principles

Every API should be:

- Consistent
- Predictable
- Versioned
- Secure
- Stateless
- Well documented
- Easy to consume
- Backward compatible whenever possible

---

# 3. API Architecture

Nebula ERP uses a RESTful API architecture.

General principles:

- Resource-oriented endpoints
- JSON request/response format
- Stateless communication
- HTTPS only
- Standard HTTP methods
- Consistent status codes

---

# 4. Base URL

Development

```
http://localhost:3000/api/v1
```

Production

```
https://api.example.com/api/v1
```

Every endpoint must begin with:

```
/api/v1/
```

Future breaking changes require a new version.

---

# 5. HTTP Methods

| Method | Purpose |
|---------|---------|
| GET | Retrieve resources |
| POST | Create resources |
| PUT | Replace resources |
| PATCH | Partially update resources |
| DELETE | Remove resources |

Use the correct HTTP method for each operation.

---

# 6. Resource Naming

Use plural nouns.

Good:

```
/products

/customers

/invoices

/purchase-orders
```

Avoid:

```
/getProducts

/createCustomer

/deleteInvoice
```

Resource names should be lowercase and use kebab-case when multiple words are required.

---

# 7. URL Structure

Examples:

```
GET /products

GET /products/{id}

POST /products

PATCH /products/{id}

DELETE /products/{id}
```

Nested resources:

```
GET /customers/{id}/addresses

GET /orders/{id}/items
```

Avoid deeply nested URLs.

---

# 8. Request Format

All requests must use JSON unless uploading files.

Example:

```json
{
  "name": "Wireless Mouse",
  "price": 1500,
  "categoryId": "cat_001"
}
```

---

# 9. Response Format

Successful responses follow a consistent structure.

```json
{
  "success": true,
  "message": "Product created successfully.",
  "data": {},
  "meta": {}
}
```

---

# 10. Error Response Format

Errors should be predictable.

Example:

```json
{
  "success": false,
  "message": "Validation failed.",
  "errors": [
    {
      "field": "price",
      "message": "Price must be greater than zero."
    }
  ]
}
```

Error messages should help API consumers resolve issues.

---

# 11. HTTP Status Codes

Use standard status codes.

| Code | Meaning |
|------|---------|
| 200 | OK |
| 201 | Created |
| 204 | No Content |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 409 | Conflict |
| 422 | Validation Error |
| 429 | Too Many Requests |
| 500 | Internal Server Error |

Avoid using `200 OK` for failed operations.

---

# 12. Authentication

Protected endpoints require authentication.

Authentication uses:

- JWT Access Token
- Refresh Token

Clients must send:

```
Authorization: Bearer <access_token>
```

Public endpoints should be explicitly documented.