# File: templates/completeness_validation_checklist.md
# Template: Validate completeness + dependencies/gaps (Task C)

## Purpose
A repeatable checklist and output structure to validate that:
- the proposed model covers the target output fully,
- dependencies are identified early,
- risks are ranked and converted to actions.

## When to use
- Before Build starts (to prevent “clarify later” surprises)
- Before approving an OpenSpec blueprint
- During modernization to avoid semantic drift
- When PRs introduce new columns or join paths (risk of grain change)

## Inputs
- Logical model (Task B)
- Legacy SQL final output (or target schema stub)
- Optional sample data extracts (CSV) for enums/type sanity checks

## Outputs (recommended)
- `planning/<use_case>_gaps_dependencies_risks.md`

## How to use (human steps)
1) Enumerate target output fields (from legacy final select or schema stub)
2) For each field, assign:
   - owning entity
   - source field(s)
   - transformation rule location (stg/int/marts)
3) Record gaps and dependencies; rank severity
4) Decide blockers vs deferrable items (HITL gate)

## Coverage matrix (template)
| Output field | Owner entity | Source field(s) | Transform summary | Layer (stg/int/mart) | Status (ok/gap) | Notes |
|---|---|---|---|---|---|---|

## Gap list (template)
| Severity (H/M/L) | Gap / ambiguity | Why it matters | Proposed action | Owner |
|---|---|---|---|---|

## Dependency list (template)
- Reference data: <code lists, mappings>
- Calendars/time zones: <needs?>
- FX rates: <needs?>
- Identity resolution: <customer matching?>
- Upstream SLAs/freshness: <needs?>

## Risk scan (what to explicitly check)
- Grain breaks / join expansion
- Enum drift (new status values)
- Type mismatches (timestamps, numeric precision)
- Null semantics (is null allowed? defaulting?)
- Late-arriving updates and dedupe ordering

## DOs
- DO force every output field to be accounted for (or explicitly flagged)
- DO rank gaps and assign an owner
- DO treat “unknown enums” as a risk and define handling strategy
- DO explicitly label which layer owns each transformation

## DON’Ts
- DON’T allow “we’ll figure it out in Build” for High severity gaps
- DON’T accept implicit joins or unproven cardinalities
- DON’T use sample data as truth; use it to detect inconsistencies only