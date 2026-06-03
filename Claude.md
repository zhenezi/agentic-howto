# Agentic Development Instructions

> Optimised workflow and principles for AI-assisted development across multiple technology stacks.

## Supported Stacks

| Stack | Frontend | Backend |
|-------|----------|---------|
| **Desktop** | Tauri WebView (HTML/CSS/TS) | Rust |
| **Web — Angular** | Angular (TypeScript) | ASP.NET Core (C#) |
| **Web — React** | React (TypeScript) | Any (Node/Deno/edge) |
| **Mobile — iOS** | SwiftUI | Swift |
| **Mobile — Android** | Jetpack Compose | Kotlin |

When working in a project, detect the stack from config files and load the matching reference from `stacks/`.

---

## Workflow Orchestration

### 1. Plan Node Default
- Enter plan mode for ANY non-trivial task (3+ steps or architectural decisions)
- If something goes sideways, STOP and re-plan immediately — don't keep pushing
- Use plan mode for verification steps, not just building
- Write detailed specs upfront to reduce ambiguity
- Decompose into vertical slices: each slice is independently testable and shippable

### 2. Subagent Strategy
- Use subagents to keep the main context window clean and focused
- **When to dispatch**: research, exploration, parallel slice implementation, code review, test generation, refactoring isolated modules
- **When NOT to dispatch**: tasks requiring multi-file coordination with shared state, tasks where you need to see incremental results before proceeding
- **Context handoff**: every subagent prompt must include: (1) the specific deliverable, (2) relevant file paths, (3) stack reference to load, (4) acceptance criteria it must meet
- **Review every result**: never merge subagent output without checking it against the acceptance criteria yourself. Trust but verify.
- **One concern per subagent**: a subagent doing "implement feature X and write tests" will cut corners on tests. Split into two subagents.
- For plans with 3+ independent slices, use the `subagent-driven-development` skill for structured dispatch and two-stage review.

### 3. Self-Improvement Loop
- After ANY correction from the user: update `tasks/lessons.md` with the pattern
- Write rules for yourself that prevent the same mistake
- Ruthlessly iterate on these lessons until mistake rate drops
- Review lessons at session start for relevant project

### 4. Verification Before Done
- Never mark a task complete without proving it works
- Diff behavior between main and your changes when relevant
- Ask yourself: "Would a staff engineer approve this?"
- Run stack-appropriate checks (see your stack reference in `stacks/` for specific commands)

### 5. Demand Elegance (Balanced)
- For non-trivial changes: pause and ask "is there a more elegant way?"
- If a fix feels hacky: "Knowing everything I know now, implement the elegant solution"
- Skip this for simple, obvious fixes — don't over-engineer
- Challenge your own work before presenting it

### 6. Autonomous Bug Fixing
- When given a bug report: just fix it. Don't ask for hand-holding
- Point at logs, errors, failing tests — then resolve them
- Zero context switching required from the user
- Go fix failing CI tests without being told how

---

## Task Management

1. **Plan First**: Write plan to `tasks/todo.md` with checkable items
2. **Verify Plan**: Check in before starting implementation
3. **Track Progress**: Mark items complete as you go
4. **Explain Changes**: High-level summary at each step
5. **Document Results**: Add review section to `tasks/todo.md`
6. **Capture Lessons**: Update `tasks/lessons.md` after corrections

---

## Core Principles

- **Simplicity First**: Make every change as simple as possible. Impact minimal code.
- **No Laziness**: Find root causes. No temporary fixes. Senior developer standards.
- **Minimal Impact**: Changes should only touch what's necessary. Avoid introducing bugs.
- **Type Safety Everywhere**: Leverage the type system to catch errors at compile time.
- **Shift-Left Security**: Validate inputs at boundaries, enforce auth server-side, never trust client-sent IDs.
- **Trunk-Based Development**: Small, frequent merges to main. Feature flags over long-lived branches.
- **Observability by Default**: Structured logging, error boundaries, health checks — built in from the start, not bolted on.

---

## Stack References

Stack-specific commands, patterns, and conventions are maintained in separate reference files. Load the appropriate file alongside any skill when working in a project.

| Stack | Reference |
|-------|-----------|
| Rust + Tauri (Desktop) | `stacks/rust-tauri.md` |
| Angular + ASP.NET Core (Web) | `stacks/angular-dotnet.md` |
| React (Web) | `stacks/react.md` |
| iOS — Swift / SwiftUI (Mobile) | `stacks/ios-swift.md` |
| Android — Kotlin / Compose (Mobile) | `stacks/android-kotlin.md` |

Detect the stack from config files (`Cargo.toml` + `tauri.conf.json`, `angular.json` + `*.csproj`, `package.json` + `vite.config.*` / `next.config.*`, `*.xcodeproj` / `Package.swift`, `build.gradle.kts` / `settings.gradle.kts`) and load the matching reference automatically.

---

## Skills Reference

Skills live in `skills/<skill-name>/SKILL.md`. Each skill is technology-agnostic. Stack-specific details live in `stacks/`.

| Skill | Purpose |
|-------|---------|
| `debugging` | 4-phase root-cause debugging: Reproduce → Localize → Fix → Guard |
| `tdd` | Red-Green-Refactor test-driven development across all 3 stacks |
| `new-feature` | Spec-first feature implementation: Spec → Plan → Implement → Verify |
| `security-review` | OWASP-aligned security gate for any change |
| `scope-and-slice-planning` | Break big requests into atomic, verifiable slices |
| `solo-session-orchestration` | 60–120 min session loop for focused execution |
| `release-readiness` | Pre-release quality, docs, and rollback checklist |
| `docs-sync-after-change` | Keep docs synchronized with implemented behavior |
| `ui-regression-guard` | Prevent visual and interaction regressions |
| `frontend-design` | Production-grade UI with distinctive aesthetics |
| `architecture-review` | ADR-driven architecture decisions and review |
| `performance-optimization` | Profile-first performance improvement workflow |
| `api-design` | Contract-first API design for any backend |
| `code-review` | Structured code review checklist and process |
| `writing-skills` | TDD-driven skill authoring: baseline → write → bulletproof |
| `subagent-driven-development` | Structured subagent dispatch with two-stage review for parallel slices |