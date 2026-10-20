# Notes

## What I kept in CLAUDE.md, and why

- **Commands**: install, dev, test, lint, plus how to run a single test file or test. These are the commands Claude needs most often, and the single-test form (`node --test <file> --test-name-pattern=...`) isn't obvious from `package.json`.
- **Conventions**: rules Claude would otherwise get wrong or have to ask about: CommonJS only (ESLint is set to `sourceType: "script"`), `node:test` + `supertest` rather than Jest, data access only through `db/store.js`, and the `{ error }` JSON shape with 400/404/201 status codes.
- **Architecture**: the parts that take several files to understand: `server.js` exports `app` and only listens when run directly (that's why tests can import it), one router per resource in `routes/`, and the in-memory store that is shared across tests in the same process, so tests can't rely on a fixed user count.

## What I left out, and why

- The full file list: Claude can see it from the code.
- The course steps from the README: they're one-off tasks, not project guidance.
- Anything from `.env` or other secrets: CLAUDE.md is committed and shared.
- Generic advice ("write good tests", "handle errors"): it adds length without changing behaviour.

## Permission rules (.claude/settings.json)

- **allow** `npm test` and `npm run lint`: safe, read-only checks that I run all the time, so Claude shouldn't have to ask before each one.
- **ask** `git push`: pushing publishes my work, so I want to confirm each time.
- **deny** reading `.env`: real secrets would live there. Without this rule Claude could read them and they could end up in a session transcript or a commit.
- **deny** `git push --force` / `git push -f`: a force-push can overwrite and destroy history on the remote. Deny takes priority over ask, so it is blocked outright instead of being one confirmation away.

## .gitignore fix

The original `.gitignore` had leading spaces on most lines. Git treats leading spaces as part of the pattern, so `.env` and `.claude/settings.local.json` were **not** actually ignored (`git check-ignore` confirmed it). I removed the spaces so a real `.env` can't accidentally be pushed to GitHub, and the personal settings file stays local.

## Verification

- `/memory` lists the project `CLAUDE.md` as loaded, so Claude picks it up at the start of each session.
- `/permissions` shows the allow, ask and deny rules from `.claude/settings.json`, so they are active.
