# File: 15-verify_readability_refactor_no_semantic_change.md
# Template 15 — Verify: Readability refactor with zero semantic change

## Purpose
Improve readability and reviewability **without changing results**:
- intent-based CTE naming
- clearer structure and comments
- stable column ordering (keys → dates → measures → attributes)
- remove trivial redundancy (SAFE only)

## When to use
- You need to make a model easier to maintain
- Reviewers struggle with the current SQL structure
- You want a “safe refactor” PR with minimal risk

## Inputs to pin
- `#file:.github/copilot_context.md`
- `#file:CONVENTIONS.md`
- `#file:planning/<use_case>_refactor_plan.md` (from Template 14)
- `#file:<TARGET_MODEL.sql>`

## Output artifacts
- A versioned refactor file (recommended):
  - `<TARGET_MODEL>_v2.sql`
- Optional PR note:
  - `planning/<use_case>_readability_refactor_note.md`

## How to use (steps)
1) Confirm from Template 14 which items are SAFE.
2) Apply only SAFE readability changes to one file.
3) Validate offline (`dbt deps`, `dbt parse`).
4) Provide a short verification note for PR.

## Prompt — SAFE-only readability refactor
```text
Apply ONLY SAFE readability refactors from planning/<use_case>_refactor_plan.md to:

- <TARGET_MODEL.sql>

Constraints:
- No semantic changes: do not change joins, filters, dedupe logic, or null handling.
- Improve only naming, comments, structure, and column ordering.
- Remove SELECT * only if you can explicitly enumerate columns without changing output.
- Output SQL only as a full file.

Non-overwrite:
- Do not overwrite the original.
- Write to: <TARGET_MODEL>_v2.sql
```

## Prompt — Verification note (PR-ready)
```text
Write a short verification note explaining:
- What changed (readability only)
- Why semantics are unchanged
- What gate was run (dbt parse) and the result

Output file:
- planning/<use_case>_readability_refactor_note.md
```

## DOs
- DO refactor one file per PR when possible
- DO keep diffs bounded and reviewable
- DO add explicit grain comment if missing (as documentation only)

## DON’Ts
- DON’T move business rules into staging under “readability”
- DON’T combine readability and performance changes in one PR