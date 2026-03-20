# File: templates/legacy_sql_reverse_engineering_playbook.md
# Template: Legacy SQL → Entities/Relationships/Grain (Task A)

## Purpose
A repeatable playbook to reverse-engineer a legacy “monster query” into:
- entity candidates (fact/dim),
- join relationships and assumptions,
- grain map per CTE and final output,
so modernization starts from an explicit understanding rather than guesswork.

## When to use
- Modernizing legacy SQL into modular dbt
- Onboarding a new engineer to an existing report/query
- Investigating parity breaks (suspected grain/key mismatch)
- Pre-migration planning (warehouse move, semantic layer refactor)

## Inputs
- Legacy SQL file: `analyses/<legacy_query>.sql`
- Optional data dictionary/contract: `inputs/<dictionary>.md`
- Optional “target output stub”: `planning/<target_schema_stub>.md`

## Outputs (recommended)
- `planning/<use_case>_entities_relationships.md`
- `planning/<use_case>_grain_map.md`
- `planning/<use_case>_open_questions.md` (optional)

## How to use (human steps)
1) Collect evidence set:
   - legacy SQL + any dictionary + target output stub
2) Run reverse engineering (manually or with Copilot Planner agent)
3) Review the results (HITL gate) and correct any wrong assumptions
4) Use outputs as inputs for logical modeling (Task B)

## How to use with Copilot (Planner/Agent)
- Pin as context:
  - `.github/copilot_context.md`
  - legacy SQL file
  - dictionary + target stub (if available)
- Ask Planner to:
  - plan first,
  - write the two output files,
  - list uncertainties as Open Questions.

## Template: Output structure (entities/relationships)
### Entity candidates
For each entity:
- Name:
- Type: Fact/Dim/Bridge/Reference
- Natural key / PK:
- Key attributes:
- Notes (dedupe, SCD, late arriving, etc.)

### Relationships
For each relationship:
- From (FK side) → To (PK side)
- Join keys:
- Expected cardinality:
- Evidence (which join/CTE implies this):
- Risk level (H/M/L) + why

### Grain map
For each CTE:
- CTE name:
- Grain statement:
- Grain change trigger (join, aggregation, dedupe):
- Risk notes

## DOs
- DO state grain explicitly everywhere (“1 row per …”)
- DO flag 1:M expansion risks even if you can’t fully prove them
- DO list missing join keys and unclear definitions as questions
- DO separate “evidence” from “assumptions” (label assumptions)

## DON’Ts
- DON’T invent entities or keys not supported by SQL/dictionary
- DON’T assume 1:1 joins without justification
- DON’T skip grain mapping—most semantic bugs come from grain drift
- DON’T conflate dedupe logic with business logic; describe them separately