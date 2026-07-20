# Document 004 — Frontend Architecture

| Field | Value |
|-------|-------|
| Document ID | DOC-004 |
| Name | Frontend Architecture |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

This document defines the frontend architecture for Nebula ERP.

It establishes engineering standards for application structure, routing, state management, UI composition, API communication, authentication, authorization, forms, caching, accessibility, responsiveness, testing, and performance.

The goal is to ensure a scalable, maintainable, and consistent frontend across every ERP module.

---

# 2. Objectives

The frontend architecture must:

- Support modular development
- Provide consistent user experience
- Be fully responsive
- Maintain high performance
- Support multi-tenancy
- Enforce permission-aware interfaces
- Optimize API communication
- Enable offline-friendly behavior where practical
- Simplify testing
- Support future expansion

---

# 3. Technology Stack

| Layer | Technology |
|--------|------------|
| Framework | React 19 |
| Build Tool | Vite |
| Language | TypeScript |
| Styling | Tailwind CSS v4 |
| Components | shadcn/ui |
| Icons | Lucide React |
| Data Fetching | TanStack Query |
| Client State | Zustand |
| Forms | React Hook Form |
| Validation | Zod |
| Routing | React Router |
| Charts | Recharts |
| Tables | TanStack Table |
| Drag & Drop | dnd-kit |
| Date Utilities | date-fns |

Future support may include React Server Components where appropriate.

---

# 4. Architectural Principles

The frontend follows these principles:

- Component-driven architecture
- Feature-based organization
- Composition over inheritance
- Single responsibility
- Predictable state management
- Type safety
- Accessibility by default
- Responsive-first design
- Performance-conscious rendering

Business logic should remain outside presentation components whenever possible.

---

# 5. Project Structure

Recommended directory structure:

```text
src/

├── app/
├── assets/
├── components/
├── features/
├── hooks/
├── layouts/
├── lib/
├── pages/
├── providers/
├── routes/
├── services/
├── stores/
├── styles/
├── types/
├── utils/
├── main.tsx
```

Each directory has a single, well-defined responsibility.

---

# 6. Feature Organization

Each business feature should remain self-contained.

Example:

```text
features/products/

├── api/
├── components/
├── hooks/
├── pages/
├── schemas/
├── store/
├── types/
├── utils/
├── index.ts
```

Shared UI components belong in the global `components` directory.

---

# 7. Routing Architecture

The application uses nested routing.

Example:

```
/

↓

Dashboard

↓

Products

↓

Product Details

↓

Edit Product
```

Routing should support:

- Lazy loading
- Nested layouts
- Protected routes
- Error routes
- Dynamic parameters
- Breadcrumb generation

All protected routes require authentication.

---

# 8. Layout System

Layouts define shared page structure.

Primary layouts include:

- Authentication Layout
- Dashboard Layout
- Fullscreen Layout
- Public Layout

Dashboard Layout consists of:

- Sidebar
- Top Navigation
- Breadcrumbs
- Content Area
- Notification Center
- Footer (optional)

Layouts should avoid business-specific logic.

---

# 9. State Management

State is divided by responsibility.

## Server State

Managed by:

- TanStack Query

Examples:

- Products
- Customers
- Reports
- Inventory
- Notifications

---

## Client State

Managed by:

- Zustand

Examples:

- Sidebar state
- Theme
- Active filters
- Selected rows
- Modal visibility
- Wizard progress

Avoid duplicating server state inside client stores.

---

# 10. API Layer

All API communication passes through a centralized service layer.

Responsibilities include:

- Authentication
- Token refresh
- Error handling
- Request retries
- Pagination helpers
- Upload handling
- Download handling

Features should never call `fetch()` directly.

Use reusable API clients instead.

---

# 11. Authentication Flow

Frontend authentication consists of:

```
Login

↓

JWT Received

↓

Secure Storage

↓

Authenticated Requests

↓

Automatic Token Refresh

↓

Logout
```

Frontend responsibilities include:

