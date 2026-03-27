# File: 17-verify_performance_optimizations_with_parity_harness.md
# Template 17 — Verify: Performance optimizations + verification harness

## Purpose
Propose and implement performance improvements safely by coupling each change to a verification harness:
- targeted optimization suggestions (Snowflake context)
- parity checks to prove no semantic drift
- staged rollout: one change at a time

## When to use
- Query runtimes or warehouse costs are growing
- Models scan too much data or contain redundant joins
- Dedupe logic is expensive or hard to reason about
- You want confidence to merge optimizations quickly

## Inputs to pin
- `#file:.github/copilot_context.md`
- `#file:CONVENTIONS.md`
- `#file:planning/<use_case>_refactor_plan.md` (from Template 14)
- `#file:<TARGET_MODEL.sql>` (intermediate or mart)
- Optional baseline/legacy query:
  - `#file:analyses/<baseline_query>.sql`

## Output artifacts
- Optimization plan: `planning/<use_case>_perf_plan.md`
- Parity harness SQL: `analyses/<use_case>_parity_checks.sql`
- Versioned optimized model: `<TARGET_MODEL>_v2.sql`

## How to use (steps)
1) Ask Copilot for 2–3 optimizations with risk tags.
2) Pick ONE optimization (SAFE/MODERATE).
3) Generate parity harness (rowcount + aggregates + anti-join).
4) Implement optimization in a _v2 file and run/attach results when possible.

## Prompt — Propose optimizations with risk tags
```text
Review <TARGET_MODEL.sql> for Snowflake/dbt performance opportunities.

Task:
- Propose 2–3 optimizations with expected benefit (scan reduction, fewer joins, less DISTINCT, earlier filters).
- Tag each SAFE/MODERATE/RISKY with rationale and any semantic risk.
- Recommend which ONE change to implement first.

Output file:
- planning/<use_case>_perf_plan.md
```

## Prompt — Generate parity harness (before vs after)
```text
Generate a parity verification harness for <TARGET_MODEL.sql> vs <TARGET_MODEL>_v2.sql.

Include:
1) rowcount comparison
2) count distinct primary key comparison (if applicable)
3) 1–2 aggregate comparisons by date (or another stable dimension)
4) anti-join on primary key (missing keys on either side)

Output file:
- analyses/<use_case>_parity_checks.sql

Constraints:
- SQL only.
- Add clear comments for what to replace if names differ.
```

## Prompt — Implement one optimization (minimal diff)
```text
Implement ONLY the first approved optimization from planning/<use_case>_perf_plan.md in:

- <TARGET_MODEL.sql>

Constraints:
- Minimal diff; do not refactor unrelated parts.
- Preserve semantics unless the plan explicitly states a semantic change.
- Output SQL only as a full file.

Non-overwrite:
- Write to: <TARGET_MODEL>_v2.sql
```

## DOs
- DO couple every perf change to parity checks
- DO implement one optimization at a time
- DO record assumptions and expected benefit in PR notes

## DON’Ts
- DON’T merge perf refactors without a verification harness
- DON’T do “big bang” rewrites; they are hard to validate