# AI agents + skills for full-stack web app development

A Claude Code setup that turns a written requirement into a working web app:

- **Web:** React + Vite + TypeScript (`ui/`)
- **API:** Node.js 25 + Express + built-in `node:sqlite` (`api/`)

Each stage is done by one **agent** (a role with limited tools) that follows one **skill** (step-by-step instructions and checklists).

## Flow

```
requirement.md          mock-ui/ (optional mockups)
      │                       │
      ▼                       │
[1] story-analyst             │
    skill: story-analysis     │
      │  → stories/US-001-*.md (one story per file)
      │  → stories/_questions.md
      ▼
  ── GATE: you answer questions and approve stories (status: approved) ──
      │
      ▼
[2] api-developer (spec mode)
    skill: api-spec
      │  → api/specs/US-001.openapi.yaml ...
      ▼
   ┌──────────────────────────────┬──────────────────────────────┐
   ▼ (parallel)                   ▼
[3] ui-developer              [4] api-developer (implement mode)
    skills: modern-web-guidance,      skill: api-development
            ui-development            → api/  (Express, node:sqlite, node --test)
    reads: stories, spec, mock-ui/
    → ui/  (React, Vitest)
   │                              │
   ▼                              ▼
[5] code-reviewer             [5] code-reviewer
    skill: ui-code-review         skill: api-code-review
    → reviews/ui-review.md        → reviews/api-review.md
   │                              │
   └── changes-requested? ────────┘
       developer fixes blocker/major findings → re-review (max 2 rounds)
   ▼                              ▼
[6] security-reviewer         [6] security-reviewer
    skill: web-security-review    skill: api-security-review
    OWASP Top 10                  OWASP Top 10 + API Security Top 10
    → reviews/ui-security.md      → reviews/api-security.md
   │                              │
   └── changes-requested? ────────┘
       developer fixes critical/high findings → re-review (max 2 rounds)
```

| # | Agent | Skill(s) | Input | Output |
|---|---|---|---|---|
| 1 | `story-analyst` | `story-analysis` | `requirement.md` | `stories/`, `stories/_questions.md` |
| 2 | `api-developer` | `api-spec` | approved stories | `api/specs/*.openapi.yaml` |
| 3 | `ui-developer` | `modern-web-guidance`, `ui-development` | stories, spec, `mock-ui/` | `ui/` + tests |
| 4 | `api-developer` | `api-development` | spec | `api/` + tests |
| 5 | `code-reviewer` | `ui-code-review`, `api-code-review` | code, stories, spec | `reviews/*.md` with verdict |
| 6 | `security-reviewer` | `web-security-review`, `api-security-review` | code, spec, running API (localhost) | `reviews/ui-security.md`, `reviews/api-security.md`: every OWASP category marked pass/fail/n/a/not verified |

Code review (5) checks correctness, spec conformance, quality and basic safety. Security review (6) goes deeper: every OWASP category gets an explicit result, and the API is probed locally with hostile input.

Rules that hold the pipeline together:

- Only stories with `status: approved` are built.
- The OpenAPI spec is the contract. Status codes and error messages in tests are copied from it.
- Reviewers are read-only. They write only the report, so they never review their own fixes.
- Story IDs (`US-001`) appear in the spec (`x-story`) and in test names, so each criterion can be traced to a test.
- Done means tests pass, the build passes, and the code review and security review verdicts are `approve`.

## Repository layout

```
CLAUDE.md                    project conventions read by all agents
requirement.md               your input (sample: Notes app)
mock-ui/                     optional UI mockups (png/jpg/pdf/html/md), name by story ID
stories/                     generated user stories
reviews/                     generated review reports
ui/                          generated web app (feature-based)
api/                         generated API (feature-based)
.claude/
  agents/                    story-analyst, ui-developer, api-developer,
                             code-reviewer, security-reviewer
  skills/                    story-analysis, api-spec, api-development, ui-development,
                             ui-code-review, api-code-review,
                             web-security-review, api-security-review
  commands/build-app.md      /build-app command
  templates/                 review-report.md, security-report.md
```

## Feature-based structure of the generated code

Code is grouped by feature (for example `notes`), with its tests next to it. A feature imports only `shared/` and other features' public `index` file. `shared/` never imports a feature.

```
api/
  specs/                       OpenAPI files, one per story
  src/
    app.js  server.js          createApp({db}) mounts feature routers; server listens
    shared/                    db.js, errors.js, middleware/, test-utils.js
    features/notes/
      index.js                 public: createRouter(db), migrate(db)
      notes.routes.js  notes.service.js  notes.repo.js
      notes.schema.js  notes.migration.js  notes.test.js

ui/
  src/
    main.tsx  App.tsx          app shell, routing
    shared/                    api/http.ts, components/, hooks/, styles/tokens.css, test/setup.ts
    features/notes/
      index.ts                 public exports
      api.ts  types.ts         typed client and types for this feature
      components/  hooks/      private to the feature
      NotesPage.tsx  Notes.test.tsx
```

## Prerequisites

- [Claude Code](https://claude.com/claude-code) (CLI or VS Code extension)
- Node.js 25 or newer (`node -v`), npm
- The `modern-web-guidance` skill installed (used by the UI stages)
- Network access for `npm install` (and the optional `npx @redocly/cli` spec lint)

## How to run

### Option A: full pipeline

1. Write your requirement in `requirement.md` (a sample Notes app is included).
2. Optional: put mockups in `mock-ui/`, for example `mock-ui/US-001-notes-list.png`.
3. Start Claude Code in this folder, then run:
   ```
   /build-app requirement.md
   ```
4. **Stage 1 pauses.** Read `stories/*.md` and `stories/_questions.md`. Answer the questions and tell Claude to approve the stories.
5. The rest runs on its own: API spec, then UI and API builds, then reviews and fix rounds.
6. Read the final report and the reviews: `reviews/ui-review.md`, `reviews/api-review.md`, `reviews/ui-security.md`, `reviews/api-security.md`.

### Option B: stage by stage

Run one stage at a time to inspect results in between:

```
Use the story-analyst agent to analyze requirement.md
Use the api-developer agent in spec mode for all approved stories
Use the api-developer agent in implement mode
Use the ui-developer agent to build the UI
Use the code-reviewer agent to review api/
Use the code-reviewer agent to review ui/
Use the security-reviewer agent to review api/ (OWASP Top 10 + API Security Top 10)
Use the security-reviewer agent to review ui/ (OWASP Top 10)
```

## Run the generated app

```bash
# API  (http://localhost:3000)
cd api && npm install && npm start

# Web  (http://localhost:5173, proxies /api to the API)
cd ui && npm install && npm run dev
```

Run the tests:

```bash
cd api && npm test      # node --test
cd ui  && npm test      # vitest
cd ui  && npm run build
```

Environment variables for the API: `PORT` (default 3000), `DB_PATH` (default `./data.db`).

## Customizing

- Change stack rules, folders or the definition of done in `CLAUDE.md`.
- Change how a stage works by editing its `SKILL.md`. Change the agent's role or allowed tools in `.claude/agents/*.md`.
- Add a stage (for example security review or E2E tests) by adding an agent and a skill, then adding a step in `.claude/commands/build-app.md`.
- After editing agents or commands, restart Claude Code so it reloads them.
