# File: templates/requirements_spec_playbook.md
# Template: Requirement Analysis → Spec + Open Questions (Planner Agent)

## Purpose
Turn free-text requirements into a **reviewable, engineering-ready spec** that can drive planning and dbt build.
Outputs are file-based artifacts (repo-native), not chat-only.

## When to use
- New feature request for a mart/fact/dim
- Modernization planning (translate stakeholder intent into acceptance criteria)
- Before any Source-to-Target mapping (STM)
- Before any dbt model work begins (to prevent “clarify later” rework)

## Inputs
- Free-text requirements: `inputs/<use_case>_requirements.md`
- Data model/dictionary/contract: `inputs/<use_case>_data_model.md`
- Optional examples/samples: `inputs/<use_case>_sample.csv`
- Standards/context:
  - `CONVENTIONS.md`
  - `.github/copilot_context.md`

## Outputs (recommended)
- `specs/<use_case>_spec.md`
- Optional: `specs/<use_case>_open_questions.md` (if you want separate)

## How to use (human steps)
1) Collect the minimal inputs (requirements + data model/dictionary).
2) Run Planner to generate the spec file.
3) HITL gate: review grain/keys/rules + open questions with requestor.
4) Only after approval, proceed to STM (Plan phase).

## How to use with Copilot (Planner/Agent)
- Pin:
  - `#file:.github/copilot_context.md`
  - `#file:CONVENTIONS.md`
  - requirements + data model files
- Require:
  - plan first → write spec to repo → list open questions ranked

## Spec content (must include)
- User stories (2–4)
- Acceptance criteria (Gherkin scenarios; ≥5)
- Data requirements table (every target field included)
- Explicit grain + PK/FKs + dedupe rule (if any)
- Business rules/filters (explicit)
- Validation plan (high-level)
- Ranked open questions (priority + why + risk)

## DOs
- DO make grain and keys explicit (this is the highest leverage)
- DO force ambiguity into Open Questions, not assumptions
- DO write acceptance criteria as testable statements
- DO keep spec concise but complete enough to plan/build

## DON’Ts
- DON’T start building before spec is reviewed (creates rework)
- DON’T invent columns beyond the data model/dictionary
- DON’T mix requirements with implementation details (save for OpenSpec/build checklist)

## Prompt skeleton (copy/paste)
```text
You are the Planner agent.

Inputs (repo files):
- templates/spec_template.md
- inputs/<use_case>_requirements.md
- inputs/<use_case>_data_model.md
- CONVENTIONS.md
- .github/copilot_context.md

Task:
1) Produce a short todo plan.
2) Create/update specs/<use_case>_spec.md following the template.
3) Produce ranked Open Questions (priority + why + risk).

Rules:
- Do not invent columns beyond the data model.
- Make grain + PK/FKs + rules explicit.
- Output must be file-based.

Now: plan first, then write the spec file.
```