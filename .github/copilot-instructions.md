# Copilot Workspace Instructions for DBT_JAFFLE_SHOP

## Purpose
This file defines conventions and best practices for AI agents and developers working in the `DBT_JAFFLE_SHOP` dbt project. It is based on project files, README, and the conventions template.

---

## 1. Build & Test Commands
- **Initialize/Verify:** `dbt debug`
- **Compile:** `dbt compile`
- **Run Models:** `dbt run`
- **Run Tests:** `dbt test`
- **Generate Docs:** `dbt docs generate`
- **Serve Docs:** `dbt docs serve`

---

## 2. Project Structure & Layering
- **Staging (`models/staging/`):** Raw-aligned cleaning (renaming, casting, normalization). *Materialized as views.*
- **Intermediate (`models/Intermediate/`):** Business rules, deduplication, reusable logic blocks.
- **Marts (`models/marts/`):** Consumption-ready facts/dims, stable interfaces. *Materialized as tables.*
- **Seeds (`seeds/`):** Source CSVs for initial data.

---

## 3. Naming Conventions
- **Staging:** `stg_<entity>.sql` (e.g., `stg_customers.sql`)
- **Intermediate:** `int_<domain>__<topic>.sql` (if used)
- **Marts:** `dim_<entity>.sql` (dimensions), `fact_<process>.sql` (facts)
- **Schema files:** `models/schema.yml`, `models/staging/schema.yml`

---

## 4. SQL Style Guide
- Use CTEs for logical steps; final output in a `final` CTE.
- Column order: keys first, then dates/measures, then descriptors.
- Always alias tables; avoid ambiguous column references.
- Avoid joins/aggregations/business logic in staging models.

---

## 5. Documentation & Testing
- Document models and columns in `schema.yml` files.
- Every model must have `unique` and `not_null` tests on its primary key.
- Use `accepted_values` and `relationships` tests as appropriate.

---

## 6. Common Pitfalls
- **Materialization mismatch:** Staging = views, marts = tables.
- **Primary key validation:** Always test for uniqueness and not-null.
- **Layering:** No joins/aggregations in staging; keep business logic in intermediate/marts.

---

## 7. Example Prompts
- "Generate a new staging model for a new source table."
- "Add a fact table for order payments."
- "Write schema.yml tests for a new dimension."
- "Refactor a model to follow CTE and column ordering conventions."

---

## 8. Related Customizations to Consider
- **/create-instruction**: For stricter SQL style enforcement or custom linting.
- **/create-skill**: For dbt-specific test generation or documentation automation.
- **/create-agent**: For automating model refactoring or migration tasks.

---

*For more details, see `LIBRARY/template-CONVENTIONS.md` and the README.*
