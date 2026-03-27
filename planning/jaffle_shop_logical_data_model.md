# Jaffle Shop Logical Data Model (Task B)

## Scope
This logical model is proposed from current implemented evidence in staging and marts models:
- `stg_customers`, `stg_orders`, `stg_payments`
- `customers`/`dim_customers` and `orders`/`fact_orders`

## Entity Summary
| Entity | Type (fact/dim) | Grain | PK | FKs | Core attributes | Notes |
|---|---|---|---|---|---|---|
| dim_customers | Dimension | 1 row per customer_id | customer_id | None | first_name, last_name, first_order, most_recent_order, number_of_orders, customer_lifetime_value | Customer profile + lifecycle aggregates |
| fact_orders | Fact | 1 row per order_id | order_id | customer_id -> dim_customers.customer_id | order_date, status, amount, credit_card_amount, coupon_amount, bank_transfer_amount, gift_card_amount | Order-level financial fact, payment-method breakout |
| stg_customers | Staging | 1 row per raw customer id | customer_id | None | first_name, last_name | Rename-only staging from raw_customers |
| stg_orders | Staging | 1 row per raw order id | order_id | customer_id (source user_id) | order_date, status | Rename-only staging from raw_orders |
| stg_payments | Staging | 1 row per raw payment id | payment_id | order_id -> fact_orders.order_id | payment_method, amount | Amount converted from cents to dollars |

## Entity Details

### Entity: dim_customers
- Type: Dimension
- Grain: One row per customer
- PK: customer_id
- FKs: None
- Core attributes:
  - first_name
  - last_name
  - first_order
  - most_recent_order
  - number_of_orders
  - customer_lifetime_value
- Dedupe strategy (if any):
  - Upstream uniqueness assumed in `stg_customers.customer_id` and enforced by tests.
  - No explicit dedupe SQL in current model.
- SCD policy (if any):
  - Current policy: Type 1 current-state only (no historical customer attribute tracking).
  - If history needed, move to snapshot-based SCD2 in `snapshots/`.
- Ownership notes (where derived fields live):
  - Order lifecycle and lifetime value aggregates belong in this mart-level dimension.
  - Raw name fields remain owned by staging source projection.

### Entity: fact_orders
- Type: Fact
- Grain: One row per order
- PK: order_id
- FKs:
  - customer_id -> dim_customers.customer_id
- Core attributes:
  - order_date
  - status
  - amount
  - credit_card_amount
  - coupon_amount
  - bank_transfer_amount
  - gift_card_amount
- Dedupe strategy (if any):
  - Upstream uniqueness assumed in `stg_orders.order_id` and tested.
  - Payment rows are aggregated to order_id in intermediate CTE logic.
- SCD policy (if any):
  - Not applicable as currently modeled (transaction fact, immutable grain).
- Ownership notes (where derived fields live):
  - Payment-method split metrics belong in fact_orders.
  - Customer enrichment should remain in dimensions or semantic layer.

### Entity: stg_customers
- Type: Staging
- Grain: One row per raw customer record
- PK: customer_id
- FKs: None
- Core attributes:
  - customer_id
  - first_name
  - last_name
- Dedupe strategy (if any):
  - None implemented; relies on raw quality + tests.
- SCD policy (if any):
  - None in staging.
- Ownership notes (where derived fields live):
  - Rename and datatype standardization only.

### Entity: stg_orders
- Type: Staging
- Grain: One row per raw order record
- PK: order_id
- FKs:
  - customer_id (expected relationship to stg_customers.customer_id)
- Core attributes:
  - order_id
  - customer_id
  - order_date
  - status
- Dedupe strategy (if any):
  - None implemented; relies on raw quality + tests.
- SCD policy (if any):
  - None in staging.
- Ownership notes (where derived fields live):
  - Rename-only from raw source (`user_id` -> `customer_id`).

### Entity: stg_payments
- Type: Staging
- Grain: One row per raw payment record
- PK: payment_id
- FKs:
  - order_id (expected relationship to stg_orders.order_id)
- Core attributes:
  - payment_id
  - order_id
  - payment_method
  - amount
- Dedupe strategy (if any):
  - None implemented; relies on raw quality + tests.
- SCD policy (if any):
  - None in staging.
- Ownership notes (where derived fields live):
  - Currency-scale normalization (cents -> dollars) belongs in staging as a minimal standardization.

## Relationship List
- fact_orders.customer_id -> dim_customers.customer_id
  - Join keys: `customer_id`
  - Cardinality: many orders to one customer
  - Risks: orphan orders if customer key missing/late
- stg_orders.customer_id -> stg_customers.customer_id
  - Join keys: `customer_id`
  - Cardinality: many orders to one customer
  - Risks: source `user_id` mapping mismatch during ingest
- stg_payments.order_id -> stg_orders.order_id
  - Join keys: `order_id`
  - Cardinality: many payments to one order (supports split tenders)
  - Risks: missing payment rows causing null/zero order amount in fact

## Dedupe & Data Quality Placement
- Staging layer:
  - Keep minimal transforms only.
  - No business dedupe logic unless source duplicates are systemic and contractually documented.
- Intermediate/mart layer:
  - Aggregate payments to order grain before joining to orders.
  - If duplicate raw keys are detected, implement deterministic dedupe in intermediate model (`int_*`) with explicit tie-breakers.

## Open Questions (Ranked)
| Priority | Question | Why it matters | Owner |
|---|---|---|---|
| P1 | Should customer name attributes be treated as SCD2 (history) instead of current-state only? | Impacts dimension design, snapshot cost, and BI historical reporting fidelity | Data Product Owner |
| P1 | Can an order exist with no payment record (timing or cancellation)? | Determines whether `fact_orders.amount` should default to 0 or remain null and how tests assert completeness | Analytics Engineering |
| P2 | Is currency always cents in raw_payments? | Incorrect scaling would distort all revenue metrics | Source System Owner |
| P2 | Should returns be represented as status only or as negative financial adjustments? | Affects gross vs net revenue definitions and KPI consistency | Finance Analytics |
| P3 | Are there late-arriving customers/orders in source refreshes? | Influences incremental strategy and relationship test severity | Platform/Data Ops |

## Proposed dbt Model Contract (Minimal)
- Keep current stable marts as business interfaces:
  - `dim_customers` (or `customers` alias)
  - `fact_orders` (or `orders` alias)
- Add optional intermediate models only when needed:
  - `int_order_payments` for reusable payment aggregation
  - `int_customer_order_rollup` for customer lifecycle stats
- Preserve clear ownership:
  - Staging: rename/recast/standardize only
  - Intermediate: reusable business transformations
  - Marts: conformed facts/dimensions for downstream use

## Completeness Check
- Grain defined for each entity: Yes
- PK defined for each entity: Yes
- FK relationships stated: Yes
- Dedupe placement explicit: Yes
- SCD policy explicit: Yes (current-state; SCD2 optional)
