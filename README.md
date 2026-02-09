Welcome to your new dbt project!

### Using the starter project

Try running the following commands:
- dbt build

# Example dbt Project

## Overview

This dbt project transforms raw e-commerce data into clean, analytics-ready tables by following a layered approach:

- Staging Layer → Standardises raw data
- Intermediate Layer → Aggregates and processes data
- Marts Layer → Provides final business-ready tables

## Project Structure

```bash
ecommerce_project/
│-- models/
│   ├── staging/      
│   │   ├── stg_orders.sql
│   │   ├── stg_customers.sql
│   │   ├── stg_payments.sql
│   │   ├── sources.yml
│   │   └── schema.yml
│   ├── intermediate/   
│   │   ├── int_order_summary.sql
│   │   ├── int_payment_summary.sql
│   │   └── schema.yml
│   ├── marts/         
│   │   ├── mart_customer_lifetime_value.sql
│   │   ├── mart_orders_payments.sql
│   │   └── schema.yml
│   ├── data/
│   │   ├── orders.csv
│   │   ├── customers.csv
│   │   ├── payments.csv
│   ├── analyses/  
│   ├── macros/  
│   ├── seeds/  
│   ├── snapshots/ 
│   ├── tests/ 
│-- .gitignore 
│-- dbt_project.yml
│-- profiles.yml
│-- README.md
```

## Installation & Setup

1. Install dbt:

```bash
pip install dbt-core dbt-postgres
```

2. Configure profiles.yml(update database credentials):

```yaml
ecommerce_project:
  target: dev
  outputs:
    dev:
      type: postgres
      host: localhost
      user: my_user
      password: my_password
      port: 5432
      dbname: my_db
      schema: public
```

3. Test the connection:

```bash
dbt debug
```

4. Run the models:

```bash
dbt run
```

5. Run the tests:

```bash
dbt test
```

6. Generate and serve documentation:

```bash
dbt docs generate
dbt docs serve
```


### Resources:
- Learn more about dbt [in the docs](https://docs.getdbt.com/docs/introduction)
- Check out [Discourse](https://discourse.getdbt.com/) for commonly asked questions and answers
- Join the [chat](http://slack.getdbt.com/) on Slack for live discussions and support
- Find [dbt events](https://events.getdbt.com) near you
- Check out [the blog](https://blog.getdbt.com/) for the latest news on dbt's development and best practices
