# File: templates/build_dq_completeness_playbook.md
# Template: Build — DQ Completeness (schema.yml + assertion pattern)

## Purpose
Standardize baseline data quality controls in Build by ensuring:
- schema.yml documentation completeness (grain, key fields)
- baseline tests (unique/not_null/accepted_values where appropriate)
- reusable assertion/audit checks for anomaly rules

## When to use
- Every new mart model
- Any model touched by a PR where tests/docs are missing
- Before adding “run/test” to CI (to ensure tests exist)
- After source drift or business rule changes

## Inputs (pin these)
- `.github/copilot_context.md`
- `CONVENTIONS.md`
- Target model SQL: `models/<layer>/<model>.sql`
- Target schema.yml: `models/<layer>/schema.yml`
- Optional: `models/tests/assertions/README.md` (pattern guide)

## Outputs
- Updated schema.yml block (docs + tests)
- New assertion model SQL (optional)
- dbt parse evidence

## How to use (human steps)
1) Select a target model (prefer marts).
2) Upgrade schema.yml: add grain, key docs, baseline tests.
3) Add one assertion model for a meaningful anomaly check (optional).
4) Validate with `dbt parse`.

## Copilot prompts
### 1) Upgrade schema.yml block
```text
Upgrade schema.yml for <MODEL> to meet standards.

Requirements:
- model description includes explicit grain
- key column descriptions
- baseline tests appropriate to the layer
- use repo-required test syntax (e.g., arguments:)

Output YAML only (model block).
```

### 2) Create assertion model (audit query)
```text
Create an assertion model that returns violating rows for one rule:
- <RULE>

Target path:
- models/tests/assertions/assert_<model>__<rule>.sql

Constraints:
- SQL only
- include key + offending values + reason
```

## DOs
- DO avoid over-strict tests that create false failures
- DO document grain and key columns
- DO keep assertions readable and reviewable

## DON’Ts
- DON’T treat DQ as optional; enforce as part of DOD
- DON’T put business logic into staging under “DQ”