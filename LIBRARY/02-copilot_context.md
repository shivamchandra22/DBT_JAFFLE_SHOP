# .github/copilot_context.md (SKELETON) — Copilot Context Pack for ADLC Data Engineering (Template)

## Purpose
Single “context pack” to pin in GitHub Copilot Chat/Agent for this repo.  
Goal: make Copilot outputs consistent, dbt-compatible, and aligned with ADLC practices.

---

## How To Use this Template
Goal: create a single pinned “context pack” that makes Copilot consistent across your repo.

1) Copy this skeleton to: `.github/copilot_context.md`
2) Replace placeholders:
   - warehouse + dbt version, banned keys, syntax rules (e.g., schema.yml `arguments:`)
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
- Use it in every exercise and daily dev:
  - “Use `#file:.github/copilot_context.md` as your primary context.”

---

## How to use copilot_context with GitHub Copilot
- In Copilot Chat, reference: `#file:.github/copilot_context.md`
- Optionally add: `#folder:inputs #folder:templates #folder:models` (if your Copilot supports folder context)
- For Planner/Agent mode: always request **“plan first, then write files”**.
- For standards work: request **violations report → minimal patch**.

---

## Repo ground rules (must follow)
- Target stack: `<warehouse> + dbt <version>`
- No secrets in chat or repo.
- No hallucinated columns: only from:
  - `inputs/*` (requirements, dictionaries, samples)
  - `models/staging/sources.yml` (source contracts)
  - existing models/schema.yml
- Staging models: minimal transforms only (no joins/aggregations/business filters/dedupe).
- Business logic + dedupe: intermediate/marts.
- schema.yml tests: use syntax required by this dbt version (e.g., `arguments:`).
- sources.yml: avoid unsupported keys for this repo (e.g., `<loaded_at_field?>` = `<allowed/forbidden>`).
- dbt_project.yml: do not include unsupported keys; must be valid YAML.

---

## Canonical files (prefer these)
- Standards: `CONVENTIONS.md`
- dbt config: `dbt_project.yml`, `packages.yml`
- Source contracts: `models/staging/sources.yml`
- YAML style: `models/staging/schema.yml`, `models/marts/schema.yml`
- Golden examples:
  - staging: `models/staging/<golden_stg_model>.sql`
  - marts: `models/marts/<golden_mart_model>.sql`
- CI workflows (if present): `.github/workflows/*.yml`
- PR template (if present): `.github/pull_request_template.md`

---

## Layering model (expected)
- staging: `models/staging/stg_<source>__<entity>.sql`
- intermediate: `models/intermediate/int_<domain>__<topic>.sql`
- marts: `models/marts/fct_<process>.sql`, `models/marts/dim_<entity>.sql`

---

## Definition of Done (DOD) — minimum
A model PR is “reviewable” only when:
- Grain explicitly stated
- schema.yml includes:
  - model description + key column descriptions
  - baseline tests appropriate to layer
- Validation evidence provided:
  - `dbt deps`
  - `dbt parse`
  - (optional) `dbt compile/run/test` when DB access exists

---

## Development Index (session-agnostic) — how to route work with this context pack

### Purpose
Provide a stable, reusable “routing guide” for day-to-day development so Copilot consistently selects the right standards, patterns, and validation gates **without being tied to any workshop**.

### What to pin by default (minimum)
- `CONVENTIONS.md`
- `.github/copilot_context.md` (this file)
- Relevant schema files for the layer you touch:
  - `models/staging/schema.yml`
  - `models/marts/schema.yml`
- One golden example near your change:
  - staging golden: `models/staging/stg_raw__orders.sql` (or closest match)
  - marts golden: `models/marts/fct_orders.sql` (or closest match)

### Which folders to add (when needed)
- `inputs/` — when working from requirements, dictionaries, sample data
- `templates/` — when producing specs/STM/OpenSpec/playbooks
- `models/` — when generating or refactoring dbt models
- `.github/workflows/` — when working on CI/CD

### How to choose the right “mode” (use-case routing)
1) **Generate (scaffolding)**
   - Use when creating new models/files from known contracts.
   - Ask for: plan → create files → validate (`dbt deps/parse`).

2) **Enforce standards (review-first)**
   - Use when you already have code and want it compliant.
   - Ask for: violations report → minimal patch plan → apply minimal diffs → validate.

3) **Plan (spec/mapping)**
   - Use when requirements are unclear or transformation logic must be explicit.
   - Ask for: spec/STM artifacts + open questions + confidence scoring (H/M/L for mappings).

4) **Fix (evidence-driven)**
   - Use when a tool run fails (dbt parse/compile/CI).
   - Provide: exact error + file path → request smallest patch → re-run gate.

### Standard validation gates (offline-first)
Run after each meaningful change set:
- `dbt deps`
- `dbt parse`

If DB access exists (later stage):
- `dbt compile`
- `dbt run -s <selection>`
- `dbt test -s <selection>`

### Non-overwrite / versioning rule (production-safe default)
- Do not overwrite files unless explicitly requested.
- If creating a “new variant” is safer, create a versioned file:
  - `<name>_v2` or `<name>_<YYYYMMDD-HHMM>`
- Always state which file is canonical after creation.

---

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
- Confidence is **AI draft confidence** (H/M/L), not a probability.
- **High**: direct mapping supported by the provided data model/contracts and clear transform.
- **Medium**: plausible mapping but requires an explicit assumption (cast/default/normalization/join detail).
- **Low**: ambiguous or missing information (unclear join/key, multiple possible sources, undefined business rule).
- Every **Medium/Low** row must:
  - include a short **“Why”** note, and
  - appear in **Unclear mappings / Questions** (with a proposed question and severity).

## Standards enforcement mode (review-first workflow)
Use this mode when the goal is **adherence to standards** rather than generation.

### Required behavior
1) Produce a **standards violation report** first:
   - list violations (High/Med/Low)
   - point to exact file(s) and sections
2) Propose a **minimal patch plan** (small diffs; no refactors unless required)
3) Apply fixes in bounded outputs:
   - “SQL only” or “YAML only”
4) Re-validate offline:
   - `dbt deps`
   - `dbt parse`

### Rules
- Do not introduce semantic changes unless explicitly requested.
- Do not move business rules into staging.
- Do not invent columns; only use contracts/dictionaries and existing files.

## Common pitfalls to avoid
- Adding unsupported keys to `dbt_project.yml`
- Using deprecated `schema.yml` test format (use repo-approved syntax, e.g., `arguments:`)
- Doing dedupe or business filters in staging
- Creating extra files not requested
- Changing existing conventions without explicit instruction

## Quick offline validation

- `dbt deps`
- `dbt parse`
