---
name: antigravity-cli
description: Delegate work to Antigravity CLI (agy) as an independent agent, or continue an existing agy conversation.
---

# Antigravity CLI

Headless Antigravity through `agy -p`. The process working directory is the workspace. `--output-format json` writes one final envelope to stdout. Redirect stderr to `/tmp/agy.err` so the turn log stays out of the caller context. A run is done when the process exits and the envelope has a `status` field. If the command fails, read `/tmp/agy.err`.

## Run a task

```bash
agy -p "<request>" \
  --output-format json \
  --model gemini-3.8-flash \
  --effort high \
  --print-timeout 10m \
  --dangerously-skip-permissions \
  </dev/null 2>/tmp/agy.err
```

Start the command in the target workspace. `--add-dir <path>` adds another folder to that workspace.

`</dev/null>` keeps piped stdin from being appended to the `-p` prompt. For a long request, write it to a file and pass `< file` in place of `-p "<request>"` and `</dev/null` — inline quoting breaks on quotes and newlines.

`--model gemini-3.8-flash` requires `--effort` (`low`, `medium`, or `high`). Default is `medium`. Set both on every run.

`--dangerously-skip-permissions` auto-approves tools in headless mode. Without it, shell, URL, and MCP calls are auto-denied.

## Read the result

From the stdout JSON:

- `status` — the run produced an answer when this is `SUCCESS`
- `conversation_id` — use this with `--conversation` to continue that thread
- `response` — the reply

The exit code is `0` for both `SUCCESS` and `CANCELED`. Read `status`.

## Continue a conversation

`-c` continues the most recent conversation in that working directory. `--conversation <conversation_id>` continues a specific one. Keep model and effort unless the caller picks different values.

```bash
agy -p "<follow-up>" \
  -c \
  --output-format json \
  --model gemini-3.8-flash \
  --effort high \
  --print-timeout 10m \
  --dangerously-skip-permissions \
  </dev/null 2>/tmp/agy.err
```

```bash
agy -p "<follow-up>" \
  --conversation <conversation_id> \
  --output-format json \
  --model gemini-3.8-flash \
  --effort high \
  --print-timeout 10m \
  --dangerously-skip-permissions \
  </dev/null 2>/tmp/agy.err
```

## Command surface

`agy --help`.
