---
name: api-security-review
description: Security review of the Node.js/Express/node:sqlite API in api/ against the OWASP Top 10 (web) and the OWASP API Security Top 10 (2023), with static checks and local probes; writes reviews/api-security.md. Use when asked for a security review of the API/backend.
---

# API security review — OWASP Top 10 + OWASP API Security Top 10

Read-only on source. May run the app locally and probe `localhost` only.

## Process
1. Read `api/specs/*.yaml`, `stories/*.md`, `api/src/**` (features + shared), `package.json`, `.gitignore`. Run `cd api && npm audit --omit=dev` and `npm test`.
2. Static greps: SQL built from strings (`` `SELECT``, `+ ` near `prepare|exec`), `exec(` with input, `eval|Function(`, `child_process`, `fs.` with request data, `fetch(|http.request` with request data, `cors(`, `helmet`, `rateLimit`, `process.env`, `console.log` of bodies/headers, `catch` blocks, `res.send(err`, `err.stack`.
3. Probes (start `PORT=3999 DB_PATH=:memory: node src/server.js`, stop it after): SQL metacharacters in `q`/ids; oversized body; wrong content-type; malformed JSON; unknown fields; negative/huge ids; mass-assignment extra fields (`id`, `createdAt`); repeated requests until 429; response headers; error bodies for stack traces; unexpected method (TRACE/PUT on collection); path traversal chars; HTTP verb tampering. Record each probe and its result in "Checks run".
4. Walk **both** tables; every row gets a result.
5. Write `reviews/api-security.md` from `.claude/templates/security-report.md`.

## Part 1 — OWASP Top 10:2025 (older 2021 IDs in brackets)
| ID | Category | What to check in the API |
|---|---|---|
| A01 | Broken Access Control | Authorization on every route; resources scoped to owner (no IDOR); deny by default; CORS allow-list not `*` with credentials; no method override; (no auth by design -> state it as residual risk, check nothing sensitive is exposed) |
| A02 | Security Misconfiguration | helmet; `x-powered-by` off; no debug/verbose errors; no default creds; `.db`/`.env` ignored in git; safe defaults for `PORT`/`DB_PATH`; body size limit; only needed HTTP methods |
| A03 | Software Supply Chain Failures | `npm audit`; lockfile; only allowed deps (CLAUDE.md); no install scripts from unknown packages; engines pinned |
| A04 | Cryptographic Failures | Passwords hashed with scrypt/argon2 via `node:crypto` (never md5/sha1/plain); secrets from env; TLS terminated upstream noted; no sensitive data in URLs/logs; random via `crypto.randomUUID` not `Math.random` |
| A05 | Injection | Every SQL statement parameterized; `LIKE` wildcards escaped; no `exec` with input; no command/path/NoSQL/template injection; header/CRLF injection; JSON parsed only with `express.json` |
| A06 | Insecure Design | Rate limiting; limits on lengths/page sizes; business rules enforced server-side; abuse cases (bulk create, huge search); transactions for multi-step writes |
| A07 | Authentication Failures | If auth exists: strong hashing, brute-force limits, session/JWT expiry & algorithm pinning, no user enumeration; else n/a |
| A08 | Software or Data Integrity Failures | No unsafe deserialization; no dynamic `import()`/`require` of request data; schema validation before use; migrations not user-influenced |
| A09 | Security Logging & Alerting Failures | Server-side logging of errors/auth events; no secrets, tokens, full bodies in logs; log injection (newlines) considered; request id optional |
| A10 | Mishandling of Exceptional Conditions | Central error handler; async errors caught; malformed JSON -> 400; DB errors -> generic 500, no SQL/stack in response; fail closed; process doesn't crash on bad input (`uncaughtException` not the strategy) |

## Part 2 — OWASP API Security Top 10:2023
| ID | Category | What to check |
|---|---|---|
| API1 | Broken Object Level Authorization | Each `/{id}` route verifies the caller may access that object; IDs unguessable if multi-user (or note single-tenant by design) |
| API2 | Broken Authentication | Same as A07 for API tokens/keys; no auth on sensitive routes unless by design |
| API3 | Broken Object Property Level Authorization | Responses expose only intended fields (no internal columns); mass assignment blocked (`additionalProperties:false`, whitelist fields on create/update) |
| API4 | Unrestricted Resource Consumption | `express-rate-limit`; body size cap; pagination/`limit` max; search/regex cost; timeouts; no unbounded queries |
| API5 | Broken Function Level Authorization | Admin/destructive operations separated and guarded; verb tampering (e.g. DELETE on collection) rejected |
| API6 | Unrestricted Access to Sensitive Business Flows | Flows that can be abused at scale (bulk create/delete) throttled; idempotency where needed |
| API7 | Server Side Request Forgery | Any outbound fetch with user-provided URL? (expect none; confirm by grep) |
| API8 | Security Misconfiguration | Headers (helmet), CORS, error verbosity, HTTP methods, TLS/HSTS noted, content-type enforced (415) |
| API9 | Improper Inventory Management | Only spec'd routes exist (diff routes vs `api/specs`); no leftover debug/test endpoints; one API version; `/health` exposes nothing sensitive |
| API10 | Unsafe Consumption of APIs | Third-party API responses validated before use (expect none; confirm) |

## Severity
- critical: injection, auth bypass/IDOR with real data exposure, RCE, secret leak.
- high: missing validation on write path, mass assignment, no rate limiting on abusable endpoint, stack/SQL leaks, high/critical prod dependency vuln.
- medium: missing helmet/CORS tightening, missing body limit, weak logging.
- low: hardening notes.
