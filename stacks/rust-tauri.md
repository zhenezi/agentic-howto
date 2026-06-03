# Rust + Tauri — Stack Reference

Technology-specific commands, patterns, and conventions for skills in this library.
Load this file alongside any skill when working in a Rust/Tauri project.

---

## Build / Check / Test Commands

| Action | Command |
|--------|---------|
| Type/compile check | `cargo check` |
| Build (dev) | `cargo tauri dev` |
| Build (release) | `cargo tauri build` |
| Run all tests | `cargo test` |
| Run single test | `cargo test <test_name>` |
| Watch mode | `cargo watch -x test` |
| Coverage | `cargo tarpaulin` |
| Lint | `cargo clippy -- -D warnings` |
| Format | `cargo fmt --check` |
| Security audit | `cargo audit` |

---

## Debugging

**Reproduction checks:**
- `cargo check` for compile errors
- Browser DevTools console + Tauri dev server terminal for runtime errors

**Localization:**
- `cargo check` for compile errors
- `cargo test -- <test_name>` for isolated test runs
- Browser DevTools for frontend issues in WebView

**Common root causes:**
- Handler not registered in `tauri::Builder` → command not found at runtime
- Missing `serde` derive → serialization/deserialization fails silently
- Scoped FS path mismatch → file operations fail

---

## Test Placement

| What | Where |
|------|-------|
| Pure functions / business logic | `#[cfg(test)] mod tests` in the same file |
| Integration tests | `tests/` directory at crate root |
| Tauri commands | Mock the state, test the handler logic directly |
| Frontend unit tests | Co-located `.test.ts` files (Vitest or Jest) |
| E2E flows | `e2e/` with Playwright or WebDriver |

---

## Feature Task Order

Typical file creation order when building a new feature:

```
1. [ ] src-tauri/src/types/<feature>.rs        (Rust structs + serde derives)
2. [ ] src-tauri/src/commands/<feature>.rs      (Tauri command handlers)
3. [ ] src-tauri/src/main.rs                    (register commands in Builder)
4. [ ] src/types/<feature>.ts                   (TypeScript interfaces)
5. [ ] src/services/<Feature>Service.ts         (invoke() wrappers)
6. [ ] src/stores/<feature>Store.ts             (frontend state)
7. [ ] src/components/<Feature>.tsx|.vue|.svelte (UI component)
8. [ ] src/App.tsx|main.ts                      (wire routing)
9. [ ] cargo test + frontend tests
```

---

## Security Patterns

**Access control:** Validate via `tauri::State` session; scope FS access via Tauri's allowlist/capabilities config.

**Input validation:** Use `serde` for deserialization + custom validators on command inputs. Never trust frontend-sent IDs.

**Injection prevention:** Never pass unsanitized user input to `std::process::Command`.

**Dependency audit:** `cargo audit` before releases.

---

## Performance Profiling & Optimization

| What | Tool |
|------|------|
| CPU profiling | `cargo flamegraph`, `perf` |
| Memory | `valgrind`, `heaptrack` |
| Bundle size | N/A (native binary) |
| Micro-benchmarks | `criterion` |
| Rendering | Browser DevTools Performance tab (WebView) |

**Optimization techniques:**
- Use `rayon` for CPU-bound parallel work
- Prefer `&str` over `String` where ownership isn't needed
- Use `async` for I/O-bound operations; `tokio::spawn` for concurrent tasks
- Profile with `criterion` for micro-benchmarks

---

## Architecture Review

- Ownership and lifetime boundaries are clean
- No unnecessary `clone()` or `unwrap()` in hot paths
- `async` boundaries are intentional (not async for the sake of it)
- Commands organized in `src-tauri/src/commands/` with one file per domain
- State management: `tauri::State<Mutex<T>>` for shared backend state

---

## Code Review Checklist

- [ ] No unnecessary `unwrap()` — use `?` or handle the error
- [ ] No `clone()` where a reference would suffice
- [ ] `unsafe` blocks are justified and documented
- [ ] Tauri commands are registered in the builder
- [ ] `Result<T, String>` or custom error type for command handlers; never panic

---

## API / Command Design

```rust
#[tauri::command]
async fn process_feature(
    state: State<'_, AppState>,
    input: FeatureInput,    // serde-deserializable
) -> Result<FeatureOutput, String>
```

- Use strong types for inputs and outputs (derive `Serialize`, `Deserialize`)
- Return `Result<T, String>` or a custom error enum
- Document side effects (file writes, state mutations)
- Keep commands thin — delegate to service functions for testability
- Document commands in a `COMMANDS.md` or inline Rust doc comments

---

## Frontend / UI Patterns

- Use standard web technologies (HTML/CSS/TS); performance is excellent in WebView
- Leverage native window decorations or custom title bars via Tauri API
- Consider offline-first design; all assets should be bundled
- Consistent use of the same CSS framework / design system across WebView
