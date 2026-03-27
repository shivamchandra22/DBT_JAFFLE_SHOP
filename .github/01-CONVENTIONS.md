# CONVENTIONS.md (SKELETON) - Analytics Engineering Standards for ADLC (dbt + Warehouse) (Template)

## Purpose

This document defines the team’s standards for analytics engineering:

- naming and layering conventions
- SQL style
- schema.yml documentation and tests
- Definition of Done (DOD)
- review and validation expectations
- ADLC alignment (Learn/Plan/Validate/Build/Verify/Deploy/Operate/Observe)

## How To Use this Template

Goal: turn this skeleton into your team’s enforceable analytics engineering standard.

1) Copy this skeleton to: `CONVENTIONS.md` at repo root
2) Fill in the “Layering & naming” section for your actual repo:
   - exact naming patterns and folder structure
3) Define your SQL style rules:
   - CTE conventions, aliasing, column ordering, comments
4) Define schema.yml policy and test syntax:
   - explicitly lock the format required by your dbt version (e.g., arguments:)
5) Define Definition of Done (DOD):
   - minimum docs/tests/validation evidence for PR acceptance
6) Add the review policy:
   - what reviewers check and what causes an automatic “return PR”
7) Add security/compliance constraints:
   - what can/can’t be pasted, redaction rules, handling of sensitive columns
8) Validate by usage:
   - apply it to 2–3 PRs and refine where engineers/reviewers struggle.

### Efficiently with Copilot

- Use Copilot to draft from existing code:
  - “Infer naming and style from our existing models and propose conventions.”
- Then lock it down with humans:
  - treat Copilot output as a draft; final wording should be agreed by TL/architects.
- Use it as a guardrail in prompts:
  - “Follow CONVENTIONS.md exactly; output SQL only; do not invent columns.”

## How to use conventions with GitHub Copilot

- Pin this file for any generation task: `#file:CONVENTIONS.md`
- For new models, also pin one golden example from each relevant layer.
- Use it as the contract in code review: reviewers enforce these rules.

---

## 1) Repo structure & layering

### Layers (what belongs where)

- Staging (`models/staging`)
  - Purpose: raw-aligned cleaning (names, casts, normalization)
  - Prohibited: joins, aggregations, business filters, dedupe
- Intermediate (`models/intermediate`)
  - Purpose: business rules, dedupe, normalization beyond staging, reusable building blocks
- Marts (`models/marts`)
  - Purpose: consumption-ready facts/dims, stable interfaces, explicit grain

### Naming conventions (fill in)

- Staging: `stg_<source>__<entity>`
- Intermediate: `int_<domain>__<topic>` (or your preferred)
- Marts facts: `fct_<process>`
- Marts dims: `dim_<entity>`
- Schema files:
  - staging: `models/staging/schema.yml`
  - marts: `models/marts/schema.yml`
  - intermediate: `<where you keep it>`

---

## 2) Model grain & keys (mandatory)

- Every model must declare:
  - target grain (one sentence)
  - primary key (if applicable)
  - foreign keys (if applicable)
  - dedupe strategy (if applicable)
Where to declare:
- top-of-file comment (SQL)
- and/or model description in schema.yml
- and PR description for major changes

---

## 3) SQL style guide

### General

- Prefer CTE-based structure with readable names
- One logical transformation per CTE
- Column ordering: keys first, then dates, then measures, then descriptors
- Always alias tables; avoid ambiguous column references

### Snowflake-specific (if applicable)

- Preferred idioms: `<QUALIFY row_number>`, `<date handling>`, `<safe casts>`
- Avoid: `<anti-patterns e.g., SELECT *>`
- Performance hints: early filtering, column pruning, avoid unnecessary DISTINCT

---

## 4) schema.yml standards (docs + tests)

### Documentation

- Model description must include purpose + grain
- Key columns must have descriptions
- Use consistent tone and brevity

### Test syntax compatibility (MANDATORY)

- Use the modern generic test format required by this repo:
  - tests must use `arguments:` (if on dbt versions that require it)

Example (correct):

```yaml
- accepted_values:
    arguments:
      values: ["a", "b"]
```

Example (avoid if deprecated):

```yaml
- accepted_values:
    values: ["a", "b"]
```

### Baseline tests (by layer)

- Staging:
  - not_null for natural keys (where appropriate)
  - accepted_values for enums (where stable)
- Intermediate:
  - unique + not_null for deduped keys
  - not_null for required derived fields
- Marts:
  - unique + not_null for primary keys
  - relationships (only when stable target exists)
  - accepted_values and domain checks for business-critical enums

(Adjust per domain; avoid over-strict tests that create false failures.)

---

## 5) Definition of Done (DOD) — PR checklist

A PR is “reviewable” only if

- Grain + keys are stated
- schema.yml updated (docs + baseline tests)
- Validation evidence included:
  - `dbt deps`
  - `dbt parse`
  - (when DB access exists) `dbt run -s <selection>` and `dbt test -s <selection>`
- No convention drift (naming, layering)

---

## 6) Review standards

Reviewers validate:

- grain correctness (no hidden duplication)
- business rules match requirements/spec
- tests are meaningful and not over-restrictive
- performance risk (obvious join explosion, huge scans)
- docs completeness

---

## 7) ADLC alignment (how we work)

- Learn: capture glossary, systems, contracts
- Plan: produce spec + STM + checklist
- Validate: check readiness and risks
- Build: implement models + docs/tests
- Verify: parity/audit checks and regression tests
- Deploy: CI/CD gates and promotion
- Operate: runbooks, troubleshooting, monitoring
- Observe: feedback loops, template/prompt improvement

---

## 8) Security & compliance

- Never paste secrets or PII into prompts/logs.
- Mask sensitive columns in examples.
- Follow org policies: `<links or references>`

---

## 9) Copilot usage rules

- Always pin: `CONVENTIONS.md` + the nearest golden example.
- Prefer bounded outputs: “SQL only”, “YAML only”.
- For fixes: provide failing output; request minimal patch.
- Ambiguity must be surfaced as Open Questions, not guessed.

---

## 10) Templates & references (fill in)

- Spec template: `templates/spec_template.md`
- STM template: `templates/stm_template.md`
- Context pack: `.github/copilot_context.md`
- Golden examples: `<list>`
