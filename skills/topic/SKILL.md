---
name: topic
description: >-
  Document-driven workflow for deferred work: capture and explore an idea or
  issue, distill it into an implementation plan, then execute. Use when the
  user wants to record an idea or issue for later, resume a topic, write a
  plan.md, or implement an existing plan document.
---

# Topic

This skill is a document-driven workflow for deferred work: things worth doing later. Capture them so they survive, shape them into plans, execute them when the time comes. Immediate fixes go straight to execution.

## Routing

### Start a topic

In a mature project, create a folder for the topic at `.agents/topics/<topic>/`. In a new project, put the documents directly in the project root, as the topic is the project itself. A user-specified path wins. Create the kickoff document, classified as one of:

- **idea**: something new should exist (feature, project, direction)
- **issue**: something is wrong (bug, UX, architecture)

Then read `references/idea.md` or `references/issue.md` accordingly. If a topic fits both, pick either; the lifecycle is the same.

### Resume a topic

Read the kickoff document's `status` and load the reference for that stage:

- `idea` or `issue`: `references/idea.md` or `references/issue.md`
- `plan`: `references/plan.md`
- `implementing`: `references/implementation.md`
- `dropped`: the topic is closed

## Documents

Each topic has a kickoff document (`idea.md` or `issue.md`), a `plan.md` when needed, and any supporting files (research notes, demo scripts). Reference supporting files from the kickoff document.

- Keep working documents out of git history; promote lasting ones into `docs/`.
- The kickoff document records the topic's stage and last update in its frontmatter:

  ```yaml
  ---
  status: idea
  updated: 2026-08-09
  ---
  ```

  The stage moves forward: `idea`/`issue` → `plan` → `implementing` → `done`. Simple topics skip the plan and go straight from `idea`/`issue` to `done`. A topic given up on ends as `dropped`.

## Working rules

- Record facts and first thoughts directly. Bring judgments and decisions to the user first.
- Keep documents organized at all times. Prefer precise edits over rewrites.
- Documents are working notes, not permanent rules. When one is wrong or outgrown, fix it. Changing what a document says goes through the user: propose the change, then edit once approved.
- The user decides when a stage ends; update the status when a new stage starts.
- Simple tasks skip the plan: once the direction converges, go straight to execution.
