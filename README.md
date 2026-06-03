# Agentic Development — Skill Library

A portable library of AI agent skills and workflows for solo developers and small teams building across multiple technology stacks:

| Stack | Frontend | Backend |
|-------|----------|---------|
| **Desktop** | Tauri WebView (HTML/CSS/TS) | Rust |
| **Web — Angular** | Angular (TypeScript) | ASP.NET Core (C#) |
| **Web — React** | React (TypeScript) | Any (Node/Deno/edge) |
| **Mobile — iOS** | SwiftUI | Swift |
| **Mobile — Android** | Jetpack Compose | Kotlin |

## Skills

| Skill | Purpose |
|-------|---------|
| [debugging](skills/debugging/SKILL.md) | 4-phase root-cause debugging: Reproduce → Localize → Fix → Guard |
| [tdd](skills/tdd/SKILL.md) | Red-Green-Refactor test-driven development |
| [new-feature](skills/new-feature/SKILL.md) | Spec-first feature implementation: Spec → Plan → Implement → Verify |
| [security-review](skills/security-review/SKILL.md) | OWASP-aligned security gate for any change |
| [scope-and-slice-planning](skills/scope-and-slice-planning/SKILL.md) | Break big requests into atomic, verifiable slices |
| [solo-session-orchestration](skills/solo-session-orchestration/SKILL.md) | 60–120 min session loop for focused execution |
| [release-readiness](skills/release-readiness/SKILL.md) | Pre-release quality, docs, and rollback checklist |
| [docs-sync-after-change](skills/docs-sync-after-change/SKILL.md) | Keep docs synchronized with implemented behavior |
| [ui-regression-guard](skills/ui-regression-guard/SKILL.md) | Prevent visual and interaction regressions |
| [frontend-design](skills/frontend-design/SKILL.md) | Production-grade UI with distinctive aesthetics |
| [architecture-review](skills/architecture-review/SKILL.md) | ADR-driven architecture decisions and review |
| [performance-optimization](skills/performance-optimization/SKILL.md) | Profile-first performance improvement workflow |
| [api-design](skills/api-design/SKILL.md) | Contract-first API design for any backend |
| [code-review](skills/code-review/SKILL.md) | Structured code review checklist and process |
| [writing-skills](skills/writing-skills/SKILL.md) | TDD-driven skill authoring: baseline → write → bulletproof |

## How to Use

### Option 1 — Copy skills into your project

```
your-project/
└── .copilot/
    └── skills/
        └── debugging/
            └── SKILL.md   ← paste from this repo
```

Reference in your `.github/copilot-instructions.md` or VS Code agent settings.

### Option 2 — Reference from VS Code skills config

In your VS Code `settings.json` or agent config:

```json
{
  "github.copilot.chat.skills": [
    {
      "name": "debugging",
      "file": "/path/to/agentic-dev/skills/debugging/SKILL.md"
    }
  ]
}
```

### Option 3 — Use Claude.md as project instructions

Copy `Claude.md` to the root of any project as `.claude/instructions.md`, `CLAUDE.md`, or `.github/copilot-instructions.md`. It contains the workflow orchestration, stack conventions, and skill reference table.

## Stack References

Skills are technology-agnostic. Stack-specific commands, patterns, and conventions live in separate reference files:

| Stack | Reference |
|-------|-----------|
| Rust + Tauri (Desktop) | [stacks/rust-tauri.md](stacks/rust-tauri.md) |
| Angular + ASP.NET Core (Web) | [stacks/angular-dotnet.md](stacks/angular-dotnet.md) |
| React (Web) | [stacks/react.md](stacks/react.md) |
| iOS — Swift / SwiftUI (Mobile) | [stacks/ios-swift.md](stacks/ios-swift.md) |
| Android — Kotlin / Compose (Mobile) | [stacks/android-kotlin.md](stacks/android-kotlin.md) |

Load the appropriate stack file alongside any skill when working in a project.

## Skill Format

Each skill follows this structure:

```markdown
---
name: skill-name
description: One-liner that tells the agent WHEN to invoke this skill.
---

# Body

Step-by-step instructions, checklists, anti-rationalizations, and verification gates.
```

The `description` frontmatter is what the agent reads to decide whether to load the full skill.

## Design Principles

1. **Technology-agnostic skills** — skills contain universal process; stack-specific details live in `stacks/`
2. **Under 150 lines** — long skills get ignored by agents
3. **Anti-rationalizations** — common excuses to shortcut are countered explicitly
4. **Verification gates** — exit criteria that must be met before declaring done
5. **Profile-based** — always measure before optimizing, always reproduce before debugging
