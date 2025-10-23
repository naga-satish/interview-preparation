# Comprehensive Apache Airflow Interview Questions by Topic

## Airflow Fundamentals

1. What is Apache Airflow and what problems does it solve?
2. What are the main components of Airflow's architecture?
3. How does Airflow differ from traditional ETL tools?
4. What are the key features of Apache Airflow?
5. When should you use Airflow versus other orchestration tools?
6. What is the history of Apache Airflow and who created it?

## DAGs (Directed Acyclic Graphs)

1. What is a DAG in Airflow?
2. Why must a DAG be acyclic?
3. How do you define a DAG in Airflow?
4. What are the important parameters when creating a DAG?
5. What is the difference between `schedule_interval` and `start_date`?
6. How do you handle dynamic DAG generation?
7. What are DAG runs and task instances?
8. How do you set default arguments for a DAG?
9. What is `catchup` in Airflow and when would you disable it?
10. How do you pass parameters to a DAG at runtime?

## Operators

1. What is an Operator in Airflow?
2. What are the most commonly used built-in operators?
3. What is the difference between PythonOperator and BashOperator?
4. When would you use a DummyOperator?
5. What is BranchPythonOperator and how does it work?
6. How do you create a custom operator?
7. What is the difference between an Operator and a Sensor?
8. What are the main sensor operators and their use cases?
9. How does the PythonOperator execute functions?
10. What is the SubDagOperator and when should you avoid using it?

## Task Dependencies and Relationships

1. How do you define task dependencies in Airflow?
2. What is the difference between `>>` and `<<` operators?
3. How do you create parallel tasks in Airflow?
4. What are the different ways to set task dependencies?
5. How do you implement branching logic in a DAG?
6. What is `set_upstream()` and `set_downstream()`?
7. How do you handle conditional task execution?
8. What is task fan-out and fan-in?
9. How do you trigger multiple tasks based on a condition?
10. What are trigger rules and what types are available?

## XComs (Cross-Communication)

1. What are XComs in Airflow?
2. How do you push and pull data using XComs?
3. What are the limitations of XComs?
4. When should you avoid using XComs?
5. How do you pass data between tasks effectively?
6. What is the difference between return value and xcom_push?
7. How are XComs stored in the metadata database?
8. What is the maximum recommended size for XCom data?

## Executors

1. What is an Executor in Airflow?
2. What are the different types of executors available?
3. What is the SequentialExecutor and when is it used?
4. What is the LocalExecutor and how does it differ from SequentialExecutor?
5. What is the CeleryExecutor and when should you use it?
6. What is the KubernetesExecutor and what are its benefits?
7. How do you configure different executors?
8. What are the trade-offs between different executor types?
9. Can you use multiple executors in a single Airflow instance?

## Scheduler

1. What is the role of the Airflow Scheduler?
2. How does the Scheduler determine which tasks to run?
3. What is the difference between the Scheduler and the Executor?
4. How does the Scheduler handle task retries?
5. What happens if the Scheduler goes down?
6. How do you optimize Scheduler performance?
7. What is the Scheduler's relationship with the metadata database?
8. How frequently does the Scheduler parse DAG files?

## Hooks and Connections

1. What are Hooks in Airflow?
2. How do Hooks differ from Operators?
3. What are some commonly used Hooks?
4. How do you create a custom Hook?
5. What are Connections in Airflow?
6. How do you configure and manage Connections?
7. Where are Connections stored?
8. How do you securely manage credentials in Airflow?
9. What is the relationship between Hooks and Connections?

## Variables and Configurations

1. What are Variables in Airflow?
2. How do you create and access Variables?
3. What is the difference between Variables and XComs?
4. How are Variables stored?
5. What are environment variables in Airflow?
6. How do you manage configurations across different environments?
7. What is the airflow.cfg file?
8. How do you override default Airflow configurations?

## Monitoring and Logging

1. How do you monitor DAG execution in Airflow?
2. What information is available in the Airflow UI?
3. How do you access task logs?
4. Where are Airflow logs stored by default?
5. How do you configure remote logging?
6. What metrics should you monitor in production Airflow?
7. How do you set up alerting in Airflow?
8. What is the role of the Webserver component?
9. How do you troubleshoot failed tasks?

## Error Handling and Retries

1. How do you handle task failures in Airflow?
2. What are retry parameters and how do you configure them?
3. What is the difference between `retries` and `retry_delay`?
4. What is `retry_exponential_backoff`?
5. How do you implement custom error handling logic?
6. What are SLAs in Airflow?
7. How do you configure timeout for tasks?
8. What is `on_failure_callback` and `on_success_callback`?
9. How do you send alerts when tasks fail?
10. What is task state and what are the different task states?

## Best Practices and Performance

1. What are the best practices for writing efficient DAGs?
2. How do you avoid anti-patterns in Airflow?
3. What is idempotency and why is it important in Airflow?
4. How do you make your Airflow tasks idempotent?
5. How do you optimize DAG performance?
6. What are the recommendations for DAG file organization?
7. How do you handle large data processing in Airflow?
8. What should you avoid putting in DAG definition files?
9. How do you manage DAG complexity?
10. What are the guidelines for task granularity?

## Metadata Database

