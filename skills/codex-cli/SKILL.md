---
name: codex-cli
description: Use Codex CLI as an independent agent for delegated work, code review, or continued discussion in an existing session.
---

# Codex CLI

Codex runs non-interactively through `codex exec`. A new run starts a separate Codex session in the selected working directory, streams progress to stderr, and prints its final response to stdout.

## Run a task

Use `gpt-5.6-sol` with `high` reasoning effort by default:

```bash
codex -a never \
  -C <working-directory> \
  -m gpt-5.6-sol \
  -c model_reasoning_effort=high \
  exec "<request>"
```

`-a never` is a global flag and therefore appears before `exec`. It keeps an unattended run from waiting for interactive approval; failed operations return to Codex instead.

Pass the request as the final argument, or replace it with `-` to read from stdin.

### Model and reasoning

`gpt-5.6-sol` is the default, higher-capability model. `gpt-5.6-luna` is the faster model for simple tasks. Set the model explicitly with `-m` on every run.

Reasoning effort accepts `low`, `medium`, `high`, or `xhigh`. Higher effort gives the model more room to deliberate and generally increases latency. Use `high` by default and set it explicitly with `-c model_reasoning_effort=<level>`.

## Continue a session

Sessions are saved by default. Add `--json` when the caller needs the session ID in a machine-readable form:

```bash
codex -a never \
  -C <working-directory> \
  -m gpt-5.6-sol \
  -c model_reasoning_effort=high \
  exec --json "<request>"
```

The `thread.started` event contains `thread_id`. Continue that session with the same model and effort unless the caller selects different values:

```bash
codex -a never \
  -C <working-directory> \
  -m gpt-5.6-sol \
  -c model_reasoning_effort=high \
  exec resume <thread-id> "<follow-up>"
```

`resume --last` selects the most recently saved session; a thread ID selects a specific one.

## Review code

Use the review subcommand with one scope:

```bash
codex -a never \
  -C <working-directory> \
  -m gpt-5.6-sol \
  -c model_reasoning_effort=high \
  exec review --uncommitted
```

The available scopes are `--uncommitted`, `--base <branch>`, and `--commit <sha>`. A final positional argument supplies review instructions.

## Command reference

Use `codex exec --help`, `codex exec resume --help`, and `codex exec review --help` for the command surface installed on the machine.
