---
name: scope-and-slice-planning
description: Break large requests into atomic implementation slices with acceptance checks. Use when tasks feel too big, ambiguous, or risky. Activates on "implement all", "big refactor", "new module", or when multiple features are requested together.
---

Use this skill to turn large asks into small shippable slices.

## Process

### 1. Normalize Request
- Extract user goal, constraints, and non-goals.
- Identify anything that is out of scope for this session.

### 2. Slice by User Value
- Each slice must be independently testable.
- Each slice should touch minimal files.
- A slice is not "build the feature" — it's "add the store" or "wire up the route".

### 3. Define Acceptance Per Slice
- Behavior expected
- Validation command(s) that prove it works (type check, test suite, build)
- Rollback strategy if it breaks something

> See your project's stack reference in `stacks/` for specific validation commands.

### 4. Order Slices
- Foundation first (types, models, schema)
- Backend before frontend (APIs must exist before UI consumes them)
- UX before polish
- Docs only after behavior stabilizes

### 5. Execute One Slice at a Time
- Implement
- Validate
- Report delta before moving to the next slice
- If 3+ slices are independent, consider dispatching via `subagent-driven-development` instead of sequential execution.

## Slice Quality Rules
- A slice is too big if it cannot be verified in under 10 minutes.
- Avoid mixing feature work and broad cleanup in the same slice.
- Keep changes reversible.
- Each slice should compile/build independently — no "it'll work when I finish the next slice."

## Evidence Required
- Numbered slice list
- Acceptance criteria for each slice
- Validation result per completed slice
