# Plan stage

A plan is the implementation contract distilled from the idea or issue. It is read by an implementer with zero context and unknown taste but solid technical ability, so everything that matters is spelled out in the plan itself: style, structure, choices. The plan is self-contained: any background or decision the implementation needs is folded into it, so implementation decisions come from the plan alone.

## Elements

- **Goal**: what to do and what done looks like. Where the scope is easily confused, state what is left out.
- **Steps**: the implementation steps, at whatever granularity fits. Each step has a clear output; decisions and pitfalls live inside the steps. Code stays out of the plan unless the code itself is the decision, such as an agreed interface signature.
- **Verification**: how to tell the work is done: commands, tests, observable behavior.
- **Constraints** (optional): technical constraints such as dependencies, versions, and compatibility, and topic-specific style constraints.

Write at the level of decisions, not mechanisms. Pin down whatever changes behavior, interfaces, boundaries, or acceptance criteria; leave the fine-grained mechanics to the implementer. The plan carries no open decisions: if one remains, the plan is not ready, so settle it first.

## Writing

The plan is written in clear, clean English: no waste, no repetition, and the logic fully explicit. The elements above are functional requirements, not a fixed template; structure follows content. A plan can take shape over several passes.
