# .github/PLAYBOOKS/FOUNDATION_BOOTSTRAP.md (SKELETON) — Data Engineering Repo Bootstrap Playbook (Template)

## Purpose
Bootstrap a new Data Engineering / Analytics Engineering repo so it is immediately usable for ADLC work:
- consistent dbt project structure
- conventions + golden examples
- source contracts and baseline tests/docs
- (optional) demo inputs for workshops when no DB access exists

This playbook is **one-time setup**. It is not daily dev guidance.

## Who this is for
- Tech leads / enablement / platform engineers setting up a new repo
- Coaches preparing workshop environments
- Engineers creating a new domain repo or sandbox

## Scope & assumptions (fill in)
- Target warehouse: `Snowflake`
- dbt version: `<e.g., 1.10.x>`
- Repo name: `DBT_JAFFLE_SHOP`
- Environments: `dev` and promotion approach: `<brief>`
- Security constraints: `<no secrets in repo, redaction rules, OIDC/secrets manager>`
- Package policy: `<allowed packages, version pinning rules>`

## Bootstrap mode (choose one)
MODE: `<sources | seeds>`
- `sources` = real source contracts in `sources.yml` (preferred)
- `seeds` = tiny CSV demo data for workshops (never production ingestion)

## Non-negotiables (guardrails)
- No credentials/secrets in repo or prompts.
- No hallucinated columns: only from provided data dictionaries/contracts.
- schema.yml tests must use the **current syntax required by your dbt version** (e.g., `arguments:`).
- Do not introduce unsupported keys into `dbt_project.yml` or `sources.yml`.
- Staging models = minimal transforms only (no business filters, no joins, no dedupe).

## Outputs checklist (definition of “bootstrap complete”)
At minimum, repo contains:
- `dbt_project.yml`
- `packages.yml`
- `CONVENTIONS.md`
- `.github/copilot_context.md`
- `models/staging/sources.yml` (or seeds alternative)
- Golden examples: at least 1 staging model + 1 mart model + corresponding schema.yml entries
- Offline validation works: `dbt deps` and `dbt parse`

---

## How To Use this Template
Goal: produce a working bootstrap playbook tailored to your repo and dbt/warehouse version.

1) Copy this skeleton to: `.github/PLAYBOOKS/FOUNDATION_BOOTSTRAP.md`
2) Fill in placeholders in the top sections:
   - stack (warehouse), dbt version, environments, security constraints, package policy
   - MODE: sources vs seeds
3) Replace the canonical YAML template blocks:
   - paste your approved `dbt_project.yml` structure (keep minimal)
   - paste your approved `packages.yml` constraints format (compatible with your dbt version)
4) Decide what “golden examples” will exist:
   - pick 1 staging model and 1 marts model as the minimum reference set
5) Add any org-specific constraints:
   - forbidden keys list, naming rules, repo structure, CI expectations
6) Validate the playbook itself:
   - run it once in a clean repo to ensure it produces `dbt deps` + `dbt parse` success.

### Efficiently with Copilot
- In Copilot Chat, pin:
  - `#file:.github/PLAYBOOKS/FOUNDATION_BOOTSTRAP.md`
  - `#file:CONVENTIONS.md` (if it exists) or a standards doc
- Ask Copilot to *only* fill placeholders and templates, not invent new sections:
  - “Fill all <PLACEHOLDER> values for our stack: <X>. Keep structure unchanged.”
- Use a grounded run:
  - generate `_repo_files.txt` and instruct Copilot to create only missing files.
- After Copilot writes files, run:
  - `dbt deps` and `dbt parse` to confirm bootstrap quality.

# How to use the playbook with GitHub Copilot (Agent/Planner)
## Recommended approach (grounded execution)
1) Generate a ground-truth file list:
   - `find . -maxdepth 6 -type f | sed 's|^\./||' | sort > _repo_files.txt`
2) In Copilot Chat, reference:
   - `#file:.github/PLAYBOOKS/FOUNDATION_BOOTSTRAP.md`
   - `#file:_repo_files.txt`
3) Ask Copilot/Agent to create **only missing** artifacts in the exact paths listed in this playbook.

## Suggested “execute bootstrap” prompt (skeleton)
```text
Use .github/PLAYBOOKS/FOUNDATION_BOOTSTRAP.md as the single source of truth.
Use _repo_files.txt as authoritative current filesystem state.

Create missing folders (Step 0) and create/write all missing files (Steps 1..N) exactly as specified.
Do not invent extra files. Do not add unsupported keys. Keep YAML valid.
Output a checklist of created/updated paths.
```

---

# Step 0 — Create folders
Create (if missing):
- `models/staging`
- `models/intermediate`
- `models/marts`
- `macros`
- `tests`
- `analyses`
- `seeds`
- `snapshots`
- `inputs`
- `templates`
- `specs`
- `planning`
- `.github/PLAYBOOKS`

