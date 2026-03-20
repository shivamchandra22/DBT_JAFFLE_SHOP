# File: templates/stm_generation_playbook.md
# Template: Plan → Source-to-Target Mapping (STM) + Consistency Checks

## Purpose
Generate a **complete Source-to-Target Mapping** that makes transformations explicit and reviewable:
- every target field mapped or flagged as unmappable
- consistency checks (types/enums/grain risk)
- confidence scoring (H/M/L)
- derived implementation checklist

## When to use
- Planning dbt build from a spec
- Modernization from legacy SQL (mapping to new model)
- Any new dataset interface where schema drift and ambiguity must be managed
- Before writing intermediate/marts models

## Inputs
- Approved spec: `specs/<use_case>_spec.md`
- Data model/dictionary: `inputs/<use_case>_data_model.md`
- Sample CSV(s): `inputs/<use_case>_sample.csv` (recommended)
- Standards/context:
  - `CONVENTIONS.md`
  - `.github/copilot_context.md`

## Outputs (recommended)
- `planning/<use_case>_stm.md`
- `planning/<use_case>_plan_checklist.md`

## How to use (human steps)
1) Ensure spec is approved (HITL gate from requirements phase).
2) Generate STM + checks with Planner.
3) HITL gate: review Medium/Low confidence mappings and resolve blockers.
4) Convert plan checklist into tickets/PR plan, then proceed to Build.

## How to use with Copilot (Planner/Agent)
- Pin:
  - `.github/copilot_context.md`
  - `CONVENTIONS.md`
  - spec + data model + sample CSV
- Require:
  - mapping table with confidence
  - consistency checks
  - “Unclear mappings / questions” section
  - plan checklist output file

## STM content (must include)
- Mapping table:
  - target_field, source_field(s), transform_rule, type, null/defaults, join/path, grain impact, tests, confidence (H/M/L)
- Consistency checks:
  - coverage, type mismatches, enum mismatches, duplication risks, missing rules
- Unclear mappings/questions (ranked)
- Implementation checklist (files/tests/validation commands)

## Confidence scoring (AI draft confidence)
- High: direct 1:1 mapping with clear type and rule
- Medium: plausible but requires assumption (cast/default/normalization)
- Low: ambiguous, missing key/join, undefined business rule
Rule: every Medium/Low must include “Why” and appear in Unclear section.

## DOs
- DO enforce coverage: no target field left unmapped silently
- DO surface join/grain risks explicitly
- DO include proposed tests (unique/not_null/accepted_values) aligned to mapping
- DO produce a plan checklist that can be executed

## DON’Ts
- DON’T treat STM as documentation only—it’s a build blueprint
- DON’T “guess” enums/relationships if not evidenced; ask questions
- DON’T skip confidence scoring; it drives HITL review

## Prompt skeleton (copy/paste)
```text
You are the Planner agent.

Inputs (repo files):
- templates/stm_template.md
- specs/<use_case>_spec.md
- inputs/<use_case>_data_model.md
- inputs/<use_case>_sample.csv (if present)
- CONVENTIONS.md
- .github/copilot_context.md

Task:
1) Produce a short todo plan.
2) Create/update planning/<use_case>_stm.md using the template.
3) Include confidence (H/M/L) per mapping + “Why” for M/L.
4) Create planning/<use_case>_plan_checklist.md derived from STM.

Rules:
- Do not invent fields beyond spec/data model.
- Every target field mapped or flagged.
- Surface type/enums/grain risks.

Now: plan first, then write both files.
```