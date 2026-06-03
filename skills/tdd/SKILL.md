---
name: tdd
description: Enforce Red-Green-Refactor test-driven development. Use this skill when writing new logic, fixing bugs, or building new features. Triggers automatically when the user asks to implement anything with business logic, fix a failing test, or add test coverage.
---

This skill enforces TDD discipline using your project's test runner. The default agent behavior is to write code and add tests later — this skill overrides that.

> See your project's stack reference in `stacks/` for specific test commands, test placement conventions, and runner configuration.

## The Cycle

**RED → GREEN → REFACTOR. Always in that order. Never write production code before a failing test.**

### 1. RED — Write a failing test first
- Identify the smallest behavior to test
- Write the test in the appropriate location (see Test Placement)
- Run it and **confirm it fails for the RIGHT reason** — not a syntax error or import fail

### 2. GREEN — Write the minimum code to pass
- Write only what the test requires. Nothing more.
- No "while I'm here" additions. No speculative features.
- Run the test again — confirm it goes GREEN
- Run the full suite to confirm no regressions

### 3. REFACTOR — Improve without breaking
- Clean up duplication, naming, structure
- Run the test suite after every refactor step
- Run type/compile check — must pass
- Commit: `test: add X coverage` + `feat: implement X`

## Test Placement

Place tests close to the code they test. Prefer co-located test files for units, dedicated test directories for integration tests, and `e2e/` for end-to-end flows.

> See your project's stack reference in `stacks/` for exact file placement conventions.

**Prefer testing pure functions first.** Extract computation out of framework code so it can be tested without mocks.

## Anti-Rationalizations

| Excuse | Counter |
|--------|---------|
| "It's a simple change, tests aren't needed" | Simple changes break things. Write the test first — it takes 2 min. |
| "I'll add tests after I see if the approach works" | You won't. The context will shift. Write the test now. |
| "UI / native / rendering is hard to test" | Test the pure logic that feeds the rendering, not the rendering itself. |
| "The type system is the test" | Types catch shape errors. Tests catch logic errors. Both are required. |

## Gotchas

- **Framework calls** (API invocations, HTTP clients, IPC): test the logic that prepares args and processes results, not the call itself. Mock the boundary.
- **DOM-dependent code**: extract pure computation into util functions and test those.
- **Async/await**: mock slow or external dependencies.
- **Float math**: use fixed seed inputs with known expected outputs; don't assert floats without tolerances.
- **Database**: mock the client or use a test database; never run tests against production data.
- **DI containers**: use test doubles registered in the container, not manual mocking when possible.

## Verification Gate

Before marking any implementation complete:
1. New tests pass ✓
2. Full test suite passes (no regressions) ✓
3. Type/compile check — zero errors ✓
4. For auth/payment/critical paths: E2E tests pass ✓
