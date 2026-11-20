---
name: ui-code-review
description: Review the React/Vite UI in ui/ against user stories, modern-web-guidance, accessibility, React best practices, security and test quality, and write reviews/ui-review.md. Use when asked to review web/UI code.
---

# UI code review

Read-only. Load the `modern-web-guidance` skill first and judge HTML/CSS/JS against it.

## Process
1. Read `stories/*.md` (approved) and `ui/` sources. Run `cd ui && npm test && npm run build`; record results. Run `npm audit --omit=dev` too.
2. Build the coverage table: each acceptance criterion -> test(s). Missing = at least major.
3. Go through the checklist; record findings with `file:line`.
4. Write `reviews/ui-review.md` using `.claude/templates/review-report.md`. Round number increments on re-review.

## Checklist
- **Stories**: all ACs implemented; empty/loading/error states exist; validation limits match spec.
- **Modern web**: native elements over div-soup (`dialog`, `details`, `form` constraint validation), modern CSS, no obsolete patterns flagged by the guidance skill.
- **Accessibility**: labels for every input, heading order, keyboard operability, focus management after create/delete/dialog, `aria-live` for status, contrast, alt text, no click handlers on non-interactive elements.
- **React**: effect deps correct and cleanup/abort on fetch, stable `key`s, no derived state copied into state, no state set during render, controlled inputs, no needless re-render hot spots, error boundary for render crashes.
- **Security**: no `dangerouslySetInnerHTML`/`innerHTML` with user data, no `javascript:` URLs, no secrets/tokens in bundle or localStorage without need, API base not hard-coded to localhost.
- **API usage**: all calls through `src/api/`, handles non-2xx per spec error shape, double-submit prevented.
- **Tests**: assert user-visible behavior (role/label queries), no snapshot-only tests, no tests that pass without the feature, story IDs in names, async handled with `findBy`/`waitFor`.
- **Build/quality**: TypeScript strict, no `any` without reason, no console noise, no unused deps, bundle sane.

## Severity
- blocker: security hole, build/test red, core AC broken.
- major: AC untested/missing, a11y barrier, data-loss/race bug.
- minor: style, naming, small refactors.
