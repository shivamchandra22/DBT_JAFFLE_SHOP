# DBT_JAFFLE_SHOP Conventions & Standards

## Purpose
This document defines the "house style" and Definition of Done (DoD) for the DBT Jaffle Shop project. All code and documentation must adhere to these standards.

---

## Naming & Layering Rules

### Model Layering
- **Staging (`stg_*`)**: Minimal transforms only. Direct mapping from source tables with:
  - Column rename/recast where necessary
  - Data cleaning (NULL handling, standardization)
  - NO business logic, NO joins, NO filtering
- **Intermediate (`int_*`)**: Intermediate business logic joins and transformations (optional layer)
- **Marts (`fct_*`, `dim_*`)**: Business-ready fact and dimension tables. Clear grain and conformed dimensions.

### Naming Conventions
- Models: lowercase with underscores (e.g., `stg_customers`, `dim_customers`, `fct_orders`)
- Sources: descriptive owner + table name (e.g., `source('raw', 'customers')`)
- Tests: use dbt standard test naming conventions
- Columns: lowercase with underscores

---

## SQL Style Guide

### Formatting
- Use 2-space indentation
- Capitalize SQL keywords: `SELECT`, `FROM`, `WHERE`, `JOIN`, `ON`, `GROUP BY`, `ORDER BY`
- One clause per line when possible
- Use CTE (WITH) clauses for readability

### Example
```sql
with source_data as (
  select
    customer_id,
    first_name,
    last_name,
    email
  from {{ source('raw', 'customers') }}
),

transformed as (
  select
    customer_id,
    upper(first_name) as first_name,
    upper(last_name) as last_name,
    lower(email) as email
  from source_data
)

select * from transformed
```

---

## schema.yml Standards

### Structure Requirements
All models must have:
1. `version: 2` at top of file
2. Model name and description
3. Column names with descriptions
4. Appropriate tests (unique, not_null, relationships, accepted_values, custom)

### Test Syntax (dbt 1.0+)
Use `arguments:` structure for parameterized tests:

```yaml
version: 2

models:
  - name: stg_customers
    description: "Customer staging table"
    columns:
      - name: customer_id
        description: "Primary key for customers"
        tests:
          - unique
          - not_null
      - name: status
        description: "Customer status"
        tests:
          - accepted_values:
              values: ['active', 'inactive', 'pending']
```

---

## Definition of Done (DoD) Checklist

Every model must satisfy:
- [ ] SQL is valid and follows style guide
- [ ] Model has `description` in schema.yml
- [ ] All columns have `description` in schema.yml
- [ ] Staging models contain only minimal transforms (no business logic)
- [ ] All PK columns have `unique` + `not_null` tests
- [ ] Schema.yml uses current dbt syntax (no deprecated keys)
- [ ] No credentials/secrets in SQL or configs
- [ ] Model compiles without errors: `dbt compile -s model_name`
- [ ] Tests pass: `dbt test -s model_name`

---

## PR Expectations

### Validation Commands
Before submitting a pull request, run:

```bash
# Install dependencies
dbt deps

# Parse and validate YAML
dbt parse

# Compile all models
dbt compile

# Run tests
dbt test

# Test specific model
dbt test -s stg_customers
```

### PR Evidence Required
Include in PR description:
- Output of `dbt compile -s <new_models>`
- Output of `dbt test -s <new_models>`
- List of affected downstream models (if any)
- For refactors: proof of semantic equivalence

### PR Template
See `.github/pull_request_template.md`

---

## Security & Redaction Rules

- **No credentials** in repo, SQL files, or configs
- Use dbt profiles.yml (git-ignored) for warehouse connections
- **No PII** in comments or example data (except where documented for schema.yml)
- Seeds are for demo/workshop data only—never production ingestion
- If working with real data, apply redaction rules in staging models

---

## Forbidden Keys
Do NOT use these keys in `dbt_project.yml` or model configs:
- `on-run-start` / `on-run-end` (use hooks sparingly; document all)
- `dispatch` (unless explicitly approved)
- Unsupported keys for your dbt version

Check your dbt version with `dbt --version` and consult [dbt documentation](https://docs.getdbt.com) for version-specific config.

---

## Common Pitfalls

1. **Hallucinated Columns**: Use only columns from actual source data. Never invent columns in stg models.
2. **Over-complex Staging**: If your stg model has joins, it's probably a mart. Stop and refactor.
3. **Deprecated Test Syntax**: Ensure tests use `arguments:` not bare nested YAML.
4. **Missing Descriptions**: Every model and column must have a description—no exceptions.
5. **Unvalidated YAML**: Always run `dbt parse` before committing schema.yml changes.

---

## Update History
- **v1.0** (2026-03-27): Initial standards document

