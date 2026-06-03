---
name: new-feature
description: Full spec-first workflow for building new features. Use this skill when the user wants to add a new module, new backend command, new UI workflow, or any significant feature. Activates on "add X feature", "build X module", "implement X", "create X tool".
---

This skill enforces a disciplined spec → plan → implement → verify cycle. The default agent behavior is to jump straight to code — this skill overrides that.

**For solo builders: a 10-minute spec saves 3 hours of rework.**

## Phase 1: SPEC — Clarify before touching code

Answer these questions before writing a single line:

1. **What problem does this solve?** — one sentence, user-facing
2. **What does the UI surface look like?** — sidebar entry, modal, form, page, dialog?
3. **Does it need a backend?** — if it writes files, reads binary data, or is performance-critical → yes
4. **What does "done" look like?** — define acceptance criteria: input, action, expected output
5. **Persistence needed?** — which state fields should survive app restart?

Write the spec as a checklist. Keep it under 20 lines.

## Phase 2: PLAN — Break into atomic tasks

Each task must be:
- **1 file or 1 concern** — not "build the module", but "add `SaveResized` backend command"
- **Verifiable** — has a clear pass/fail check
- **Ordered** — types → store/state → service → component → routing/registration → tests

> See your project's stack reference in `stacks/` for the typical file creation order for your technology.

## Phase 3: IMPLEMENT — One slice at a time

- Work through the checklist top to bottom
- Mark each item `[x]` only after it's verified
- Run type/compile check after each file change — catch errors early
- For backend changes: check that the handler compiles and is registered before wiring up the frontend

## Phase 4: VERIFY — Prove it before marking done

Before declaring complete:
- [ ] Type/compile check passes
- [ ] Backend compiles cleanly (if backend was touched)
- [ ] Feature renders and functions in the dev app — smoke test the happy path
- [ ] File upload / file input works (if applicable)
- [ ] Output is written correctly (file, DB row, API response)
- [ ] State persists across restart (if persistence was intended)
- [ ] Permissions / auth gate enforced (if applicable)

## Anti-Rationalizations

| Excuse | Counter |
|--------|---------|
| "It's a small feature, I'll skip the spec" | Small features grow. A 5-line spec takes 3 minutes. |
| "I'll add auth/gating in a follow-up" | Security gaps ship to production. Do it now. |
| "Tests can wait until the feature stabilizes" | Unstable features need tests most. Write them as you go. |
| "I'll add i18n / a11y / error states later" | You'll forget. Handle them during implementation. |

## Gotchas

- **Multi-tenant / org isolation**: any new query reading org data must filter by the session's org ID — never trust a client-sent ID
- **Audit logging**: mutations to sensitive tables must write to your audit log
- **Rollback**: if the feature touches DB schema or persistent state, define a rollback plan before starting
- **Registration**: backend commands/routes must be registered in both the handler AND the routing/capabilities config
