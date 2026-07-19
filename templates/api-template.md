# {{API Name}}

| Field | Value |
|-------|-------|
| API ID | API-XXX |
| Version | 1.0.0 |
| Status | Draft |
| Last Updated | YYYY-MM-DD |

---

# 1. Purpose

Describe what this endpoint does.

---

# 2. Endpoint

| Property | Value |
|----------|-------|
| Method | GET |
| URL | /api/v1/... |
| Authentication | JWT |
| Permission | |

---

# 3. Request

## Headers

| Name | Required | Description |
|------|----------|-------------|
| Authorization | Yes | Bearer Token |

### Path Parameters

| Name | Type | Description |
|------|------|-------------|
| | | |

### Query Parameters

| Name | Type | Required | Description |
|------|------|----------|-------------|
| | | | |

### Request Body

```json
{}
```

---

# 4. Response

### Success (200)

```json
{}
```

### Error Responses

| Code | Meaning |
|------|----------|
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 422 | Validation Error |
| 500 | Internal Server Error |

---

# 5. Validation Rules

- Required fields
- Field length
- Business validation

---

# 6. Business Rules

| Rule ID | Description |
|---------|-------------|
| BR-001 | |

---

# 7. Database Impact

Tables used:

-

---

# 8. Audit Requirements

Describe what should be logged.

---

# 9. AI Context Summary

## Related Module

-

## Related Tables

-

## Notes

This API specification is the implementation source of truth.