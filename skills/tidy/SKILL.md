---
name: tidy
description: Simplify and tidy existing code for readability. Use when asked to clean up, simplify, refactor for clarity, remove indirection, judge whether a helper or abstraction earns its place, or sanity-check whether recent code over-engineered things.
---

# Tidy

Make code direct and quick to read: less needless abstraction, less indirection, less redundancy, fewer structures that don't earn their place. Behavior preserved.

Clarity is the target, not line count. A clearer version is sometimes longer. Prefer the obvious over the ingenious. Bias toward removing structure that doesn't pay for itself, but don't over-simplify: deleting a helpful seam or crushing logic into a dense expression to shrink it is just as bad.

## Workflow

Scope is the recently modified files, or whatever range the user names.

Small or localized (one function or file):

1. Read the target plus its callers and the producers of the values it handles.
2. If anything touches behavior or an interface contract, say so and align first.
3. Make the focused change, then run the checks and tests.

Large (a package-wide pass):

1. Survey: read broadly (fan out with subagents if available); collect concrete findings, each with its cost.
2. Plan and confirm: group into phases, flag the behavior-touching ones, and agree scope and risk before editing.
3. Phase by phase: one commit per phase, each passing the verification gate before the next. If a finding turns out to need added abstraction or a behavior change, skip it and record why.

When scope or a tradeoff is unclear, discuss before doing.

## Confirm, don't guess

Two failure modes ruin a cleanup: silently changing behavior, and reworking something the user didn't want touched.

- Before any edit that could alter behavior or an interface contract, read the relevant code (who produces this value? what type is guaranteed? is this guard reachable?) and check the tests. Verifying is cheap; a silent regression is expensive.
- For anything non-trivial or behavior-affecting, surface the approach and the tradeoff, and align before editing.
- Anything touching behavior, a wire/format contract, or a public API is confirm-first. Don't fold it into a routine batch.

## What earns removal

Behavior-preserving unless flagged:

- Dead defensiveness: guards, fallbacks, coercions, or validation for states that can't occur. Read the producer to confirm the state is truly impossible, then delete.
- Low-altitude round-trips: data serialized then re-parsed, or typed to dict to typed. Carry the typed value through.
- Derivable state: a field that always equals an expression of other state. Compute it at the point of use.
- Deep nesting: flatten with early returns; collapse near-identical branches.
- Dead code: unused parameters, unreachable branches, comments describing code that no longer exists, unused backwards-compat shims, and "just in case" configurability. If nobody uses it, delete it.

## When a helper earns its place

Default to inline. A helper called three times or fewer is not worth extracting by call count alone. "Used twice, so DRY it" is the wrong test; a few short inline copies often read better than one indirection.

Keep or introduce a helper only when it earns itself on readability, not DRY:

- Non-trivial correctness: the logic encodes a rule that inline copies could get quietly wrong, or that multiple call sites must stay in sync on (an error shape, a wire format, a business rule). One home prevents drift.
- Large construction: it assembles something with many fields, so each call site shows only what differs.
- Clearer interface: extracting a cohesive sub-step lets a long method read as a short sequence of named operations.

Litmus: does the call site get faster to understand, and would inlining risk the copies drifting? If not both, inline.

Keep genuinely different paths separate. Unifying them behind a strict/lenient flag or mode parameter hides a real difference behind new abstraction.

## Verify and report

Behavior-preserving means verified, not hoped. Run the project's lint, type-check, and tests after each change or phase, and keep them green. For a refactor you call equivalent, prove the invariant by reading who produces the value and what shape is guaranteed.

Report: lead with what changed and why it's clearer; then what you confirmed (behavior preserved, checks green); then what you deliberately skipped and why. The skip list shows the bar was applied, and it's where the user catches a disagreement early.
