# Comprehensive ETL/ELT Interview Questions by Topic

## Table of Contents
1. [ETL/ELT Fundamentals](#etlelt-fundamentals)
   - What is ETL and explain its three stages
   - What is ELT and how does it differ from ETL
   - When would you choose ETL over ELT
   - Advantages and disadvantages of ETL vs ELT
   - Three-layer architecture of ETL cycle
   - ETL mapping sheets
   - Role of staging area
   - Full load vs incremental load
   - Handling slowly changing dimensions
   - Types of SCDs (0, 1, 2, 3)

2. [Data Transformation and Quality](#data-transformation-and-quality)
   - Common data transformation operations
   - Data cleansing techniques
   - Data validation techniques
   - Ensuring data quality
   - Data profiling
   - Handling null values
   - Data standardization
   - Data deduplication
   - Lookup transformations
   - Data type conversions

3. [ETL Performance and Optimization](#etl-performance-and-optimization)
   - Performance optimization strategies
   - Large volume data processing
   - Parallel processing
   - Identifying and resolving bottlenecks
   - Partitioning strategies
   - SQL query optimization
   - Pushdown vs in-memory processing
   - Monitoring ETL job performance
   - Big data handling techniques
   - Implementing incremental loads

4. [Data Warehousing Concepts](#data-warehousing-concepts)
   - Data warehouse vs database
   - Star schema
   - Snowflake schema
   - Fact and dimension tables
   - Types of facts (additive, semi-additive, non-additive)
   - Data marts
   - OLTP vs OLAP
   - Surrogate keys
   - Normalization and denormalization
   - Data warehouse architecture design

5. [ETL Testing](#etl-testing)
   - ETL testing overview
   - Types of ETL testing
   - Validating data accuracy
   - Source-to-staging vs staging-to-target testing
   - Testing transformations and business rules
   - Key validation checks
   - Handling rejected records
   - Regression testing
   - Testing business scenarios
   - Validation tools and queries

6. [ETL Tools and Technologies](#etl-tools-and-technologies)
   - Common ETL tools (Informatica, Talend, SSIS, AWS Glue, Airflow)
   - Apache Airflow for orchestration
   - Cloud ETL services (AWS, Azure, GCP)
   - Batch vs real-time streaming tools
   - Python for ETL pipelines
   - Apache Spark in ETL
   - Change Data Capture (CDC)
   - AWS Glue components
   - dbt for transformations
   - Containerization with Docker

7. [Error Handling and Logging](#error-handling-and-logging)
   - Implementing error handling
   - Logging strategies
   - Handling failed records and retry logic
   - Job monitoring and alerting
   - Data lineage tracking
   - Audit log information
   - Transactional consistency
   - Rollback and recovery strategies
   - Data quality alerts
   - Pipeline health metrics

8. [Data Integration Patterns](#data-integration-patterns)
   - Integration patterns (batch, real-time, micro-batch)
   - Real-time data streaming
   - Lambda architecture
   - Kappa architecture
   - Handling heterogeneous sources
   - API-based integration
   - Event-driven architectures
   - Medallion architecture (bronze, silver, gold)
   - Schema evolution handling
   - Data federation vs consolidation

9. [SQL and Database Concepts for ETL](#sql-and-database-concepts-for-etl)
   - Types of SQL joins
   - UNION vs UNION ALL
   - SQL query optimization
   - Window functions
   - DELETE, TRUNCATE, and DROP
   - Upsert (MERGE) operations
   - Indexes and performance
   - Transaction handling
   - Clustered vs non-clustered indexes
   - Common Table Expressions (CTEs)

10. [Data Security and Governance](#data-security-and-governance)
    - Data security implementation
    - Data masking
    - Handling PII
    - Compliance requirements
    - Encryption (in transit and at rest)
    - Role-based access control (RBAC)
    - Data retention policies
    - Data governance
    - Data lineage and metadata management
    - Auditing requirements

11. [Scenario-Based and Problem-Solving](#scenario-based-and-problem-solving)
    - Real-time fraud detection ETL
    - Handling frequent schema changes
    - Legacy system migration
    - Late-arriving dimensions
    - Structured and unstructured data handling
    - Multi-tenant environment ETL
    - Time-series data ETL
    - Data lake implementations
    - Pipeline dependency management
    - Disaster recovery planning

12. [Advanced ETL Concepts](#advanced-etl-concepts)
    - Data virtualization vs ETL
    - Data partitioning strategies
    - Role of metadata
    - Complex business logic transformations
    - Horizontal vs vertical scaling
    - Data quality frameworks
    - Machine learning in ETL
    - Bi-temporal data handling
    - Reverse ETL
    - DataOps practices

## ETL/ELT Fundamentals

1. **What is ETL and explain its three stages (Extract, Transform, Load)?**
   
   ETL is a data integration process that moves data from source systems to a target data warehouse.
   - **Extract**: Retrieve data from various source systems (databases, APIs, files, streams). Handles connection management, data extraction logic, and change detection.
   - **Transform**: Cleanse, validate, and convert data to match target schema. Includes data cleansing, deduplication, aggregation, joining, and applying business rules.
   - **Load**: Insert transformed data into the target data warehouse or database. Can be full load (complete refresh) or incremental load (only new/changed records).

2. **What is ELT and how does it differ from ETL?**
   
   ELT (Extract, Load, Transform) loads raw data into the target system first, then transforms it using the target system's processing power.
   
   **Key differences:**
   - ETL transforms data before loading (transformation happens in a separate engine)
   - ELT loads raw data first, then transforms in-place (leverages destination system's compute)
   - ELT better suited for cloud data warehouses (Snowflake, BigQuery, Redshift) with massive compute power
   - ETL provides better data privacy (sensitive data transformed before reaching target)

3. **When would you choose ETL over ELT and vice versa?**
   
   **Choose ETL when:**
   - Working with on-premise legacy systems with limited compute resources
   - Handling sensitive data requiring transformation before storage
   - Complex transformations need specialized transformation tools
   - Target system has limited processing capabilities
   
   **Choose ELT when:**
   - Using modern cloud data warehouses with powerful compute
   - Need faster data ingestion (transformation can happen asynchronously)
   - Schema-on-read flexibility is beneficial
   - Working with large data volumes where target system can parallelize transformations

4. **What are the advantages and disadvantages of ETL versus ELT?**
   
   **ETL Advantages:**
   - Better for data privacy (transform sensitive data before loading)
   - Works with legacy systems
   - Reduces target system load
   - Cleaner data warehouse (only transformed data stored)
   
   **ETL Disadvantages:**
   - Slower processing (sequential Extract → Transform → Load)
   - Additional infrastructure for transformation layer
   - Less flexible for schema changes
   
   **ELT Advantages:**
   - Faster initial data loading
   - Leverages scalable cloud compute
   - More flexible (raw data available for reprocessing)
   - Lower infrastructure overhead
   
   **ELT Disadvantages:**
   - Stores raw data (privacy concerns, more storage needed)
   - Requires powerful target system
   - Potentially higher cloud compute costs

5. **Explain the three-layer architecture of an ETL cycle (staging, integration, access layers)?**
   
   **Staging Layer:**
   - Temporary storage for raw extracted data
   - Minimal transformations, preserves source system state
   - Enables recovery without re-extracting from source
   - Data is typically truncated after successful load
   
   **Integration Layer (Data Warehouse):**
   - Consolidated, transformed data from multiple sources
   - Implements business rules and data quality checks
   - Historical data storage with SCD implementation
   - Normalized or dimensional model structure
   
   **Access Layer (Data Marts):**
   - Subject-specific subsets of data warehouse
   - Optimized for specific business units or use cases
   - Denormalized for query performance
   - Contains aggregated, summarized data for reporting

6. **What are ETL mapping sheets and why are they important?**
   
   ETL mapping sheets document the transformation logic between source and target systems. They specify:
   - Source table/column mappings to target
   - Transformation rules and business logic
   - Data type conversions
   - Default values and null handling
   - Lookup/reference tables
   
   **Importance:**
   - Provides clear documentation for developers and testers
   - Ensures consistency across ETL development
   - Facilitates impact analysis for changes
   - Required for audit and compliance
   - Enables knowledge transfer and onboarding

7. **What is the role of a staging area in ETL processes?**
   
   The staging area is a temporary storage location between source and target systems.
   
   **Key roles:**
   - **Isolation**: Reduces load on source systems (extract once, transform multiple times)
   - **Recovery**: Enables reprocessing without re-extracting from source
   - **Performance**: Allows complex transformations without impacting source
   - **Checkpoint**: Facilitates incremental processing and error recovery
   - **Audit**: Preserves raw data for validation and troubleshooting
   - **Data integration**: Combines data from multiple sources before transformation

8. **Explain full load versus incremental load in ETL?**
   
   **Full Load:**
   - Extracts and loads all records from source, regardless of changes
   - Target table is truncated and reloaded completely
   - Simple to implement but resource-intensive
   - Used for small tables, initial loads, or when change tracking isn't available
   
   **Incremental Load:**
   - Loads only new or modified records since last extraction
   - Uses change indicators (timestamp, sequence number, CDC flags)
   - More efficient, reduces processing time and resource usage
   - Requires change tracking mechanism in source
   
   Common approaches: Timestamp-based (last_modified_date), CDC (Change Data Capture), Trigger-based, Log-based replication.

9. **How do you handle slowly changing dimensions (SCD) in ETL?**
   
   SCDs manage changes to dimension attributes over time. Implementation approach depends on business requirements:
   
   **Key techniques:**
   - Compare incoming data with existing dimension records
   - Use business keys (natural keys) to identify matching records
   - Apply appropriate SCD type based on attribute classification
   - Maintain effective dates and current flags for historical tracking
   - Use surrogate keys to maintain referential integrity
   - Implement version numbers for Type 2 dimensions
   
   Common detection method: Use hash values (MD5/SHA) of attribute columns to quickly identify changes.

10. **What are the different types of SCDs (Type 0, 1, 2, 3)?**
    
    **Type 0 (Retain Original):**
    - Never update; original value preserved permanently
    - Example: Birth date, original hire date
    
    **Type 1 (Overwrite):**
    - Overwrites old value with new value; no history kept
    - Simple implementation, smallest storage
    - Example: Correcting data entry errors
    
    **Type 2 (Add New Row):**
    - Creates new row for each change; maintains full history
    - Uses surrogate keys, effective dates (start_date, end_date), and current_flag
    - Most common approach for tracking historical changes
    - Example: Customer address changes, employee department changes
    
    **Type 3 (Add New Column):**
    - Adds columns to store limited history (current and previous value)
    - Example: current_address, previous_address
    - Limited history (typically one previous value)
    
    **Type 4 (History Table):**
    - Current values in main dimension, historical values in separate history table
    
    **Type 6 (Hybrid):**
    - Combination of Type 1, 2, and 3 (1+2+3=6)
    - Maintains current and historical values in same row

## Data Transformation and Quality

1. **What are the most common data transformation operations in ETL?**
   
   - **Filtering**: Removing unwanted records based on conditions
   - **Mapping**: Converting source fields to target schema
   - **Aggregation**: Summing, averaging, counting, grouping data
   - **Joining**: Combining data from multiple sources
   - **Sorting**: Ordering data for processing or loading
   - **Data type conversion**: Changing data types (string to date, etc.)
   - **String manipulation**: Concatenation, substring, trim, case conversion
   - **Lookup/enrichment**: Adding reference data from lookup tables
   - **Pivoting/Unpivoting**: Restructuring row-to-column or column-to-row
   - **Data cleansing**: Removing duplicates, handling nulls, standardizing formats
   - **Splitting**: Dividing data into multiple targets based on rules
   - **Derivation**: Calculating new fields from existing ones

2. **How do you handle data cleansing in the transformation phase?**
   
   **Data cleansing involves:**
   - **Standardization**: Normalize formats (dates, phone numbers, addresses)
   - **Validation**: Check against business rules and constraints
   - **Deduplication**: Identify and remove duplicate records
   - **Null handling**: Replace nulls with defaults or derive values
   - **Outlier detection**: Identify and handle statistical anomalies
   - **Correction**: Fix known data quality issues (typos, formatting)
   - **Completeness checks**: Ensure required fields are populated
   - **Consistency checks**: Verify data coherence across fields
   
   **Approaches:**
   - Use data profiling to identify quality issues
   - Apply cleansing rules based on data quality assessment
   - Route bad records to error tables for review
   - Document cleansing rules in transformation logic

3. **What techniques do you use for data validation during ETL?**
   
   - **Schema validation**: Verify data types, lengths, formats match target
   - **Business rule validation**: Check domain-specific constraints (age > 0, valid state codes)
   - **Referential integrity**: Ensure foreign keys exist in reference tables
   - **Range checks**: Validate values within acceptable bounds
   - **Format validation**: Verify email, phone, SSN, date formats using regex
   - **Null checks**: Ensure required fields are not null
   - **Uniqueness checks**: Validate primary key constraints
   - **Cross-field validation**: Check interdependent fields (end_date > start_date)
   - **Data reconciliation**: Compare record counts and totals between source and target
   - **Threshold checks**: Alert if data volumes deviate significantly from expected

4. **How do you ensure data quality throughout the ETL process?**
   
   **Preventive measures:**
   - Implement data quality rules at each stage (extract, transform, load)
   - Use data profiling to understand source data quality
   - Define data quality metrics and SLAs
   - Implement schema validation at source
   
   **Detective measures:**
   - Automated data quality checks during transformation
   - Record counts and checksum validation
   - Outlier and anomaly detection
   - Data quality dashboards and monitoring
   
   **Corrective measures:**
   - Error handling and rejection mechanisms
   - Bad data routing to quarantine tables
   - Automated alerts for quality threshold breaches
   - Data quality incident tracking and resolution
   
   **Governance:**
   - Document data quality rules
   - Maintain data lineage
   - Regular data quality audits
   - Stakeholder communication on quality metrics

5. **What is data profiling and when is it performed?**
   
   Data profiling is the process of examining source data to understand its structure, content, quality, and relationships.
   
   **Activities:**
   - Analyze data patterns, frequencies, distributions
   - Identify null percentages, unique values, duplicates
   - Detect data types, formats, ranges
   - Discover relationships between tables/columns
   - Identify data quality issues and anomalies
   
   **When performed:**
   - **Before ETL design**: Understand source data for mapping and transformation design
   - **During development**: Validate transformation logic
   - **Post-implementation**: Monitor ongoing data quality
   - **After source changes**: Assess impact of schema or data changes
   
   **Benefits:**
   - Informs ETL design decisions
   - Identifies data quality issues early
   - Helps estimate processing time and resources
   - Provides metadata for documentation

6. **How do you handle null values and missing data during transformations?**
   
   **Strategies:**
   - **Default values**: Replace nulls with business-approved defaults (0, 'Unknown', current date)
   - **Derived values**: Calculate from other columns (full_name from first_name + last_name)
   - **Previous value**: Use last known value (forward fill)
   - **Lookup**: Retrieve from reference tables
   - **Rejection**: Route records with critical nulls to error tables
   - **Flagging**: Add indicator column to track imputed values
   - **Statistical imputation**: Use mean, median, mode for numerical fields
   - **NULL preservation**: Keep NULL for optional fields where business-appropriate
   
   **Best practices:**
   - Document null handling rules in mapping sheets
   - Different strategies for different fields based on business criticality
   - Maintain audit trail of null replacements
   - Distinguish between NULL (unknown) and empty string

7. **What is data standardization and why is it important?**
   
   Data standardization transforms data into a consistent, uniform format across all sources.
   
   **Examples:**
   - Date formats: Convert all dates to YYYY-MM-DD
   - Phone numbers: (123) 456-7890 → +1-123-456-7890
   - Addresses: Standardize street abbreviations (St., Street → ST)
   - Currency: Convert all to single currency with consistent decimal places
   - Case: Standardize to UPPER, lower, or Title Case
   - Units: Convert measurements to standard units (lbs to kg)
   
   **Importance:**
   - **Data integration**: Enables combining data from multiple sources
   - **Data quality**: Reduces duplicates caused by format variations
   - **Reporting accuracy**: Ensures consistent aggregations and comparisons
   - **Analytics**: Improves accuracy of analytical models
   - **Compliance**: Meets regulatory requirements for data consistency
   - **Searchability**: Improves query performance and data retrieval

8. **How do you implement data deduplication in ETL pipelines?**
   
   **Approaches:**
   
   **Exact match deduplication:**
   - Use GROUP BY with business key columns
   - Apply DISTINCT or ROW_NUMBER() with PARTITION BY
   - Keep most recent record using max(timestamp)
   
   **Fuzzy matching:**
   - Use similarity algorithms (Levenshtein distance, Soundex)
   - Implement hash functions on normalized values
   - Apply matching rules (addresses, names with typos)
   
   **Implementation strategies:**
   - **During extraction**: Use SELECT DISTINCT from source
   - **In staging**: Hash key columns and identify duplicates
   - **In transformation**: Apply business rules to choose master record
   - **Merge strategy**: Combine data from duplicate records (take best available value per field)
   
   **Tools:**
   - SQL window functions (ROW_NUMBER, RANK)
   - Built-in deduplication components in ETL tools
   - MDM (Master Data Management) tools for complex scenarios

9. **What are lookup transformations and when are they used?**
   
   Lookup transformations retrieve additional data from reference tables based on matching keys.
   
   **Common use cases:**
   - **Dimension key lookup**: Get surrogate keys from dimension tables
   - **Code translation**: Convert codes to descriptions (state code → state name)
   - **Data enrichment**: Add customer details using customer_id
   - **Validation**: Check if value exists in reference table
   - **Derivation**: Calculate values based on lookup table logic
   
   **Types:**
   - **Connected lookup**: Returns values to pipeline
   - **Unconnected lookup**: Called from other transformations (function-like)
   - **Cached lookup**: Loads reference table into memory (fast, limited size)
   - **Uncached lookup**: Queries database for each lookup (slower, unlimited size)
   - **Dynamic lookup**: Updates cache when new values encountered
   
   **Performance considerations:**
   - Cache small, frequently used lookup tables
   - Use database lookups for large reference tables
   - Index lookup keys in reference tables
   - Consider pre-joining in source query for better performance

10. **How do you handle data type conversions and format standardizations?**
    
    **Data type conversions:**
    - **String to Date**: Use TO_DATE(), CAST(), STR_TO_DATE() with format patterns
    - **String to Number**: CAST(), TO_NUMBER() with null handling for invalid values
    - **Number to String**: TO_CHAR() with format masks
    - **Date to String**: Format using DATE_FORMAT(), TO_CHAR()
    - **Implicit conversions**: Avoid relying on database implicit conversions
    
    **Format standardizations:**
    - **Date formats**: Use explicit format patterns (YYYY-MM-DD HH24:MI:SS)
    - **Numeric formats**: Standardize decimal places, thousand separators
    - **String formats**: UPPER(), LOWER(), TRIM(), REGEXP_REPLACE()
    - **Phone/SSN**: Apply regex patterns for consistent formatting
    
    **Best practices:**
    - Handle conversion errors gracefully (try-catch, NULLIF)
    - Validate before conversion to avoid data loss
    - Document conversion rules in mapping sheets
    - Test edge cases (nulls, invalid formats, boundary values)
    - Use staging tables to preserve original values
    - Consider locale-specific formatting (currency, dates)

## ETL Performance and Optimization

1. **What strategies do you use to optimize ETL performance?**
   
   - **Parallel processing**: Process multiple data streams simultaneously
   - **Partitioning**: Divide large datasets into smaller chunks
   - **Incremental loading**: Load only changed data instead of full refresh
   - **Indexing**: Create indexes on join and filter columns
   - **Bulk operations**: Use bulk inserts instead of row-by-row processing
   - **Pushdown optimization**: Push transformations to source database
   - **Caching**: Cache lookup tables and reference data in memory
   - **Minimize transformations**: Reduce unnecessary operations
   - **Optimize SQL**: Write efficient queries, avoid SELECT *
   - **Connection pooling**: Reuse database connections
   - **Compression**: Compress data in transit and storage
   - **Filter early**: Apply filters as early as possible in pipeline
   - **Hardware optimization**: Use appropriate CPU, memory, I/O resources

2. **How do you handle large volume data processing in ETL?**
   
   **Strategies:**
   - **Chunking**: Process data in manageable batches
   - **Parallel processing**: Distribute workload across multiple threads/nodes
   - **Incremental loads**: Extract only changes using CDC or timestamps
   - **Partition-based processing**: Process partitions independently
   - **Compression**: Reduce data volume during transfer
   - **Distributed computing**: Use frameworks like Spark for massive parallelism
   - **Stream processing**: Process data as it arrives for real-time scenarios
   - **Staging optimization**: Use staging tables to break complex operations
   - **Resource allocation**: Allocate sufficient memory, CPU based on volume
   
   **Best practices:**
   - Monitor memory usage to avoid out-of-memory errors
   - Use appropriate batch sizes (too small = overhead, too large = memory issues)
   - Implement checkpointing for recovery
   - Schedule during off-peak hours for batch processing
   - Scale horizontally by adding more processing nodes

3. **What is parallel processing in ETL and how do you implement it?**
   
   Parallel processing executes multiple operations simultaneously to reduce overall processing time.
   
   **Types:**
   - **Pipeline parallelism**: Different stages execute concurrently (extract, transform, load running simultaneously on different batches)
   - **Data parallelism**: Same operation on different data subsets (partitions processed in parallel)
   - **Component parallelism**: Multiple transformations execute simultaneously
   
   **Implementation:**
   - **Partition data**: Split by date ranges, hash keys, or round-robin
   - **Multi-threading**: Use thread pools in application code
   - **Parallel sessions**: Configure ETL tools to run multiple sessions
   - **Database parallelism**: Use parallel hints in SQL (PARALLEL hint in Oracle)
   - **Distributed frameworks**: Spark, Hadoop for cluster-based parallelism
   
   **Considerations:**
   - Ensure data independence between parallel streams
   - Manage shared resources (connections, memory)
   - Handle synchronization and merge results
   - Monitor for bottlenecks (I/O, network, locks)

4. **How do you identify and resolve ETL bottlenecks?**
   
   **Identification:**
   - **Monitoring tools**: Use ETL tool performance dashboards
   - **Execution logs**: Analyze step-by-step execution times
   - **Database profiling**: Check slow queries with execution plans
   - **Resource monitoring**: Track CPU, memory, disk I/O, network usage
   - **Data flow analysis**: Identify stages with longest processing time
   
   **Common bottlenecks:**
   - **Disk I/O**: Slow reads/writes to storage
   - **Network**: Slow data transfer between systems
   - **Database locks**: Contention on target tables
   - **Insufficient memory**: Swapping to disk
   - **Inefficient SQL**: Missing indexes, full table scans
   - **Sequential processing**: Lack of parallelism
   
   **Resolution:**
   - **Optimize queries**: Add indexes, rewrite SQL, use hints
   - **Increase parallelism**: Add parallel sessions/threads
   - **Upgrade hardware**: Add memory, faster disks (SSD)
   - **Partition data**: Enable partition pruning
   - **Cache optimization**: Increase cache sizes for lookups
   - **Bulk operations**: Replace row-by-row with bulk operations
   - **Reduce data volume**: Filter early, select only needed columns

5. **What is partitioning and how does it improve ETL performance?**
   
   Partitioning divides large tables into smaller, manageable segments based on a partition key.
   
   **Types:**
   - **Range partitioning**: By date ranges (monthly, yearly partitions)
   - **Hash partitioning**: By hash function on key column
   - **List partitioning**: By discrete values (regions, categories)
   - **Composite partitioning**: Combination of above (range-hash)
   
   **Performance benefits:**
   - **Partition pruning**: Query scans only relevant partitions
   - **Parallel processing**: Process partitions independently
   - **Faster loads**: Load into specific partitions without affecting others
   - **Easier maintenance**: Archive/purge old partitions quickly
   - **Improved availability**: Partition-level recovery
   - **Better indexing**: Smaller partition-local indexes
   
   **ETL implementation:**
   - Extract data by partition (e.g., current month only)
   - Load into partition-specific targets
   - Enable partition exchange for faster loads
   - Use partition-wise joins for better performance

6. **How do you optimize SQL queries in ETL processes?**
   
   **Query optimization techniques:**
   - **Use indexes**: Create indexes on WHERE, JOIN, ORDER BY columns
   - **Avoid SELECT ***: Select only required columns
   - **Filter early**: Apply WHERE clauses to reduce dataset size
   - **Use EXPLAIN PLAN**: Analyze execution plans for inefficiencies
   - **Optimize JOINs**: Join on indexed columns, consider join order
   - **Avoid functions on indexed columns**: WHERE YEAR(date) = 2024 → WHERE date >= '2024-01-01'
   - **Use EXISTS instead of IN**: For subqueries with large datasets
   - **Batch operations**: Use bulk inserts, MERGE statements
   - **Avoid DISTINCT when unnecessary**: Use GROUP BY or proper joins
   - **Minimize subqueries**: Use CTEs or temp tables for readability
   - **Use UNION ALL instead of UNION**: If duplicates are not an issue
   - **Partition pruning**: Include partition key in WHERE clause
   
   **Best practices:**
   - Update statistics regularly for optimal execution plans
   - Use bind variables to enable query plan caching
   - Avoid implicit data type conversions
   - Consider materialized views for complex aggregations

7. **What is the difference between pushdown optimization and in-memory processing?**
   
   **Pushdown Optimization:**
   - Delegates transformation logic to the source/target database
   - Database executes SQL transformations using its query engine
   - Reduces data movement between systems
   - Leverages database indexing and optimization
   - Best for: SQL-compatible sources, complex SQL operations, large datasets
   
   **Example**: Instead of extracting 1M rows and filtering in ETL tool, push WHERE clause to database to extract only 10K filtered rows.
   
   **In-Memory Processing:**
   - Loads data into ETL engine's memory for transformations
   - Uses application-level processing (not SQL)
   - Faster for complex, non-SQL transformations
   - Limited by available memory
   - Best for: Complex business logic, non-relational transformations, moderate data volumes
   
   **When to use each:**
   - **Pushdown**: Large volumes, SQL-based transforms, powerful source database
   - **In-memory**: Complex non-SQL logic, fast memory-based operations, smaller datasets
   - **Hybrid**: Use pushdown for initial filtering, in-memory for complex transformations

8. **How do you monitor ETL job performance?**
   
   **Monitoring metrics:**
   - **Execution time**: Overall job duration and per-step timing
   - **Record counts**: Rows extracted, transformed, loaded, rejected
   - **Throughput**: Records per second, data volume per hour
   - **Resource utilization**: CPU, memory, disk I/O, network
   - **Error rates**: Failed records, exceptions, warnings
   - **Data quality**: Validation failures, null percentages
   - **SLA compliance**: Jobs completing within defined windows
   
   **Monitoring tools:**
   - ETL tool dashboards (Airflow UI, Informatica Monitor)
   - Database monitoring tools (query performance)
   - Application Performance Monitoring (APM) tools
   - Custom logging and metrics collection
   - Cloud monitoring (CloudWatch, Azure Monitor, Stackdriver)
   
   **Implementation:**
   - Log start/end times for each stage
   - Capture row counts at checkpoints
   - Set up alerts for failures and SLA breaches
   - Create performance dashboards
   - Maintain historical performance data for trend analysis
   - Implement automated notifications for anomalies

9. **What techniques do you use for handling big data in ETL pipelines?**
   
   **Technologies:**
   - **Apache Spark**: Distributed processing with in-memory computation
   - **Hadoop MapReduce**: Batch processing for massive datasets
   - **Cloud data warehouses**: Snowflake, BigQuery, Redshift (MPP architecture)
   - **Streaming platforms**: Kafka, Kinesis for real-time ingestion
   
   **Techniques:**
   - **Distributed processing**: Spread workload across cluster nodes
   - **Columnar storage**: Parquet, ORC for efficient compression and queries
   - **Data partitioning**: Partition by date, hash, range for parallel processing
   - **Lazy evaluation**: Build execution plan before processing (Spark)
   - **Broadcast joins**: Replicate small tables to all nodes
   - **Incremental processing**: Process only new/changed data
   - **Data sampling**: Test transformations on subset before full run
   - **Schema evolution**: Handle schema changes without reprocessing
   
   **Best practices:**
   - Use appropriate file formats (Parquet > CSV for big data)
   - Optimize cluster sizing and autoscaling
   - Cache frequently accessed datasets
   - Minimize shuffling operations
   - Use predicate pushdown and column pruning

10. **How do you implement incremental loads to improve performance?**
    
    **Change detection methods:**
    
    **Timestamp-based:**
    - Use `last_modified_date` or `updated_timestamp` column
    - Extract records WHERE last_modified >= last_run_timestamp
    - Maintain high watermark in control table
    
    **Change Data Capture (CDC):**
    - Database-level tracking of INSERT, UPDATE, DELETE operations
    - Use database logs (Oracle LogMiner, SQL Server CDC)
    - CDC tools: Debezium, Oracle GoldenGate, AWS DMS
    
    **Version/Sequence number:**
    - Use incrementing sequence or version column
    - Extract records WHERE version > last_processed_version
    
    **Trigger-based:**
    - Database triggers populate change tracking table
    - ETL reads from change table
    
    **Hash comparison:**
    - Calculate hash of record columns
    - Compare with stored hash to detect changes
    
    **Implementation pattern:**
    ```sql
    -- Store last run timestamp
    SELECT MAX(last_modified) FROM staging_table
    
    -- Extract only new/changed records
    SELECT * FROM source 
    WHERE last_modified > :last_run_timestamp
    
    -- Update control table with new watermark
    UPDATE etl_control 
    SET last_run_timestamp = CURRENT_TIMESTAMP
    ```
    
    **Benefits:**
    - Reduced processing time (process only changes)
    - Lower resource consumption
    - Minimal impact on source systems
    - Faster time-to-insight for near real-time requirements

## Data Warehousing Concepts

1. **What is a data warehouse and how does it differ from a database?**
   
   A data warehouse is a centralized repository designed for analytical processing and reporting, optimized for read-heavy queries.
   
   **Data Warehouse:**
   - **Purpose**: Analytics, reporting, business intelligence
   - **Design**: Denormalized schemas (star/snowflake)
   - **Operations**: OLAP (Online Analytical Processing)
   - **Data**: Historical, integrated from multiple sources
   - **Optimization**: Read-optimized, complex queries
   - **Updates**: Batch loads, less frequent
   - **Size**: Very large (TB-PB)
   
   **Transactional Database:**
   - **Purpose**: Day-to-day operations, transaction processing
   - **Design**: Normalized schemas (3NF)
   - **Operations**: OLTP (Online Transaction Processing)
   - **Data**: Current operational data
   - **Optimization**: Write-optimized, simple transactions
   - **Updates**: Real-time, frequent INSERT/UPDATE/DELETE
   - **Size**: Smaller, operational data only

2. **What is a star schema and when is it used?**
   
   Star schema is a dimensional modeling design with a central fact table surrounded by dimension tables.
   
   **Structure:**
   - **Fact table** (center): Contains measurable metrics and foreign keys to dimensions
   - **Dimension tables** (points): Contain descriptive attributes, denormalized
   
   **Characteristics:**
   - Simple design, easy to understand
   - Denormalized dimensions (all attributes in one table)
   - Optimized for query performance
   - Fewer joins required
   - Some data redundancy in dimensions
   
   **When to use:**
   - Most common data warehouse design
   - When query performance is priority
   - Business users need simple, intuitive schema
   - Data marts for specific business areas
   - When data redundancy is acceptable
   
   **Example:**
   - Fact: Sales (sale_id, date_key, product_key, customer_key, amount, quantity)
   - Dimensions: Date, Product, Customer, Store

3. **What is a snowflake schema and how does it differ from a star schema?**
   
   Snowflake schema is a normalized version of star schema where dimension tables are normalized into multiple related tables.
   
   **Characteristics:**
   - Dimensions normalized into hierarchies
   - Reduced data redundancy
   - More complex structure with more tables
   - More joins required for queries
   - Lower storage requirements
   
   **Differences from Star Schema:**
   
   | Aspect | Star Schema | Snowflake Schema |
   |--------|-------------|------------------|
   | Normalization | Denormalized dimensions | Normalized dimensions |
   | Complexity | Simple, fewer tables | Complex, more tables |
   | Joins | Fewer joins | More joins |
   | Query Performance | Faster | Slower (more joins) |
   | Storage | Higher (redundancy) | Lower |
   | Maintenance | Easier updates | More complex updates |
   
   **When to use snowflake:**
   - Storage optimization is critical
   - Dimension hierarchies are complex
   - Data integrity and consistency are priorities
   - Dimension update frequency is high

4. **What are fact tables and dimension tables?**
   
   **Fact Tables:**
   - Store quantitative metrics/measurements (facts)
   - Contain foreign keys to dimension tables
   - Grain defines level of detail (e.g., one row per transaction)
   - Large tables with many rows
   - Narrow tables (few columns)
   
   **Types of facts:**
   - Transactional facts: One row per event (sales transactions)
   - Periodic snapshot: State at regular intervals (monthly account balances)
   - Accumulating snapshot: Lifecycle of process (order fulfillment stages)
   
   **Dimension Tables:**
   - Store descriptive attributes/context for facts
   - Provide the "who, what, where, when, why" 
   - Referenced by fact tables via foreign keys
   - Smaller row count than facts
   - Wide tables (many columns)
   - Often denormalized for query performance
   
   **Common dimensions:**
   - Date/Time: Calendar attributes, fiscal periods
   - Product: Product attributes, categories, hierarchies
   - Customer: Customer demographics, segments
   - Geography: Location hierarchies (country → state → city)

5. **What are the different types of facts (additive, semi-additive, non-additive)?**
   
   **Additive Facts:**
   - Can be summed across all dimensions
   - Most common and useful type
   - Examples: sales amount, quantity sold, cost, revenue
   - Can aggregate: SUM(sales) by any dimension combination
   
   **Semi-Additive Facts:**
   - Can be summed across some dimensions but not all
   - Typically cannot sum across time dimension
   - Examples: account balance, inventory level, headcount
   - Valid: SUM(inventory) by product (across stores)
   - Invalid: SUM(inventory) by time (would double-count)
   - Use AVG, MIN, MAX, FIRST, LAST for time dimension
   
   **Non-Additive Facts:**
   - Cannot be summed across any dimension
   - Examples: ratios, percentages, prices, unit costs
   - Use for display or further calculations
   - Examples: profit margin %, temperature, exchange rates
   - Aggregate using: AVG, MIN, MAX, COUNT
   
   **Derived/Calculated Facts:**
   - Calculated from other facts at query time or load time
   - Examples: profit = revenue - cost, average price = revenue / quantity

6. **What is a data mart and how does it relate to a data warehouse?**
   
   A data mart is a subset of a data warehouse focused on a specific business area, department, or subject.
   
   **Characteristics:**
   - Smaller scope than data warehouse
   - Subject-oriented (Sales, Finance, Marketing)
   - Optimized for specific user group needs
   - Faster to implement than full warehouse
   - Better query performance (smaller dataset)
   
   **Relationship to Data Warehouse:**
   
   **Top-down approach (Inmon):**
   - Build enterprise data warehouse first
   - Create data marts from warehouse (dependent data marts)
   - Ensures consistency across organization
   
   **Bottom-up approach (Kimball):**
   - Build independent data marts first
   - Integrate using conformed dimensions
   - Faster time-to-value, incremental delivery
   
   **Types:**
   - **Dependent**: Sourced from enterprise data warehouse
   - **Independent**: Built directly from source systems
   - **Hybrid**: Combination of both approaches
   
   **Benefits:**
   - Improved performance for specific analyses
   - Easier data governance for department
   - Lower cost than full warehouse
   - Focused on specific business requirements

7. **What is OLTP versus OLAP?**
   
   **OLTP (Online Transaction Processing):**
   - **Purpose**: Handle day-to-day transactions
   - **Operations**: INSERT, UPDATE, DELETE dominant
   - **Queries**: Simple, fast, affect few records
   - **Design**: Normalized (3NF) for data integrity
   - **Users**: Large number of concurrent users
   - **Response time**: Milliseconds
   - **Data volume**: GB to TB, current data
   - **Examples**: Order processing, banking transactions, inventory updates
   
   **OLAP (Online Analytical Processing):**
   - **Purpose**: Support analysis and decision-making
   - **Operations**: Complex SELECT queries, aggregations
   - **Queries**: Complex, affect many records
   - **Design**: Denormalized (star/snowflake) for query performance
   - **Users**: Limited number of analysts
   - **Response time**: Seconds to minutes
   - **Data volume**: TB to PB, historical data
   - **Examples**: Sales analysis, trend reporting, financial forecasting
   
   **OLAP Operations:**
   - **Slice**: Filter on one dimension
   - **Dice**: Filter on multiple dimensions
   - **Drill-down**: Navigate from summary to detail
   - **Roll-up**: Navigate from detail to summary
   - **Pivot**: Rotate data view, swap dimensions

8. **What are surrogate keys and why are they used in data warehousing?**
   
   Surrogate keys are artificially generated unique identifiers (typically integers) assigned to dimension records, separate from natural business keys.
   
   **Characteristics:**
   - System-generated (auto-increment, sequence)
   - No business meaning
   - Typically integers for efficiency
   - Immutable once assigned
   
   **Why use surrogate keys:**
   
   **Handle slowly changing dimensions:**
   - Enable multiple versions of same business entity (Type 2 SCD)
   - Same customer can have multiple surrogate keys for different time periods
   
   **Performance:**
   - Integer joins faster than composite or string keys
   - Smaller index size
   - Reduced fact table size
   
   **Data integration:**
   - Handle same business key from different sources
   - Avoid collisions when merging data
   - Source system key changes don't impact warehouse
   
   **Flexibility:**
   - Independent from source system changes
   - Support for missing/unknown dimension values
   - Handle late-arriving dimensions
   
   **Example:**
   - Natural key: Customer_ID = 'CUST12345'
   - Surrogate key: Customer_Key = 789 (warehouse-generated)
   - Fact table stores surrogate key for efficiency

9. **What is data normalization and denormalization in context of warehousing?**
   
   **Normalization:**
   - Process of organizing data to reduce redundancy
   - Divides tables into smaller related tables
   - Uses foreign keys to establish relationships
   - Forms: 1NF, 2NF, 3NF, BCNF
   
   **In warehousing context:**
   - **OLTP databases**: Highly normalized (3NF)
     - Reduces data redundancy
     - Ensures data integrity
     - Optimizes INSERT/UPDATE/DELETE
   - **Data warehouses**: Typically denormalized
     - Snowflake schema uses some normalization
   
   **Denormalization:**
   - Intentionally introducing redundancy for query performance
   - Combines related tables into fewer tables
   - Reduces joins needed for queries
   
   **In warehousing context:**
   - **Star schema dimensions**: Fully denormalized
     - All product attributes in one Product dimension
     - Product → Category → Department hierarchy flattened
   - **Benefits**:
     - Faster query performance (fewer joins)
     - Simpler SQL for business users
     - Better suited for read-heavy analytics
   - **Trade-offs**:
     - More storage space
     - Update complexity
     - Some data redundancy
   
   **Best practice**: Normalize in OLTP, denormalize in OLAP

10. **How do you design a data warehouse architecture?**
    
    **Key design steps:**
    
    **1. Requirements gathering:**
    - Identify business processes and KPIs
    - Define analytical requirements
    - Understand source systems
    - Determine data latency needs
    
    **2. Dimensional modeling:**
    - Identify facts and dimensions
    - Define grain (level of detail)
    - Design star or snowflake schemas
    - Establish conformed dimensions
    
    **3. Architecture layers:**
    - **Source systems**: Operational databases, files, APIs
    - **Staging layer**: Temporary storage for raw data
    - **Integration layer**: Enterprise data warehouse
    - **Presentation layer**: Data marts, OLAP cubes
    - **Access layer**: BI tools, reporting
    
    **4. ETL design:**
    - Data extraction strategies
    - Transformation rules
    - Load patterns (full vs incremental)
    - SCD implementation
    
    **5. Infrastructure:**
    - On-premise vs cloud platform
    - Storage architecture (relational, columnar, data lake)
    - Compute resources and scalability
    - Network and security
    
    **6. Data governance:**
    - Data quality framework
    - Metadata management
    - Security and access control
    - Data lineage tracking
    
    **Design approaches:**
    - **Kimball (bottom-up)**: Build dimensional data marts, integrate with conformed dimensions
    - **Inmon (top-down)**: Build normalized enterprise warehouse, create dependent data marts
    - **Hybrid**: Combine both approaches based on needs

## ETL Testing

1. **What is ETL testing and why is it important?**
   
   ETL testing validates that data is accurately extracted, transformed, and loaded from source to target systems.
   
   **Objectives:**
   - Verify data completeness (all records extracted/loaded)
   - Validate data accuracy (correct transformations applied)
   - Ensure data quality (cleansing rules work correctly)
   - Check performance (meets SLAs)
   - Validate business rules implementation
   
   **Importance:**
   - **Data quality**: Ensures reliable data for business decisions
   - **Business impact**: Incorrect data leads to wrong decisions
   - **Compliance**: Regulatory requirements for data accuracy
   - **Cost**: Prevents expensive fixes after production deployment
   - **Trust**: Builds confidence in data warehouse
   - **Early detection**: Identifies issues before production
   
   **Scope:**
   - Source-to-staging validation
   - Transformation logic verification
   - Staging-to-target validation
   - Data quality checks
   - Performance testing
   - Reconciliation with source systems

2. **What are the different types of ETL testing (validation, transformation, performance)?**
   
   **Data Validation Testing:**
   - Verify data completeness (row counts match)
   - Check data types and formats
   - Validate null handling
   - Verify referential integrity
   - Check primary/unique key constraints
   
   **Transformation Testing:**
   - Validate business rules implementation
   - Check data cleansing rules
   - Verify calculations and aggregations
   - Test lookup and joins
   - Validate data type conversions
   - Check SCD implementations
   
   **Performance Testing:**
   - Measure ETL execution time
   - Test with production-like volumes
   - Identify bottlenecks
   - Verify SLA compliance
   - Stress testing with peak loads
   
   **Data Quality Testing:**
   - Check for duplicates
   - Validate data accuracy
   - Test constraint violations
   - Verify data completeness
   
   **Integration Testing:**
   - End-to-end workflow testing
   - Multiple system integration
   - Dependency management
   
   **Regression Testing:**
   - Re-test after changes
   - Verify existing functionality unaffected
   - Automated test suite execution
   
   **User Acceptance Testing (UAT):**
   - Business user validation
   - Report accuracy verification
   - Real-world scenario testing

3. **How do you validate data accuracy between source and target?**
   
   **Record count validation:**
   ```sql
   -- Source count
   SELECT COUNT(*) FROM source_table WHERE load_date = '2024-01-01'
   
   -- Target count
   SELECT COUNT(*) FROM target_table WHERE load_date = '2024-01-01'
   ```
   
   **Column-level validation:**
   - Compare aggregates (SUM, AVG, MIN, MAX) for numeric columns
   - Check distinct values match for categorical columns
   - Validate null percentages
   
   **Sample data comparison:**
   ```sql
   -- Compare specific records
   SELECT * FROM source WHERE id = 123
   SELECT * FROM target WHERE source_id = 123
   ```
   
   **Hash/checksum validation:**
   - Calculate hash of concatenated columns
   - Compare source and target hashes
   
   **Data profiling:**
   - Compare data distributions
   - Verify value ranges
   - Check pattern consistency
   
   **Reconciliation queries:**
   - Identify records in source but not in target
   - Find mismatched values
   ```sql
   SELECT s.id FROM source s
   LEFT JOIN target t ON s.id = t.source_id
   WHERE t.source_id IS NULL
   ```
   
   **Business rule validation:**
   - Verify calculated fields
   - Check derived values
   - Validate transformations

4. **What is the difference between source-to-staging and staging-to-target testing?**
   
   **Source-to-Staging Testing:**
   
   **Purpose**: Validate extraction accuracy
   
   **Focus areas:**
   - Data completeness (all records extracted)
   - Data accuracy (values match source)
   - Extraction filters work correctly
   - Incremental logic captures changes
   - Source connection stability
   - Minimal transformations validation
   
   **Tests:**
   - Row count reconciliation
   - Sample record comparison
   - Null value preservation
   - Data type retention (or documented conversion)
   - Timestamp-based extraction validation
   
   **Staging-to-Target Testing:**
   
   **Purpose**: Validate transformation and load accuracy
   
   **Focus areas:**
   - Business rules implementation
   - Data transformations correctness
   - Data quality improvements (cleansing, deduplication)
   - SCD implementation
   - Aggregations and calculations
   - Referential integrity
   - Dimension/fact relationships
   
   **Tests:**
   - Transformation logic verification
   - Business rule validation
   - Lookup accuracy
   - Aggregate calculations
   - SCD type verification
   - Data quality metrics
   
   **Key difference**: Source-to-staging validates extraction with minimal transformation; staging-to-target validates complex business logic and transformations.

5. **How do you test data transformations and business rules?**
   
   **Test approach:**
   
   **1. Understand transformation logic:**
   - Review ETL mapping documents
   - Understand business rules
   - Identify input-output expectations
   
   **2. Prepare test data:**
   - Create test datasets with known outcomes
   - Include edge cases (nulls, zeros, max values)
   - Cover all transformation scenarios
   
   **3. Execute transformations:**
   - Run ETL on test data
   - Capture transformation results
   
   **4. Validate results:**
   ```sql
   -- Example: Test calculation
   SELECT 
       source_value1,
       source_value2,
       target_calculated_value,
       (source_value1 * source_value2) AS expected_value,
       CASE 
           WHEN target_calculated_value = (source_value1 * source_value2)
           THEN 'PASS' ELSE 'FAIL' 
       END AS test_result
   FROM test_results
   ```
   
   **5. Test specific scenarios:**
   - **Lookup transformations**: Verify correct values retrieved
   - **Aggregations**: Validate SUM, COUNT, AVG match manual calculations
   - **String functions**: Check CONCAT, SUBSTRING, TRIM outputs
   - **Date transformations**: Verify date calculations, formatting
   - **Conditional logic**: Test all IF-THEN-ELSE branches
   - **Data cleansing**: Validate deduplication, standardization
   
   **6. Negative testing:**
   - Invalid inputs
   - Null handling
   - Data type mismatches
   - Boundary values

6. **What are the key validation checks in ETL testing?**
   
   **Completeness checks:**
   - Record count reconciliation (source vs target)
   - No data loss during transformation
   - All expected files/tables processed
   
   **Accuracy checks:**
   - Column-level data comparison
   - Aggregate value validation (SUM, COUNT, AVG)
   - Sample record verification
   - Business rule correctness
   
   **Consistency checks:**
   - Data format standardization
   - Cross-field validation (end_date >= start_date)
   - Referential integrity maintained
   - Lookup values consistent
   
   **Uniqueness checks:**
   - Primary key constraints
   - Duplicate detection
   - Business key uniqueness
   
   **Null checks:**
   - Required fields not null
   - Null handling per business rules
   - Default value application
   
   **Data type checks:**
   - Target data types match specifications
   - Precision and scale for decimals
   - Date format compliance
   
   **Data quality checks:**
   - Valid value ranges
   - Pattern matching (email, phone, SSN)
   - Outlier detection
   - Constraint violations
   
   **Performance checks:**
   - Execution time within SLA
   - Resource utilization acceptable
   - Throughput meets requirements
   
   **Metadata checks:**
   - Correct number of columns
   - Column names match target schema
   - Audit columns populated (created_date, updated_by)

7. **How do you handle rejected records and error handling in ETL?**
   
   **Rejection handling strategies:**
   
   **1. Error tables:**
   - Create error/reject tables with same structure as target
   - Add error columns: error_code, error_description, error_timestamp
   - Route failed records to error tables
   
   **2. Categorize errors:**
   - **Fatal errors**: Stop processing (connection failures, system errors)
   - **Business errors**: Log and continue (validation failures, constraint violations)
   - **Warnings**: Log but load data (data quality issues)
   
   **3. Error logging:**
   ```sql
   INSERT INTO error_log (
       job_id, record_id, error_type, error_message, 
       source_record, error_timestamp
   ) VALUES (...)
   ```
   
   **4. Notification:**
   - Email alerts for critical errors
   - Dashboard for error monitoring
   - Daily error summary reports
   
   **5. Recovery process:**
   - Manual review and correction
   - Reprocessing capability
   - Error data quarantine and resolution workflow
   
   **6. Testing error handling:**
   - Test with known bad data
   - Verify errors logged correctly
   - Check job continues after non-fatal errors
   - Validate notification mechanisms
   - Test recovery procedures
   
   **Best practices:**
   - Define error handling rules in requirements
   - Document expected error scenarios
   - Track error rates over time
   - Automate error correction where possible
   - Maintain error code catalog

8. **What is regression testing in ETL and when is it performed?**
   
   Regression testing ensures that ETL changes don't break existing functionality.
   
   **When performed:**
   - After ETL code modifications
   - Source system schema changes
   - New transformation logic added
   - ETL tool version upgrades
   - Database upgrades
   - Infrastructure changes
   - Bug fixes implementation
   
   **What to test:**
   - Existing data flows still work
   - Previous transformations unchanged (unless intended)
   - Historical data integrity maintained
   - Downstream reports still accurate
   - Performance hasn't degraded
   - Error handling still functions
   
   **Approach:**
   
   **1. Baseline establishment:**
   - Save current output as baseline
   - Document expected results
   - Store checksums/hashes
   
   **2. Execute tests:**
   - Run ETL with same input data
   - Compare new output with baseline
   - Identify differences
   
   **3. Automated regression testing:**
   ```sql
   -- Compare current vs baseline
   SELECT 'Row count variance' AS test,
          current_count - baseline_count AS variance
   FROM (SELECT COUNT(*) AS current_count FROM current_run) c,
        (SELECT COUNT(*) AS baseline_count FROM baseline_run) b
   ```
   
   **4. Test scope:**
   - Full regression: All ETL jobs (time-consuming)
   - Partial regression: Impacted jobs only
   - Smoke testing: Critical paths only
   
   **Best practices:**
   - Maintain automated test suite
   - Version control test scripts
   - Document expected outcomes
   - Use test data that covers edge cases
   - Include performance benchmarks

9. **How do you test ETL workflows for different business scenarios?**
   
   **Scenario-based testing approach:**
   
   **1. Initial load (full refresh):**
   - Load empty target with all historical data
   - Verify completeness
   - Check performance with full volume
   - Validate dimension seeding
   
   **2. Incremental load:**
   - New records added to source
   - Modified records in source
   - Deleted records in source
   - Verify only changes processed
   
   **3. Slowly Changing Dimensions:**
   - Test Type 1: Verify overwrite
   - Test Type 2: New row created, old row end-dated
   - Test Type 3: Previous value column updated
   - Verify effective dates, current flags
   
   **4. Late-arriving data:**
   - Fact arrives before dimension
   - Historical data arrives out of order
   - Test default/placeholder handling
   
   **5. Data corrections:**
   - Source corrections to previously loaded data
   - Test update vs insert logic
   - Verify audit trail maintained
   
   **6. Missing/incomplete data:**
   - Missing source files
   - Partial data loads
   - Null values in required fields
   - Test error handling
   
   **7. High volume scenarios:**
   - Peak data volumes
   - Multiple concurrent loads
   - Stress test performance
   
   **8. Schema evolution:**
   - New columns in source
   - Dropped columns
   - Data type changes
   - Test adaptability
   
   **9. Business rule changes:**
   - Modified calculation logic
   - New validation rules
   - Changed categorizations
   - Verify backward compatibility
   
   **Test data management:**
   - Create realistic test datasets for each scenario
   - Use production-like volumes for performance testing
   - Maintain test data version control

10. **What tools and queries do you use for ETL validation?**
    
    **SQL Queries:**
    
    ```sql
    -- Row count comparison
    SELECT 'Source' AS source, COUNT(*) AS cnt FROM source_table
    UNION ALL
    SELECT 'Target' AS source, COUNT(*) AS cnt FROM target_table
    
    -- Aggregate validation
    SELECT SUM(amount), AVG(amount), MIN(amount), MAX(amount)
    FROM source_table
    -- Compare with target
    
    -- Find missing records
    SELECT s.id FROM source s
    LEFT JOIN target t ON s.id = t.source_id
    WHERE t.source_id IS NULL
    
    -- Data quality checks
    SELECT 
        COUNT(*) AS total_records,
        COUNT(DISTINCT id) AS unique_records,
        COUNT(*) - COUNT(DISTINCT id) AS duplicates,
        COUNT(*) - COUNT(required_field) AS null_count
    FROM target_table
    
    -- Hash comparison
    SELECT 
        id,
        MD5(CONCAT(col1, col2, col3)) AS source_hash
    FROM source
    -- Compare with target hashes
    ```
    
    **ETL Testing Tools:**
    - **QuerySurge**: Automated ETL testing, data validation
    - **iCEDQ**: Data testing automation
    - **Informatica Data Validation**: Built-in validation features
    - **Talend Data Quality**: Data profiling and validation
    - **Great Expectations**: Python library for data validation
    - **dbt tests**: Built-in testing framework for transformations
    
    **Data Profiling Tools:**
    - **Talend Data Preparation**
    - **Informatica Data Quality**
    - **Apache Griffin**: Open-source data quality platform
    
    **Comparison Tools:**
    - **Beyond Compare**: File and data comparison
    - **WinMerge**: Text and data comparison
    - **SQL Data Compare**: Database comparison tools
    
    **Custom Scripts:**
    - Python scripts using pandas for data comparison
    - Shell scripts for file validation
    - SQL stored procedures for validation
    
    **Automation Frameworks:**
    - **Apache Airflow**: Workflow automation with testing
    - **Jenkins**: CI/CD with automated tests
    - **pytest**: Python testing framework
    
    **Monitoring Tools:**
    - ETL tool built-in monitors
    - Custom dashboards (Grafana, Tableau)
    - Log analysis tools (ELK Stack)

## ETL Tools and Technologies

1. **What are the most common ETL tools (Informatica, Talend, SSIS, AWS Glue, Apache Airflow)?**
   
   **Informatica PowerCenter:**
   - Enterprise-grade ETL tool
   - GUI-based development
   - Strong data quality features
   - High performance and scalability
   - Expensive licensing
   
   **Talend:**
   - Open-source and commercial versions
   - Java code generation
   - Big data integration support
   - Cloud and on-premise options
   - Active community
   
   **Microsoft SSIS (SQL Server Integration Services):**
   - Part of SQL Server stack
   - Visual workflow designer
   - Strong Windows integration
   - Good for Microsoft ecosystems
   - Cost-effective with SQL Server license
   
   **AWS Glue:**
   - Serverless ETL service
   - Auto-scaling, pay-per-use
   - Integrated with AWS ecosystem
   - Python/Scala based
   - Built-in data catalog
   
   **Apache Airflow:**
   - Workflow orchestration platform
   - Python-based DAGs (Directed Acyclic Graphs)
   - Scheduling and monitoring
   - Open-source, highly extensible
   - Not a traditional ETL tool, but orchestrates ETL workflows
   
   **Other notable tools:**
   - **Azure Data Factory**: Cloud ETL/ELT service
   - **Google Cloud Dataflow**: Stream and batch processing
   - **Apache NiFi**: Data flow automation
   - **Pentaho**: Open-source BI and ETL platform

2. **What is Apache Airflow and how is it used for ETL orchestration?**
   
   Apache Airflow is an open-source platform for authoring, scheduling, and monitoring workflows.
   
   **Key concepts:**
   - **DAG (Directed Acyclic Graph)**: Defines workflow structure
   - **Operators**: Define individual tasks (PythonOperator, BashOperator, SQLOperator)
   - **Tasks**: Instances of operators
   - **Scheduler**: Triggers workflows based on schedule
   - **Executor**: Runs tasks (Sequential, Local, Celery, Kubernetes)
   
   **ETL orchestration features:**
   - Schedule ETL jobs (cron-based)
   - Define task dependencies
   - Retry failed tasks
   - Monitor execution via web UI
   - Send alerts on failures
   - Dynamic pipeline generation
   - Backfilling historical data
   
   **Example DAG structure:**
   ```python
   from airflow import DAG
   from airflow.operators.python import PythonOperator
   from datetime import datetime
   
   dag = DAG(
       'etl_pipeline',
       start_date=datetime(2024, 1, 1),
       schedule_interval='@daily'
   )
   
   extract = PythonOperator(task_id='extract', python_callable=extract_data, dag=dag)
   transform = PythonOperator(task_id='transform', python_callable=transform_data, dag=dag)
   load = PythonOperator(task_id='load', python_callable=load_data, dag=dag)
   
   extract >> transform >> load
   ```
   
   **Use cases:**
   - Orchestrating complex ETL workflows
   - Managing dependencies between jobs
   - Scheduling batch processes
   - Coordinating data pipelines across systems

3. **How do you implement ETL pipelines using cloud services (AWS, Azure, GCP)?**
   
   **AWS Stack:**
   - **AWS Glue**: Serverless ETL, data cataloging
   - **AWS Lambda**: Event-driven transformations
   - **S3**: Data lake storage
   - **Redshift**: Data warehouse
   - **DMS (Database Migration Service)**: Database replication, CDC
   - **Step Functions**: Workflow orchestration
   - **Kinesis**: Real-time data streaming
   
   **Azure Stack:**
   - **Azure Data Factory**: Cloud ETL/ELT service
   - **Azure Databricks**: Spark-based processing
   - **Azure Synapse Analytics**: Unified analytics platform
   - **Azure Data Lake Storage**: Scalable storage
   - **Azure Stream Analytics**: Real-time analytics
   - **Azure Logic Apps**: Workflow automation
   
   **GCP Stack:**
   - **Cloud Dataflow**: Batch and stream processing (Apache Beam)
   - **Cloud Dataproc**: Managed Spark/Hadoop
   - **BigQuery**: Data warehouse
   - **Cloud Storage**: Object storage
   - **Cloud Composer**: Managed Airflow
   - **Pub/Sub**: Messaging for streaming
   - **Datastream**: CDC service
   
   **Common patterns:**
   - Ingest raw data to cloud storage (S3, GCS, ADLS)
   - Use serverless compute for transformations
   - Load into cloud data warehouse
   - Orchestrate with native tools (Step Functions, Data Factory, Composer)
   - Leverage managed services for scalability

4. **What is the difference between batch ETL tools and real-time streaming tools?**
   
   **Batch ETL Tools:**
   - Process data in scheduled intervals (hourly, daily)
   - Handle large volumes at once
   - Examples: Informatica, Talend, SSIS, AWS Glue
   - Use cases: Historical reporting, data warehousing
   - Characteristics:
     - Higher latency (minutes to hours)
     - More efficient for large volumes
     - Simpler error handling and recovery
     - Lower cost per record
   
   **Real-time Streaming Tools:**
   - Process data continuously as it arrives
   - Handle events/records immediately
   - Examples: Apache Kafka, Kinesis, Flink, Spark Streaming
   - Use cases: Fraud detection, real-time analytics, monitoring
   - Characteristics:
     - Low latency (milliseconds to seconds)
     - Continuous processing
     - More complex error handling
     - Higher infrastructure costs
   
   **Hybrid Approach (Micro-batch):**
   - Process small batches frequently (every few seconds)
   - Examples: Spark Structured Streaming
   - Balance between batch efficiency and streaming latency
   
   **Choosing between them:**
   - **Batch**: Data can wait, cost-sensitive, large volumes
   - **Streaming**: Real-time decisions needed, event-driven architecture
   - **Hybrid**: Near real-time with batch benefits

5. **How do you use Python for building ETL pipelines?**
   
   **Popular Python libraries:**
   - **pandas**: Data manipulation and transformation
   - **SQLAlchemy**: Database connectivity
   - **pyodbc/psycopg2**: Database drivers
   - **requests**: API data extraction
   - **boto3**: AWS services integration
   - **apache-airflow**: Workflow orchestration
   - **great_expectations**: Data validation
   
   **Basic ETL pattern:**
   ```python
   import pandas as pd
   from sqlalchemy import create_engine
   
   # Extract
   def extract():
       engine = create_engine('postgresql://user:pass@host/db')
       df = pd.read_sql_query("SELECT * FROM source_table", engine)
       return df
   
   # Transform
   def transform(df):
       df['amount'] = df['amount'].fillna(0)
       df['category'] = df['category'].str.upper()
       df['total'] = df['quantity'] * df['price']
       return df
   
   # Load
   def load(df):
       engine = create_engine('postgresql://user:pass@host/warehouse')
       df.to_sql('target_table', engine, if_exists='append', index=False)
   
   # Execute ETL
   data = extract()
   transformed = transform(data)
   load(transformed)
   ```
   
   **Advanced features:**
   - Parallel processing with multiprocessing/concurrent.futures
   - Chunked reading for large files: `pd.read_csv(chunksize=10000)`
   - Error handling and logging
   - Configuration management (config files, environment variables)
   - Unit testing with pytest
   
   **Benefits:**
   - Flexible and customizable
   - Rich ecosystem of libraries
   - Easy integration with APIs and cloud services
   - Version control friendly (code-based)
   - Cost-effective (open-source)

6. **What is Apache Spark and how is it used in ETL processes?**
   
   Apache Spark is a distributed computing framework for large-scale data processing.
   
   **Key features:**
   - **In-memory processing**: 100x faster than MapReduce
   - **Distributed**: Scales horizontally across cluster
   - **Unified**: Batch, streaming, ML, graph processing
   - **Lazy evaluation**: Optimizes execution plan
   - **Fault-tolerant**: Automatic recovery
   
   **Core components:**
   - **Spark Core**: Distributed task execution
   - **Spark SQL**: Structured data processing
   - **Spark Streaming**: Real-time processing
   - **MLlib**: Machine learning
   - **GraphX**: Graph processing
   
   **ETL with PySpark:**
   ```python
   from pyspark.sql import SparkSession
   from pyspark.sql.functions import col, sum, when
   
   spark = SparkSession.builder.appName("ETL").getOrCreate()
   
   # Extract
   df = spark.read.parquet("s3://bucket/source/")
   
   # Transform
   transformed = df.filter(col("amount") > 0) \
       .withColumn("category", when(col("price") > 100, "High").otherwise("Low")) \
       .groupBy("category").agg(sum("amount").alias("total"))
   
   # Load
   transformed.write.mode("overwrite").parquet("s3://bucket/target/")
   ```
   
   **ETL use cases:**
   - Processing massive datasets (TB-PB scale)
   - Complex transformations requiring parallel processing
   - Real-time streaming ETL
   - Data lake processing (Parquet, Delta Lake)
   - Machine learning feature engineering
   
   **Advantages:**
   - Handles big data efficiently
   - Supports multiple data formats
   - Built-in optimization (Catalyst optimizer)
   - Cloud-native (EMR, Databricks, Dataproc)

7. **How do you implement CDC (Change Data Capture) in ETL?**
   
   CDC captures and tracks changes (INSERT, UPDATE, DELETE) in source systems for incremental ETL.
   
   **CDC approaches:**
   
   **1. Database-native CDC:**
   - **Oracle**: LogMiner, Oracle GoldenGate
   - **SQL Server**: Built-in CDC feature, Change Tracking
   - **PostgreSQL**: Logical replication, wal2json
   - **MySQL**: Binary log (binlog) replication
   
   **2. Trigger-based CDC:**
   - Create triggers on source tables
   - Populate change tracking table with before/after values
   - ETL reads from change table
   - Pros: Database-agnostic; Cons: Performance overhead
   
   **3. Timestamp-based:**
   - Use `last_modified_date` column
   - Query: `WHERE last_modified > :last_run`
   - Pros: Simple; Cons: Doesn't capture deletes
   
   **4. Log-based CDC:**
   - Read database transaction logs
   - Tools: Debezium, Maxwell, AWS DMS
   - Pros: Minimal source impact; Cons: Complex setup
   
   **5. Snapshot comparison:**
   - Hash current and previous snapshots
   - Compare to identify changes
   - Pros: Works anywhere; Cons: Resource-intensive
   
   **CDC tools:**
   - **Debezium**: Open-source CDC platform (Kafka-based)
   - **AWS DMS**: Database migration with CDC
   - **Azure Data Factory**: Change data capture activities
   - **Qlik Replicate**: Enterprise CDC solution
   - **Oracle GoldenGate**: Real-time data integration
   
   **Implementation pattern:**
   - CDC tool captures changes → Kafka/queue → ETL consumes changes → Apply to target

8. **What is AWS Glue and what are its key components?**
   
   AWS Glue is a serverless ETL service for data preparation and loading.
   
   **Key components:**
   
   **1. Data Catalog:**
   - Centralized metadata repository
   - Stores table definitions, schemas, partitions
   - Integrates with Athena, Redshift, EMR
   - Crawlers auto-discover schema
   
   **2. Crawlers:**
   - Scan data sources (S3, databases)
   - Infer schema automatically
   - Populate Data Catalog
   - Schedule periodic crawls
   
   **3. ETL Jobs:**
   - Python or Scala scripts
   - Auto-generated code or custom
   - Serverless execution (Apache Spark-based)
   - Support for bookmarks (incremental processing)
   
   **4. Development Endpoints:**
   - Interactive notebook environment
   - Test and develop ETL scripts
   - SageMaker or Zeppelin notebooks
   
   **5. Triggers:**
   - Schedule jobs (cron)
   - Event-based triggers
   - Job chaining (on completion/failure)
   
   **6. Workflows:**
   - Orchestrate multiple jobs and crawlers
   - Define dependencies
   - Monitoring and logging
   
   **Features:**
   - **Automatic scaling**: No server management
   - **Job bookmarks**: Track processed data
   - **Built-in transformations**: ApplyMapping, Filter, Join
   - **Integration**: S3, Redshift, RDS, DynamoDB
   - **Development**: Visual editor or code
   
   **Use case:**
   - S3 data lake ETL
   - Database to warehouse loads
   - Format conversions (CSV to Parquet)
   - Data cataloging and discovery

9. **How do you use dbt (data build tool) for transformations?**
   
   dbt is a transformation tool that enables data analysts to transform data using SQL SELECT statements.
   
   **Key concepts:**
   
   **Models:**
   - SQL SELECT statements defining transformations
   - Materialized as tables, views, or incremental tables
   - Version controlled in Git
   
   **Example model:**
   ```sql
   -- models/staging/stg_orders.sql
   {{ config(materialized='view') }}
   
   SELECT
       order_id,
       customer_id,
       order_date,
       amount,
       status
   FROM {{ source('raw', 'orders') }}
   WHERE status != 'cancelled'
   ```
   
   **Features:**
   
   **1. Modularity:**
   - Break complex SQL into reusable models
   - Reference models: `{{ ref('stg_orders') }}`
   
   **2. Testing:**
   - Built-in tests (unique, not_null, relationships, accepted_values)
   - Custom tests with SQL
   ```yaml
   models:
     - name: stg_orders
       columns:
         - name: order_id
           tests:
             - unique
             - not_null
   ```
   
   **3. Documentation:**
   - Auto-generated documentation
   - Lineage graphs showing dependencies
   
   **4. Incremental models:**
   - Process only new/changed records
   ```sql
   {{ config(materialized='incremental') }}
   
   SELECT * FROM events
   {% if is_incremental() %}
   WHERE event_time > (SELECT MAX(event_time) FROM {{ this }})
   {% endif %}
   ```
   
   **5. Macros:**
   - Reusable Jinja templates
   - Custom functions
   
   **Workflow:**
   - Extract: Load raw data (Fivetran, Stitch, custom)
   - Transform: dbt transforms in warehouse
   - Load: Materialized tables/views in warehouse
   
   **Benefits:**
   - ELT approach (transform in warehouse)
   - Version control and collaboration
   - Testing and documentation built-in
   - Works with modern data warehouses (Snowflake, BigQuery, Redshift)

10. **What is the role of containerization (Docker) in ETL deployments?**
    
    Docker containers package ETL applications with dependencies for consistent deployment.
    
    **Benefits for ETL:**
    
    **1. Consistency:**
    - Same environment in dev, test, production
    - Eliminates "works on my machine" issues
    - Package Python version, libraries, OS dependencies
    
    **2. Isolation:**
    - Each ETL job runs in isolated container
    - No dependency conflicts
    - Multiple Python versions on same host
    
    **3. Portability:**
    - Run anywhere Docker is available
    - Easy migration between on-premise and cloud
    - Consistent across different cloud providers
    
    **4. Scalability:**
    - Quick startup/shutdown
    - Easy horizontal scaling
    - Container orchestration (Kubernetes, ECS)
    
    **5. Version control:**
    - Docker images versioned and stored in registry
    - Easy rollback to previous versions
    - Reproducible builds
    
    **Example Dockerfile for Python ETL:**
    ```dockerfile
    FROM python:3.9-slim
    
    WORKDIR /app
    
    COPY requirements.txt .
    RUN pip install -r requirements.txt
    
    COPY etl_script.py .
    
    CMD ["python", "etl_script.py"]
    ```
    
    **Deployment patterns:**
    - **AWS ECS/Fargate**: Run containers serverlessly
    - **Kubernetes**: Orchestrate complex ETL workflows
    - **Docker Compose**: Multi-container local development
    - **Cloud Run (GCP)**: Serverless container execution
    
    **ETL orchestration:**
    - Airflow in Docker for portable orchestration
    - Each ETL task as separate container
    - Kubernetes operators for Airflow (KubernetesPodOperator)
    
    **Best practices:**
    - Use small base images (alpine, slim)
    - Multi-stage builds to reduce size
    - Don't store credentials in images
    - Tag images with version numbers
    - Scan images for vulnerabilities

## Error Handling and Logging

1. **How do you implement error handling in ETL pipelines?**
   
   **Error handling strategies:**
   
   **1. Error categorization:**
   - **Fatal errors**: Stop processing (connection failures, missing source)
   - **Business errors**: Log and continue (validation failures)
   - **Warnings**: Log informational issues (data quality concerns)
   
   **2. Try-catch mechanisms:**
   ```python
   try:
       extract_data()
   except ConnectionError as e:
       log_error(f"Connection failed: {e}")
       send_alert("ETL Failed - Connection Error")
       sys.exit(1)
   except DataValidationError as e:
       log_error(f"Validation failed: {e}")
       route_to_error_table(bad_records)
       # Continue processing
   ```
   
   **3. Error routing:**
   - Good records → target tables
   - Bad records → error/quarantine tables
   - Include error codes, descriptions, timestamps
   
   **4. Error tables structure:**
   ```sql
   CREATE TABLE error_records (
       error_id INT PRIMARY KEY,
       job_id VARCHAR(100),
       error_timestamp TIMESTAMP,
       error_type VARCHAR(50),
       error_message TEXT,
       source_record TEXT,
       record_id VARCHAR(100)
   )
   ```
   
   **5. Retry logic:**
   - Implement exponential backoff for transient errors
   - Configure retry attempts and intervals
   - Distinguish between retryable and non-retryable errors
   
   **6. Circuit breaker pattern:**
   - Stop processing after threshold of errors
   - Prevents cascading failures
   - Example: Stop job if >10% records fail validation
   
   **7. Graceful degradation:**
   - Use default values when lookups fail
   - Continue with partial data when non-critical source unavailable
   
   **Best practices:**
   - Define error handling strategy in design phase
   - Document expected errors and responses
   - Test error scenarios
   - Monitor error rates and trends

2. **What logging strategies do you use in ETL processes?**
   
   **Logging levels:**
   - **DEBUG**: Detailed diagnostic information
   - **INFO**: General informational messages (job start/end, record counts)
   - **WARNING**: Potential issues that don't stop processing
   - **ERROR**: Errors that require attention
   - **CRITICAL**: Severe errors causing job failure
   
   **What to log:**
   
   **Job-level:**
   - Job start/end timestamps
   - Job parameters and configurations
   - Overall status (success/failure)
   - Total records processed
   - Execution duration
   
   **Step-level:**
   - Each stage start/end (extract, transform, load)
   - Record counts at each stage
   - Step duration
   - Resource utilization
   
   **Error details:**
   - Error type and message
   - Stack trace
   - Failed record details
   - Context (what was being processed)
   
   **Data quality:**
   - Validation failures count
   - Null percentages
   - Duplicate counts
   - Outliers detected
   
   **Implementation:**
   ```python
   import logging
   
   logging.basicConfig(
       level=logging.INFO,
       format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
       handlers=[
           logging.FileHandler('etl.log'),
           logging.StreamHandler()
       ]
   )
   
   logger = logging.getLogger(__name__)
   
   logger.info(f"Starting ETL job: {job_id}")
   logger.info(f"Extracted {row_count} records")
   logger.error(f"Transformation failed: {error}")
   ```
   
   **Centralized logging:**
   - Use log aggregation tools (ELK Stack, Splunk, CloudWatch)
   - Structured logging (JSON format)
   - Correlation IDs for tracking across systems

3. **How do you handle failed records and implement retry logic?**
   
   **Failed record handling:**
   
   **1. Error table routing:**
   - Route failed records to quarantine table
   - Preserve original record
   - Add error metadata
   
   **2. Error file generation:**
   - Write failed records to separate file
   - Include error reason
   - Enable manual review and reprocessing
   
   **3. Dead letter queue:**
   - Use message queue for failed messages
   - Retry from queue
   - Manual intervention for persistent failures
   
   **Retry logic implementation:**
   
   **Retry strategies:**
   ```python
   from tenacity import retry, stop_after_attempt, wait_exponential
   
   @retry(
       stop=stop_after_attempt(3),
       wait=wait_exponential(multiplier=1, min=4, max=10)
   )
   def extract_data():
       # Extraction logic
       pass
   ```
   
   **Custom retry logic:**
   ```python
   def process_with_retry(record, max_retries=3):
       for attempt in range(max_retries):
           try:
               process_record(record)
               return True
           except TransientError as e:
               if attempt == max_retries - 1:
                   log_error(f"Failed after {max_retries} attempts: {e}")
                   route_to_error_table(record, e)
                   return False
               time.sleep(2 ** attempt)  # Exponential backoff
           except PermanentError as e:
               log_error(f"Permanent error: {e}")
               route_to_error_table(record, e)
               return False
   ```
   
   **Retry considerations:**
   - Distinguish transient vs permanent errors
   - Implement exponential backoff
   - Set maximum retry attempts
   - Log each retry attempt
   - Avoid infinite retry loops
   
   **Reprocessing failed records:**
   - Maintain reprocessing queue/table
   - Scheduled jobs to retry failed records
   - Manual correction followed by reprocessing
   - Track retry attempts per record

4. **What is your approach to ETL job monitoring and alerting?**
   
   **Monitoring dimensions:**
   
   **1. Job execution:**
   - Job success/failure status
   - Execution duration
   - Start/end times
   - Schedule adherence (SLA compliance)
   
   **2. Data metrics:**
   - Record counts (source, target, rejected)
   - Data volume processed
   - Data quality metrics
   - Validation failure rates
   
   **3. Performance:**
   - Processing throughput (records/second)
   - Resource utilization (CPU, memory, I/O)
   - Database query performance
   - Bottleneck identification
   
   **4. Error tracking:**
   - Error counts by type
   - Error rate trends
   - Failed job history
   
   **Alerting strategies:**
   
   **Alert triggers:**
   - Job failure
   - SLA breach (job running longer than expected)
   - Error rate exceeds threshold (>5% records failed)
   - Data volume anomaly (50% deviation from average)
   - Missing source files
   - Data quality issues
   
   **Alert channels:**
   - Email notifications
   - Slack/Teams messages
   - PagerDuty for critical issues
   - SMS for after-hours failures
   - Dashboard visual alerts
   
   **Implementation:**
   ```python
   def check_and_alert(metrics):
       if metrics['error_rate'] > 0.05:
           send_alert(
               severity='WARNING',
               message=f"High error rate: {metrics['error_rate']*100}%"
           )
       
       if metrics['duration'] > SLA_THRESHOLD:
           send_alert(
               severity='ERROR',
               message=f"SLA breach: {metrics['duration']} minutes"
           )
   ```
   
   **Monitoring tools:**
   - ETL tool dashboards (Airflow UI, Informatica Monitor)
   - Cloud monitoring (CloudWatch, Azure Monitor, Stackdriver)
   - Custom dashboards (Grafana, Tableau)
   - Application monitoring (New Relic, Datadog)
   
   **Best practices:**
   - Set appropriate thresholds (avoid alert fatigue)
   - Prioritize alerts by severity
   - Include actionable information in alerts
   - Regular alert rule reviews
   - On-call rotation for critical pipelines

5. **How do you implement data lineage tracking?**
   
   Data lineage tracks data flow from source to target through transformations.
   
   **What to track:**
   - Source systems and tables
   - Transformation steps applied
   - Target tables and columns
   - Timestamp of processing
   - Job/process that created data
   - Data quality checks applied
   
   **Implementation approaches:**
   
   **1. Metadata tables:**
   ```sql
   CREATE TABLE data_lineage (
       lineage_id INT PRIMARY KEY,
       source_system VARCHAR(100),
       source_table VARCHAR(100),
       target_table VARCHAR(100),
       transformation_applied VARCHAR(500),
       job_id VARCHAR(100),
       processed_timestamp TIMESTAMP,
       record_count INT
   )
   ```
   
   **2. Audit columns in tables:**
   - created_date, created_by
   - modified_date, modified_by
   - source_system, source_file
   - etl_job_id, load_timestamp
   
   **3. Lineage tools:**
   - **Apache Atlas**: Metadata management and lineage
   - **AWS Glue Data Catalog**: Automatic lineage tracking
   - **Informatica MDM**: Enterprise lineage
   - **dbt**: Automatic lineage graphs
   - **Talend**: Built-in lineage tracking
   
   **4. Code-based lineage:**
   ```python
   def track_lineage(source, target, transformation):
       lineage_record = {
           'source': source,
           'target': target,
           'transformation': transformation,
           'timestamp': datetime.now(),
           'job_id': job_id
       }
       insert_lineage(lineage_record)
   ```
   
   **Benefits:**
   - **Impact analysis**: Understand downstream effects of changes
   - **Debugging**: Trace data issues to source
   - **Compliance**: Demonstrate data provenance
   - **Documentation**: Visual representation of data flows
   - **Root cause analysis**: Identify where data quality issues originated
   
   **Visualization:**
   - DAG (Directed Acyclic Graph) representation
   - Interactive lineage browsers
   - Column-level lineage for detailed tracking

6. **What information should be captured in ETL audit logs?**
   
   **Job execution details:**
   - Job ID/name
   - Job start timestamp
   - Job end timestamp
   - Execution duration
   - Job status (success/failure/partial)
   - User/service account that triggered job
   - Server/environment where job ran
   
   **Data volume metrics:**
   - Source record count
   - Records extracted
   - Records transformed
   - Records loaded to target
   - Records rejected/failed
   - Records updated vs inserted
   - Data volume in bytes
   
   **Data quality metrics:**
   - Validation failures by rule
   - Null value counts
   - Duplicate records found
   - Outliers detected
   - Data cleansing actions taken
   
   **Source/target information:**
   - Source system(s) accessed
   - Source tables/files processed
   - Target tables loaded
   - Database connections used
   - File paths processed
   
   **Error information:**
   - Error count by type
   - Error messages
   - Failed record samples
   - Error severity levels
   
   **Performance metrics:**
   - CPU utilization
   - Memory usage
   - I/O statistics
   - Network throughput
   - Query execution times
   
   **Configuration:**
   - ETL parameters used
   - Configuration version
   - Environment variables
   
   **Audit table schema:**
   ```sql
   CREATE TABLE etl_audit_log (
       audit_id BIGINT PRIMARY KEY,
       job_id VARCHAR(100),
       job_name VARCHAR(200),
       start_time TIMESTAMP,
       end_time TIMESTAMP,
       duration_seconds INT,
       status VARCHAR(20),
       source_system VARCHAR(100),
       source_count BIGINT,
       target_count BIGINT,
       error_count INT,
       inserted_count BIGINT,
       updated_count BIGINT,
       deleted_count BIGINT,
       error_message TEXT,
       executed_by VARCHAR(100)
   )
   ```

7. **How do you handle transactional consistency in ETL?**
   
   Transactional consistency ensures data integrity during ETL operations.
   
   **Strategies:**
   
   **1. Database transactions:**
   ```sql
   BEGIN TRANSACTION;
   
   DELETE FROM target WHERE batch_id = :current_batch;
   INSERT INTO target SELECT * FROM staging;
   UPDATE control_table SET last_run = CURRENT_TIMESTAMP;
   
   COMMIT;
   -- ROLLBACK on error
   ```
   
   **2. All-or-nothing loading:**
   - Load to staging table first
   - Validate all data
   - Atomic swap/rename or truncate-insert to production
   - No partial loads visible to users
   
   **3. Batch/job control:**
   - Assign batch_id to each ETL run
   - All records in batch succeed or fail together
   - Track batch status in control table
   
   **4. Checkpointing:**
   - Save state at consistent points
   - Resume from last successful checkpoint on failure
   - Ensure checkpoint boundaries maintain consistency
   
   **5. Two-phase commit:**
   - Prepare phase: Validate and stage data
   - Commit phase: Atomically apply changes
   - Used when loading across multiple databases
   
   **6. Idempotency:**
   - Design ETL to produce same result if run multiple times
   - Use MERGE/UPSERT instead of INSERT
   - Support reprocessing without duplicates
   
   **7. Isolation levels:**
   - Use appropriate transaction isolation
   - Avoid dirty reads during ETL execution
   - Balance consistency vs performance
   
   **Example pattern:**
   ```python
   def load_with_consistency():
       try:
           # Start transaction
           conn.begin()
           
           # Validate all data
           if not validate_staging_data():
               raise ValidationError("Data validation failed")
           
           # Load data
           truncate_target()
           insert_from_staging()
           update_control_table()
           
           # Commit transaction
           conn.commit()
       except Exception as e:
           # Rollback on any error
           conn.rollback()
           raise e
   ```
   
   **Challenges:**
   - Long-running transactions can cause locks
   - Balance transaction size vs consistency requirements
   - Cross-system consistency requires distributed transactions

8. **What is your strategy for ETL rollback and recovery?**
   
   **Rollback strategies:**
   
   **1. Backup before load:**
   - Create backup of target tables before ETL
   - Restore from backup on failure
   - Space-intensive but simple
   
   **2. Version tracking:**
   - Maintain version number in records
   - Delete records with current version on rollback
   ```sql
   DELETE FROM target WHERE etl_version = :failed_version
   ```
   
   **3. Staging table approach:**
   - Keep staging data until confirmed success
   - Reload from staging if issues detected
   - Clear staging only after validation
   
   **4. Time-based rollback:**
   - Use load_timestamp for identification
   - Delete records loaded in failed batch
   ```sql
   DELETE FROM target WHERE load_timestamp >= :batch_start_time
   ```
   
   **5. Snapshot isolation:**
   - Use database snapshots (SQL Server)
   - Revert to snapshot on failure
   - Available in enterprise databases
   
   **Recovery strategies:**
   
   **1. Checkpoint/restart:**
   - Save progress at regular intervals
   - Resume from last successful checkpoint
   - Avoid reprocessing all data
   
   **2. Incremental reprocessing:**
   - Identify failed records/batches
   - Reprocess only failed portion
   - Use control tables to track what needs reprocessing
   
   **3. Source preservation:**
   - Keep source files until confirmed success
   - Replay from source on failure
   - Archive processed files for historical recovery
   
   **4. State management:**
   ```python
   # Save state
   save_checkpoint({
       'last_processed_id': max_id,
       'timestamp': current_time,
       'status': 'in_progress'
   })
   
   # Recovery
   state = load_checkpoint()
   if state['status'] == 'in_progress':
       resume_from_id(state['last_processed_id'])
   ```
   
   **5. Manual intervention procedures:**
   - Document rollback procedures
   - Maintain runbooks for common failures
   - Define approval process for rollbacks
   
   **Best practices:**
   - Test rollback procedures regularly
   - Automate rollback where possible
   - Maintain recovery time objective (RTO)
   - Document recovery point objective (RPO)
   - Keep audit trail of rollbacks

9. **How do you implement data quality alerts and notifications?**
   
   **Data quality checks:**
   
   **1. Completeness checks:**
   - Record count within expected range
   - All required fields populated
   - No missing source files
   
   **2. Accuracy checks:**
   - Reconciliation with source
   - Aggregates match expectations
   - Referential integrity maintained
   
   **3. Consistency checks:**
   - Cross-field validation
   - Historical trend comparison
   - Business rule compliance
   
   **4. Timeliness checks:**
   - Data arrived within SLA
   - Freshness of data acceptable
   
   **Alert implementation:**
   
   ```python
   def check_data_quality(df, metrics):
       alerts = []
       
       # Completeness
       if df.isnull().sum().sum() > metrics['max_nulls']:
           alerts.append({
               'severity': 'WARNING',
               'type': 'COMPLETENESS',
               'message': f"High null count: {df.isnull().sum().sum()}"
           })
       
       # Volume anomaly
       expected_count = metrics['expected_count']
       actual_count = len(df)
       deviation = abs(actual_count - expected_count) / expected_count
       
       if deviation > 0.2:  # 20% deviation
           alerts.append({
               'severity': 'ERROR',
               'type': 'VOLUME_ANOMALY',
               'message': f"Expected {expected_count}, got {actual_count}"
           })
       
       # Duplicate check
       duplicates = df.duplicated().sum()
       if duplicates > 0:
           alerts.append({
               'severity': 'WARNING',
               'type': 'DUPLICATES',
               'message': f"Found {duplicates} duplicate records"
           })
       
       return alerts
   
   def send_alerts(alerts):
       for alert in alerts:
           if alert['severity'] == 'ERROR':
               send_email(urgent=True, message=alert['message'])
               send_slack(channel='#data-alerts', message=alert['message'])
           elif alert['severity'] == 'WARNING':
               log_warning(alert['message'])
   ```
   
   **Notification channels:**
   - **Email**: Detailed alerts with data samples
   - **Slack/Teams**: Real-time notifications
   - **Dashboards**: Visual indicators
   - **SMS/PagerDuty**: Critical issues only
   - **Ticketing systems**: Create incidents automatically
   
   **Alert configuration:**
   - Define thresholds per metric
   - Set different severity levels
   - Configure escalation rules
   - Suppress duplicate alerts (prevent alert storms)
   - Schedule quiet hours if appropriate
   
   **Alert content:**
   - Clear description of issue
   - Affected data/tables
   - Timestamp of detection
   - Metric values (actual vs expected)
   - Recommended action
   - Runbook link for resolution

10. **What metrics do you track for ETL pipeline health?**
    
    **Performance metrics:**
    - **Execution duration**: Total job runtime
    - **Throughput**: Records processed per second
    - **Resource utilization**: CPU, memory, disk I/O
    - **Query performance**: Slowest queries, execution plans
    - **Parallel efficiency**: Speedup from parallelization
    
    **Reliability metrics:**
    - **Success rate**: Percentage of successful runs
    - **Mean time between failures (MTBF)**
    - **Mean time to recovery (MTTR)**
    - **SLA compliance**: Jobs meeting time windows
    - **Uptime percentage**
    
    **Data quality metrics:**
    - **Completeness**: % of expected records loaded
    - **Accuracy**: % of records passing validation
    - **Duplicate rate**: % of duplicate records
    - **Null rate**: % of null values in required fields
    - **Error rate**: % of rejected records
    
    **Volume metrics:**
    - **Daily/weekly record counts**
    - **Data growth trends**
    - **Source vs target record counts**
    - **Processed vs rejected records**
    
    **Latency metrics:**
    - **Data freshness**: Time from source update to warehouse availability
    - **End-to-end latency**: Source to BI tool
    - **Processing lag**: Behind real-time by X hours
    
    **Cost metrics:**
    - **Compute cost per job**
    - **Storage costs**
    - **Data transfer costs**
    - **Cost per million records processed**
    
    **Dashboard example:**
    ```
    ETL Health Dashboard
    ├── Job Success Rate (Last 30 days): 98.5%
    ├── Average Duration: 45 minutes
    ├── Records Processed Today: 15.2M
    ├── Error Rate: 0.8%
    ├── SLA Compliance: 95%
    ├── Data Freshness: 30 minutes
    └── Top 5 Slowest Jobs
    ```
    
    **Alerting thresholds:**
    - Job duration > 120% of baseline
    - Error rate > 5%
    - Record count deviation > 25%
    - SLA breach
    - Zero records processed (unexpected)
    
    **Tracking implementation:**
    - Store metrics in time-series database
    - Create historical trend analysis
    - Compare current vs historical performance
    - Identify degradation patterns
    - Capacity planning based on trends

## Data Integration Patterns

1. **What are the different data integration patterns (batch, real-time, micro-batch)?**
   
   **Batch Processing:**
   - Process data in scheduled intervals (hourly, daily, weekly)
   - Collects data over time, processes in bulk
   - High latency (minutes to hours)
   - Most efficient for large volumes
   - Examples: Nightly data warehouse loads, monthly reporting
   
   **Characteristics:**
   - Predictable resource usage
   - Easier error handling and recovery
   - Lower cost per record
   - Suitable for historical analysis
   
   **Real-time/Streaming:**
   - Process data continuously as it arrives
   - Event-by-event or small window processing
   - Low latency (milliseconds to seconds)
   - Requires continuous infrastructure
   - Examples: Fraud detection, IoT monitoring, stock trading
   
   **Characteristics:**
   - Immediate insights
   - Complex state management
   - Higher infrastructure cost
   - Requires robust error handling
   
   **Micro-batch:**
   - Hybrid approach: small batches processed frequently
   - Batch intervals: seconds to minutes
   - Balances latency and efficiency
   - Examples: Spark Structured Streaming
   
   **Characteristics:**
   - Near real-time (seconds delay)
   - Batch processing benefits
   - Simpler than pure streaming
   - Good for near real-time analytics
   
   **Choosing the pattern:**
   - **Batch**: Cost-effective, historical data, non-time-sensitive
   - **Real-time**: Critical decisions, immediate action required
   - **Micro-batch**: Near real-time needs without streaming complexity

2. **How do you implement real-time data streaming in ETL/ELT?**
   
   **Streaming architecture components:**
   
   **1. Message brokers/streaming platforms:**
   - **Apache Kafka**: Distributed event streaming
   - **Amazon Kinesis**: AWS managed streaming
   - **Azure Event Hubs**: Azure streaming service
   - **Google Pub/Sub**: GCP messaging service
   
   **2. Stream processing engines:**
   - **Apache Flink**: Stateful stream processing
   - **Spark Structured Streaming**: Micro-batch streaming
   - **Apache Storm**: Real-time computation
   - **Kafka Streams**: Lightweight stream processing
   
   **3. Implementation pattern:**
   ```
   Source Systems → Kafka Topics → Stream Processor → Target (Data Warehouse/Lake)
   ```
   
   **Example with Kafka + Spark:**
   ```python
   from pyspark.sql import SparkSession
   from pyspark.sql.functions import from_json, col
   
   spark = SparkSession.builder.appName("Streaming ETL").getOrCreate()
   
   # Read from Kafka
   df = spark.readStream \
       .format("kafka") \
       .option("kafka.bootstrap.servers", "localhost:9092") \
       .option("subscribe", "transactions") \
       .load()
   
   # Transform
   transformed = df.select(
       from_json(col("value").cast("string"), schema).alias("data")
   ).select("data.*") \
    .filter(col("amount") > 100)
   
   # Write to target
   query = transformed.writeStream \
       .format("parquet") \
       .option("path", "s3://bucket/output") \
       .option("checkpointLocation", "s3://bucket/checkpoint") \
       .start()
   ```
   
   **Key considerations:**
   - **State management**: Maintain processing state across events
   - **Exactly-once semantics**: Ensure no duplicate processing
   - **Watermarking**: Handle late-arriving data
   - **Checkpointing**: Enable recovery from failures
   - **Backpressure handling**: Manage varying ingestion rates
   
   **Use cases:**
   - Real-time analytics dashboards
   - Fraud detection systems
   - IoT sensor data processing
   - Log aggregation and monitoring
   - Real-time recommendations

3. **What is the Lambda architecture for data processing?**
   
   Lambda architecture combines batch and real-time processing for comprehensive data analysis.
   
   **Architecture layers:**
   
   **1. Batch Layer:**
   - Stores master dataset (immutable, append-only)
   - Computes batch views on all historical data
   - High latency but accurate and comprehensive
   - Technologies: Hadoop, Spark batch jobs
   
   **2. Speed Layer:**
   - Processes only recent data in real-time
   - Compensates for batch layer latency
   - Incremental updates with low latency
   - Technologies: Storm, Flink, Spark Streaming
   
   **3. Serving Layer:**
   - Merges batch and real-time views
   - Responds to queries by combining both layers
   - Technologies: Druid, Cassandra, HBase
   
   **Data flow:**
   ```
   Data Sources
      ├─→ Batch Layer → Batch Views ─┐
      │                               ├─→ Serving Layer → Query
      └─→ Speed Layer → Real-time Views ─┘
   ```
   
   **Benefits:**
   - **Accuracy**: Batch layer ensures correctness
   - **Low latency**: Speed layer provides real-time insights
   - **Fault tolerance**: Batch layer can recompute if speed layer fails
   - **Scalability**: Both layers scale independently
   
   **Challenges:**
   - **Complexity**: Maintain two processing systems
   - **Duplicate code**: Similar logic in batch and stream
   - **Operational overhead**: More systems to manage
   - **Data synchronization**: Merge views correctly
   
   **Example use case:**
   - Batch layer: Compute daily sales aggregations from all history
   - Speed layer: Track sales in last few hours
   - Serving layer: Query combines both for complete view

4. **What is the Kappa architecture and how does it differ from Lambda?**
   
   Kappa architecture simplifies Lambda by using only stream processing for all data.
   
   **Core principle:**
   - Everything is a stream (including historical data)
   - Single processing engine for batch and real-time
   - Reprocess historical data by replaying stream
   
   **Architecture:**
   ```
   Data Sources → Kafka (event log) → Stream Processing → Serving Layer
                     ↑_____________replay for reprocessing
   ```
   
   **Components:**
   - **Event log**: Kafka retains all historical events
   - **Stream processor**: Single codebase (Flink, Spark Streaming)
   - **Serving database**: Store processed results
   
   **Key differences from Lambda:**
   
   | Aspect | Lambda | Kappa |
   |--------|--------|-------|
   | Processing layers | Batch + Speed | Stream only |
   | Code maintenance | Two codebases | One codebase |
   | Complexity | Higher | Lower |
   | Reprocessing | Run batch job | Replay stream |
   | Best for | Complex aggregations | Event-driven systems |
   
   **Benefits:**
   - **Simplicity**: Single processing paradigm
   - **No code duplication**: One implementation
   - **Easier maintenance**: Fewer systems
   - **Flexibility**: Reprocess by changing version and replaying
   
   **Challenges:**
   - **Storage**: Must retain all events for replay
   - **Replay time**: Can be slow for large histories
   - **Stream processing complexity**: Must handle all scenarios
   
   **When to use Kappa:**
   - Event-driven systems
   - All processing fits stream paradigm
   - Team expertise in stream processing
   - Simpler operations preferred
   
   **When to use Lambda:**
   - Complex batch algorithms don't fit streaming
   - Very large historical computations
   - Different optimization for batch vs stream

5. **How do you handle data from multiple heterogeneous sources?**
   
   **Challenges:**
   - Different data formats (JSON, CSV, XML, databases)
   - Varying schemas and structures
   - Inconsistent data quality
   - Different update frequencies
   - Multiple communication protocols
   
   **Strategies:**
   
   **1. Source-specific extractors:**
   - Create dedicated connector for each source type
   - Database connectors (JDBC, ODBC)
   - API connectors (REST, SOAP)
   - File processors (CSV, Parquet, Avro)
   - Streaming consumers (Kafka, Kinesis)
   
   **2. Schema harmonization:**
   - Map source schemas to canonical model
   - Use common data model (CDM)
   - Implement schema registry for consistency
   ```python
   # Define canonical schema
   canonical_customer = {
       'customer_id': source1['cust_id'],  # Different field names
       'name': source2['customer_name'],
       'email': standardize_email(source3['email_addr'])
   }
   ```
   
   **3. Data standardization:**
   - Convert to common formats (dates, currencies, addresses)
   - Normalize values (uppercase, trim whitespace)
   - Apply consistent business rules across sources
   
   **4. Master data management:**
   - Create master customer/product records
   - De-duplicate across sources
   - Maintain golden record
   - Cross-reference tables for source mappings
   
   **5. Incremental integration:**
   - Start with critical sources
   - Add sources iteratively
   - Validate each integration separately
   
   **6. Metadata-driven approach:**
   - Configuration files define source properties
   - Generic extractors use metadata
   - Reduces custom code per source
   
   **7. Data quality framework:**
   - Source-specific validation rules
   - Standardized error handling
   - Quality scoring per source
   
   **Technology solutions:**
   - **ETL tools**: Informatica, Talend (pre-built connectors)
   - **Data virtualization**: Denodo, Tibco
   - **Integration platforms**: MuleSoft, Dell Boomi
   - **Custom frameworks**: Python with multiple libraries

6. **What is API-based data integration and when is it used?**
   
   API-based integration extracts data from web services using REST, SOAP, or GraphQL APIs.
   
   **Implementation approach:**
   
   **REST API extraction:**
   ```python
   import requests
   import pandas as pd
   
   def extract_from_api(endpoint, api_key):
       headers = {'Authorization': f'Bearer {api_key}'}
       all_data = []
       page = 1
       
       while True:
           response = requests.get(
               f"{endpoint}?page={page}",
               headers=headers
           )
           
           if response.status_code == 200:
               data = response.json()
               if not data['results']:
                   break
               all_data.extend(data['results'])
               page += 1
           else:
               raise Exception(f"API error: {response.status_code}")
       
       return pd.DataFrame(all_data)
   ```
   
   **Key considerations:**
   
   **1. Pagination:**
   - Handle large result sets split across pages
   - Cursor-based or offset pagination
   - Track position for incremental loads
   
   **2. Rate limiting:**
   - Respect API rate limits
   - Implement exponential backoff
   - Use token bucket algorithm
   ```python
   import time
   from ratelimit import limits, sleep_and_retry
   
   @sleep_and_retry
   @limits(calls=100, period=60)  # 100 calls per minute
   def call_api(url):
       return requests.get(url)
   ```
   
   **3. Authentication:**
   - API keys, OAuth tokens, JWT
   - Secure credential storage
   - Token refresh mechanisms
   
   **4. Error handling:**
   - Retry transient failures (500, 503)
   - Handle permanent errors (400, 401, 404)
   - Circuit breaker for repeated failures
   
   **5. Incremental extraction:**
   - Use timestamp parameters (modified_since)
   - Track last successful extraction
   - Request only changed data
   
   **When to use:**
   - **SaaS applications**: Salesforce, HubSpot, Stripe
   - **Cloud services**: AWS APIs, Google APIs
   - **Real-time data**: Stock prices, weather data
   - **Microservices**: Internal service integration
   - **No direct DB access**: Third-party systems
   
   **Advantages:**
   - Standard interface (HTTP/REST)
   - No direct database access needed
   - Real-time or near real-time data
   - Vendor-supported integration method
   
   **Challenges:**
   - Rate limiting constraints
   - API versioning changes
   - Network reliability dependencies
   - Complex authentication flows
   - Potentially slower than direct DB access

7. **How do you implement event-driven ETL architectures?**
   
   Event-driven ETL triggers processing based on events rather than schedules.
   
   **Architecture components:**
   
   **1. Event sources:**
   - File uploads to S3
   - Database changes (CDC)
   - Message queue messages
   - API webhooks
   - Application events
   
   **2. Event bus/broker:**
   - AWS EventBridge
   - Apache Kafka
   - Azure Event Grid
   - RabbitMQ
   
   **3. Event processors:**
   - AWS Lambda (serverless)
   - Azure Functions
   - Kubernetes jobs
   - Airflow triggered DAGs
   
   **Implementation pattern:**
   ```
   Event Source → Event Broker → Event Processor → ETL Pipeline
   ```
   
   **Example: S3 triggered ETL:**
   ```python
   # AWS Lambda function
   import boto3
   
   def lambda_handler(event, context):
       # Triggered when file uploaded to S3
       s3 = boto3.client('s3')
       
       for record in event['Records']:
           bucket = record['s3']['bucket']['name']
           key = record['s3']['object']['key']
           
           # Process the file
           process_file(bucket, key)
           
           # Trigger downstream ETL
           trigger_etl_pipeline(file_path=f"s3://{bucket}/{key}")
   ```
   
   **Kafka-based event streaming:**
   ```python
   from kafka import KafkaConsumer
   
   consumer = KafkaConsumer(
       'data-events',
       bootstrap_servers=['localhost:9092']
   )
   
   for message in consumer:
       event = json.loads(message.value)
       
       if event['type'] == 'new_data':
           trigger_etl(event['source'])
       elif event['type'] == 'data_updated':
           trigger_incremental_load(event['entity_id'])
   ```
   
   **Benefits:**
   - **Real-time responsiveness**: Process data as it arrives
   - **Resource efficiency**: No idle polling
   - **Scalability**: Process multiple events in parallel
   - **Decoupling**: Source and processor independent
   - **Flexibility**: Easy to add new event handlers
   
   **Use cases:**
   - File arrival triggers ETL
   - Database change triggers downstream updates
   - API events trigger data sync
   - IoT sensor data processing
   - Microservices data integration
   
   **Best practices:**
   - Implement idempotency (handle duplicate events)
   - Use dead letter queues for failed events
   - Monitor event lag and processing times
   - Implement event schema versioning
   - Add circuit breakers for downstream failures

8. **What is the medallion architecture (bronze, silver, gold layers)?**
   
   Medallion architecture organizes data in progressive layers of refinement (common in data lakes).
   
   **Bronze Layer (Raw):**
   - **Purpose**: Ingestion layer, raw data landing zone
   - **Characteristics**:
     - Unprocessed, as-is from source
     - Preserves original structure and format
     - Append-only, immutable
     - May include duplicate or invalid data
   - **Format**: Parquet, Avro, JSON
   - **Use**: Reprocessing, data recovery, audit trail
   
   **Silver Layer (Refined/Cleansed):**
   - **Purpose**: Cleansed and conformed data
   - **Characteristics**:
     - Validated and cleansed
     - Standardized formats
     - Deduplicated
     - Type conversions applied
     - Still relatively granular
   - **Transformations**:
     - Data quality checks
     - Schema enforcement
     - Null handling
     - Deduplication
   - **Use**: Downstream analytics, ML features
   
   **Gold Layer (Curated/Business):**
   - **Purpose**: Business-level aggregates and models
   - **Characteristics**:
     - Aggregated for business use
     - Dimension and fact tables
     - Optimized for consumption
     - BI/reporting ready
   - **Examples**:
     - Daily sales by region
     - Customer 360 views
     - Aggregated metrics
   - **Use**: BI tools, reports, dashboards
   
   **Data flow:**
   ```
   Sources → Bronze (raw) → Silver (cleansed) → Gold (aggregated) → Consumption
   ```
   
   **Example implementation:**
   ```
   /data-lake/
   ├── bronze/
   │   └── sales/2024/01/15/data.parquet (raw)
   ├── silver/
   │   └── sales_cleansed/ (validated, deduplicated)
   └── gold/
       └── sales_by_region/ (aggregated for BI)
   ```
   
   **Benefits:**
   - **Clear separation**: Each layer has defined purpose
   - **Reprocessability**: Bronze layer enables full reprocessing
   - **Progressive refinement**: Incrementally add value
   - **Performance**: Gold layer optimized for queries
   - **Debugging**: Easy to trace data through layers
   
   **Delta Lake implementation:**
   ```python
   # Bronze: Ingest raw data
   df.write.format("delta").mode("append").save("/bronze/sales")
   
   # Silver: Cleanse and validate
   bronze_df = spark.read.format("delta").load("/bronze/sales")
   silver_df = bronze_df.filter(col("amount") > 0).dropDuplicates()
   silver_df.write.format("delta").mode("overwrite").save("/silver/sales")
   
   # Gold: Aggregate for business
   gold_df = silver_df.groupBy("region").agg(sum("amount"))
   gold_df.write.format("delta").mode("overwrite").save("/gold/sales_by_region")
   ```

9. **How do you handle schema evolution in data pipelines?**
   
   Schema evolution manages changes to data structure over time without breaking pipelines.
   
   **Types of schema changes:**
   
   **1. Backward compatible:**
   - Adding new columns (with defaults)
   - Making required fields optional
   - Safe to deploy without reprocessing
   
   **2. Forward compatible:**
   - Removing columns
   - Changing column types (widening)
   - Old code can read new data
   
   **3. Breaking changes:**
   - Renaming columns
   - Changing data types (narrowing)
   - Removing required fields
   - Requires coordinated updates
   
   **Strategies:**
   
   **1. Schema-on-read:**
   - Store raw data without enforcing schema
   - Apply schema at query time
   - Flexible but slower queries
   - Common in data lakes
   
   **2. Schema registry:**
   - Centralized schema management (Confluent Schema Registry)
   - Version schemas
   - Enforce compatibility rules
   ```python
   from confluent_kafka import avro
   from confluent_kafka.avro import AvroProducer
   
   # Schema evolution with Avro
   schema = {
       "type": "record",
       "name": "Customer",
       "fields": [
           {"name": "id", "type": "int"},
           {"name": "name", "type": "string"},
           {"name": "email", "type": ["null", "string"], "default": null}  # New optional field
       ]
   }
   ```
   
   **3. Versioned tables:**
   - Maintain multiple schema versions
   - Route data to appropriate version
   ```
   /data/customers/v1/  (old schema)
   /data/customers/v2/  (new schema)
   ```
   
   **4. Default values:**
   - Provide defaults for new columns
   - Handle missing fields gracefully
   ```python
   df = df.withColumn("new_field", 
                      when(col("new_field").isNull(), lit("default")).otherwise(col("new_field")))
   ```
   
   **5. Schema mapping:**
   - Map old column names to new
   - Transform deprecated structures
   ```python
   # Handle column rename
   if "old_name" in df.columns:
       df = df.withColumnRenamed("old_name", "new_name")
   ```
   
   **6. Gradual migration:**
   - Support both old and new schemas temporarily
   - Dual-write during transition
   - Deprecate old schema after migration complete
   
   **Best practices:**
   - Document schema changes
   - Use backward-compatible changes when possible
   - Test schema evolution scenarios
   - Communicate changes to consumers
   - Maintain schema version in metadata
   - Use format that supports schema evolution (Avro, Parquet)
   
   **Handling in ETL:**
   ```python
   def safe_extract(df, column, default=None):
       """Safely extract column with default if not exists"""
       if column in df.columns:
           return df[column]
       else:
           return default
   
   # Use in transformation
   transformed = pd.DataFrame({
       'id': safe_extract(source_df, 'id'),
       'name': safe_extract(source_df, 'name'),
       'email': safe_extract(source_df, 'email', 'unknown@email.com')
   })
   ```

10. **What is data federation versus data consolidation?**
    
    **Data Consolidation (ETL/ELT approach):**
    
    **Concept:**
    - Physically move and store data in central repository
    - Copy data from sources to data warehouse/lake
    - Data persisted in target system
    
    **Characteristics:**
    - **Data movement**: Extract, transform, load to central location
    - **Storage**: Duplicate data storage
    - **Performance**: Fast queries (local data)
    - **Latency**: Depends on ETL frequency (batch delay)
    - **Ownership**: Centralized data management
    
    **Benefits:**
    - Fast query performance
    - Offline analysis possible
    - Optimized for analytics
    - Historical data easily accessible
    - Single version of truth
    
    **Challenges:**
    - Storage costs (data duplication)
    - Data staleness (refresh lag)
    - Complex ETL pipelines
    - Maintenance overhead
    
    **Use cases:**
    - Data warehousing
    - Historical analysis
    - Complex transformations needed
    - Predictable query patterns
    
    ---
    
    **Data Federation (Virtual integration):**
    
    **Concept:**
    - Query data from sources without moving it
    - Virtual layer provides unified view
    - Data remains in source systems
    
    **Characteristics:**
    - **Data movement**: None (query at runtime)
    - **Storage**: No duplication
    - **Performance**: Depends on source system performance
    - **Latency**: Real-time (always current)
    - **Ownership**: Distributed (sources maintain data)
    
    **Benefits:**
    - Real-time data access
    - No data duplication
    - Lower storage costs
    - Simpler architecture
    - Always up-to-date
    
    **Challenges:**
    - Slower query performance
    - Dependent on source availability
    - Limited transformation capability
    - Network overhead
    - Source system load
    
    **Technologies:**
    - Denodo
    - Tibco Data Virtualization
    - Presto/Trino
    - Apache Drill
    
    **Use cases:**
    - Real-time operational reporting
    - Ad-hoc queries across systems
    - Data exploration
    - When storage constraints exist
    
    ---
    
    **Comparison:**
    
    | Aspect | Consolidation | Federation |
    |--------|---------------|------------|
    | Data location | Centralized | Distributed |
    | Query speed | Fast | Variable |
    | Data freshness | Delayed | Real-time |
    | Storage cost | High | Low |
    | Complexity | ETL complexity | Query complexity |
    | Best for | Analytics | Operational queries |
    
    **Hybrid approach:**
    - Consolidate frequently accessed data
    - Federate rarely accessed or real-time data
    - Best of both worlds
    
    **Example:**
    ```sql
    -- Federated query (virtual)
    SELECT c.customer_name, o.order_total
    FROM remote_source1.customers c
    JOIN remote_source2.orders o ON c.id = o.customer_id
    -- Executed at query time across systems
    
    -- Consolidated query (physical)
    SELECT c.customer_name, o.order_total
    FROM warehouse.dim_customer c
    JOIN warehouse.fact_orders o ON c.customer_key = o.customer_key
    -- Data pre-loaded, fast execution
    ```

## SQL and Database Concepts for ETL

1. **What are the different types of SQL joins and when do you use each?**
   
   **INNER JOIN:**
   - Returns only matching records from both tables
   - Most common join type
   ```sql
   SELECT o.order_id, c.customer_name
   FROM orders o
   INNER JOIN customers c ON o.customer_id = c.customer_id
   ```
   - **Use when**: Need only records with matches in both tables
   
   **LEFT (OUTER) JOIN:**
   - Returns all records from left table, matching records from right
   - NULL for right table when no match
   ```sql
   SELECT c.customer_name, o.order_id
   FROM customers c
   LEFT JOIN orders o ON c.customer_id = o.customer_id
   ```
   - **Use when**: Need all records from primary table, even without matches (e.g., customers without orders)
   
   **RIGHT (OUTER) JOIN:**
   - Returns all records from right table, matching records from left
   - Opposite of LEFT JOIN
   - **Use when**: Need all records from second table (less common, can rewrite as LEFT JOIN)
   
   **FULL (OUTER) JOIN:**
   - Returns all records from both tables
   - NULL where no match on either side
   ```sql
   SELECT c.customer_name, o.order_id
   FROM customers c
   FULL OUTER JOIN orders o ON c.customer_id = o.customer_id
   ```
   - **Use when**: Need all records from both tables, identify unmatched records
   
   **CROSS JOIN:**
   - Cartesian product of both tables
   - Every row from first table with every row from second
   ```sql
   SELECT p.product, r.region
   FROM products p
   CROSS JOIN regions r
   ```
   - **Use when**: Generate all combinations (e.g., product-region matrix)
   
   **SELF JOIN:**
   - Table joined with itself
   ```sql
   SELECT e1.name AS employee, e2.name AS manager
   FROM employees e1
   JOIN employees e2 ON e1.manager_id = e2.employee_id
   ```
   - **Use when**: Hierarchical data, compare rows within same table

2. **What is the difference between UNION and UNION ALL?**
   
   **UNION:**
   - Combines result sets from multiple queries
   - **Removes duplicates** automatically
   - Slower due to duplicate elimination (requires sorting/hashing)
   ```sql
   SELECT customer_id FROM active_customers
   UNION
   SELECT customer_id FROM inactive_customers
   -- Returns unique customer_ids only
   ```
   
   **UNION ALL:**
   - Combines result sets from multiple queries
   - **Keeps all rows including duplicates**
   - Faster (no duplicate check)
   ```sql
   SELECT customer_id FROM active_customers
   UNION ALL
   SELECT customer_id FROM inactive_customers
   -- Returns all rows, duplicates included
   ```
   
   **Key differences:**
   
   | Aspect | UNION | UNION ALL |
   |--------|-------|-----------|
   | Duplicates | Removed | Retained |
   | Performance | Slower | Faster |
   | Use case | Need unique records | Need all records |
   
   **ETL usage:**
   - **UNION ALL**: Preferred in ETL for better performance when duplicates acceptable or don't exist
   - **UNION**: When combining sources that may have overlaps and need unique records
   
   **Requirements for both:**
   - Same number of columns
   - Compatible data types in corresponding columns
   - Column order matters

3. **How do you optimize SQL queries for ETL operations?**
   
   **Indexing strategies:**
   - Create indexes on join columns
   - Index WHERE clause predicates
   - Index ORDER BY columns
   - Use covering indexes for frequently accessed columns
   - Avoid over-indexing (slows INSERT/UPDATE)
   
   **Query optimization techniques:**
   
   **1. Select only needed columns:**
   ```sql
   -- Bad
   SELECT * FROM large_table
   
   -- Good
   SELECT customer_id, order_date, amount FROM large_table
   ```
   
   **2. Use WHERE to filter early:**
   ```sql
   -- Filter before join
   SELECT o.*, c.customer_name
   FROM (SELECT * FROM orders WHERE order_date >= '2024-01-01') o
   JOIN customers c ON o.customer_id = c.customer_id
   ```
   
   **3. Avoid functions on indexed columns:**
   ```sql
   -- Bad (prevents index usage)
   WHERE YEAR(order_date) = 2024
   
   -- Good (uses index)
   WHERE order_date >= '2024-01-01' AND order_date < '2025-01-01'
   ```
   
   **4. Use EXISTS instead of IN for large subqueries:**
   ```sql
   -- Better performance for large subquery
   SELECT * FROM customers c
   WHERE EXISTS (
       SELECT 1 FROM orders o WHERE o.customer_id = c.customer_id
   )
   ```
   
   **5. Optimize joins:**
   - Join on indexed columns
   - Smaller table first (in some databases)
   - Use appropriate join type
   
   **6. Use EXPLAIN PLAN:**
   ```sql
   EXPLAIN SELECT * FROM orders WHERE customer_id = 123
   -- Analyze execution plan for full table scans, index usage
   ```
   
   **7. Batch operations:**
   ```sql
   -- Better than row-by-row
   INSERT INTO target SELECT * FROM source WHERE condition
   ```
   
   **8. Partition pruning:**
   ```sql
   -- Include partition key in WHERE
   SELECT * FROM sales
   WHERE sale_date >= '2024-01-01'  -- Scans only relevant partitions
   ```
   
   **9. Avoid DISTINCT when unnecessary:**
   ```sql
   -- Use GROUP BY or proper joins instead of DISTINCT when possible
   ```
   
   **10. Update statistics:**
   ```sql
   -- Keep statistics current for optimal execution plans
   ANALYZE TABLE customers;
   UPDATE STATISTICS orders;
   ```

4. **What are window functions and how are they used in transformations?**
   
   Window functions perform calculations across a set of rows related to the current row.
   
   **Common window functions:**
   
   **ROW_NUMBER():**
   - Assigns unique sequential number
   ```sql
   SELECT 
       customer_id,
       order_date,
       amount,
       ROW_NUMBER() OVER (PARTITION BY customer_id ORDER BY order_date) AS order_seq
   FROM orders
   -- Use: Number orders per customer, find first/last order
   ```
   
   **RANK() / DENSE_RANK():**
   - Assign rank with/without gaps
   ```sql
   SELECT 
       product_name,
       sales,
       RANK() OVER (ORDER BY sales DESC) AS sales_rank
   FROM products
   -- Use: Top N analysis, ranking products
   ```
   
   **LAG() / LEAD():**
   - Access previous/next row values
   ```sql
   SELECT 
       sale_date,
       amount,
       LAG(amount, 1) OVER (ORDER BY sale_date) AS previous_day_amount,
       amount - LAG(amount, 1) OVER (ORDER BY sale_date) AS day_over_day_change
   FROM daily_sales
   -- Use: Calculate changes, trends, deltas
   ```
   
   **Aggregate window functions:**
   ```sql
   SELECT 
       customer_id,
       order_date,
       amount,
       SUM(amount) OVER (PARTITION BY customer_id) AS customer_total,
       AVG(amount) OVER (PARTITION BY customer_id) AS customer_avg,
       amount / SUM(amount) OVER (PARTITION BY customer_id) AS pct_of_customer_total
   FROM orders
   -- Use: Calculate running totals, moving averages
   ```
   
   **NTILE():**
   - Divide rows into buckets
   ```sql
   SELECT 
       customer_id,
       revenue,
       NTILE(4) OVER (ORDER BY revenue DESC) AS quartile
   FROM customer_revenue
   -- Use: Segment customers, create buckets
   ```
   
   **ETL use cases:**
   - Deduplication (keep ROW_NUMBER() = 1)
   - Calculate running totals
   - Identify first/last occurrences
   - Trend analysis (period-over-period)
   - Data quality (find gaps in sequences)
   - Customer segmentation
   
   **Deduplication example:**
   ```sql
   WITH ranked AS (
       SELECT *,
              ROW_NUMBER() OVER (
                  PARTITION BY customer_id 
                  ORDER BY updated_date DESC
              ) AS rn
       FROM customers
   )
   SELECT * FROM ranked WHERE rn = 1
   -- Keeps most recent record per customer
   ```

5. **What is the difference between DELETE, TRUNCATE, and DROP?**
   
   **DELETE:**
   - DML command, removes rows based on condition
   - Can use WHERE clause (selective deletion)
   - Logged operation (slower, more space)
   - Can be rolled back (in transaction)
   - Triggers fire
   - Doesn't reset identity counters
   ```sql
   DELETE FROM orders WHERE order_date < '2020-01-01'
   ```
   - **Use when**: Selective row deletion, need transaction control
   
   **TRUNCATE:**
   - DDL command, removes all rows
   - No WHERE clause (all or nothing)
   - Minimal logging (faster, less space)
   - Cannot be rolled back (in most databases)
   - Triggers don't fire
   - Resets identity counters
   - Requires ALTER permission
   ```sql
   TRUNCATE TABLE staging_table
   ```
   - **Use when**: Remove all rows quickly, staging table cleanup
   
   **DROP:**
   - DDL command, removes entire table structure
   - Deletes table definition, data, indexes, constraints
   - Cannot be rolled back
   - Frees storage completely
   - Requires DROP permission
   ```sql
   DROP TABLE temp_table
   ```
   - **Use when**: Remove table completely, temporary tables cleanup
   
   **Comparison:**
   
   | Aspect | DELETE | TRUNCATE | DROP |
   |--------|--------|----------|------|
   | Type | DML | DDL | DDL |
   | Removes | Rows | All rows | Table + data |
   | WHERE clause | Yes | No | No |
   | Speed | Slowest | Fast | Fast |
   | Rollback | Yes | No | No |
   | Triggers | Fire | Don't fire | Don't fire |
   | Identity reset | No | Yes | N/A |
   
   **ETL usage:**
   - **DELETE**: Remove old data (archival policy)
   - **TRUNCATE**: Clear staging tables between runs
   - **DROP**: Remove temporary ETL tables

6. **How do you implement upsert (merge) operations in ETL?**
   
   Upsert (UPDATE or INSERT) handles both new and existing records in single operation.
   
   **MERGE statement (SQL Server, Oracle, PostgreSQL 15+):**
   ```sql
   MERGE INTO target t
   USING source s ON t.customer_id = s.customer_id
   WHEN MATCHED THEN
       UPDATE SET 
           t.customer_name = s.customer_name,
           t.email = s.email,
           t.updated_date = CURRENT_TIMESTAMP
   WHEN NOT MATCHED THEN
       INSERT (customer_id, customer_name, email, created_date)
       VALUES (s.customer_id, s.customer_name, s.email, CURRENT_TIMESTAMP)
   ```
   
   **PostgreSQL - INSERT ON CONFLICT:**
   ```sql
   INSERT INTO customers (customer_id, customer_name, email)
   SELECT customer_id, customer_name, email FROM staging
   ON CONFLICT (customer_id)
   DO UPDATE SET
       customer_name = EXCLUDED.customer_name,
       email = EXCLUDED.email,
       updated_date = CURRENT_TIMESTAMP
   ```
   
   **MySQL - INSERT ON DUPLICATE KEY:**
   ```sql
   INSERT INTO customers (customer_id, customer_name, email)
   SELECT customer_id, customer_name, email FROM staging
   ON DUPLICATE KEY UPDATE
       customer_name = VALUES(customer_name),
       email = VALUES(email),
       updated_date = CURRENT_TIMESTAMP
   ```
   
   **Traditional approach (works everywhere):**
   ```sql
   -- Update existing
   UPDATE target t
   SET customer_name = s.customer_name,
       email = s.email
   FROM staging s
   WHERE t.customer_id = s.customer_id
   
   -- Insert new
   INSERT INTO target
   SELECT s.*
   FROM staging s
   LEFT JOIN target t ON s.customer_id = t.customer_id
   WHERE t.customer_id IS NULL
   ```
   
   **Python with pandas:**
   ```python
   import pandas as pd
   from sqlalchemy import create_engine
   
   engine = create_engine('postgresql://...')
   
   # Read existing data
   existing = pd.read_sql("SELECT * FROM customers", engine)
   new_data = pd.read_csv("new_customers.csv")
   
   # Merge (upsert logic)
   merged = pd.concat([existing, new_data]).drop_duplicates(
       subset=['customer_id'], keep='last'
   )
   
   # Truncate and reload
   merged.to_sql('customers', engine, if_exists='replace', index=False)
   ```
   
   **Delta Lake (Spark):**
   ```python
   from delta.tables import DeltaTable
   
   deltaTable = DeltaTable.forPath(spark, "/data/customers")
   
   deltaTable.alias("target").merge(
       source.alias("source"),
       "target.customer_id = source.customer_id"
   ).whenMatchedUpdate(set = {
       "customer_name": "source.customer_name",
       "email": "source.email"
   }).whenNotMatchedInsert(values = {
       "customer_id": "source.customer_id",
       "customer_name": "source.customer_name",
       "email": "source.email"
   }).execute()
   ```
   
   **Performance considerations:**
   - Index on merge key columns
   - Batch upserts when possible
   - Use staging table for large upserts
   - Consider partitioning for very large tables

7. **What are indexes and how do they affect ETL performance?**
   
   Indexes are database structures that improve query performance by allowing faster data retrieval.
   
   **How indexes work:**
   - Create ordered data structure (B-tree, hash, bitmap)
   - Point to actual table rows
   - Speed up SELECT, WHERE, JOIN, ORDER BY operations
   - Slow down INSERT, UPDATE, DELETE operations
   
   **Types of indexes:**
   
   **1. B-tree index (most common):**
   - Balanced tree structure
   - Good for range queries and equality
   - Default index type
   
   **2. Clustered index:**
   - Determines physical order of data
   - One per table
   - Primary key often clustered
   
   **3. Non-clustered index:**
   - Separate structure from data
   - Multiple per table
   - Contains pointers to data rows
   
   **4. Bitmap index:**
   - Good for low-cardinality columns
   - Common in data warehouses
   - Efficient for AND/OR queries
   
   **5. Hash index:**
   - Fast equality lookups
   - No range queries
   
   **Impact on ETL:**
   
   **Negative impacts:**
   - **Slower loads**: Each INSERT/UPDATE must update indexes
   - **Increased load time**: More indexes = slower bulk loads
   - **Maintenance overhead**: Index rebuilds, reorganization
   
   **Positive impacts:**
   - **Faster lookups**: Dimension key lookups in transformations
   - **Faster joins**: Indexed join columns perform better
   - **Faster validation**: Duplicate checks, referential integrity
   
   **ETL strategies:**
   
   **1. Drop and recreate indexes:**
   ```sql
   -- Drop indexes before bulk load
   DROP INDEX idx_customer_email ON customers;
   
   -- Perform bulk load
   INSERT INTO customers SELECT * FROM staging;
   
   -- Recreate indexes
   CREATE INDEX idx_customer_email ON customers(email);
   ```
   
   **2. Disable indexes (SQL Server):**
   ```sql
   ALTER INDEX idx_customer_email ON customers DISABLE;
   -- Load data
   ALTER INDEX idx_customer_email ON customers REBUILD;
   ```
   
   **3. Partition-wise index maintenance:**
   - Load into new partition
   - Index only new partition
   - Don't rebuild entire index
   
   **4. Choose indexes carefully:**
   - Index columns used in WHERE, JOIN
   - Don't over-index (diminishing returns)
   - Consider composite indexes for multi-column queries
   
   **Best practices for ETL:**
   - Fewer indexes during data loading
   - More indexes for query performance
   - Balance based on load frequency vs query frequency
   - Monitor index fragmentation
   - Rebuild indexes during maintenance windows

8. **How do you handle transactions in ETL processes?**
   
   Transactions ensure ACID properties (Atomicity, Consistency, Isolation, Durability) in ETL.
   
   **Transaction basics:**
   ```sql
   BEGIN TRANSACTION;
   
   -- ETL operations
   DELETE FROM target WHERE load_date = CURRENT_DATE;
   INSERT INTO target SELECT * FROM staging;
   UPDATE control_table SET last_load = CURRENT_TIMESTAMP;
   
   COMMIT;  -- Make changes permanent
   -- or ROLLBACK to undo
   ```
   
   **Transaction scope strategies:**
   
   **1. Single large transaction:**
   - All ETL steps in one transaction
   - **Pros**: Complete rollback on failure
   - **Cons**: Long transaction locks, log space issues
   ```sql
   BEGIN TRANSACTION;
       TRUNCATE staging;
       INSERT INTO staging SELECT * FROM source;
       MERGE INTO target ...;
   COMMIT;
   ```
   
   **2. Multiple smaller transactions:**
   - Commit after each logical unit
   - **Pros**: Less locking, smaller log impact
   - **Cons**: Partial rollback complexity
   ```sql
   -- Transaction 1: Extract
   BEGIN; INSERT INTO staging ...; COMMIT;
   
   -- Transaction 2: Transform  
   BEGIN; UPDATE staging ...; COMMIT;
   
   -- Transaction 3: Load
   BEGIN; INSERT INTO target ...; COMMIT;
   ```
   
   **3. Batch transactions:**
   - Process in chunks
   ```python
   batch_size = 10000
   for batch in chunked(data, batch_size):
       with connection.begin():
           load_batch(batch)
   ```
   
   **Isolation levels:**
   
   **READ UNCOMMITTED:**
   - Dirty reads possible
   - Fastest, least locking
   - Risky for ETL
   
   **READ COMMITTED:**
   - No dirty reads
   - Default in most databases
   - Good for most ETL
   
   **REPEATABLE READ:**
   - Consistent reads within transaction
   - More locking
   - Use for critical consistency needs
   
   **SERIALIZABLE:**
   - Highest isolation
   - Most locking, slowest
   - Rarely needed in ETL
   
   **ETL transaction patterns:**
   
   **Pattern 1: Staging + atomic swap:**
   ```sql
   -- Load to temp table (no transaction)
   INSERT INTO customers_temp SELECT * FROM source;
   
   -- Atomic rename (transaction)
   BEGIN TRANSACTION;
       DROP TABLE customers_old;
       ALTER TABLE customers RENAME TO customers_old;
       ALTER TABLE customers_temp RENAME TO customers;
   COMMIT;
   ```
   
   **Pattern 2: All-or-nothing with savepoints:**
   ```sql
   BEGIN TRANSACTION;
       SAVEPOINT extract_done;
       INSERT INTO staging ...;
       
       SAVEPOINT transform_done;
       UPDATE staging ...;
       
       INSERT INTO target ...;
   COMMIT;
   -- Can rollback to savepoints if needed
   ```
   
   **Error handling:**
   ```python
   try:
       conn.begin()
       extract_data()
       transform_data()
       load_data()
       conn.commit()
   except Exception as e:
       conn.rollback()
       log_error(e)
       raise
   ```
   
   **Best practices:**
   - Keep transactions as short as possible
   - Commit frequently for long-running ETL
   - Use appropriate isolation level
   - Monitor transaction log space
   - Handle deadlocks with retry logic
   - Test rollback scenarios

9. **What is the difference between clustered and non-clustered indexes?**
   
   **Clustered Index:**
   
   **Characteristics:**
   - Determines physical storage order of data
   - One per table (data can be sorted only one way)
   - Leaf nodes contain actual data rows
   - Primary key often used as clustered index
   - Faster for range queries on indexed column
   
   **Structure:**
   ```
   Clustered Index B-tree
   ├── Root
   ├── Intermediate nodes
   └── Leaf nodes → Actual data rows
   ```
   
   **Example:**
   ```sql
   CREATE TABLE orders (
       order_id INT PRIMARY KEY CLUSTERED,  -- Physical order by order_id
       customer_id INT,
       order_date DATE,
       amount DECIMAL
   )
   ```
   
   **Pros:**
   - Faster retrieval for range scans
   - No separate lookup needed (data in leaf)
   - Good for frequent range queries
   
   **Cons:**
   - Slower INSERT/UPDATE/DELETE (may reorganize data)
   - Only one per table
   - Page splits can cause fragmentation
   
   ---
   
   **Non-Clustered Index:**
   
   **Characteristics:**
   - Separate structure from data
   - Contains key values and pointers to data
   - Multiple per table (up to database limits)
   - Leaf nodes contain row locators
   - Requires additional lookup to get full row
   
   **Structure:**
   ```
   Non-Clustered Index B-tree
   ├── Root
   ├── Intermediate nodes
   └── Leaf nodes → Pointers to data rows
   ```
   
   **Example:**
   ```sql
   CREATE INDEX idx_customer ON orders(customer_id)
   -- Separate structure, pointers to actual rows
   ```
   
   **Pros:**
   - Multiple indexes per table
   - Faster for specific column lookups
   - Less impact on INSERT/UPDATE/DELETE than clustered
   
   **Cons:**
   - Additional storage overhead
   - Requires extra lookup step (bookmark lookup)
   - Slower than clustered for range scans
   
   ---
   
   **Comparison:**
   
   | Aspect | Clustered | Non-Clustered |
   |--------|-----------|---------------|
   | Count per table | 1 | Multiple |
   | Data storage | In index | Separate |
   | Leaf nodes | Data rows | Pointers |
   | Insert speed | Slower | Faster |
   | Range queries | Faster | Slower |
   | Storage | No extra | Extra space |
   
   **ETL considerations:**
   - **Clustered**: Choose on column used for range queries (date, ID)
   - **Non-clustered**: Create on lookup columns, foreign keys
   - Drop/disable during bulk loads
   - Rebuild after ETL for optimal performance
   
   **Covering index (special non-clustered):**
   ```sql
   CREATE INDEX idx_covering ON orders(customer_id)
   INCLUDE (order_date, amount)
   -- Contains all needed columns, no additional lookup
   ```

10. **How do you use CTEs (Common Table Expressions) in ETL queries?**
    
    CTEs are temporary named result sets that exist within a single query execution.
    
    **Basic syntax:**
    ```sql
    WITH cte_name AS (
        SELECT column1, column2 FROM table
        WHERE condition
    )
    SELECT * FROM cte_name
    ```
    
    **ETL use cases:**
    
    **1. Breaking complex queries into steps:**
    ```sql
    WITH raw_data AS (
        SELECT customer_id, order_date, amount
        FROM orders
        WHERE order_date >= '2024-01-01'
    ),
    aggregated AS (
        SELECT 
            customer_id,
            COUNT(*) AS order_count,
            SUM(amount) AS total_amount
        FROM raw_data
        GROUP BY customer_id
    ),
    enriched AS (
        SELECT 
            a.*,
            c.customer_name,
            c.segment
        FROM aggregated a
        JOIN customers c ON a.customer_id = c.customer_id
    )
    SELECT * FROM enriched
    WHERE total_amount > 1000
    ```
    
    **2. Deduplication:**
    ```sql
    WITH ranked AS (
        SELECT *,
            ROW_NUMBER() OVER (
                PARTITION BY customer_id 
                ORDER BY updated_date DESC
            ) AS rn
        FROM customer_staging
    )
    INSERT INTO customers
    SELECT customer_id, customer_name, email
    FROM ranked
    WHERE rn = 1
    ```
    
    **3. Recursive CTEs (hierarchical data):**
    ```sql
    WITH RECURSIVE employee_hierarchy AS (
        -- Anchor: top-level employees
        SELECT employee_id, name, manager_id, 1 AS level
        FROM employees
        WHERE manager_id IS NULL
        
        UNION ALL
        
        -- Recursive: add subordinates
        SELECT e.employee_id, e.name, e.manager_id, eh.level + 1
        FROM employees e
        JOIN employee_hierarchy eh ON e.manager_id = eh.employee_id
    )
    SELECT * FROM employee_hierarchy
    ```
    
    **4. Multiple references to same subquery:**
    ```sql
    WITH monthly_sales AS (
        SELECT 
            DATE_TRUNC('month', sale_date) AS month,
            SUM(amount) AS monthly_total
        FROM sales
        GROUP BY DATE_TRUNC('month', sale_date)
    )
    SELECT 
        current.month,
        current.monthly_total,
        previous.monthly_total AS previous_month,
        current.monthly_total - previous.monthly_total AS month_over_month
    FROM monthly_sales current
    LEFT JOIN monthly_sales previous 
        ON current.month = previous.month + INTERVAL '1 month'
    ```
    
    **5. Data validation:**
    ```sql
    WITH validation AS (
        SELECT 
            'null_check' AS test,
            COUNT(*) AS failed_records
        FROM staging
        WHERE required_field IS NULL
        
        UNION ALL
        
        SELECT 
            'duplicate_check',
            COUNT(*) - COUNT(DISTINCT customer_id)
        FROM staging
        
        UNION ALL
        
        SELECT 
            'range_check',
            COUNT(*)
        FROM staging
        WHERE amount < 0
    )
    SELECT * FROM validation WHERE failed_records > 0
    ```
    
    **Benefits:**
    - **Readability**: Complex logic broken into named steps
    - **Reusability**: Reference CTE multiple times in query
    - **Maintainability**: Easier to modify and debug
    - **Performance**: Query optimizer can optimize entire statement
    - **No temp tables**: No need to create physical temporary tables
    
    **vs Subqueries:**
    - CTEs more readable than nested subqueries
    - Can reference CTE multiple times (subquery would be re-executed)
    - CTEs can be recursive
    
    **vs Temp tables:**
    - CTEs exist only for query duration
    - No physical storage (temp tables use storage)
    - Can't have indexes (temp tables can)
    - Good for moderate data volumes
    
    **Performance note:**
    - CTEs are not materialized by default (re-executed if referenced multiple times in some databases)
    - For large datasets referenced multiple times, consider temp tables
    - Modern optimizers handle CTEs efficiently

## Data Security and Governance

1. **How do you implement data security in ETL pipelines?**
   
   **Access control:**
   - **Authentication**: Verify identity (service accounts, OAuth, MFA)
   - **Authorization**: Control permissions (RBAC, ABAC)
   - **Least privilege**: Grant minimum necessary permissions
   - **Separate credentials**: Different accounts for dev/test/prod
   
   **Data encryption:**
   - **In transit**: TLS/SSL for all connections
   - **At rest**: Encrypt data in databases and files
   - **Column-level**: Encrypt sensitive columns
   - **Key management**: Use KMS (AWS KMS, Azure Key Vault, HashiCorp Vault)
   
   **Network security:**
   - VPNs for on-premise to cloud connections
   - Private subnets for databases
   - Security groups/firewall rules
   - Whitelist IP addresses
   
   **Credential management:**
   ```python
   # Bad: Hardcoded credentials
   conn = psycopg2.connect(
       host="db.example.com",
       password="hardcoded_password"  # DON'T DO THIS
   )
   
   # Good: Environment variables or secrets manager
   import os
   from boto3 import client
   
   secrets = client('secretsmanager')
   secret = secrets.get_secret_value(SecretId='db-credentials')
   
   conn = psycopg2.connect(
       host=os.environ['DB_HOST'],
       password=secret['password']
   )
   ```
   
   **Audit logging:**
   - Log all data access
   - Track who accessed what data when
   - Monitor for unusual patterns
   - Retain audit logs per compliance requirements
   
   **Data masking:**
   - Mask PII in non-production environments
   - Tokenization for sensitive data
   - Anonymization for analytics
   
   **Code security:**
   - Store ETL code in version control
   - Code review processes
   - Vulnerability scanning
   - Dependency updates
   
   **Compliance:**
   - GDPR, HIPAA, SOC 2, PCI-DSS requirements
   - Data residency rules
   - Right to deletion/modification
   - Privacy by design

2. **What is data masking and when is it applied in ETL?**
   
   Data masking replaces sensitive data with realistic but fictitious data.
   
   **Types of masking:**
   
   **Static masking:**
   - Permanently replace data in database copy
   - Used for non-production environments
   ```sql
   UPDATE customers_dev
   SET ssn = 'XXX-XX-' || RIGHT(ssn, 4),
       email = CONCAT('user', customer_id, '@example.com'),
       credit_card = 'XXXX-XXXX-XXXX-' || RIGHT(credit_card, 4)
   ```
   
   **Dynamic masking:**
   - Mask data at query time based on user permissions
   - Original data unchanged
   - SQL Server, Oracle support this
   ```sql
   CREATE MASK masked_ssn AS
   CASE 
       WHEN IS_MEMBER('Admins') = 1 THEN ssn
       ELSE 'XXX-XX-' || RIGHT(ssn, 4)
   END
   ```
   
   **Masking techniques:**
   
   **Redaction:**
   - Replace with fixed characters
   - Example: Credit card → XXXX-XXXX-XXXX-1234
   
   **Substitution:**
   - Replace with realistic fake data
   - Example: Real name → Fake name from lookup table
   ```python
   from faker import Faker
   fake = Faker()
   
   df['customer_name'] = df['customer_id'].apply(lambda x: fake.name())
   df['email'] = df['customer_id'].apply(lambda x: fake.email())
   ```
   
   **Shuffling:**
   - Redistribute values among records
   - Maintains data distribution
   - Example: Shuffle salaries across employees
   
   **Encryption:**
   - Reversible masking with key
   - Format-preserving encryption
   
   **Nulling:**
   - Replace with NULL
   - Simple but loses data utility
   
   **When to apply in ETL:**
   
   **During extraction:**
   - Mask at source before loading to staging
   - Reduces exposure in ETL pipeline
   
   **During transformation:**
   - Apply business rules for masking
   - Different masking for different environments
   
   **Environment-specific:**
   - Production: Original data
   - UAT/QA: Partial masking
   - Development: Full masking
   
   **Use cases:**
   - Protecting PII in development/test
   - Compliance with GDPR, HIPAA
   - Third-party data sharing
   - Analytics on sensitive data
   - Developer access to production-like data

3. **How do you handle PII (Personally Identifiable Information) in ETL?**
   
   **Identification:**
   - **Direct PII**: Name, SSN, email, phone, address, biometrics
   - **Indirect PII**: IP address, device ID, date of birth, zip code
   - Classify data during design phase
   - Use data discovery tools to identify PII
   
   **Protection strategies:**
   
   **Pseudonymization:**
   - Replace PII with pseudonyms/tokens
   - Maintain mapping in secure location
   ```python
   import hashlib
   
   def pseudonymize(pii_value, salt):
       return hashlib.sha256(f"{pii_value}{salt}".encode()).hexdigest()
   
   df['customer_token'] = df['customer_email'].apply(
       lambda x: pseudonymize(x, SECRET_SALT)
   )
   ```
   
   **Anonymization:**
   - Remove ability to identify individuals
   - Irreversible process
   - Generalization: Age 34 → Age range 30-40
   - Suppression: Remove identifying columns
   - Perturbation: Add noise to data
   
   **Encryption:**
   - Encrypt PII columns
   - Application-level or database encryption
   ```sql
   -- Column-level encryption
   INSERT INTO customers (name, ssn_encrypted)
   VALUES ('John Doe', EncryptByKey(Key_GUID('SSNKey'), @ssn))
   ```
   
   **Access controls:**
   - Limit who can view PII
   - Role-based access
   - Audit all PII access
   
   **Data minimization:**
   - Collect only necessary PII
   - Don't extract PII if not needed downstream
   - Purge PII when no longer needed
   
   **ETL pipeline practices:**
   
   **Segregation:**
   - Store PII separately from other data
   - Join only when necessary
   ```
   customer_id (linkage)
   customer_profile (non-PII)
   customer_pii (encrypted, access controlled)
   ```
   
   **Early protection:**
   - Mask/encrypt immediately after extraction
   - Don't store raw PII in staging
   
   **Separate pipelines:**
   - PII data through secured pipeline
   - Non-PII through standard pipeline
   
   **Compliance requirements:**
   
   **GDPR:**
   - Right to access
   - Right to deletion ("right to be forgotten")
   - Data portability
   - Consent management
   
   **HIPAA:**
   - 18 types of protected health information (PHI)
   - De-identification requirements
   - Audit trails
   
   **CCPA:**
   - California consumer privacy
   - Disclosure requirements
   - Deletion rights
   
   **Implementation:**
   ```python
   def process_pii_data(df):
       # Separate PII from non-PII
       pii_columns = ['email', 'phone', 'ssn', 'address']
       non_pii = df.drop(columns=pii_columns)
       pii = df[['customer_id'] + pii_columns]
       
       # Encrypt PII
       pii_encrypted = encrypt_dataframe(pii, pii_columns)
       
       # Load to different tables with different access controls
       load_to_table(non_pii, 'customer_profile')
       load_to_secure_table(pii_encrypted, 'customer_pii')
   ```

4. **What are the compliance requirements for ETL in regulated industries?**
   
   **Healthcare (HIPAA):**
   - Protect PHI (Protected Health Information)
   - Access controls and audit logs
   - Encryption in transit and at rest
   - Business Associate Agreements (BAAs) with vendors
   - De-identification for analytics
   - Breach notification procedures
   
   **Finance (SOX, PCI-DSS):**
   
   **SOX (Sarbanes-Oxley):**
   - Accurate financial reporting
   - Data integrity controls
   - Change management and audit trails
   - Segregation of duties
   - Regular testing and documentation
   
   **PCI-DSS (Payment Card Industry):**
   - Protect cardholder data
   - Encrypt card data
   - Limit storage of sensitive authentication data
   - Regular security testing
   - Access control measures
   
   **Data Privacy (GDPR, CCPA):**
   
   **GDPR:**
   - Data minimization
   - Purpose limitation
   - Right to erasure/deletion
   - Data portability
   - Consent management
   - Data processing agreements
   - DPIA (Data Protection Impact Assessment)
   
   **CCPA:**
   - Consumer rights (access, delete, opt-out)
   - Privacy notices
   - Data inventory
   - Vendor management
   
   **Industry-specific:**
   
   **Banking (GLBA):**
   - Financial privacy
   - Safeguards for customer information
   - Information sharing disclosure
   
   **Government (FedRAMP, FISMA):**
   - Security controls
   - Continuous monitoring
   - Incident response
   - System categorization
   
   **ETL compliance requirements:**
   
   **Data lineage:**
   - Track data origin to destination
   - Document transformations
   - Audit trail of changes
   
   **Data quality:**
   - Validation rules
   - Accuracy checks
   - Completeness verification
   - Regular data quality reports
   
   **Access controls:**
   - Role-based access (RBAC)
   - Principle of least privilege
   - MFA for privileged access
   - Regular access reviews
   
   **Encryption:**
   - TLS/SSL in transit
   - AES-256 at rest
   - Key rotation policies
   - Secure key management
   
   **Audit logging:**
   - Who accessed what data when
   - All modifications logged
   - Log retention per regulations
   - Regular log review
   
   **Change management:**
   - Version control for ETL code
   - Approval workflows
   - Testing requirements
   - Rollback procedures
   
   **Data retention:**
   - Define retention periods
   - Automated purging
   - Legal hold capabilities
   - Secure deletion
   
   **Disaster recovery:**
   - Backup procedures
   - RPO/RTO requirements
   - Regular DR testing
   - Business continuity planning
   
   **Documentation:**
   - Data flow diagrams
   - System architecture
   - Security controls
   - Compliance attestations
   - SOC 2 reports

5. **How do you implement data encryption in transit and at rest?**
   
   **Encryption in Transit:**
   
   **TLS/SSL for connections:**
   ```python
   # Database connection with SSL
   import psycopg2
   
   conn = psycopg2.connect(
       host="db.example.com",
       database="warehouse",
       user="etl_user",
       password=password,
       sslmode='require',  # Require SSL
       sslrootcert='/path/to/ca.crt'
   )
   ```
   
   **HTTPS for APIs:**
   ```python
   import requests
   
   # Verify SSL certificate
   response = requests.get(
       'https://api.example.com/data',
       headers={'Authorization': f'Bearer {token}'},
       verify=True  # Verify SSL cert
   )
   ```
   
   **SFTP for file transfers:**
   ```python
   import paramiko
   
   ssh = paramiko.SSHClient()
   ssh.connect(
       hostname='sftp.example.com',
       username='user',
       key_filename='/path/to/private_key'
   )
   sftp = ssh.open_sftp()
   sftp.get('remote_file.csv', 'local_file.csv')
   ```
   
   **VPN/Private connections:**
   - AWS Direct Connect
   - Azure ExpressRoute
   - GCP Dedicated Interconnect
   - Site-to-site VPN
   
   ---
   
   **Encryption at Rest:**
   
   **Database encryption:**
   
   **Transparent Data Encryption (TDE):**
   ```sql
   -- SQL Server
   CREATE DATABASE ENCRYPTION KEY
   WITH ALGORITHM = AES_256
   ENCRYPTION BY SERVER CERTIFICATE MyServerCert;
   
   ALTER DATABASE warehouse SET ENCRYPTION ON;
   ```
   
   **Column-level encryption:**
   ```sql
   -- Encrypt specific columns
   CREATE TABLE customers (
       customer_id INT,
       name VARCHAR(100),
       ssn VARBINARY(128)  -- Encrypted column
   );
   
   -- Insert with encryption
   INSERT INTO customers (customer_id, name, ssn)
   VALUES (1, 'John Doe', 
           EncryptByKey(Key_GUID('SSNKey'), '123-45-6789'));
   
   -- Query with decryption
   SELECT customer_id, name,
          CAST(DecryptByKey(ssn) AS VARCHAR) AS ssn_decrypted
   FROM customers;
   ```
   
   **Cloud storage encryption:**
   
   **AWS S3:**
   ```python
   import boto3
   
   s3 = boto3.client('s3')
   
   # Server-side encryption
   s3.put_object(
       Bucket='my-bucket',
       Key='data.csv',
       Body=data,
       ServerSideEncryption='AES256'  # or 'aws:kms'
   )
   
   # Or use KMS key
   s3.put_object(
       Bucket='my-bucket',
       Key='data.csv',
       Body=data,
       ServerSideEncryption='aws:kms',
       SSEKMSKeyId='arn:aws:kms:region:account:key/key-id'
   )
   ```
   
   **Azure Blob:**
   ```python
   from azure.storage.blob import BlobServiceClient
   
   blob_service = BlobServiceClient(account_url=url, credential=credential)
   blob_client = blob_service.get_blob_client(container='data', blob='file.csv')
   
   # Encryption enabled by default in Azure
   blob_client.upload_blob(data, overwrite=True)
   ```
   
   **File system encryption:**
   - dm-crypt/LUKS (Linux)
   - BitLocker (Windows)
   - FileVault (macOS)
   
   **Key management:**
   
   **AWS KMS:**
   ```python
   import boto3
   
   kms = boto3.client('kms')
   
   # Encrypt data
   response = kms.encrypt(
       KeyId='alias/my-key',
       Plaintext=b'sensitive data'
   )
   ciphertext = response['CiphertextBlob']
   
   # Decrypt data
   response = kms.decrypt(CiphertextBlob=ciphertext)
   plaintext = response['Plaintext']
   ```
   
   **Azure Key Vault:**
   ```python
   from azure.keyvault.secrets import SecretClient
   from azure.identity import DefaultAzureCredential
   
   credential = DefaultAzureCredential()
   client = SecretClient(vault_url=vault_url, credential=credential)
   
   # Store secret
   client.set_secret("db-password", "SecurePassword123")
   
   # Retrieve secret
   secret = client.get_secret("db-password")
   password = secret.value
   ```
   
   **Best practices:**
   - Use strong encryption algorithms (AES-256)
   - Rotate encryption keys regularly
   - Separate key management from data storage
   - Never hardcode keys in code
   - Use HSM (Hardware Security Module) for critical keys
   - Implement key versioning
   - Test encryption/decryption processes
   - Monitor key usage
   - Document encryption strategy

6. **What is role-based access control (RBAC) in ETL systems?**
   
   RBAC assigns permissions based on roles rather than individual users.
   
   **Core concepts:**
   - **Users**: Individual accounts
   - **Roles**: Collections of permissions
   - **Permissions**: Specific actions allowed
   - **Resources**: Data, tables, pipelines
   
   **Common ETL roles:**
   
   **ETL Developer:**
   - Read from source systems
   - Write to staging area
   - Execute ETL jobs in development
   - View logs and monitoring
   
   **ETL Administrator:**
   - All developer permissions
   - Modify ETL configurations
   - Deploy to production
   - Manage schedules and dependencies
   - Access control management
   
   **Data Engineer:**
   - Design and build pipelines
   - Access to all environments
   - Infrastructure management
   - Performance tuning
   
   **Data Analyst:**
   - Read from data warehouse
   - No write access to production
   - Execute queries
   - Create reports
   
   **Data Steward:**
   - Data quality management
   - Metadata management
   - Business rule definitions
   - Limited technical access
   
   **Auditor:**
   - Read-only access to logs
   - Compliance reporting
   - No data modification
   
   **Implementation examples:**
   
   **Database level:**
   ```sql
   -- Create roles
   CREATE ROLE etl_developer;
   CREATE ROLE etl_admin;
   CREATE ROLE data_analyst;
   
   -- Grant permissions to roles
   GRANT SELECT ON schema.source_tables TO etl_developer;
   GRANT INSERT, UPDATE, DELETE ON schema.staging_tables TO etl_developer;
   
   GRANT ALL ON schema.* TO etl_admin;
   
   GRANT SELECT ON schema.warehouse_tables TO data_analyst;
   
   -- Assign users to roles
   GRANT etl_developer TO user_john;
   GRANT etl_admin TO user_jane;
   GRANT data_analyst TO user_bob;
   ```
   
   **Apache Airflow:**
   ```python
   # Configure roles in webserver_config.py
   from airflow.www.security import AirflowSecurityManager
   
   ROLE_CONFIGS = {
       'ETL_Developer': {
           'can_read': ['DAG', 'Task'],
           'can_edit': ['DAG'],
           'can_create': ['DAG']
       },
       'ETL_Admin': {
           'can_read': ['*'],
           'can_edit': ['*'],
           'can_create': ['*'],
           'can_delete': ['*']
       },
       'Viewer': {
           'can_read': ['DAG', 'Task', 'Log']
       }
   }
   ```
   
   **AWS IAM:**
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Effect": "Allow",
         "Action": [
           "s3:GetObject",
           "s3:PutObject"
         ],
         "Resource": "arn:aws:s3:::etl-staging/*"
       },
       {
         "Effect": "Allow",
         "Action": [
           "glue:StartJobRun",
           "glue:GetJobRun"
         ],
         "Resource": "*"
       }
     ]
   }
   ```
   
   **Best practices:**
   - Follow principle of least privilege
   - Separate roles for dev/test/prod
   - Regular access reviews
   - Document role definitions
   - Use groups for role assignment
   - Implement approval workflows
   - Audit role changes
   - Temporary elevated access when needed
   - Automated onboarding/offboarding

7. **How do you implement data retention policies in ETL?**
   
   **Define retention requirements:**
   - Legal/regulatory requirements
   - Business needs
   - Storage cost optimization
   - Different policies per data type
   
   **Retention strategies:**
   
   **Time-based retention:**
   ```sql
   -- Delete data older than retention period
   DELETE FROM transactions
   WHERE transaction_date < CURRENT_DATE - INTERVAL '7 years';
   
   -- Archive before delete
   INSERT INTO transactions_archive
   SELECT * FROM transactions
   WHERE transaction_date < CURRENT_DATE - INTERVAL '7 years';
   
   DELETE FROM transactions
   WHERE transaction_date < CURRENT_DATE - INTERVAL '7 years';
   ```
   
   **Tiered storage:**
   - **Hot storage** (0-90 days): Fast access, expensive
   - **Warm storage** (91 days - 1 year): Moderate access, medium cost
   - **Cold storage** (1-7 years): Rare access, cheap (S3 Glacier)
   - **Archive** (7+ years): Compliance only, very cheap
   
   **Implementation patterns:**
   
   **Partition-based cleanup:**
   ```sql
   -- Drop old partitions
   ALTER TABLE sales DROP PARTITION p_2020_q1;
   
   -- Archive partition before drop
   CREATE TABLE sales_archive_2020_q1 AS
   SELECT * FROM sales PARTITION (p_2020_q1);
   
   ALTER TABLE sales DROP PARTITION p_2020_q1;
   ```
   
   **Automated archival:**
   ```python
   from datetime import datetime, timedelta
   
   def archive_old_data(table, retention_days, archive_table):
       cutoff_date = datetime.now() - timedelta(days=retention_days)
       
       # Archive to cold storage
       archive_query = f"""
           INSERT INTO {archive_table}
           SELECT * FROM {table}
           WHERE created_date < '{cutoff_date}'
       """
       execute_query(archive_query)
       
       # Delete from hot storage
       delete_query = f"""
           DELETE FROM {table}
           WHERE created_date < '{cutoff_date}'
       """
       execute_query(delete_query)
       
       # Log retention action
       log_retention_action(table, cutoff_date, rows_archived)
   
   # Schedule regular execution
   schedule.every().week.do(archive_old_data, 'transactions', 365, 'transactions_archive')
   ```
   
   **Cloud lifecycle policies:**
   
   **AWS S3 Lifecycle:**
   ```json
   {
     "Rules": [
       {
         "Id": "Archive-old-data",
         "Status": "Enabled",
         "Transitions": [
           {
             "Days": 90,
             "StorageClass": "STANDARD_IA"
           },
           {
             "Days": 365,
             "StorageClass": "GLACIER"
           }
         ],
         "Expiration": {
           "Days": 2555
         }
       }
     ]
   }
   ```
   
   **Retention metadata:**
   ```sql
   CREATE TABLE retention_policy (
       table_name VARCHAR(100),
       retention_period_days INT,
       archive_location VARCHAR(500),
       last_cleanup_date DATE,
       next_cleanup_date DATE,
       legal_hold BOOLEAN
   );
   
   -- Track retention execution
   CREATE TABLE retention_audit (
       audit_id INT,
       table_name VARCHAR(100),
       cleanup_date DATE,
       records_archived INT,
       records_deleted INT,
       storage_freed_gb DECIMAL
   );
   ```
   
   **Legal hold:**
   - Suspend retention for litigation
   - Flag records not to be deleted
   ```sql
   UPDATE transactions
   SET legal_hold = TRUE
   WHERE customer_id IN (SELECT customer_id FROM litigation_cases);
   
   -- Modify deletion to respect legal hold
   DELETE FROM transactions
   WHERE transaction_date < :cutoff_date
   AND legal_hold = FALSE;
   ```
   
   **Best practices:**
   - Document retention policies
   - Automate retention processes
   - Test recovery from archives
   - Monitor storage costs
   - Regular policy reviews
   - Compliance verification
   - Audit retention activities

8. **What is data governance and why is it important in ETL?**
   
   Data governance is the framework for managing data availability, usability, integrity, and security.
   
   **Key components:**
   
   **Data Quality:**
   - Accuracy, completeness, consistency
   - Validation rules and checks
   - Data profiling and monitoring
   - Issue remediation processes
   
   **Metadata Management:**
   - Business glossary (business terms)
   - Technical metadata (schemas, formats)
   - Operational metadata (lineage, usage)
   - Data catalog
   
   **Data Lineage:**
   - Track data from source to destination
   - Document transformations
   - Impact analysis
   - Troubleshooting support
   
   **Data Security:**
   - Access controls
   - Encryption
   - Privacy compliance
   - Audit trails
   
   **Data Standards:**
   - Naming conventions
   - Data types and formats
   - Validation rules
   - Documentation requirements
   
   **Importance in ETL:**
   
   **Trust and confidence:**
   - Business trusts data for decisions
   - Consistent definitions across organization
   - Known data quality levels
   
   **Compliance:**
   - Meet regulatory requirements
   - Audit readiness
   - Privacy protection
   - Data sovereignty
   
   **Efficiency:**
   - Reduce duplicate efforts
   - Reuse validated data sets
   - Clear ownership and responsibilities
   - Faster issue resolution
   
   **Risk management:**
   - Identify and mitigate data risks
   - Prevent data breaches
   - Business continuity
   - Quality assurance
   
   **Governance framework:**
   
   **Roles and responsibilities:**
   - **Data Owners**: Business stakeholders, accountable for data
   - **Data Stewards**: Day-to-day management, quality checks
   - **Data Custodians**: Technical implementation (DBAs, engineers)
   - **Data Governance Council**: Oversee governance program
   
   **Policies and procedures:**
   - Data classification policy
   - Data retention policy
   - Access control policy
   - Data quality standards
   - Change management procedures
   
   **Tools and technology:**
   - Data catalogs (Collibra, Alation, Apache Atlas)
   - Metadata repositories
   - Data quality tools
   - Lineage tracking
   - Workflow management
   
   **Metrics and monitoring:**
   - Data quality scores
   - Policy compliance rates
   - Issue resolution times
   - Data usage metrics
   - Access audit results
   
   **ETL governance practices:**
   - Document data sources and transformations
   - Implement data quality checks
   - Track lineage through pipelines
   - Enforce naming conventions
   - Version control for ETL code
   - Change approval workflows
   - Regular data quality reports
   - Metadata capture in ETL processes

9. **How do you maintain data lineage and metadata management?**
   
   **Data lineage tracking:**
   
   **What to track:**
   - Source systems and tables
   - Extraction time and method
   - Transformations applied
   - Target tables and columns
   - Job/process identifiers
   - User/service account
   
   **Implementation approaches:**
   
   **Metadata tables:**
   ```sql
   CREATE TABLE data_lineage (
       lineage_id BIGINT PRIMARY KEY,
       source_system VARCHAR(100),
       source_table VARCHAR(100),
       source_column VARCHAR(100),
       target_table VARCHAR(100),
       target_column VARCHAR(100),
       transformation_rule TEXT,
       job_name VARCHAR(200),
       created_date TIMESTAMP
   );
   
   -- Capture column-level lineage
   INSERT INTO data_lineage VALUES (
       1,
       'CRM',
       'customers',
       'email_address',
       'dim_customer',
       'customer_email',
       'LOWER(TRIM(email_address))',
       'customer_etl_job',
       CURRENT_TIMESTAMP
   );
   ```
   
   **Automated capture:**
   ```python
   def track_lineage(source, target, transformation, job_id):
       lineage_data = {
           'source_system': source['system'],
           'source_table': source['table'],
           'target_table': target['table'],
           'transformation': transformation,
           'job_id': job_id,
           'timestamp': datetime.now(),
           'record_count': source['count']
       }
       
       insert_into_lineage_table(lineage_data)
   
   # Use in ETL
   source = extract_data('CRM', 'customers')
   transformed = apply_transformation(source, 'standardize_email')
   track_lineage(source, target, 'standardize_email', job_id)
   load_data(transformed, target)
   ```
   
   **Tool-based lineage:**
   
   **Apache Atlas:**
   - Automatic lineage for Spark, Hive
   - REST API for custom lineage
   - Graph-based visualization
   
   **AWS Glue Data Catalog:**
   - Automatic lineage tracking
   - Integration with Glue ETL jobs
   
   **dbt:**
   - Automatically generates lineage from SQL
   - Interactive lineage graphs
   ```yaml
   # dbt automatically tracks lineage
   models:
     - name: customer_summary
       description: "Customer aggregated metrics"
       columns:
         - name: customer_id
           description: "Primary key"
   ```
   
   **Metadata management:**
   
   **Types of metadata:**
   
   **Business metadata:**
   - Data definitions and glossary
   - Data ownership
   - Business rules
   - Use cases
   
   **Technical metadata:**
   - Schemas, data types
   - Table relationships
   - Indexes, partitions
   - File formats, locations
   
   **Operational metadata:**
   - Load times, frequencies
   - Data volumes
   - Job execution history
   - Performance metrics
   
   **Metadata repository:**
   ```sql
   CREATE TABLE metadata_catalog (
       table_name VARCHAR(100),
       column_name VARCHAR(100),
       data_type VARCHAR(50),
       business_name VARCHAR(200),
       description TEXT,
       data_owner VARCHAR(100),
       sensitivity_level VARCHAR(20),
       sample_values TEXT,
       created_date DATE,
       last_updated DATE
   );
   ```
   
   **Metadata capture:**
   ```python
   def capture_metadata(df, table_name):
       metadata = {
           'table_name': table_name,
           'row_count': len(df),
           'column_count': len(df.columns),
           'columns': []
       }
       
       for col in df.columns:
           col_meta = {
               'name': col,
               'dtype': str(df[col].dtype),
               'null_count': df[col].isnull().sum(),
               'unique_count': df[col].nunique(),
               'sample_values': df[col].head(5).tolist()
           }
           metadata['columns'].append(col_meta)
       
       store_metadata(metadata)
       return metadata
   ```
   
   **Metadata tools:**
   - **Data catalogs**: Collibra, Alation, DataHub
   - **Cloud-native**: AWS Glue Catalog, Azure Purview
   - **Open-source**: Apache Atlas, Amundsen, Marquez
   
   **Best practices:**
   - Automate metadata capture
   - Keep metadata up-to-date
   - Document business context
   - Visualize lineage graphs
   - Enable searchable catalogs
   - Tag sensitive data
   - Version metadata changes

10. **What auditing requirements should ETL processes fulfill?**
    
    **Audit trail requirements:**
    
    **Data access auditing:**
    - Who accessed what data when
    - Query patterns and frequency
    - Export/download activities
    - Unauthorized access attempts
    ```sql
    CREATE TABLE audit_access_log (
        audit_id BIGINT,
        user_id VARCHAR(100),
        access_time TIMESTAMP,
        table_accessed VARCHAR(100),
        action VARCHAR(20),  -- SELECT, INSERT, UPDATE, DELETE
        row_count INT,
        ip_address VARCHAR(45),
        session_id VARCHAR(100)
    );
    ```
    
    **Data modification auditing:**
    - Track INSERT, UPDATE, DELETE operations
    - Before and after values
    - Reason for change
    ```sql
    CREATE TABLE audit_changes (
        change_id BIGINT,
        table_name VARCHAR(100),
        record_id VARCHAR(100),
        column_name VARCHAR(100),
        old_value TEXT,
        new_value TEXT,
        change_type VARCHAR(20),
        changed_by VARCHAR(100),
        change_timestamp TIMESTAMP,
        change_reason TEXT
    );
    ```
    
    **ETL job auditing:**
    - Job execution history
    - Parameters used
    - Success/failure status
    - Performance metrics
    ```sql
    CREATE TABLE audit_etl_jobs (
        job_id BIGINT,
        job_name VARCHAR(200),
        start_time TIMESTAMP,
        end_time TIMESTAMP,
        status VARCHAR(20),
        records_processed BIGINT,
        records_failed INT,
        execution_time_seconds INT,
        executed_by VARCHAR(100),
        parameters JSON
    );
    ```
    
    **Configuration changes:**
    - ETL code modifications
    - Parameter changes
    - Schedule modifications
    - Who made the change and when
    
    **Data quality auditing:**
    - Validation results
    - Data quality scores
    - Issues identified
    - Remediation actions
    
    **Implementation:**
    
    **Database triggers:**
    ```sql
    CREATE TRIGGER audit_customer_changes
    AFTER UPDATE ON customers
    FOR EACH ROW
    BEGIN
        INSERT INTO audit_changes (
            table_name, record_id, column_name,
            old_value, new_value, changed_by, change_timestamp
        )
        VALUES (
            'customers', OLD.customer_id, 'email',
            OLD.email, NEW.email, CURRENT_USER, NOW()
        );
    END;
    ```
    
    **Application-level auditing:**
    ```python
    def audit_decorator(func):
        def wrapper(*args, **kwargs):
            start_time = datetime.now()
            try:
                result = func(*args, **kwargs)
                status = 'SUCCESS'
                return result
            except Exception as e:
                status = 'FAILED'
                raise
            finally:
                audit_log = {
                    'function': func.__name__,
                    'start_time': start_time,
                    'end_time': datetime.now(),
                    'status': status,
                    'user': get_current_user(),
                    'parameters': kwargs
                }
                log_audit(audit_log)
        return wrapper
    
    @audit_decorator
    def load_customer_data(source, target):
        # ETL logic
        pass
    ```
    
    **Compliance requirements:**
    
    **SOX compliance:**
    - Change controls and approvals
    - Segregation of duties
    - Access controls
    - Regular reviews
    
    **GDPR compliance:**
    - Data processing records
    - Consent tracking
    - Deletion logs
    - Data transfer logs
    
    **HIPAA compliance:**
    - PHI access logs
    - Minimum 6-year retention
    - Encryption evidence
    - Breach notification logs
    
    **Audit log retention:**
    - Define retention periods (often 7 years)
    - Secure storage
    - Tamper-proof (write-once storage)
    - Efficient search/retrieval
    
    **Audit reporting:**
    ```sql
    -- Access patterns
    SELECT user_id, COUNT(*) as access_count,
           MIN(access_time) as first_access,
           MAX(access_time) as last_access
    FROM audit_access_log
    WHERE access_time >= CURRENT_DATE - INTERVAL '30 days'
    GROUP BY user_id
    ORDER BY access_count DESC;
    
    -- Failed ETL jobs
    SELECT job_name, COUNT(*) as failure_count
    FROM audit_etl_jobs
    WHERE status = 'FAILED'
      AND start_time >= CURRENT_DATE - INTERVAL '7 days'
    GROUP BY job_name;
    ```
    
    **Best practices:**
    - Automate audit logging
    - Separate audit storage from operational
    - Immutable audit logs
    - Regular audit reviews
    - Alert on suspicious patterns
    - Comprehensive but efficient logging
    - Test audit completeness
    - Document audit procedures

## Scenario-Based and Problem-Solving

1. **How would you design an ETL pipeline for a real-time fraud detection system?**
   
   **Architecture:**
   - Event-driven, streaming architecture
   - Low latency (<1 second) processing
   - High availability and fault tolerance
   
   **Components:**
   
   **Data ingestion:**
   - Kafka/Kinesis for transaction streams
   - Multiple producers (payment systems, apps)
   - Partitioned by account/region for parallel processing
   
   **Stream processing:**
   - Apache Flink or Spark Streaming
   - Stateful processing (maintain user behavior history)
   - Complex event processing (pattern detection)
   
   **Feature engineering:**
   - Real-time feature calculation
   - Windowed aggregations (last 1 hour, 24 hours)
   - Historical feature lookup from feature store
   
   **Fraud detection:**
   - ML model inference (pre-trained models)
   - Rule-based checks (threshold violations)
   - Risk scoring
   - Multiple detection layers
   
   **Implementation example:**
   ```python
   # Flink streaming job
   from pyflink.datastream import StreamExecutionEnvironment
   
   env = StreamExecutionEnvironment.get_execution_environment()
   
   # Ingest transactions
   transactions = env.add_source(
       FlinkKafkaConsumer('transactions', schema, properties)
   )
   
   # Enrich with features
   enriched = transactions.map(lambda tx: {
       **tx,
       'velocity_1h': get_transaction_count_1h(tx['account_id']),
       'avg_amount_24h': get_avg_amount_24h(tx['account_id']),
       'location_change': check_location_anomaly(tx),
       'device_fingerprint': tx['device_id']
   })
   
   # Apply fraud detection
   scored = enriched.map(lambda tx: {
       **tx,
       'fraud_score': fraud_model.predict(extract_features(tx)),
       'risk_level': calculate_risk(tx)
   })
   
   # Route based on score
   high_risk = scored.filter(lambda tx: tx['fraud_score'] > 0.8)
   medium_risk = scored.filter(lambda tx: 0.5 < tx['fraud_score'] <= 0.8)
   
   # Send to different sinks
   high_risk.add_sink(KafkaSink('fraud-alerts'))
   medium_risk.add_sink(KafkaSink('manual-review'))
   scored.add_sink(KafkaSink('transaction-log'))
   ```
   
   **Data storage:**
   - **Hot path**: Redis/Elasticsearch for real-time queries
   - **Warm path**: Cassandra for recent history
   - **Cold path**: S3/data lake for analytics
   
   **Key considerations:**
   - **Low latency**: Process within milliseconds
   - **Scalability**: Handle millions of transactions/day
   - **Feature store**: Pre-computed features for fast lookup
   - **Model versioning**: A/B testing, gradual rollout
   - **Feedback loop**: Labeled data back to training
   - **Fallback**: Operate even if ML model fails
   
   **Monitoring:**
   - Processing lag metrics
   - Fraud detection accuracy
   - False positive/negative rates
   - System throughput and latency

2. **How do you handle a scenario where source data schema changes frequently?**
   
   **Strategies:**
   
   **Schema evolution framework:**
   - Implement backward/forward compatible changes
   - Use schema registry (Confluent, AWS Glue)
   - Version all schemas
   
   **Flexible data structures:**
   - Use schema-on-read approach
   - Store raw data in flexible formats (JSON, Parquet)
   - Apply schema at transformation time
   
   **Dynamic schema detection:**
   ```python
   def handle_schema_changes(df, expected_schema):
       # Detect new columns
       new_columns = set(df.columns) - set(expected_schema.keys())
       if new_columns:
           log.info(f"New columns detected: {new_columns}")
           for col in new_columns:
               register_new_column(col, df[col].dtype)
       
       # Handle missing columns
       missing_columns = set(expected_schema.keys()) - set(df.columns)
       for col in missing_columns:
           df[col] = expected_schema[col]['default']
       
       # Handle type changes
       for col in df.columns:
           if col in expected_schema:
               expected_type = expected_schema[col]['type']
               if df[col].dtype != expected_type:
                   log.warning(f"Type change in {col}: {df[col].dtype} -> {expected_type}")
                   df[col] = safe_cast(df[col], expected_type)
       
       return df
   ```
   
   **Configuration-driven ETL:**
   - Store mapping rules in configuration
   - Update config instead of code
   ```yaml
   source_mappings:
     version: 2.0
     fields:
       - source: customer_email
         target: email
         type: string
         default: null
         transformations:
           - lowercase
           - trim
       - source: customer_name  # New field
         target: full_name
         type: string
         default: "Unknown"
   ```
   
   **Graceful degradation:**
   - Continue processing with available fields
   - Log schema mismatches
   - Alert on breaking changes
   
   **Best practices:**
   - **Communication**: Coordinate with source system owners
   - **Schema registry**: Centralized schema management
   - **Automated alerts**: Notify on schema changes
   - **Versioned tables**: Maintain multiple schema versions
   - **Testing**: Test with various schema versions
   - **Documentation**: Keep schema change log

3. **What approach would you take for migrating data from legacy systems?**
   
   **Migration strategy:**
   
   **Assessment phase:**
   - Inventory all legacy systems and data
   - Understand data volumes and complexity
   - Identify data dependencies
   - Document business rules and transformations
   - Assess data quality issues
   
   **Planning:**
   - Define migration scope and priorities
   - Choose migration approach (big bang vs phased)
   - Establish rollback plan
   - Set success criteria and validation rules
   
   **Migration approaches:**
   
   **Big bang migration:**
   - Migrate everything at once
   - Downtime required
   - Higher risk but faster completion
   - Suitable for smaller datasets
   
   **Phased migration:**
   - Migrate in incremental stages
   - Parallel run period
   - Lower risk, longer timeline
   - Suitable for large, complex systems
   
   **Implementation:**
   
   **Initial full load:**
   ```python
   def initial_migration(legacy_source, modern_target):
       # Phase 1: Extract all historical data
       legacy_data = extract_legacy_data(legacy_source)
       
       # Phase 2: Data quality assessment
       quality_report = assess_data_quality(legacy_data)
       if quality_report['critical_issues'] > 0:
           raise MigrationException("Critical data quality issues")
       
       # Phase 3: Transform to new schema
       transformed = transform_legacy_to_modern(legacy_data)
       
       # Phase 4: Validate transformation
       validation_results = validate_migration(legacy_data, transformed)
       
       # Phase 5: Load to target
       load_to_target(transformed, modern_target)
       
       # Phase 6: Reconciliation
       reconcile(legacy_source, modern_target)
   ```
   
   **Parallel run strategy:**
   - Run both old and new systems simultaneously
   - Compare outputs for validation period
   - Gradually shift traffic to new system
   
   **Data transformation:**
   - Map legacy structures to modern schema
   - Handle deprecated fields
   - Normalize inconsistent formats
   - Resolve data quality issues
   
   **Validation and reconciliation:**
   ```python
   def reconcile_migration(source, target):
       checks = {
           'record_count': compare_counts(source, target),
           'key_uniqueness': validate_keys(target),
           'referential_integrity': check_references(target),
           'aggregates': compare_aggregates(source, target),
           'sample_records': compare_samples(source, target, n=1000)
       }
       
       failed_checks = [k for k, v in checks.items() if not v['passed']]
       if failed_checks:
           raise ReconciliationException(f"Failed: {failed_checks}")
   ```
   
   **Challenges and solutions:**
   - **Data quality**: Cleanse during migration
   - **Complex transformations**: Break into smaller steps
   - **Large volumes**: Use partitioned, parallel processing
   - **Downtime constraints**: Incremental migration with CDC
   - **Legacy system limitations**: Read-only replicas
   
   **Post-migration:**
   - Monitor new system performance
   - Keep legacy system as backup initially
   - Gradual decommissioning
   - Document migration for audit

4. **How would you handle late-arriving dimensions in a data warehouse?**
   
   Late-arriving dimensions occur when fact records arrive before their dimension records.
   
   **Strategies:**
   
   **Default/placeholder dimension:**
   - Create "unknown" or "pending" dimension record
   - Assign facts to placeholder initially
   - Update when actual dimension arrives
   ```sql
   -- Create placeholder
   INSERT INTO dim_customer (customer_key, customer_id, name, status)
   VALUES (-1, 'UNKNOWN', 'Pending Customer', 'PENDING');
   
   -- Load fact with placeholder
   INSERT INTO fact_sales (customer_key, product_key, amount)
   SELECT COALESCE(d.customer_key, -1), f.product_key, f.amount
   FROM staging_sales f
   LEFT JOIN dim_customer d ON f.customer_id = d.customer_id;
   
   -- Update when dimension arrives
   UPDATE fact_sales f
   SET customer_key = d.customer_key
   FROM dim_customer d
   WHERE f.customer_key = -1
   AND d.customer_id = (
       SELECT customer_id FROM staging_customer 
       WHERE customer_key = -1
   );
   ```
   
   **Inferred member approach:**
   - Create minimal dimension record with available info
   - Mark as "inferred"
   - Enhance when complete data arrives
   ```sql
   -- Create inferred dimension
   INSERT INTO dim_customer (customer_key, customer_id, inferred_flag)
   SELECT NEXTVAL('dim_customer_seq'), customer_id, TRUE
   FROM fact_staging
   WHERE customer_id NOT IN (SELECT customer_id FROM dim_customer);
   
   -- Update with complete data
   UPDATE dim_customer d
   SET name = s.name,
       email = s.email,
       inferred_flag = FALSE,
       updated_date = CURRENT_TIMESTAMP
   FROM staging_customer s
   WHERE d.customer_id = s.customer_id
   AND d.inferred_flag = TRUE;
   ```
   
   **Queue and reprocess:**
   - Hold facts in staging until dimensions available
   - Periodic check and retry
   ```python
   def process_late_arriving_dimensions():
       # Check for pending facts
       pending_facts = get_pending_facts()
       
       for fact in pending_facts:
           dimension = lookup_dimension(fact['dimension_id'])
           if dimension:
               # Dimension now available
               update_fact(fact, dimension)
               mark_processed(fact)
           elif days_pending(fact) > threshold:
               # Too old, use default
               update_fact(fact, default_dimension)
               log_late_arrival(fact)
   ```
   
   **Temporal staging:**
   - Stage facts by arrival date
   - Process in batches after grace period
   - Reduces placeholder usage
   
   **Early arriving facts table:**
   - Separate table for facts waiting on dimensions
   - Join and move to main fact table when dimension arrives
   
   **Best practices:**
   - **Grace period**: Wait reasonable time before using placeholder
   - **Monitoring**: Track late-arrival frequency
   - **Alerting**: Notify on excessive late arrivals
   - **Root cause**: Investigate and fix source timing issues
   - **Documentation**: Define SLAs for dimension availability

5. **How do you design ETL for handling both structured and unstructured data?**
   
   **Unified architecture:**
   
   **Data lake foundation:**
   - Central repository for all data types
   - Raw zone for original format
   - Processed zones for refined data
   
   **Structure:**
   ```
   /data-lake/
   ├── raw/
   │   ├── structured/ (CSV, databases)
   │   ├── semi-structured/ (JSON, XML, logs)
   │   └── unstructured/ (PDFs, images, videos)
   ├── processed/
   │   ├── structured/ (Parquet, optimized)
   │   └── enriched/ (extracted text, metadata)
   └── curated/
       └── analytics/ (ready for BI)
   ```
   
   **Processing approach:**
   
   **Structured data (traditional ETL):**
   ```python
   # Standard ETL for databases, CSV
   structured_df = spark.read.jdbc(url, table, properties)
   transformed = structured_df.select(...).filter(...).groupBy(...)
   transformed.write.parquet("s3://lake/processed/structured/")
   ```
   
   **Semi-structured data (JSON, XML):**
   ```python
   # JSON processing
   json_df = spark.read.json("s3://lake/raw/semi-structured/")
   
   # Flatten nested structures
   flattened = json_df.select(
       col("id"),
       col("user.name").alias("user_name"),
       explode(col("transactions")).alias("transaction")
   )
   
   # Schema enforcement
   from pyspark.sql.types import StructType, StringType, IntegerType
   schema = StructType([
       StructField("id", IntegerType()),
       StructField("name", StringType())
   ])
   validated = spark.read.schema(schema).json("s3://lake/raw/")
   ```
   
   **Unstructured data processing:**
   
   **Text extraction:**
   ```python
   import pdfplumber
   from PIL import Image
   import pytesseract
   
   def process_unstructured_file(file_path):
       file_type = get_file_type(file_path)
       
       if file_type == 'pdf':
           # Extract text from PDF
           with pdfplumber.open(file_path) as pdf:
               text = '\n'.join([page.extract_text() for page in pdf.pages])
       
       elif file_type == 'image':
           # OCR for images
           image = Image.open(file_path)
           text = pytesseract.image_to_string(image)
       
       elif file_type == 'doc':
           # Word document processing
           text = extract_text_from_doc(file_path)
       
       # Store extracted text
       metadata = {
           'source_file': file_path,
           'extracted_text': text,
           'processed_date': datetime.now(),
           'file_size': os.path.getsize(file_path)
       }
       
       return metadata
   ```
   
   **Integration pattern:**
   ```python
   def unified_etl_pipeline():
       # Process structured data
       structured = process_structured_sources()
       
       # Process semi-structured data
       semi_structured = process_json_xml_logs()
       
       # Process unstructured data
       unstructured_metadata = process_documents_images()
       
       # Combine in data lake
       all_data = {
           'structured': structured,
           'semi_structured': semi_structured,
           'unstructured': unstructured_metadata
       }
       
       # Create unified catalog
       catalog_entry = {
           'dataset_id': generate_id(),
           'sources': all_data,
           'schema': infer_combined_schema(all_data),
           'location': 's3://lake/curated/'
       }
       
       register_in_catalog(catalog_entry)
   ```
   
   **Technology choices:**
   - **Structured**: Traditional ETL tools, SQL engines
   - **Semi-structured**: Spark, schema inference
   - **Unstructured**: NLP libraries, ML models, specialized tools
   
   **Use cases:**
   - **Customer 360**: Combine CRM (structured) + emails (unstructured) + clickstream (semi-structured)
   - **Document processing**: Extract data from invoices, contracts
   - **Multi-modal analytics**: Integrate images, text, and metadata

6. **What strategy would you use for ETL in a multi-tenant environment?**
   
   **Isolation strategies:**
   
   **Data isolation approaches:**
   - **Database per tenant**: Separate database for each tenant (strongest isolation, highest cost)
   - **Schema per tenant**: Separate schemas within shared database (good isolation, moderate cost)
   - **Table per tenant**: Tenant-specific tables with naming convention (tenant_123_orders)
   - **Row-level isolation**: Shared tables with tenant_id column (lowest cost, requires careful security)
   
   **ETL design patterns:**
   
   **Separate pipelines:**
   - Individual ETL jobs per tenant
   - Isolated execution and failure domains
   - Custom schedules and SLAs per tenant
   - Higher operational overhead
   
   **Shared pipeline with partitioning:**
   - Single ETL job processes all tenants
   - Partition by tenant_id for parallel processing
   - Shared infrastructure, lower costs
   - Risk of one tenant affecting others
   
   **Hybrid approach:**
   - Shared extraction and staging
   - Tenant-specific transformation and loading
   - Balance between efficiency and isolation
   
   **Implementation considerations:**
   - **Resource allocation**: CPU/memory limits per tenant to prevent monopolization
   - **Data security**: Encrypt tenant data, strict access controls, audit all access
   - **Performance SLAs**: Different tiers (premium tenants get priority processing)
   - **Metadata management**: Track tenant-specific schemas, configurations
   - **Monitoring**: Per-tenant metrics, separate alerting thresholds
   - **Scalability**: Auto-scaling based on tenant growth
   
   **Best practices:**
   - Use tenant_id in all tables for filtering
   - Implement row-level security policies
   - Separate staging areas per tenant
   - Parameterize ETL jobs with tenant context
   - Monitor resource usage per tenant
   - Test cross-tenant data leakage scenarios

7. **How would you handle ETL for time-series data?**
   
   **Time-series characteristics:**
   - High volume, continuous data streams
   - Time-ordered records with timestamps
   - Often append-only (historical data doesn't change)
   - Regular intervals (seconds, minutes, hours)
   - Requires aggregations over time windows
   
   **ETL design approach:**
   
   **Partitioning strategy:**
   - Partition by time (hourly, daily, monthly)
   - Enables efficient queries and data retention
   - Facilitates incremental processing
   
   **Incremental loading:**
   - Use timestamp-based watermarking
   - Process only new time ranges
   - Maintain high watermark for each source
   
   **Aggregation patterns:**
   - Pre-compute common time-based aggregations (hourly, daily, weekly)
   - Store at multiple granularities for performance
   - Use materialized views or summary tables
   
   **Late data handling:**
   - Define acceptable lateness window (e.g., accept data up to 24 hours late)
   - Implement reprocessing for late arrivals
   - Track completeness metrics per time window
   
   **Data retention:**
   - Hot storage: Recent data (last 30 days) in fast storage
   - Warm storage: Medium-term data (3-12 months) in standard storage
   - Cold storage: Historical data (>1 year) in archival storage
   - Implement automated data aging and archival
   
   **Technologies:**
   - **Databases**: InfluxDB, TimescaleDB, Prometheus (purpose-built for time-series)
   - **Streaming**: Kafka, Kinesis for real-time ingestion
   - **Processing**: Spark Structured Streaming for windowed aggregations
   - **Storage**: Columnar formats (Parquet) with time-based partitioning
   
   **Optimization techniques:**
   - Downsample old data (reduce granularity over time)
   - Compress historical partitions
   - Use time-series specific compression algorithms
   - Index on timestamp column
   - Partition pruning in queries

8. **How do you approach ETL for data lake implementations?**
   
   **Data lake architecture:**
   
   **Zone-based organization:**
   - **Raw/Landing zone**: Exact copy of source data, immutable
   - **Cleansed/Standardized zone**: Validated, standardized formats
   - **Curated/Conformed zone**: Business-ready, integrated datasets
   - **Consumption zone**: Purpose-specific data marts, aggregated views
   
   **ETL patterns for data lakes:**
   
   **Schema-on-read approach:**
   - Store raw data without enforcing schema
   - Apply schema during query/processing
   - Flexibility for future use cases
   
   **File format strategy:**
   - Landing: Original format (CSV, JSON, Avro)
   - Processed: Columnar formats (Parquet, ORC) for analytics
   - Benefits: Better compression, faster queries, schema evolution support
   
   **Partitioning strategy:**
   - Partition by date, source system, data type
   - Enables efficient data pruning
   - Supports parallel processing
   
   **Metadata management:**
   - Catalog all datasets (AWS Glue Catalog, Hive Metastore)
   - Track schema versions
   - Maintain data lineage
   - Document data quality metrics
   
   **Data quality layers:**
   - Raw zone: No quality checks (preserve original)
   - Cleansed zone: Validation, deduplication, standardization
   - Curated zone: Business rules, enrichment, aggregation
   
   **Processing approach:**
   - Batch processing: Spark jobs for large-scale transformations
   - Streaming: Real-time ingestion with Kafka/Kinesis
   - Lambda architecture: Both batch and stream processing
   
   **Governance:**
   - Apply data classification tags
   - Implement access controls per zone
   - Audit data access
   - Enforce retention policies
   - Version control for ETL code
   
   **Best practices:**
   - Never delete raw data (storage is cheap)
   - Version all transformations
   - Make zones immutable (write once, read many)
   - Automate data quality checks
   - Implement data discovery tools

9. **What is your strategy for handling ETL pipeline dependencies?**
   
   **Dependency types:**
   - **Data dependencies**: Job B requires completion of Job A
   - **Resource dependencies**: Jobs compete for same resources
   - **Temporal dependencies**: Time-based sequencing requirements
   - **External dependencies**: Third-party data or service availability
   
   **Management approaches:**
   
   **Workflow orchestration:**
   - Use DAG (Directed Acyclic Graph) based tools (Airflow, Prefect, Dagster)
   - Define explicit dependencies between tasks
   - Handle complex workflows with branching and conditionals
   
   **Dependency declaration:**
   - Upstream/downstream relationships
   - Service-level dependencies (database, API availability)
   - Data availability sensors/triggers
   
   **Execution patterns:**
   - **Sequential**: Jobs run one after another
   - **Parallel**: Independent jobs run simultaneously
   - **Fan-out/Fan-in**: One job triggers multiple, then converge
   
   **Handling strategies:**
   
   **Data availability checks:**
   - Sensor tasks wait for upstream data
   - File/table existence validation
   - Data freshness checks (timestamp validation)
   - Timeout configuration for waiting
   
   **Failure handling:**
   - Retry logic for transient failures
   - Skip downstream if upstream fails
   - Alert on dependency violations
   - Maintain dependency health dashboard
   
   **Cross-system dependencies:**
   - API health checks before extraction
   - Database availability validation
   - External data feed monitoring
   - Circuit breaker patterns for unreliable dependencies
   
   **Best practices:**
   - Document all dependencies explicitly
   - Minimize inter-job dependencies where possible
   - Implement dependency timeouts
   - Use idempotent operations
   - Version control workflow definitions
   - Test dependency chains in non-production
   - Monitor dependency graph complexity
   - Implement backfill capabilities for failed dependencies

10. **How would you design a disaster recovery plan for ETL systems?**
    
    **DR planning components:**
    
    **Backup strategy:**
    - **Code**: Version control (Git) with offsite repository
    - **Configuration**: Backup connection strings, parameters, schedules
    - **Metadata**: ETL job definitions, mappings, lineage
    - **Data**: Backup staging, audit tables, control tables
    - **Frequency**: Daily incremental, weekly full backups
    
    **Recovery objectives:**
    - **RTO (Recovery Time Objective)**: Maximum acceptable downtime
    - **RPO (Recovery Point Objective)**: Maximum acceptable data loss
    - Define per pipeline based on criticality
    
    **Infrastructure redundancy:**
    - **Primary site**: Production ETL environment
    - **DR site**: Standby environment in different region/datacenter
    - **Active-passive**: DR site on standby, activated during disaster
    - **Active-active**: Both sites process data (geo-distributed)
    
    **Data replication:**
    - **Source systems**: Access from DR site or replicate critical sources
    - **Data warehouse**: Real-time or near-real-time replication
    - **Staging area**: Replicate to DR site or recreate from source
    
    **Failover procedures:**
    
    **Automated failover:**
    - Health monitoring triggers automatic failover
    - DNS/load balancer redirects to DR site
    - Automated testing of DR readiness
    
    **Manual failover:**
    - Documented step-by-step procedures
    - Communication plan for stakeholders
    - Validation checklist post-failover
    
    **Recovery scenarios:**
    
    **Infrastructure failure:**
    - Server crash: Restart jobs on standby server
    - Network outage: Reroute through alternate network
    - Data center failure: Activate DR site
    
    **Data corruption:**
    - Restore from backup to point-in-time
    - Rerun ETL from last known good state
    - Quarantine corrupted data
    
    **Complete disaster:**
    - Activate full DR environment
    - Restore all systems from backups
    - Resume operations at DR site
    
    **Testing and validation:**
    - **DR drills**: Quarterly failover tests
    - **Backup validation**: Monthly restore tests
    - **Documentation review**: Annual updates
    - **Performance testing**: Verify DR site capacity
    
    **Monitoring and alerts:**
    - Real-time replication lag monitoring
    - Backup success/failure alerts
    - DR site health checks
    - Capacity monitoring at both sites
    
    **Documentation requirements:**
    - Network diagrams and configurations
    - Server inventory and specifications
    - Runbooks for recovery procedures
    - Contact lists for escalation
    - Vendor support contacts
    - Recovery time estimates per scenario
    
    **Best practices:**
    - Automate backup and recovery processes
    - Store backups in geographically separate locations
    - Encrypt backup data
    - Test recovery regularly
    - Keep DR documentation current
    - Train team on recovery procedures
    - Maintain spare capacity in DR site
    - Document dependencies clearly

## Advanced ETL Concepts

1. **What is data virtualization and how does it compare to ETL?**
   
   Data virtualization creates a unified virtual layer that provides real-time access to data from multiple sources without physically moving or replicating it.
   
   **Key differences:**
   
   | Aspect | ETL | Data Virtualization |
   |--------|-----|---------------------|
   | Data movement | Physical copy to target | No data movement |
   | Latency | Batch delays (minutes to hours) | Real-time access |
   | Storage | Requires target storage | Uses source storage |
   | Transformation | Pre-processed | On-demand processing |
   | Performance | Optimized for analytics | Depends on source performance |
   | Historical data | Maintains history | Accesses current data only |
   
   **When to use data virtualization:**
   - Real-time data access required
   - Multiple source systems with infrequent queries
   - Proof of concept before full ETL implementation
   - Data exploration and discovery
   - Complementing ETL for ad-hoc queries
   
   **When to use ETL:**
   - Heavy analytics and reporting workloads
   - Historical data tracking needed
   - Complex transformations required
   - Source systems can't handle query load
   - Data quality and consistency critical
   
   **Hybrid approach:**
   - Use virtualization for real-time operational queries
   - Use ETL for historical analytics and reporting
   - Virtual views on top of data warehouse

2. **How do you implement data partitioning strategies in ETL?**
   
   **Partitioning types:**
   
   **Range partitioning:**
   - Partition by continuous values (dates, numbers)
   - Most common for time-based data
   - Example: Daily, monthly, yearly partitions
   
   **Hash partitioning:**
   - Distribute data evenly using hash function
   - Good for parallel processing
   - Example: Hash on customer_id for balanced distribution
   
   **List partitioning:**
   - Partition by discrete values
   - Example: By region (NORTH, SOUTH, EAST, WEST)
   
   **Composite partitioning:**
   - Combine multiple methods
   - Example: Range by date, then hash by customer_id
   
   **Implementation strategies:**
   
   **Source partitioning:**
   - Query specific partitions from source
   - Reduces data extraction volume
   - Enables parallel extraction
   
   **Processing partitioning:**
   - Process partitions independently in parallel
   - Reduces memory requirements
   - Enables horizontal scaling
   
   **Target partitioning:**
   - Load into specific target partitions
   - Faster loads using partition exchange
   - Easier maintenance and archival
   
   **Benefits:**
   - **Query performance**: Partition pruning reduces scan volume
   - **Parallel processing**: Multiple partitions processed simultaneously
   - **Maintenance**: Drop/archive old partitions easily
   - **Availability**: Partition-level operations don't lock entire table
   - **Manageability**: Smaller, manageable data chunks
   
   **Best practices:**
   - Choose partition key based on query patterns
   - Avoid too many partitions (overhead)
   - Avoid too few partitions (defeats purpose)
   - Align ETL batch size with partitions
   - Monitor partition sizes for balance

3. **What is the role of metadata in ETL processes?**
   
   Metadata is "data about data" that describes the structure, lineage, and characteristics of data in ETL systems.
   
   **Types of metadata:**
   
   **Technical metadata:**
   - Table and column names, data types, lengths
   - Primary/foreign key relationships
   - Indexes and partitioning schemes
   - Source-to-target mappings
   - ETL job schedules and dependencies
   
   **Business metadata:**
   - Business definitions and glossary
   - Data ownership and stewardship
   - Business rules and calculations
   - Data quality metrics and SLAs
   
   **Operational metadata:**
   - Job execution logs (start time, end time, status)
   - Row counts processed
   - Error logs and rejection counts
   - Performance metrics
   - Data lineage (source to target flow)
   
   **Uses in ETL:**
   
   **Design phase:**
   - Understand source system schemas
   - Define transformation rules
   - Document mappings
   
   **Development phase:**
   - Generate ETL code from metadata
   - Configure connections and parameters
   - Validate data types and constraints
   
   **Execution phase:**
   - Dynamic job configuration
   - Impact analysis for changes
   - Automated documentation
   
   **Monitoring phase:**
   - Track job performance
   - Data quality monitoring
   - Lineage tracking for audits
   
   **Metadata management tools:**
   - Informatica Enterprise Data Catalog
   - Collibra
   - Alation
   - Apache Atlas
   - AWS Glue Data Catalog
   
   **Benefits:**
   - Improved data discovery and understanding
   - Faster impact analysis
   - Automated documentation
   - Better data governance
   - Simplified troubleshooting

4. **How do you handle complex data transformations with business logic?**
   
   **Approaches for complex transformations:**
   
   **Layered transformation architecture:**
   - **Stage 1**: Basic cleansing and standardization
   - **Stage 2**: Business rule application
   - **Stage 3**: Aggregation and enrichment
   - **Stage 4**: Final formatting for target
   
   **Business rules engine:**
   - Externalize rules from ETL code
   - Store rules in configuration tables/files
   - Enable business users to modify rules
   - Version control rule changes
   
   **Lookup and reference data:**
   - Maintain reference tables for mappings
   - Cache frequently used lookups
   - Handle missing lookup values gracefully
   
   **Custom functions/procedures:**
   - Encapsulate complex logic in reusable functions
   - Database stored procedures for DB-specific logic
   - User-defined functions in ETL tools
   - Python/Java libraries for advanced calculations
   
   **Conditional logic patterns:**
   - Use CASE statements for multi-condition logic
   - Implement decision trees for complex classifications
   - Route records to different transformations based on rules
   
   **Data quality integration:**
   - Validate data against business rules
   - Flag violations for review
   - Apply default values based on business logic
   
   **Implementation example:**
   - Customer segmentation based on multiple criteria
   - Revenue calculation with complex tax rules
   - Risk scoring with weighted factors
   - Product categorization using hierarchical rules
   
   **Best practices:**
   - Document business rules clearly
   - Make transformations testable and modular
   - Separate business logic from technical code
   - Version control all rule changes
   - Involve business stakeholders in validation
   - Handle edge cases explicitly
   - Log rule application for audit

5. **What is the difference between horizontal and vertical scaling in ETL?**
   
   **Vertical scaling (Scale-up):**
   - Add more resources to existing server (CPU, RAM, storage)
   - Single machine with increased capacity
   
   **Advantages:**
   - Simpler to implement (no code changes)
   - No data distribution complexity
   - Lower licensing costs (single server)
   - Easier to manage and monitor
   
   **Disadvantages:**
   - Physical hardware limits
   - Single point of failure
   - Diminishing returns (cost increases exponentially)
   - Downtime required for upgrades
   
   **Use cases:**
   - Small to medium data volumes
   - Legacy ETL tools that don't support distribution
   - Quick performance improvements needed
   
   **Horizontal scaling (Scale-out):**
   - Add more servers to distribute workload
   - Cluster of machines working together
   
   **Advantages:**
   - Nearly unlimited scalability
   - Better fault tolerance (redundancy)
   - Cost-effective (commodity hardware)
   - No downtime for adding nodes
   
   **Disadvantages:**
   - Complex architecture
   - Data distribution overhead
   - Network latency considerations
   - More complex monitoring
   
   **Use cases:**
   - Big data processing
   - Cloud-native ETL
   - Elastic workloads with varying demand
   
   **ETL-specific considerations:**
   - **Vertical**: Better for complex transformations on moderate data
   - **Horizontal**: Better for simple transformations on massive data
   - **Hybrid**: Common approach combining both strategies
   
   **Technologies:**
   - Vertical: Traditional ETL tools (Informatica, SSIS)
   - Horizontal: Spark, Hadoop, cloud services with auto-scaling

6. **How do you implement data quality frameworks in ETL?**
   
   **Data quality dimensions:**
   - **Accuracy**: Data correctly represents reality
   - **Completeness**: All required data is present
   - **Consistency**: Data is uniform across systems
   - **Timeliness**: Data is current and available when needed
   - **Validity**: Data conforms to defined formats and rules
   - **Uniqueness**: No duplicate records
   
   **Framework implementation:**
   
   **Rule definition layer:**
   - Define quality rules per data element
   - Store rules in metadata repository
   - Categorize rules by severity (critical, warning, info)
   
   **Validation layer:**
   - Apply rules during ETL execution
   - Check at multiple stages (source, transform, target)
   - Generate quality scores and metrics
   
   **Monitoring layer:**
   - Track quality metrics over time
   - Dashboards for quality trends
   - Automated alerts for threshold breaches
   
   **Remediation layer:**
   - Automated correction for known issues
   - Manual review queues for complex cases
   - Track resolution status
   
   **Quality checks implementation:**
   - **Schema validation**: Data types, nullability, constraints
   - **Domain validation**: Value ranges, allowed values
   - **Format validation**: Regex patterns for emails, phones
   - **Cross-field validation**: Logical consistency checks
   - **Statistical validation**: Outlier detection, distribution checks
   - **Referential integrity**: Foreign key validation
   
   **Tools and techniques:**
   - Great Expectations (Python framework)
   - Informatica Data Quality
   - Talend Data Quality
   - Custom SQL validation queries
   - dbt tests
   
   **Best practices:**
   - Define quality SLAs with stakeholders
   - Automate quality checks in CI/CD
   - Create quality scorecards
   - Root cause analysis for failures
   - Continuous improvement based on metrics

7. **What is the role of machine learning in modern ETL pipelines?**
   
   **ML applications in ETL:**
   
   **Data quality enhancement:**
   - Anomaly detection for data quality issues
   - Automated data cleansing using ML models
   - Duplicate detection with fuzzy matching
   - Missing value imputation using predictive models
   
   **Intelligent data mapping:**
   - Auto-suggest column mappings based on similarity
   - Schema matching using ML algorithms
   - Semantic understanding of data relationships
   
   **Predictive optimization:**
   - Forecast data volumes for resource planning
   - Predict job failures before they occur
   - Optimize execution schedules based on patterns
   
   **Automated classification:**
   - Auto-tag and categorize data
   - PII detection and classification
   - Content-based routing and transformation
   
   **Data extraction:**
   - OCR and document parsing using computer vision
   - Named entity recognition for unstructured data
   - Sentiment analysis for text data
   
   **Performance optimization:**
   - Query optimization using ML
   - Adaptive partitioning based on access patterns
   - Resource allocation optimization
   
   **Example implementations:**
   - Amazon Comprehend for NLP in ETL
   - Google Cloud AutoML for custom models
   - Azure Cognitive Services for data enrichment
   - Open-source libraries (scikit-learn, TensorFlow)
   
   **Integration patterns:**
   - ML models as transformation steps
   - Real-time scoring during data flow
   - Batch prediction for large datasets
   - Model versioning and A/B testing

8. **How do you handle bi-temporal data in ETL?**
   
   Bi-temporal data tracks two time dimensions: business time (when event occurred) and system time (when data was recorded).
   
   **Time dimensions:**
   - **Valid time (Business time)**: When fact was true in real world
   - **Transaction time (System time)**: When fact was recorded in database
   
   **Use cases:**
   - Financial transactions (trade date vs settlement date)
   - Insurance policies (effective date vs record date)
   - Historical corrections (retroactive adjustments)
   - Audit and compliance requirements
   
   **Schema design:**
   
   **Four timestamps approach:**
   - valid_from: Start of business validity
   - valid_to: End of business validity
   - system_from: When record was inserted
   - system_to: When record was superseded (NULL for current)
   
   **ETL implementation:**
   
   **Insert pattern:**
   - New records get current timestamp for system_from
   - Business dates from source data
   - system_to set to NULL (current record)
   
   **Update pattern:**
   - Don't update existing records
   - Close old record (set system_to = current_timestamp)
   - Insert new record with updated values
   - Maintain same valid_from/valid_to for corrections
   
   **Retroactive changes:**
   - Update affects historical business time
   - Create new system time record
   - Preserves history of what we knew and when
   
   **Query patterns:**
   - Current view: WHERE system_to IS NULL
   - Point-in-time business view: WHERE valid_from <= date AND valid_to > date AND system_to IS NULL
   - Historical system view: WHERE system_from <= date AND (system_to > date OR system_to IS NULL)
   
   **Complexity considerations:**
   - Increased storage requirements
   - More complex queries
   - Requires careful SCD implementation
   - Testing retroactive scenarios
   
   **Benefits:**
   - Complete audit trail
   - Support for corrections without losing history
   - Regulatory compliance
   - Time-travel queries

9. **What is reverse ETL and when is it used?**
   
   Reverse ETL moves data from data warehouse/data lake back to operational systems and SaaS applications.
   
   **Traditional ETL flow:**
   Source Systems → Data Warehouse → BI Tools
   
   **Reverse ETL flow:**
   Data Warehouse → Operational Systems (CRM, Marketing tools)
   
   **Use cases:**
   
   **Customer data activation:**
   - Sync enriched customer segments to marketing platforms
   - Update CRM with calculated metrics (LTV, propensity scores)
   - Push personalized recommendations to applications
   
   **Operational analytics:**
   - Feed ML predictions back to production systems
   - Update inventory systems with forecasted demand
   - Sync risk scores to fraud detection systems
   
   **Data democratization:**
   - Make warehouse insights available in business tools
   - Update Google Sheets/Excel with latest metrics
   - Sync to Salesforce, HubSpot, Marketo
   
   **Implementation approaches:**
   
   **ETL tools in reverse:**
   - Configure traditional ETL tools to write to APIs
   - Schedule regular syncs
   
   **Specialized reverse ETL tools:**
   - Census, Hightouch, Polytomic
   - Built-in connectors for common SaaS apps
   - Field mapping and transformation
   - Sync monitoring and error handling
   
   **Custom solutions:**
   - Python scripts calling APIs
   - Cloud Functions/Lambda for event-driven syncs
   
   **Challenges:**
   - API rate limits and throttling
   - Schema differences between systems
   - Error handling for failed syncs
   - Maintaining data consistency
   - Security and access control
   
   **Benefits:**
   - Operationalize analytics insights
   - Close the data loop
   - Empower business users with warehouse data
   - Reduce data silos

10. **How do you implement DataOps practices in ETL development?**
    
    DataOps applies DevOps principles to data analytics and ETL development for faster, higher-quality delivery.
    
    **Core principles:**
    
    **Version control:**
    - All ETL code in Git
    - SQL scripts, configurations, schemas
    - Branch strategies (feature, develop, main)
    - Pull requests for code review
    
    **Automated testing:**
    - Unit tests for transformation logic
    - Integration tests for end-to-end workflows
    - Data quality tests
    - Regression tests for changes
    - Test data management
    
    **Continuous Integration (CI):**
    - Automated builds on code commit
    - Run test suites automatically
    - Code quality checks (linting, complexity)
    - Immediate feedback to developers
    
    **Continuous Deployment (CD):**
    - Automated deployment to environments
    - DEV → QA → UAT → PROD pipeline
    - Automated smoke tests post-deployment
    - Rollback capabilities
    
    **Environment management:**
    - Separate DEV, QA, PROD environments
    - Infrastructure as Code (Terraform, CloudFormation)
    - Containerization for consistency (Docker)
    - Environment parity
    
    **Monitoring and observability:**
    - Real-time job monitoring
    - Data quality dashboards
    - Performance metrics tracking
    - Centralized logging
    - Alerting and incident management
    
    **Collaboration:**
    - Cross-functional teams (data engineers, analysts, business)
    - Documentation as code
    - Knowledge sharing and retrospectives
    - Agile/Scrum methodologies
    
    **Orchestration:**
    - Workflow automation (Airflow, Prefect)
    - Dependency management
    - Scheduled and event-driven execution
    
    **Tools:**
    - Git/GitHub/GitLab for version control
    - Jenkins/CircleCI/GitHub Actions for CI/CD
    - dbt for transformation testing and documentation
    - Docker for containerization
    - Terraform for infrastructure
    - Datadog/New Relic for monitoring
    
    **Best practices:**
    - Treat data pipelines as software
    - Automate everything possible
    - Small, frequent releases
    - Monitor data quality continuously
    - Fail fast and recover quickly
    - Document automatically
    - Measure and improve cycle time

Sources
1. [Top 17 ETL Interview Questions and Answers For All Levels](https://www.datacamp.com/blog/top-etl-interview-questions-and-answers)
2. [100+ ETL Interview Questions and Answers for 2025](https://www.turing.com/interview-questions/etl)
3. [100+ ETL Interview Questions and Answers (2025)](https://www.wecreateproblems.com/interview-questions/etl-interview-questions)
4. [ETL Developer Interview Questions and Answers for 2025](https://www.simplilearn.com/etl-testing-interview-questions-article)
5. [50+ ETL Interview Questions and Answers for 2025](https://www.projectpro.io/article/etl-interview-questions-and-answers/744)
6. [Top 60+ Data Engineer Interview Questions and Answers](https://www.geeksforgeeks.org/data-engineering/data-engineer-interview-questions/)
7. [Top 40+ ETL Testing Interview Questions and Answers](https://www.hirist.tech/blog/top-40-etl-testing-interview-questions-and-answers/)
8. [The Top 39 Data Engineering Interview Questions and ...](https://www.datacamp.com/blog/top-21-data-engineering-interview-questions-and-answers)
9. [Ace Your ETL Interview: Basic, Testing, SQL & more](https://upesonline.ac.in/blog/etl-interview-questions)
10. [Top 90+ Data Engineer Interview Questions and Answers](https://www.netcomlearning.com/blog/data-engineer-interview-questions)
11. [Top 40+ ETL Testing Interview Questions 2025 (For ...](https://www.geeksforgeeks.org/software-testing/etl-testing-interview-questions/)
12. [Interview Questions & Answers](https://www.ctanujit.org/uploads/2/5/3/9/25393293/data_engineering_interviews.pdf)
13. [14 Data Engineer Interview Questions and How to Answer ...](https://www.coursera.org/in/articles/data-engineer-interview-questions)
14. [15 Data Engineering Interview Questions & Answers](https://datalemur.com/blog/data-engineer-interview-guide)
15. [Data Engineer Interview Questions & Answers 2025](https://365datascience.com/career-advice/job-interview-tips/data-engineer-interview-questions/)
