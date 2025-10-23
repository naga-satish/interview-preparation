# Comprehensive PySpark Interview Questions by Topic

## PySpark Fundamentals

1. What is PySpark and how does it differ from Apache Spark?
2. Explain the PySpark architecture and its core components
3. What is SparkContext and SparkSession? When do you use each?
4. How does PySpark enable distributed computing?
5. What is the role of Py4j in PySpark?
6. Explain the concept of lazy evaluation in PySpark
7. What are the different deployment modes in PySpark?
8. How do you create a SparkSession in PySpark?

## RDD (Resilient Distributed Datasets)

1. What is an RDD and what are its key characteristics?
2. Explain the difference between RDD transformations and actions
3. What are the different ways to create an RDD in PySpark?
4. How does RDD achieve fault tolerance?
5. What happens if RDD partitions are lost due to worker node failure?
6. Explain RDD lineage and DAG (Directed Acyclic Graph)
7. What is the difference between persist() and cache() in RDD?
8. When would you choose RDD over DataFrame?

## PySpark DataFrames and Datasets

1. What is a DataFrame in PySpark and how does it differ from RDD?
2. How do you read a CSV file into a DataFrame?
3. How do you display the schema of a DataFrame?
4. What are the different ways to create a DataFrame in PySpark?
5. How do you select specific columns from a DataFrame?
6. Explain the difference between select() and selectExpr()
7. How do you rename columns in a PySpark DataFrame?
8. How do you convert a PySpark DataFrame to a Pandas DataFrame?
9. What are the performance implications of converting to Pandas?
10. How do you handle schema inference in DataFrames?

## Transformations

1. Explain the difference between narrow and wide transformations
2. What are examples of narrow transformations?
3. What are examples of wide transformations?
4. How do you filter rows in a DataFrame?
5. How do you perform map and flatMap operations?
6. What is the difference between map() and mapPartitions()?
7. How do you use withColumn() to create derived columns?
8. Explain the union() and unionAll() operations
9. How do you remove duplicate rows from a DataFrame?
10. What is the difference between distinct() and dropDuplicates()?

## Aggregations and Grouping

1. How do you perform group by operations in PySpark?
2. What aggregation functions are available in pyspark.sql.functions?
3. How do you implement custom aggregations in PySpark?
4. Explain the use of agg() with multiple aggregation functions
5. How do you perform window functions in PySpark?
6. What is the difference between groupBy() and window functions?
7. How do you calculate running totals using window functions?
8. How do you rank data within groups?
9. What is pivot and unpivot in PySpark?
10. How do you handle null values during aggregations?

## Joins and Data Manipulation

1. What are the different types of joins available in PySpark?
2. How do you perform an inner join between two DataFrames?
3. Explain left outer join, right outer join, and full outer join
4. What is a cross join and when should it be avoided?
5. What is a broadcast join and when should you use it?
6. How do you optimize joins for large datasets?
7. What is data skew and how does it affect joins?
8. How do you handle null values in joins?
9. What is the difference between join() and union()?
10. How do you perform self-joins in PySpark?

## Data Handling and Quality

1. How do you handle missing or null values in PySpark?
2. What is the difference between na.drop() and na.fill()?
3. How do you replace specific values in a DataFrame?
4. How do you perform data type conversions (casting)?
5. How do you validate data quality in PySpark?
6. How do you detect and handle outliers?
7. What strategies exist for handling duplicate data?
8. How do you perform data sampling in PySpark?
9. How do you split data into training and test sets?
10. What are the best practices for data validation checks?

## User-Defined Functions (UDFs)

1. What is a UDF in PySpark and when should you use it?
2. How do you create and register a UDF?
3. What are the performance implications of using UDFs?
4. What is a Pandas UDF and how does it differ from regular UDFs?
5. How do you use applyInPandas() for custom aggregations?
6. When should you use built-in functions instead of UDFs?
7. How do you handle exceptions in UDFs?
8. What are the different types of Pandas UDFs?
9. How do you test and debug UDFs?
10. What are vectorized UDFs and their benefits?

## Performance Optimization

1. How would you optimize a slow-running PySpark job?
2. What is data partitioning and why is it important?
3. How do you repartition or coalesce a DataFrame?
4. What is the difference between repartition() and coalesce()?
5. When should you use cache() or persist()?
6. What are the different storage levels for persistence?
7. How do you choose the optimal number of partitions?
8. What is broadcast variable and when should you use it?
9. What is an accumulator in PySpark?
10. How do you minimize data shuffling in PySpark?
11. What file formats are best for performance and why?
12. How does partitioning data on disk improve query performance?
13. What is predicate pushdown and how does it help?
14. How do you monitor and debug PySpark job performance?
15. What Spark UI metrics are most important for optimization?

## Spark SQL and Catalyst Optimizer

1. What is Spark SQL and how does it relate to DataFrames?
2. How do you execute SQL queries in PySpark?
3. How do you create temporary views from DataFrames?
4. What is the Catalyst optimizer and how does it work?
5. What are the phases of query optimization in Catalyst?
6. How does Catalyst generate physical execution plans?
7. What is the Tungsten execution engine?
8. How do you view the execution plan of a query?
9. What is the difference between logical and physical plans?
10. How does cost-based optimization work in Spark SQL?

