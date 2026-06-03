---
name: solo-session-orchestration
description: Structured solo-founder workflow for each coding session. Use this skill at session start, when switching tasks, or when work feels scattered. Activates on requests like "plan this", "organize my workflow", "what next", "help me prioritize", or when multiple bugs/features/docs are mixed together.
---

This skill enforces a fast, repeatable session loop for one-person teams shipping production software.

## Goals
- Ship meaningful progress in 60–120 minute blocks.
- Keep context clean and avoid half-finished work.
- Always end with a releasable or resumable state.

## Session Loop

### 1. Define Outcome (5 min)
- Write one sentence: what must be true by session end.
- Pick one KPI: bug closed, feature slice shipped, docs updated, or test gap reduced.

### 2. Scope Guard (5 min)
- Split into must-have, should-have, later.
- Freeze scope after first implementation starts.
- Park anything that surfaces mid-session into a backlog note — do not chase it now.

### 3. Execute in Vertical Slices (45–90 min)
- Build smallest user-visible slice first.
- After each slice: run validation, then continue.
- If stuck for 20+ minutes without a verifiable result → rescope smaller or stop and debug.

### 4. Verify Before Moving On (10 min)
- Run type/compile check, test suite, and linter for your stack.
- Quick manual smoke pass on changed UI or flow.
- Confirm the outcome sentence is met.

> See your project's stack reference in `stacks/` for specific verification commands.

### 5. Session Close (5–10 min)
- Record what changed, what failed, and the next first step.
- Update docs only for changed behavior.
- Leave the working tree clean (commit or stash).

## Solo Founder Prioritization Rule

Prefer tasks with highest leverage:
1. Revenue or retention blocker
2. Workflow speed multiplier
3. Defect that harms trust
4. Cosmetic polish

## Anti-Drift Checks
- If 20+ minutes pass without a verifiable result, rescope smaller.
- If a task branches into unrelated fixes, park extras in a backlog note.
- If context is noisy, summarize and start a fresh task-focused session.

## Evidence Required
- Clear outcome sentence
- At least one completed, verified slice
- Short end-of-session handoff note
