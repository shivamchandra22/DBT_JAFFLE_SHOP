# File: templates/build_fix_loop_playbook.md
# Template: Build — Evidence-driven Fix Loop (fast time-to-green)

## Purpose
A repeatable debugging workflow using Copilot to reduce cycle time:
- use real failure evidence (dbt/CI error output)
- request the smallest possible patch
- minimize diffs and avoid semantic drift

## When to use
- dbt parse/compile failures
- schema.yml syntax/test-format warnings
- CI workflow YAML issues
- quick recovery from broken PR checks

## Inputs
- Exact error output (copy/paste)
- File path(s) referenced by the error
- The relevant file(s) opened/pinned if possible

## Output
- Minimal patch (changed block only)
- Gate returns to green (`dbt parse` or CI rerun)

## How to use (human steps)
1) Run the gate to generate evidence:
   - `dbt parse` (offline) or CI logs
2) Copy only the failing error block + file path.
3) Ask Copilot for the smallest patch.
4) Apply patch and re-run gate.
5) Capture evidence in PR.

## Copilot prompt (minimal patch)
```text
This gate failed. Here is the exact error output:

<PASTE ERROR>

Constraints:
- Provide the smallest possible patch.
- Output only the corrected block (SQL/YAML).
- No refactors or unrelated edits.
- Do not change semantics unless required to fix the failure.
```

## DOs
- DO fix one failure at a time
- DO keep outputs bounded (“only changed block”)
- DO re-run gate after each patch

## DON’Ts
- DON’T paste entire logs or entire repo context
- DON’T accept broad refactors during a fix
- DON’T change logic while fixing syntax