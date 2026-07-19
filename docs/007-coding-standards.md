# Nebula ERP Coding Standards

| Field | Value |
|-------|-------|
| Document ID | DOC-007 |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

This document defines the official coding standards for Nebula ERP. It establishes consistent rules for writing, reviewing, and maintaining source code across all applications and packages.

Consistent coding standards improve readability, reduce bugs, simplify onboarding, and enable effective collaboration.

---

# 2. Guiding Principles

Every piece of code should be:

- Readable
- Maintainable
- Predictable
- Testable
- Secure
- Performant
- Reusable
- Well documented

Code is written for humans first and computers second.

---

# 3. General Standards

All production code must:

- Compile without warnings.
- Pass linting.
- Pass formatting checks.
- Include appropriate error handling.
- Avoid unnecessary complexity.
- Follow the project folder structure.
- Follow naming conventions.
- Use TypeScript strict mode.

---

# 4. TypeScript Standards

TypeScript is mandatory for all frontend and backend development.

## Rules

- Avoid `any`.
- Prefer explicit types.
- Use interfaces for object contracts.
- Use enums sparingly.
- Enable strict mode.
- Keep generic types simple.

Example:

```typescript
interface Product {
  id: string;
  name: string;
  price: number;
}
```

---

# 5. Naming Conventions

## Variables

Use descriptive camelCase names.

Good:

```typescript
customerName
invoiceTotal
productPrice
```

Bad:

```typescript
a
data1
temp
```

---

## Functions

Function names should describe an action.

Examples:

```typescript
calculateTotal()

createInvoice()

updateInventory()

sendNotification()
```

---

## Classes

Use PascalCase.

Examples:

```typescript
InventoryService

ProductController

CustomerRepository
```

---

## Constants

Use UPPER_SNAKE_CASE for application-wide constants.

```typescript
MAX_LOGIN_ATTEMPTS

DEFAULT_PAGE_SIZE
```

---

# 6. File Naming

Use kebab-case for filenames unless framework conventions require otherwise.

Examples:

```
inventory.service.ts

customer.controller.ts

sales.repository.ts
```

File names should clearly describe their contents.

---

# 7. Functions

Functions should:

- Have a single responsibility.
- Be small and focused.
- Avoid excessive nesting.
- Return predictable values.
- Minimize side effects.

Prefer early returns over deeply nested conditions.

---

# 8. Classes

Classes should:

- Represent a single responsibility.
- Keep constructors simple.
- Avoid excessive public methods.
- Prefer dependency injection.
- Minimize shared mutable state.

---

# 9. Error Handling

Errors should be handled explicitly.

Guidelines:

- Throw meaningful exceptions.
- Avoid swallowing errors.
- Log unexpected failures.
- Return user-friendly messages where appropriate.
- Never expose sensitive implementation details.

---

# 10. Comments

Write code that is self-explanatory whenever possible.

Use comments to explain:

- Business rules
- Non-obvious decisions
- Complex algorithms
- Temporary workarounds (with references)

Avoid comments that simply repeat the code.

# Nebula ERP Coding Standards

| Field | Value |
|-------|-------|
| Document ID | DOC-007 |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

This document defines the official coding standards for Nebula ERP. It establishes consistent rules for writing, reviewing, and maintaining source code across all applications and packages.

Consistent coding standards improve readability, reduce bugs, simplify onboarding, and enable effective collaboration.

---

# 2. Guiding Principles

Every piece of code should be:

- Readable
- Maintainable
- Predictable
- Testable
- Secure
- Performant
- Reusable
- Well documented

Code is written for humans first and computers second.

---

# 3. General Standards

All production code must:

- Compile without warnings.
- Pass linting.
- Pass formatting checks.
- Include appropriate error handling.
- Avoid unnecessary complexity.
- Follow the project folder structure.
- Follow naming conventions.
- Use TypeScript strict mode.

---

# 4. TypeScript Standards

TypeScript is mandatory for all frontend and backend development.

## Rules

- Avoid `any`.
- Prefer explicit types.
- Use interfaces for object contracts.
- Use enums sparingly.
- Enable strict mode.
- Keep generic types simple.

Example:

```typescript
interface Product {
  id: string;
  name: string;
  price: number;
}
```

---

# 5. Naming Conventions

## Variables

Use descriptive camelCase names.

Good:

```typescript
customerName
invoiceTotal
productPrice
```

Bad:

```typescript
a
data1
temp
```

---

## Functions

Function names should describe an action.

Examples:

```typescript
calculateTotal()

createInvoice()

updateInventory()

sendNotification()
```

---

## Classes

Use PascalCase.

Examples:

```typescript
InventoryService

ProductController

CustomerRepository
```

---

## Constants

Use UPPER_SNAKE_CASE for application-wide constants.

```typescript
MAX_LOGIN_ATTEMPTS

DEFAULT_PAGE_SIZE
```

---

# 6. File Naming

Use kebab-case for filenames unless framework conventions require otherwise.

Examples:

```
inventory.service.ts

customer.controller.ts

sales.repository.ts
```

File names should clearly describe their contents.

---

# 7. Functions

Functions should:

- Have a single responsibility.
- Be small and focused.
- Avoid excessive nesting.
- Return predictable values.
- Minimize side effects.

Prefer early returns over deeply nested conditions.

---

# 8. Classes

Classes should:

- Represent a single responsibility.
- Keep constructors simple.
- Avoid excessive public methods.
- Prefer dependency injection.
- Minimize shared mutable state.

---

# 9. Error Handling

Errors should be handled explicitly.

Guidelines:

- Throw meaningful exceptions.
- Avoid swallowing errors.
- Log unexpected failures.
- Return user-friendly messages where appropriate.
- Never expose sensitive implementation details.

---

# 10. Comments

Write code that is self-explanatory whenever possible.

Use comments to explain:

- Business rules
- Non-obvious decisions
- Complex algorithms
- Temporary workarounds (with references)

Avoid comments that simply repeat the code.