- Session restoration
- Token refresh
- Logout handling
- Permission loading
- Organization context loading

Authentication state should be initialized before protected routes render.

---

# 12. Permission-Aware User Interface

The frontend shall enforce permission-aware rendering.

Every authenticated user receives:

- Organization Context
- Branch Context
- Assigned Roles
- Effective Permissions
- Feature Flags

UI elements should only be displayed when the user has the required permission.

Examples:

- Hide unauthorized menu items
- Disable restricted actions
- Prevent navigation to protected pages
- Display read-only views when applicable

Permission checks improve user experience but **do not replace backend authorization**.

---

# 13. Component Standards

Components are divided into three categories.

## UI Components

Reusable presentation components.

Examples:

- Button
- Input
- Card
- Badge
- Dialog
- Table
- Tooltip

These components contain no business logic.

---

## Shared Components

Reusable business-aware components.

Examples:

- DataTable
- SearchBar
- EntityPicker
- StatusBadge
- FileUploader
- Pagination
- EmptyState

---

## Feature Components

Specific to one feature.

Examples:

- ProductForm
- CustomerDetails
- PurchaseApprovalDialog
- StockMovementTimeline

Feature components should remain inside their corresponding feature directories.

---

# 14. Forms & Validation

Forms shall use:

- React Hook Form
- Zod

Validation occurs in two stages.

## Client Validation

Examples:

- Required fields
- Length validation
- Email format
- Number ranges
- Date validation

Provides immediate user feedback.

---

## Server Validation

Handles:

- Business rules
- Duplicate records
- Permission validation
- Referential integrity

Server validation remains the source of truth.

---

## Form Principles

- Controlled submission
- Dirty state tracking
- Loading indicators
- Disabled submit during requests
- Inline validation messages
- Unsaved changes warning

---

# 15. Data Fetching & Caching

Server data is managed through TanStack Query.

Responsibilities:

- Fetching
- Caching
- Background refetching
- Optimistic updates
- Retry logic
- Pagination
- Infinite scrolling
- Cache invalidation

Recommended cache keys:

```
organization

products

customers

inventory

reports

settings
```

Cache keys should always include tenant-specific context where applicable.

---

# 16. Error Boundaries

Application errors should be isolated.

Error boundaries should protect:

- Entire application
- Feature pages
- Dashboard widgets
- Complex visualizations
- Third-party integrations

Fallback UI should include:

- Friendly error message
- Retry option
- Navigation back to safety
- Error reference ID (if available)

Critical failures should be logged automatically.

---

# 17. Accessibility

Accessibility is a core requirement.

The application should follow WCAG 2.2 AA where practical.

Requirements include:

- Keyboard navigation
- Visible focus indicators
- Semantic HTML
- Proper heading hierarchy
- Accessible labels
- ARIA attributes where appropriate
- Screen reader compatibility
- Color contrast compliance

Interactive elements must remain usable without a mouse.

---

# 18. Responsive Design

Nebula ERP follows a mobile-first responsive strategy.

Supported breakpoints:

| Size | Target Devices |
|------|----------------|
| XS | Small phones |
| SM | Phones |
| MD | Tablets |
| LG | Laptops |
| XL | Desktops |
| 2XL | Large displays |

Responsive behaviors include:

- Collapsible sidebar
- Adaptive tables
- Responsive forms
- Flexible grid layouts
- Touch-friendly controls

No horizontal scrolling should be required for standard workflows on supported devices.

---

# 19. Performance Optimization

Frontend performance principles:

- Lazy-load routes
- Code splitting
- Dynamic imports
- Memoize expensive computations
- Virtualize large lists
- Debounce search inputs
- Throttle resize and scroll handlers
- Optimize image loading
- Minimize unnecessary re-renders

Recommended performance targets:

| Metric | Target |
|--------|--------|
| Initial Page Load | < 2 seconds |
| Route Transition | < 300 ms |
| Search Response | < 200 ms |
| Dashboard Render | < 1 second |

