# Comprehensive DBT Interview Questions by Topic

## Table of Contents

1. [Core DBT Fundamentals](#core-dbt-fundamentals)
2. [DBT Models and Materializations](#dbt-models-and-materializations)
3. [DBT Testing](#dbt-testing)
4. [DBT Macros and Jinja](#dbt-macros-and-jinja)
5. [DBT Sources and Seeds](#dbt-sources-and-seeds)
6. [DBT Snapshots](#dbt-snapshots)
7. [DBT Project Organization and Best Practices](#dbt-project-organization-and-best-practices)
8. [DBT Configuration and Deployment](#dbt-configuration-and-deployment)
9. [DBT Performance Optimization](#dbt-performance-optimization)
10. [DBT Testing and Debugging](#dbt-testing-and-debugging)
11. [DBT with Data Warehouses](#dbt-with-data-warehouses)
12. [DBT Documentation and Collaboration](#dbt-documentation-and-collaboration)
13. [Advanced DBT Concepts](#advanced-dbt-concepts)
14. [Scenario-Based DBT Questions](#scenario-based-dbt-questions)
15. [DBT Monitoring and Observability](#dbt-monitoring-and-observability)
16. [DBT Integration and Ecosystem](#dbt-integration-and-ecosystem)

---

## Core DBT Fundamentals

### 1. What is DBT and how does it fit into the modern data stack?
**Answer:** DBT (Data Build Tool) is a transformation framework that enables data analysts and engineers to transform data in their warehouse using SQL and software engineering best practices. It fits in the modern data stack as the "T" in ELT: Extract (tools like Fivetran, Stitch), Load (into warehouses like Snowflake, BigQuery), Transform (DBT). DBT handles data modeling, testing, and documentation while leveraging the warehouse's compute power.

### 2. Explain the ELT paradigm and how DBT enables it
**Answer:** ELT (Extract, Load, Transform) loads raw data into the warehouse first, then transforms it, leveraging the warehouse's processing power. DBT enables ELT by providing a framework to write modular SQL transformations that compile into efficient SQL queries executed directly in the warehouse. This approach is faster and more scalable than traditional ETL tools that transform data outside the warehouse.

### 3. What are the main components of a DBT project?
**Answer:** Main components include: **Models** (SQL files defining transformations), **Tests** (data quality validations), **Snapshots** (Type 2 SCD implementation), **Seeds** (CSV files for reference data), **Macros** (reusable SQL functions), **Sources** (raw data declarations), **Analyses** (ad-hoc queries), **Documentation** (descriptions and metadata), and **dbt_project.yml** (project configuration).

### 4. Describe the DBT project structure and key directories (models, tests, macros, seeds)
**Answer:** 
- **models/**: SQL files defining transformations, organized by layers (staging, intermediate, marts)
- **tests/**: Singular tests as SQL files
- **macros/**: Jinja macros for reusable logic
- **seeds/**: CSV files for static reference data
- **snapshots/**: Snapshot definitions for SCD Type 2
- **analyses/**: Ad-hoc analytical queries
- **target/**: Compiled SQL and artifacts (gitignored)
- **dbt_project.yml**: Project configuration at root

### 5. What is the purpose of the dbt_project.yml file?
**Answer:** The dbt_project.yml configures the entire DBT project including: project name and version, model paths and materialization defaults, test configurations, variable definitions, documentation paths, package dependencies, model-specific configs organized by directory, and dispatch configurations for cross-database compatibility.

### 6. How does DBT handle version control and collaboration?
**Answer:** DBT projects are plain text files (SQL, YAML, Jinja) ideal for Git version control. Teams use Git workflows (branches, pull requests, code reviews) to collaborate. DBT Cloud offers built-in Git integration with IDE, while DBT Core users can use any Git workflow. Model lineage and documentation help teams understand dependencies and impacts of changes.

### 7. What are DBT packages and how do you use them?
**Answer:** DBT packages are reusable DBT projects that can be imported into your project. Install by declaring in `packages.yml`, then run `dbt deps`. Common packages include dbt_utils (utility macros), dbt_expectations (data quality tests), and source-specific packages (e.g., snowflake_spend). Packages provide macros, models, and tests without reinventing common functionality.

### 8. Explain the difference between DBT Core and DBT Cloud
**Answer:** 
- **DBT Core**: Open-source CLI tool, runs locally or in custom infrastructure, requires manual orchestration, free
- **DBT Cloud**: SaaS platform with web IDE, job scheduling, logging/alerting, documentation hosting, integrated development environment, metadata API, and managed infrastructure. Offers both free and paid tiers.

### 9. What are the benefits of using DBT over traditional ETL tools?
**Answer:** Benefits include: version control for transformations, SQL-based (accessible to analysts), automated testing and documentation, separation of concerns (transformation logic vs orchestration), leverage warehouse compute, modular and reusable code, open-source with strong community, faster development cycles, and built-in lineage tracking.

### 10. How does DBT generate documentation automatically?
**Answer:** DBT generates documentation by parsing project files (models, tests, sources) and YAML descriptions to create a browsable website. Run `dbt docs generate` to create manifest and catalog, then `dbt docs serve` to launch locally. Documentation includes model descriptions, column details, lineage DAG, test results, and source freshness, all searchable and interactive.

## DBT Models and Materializations

### 1. What are DBT models and how do you create them?
**Answer:** DBT models are SQL SELECT statements that define data transformations. Create by adding a `.sql` file in the models directory. Each model becomes a table/view in the warehouse. Models reference other models using `{{ ref('model_name') }}` to build dependencies. Configuration can be in-file using `{{ config() }}` or in schema.yml.

### 2. Explain the different materialization types in DBT (view, table, incremental, ephemeral)
**Answer:** 
- **View**: Creates database view, no data stored, queries run on-demand, fast to build
- **Table**: Creates physical table, data persisted, slower build but faster queries
- **Incremental**: Appends/updates only new records, efficient for large datasets
- **Ephemeral**: Interpolated as CTE in dependent models, no database object created

### 3. When would you use a view materialization versus a table materialization?
**Answer:** Use **view** for: lightweight transformations, always current data, storage constraints, development/testing. Use **table** for: complex transformations, frequently queried models, performance-critical queries, aggregations, or when query speed matters more than build time.

### 4. How do incremental models work and when should you use them?
**Answer:** Incremental models process only new/changed data since last run. On first run, builds full table. Subsequent runs use `is_incremental()` macro to filter new data and merge/append. Use for: large fact tables, event logs, immutable data, or when full rebuilds are too slow. Requires `unique_key` for updates.

```sql
{{ config(materialized='incremental', unique_key='id') }}

select * from {{ source('raw', 'events') }}
{% if is_incremental() %}
  where event_timestamp > (select max(event_timestamp) from {{ this }})
{% endif %}
```

### 5. What is an ephemeral materialization and what are its use cases?
**Answer:** Ephemeral models don't create database objects; they're compiled as CTEs in dependent models. Use for: intermediate transformations, reducing table clutter, when model is only referenced by one downstream model, or DRY principle without database overhead. Cannot be queried directly or used as source for multiple models efficiently.

### 6. How do you configure materialization at the project, folder, and model level?
**Answer:** 
- **Project level**: In dbt_project.yml under models section
- **Folder level**: In dbt_project.yml using directory paths
- **Model level**: In-file `{{ config(materialized='table') }}` or in schema.yml

Model-level config overrides folder-level, which overrides project-level.

### 7. What are the performance implications of each materialization type?
**Answer:** 
- **View**: Fast build (seconds), slow query (runs each time), no storage cost
- **Table**: Slow build (minutes-hours), fast query, storage cost
- **Incremental**: Fast build after initial (seconds-minutes), fast query, storage cost, complexity in handling updates
- **Ephemeral**: No build, query performance depends on complexity and usage

### 8. How do you handle schema changes in incremental models?
**Answer:** Use `on_schema_change` config: 
- `ignore`: Keep existing schema (default)
- `fail`: Error on mismatch
- `append_new_columns`: Add new columns
- `sync_all_columns`: Add new, remove old columns

For major changes, perform full refresh with `dbt run --full-refresh` or `dbt run --full-refresh --select model_name`.

### 9. Explain the unique_key parameter in incremental models
**Answer:** The unique_key identifies rows for updates in incremental models. Can be single column or list of columns. When new data arrives with existing unique_key, DBT updates the row instead of inserting duplicate. Without unique_key, DBT only appends. Essential for maintaining data integrity in incremental models handling updates.

### 10. What is the purpose of the is_incremental() macro?
**Answer:** `is_incremental()` returns true when model is incremental and not doing full refresh. Use it to add filtering logic that processes only new data. Returns false on first run or when `--full-refresh` flag is used, causing full table rebuild.

### 11. How do you implement a full refresh for incremental models?
**Answer:** Run `dbt run --full-refresh` to rebuild all incremental models from scratch, or `dbt run --full-refresh --select model_name` for specific model. Useful when: fixing data issues, schema changes, or logic changes affecting historical data. Can also use `full_refresh: true` in model config.

### 12. What strategies can you use to optimize incremental model performance?
**Answer:** Use appropriate unique_key, implement efficient filtering (timestamp-based), partition large tables, use proper incremental strategy (append, merge, delete+insert), add clustering/partitioning in warehouse, handle late-arriving data, monitor and tune batch sizes, and consider separate models for inserts vs updates if patterns differ.

## DBT Testing

### 1. What are the different types of tests available in DBT?
**Answer:** DBT has two main test types: **Generic tests** (reusable tests defined in YAML, applied to models/columns), and **Singular tests** (custom SQL queries in tests/ directory). Generic tests include built-in (unique, not_null, accepted_values, relationships) and custom tests. Singular tests are one-off SQL assertions specific to business logic.

### 2. Explain the difference between schema tests and data tests
**Answer:** **Schema tests** (now called generic tests) are defined in schema.yml files and test column-level or model-level properties (unique, not_null, etc.). **Data tests** (now called singular tests) are SQL files in the tests/ directory that check specific business rules. Schema tests are reusable; data tests are specific queries.

### 3. What are the built-in generic tests in DBT?
**Answer:** 
- **unique**: Ensures column has no duplicate values
- **not_null**: Ensures column has no NULL values
- **accepted_values**: Column values must be in specified list
- **relationships**: Foreign key validation, values must exist in referenced table

### 4. How do you create custom schema tests?
**Answer:** Create a macro in macros/ directory that returns rows violating the test (test fails if query returns rows). Define in tests/ or macros/schema_tests/. Example:

```sql
{% macro test_is_positive(model, column_name) %}
select * from {{ model }}
where {{ column_name }} <= 0
{% endmacro %}
```

Apply in schema.yml:
```yaml
models:
  - name: orders
    columns:
      - name: amount
        tests:
          - is_positive
```

### 5. What are singular tests (formerly data tests) and when would you use them?
**Answer:** Singular tests are SQL queries in tests/ directory that return failing rows. Use for: complex business logic, cross-table validation, custom aggregation checks, or one-time assertions not worth generalizing. Test passes if query returns zero rows.

### 6. How do you write custom data quality tests?
**Answer:** Write SQL query that selects records violating the rule. Save in tests/ directory with descriptive name. Test fails if any rows returned. Example:

```sql
-- tests/assert_positive_revenue.sql
select order_id, revenue
from {{ ref('fct_orders') }}
where revenue < 0
```

### 7. What is the purpose of test severity levels?
**Answer:** Severity levels (warn, error) determine test failure behavior. **error** (default) causes `dbt test` to exit with error code, failing CI/CD. **warn** logs warning but doesn't fail build, useful for soft validations. Configure in schema.yml or dbt_project.yml.

### 8. How do you implement tests that run on specific conditions?
**Answer:** Use `where` config to filter test scope, or create custom test macros with conditional logic using Jinja. Example:

```yaml
models:
  - name: orders
    columns:
      - name: status
        tests:
          - accepted_values:
              values: ['pending', 'completed']
              where: "order_date >= '2024-01-01'"
```

### 9. Explain how to use the store_failures configuration
**Answer:** `store_failures` saves failing test records to database table for analysis. Configure globally in dbt_project.yml or per-test. When enabled, DBT creates table with failing rows. Useful for debugging and tracking test failures over time.

```yaml
tests:
  +store_failures: true
  +schema: dbt_test_failures
```

### 10. What metrics would you track to measure data quality in DBT?
**Answer:** Track: test pass/fail rates, test execution time, model freshness, number of records processed, data volume trends, schema change frequency, incremental model performance, test coverage per model, and failure patterns. Use run_results.json and sources.json for monitoring.

### 11. How do you handle failing tests in production?
**Answer:** Strategies include: set severity to 'warn' for non-critical tests, implement alerting via DBT Cloud or external tools, investigate and fix root cause, use store_failures to analyze failures, implement data quality SLAs, create runbooks for common failures, and consider rolling back recent changes if tests suddenly fail.

### 12. What is the difference between warn and error severity?
**Answer:** **error**: Test failure causes dbt test to exit with non-zero code, failing CI/CD pipelines and blocking deployments. **warn**: Test failure logs warning message but dbt test exits successfully, allowing deployment while flagging issues for investigation. Use warn for aspirational tests or during migration periods.

## DBT Macros and Jinja

### 1. What are macros in DBT and why are they useful?
**Answer:** Macros are reusable Jinja functions that generate SQL code. They enable DRY principles, encapsulate complex logic, support cross-database compatibility, and allow dynamic SQL generation. Defined in macros/ directory and called using `{{ macro_name() }}`. Built-in macros like ref(), source(), and config() are fundamental to DBT.

### 2. How do you create and use custom macros?
**Answer:** Create .sql file in macros/ directory with macro definition:

```sql
{% macro cents_to_dollars(column_name) %}
    ({{ column_name }} / 100)::decimal(10,2)
{% endmacro %}
```

Use in models:
```sql
select
    order_id,
    {{ cents_to_dollars('amount_cents') }} as amount_dollars
from {{ ref('orders') }}
```

### 3. Explain how Jinja templating works in DBT
**Answer:** Jinja is a Python templating language that DBT uses to generate SQL. DBT compiles Jinja templates into pure SQL before execution. Supports variables, control structures (if/for), macros, filters, and expressions. Enables dynamic SQL generation based on configuration, environment, or data.

### 4. What are some common use cases for macros?
**Answer:** Common uses include: standardized date logic, environment-specific SQL, cross-database compatibility, dynamic column generation, reusable business calculations, grant statements, custom materializations, audit columns (created_at, updated_at), handling NULL values, and wrapping complex transformations.

### 5. How do you use variables in DBT with Jinja?
**Answer:** Define variables in dbt_project.yml:
```yaml
vars:
  start_date: '2024-01-01'
  exclude_test_users: true
```

Access in models:
```sql
select * from events
where event_date >= '{{ var("start_date") }}'
{% if var("exclude_test_users") %}
  and is_test_user = false
{% endif %}
```

Override with CLI: `dbt run --vars '{"start_date": "2024-06-01"}'`

### 6. What is the difference between {% %} and {{ }} in Jinja?
**Answer:** 
- `{% %}`: **Statements** for control flow (if, for, set, macro). Execute logic but don't render output directly.
- `{{ }}`: **Expressions** that render values into compiled SQL. Outputs result of evaluation.

Example: `{% if condition %}` (control) vs `{{ ref('model') }}` (output).

### 7. How do you implement conditional logic using Jinja in DBT?
**Answer:** Use Jinja if-statements:

```sql
select
    order_id,
    {% if target.name == 'prod' %}
        revenue
    {% else %}
        revenue * 0.1 as revenue  -- sampled in dev
    {% endif %}
from {{ ref('orders') }}
where created_at >= '2024-01-01'
{% if var('include_cancelled', false) == false %}
    and status != 'cancelled'
{% endif %}
```

### 8. What are context variables in DBT and how do you access them?
**Answer:** Context variables provide runtime information: **target** (connection details), **env_var** (environment variables), **var** (custom variables), **this** (current model), **flags** (CLI flags). Access using `{{ target.name }}`, `{{ env_var('KEY') }}`, etc. Available during compilation.

### 9. How do you create reusable SQL snippets using macros?
**Answer:** Define macro with parameters and use across models:

```sql
{% macro fiscal_year(date_column) %}
    case
        when extract(month from {{ date_column }}) >= 7
        then extract(year from {{ date_column }}) + 1
        else extract(year from {{ date_column }})
    end
{% endmacro %}
```

Use: `select {{ fiscal_year('order_date') }} as fiscal_year`

### 10. Explain the ref() and source() functions
**Answer:** 
- **ref('model_name')**: References another DBT model, creates dependency graph, resolves to correct schema/table name based on target
- **source('source_name', 'table_name')**: References raw data tables defined in sources.yml, enables source freshness checks

Both enable dependency tracking and automatic schema resolution.

### 11. What is the this variable in DBT?
**Answer:** `{{ this }}` represents the current model's database object (schema.table). Useful in incremental models, pre/post-hooks, and when model needs to reference itself. Example: `select max(updated_at) from {{ this }}`. Only available during model execution, not compilation.

### 12. How do you use macros to generate dynamic SQL?
**Answer:** Use loops and conditionals to generate SQL based on metadata:

```sql
{% set payment_methods = ['credit_card', 'paypal', 'bank_transfer'] %}

select
    order_id,
    {% for method in payment_methods %}
    sum(case when payment_method = '{{ method }}' then amount else 0 end) as {{ method }}_total
    {{ "," if not loop.last }}
    {% endfor %}
from {{ ref('payments') }}
group by 1
```

### 13. What are dispatch macros and when would you use them?
**Answer:** Dispatch macros enable cross-database compatibility by calling different macro implementations based on adapter. DBT selects appropriate version for current warehouse. Use for SQL dialect differences. Example: dbt_utils.dateadd() works across Snowflake, BigQuery, Redshift with adapter-specific implementations.

## DBT Sources and Seeds

### 1. What are sources in DBT and how do you define them?
**Answer:** Sources represent raw data tables in your warehouse loaded by external tools. Define in schema.yml files to document, test, and track freshness. Enables lineage from raw to transformed data. Example:

```yaml
sources:
  - name: jaffle_shop
    database: raw
    schema: public
    tables:
      - name: customers
      - name: orders
```

Reference with `{{ source('jaffle_shop', 'customers') }}`.

### 2. How do you configure source freshness checks?
**Answer:** Add freshness configuration to source tables:

```yaml
sources:
  - name: jaffle_shop
    tables:
      - name: orders
        freshness:
          warn_after: {count: 12, period: hour}
          error_after: {count: 24, period: hour}
        loaded_at_field: created_at
```

Run `dbt source freshness` to check. Useful for monitoring data pipeline health.

### 3. What is the purpose of source() function?
**Answer:** The source() function references raw data tables defined in sources.yml. Benefits: documents data lineage, enables freshness monitoring, separates raw from transformed data, allows testing on source tables, and resolves to correct database/schema/table name. Syntax: `{{ source('source_name', 'table_name') }}`.

### 4. How do you document sources in DBT?
**Answer:** Add descriptions in sources.yml:

```yaml
sources:
  - name: jaffle_shop
    description: Raw data from production Jaffle Shop database
    tables:
      - name: customers
        description: Customer master data
        columns:
          - name: customer_id
            description: Primary key for customers
```

Documentation appears in dbt docs site with full lineage.

### 5. What are seeds in DBT and when should you use them?
**Answer:** Seeds are CSV files in seeds/ directory loaded into warehouse as tables using `dbt seed`. Use for: small reference data (country codes, mapping tables), static configurations, lookup tables. Not for large datasets or frequently changing data. Stored in version control alongside code.

### 6. What are the limitations of seeds?
**Answer:** Limitations: not suitable for large files (>1MB), slow load performance, no incremental loading, stored in Git (repo size concerns), manual updates required, no automatic freshness monitoring. Better alternatives for large/dynamic data: sources with regular ETL, or DBT models loading from stages.

### 7. How do you load seed files into your data warehouse?
**Answer:** Run `dbt seed` to load all CSV files from seeds/ directory, or `dbt seed --select seed_name` for specific files. DBT creates table based on CSV structure. Seed runs are idempotent (full refresh each time). Configure in dbt_project.yml for custom schemas or column types.

### 8. What file formats are supported for seeds?
**Answer:** Seeds support only CSV format. Files must be in seeds/ directory with .csv extension. First row should contain column headers. Values should be properly quoted if containing delimiters. For other formats (JSON, Parquet), use external loading tools and define as sources.

### 9. How do you override seed column types?
**Answer:** Configure in dbt_project.yml:

```yaml
seeds:
  my_project:
    country_codes:
      +column_types:
        country_code: varchar(2)
        population: bigint
```

Useful for controlling data types, especially for numeric codes that should be strings or precision requirements.

### 10. What is source freshness and how do you monitor it?
**Answer:** Source freshness monitors how recently source data was updated. Configure warn_after and error_after thresholds with loaded_at_field timestamp column. Run `dbt source freshness` to check all sources. Results in sources.json. Integrate with orchestration tools to alert on stale data. Critical for SLA monitoring.

## DBT Snapshots

### 1. What are snapshots in DBT and what problem do they solve?
**Answer:** Snapshots implement Type 2 Slowly Changing Dimensions (SCD) to track historical changes in mutable source data. They capture the state of a table over time by adding validity timestamps. Solve the problem of losing historical data when source records are updated or deleted. Essential for compliance, auditing, and historical analysis.

### 2. How do you implement Type 2 Slowly Changing Dimensions using snapshots?
**Answer:** Create snapshot file in snapshots/ directory:

```sql
{% snapshot orders_snapshot %}
{{
    config(
      target_schema='snapshots',
      unique_key='order_id',
      strategy='timestamp',
      updated_at='updated_at'
    )
}}
select * from {{ source('jaffle_shop', 'orders') }}
{% endsnapshot %}
```

Run `dbt snapshot`. DBT adds dbt_valid_from, dbt_valid_to, dbt_updated_at columns.

### 3. Explain the difference between timestamp and check strategy for snapshots
**Answer:** 
- **timestamp**: Uses updated_at column to detect changes. More efficient, requires reliable timestamp in source. Best for tables with maintained updated_at columns.
- **check**: Compares all columns (or specified columns) to detect changes. Works without timestamp column but more resource-intensive. Use when source lacks reliable updated_at field.

### 4. What are the required columns in a snapshot table?
**Answer:** DBT automatically adds:
- **dbt_valid_from**: When record became current
- **dbt_valid_to**: When record was superseded (NULL for current)
- **dbt_updated_at**: Timestamp of snapshot run
- **dbt_scd_id**: Unique identifier for snapshot record

Source table must have unique_key to identify records across snapshots.

### 5. When would you use snapshots versus incremental models?
**Answer:** Use **snapshots** for: tracking changes in mutable data, maintaining history of updates/deletes, SCD Type 2, auditing source changes. Use **incremental models** for: immutable event data, append-only scenarios, large fact tables, performance optimization. Snapshots preserve history; incrementals optimize processing.

### 6. How do you handle hard deletes with snapshots?
**Answer:** Snapshots capture hard deletes by setting dbt_valid_to when record disappears from source. Configure `invalidate_hard_deletes: true` (default behavior) to mark deleted records. Query current state with `where dbt_valid_to is null`. Historical queries use valid_from/valid_to ranges.

### 7. What are the performance considerations for snapshots?
**Answer:** Snapshots compare current source to existing snapshot, which can be slow for large tables. Considerations: timestamp strategy is faster than check, indexed unique_key improves performance, frequent snapshots increase storage, partition snapshot tables by dbt_valid_from, limit columns in check strategy, and schedule snapshots appropriately (not every hour if daily is sufficient).

### 8. How do you rebuild or invalidate snapshots?
**Answer:** Snapshots are immutable by design. To rebuild: rename/drop existing snapshot table and re-run `dbt snapshot` (loses history). To invalidate specific records: manually update dbt_valid_to. For schema changes: hard refresh destroys history. Plan snapshot schema carefully. Use check strategy with specific columns for flexibility.

## DBT Project Organization and Best Practices

### 1. How would you structure a DBT project with multiple teams?
**Answer:** Use modular approach: separate folders by team/domain (marketing/, finance/, product/), shared staging layer for common sources, team-specific marts, centralized macros in shared/ folder, clear ownership in CODEOWNERS file, use DBT packages for cross-team dependencies, and implement naming conventions with team prefixes (mkt_customers, fin_revenue).

### 2. Explain the staging, intermediate, and mart layer pattern
**Answer:** 
- **Staging (stg_)**: One-to-one with sources, light transformations (renaming, casting, deduplication), materialized as views
- **Intermediate (int_)**: Complex business logic, joins, aggregations, not exposed to end users, ephemeral or views
- **Marts (fct_/dim_)**: Final analytical models, fact and dimension tables, optimized for BI tools, materialized as tables/incremental

This pattern follows separation of concerns and enables modularity.

### 3. What naming conventions do you recommend for DBT models?
**Answer:** 
- **Staging**: stg_[source]__[entity] (stg_jaffle__customers)
- **Intermediate**: int_[entity]__[verb] (int_orders__joined)
- **Facts**: fct_[entity] (fct_orders)
- **Dimensions**: dim_[entity] (dim_customers)
- Use double underscore to separate concepts, singular nouns for entities, descriptive verbs for transformations, and consistent snake_case.

### 4. How do you manage dependencies between models?
**Answer:** DBT automatically manages dependencies through ref() and source() functions. Best practices: keep DAG acyclic, minimize cross-layer dependencies, use intermediate models to break complex dependencies, avoid circular references, leverage DBT's selector syntax for managing subgraphs, and visualize with `dbt docs generate` to review lineage.

### 5. What is model lineage and why is it important?
**Answer:** Model lineage shows the dependency graph from sources through transformations to final models. Important for: understanding data flow, impact analysis when changing models, debugging data issues, documenting transformations, onboarding new team members, and compliance/governance. View in DBT docs DAG visualization.

### 6. How do you implement modular DBT projects?
**Answer:** Use DBT packages for reusable logic, organize models by business domain, create internal packages for shared code, use project variables for configuration, implement clear interfaces between layers, document dependencies, use tags for logical grouping, and leverage dbt_project.yml for modular configuration by directory.

### 7. What strategies would you use to organize models by business domain?
**Answer:** Create domain-specific folders (marketing/, sales/, finance/), maintain separate staging layers per domain, use domain prefixes in model names, assign domain ownership, create domain-specific documentation, implement domain marts, use tags for cross-domain tracking, and consider separate DBT projects for very large organizations.

### 8. How do you handle shared logic across multiple models?
**Answer:** Create reusable macros in macros/ directory, use ephemeral models for shared intermediate logic, leverage DBT packages for cross-project sharing, implement naming conventions for shared models, create utility macros for common transformations, and document shared logic thoroughly to prevent breaking changes.

### 9. What is the purpose of the intermediate layer?
**Answer:** Intermediate models contain complex business logic that's reused by multiple marts, break down complex transformations into manageable steps, isolate business rules from final models, improve testability, make DAG more maintainable, and serve as documentation of transformation logic. Typically not exposed to end users.

### 10. How do you manage technical debt in DBT projects?
**Answer:** Regular refactoring sprints, maintain TODO comments with tickets, monitor test coverage metrics, deprecate unused models, consolidate duplicate logic into macros, improve documentation continuously, enforce code review standards, use linters (SQLFluff), track model runtime and optimize slow models, and schedule time for cleanup alongside feature work.

## DBT Configuration and Deployment

### 1. How do you configure different environments (dev, staging, prod) in DBT?
**Answer:** Use profiles.yml to define multiple targets (dev, staging, prod) with different database/schema configurations. Switch environments using `--target` flag or DBT_TARGET environment variable. Use `{{ target.name }}` in models for environment-specific logic. Configure separate schemas per environment to isolate data.

```yaml
my_project:
  target: dev
  outputs:
    dev:
      type: snowflake
      schema: dbt_dev
    prod:
      type: snowflake
      schema: analytics
```

### 2. What are profiles in DBT and how do you configure them?
**Answer:** Profiles define database connection credentials and configuration. Stored in ~/.dbt/profiles.yml (not in project repo for security). Contains targets (environments), connection type, credentials, and warehouse-specific settings. Each project references a profile name in dbt_project.yml.

### 3. How do you use environment variables in DBT?
**Answer:** Reference with `{{ env_var('VARIABLE_NAME') }}` or `{{ env_var('VAR', 'default_value') }}`. Common use: credentials in profiles.yml, feature flags, environment-specific configs. Example:

```yaml
outputs:
  prod:
    type: snowflake
    account: "{{ env_var('SNOWFLAKE_ACCOUNT') }}"
    password: "{{ env_var('SNOWFLAKE_PASSWORD') }}"
```

### 4. Explain the purpose of target in profiles.yml
**Answer:** Target specifies which environment configuration to use when running DBT. Contains connection details (database, schema, warehouse size), credentials, and environment-specific settings. Access target properties in models using `{{ target.name }}`, `{{ target.schema }}`, etc. Enables environment-aware logic.

### 5. How do you implement CI/CD pipelines for DBT?
**Answer:** Typical pipeline: on PR, run `dbt run --select state:modified+` (slim CI), execute `dbt test`, generate docs, lint SQL. On merge to main, run full `dbt build` in production, run source freshness checks, deploy docs to hosting. Use DBT Cloud, GitHub Actions, GitLab CI, or Airflow for orchestration.

### 6. What is the dbt run command and what does it do?
**Answer:** `dbt run` executes all models (or selected models) in dependency order, compiling Jinja to SQL and running in warehouse. Creates/replaces tables/views based on materialization. Returns success/failure status. Common flags: `--select`, `--exclude`, `--full-refresh`, `--threads`, `--target`.

### 7. How do you use model selection syntax (--select, --exclude)?
**Answer:** Select specific models and dependencies:
- `--select model_name`: Single model
- `--select tag:daily`: Models with tag
- `--select +model_name`: Model and all upstream
- `--select model_name+`: Model and all downstream
- `--select path/to/models`: All in folder
- `--exclude tag:deprecated`: Exclude tagged models

Combine with commas or multiple flags.

### 8. What are tags in DBT and how do you use them?
**Answer:** Tags are labels for organizing and selecting models. Define in config or schema.yml:

```yaml
models:
  - name: fct_orders
    config:
      tags: ["daily", "finance", "pii"]
```

Select with `dbt run --select tag:daily`. Use for: scheduling groups, ownership, data sensitivity, refresh frequency, or any logical grouping.

### 9. How do you implement pre-hooks and post-hooks?
**Answer:** Hooks run SQL before or after model execution. Configure in model or dbt_project.yml:

```sql
{{ config(
    pre_hook="insert into audit_log values (current_timestamp, 'started')",
    post_hook="grant select on {{ this }} to role analyst"
) }}
```

Common uses: grants, audit logging, table optimization, vacuuming.

### 10. What is the purpose of on-run-start and on-run-end?
**Answer:** Project-level hooks in dbt_project.yml that execute at beginning/end of dbt run. Use for: creating schemas, setting warehouse size, logging run metadata, sending notifications. Example:

```yaml
on-run-start:
  - "create schema if not exists {{ target.schema }}"
on-run-end:
  - "call log_dbt_run('{{ run_started_at }}', '{{ invocation_id }}')"
```

### 11. How do you manage DBT package versions?
**Answer:** Specify versions in packages.yml:

```yaml
packages:
  - package: dbt-labs/dbt_utils
    version: 1.1.1
  - git: "https://github.com/org/repo"
    revision: 0.2.0
```

Use semantic versioning, test upgrades in dev, pin versions for stability, run `dbt deps` after changes, and review changelogs before upgrading.

### 12. What strategies do you use for deployment orchestration?
**Answer:** Options: DBT Cloud scheduler (managed), Airflow with dbt operators (flexible), cron with dbt CLI (simple), GitHub Actions (CI/CD native), Dagster (asset-oriented), or Prefect (Pythonic). Consider: scheduling needs, dependency management, monitoring requirements, team expertise, and cost. Implement alerting and retry logic.

## DBT Performance Optimization

### 1. What techniques would you use to optimize DBT model performance?
**Answer:** Use appropriate materializations (incremental for large tables), implement clustering/partitioning, select only needed columns, optimize join order and types, use CTEs for readability without performance cost, leverage warehouse-specific features (Snowflake clustering, BigQuery partitioning), minimize DISTINCT and window functions, add indexes in post-hooks, and use `limit` during development.

### 2. How do you identify slow-running models?
**Answer:** Check run_results.json for execution times, use DBT Cloud run history, query warehouse query history, enable timing logs with `dbt run --debug`, review compilation time vs execution time, identify models with large data scans, and monitor warehouse resource consumption. Focus on models with >5min runtime or exponential growth.

### 3. What is the purpose of model timing in DBT?
**Answer:** Model timing tracks compilation and execution duration for each model. Available in run_results.json and DBT Cloud UI. Use to: identify performance bottlenecks, track optimization impact, prioritize improvement efforts, set SLAs, detect regressions after changes, and understand total pipeline runtime. Critical for production monitoring.

### 4. How would you optimize a long-running incremental model?
**Answer:** Ensure proper unique_key and filtering logic, add clustering/partitioning on filter columns, reduce lookback window if possible, optimize join conditions, use appropriate incremental strategy (merge vs insert_overwrite), consider breaking into multiple models, add indexes on join keys, and verify statistics are current in warehouse.

### 5. What are the performance implications of using views versus tables?
**Answer:** 
- **Views**: No build time, no storage cost, query executes each time (slower queries), always current data, chain views = nested queries (performance degradation)
- **Tables**: Build time cost, storage cost, fast queries, data staleness between runs, better for complex transformations or frequent access

### 6. How do you implement partitioning and clustering in DBT models?
**Answer:** Use warehouse-specific configurations in model config:

**Snowflake:**
```sql
{{ config(
    cluster_by=['date', 'user_id']
) }}
```

**BigQuery:**
```sql
{{ config(
    partition_by={
      "field": "date",
      "data_type": "date"
    },
    cluster_by=['user_id', 'product_id']
) }}
```

### 7. What strategies would you use to reduce warehouse compute costs?
**Answer:** Right-size warehouse/cluster sizes, enable auto-suspend and auto-resume, use incremental models for large tables, materialize as views when appropriate, schedule non-urgent jobs during off-peak, implement query result caching, avoid over-testing in production, use slim CI, consolidate small models, and monitor credit consumption by model.

### 8. How do you optimize complex joins in DBT models?
**Answer:** Filter data early in CTEs, join on indexed columns, use appropriate join types (INNER when possible), put smaller table on right side for some warehouses, avoid joining on functions/calculations, denormalize when appropriate, consider pre-aggregating before joining, and use SEMI JOIN instead of EXISTS when applicable.

### 9. What is the impact of using SELECT * in DBT models?
**Answer:** SELECT * causes: increased data transfer, slower queries reading unnecessary columns, breaks when upstream schema changes, unclear dependencies, larger result caching overhead, and compilation issues with column renaming. Best practice: explicitly list needed columns for clarity, maintainability, and performance.

### 10. How do you handle large seed files?
**Answer:** Seeds aren't designed for large files. Alternatives: load via ETL tool and define as source, use external stages with COPY commands, split into multiple smaller seeds, compress before loading, or create DBT model that loads from cloud storage. Seeds should be <1MB for optimal performance.

### 11. What are query optimization best practices in DBT?
**Answer:** Use CTEs for readability and reusability, filter early in query logic, avoid SELECT *, use appropriate join types, leverage warehouse statistics, partition and cluster tables, minimize nested subqueries, use window functions efficiently, avoid DISTINCT when possible, test with LIMIT during development, and review query execution plans.

### 12. How do you use the limit configuration during development?
**Answer:** Add `{{ limit_data_in_dev() }}` macro from dbt_utils or create custom macro:

```sql
{% macro limit_data_in_dev() %}
  {% if target.name == 'dev' %}
    limit 1000
  {% endif %}
{% endmacro %}
```

Speeds up development iterations by processing subset of data. Remove in production using target context.

## DBT Testing and Debugging

### 1. How do you debug a failing DBT model?
**Answer:** Check error message in console, review compiled SQL in target/compiled/, run compiled SQL directly in warehouse to isolate issue, add `{{ log() }}` statements for debugging, use `--debug` flag for verbose output, check upstream model data quality, verify source data freshness, review recent changes in Git, and test with smaller data subset using LIMIT.

### 2. What information is available in the logs for troubleshooting?
**Answer:** Logs contain: model execution order, SQL compilation errors, database errors, execution timing, rows affected, warning messages, test results, connection details, and dependency resolution. Access via console output, logs/dbt.log file, or DBT Cloud UI. Use `--debug` for additional context including full SQL.

### 3. How do you use the --debug flag?
**Answer:** Run `dbt run --debug` to get verbose logging including: full compiled SQL, connection attempts, macro expansions, Jinja rendering, database responses, and internal DBT operations. Useful for troubleshooting compilation errors, understanding generated SQL, or diagnosing connection issues. Output can be large, so redirect to file if needed.

### 4. What is the purpose of the compiled folder?
**Answer:** target/compiled/ contains rendered SQL with Jinja compiled away. Each model has corresponding .sql file showing exact SQL that will execute. Essential for: debugging model logic, understanding macro expansion, copying SQL for direct warehouse execution, reviewing query optimization opportunities, and learning how DBT transforms code.

### 5. How do you validate SQL syntax before running models?
**Answer:** Use `dbt compile` to render Jinja and validate syntax without execution. Review target/compiled/ output. Use SQL linters like SQLFluff integrated with DBT. Enable warehouse-specific validation if available. Implement pre-commit hooks with compilation checks. Use IDE extensions with DBT support for real-time validation.

### 6. What strategies do you use to test models in development?
**Answer:** Use dev target with separate schema, implement `limit_data_in_dev()` macro, test individual models with `--select`, start with small date ranges, validate with simple SELECTs, compare row counts to expectations, use dbt_expectations package for comprehensive tests, implement unit-test-like singular tests, and leverage dbt_utils.equality for comparing datasets.

### 7. How do you use the run_results.json file?
**Answer:** run_results.json contains execution metadata: success/failure status, execution timing, rows affected, error messages, compiled SQL paths. Use for: building monitoring dashboards, triggering alerts on failures, tracking performance trends, auditing runs, identifying slow models, and integrating with external systems via parsing.

### 8. What is the manifest.json file and what information does it contain?
**Answer:** manifest.json is complete project compilation artifact containing: all model definitions, dependencies and lineage, column schemas, test definitions, macro code, source configurations, documentation, and metadata. Used by: DBT Cloud, dbt-docs, state-based selection, external tools for analysis, and CI/CD for state comparison.

### 9. How do you test DBT projects locally before deployment?
**Answer:** Run full test suite with `dbt build`, test affected models with state-based selection, validate with production-like data volume, run source freshness checks, compile documentation and review, use separate dev schema, implement pre-commit hooks, run linters (SQLFluff), and compare outputs to production using dbt_utils.equality.

### 10. What tools can you use for linting DBT code?
**Answer:** **SQLFluff** (SQL linting with DBT templating support), **dbt-checkpoint** (pre-commit hooks for DBT), **dbt-coverage** (test coverage analysis), **pre-commit** (framework for checks), and IDE extensions (VSCode DBT Power User). Configure style guides, enforce naming conventions, and automate checks in CI/CD.

### 11. How do you handle circular dependencies?
**Answer:** DBT prevents circular dependencies at compile time. If detected: review model lineage with dbt docs, identify cycle in error message, refactor models to break cycle (use intermediate model, split logic, or use sources instead of ref), restructure data flow, or consolidate models if they're truly interdependent. Proper layering prevents this.

### 12. What is the best way to test custom macros?
**Answer:** Create test models that use the macro with known inputs, write singular tests to validate macro output, use dbt_utils.equality to compare results, test edge cases (NULLs, empty strings), document expected behavior, version macros for breaking changes, and maintain example usage in documentation. Consider integration tests in separate test project.

## DBT with Data Warehouses

### 1. How does DBT work with Snowflake?
**Answer:** DBT connects to Snowflake via adapter, leveraging Snowflake-specific features: automatic clustering, zero-copy cloning, time travel, transient tables, query tags for monitoring. Configure in profiles.yml with account, warehouse, database, schema. Supports all DBT features plus Snowflake-specific materializations and optimizations.

### 2. What are Snowflake-specific configurations in DBT?
**Answer:** Configurations include: `transient: true` (no Fail-safe), `cluster_by` (clustering keys), `automatic_clustering: true`, `query_tag` (metadata), `warehouse` (compute), `copy_grants: true` (preserve permissions), `snowflake_warehouse` (override default), and `secure: true` for views. Set in model config or dbt_project.yml.

### 3. How do you implement incremental models in BigQuery using DBT?
**Answer:** BigQuery incremental models use merge statement or insert_overwrite. Configure partitioning and clustering:

```sql
{{ config(
    materialized='incremental',
    unique_key='id',
    partition_by={'field': 'date', 'data_type': 'date'},
    cluster_by=['user_id'],
    incremental_strategy='merge'
) }}
```

Supports merge, insert_overwrite, and microbatch strategies for efficient processing.

### 4. What are the differences in DBT implementation across different warehouses?
**Answer:** Differences include: SQL dialect variations (DATE vs TIMESTAMP), materialization strategies (BigQuery partitioning vs Snowflake clustering), incremental approaches (merge support), function names (DATEADD vs DATE_ADD), performance optimizations, cost models (slots vs credits), and feature availability (Snowflake transient tables). Use adapters and dispatch macros for cross-platform compatibility.

### 5. How do you handle warehouse-specific SQL in DBT?
**Answer:** Use `target.type` for conditional logic:

```sql
{% if target.type == 'snowflake' %}
    dateadd(day, 1, order_date)
{% elif target.type == 'bigquery' %}
    date_add(order_date, interval 1 day)
{% endif %}
```

Or use dbt_utils dispatch macros that handle differences automatically. Maintain separate macro implementations per adapter.

### 6. What is the adapter pattern in DBT?
**Answer:** Adapters translate DBT operations to warehouse-specific SQL. Each warehouse (Snowflake, BigQuery, Redshift, Postgres) has adapter plugin implementing: connection management, SQL generation, materialization logic, and catalog queries. Enables DBT to support multiple platforms with consistent interface. Extend via custom adapter development.

### 7. How do you configure database-specific materializations?
**Answer:** Use warehouse-specific configs in model configuration:

```yaml
models:
  my_project:
    marts:
      +materialized: table
      snowflake:
        +cluster_by: ['date']
      bigquery:
        +partition_by: {field: date, data_type: date}
```

Adapters ignore configurations not applicable to their platform.

### 8. What are the considerations for using DBT with Redshift?
**Answer:** Redshift-specific considerations: distribution keys (DISTKEY) and sort keys (SORTKEY) for performance, vacuum and analyze operations in post-hooks, limited JSON support compared to Snowflake/BigQuery, different incremental strategies, slower schema changes, and WLM (Workload Management) configuration. Use late-binding views and optimize for columnar storage.

### 9. How do you optimize DBT models for specific warehouses?
**Answer:** Leverage warehouse features: Snowflake (clustering, transient tables), BigQuery (partitioning, clustering, nested fields), Redshift (dist/sort keys), Postgres (indexes). Use appropriate data types, understand cost models, implement warehouse-specific post-hooks (grants, optimization), and review query execution plans in native warehouse tools.

### 10. What warehouse features can DBT leverage for better performance?
**Answer:** Features include: materialized views (BigQuery, Snowflake), partitioning/clustering, result caching, dynamic tables (Snowflake), streaming inserts (BigQuery), external tables, zero-copy cloning for dev/test, time travel for recovery, workload management, and automatic optimization features. Configure via model config to utilize platform strengths.

## DBT Documentation and Collaboration

### 1. How does DBT generate documentation?
**Answer:** DBT parses project files, YAML descriptions, and warehouse schemas to generate static documentation website. Run `dbt docs generate` to create manifest.json and catalog.json, then `dbt docs serve` to launch local server. Documentation includes model descriptions, lineage DAG, column details, test results, source freshness, and SQL code.

### 2. What is the purpose of schema.yml files?
**Answer:** Schema.yml files (or properties.yml) define: model and column descriptions, tests configuration, source definitions, documentation references, column data types and constraints, and metadata. These files separate documentation and testing config from business logic, making models more maintainable and providing content for dbt docs.

### 3. How do you add descriptions to models, columns, and tests?
**Answer:** Add in schema.yml:

```yaml
models:
  - name: fct_orders
    description: "Order fact table with one row per order"
    columns:
      - name: order_id
        description: "Unique order identifier"
        tests:
          - unique:
              description: "Ensures no duplicate orders"
          - not_null
```

Supports markdown formatting and doc blocks for longer content.

### 4. What is the docs() function and how do you use it?
**Answer:** `docs()` references doc blocks for reusable documentation. Define doc block in any .md file or schema.yml:

```
{% docs order_status %}
Order status values:
- pending: Order placed
- shipped: Order shipped
- delivered: Order delivered
{% enddocs %}
```

Reference: `description: "{{ doc('order_status') }}"`. Enables DRY documentation across multiple models.

### 5. How do you generate and serve DBT documentation?
**Answer:** Generate with `dbt docs generate` (creates manifest.json and catalog.json in target/). Serve locally with `dbt docs serve` (launches at localhost:8080). For production: host static site on S3/GCS/Azure Blob, use DBT Cloud (automatic hosting), or deploy to internal web server. Update regularly after schema changes.

### 6. What are exposures in DBT and when would you use them?
**Answer:** Exposures document downstream usage of DBT models in dashboards, reports, or applications. Define in schema.yml:

```yaml
exposures:
  - name: weekly_sales_dashboard
    type: dashboard
    owner: {name: Analytics Team, email: analytics@co.com}
    depends_on:
      - ref('fct_sales')
```

Use for: impact analysis, understanding model usage, documenting stakeholders, and tracking dependencies outside DBT.

### 7. How do you document complex business logic?
**Answer:** Use multi-line descriptions with markdown in schema.yml, create doc blocks for reusable explanations, add inline comments in SQL, include examples in descriptions, document assumptions and edge cases, reference business requirements/tickets, use README files for model groups, and maintain changelog for significant logic changes.

### 8. What is the purpose of the dbt docs generate command?
**Answer:** Generates documentation artifacts: manifest.json (project structure, dependencies, compiled code) and catalog.json (warehouse metadata, column types, statistics). These power the documentation website with searchable model info, interactive lineage graph, and warehouse statistics. Run after model changes to update docs.

### 9. How do you share documentation with stakeholders?
**Answer:** Host docs website publicly or internally, use DBT Cloud built-in hosting, export DAG images for presentations, create PDF/markdown reports from descriptions, share lineage screenshots, schedule automated doc generation and deployment, provide stakeholders with direct warehouse access, and maintain business-friendly README files in project.

### 10. What information is included in the DBT DAG?
**Answer:** DAG (Directed Acyclic Graph) visualizes: model dependencies and lineage, sources and exposures, model types (staging, marts), test locations, documentation status, model materialization, execution order, and clickable nodes linking to details. Interactive exploration allows understanding data flow from source to consumption. Critical for impact analysis.

## Advanced DBT Concepts

### 1. What are analyses in DBT and how do they differ from models?
**Answer:** Analyses are SQL queries in analyses/ directory that compile but don't execute. Used for: ad-hoc queries, data exploration, one-time analyses, training queries. Unlike models, they don't create database objects. Useful for documenting analytical approaches without cluttering warehouse. Access compiled SQL in target/compiled/analyses/.

### 2. How do you implement data contracts in DBT?
**Answer:** Data contracts enforce schema expectations. Implement using: model contracts with strict column definitions, contract: enforced configuration, schema validation tests, custom tests for business rules, and source constraints. Example:

```yaml
models:
  - name: dim_customers
    config:
      contract:
        enforced: true
    columns:
      - name: customer_id
        data_type: int
        constraints:
          - type: not_null
          - type: primary_key
```

### 3. What are metrics in DBT and how do you define them?
**Answer:** DBT Metrics (MetricFlow) define business metrics once for consistent calculation. Define in schema.yml:

```yaml
metrics:
  - name: revenue
    type: sum
    sql: amount
    timestamp: order_date
    dimensions: [customer_id, product_id]
```

Enables consistent metric definitions across BI tools, reduces metric sprawl, and provides single source of truth for calculations.

### 4. How do you handle schema evolution in DBT projects?
**Answer:** Strategies: use `on_schema_change: sync_all_columns` for incremental models, implement contract enforcement for critical models, version models for breaking changes (v1/v2), use flexible VARIANT types for semi-structured data, create schema migration models, communicate changes via Git/documentation, and test schema changes in dev before prod deployment.

### 5. What strategies would you use for data lineage tracking?
**Answer:** DBT provides automatic lineage via ref() and source(). Enhance with: exposures for downstream dependencies, detailed documentation, metadata tracking in models, integration with data catalogs (Atlan, Alation), custom lineage macros for external dependencies, tags for data classification, and regular lineage reviews. Export manifest.json for external lineage tools.

### 6. How do you implement row-level security in DBT?
**Answer:** Implement using post-hooks or separate secured views:

```sql
{{ config(
    post_hook=[
        "grant select on {{ this }} to role analyst",
        "create or replace row access policy rap_region on {{ this }}(region)"
    ]
) }}
```

Or create separate models with WHERE filters per role. Leverage warehouse-native RLS features (Snowflake row access policies, BigQuery row-level security).

### 7. What are exposures and how do they help with impact analysis?
**Answer:** Exposures document how models are consumed (dashboards, ML models, reverse ETL). Help with impact analysis by: showing downstream dependencies outside DBT, identifying stakeholders affected by changes, preventing breaking changes to consumed models, and providing complete lineage from source to consumption. Essential for production changes.

### 8. How do you use DBT with orchestration tools like Airflow?
**Answer:** Integration approaches: use airflow-dbt provider for native operators, run dbt CLI via BashOperator, use dbt Cloud jobs triggered via API, leverage ExternalTaskSensor for dependencies, pass runtime variables, monitor via callbacks, implement retries and alerting, and use Airflow for scheduling while DBT handles transformation logic.

### 9. What is the purpose of the state method in DBT?
**Answer:** State method enables comparison between current and previous project states. Enables: slim CI (run only modified models), defer to production (use prod artifacts in dev), state-based selection (`state:modified`, `state:new`), and efficient CI/CD. Requires manifest.json from previous run. Saves time and compute by avoiding full rebuilds.

### 10. How do you implement slim CI in DBT Cloud?
**Answer:** Slim CI runs only changed models and their dependents. In DBT Cloud: enable on PR, configure to defer to production run, use `dbt build --select state:modified+` command. Benefits: faster CI runs, reduced compute costs, quicker feedback, and enables testing at scale. Compares current branch to production manifest.

### 11. What are defer and state in DBT?
**Answer:** 
- **Defer**: Uses production artifacts when model hasn't been built in current environment. Enables development without building entire DAG. Configure with `--defer --state path/to/artifacts`.
- **State**: Compares current project to previous state for differential execution. Combined with defer for efficient CI/CD workflows.

### 12. How do you handle multi-project dependencies?
**Answer:** Use DBT packages for cross-project dependencies, reference via package namespace, maintain clear interfaces between projects, version packages for stability, use cross-database refs for separate deployments, document dependencies clearly, implement integration testing, and consider monorepo vs multi-repo tradeoffs based on team structure.

## Scenario-Based DBT Questions

### 1. How would you migrate a legacy ETL pipeline to DBT?
**Answer:** Approach: analyze current pipeline logic and dependencies, identify source tables and define as DBT sources, recreate transformations as staged models (staging → intermediate → marts), implement tests for data quality validation, run parallel (shadow mode) comparing outputs, gradually shift downstream consumers, document business logic, train team on DBT, and deprecate legacy pipeline after validation period.

### 2. A DBT model is failing in production but works in development - how do you troubleshoot?
**Answer:** Check for: environment-specific data differences (volume, edge cases), configuration differences in profiles.yml, permissions issues, warehouse resource constraints, different warehouse sizes/settings, schema drift between environments, timezone or locale differences, hardcoded values instead of variables, and recent production data changes. Compare target/compiled SQL across environments.

### 3. How would you handle a situation where upstream schema changes break your models?
**Answer:** Immediate: fix models to handle new schema, use dbt run --full-refresh if needed, communicate with data producers about change management. Preventive: implement contract enforcement, add schema tests, monitor source freshness with alerts, establish schema change protocols with upstream teams, use flexible VARIANT for semi-structured data, and version critical interfaces.

### 4. You need to process data from multiple sources with different refresh frequencies - how would you design this?
**Answer:** Create separate source groups by refresh frequency, implement source freshness checks per group, use tags for scheduling (hourly, daily, weekly), design models to handle varying data availability, implement incremental models with appropriate lookback windows, schedule DBT runs matching fastest refresh frequency with conditional logic, and consider separate DBT projects or jobs per frequency tier.

### 5. How would you implement a medallion architecture (bronze, silver, gold) using DBT?
**Answer:** 
- **Bronze**: Source definitions pointing to raw ingested data
- **Silver**: Staging models with cleaning, deduplication, type casting (views or incremental)
- **Gold**: Marts with business logic, aggregations, denormalization (tables/incremental)

Structure: models/bronze (sources), models/silver (staging), models/gold (marts). Use appropriate materializations per layer and implement tests at each stage.

### 6. Your incremental model is generating duplicates - what could be the causes and solutions?
**Answer:** Causes: missing or incorrect unique_key, duplicate source data, race conditions with concurrent loads, incorrect filter logic in is_incremental(), or improper merge strategy. Solutions: verify unique_key uniqueness, add deduplication in source, use appropriate incremental_strategy (merge vs append), implement unique test, review compiled SQL for merge logic, and consider full refresh to clean.

### 7. How would you design a DBT project for a multi-tenant application?
**Answer:** Approaches: (1) Separate schema per tenant with dynamic model generation using macros, (2) Single schema with tenant_id column and row-level security, (3) Separate DBT projects per major tenant. Implement tenant-aware tests, use variables for tenant configuration, consider performance at scale, implement proper security/isolation, and monitor per-tenant costs and performance.

### 8. Your DBT runs are taking too long - what steps would you take to optimize?
**Answer:** Profile run_results.json for slow models, convert large tables to incremental, implement clustering/partitioning, increase warehouse size for complex transformations, parallelize with more threads, use slim CI to reduce scope, eliminate unnecessary dependencies, materialize as tables strategically, reduce lookback windows, schedule non-urgent models separately, and consider breaking large models into smaller units.

### 9. How would you implement a data quality framework using DBT?
**Answer:** Combine DBT tests with dbt_expectations package, implement tiered severity (warn vs error), create custom test macros for business rules, use store_failures for investigation, implement source freshness monitoring, add row count and null rate tests, track test results over time, integrate with alerting systems, document acceptable quality thresholds, and maintain data quality SLAs.

### 10. You need to coordinate DBT runs with other data pipeline tools - what approach would you take?
**Answer:** Use orchestration tool (Airflow, Prefect, Dagster) as coordinator, trigger DBT via CLI/API, implement proper dependency management, use sensors/webhooks for event-driven triggers, monitor DBT job status via run_results.json, implement retry logic and alerting, use DBT Cloud API for managed coordination, and maintain clear SLAs between pipeline stages.

### 11. How would you handle a requirement for real-time data updates in DBT?
**Answer:** DBT isn't designed for real-time processing. Solutions: use DBT for batch transformations with frequent runs (micro-batch approach), implement streaming layer outside DBT (Kafka, Flink) for real-time, create DBT models on top of streaming tables, use warehouse features (Snowflake streams, BigQuery streaming), or hybrid approach with real-time raw layer and DBT for aggregations/enrichment.

### 12. Your team needs to maintain separate DBT projects that share common logic - how would you structure this?
**Answer:** Create shared DBT package with common macros/models, publish to Git repository, reference in each project's packages.yml, version the shared package carefully, maintain backward compatibility, document shared logic thoroughly, implement testing in shared package, use semantic versioning, and consider monorepo vs multi-repo based on team size and deployment needs.

## DBT Monitoring and Observability

### 1. What metrics should you monitor for DBT pipeline health?
**Answer:** Monitor: model execution success rate, run duration and trends, test pass/fail rates, source freshness status, rows processed per model, compilation time, warehouse credit consumption, test coverage percentage, model build frequency, error patterns and frequencies, dependency resolution time, and incremental model efficiency. Track via run_results.json and DBT Cloud metrics.

### 2. How do you implement alerting for DBT job failures?
**Answer:** Use DBT Cloud built-in notifications (email, Slack), parse run_results.json in orchestration tool for custom alerts, implement webhooks for external systems, use on-run-end hooks to send notifications, integrate with monitoring platforms (PagerDuty, Datadog), set up email alerts via orchestrator (Airflow), create Slack bots for status updates, and implement escalation for critical failures.

### 3. What is the purpose of model run results and how do you use them?
**Answer:** Run results (run_results.json) contain execution metadata per model: status (success/error/skipped), timing information, rows affected, error messages, and invocation details. Use for: building monitoring dashboards, identifying performance regressions, tracking reliability metrics, debugging failures, generating reports, and feeding into alerting systems. Parse programmatically for automation.

### 4. How do you track data freshness in DBT?
**Answer:** Implement source freshness checks with warn_after/error_after thresholds, run `dbt source freshness` on schedule, monitor sources.json results, set up alerts for stale data, track loaded_at timestamps in models, create freshness dashboards, integrate with data observability tools, and establish SLAs for data currency. Critical for time-sensitive analytics.

### 5. What strategies would you use to monitor data quality over time?
**Answer:** Track test results historically in database, use store_failures to analyze failure patterns, implement trending tests (row counts, null rates), create data quality dashboards, monitor test coverage metrics, use dbt_expectations for statistical tests, set up anomaly detection on key metrics, track data volume trends, and establish baseline quality metrics with acceptable thresholds.

### 6. How do you implement logging in DBT projects?
**Answer:** DBT provides logging via console and logs/dbt.log file. Enhance with: custom log statements using `{{ log() }}`, on-run-start/end hooks for audit logging, warehouse-based audit tables via post-hooks, integration with centralized logging (CloudWatch, Splunk), structured logging in JSON format, log level configuration, and retention policies for historical analysis.

### 7. What tools can you integrate with DBT for observability?
**Answer:** Tools include: **DBT Cloud** (native monitoring), **Monte Carlo/Datafold** (data observability), **Elementary** (DBT-native monitoring), **Lightdash/Metabase** (BI on metadata), **Datadog/New Relic** (APM integration), **Atlan/Alation** (data catalogs), **Sentry** (error tracking), and **Airflow/Prefect** (orchestration monitoring). Parse manifest and run_results for custom integrations.

### 8. How do you track warehouse costs related to DBT runs?
**Answer:** Monitor warehouse usage by query/session tags, implement query_tag in DBT for attribution, review run_results timing vs warehouse pricing, track credit consumption per model, use warehouse native tools (Snowflake query history), allocate costs by tag/schema, optimize expensive models, implement budgets and alerts, and consider resource scheduling for cost efficiency.

### 9. What is the purpose of source freshness checks?
**Answer:** Source freshness validates that raw data is up-to-date by checking when data was last loaded. Prevents stale analytics, ensures SLA compliance, detects upstream pipeline failures early, triggers alerts before users notice issues, and maintains trust in data products. Configure warn/error thresholds based on business requirements and upstream refresh schedules.

### 10. How would you implement SLA monitoring for DBT models?
**Answer:** Define SLAs per model (completion time, freshness), track execution time trends in run_results, set up alerts for SLA breaches, implement source freshness for timeliness, create SLA dashboards showing compliance, use exposures to link SLAs to business impact, automate escalation for violations, document expected run times, and regularly review and adjust thresholds.

## DBT Integration and Ecosystem

### 1. How does DBT integrate with orchestration tools like Airflow and Dagster?
**Answer:** 
- **Airflow**: Use airflow-dbt provider with DbtRunOperator, BashOperator with dbt CLI, or trigger DBT Cloud jobs via API. Leverage task dependencies, retries, and scheduling.
- **Dagster**: Use dagster-dbt library for asset-based orchestration, automatic DBT asset definitions from manifest, and integrated lineage.
Both provide scheduling, monitoring, and coordination with other data tools.

### 2. What is the purpose of the dbt-rpc server?
**Answer:** DBT RPC (Remote Procedure Call) server enables programmatic interaction with DBT via JSON-RPC API. Allows: running specific models remotely, querying project metadata, compiling models on-demand, and integration with applications. Use cases: real-time documentation, IDE integrations, custom UIs. Note: Being deprecated in favor of DBT Cloud API and CLI improvements.

### 3. How do you use DBT with BI tools?
**Answer:** DBT creates tables/views consumed by BI tools (Looker, Tableau, Power BI, Metabase). Integration: expose marts layer to BI, use DBT metrics for consistent definitions, leverage DBT docs for BI tool documentation, implement semantic layer for standardized metrics, grant appropriate permissions via post-hooks, and maintain naming conventions BI-friendly for exploration.

### 4. What are the integration options between DBT and data quality tools?
**Answer:** Options include: **dbt_expectations** (Great Expectations tests in DBT), **Elementary** (DBT-native monitoring), **Monte Carlo/Datafold** (anomaly detection on DBT models), **Soda** (data quality as code), **re_data** (metrics and monitoring). Parse DBT artifacts (manifest, catalog, run_results) for custom integrations with quality platforms.

### 5. How does DBT work with reverse ETL tools?
**Answer:** Reverse ETL tools (Hightouch, Census, Grouparion) read DBT-transformed data and sync to operational systems. Integration: DBT creates analysis-ready tables, reverse ETL syncs to CRM/marketing tools, exposures document reverse ETL dependencies, coordinated scheduling ensures data freshness, and DBT tests ensure quality before syncing.

### 6. What is the DBT Semantic Layer and what problem does it solve?
**Answer:** DBT Semantic Layer (MetricFlow) provides unified metric definitions accessible across tools. Solves: metric inconsistency across teams, redundant metric logic in BI tools, metric sprawl, and trust issues. Define metrics once in DBT, query via API from any tool. Available in DBT Cloud for standardized, governed metrics.

### 7. How do you integrate DBT with Git workflows?
**Answer:** Use Git for version control: feature branches for development, pull requests for code review, CI/CD pipelines on PR (slim CI with state:modified), merge to main triggers production deployment, use Git tags for releases, maintain CHANGELOG, implement branch protections, and leverage DBT Cloud Git integration for IDE and automated deployments.

### 8. What third-party packages are commonly used with DBT?
**Answer:** Common packages:
- **dbt_utils**: Utility macros (surrogate_key, pivot, date_spine)
- **dbt_expectations**: Data quality tests
- **dbt_project_evaluator**: Project structure linting
- **audit_helper**: Comparing datasets during refactoring
- **codegen**: Code generation helpers
- **dbt_metrics**: Metrics framework
- Source-specific: snowflake_spend, bigquery_audit

### 9. How do you use DBT with modern data observability platforms?
**Answer:** Platforms (Monte Carlo, Datafold, Elementary) integrate via: DBT artifacts parsing (manifest, catalog), warehouse metadata queries, DBT Cloud API, custom schemas for monitoring tables, webhooks for events, and automated anomaly detection on DBT models. Provide end-to-end lineage, data quality monitoring, and impact analysis beyond DBT's native capabilities.

### 10. What is the role of DBT in the modern data stack?
**Answer:** DBT serves as the transformation layer ("T" in ELT): sits between data ingestion (Fivetran, Airbyte) and consumption (BI, ML). Enables analytics engineering, brings software engineering practices to data, democratizes data transformation for SQL users, provides testing and documentation, creates single source of truth, and bridges data engineering and analytics. Central to modern cloud-native data platforms.

Sources
1. [Top 26 dbt Interview Questions and Answers for 2025](https://www.datacamp.com/blog/dbt-interview-questions)
2. [DBT Interview Questions in 2025](https://snowflakemasters.in/dbt-interview-questions/)
3. [Top 25 DBT Interview Questions and Answers for 2025](https://www.projectpro.io/article/dbt-interview-questions-and-answers/1062)
4. [Data Build Tool Interview Questions and Answers](https://www.getorchestra.io/guides/data-build-tool-interview-questions-and-answers)
5. [Top 30 Most Common dbt Interview Questions You Should ...](https://www.vervecopilot.com/interview-questions/top-30-most-common-dbt-interview-questions-you-should-prepare-for)
6. [Top 60+ Data Engineer Interview Questions and Answers](https://www.geeksforgeeks.org/data-engineering/data-engineer-interview-questions/)
7. [Data Build Tool (DBT) Interview Questions & Answers](https://www.youtube.com/watch?v=G3t0EF6hMkQ)
8. [60+ Data Engineer Interview Questions and Answers](https://www.foundit.in/career-advice/data-engineer-interview-questions-and-answers/)
9. [dbt Developer Interview Questions and Answers](https://www.index.dev/interview-questions/dbt)
10. [ Data Engineering Interview Prep & Top Questions](https://www.youtube.com/watch?v=uGbpITJKSfY)
11. [ 14 Data Engineer Interview Questions and How to Answer ...](https://www.coursera.org/in/articles/data-engineer-interview-questions)
12. [ Ace Your Data Engineering Interview (2025 Guide)](https://www.tryexponent.com/blog/data-engineering-interview)
13. [ Top 50 Data Engineer Interview Questions and Answers](https://www.henryharvin.com/blog/top-data-engineer-interview-questions-and-answers/)
14. [ Big Data Engineering Interview Questions | PDF](https://www.scribd.com/document/515331679/Big-Data-Engineering-Interview-Questions)
