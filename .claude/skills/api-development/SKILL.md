---
name: api-development
description: Implement and test a REST API from OpenAPI specs in api/specs using Node.js 25, Express, and built-in node:sqlite with a feature-based structure and node --test. Use when asked to build, scaffold, or implement the API or its tests from the spec.
---

# OpenAPI spec -> Express + node:sqlite API

Node >= 25, ESM. Only deps: `express`, `helmet`, `cors`, `express-rate-limit`. No ORM.

## Scaffold (if `api/package.json` missing)
```
api/package.json   {"type":"module","scripts":{"start":"node src/server.js","test":"node --test"},"engines":{"node":">=25"}}
api/src/db.js      openDb(path=process.env.DB_PATH ?? './data.db') -> DatabaseSync; runs migrations; PRAGMA foreign_keys=ON; journal_mode=WAL
api/src/app.js     createApp({db}) -> express app (helmet, cors, express.json({limit:'100kb'}), rate limit, routes, 404, error handler)
api/src/server.js  createApp(openDb()).listen(PORT ?? 3000)
api/src/features/<feature>/{routes,service,repo,schema}.js
api/src/middleware/{error,validate}.js
api/test/<feature>.test.js
```
`createApp` takes the db as a parameter so tests use `new DatabaseSync(':memory:')`.

## Layering
- **routes**: parse request, call schema validator, call service, shape response. No SQL.
- **schema**: pure validators returning `{ok, value}` or `{ok:false, details:[{field,message}]}`; trim strings, reject unknown fields, enforce lengths from the spec.
- **service**: business rules; throws `AppError(status, code, message)`.
- **repo**: the only place with SQL. Always `db.prepare('... WHERE id = ?').get(id)`; never interpolate input. Search uses `LIKE ? ESCAPE '\'` with escaped `%`/`_`.
- **error middleware**: maps `AppError` -> spec'd status/body; unknown -> 500 `{error:{code:'INTERNAL_ERROR',message:'Internal server error'}}` and logs server-side only. Malformed JSON -> 400.
- Express 5 handles async errors; if on Express 4 wrap handlers.

## Steps
1. Read `api/specs/*.yaml`; checklist every operation, status, exact message.
2. Write migrations (`CREATE TABLE IF NOT EXISTS`, constraints, indexes for searched columns). Timestamps as ISO strings.
3. Implement feature by feature, in story order.
4. Tests (`node --test`, `node:assert/strict`): start app on port 0 with in-memory db, call with `fetch`. One test per spec test-case row; name format `US-001 TC-03: rejects title over 100 chars`. Assert status, content-type, and body shape/messages verbatim.
5. Add tests for security basics: SQL metacharacters in search/ids are treated as data; oversized body -> 413/400; unknown JSON fields rejected.
6. Run `npm test`. Fix until green. Also boot `node src/server.js` once and curl a route to confirm it really starts.

## Report
Table: spec test case -> test name -> pass/fail. Paste the real `npm test` summary line.
