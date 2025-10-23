# Comprehensive DBT Interview Questions by Topic

## Core DBT Fundamentals

1. What is DBT and how does it fit into the modern data stack?
2. Explain the ELT paradigm and how DBT enables it
3. What are the main components of a DBT project?
4. Describe the DBT project structure and key directories (models, tests, macros, seeds)
5. What is the purpose of the dbt_project.yml file?
6. How does DBT handle version control and collaboration?
7. What are DBT packages and how do you use them?
8. Explain the difference between DBT Core and DBT Cloud
9. What are the benefits of using DBT over traditional ETL tools?
10. How does DBT generate documentation automatically?

## DBT Models and Materializations

1. What are DBT models and how do you create them?
2. Explain the different materialization types in DBT (view, table, incremental, ephemeral)
3. When would you use a view materialization versus a table materialization?
4. How do incremental models work and when should you use them?
5. What is an ephemeral materialization and what are its use cases?
6. How do you configure materialization at the project, folder, and model level?
7. What are the performance implications of each materialization type?
8. How do you handle schema changes in incremental models?
9. Explain the unique_key parameter in incremental models
10. What is the purpose of the is_incremental() macro?
11. How do you implement a full refresh for incremental models?
12. What strategies can you use to optimize incremental model performance?

## DBT Testing

1. What are the different types of tests available in DBT?
2. Explain the difference between schema tests and data tests
3. What are the built-in generic tests in DBT?
4. How do you create custom schema tests?
5. What are singular tests (formerly data tests) and when would you use them?
6. How do you write custom data quality tests?
7. What is the purpose of test severity levels?
8. How do you implement tests that run on specific conditions?
9. Explain how to use the store_failures configuration
10. What metrics would you track to measure data quality in DBT?
11. How do you handle failing tests in production?
12. What is the difference between warn and error severity?

## DBT Macros and Jinja

1. What are macros in DBT and why are they useful?
2. How do you create and use custom macros?
3. Explain how Jinja templating works in DBT
4. What are some common use cases for macros?
5. How do you use variables in DBT with Jinja?
6. What is the difference between {% %} and {{ }} in Jinja?
7. How do you implement conditional logic using Jinja in DBT?
8. What are context variables in DBT and how do you access them?
9. How do you create reusable SQL snippets using macros?
10. Explain the ref() and source() functions
11. What is the this variable in DBT?
12. How do you use macros to generate dynamic SQL?
13. What are dispatch macros and when would you use them?

## DBT Sources and Seeds

1. What are sources in DBT and how do you define them?
2. How do you configure source freshness checks?
3. What is the purpose of source() function?
4. How do you document sources in DBT?
5. What are seeds in DBT and when should you use them?
6. What are the limitations of seeds?
7. How do you load seed files into your data warehouse?
8. What file formats are supported for seeds?
9. How do you override seed column types?
10. What is source freshness and how do you monitor it?

## DBT Snapshots

1. What are snapshots in DBT and what problem do they solve?
2. How do you implement Type 2 Slowly Changing Dimensions using snapshots?
3. Explain the difference between timestamp and check strategy for snapshots
4. What are the required columns in a snapshot table?
5. When would you use snapshots versus incremental models?
6. How do you handle hard deletes with snapshots?
7. What are the performance considerations for snapshots?
8. How do you rebuild or invalidate snapshots?

## DBT Project Organization and Best Practices

1. How would you structure a DBT project with multiple teams?
2. Explain the staging, intermediate, and mart layer pattern
3. What naming conventions do you recommend for DBT models?
4. How do you manage dependencies between models?
5. What is model lineage and why is it important?
6. How do you implement modular DBT projects?
7. What strategies would you use to organize models by business domain?
8. How do you handle shared logic across multiple models?
9. What is the purpose of the intermediate layer?
10. How do you manage technical debt in DBT projects?

## DBT Configuration and Deployment

1. How do you configure different environments (dev, staging, prod) in DBT?
2. What are profiles in DBT and how do you configure them?
3. How do you use environment variables in DBT?
4. Explain the purpose of target in profiles.yml
5. How do you implement CI/CD pipelines for DBT?
6. What is the dbt run command and what does it do?
7. How do you use model selection syntax (--select, --exclude)?
8. What are tags in DBT and how do you use them?
9. How do you implement pre-hooks and post-hooks?
10. What is the purpose of on-run-start and on-run-end?
11. How do you manage DBT package versions?
12. What strategies do you use for deployment orchestration?

## DBT Performance Optimization

