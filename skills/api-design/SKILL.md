---
name: api-design
description: Contract-first API design for REST, gRPC, and Tauri commands. Use this skill when designing new endpoints, restructuring existing APIs, or when API contracts need to be formalized. Activates on "design API", "new endpoint", "API contract", "Tauri command", "REST API", "gRPC".
---

This skill enforces contract-first API design. Define the contract before implementing the handler.

## Process

### 1. Define the Contract

Before writing any handler code, define:

```
Endpoint: POST /api/features/{id}/actions
Request:  { field: string, options: { ... } }
Response: { id: string, status: "created", createdAt: string }
Errors:   400 (validation), 401 (unauth), 403 (forbidden), 404 (not found)
```

> See your project's stack reference in `stacks/` for contract definition patterns in your technology (DTOs, Zod schemas, serde structs, etc.).

### 2. Design Principles

- **Resource-oriented**: URLs are nouns, HTTP methods are verbs (`GET /users`, not `GET /getUsers`)
- **Consistent naming**: use your stack's conventions (camelCase for JSON, PascalCase for typed DTOs, kebab-case for URLs)
- **Idempotent where possible**: `PUT` and `DELETE` should be safe to retry
- **Pagination by default**: any list endpoint must support `limit`/`offset` or cursor-based pagination
- **Versioning strategy**: URL prefix (`/api/v2/`) or header — decide once per project
- **Error format**: consistent error response shape across all endpoints

### 3. Error Response Standard

Use a consistent error envelope:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human-readable message",
    "details": [
      { "field": "email", "message": "Invalid email format" }
    ]
  }
}
```

Map to HTTP status codes:
| Scenario | Status |
|----------|--------|
| Invalid input | 400 |
| Not authenticated | 401 |
| Authenticated but not authorized | 403 |
| Resource not found | 404 |
| Conflict (duplicate) | 409 |
| Rate limited | 429 |
| Server error | 500 |

### 4. Implementation Conventions

- Use strong types for inputs and outputs (schemas, DTOs, or structs)
- Validate inputs at the API boundary with a schema validator
- Keep handlers thin — delegate to service functions for testability
- Return typed responses with appropriate status codes
- Document side effects (file writes, state mutations, external calls)

> See your project's stack reference in `stacks/` for specific API design patterns and tooling.

### 5. Documentation

- Generate API specs from code annotations where possible (not manually)
- Keep request/response examples in tests (they serve as living documentation)

## Anti-Rationalizations

| Excuse | Counter |
|--------|---------|
| "I'll document the API later" | The contract IS the documentation. Write it first. |
| "It's just an internal API" | Internal APIs become external. Design properly now. |
| "Validation can happen on the frontend" | Never trust the client. Validate server-side. |
| "We don't need error codes" | Clients need machine-readable errors for proper UX. |

## Verification Gate

- [ ] Contract defined before implementation
- [ ] Input validation at the API boundary
- [ ] Consistent error response format
- [ ] Auth/authz enforced on every endpoint
- [ ] List endpoints support pagination
- [ ] API documented (OpenAPI, doc comments, or COMMANDS.md)
