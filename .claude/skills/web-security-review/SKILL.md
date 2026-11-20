---
name: web-security-review
description: Security review of the React/Vite web UI in ui/ against the OWASP Top 10 (web), covering XSS, client-side access control, secrets, supply chain, headers/CSP and error handling; writes reviews/ui-security.md. Use when asked for a security review of the web/frontend code.
---

# Web (UI) security review — OWASP Top 10

Read-only. Load `modern-web-guidance` and use its security guidance (CSP, Trusted Types, sanitization, safe storage) as the baseline for what "good" looks like.

## Process
1. Read `ui/` sources, `ui/package.json` + lockfile, `vite.config.ts`, `index.html`, `.env*` (report location of secrets, never values). Run `cd ui && npm audit --omit=dev` and `npm run build`.
2. Search patterns: `dangerouslySetInnerHTML|innerHTML|outerHTML|insertAdjacentHTML|document.write|eval\(|new Function|href=\{|src=\{|window.open|postMessage|localStorage|sessionStorage|document.cookie|VITE_|console\.`.
3. Walk every category below; record result + finding refs in the report table.
4. Write `reviews/ui-security.md` from `.claude/templates/security-report.md`.

## Categories (OWASP Top 10:2025; older 2021 IDs in brackets)
| ID | Category | What to check in the UI |
|---|---|---|
| A01 | Broken Access Control | Route guards are UX only, real enforcement must be on API; no hidden admin UI relying on client flags; IDs from URL not trusted; open redirects (`?next=`, `window.location = param`); CORS-dependent assumptions |
| A02 | Security Misconfiguration (2021 A05) | CSP present or documented (no `unsafe-inline`/`unsafe-eval` needed); Vite dev server/proxy not exposed in prod; source maps not shipped publicly; no debug flags; `.env` not committed; clickjacking protection (frame-ancestors) noted for host config |
| A03 | Software Supply Chain Failures (2021 A06) | `npm audit` high/critical; lockfile committed; unpinned/`latest`/git deps; unnecessary or abandoned packages; install scripts; CDN scripts without SRI |
| A04 | Cryptographic Failures (2021 A02) | No secrets/keys/tokens in bundle or `VITE_*`; sensitive data not in localStorage/URL; no home-made crypto; API calls over relative/https URLs, no hard-coded `http://` |
| A05 | Injection (includes XSS; 2021 A03) | No `dangerouslySetInnerHTML`/`innerHTML` with user data (or sanitized with a vetted lib/Sanitizer API); no `javascript:`/`data:` URLs from user input; no `eval`; URL params encoded; no dynamic script/style injection; template/markdown rendering sanitized |
| A06 | Insecure Design | Destructive actions confirmed; double-submit prevented; client validation mirrors server but never replaces it; no trust in client-side business rules |
| A07 | Authentication Failures (2021 A07) | If auth exists: no tokens in localStorage (prefer httpOnly cookie), logout clears state, no credentials in URL, no password in logs; else mark n/a "no auth by design" |
| A08 | Software or Data Integrity Failures | SRI for third-party scripts, no unsafe deserialization of API/`postMessage` data without validation, dependency integrity via lockfile, build pipeline not fetching code at runtime |
| A09 | Security Logging & Alerting Failures | No sensitive data in `console.*`/error reports; errors surfaced to user without internals; note if no client error reporting (low) |
| A10 | Mishandling of Exceptional Conditions | Error boundary, API failures/timeouts/non-JSON responses handled, no crashes or blank screens on unexpected data, no stack traces or raw server messages rendered as HTML, fail closed |

## Severity
- critical: exploitable XSS, secret in bundle, auth bypass.
- high: unsanitized HTML sink with plausible user data, high/critical vuln in prod dependency, tokens in localStorage with XSS exposure.
- medium: missing CSP, missing SRI, weak error handling leaking info.
- low: hardening gaps, notes.
