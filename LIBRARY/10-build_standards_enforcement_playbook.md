# File: templates/build_standards_enforcement_playbook.md
# Template: Build — Standards Enforcement (dbt + YAML + reviews)

## Purpose
A reusable playbook to use Copilot for **standards enforcement** during Build:
- detect violations early (before reviewer time is spent)
- apply minimal patches (small diffs)
- validate with dbt gates (`deps/parse`) and CI gates

## When to use
- New projects or new domains where standards drift is common
- Any PR that touches dbt models or schema.yml
- After refactors / modernization (legacy SQL → modular dbt)
- Before merging workflow changes (.github/workflows)

## Inputs (pin these)
- `.github/copilot_context.md`
- `CONVENTIONS.md`
- Relevant file(s) being modified:
  - `models/*/*.sql`
  - `models/*/schema.yml`
  - `.github/workflows/*.yml`
- One golden example closest to your layer:
  - `models/staging/<golden_stg_model>.sql`
  - `models/marts/<golden_mart_model>.sql`

## Outputs
- A standards violation report (High/Med/Low)
- Minimal patch set (SQL-only / YAML-only)
- Validation evidence (commands + results)

## How to use (human steps)
1) Pin context pack + conventions + target files.
2) Ask Copilot for a **violations report** first.
3) Apply minimal patches in bounded mode:
   - “SQL only”, “YAML only”, or “only the changed block”.
4) Run validation gates:
   - `dbt deps`
   - `dbt parse`
5) Capture evidence in PR description.

## How to use with Copilot (recommended prompts)
### 1) Violations report (review-first)
```text
Review the following files against CONVENTIONS.md and copilot_context.md.

Targets:
- <PATH_1>
- <PATH_2>

Output:
1) Violations list (High/Med/Low) with exact locations.
2) Minimal patch plan (what to change and where).
3) Ambiguities / questions (do not guess).
Rules:
- No refactors unless required for compliance.
- Do not change semantics unless explicitly requested.
```

### 2) Apply minimal SQL patch
```text
Apply only the minimal SQL changes required for standards compliance for:
- <MODEL PATH>

Constraints:
- Output SQL only.
- No semantic change unless explicitly required.
- Add explicit grain comment if missing.
```

### 3) Apply minimal YAML patch
```text
Apply only the minimal schema.yml changes required for standards compliance for:
- <schema.yml PATH>

Constraints:
- Output YAML only.
- Use repo-required test syntax (e.g., arguments:).
- Add model description with explicit grain and key column docs.
```

### 4) Evidence-driven fix loop
```text
This gate failed. Here is the exact error output:

<PASTE ERROR>

Provide the smallest possible patch.
- Output only the corrected block.
- No refactors or unrelated edits.
```

## DOs
- DO run “review-first” before writing code
- DO keep diffs minimal and bounded
- DO enforce staging minimalism (no joins/filters/dedupe)
- DO ensure schema.yml uses correct syntax (arguments: if required)

## DON’Ts
- DON’T let Copilot invent fields or schema facts
- DON’T accept broad refactors during a fix
- DON’T add over-strict tests that will create false failures
- DON’T merge without `dbt parse` evidence