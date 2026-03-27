# File: README.md
# ADLC Coaching Assets — Templates, Playbooks, and Workshop Progression

## Purpose
This repo contains reusable templates and playbooks to apply AI-assisted practices across the **full ADLC cycle**.
It is designed to be extended as the coaching program progresses (append new modules, exercises, and examples).

---

## Quick start (recommended workflow)
1) Bootstrap a consistent repo foundation (one-time)
2) Pin the Copilot context pack (daily)
3) Apply templates by ADLC phase:
   - Requirements → Spec
   - Plan → STM / model planning
   - Plan → OpenSpec blueprint (for modernization/model preparation)
   - Build → execute blueprint into dbt artifacts
   - Verify → parity/audit checks
   - Deploy/Operate/Observe → CI, runbooks, metrics feedback loops

---

## Repository foundations (one-time setup)
These templates establish the baseline repo structure and standards:

- **Foundation bootstrap playbook (template)**
  - `.github/PLAYBOOKS/FOUNDATION_BOOTSTRAP.md`  
  - Purpose: generate dbt project config, packages, golden examples, sources contracts, templates folders, PR scaffolding.

- **Copilot context pack (template)**
  - `.github/copilot_context.md`  
  - Purpose: the single pinned “truth pack” so Copilot outputs are consistent and safe.

- **Engineering conventions (template)**
  - `CONVENTIONS.md`  
  - Purpose: naming, layering, SQL style, schema.yml tests/docs policy, DOD, review and validation expectations.

> Coaching note: treat these as “standards-as-code”. Changes should be reviewed and versioned.

---

## Templates by ADLC phase

### Learn / Requirement Analysis
Goal: convert free text into a spec + open questions to prevent rework.
- `templates/requirements_spec_playbook.md`
- (supporting template) `templates/spec_template.md`
Outputs:
- `specs/<use_case>_spec.md`

### Plan
Goal: turn spec into an explicit mapping and an execution-ready plan.
- `templates/stm_generation_playbook.md`
- (supporting template) `templates/stm_template.md`
Outputs:
- `planning/<use_case>_stm.md`
- `planning/<use_case>_plan_checklist.md`

### Plan (advanced) — Data model preparation / modernization planning
Goal: reverse engineer legacy SQL into a model and an OpenSpec blueprint.
- `templates/legacy_sql_reverse_engineering_playbook.md`
- `templates/logical_data_model_template.md`
- `templates/completeness_validation_checklist.md`
- `templates/openspec_blueprint_template.md`
Outputs:
- `planning/taskA_*.md`, `planning/taskB_*.md`, `planning/taskC_*.md`
- `03-specs/<use_case>_openspec_blueprint.md`
- `planning/<use_case>_build_plan_checklist.md`

### Build
Goal: execute the plan/blueprint into dbt code artifacts.
- `templates/spec_to_dbt_skeleton_playbook.md`
Outputs:
- `models/staging/*`, `models/intermediate/*`, `models/marts/*`
- schema.yml updates + baseline tests

### Verify
Goal: prove correctness (especially modernization).
- Use parity/audit harness described in OpenSpec:
  - rowcounts, aggregates, anti-joins, regression checks
Outputs:
- `analyses/<use_case>_parity_checks.sql` (optional)
- test artifacts and run evidence in CI

### Deploy / Operate / Observe (to be appended)
This section will be extended as coaching progresses:
- CI/CD workflows for dbt
- promotion gates and release hygiene
- runbooks and troubleshooting
- quality and value metrics feedback loops (cycle time, defect leakage, cost)

---

## Folder conventions (recommended)
- `inputs/` — workshop/demo inputs (dictionaries, free-text requirements, samples)
- `templates/` — reusable templates and playbooks
- `specs/` — requirement specs (Learn/Analysis)
- `planning/` — STM, gap lists, checklists (Plan)
- `03-specs/` — OpenSpec blueprints (Plan → Build bridge)
- `analyses/` — legacy SQL baselines and parity queries
- `models/` — dbt models (staging/intermediate/marts)

---

## How to extend this README (coaching progression)
Append new modules as you deliver them:
- Module name + ADLC phase focus
- templates introduced
- artifacts produced
- KPIs measured
- homework assigned
- lessons learned / pitfalls

---

## Suggested “standard flow” for teams
1) Pin `.github/copilot_context.md`
2) Generate spec (requirements → `specs/`)
3) Generate STM (plan → `planning/`)
4) If legacy modernization: run A–D to produce OpenSpec (`03-specs/`)
5) Execute Build via checklist into dbt models + schema.yml
6) Verify with parity harness + tests
7) Feed learnings back into conventions/context/templates