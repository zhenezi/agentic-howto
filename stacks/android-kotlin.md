# Android (Kotlin / Jetpack Compose) — Stack Reference

Technology-specific commands, patterns, and conventions for skills in this library.
Load this file alongside any skill when working in an Android project.

---

## Build / Check / Test Commands

| Action | Command |
|--------|---------|
| Build (debug) | `./gradlew assembleDebug` |
| Build (release) | `./gradlew assembleRelease` |
| Run all unit tests | `./gradlew test` |
| Run single test | `./gradlew test --tests "com.example.FeatureTest"` |
| Run instrumented tests | `./gradlew connectedAndroidTest` |
| Lint | `./gradlew lint` |
| Lint (Kotlin) | `./gradlew ktlintCheck` or `./gradlew detekt` |
| Format | `./gradlew ktlintFormat` |
| Dependency check | `./gradlew dependencyUpdates` |
| Type check | Build is the type check — Kotlin has no separate step |

---

## Debugging

**Reproduction checks:**
- Logcat output via `adb logcat` or Android Studio Logcat panel
- Android Studio debugger with breakpoints
- `adb shell am start` to launch specific activities/deep links

**Localization:**
- Filter Logcat by tag/package: `adb logcat -s TAG`
- `./gradlew test --tests "*.FeatureTest"` for isolated test runs
- Android Studio Profiler for performance issues
- Layout Inspector for Compose rendering issues

**Common root causes:**
- Missing permission in `AndroidManifest.xml` → `SecurityException`
- Composable recomposition loop → UI jank or infinite loop
- ProGuard/R8 stripping classes → `ClassNotFoundException` in release builds
- Missing `@Serializable` annotation → runtime serialization crash
- Coroutine scope cancelled prematurely → `CancellationException`

---

## Test Placement

| What | Where |
|------|-------|
| Unit tests (pure logic) | `src/test/java/` or `src/test/kotlin/` |
| ViewModel tests | `src/test/kotlin/.../viewmodel/` |
| Compose UI tests | `src/androidTest/kotlin/` with `ComposeTestRule` |
| Integration tests | `src/androidTest/kotlin/.../integration/` |
| E2E tests | `src/androidTest/kotlin/` with Espresso or UI Automator |

---

## Feature Task Order

```
1. [ ] data/model/<Feature>.kt                          (data classes, serialization)
2. [ ] data/repository/<Feature>Repository.kt            (data source abstraction)
3. [ ] data/remote/<Feature>Api.kt                       (Retrofit/Ktor interface)
4. [ ] domain/usecase/<Feature>UseCase.kt                (business logic, optional)
5. [ ] ui/<feature>/<Feature>ViewModel.kt                (ViewModel with StateFlow)
6. [ ] ui/<feature>/<Feature>Screen.kt                   (Composable screen)
7. [ ] navigation/ wired                                 (NavHost graph entry)
8. [ ] di/<Feature>Module.kt                             (Hilt/Koin module)
9. [ ] src/test/kotlin/.../<Feature>ViewModelTest.kt     (unit tests)
10. [ ] ./gradlew test passes
```

---

## Security Patterns

**Access control:** Use `EncryptedSharedPreferences` or `EncryptedFile` from Jetpack Security for sensitive data. Never store tokens in plain `SharedPreferences`.

**Input validation:** Validate all server responses via Kotlin serialization / Moshi / Gson — never cast raw JSON. Use sealed classes for error handling.

**Network security:** Use Network Security Config (`network_security_config.xml`) — enforce certificate pinning for production APIs. Never allow cleartext traffic.

**Biometrics:** Use `BiometricPrompt` from Jetpack Biometric library; always provide fallback.

**Dependency audit:** `./gradlew dependencyUpdates` + review before upgrading. Use Dependabot or Renovate for automation.

**ProGuard/R8:** Keep rules up to date; test release builds to catch stripped classes.

---

## Performance Profiling & Optimization

| What | Tool |
|------|------|
| CPU profiling | Android Studio Profiler → CPU |
| Memory | Android Studio Profiler → Memory, LeakCanary |
| Rendering | Android Studio Profiler → GPU, Layout Inspector |
| Network | Android Studio Profiler → Network, OkHttp logging |
| App startup | `adb shell am start -W`, Macrobenchmark |
| Compose recomposition | Layout Inspector recomposition counts |

**Optimization techniques:**
- Use `remember` and `derivedStateOf` to avoid unnecessary recompositions
- Use `LazyColumn` / `LazyRow` for lists — never `Column` with `forEach` for dynamic data
- Use `Modifier.drawWithCache` for expensive drawing operations
- Use `collectAsStateWithLifecycle()` for lifecycle-aware state collection
- Use baseline profiles (`BaselineProfileRule`) for startup optimization
- R8 full mode for release builds — reduces APK size and improves runtime

---

## Architecture Review

- MVVM or MVI with unidirectional data flow
- No business logic in Composables — delegate to ViewModel
- `StateFlow` / `SharedFlow` for reactive state; avoid `LiveData` in new code
- Dependency injection via Hilt (preferred) or Koin
- Repository pattern for data access — single source of truth
- Structured concurrency: `viewModelScope`, `lifecycleScope` — avoid `GlobalScope`
- Navigation via Jetpack Navigation Compose with type-safe args

---

## Code Review Checklist

- [ ] No `GlobalScope` — use structured scopes (`viewModelScope`, `lifecycleScope`)
- [ ] No force-unwrap (`!!`) unless provably safe
- [ ] `collectAsStateWithLifecycle()` used instead of `collectAsState()` for flows
- [ ] Composables are stateless where possible — state hoisted to ViewModel
- [ ] No side effects in `@Composable` functions — use `LaunchedEffect` / `SideEffect`
- [ ] ProGuard/R8 rules tested with release build
- [ ] Accessibility: content descriptions, touch targets ≥ 48dp
- [ ] No hardcoded strings — use `stringResource()` / `strings.xml`

---

## API Design

```kotlin
// Request model
@Serializable
data class CreateFeatureRequest(
    val name: String,
    val options: FeatureOptions
)

// Response model
@Serializable
data class FeatureResponse(
    val id: String,
    val status: String,
    val createdAt: String
)

// Retrofit interface
interface FeatureApi {
    @POST("api/features/{id}/actions")
    suspend fun createFeature(
        @Path("id") id: String,
        @Body request: CreateFeatureRequest
    ): FeatureResponse
}
```

- Use `suspend` functions for all network calls
- Return typed responses — never raw `Response<ResponseBody>`
- Use sealed classes for Result types: `Success`, `Error`, `Loading`
- Keep API interfaces thin — one per domain
- Use interceptors for auth headers, logging, retry

---

## Frontend / UI Patterns

- Jetpack Compose-first; XML layouts only for legacy or WebView
- Material 3 (`androidx.compose.material3`) as the design foundation
- Use `@Preview` annotations for rapid iteration
- Theme via `MaterialTheme` with custom color schemes and typography
- Navigation via `NavHost` with type-safe routes (Navigation Compose 2.8+)
- Animations via `animate*AsState`, `AnimatedVisibility`, `Crossfade`
- Support landscape, tablet, and foldable layouts with `WindowSizeClass`

---

## Conventions

- Naming: PascalCase for classes, camelCase for functions/properties
- File organization: feature-based packages (`ui/<feature>/`, `data/<feature>/`)
- Use `ktlint` or `detekt` for consistent style
- Minimum SDK: document in `build.gradle.kts` and enforce
- Use Kotlin DSL (`build.gradle.kts`) over Groovy for build scripts
- Version catalog (`libs.versions.toml`) for dependency management
