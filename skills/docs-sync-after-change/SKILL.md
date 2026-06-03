---
name: docs-sync-after-change
description: Keep documentation synchronized with implemented behavior immediately after feature or bugfix changes. Use when code changes alter UX, commands, module capabilities, or setup steps.
---

Use this skill to prevent doc drift and reduce support overhead. Stale docs erode trust and create more work than writing them correctly the first time.

## Process

### 1. Identify Impacted Docs
- README feature list / overview
- Module-specific docs or wiki pages
- Setup, run, and build instructions
- API reference (if applicable)
- Changelog

### 2. Update Only Verified Facts
- Document what is implemented **now**.
- Do not promise near-term roadmap items as done.
- Remove any mention of behavior that was changed or removed.

### 3. Add Behavioral Deltas
- New controls, commands, or actions
- Changed defaults or configuration options
- Removed or deprecated flows (with migration instructions if needed)

### 4. Validate Doc Accuracy
- Cross-check with current code paths and commands.
- Run any commands that appear in docs to confirm they still work.
- Keep wording short and operational — prefer examples over prose.

## Documentation Quality Bar
- Every feature statement maps to observable, testable behavior.
- No stale screenshots or outdated command names.
- No contradictions between sections.
- Changelog entry present for every user-facing change.

## Evidence Required
- List of updated docs
- Bullet summary of behavior deltas
- Confirmation that commands in docs were validated
