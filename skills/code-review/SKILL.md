---
name: code-review
description: Structured code review checklist and process. Use this skill when reviewing PRs, performing self-review before merge, or when asked to review code quality. Activates on "review this", "code review", "PR review", "check my code", "is this ready to merge".
---

This skill provides a systematic code review process. Use it for self-review (before pushing) and peer review (PRs from others).

## Review Dimensions

### 1. Correctness
- [ ] Does the code do what it claims to do?
- [ ] Are edge cases handled (null, empty, boundary values)?
- [ ] Are error paths handled (not just happy path)?
- [ ] Does the code match the spec / issue description?

### 2. Security
- [ ] No hardcoded secrets
- [ ] Input validated at boundaries
- [ ] Auth/authz checked server-side
- [ ] No SQL injection, XSS, or command injection vectors
- [ ] Refer to `security-review` skill for deep review

### 3. Performance
- [ ] No N+1 query patterns
- [ ] No unbounded loops or recursion
- [ ] Large lists virtualized or paginated
- [ ] No unnecessary work in hot paths (render loops, request handlers)

### 4. Maintainability
- [ ] Names are clear and intention-revealing
- [ ] No dead code or commented-out blocks
- [ ] Functions are small and single-purpose
- [ ] Abstractions are justified (not premature)
- [ ] No magic numbers — use named constants

### 5. Testing
- [ ] New behavior has test coverage
- [ ] Tests test behavior, not implementation details
- [ ] Tests are deterministic (no flaky assertions)
- [ ] Edge cases covered in tests

### 6. Stack-Specific

> See your project's stack reference in `stacks/` for the technology-specific code review checklist.

## Review Process

1. **Understand the context** — read the issue/spec before reading the code
2. **Big picture first** — scan the file list and architecture impact
3. **Detail review** — go through each file methodically
4. **Run it** — check out the branch, run tests, smoke test manually
5. **Feedback** — categorize comments as: blocking, suggestion, question, nit

## Comment Categories

| Prefix | Meaning |
|--------|---------|
| `[blocking]` | Must fix before merge |
| `[suggestion]` | Recommended but not required |
| `[question]` | Need clarification on intent |
| `[nit]` | Style/preference, take or leave |
| `[praise]` | Something done well — acknowledge it |

## Anti-Rationalizations

| Excuse | Counter |
|--------|---------|
| "I wrote it, so it's fine" | You're the worst reviewer of your own code. Use the checklist. |
| "It's a small change, no review needed" | Small changes break things. 2-minute review catches 80% of issues. |
| "Tests pass, ship it" | Tests check what you thought to test. Review checks what you didn't. |

## Verification Gate

- [ ] All review dimensions checked
- [ ] No blocking issues remain
- [ ] Tests pass
- [ ] Type/compile check passes
- [ ] Self-review completed before requesting peer review
