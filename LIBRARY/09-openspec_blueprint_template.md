# File: templates/openspec_blueprint_template.md
# Template: OpenSpec — executable blueprint for dbt modular implementation (Task D)

## Purpose
An OpenSpec-style blueprint that turns a validated model into a build-ready plan:
- stg/int/marts decomposition
- responsibilities + grain per model
- tests plan + parity/audit plan
- implementation checklist (files + validation commands)

## When to use
- Any modernization from legacy SQL to dbt
- Any new data product where you want Build to be execution, not discovery
- High-risk domains where traceability and verification are required

## Inputs
- Logical model (Task B)
- Gaps/dependencies/risks (Task C)
- Legacy SQL or prior baseline (for parity target)

## Outputs (recommended)
- `03-specs/<use_case>_openspec_blueprint.md`
- `planning/<use_case>_build_plan_checklist.md`

## How to use (human steps)
1) Confirm blockers are resolved or explicitly tracked
2) Write/approve OpenSpec blueprint (HITL gate)
3) Execute blueprint in Build (human or Copilot agent)
4) Verify using parity harness before declaring success

## OpenSpec structure (template)

### 1) Target data product
- Name:
- Purpose/consumers:
- Grain (explicit):
- PK/FKs:
- Output columns (ordered):
- Definitions (brief):

### 2) Inputs / source contracts
- Sources used:
- Required keys and constraints:
- Enum expectations:
- Dedupe policy inputs (ordering field, late arriving rules):

### 3) Layer plan (dbt decomposition)
#### Staging models (minimal transforms only)
For each staging model:
- File path:
- Input source:
- Grain:
- Transform rules (casts, normalization only):
- Notes:

#### Intermediate models (rules + dedupe)
For each intermediate model:
- File path:
- Input ref(s):
- Grain:
- Business rules:
- Dedupe strategy:
- Derived fields:

#### Marts models (facts/dims)
For each marts model:
- File path:
- Input ref(s):
- Grain:
- Output columns:
- Notes on stability/versioning:

### 4) Tests plan
Baseline (must-have):
- unique/not_null where appropriate
- accepted_values for stable enums
- relationships where stable targets exist
Critical custom checks (risk-driven):
- duplication checks
- sanity thresholds (rates, totals)
- audit models (optional)

### 5) Verification plan (parity/audit)
- Parity checks vs legacy baseline:
  - rowcount
  - group counts (e.g., by status)
  - aggregates (e.g., sum(refund_amount) by day)
  - anti-joins on keys
- Acceptance criteria for “match”:
  - thresholds or exact match rules

### 6) Open questions & decisions log
- Carry forward unresolved items from Task C
- Record decisions and rationale for major modeling choices

### 7) Implementation checklist
- Files to create/update:
- Commands to run:
  - dbt deps
  - dbt parse
  - (later) dbt run/test selections
- Review evidence to include in PR:

## DOs
- DO keep staging minimal; put rules/dedupe in intermediate
- DO define verification upfront (parity harness) before Build
- DO specify grain and ownership per model
- DO carry open questions explicitly with owners

## DON’Ts
- DON’T treat OpenSpec as “documentation only”—it must be executable
- DON’T allow silent grain changes without stating them
- DON’T add relationships/tests without stable key targets