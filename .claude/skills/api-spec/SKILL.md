---
name: api-spec
description: Convert approved user stories into OpenAPI 3.1 YAML specs (one per story) in api/specs/, with error format, validation rules, and a test-case table. Use when asked to design or write the REST API contract from user stories.
---

# User stories -> OpenAPI spec

## Steps
1. Read approved stories in `stories/` (status: approved). Skip drafts.
2. Design REST resources from the "API needs" and acceptance criteria. Plural nouns, standard verbs, JSON only.
   - create -> `POST /notes` 201 + body; list -> `GET /notes` 200; get -> `GET /notes/{id}`; update -> `PUT/PATCH`; delete -> `DELETE` 204.
   - Search/filter via query params (`?q=`). Reuse paths across stories; one file per story contains only the operations that story introduces, referencing shared schemas in `api/specs/components.yaml`.
3. For each story write `api/specs/US-nnn.openapi.yaml` (OpenAPI 3.1):
   - `x-story: US-nnn` on every operation; `operationId` in camelCase.
   - Request/response schemas with `type`, `required`, `maxLength`, `minLength`, `additionalProperties: false`.
   - Every operation declares success + `400` (validation), `404` (when an id is used), `500`.
   - Shared error schema: `{ "error": { "code": "VALIDATION_ERROR", "message": "...", "details": [{ "field", "message" }] } }`.
   - At least one `example` per response with exact messages (tests copy them verbatim).
4. Add the **test-case table** inside `info.description` or `x-test-cases`: id, request, expected status, expected body fragment. Cover happy, each validation rule, not-found, boundary (exact max length, max+1).
5. Validate: every acceptance criterion in the story maps to at least one test case. Report any criterion you could not map.
6. Lint if available: `npx --yes @redocly/cli lint api/specs/*.yaml` (network allowed only for this); otherwise parse with a YAML loader to confirm validity.

## Don't
- No implementation details (tables, libs). No auth unless a story says so.
- Don't change the story files; raise conflicts back to the caller.
