---
name: ui-development
description: Generate and test a React + Vite + TypeScript web UI in ui/ from approved user stories, OpenAPI specs and mockups html and design.md and screen in mock-ui/, using Vitest and Testing Library and the modern-web-guidance skill. Use when asked to build the web UI, screens, or UI tests from stories.
---

# User stories + API spec -> React UI

**First:** invoke the `modern-web-guidance` skill and follow it for all HTML/CSS/client JS decisions (native `<dialog>`, form validation, accessibility, CWV, etc.).

## Scaffold (if `ui/package.json` missing)
`npm create vite@latest ui -- --template react-ts`, then add dev deps: `vitest jsdom @testing-library/react @testing-library/user-event @testing-library/jest-dom`. Configure `test` in `vite.config.ts` (`environment: 'jsdom'`, `setupFiles`). Scripts: `dev`, `build`, `test` (`vitest run`). Dev proxy `/api` -> `http://localhost:3000`.

## Structure
```
ui/src/main.tsx, App.tsx      app shell + routing; composes feature pages
ui/src/features/<feature>/
  api.ts                      typed client, one function per operationId of this feature; uses shared/api/http.ts
  types.ts                    types mirroring the spec schemas for this feature
  components/  hooks/         feature-private UI and hooks
  <Feature>Page.tsx           route-level component
  <Feature>.test.tsx          tests (colocated), story IDs in names
  index.ts                    public exports (page, hooks/types others may use)
ui/src/shared/
  api/http.ts                 the only place that calls fetch; base path '/api'; maps the spec error shape to ApiError
  components/ hooks/          generic, feature-agnostic UI (Button, Dialog, Field...)
  styles/tokens.css           design tokens
  test/setup.ts               Vitest setup
```
Group by feature, not by file type. A feature imports only `shared/` and other features' `index.ts`; `shared/` never imports features. Feature name = resource/capability (`notes`), not story ID. Move code to `shared/` only when a second feature needs it.

## Mockups (`mock-ui/`)
Before building, look in `mock-ui/` at the repo root for UI mockups (images: png/jpg/webp/svg/pdf; HTML/CSS prototypes; or notes in md). Read every file (use the Read tool for images/PDFs).
- Match each mockup to a story by filename (e.g. `US-001-*.png`) or by content; list the mapping in your report.
- Treat mockups as the source of truth for layout, hierarchy, copy, spacing, colors and typography; extract these into CSS variables (design tokens) in `ui/src/shared/styles/tokens.css`.
- If a mockup conflicts with a story's acceptance criteria or the API spec, follow the story/spec and report the conflict. Never silently drop a mockup element; list anything you could not implement.
- If a mockup is HTML/CSS, reuse its structure but rewrite it as semantic, accessible React components per `modern-web-guidance`; don't paste it verbatim.
- If `mock-ui/` is missing or empty, continue from stories alone and say so in the report.
- After building, run the dev server and compare against the mockup visually (screenshot if a browser tool is available); note differences.

## Steps
1. Read approved stories, the spec, and the mockups above. List screens/states per story: loading, empty, error, success, validation errors (mockups often show only the happy state, so design the others consistently with the mockup style).
2. Per feature: `types.ts` mirrors spec schemas, `api.ts` wraps `shared/api/http.ts`, which maps the spec's error shape to a typed `ApiError`.
3. Build components: semantic HTML, labelled inputs, visible focus, `aria-live` for async messages, buttons for actions, links for navigation. Client-side validation mirrors the spec limits (`maxLength`, required) but always display server errors too.
4. Safety: never `dangerouslySetInnerHTML` with user content; no secrets; encode URL params.
5. Tests: mock the api client (not fetch internals). One test per acceptance criterion; name `US-001 AC-2: shows error when title is empty`. Query by role/label, use `userEvent`.
6. Run `npm test` and `npm run build`. Fix until both pass; then start `npm run dev` briefly to confirm it boots.

## Report
Table: story AC -> test name -> pass/fail, plus real test/build summary lines.
