---
name: antigravity-cli
description: Delegate work to Antigravity CLI (agy) as an independent agent, or continue an existing agy conversation.
---

# Antigravity CLI

`agy -p` runs one headless turn. The working directory is the agent's workspace; agy has no flag to set it, so `cd` into it in the same command. The final JSON envelope goes to stdout and the turn log to stderr; send both to per-run files.

## Run a task

```bash
cd <workspace> && agy -p "<request>" \
  --model gemini-3.8-flash --effort high \
  --output-format json \
  --print-timeout 30m \
  --dangerously-skip-permissions \
  </dev/null >/tmp/agy-<run>.json 2>/tmp/agy-<run>.err
```

- Start it as a background command. A run takes minutes, longer than a foreground shell call waits. The run is done when the process exits.
- `<run>` is a name unique to this run, so parallel runs keep separate files.
- `--model gemini-3.8-flash` requires `--effort` (`low`, `medium`, or `high`).
- `--dangerously-skip-permissions` lets the agent run shell, URL, and MCP tools. Without it headless mode denies them.
- `--add-dir <path>` adds another folder to the workspace.
- For a long or multi-line request, write it to a file and pass `< <file>` in place of both `-p "<request>"` and `</dev/null`. Inline quoting breaks on quotes and newlines.

## Read the result

Read `/tmp/agy-<run>.json`. Check in this order:

1. `status` is `ERROR` (exit code 1): the run never started. `error` names the cause, usually a bad flag.
2. `/tmp/agy-<run>.err` contains `print timeout`: the turn hit `--print-timeout`. `status` is still `SUCCESS`, but `response` is partial or empty. Continue the conversation to let it finish.
3. `denied_actions` is present: tool calls were blocked, and the work they needed is missing.
4. Otherwise `response` is the reply. When the agent waited on a background command, `response` opens with its progress notes; the result comes last.

Keep `conversation_id` to continue the thread.

## Continue a conversation

```bash
cd <workspace> && agy -p "<follow-up>" \
  --conversation <conversation_id> \
  --model gemini-3.8-flash --effort high \
  --output-format json \
  --print-timeout 30m \
  --dangerously-skip-permissions \
  </dev/null >/tmp/agy-<run>.json 2>/tmp/agy-<run>.err
```

An unknown ID starts a new, empty conversation and still reports `SUCCESS`. Confirm the returned `conversation_id` matches the one you passed.

`-c` in place of `--conversation <id>` continues the most recent conversation in the working directory.

Run `agy --help` for the full command surface and `agy models` for other models.
