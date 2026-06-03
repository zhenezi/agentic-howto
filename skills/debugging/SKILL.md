---
name: debugging
description: Systematic debugging workflow. Use this skill when a bug is reported, a test is failing, behavior is unexpected, or the dev server throws errors. Activates on "it's broken", "this isn't working", "why is X happening", "fix this error", or when logs/errors are pasted.
---

This skill replaces random guessing with a 4-phase root-cause process. The default agent behavior is to guess and patch — this skill overrides that.

## Phase 1: REPRODUCE — Confirm the problem exists

Before touching any code:
1. Identify the **exact symptom**: error message, wrong output, wrong behavior — copy the exact text
2. Identify the **entry point**: which URL, which API endpoint, which component, which test
3. Determine the **scope**: is it always reproducible? Only for certain users/data/environments?
4. Run the failing test or trigger the failing path to confirm you can reproduce it

For runtime errors, check in this order: compiler/type-checker output, browser DevTools console, dev server terminal, framework-specific logs.

> See your project's stack reference in `stacks/` for specific reproduction commands.

## Phase 2: LOCALIZE — Narrow to the smallest broken unit

Work inward from the symptom:
1. **Frontend render bug** → is it state, a rendering issue, or layout? Add `console.log` to narrow store value vs render branch
2. **API call fails** → check network tab for the request; check server terminal for error output; verify the handler is registered
3. **Type error** → run the type checker for full error with line numbers
4. **Compile error** → run the compiler from the correct directory
5. **Test failure** → run only that test file in isolation

**Stop when you can point to a single function, line, or handler that is wrong.**

> See your project's stack reference in `stacks/` for specific localization commands.

## Phase 3: FIX — Minimal, root-cause change

- Fix the root cause, not the symptom
- If the fix is more than 20 lines, you're probably fixing the wrong thing
- Write a test that would have caught this bug (RED first) — for pure logic only
- Apply the fix (GREEN)
- Run the test suite to confirm no regressions

### Common Root Causes by Symptom

| Symptom | Usually caused by |
|---------|-------------------|
| UI element drifts from expected position | Coordinates not adjusted for element offset / bounding rect |
| API command not found at runtime | Handler not registered in the framework's routing/command registry |
| State lost on app restart | Persistence middleware missing or excluding fields |
| File path resolves incorrectly | Raw filename used instead of resolved absolute path |
| Network request CORS failure | Missing `Access-Control-Allow-Origin` header or wrong method in preflight |
| Token/auth fails intermittently | Token expiry not handled; refresh not triggered |
| Canvas looks blurry | Missing HiDPI setup — multiply dimensions by `devicePixelRatio` |
| Dependency injection fails | Service not registered in DI container or not provided |

## Phase 4: GUARD — Prevent recurrence

After every bug fix:
1. Add the pattern to your project's `tasks/lessons.md` under "What Went Wrong → What To Do Instead"
2. Add a unit test that catches this exact bug
3. Check if the same bug exists in similar code (same pattern, different component)

## Anti-Rationalizations

| Excuse | Counter |
|--------|---------|
| "I'll just try changing X and see" | Random changes waste 30 min and leave the code dirtier. Localize first. |
| "It works on my machine" | Check env vars, locale, tier, user role — context matters. |
| "The fix is obvious, no test needed" | "Obvious" bugs come back. Write the test. |
| "I don't have time to update lessons" | Takes 2 minutes. Prevents the same mistake next week. |

## Verification Gate

- [ ] The original symptom no longer occurs
- [ ] Test suite passes (no regressions)
- [ ] Type/compile check passes
- [ ] A test exists that would catch this regression (for pure-logic bugs)
- [ ] Pattern added to project lessons doc
