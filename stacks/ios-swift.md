# iOS (Swift / SwiftUI) — Stack Reference

Technology-specific commands, patterns, and conventions for skills in this library.
Load this file alongside any skill when working in an iOS project.

---

## Build / Check / Test Commands

| Action | Command |
|--------|---------|
| Build | `xcodebuild -scheme <Scheme> -sdk iphonesimulator build` |
| Build (release) | `xcodebuild -scheme <Scheme> -configuration Release archive` |
| Run all tests | `xcodebuild test -scheme <Scheme> -destination 'platform=iOS Simulator,name=iPhone 16'` |
| Run single test | `xcodebuild test -scheme <Scheme> -only-testing:<Target>/<TestClass>/<testMethod>` |
| Lint | `swiftlint` |
| Format | `swiftformat .` |
| Resolve packages | `xcodebuild -resolvePackageDependencies` |
| Type check | Build is the type check — Swift has no separate step |

For SPM-only projects:

| Action | Command |
|--------|---------|
| Build | `swift build` |
| Test | `swift test` |
| Test single | `swift test --filter <TestName>` |

---

## Debugging

**Reproduction checks:**
- Xcode console output for runtime errors and crashes
- Simulator logs via `Console.app` or `xcrun simctl spawn booted log stream`
- Crash logs in `~/Library/Logs/DiagnosticReports/`

**Localization:**
- Use Xcode breakpoints (symbolic and exception breakpoints)
- `po` and `v` in LLDB for inspecting state
- Instruments for performance and memory issues
- `swift test --filter <TestName>` for isolated test runs

**Common root causes:**
- Force-unwrap (`!`) on nil optional → crash
- Missing `@MainActor` annotation → UI update off main thread
- Retain cycle from strong `self` capture in closures → memory leak
- Missing `Info.plist` key → permission denied at runtime
- Incorrect `Codable` property names → silent decoding failure

---

## Test Placement

| What | Where |
|------|-------|
| Unit tests (pure logic) | `<Project>Tests/` target, co-located by module |
| View model / store tests | `<Project>Tests/ViewModels/` |
| Snapshot / UI tests | `<Project>UITests/` target |
| Integration tests | `<Project>Tests/Integration/` |
| Performance tests | `measure {}` blocks inside XCTest cases |

---

## Feature Task Order

```
1. [ ] Models/<Feature>.swift                    (data models, Codable structs)
2. [ ] Services/<Feature>Service.swift           (networking / persistence logic)
3. [ ] ViewModels/<Feature>ViewModel.swift        (ObservableObject / @Observable)
4. [ ] Views/<Feature>View.swift                 (SwiftUI view)
5. [ ] Navigation/routing wired                  (NavigationStack path or coordinator)
6. [ ] <Project>Tests/<Feature>Tests.swift       (unit tests)
7. [ ] Build succeeds, tests pass
```

---

## Security Patterns

**Access control:** Use App Transport Security (ATS) — never disable globally. Use `URLSession` with certificate pinning for sensitive APIs.

**Input validation:** Validate all server responses with `Codable` — never force-cast JSON. Use `guard` / `if let` for optionals.

**Keychain:** Store secrets (tokens, passwords) in Keychain via `Security` framework or a wrapper — never `UserDefaults`.

**Biometrics:** Use `LocalAuthentication` framework; always provide a fallback.

**Dependency audit:** Review SPM dependencies before adding; prefer Apple frameworks over third-party when equivalent.

---

## Performance Profiling & Optimization

| What | Tool |
|------|------|
| CPU profiling | Instruments → Time Profiler |
| Memory | Instruments → Leaks, Allocations |
| Rendering | Instruments → Core Animation, SwiftUI view body count |
| Network | Instruments → Network, Charles Proxy |
| Energy | Instruments → Energy Log |
| App launch | Instruments → App Launch |

**Optimization techniques:**
- Use `@Observable` (Observation framework) over `ObservableObject` for fine-grained updates
- Lazy load views with `LazyVStack` / `LazyHStack` for long lists
- Use `task {}` modifier for async work tied to view lifecycle
- Avoid expensive work in `body` — move to view model
- Profile with Instruments before optimizing — never guess
- Use `nonisolated` where `@MainActor` isolation isn't needed

---

## Architecture Review

- Clear separation: View → ViewModel → Service → Model
- No business logic in SwiftUI views — delegate to view models
- `@Observable` or `ObservableObject` for reactive state; avoid `@State` for shared state
- Dependency injection via initializer or environment — no singletons unless justified
- Navigation via `NavigationStack` path-based routing or coordinator pattern
- Concurrency: use structured concurrency (`async/await`, `TaskGroup`) over GCD

---

## Code Review Checklist

- [ ] No force-unwraps (`!`) unless provably safe (e.g., `IBOutlet`, static resources)
- [ ] No retain cycles — closures use `[weak self]` where needed
- [ ] `@MainActor` on all UI-mutating code
- [ ] `Sendable` conformance for types crossing actor boundaries
- [ ] Error handling uses `do/catch` — no silent `try?` unless intentional
- [ ] Accessibility: labels, traits, and dynamic type supported
- [ ] No hardcoded strings — use `String(localized:)` or Localizable

---

## API Design

```swift
// Request model
struct CreateFeatureRequest: Codable {
    let name: String
    let options: FeatureOptions
}

// Response model
struct FeatureResponse: Codable {
    let id: String
    let status: String
    let createdAt: Date
}

// Service method
func createFeature(_ request: CreateFeatureRequest) async throws -> FeatureResponse
```

- Use `Codable` structs for all request/response models
- Return `async throws` — let callers handle errors
- Use `URLSession` with typed responses; avoid raw `Data` handling
- Keep networking layer thin — one service per domain
- Use `HTTPURLResponse` status codes for error classification

---

## Frontend / UI Patterns

- SwiftUI-first; UIKit only when SwiftUI lacks capability
- Use `NavigationStack` with typed paths for navigation
- Prefer `@Observable` macro (iOS 17+) over `ObservableObject`
- Design for Dynamic Type, Dark Mode, and accessibility from the start
- Use SF Symbols for iconography — consistent with platform conventions
- Animations via `withAnimation {}` and `.animation()` modifiers
- Support iPad multitasking layouts where applicable

---

## Conventions

- Naming: PascalCase for types, camelCase for properties/functions
- File organization: one type per file, grouped by feature
- Use `swift-format` or `SwiftLint` for consistent style
- Minimum deployment target: document in project and enforce via Xcode settings
- Use SPM for dependencies; avoid CocoaPods/Carthage for new projects
