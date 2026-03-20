# .github/copilot_context.md (SKELETON) - Copilot Context Pack for ADLC Data Engineering (Template)

## Purpose
Single “context pack” to pin in GitHub Copilot Chat/Agent for this repo.
Goal: make Copilot outputs consistent, dbt-compatible, and aligned with ADLC practices.

## How To Use this Template
Goal: create a single pinned “context pack” that makes Copilot consistent across your repo.

1) Copy this skeleton to: `.github/copilot_context.md`
2) Replace placeholders:
   - warehouse + dbt version, banned keys, syntax rules (e.g., schema.yml arguments)
   - repo-specific canonical files and golden examples (paths)
3) Decide what developers should pin by default:
   - this file + which folders (`inputs/`, `templates/`, `models/`) if supported
4) Add your repo’s “must-follow” rules:
   - no hallucinated columns, staging minimal transforms, YAML syntax requirements
5) Include one approved “Planner/Agent skeleton prompt” that your team will reuse verbatim.

### Efficiently with Copilot
- Ask Copilot to populate the canonical file pointers automatically:
  - “Scan the repo structure and fill the ‘Canonical files’ section with real paths.”
- Keep it stable:
  - treat this file like a contract; changes should be reviewed in PRs.
- Use it in every exercise:
  - “Use #file:.github/copilot_context.md as your primary context.”

## How to use copilot_context with GitHub Copilot
- In Copilot Chat, reference: `#file:.github/copilot_context.md`
- Optionally add: `#folder:inputs #folder:templates #folder:models` (if your Copilot supports folder context)
- For Planner/Agent mode: always request “plan first, then write files”.

## Repo ground rules (must follow)
- Target stack: `<warehouse> + dbt <version>`
- No secrets in chat or repo.
- No hallucinated columns: only from:
  - `inputs/*` (exercise inputs)
  - `models/staging/sources.yml` (source contracts)
  - existing models/schema.yml
- Staging models: minimal transforms only (no joins/aggregations/business filters/dedupe).
- Business logic + dedupe: intermediate/marts.
- schema.yml tests: use syntax required by this dbt version (e.g., `arguments:`).
- sources.yml: avoid unsupported keys for this repo (e.g., `<loaded_at_field?>` = `<allowed/forbidden>`).

## Canonical files (prefer these)
- Standards: `CONVENTIONS.md`
- dbt config: `dbt_project.yml`, `packages.yml`
- Source contracts: `models/staging/sources.yml`
- YAML style: `models/staging/schema.yml`, `models/marts/schema.yml`
- Golden examples:
  - staging: `models/staging/<golden_stg_model>.sql`
  - marts: `models/marts/<golden_mart_model>.sql`

## Layering model (expected)
- staging: `models/staging/stg_<source>__<entity>.sql`
- intermediate: `models/intermediate/int_<domain>__<topic>.sql`
- marts: `models/marts/fct_<process>.sql`, `models/marts/dim_<entity>.sql`

## Definition of Done (DOD) — minimum
A model PR is “reviewable” only when:
- Grain explicitly stated
- schema.yml includes:
  - model description + key column descriptions
  - baseline tests appropriate to layer
- Validation evidence provided:
  - `dbt deps`
  - `dbt parse`
  - (optional) `dbt run/test` when DB access exists

## Planner/Agent operating mode — prompt skeleton
```text
You are the Planner agent.

Inputs (repo files/folders):
- <list exact files or reference #file/#folder>

Task:
1) Produce a short todo plan.
2) Create/update these artifacts at these exact paths:
   - <path 1>
   - <path 2>

Rules:
- Do not invent columns beyond sources.yml / provided data model.
- Follow CONVENTIONS.md and golden examples.
- Use schema.yml test syntax required by this repo (e.g., arguments:).
- List uncertainties as “Open Questions” with priority.

Now: plan first, then write the files.
```

## Confidence scoring (for STM-style tasks)
- Confidence is “AI draft confidence” (H/M/L), not a probability.
- High: direct mapping; Medium: assumption required; Low: ambiguous/missing info.
- All Medium/Low must include “Why” and appear in “Unclear mappings / Questions”.

## Common pitfalls to avoid
- Adding unsupported keys to dbt_project.yml
- Using deprecated schema.yml test format
- Doing dedupe or business filters in staging
- Creating extra files not requested
- Changing existing conventions without explicit instruction

## Quick offline validation
- `dbt deps`
- `dbt parse`
If parse fails: fix YAML structure/syntax first.