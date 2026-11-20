---
name: api-code-review
description: Review the Node.js/Express/node:sqlite API in api/ against its OpenAPI specs and user stories for correctness, security, data access, and test coverage, and write reviews/api-review.md. Use when asked to review API/backend code.
---

# API code review

Read-only. Never edit source.

## Process
1. Read `api/specs/*.yaml`, `stories/*.md`, and `api/src` (features and their colocated `*.test.js`). Run `cd api && npm test` and `npm audit --omit=dev`; record results.
2. Build the coverage table: every spec operation/status/test-case row -> test name -> pass. Missing test = major; behavior differs from spec = major or blocker.
3. Walk the checklist; record findings with `file:line`.
4. Write `reviews/api-review.md` using `.claude/templates/review-report.md`.

## Checklist
- **Spec conformance**: paths, methods, status codes, headers, response shapes, exact error messages, `additionalProperties:false` enforced.
- **Validation**: all inputs (body, params, query) validated server-side; ids parsed/checked; lengths and types; unknown fields rejected; body size limit.
- **SQL injection**: every query uses `prepare()` with `?`/named params; grep for template strings or `+` in SQL; `LIKE` wildcards escaped.
- **Structure**: feature-based (`src/features/<name>/` holds routes, service, repo, schema, migration, tests, `index.js`); cross-feature imports only via `index.js`; `shared/` imports no feature; no top-level `controllers/`, `models/`, `test/` folders.
- **Layering**: SQL only in repo; no business logic in routes; `createApp(db)` injectable.
- **AuthN/AuthZ**: whatever the stories require; no IDOR (resource scoped to owner if users exist); otherwise note "none by design".
- **Hardening**: helmet, CORS not `*` for credentialed use, rate limiting, `x-powered-by` off, JSON-only, no stack traces or SQL errors in responses, no secrets or `.db` files committed (check `.gitignore`).
- **Errors**: central handler, async errors caught, 404 handler, malformed JSON -> 400, consistent error schema, server-side logging without sensitive data.
- **Database**: constraints (NOT NULL, CHECK, UNIQUE), indexes for filtered columns, foreign keys on, transactions for multi-statement writes, migrations idempotent, `node:sqlite` usage correct (`DatabaseSync`, closed on shutdown).
- **HTTP semantics**: correct verbs, 201 + Location on create, 204 on delete with empty body, idempotent PUT/DELETE, pagination limits if lists can grow.
- **Tests**: use in-memory db, isolated per test, assert status + body + content-type, boundary cases, injection attempts, no order dependence, names carry story + case IDs.
- **Dependencies**: only allowed deps (see CLAUDE.md), no known high/critical `npm audit` findings, Node engine pinned.

## Severity
- blocker: injection, data leak, server crash path, tests red, spec contradicted on core flow.
- major: missing validation, untested spec case, missing hardening, wrong status code.
- minor: naming, structure, docs.
