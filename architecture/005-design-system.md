# Document 005 — Design System

| Field | Value |
|-------|-------|
| Document ID | DOC-005 |
| Name | Design System |
| Version | 0.1.0 |
| Status | Draft |
| Owner | Nebula Labs |
| Last Updated | 2026-07-20 |

---

# 1. Purpose

The Nebula Design System defines the visual language and interaction standards for the entire ERP platform.

It provides a unified set of design principles, tokens, components, layouts, patterns, and accessibility guidelines to ensure consistency across every module, regardless of the development team or feature.

The Design System is the single source of truth for the visual identity of Nebula ERP.

---

# 2. Objectives

The Design System must:

- Provide a consistent visual language
- Standardize UI components
- Improve usability
- Increase development speed
- Ensure accessibility
- Support responsive layouts
- Support light and dark themes
- Reduce design inconsistency
- Enable scalable component development
- Maintain brand identity

---

# 3. Design Philosophy

Nebula ERP follows five core design principles.

## Clarity

Interfaces should communicate intent immediately.

Avoid unnecessary visual noise.

---

## Consistency

Every interaction should behave predictably.

Users should not relearn interface behavior between modules.

---

## Efficiency

Experienced users should complete tasks quickly.

Support:

- Keyboard shortcuts
- Bulk actions
- Smart defaults
- Progressive disclosure

---

## Accessibility

Interfaces should be usable by everyone.

Accessibility is treated as a fundamental requirement, not an enhancement.

---

## Scalability

The design language must support hundreds of screens while maintaining visual consistency.

---

# 4. Brand Identity

Nebula ERP represents:

- Professionalism
- Precision
- Reliability
- Modern Engineering
- Enterprise Quality

Visual personality:

- Clean
- Minimal
- Functional
- Data-focused
- Calm
- Trustworthy

The interface should prioritize readability over decoration.

---

# 5. Color System

Colors are organized by semantic purpose rather than feature ownership.

## Primary

Used for:

- Primary actions
- Active navigation
- Interactive emphasis
- Focus states

---

## Secondary

Used for:

- Supporting actions
- Informational UI
- Neutral emphasis

---

## Success

Used for:

- Completed actions
- Positive status
- Successful operations

---

## Warning

Used for:

- Pending actions
- Attention required
- Validation warnings

---

## Danger

Used for:

- Destructive actions
- Errors
- Critical alerts

---

## Neutral

Used for:

- Borders
- Backgrounds
- Text hierarchy
- Disabled states

Component behavior should rely on semantic tokens instead of hardcoded colors.

---

# 6. Design Tokens

The Design System uses tokens rather than fixed values.

Categories include:

- Colors
- Typography
- Spacing
- Radius
- Shadows
- Borders
- Opacity
- Motion
- Z-Index

Example naming:

```text
color.primary

color.success

spacing.md

radius.lg

shadow.card

font.heading

motion.fast
```

Components consume tokens instead of raw CSS values.

---

# 7. Typography

Typography hierarchy should remain consistent.

Text categories:

| Type | Usage |
|------|-------|
| Display | Landing pages |
| Heading 1 | Page titles |
| Heading 2 | Section titles |
| Heading 3 | Cards |
| Heading 4 | Dialog titles |
| Body | General content |
| Small | Supporting text |
| Caption | Metadata |
| Code | Technical content |

Guidelines:

- Consistent line height
- Predictable spacing
- Readable contrast
- Avoid excessive font weights

---

# 8. Spacing System

Use a consistent spacing scale.

Recommended scale:

```
4px

8px

12px

16px

20px

24px

32px

40px

48px

64px
```

Rules:

- Equal spacing improves readability.
- Avoid arbitrary spacing values.
- Components should align to the spacing system.

---

# 9. Layout Grid

Nebula ERP uses a responsive grid.

Layout principles:

- 12-column grid
- Consistent gutters
- Responsive breakpoints
- Maximum content width
- Flexible containers

Dashboard layouts should prioritize information density without overwhelming users.

---

# 10. Iconography

Primary icon library:

- Lucide React

Guidelines:

- Simple
- Consistent stroke width
- Recognizable
- Accessible

Icons should reinforce meaning rather than replace text.

Avoid using icons without accompanying labels in critical workflows.

---

