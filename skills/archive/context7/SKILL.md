---
name: context7
description: Fetch up-to-date library docs via the ctx7 CLI. Use when the user mentions ctx7 or Context7, or needs current API docs, code examples, or version-specific usage for any library.
---

# ctx7 — library documentation lookup

Two-step flow: resolve the library ID, then fetch docs. Skip step 1 when the user already provided an exact ID (like `/org/project`).

## Step 1 — resolve the library ID

```bash
npx ctx7@latest library <name> "<query>"
```

```bash
ctx7 library react "useEffect cleanup with async operations"
ctx7 library nextjs "app router middleware auth"
ctx7 library prisma "one-to-many relations with cascade delete"
```

- Write a descriptive query drawn from the user's actual task. Single concept per query — if the question spans multiple topics, make separate calls.
- Prefer official / primary packages when multiple matches look similar.
- When the user asks for a specific version, check the returned version list and use a versioned ID (`/org/project/version`) in step 2.
- If the match is ambiguous, say which one you chose.

## Step 2 — fetch docs

```bash
ctx7 docs <libraryId> "<query>"
```

Library IDs always start with `/`.

```bash
ctx7 docs /facebook/react "useEffect cleanup with async operations"
ctx7 docs /vercel/next.js "middleware that redirects unauthenticated users"
ctx7 docs /prisma/prisma "one-to-many relations with cascade delete"
ctx7 docs /vercel/next.js/v14.3.0-canary.87 "app router setup"
```

- Single concept per query — split multi-topic questions into separate calls.
- Use `--json` only when structured fields matter for downstream processing.
- Budget: at most 3 `library` calls and 3 `docs` calls per question.

## Rate limits

`library` and `docs` work without login. If the CLI reports quota exhaustion, tell the user and suggest `ctx7 login` or setting `CONTEXT7_API_KEY`.
