---
name: security-review
description: OWASP-aligned security review for any application. Use this skill before merging any change that touches user input, authentication, authorization, data storage, external API calls, file uploads, or API endpoints. Activates on "security", "auth", "upload", "permissions", "review before merge".
---

This skill enforces a security gate before any change ships. Security regressions are business-ending — review before every merge that touches auth, data, or user input.

## Quick Checklist (run before every merge)

- [ ] **Input validation**: All API inputs have schema validators at the boundary
- [ ] **Authorization on every endpoint**: Sensitive routes use authenticated middleware, not public handlers
- [ ] **Multi-tenant isolation**: DB queries filter by the session's org/user ID (never trust client-sent IDs)
- [ ] **No secrets in code**: No hardcoded API keys, tokens, or passwords — use env vars validated at startup
- [ ] **File uploads sanitized**: Allowlist of MIME types; size limits enforced; stored safely (not served directly from disk)
- [ ] **Tier/permission enforcement**: Gated features checked server-side, not just hidden in the UI

## OWASP Top 10 — Checks

### A01 Broken Access Control
- Every endpoint must verify the calling user has permission for the resource
- Pattern: load the resource, then assert ownership or role
- Admin-only actions must check roles server-side

> See your project's stack reference in `stacks/` for specific access control patterns.

### A02 Cryptographic Failures
- Passwords: use bcrypt/argon2 (or delegate to an auth provider) — never store plaintext
- Session tokens: HttpOnly cookies, `Secure: true` in production, `SameSite: Lax` or `Strict`
- Sensitive data at rest: encrypted; encryption keys never logged or returned to clients
- HTTPS everywhere — no mixed-content

### A03 Injection
- **SQL**: use parameterized queries or an ORM — never string-interpolate user input into queries
- **XSS**: sanitize user-generated HTML before rendering; use framework-safe defaults
- **Command injection**: never pass unsanitized user input to shell/process commands
- **Prompt injection**: AI features must sandbox user input — never concatenate raw user text into system prompts

### A05 Security Misconfiguration
- CSP header configured; no `'unsafe-inline'` without nonce
- CORS restricted to known origins; new routes must not bypass it
- Rate limiting on auth endpoints and expensive operations
- Remove debug endpoints, stack traces, and verbose error messages in production

### A06 Vulnerable Components
- Audit dependencies before adding (use your stack's audit tool)
- Keep dependencies updated; automate with Dependabot or Renovate

### A07 Authentication Failures
- Password reset tokens: single-use, expire within 1 hour, bound to user's email
- Session revocation: invalidate server-side on logout/password change
- MFA tokens: validate against a narrow time window only

### A08 Software and Data Integrity Failures
- Webhook signatures: always verify HMAC signatures before processing
- OAuth callbacks: validate the `state` parameter to prevent CSRF
- Supply chain: pin critical dependencies; review any `postinstall` or build scripts

## What NOT to Do

| Don't | Do instead |
|-------|-----------|
| Trust `orgId` / `userId` from the request body | Read from the session/JWT on the server |
| Use public endpoints for authenticated resources | Require auth middleware |
| Return the full DB row to the client | Select only the columns the client needs |
| Store uploads in `public/` and serve directly | Use signed URLs or scoped storage |
| Use `dangerouslySetInnerHTML` / `[innerHTML]` / raw HTML with user content | Use framework-safe rendering or sanitize with DOMPurify |
| Log user-identifiable data | Log only IDs and action types, never PII |
| Hard-code secrets in source | Use env vars; validate at startup with a schema |

## Verification Gate

After any security-relevant change:
1. [ ] All new API inputs have schema validation
2. [ ] No client-supplied ID trusted for authorization
3. [ ] Lint/audit passes (security rules catch some patterns)
4. [ ] Type/compile check passes
5. [ ] Test with a different user/role — they must not see or modify other users' data
6. [ ] For auth changes: test with expired token, wrong role, missing MFA
