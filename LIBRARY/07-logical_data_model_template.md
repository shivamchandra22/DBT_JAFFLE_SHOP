# File: templates/logical_data_model_template.md
# Template: Propose initial logical data model (Task B)

## Purpose
A reusable template to propose a dbt-implementable logical model:
- facts/dimensions,
- PK/FK,
- grain per entity,
- attribute ownership,
- dedupe strategy placement,
based on Task A outputs and data dictionary.

## When to use
- Planning a new mart or KPI dataset
- Modernizing legacy SQL to modular dbt
- Aligning multiple teams on a shared model contract
- Preparing an OpenSpec blueprint (Task D)

## Inputs
- Task A outputs: entities/relationships + grain map
- Optional dictionary/contract and sample extracts

## Outputs (recommended)
- `planning/<use_case>_logical_data_model.md`
- (optional) `planning/<use_case>_open_questions.md`

## How to use (human steps)
1) Start from reverse-engineered evidence (Task A), not preferences
2) Propose minimal facts/dims that fully support the target output
3) Make dedupe and SCD decisions explicit
4) Validate completeness (Task C)

## Logical model structure
### Entity summary table
| Entity | Type (fact/dim) | Grain | PK | FKs | Core attributes | Notes |
|---|---|---|---|---|---|---|

### Entity details (repeat per entity)
- Entity:
- Type:
- Grain:
- PK:
- FKs:
- Core attributes:
- Dedupe strategy (if any):
- SCD policy (if any):
- Ownership notes (where derived fields live):

### Relationship list
- FK → PK, join keys, cardinality, risks

### Open questions (ranked)
- Priority / question / why it matters / owner

## DOs
- DO define grain + PK for every entity (or mark unknown explicitly)
- DO keep staging minimal; put business rules and dedupe in intermediate/marts
- DO prefer stable interfaces in marts (facts/dims) to reduce downstream churn
- DO include attribute ownership so logic isn’t duplicated everywhere

## DON’Ts
- DON’T copy legacy CTEs into a “model” without rationalizing boundaries
- DON’T mix facts and dims without a reason (causes unclear grain)
- DON’T hide dedupe inside downstream queries—make it a design decision
- DON’T introduce relationships unless keys exist or are explicitly requested