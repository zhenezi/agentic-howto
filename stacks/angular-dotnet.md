# Angular + ASP.NET Core — Stack Reference

Technology-specific commands, patterns, and conventions for skills in this library.
Load this file alongside any skill when working in an Angular/.NET project.

---

## Build / Check / Test Commands

| Action | Command |
|--------|---------|
| Type check (frontend) | `npx tsc --noEmit` |
| Type check (backend) | `dotnet build` |
| Build (frontend) | `ng build --configuration production` |
| Build (backend) | `dotnet publish` |
| Run all tests (frontend) | `ng test` |
| Run all tests (backend) | `dotnet test` |
| Run single spec | `ng test --include=<spec>` |
| Run single .NET test | `dotnet test --filter <TestName>` |
| Watch mode (frontend) | `ng test` (default watch) |
| Watch mode (backend) | `dotnet watch test` |
| Coverage (frontend) | `ng test --code-coverage` |
| Coverage (backend) | `dotnet test --collect:"XPlat Code Coverage"` |
| Lint (frontend) | `ng lint` |
| Lint (backend) | `dotnet format --verify-no-changes` |
| Security audit | `dotnet list package --vulnerable` + `npm audit` |

---

## Debugging

**Reproduction checks:**
- `ng serve` console for frontend errors
- Browser DevTools for runtime errors
- `dotnet run` terminal for backend errors

**Localization:**
- `ng test --include=<spec>` for isolated Angular test runs
- `dotnet test --filter <TestName>` for isolated .NET test runs
- Browser DevTools Network tab for API call failures

**Common root causes:**
- Service not registered in DI container → dependency injection fails
- Missing `[Authorize]` attribute → endpoint is publicly accessible
- EF Core navigation property lazy loading → N+1 queries
- Missing route registration → 404 at runtime

---

## Test Placement

| What | Where |
|------|-------|
| Angular components | `component.spec.ts` co-located |
| Angular services | `service.spec.ts` co-located |
| .NET controllers / services | `Tests/` project with xUnit |
| .NET integration tests | `IntegrationTests/` project with WebApplicationFactory |
| E2E flows | `e2e/` with Playwright |

---

## Feature Task Order

Typical file creation order when building a new feature:

```
1. [ ] Backend/DTOs/<Feature>Dto.cs                       (request/response DTOs)
2. [ ] Backend/Services/<Feature>Service.cs               (business logic)
3. [ ] Backend/Controllers/<Feature>Controller.cs         (API endpoints)
4. [ ] Backend.Tests/<Feature>ServiceTests.cs             (unit tests)
5. [ ] frontend/src/app/<feature>/                        (Angular feature module)
6. [ ] frontend/src/app/<feature>/<feature>.service.ts    (HTTP service)
7. [ ] frontend/src/app/<feature>/<feature>.component.ts  (component)
8. [ ] frontend/src/app/app-routing.module.ts             (route registration)
9. [ ] ng test + dotnet test
```

---

## Security Patterns

**Access control:** `[Authorize]` attribute + policy-based authorization; claim checks in service layer.

**Input validation:** `FluentValidation` or Data Annotations for request validation; never trust raw request bodies.

**Authentication:** ASP.NET Identity or Microsoft Entra — never roll custom auth.

**Injection prevention:** Use parameterized queries via EF Core; never string-interpolate user input.

**Dependency audit:** `dotnet list package --vulnerable` + `npm audit`.

---

## Performance Profiling & Optimization

| What | Tool |
|------|------|
| CPU profiling | dotProfiler, `dotnet-trace` |
| Memory | `dotnet-counters`, `dotnet-dump` |
| Bundle size | `ng build --stats-json` + webpack-bundle-analyzer |
| API latency | Application Insights, `Stopwatch` |
| DB queries | EF Core query logging, SQL Profiler |
| Rendering | Angular DevTools profiler |

**Optimization techniques:**
- Use `OnPush` change detection; avoid `ChangeDetectorRef.detectChanges()` unless measured
- Lazy load feature modules; use `loadChildren` in routes
- Use `AsNoTracking()` for read-only EF Core queries
- Use `IMemoryCache` or `IDistributedCache` for expensive computations
- Use `trackBy` in `*ngFor` / `@for` to minimize DOM mutations

---

## Architecture Review

- DI registration is correct (singleton vs scoped vs transient)
- EF Core navigation properties don't cause lazy loading traps
- Angular change detection strategy is appropriate (OnPush where possible)
- Vertical slice architecture — one folder per feature containing controller, service, DTOs, validators
- Services are `@Injectable({ providedIn: 'root' })` — avoid module-scoped providers unless intentional

---

## Code Review Checklist

- [ ] Components use `OnPush` change detection where appropriate
- [ ] Services are properly scoped in DI (singleton vs scoped vs transient)
- [ ] EF Core queries use `AsNoTracking()` for reads
- [ ] No business logic in controllers — delegate to services
- [ ] Observables are properly unsubscribed (`takeUntilDestroyed`, `async` pipe)
- [ ] DTOs used for API request/response — never expose EF entities directly
- [ ] Use Angular's typed reactive forms (`FormGroup`, `FormControl<T>`)

---

## API Design

```csharp
[ApiController]
[Route("api/[controller]")]
public class FeaturesController : ControllerBase
{
    [HttpPost("{id}/actions")]
    [ProducesResponseType(typeof(FeatureDto), StatusCodes.Status201Created)]
    public async Task<ActionResult<FeatureDto>> Create(
        [FromRoute] string id,
        [FromBody] CreateFeatureRequest request) { ... }
}
```

- Use `[ApiController]` for automatic model validation
- DTOs for request/response — never expose EF entities directly
- Use `FluentValidation` for complex validation rules
- Return `ActionResult<T>` with typed responses
- Use `[ProducesResponseType]` attributes for OpenAPI documentation
- Generate OpenAPI/Swagger specs from code annotations

---

## Frontend / UI Patterns

- Organize Angular in feature modules with lazy loading; standalone components preferred
- Use Angular Material or CDK as a foundation, then customize heavily
- Leverage Angular animations module (`@angular/animations`) for declarative transitions
- Use `@HostBinding` and `:host` selectors for component-level theming
- Standalone components for isolated, reusable design atoms
- Shared components in a `SharedModule` or standalone library

---

## Conventions

- EF Core migrations: always script with `dotnet ef migrations script` and review before applying
- API contracts: use strongly-typed HttpClient services generated from OpenAPI specs
- Naming: PascalCase for DTOs, camelCase for JSON fields, kebab-case for URLs
