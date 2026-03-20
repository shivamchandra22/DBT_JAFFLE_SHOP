# File: templates/spec_to_dbt_skeleton_playbook.md
# Template: Spec/STM → dbt Skeleton Artifacts (staging + schema.yml)

## Purpose
Produce “dbt-ish” outputs early by turning an approved spec and/or STM into:
- sources.yml updates (contracts)
- staging models (minimal transforms)
- schema.yml docs + baseline tests

This bridges Plan → Build.

## When to use
- After spec approval to start scaffolding safely
- After STM approval to generate consistent SQL/YAML artifacts
- In workshops where you need tangible dbt outputs without full DB access

## Inputs
- `specs/<use_case>_spec.md` and/or `planning/<use_case>_stm.md`
- `inputs/<use_case>_data_model.md`
- Repo standards:
  - `CONVENTIONS.md`
  - `.github/copilot_context.md`
- Existing contracts:
  - `models/staging/sources.yml`
  - `models/staging/schema.yml`
- Golden example:
  - `models/staging/<golden_stg_model>.sql`

## Outputs (recommended)
- Update: `models/staging/sources.yml` (add new table(s))
- Create: `models/staging/stg_raw__<entity>.sql`
- Update: `models/staging/schema.yml` (docs + tests)

## DOs
- DO keep staging minimal (casts/normalization only)
- DO use modern schema.yml test syntax required by repo (e.g., `arguments:`)
- DO ensure no hallucinated columns (use data model + sources.yml)
- DO add baseline tests appropriate to staging (not overly strict)

## DON’Ts
- DON’T implement business filters/dedupe in staging
- DON’T introduce unsupported keys in sources.yml (repo-specific)
- DON’T overwrite existing files without explicit permission

## Prompt skeleton (copy/paste)
```text
You are the Planner agent.

Inputs:
- specs/<use_case>_spec.md (if available)
- planning/<use_case>_stm.md (if available)
- inputs/<use_case>_data_model.md
- CONVENTIONS.md
- .github/copilot_context.md
- models/staging/sources.yml
- models/staging/schema.yml
- models/staging/<golden_stg_model>.sql

Task:
1) Produce a short todo plan.
2) Update sources.yml to include new source tables/columns (no unsupported keys).
3) Create staging model(s) with minimal transforms only.
4) Update staging schema.yml with docs + baseline tests (modern syntax).

Now: plan first, then write/modify the files.
```