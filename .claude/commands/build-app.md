---
description: Run the full pipeline (stories -> api spec -> ui + api -> reviews) from a requirement file
argument-hint: <requirement file, default requirement.md>
---

Requirement file: `$ARGUMENTS` (default `requirement.md`).

Run these stages in order, using the named subagents. Stop and report if a stage fails.

1. **Stories** — `story-analyst`: analyze the requirement, write `stories/US-xxx-*.md` and `stories/_questions.md`.
   **GATE:** show the user the stories and open questions. Wait for answers/approval. Set approved stories to `status: approved`. Do not continue until the user approves.
2. **API spec** — `api-developer` in *spec mode* for all approved stories.
3. **Build** (can run in parallel, they touch different folders):
   - `ui-developer` for `ui/`
   - `api-developer` in *implement mode* for `api/`
4. **Review** — `code-reviewer` for `ui/` and for `api/` (two separate runs).
5. **Fix loop** — if a verdict is `changes-requested`, send the review file to the matching developer agent, then re-review. Maximum 2 rounds; then report remaining findings to the user.
6. **Report** — per stage: what was produced, test/build results, review verdicts, anything unresolved.
