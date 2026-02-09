📦 Quick Project Polishing – dbt Jaffle Shop Project

This project demonstrates a complete dbt workflow including staging, marts, and fact modeling using the classic jaffle_shop dataset and Stripe payments data.

🔧 Project Configuration
dbt_project.yml Updates

Updated the project name in two locations (line 5 and line 35) to match the repository name.

Configured model materializations by layer:

models:
  jaffle_shop:
    staging:
      +materialized: view
    marts:
      +materialized: table

🧱 Staging Layer

All staging models are materialized as views and serve as a clean interface over raw source data.

Directory Structure
models/
├── staging/
│   ├── jaffle_shop/
│   │   ├── stg_jaffle_shop__customers.sql
│   │   └── stg_jaffle_shop__orders.sql
│   └── stripe/
│       └── stg_stripe__payments.sql

stg_jaffle_shop__customers.sql
select
    id as customer_id,
    first_name,
    last_name
from raw.jaffle_shop.customers

stg_jaffle_shop__orders.sql
select
    id as order_id,
    user_id as customer_id,
    order_date,
    status
from raw.jaffle_shop.orders

stg_stripe__payments.sql

Built by inspecting raw.stripe.payment and standardizing fields required for downstream fact modeling.

🏗️ Mart Layer

Mart models are materialized as tables and optimized for analytics use cases.

Directory Structure
models/
└── marts/
    ├── marketing/
    │   └── dim_customers.sql
    └── finance/
        └── fct_orders.sql

📊 dim_customers Model

Customer dimension enriched with behavioral and financial metrics.

Fields

customer_id

first_name

last_name

first_order_date

most_recent_order_date

number_of_orders

lifetime_value

Business Logic

Aggregates order history per customer

Joins customer attributes with order metrics

Calculates lifetime_value as total spend per customer

📌 Validation check
Total lifetime value across all customers:

$1,672

💰 fct_orders Model

Fact table representing financial performance at the order level.

Fields

order_id

customer_id

amount

Notes

amount is sourced from Stripe payments data

Model is built in the marts/finance directory

Designed for revenue, LTV, and cohort analyses

🧠 Modeling Principles Applied

Clear separation of staging, dimensions, and facts

Consistent naming conventions (stg_, dim_, fct_)

Reusable transformations using ref()

Analytics-ready schemas with minimal business logic leakage

✅ Outcome

This project results in a clean, layered dbt architecture that supports:

Customer analytics

Revenue and lifetime value analysis

Scalable and testable transformations