Performance should be monitored continuously throughout development.

---

## ✅ End of Part 2

**Don't commit yet.**

**Part 3** will include:

- Frontend Testing Strategy
- Internationalization
- Offline Strategy
- Build & Deployment
- Acceptance Criteria
- AI Context Summary
- Revision History
- Git Commit
- Progress Tracker

---

# 20. Frontend Testing Strategy

Testing is required for all production-ready frontend code.

## Unit Testing

Unit tests should cover:

- UI Components
- Utility Functions
- Custom Hooks
- Validation Schemas
- Client Stores

Recommended tools:

- Vitest
- React Testing Library

---

## Component Testing

Component tests validate:

- Rendering
- User interactions
- Accessibility
- Form behavior
- Error states
- Loading states

Reusable UI components should maintain high test coverage.

---

## Integration Testing

Integration tests validate:

- API interactions
- Routing
- Authentication flow
- State management
- Permission-aware rendering
- Feature workflows

Mock external services where appropriate.

---

## End-to-End Testing

Critical business workflows should be covered using browser automation.

Recommended scenarios:

- Login & Logout
- Product Management
- Purchase Workflow
- Sales Workflow
- Inventory Operations
- File Upload
- Report Generation
- User Management

Recommended tool:

- Playwright

---

# 21. Internationalization (i18n)

Nebula ERP supports multiple languages.

Requirements:

- Translation dictionaries
- Locale-aware formatting
- Right-to-left (RTL) readiness
- Language switching
- Pluralization support
- Localized validation messages

Localized content includes:

- Dates
- Numbers
- Currency
- Time
- UI Labels
- Error Messages

User language preference should persist across sessions.

---

# 22. Offline Strategy

The frontend should degrade gracefully during connectivity issues.

Supported behaviors:

- Detect offline status
- Notify users of connectivity changes
- Retry failed requests where appropriate
- Preserve unsaved form data
- Queue selected non-critical actions (future)
- Display cached data when available

Critical financial operations should always require server confirmation.

---

# 23. Build & Deployment

Frontend builds should be:

- Deterministic
- Optimized
- Versioned
- Environment-aware

Deployment stages:

```
Development

↓

Continuous Integration

↓

Staging

↓

Production
```

Build optimizations include:

- Tree shaking
- Asset hashing
- Code splitting
- CSS optimization
- Image optimization
- Source maps (non-production configurable)

Environment-specific configuration should be injected during build or deployment without hardcoding secrets.

---

# 24. Browser Support

Supported browsers:

| Browser | Support Level |
|----------|---------------|
| Chrome | Current & previous major version |
| Edge | Current & previous major version |
| Firefox | Current & previous major version |
| Safari | Current & previous major version |

General requirements:

- Responsive layout
- Keyboard accessibility
- Progressive enhancement
- Graceful degradation for unsupported features

Internet Explorer is not supported.

---

# 25. Acceptance Criteria

The frontend architecture is complete when:

- Application structure is standardized.
- Feature organization is consistent.
- Routing supports protected and nested routes.
- State management separates client and server state.
- API communication uses a centralized service layer.
- Forms use standardized validation.
- Permission-aware rendering is implemented.
- Accessibility guidelines are followed.
- Responsive layouts function across supported devices.
- Testing strategy covers unit, integration, and end-to-end testing.
- Build and deployment standards are documented.

---

# 26. AI Context Summary

## Summary

The Frontend Architecture defines how Nebula ERP is implemented using React, TypeScript, and a component-driven design. It establishes standards for routing, layouts, state management, forms, validation, API communication, accessibility, responsive design, testing, internationalization, offline behavior, and deployment to deliver a scalable and maintainable user experience.

## Dependencies

- DOC-001 — System Architecture
- DOC-002 — Database Architecture
- DOC-003 — Backend Architecture
- Business Specification (Modules 001–024)

## Referenced By

- Design System
- Infrastructure
- Security
- AI Architecture
- Development Standards

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial Frontend Architecture specification |