1. What techniques would you use to optimize DBT model performance?
2. How do you identify slow-running models?
3. What is the purpose of model timing in DBT?
4. How would you optimize a long-running incremental model?
5. What are the performance implications of using views versus tables?
6. How do you implement partitioning and clustering in DBT models?
7. What strategies would you use to reduce warehouse compute costs?
8. How do you optimize complex joins in DBT models?
9. What is the impact of using SELECT * in DBT models?
10. How do you handle large seed files?
11. What are query optimization best practices in DBT?
12. How do you use the limit configuration during development?

## DBT Testing and Debugging

1. How do you debug a failing DBT model?
2. What information is available in the logs for troubleshooting?
3. How do you use the --debug flag?
4. What is the purpose of the compiled folder?
5. How do you validate SQL syntax before running models?
6. What strategies do you use to test models in development?
7. How do you use the run_results.json file?
8. What is the manifest.json file and what information does it contain?
9. How do you test DBT projects locally before deployment?
10. What tools can you use for linting DBT code?
11. How do you handle circular dependencies?
12. What is the best way to test custom macros?

## DBT with Data Warehouses

1. How does DBT work with Snowflake?
2. What are Snowflake-specific configurations in DBT?
3. How do you implement incremental models in BigQuery using DBT?
4. What are the differences in DBT implementation across different warehouses?
5. How do you handle warehouse-specific SQL in DBT?
6. What is the adapter pattern in DBT?
7. How do you configure database-specific materializations?
8. What are the considerations for using DBT with Redshift?
9. How do you optimize DBT models for specific warehouses?
10. What warehouse features can DBT leverage for better performance?

## DBT Documentation and Collaboration

1. How does DBT generate documentation?
2. What is the purpose of schema.yml files?
3. How do you add descriptions to models, columns, and tests?
4. What is the docs() function and how do you use it?
5. How do you generate and serve DBT documentation?
6. What are exposures in DBT and when would you use them?
7. How do you document complex business logic?
8. What is the purpose of the dbt docs generate command?
9. How do you share documentation with stakeholders?
10. What information is included in the DBT DAG?

## Advanced DBT Concepts

1. What are analyses in DBT and how do they differ from models?
2. How do you implement data contracts in DBT?
3. What are metrics in DBT and how do you define them?
4. How do you handle schema evolution in DBT projects?
5. What strategies would you use for data lineage tracking?
6. How do you implement row-level security in DBT?
7. What are exposures and how do they help with impact analysis?
8. How do you use DBT with orchestration tools like Airflow?
9. What is the purpose of the state method in DBT?
10. How do you implement slim CI in DBT Cloud?
11. What are defer and state in DBT?
12. How do you handle multi-project dependencies?

## Scenario-Based DBT Questions

1. How would you migrate a legacy ETL pipeline to DBT?
2. A DBT model is failing in production but works in development - how do you troubleshoot?
3. How would you handle a situation where upstream schema changes break your models?
4. You need to process data from multiple sources with different refresh frequencies - how would you design this?
5. How would you implement a medallion architecture (bronze, silver, gold) using DBT?
6. Your incremental model is generating duplicates - what could be the causes and solutions?
7. How would you design a DBT project for a multi-tenant application?
8. Your DBT runs are taking too long - what steps would you take to optimize?
9. How would you implement a data quality framework using DBT?
10. You need to coordinate DBT runs with other data pipeline tools - what approach would you take?
11. How would you handle a requirement for real-time data updates in DBT?
12. Your team needs to maintain separate DBT projects that share common logic - how would you structure this?

## DBT Monitoring and Observability

1. What metrics should you monitor for DBT pipeline health?
2. How do you implement alerting for DBT job failures?
3. What is the purpose of model run results and how do you use them?
4. How do you track data freshness in DBT?
5. What strategies would you use to monitor data quality over time?
6. How do you implement logging in DBT projects?
7. What tools can you integrate with DBT for observability?
8. How do you track warehouse costs related to DBT runs?
9. What is the purpose of source freshness checks?
10. How would you implement SLA monitoring for DBT models?

## DBT Integration and Ecosystem

1. How does DBT integrate with orchestration tools like Airflow and Dagster?
2. What is the purpose of the dbt-rpc server?
3. How do you use DBT with BI tools?
4. What are the integration options between DBT and data quality tools?
5. How does DBT work with reverse ETL tools?
6. What is the DBT Semantic Layer and what problem does it solve?
7. How do you integrate DBT with Git workflows?
8. What third-party packages are commonly used with DBT?
9. How do you use DBT with modern data observability platforms?
10. What is the role of DBT in the modern data stack?

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