---

# Step 1 — Generate dbt_project.yml
## Human notes
- Canonical dbt config. Keep minimal and dbt-version compatible.
- Do not include unsupported keys; avoid “extra boilerplate”.

## Prompt (for Copilot/assistant)
Create `dbt_project.yml` for project `<PROJECT_NAME>` using profile `<PROFILE_NAME>` with:
- paths (models/analyses/tests/seeds/macros/snapshots)
- layer defaults (staging/intermediate/marts materializations)
- persist docs defaults
- tags by layer
- clean targets
Constraints:
- Valid YAML only.
- Do not add nonstandard keys (forbidden keys: `<list>`).
- Use the exact template below (may adjust values, but do not add top-level keys):

```yaml
name: "<PROJECT_NAME>"
version: "1.0.0"
config-version: 2

profile: "<PROFILE_NAME>"

model-paths: ["models"]
analysis-paths: ["analyses"]
test-paths: ["tests"]
seed-paths: ["seeds"]
macro-paths: ["macros"]
snapshot-paths: ["snapshots"]

target-path: "target"
clean-targets: ["target", "dbt_packages"]

models:
  <PROJECT_NAME>:
    +persist_docs:
      relation: true
      columns: true

    staging:
      +materialized: view
      +tags: ["staging"]

    intermediate:
      +materialized: view
      +tags: ["intermediate"]

    marts:
      +materialized: table
      +tags: ["marts"]
```

---

# Step 2 — Generate packages.yml
## Human notes
- Prefer conservative version spec compatible with dbt major/minor.
- Add only approved packages.

## Prompt
Generate `packages.yml` including:
- `dbt-labs/dbt_utils` (use a version constraint format compatible with your dbt version, e.g. array style)
- `<other approved packages, optional>`
Output YAML only.

---

# Step 3 — Generate CONVENTIONS.md
## Human notes
- This is the “house style” and Definition of Done.
- It will be referenced in code review and pinned for Copilot.

## Prompt
Generate `CONVENTIONS.md` for this repo including:
- naming & layering rules
- SQL style guide
- schema.yml standards (including test syntax rules for this dbt version)
- Definition of Done checklist
- PR expectations (validation commands, evidence)
- security/redaction rules

---

# Step 4 — Generate .github/copilot_context.md
## Human notes
- Single file to pin as Copilot context for all exercises and daily dev.

## Prompt
Generate `.github/copilot_context.md` containing:
- repo ground rules (no hallucinated columns, staging minimal, YAML syntax)
- canonical file pointers
- layering model
- DOD
- Planner/Agent skeleton prompt
- common pitfalls

---

# Step 5 — Create source contracts (sources mode) OR seeds (seeds mode)

## 5A — Sources mode: models/staging/sources.yml
### Human notes
- Prefer explicit columns + short descriptions.
- Avoid keys that cause dbt warnings for your version.

### Prompt
Generate/extend `models/staging/sources.yml` defining:
- source: `<SOURCE_NAME>`
- tables: `<TABLE_1>`, `<TABLE_2>`, ...
Each table includes:
- columns list (name + brief description)
Output YAML only.

## 5B — Seeds mode: demo data (MODE=seeds only)
### Human notes
- Seeds are workshop/demo-only and must be labeled as such.

### Prompt
Create:
- `seeds/<seed_1>.csv` (~20 rows)
- `seeds/<seed_2>.csv` (~10 rows)
- `seeds/schema.yml` describing the seeds and columns
Output as separate files.

---

# Step 6 — Golden examples (minimum set)
## Human notes
- These become reference patterns for Copilot and humans.

## Prompt
Create:
- `models/staging/<golden_stg_model>.sql` (minimal transforms only)
- `models/staging/schema.yml` entry for that model (docs + tests)
- `models/marts/<golden_mart_model>.sql` (clear grain)
- `models/marts/schema.yml` entry (docs + tests)
Use modern test syntax required by this repo.

---

# Step 7 — Optional: PR template + CI skeleton
## Prompt
Create:
- `.github/pull_request_template.md` with DOD checklist and validation evidence section
- (optional) `.github/workflows/<ci>.yml` skeleton for dbt deps/parse/compile

---

# Step 8 — Validation
Offline (no DB required):
- `dbt deps`
- `dbt parse`

If DB access exists later:
- `dbt compile`
- `dbt run -s <selection>`
- `dbt test -s <selection>`

---

# Step 9 — Mark completion
Update a single status file in repo root:
- `FOUNDATION_STATUS.md`
Include:
- FOUNDATION_COMPLETE=true/false
- VALIDATION_MODE=parse-only/full
- Date/Owner/Notes