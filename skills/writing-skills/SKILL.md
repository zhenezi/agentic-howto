---
name: writing-skills
description: Use when creating new skills, editing existing skills, or verifying skills work before deployment. Activates on "create a skill", "write a skill", "new SKILL.md", "add a skill", or when documenting reusable agent techniques.
---

# Writing Skills

## Overview

**Writing skills IS Test-Driven Development applied to process documentation.**

You write test cases (pressure scenarios with subagents), watch them fail (baseline behavior), write the skill (documentation), watch tests pass (agents comply), and refactor (close loopholes).

**Core principle:** If you didn't watch an agent fail without the skill, you don't know if the skill teaches the right thing.

## What is a Skill?

A **skill** is a reference guide for proven techniques, patterns, or tools. Skills help future agent instances find and apply effective approaches.

**Skills are:** Reusable techniques, patterns, tools, reference guides
**Skills are NOT:** Narratives about how you solved a problem once

## TDD Mapping for Skills

| TDD Concept | Skill Creation |
|-------------|----------------|
| **Test case** | Pressure scenario with subagent |
| **Production code** | Skill document (SKILL.md) |
| **Test fails (RED)** | Agent violates rule without skill (baseline) |
| **Test passes (GREEN)** | Agent complies with skill present |
| **Refactor** | Close loopholes while maintaining compliance |

## When to Create a Skill

**Create when:**
- Technique wasn't intuitively obvious to you
- You'd reference this again across projects
- Pattern applies broadly (not project-specific)
- Others would benefit

**Don't create for:**
- One-off solutions
- Standard practices well-documented elsewhere
- Project-specific conventions (put in project instructions / CLAUDE.md)
- Mechanical constraints (if enforceable with lint/validation, automate it)

## SKILL.md Structure

**Frontmatter (YAML) — two required fields:**
- `name`: Use letters, numbers, and hyphens only
- `description`: Third-person, describes ONLY **when** to use (NOT what it does)

```yaml
# ❌ BAD: Summarizes workflow — agent may follow this instead of reading skill
description: Use when executing plans - dispatches subagent per task with code review

# ✅ GOOD: Just triggering conditions, no workflow summary
description: Use when executing implementation plans with independent tasks
```

**Why this matters:** When a description summarizes workflow, agents follow the description instead of reading the full skill body. The skill body becomes documentation the agent skips.

**Body structure:**
```markdown
## Overview
Core principle in 1-2 sentences.

## When to Use
Bullet list with SYMPTOMS and use cases. When NOT to use.

## Core Pattern
Before/after comparison or step-by-step process.

## Quick Reference
Table or bullets for scanning common operations.

## Anti-Rationalizations
Table of excuses and counters.

## Verification Gate
Exit criteria checklist.
```

## Token Efficiency

**Target word counts:**
- Frequently-loaded skills: <200 words
- Other skills: <500 words (still be concise)

**Techniques:**
- Use cross-references to other skills instead of repeating content
- One excellent example beats many mediocre ones
- Don't implement examples in multiple languages — one great example is enough
- Compress examples to minimal necessary form
- Move heavy reference to separate files

## RED-GREEN-REFACTOR for Skills

### RED: Write Failing Test (Baseline)
Run pressure scenario with subagent WITHOUT the skill. Document:
- What choices did the agent make?
- What rationalizations did it use (verbatim)?
- Which pressures triggered violations?

### GREEN: Write Minimal Skill
Write skill addressing those specific rationalizations. Don't add extra content for hypothetical cases.
Run same scenarios WITH skill. Agent should now comply.

### REFACTOR: Close Loopholes
Agent found new rationalization? Add explicit counter. Re-test until bulletproof.

## Bulletproofing Against Rationalization

For discipline-enforcing skills:

1. **Close every loophole explicitly** — don't just state the rule, forbid specific workarounds
2. **Build rationalization table** — every excuse agents make goes in the table with a counter
3. **Create red flags list** — make it easy for agents to self-check when rationalizing
4. **Address "spirit vs letter" arguments** — add: "Violating the letter of the rules is violating the spirit of the rules."

## Anti-Patterns

| Pattern | Why it's bad |
|---------|-------------|
| Narrative example ("In session X, we found...") | Too specific, not reusable |
| Multi-language dilution (example-js, example-py, example-go) | Mediocre quality, maintenance burden |
| Code in flowcharts | Can't copy-paste, hard to read |
| Generic labels (helper1, step2) | Labels should have semantic meaning |
| Batch-creating skills without testing each | Same as deploying untested code |

## Skill Creation Checklist

**RED Phase:**
- [ ] Create pressure scenarios (3+ combined pressures for discipline skills)
- [ ] Run scenarios WITHOUT skill — document baseline behavior verbatim
- [ ] Identify patterns in rationalizations/failures

**GREEN Phase:**
- [ ] YAML frontmatter with `name` and `description`
- [ ] Description starts with "Use when..." — triggers only, no workflow summary
- [ ] Keywords throughout for search (errors, symptoms, tools)
- [ ] Clear overview with core principle
- [ ] Address specific baseline failures identified in RED
- [ ] One excellent code example (not multi-language)
- [ ] Run scenarios WITH skill — verify agents now comply

**REFACTOR Phase:**
- [ ] Identify NEW rationalizations from testing
- [ ] Add explicit counters
- [ ] Build rationalization table
- [ ] Re-test until bulletproof

**Quality Checks:**
- [ ] Under 150 lines
- [ ] Quick reference table
- [ ] Anti-rationalizations section
- [ ] Verification gate with exit criteria
- [ ] No narrative storytelling
- [ ] Stack-aware where relevant (Rust/Tauri, Angular/.NET, React)

## The Iron Law

```
NO SKILL WITHOUT A FAILING TEST FIRST
```

Write skill before testing? Delete it. Start over.
Edit skill without testing? Same violation. **No exceptions.**
