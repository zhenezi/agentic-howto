---
name: architecture-review
description: ADR-driven architecture decision review. Use this skill when making significant structural decisions — new services, database changes, API redesigns, dependency additions, or cross-cutting concerns. Activates on "should we use X", "architecture", "design decision", "ADR", "tech choice", or when a change affects multiple modules.
---

This skill enforces lightweight Architecture Decision Records (ADRs) for decisions that are expensive to reverse. Not every code change needs an ADR — use judgment.

## When to Write an ADR

- Adding a new external dependency or framework
- Changing the database schema in a non-trivial way
- Splitting or merging services/modules
- Choosing between competing approaches with trade-offs
- Any decision you'd want to explain to a future teammate

## ADR Template

Write to `docs/adr/NNNN-<title>.md`:

```markdown
# NNNN — <Title>

**Status**: Proposed | Accepted | Deprecated | Superseded by NNNN
**Date**: YYYY-MM-DD
**Deciders**: <who was involved>

## Context
What is the issue motivating this decision? What constraints exist?

## Decision
What is the change being proposed or made?

## Consequences
### Positive
- ...
### Negative
- ...
### Neutral
- ...

## Alternatives Considered
| Option | Pros | Cons | Why rejected |
|--------|------|------|-------------|
| ... | ... | ... | ... |
```

## Review Checklist

### Structural Integrity
- [ ] Change respects existing module boundaries
- [ ] No circular dependencies introduced
- [ ] New module has a clear single responsibility
- [ ] Public API surface is minimal (expose only what consumers need)

### Cross-Cutting Concerns
- [ ] Error handling strategy is consistent with the rest of the codebase
- [ ] Logging follows existing patterns (structured, appropriate level)
- [ ] Auth/authz model is not bypassed or duplicated
- [ ] Configuration follows existing patterns (env vars, config files)

### Data Flow
- [ ] Data ownership is clear (one authoritative source per entity)
- [ ] No N+1 query patterns introduced
- [ ] Caching strategy is explicit (if applicable)
- [ ] Migration path exists for schema changes

> See your project's stack reference in `stacks/` for stack-specific architecture review items.

## Verification Gate

- [ ] ADR written for non-trivial decisions
- [ ] No circular dependencies
- [ ] Module boundaries respected
- [ ] Cross-cutting concerns handled consistently
