---
name: hr
description: "Herdr agent coordination — spawn a coding agent, prompt an agent already in a pane, fan out parallel work, chain agent dependencies, unblock a blocked agent, inspect state, or run background commands through Herdr. Prefer built-in subagents for simple delegation; use Herdr when different agent kinds, user-observable panes, or persistent sessions are needed. Requires HERDR_ENV=1."
---

# hr

Guard: `test "${HERDR_ENV:-}" = 1`. If it fails, say so and stop.

A **pane** is a terminal location; an **agent** is the recognized coding-agent process inside it. Panes run ordinary commands (shells, tests, servers). Agents get lifecycle state that tells you when to act.

Never run bare `herdr` — it launches the TUI. This file is the protocol. Run `herdr agent` or `herdr pane` when you need a flag, kind, or option that is not listed here.

## Target

Agent commands take a **target**: a live **name**, or the **pane ID** that currently hosts the agent. Names match `[a-z][a-z0-9_-]{0,31}`, are unique among live agents, and are cleared on exit. Copy those values from JSON the CLI returns. Do not invent a pane ID or name.

`herdr agent list` puts kind in `.agent` (`opencode`, `pi`, `claude`, …). Use `.agent` to choose the row. The target you pass is `.name` when that field is present, otherwise `.pane_id`. `.agent` is kind, not a target.

A name exists only after `herdr agent start <name>` or `herdr agent rename`. Agents the user launched in a pane have no `.name` field. Address those by `.pane_id`.

```json
{
  "agent": "opencode",
  "agent_status": "idle",
  "pane_id": "w1:p2"
}
```

This row has no `name`. The target is `w1:p2`. Kind `opencode` is how you recognized the row.

If this conversation already has a live target for that agent, use it.

Otherwise list, pick the row, and copy the target from that JSON:

```bash
herdr agent list
```

Done when the next agent command is given a `.name` or `.pane_id` copied from the list. If no row matches and the user asked to create one, go to spawn.

## Scenes

Each heading is one job.

### spawn

Create pane → start agent → dispatch task → collect result.

Inspect the caller layout first. Split a wide pane to the right. Split a narrow or tall pane down. Alternate direction on each new split so columns stay usable.

```bash
herdr pane layout --pane "$HERDR_PANE_ID"
```

```bash
pane=$(herdr pane split --current --direction right --cwd "$PWD" --no-focus | jq -r '.result.pane.pane_id')
herdr agent start <name> --kind <kind> --pane "$pane"
herdr agent prompt <name> "<task>" --wait --timeout 120000
herdr agent read <name> --source recent-unwrapped --lines 120
```

Done when read returns the response and state is idle or done. If blocked, switch to unblock.

### prompt

Talk to an agent that is already in a pane. Use the target obtained under Target.

```bash
herdr agent prompt <target> "<task>" --wait --timeout 120000
herdr agent read <target> --source recent-unwrapped --lines 120
```

Done when read returns the response and state is idle or done. If blocked, switch to unblock.

If you will send many more prompts to an unnamed agent, you may `herdr agent rename <target> <name>` and use that name afterward.

### inspect

Read state and output without acting.

```bash
herdr agent get <target>
herdr agent read <target> --source recent-unwrapped --lines 80
```

### unblock

Agent is `blocked` — needs input, approval, or a decision. Read first:

```bash
herdr agent read <target> --source recent-unwrapped --lines 80
```

If the answer is clear and safe, send keys: `herdr agent send-keys <target> <key>`.
If it needs human judgment, report the blocker and wait.

### fan-out

Independent slices in parallel. Create all panes and agents, dispatch in parallel, collect each. Use the same split-direction rule as spawn.

```bash
p1=$(herdr pane split --current --direction right --cwd "$PWD" --no-focus | jq -r '.result.pane.pane_id')
p2=$(herdr pane split --pane "$p1" --direction down --cwd "$PWD" --no-focus | jq -r '.result.pane.pane_id')

herdr agent start <name1> --kind <kind> --pane "$p1"
herdr agent start <name2> --kind <kind> --pane "$p2"

herdr agent prompt <name1> "<task1>" --wait --timeout 300000 &
herdr agent prompt <name2> "<task2>" --wait --timeout 300000 &
wait

herdr agent read <name1> --source recent-unwrapped --lines 120
herdr agent read <name2> --source recent-unwrapped --lines 120
```

Give parallel agents editing the same repo separate git worktrees.

### pipeline

B needs A's output. A is already running — wait for it, then spawn B with the result embedded.

```bash
# wait for A (matches idle, done, or blocked)
herdr agent wait <target_a> --timeout 300000
result_a=$(herdr agent read <target_a> --source recent-unwrapped --lines 120)

# spawn B
pane_b=$(herdr pane split --current --direction right --cwd "$PWD" --no-focus | jq -r '.result.pane.pane_id')
herdr agent start <name_b> --kind <kind> --pane "$pane_b"
herdr agent prompt <name_b> "Based on this: $result_a — <task_b>" --wait --timeout 120000
herdr agent read <name_b> --source recent-unwrapped --lines 120
```

### background

Non-agent command (tests, build, server) in a side pane. Use the same split-direction rule as spawn.

```bash
pane=$(herdr pane split --current --direction right --cwd "$PWD" --no-focus | jq -r '.result.pane.pane_id')
herdr pane run "$pane" "<command>"
herdr pane wait-output "$pane" --match "<expected>" --timeout 120000
herdr pane read "$pane" --source recent-unwrapped --lines 120
```

### retire

Close panes you created when their work is done: `herdr pane close <pane-id>`.

---

## States

- `idle` — ready for input, tab seen.
- `working` — actively running.
- `blocked` — needs input, approval, or a decision. Your cue to act.
- `done` — finished in background, not yet viewed. Focusing the tab or targeting the pane turns it into `idle`.
- `unknown` — agent detected but state uncertain. Does not prove completion.

## Prompt and wait

`agent prompt --wait` submits text and waits for the first settled state (idle, done, or blocked). It tracks lifecycle state, not individual turns — if the agent is already working, the current turn's completion may satisfy it.

A prompt from idle must produce a lifecycle change within five seconds, or Herdr returns `agent_prompt_stalled`.

Use `--until` only for a specific state: `herdr agent wait <target> --until blocked`.

## Read sources

- `recent-unwrapped` — recent output with soft wraps joined. Default for transcripts and logs.
- `visible` — the current viewport.
- `recent` — recent rendered output including soft wraps.
- `detection` — plain-text snapshot used for agent detection.

`--lines N` requests more rows. Alternate-screen TUIs (opencode, claude) auto-scroll idle agents to collect history. If a full response still can't be captured, ask the agent to write it to `/tmp/<name>.md` and reply with the path.

## Safety

- `--no-focus` for background work; switch focus only when asked.
- Target by `--current`, explicit pane ID, or agent name — not the UI-focused pane, which may belong to another client.
- Preserve the caller's cwd: `--cwd "$PWD"`.
- Close only panes you created.
- Never run `herdr server stop` from an active session unless explicitly asked.
