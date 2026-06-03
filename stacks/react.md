# React — Stack Reference

Technology-specific commands, patterns, and conventions for skills in this library.
Load this file alongside any skill when working in a React project.

---

## Build / Check / Test Commands

| Action | Command |
|--------|---------|
| Type check | `npx tsc --noEmit` |
| Build | `npm run build` |
| Run all tests | `npx vitest run` |
| Run single file | `npx vitest run path/to/test.spec.ts` |
| Watch mode | `npx vitest` |
| Coverage | `npx vitest run --coverage` |
| Lint | `npx eslint .` |
| Security audit | `npm audit` |

For Jest: replace `npx vitest run` with `npx jest`.

---

## Debugging

**Reproduction checks:**
- `npx tsc --noEmit` for type errors
- Browser DevTools console for runtime errors
- Dev server terminal for server-side errors

**Localization:**
- `npx vitest run path/to/test.spec.ts` for isolated test runs
- `npx tsc --noEmit` for full type error with line numbers
- Browser DevTools Network tab for API call failures

**Common root causes:**
- Missing dependency in `useEffect` array → stale closures
- Incorrect server/client component boundary (RSC) → hydration mismatch
- State not colocated → unnecessary re-renders
- Missing API route handler → 404 at runtime

---

## Test Placement

| What | Where |
|------|-------|
| Pure utility functions | `src/utils/__tests__/<util>.test.ts` |
| Pure business logic | `src/__tests__/<module>.test.ts` |
| Store logic (pure reducers) | Co-located `__tests__/` next to store file |
| React components | Co-located `Feature.test.tsx` with Testing Library |
| E2E flows | `e2e/` with Playwright |

---

## Feature Task Order

Typical file creation order when building a new feature:

```
1. [ ] src/types/<feature>.ts                        (TypeScript interfaces)
2. [ ] src/stores/<feature>Store.ts                  (Zustand store or context)
3. [ ] src/services/<Feature>Service.ts              (API wrappers)
4. [ ] src/components/<Feature>/<Feature>.tsx         (React component)
5. [ ] src/App.tsx or pages/<feature>/                (wire routing)
6. [ ] src/__tests__/<feature>.test.ts               (unit tests)
7. [ ] npx tsc --noEmit passes
```

---

## Security Patterns

**Access control:** Server-side middleware on every API route; never rely on client-side route guards alone.

**Input validation:** Zod schemas at the API boundary; TypeScript types generated from schemas.

**XSS prevention:** Framework-safe JSX by default; never use `dangerouslySetInnerHTML` with user content without DOMPurify.

**Dependency audit:** `npm audit` before releases.

---

## Performance Profiling & Optimization

| What | Tool |
|------|------|
| CPU profiling | Chrome DevTools Performance tab |
| Memory | Chrome DevTools Memory tab |
| Bundle size | `npx vite-bundle-visualizer` or webpack-bundle-analyzer |
| API latency | Network tab, server logs |
| DB queries | Prisma/Drizzle query logging |
| Rendering | React DevTools Profiler |

**Optimization techniques:**
- `React.memo()` for expensive pure components
- `useMemo` / `useCallback` only when profiler shows re-render cost
- Code split with `React.lazy()` + `Suspense`
- Use React Server Components to reduce client bundle (Next.js/Remix)
- Virtualize long lists with `@tanstack/react-virtual`

---

## Architecture Review

- Component tree doesn't cause unnecessary re-renders
- Server/client component boundary is intentional (if using RSC)
- State colocation is correct (not everything in global store)
- Function components only; hooks for all state and effects
- Client state: Zustand or React context — pick one per project, don't mix

---

## Code Review Checklist

- [ ] No unnecessary re-renders (check with React DevTools)
- [ ] Effects have correct dependency arrays
- [ ] No direct DOM manipulation — use refs when necessary
- [ ] Server/client component boundary is correct (if using RSC)
- [ ] State is colocated appropriately
- [ ] Forms use React Hook Form + Zod schema validation
- [ ] Server state uses React Query / TanStack Query with proper config

---

## API Design

```typescript
// Input schema (Zod)
const InputSchema = z.object({
  id: z.string(),
  options: z.object({ /* ... */ })
})

// Output type
type Output = { id: string; status: string; createdAt: string }
```

- Zod schemas for input validation at the API boundary
- TypeScript types generated from schemas (single source of truth)
- Use React Query / TanStack Query for client-side caching and retry
- Server Actions (Next.js) or API routes — be consistent per project
- Routing: file-based (Next.js/Remix) or React Router v7 — match the framework

---

## Frontend / UI Patterns

- Co-locate components, styles, types, and tests: `Feature/Feature.tsx`, `Feature.test.tsx`, `Feature.module.css`
- CSS Modules, Tailwind, or styled-components — project-consistent, never mix
- Use `framer-motion` or CSS animations for motion
- Server components for performance-critical pages (Next.js/Remix)
- Shared components in a `components/ui/` directory
- SSR/SSG: prefer server components and streaming where the framework supports it

---

## Conventions

- Styling: CSS Modules, Tailwind, or styled-components — pick one, never mix
- Forms: React Hook Form + Zod schema validation
- Naming: camelCase for JSON fields, kebab-case for URLs
