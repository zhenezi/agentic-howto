---
name: ui-regression-guard
description: Prevent visual and interaction regressions in frontend changes. Use when editing components, upload flows, canvas rendering, layout behavior, or responsive UI. Activates on any UI bugfix or feature touching user-facing components.
---

Use this skill to avoid shipping UI regressions while moving fast.

## Checklist

### 1. Interaction Consistency
- Shared UI primitives reused (same button variant, same modal pattern, same form controls)
- No duplicate implementations of the same interaction pattern
- Shared components organized in a common library or directory

### 2. Visual Integrity
- Aspect ratio preserved for media rendering (images, video, canvas)
- No clipped controls at common viewport sizes (375px, 768px, 1280px, 1440px)
- Dark mode / light mode both verified (if applicable)

### 3. State Integrity
- Clear/reset controls clean all related state
- Empty state, loading state, and loaded state all render correctly
- Error state shown (not a blank screen)

### 4. Responsiveness
- Validate desktop and compact (mobile/narrow) widths
- No overflow in sidebars, panels, or modals
- Touch targets are at least 44×44px on mobile

### 5. Accessibility Basics
- Buttons have visible labels or `aria-label` attributes
- Interactive elements are keyboard reachable (Tab order correct)
- Focus indicators visible
- Color contrast meets WCAG 2.1 AA (4.5:1 for normal text)

## Fast Verification
1. Run type/compile check
2. Smoke test changed screens with representative content
3. Walk the empty → loaded → cleared → reloaded loop
4. Check at a narrow viewport (375px width)

## Evidence Required
- Regression checklist completed
- Validation command output summary
- Explicit mention of UI states tested (empty, loaded, error, cleared)
