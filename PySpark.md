# Comprehensive PySpark Interview Questions by Topic

## Table of Contents
1. [PySpark Fundamentals](#pyspark-fundamentals)
2. [RDD (Resilient Distributed Datasets)](#rdd-resilient-distributed-datasets)
3. [PySpark DataFrames and Datasets](#pyspark-dataframes-and-datasets)
4. [Transformations](#transformations)
5. [Aggregations and Grouping](#aggregations-and-grouping)
6. [Joins and Data Manipulation](#joins-and-data-manipulation)
7. [Data Handling and Quality](#data-handling-and-quality)
8. [User-Defined Functions (UDFs)](#user-defined-functions-udfs)
9. [Performance Optimization](#performance-optimization)
10. [Spark SQL and Catalyst Optimizer](#spark-sql-and-catalyst-optimizer)
11. [File Formats and I/O Operations](#file-formats-and-io-operations)
12. [Spark Streaming](#spark-streaming)
13. [Cluster Architecture and Resource Management](#cluster-architecture-and-resource-management)
14. [Fault Tolerance and Reliability](#fault-tolerance-and-reliability)
15. [Delta Lake and Advanced Topics](#delta-lake-and-advanced-topics)
16. [Machine Learning with MLlib](#machine-learning-with-mllib)
17. [Scenario-Based and Coding Questions](#scenario-based-and-coding-questions)

## PySpark Fundamentals

1. What is PySpark and how does it differ from Apache Spark?

    **PySpark** is the Python API for Apache Spark, enabling distributed data processing using Python.
    
    **Key Differences:**
    - **Apache Spark:** Core distributed computing engine written in Scala
    - **PySpark:** Python wrapper providing Pythonic interface to Spark
    - Communication via **Py4J** (Python-to-JVM bridge)
    - PySpark slightly slower than Scala due to serialization overhead
    - PySpark leverages Python ecosystem (NumPy, Pandas, scikit-learn)

2. Explain the PySpark architecture and its core components

    **Architecture Layers:**
    
    **1. Driver Program:**
    - Runs main() function
    - Creates SparkContext/SparkSession
    - Converts code to execution plan
    
    **2. Cluster Manager:**
    - Allocates resources (YARN, Mesos, Kubernetes, Standalone)
    
    **3. Executors:**
    - Run tasks on worker nodes
    - Store data in memory/disk
    - Return results to driver
    
    **Core Components:**
    - **SparkContext:** Entry point for RDD operations
    - **SparkSession:** Unified entry point (DataFrame, SQL, Streaming)
    - **RDD/DataFrame/Dataset:** Data abstractions
    - **DAG Scheduler:** Creates execution plan
    - **Task Scheduler:** Schedules tasks on executors

3. What is SparkContext and SparkSession? When do you use each?

    **SparkContext:**
    - Lower-level entry point for Spark functionality
    - Used for RDD operations
    - One per JVM (application)
    
    **SparkSession:**
    - Unified entry point (introduced in Spark 2.0)
    - Encapsulates SparkContext, SQLContext, HiveContext
    - Used for DataFrame, SQL, and streaming operations
    - Recommended for all new applications
    
    ```python
    # Modern approach - SparkSession
    from pyspark.sql import SparkSession
    spark = SparkSession.builder.appName("MyApp").getOrCreate()
    
    # Access SparkContext if needed
    sc = spark.sparkContext
    ```

4. How does PySpark enable distributed computing?

    **Distribution Mechanism:**
    
    **1. Data Partitioning:**
    - Data split into partitions across cluster nodes
    - Each partition processed independently
    
    **2. Parallel Execution:**
    - Tasks run concurrently on multiple executors
    - One task per partition
    
    **3. Fault Tolerance:**
    - Lineage tracking enables recomputation on failure
    
    **4. In-Memory Processing:**
    - Intermediate results cached in memory
    - Reduces disk I/O
    
    **5. Lazy Evaluation:**
    - Builds execution plan before running
    - Optimizes entire workflow

5. What is the role of Py4j in PySpark?

    **Py4J** is a library that enables Python programs to dynamically access Java objects in a JVM.
    
    **Role in PySpark:**
    - Bridges Python code to Spark's JVM
    - Translates Python API calls to Java/Scala Spark operations
    - Serializes/deserializes data between Python and JVM
    - Handles communication between Python driver and Spark executors
    
    **Limitation:** Adds serialization overhead compared to native Scala/Java Spark.

6. Explain the concept of lazy evaluation in PySpark

    **Lazy Evaluation:** Transformations are not executed immediately; only when an action is called.
    
    **How it Works:**
    - **Transformations** (map, filter, join) build execution plan (DAG)
    - **Actions** (collect, count, save) trigger computation
    - Spark optimizes entire plan before execution
    
    **Benefits:**
    - Query optimization opportunities
    - Reduced intermediate data
    - Better resource utilization
    - Avoids unnecessary computations
    
    ```python
    # Transformations - lazy (not executed yet)
    df2 = df.filter(col("age") > 25)
    df3 = df2.select("name", "age")
    
    # Action - triggers execution of entire pipeline
    df3.show()
    ```

7. What are the different deployment modes in PySpark?

    **1. Local Mode:**
    - Runs on single machine
    - `--master local[*]` (use all cores)
    - For development/testing
    
    **2. Standalone Mode:**
    - Spark's built-in cluster manager
    - Simple setup for dedicated clusters
    
    **3. YARN Mode:**
    - Runs on Hadoop YARN cluster
    - **Client mode:** Driver on client machine
    - **Cluster mode:** Driver on cluster
    
    **4. Mesos Mode:**
    - Uses Apache Mesos for resource management
    
    **5. Kubernetes Mode:**
    - Containerized deployment
    - Modern cloud-native approach

8. How do you create a SparkSession in PySpark?

    ```python
    from pyspark.sql import SparkSession
    
    # Basic creation
    spark = SparkSession.builder \
        .appName("MyApplication") \
        .getOrCreate()
    
    # With configurations
    spark = SparkSession.builder \
        .appName("MyApp") \
        .master("local[4]") \
        .config("spark.executor.memory", "4g") \
        .config("spark.sql.shuffle.partitions", "200") \
        .enableHiveSupport() \
        .getOrCreate()
    
    # Access existing session
    spark = SparkSession.getActiveSession()
    ```

## RDD (Resilient Distributed Datasets)

1. What is an RDD and what are its key characteristics?

    **RDD (Resilient Distributed Dataset)** is the fundamental data structure of Spark, representing an immutable, distributed collection of objects that can be processed in parallel.

    **Key Characteristics:**

    **1. Resilient (Fault-Tolerant):**
    - Automatically recovers from node failures using lineage information
    - Can recompute lost partitions from source data

    **2. Distributed:**
    - Data split across multiple nodes in cluster
    - Parallel processing on partitions

    **3. Immutable:**
    - Cannot be changed once created
    - Transformations create new RDDs

    **4. Lazy Evaluation:**
    - Transformations build execution plan
    - Actions trigger computation

    **5. In-Memory Computation:**
    - Can cache data in memory for faster access
    - Falls back to disk if memory insufficient

    **6. Type-Safe:**
    - Compile-time type safety (in Scala/Java)
    - Runtime flexibility (in Python)

    **7. Partitioned:**
    - Data divided into logical partitions
    - Each partition processed independently

2. Explain the difference between RDD transformations and actions

    **Transformations** create new RDDs from existing ones (lazy), while **Actions** trigger computation and return results (eager).

    **Transformations (Lazy):**
    - Return new RDD
    - Build execution plan without execution
    - Examples: `map()`, `filter()`, `flatMap()`, `reduceByKey()`, `join()`
    
    **Actions (Eager):**
    - Trigger computation
    - Return values to driver or write to storage
    - Examples: `collect()`, `count()`, `reduce()`, `take()`, `saveAsTextFile()`

    **Key Differences:**
    - Transformations are lazy; actions are eager
    - Transformations return RDDs; actions return concrete values
    - Multiple transformations can be optimized together; actions execute the pipeline

3. What are the different ways to create an RDD in PySpark?

    **1. From Python Collection** - Using `parallelize()`:
    ```python
    rdd = sc.parallelize([1, 2, 3, 4, 5])
    ```

    **2. From External Files** - Using `textFile()`:
    ```python
    rdd = sc.textFile("hdfs://path/to/file.txt")
    ```

    **3. From Existing RDD** - Using transformations:
    ```python
    new_rdd = rdd.map(lambda x: x * 2)
    ```

    **4. From DataFrame** - Converting DataFrame to RDD:
    ```python
    rdd = df.rdd
    ```

4. How does RDD achieve fault tolerance?

    RDDs achieve fault tolerance through **lineage tracking** instead of data replication.

    **Lineage Graph:**
    - Records sequence of transformations (parent RDDs)
    - If partition lost, recompute from source using lineage
    - No need to replicate intermediate data

    **Recovery Process:**
    - Spark tracks transformation history (DAG)
    - On failure, only lost partitions recomputed
    - Uses source data and transformation sequence

    **Benefits:**
    - Memory efficient (no replication overhead)
    - Cost-effective recovery (only lost data recomputed)
    - Automatic and transparent to user

5. What happens if RDD partitions are lost due to worker node failure?

    When partitions are lost due to node failure:

    **1. Detection:** Spark detects missing partitions through heartbeat mechanism

    **2. Recomputation:** 
    - Lost partitions automatically recomputed using lineage
    - Only affected partitions recalculated, not entire RDD
    - Recomputation happens on available nodes

    **3. Speculative Execution:**
    - Slow tasks may be re-executed on different nodes
    - Helps with stragglers and partial failures

    **4. Checkpointing Option:**
    - For long lineage chains, use `checkpoint()` to save RDD to reliable storage
    - Truncates lineage, speeds up recovery

6. Explain RDD lineage and DAG (Directed Acyclic Graph)

    **RDD Lineage** is the graph of all parent RDDs and transformations used to build an RDD. **DAG** is the directed acyclic graph representing the execution plan.

    **Lineage:**
    - Records transformation dependencies between RDDs
    - Enables fault recovery by recomputation
    - Tracks entire transformation history from source

    **DAG:**
    - Logical execution plan of all operations
    - **Directed:** Dependencies flow in one direction
    - **Acyclic:** No circular dependencies
    - Optimized by Spark before execution

    **Stages:**
    - DAG divided into stages at shuffle boundaries
    - Each stage contains pipelined transformations
    - Tasks within stage execute in parallel

    **Benefits:**
    - Query optimization opportunities
    - Fault tolerance through recomputation
    - Parallel execution planning

7. What is the difference between persist() and cache() in RDD?

    **cache()** is a shorthand for **persist()** with default storage level (MEMORY_ONLY).

    **cache():**
    - Stores RDD in memory only
    - Equivalent to `persist(StorageLevel.MEMORY_ONLY)`
    - Simple, no configuration needed

    **persist():**
    - Allows custom storage level
    - More flexible with storage options
    - Can combine memory and disk

    **Storage Levels:**
    - `MEMORY_ONLY`: Store in memory, recompute if doesn't fit
    - `MEMORY_AND_DISK`: Spill to disk if memory full
    - `DISK_ONLY`: Store only on disk
    - `MEMORY_ONLY_SER`: Serialized in memory (more space-efficient)
    - `OFF_HEAP`: Store in off-heap memory

    **Usage:**
    ```python
    rdd.cache()  # Default memory storage
    rdd.persist(StorageLevel.MEMORY_AND_DISK)  # Custom storage
    ```

8. When would you choose RDD over DataFrame?

    Choose **RDD** when you need fine-grained control or work with unstructured data. Choose **DataFrame** for structured data and better performance.

    **Use RDD When:**
    - Processing unstructured data (text logs, streams)
    - Need low-level transformations and control
    - Complex custom partitioning logic required
    - Working with non-tabular data formats
    - Need precise control over data flow
    - Using functional programming patterns extensively

    **Use DataFrame When:**
    - Working with structured/semi-structured data
    - Need SQL-like operations
    - Want Catalyst optimizer benefits
    - Better performance required
    - Working with columnar data
    - Need schema enforcement
    - Integration with Spark SQL

    **Performance Note:** DataFrames are generally faster due to Catalyst optimization and Tungsten execution engine.

## PySpark DataFrames and Datasets

1. What is a DataFrame in PySpark and how does it differ from RDD?

    **DataFrame** is a distributed collection of data organized into named columns, similar to a table in a relational database.

    **Key Differences:**

    | Aspect | RDD | DataFrame |
    |--------|-----|-----------|
    | **Structure** | Unstructured collection of objects | Structured with named columns and schema |
    | **Optimization** | No automatic optimization | Catalyst optimizer and Tungsten engine |
    | **API** | Low-level, functional | High-level, declarative (SQL-like) |
    | **Type Safety** | Compile-time (Scala/Java) | Runtime schema validation |
    | **Performance** | Slower for structured data | Faster due to optimizations |
    | **Serialization** | Java serialization | Efficient binary format |
    | **Ease of Use** | More complex | Simpler, more intuitive |

    **In essence:** DataFrames are optimized, schema-aware abstractions built on top of RDDs, offering better performance and ease of use for structured data.

2. How do you read a CSV file into a DataFrame?

    ```python
    # Basic read
    df = spark.read.csv("path/to/file.csv")
    
    # With options
    df = spark.read.csv("path/to/file.csv", 
                        header=True,           # First row as header
                        inferSchema=True,      # Auto-detect data types
                        sep=",",              # Delimiter
                        nullValue="NA")       # Null representation
    
    # Alternative syntax
    df = spark.read \
        .format("csv") \
        .option("header", "true") \
        .option("inferSchema", "true") \
        .load("path/to/file.csv")
    ```

3. How do you display the schema of a DataFrame?

    ```python
    # Print schema in tree format
    df.printSchema()
    
    # Get schema object
    schema = df.schema
    
    # Get column names
    columns = df.columns
    
    # Get data types
    dtypes = df.dtypes  # Returns list of (column_name, data_type) tuples
    ```

4. What are the different ways to create a DataFrame in PySpark?

    **1. From Python List/Tuple:**
    ```python
    data = [("Alice", 25), ("Bob", 30)]
    df = spark.createDataFrame(data, ["name", "age"])
    ```

    **2. From RDD:**
    ```python
    rdd = sc.parallelize([(1, "a"), (2, "b")])
    df = spark.createDataFrame(rdd, ["id", "value"])
    ```

    **3. From Pandas DataFrame:**
    ```python
    import pandas as pd
    pdf = pd.DataFrame({"a": [1, 2], "b": [3, 4]})
    df = spark.createDataFrame(pdf)
    ```

    **4. From External Files:**
    ```python
    df = spark.read.csv("file.csv")
    df = spark.read.json("file.json")
    df = spark.read.parquet("file.parquet")
    ```

    **5. From Database:**
    ```python
    df = spark.read.jdbc(url, table, properties)
    ```

5. How do you select specific columns from a DataFrame?

    ```python
    # Using column names as strings
    df.select("name", "age")
    
    # Using column objects
    from pyspark.sql.functions import col
    df.select(col("name"), col("age"))
    
    # Using DataFrame column notation
    df.select(df.name, df.age)
    df.select(df["name"], df["age"])
    
    # Using list of column names
    columns = ["name", "age", "salary"]
    df.select(*columns)
    
    # With expressions
    df.select(col("age") + 1, col("name"))
    ```

6. Explain the difference between select() and selectExpr()

    **select()** uses column objects/names, while **selectExpr()** accepts SQL expressions as strings.

    **select():**
    ```python
    from pyspark.sql.functions import col
    df.select(col("age") + 1, col("name").alias("full_name"))
    ```

    **selectExpr():**
    ```python
    # SQL-like string expressions
    df.selectExpr("age + 1 as new_age", "name as full_name", "salary * 2")
    
    # Easier for SQL users
    df.selectExpr("*", "age * 2 as double_age")
    ```

    **Use selectExpr() when:** You prefer SQL syntax or have complex SQL expressions
    **Use select() when:** Using Python/PySpark functions and better IDE support needed

7. How do you rename columns in a PySpark DataFrame?

    ```python
    # Single column - withColumnRenamed()
    df = df.withColumnRenamed("old_name", "new_name")
    
    # Multiple columns - alias() in select
    df = df.select(col("name").alias("full_name"), 
                   col("age").alias("years"))
    
    # Multiple columns - toDF()
    df = df.toDF("new_col1", "new_col2", "new_col3")
    
    # Using selectExpr
    df = df.selectExpr("name as full_name", "age as years")
    
    # Multiple renames with loop
    for old, new in [("col1", "new1"), ("col2", "new2")]:
        df = df.withColumnRenamed(old, new)
    ```

8. How do you convert a PySpark DataFrame to a Pandas DataFrame?

    ```python
    # Convert entire DataFrame
    pandas_df = spark_df.toPandas()
    
    # With Arrow optimization (faster)
    spark.conf.set("spark.sql.execution.arrow.pyspark.enabled", "true")
    pandas_df = spark_df.toPandas()
    
    # Sample before converting (for large DataFrames)
    pandas_df = spark_df.limit(1000).toPandas()
    ```

    **Important:** Only use for small datasets that fit in driver memory.

9. What are the performance implications of converting to Pandas?

    **Performance Issues:**

    **1. Memory Constraints:**
    - All data collected to single driver node
    - Must fit in driver memory
    - Risk of OutOfMemory errors

    **2. Loss of Distributed Processing:**
    - No parallelism on Pandas DataFrame
    - Single-machine computation only

    **3. Network Overhead:**
    - Data transfer from all executors to driver
    - Serialization/deserialization cost

    **4. No Lazy Evaluation:**
    - Immediate execution triggered
    - Cannot optimize further operations

    **Best Practices:**
    - Use only for small datasets or samples
    - Enable Arrow for faster conversion
    - Consider Pandas UDFs instead for distributed operations
    - Filter/aggregate before converting

10. How do you handle schema inference in DataFrames?

    **Automatic Schema Inference:**
    ```python
    # Infer schema from data (scans data)
    df = spark.read.csv("file.csv", inferSchema=True, header=True)
    ```

    **Explicit Schema Definition (Recommended for Production):**
    ```python
    from pyspark.sql.types import StructType, StructField, StringType, IntegerType
    
    schema = StructType([
        StructField("name", StringType(), True),
        StructField("age", IntegerType(), True),
        StructField("salary", IntegerType(), True)
    ])
    
    df = spark.read.csv("file.csv", schema=schema, header=True)
    ```

    **Benefits of Explicit Schema:**
    - Faster (no data scanning needed)
    - Predictable data types
    - Better error handling
    - Production-ready

    **When to Infer:**
    - Exploratory analysis
    - Unknown/changing schema
    - Development phase

## Transformations

1. Explain the difference between narrow and wide transformations

    **Narrow Transformations:** Each input partition contributes to at most one output partition (no shuffling).

    **Wide Transformations:** Each input partition contributes to multiple output partitions (requires shuffling).

    **Narrow Transformations:**
    - **Characteristics:** No data movement across partitions, pipeline-able, fast
    - **Examples:** `map()`, `filter()`, `select()`, `withColumn()`, `union()`
    - **Execution:** Processed in-memory within same stage
    
    **Wide Transformations:**
    - **Characteristics:** Data shuffled across network, expensive, creates stage boundaries
    - **Examples:** `groupBy()`, `join()`, `orderBy()`, `distinct()`, `repartition()`
    - **Execution:** Requires shuffle (read/write to disk, network transfer)

    **Performance Impact:** Narrow transformations are much faster; minimize wide transformations when possible.

2. What are examples of narrow transformations?

    **Common Narrow Transformations:**
    - `map()` - Apply function to each element
    - `filter()` / `where()` - Filter rows based on condition
    - `select()` - Select specific columns
    - `withColumn()` - Add/modify column
    - `drop()` - Remove columns
    - `union()` - Combine DataFrames vertically
    - `sample()` - Random sampling
    - `mapPartitions()` - Apply function to each partition
    - `coalesce()` - Reduce partitions (only decrease)

    All operate independently on each partition without cross-partition communication.

3. What are examples of wide transformations?

    **Common Wide Transformations:**
    - `groupBy()` - Group data by key (shuffle)
    - `join()` / `crossJoin()` - Join DataFrames (shuffle)
    - `orderBy()` / `sort()` - Sort data (shuffle)
    - `distinct()` - Remove duplicates (shuffle)
    - `repartition()` - Increase/decrease partitions (shuffle)
    - `reduceByKey()` - Aggregate by key (shuffle)
    - `intersection()` - Find common rows (shuffle)
    - `subtract()` - Set difference (shuffle)

    All require data shuffling across network, creating stage boundaries.

4. How do you filter rows in a DataFrame?

    ```python
    from pyspark.sql.functions import col
    
    # Using filter()
    df.filter(col("age") > 25)
    df.filter("age > 25")  # SQL expression
    
    # Using where() (same as filter)
    df.where(col("age") > 25)
    
    # Multiple conditions
    df.filter((col("age") > 25) & (col("salary") > 50000))
    df.filter((col("age") > 25) | (col("city") == "NYC"))
    
    # Using SQL expression
    df.filter("age > 25 AND salary > 50000")
    
    # Negation
    df.filter(~(col("age") > 25))
    ```

5. How do you perform map and flatMap operations?

    **On RDDs:**
    ```python
    # map - one-to-one transformation
    rdd = sc.parallelize([1, 2, 3])
    rdd.map(lambda x: x * 2).collect()  # [2, 4, 6]
    
    # flatMap - one-to-many transformation, flattens result
    rdd = sc.parallelize(["hello world", "spark is great"])
    rdd.flatMap(lambda x: x.split()).collect()  
    # ['hello', 'world', 'spark', 'is', 'great']
    ```

    **On DataFrames (less common):**
    ```python
    # Use withColumn or select instead
    df.withColumn("doubled", col("value") * 2)
    ```

    **Key Difference:** `map` returns same number of elements; `flatMap` can return any number and flattens the results.

6. What is the difference between map() and mapPartitions()?

    **map():** Applies function to each element individually
    **mapPartitions():** Applies function to entire partition at once (more efficient)

    ```python
    # map - per element
    rdd.map(lambda x: x * 2)
    
    # mapPartitions - per partition (iterator)
    def process_partition(iterator):
        # Setup (e.g., DB connection) done once per partition
        result = []
        for x in iterator:
            result.append(x * 2)
        return iter(result)
    
    rdd.mapPartitions(process_partition)
    ```

    **Use mapPartitions() when:**
    - Need to initialize resources (DB connections, ML models)
    - Batch processing is more efficient
    - Reduce overhead of function calls

7. How do you use withColumn() to create derived columns?

    ```python
    from pyspark.sql.functions import col, when, lit
    
    # Add new column
    df = df.withColumn("age_plus_10", col("age") + 10)
    
    # Modify existing column
    df = df.withColumn("age", col("age") * 2)
    
    # Conditional logic
    df = df.withColumn("age_group", 
        when(col("age") < 18, "minor")
        .when(col("age") < 65, "adult")
        .otherwise("senior"))
    
    # Add constant column
    df = df.withColumn("country", lit("USA"))
    
    # Multiple columns (chain calls)
    df = df.withColumn("col1", col("a") + 1) \
           .withColumn("col2", col("b") * 2)
    ```

8. Explain the union() and unionAll() operations

    Both combine two DataFrames vertically (append rows).

    **union():**
    - Combines DataFrames by position (not by column name)
    - Does NOT remove duplicates
    - Requires same number of columns
    - Column names can differ (uses position)

    **unionAll():**
    - Deprecated since Spark 2.0
    - Now alias for `union()`
    - Behaves identically to `union()`

    ```python
    df1 = spark.createDataFrame([(1, "a"), (2, "b")])
    df2 = spark.createDataFrame([(3, "c"), (4, "d")])
    
    # Union (no deduplication)
    result = df1.union(df2)  # 4 rows
    
    # To remove duplicates
    result = df1.union(df2).distinct()
    ```

    **Note:** Use `unionByName()` to union by column names instead of position.

9. How do you remove duplicate rows from a DataFrame?

    ```python
    # Remove all duplicate rows
    df_unique = df.distinct()
    
    # Remove duplicates based on specific columns
    df_unique = df.dropDuplicates(["name", "age"])
    
    # Alternative syntax
    df_unique = df.drop_duplicates(["name", "age"])
    
    # Remove duplicates on all columns
    df_unique = df.dropDuplicates()
    ```

    Both trigger a wide transformation (shuffle operation).

10. What is the difference between distinct() and dropDuplicates()?

    **distinct():**
    - Removes duplicates based on ALL columns
    - No parameters
    - Returns rows where entire row is unique

    **dropDuplicates():**
    - Can specify subset of columns for comparison
    - More flexible
    - Can remove duplicates based on specific columns only

    ```python
    # distinct - considers all columns
    df.distinct()
    
    # dropDuplicates - all columns (same as distinct)
    df.dropDuplicates()
    
    # dropDuplicates - specific columns
    df.dropDuplicates(["id", "email"])  # Keep first occurrence
    ```

    **Use distinct()** when deduplicating entire rows; **use dropDuplicates()** when deduplicating based on specific columns.

## Aggregations and Grouping

1. How do you perform group by operations in PySpark?

    ```python
    from pyspark.sql.functions import sum, avg, count, max, min
    
    # Basic groupBy with single aggregation
    df.groupBy("department").count()
    
    # Multiple aggregations using agg()
    df.groupBy("department").agg(
        sum("salary").alias("total_salary"),
        avg("salary").alias("avg_salary"),
        count("*").alias("employee_count")
    )
    
    # Group by multiple columns
    df.groupBy("department", "city").agg(avg("salary"))
    
    # Using dictionary syntax
    df.groupBy("department").agg({"salary": "sum", "age": "avg"})
    ```

2. What aggregation functions are available in pyspark.sql.functions?

    **Common Aggregation Functions:**
    - `count()` - Count rows
    - `sum()` - Sum values
    - `avg()` / `mean()` - Average
    - `min()` - Minimum value
    - `max()` - Maximum value
    - `first()` - First value
    - `last()` - Last value
    - `collect_list()` - Collect values into list (with duplicates)
    - `collect_set()` - Collect unique values into set
    - `countDistinct()` - Count unique values
    - `approx_count_distinct()` - Approximate distinct count (faster)
    - `stddev()` / `variance()` - Standard deviation/variance
    - `percentile_approx()` - Approximate percentile

3. How do you implement custom aggregations in PySpark?

    **Using Pandas UDF (Recommended):**
    ```python
    from pyspark.sql.functions import pandas_udf
    import pandas as pd
    
    @pandas_udf("double")
    def custom_mean(values: pd.Series) -> float:
        return values.mean()
    
    df.groupBy("category").agg(custom_mean(col("value")))
    ```

    **Using User-Defined Aggregate Function (UDAF):**
    Requires extending `UserDefinedAggregateFunction` (more complex, Java/Scala preferred).

    **Alternative - Use built-in functions creatively:**
    Combine existing functions to achieve custom aggregation logic when possible for better performance.

4. Explain the use of agg() with multiple aggregation functions

    `agg()` allows applying multiple aggregation functions in a single operation.

    ```python
    from pyspark.sql.functions import sum, avg, max, min, count
    
    # Multiple functions on different columns
    df.groupBy("department").agg(
        sum("salary").alias("total_salary"),
        avg("age").alias("average_age"),
        count("employee_id").alias("emp_count"),
        max("hire_date").alias("latest_hire")
    )
    
    # Multiple functions on same column
    df.groupBy("category").agg(
        min("price").alias("min_price"),
        max("price").alias("max_price"),
        avg("price").alias("avg_price")
    )
    
    # Dictionary syntax
    df.groupBy("dept").agg({
        "salary": "sum",
        "age": "avg",
        "id": "count"
    })
    ```

5. How do you perform window functions in PySpark?

    Window functions perform calculations across rows related to the current row.

    ```python
    from pyspark.sql.window import Window
    from pyspark.sql.functions import row_number, rank, dense_rank, sum, avg
    
    # Define window specification
    window_spec = Window.partitionBy("department").orderBy("salary")
    
    # Apply window functions
    df = df.withColumn("row_num", row_number().over(window_spec))
    df = df.withColumn("rank", rank().over(window_spec))
    df = df.withColumn("running_total", sum("salary").over(window_spec))
    
    # With range
    window_range = Window.partitionBy("dept").orderBy("date") \
                         .rowsBetween(-2, 0)  # Current + 2 preceding
    df = df.withColumn("moving_avg", avg("value").over(window_range))
    ```

6. What is the difference between groupBy() and window functions?

    **groupBy():**
    - Reduces rows (aggregates to one row per group)
    - Loses individual row details
    - Result has fewer rows than input

    **Window Functions:**
    - Keeps all rows (no reduction)
    - Adds calculated column based on window
    - Result has same number of rows as input
    - Can access neighboring rows

    ```python
    # groupBy - reduces to 3 rows (one per department)
    df.groupBy("department").agg(avg("salary"))
    
    # Window - keeps all rows, adds average column
    window = Window.partitionBy("department")
    df.withColumn("dept_avg_salary", avg("salary").over(window))
    ```

    **Use groupBy** for aggregated summaries; **use windows** when you need row-level details with aggregate context.

7. How do you calculate running totals using window functions?

    ```python
    from pyspark.sql.window import Window
    from pyspark.sql.functions import sum, col
    
    # Define window from start to current row
    window_spec = Window.partitionBy("customer_id") \
                        .orderBy("date") \
                        .rowsBetween(Window.unboundedPreceding, Window.currentRow)
    
    # Calculate running total
    df = df.withColumn("running_total", sum("amount").over(window_spec))
    
    # Alternative: simplified syntax
    window_spec = Window.partitionBy("customer_id").orderBy("date")
    df = df.withColumn("cumulative_sum", sum("amount").over(window_spec))
    ```

    The window includes all rows from the start of the partition up to the current row.

8. How do you rank data within groups?

    ```python
    from pyspark.sql.window import Window
    from pyspark.sql.functions import row_number, rank, dense_rank
    
    window_spec = Window.partitionBy("department").orderBy(col("salary").desc())
    
    df = df.withColumn("row_number", row_number().over(window_spec)) \
           .withColumn("rank", rank().over(window_spec)) \
           .withColumn("dense_rank", dense_rank().over(window_spec))
    ```

    **Differences:**
    - `row_number()`: 1, 2, 3, 4 (unique sequential)
    - `rank()`: 1, 2, 2, 4 (skips after ties)
    - `dense_rank()`: 1, 2, 2, 3 (no gaps after ties)

9. What is pivot and unpivot in PySpark?

    **Pivot:** Converts rows to columns (wide format)
    ```python
    # Before: year, product, sales
    # After: year, product1_sales, product2_sales, ...
    
    df.groupBy("year").pivot("product").sum("sales")
    
    # With specific values (better performance)
    df.groupBy("year").pivot("product", ["A", "B", "C"]).sum("sales")
    ```

    **Unpivot:** Converts columns to rows (long format)
    ```python
    # Using stack() in selectExpr
    df.selectExpr("id", "stack(3, 'Q1', Q1, 'Q2', Q2, 'Q3', Q3) as (quarter, sales)")
    
    # Using melt pattern
    from pyspark.sql.functions import expr
    df.select("id", expr("stack(2, 'col1', col1, 'col2', col2) as (name, value)"))
    ```

10. How do you handle null values during aggregations?

    **Most aggregate functions automatically ignore nulls:**
    ```python
    # These ignore null values
    df.groupBy("category").agg(
        sum("sales"),      # Nulls ignored
        avg("price"),      # Nulls ignored
        count("id")        # Counts non-null values
    )
    
    # Count including nulls
    df.groupBy("category").agg(count("*"))  # Counts all rows
    
    # Handle nulls explicitly
    from pyspark.sql.functions import coalesce, lit
    df.groupBy("category").agg(
        sum(coalesce(col("sales"), lit(0)))  # Replace null with 0
    )
    
    # Filter nulls before aggregation
    df.filter(col("sales").isNotNull()).groupBy("category").agg(sum("sales"))
    ```

    **Note:** `count(column)` counts non-null values; `count("*")` or `count(lit(1))` counts all rows.

## Joins and Data Manipulation

1. What are the different types of joins available in PySpark?

    **Join Types:**
    - **inner** (default): Returns matching rows from both DataFrames
    - **left** / **left_outer**: All rows from left + matching from right
    - **right** / **right_outer**: All rows from right + matching from left
    - **outer** / **full** / **full_outer**: All rows from both (union)
    - **left_semi**: Rows from left that have match in right (no right columns)
    - **left_anti**: Rows from left that DON'T have match in right
    - **cross**: Cartesian product (all combinations)

2. How do you perform an inner join between two DataFrames?

    ```python
    # Simple join on common column name
    result = df1.join(df2, "id", "inner")
    
    # Join on different column names
    result = df1.join(df2, df1.id == df2.customer_id, "inner")
    
    # Join on multiple columns
    result = df1.join(df2, ["id", "date"], "inner")
    
    # Join with explicit condition
    result = df1.join(df2, (df1.id == df2.id) & (df1.year == df2.year), "inner")
    
    # Default is inner, so can omit
    result = df1.join(df2, "id")  # inner join
    ```

3. Explain left outer join, right outer join, and full outer join

    **Left Outer Join:**
    - All rows from left DataFrame
    - Matching rows from right (nulls if no match)
    ```python
    df1.join(df2, "id", "left")  # or "left_outer"
    ```

    **Right Outer Join:**
    - All rows from right DataFrame
    - Matching rows from left (nulls if no match)
    ```python
    df1.join(df2, "id", "right")  # or "right_outer"
    ```

    **Full Outer Join:**
    - All rows from both DataFrames
    - Nulls where no match on either side
    ```python
    df1.join(df2, "id", "outer")  # or "full", "full_outer"
    ```

4. What is a cross join and when should it be avoided?

    **Cross Join** produces Cartesian product (every row from left combined with every row from right).

    ```python
    # Explicit cross join
    df1.crossJoin(df2)
    
    # Implicit cross join (no condition)
    df1.join(df2)  # Creates cross join if no condition
    ```

    **Result Size:** If df1 has M rows and df2 has N rows, result has M × N rows.

    **When to Avoid:**
    - Large DataFrames (explodes data size)
    - Unintentional (missing join condition)
    - Performance concerns (very expensive)

    **Valid Use Cases:**
    - Small dimension tables
    - Generating all combinations intentionally
    - Creating test data

5. What is a broadcast join and when should you use it?

    **Broadcast Join:** Small DataFrame copied to all executor nodes, avoiding shuffle of large DataFrame.

    ```python
    from pyspark.sql.functions import broadcast
    
    # Broadcast small DataFrame
    result = large_df.join(broadcast(small_df), "id")
    ```

    **How It Works:**
    - Small DataFrame sent to all executors
    - Large DataFrame stays partitioned
    - No shuffle needed for large DataFrame

    **When to Use:**
    - One DataFrame is small (< 10MB default, configurable)
    - Large performance gain by avoiding shuffle
    - One-to-many or many-to-one joins

    **Configuration:**
    ```python
    spark.conf.set("spark.sql.autoBroadcastJoinThreshold", 10485760)  # 10MB
    ```

6. How do you optimize joins for large datasets?

    **Optimization Strategies:**

    **1. Use Broadcast Joins** for small tables
    **2. Partition Data** on join keys before joining
    **3. Filter Early** - reduce data before join
    **4. Bucketing** - pre-partition data with same hash
    ```python
    df.write.bucketBy(100, "id").saveAsTable("table")
    ```
    **5. Salting** - add random prefix to handle skew
    **6. Use Appropriate Join Type** - avoid full outer when not needed
    **7. Cache** frequently used DataFrames
    **8. Repartition** to balance partitions
    **9. Column Pruning** - select only needed columns before join
    **10. Enable AQE** (Adaptive Query Execution)
    ```python
    spark.conf.set("spark.sql.adaptive.enabled", "true")
    ```

7. What is data skew and how does it affect joins?

    **Data Skew:** Uneven distribution of data across partitions (some keys have many more records).

    **Effects on Joins:**
    - Some tasks process much more data than others
    - Long-running stragglers delay entire job
    - Memory issues on heavily loaded executors
    - Underutilization of cluster resources

    **Solutions:**

    **1. Salting (for skewed keys):**
    ```python
    # Add random salt to skewed keys
    df1 = df1.withColumn("salt", (rand() * 10).cast("int"))
    df1 = df1.withColumn("salted_key", concat(col("key"), lit("_"), col("salt")))
    ```

    **2. Broadcast smaller table** if applicable
    **3. Increase partitions** for better distribution
    **4. Adaptive Query Execution** handles some skew automatically
    **5. Separate skewed keys** and process differently

8. How do you handle null values in joins?

    **Key Point:** Nulls in join keys DO NOT match (null != null in SQL).

    **Handling Strategies:**

    **1. Filter nulls before join:**
    ```python
    df1.filter(col("id").isNotNull()).join(df2, "id")
    ```

    **2. Replace nulls with placeholder:**
    ```python
    df1 = df1.fillna({"id": -1})
    df2 = df2.fillna({"id": -1})
    result = df1.join(df2, "id")
    ```

    **3. Use outer join** to keep null rows:
    ```python
    df1.join(df2, "id", "left")  # Keeps df1 rows with null id
    ```

    **4. Coalesce join condition:**
    ```python
    df1.join(df2, coalesce(df1.id, lit(-1)) == coalesce(df2.id, lit(-1)))
    ```

9. What is the difference between join() and union()?

    **join():** Combines DataFrames horizontally (adds columns)
    **union():** Combines DataFrames vertically (adds rows)

    **join():**
    - Matches rows based on condition
    - Increases number of columns
    - Can increase/decrease/maintain row count
    - Requires join key/condition

    **union():**
    - Appends rows from second DataFrame
    - Same number of columns required
    - Increases row count (sum of both)
    - No matching logic, just stacks rows

    ```python
    # Join - horizontal combination
    df1.join(df2, "id")  # Adds df2 columns to df1
    
    # Union - vertical combination
    df1.union(df2)  # Adds df2 rows below df1
    ```

10. How do you perform self-joins in PySpark?

    **Self-Join:** Join DataFrame with itself (use aliases to distinguish).

    ```python
    # Create aliases
    df1_alias = df.alias("df1")
    df2_alias = df.alias("df2")
    
    # Self-join example: find employees and their managers
    result = df1_alias.join(
        df2_alias,
        col("df1.manager_id") == col("df2.employee_id"),
        "left"
    ).select(
        col("df1.employee_id"),
        col("df1.name").alias("employee_name"),
        col("df2.name").alias("manager_name")
    )
    
    # Another example: find pairs of employees in same department
    result = df1_alias.join(
        df2_alias,
        (col("df1.dept") == col("df2.dept")) & 
        (col("df1.id") < col("df2.id"))  # Avoid duplicates
    )
    ```

    **Key:** Use aliases to reference columns from different instances of the same DataFrame.

## Data Handling and Quality

1. How do you handle missing or null values in PySpark?

    **Detection:**
    ```python
    # Check for nulls
    df.filter(col("column").isNull())
    df.where(col("column").isNotNull())
    ```

    **Handling Methods:**

    **1. Drop rows with nulls:**
    ```python
    df.na.drop()  # Drop if any column has null
    df.na.drop(subset=["col1", "col2"])  # Drop if specific columns null
    df.dropna(how="all")  # Drop only if all values are null
    ```

    **2. Fill nulls:**
    ```python
    df.na.fill(0)  # Fill all numeric columns with 0
    df.na.fill({"age": 0, "name": "Unknown"})  # Column-specific
    df.fillna({"salary": df.agg(avg("salary")).collect()[0][0]})  # Fill with average
    ```

    **3. Replace values:**
    ```python
    df.na.replace(["NA", "null"], None)
    ```

2. What is the difference between na.drop() and na.fill()?

    **na.drop():** Removes rows containing null values
    **na.fill():** Replaces null values with specified values

    **na.drop():**
    - Reduces row count
    - Data loss (removes entire rows)
    - Use when nulls indicate invalid data
    ```python
    df.na.drop(how="any")  # Drop if ANY column is null
    df.na.drop(how="all")  # Drop if ALL columns are null
    ```

    **na.fill():**
    - Maintains row count
    - Imputes missing values
    - Use when nulls can be reasonably replaced
    ```python
    df.na.fill(0)  # Replace nulls with 0
    df.na.fill({"age": 30, "city": "Unknown"})
    ```

    **Choice depends on:** Data importance, analysis requirements, and business logic.

3. How do you replace specific values in a DataFrame?

    ```python
    from pyspark.sql.functions import when, col
    
    # Using na.replace()
    df.na.replace(["male", "female"], ["M", "F"], subset=["gender"])
    df.na.replace([999, -999], None)  # Replace with null
    
    # Using withColumn and when
    df = df.withColumn("status",
        when(col("status") == "A", "Active")
        .when(col("status") == "I", "Inactive")
        .otherwise(col("status"))
    )
    
    # Using regexp_replace for string patterns
    from pyspark.sql.functions import regexp_replace
    df = df.withColumn("phone", regexp_replace("phone", "-", ""))
    ```

4. How do you perform data type conversions (casting)?

    ```python
    from pyspark.sql.types import IntegerType, StringType, DoubleType, DateType
    from pyspark.sql.functions import col, to_date, to_timestamp
    
    # Using cast()
    df = df.withColumn("age", col("age").cast(IntegerType()))
    df = df.withColumn("age", col("age").cast("int"))  # String type name
    
    # Multiple columns
    df = df.withColumn("price", col("price").cast(DoubleType())) \
           .withColumn("quantity", col("quantity").cast(IntegerType()))
    
    # Date conversions
    df = df.withColumn("date", to_date(col("date_str"), "yyyy-MM-dd"))
    df = df.withColumn("timestamp", to_timestamp(col("ts_str"), "yyyy-MM-dd HH:mm:ss"))
    
    # String to integer (with error handling)
    df = df.withColumn("safe_int", col("str_col").cast("int"))  # Returns null if fails
    ```

5. How do you validate data quality in PySpark?

    **Common Validation Checks:**

    ```python
    # 1. Check for nulls
    null_counts = df.select([count(when(col(c).isNull(), c)).alias(c) for c in df.columns])
    
    # 2. Check for duplicates
    duplicate_count = df.count() - df.dropDuplicates().count()
    
    # 3. Value range validation
    invalid_age = df.filter((col("age") < 0) | (col("age") > 120)).count()
    
    # 4. Data type validation
    df.printSchema()  # Verify schema
    
    # 5. Unique value counts
    df.groupBy("status").count()
    
    # 6. Statistical summary
    df.describe().show()
    
    # 7. Custom business rules
    invalid_data = df.filter(col("end_date") < col("start_date"))
    ```

6. How do you detect and handle outliers?

    **Detection Methods:**

    **1. IQR (Interquartile Range) Method:**
    ```python
    # Calculate quartiles
    quantiles = df.approxQuantile("value", [0.25, 0.75], 0.01)
    Q1, Q3 = quantiles[0], quantiles[1]
    IQR = Q3 - Q1
    
    # Define bounds
    lower_bound = Q1 - 1.5 * IQR
    upper_bound = Q3 + 1.5 * IQR
    
    # Filter outliers
    df_no_outliers = df.filter((col("value") >= lower_bound) & (col("value") <= upper_bound))
    ```

    **2. Standard Deviation Method:**
    ```python
    stats = df.select(mean("value"), stddev("value")).first()
    mean_val, std_val = stats[0], stats[1]
    
    df_no_outliers = df.filter(
        (col("value") >= mean_val - 3 * std_val) &
        (col("value") <= mean_val + 3 * std_val)
    )
    ```

    **3. Cap/Floor outliers instead of removing:**
    ```python
    df = df.withColumn("value_capped", 
        when(col("value") > upper_bound, upper_bound)
        .when(col("value") < lower_bound, lower_bound)
        .otherwise(col("value")))
    ```

7. What strategies exist for handling duplicate data?

    **1. Remove all duplicates:**
    ```python
    df.distinct()  # All columns
    df.dropDuplicates(["id", "email"])  # Specific columns
    ```

    **2. Keep first/last occurrence:**
    ```python
    window = Window.partitionBy("id").orderBy(col("timestamp").desc())
    df_dedup = df.withColumn("row_num", row_number().over(window)) \
                 .filter(col("row_num") == 1) \
                 .drop("row_num")
    ```

    **3. Aggregate duplicates:**
    ```python
    # Sum values for duplicates
    df.groupBy("id").agg(sum("amount"))
    ```

    **4. Flag duplicates without removing:**
    ```python
    df = df.withColumn("is_duplicate", 
        count("*").over(Window.partitionBy("id")) > 1)
    ```

    **5. Identify and analyze duplicates before removal:**
    ```python
    duplicates = df.groupBy("id").count().filter(col("count") > 1)
    ```

8. How do you perform data sampling in PySpark?

    ```python
    # Random sampling (with replacement)
    sample_with_replacement = df.sample(withReplacement=True, fraction=0.1, seed=42)
    
    # Random sampling (without replacement)
    sample_without_replacement = df.sample(withReplacement=False, fraction=0.1, seed=42)
    
    # Take first N rows
    sample_n = df.limit(1000)
    
    # Stratified sampling
    fractions = {"A": 0.1, "B": 0.2, "C": 0.15}
    stratified_sample = df.sampleBy("category", fractions, seed=42)
    
    # Random split for train/test
    train, test = df.randomSplit([0.8, 0.2], seed=42)
    ```

    **fraction:** Approximate proportion of data to sample (0.0 to 1.0)
    **seed:** For reproducibility

9. How do you split data into training and test sets?

    ```python
    # Simple 80/20 split
    train_df, test_df = df.randomSplit([0.8, 0.2], seed=42)
    
    # 70/15/15 split (train/validation/test)
    train, val, test = df.randomSplit([0.7, 0.15, 0.15], seed=42)
    
    # Stratified split (maintain class distribution)
    # Manual approach using sampleBy
    fractions_train = {"class1": 0.8, "class2": 0.8}
    fractions_test = {"class1": 0.2, "class2": 0.2}
    train = df.sampleBy("label", fractions_train, seed=42)
    test = df.subtract(train)
    ```

    **Important:** Use `seed` parameter for reproducibility.

10. What are the best practices for data validation checks?

    **Best Practices:**

    **1. Schema Validation:**
    - Enforce schema on read
    - Use explicit schema definitions
    - Validate data types match expectations

    **2. Completeness Checks:**
    - Check for null values in critical columns
    - Validate required fields are populated

    **3. Uniqueness Checks:**
    - Verify primary keys are unique
    - Check for unexpected duplicates

    **4. Range/Constraint Checks:**
    - Validate numeric values within expected ranges
    - Check date ranges are logical
    - Verify categorical values are in allowed set

    **5. Referential Integrity:**
    - Validate foreign keys exist in reference tables
    - Check join keys match between datasets

    **6. Statistical Profiling:**
    - Monitor data distribution changes
    - Check for anomalies in aggregates

    **7. Business Rule Validation:**
    - Implement domain-specific checks
    - Validate calculated fields

    **8. Logging and Monitoring:**
    - Log validation failures
    - Track data quality metrics over time
    - Alert on threshold breaches

    **9. Early Validation:**
    - Validate at ingestion time
    - Fail fast on critical violations

## User-Defined Functions (UDFs)

1. What is a UDF in PySpark and when should you use it?

    **UDF (User-Defined Function):** Custom Python function registered to use in Spark transformations.

    **When to Use:**
    - Complex custom logic not available in built-in functions
    - Business-specific transformations
    - String manipulation beyond built-in functions
    - Custom calculations unique to your domain

    **When NOT to Use:**
    - Built-in function exists (always prefer built-in)
    - Simple arithmetic or logic (use Column expressions)
    - Performance-critical operations (UDFs are slower)

    **Performance Impact:** UDFs serialize data between JVM and Python, causing overhead. Avoid when possible.

2. How do you create and register a UDF?

    ```python
    from pyspark.sql.functions import udf
    from pyspark.sql.types import StringType, IntegerType
    
    # Method 1: Decorator
    @udf(returnType=StringType())
    def upper_case(text):
        return text.upper() if text else None
    
    df = df.withColumn("upper_name", upper_case(col("name")))
    
    # Method 2: Explicit registration
    def multiply_by_two(x):
        return x * 2
    
    multiply_udf = udf(multiply_by_two, IntegerType())
    df = df.withColumn("doubled", multiply_udf(col("value")))
    
    # Method 3: Register for SQL use
    spark.udf.register("multiply_two", multiply_by_two, IntegerType())
    spark.sql("SELECT multiply_two(value) FROM table")
    ```

3. What are the performance implications of using UDFs?

    **Performance Issues:**

    **1. Serialization Overhead:**
    - Data serialized from JVM to Python for each row
    - Results serialized back to JVM
    - Significant overhead for large datasets

    **2. No Catalyst Optimization:**
    - Spark cannot optimize UDF logic
    - Treated as black box
    - Loses query plan optimizations

    **3. Row-by-Row Processing:**
    - Standard UDFs process one row at a time
    - Cannot leverage vectorization
    - Slower than batch operations

    **4. Python GIL:**
    - Global Interpreter Lock limits parallelism
    - Single-threaded Python execution per executor

    **Mitigation:**
    - Use Pandas UDFs (vectorized) instead
    - Prefer built-in functions
    - Minimize UDF usage

4. What is a Pandas UDF and how does it differ from regular UDFs?

    **Pandas UDF (Vectorized UDF):** Operates on batches of data as Pandas Series/DataFrames, much faster than row-by-row UDFs.

    **Key Differences:**

    | Aspect | Regular UDF | Pandas UDF |
    |--------|-------------|------------|
    | **Processing** | Row-by-row | Batch (vectorized) |
    | **Input Type** | Single values | Pandas Series/DataFrame |
    | **Performance** | Slow | 10-100x faster |
    | **Serialization** | Per row | Per batch (Arrow) |

    ```python
    from pyspark.sql.functions import pandas_udf
    import pandas as pd
    
    # Pandas UDF (vectorized)
    @pandas_udf("int")
    def multiply_pandas(series: pd.Series) -> pd.Series:
        return series * 2
    
    # Regular UDF (row-by-row)
    @udf("int")
    def multiply_regular(value: int) -> int:
        return value * 2
    ```

    **Pandas UDFs use Apache Arrow** for efficient data transfer, significantly reducing serialization overhead.

5. How do you use applyInPandas() for custom aggregations?

    **applyInPandas():** Applies a function to each group as a Pandas DataFrame, useful for complex grouped operations.

    ```python
    from pyspark.sql.types import StructType, StructField, StringType, DoubleType
    
    # Define schema for output
    schema = StructType([
        StructField("category", StringType()),
        StructField("mean_value", DoubleType()),
        StructField("std_value", DoubleType())
    ])
    
    # Custom aggregation function
    def custom_agg(pdf):
        return pd.DataFrame({
            "category": [pdf["category"].iloc[0]],
            "mean_value": [pdf["value"].mean()],
            "std_value": [pdf["value"].std()]
        })
    
    # Apply to groups
    result = df.groupBy("category").applyInPandas(custom_agg, schema)
    ```

    **Use when:** Complex aggregations requiring Pandas functionality (rolling windows, custom stats).

6. When should you use built-in functions instead of UDFs?

    **Always prefer built-in functions** - they are optimized and execute in JVM.

    **Built-in Function Advantages:**
    - 10-100x faster (no serialization)
    - Catalyst optimizer can optimize
    - Execute in JVM (no Python overhead)
    - Pushed down to data sources when possible

    **Examples of Preferring Built-ins:**
    ```python
    # ❌ BAD: UDF
    @udf("int")
    def add_one(x):
        return x + 1
    df.withColumn("result", add_one(col("value")))
    
    # ✅ GOOD: Built-in
    df.withColumn("result", col("value") + 1)
    
    # ❌ BAD: UDF for string concatenation
    @udf("string")
    def concat_strings(a, b):
        return a + " " + b
    
    # ✅ GOOD: Built-in concat
    from pyspark.sql.functions import concat, lit
    df.withColumn("result", concat(col("a"), lit(" "), col("b")))
    ```

    **Use UDF only when:** No built-in function can achieve the required logic.

7. How do you handle exceptions in UDFs?

    **Exception Handling in UDFs:**

    ```python
    from pyspark.sql.functions import udf
    from pyspark.sql.types import IntegerType
    
    # Method 1: Try-except with default value
    @udf(IntegerType())
    def safe_divide(a, b):
        try:
            return int(a / b)
        except (ZeroDivisionError, TypeError):
            return None  # or 0, or any default
    
    # Method 2: Validate inputs
    @udf("string")
    def safe_upper(text):
        if text is None:
            return None
        try:
            return text.upper()
        except AttributeError:
            return None
    
    # Method 3: Return error indicator
    @udf("struct<result:int, error:string>")
    def divide_with_error(a, b):
        try:
            return {"result": int(a / b), "error": None}
        except Exception as e:
            return {"result": None, "error": str(e)}
    ```

    **Best Practice:** Return None or default value on error, log issues separately for debugging.

8. What are the different types of Pandas UDFs?

    **Pandas UDF Types (Spark 3.0+):**

    **1. Series to Series (Scalar):**
    ```python
    @pandas_udf("int")
    def multiply(s: pd.Series) -> pd.Series:
        return s * 2
    ```

    **2. Iterator of Series to Iterator of Series:**
    ```python
    @pandas_udf("int")
    def multiply_iter(iterator):
        for batch in iterator:
            yield batch * 2
    ```

    **3. Iterator of Multiple Series to Iterator of Series:**
    ```python
    @pandas_udf("int")
    def add_cols(iterator):
        for s1, s2 in iterator:
            yield s1 + s2
    ```

    **4. Series to Scalar (Grouped Aggregate):**
    ```python
    @pandas_udf("double")
    def mean_udf(s: pd.Series) -> float:
        return s.mean()
    ```

    **Iterator variants** allow processing large partitions in batches, reducing memory usage.

9. How do you test and debug UDFs?

    **Testing Strategies:**

    **1. Test as regular Python function first:**
    ```python
    def my_function(x):
        return x * 2
    
    # Test with normal Python
    assert my_function(5) == 10
    
    # Then convert to UDF
    my_udf = udf(my_function, IntegerType())
    ```

    **2. Test on small sample:**
    ```python
    small_df = df.limit(100)
    result = small_df.withColumn("new_col", my_udf(col("col")))
    result.show()
    ```

    **3. Collect and inspect locally:**
    ```python
    results = df.select(my_udf(col("value"))).take(10)
    print(results)
    ```

    **4. Add logging:**
    ```python
    import logging
    logger = logging.getLogger(__name__)
    
    @udf("int")
    def debug_udf(x):
        logger.info(f"Processing: {x}")
        return x * 2
    ```

    **5. Use toPandas() for debugging:**
    ```python
    # Convert small sample to Pandas for inspection
    df.limit(10).toPandas()
    ```

10. What are vectorized UDFs and their benefits?

    **Vectorized UDFs** (Pandas UDFs) operate on batches of data using Apache Arrow, providing massive performance improvements.

    **Benefits:**

    **1. Performance:**
    - 10-100x faster than row-at-a-time UDFs
    - Batch processing reduces overhead
    - Efficient Arrow serialization

    **2. Vectorized Operations:**
    - Leverage NumPy/Pandas optimizations
    - SIMD (Single Instruction Multiple Data) operations
    - Better CPU utilization

    **3. Reduced Serialization:**
    - Arrow format reduces serialization cost
    - Batch transfer instead of per-row

    **4. Memory Efficiency:**
    - Columnar format is memory efficient
    - Better cache utilization

    **Example:**
    ```python
    # Vectorized (fast)
    @pandas_udf("double")
    def vectorized_mean(values: pd.Series) -> float:
        return values.mean()
    
    # vs Row-at-a-time (slow)
    @udf("double")
    def slow_mean(values):
        return sum(values) / len(values)
    ```

    **Recommendation:** Always use Pandas UDFs over regular UDFs when possible.

## Performance Optimization

1. How would you optimize a slow-running PySpark job?

    **Optimization Checklist:**

    **1. Analyze Execution Plan:**
    - Use `explain()` to understand query plan
    - Check Spark UI for bottlenecks
    - Identify shuffle operations

    **2. Optimize Shuffles:**
    - Minimize wide transformations
    - Use broadcast joins for small tables
    - Repartition strategically

    **3. Caching/Persistence:**
    - Cache frequently accessed DataFrames
    - Choose appropriate storage level

    **4. Partition Management:**
    - Optimize partition count (not too many/few)
    - Use partitioning on join/group keys

    **5. Avoid UDFs:**
    - Replace with built-in functions
    - Use Pandas UDFs if UDFs necessary

    **6. Filter Early:**
    - Push down predicates
    - Filter before joins/aggregations

    **7. Column Pruning:**
    - Select only needed columns early

    **8. File Format:**
    - Use Parquet instead of CSV
    - Enable compression

    **9. Configuration Tuning:**
    - Adjust executor memory/cores
    - Enable adaptive query execution

2. What is data partitioning and why is it important?

    **Data Partitioning:** Dividing dataset into smaller chunks (partitions) distributed across cluster nodes.

    **Why Important:**

    **1. Parallelism:**
    - Each partition processed independently
    - More partitions = more parallel tasks
    - Better cluster utilization

    **2. Performance:**
    - Enables distributed processing
    - Reduces data movement
    - Optimizes shuffle operations

    **3. Memory Management:**
    - Prevents OutOfMemory errors
    - Each task processes manageable data size

    **4. Fault Tolerance:**
    - Lost partitions can be recomputed independently
    - Faster recovery

    **Rule of Thumb:**
    - 2-3 partitions per CPU core
    - Partition size: 128MB - 1GB
    - Not too many (overhead) or too few (underutilization)

3. How do you repartition or coalesce a DataFrame?

    ```python
    # repartition() - increase or decrease partitions (full shuffle)
    df_repartitioned = df.repartition(100)
    df_repartitioned = df.repartition("department")  # By column
    df_repartitioned = df.repartition(50, "date")  # Number + column
    
    # coalesce() - decrease partitions only (optimized, no full shuffle)
    df_coalesced = df.coalesce(10)
    
    # Check partition count
    print(df.rdd.getNumPartitions())
    ```

    **repartition():** Full shuffle, can increase/decrease
    **coalesce():** No full shuffle, only decrease (combines partitions)

4. What is the difference between repartition() and coalesce()?

    | Aspect | repartition() | coalesce() |
    |--------|---------------|------------|
    | **Direction** | Increase or decrease | Decrease only |
    | **Shuffle** | Full shuffle | Minimal shuffle |
    | **Performance** | Slower (expensive) | Faster (optimized) |
    | **Balance** | Even distribution | May be unbalanced |
    | **Use Case** | Increase partitions, hash partitioning | Reduce partitions for output |

    **repartition():**
    - Hash-partitions data across all nodes
    - Creates balanced partitions
    - More expensive due to full shuffle

    **coalesce():**
    - Combines existing partitions
    - Avoids full shuffle (moves minimal data)
    - Faster but may create unbalanced partitions

    **When to Use:**
    - **repartition()**: Before joins/aggregations, need even distribution
    - **coalesce()**: Before writing to reduce output files

5. When should you use cache() or persist()?

    **Use When:**
    - DataFrame accessed multiple times
    - Iterative algorithms (ML training)
    - DataFrame is expensive to recompute
    - After expensive operations (joins, aggregations)
    - Interactive analysis on same data

    **Don't Use When:**
    - DataFrame used only once
    - Data is cheap to recompute
    - Limited memory available
    - Dataset too large for memory

    ```python
    # Example: Reused DataFrame
    expensive_df = df.join(large_df).filter(col("x") > 100)
    expensive_df.cache()
    
    # Multiple actions on cached DataFrame
    count = expensive_df.count()
    summary = expensive_df.describe()
    results = expensive_df.filter(col("y") < 50).collect()
    
    # Unpersist when done
    expensive_df.unpersist()
    ```

    **Remember:** Caching consumes memory; use judiciously.

6. What are the different storage levels for persistence?

    **Storage Levels:**

    - **MEMORY_ONLY** (default for cache): Store in memory, recompute if doesn't fit
    - **MEMORY_AND_DISK**: Spill to disk if memory full
    - **MEMORY_ONLY_SER**: Serialized in memory (space-efficient but slower)
    - **MEMORY_AND_DISK_SER**: Serialized, spill to disk
    - **DISK_ONLY**: Store only on disk
    - **OFF_HEAP**: Store in off-heap memory (experimental)
    - **_2 suffix**: Replicate each partition on 2 nodes (e.g., MEMORY_AND_DISK_2)

    ```python
    from pyspark import StorageLevel
    
    df.persist(StorageLevel.MEMORY_AND_DISK)
    df.persist(StorageLevel.MEMORY_ONLY_SER)
    df.cache()  # Equivalent to MEMORY_ONLY
    ```

    **Trade-offs:**
    - Memory-only: Fastest but limited capacity
    - Serialized: More space-efficient but CPU overhead
    - Disk: Slower but handles large data
    - Replication: Fault-tolerant but doubles storage

7. How do you choose the optimal number of partitions?

    **Guidelines:**

    **1. CPU-Based Rule:**
    - 2-4 partitions per CPU core
    - Example: 100 cores → 200-400 partitions

    **2. Size-Based Rule:**
    - Partition size: 128MB - 1GB
    - Formula: `total_data_size / target_partition_size`
    - Example: 100GB / 256MB = 400 partitions

    **3. Too Few Partitions:**
    - Underutilizes cluster
    - Risk of OOM on individual tasks
    - Limited parallelism

    **4. Too Many Partitions:**
    - Task scheduling overhead
    - Small task inefficiency
    - Increased metadata

    **Configuration:**
    ```python
    # For DataFrame operations
    spark.conf.set("spark.sql.shuffle.partitions", "200")  # Default
    
    # For RDD operations
    spark.conf.set("spark.default.parallelism", "100")
    ```

    **Dynamic Adjustment:** Enable AQE for automatic optimization:
    ```python
    spark.conf.set("spark.sql.adaptive.enabled", "true")
    ```

8. What is broadcast variable and when should you use it?

    **Broadcast Variable:** Read-only variable cached on each executor, avoiding repeated network transfer.

    **Use Cases:**
    - Small lookup tables/dictionaries
    - Configuration data needed by all tasks
    - Machine learning models
    - Small reference datasets

    ```python
    # Broadcast a dictionary
    lookup = {"A": 1, "B": 2, "C": 3}
    broadcast_lookup = sc.broadcast(lookup)
    
    # Use in UDF
    @udf("int")
    def map_value(key):
        return broadcast_lookup.value.get(key, 0)
    
    # Broadcast join (DataFrame)
    from pyspark.sql.functions import broadcast
    large_df.join(broadcast(small_df), "id")
    ```

    **Benefits:**
    - Sent once per executor (not per task)
    - Reduces network traffic
    - Faster task execution
    - Efficient for small data reused across tasks

    **Size Limit:** Keep < 10MB for best results.

9. What is an accumulator in PySpark?

    **Accumulator:** Shared variable for aggregating values across tasks (write-only from workers, read from driver).

    **Use Cases:**
    - Counters (errors, processed records)
    - Sum metrics across partitions
    - Debugging and monitoring

    ```python
    # Create accumulator
    error_count = sc.accumulator(0)
    processed_count = sc.accumulator(0)
    
    # Use in transformations
    def process_row(row):
        try:
            # Process logic
            processed_count.add(1)
            return row
        except Exception:
            error_count.add(1)
            return None
    
    result = df.rdd.map(process_row)
    result.count()  # Trigger action
    
    # Read from driver
    print(f"Processed: {processed_count.value}")
    print(f"Errors: {error_count.value}")
    ```

    **Important:** Only reliable values after an action is executed. Transformations may be retried.

10. How do you minimize data shuffling in PySpark?

    **Strategies to Reduce Shuffle:**

    **1. Use Broadcast Joins:** For small tables
    **2. Pre-partition Data:** Partition by join/group keys before operations
    **3. Avoid** `groupByKey()`: Use `reduceByKey()` or `aggregateByKey()`
    **4. Filter Early:** Reduce data before shuffle operations
    **5. Use `coalesce()`:** Instead of repartition when reducing partitions
    **6. Bucketing:** Pre-partition tables with same bucketing strategy
    **7. Minimize Distinct:** Expensive operation, use alternatives if possible
    **8. Combine Operations:** Chain transformations to minimize stages
    **9. Partition Pruning:** Use partitioned tables and filter on partition columns
    **10. Column Pruning:** Select only necessary columns before joins

    **Example:**
    ```python
    # Bad: Multiple shuffles
    df.groupBy("key").count().join(other_df, "key")
    
    # Better: Reduce before join
    df_small = df.filter(col("value") > 100).groupBy("key").count()
    result = df_small.join(broadcast(other_df), "key")
    ```

11. What file formats are best for performance and why?

    **Best to Worst Performance:**

    **1. Parquet (Best):**
    - Columnar format (read only needed columns)
    - Built-in compression
    - Predicate pushdown support
    - Schema stored with data
    - Best for analytical workloads

    **2. ORC:**
    - Similar to Parquet
    - Better for Hive integration
    - Excellent compression

    **3. Avro:**
    - Row-based format
    - Good for write-heavy workloads
    - Schema evolution support

    **4. JSON:**
    - Human-readable
    - Slower than binary formats
    - No built-in compression

    **5. CSV (Worst):**
    - Text-based, no schema
    - No compression
    - Slow parsing
    - Type inference overhead

    **Recommendation:** Use Parquet for data lakes and analytical processing.

12. How does partitioning data on disk improve query performance?

    **Disk Partitioning:** Organizing data into subdirectories based on column values.

    **Benefits:**

    **1. Partition Pruning:**
    - Reads only relevant partitions
    - Skips entire directories
    - Massive I/O reduction

    **2. Faster Queries:**
    - Filters on partition columns very fast
    - No need to scan full dataset

    **3. Parallel Processing:**
    - Each partition processed independently
    - Better task distribution

    ```python
    # Write partitioned data
    df.write.partitionBy("year", "month").parquet("path/")
    
    # Directory structure:
    # path/year=2024/month=01/
    # path/year=2024/month=02/
    
    # Query with partition pruning
    df = spark.read.parquet("path/")
    df.filter((col("year") == 2024) & (col("month") == 1))
    # Only reads path/year=2024/month=01/
    ```

    **Choose partition columns** with low cardinality that are frequently filtered.

13. What is predicate pushdown and how does it help?

    **Predicate Pushdown:** Pushing filter conditions down to the data source level, so filtering happens during data read rather than after.

    **How It Helps:**

    **1. Reduces Data Transfer:**
    - Filters applied at source
    - Only matching data loaded into Spark
    - Less network/disk I/O

    **2. Faster Execution:**
    - Smaller dataset to process
    - Less memory required
    - Fewer shuffle operations

    **3. Leverages Data Source Optimizations:**
    - Databases use indexes
    - Parquet/ORC skip row groups
    - Partitioned data skips partitions

    **Example:**
    ```python
    # Filter pushdown to Parquet
    df = spark.read.parquet("data.parquet")
    df.filter(col("year") == 2024)  # Filter pushed to file read
    
    # Database pushdown
    df = spark.read.jdbc(url, "table", properties)
    df.filter(col("status") == "active")  # Becomes SQL WHERE clause
    ```

    **Supported by:** Parquet, ORC, JDBC, Hive, Delta Lake

14. How do you monitor and debug PySpark job performance?

    **Tools and Techniques:**

    **1. Spark UI (Port 4040):**
    - Jobs, stages, tasks overview
    - DAG visualization
    - Task execution times
    - Shuffle read/write metrics
    - Executor resource usage

    **2. explain() Method:**
    ```python
    df.explain(extended=True)  # Shows logical and physical plans
    ```

    **3. Logging:**
    ```python
    # Set log level
    spark.sparkContext.setLogLevel("INFO")
    ```

    **4. Stage/Task Analysis:**
    - Identify slow stages in Spark UI
    - Check for stragglers (unbalanced tasks)
    - Monitor GC time

    **5. Metrics to Watch:**
    - Shuffle read/write size
    - Task duration distribution
    - Spill (memory/disk)
    - GC time percentage
    - Data skew indicators

    **6. DataFrame Statistics:**
    ```python
    df.printSchema()
    df.explain()
    print(df.rdd.getNumPartitions())
    ```

15. What Spark UI metrics are most important for optimization?

    **Key Metrics:**

    **1. Task Duration:**
    - Median vs max task time
    - Large variance = data skew
    - Look for stragglers

    **2. Shuffle Read/Write:**
    - High shuffle = performance bottleneck
    - Indicates wide transformations
    - Optimize to reduce

    **3. Spill (Memory/Disk):**
    - Memory spill = insufficient executor memory
    - Disk spill = severe memory pressure
    - Increase executor memory or optimize operations

    **4. GC Time:**
    - High GC time (>10%) = memory pressure
    - Reduce cached data or increase memory

    **5. Data Skew:**
    - Check task input size distribution
    - Few tasks with much larger data = skew
    - Use salting or custom partitioning

    **6. Stage DAG:**
    - Number of stages
    - Dependencies between stages
    - Wide vs narrow transformations

    **7. Executor Utilization:**
    - Idle executors = resource waste
    - All busy but slow = need more resources

    **Optimization Priority:** Focus on stages with longest duration and highest shuffle.

## Spark SQL and Catalyst Optimizer

1. What is Spark SQL and how does it relate to DataFrames?

    **Spark SQL:** Module for structured data processing using SQL queries or DataFrame API.

    **Relationship to DataFrames:**
    - DataFrames are distributed tables in Spark SQL
    - Can query DataFrames using SQL syntax
    - Both use same Catalyst optimizer
    - Interchangeable - can switch between SQL and DataFrame API
    - Share same execution engine

    ```python
    # DataFrame API
    df.filter(col("age") > 25).select("name", "age")
    
    # Equivalent SQL
    df.createOrReplaceTempView("people")
    spark.sql("SELECT name, age FROM people WHERE age > 25")
    ```

    **Both compile to same optimized physical plan.**

2. How do you execute SQL queries in PySpark?

    ```python
    # 1. Create temp view from DataFrame
    df.createOrReplaceTempView("employees")
    
    # 2. Execute SQL query
    result = spark.sql("""
        SELECT department, AVG(salary) as avg_salary
        FROM employees
        WHERE age > 30
        GROUP BY department
        ORDER BY avg_salary DESC
    """)
    
    # 3. Result is a DataFrame
    result.show()
    
    # Global temp view (session-scoped)
    df.createGlobalTempView("global_employees")
    spark.sql("SELECT * FROM global_temp.global_employees")
    
    # Drop view
    spark.catalog.dropTempView("employees")
    ```

3. How do you create temporary views from DataFrames?

    ```python
    # Temporary view (session-scoped, overwrites if exists)
    df.createOrReplaceTempView("my_table")
    
    # Temporary view (errors if exists)
    df.createTempView("my_table")
    
    # Global temporary view (cross-session, requires global_temp prefix)
    df.createGlobalTempView("shared_table")
    spark.sql("SELECT * FROM global_temp.shared_table")
    
    # Check if view exists
    spark.catalog.tableExists("my_table")
    
    # List all views
    spark.catalog.listTables()
    
    # Drop view
    spark.catalog.dropTempView("my_table")
    spark.catalog.dropGlobalTempView("shared_table")
    ```

    **Temporary views** last for the session; **global temp views** can be shared across sessions.

4. What is the Catalyst optimizer and how does it work?

    **Catalyst:** Spark's query optimization engine that transforms logical plans into optimized physical execution plans.

    **How It Works:**

    **Four Phases:**

    **1. Analysis:**
    - Resolves column names and types
    - Validates query semantics
    - Creates analyzed logical plan

    **2. Logical Optimization:**
    - Predicate pushdown
    - Constant folding
    - Projection pruning (column elimination)
    - Boolean expression simplification
    - Combines filters

    **3. Physical Planning:**
    - Generates multiple physical plans
    - Estimates cost of each plan
    - Selects lowest-cost plan

    **4. Code Generation:**
    - Converts to Java bytecode
    - Whole-stage code generation for efficiency

    **Example Optimizations:**
    - Combines multiple filters into one
    - Pushes filters before joins
    - Eliminates unnecessary columns early
    - Reorders operations for efficiency

5. What are the phases of query optimization in Catalyst?

    **Catalyst Optimization Phases:**

    **1. Analysis Phase:**
    - Resolves table/column references
    - Validates types and schema
    - Unresolved logical plan → Analyzed logical plan

    **2. Logical Optimization Phase:**
    - Rule-based optimizations
    - Predicate pushdown, projection pruning
    - Constant folding, filter combination
    - Analyzed logical plan → Optimized logical plan

    **3. Physical Planning Phase:**
    - Generates candidate physical plans
    - Cost-based optimization (CBO)
    - Selects best execution strategy
    - Optimized logical plan → Physical plan

    **4. Code Generation Phase:**
    - Whole-stage code generation
    - Compiles to Java bytecode
    - Physical plan → Executable code

    View with: `df.explain(extended=True)`

6. How does Catalyst generate physical execution plans?

    **Physical Plan Generation:**

    **1. Strategy Selection:**
    - Evaluates multiple physical implementations for each logical operation
    - Example: BroadcastHashJoin vs SortMergeJoin for joins

    **2. Cost-Based Optimization (CBO):**
    - Estimates cost of each plan (CPU, I/O, memory)
    - Uses table statistics (row count, data size)
    - Selects plan with lowest estimated cost

    **3. Join Strategy Selection:**
    - Broadcast join for small tables
    - Sort-merge join for large tables
    - Shuffle hash join for medium tables

    **4. Aggregation Strategy:**
    - Hash aggregation vs sort aggregation
    - Based on memory and data characteristics

    **5. Execution Operators:**
    - Maps logical operators to physical operators
    - Adds exchange (shuffle) operators where needed

    Enable CBO: `spark.sql.cbo.enabled=true` and run `ANALYZE TABLE` for statistics.

7. What is the Tungsten execution engine?

    **Tungsten:** Spark's execution engine that improves performance through memory and CPU efficiency optimizations.

    **Key Features:**

    **1. Memory Management:**
    - Off-heap memory storage
    - Binary in-memory data format
    - Reduces GC overhead
    - More compact than Java objects

    **2. Cache-Aware Computation:**
    - Algorithms optimized for CPU cache
    - Reduces memory bandwidth bottlenecks

    **3. Code Generation:**
    - Whole-stage code generation
    - Fuses multiple operators into single function
    - Eliminates virtual function calls
    - Reduces interpretation overhead

    **4. Binary Processing:**
    - Direct binary data manipulation
    - Avoids serialization/deserialization
    - Faster comparisons and hashing

    **Benefits:** 5-10x performance improvement over traditional Spark execution for CPU-intensive workloads.

8. How do you view the execution plan of a query?

    ```python
    # Simple execution plan (physical plan only)
    df.explain()
    
    # Extended plan (all phases)
    df.explain(extended=True)
    # Shows:
    # - Parsed Logical Plan
    # - Analyzed Logical Plan
    # - Optimized Logical Plan
    # - Physical Plan
    
    # Formatted plan (Spark 3.0+)
    df.explain(mode="formatted")
    
    # Cost plan (with statistics)
    df.explain(mode="cost")
    
    # Simple mode
    df.explain(mode="simple")
    ```

    Look for shuffle operations (Exchange), join strategies, and filter pushdown in the plan.

9. What is the difference between logical and physical plans?

    **Logical Plan:**
    - **What** to compute (abstract operations)
    - Platform-independent
    - Describes transformations without implementation details
    - Example: "Filter age > 25, then select name"
    - Optimized by rule-based Catalyst optimizer

    **Physical Plan:**
    - **How** to compute (concrete execution)
    - Actual implementation strategies
    - Includes specific algorithms and operators
    - Example: "Use HashAggregate, BroadcastHashJoin"
    - Chosen based on cost estimation
    - Includes shuffle/exchange operators

    **Flow:** Logical Plan → Optimized Logical Plan → Physical Plan → Execution

10. How does cost-based optimization work in Spark SQL?

    **Cost-Based Optimization (CBO):** Uses table statistics to choose optimal execution plans.

    **How It Works:**

    **1. Collect Statistics:**
    ```python
    # Analyze table to gather statistics
    spark.sql("ANALYZE TABLE my_table COMPUTE STATISTICS")
    
    # Column-level statistics
    spark.sql("ANALYZE TABLE my_table COMPUTE STATISTICS FOR COLUMNS col1, col2")
    ```

    **2. Statistics Used:**
    - Row count
    - Data size
    - Column cardinality (distinct values)
    - Min/max values
    - Null count

    **3. Cost Estimation:**
    - Estimates I/O, CPU, network costs
    - Predicts result sizes
    - Evaluates join strategies

    **4. Plan Selection:**
    - Chooses join order
    - Selects join algorithm (broadcast vs sort-merge)
    - Optimizes aggregation strategy

    **Enable:** `spark.conf.set("spark.sql.cbo.enabled", "true")`

## File Formats and I/O Operations

1. What file formats does PySpark support?

    **Supported Formats:**
    - **Parquet** - Columnar, best for analytics
    - **ORC** - Optimized Row Columnar
    - **CSV** - Comma-separated values
    - **JSON** - JavaScript Object Notation
    - **Avro** - Row-based binary format
    - **Text** - Plain text files
    - **Delta Lake** - ACID transactional format
    - **JDBC** - Database tables
    - **Hive Tables** - Hive metastore integration
    - **XML** - With external library
    - **Excel** - With external library

    ```python
    # Reading various formats
    df_parquet = spark.read.parquet("path.parquet")
    df_csv = spark.read.csv("path.csv", header=True)
    df_json = spark.read.json("path.json")
    df_orc = spark.read.orc("path.orc")
    ```

2. What are the advantages of Parquet format?

    **Parquet Advantages:**

    **1. Columnar Storage:**
    - Read only needed columns (column pruning)
    - Better compression (similar data together)
    - Ideal for analytical queries (select few columns)

    **2. Efficient Compression:**
    - Built-in compression (Snappy, Gzip, LZO)
    - Smaller file sizes
    - Reduced I/O

    **3. Schema Evolution:**
    - Schema stored in file metadata
    - Supports adding/removing columns
    - Type-safe

    **4. Predicate Pushdown:**
    - Row group statistics enable skipping
    - Min/max values per column
    - Faster filtering

    **5. Platform Independent:**
    - Works across Hadoop ecosystem
    - Language agnostic

    **Performance:** 10-100x faster than CSV for analytical queries.

3. How does columnar storage benefit analytical queries?

    **Benefits:**

    **1. Column Pruning:**
    - Read only columns needed for query
    - Skip irrelevant columns entirely
    - Massive I/O reduction

    **2. Better Compression:**
    - Similar values stored together
    - Higher compression ratios
    - Less disk space and network transfer

    **3. Vectorized Processing:**
    - Process batches of same-type values
    - CPU cache efficiency
    - SIMD optimizations

    **4. Aggregations:**
    - Scan single column for aggregates
    - No need to parse entire rows

    **Example:**
    ```
    # Query: SELECT AVG(salary) FROM employees WHERE dept = 'Sales'
    # Columnar: Read only 'salary' and 'dept' columns
    # Row-based: Read all columns for every row
    ```

    **Ideal for:** SELECT few columns from wide tables (common in analytics).

4. What is the difference between Parquet and ORC formats?

    | Aspect | Parquet | ORC |
    |--------|---------|-----|
    | **Origin** | Apache/Twitter | Apache/Hortonworks |
    | **Ecosystem** | General purpose | Hive-optimized |
    | **Compression** | Good | Excellent |
    | **Nested Data** | Better support | Limited |
    | **Statistics** | Column stats | Rich statistics |
    | **Read Performance** | Excellent | Excellent |
    | **Write Performance** | Good | Good |
    | **File Size** | Smaller with nested | Smaller for flat |
    | **Default in** | Spark | Hive |

    **Use Parquet** for general Spark workloads and nested data.
    **Use ORC** for Hive-heavy environments or when maximum compression needed.

    Both are columnar and performant; choice often based on ecosystem.

5. When would you use CSV versus Parquet?

    **Use CSV When:**
    - Human readability required
    - Interoperability with non-big-data tools (Excel, etc.)
    - Small datasets
    - Data exchange format
    - Source data from legacy systems
    - Quick exploration/debugging

    **Use Parquet When:**
    - Production data storage
    - Large datasets (>100MB)
    - Analytical workloads
    - Performance critical
    - Repeated querying
    - Data warehouse/lake storage
    - Schema enforcement needed

    **Performance Comparison:**
    - Parquet: 10-100x faster reads, 50-90% smaller size
    - CSV: Slower, larger, but universally compatible

    **Best Practice:** Ingest CSV → Transform to Parquet for processing.

6. How do you read and write JSON files in PySpark?

    ```python
    # Read JSON
    df = spark.read.json("path/to/file.json")
    
    # With options
    df = spark.read.json("path/to/file.json",
                         multiLine=True,        # For multi-line JSON
                         allowUnquotedFieldNames=True)
    
    # Read JSON lines (one JSON per line - default)
    df = spark.read.json("data.jsonl")
    
    # Infer schema with sample
    df = spark.read.option("samplingRatio", 0.1).json("large.json")
    
    # Write JSON
    df.write.json("output/path")
    
    # Write with options
    df.write.mode("overwrite") \
        .option("compression", "gzip") \
        .json("output/path")
    ```

    **Note:** Default expects one JSON object per line (JSON Lines format).

7. How do you handle nested JSON structures?

    ```python
    from pyspark.sql.functions import col, explode, struct
    
    # Access nested fields with dot notation
    df.select(col("address.city"), col("address.zipcode"))
    
    # Flatten struct
    df.select("id", "address.*")
    
    # Explode arrays (one row per array element)
    df.select("id", explode("orders").alias("order"))
    
    # Access array elements
    df.select(col("items")[0].alias("first_item"))
    
    # Nested explosion
    df.select("id", explode("orders").alias("order")) \
      .select("id", "order.order_id", explode("order.items").alias("item"))
    
    # JSON string to struct
    from pyspark.sql.functions import from_json
    schema = "name STRING, age INT"
    df.withColumn("parsed", from_json(col("json_col"), schema))
    ```

8. What is the benefit of using compression with file formats?

    **Benefits:**

    **1. Reduced Storage:**
    - 50-90% size reduction
    - Lower storage costs
    - More data fits in cache

    **2. Faster I/O:**
    - Less data to read from disk
    - Less network transfer
    - I/O often bottleneck, compression helps

    **3. Cost Savings:**
    - Cloud storage costs reduced
    - Network egress costs reduced

    **Compression Codecs:**
    - **Snappy**: Fast, moderate compression (default for Parquet)
    - **Gzip**: Slower, better compression
    - **LZO**: Fast, splittable
    - **Zstd**: Good balance of speed and compression

    ```python
    df.write.option("compression", "snappy").parquet("path")
    ```

    **Trade-off:** CPU overhead for compression/decompression vs I/O savings (usually worth it).

9. How do you write data with partitioning to disk?

    ```python
    # Partition by single column
    df.write.partitionBy("year").parquet("output/")
    
    # Partition by multiple columns
    df.write.partitionBy("year", "month", "day").parquet("output/")
    
    # With mode and compression
    df.write.mode("overwrite") \
        .partitionBy("country", "date") \
        .option("compression", "snappy") \
        .parquet("output/")
    
    # Creates directory structure:
    # output/country=US/date=2024-01-01/part-*.parquet
    # output/country=UK/date=2024-01-01/part-*.parquet
    ```

    **Best Practices:**
    - Choose low-to-medium cardinality columns
    - Avoid high cardinality (creates too many small files)
    - Order by most frequently filtered columns first

10. What write modes are available (append, overwrite, etc.)?

    **Write Modes:**

    **1. append:**
    - Add new data to existing files
    - Preserves existing data
    ```python
    df.write.mode("append").parquet("path")
    ```

    **2. overwrite:**
    - Deletes existing data and writes new
    - Replaces entire directory/table
    ```python
    df.write.mode("overwrite").parquet("path")
    ```

    **3. error / errorifexists (default):**
    - Fails if path/table already exists
    - Prevents accidental overwrites
    ```python
    df.write.mode("error").parquet("path")
    ```

    **4. ignore:**
    - Does nothing if path/table exists
    - Silently skips write
    ```python
    df.write.mode("ignore").parquet("path")
    ```

    **With partitioning:** `overwrite` mode with `partitionOverwriteMode=dynamic` overwrites only affected partitions.

11. How do you read data from multiple files or directories?

    ```python
    # Read all files in directory
    df = spark.read.parquet("data/")
    
    # Wildcard patterns
    df = spark.read.parquet("data/*.parquet")
    df = spark.read.parquet("data/year=2024/month=*/")
    
    # Multiple paths
    df = spark.read.parquet("data1/", "data2/", "data3/")
    
    # List of paths
    paths = ["path1/", "path2/", "path3/"]
    df = spark.read.parquet(*paths)
    
    # Recursive read
    df = spark.read.option("recursiveFileLookup", "true").parquet("data/")
    
    # Add filename column
    from pyspark.sql.functions import input_file_name
    df = spark.read.parquet("data/")
    df = df.withColumn("source_file", input_file_name())
    ```

12. How do you handle schema evolution in Parquet files?

    **Schema Evolution:** Reading files with different schemas (columns added/removed over time).

    ```python
    # Enable schema merging (slower but handles evolution)
    df = spark.read.option("mergeSchema", "true").parquet("path/")
    
    # Handle missing columns (fills with nulls)
    df = spark.read.parquet("path/")  # Automatic null fill for missing columns
    
    # Explicitly handle schema differences
    from pyspark.sql.functions import lit
    
    # Add missing column if doesn't exist
    if "new_column" not in df.columns:
        df = df.withColumn("new_column", lit(None).cast("string"))
    ```

    **Best Practices:**
    - Use `mergeSchema` when columns added over time
    - Document schema changes
    - Consider Delta Lake for better schema evolution support
    - Test backwards compatibility

    **Note:** Adding columns is safe; removing or changing types requires careful handling.

## Spark Streaming

1. What is PySpark Streaming and what are its use cases?

    **PySpark Streaming:** Processing continuous data streams in real-time or near-real-time.

    **Two APIs:**
    - **Structured Streaming** (recommended): High-level, DataFrame-based
    - **DStreams** (legacy): Low-level, RDD-based

    **Use Cases:**
    - Real-time analytics dashboards
    - Fraud detection systems
    - IoT sensor data processing
    - Log monitoring and alerting
    - Real-time ETL pipelines
    - Click stream analysis
    - Social media sentiment analysis
    - Financial trading systems

    **Key Feature:** Treats streaming as unbounded table with incremental updates.

2. How does Structured Streaming differ from DStreams?

    | Aspect | Structured Streaming | DStreams |
    |--------|---------------------|----------|
    | **API** | DataFrame/SQL | RDD-based |
    | **Level** | High-level | Low-level |
    | **Optimization** | Catalyst optimizer | No optimization |
    | **Event Time** | Native support | Manual implementation |
    | **Watermarks** | Built-in | Manual |
    | **Exactly-once** | Guaranteed | Manual checkpointing |
    | **Status** | Recommended | Legacy/deprecated |
    | **Ease of Use** | Easier | More complex |

    **Recommendation:** Use Structured Streaming for all new applications.

3. How do you stream data using TCP/IP protocol?

    ```python
    # Read from socket source
    stream_df = spark.readStream \
        .format("socket") \
        .option("host", "localhost") \
        .option("port", 9999) \
        .load()
    
    # Process stream
    word_counts = stream_df \
        .selectExpr("CAST(value AS STRING)") \
        .groupBy("value") \
        .count()
    
    # Write to console
    query = word_counts.writeStream \
        .outputMode("complete") \
        .format("console") \
        .start()
    
    query.awaitTermination()
    ```

    **Note:** Socket source useful for testing, not production. Use Kafka, Kinesis for production.

4. What are the common data sources for streaming?

    **Production Sources:**
    - **Apache Kafka** - Most common, distributed message broker
    - **Amazon Kinesis** - AWS managed streaming
    - **Azure Event Hubs** - Azure managed streaming
    - **File Source** - Monitor directory for new files
    - **Delta Lake** - Read changes from Delta tables

    **Development/Testing:**
    - **Socket** - TCP/IP streaming
    - **Rate Source** - Generate test data

    ```python
    # Kafka
    df = spark.readStream.format("kafka") \
        .option("kafka.bootstrap.servers", "localhost:9092") \
        .option("subscribe", "topic") \
        .load()
    
    # File source
    df = spark.readStream.format("parquet").load("input_dir/")
    ```

5. How do you implement windowing operations in streaming?

    ```python
    from pyspark.sql.functions import window, col
    
    # Tumbling window (non-overlapping, 10 minutes)
    windowed = stream_df.groupBy(
        window(col("timestamp"), "10 minutes")
    ).count()
    
    # Sliding window (overlapping, 10 min window, 5 min slide)
    windowed = stream_df.groupBy(
        window(col("timestamp"), "10 minutes", "5 minutes")
    ).count()
    
    # Session window (gap-based)
    # Not directly supported, use custom logic
    ```

    **Window Types:**
    - **Tumbling:** Fixed size, non-overlapping (e.g., every hour)
    - **Sliding:** Fixed size, overlapping (e.g., last 10 mins, every 5 mins)
    - **Session:** Variable size based on inactivity gap

6. What is watermarking in Structured Streaming?

    **Watermark:** Threshold for how late data can arrive before being ignored, enabling state cleanup.

    ```python
    from pyspark.sql.functions import window
    
    # Define watermark (tolerate 10 minutes late data)
    windowed_counts = stream_df \
        .withWatermark("timestamp", "10 minutes") \
        .groupBy(window("timestamp", "5 minutes")) \
        .count()
    ```

    **How It Works:**
    - Tracks maximum event time seen
    - Watermark = max_event_time - delay_threshold
    - Events older than watermark are dropped
    - Enables state garbage collection

    **Benefits:**
    - Prevents unbounded state growth
    - Handles late-arriving data
    - Automatic old state cleanup

7. How do you handle late-arriving data?

    **Strategies:**

    **1. Watermarking (Recommended):**
    - Define acceptable lateness threshold
    - Process late data within threshold
    - Drop data beyond threshold

    **2. No Watermark:**
    - Keep all state indefinitely
    - Risk unbounded memory growth
    - Use for small state or short-lived queries

    **3. Side Output for Late Data:**
    - Capture dropped late data separately
    - Reprocess or log for analysis

    ```python
    # Accept data up to 15 minutes late
    stream_df.withWatermark("event_time", "15 minutes") \
        .groupBy(window("event_time", "10 minutes")) \
        .count()
    ```

    **Trade-off:** Longer watermark = more memory vs better late data handling.

8. What output modes are available in Structured Streaming?

    **Output Modes:**

    **1. Append (default):**
    - Only new rows added to result table
    - Cannot change previously output rows
    - Works with: non-aggregations, watermarked aggregations
    ```python
    .outputMode("append")
    ```

    **2. Complete:**
    - Entire result table output every trigger
    - Works with: aggregations
    - High output volume
    ```python
    .outputMode("complete")
    ```

    **3. Update:**
    - Only rows updated since last trigger
    - Works with: aggregations
    - Most efficient for aggregations
    ```python
    .outputMode("update")
    ```

    **Choice depends on:** Query type and downstream requirements.

9. How do you ensure exactly-once processing semantics?

    **Exactly-Once Requirements:**

    **1. Idempotent Sinks:**
    - Writes can be replayed safely
    - Examples: File sink, Delta Lake, some JDBC

    **2. Checkpointing:**
    - Enable checkpoint location
    - Tracks processed offsets
    ```python
    query = stream_df.writeStream \
        .option("checkpointLocation", "s3://bucket/checkpoint/") \
        .start()
    ```

    **3. Replayable Sources:**
    - Kafka (offset-based)
    - Kinesis (sequence numbers)
    - File sources

    **4. Transactional Sinks:**
    - Use sinks supporting transactions
    - Delta Lake provides ACID guarantees

    **Structured Streaming guarantees** exactly-once with proper checkpoint and idempotent sinks.

10. How do you stream data to Kafka or other sinks?

    ```python
    # Write to Kafka
    query = df.selectExpr("CAST(key AS STRING)", "CAST(value AS STRING)") \
        .writeStream \
        .format("kafka") \
        .option("kafka.bootstrap.servers", "localhost:9092") \
        .option("topic", "output_topic") \
        .option("checkpointLocation", "/checkpoint/") \
        .start()
    
    # Write to Parquet files
    query = df.writeStream \
        .format("parquet") \
        .option("path", "output/") \
        .option("checkpointLocation", "/checkpoint/") \
        .start()
    
    # Write to console (debugging)
    query = df.writeStream.format("console").start()
    
    # Write to memory (testing)
    query = df.writeStream.format("memory").queryName("table").start()
    spark.sql("SELECT * FROM table").show()
    ```

11. What is checkpoint in streaming and why is it important?

    **Checkpoint:** Persistent storage of streaming query state and progress for fault tolerance.

    **What's Stored:**
    - Source offsets (what's been processed)
    - Streaming state (aggregations, joins)
    - Configuration metadata
    - Query progress information

    **Why Important:**
    - **Fault Tolerance:** Resume from failure without data loss
    - **Exactly-Once:** Track processed records
    - **State Recovery:** Restore aggregation state
    - **Restart Safety:** Continue from last checkpoint

    ```python
    .option("checkpointLocation", "s3://bucket/checkpoints/query1/")
    ```

    **Best Practices:**
    - Use reliable storage (HDFS, S3, Azure Blob)
    - One checkpoint per query
    - Don't delete while query running

12. How do you monitor streaming query progress?

    ```python
    # Get query object
    query = df.writeStream.format("console").start()
    
    # Check status
    query.status
    
    # Get last progress
    query.lastProgress
    
    # Recent progress (last few batches)
    query.recentProgress
    
    # Query details
    print(f"ID: {query.id}")
    print(f"Name: {query.name}")
    print(f"Active: {query.isActive}")
    
    # Progress metrics include:
    # - Input rows/rate
    # - Processing rate
    # - Batch duration
    # - State store metrics
    # - Source/sink details
    
    # Await termination
    query.awaitTermination()  # Blocks until stopped
    query.awaitTermination(timeout=60)  # Wait max 60 sec
    ```

    **Monitor:** Input rate, processing time, lag, state size in Spark UI.

## Cluster Architecture and Resource Management

1. Explain the Spark cluster architecture

    **Spark Cluster Architecture** has a master-worker topology with three main components:

    **Components:**

    **1. Driver:**
    - Runs main() function
    - Creates SparkContext/SparkSession
    - Converts code to execution plan (DAG)
    - Schedules tasks on executors
    - Coordinates job execution

    **2. Cluster Manager:**
    - Allocates resources
    - Types: Standalone, YARN, Mesos, Kubernetes
    - Manages worker nodes
    - Assigns executors to applications

    **3. Executors:**
    - Run on worker nodes
    - Execute tasks assigned by driver
    - Store data for cached RDDs/DataFrames
    - Return results to driver
    - Independent JVM processes

    **Flow:** Driver → Cluster Manager → Executors (parallel task execution) → Results back to Driver

2. What are the roles of Driver and Executor nodes?

    **Driver Node:**
    - Orchestrates Spark application
    - Maintains cluster state and metadata
    - Converts user code to tasks
    - Schedules tasks across executors
    - Tracks task completion
    - Collects results from executors
    - Hosts SparkContext/SparkSession
    - Single point of coordination

    **Executor Nodes:**
    - Execute tasks assigned by driver
    - Store partition data in memory/disk
    - Run computations in parallel
    - Cache data when requested
    - Send task results to driver
    - Multiple per worker node
    - Independent failure domains

    **Key Difference:** Driver coordinates; executors execute.

3. What is the Cluster Manager and what types exist?

    **Cluster Manager:** External service that manages cluster resources and allocates them to Spark applications.

    **Types:**

    **1. Standalone:**
    - Spark's built-in cluster manager
    - Simple setup
    - Good for dedicated Spark clusters
    - No dependencies

    **2. YARN (Yet Another Resource Negotiator):**
    - Hadoop's resource manager
    - Most common in enterprise
    - Shares resources with other Hadoop apps
    - Better resource utilization

    **3. Mesos:**
    - General-purpose cluster manager
    - Fine-grained resource sharing
    - Supports multiple frameworks
    - Less common now

    **4. Kubernetes:**
    - Container orchestration platform
    - Cloud-native deployments
    - Dynamic scaling
    - Growing popularity

    **5. Local:**
    - Single machine (development)
    - No actual cluster manager

4. How do you configure executor memory and cores?

    ```python
    # Via SparkSession
    spark = SparkSession.builder \
        .config("spark.executor.memory", "8g") \
        .config("spark.executor.cores", "4") \
        .config("spark.driver.memory", "4g") \
        .getOrCreate()
    
    # Via spark-submit
    spark-submit \
        --executor-memory 8G \
        --executor-cores 4 \
        --num-executors 10 \
        --driver-memory 4G \
        my_script.py
    
    # Additional memory configs
    --conf spark.executor.memoryOverhead=2G  # Off-heap memory
    --conf spark.memory.fraction=0.8          # For execution/storage
    --conf spark.memory.storageFraction=0.5   # For caching
    ```

    **Guidelines:**
    - Executor memory: 4-64 GB typical
    - Executor cores: 4-8 cores (diminishing returns beyond)
    - Leave resources for OS and other processes

5. What is dynamic allocation in Spark?

    **Dynamic Allocation:** Automatically adjusts number of executors based on workload.

    **How It Works:**
    - Starts with minimum executors
    - Adds executors when tasks pending
    - Removes idle executors after timeout
    - Scales within min/max bounds

    **Configuration:**
    ```python
    spark.conf.set("spark.dynamicAllocation.enabled", "true")
    spark.conf.set("spark.dynamicAllocation.minExecutors", "2")
    spark.conf.set("spark.dynamicAllocation.maxExecutors", "100")
    spark.conf.set("spark.dynamicAllocation.initialExecutors", "10")
    spark.conf.set("spark.dynamicAllocation.executorIdleTimeout", "60s")
    ```

    **Benefits:**
    - Better resource utilization
    - Cost savings (cloud environments)
    - Handles variable workloads
    - Shared cluster efficiency

    **Requires:** External shuffle service enabled.

6. How do you determine optimal executor configuration?

    **Calculation Method:**

    **1. Available Resources:**
    - Total cluster memory and cores
    - Leave 1-2 cores and memory for OS per node

    **2. Executor Cores:**
    - Recommend: 4-5 cores per executor
    - More cores = less HDFS throughput per executor
    - Formula: `cores_per_executor = 5` (typical)

    **3. Executor Memory:**
    - Formula: `(node_memory - 1GB) / executors_per_node`
    - Include overhead: `executor_memory * 0.10` minimum

    **4. Number of Executors:**
    - Formula: `(total_cores / cores_per_executor) - 1` (for driver)

    **Example:**
    - 10 nodes, 16 cores, 64GB each
    - Cores per executor: 5
    - Executors per node: (16-1)/5 = 3
    - Total executors: 10*3 - 1 = 29
    - Memory per executor: (64-1)/3 ≈ 21GB

7. What is the difference between client and cluster deploy mode?

    | Aspect | Client Mode | Cluster Mode |
    |--------|-------------|--------------|
    | **Driver Location** | Client machine | Worker node |
    | **Network** | Driver-executor traffic via client | All internal to cluster |
    | **Use Case** | Interactive, development | Production, automated |
    | **Logs** | On client machine | On cluster node |
    | **Client Disconnect** | Job fails | Job continues |
    | **Debugging** | Easier (local logs) | Harder (cluster logs) |
    | **Latency** | Higher if client remote | Lower (cluster-local) |

    ```bash
    # Client mode
    spark-submit --deploy-mode client my_app.py
    
    # Cluster mode
    spark-submit --deploy-mode cluster my_app.py
    ```

    **Use client** for development/notebooks; **use cluster** for production.

8. How does data locality affect performance?

    **Data Locality:** How close data is to the computation (executor processing it).

    **Locality Levels (Best to Worst):**

    **1. PROCESS_LOCAL:**
    - Data in same JVM as executor
    - Cached data
    - Fastest (no network/disk I/O)

    **2. NODE_LOCAL:**
    - Data on same node, different process
    - HDFS blocks on same machine
    - Fast (disk I/O only)

    **3. RACK_LOCAL:**
    - Data on same rack
    - Network within rack
    - Moderate speed

    **4. ANY:**
    - Data anywhere in cluster
    - Cross-rack network transfer
    - Slowest

    **Impact:** PROCESS_LOCAL can be 10-100x faster than ANY.

    **Optimization:** Spark automatically delays scheduling to wait for better locality.

9. What happens during stage failures in a Spark job?

    **Failure Recovery Process:**

    **1. Task Failure:**
    - Task retried on different executor
    - Up to `spark.task.maxFailures` attempts (default: 4)
    - Uses lineage to recompute lost data

    **2. Stage Failure:**
    - If all task retries fail, stage fails
    - Entire stage resubmitted
    - Recomputes all stage tasks

    **3. Job Failure:**
    - After `spark.stage.maxConsecutiveAttempts` stage failures
    - Job marked as failed
    - Exception thrown to driver

    **4. Executor Failure:**
    - Lost partitions recomputed using lineage
    - Tasks reassigned to healthy executors
    - Cached data lost from that executor

    **Mitigation:**
    - Use checkpointing for long lineages
    - Monitor Spark UI for repeated failures
    - Increase memory if OOM errors

10. How do you handle out-of-memory errors?

    **Solutions by Error Type:**

    **Driver OOM:**
    - Increase driver memory: `--driver-memory 8G`
    - Avoid `collect()` on large DataFrames
    - Use `take()` or `limit()` instead
    - Sample data before collecting

    **Executor OOM:**
    - Increase executor memory: `--executor-memory 16G`
    - Increase partitions: More, smaller partitions
    - Reduce `spark.sql.shuffle.partitions`
    - Avoid data skew (use salting)
    - Unpersist unused cached data

    **General Strategies:**
    ```python
    # Increase partitions
    df = df.repartition(1000)
    
    # Handle skew
    df = df.withColumn("salt", (rand() * 100).cast("int"))
    
    # Reduce memory usage
    df.unpersist()
    
    # Use broadcast for small tables
    result = large_df.join(broadcast(small_df), "key")
    ```

    **Monitor:** Spark UI for memory spill and GC time.

## Fault Tolerance and Reliability

1. How does PySpark ensure fault tolerance?

    **Fault Tolerance Mechanisms:**

    **1. Lineage Tracking:**
    - Records transformation history (DAG)
    - Recomputes lost partitions from source
    - No data replication overhead

    **2. RDD/DataFrame Immutability:**
    - Once created, cannot be modified
    - Safe to recompute from lineage
    - Consistent results

    **3. Task Retry:**
    - Failed tasks automatically retried
    - Up to configured max attempts
    - Reassigned to different executors

    **4. Checkpointing:**
    - Persists data to reliable storage
    - Truncates long lineages
    - Faster recovery for iterative jobs

    **5. Write-Ahead Logs (Streaming):**
    - Records received data before processing
    - Enables recovery from driver failures

    **Result:** Automatic recovery from node failures without data loss.

2. What is checkpointing and when should you use it?

    **Checkpointing:** Saving RDD/DataFrame to reliable storage (HDFS, S3) to truncate lineage.

    ```python
    # Set checkpoint directory
    sc.setCheckpointDir("hdfs://path/to/checkpoint")
    
    # Checkpoint RDD
    rdd.checkpoint()
    rdd.count()  # Triggers checkpoint
    
    # For DataFrames (streaming)
    df.writeStream \
        .option("checkpointLocation", "s3://bucket/checkpoint/") \
        .start()
    ```

    **When to Use:**
    - Long lineage chains (>10 transformations)
    - Iterative algorithms (ML, graph processing)
    - Streaming applications (required)
    - Wide transformations repeated on same data
    - After expensive shuffles

    **Benefits:**
    - Faster recovery (no full recomputation)
    - Avoids stack overflow from deep lineage
    - Required for stateful streaming

    **Trade-off:** Disk I/O cost vs faster recovery.

3. How does data replication contribute to fault tolerance?

    **Replication in Spark:**

    **1. Storage Level Replication:**
    ```python
    from pyspark import StorageLevel
    
    # Replicate cached data
    df.persist(StorageLevel.MEMORY_AND_DISK_2)  # 2 replicas
    rdd.persist(StorageLevel.MEMORY_ONLY_2)
    ```

    **2. Input Data Replication:**
    - HDFS already replicates blocks (default: 3x)
    - S3/Cloud storage has built-in redundancy
    - No Spark-level replication needed for source

    **3. Shuffle Data:**
    - Not replicated by default
    - Lost shuffle data recomputed from lineage

    **Benefits:**
    - Faster recovery (no recomputation)
    - Better locality for cached data
    - Survives executor failures

    **Trade-offs:**
    - 2x storage cost for replicated cache
    - Spark prefers lineage over replication (more efficient)

4. What is write-ahead logging in streaming applications?

    **Write-Ahead Log (WAL):** Records received data to reliable storage before processing (DStreams only, not Structured Streaming).

    **How It Works:**
    - Data received from source
    - Written to HDFS/S3 immediately
    - Then processed
    - Ensures data not lost if driver fails

    **Configuration (DStreams):**
    ```python
    streamingContext.checkpoint("hdfs://checkpoint")
    spark.conf.set("spark.streaming.receiver.writeAheadLog.enable", "true")
    ```

    **Structured Streaming:**
    - Uses checkpointing instead of WAL
    - Reads from replayable sources (Kafka offsets)
    - More efficient approach

    **Trade-off:** Adds latency but ensures zero data loss for receiver-based DStreams.

    **Note:** Structured Streaming doesn't need WAL due to better fault tolerance model.

5. How does Spark recover from executor failures?

    **Recovery Process:**

    **1. Detection:**
    - Driver detects executor failure via heartbeat timeout
    - Tasks running on failed executor marked as lost

    **2. Task Reassignment:**
    - Lost tasks reassigned to healthy executors
    - Recomputed using lineage from source data

    **3. Lost Data Recovery:**
    - **Cached data:** Lost from failed executor, recomputed if needed
    - **Shuffle data:** Recomputed from parent stages
    - **Checkpointed data:** Read from persistent storage

    **4. Automatic Retry:**
    - Tasks retried up to `spark.task.maxFailures` times
    - If stage fails repeatedly, job fails

    **5. Resource Reallocation:**
    - Cluster manager can allocate new executor
    - With dynamic allocation, replacement automatic

    **Key:** Lineage enables selective recomputation of only lost partitions.

6. What happens when the driver fails?

    **Driver Failure Impact:**

    **Batch Jobs:**
    - **Job fails completely**
    - All executors terminated
    - Must restart entire application
    - No automatic recovery

    **Streaming (Structured Streaming):**
    - Can recover with checkpointing
    - Restart reads from checkpoint
    - Continues from last processed offset
    - Stateful operations restored

    **Prevention Strategies:**

    **1. Cluster Deploy Mode:**
    - Driver runs on cluster (not client machine)
    - Cluster manager can restart driver

    **2. Checkpointing (Streaming):**
    ```python
    .option("checkpointLocation", "s3://reliable/path")
    ```

    **3. Monitoring:**
    - Alert on driver failures
    - Automatic restart mechanisms

    **4. Reduce Driver Load:**
    - Avoid `collect()` on large data
    - Minimize driver-side operations

7. How do you implement retry mechanisms for failed tasks?

    **Built-in Task Retry:**

    ```python
    # Configure task retries
    spark.conf.set("spark.task.maxFailures", "4")  # Default
    
    # Stage retry
    spark.conf.set("spark.stage.maxConsecutiveAttempts", "4")
    ```

    **Automatic Behavior:**
    - Spark automatically retries failed tasks
    - Reassigns to different executors
    - Uses lineage for recomputation

    **Custom Retry Logic:**
    ```python
    from functools import wraps
    import time
    
    def retry(max_attempts=3, delay=5):
        def decorator(func):
            @wraps(func)
            def wrapper(*args, **kwargs):
                for attempt in range(max_attempts):
                    try:
                        return func(*args, **kwargs)
                    except Exception as e:
                        if attempt < max_attempts - 1:
                            time.sleep(delay)
                            continue
                        raise
            return wrapper
        return decorator
    
    @retry(max_attempts=3)
    def process_partition(iterator):
        # Custom logic with retry
        pass
    ```

8. What is speculative execution in Spark?

    **Speculative Execution:** Running duplicate copies of slow tasks (stragglers) on other executors.

    **How It Works:**
    - Detects tasks running slower than median
    - Launches duplicate task on different executor
    - Uses whichever finishes first
    - Kills the other copy

    **Configuration:**
    ```python
    spark.conf.set("spark.speculation", "true")  # Enable
    spark.conf.set("spark.speculation.interval", "100ms")  # Check interval
    spark.conf.set("spark.speculation.multiplier", "1.5")  # Slowness threshold
    spark.conf.set("spark.speculation.quantile", "0.75")  # Percentile to check
    ```

    **Benefits:**
    - Handles slow nodes/executors
    - Reduces impact of stragglers
    - Improves overall job completion time

    **Caution:**
    - Don't use with non-idempotent operations
    - Consumes extra resources
    - Not suitable for all workloads

9. How do you handle data corruption issues?

    **Prevention and Handling:**

    **1. Validation at Read:**
    ```python
    # Strict schema enforcement
    df = spark.read.schema(schema).csv("data.csv")
    
    # Handle corrupt records
    df = spark.read \
        .option("mode", "PERMISSIVE") \  # Default: puts corrupt in _corrupt_record
        .option("columnNameOfCorruptRecord", "_corrupt") \
        .json("data.json")
    
    # Drop corrupt records
    df = spark.read.option("mode", "DROPMALFORMED").json("data.json")
    
    # Fail on corruption
    df = spark.read.option("mode", "FAILFAST").json("data.json")
    ```

    **2. Data Quality Checks:**
    - Validate data types, ranges, nulls
    - Log corrupt records separately
    - Alert on high corruption rates

    **3. Checksums:**
    - Use file formats with built-in checksums (Parquet, ORC)
    - Verify data integrity

    **4. Replication:**
    - Use replicated storage (HDFS, S3)
    - Corruption detected and recovered automatically

10. What are best practices for building fault-tolerant applications?

    **Best Practices:**

    **1. Use Reliable Storage:**
    - HDFS, S3, Azure Blob with replication
    - Checkpoints to durable storage

    **2. Enable Checkpointing:**
    - For streaming: mandatory
    - For batch: long lineages or iterative jobs

    **3. Idempotent Operations:**
    - Design operations to be safely re-executed
    - Handle duplicate processing gracefully

    **4. Avoid Stateful Operations on Driver:**
    - Minimize driver-side state
    - Use accumulators carefully

    **5. Configure Retries:**
    - Set appropriate task/stage retry limits
    - Balance retry vs fail-fast

    **6. Monitor and Alert:**
    - Track job failures, task retries
    - Alert on repeated failures
    - Monitor resource usage

    **7. Test Failure Scenarios:**
    - Simulate executor/driver failures
    - Verify recovery mechanisms
    - Test checkpoint restoration

    **8. Use Cluster Deploy Mode:**
    - For production jobs
    - Enables driver restart

## Delta Lake and Advanced Topics

1. What is Delta Lake and how does it enhance PySpark?

    **Delta Lake:** Open-source storage layer that brings ACID transactions and reliability to data lakes (Parquet-based).

    **Enhancements to PySpark:**

    **1. ACID Transactions:**
    - Atomic writes (all-or-nothing)
    - Consistency guarantees
    - Isolation between reads/writes

    **2. Time Travel:**
    - Query historical versions
    - Rollback to previous states

    **3. Schema Evolution:**
    - Safe schema changes
    - Automatic schema validation

    **4. Upserts/Merges:**
    - Native MERGE operations
    - Update, delete, upsert support

    **5. Unified Batch/Streaming:**
    - Same table for both paradigms
    - Exactly-once guarantees

    **Usage:**
    ```python
    # Write Delta table
    df.write.format("delta").save("/path/to/table")
    
    # Read Delta table
    df = spark.read.format("delta").load("/path/to/table")
    ```

2. What are the key features of Delta Lake?

    **Key Features:**

    **1. ACID Transactions** - Reliable concurrent reads/writes
    **2. Time Travel** - Query/restore historical data
    **3. Schema Enforcement** - Prevents bad data writes
    **4. Schema Evolution** - Controlled schema changes
    **5. Upserts/Deletes** - SQL MERGE, UPDATE, DELETE operations
    **6. Unified Batch/Streaming** - Same table for both
    **7. Audit History** - Complete operation log (transaction log)
    **8. Scalable Metadata** - Handles millions of files efficiently
    **9. Data Versioning** - Every write creates new version
    **10. Compaction** - OPTIMIZE command for small files
    **11. Z-Ordering** - Multi-dimensional clustering
    **12. Vacuum** - Clean up old versions

    All features work seamlessly with standard PySpark APIs.

3. How does Delta Lake provide ACID transactions?

    **ACID Implementation:**

    **Transaction Log:**
    - Every operation writes to `_delta_log/` directory
    - JSON files record all changes (commits)
    - Atomic log updates using file system guarantees
    - Sequential version numbers (000.json, 001.json, etc.)

    **Atomicity:**
    - All-or-nothing writes
    - Transaction committed only when log entry successful

    **Consistency:**
    - Schema validation before writes
    - Constraint checking

    **Isolation:**
    - Optimistic concurrency control
    - Readers see consistent snapshots
    - Writers don't block readers

    **Durability:**
    - Changes persisted to storage
    - Transaction log is source of truth

    **Example:**
    ```python
    # Multiple writers - Delta handles conflicts
    df.write.format("delta").mode("append").save("/delta/table")
    ```

4. What is time travel in Delta Lake?

    **Time Travel:** Query historical versions of Delta table data.

    ```python
    # Read specific version
    df = spark.read.format("delta").option("versionAsOf", 5).load("/delta/table")
    
    # Read by timestamp
    df = spark.read.format("delta") \
        .option("timestampAsOf", "2024-01-01 00:00:00") \
        .load("/delta/table")
    
    # Using SQL
    spark.sql("SELECT * FROM delta.`/delta/table` VERSION AS OF 10")
    spark.sql("SELECT * FROM delta.`/delta/table` TIMESTAMP AS OF '2024-01-01'")
    
    # View history
    from delta.tables import DeltaTable
    deltaTable = DeltaTable.forPath(spark, "/delta/table")
    deltaTable.history().show()
    
    # Restore to previous version
    deltaTable.restoreToVersion(5)
    ```

    **Use Cases:** Audit trails, rollback errors, reproduce ML models, compare versions.

5. How do you perform upserts (merge operations) in Delta Lake?

    ```python
    from delta.tables import DeltaTable
    
    # Load Delta table
    deltaTable = DeltaTable.forPath(spark, "/delta/table")
    
    # Merge (upsert) new data
    deltaTable.alias("target").merge(
        updates_df.alias("source"),
        "target.id = source.id"  # Match condition
    ).whenMatchedUpdate(set={
        "name": "source.name",
        "updated_at": "source.updated_at"
    }).whenNotMatchedInsert(values={
        "id": "source.id",
        "name": "source.name",
        "updated_at": "source.updated_at"
    }).execute()
    
    # Conditional update
    deltaTable.alias("t").merge(
        updates.alias("s"), "t.id = s.id"
    ).whenMatchedUpdate(
        condition="s.status = 'active'",
        set={"value": "s.value"}
    ).whenMatchedDelete(
        condition="s.status = 'deleted'"
    ).whenNotMatchedInsert(
        values={"id": "s.id", "value": "s.value"}
    ).execute()
    ```

6. What is schema evolution in Delta Lake?

    **Schema Evolution:** Automatically adapt table schema when writing data with new columns.

    ```python
    # Enable schema evolution
    df_new.write.format("delta") \
        .mode("append") \
        .option("mergeSchema", "true") \
        .save("/delta/table")
    
    # Schema enforcement (default - prevents incompatible writes)
    df.write.format("delta").mode("append").save("/delta/table")  # Fails if schema mismatch
    
    # Overwrite schema
    df.write.format("delta") \
        .mode("overwrite") \
        .option("overwriteSchema", "true") \
        .save("/delta/table")
    ```

    **Allowed Changes:**
    - Add new columns (nulls for existing rows)
    - Widen column types (int → long)

    **Not Allowed:**
    - Drop columns (use overwriteSchema)
    - Change column types incompatibly
    - Rename columns (appears as drop + add)

7. How does Delta Lake handle concurrent writes?

    **Optimistic Concurrency Control:**

    **Process:**
    1. Read current version from transaction log
    2. Perform operation (write files)
    3. Attempt to commit to transaction log
    4. If conflict detected (another commit happened), retry

    **Conflict Resolution:**
    - **Compatible operations**: Both succeed (e.g., appends to different partitions)
    - **Conflicting operations**: One succeeds, other retries
    - Automatic retry with exponential backoff

    **Isolation Levels:**
    ```python
    # Serializable (default) - strongest isolation
    spark.conf.set("spark.databricks.delta.isolationLevel", "Serializable")
    
    # WriteSerializable - allows concurrent appends
    spark.conf.set("spark.databricks.delta.isolationLevel", "WriteSerializable")
    ```

    **Best Practice:** Design for append-heavy workloads to minimize conflicts.

8. What is Z-ordering optimization?

    **Z-Ordering:** Multi-dimensional clustering technique that co-locates related data in files for faster queries.

    ```python
    from delta.tables import DeltaTable
    
    # Optimize with Z-order
    deltaTable = DeltaTable.forPath(spark, "/delta/table")
    deltaTable.optimize().executeZOrderBy("date", "country")
    
    # SQL syntax
    spark.sql("OPTIMIZE delta.`/delta/table` ZORDER BY (date, country)")
    ```

    **How It Works:**
    - Maps multi-dimensional data to one dimension using Z-order curve
    - Co-locates data with similar values
    - Reduces files scanned for range queries

    **Benefits:**
    - Faster queries on multiple columns
    - Better than single-column sorting
    - Works with any frequently filtered columns

    **Use When:** Queries filter on multiple columns (e.g., date AND country).

9. How do you implement Change Data Capture (CDC) with Delta?

    **CDC Pattern with Delta Lake:**

    ```python
    from delta.tables import DeltaTable
    
    # Assume CDC feed with columns: id, data, operation (I/U/D), timestamp
    cdc_df = spark.read.format("delta").load("/cdc/feed")
    
    # Apply CDC changes using merge
    deltaTable = DeltaTable.forPath(spark, "/delta/target")
    
    deltaTable.alias("target").merge(
        cdc_df.alias("cdc"),
        "target.id = cdc.id"
    ).whenMatchedDelete(
        condition="cdc.operation = 'D'"
    ).whenMatchedUpdate(
        condition="cdc.operation = 'U'",
        set={"data": "cdc.data", "updated_at": "cdc.timestamp"}
    ).whenNotMatchedInsert(
        condition="cdc.operation = 'I'",
        values={"id": "cdc.id", "data": "cdc.data", "created_at": "cdc.timestamp"}
    ).execute()
    
    # Track changes with CDF (Change Data Feed)
    spark.conf.set("spark.databricks.delta.properties.defaults.enableChangeDataFeed", "true")
    
    # Read changes between versions
    changes = spark.read.format("delta") \
        .option("readChangeFeed", "true") \
        .option("startingVersion", 5) \
        .option("endingVersion", 10) \
        .load("/delta/table")
    ```

10. What are the benefits of using Delta Lake over Parquet?

    | Feature | Parquet | Delta Lake |
    |---------|---------|------------|
    | **ACID** | ❌ No | ✅ Yes |
    | **Updates/Deletes** | ❌ No | ✅ Yes |
    | **Time Travel** | ❌ No | ✅ Yes |
    | **Schema Enforcement** | ❌ No | ✅ Yes |
    | **Concurrent Writes** | ❌ Conflicts | ✅ Handled |
    | **Upserts** | ❌ Manual | ✅ Native MERGE |
    | **Audit History** | ❌ No | ✅ Transaction log |
    | **Metadata Management** | ⚠️ Manual | ✅ Automatic |
    | **File Format** | Parquet | Parquet + transaction log |
    | **Performance** | ✅ Fast | ✅ Fast + optimizations |

    **Delta Lake = Parquet + Reliability + Advanced Features**

    **Use Parquet** for immutable data archives.
    **Use Delta Lake** for data lakes, warehouses, and production pipelines.

## Machine Learning with MLlib

1. What is MLlib in PySpark?

    **MLlib:** Spark's scalable machine learning library for distributed ML on large datasets.

    **Components:**
    - **ML Algorithms**: Classification, regression, clustering, collaborative filtering
    - **Featurization**: Feature extraction, transformation, selection
    - **Pipelines**: Chain transformations and models
    - **Utilities**: Evaluation metrics, hyperparameter tuning, model persistence

    **Two APIs:**
    - **spark.ml** (DataFrame-based) - Recommended
    - **spark.mllib** (RDD-based) - Legacy

    **Key Strengths:**
    - Distributed training on massive datasets
    - Integration with Spark ecosystem
    - Pipeline abstraction for workflows

    **Use When:** Data too large for single-machine libraries (scikit-learn, etc.).

2. How do you prepare data for machine learning in PySpark?

    ```python
    from pyspark.ml.feature import VectorAssembler, StringIndexer, StandardScaler
    
    # 1. Handle missing values
    df = df.na.fill({"age": 30, "income": 0})
    
    # 2. Encode categorical variables
    indexer = StringIndexer(inputCol="category", outputCol="categoryIndex")
    df = indexer.fit(df).transform(df)
    
    # 3. Assemble features into vector
    assembler = VectorAssembler(
        inputCols=["age", "income", "categoryIndex"],
        outputCol="features"
    )
    df = assembler.transform(df)
    
    # 4. Scale features
    scaler = StandardScaler(inputCol="features", outputCol="scaledFeatures")
    df = scaler.fit(df).transform(df)
    
    # 5. Split train/test
    train, test = df.randomSplit([0.8, 0.2], seed=42)
    ```

3. What is a feature vector and how do you create it?

    **Feature Vector:** Dense or sparse array containing all input features for ML model (required by MLlib).

    ```python
    from pyspark.ml.feature import VectorAssembler
    from pyspark.ml.linalg import Vectors
    
    # Create vector from multiple columns
    assembler = VectorAssembler(
        inputCols=["age", "salary", "years_exp"],
        outputCol="features"
    )
    df_vector = assembler.transform(df)
    
    # Manual vector creation (less common)
    df = spark.createDataFrame([
        (1.0, Vectors.dense([1.0, 2.0, 3.0])),
        (0.0, Vectors.sparse(3, {0: 1.0, 2: 3.0}))  # Sparse vector
    ], ["label", "features"])
    ```

    **Result:** Single column containing all features as vector.

4. What transformers and estimators are available in MLlib?

    **Transformers (transform data without learning):**
    - `VectorAssembler` - Combine columns into feature vector
    - `StandardScaler` - Standardize features
    - `MinMaxScaler` - Scale to range
    - `Normalizer` - Normalize vectors
    - `Tokenizer` - Split text into words
    - `StopWordsRemover` - Remove common words
    - `CountVectorizer` - Convert text to vectors
    - `PCA` - Dimensionality reduction

    **Estimators (learn from data, produce model):**
    - `StringIndexer` - Encode categorical to numeric
    - `LogisticRegression` - Binary/multiclass classification
    - `LinearRegression` - Regression
    - `DecisionTreeClassifier` - Tree-based classifier
    - `RandomForestClassifier` - Ensemble classifier
    - `GBTClassifier` - Gradient boosted trees
    - `KMeans` - Clustering
    - `ALS` - Collaborative filtering

5. How do you implement a machine learning pipeline?

    ```python
    from pyspark.ml import Pipeline
    from pyspark.ml.feature import StringIndexer, VectorAssembler
    from pyspark.ml.classification import LogisticRegression
    
    # Define pipeline stages
    # Stage 1: Index categorical column
    indexer = StringIndexer(inputCol="category", outputCol="categoryIndex")
    
    # Stage 2: Assemble features
    assembler = VectorAssembler(
        inputCols=["age", "salary", "categoryIndex"],
        outputCol="features"
    )
    
    # Stage 3: Train model
    lr = LogisticRegression(featuresCol="features", labelCol="label")
    
    # Create pipeline
    pipeline = Pipeline(stages=[indexer, assembler, lr])
    
    # Fit pipeline (runs all stages)
    model = pipeline.fit(train_df)
    
    # Make predictions
    predictions = model.transform(test_df)
    
    # Save pipeline
    model.save("/path/to/model")
    ```

    **Benefits:** Reproducible, maintainable, prevents data leakage.

6. How do you perform feature engineering in PySpark?

    **Answer:** PySpark MLlib provides various transformers for feature engineering:
    
    - **Numerical Features:** StandardScaler, MinMaxScaler, Normalizer, Binarizer
    - **Categorical Features:** StringIndexer, OneHotEncoder, VectorIndexer
    - **Text Features:** Tokenizer, HashingTF, IDF, Word2Vec, CountVectorizer
    - **Feature Selection:** ChiSqSelector, UnivariateFeatureSelector
    - **Feature Extraction:** PCA, PolynomialExpansion, FeatureHasher
    - **Feature Transformation:** Bucketizer (binning), SQLTransformer (custom SQL), QuantileDiscretizer
    
    Common pattern: Chain transformers in Pipeline for automated feature engineering.

7. What algorithms does MLlib support?

    **Classification:**
    - Logistic Regression
    - Decision Trees
    - Random Forests
    - Gradient-Boosted Trees (GBT)
    - Naive Bayes
    - Linear SVM
    - Multilayer Perceptron

    **Regression:**
    - Linear Regression
    - Decision Tree Regression
    - Random Forest Regression
    - GBT Regression
    - Isotonic Regression
    - Generalized Linear Regression

    **Clustering:**
    - K-Means
    - Gaussian Mixture Model (GMM)
    - Bisecting K-Means
    - Latent Dirichlet Allocation (LDA)

    **Collaborative Filtering:** ALS (Alternating Least Squares)

8. How do you train and evaluate models in PySpark?

    ```python
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.evaluation import BinaryClassificationEvaluator, MulticlassClassificationEvaluator
    
    # Split data
    train, test = df.randomSplit([0.8, 0.2], seed=42)
    
    # Train model
    lr = LogisticRegression(featuresCol="features", labelCol="label", maxIter=10)
    model = lr.fit(train)
    
    # Make predictions
    predictions = model.transform(test)
    
    # Evaluate
    evaluator = BinaryClassificationEvaluator(labelCol="label", metricName="areaUnderROC")
    auc = evaluator.evaluate(predictions)
    
    # For multiclass
    mc_evaluator = MulticlassClassificationEvaluator(labelCol="label", metricName="accuracy")
    accuracy = mc_evaluator.evaluate(predictions)
    
    # Other metrics: f1, precision, recall, areaUnderPR
    ```

9. How do you perform hyperparameter tuning?

    ```python
    from pyspark.ml.tuning import ParamGridBuilder, CrossValidator
    from pyspark.ml.classification import RandomForestClassifier
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    
    # Create model
    rf = RandomForestClassifier(labelCol="label", featuresCol="features")
    
    # Build parameter grid
    paramGrid = ParamGridBuilder() \
        .addGrid(rf.numTrees, [10, 20, 50]) \
        .addGrid(rf.maxDepth, [5, 10, 15]) \
        .addGrid(rf.minInstancesPerNode, [1, 5, 10]) \
        .build()
    
    # Create cross-validator
    crossval = CrossValidator(
        estimator=rf,
        estimatorParamMaps=paramGrid,
        evaluator=BinaryClassificationEvaluator(),
        numFolds=3
    )
    
    # Train with cross-validation
    cvModel = crossval.fit(train)
    
    # Best model
    bestModel = cvModel.bestModel
    
    # Alternative: TrainValidationSplit (faster, single split)
    ```

10. How do you save and load trained models?

    ```python
    # Save model
    model.write().overwrite().save("/path/to/model")
    
    # Save pipeline model
    pipelineModel.write().overwrite().save("/path/to/pipeline")
    
    # Load model
    from pyspark.ml.classification import LogisticRegressionModel
    loadedModel = LogisticRegressionModel.load("/path/to/model")
    
    # Load pipeline
    from pyspark.ml import PipelineModel
    loadedPipeline = PipelineModel.load("/path/to/pipeline")
    
    # Use loaded model
    predictions = loadedModel.transform(new_data)
    ```

    **Note:** Pipeline models include all transformers and estimators, ensuring consistent preprocessing.

## Scenario-Based and Coding Questions

1. How would you remove duplicates based on specific columns?

    ```python
    # Remove duplicates based on specific columns, keeping first occurrence
    df_deduped = df.dropDuplicates(["customer_id", "transaction_date"])
    
    # Keep latest record by timestamp
    from pyspark.sql import Window
    from pyspark.sql.functions import row_number, desc
    
    window = Window.partitionBy("customer_id", "transaction_date").orderBy(desc("timestamp"))
    df_deduped = df.withColumn("rn", row_number().over(window)) \
                   .filter(col("rn") == 1) \
                   .drop("rn")
    ```

2. How do you find the top N records per group?

    ```python
    from pyspark.sql import Window
    from pyspark.sql.functions import row_number, desc
    
    # Top 3 products by sales per category
    window = Window.partitionBy("category").orderBy(desc("sales"))
    
    top_n = df.withColumn("rank", row_number().over(window)) \
              .filter(col("rank") <= 3) \
              .drop("rank")
    
    # Alternative using rank() - handles ties differently
    from pyspark.sql.functions import rank
    window = Window.partitionBy("category").orderBy(desc("sales"))
    top_n = df.withColumn("rank", rank().over(window)) \
              .filter(col("rank") <= 3)
    ```

3. How would you implement a slowly changing dimension (SCD) Type 2?

    ```python
    from pyspark.sql.functions import current_date, lit
    
    # Existing dimension table
    existing = spark.read.format("delta").load("/dim/customer")
    
    # New incoming data
    new_data = spark.read.csv("new_customers.csv", header=True)
    
    # Find changed records
    changed = new_data.alias("new").join(
        existing.filter(col("is_current") == True).alias("old"),
        "customer_id",
        "inner"
    ).where(
        (col("new.address") != col("old.address")) |
        (col("new.phone") != col("old.phone"))
    )
    
    # Expire old records
    expired = changed.select(
        col("old.surrogate_key"),
        lit(current_date()).alias("end_date"),
        lit(False).alias("is_current")
    )
    
    # Create new records with new surrogate key
    from pyspark.sql.functions import monotonically_increasing_id
    new_records = changed.select("new.*") \
        .withColumn("surrogate_key", monotonically_increasing_id()) \
        .withColumn("start_date", current_date()) \
        .withColumn("end_date", lit(None).cast("date")) \
        .withColumn("is_current", lit(True))
    
    # Merge: update expired, insert new
    # Use Delta merge for this operation
    ```

4. How do you calculate cumulative sums across partitions?

    ```python
    from pyspark.sql import Window
    from pyspark.sql.functions import sum as _sum
    
    # Cumulative sum of sales by customer, ordered by date
    window = Window.partitionBy("customer_id") \
                   .orderBy("transaction_date") \
                   .rowsBetween(Window.unboundedPreceding, Window.currentRow)
    
    df_cumsum = df.withColumn("cumulative_sales", _sum("amount").over(window))
    
    # Running average
    from pyspark.sql.functions import avg
    df_avg = df.withColumn("running_avg", avg("amount").over(window))
    
    # Cumulative count
    from pyspark.sql.functions import count
    df_count = df.withColumn("transaction_number", count("*").over(window))
    ```

5. How would you handle a dataset with extreme data skew?

    **Strategies:**

    **1. Salting (for skewed joins):**
    ```python
    from pyspark.sql.functions import rand, concat, lit
    
    # Add salt to skewed key
    salt_factor = 10
    skewed_df = df1.withColumn("salt", (rand() * salt_factor).cast("int")) \
                   .withColumn("salted_key", concat(col("key"), lit("_"), col("salt")))
    
    # Replicate smaller table
    normal_df = df2.withColumn("salt", explode(array([lit(i) for i in range(salt_factor)]))) \
                   .withColumn("salted_key", concat(col("key"), lit("_"), col("salt")))
    
    # Join on salted key
    result = skewed_df.join(normal_df, "salted_key")
    ```

    **2. Broadcast smaller table:**
    ```python
    from pyspark.sql.functions import broadcast
    result = large_df.join(broadcast(small_df), "key")
    ```

    **3. Adaptive Query Execution:**
    ```python
    spark.conf.set("spark.sql.adaptive.enabled", "true")
    spark.conf.set("spark.sql.adaptive.skewJoin.enabled", "true")
    ```

6. How do you implement complex business logic across multiple tables?

    ```python
    # Example: Calculate customer lifetime value with multiple dimensions
    
    # 1. Get customer orders
    orders = spark.table("orders").filter(col("status") == "completed")
    
    # 2. Join with product details
    order_details = orders.join(spark.table("products"), "product_id")
    
    # 3. Calculate revenue per customer
    customer_revenue = order_details.groupBy("customer_id") \
        .agg(
            _sum("price").alias("total_revenue"),
            count("order_id").alias("total_orders"),
            avg("price").alias("avg_order_value")
        )
    
    # 4. Join with customer segments
    customers = spark.table("customers")
    enriched = customer_revenue.join(customers, "customer_id")
    
    # 5. Apply business rules
    result = enriched.withColumn(
        "customer_tier",
        when(col("total_revenue") > 10000, "platinum")
        .when(col("total_revenue") > 5000, "gold")
        .when(col("total_revenue") > 1000, "silver")
        .otherwise("bronze")
    ).withColumn(
        "retention_risk",
        when((datediff(current_date(), col("last_order_date")) > 90) & 
             (col("total_orders") < 5), "high")
        .otherwise("low")
    )
    
    # 6. Save results
    result.write.format("delta").mode("overwrite").save("/customer_metrics")
    ```

7. How would you optimize a job that processes terabytes of data daily?

    **Optimization Strategy:**

    **1. Partition Pruning:**
    ```python
    # Partition by date for incremental processing
    df.write.partitionBy("year", "month", "day").parquet("/data")
    
    # Read only required partitions
    df = spark.read.parquet("/data").filter(col("date") == "2024-01-01")
    ```

    **2. Columnar Format:**
    ```python
    # Use Parquet/ORC with compression
    df.write.format("parquet").option("compression", "snappy").save("/data")
    ```

    **3. Predicate Pushdown:**
    ```python
    # Filter early, before joins
    df = spark.read.parquet("/large_data") \
         .filter(col("date") >= "2024-01-01") \
         .select("id", "value", "date")  # Only needed columns
    ```

    **4. Broadcast Small Tables:**
    ```python
    result = large_df.join(broadcast(dimension_table), "key")
    ```

    **5. Optimize Shuffles:**
    ```python
    # Repartition before expensive operations
    df.repartition(200, "customer_id").write.partitionBy("date").parquet("/data")
    ```

    **6. Cache Strategic DataFrames:**
    ```python
    # Cache lookup tables used multiple times
    lookup_df.cache()
    ```

    **7. Adaptive Query Execution:**
    ```python
    spark.conf.set("spark.sql.adaptive.enabled", "true")
    spark.conf.set("spark.sql.adaptive.coalescePartitions.enabled", "true")
    ```

8. How do you implement incremental data processing?

    ```python
    # Method 1: Watermark-based (for streaming-style batch)
    last_processed = spark.read.format("delta").load("/checkpoints").select(max("timestamp")).collect()[0][0]
    
    new_data = spark.read.parquet("/input") \
        .filter(col("timestamp") > last_processed)
    
    # Process new data
    processed = new_data.transform(business_logic)
    
    # Append to target
    processed.write.format("delta").mode("append").save("/output")
    
    # Update checkpoint
    new_data.select(max("timestamp").alias("timestamp")) \
        .write.format("delta").mode("overwrite").save("/checkpoints")
    
    # Method 2: Delta Lake merge (upsert)
    from delta.tables import DeltaTable
    
    target = DeltaTable.forPath(spark, "/target")
    source = spark.read.parquet("/incremental_source")
    
    target.alias("t").merge(
        source.alias("s"),
        "t.id = s.id"
    ).whenMatchedUpdateAll() \
     .whenNotMatchedInsertAll() \
     .execute()
    ```

9. How would you debug a job failing with out-of-memory errors?

    **Debugging Steps:**

    **1. Check Spark UI:**
    - Identify stage with failures
    - Check task distribution (skew?)
    - Review executor memory metrics
    - Look for GC time spikes

    **2. Common Causes & Fixes:**

    **Data Skew:**
    ```python
    # Check partition sizes
    df.rdd.glom().map(len).collect()
    
    # Repartition by multiple keys
    df = df.repartition(200, "key1", "key2")
    ```

    **Collect/Broadcast Issues:**
    ```python
    # Avoid collecting large datasets
    # df.collect()  # Don't do this on large data
    
    # Check broadcast size
    spark.conf.set("spark.sql.autoBroadcastJoinThreshold", "10485760")  # 10MB
    ```

    **Shuffle Spills:**
    ```python
    # Increase shuffle partitions
    spark.conf.set("spark.sql.shuffle.partitions", "400")
    
    # Increase executor memory
    # --executor-memory 8g
    # --executor-memoryOverhead 2g
    ```

    **Window Functions:**
    ```python
    # Partition window operations
    window = Window.partitionBy("customer_id").orderBy("date")
    # vs unbounded window (memory intensive)
    ```

    **3. Memory Tuning:**
    ```python
    spark.conf.set("spark.memory.fraction", "0.6")
    spark.conf.set("spark.memory.storageFraction", "0.5")
    ```

10. How do you implement data quality checks in production pipelines?

    ```python
    from pyspark.sql.functions import col, count, isnan, when
    
    def data_quality_checks(df, rules):
        """Run comprehensive data quality checks"""
        results = {}
        
        # 1. Null checks
        null_counts = df.select([
            count(when(col(c).isNull(), c)).alias(c) 
            for c in df.columns
        ])
        results['null_counts'] = null_counts
        
        # 2. Duplicate check
        total = df.count()
        distinct = df.dropDuplicates(["id"]).count()
        results['duplicates'] = total - distinct
        
        # 3. Range validation
        invalid_age = df.filter((col("age") < 0) | (col("age") > 120)).count()
        results['invalid_age'] = invalid_age
        
        # 4. Referential integrity
        orphaned = df.join(reference_df, "foreign_key", "left_anti").count()
        results['orphaned_records'] = orphaned
        
        # 5. Schema validation
        expected_schema = rules.get("schema")
        if df.schema != expected_schema:
            results['schema_mismatch'] = True
        
        # 6. Business rules
        invalid_total = df.filter(col("quantity") * col("price") != col("total")).count()
        results['invalid_totals'] = invalid_total
        
        # Fail pipeline if critical checks fail
        if results['duplicates'] > 0 or results['schema_mismatch']:
            raise ValueError(f"Data quality check failed: {results}")
        
        # Log warnings for non-critical issues
        if results['null_counts'].first()[0] > 100:
            print(f"Warning: High null count detected")
        
        return results
    
    # Run checks
    quality_results = data_quality_checks(df, quality_rules)
    ```

11. How would you handle streaming data from multiple sources?

    ```python
    # Read from multiple Kafka topics
    kafka_stream1 = spark.readStream \
        .format("kafka") \
        .option("kafka.bootstrap.servers", "localhost:9092") \
        .option("subscribe", "orders") \
        .load() \
        .select(from_json(col("value").cast("string"), order_schema).alias("data")) \
        .select("data.*") \
        .withColumn("source", lit("kafka_orders"))
    
    # Read from file source
    file_stream = spark.readStream \
        .schema(event_schema) \
        .json("/input/events") \
        .withColumn("source", lit("file_events"))
    
    # Union streams (must have same schema)
    unified_stream = kafka_stream1.union(file_stream)
    
    # Process unified stream
    processed = unified_stream \
        .withWatermark("timestamp", "10 minutes") \
        .groupBy(window("timestamp", "5 minutes"), "source") \
        .agg(count("*").alias("count"))
    
    # Write to sink
    query = processed.writeStream \
        .outputMode("append") \
        .format("delta") \
        .option("checkpointLocation", "/checkpoints/unified") \
        .start("/output/metrics")
    ```

12. How do you implement real-time aggregations on streaming data?

    ```python
    from pyspark.sql.functions import window, avg, sum as _sum, count
    
    # Read stream
    stream_df = spark.readStream \
        .format("kafka") \
        .option("kafka.bootstrap.servers", "localhost:9092") \
        .option("subscribe", "transactions") \
        .load()
    
    # Parse and add watermark
    parsed = stream_df \
        .select(from_json(col("value").cast("string"), schema).alias("data")) \
        .select("data.*") \
        .withWatermark("event_time", "15 minutes")
    
    # Windowed aggregations
    aggregated = parsed \
        .groupBy(
            window("event_time", "5 minutes", "1 minute"),  # 5-min window, 1-min slide
            "customer_id"
        ) \
        .agg(
            _sum("amount").alias("total_amount"),
            avg("amount").alias("avg_amount"),
            count("*").alias("transaction_count")
        )
    
    # Write with update mode (for aggregations)
    query = aggregated.writeStream \
        .outputMode("update") \
        .format("delta") \
        .option("checkpointLocation", "/checkpoints/aggregations") \
        .start("/output/real_time_metrics")
    
    # Session window (for user sessions)
    session_agg = parsed \
        .groupBy(session_window("event_time", "30 minutes"), "user_id") \
        .agg(count("*").alias("actions_per_session"))
    ```

13. How would you design a data pipeline for a reporting system?

    **Architecture:**

    ```python
    # Layer 1: Bronze (Raw Ingestion)
    def ingest_raw_data():
        raw_df = spark.read \
            .format("json") \
            .load("/landing/transactions")
        
        raw_df.write \
            .format("delta") \
            .mode("append") \
            .partitionBy("ingestion_date") \
            .save("/bronze/transactions")
    
    # Layer 2: Silver (Cleaned & Validated)
    def create_silver_layer():
        bronze_df = spark.read.format("delta").load("/bronze/transactions")
        
        cleaned_df = bronze_df \
            .dropDuplicates(["transaction_id"]) \
            .na.fill({"amount": 0, "status": "unknown"}) \
            .filter(col("amount") >= 0) \
            .withColumn("processed_date", current_date())
        
        cleaned_df.write \
            .format("delta") \
            .mode("overwrite") \
            .partitionBy("date") \
            .save("/silver/transactions")
    
    # Layer 3: Gold (Aggregated for Reporting)
    def create_gold_layer():
        silver_df = spark.read.format("delta").load("/silver/transactions")
        
        # Daily metrics
        daily_metrics = silver_df \
            .groupBy("date", "customer_segment") \
            .agg(
                _sum("amount").alias("total_revenue"),
                count("transaction_id").alias("transaction_count"),
                avg("amount").alias("avg_transaction_value")
            )
        
        daily_metrics.write \
            .format("delta") \
            .mode("overwrite") \
            .save("/gold/daily_metrics")
    
    # Orchestrate
    ingest_raw_data()
    create_silver_layer()
    create_gold_layer()
    ```

    **Benefits:** Data quality improves through layers, enables both detailed and aggregated queries.

14. How do you handle late-arriving facts in a data warehouse?

    ```python
    from delta.tables import DeltaTable
    
    # Approach 1: Merge with Delta Lake
    fact_table = DeltaTable.forPath(spark, "/warehouse/fact_sales")
    late_arrivals = spark.read.parquet("/landing/late_sales")
    
    # Upsert late arrivals
    fact_table.alias("target").merge(
        late_arrivals.alias("source"),
        "target.transaction_id = source.transaction_id"
    ).whenMatchedUpdateAll() \
     .whenNotMatchedInsertAll() \
     .execute()
    
    # Approach 2: Reprocess affected aggregates
    # Get date range of late arrivals
    affected_dates = late_arrivals.select("transaction_date").distinct()
    
    # Recompute aggregates for affected dates
    for date in affected_dates.collect():
        date_val = date[0]
        
        # Read all facts for this date (including late arrivals)
        daily_facts = spark.read.format("delta") \
            .load("/warehouse/fact_sales") \
            .filter(col("transaction_date") == date_val)
        
        # Recompute aggregates
        daily_agg = daily_facts.groupBy("date", "product_id") \
            .agg(_sum("amount").alias("total_sales"))
        
        # Overwrite aggregate partition
        daily_agg.write \
            .format("delta") \
            .mode("overwrite") \
            .option("replaceWhere", f"date = '{date_val}'") \
            .save("/warehouse/agg_daily_sales")
    
    # Approach 3: Streaming with watermark
    # Allow 7 days for late data
    stream_df.withWatermark("event_time", "7 days")
    ```

15. How would you implement data lineage tracking in PySpark?

    ```python
    # Custom lineage metadata
    def track_lineage(df, source, target, transformation):
        """Track data lineage for audit/debugging"""
        lineage_record = {
            "timestamp": datetime.now().isoformat(),
            "source": source,
            "target": target,
            "transformation": transformation,
            "record_count": df.count(),
            "columns": df.columns,
            "job_id": spark.sparkContext.applicationId
        }
        
        # Save lineage metadata
        lineage_df = spark.createDataFrame([lineage_record])
        lineage_df.write \
            .format("delta") \
            .mode("append") \
            .save("/metadata/lineage")
        
        return df
    
    # Usage in pipeline
    raw_df = spark.read.parquet("/input/raw")
    cleaned_df = raw_df.dropDuplicates()
    track_lineage(cleaned_df, "/input/raw", "/silver/cleaned", "dropDuplicates")
    
    # Delta Lake history (built-in lineage)
    from delta.tables import DeltaTable
    delta_table = DeltaTable.forPath(spark, "/delta/table")
    history = delta_table.history()  # Shows all operations, timestamps, users
    
    # Query history
    history.select("version", "timestamp", "operation", "operationMetrics").show()
    ```

    **Tools:** Apache Atlas, OpenLineage for enterprise-grade lineage tracking.

Sources
1. [Top 36 PySpark Interview Questions and Answers for 2025](https://www.datacamp.com/blog/pyspark-interview-questions)
2. [PySpark Interview Questions (2025)](https://www.youtube.com/watch?v=fOCiis31Ng4)
3. [Top PySpark Interview Questions and Answers (2025)](https://www.interviewbit.com/pyspark-interview-questions/)
4. [Lokesh Tendulkar's Post](https://www.linkedin.com/posts/certification-champs_jobinterviews-pyspark-dataengineer-activity-7149877251193344001-FYFS)
5. [60+ Must-Know Pyspark Interview Questions and Answers](https://k21academy.com/data-engineering/pyspark-interview-questions/)
6. [50 PySpark Interview Questions and Answers For 2025](https://www.projectpro.io/article/pyspark-interview-questions-and-answers/520)
7. [8 PySpark Interview Questions and How to Answer Them](https://www.coursera.org/in/articles/pyspark-interview-questions)
