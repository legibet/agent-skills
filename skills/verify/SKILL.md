---
name: verify
description: Use before delivering a code change, before adding or changing project tests, or when asked what to test.
---

# Verify

Verify the change before delivering it. Add project tests only when the user asks.

## Verifying a change

- Verify the changed behavior or interface with a one-off e2e test: a command, a scratch script, or a browser check. (When available, prefer delegating a complex, multi-step e2e test (such as a browser flow) to a subagent that reports each check as pass or fail with evidence.)
- Run the project's existing checks and test suite.

## Project tests

A test is worth adding only when it can catch a real regression.

- Test behavior callers depend on, through the contract they see: rules, boundaries, edge cases, fixed bugs.
- Do not add tests that restate the implementation or pin constants and literals.

Before delivering, decide whether this change warrants a regression test. If so, propose it at the end of the delivery, naming the regression it would catch. Add it only after the user agrees. If not, say nothing about tests.
