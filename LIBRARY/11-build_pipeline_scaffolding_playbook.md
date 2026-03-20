# File: templates/build_pipeline_scaffolding_playbook.md
# Template: Build — GitHub Actions Pipeline Scaffolding (dbt CI)

## Purpose
A reusable playbook to scaffold and harden GitHub Actions workflows for dbt:
- consistent PR gates (deps/parse/compile)
- safe defaults (least permissions, no secret leaks)
- caching for speed
- reusable patterns across repos

## When to use
- Creating CI for a new dbt repo
- Standardizing CI across teams
- Improving runtime and reliability of existing workflows
- Adding gates and artifacts for better review evidence

## Inputs (pin these)
- `.github/copilot_context.md`
- `CONVENTIONS.md` (CI expectations section, if exists)
- Existing workflow (if any): `.github/workflows/<existing>.yml`

## Outputs
- `.github/workflows/dbt-ci.yml` (or your chosen name)
- Optional supporting scripts: `scripts/<...>.sh` / `scripts/<...>.py`
- Optional: PR template updates to require evidence

## How to use (human steps)
1) Decide what gates you want:
   - PR: deps + parse (+ compile)
   - main: (later) run/test selections
2) Generate workflow YAML (YAML-only).
3) Hardening pass: permissions, pinning, caching, safe logs.
4) Validate by opening a PR and checking Actions results.

## How to use with Copilot (recommended prompts)
### 1) Generate minimal CI workflow
```text
Create a GitHub Actions workflow for dbt CI.

Target file:
- .github/workflows/dbt-ci.yml

Requirements:
- triggers: pull_request (main), push (main)
- job: dbt-parse
- steps: checkout, setup python, install dbt adapter (pin version), dbt deps, dbt parse
- include caching: pip + dbt_packages
- permissions: contents: read
- no secrets; no env dumps

Output YAML only.
```

### 2) Harden the workflow
```text
Review this workflow for:
- least privilege permissions
- deterministic installs (pin versions)
- caching correctness
- safe logging (no secret leaks)

Output:
- High/Med/Low risks and minimal YAML edits to fix.
```

### 3) Optional: add compile gate
```text
Add a dbt compile step to the workflow.
Constraints:
- YAML-only snippet
- do not add DB credentials
```

## DOs
- DO default to least permissions
- DO pin versions for actions and dbt adapter
- DO add caching for pip and dbt_packages
- DO keep PR gates minimal and stable first

## DON’Ts
- DON’T print env vars or secrets
- DON’T build a complex deploy pipeline on day one
- DON’T run everything; plan for selectors/tags/state later