# 11. Elevation & Shadows

Elevation communicates hierarchy.

Recommended levels:

| Level | Usage |
|--------|-------|
| Level 0 | Flat surfaces |
| Level 1 | Cards |
| Level 2 | Dropdowns |
| Level 3 | Popovers |
| Level 4 | Dialogs |
| Level 5 | Global overlays |

Shadows should remain subtle.

Avoid excessive visual depth.

---

# 12. Component Standards

All UI components shall follow a shared design language.

General principles:

- Single responsibility
- Predictable behavior
- Keyboard accessible
- Responsive
- Theme-aware
- Token-driven styling
- Composable APIs
- Strong TypeScript support

Every component should expose only the minimum required public API.

---

# 13. Buttons

Buttons communicate user actions.

Supported variants:

- Primary
- Secondary
- Outline
- Ghost
- Link
- Destructive

Supported sizes:

- Small
- Medium
- Large
- Icon

Supported states:

- Default
- Hover
- Focus
- Active
- Disabled
- Loading

Rules:

- Only one primary action per section where practical.
- Destructive actions require clear visual distinction.
- Loading buttons should preserve width to avoid layout shift.
- Buttons should display visible focus indicators.

---

# 14. Inputs

Standard input components include:

- Text Input
- Password
- Email
- Number
- Search
- Textarea
- Select
- Combobox
- Checkbox
- Radio Group
- Switch
- Date Picker
- Time Picker
- File Upload

Input requirements:

- Label
- Placeholder (optional)
- Helper text
- Validation message
- Disabled state
- Read-only state
- Loading state (where applicable)

Inputs should clearly distinguish required and optional fields.

---

# 15. Forms

Forms should provide a consistent experience.

Standard elements:

- Section titles
- Labels
- Validation messages
- Required indicators
- Inline help
- Submit actions
- Reset actions

Form behaviors:

- Validate on submit
- Optional live validation
- Preserve entered values
- Prevent duplicate submission
- Display success confirmation
- Display actionable error messages

Long forms should be divided into logical sections or steps.

---

# 16. Tables

Tables are optimized for business data.

Required capabilities:

- Sorting
- Filtering
- Pagination
- Column resizing
- Column visibility
- Row selection
- Bulk actions
- Sticky headers
- Responsive behavior

Optional capabilities:

- Row expansion
- Grouping
- Inline editing
- Export

Large datasets should support virtualization.

---

# 17. Navigation

Primary navigation includes:

- Sidebar
- Top Navigation
- Breadcrumbs
- User Menu
- Notifications

Sidebar behavior:

- Expand/Collapse
- Nested menus
- Active state
- Permission-aware visibility
- Search (optional)

Navigation should always communicate the user's current location.

---

# 18. Cards

Cards group related information.

Common card types:

- Summary Card
- Statistics Card
- Information Card
- Activity Card
- Action Card

Card structure:

- Header
- Content
- Footer (optional)
- Actions (optional)

Cards should avoid excessive nesting.

---

# 19. Dialogs & Drawers

Dialogs are used for focused interactions.

Examples:

- Confirmation
- Delete
- Edit
- Create
- Warning

Drawers are preferred for:

- Entity details
- Quick edits
- Contextual workflows

Requirements:

- Focus trapping
- Keyboard navigation
- Escape to close (when appropriate)
- Accessible labels
- Prevent accidental data loss

Critical destructive actions should require explicit confirmation.

---

# 20. Badges & Status Indicators

Badges communicate concise status information.

Common statuses:

- Active
- Inactive
- Draft
- Pending
- Processing
- Completed
- Failed
- Archived

Status colors should use semantic tokens.

Never rely solely on color to communicate status.

---

# 21. Empty States

Every empty screen should provide guidance.

Empty states should include:

- Friendly illustration or icon
- Clear title
- Short explanation
- Primary action
- Secondary action (optional)

Examples:

- No Products
- No Customers
- No Reports
- No Notifications

Avoid presenting users with blank screens.

---

# 22. Loading States

Loading feedback improves perceived performance.

Supported loading patterns:

- Skeleton screens
- Progress bars
- Spinners
- Shimmer placeholders

Guidelines:

- Prefer skeletons for page content.
- Use progress indicators for long-running operations.
- Avoid unnecessary animation for very short loads.
- Preserve layout during loading to reduce visual shifts.