## File Formats and I/O Operations

1. What file formats does PySpark support?
2. What are the advantages of Parquet format?
3. How does columnar storage benefit analytical queries?
4. What is the difference between Parquet and ORC formats?
5. When would you use CSV versus Parquet?
6. How do you read and write JSON files in PySpark?
7. How do you handle nested JSON structures?
8. What is the benefit of using compression with file formats?
9. How do you write data with partitioning to disk?
10. What write modes are available (append, overwrite, etc.)?
11. How do you read data from multiple files or directories?
12. How do you handle schema evolution in Parquet files?

## Spark Streaming

1. What is PySpark Streaming and what are its use cases?
2. How does Structured Streaming differ from DStreams?
3. How do you stream data using TCP/IP protocol?
4. What are the common data sources for streaming?
5. How do you implement windowing operations in streaming?
6. What is watermarking in Structured Streaming?
7. How do you handle late-arriving data?
8. What output modes are available in Structured Streaming?
9. How do you ensure exactly-once processing semantics?
10. How do you stream data to Kafka or other sinks?
11. What is checkpoint in streaming and why is it important?
12. How do you monitor streaming query progress?

## Cluster Architecture and Resource Management

1. Explain the Spark cluster architecture
2. What are the roles of Driver and Executor nodes?
3. What is the Cluster Manager and what types exist?
4. How do you configure executor memory and cores?
5. What is dynamic allocation in Spark?
6. How do you determine optimal executor configuration?
7. What is the difference between client and cluster deploy mode?
8. How does data locality affect performance?
9. What happens during stage failures in a Spark job?
10. How do you handle out-of-memory errors?

## Fault Tolerance and Reliability

1. How does PySpark ensure fault tolerance?
2. What is checkpointing and when should you use it?
3. How does data replication contribute to fault tolerance?
4. What is write-ahead logging in streaming applications?
5. How does Spark recover from executor failures?
6. What happens when the driver fails?
7. How do you implement retry mechanisms for failed tasks?
8. What is speculative execution in Spark?
9. How do you handle data corruption issues?
10. What are best practices for building fault-tolerant applications?

## Delta Lake and Advanced Topics

1. What is Delta Lake and how does it enhance PySpark?
2. What are the key features of Delta Lake?
3. How does Delta Lake provide ACID transactions?
4. What is time travel in Delta Lake?
5. How do you perform upserts (merge operations) in Delta Lake?
6. What is schema evolution in Delta Lake?
7. How does Delta Lake handle concurrent writes?
8. What is Z-ordering optimization?
9. How do you implement Change Data Capture (CDC) with Delta?
10. What are the benefits of using Delta Lake over Parquet?

## Machine Learning with MLlib

1. What is MLlib in PySpark?
2. How do you prepare data for machine learning in PySpark?
3. What is a feature vector and how do you create it?
4. What transformers and estimators are available in MLlib?
5. How do you implement a machine learning pipeline?
6. How do you perform feature engineering in PySpark?
7. What algorithms does MLlib support?
8. How do you train and evaluate models in PySpark?
9. How do you perform hyperparameter tuning?
10. How do you save and load trained models?

## Scenario-Based and Coding Questions

1. How would you remove duplicates based on specific columns?
2. How do you find the top N records per group?
3. How would you implement a slowly changing dimension (SCD) Type 2?
4. How do you calculate cumulative sums across partitions?
5. How would you handle a dataset with extreme data skew?
6. How do you implement complex business logic across multiple tables?
7. How would you optimize a job that processes terabytes of data daily?
8. How do you implement incremental data processing?
9. How would you debug a job failing with out-of-memory errors?
10. How do you implement data quality checks in production pipelines?
11. How would you handle streaming data from multiple sources?
12. How do you implement real-time aggregations on streaming data?
13. How would you design a data pipeline for a reporting system?
14. How do you handle late-arriving facts in a data warehouse?
15. How would you implement data lineage tracking in PySpark?

Sources
1. [Top 36 PySpark Interview Questions and Answers for 2025](https://www.datacamp.com/blog/pyspark-interview-questions)
2. [PySpark Interview Questions (2025)](https://www.youtube.com/watch?v=fOCiis31Ng4)
3. [Top PySpark Interview Questions and Answers (2025)](https://www.interviewbit.com/pyspark-interview-questions/)
4. [Lokesh Tendulkar's Post](https://www.linkedin.com/posts/certification-champs_jobinterviews-pyspark-dataengineer-activity-7149877251193344001-FYFS)
5. [60+ Must-Know Pyspark Interview Questions and Answers](https://k21academy.com/data-engineering/pyspark-interview-questions/)
6. [50 PySpark Interview Questions and Answers For 2025](https://www.projectpro.io/article/pyspark-interview-questions-and-answers/520)
7. [8 PySpark Interview Questions and How to Answer Them](https://www.coursera.org/in/articles/pyspark-interview-questions)
