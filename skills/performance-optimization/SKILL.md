---
name: performance-optimization
description: Profile-first performance improvement workflow. Use this skill when the user reports slowness, high memory usage, large bundle sizes, slow queries, or when optimizing critical paths. Activates on "slow", "performance", "optimize", "bundle size", "memory leak", "latency".
---

This skill enforces a measure-first approach. Never optimize without a profile. Premature optimization is the root of all evil — but measured optimization is engineering.

## Phase 1: MEASURE — Establish a baseline

Before changing anything:
1. Identify the **specific operation** that is slow (page load, API call, render, build)
2. Measure it with appropriate tools (CPU profiler, memory profiler, bundle analyzer, query logger, rendering profiler)

> See your project's stack reference in `stacks/` for specific profiling tools and commands.

3. Record the baseline number (e.g., "page load: 3.2s", "API P95: 450ms")
4. Set a target (e.g., "page load < 1.5s", "API P95 < 200ms")

## Phase 2: IDENTIFY — Find the bottleneck

- **One bottleneck at a time.** Don't scatter-shot optimize.
- Read the profile. The hottest path is the only thing worth optimizing.
- Common bottleneck patterns:

| Pattern | Symptom | Fix |
|---------|---------|-----|
| N+1 queries | Many small DB calls | Batch/join/include |
| Unbounded list rendering | UI jank on scroll | Virtualization (virtual scroll) |
| Synchronous blocking | UI freeze, thread starvation | Move to async/worker |
| Large bundle | Slow initial load | Code splitting, lazy loading, tree shaking |
| Unnecessary re-renders | UI jank on interaction | Memoization, change detection optimization |
| Unindexed query | Slow DB response | Add index, review query plan |
| Memory leak | Growing memory over time | Cleanup subscriptions, dispose resources |

## Phase 3: FIX — Targeted change

- Change ONE thing at a time
- Re-measure after each change
- If the improvement is < 10%, it's probably not worth the complexity
- Document what you changed and the measured impact

> See your project's stack reference in `stacks/` for specific optimization techniques.

## Phase 4: VERIFY — Prove improvement

- Re-run the same measurement from Phase 1
- Compare before/after numbers
- If target not met, return to Phase 2
- Document the optimization in a commit message with before/after metrics

## Anti-Rationalizations

| Excuse | Counter |
|--------|---------|
| "I know what's slow without measuring" | You don't. Profile first. |
| "Let's optimize everything while we're here" | One bottleneck at a time. Measure each. |
| "This optimization is too small to matter" | If the profile says it's the hottest path, it matters. |
| "We'll optimize later" | If users are complaining now, optimize now. |

## Verification Gate

- [ ] Baseline measurement recorded
- [ ] Bottleneck identified via profiling (not guessing)
- [ ] Targeted fix applied (one change at a time)
- [ ] Re-measurement shows improvement
- [ ] No functionality regressions
- [ ] Before/after metrics documented
