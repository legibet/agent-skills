---
name: codex-cli
description: Use Codex CLI as an independent agent for delegated work, code review, or continued discussion in an existing session.
---

# Codex CLI

Codex runs non-interactively through `codex exec`. A new run starts a separate Codex session in the selected working directory, streams progress to stderr, and prints its final response to stdout. Redirect stderr to `/tmp/codex.err` so the turn log stays out of the caller context. A run is done when the process exits; stdout is the reply. If the command fails, read `/tmp/codex.err`.

## Run a task

Use `gpt-5.6-sol` with `high` reasoning effort by default:

```bash
codex -a never \
  -C <working-directory> \
  -m gpt-5.6-sol \
  -c model_reasoning_effort=high \
  exec "<request>" \
  </dev/null 2>/tmp/codex.err
```

`-a never` is a global flag and therefore appears before `exec`. It keeps an unattended run from waiting for interactive approval; failed operations return to Codex instead.

`</dev/null>` keeps piped stdin from being appended to the prompt.

### Model and reasoning

`gpt-5.6-sol` is the default, higher-capability model. `gpt-5.6-luna` is the faster model for simple tasks. Set the model explicitly with `-m` on every run.

Reasoning effort accepts `low`, `medium`, `high`, or `xhigh`. Higher effort gives the model more room to deliberate and generally increases latency. Use `high` by default and set it explicitly with `-c model_reasoning_effort=<level>`.

## Continue a session

`resume --last` continues the most recent session in that working directory. A thread ID continues a specific one. The thread id is on the `session id:` line in `/tmp/codex.err`. Keep model and effort unless the caller picks different values.

```bash
codex -a never \
  -C <working-directory> \
  -m gpt-5.6-sol \
  -c model_reasoning_effort=high \
  exec resume --last "<follow-up>" \
  </dev/null 2>/tmp/codex.err
```

```bash
codex -a never \
  -C <working-directory> \
  -m gpt-5.6-sol \
  -c model_reasoning_effort=high \
  exec resume <thread-id> "<follow-up>" \
  </dev/null 2>/tmp/codex.err
```

## Review code

Pass either one scope flag or a prompt. `--help` lists both; the CLI still rejects that combination (exit 2).

```bash
codex -a never \
  -C <working-directory> \
  -m gpt-5.6-sol \
  -c model_reasoning_effort=high \
  exec review --uncommitted \
  </dev/null 2>/tmp/codex.err
```

Other shapes: `review --base <branch>`, `review --commit <sha>`, `review "<instructions>"`.

## Command reference

Use `codex exec --help`, `codex exec resume --help`, and `codex exec review --help` for the command surface installed on the machine.