1. What is the metadata database in Airflow?
2. What information is stored in the metadata database?
3. Which databases are supported as metadata backends?
4. Why should you avoid using SQLite in production?
5. How does Airflow interact with the metadata database?
6. What happens if the metadata database is unavailable?
7. How do you backup the metadata database?
8. What are the performance considerations for the metadata database?

## Triggers and Sensors

1. What are Sensors in Airflow?
2. What is the difference between a Sensor and a regular Operator?
3. What is `poke_interval` and `timeout` in Sensors?
4. What are the different sensor modes (poke vs reschedule)?
5. When should you use reschedule mode?
6. What are some commonly used Sensors?
7. How do you create a custom Sensor?
8. What is the FileSensor and when would you use it?
9. What is the ExternalTaskSensor?
10. How do Sensors impact scheduler performance?

## Advanced Topics

1. What are Pools in Airflow and why are they used?
2. How do you implement priority weighting for tasks?
3. What are DAG dependencies and how do you implement them?
4. What is the TriggerDagRunOperator?
5. How do you implement dynamic task generation?
6. What are Airflow Plugins?
7. How do you create and use custom plugins?
8. What is TaskFlow API and how does it differ from traditional operators?
9. What are Datasets in Airflow 2.0+?
10. How do you implement data-aware scheduling?

## Airflow 2.x Features

1. What are the major differences between Airflow 1.x and 2.x?
2. What is the TaskFlow API?
3. How do decorators work in Airflow 2.0?
4. What are the benefits of using the TaskFlow API?
5. What is the Scheduler HA feature in Airflow 2.0?
6. What improvements were made to the UI in Airflow 2.0?
7. How has the REST API evolved in Airflow 2.0?
8. What is the role of providers in Airflow 2.0?

## Security

1. How do you secure an Airflow deployment?
2. What authentication methods does Airflow support?
3. How do you implement role-based access control (RBAC)?
4. How do you encrypt sensitive information in Airflow?
5. What is Fernet key and why is it important?
6. How do you manage secrets in Airflow?
7. What are Secrets Backends?
8. How do you integrate Airflow with external secret management systems?

## Deployment and Scaling

1. How do you deploy Airflow in production?
2. What are the considerations for scaling Airflow?
3. How do you containerize Airflow using Docker?
4. What is the official Airflow Helm chart?
5. How do you deploy Airflow on Kubernetes?
6. What are the resource requirements for running Airflow?
7. How do you implement high availability for Airflow components?
8. What are the networking considerations for distributed Airflow?
9. How do you manage multiple environments (dev, staging, prod)?

## Integration and External Systems

1. How does Airflow integrate with Apache Spark?
2. What is the SparkSubmitOperator?
3. How do you integrate Airflow with AWS services?
4. What operators are available for Google Cloud Platform?
5. How do you integrate Airflow with databases?
6. How does Airflow work with data warehouses like Snowflake or Redshift?
7. How do you use Airflow with Kubernetes?
8. What is the KubernetesPodOperator?
9. How do you integrate Airflow with messaging systems like Kafka?
10. How do you trigger Airflow DAGs from external systems?

## Scenario-Based Questions

1. How would you migrate a legacy cron-based pipeline to Airflow?
2. How would you handle a situation where a task needs to run only if the previous day's task succeeded?
3. How would you implement a pipeline that processes files as they arrive?
4. How would you design a DAG that handles incremental data loads?
5. How would you troubleshoot a DAG that is running slower than expected?
6. How would you handle timezone-related issues in Airflow?
7. How would you implement a data quality check in an Airflow pipeline?
8. How would you orchestrate a machine learning pipeline using Airflow?
9. How would you handle backfilling data for a specific date range?
10. How would you design a fault-tolerant data pipeline using Airflow?
11. How would you implement a pipeline that depends on multiple external data sources?
12. How would you optimize a DAG with hundreds of tasks?
13. How would you implement circuit breaker patterns in Airflow?
14. How would you handle a scenario where upstream data is delayed?
15. How would you coordinate workflows across multiple Airflow instances?

Sources
1. [The Top 21 Airflow Interview Questions and How to Answer ...](https://www.datacamp.com/blog/top-airflow-interview-questions)
2. [50 Apache Airflow Interview Questions and Answers](https://www.projectpro.io/article/airflow-interview-questions-and-answers/685)
3. [Deloitte Airflow interview questions for Data Engineer 2024 ...](https://www.linkedin.com/posts/shubhamwadekar_deloitte-airflow-interview-questions-for-activity-7205532984186163200-25rQ)
4. [Top 20 Airflow Interview Questions and Answers (2025)](https://www.hirist.tech/blog/top-20-airflow-interview-questions-and-answers/)
5. [Airflow Interview Questions](https://cloudfoundation.com/blog/airflow-interview-questions/)
6. [Top 90+ Data Engineer Interview Questions and Answers](https://www.netcomlearning.com/blog/data-engineer-interview-questions)
7. [Top 35 Apache Airflow Interview Questions](https://360digitmg.com/blog/data-engineer-apache-airflow-interview-questions)
8. [Top 50 data engineering interview Questions and Answers](https://browsejobs.in/top-50-data-engineering-interview-questions/)
9. [Airflow — Interview Questions VI - Dev Genius](https://blog.devgenius.io/airflow-interview-questions-vi-2047bef398d4)
10. [ ▷ Top 20 Airflow Interview Questions 2025](https://mindmajix.com/airflow-interview-questions)
