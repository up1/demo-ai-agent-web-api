# Project conventions

Full-stack app built by an agent pipeline. Run it with `/build-app requirement.md`.

## Layout
```
requirement.md        user input
stories/              US-001-<slug>.md (one story per file), _questions.md
reviews/              ui-review.md, api-review.md
mock-ui/              UI mockups (images, HTML, notes) read by ui-developer; optional
ui/                   React + Vite + TypeScript, Vitest + Testing Library
api/                  Node.js 25 + Express + built-in node:sqlite
  specs/              OpenAPI 3.1 YAML, one file per story (US-001.openapi.yaml)
  src/features/<name>/{routes,service,repo,schema}.js
  src/db.js, src/app.js (exports app, no listen), src/server.js (listen)
  test/               node --test
```

## Pipeline
1. `story-analyst` + skill `story-analysis` -> `stories/`  (human approves, status: approved)
2. `api-developer` + skill `api-spec` -> `api/specs/`
3. `ui-developer` + skill `ui-development` -> `ui/`   (loads `modern-web-guidance` first)
4. `api-developer` + skill `api-development` -> `api/`
5. `code-reviewer` + `ui-code-review` / `api-code-review` -> `reviews/`; developer fixes blocker/major; max 2 rounds

Only work on stories with `status: approved`.

## Stack rules
- Node >= 25, ESM only (`"type": "module"`). API uses `node:sqlite` (`DatabaseSync`). No ORM, no better-sqlite3.
- API runtime deps allowed: express, helmet, cors, express-rate-limit. Dev deps: none needed (`node --test`).
- SQL: parameterized statements only (`db.prepare(sql).run(...params)`). Never string-concatenate input.
- Validate every input at the route boundary. Errors return `{ "error": { "code", "message" } }`; never leak stack traces.
- No secrets in the repo. Config via env vars with safe defaults (`PORT`, `DB_PATH`).
- UI: React + Vite + TypeScript, plain CSS, Vitest + React Testing Library. Call the API only through `ui/src/api/` client.
- Traceability: story ID (e.g. US-001) appears in the spec (`x-story`), API test names, and UI test names.

## Commands
- API: `cd api && npm test` / `npm start`
- UI: `cd ui && npm test` / `npm run build` / `npm run dev`

## Definition of done
Tests green, build passes, review verdict `approve`, every acceptance criterion covered by a test.
