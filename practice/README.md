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

🧩 Models in dbt

In dbt, models are .sql files that live inside the models/ directory. Each model is written purely as a select statement—there is no need to write any explicit DDL or DML. This design allows analytics engineers to focus entirely on transformation logic rather than database mechanics.

In dbt Studio, the Preview button executes the model’s select statement directly against the data warehouse. The previewed result set is exactly what the model will return once it is materialized.

When dbt run is executed from the command line, dbt materializes these models into the data warehouse. By default, models are materialized as views, but this behavior can be customized.

Materializations

Materialization can be configured at the model level using a config block at the top of the SQL file.

To materialize a model as a table:

{{ config(
    materialized='table'
) }}


To materialize a model as a view:

{{ config(
    materialized='view'
) }}


During execution, dbt automatically wraps the select statement with the appropriate DDL/DML required to build the table or view. If the model already exists in the warehouse, dbt will drop and recreate it.
Note: On BigQuery, a dbt run --full-refresh may be required for this behavior.

All compiled SQL and executed DDL/DML statements can be inspected via the dbt logs or inside the target/ directory.

🧱 Modularity in dbt

While it is possible to build final models in a single SQL file (as demonstrated with dim_customers), dbt strongly encourages a modular approach to data modeling.

Modularity refers to designing systems whose components can be independently built, tested, and recombined. In analytics engineering, this means constructing data artifacts in logical, incremental steps.

For example:

Raw customer and order data are first staged and standardized

Downstream models then reference these staging models

Final dimension or fact tables are built by composing these modular components

This mirrors how software engineers build applications and makes analytics pipelines more maintainable, testable, and scalable.

🔗 The ref() Macro

Hardcoding database object names (e.g. analytics.dbt_jsmith.stg_jaffle_shop_customers) makes dbt projects difficult to share across environments and team members.

The ref() macro solves this problem by creating flexible, environment-aware references between models.

Example:

{{ ref('stg_jaffle_shop_customers') }}


This compiles to the fully qualified relation name in the target environment, such as:

analytics.dbt_jsmith.stg_jaffle_shop_customers


Beyond abstraction, ref() enables dbt to:

Automatically infer dependencies between models

Build models in the correct execution order

Generate a lineage graph showing upstream and downstream relationships

🕰️ Modeling History & Approach

Traditional data modeling approaches were largely normalized, designed for environments where storage was expensive and compute was limited.

Modern cloud data warehouses invert these constraints: storage is cheap and compute is abundant. As a result, analytics teams often favor denormalized, analytics-friendly schemas that support fast querying and ad hoc analysis.

dbt does not enforce a specific modeling paradigm. Instead, it provides the tooling to implement any approach—normalized, denormalized, or hybrid—based on business needs.

📛 Naming Conventions

This project follows common dbt naming conventions to clearly communicate intent and layer responsibility.

Sources (src)
Raw tables loaded into the warehouse via ingestion pipelines.

Staging (stg)
Models built directly on top of sources with a one-to-one relationship.
Used for light transformations such as renaming, casting, and basic cleaning.
Typically materialized as views.

Intermediate (int)
Models that sit between staging and final marts.
Built on staging models to leverage standardized data.

Fact (fct)
Tables that represent events or transactions (e.g. orders, payments).
Usually long and narrow.

Dimension (dim)
Tables that represent entities such as customers, products, or employees.

While the fact/dimension terminology originates from normalized modeling, it remains highly effective for analytical use cases.

🗂️ Project Organization

When dbt run is executed, dbt builds all models in the models/ directory by default.
Subfolder structure is therefore a key organizational tool.

This project uses the following framework:

models/staging
Contains all staging models and source configurations
Subfolders are organized by data source (e.g. jaffle_shop, stripe)

models/marts
Contains intermediate, fact, and dimension models
Subfolders are organized by business domain (e.g. marketing, finance)

Folder structure can also be leveraged by dbt selectors:

dbt run -s staging


This command runs only models located under models/staging.
The same pattern applies to dbt test and other dbt commands.
