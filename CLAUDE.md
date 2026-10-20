# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

Small Express REST API (users + health check) with an in-memory store, used as the starter project for a Claude Code course.

## Commands

- `npm install` — install dependencies (required before anything else)
- `npm run dev` — start the API on http://localhost:3000 with `node --watch` (port via `PORT`)
- `npm test` — run all tests with the built-in `node --test` runner
- `node --test tests/users.test.js` — run one test file; add `--test-name-pattern="404"` to run a single test
- `npm run lint` — ESLint (`eslint:recommended`); CI runs lint then tests on Node 22

## Conventions

- CommonJS only: use `require` / `module.exports`, not `import` / `export` (ESLint is set to `sourceType: "script"`).
- Tests use `node:test` + `node:assert` + `supertest`, not Jest or Mocha. Import `app` from `../server` and call `request(app)`; never start a real server in tests.
- Route handlers access data only through functions exported by `db/store.js`, never by touching the `users` array directly.
- Errors are returned as JSON `{ error: "<message>" }` with the matching status (400 for missing fields, 404 for not found); successful creates return 201.
- Use double quotes and semicolons, matching the existing files.

## Architecture

- `server.js` builds the Express app, mounts each router under its resource path (`/users`, `/health`), and exports `app`. It only calls `listen()` when run directly (`require.main === module`), which is what lets tests import it.
- `routes/` holds one `express.Router()` file per resource. A new resource means a new file here plus an `app.use(...)` line in `server.js`.
- `db/store.js` is a module-level in-memory array that stands in for a database. State resets on restart but is **shared across tests in the same process**, so tests must not assume a fixed user count or IDs beyond the two seeded users.
- Route params are strings; convert them with `Number(req.params.id)` before lookup because the store compares with `===`.
