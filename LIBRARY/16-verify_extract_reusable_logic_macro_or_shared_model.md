# File: 16-verify_extract_reusable_logic_macro_or_shared_model.md
# Template 16 — Verify: Remove redundancy by extracting reusable logic

## Purpose
Eliminate repeated transformations and reduce drift by extracting one reusable component:
- macro (preferred) OR
- small shared intermediate model

Update one consumer to prove the pattern.

## When to use
- The same CASE/normalization logic appears in multiple models
- Fixes are applied inconsistently across the repo
- You want a single source of truth for mappings/standardization

## Inputs to pin
- `#file:.github/copilot_context.md`
- `#file:CONVENTIONS.md`
- `#file:planning/<use_case>_refactor_plan.md` (from Template 14)
- `#file:<SOURCE_MODEL_1.sql>`
- `#file:<SOURCE_MODEL_2.sql>` (optional additional consumer)

## Output artifacts
- Macro: `macros/<component_name>.sql`  
  and/or
- Shared model: `models/intermediate/<shared_model>.sql`
- Consumer update (one file): `<TARGET_MODEL>_v2.sql`
- Optional short doc: `macros/README_<component>.md`

## How to use (steps)
1) Choose ONE redundancy pattern (SAFE or MODERATE) from Template 14 plan.
2) Extract into a macro/shared model.
3) Update exactly one consumer in this PR.
4) Validate offline (parse); parity checks if risk is MODERATE.

## Prompt — Extract and update one consumer
```text
From planning/<use_case>_refactor_plan.md, choose ONE repeated pattern (SAFE or MODERATE only):
- e.g., normalize_currency / normalize_channel / normalize_country / status_mapping

Implement it as:
- a dbt macro (preferred) OR a small shared intermediate model if macro is not appropriate.

Create:
- macros/<component_name>.sql   (if macro)
OR
- models/intermediate/<shared_model>.sql (if shared model)

Then update exactly ONE consumer model to use it:
- <TARGET_MODEL.sql>

Constraints:
- Preserve semantics (including null/default behavior).
- Output full file contents for created/updated files (SQL only).
- Do not overwrite existing files; use _v2 variants where needed.
```

## Prompt — Document the reusable component
```text
Create a short README for the extracted component:
- what it does
- inputs/outputs
- usage example
- do/don’t
- risks (null semantics, enum coverage)

Output file:
- macros/README_<component_name>.md
```

## DOs
- DO keep component scope tight (single responsibility)
- DO explicitly preserve null semantics and mapping behavior
- DO update only one consumer per PR to limit blast radius

## DON’Ts
- DON’T extract multiple patterns in one PR
- DON’T change mapping behavior unless explicitly approved and tested