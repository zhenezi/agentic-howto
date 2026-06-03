---
name: release-readiness
description: Pre-release hardening workflow for any project. Use before publishing, tagging, or handing builds to users. Activates on "prepare release", "ship", "build", "final check", or milestone completion.
---

Use this skill to reduce release risk. Especially important for solo maintainers with no QA team.

## Pre-Release Gate

### 1. Quality

- [ ] Type/compile check passes
- [ ] Build passes (production configuration)
- [ ] Test suite passes with no failures
- [ ] Lint clean (no warnings treated as errors)
- [ ] High-severity known issues reviewed and triaged

> See your project's stack reference in `stacks/` for specific build, test, and lint commands.

### 2. Product
- [ ] Core flows smoke-tested manually with realistic data
- [ ] New feature / module verified end-to-end
- [ ] Error states and empty states confirmed (not just happy path)

### 3. Security
- [ ] Dependency audit clean — no critical vulnerabilities (use your stack's audit tool)
- [ ] Auth flows tested (login, logout, expired token, wrong role)
- [ ] Secrets are in env vars or secret store, not in code

### 4. Documentation
- [ ] README feature bullets match actual behavior
- [ ] Any new commands, settings, or environment variables documented
- [ ] Breaking changes clearly called out in the changelog

### 5. Rollback Plan
- [ ] Last known-good tag or commit identified
- [ ] Hotfix path defined (branch strategy or direct patch process)

### 6. Release Notes Draft
- User-facing changes (what's new, what changed)
- Breaking changes (with migration steps if needed)
- Known limitations or deferred bugs

## Solo Risk Rules
- Do not ship unverified UI changes.
- Avoid mixing late refactors into a release commit.
- If uncertain about a change, stage it behind a feature toggle or defer to the next release.

## Evidence Required
- Checklist with pass/fail for each item above
- Build verification output
- Release note draft ready
