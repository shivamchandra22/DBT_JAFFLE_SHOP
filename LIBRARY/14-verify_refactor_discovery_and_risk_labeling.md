# File: 14-verify_refactor_discovery_and_risk_labeling.md
# Template 14 — Verify: Refactor candidate discovery & risk labeling (SAFE/MODERATE/RISKY)

## Purpose
Produce a short, evidence-based **refactor plan** before editing code:
- identify redundancies and readability issues
- surface performance smells (Snowflake/dbt context)
- label semantic risk (grain, join cardinality, null semantics)
- decide what is in-scope vs out-of-scope for this PR

## When to use
- Any “cleanup/refactor” PR
- Before optimizing SQL for performance
- Before extracting shared logic (macros/models)
- When onboarding into a complex model (understand risks first)

## Inputs to pin (minimum)
- `#file:.github/copilot_context.md`
- `#file:CONVENTIONS.md`
- Target model(s) to analyze:
  - `#file:<PATH_TO_MODEL_1.sql>`
  - `#file:<PATH_TO_MODEL_2.sql>` (optional)
- Optional evidence (recommended if available):
  - `#file:inputs/<profiling_summary>.md`
  - `#file:analyses/<legacy_or_baseline_query>.sql`
  - `#file:planning/<grain_or_model_notes>.md`

## Output artifacts (recommended)
- `planning/<use_case>_refactor_plan.md`
- (optional) `planning/<use_case>_refactor_scope.md` (what’s allowed in PR)

## How to use (steps)
1) Pin the context + target model(s).
2) Run the “Refactor plan” prompt below.
3) HITL gate: select changes allowed in this PR (usually SAFE-only or SAFE+1 MODERATE).
4) Only then proceed to Tasks 15–17.

## Prompt — Refactor plan + risk tags
```text
You are acting as a Verify-phase refactoring reviewer.

Inputs:
- CONVENTIONS.md
- .github/copilot_context.md
- <MODEL PATH 1>
- <MODEL PATH 2> (optional)
- <PROFILING SUMMARY PATH> (optional)

Task:
1) Produce a short todo plan.
2) Create a refactor plan with 4 sections:
   A) Redundancy opportunities (ranked)
   B) Readability improvements (ranked)
   C) Performance smells (ranked; Snowflake-specific where relevant)
   D) Semantic risk flags + DO-NOT-CHANGE list (grain, join expansion, null semantics)
3) For each proposed change, tag it SAFE / MODERATE / RISKY and add a one-line rationale.

Output file:
- planning/<use_case>_refactor_plan.md

Rules:
- Do not propose semantic changes unless clearly flagged RISKY.
- If you cannot prove a claim from the model text, say UNKNOWN and suggest how to verify (e.g., parity check).
Now: plan first, then write the file.
```

## DOs
- DO label grain and cardinality risk explicitly (this prevents drift)
- DO propose small, reviewable diffs
- DO separate style/readability from logic/performance changes
- DO create a DO-NOT-CHANGE list for the PR

## DON’Ts
- DON’T start refactoring without a scoped plan
- DON’T assume join cardinality is 1:1 without proof
- DON’T “optimize” by changing semantics unless the PR explicitly targets it