Loading indicators should accurately reflect application state.

---

# 23. Feedback Patterns

User feedback should be timely and consistent.

Supported feedback types:

- Success
- Information
- Warning
- Error

Delivery methods:

- Toast notifications
- Inline alerts
- Banner messages
- Modal confirmations

Messages should:

- Clearly describe the outcome.
- Suggest corrective actions where appropriate.
- Avoid technical jargon.
- Remain concise and actionable.

---

## ✅ End of Part 2

**Don't commit yet.**

**Part 3** will include:

- Motion & Animation
- Dark & Light Themes
- Accessibility Standards
- Design Tokens Reference
- Acceptance Criteria
- AI Context Summary
- Revision History
- Git Commit
- Progress Tracker

---

# 24. Motion & Animation

Motion should improve usability rather than distract users.

Animations should communicate:

- State changes
- Navigation
- Loading
- Success
- Error
- Focus transitions

Recommended durations:

| Animation | Duration |
|-----------|-----------|
| Hover | 100–150 ms |
| Button Press | 75–100 ms |
| Dialog | 200–250 ms |
| Drawer | 250–300 ms |
| Toast | 200 ms |
| Page Transition | 250–350 ms |

Guidelines:

- Use easing consistently.
- Avoid excessive movement.
- Respect reduced motion preferences.
- Never block user interaction with animations.

---

# 25. Dark & Light Themes

Nebula ERP supports both Light and Dark themes.

Theme requirements:

- Semantic color tokens
- Automatic system preference detection
- Manual user override
- Persistent theme preference
- Accessible contrast ratios

All components must:

- Render correctly in both themes
- Avoid hardcoded colors
- Use semantic design tokens
- Maintain consistent spacing and typography

Theme switching should occur without requiring a page reload.

---

# 26. Accessibility Standards

Accessibility requirements apply to every component.

Minimum standards:

- WCAG 2.2 AA compliance where practical
- Keyboard-only navigation
- Visible focus indicators
- Screen reader compatibility
- Semantic HTML structure
- Sufficient color contrast
- Proper form labels
- Accessible error messaging
- Logical tab order

Accessibility should be validated throughout the design and development process.

---

# 27. Design Tokens Reference

Core design token categories include:

## Color

```text
color.primary
color.secondary
color.success
color.warning
color.danger
color.background
color.surface
color.border
color.text.primary
color.text.secondary
```

---

## Typography

```text
font.family
font.size.xs
font.size.sm
font.size.md
font.size.lg
font.weight.normal
font.weight.medium
font.weight.bold
```

---

## Spacing

```text
spacing.xs
spacing.sm
spacing.md
spacing.lg
spacing.xl
spacing.2xl
```

---

## Radius

```text
radius.sm
radius.md
radius.lg
radius.xl
radius.full
```

---

## Shadow

```text
shadow.sm
shadow.md
shadow.lg
shadow.xl
```

---

## Motion

```text
motion.fast
motion.normal
motion.slow
```

All UI components should reference design tokens instead of hardcoded values.

---

# 28. Acceptance Criteria

The Design System is complete when:

- Design principles are documented.
- Brand identity is defined.
- Semantic color system is established.
- Design tokens are standardized.
- Typography hierarchy is consistent.
- Spacing and layout grids are documented.
- Component standards are defined.
- Navigation patterns are standardized.
- Forms and tables follow shared conventions.
- Motion and animation guidelines are documented.
- Light and Dark themes are supported.
- Accessibility requirements are integrated across all components.

---

# 29. AI Context Summary

## Summary

The Design System defines Nebula ERP's visual language, reusable UI components, design tokens, interaction patterns, accessibility standards, themes, and motion guidelines. It serves as the foundation for building a consistent, scalable, and user-friendly interface across all ERP modules.

## Dependencies

- DOC-001 — System Architecture
- DOC-004 — Frontend Architecture
- Business Specification (Modules 001–024)

## Referenced By

- Infrastructure
- Security
- AI Architecture
- Development Standards
- All Frontend Features

---

# Revision History

| Version | Date | Author | Changes |
|----------|------------|--------------|-------------------------------|
| 0.1.0 | 2026-07-20 | Nebula Labs | Initial Design System specification |