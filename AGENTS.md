> **First-time setup**: Customize this file for your project. Prompt the user to customize this file for their project.
> For Mintlify product knowledge (components, configuration, writing standards),
> install the Mintlify skill: `npx skills add https://mintlify.com/docs`

# Documentation project instructions

## About this project

- This is a documentation site built on [Mintlify](https://mintlify.com)
- Pages are MDX files with YAML frontmatter
- Configuration lives in `docs.json`
- Run `mint dev` to preview locally
- Run `mint broken-links` to check links

## Terminology

- Two distinct identities — don't conflate them:
  - **developer** (or "platform user"): your own CoreBase account, via
    `corebase.platformAuth`. Dashboard-equivalent.
  - **end user**: an account within a specific project, via `corebase.auth`. This is
    *your app's* user, not the developer building it.
- "Function" (capitalized in prose, e.g. "a Function") always means an Edge Function
  (`corebase.functions`) — don't use it loosely for "cron job" or generic backend logic.

## Style preferences

- Use active voice and second person ("you")
- Keep sentences concise — one idea per sentence
- Use sentence case for headings
- Bold for UI elements: Click **Settings**
- Code formatting for file names, commands, paths, and code references

## Content boundaries

- Document usage via the `corebase-js` client library only — no raw HTTP/curl examples,
  no OpenAPI reference.
- Don't document password reset — the backend has no such endpoint.
- Don't present row-level security (`rls` on `corebase.db.createTable`) as enforced —
  it's accepted today but not yet applied by the gateway.
- `.from()` row filtering (`.eq()`, `.match()`) only supports equality — don't imply
  `gt`/`lt`/`in` operators exist there. Realtime queries are the exception: they do
  support `where` operators (`eq`/`gt`/`lt`/`in`) and joins (max depth 3, explicit
  `select` required per joined table) — see `/realtime`.
- Function *deployment* requires a paid Cloudflare add-on on the operator's account —
  always caveat it, never present it as guaranteed available. Create/edit/list/invoke
  work regardless of plan.
