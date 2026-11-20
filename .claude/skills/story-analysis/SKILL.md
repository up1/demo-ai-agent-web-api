---
name: story-analysis
description: Analyze a free-text requirement, identify ambiguities, and produce INVEST-style user stories with Given/When/Then acceptance criteria, one story per file in stories/. Use when the user provides a requirement or asks to clarify, split, or write user stories.
---

# Requirement -> user stories

## Steps
1. Read the requirement file fully. List actors, capabilities, data, constraints, non-functional hints (security, performance).
2. Find gaps: undefined validation limits, missing error behavior, auth, ordering/pagination, delete semantics (hard/soft), empty states, concurrency. Write each as a numbered question with your **proposed default** so the user can just say "ok".
3. Split into small stories (one user-visible capability each; one CRUD verb per story is fine). Each must be independent, testable, and fit one UI view + a few endpoints.
4. Write each story to `stories/US-<nnn>-<kebab-slug>.md` using `templates/user-story.md`. IDs are sequential and never reused.
5. Write `stories/_questions.md` (template below). Stories depending on unanswered questions stay `status: draft`; others may be proposed as `draft` too — only the user approves.
6. Reply with a table: ID, title, status, and the questions needing answers.

## Quality bar
- Acceptance criteria are observable and specific (exact limits, exact messages if known). No "should be user friendly".
- Always cover: happy path, validation failure, not-found, empty state.
- Include `API needs` (resource + operation, no design) and `UI notes` (views/states) so downstream agents have hints without being constrained.
- Put anything not in scope under `Out of scope`, don't silently drop requirements.

## `_questions.md` format
```
# Open questions
| # | Question | Proposed default | Affects | Answer |
|---|---|---|---|---|
```
After the user answers, update stories, fill the Answer column, and set `status: approved` only on explicit approval.
