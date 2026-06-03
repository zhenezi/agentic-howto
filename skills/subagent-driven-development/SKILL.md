---
name: subagent-driven-development
description: Structured subagent dispatch with two-stage review for parallel slice execution. Use when a plan has 3+ independent slices, when parallel work can compress timelines, or when sequential execution would waste context. Activates on "implement this plan", "build these slices", "parallelize this work", or any plan with 3+ independent slices.
---

This skill replaces ad-hoc subagent usage with a disciplined dispatch-review-integrate cycle. The key insight: the implementing agent has confirmation bias about its own output. A separate review pass catches what it misses.

## When to Use

- A plan (from `scope-and-slice-planning` or `new-feature`) has 3+ slices
- At least 2 slices are independent (no data dependency between them)
- Each slice touches a bounded set of files (not cross-cutting refactors)

## When NOT to Use

- Slices share mutable state or write to the same files
- You need to see incremental results before deciding next steps
- The task is exploratory (research first, then decide)
- Total work is < 30 minutes — overhead of dispatch isn't worth it

## Process

### 1. PLAN — Produce Numbered Slices

Use `scope-and-slice-planning` to produce a numbered slice list. Each slice must have:
- Title and scope (which files, which concern)
- Acceptance criteria (what "done" looks like)
- Validation command (compile, typecheck, test, manual check)

### 2. CLASSIFY — Mark Dependencies

Label each slice:

| Label | Meaning | Rule |
|-------|---------|------|
| `parallel` | Independent of other slices | Can be dispatched immediately |
| `sequential(N)` | Depends on slice N completing first | Must wait for slice N's output |
| `sequential(N,M)` | Depends on slices N and M | Must wait for both |

Group into **waves**:
- **Wave 1**: All `parallel` slices
- **Wave 2**: Slices that depend only on Wave 1 outputs
- **Wave N**: Continue until all slices are assigned

### 3. DISPATCH — Write Subagent Prompts

Each subagent prompt must include ALL of these:

```
1. SLICE: [number] — [title]
2. DELIVERABLE: [exact files to create or modify, with full paths]
3. STACK: Load [stacks/xxx.md] for conventions and commands
4. ACCEPTANCE CRITERIA:
   - [criterion 1]
   - [criterion 2]
5. CONSTRAINTS:
   - Do not modify files outside the listed paths
   - Do not add dependencies without noting them
   - Follow existing code style — match adjacent files
6. ANTI-PATTERNS: [from tasks/lessons.md if available]
7. VALIDATION: Run [specific command] and confirm it passes
```

**Rules:**
- One concern per subagent. Never "implement and test" in the same dispatch.
- If a slice needs tests, dispatch a second subagent for tests after the implementation subagent completes.
- Dispatch all Wave 1 subagents in parallel.
- Wait for Wave 1 to complete before dispatching Wave 2.

### 4. REVIEW — Two-Stage Gate

Every subagent result goes through two review stages before integration:

**Stage 1 — Spec Compliance**
- [ ] Output matches the deliverable list (correct files, correct paths)
- [ ] All acceptance criteria are met
- [ ] Validation command passes (compile, typecheck, test)
- [ ] No files outside scope were modified
- [ ] No undeclared dependencies added

**Stage 2 — Quality Gate**
- [ ] Code passes the `code-review` skill checklist (naming, structure, duplication)
- [ ] Security: no input trust violations, no auth gaps (per `security-review`)
- [ ] Error handling: failures don't crash, errors are surfaced to the user
- [ ] No TODO/FIXME/HACK comments left without a tracking issue
- [ ] Consistent with adjacent code style

**If either stage fails:** fix in the current context (don't re-dispatch for small fixes) or re-dispatch with the specific failure noted in the prompt.

### 5. INTEGRATE — Merge and Resolve

- Apply all reviewed outputs to the working tree
- Resolve any file conflicts between parallel slices
- Run full validation suite across all changed files
- If integration breaks something, diagnose whether it's a slice boundary issue (fix the integration) or a slice quality issue (re-review that slice)

### 6. VERIFY — End-to-End Check

- [ ] Full compile/typecheck passes
- [ ] Test suite passes (existing + new)
- [ ] Manual smoke test of the integrated feature
- [ ] No regressions in adjacent functionality
- [ ] Acceptance criteria from the original plan are all met

## Subagent Prompt Anti-Patterns

| Anti-Pattern | Why It Fails | Do Instead |
|-------------|-------------|------------|
| "Implement feature X" (vague) | Agent invents scope, misses requirements | List exact files and acceptance criteria |
| "Implement and write tests" | Tests become afterthought, coverage is shallow | Dispatch implementation and test-writing as separate subagents |
| No stack reference | Agent uses wrong patterns, imports, conventions | Always include which `stacks/*.md` to load |
| No acceptance criteria | No way to review objectively | Copy criteria from the slice plan |
| Dispatching cross-cutting changes | Multiple subagents edit the same files, conflicts | Keep cross-cutting work sequential in the main context |
| Skipping Stage 2 review | "It compiles" ≠ "it's good" | Always run both stages |

## Evidence Required

- Numbered slice list with dependency labels and wave assignments
- Subagent prompt for each dispatched slice
- Stage 1 + Stage 2 review results per slice
- Integration validation result
- End-to-end verification checklist
