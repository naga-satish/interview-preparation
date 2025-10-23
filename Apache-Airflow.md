# Comprehensive Apache Airflow Interview Questions by Topic

## Table of Contents

* [1. Airflow Fundamentals](#1-airflow-fundamentals)
* [2. DAGs (Directed Acyclic Graphs)](#2-dags-directed-acyclic-graphs)
* [3. Operators](#3-operators)
* [4. Task Dependencies and Relationships](#4-task-dependencies-and-relationships)
* [5. Scheduling and Execution](#5-scheduling-and-execution)
* [6. Airflow UI and Monitoring](#6-airflow-ui-and-monitoring)
* [7. Airflow Architecture](#7-airflow-architecture)
* [8. Airflow Configuration and Setup](#8-airflow-configuration-and-setup)
* [9. Airflow Plugins and Extensions](#9-airflow-plugins-and-extensions)
* [10. Airflow Operators and Hooks](#10-airflow-operators-and-hooks)
* [11. Airflow Testing and Debugging](#11-airflow-testing-and-debugging)
* [12. Airflow Best Practices](#12-airflow-best-practices)
* [13. Airflow Metadata Database](#13-airflow-metadata-database)
* [14. Airflow Security and Authentication](#14-airflow-security-and-authentication)
* [15. Airflow Deployment and Scaling](#15-airflow-deployment-and-scaling)
* [16. Airflow 2.x Features](#16-airflow-2x-features)
* [17. Security](#17-security)
* [18. Deployment and Scaling](#18-deployment-and-scaling)
* [19. Integration and External Systems](#19-integration-and-external-systems)
* [20. Scenario-Based Questions](#20-scenario-based-questions)

## 1. Airflow Fundamentals

### 1. What is Apache Airflow and what problems does it solve?

**Answer:** Apache Airflow is an open-source platform for programmatically authoring, scheduling, and monitoring workflows. It solves several key problems:

- **Complex workflow orchestration**: Manages dependencies between tasks in data pipelines
- **Scheduling**: Handles time-based and data-driven scheduling with cron-like expressions
- **Monitoring**: Provides visibility into pipeline execution with a rich UI
- **Failure handling**: Offers retry logic, alerting, and error recovery mechanisms
- **Scalability**: Supports distributed execution across multiple workers
- **Reproducibility**: Enables backfilling historical data and rerunning failed tasks

### 2. What are the main components of Airflow's architecture?

**Answer:** The core components are:

- **Scheduler**: Monitors DAGs and triggers task instances based on schedules and dependencies
- **Executor**: Determines how tasks are executed (SequentialExecutor, LocalExecutor, CeleryExecutor, KubernetesExecutor)
- **Webserver**: Provides the UI for monitoring DAGs, viewing logs, and managing workflows
- **Metadata Database**: Stores state of DAGs, tasks, connections, variables, and execution history
- **Worker**: Processes that execute task instances (in distributed setups)
- **DAG Directory**: File system location where DAG Python files are stored

### 3. How does Airflow differ from traditional ETL tools?

**Answer:** Key differences:

- **Code-based**: Workflows defined in Python code vs GUI-based configuration
- **Dynamic**: DAGs can be generated programmatically at runtime
- **Orchestration focus**: Airflow orchestrates but doesn't transform data itself
- **Extensible**: Supports custom operators, hooks, and plugins
- **Platform agnostic**: Can integrate with any system via operators
- **Version control**: DAGs are code, enabling Git workflows
- **Developer-centric**: Requires programming knowledge vs drag-and-drop interfaces

### 4. What are the key features of Apache Airflow?

**Answer:** Major features include:

- **Pure Python**: DAGs written in Python for maximum flexibility
- **Dynamic pipeline generation**: Create DAGs programmatically based on configuration
- **Extensible**: Custom operators, executors, and hooks
- **Rich UI**: Web interface for monitoring, logs, and manual interventions
- **Scalability**: Horizontal scaling with distributed executors
- **Backfilling**: Rerun historical data for specific date ranges
- **Integration**: 200+ pre-built operators for cloud platforms, databases, etc.
- **Task dependencies**: Complex dependency graphs with branching and conditional logic
- **Retry mechanisms**: Automatic retries with exponential backoff

### 5. When should you use Airflow versus other orchestration tools?

**Answer:** Use Airflow when:

- You need complex, code-based workflow definitions
- Your team has Python expertise
- You require extensive customization and extensibility
- You need to orchestrate across multiple systems/platforms
- You want strong community support and active development

Consider alternatives when:
- You need simple scheduled tasks (use cron)
- You want a no-code/low-code solution (use Azure Data Factory, AWS Glue)
- You need event-driven architecture (use Apache Kafka, AWS Step Functions)
- Your primary focus is CI/CD (use Jenkins, GitLab CI)

### 6. What is the history of Apache Airflow and who created it?

**Answer:** Apache Airflow was created by Maxime Beauchemin at Airbnb in 2014 to solve their growing data pipeline management challenges. It was open-sourced in June 2015 and entered the Apache Incubator in March 2016. It graduated as a top-level Apache Software Foundation project in January 2019. The tool was originally designed to address limitations in existing workflow tools, providing a programmatic, extensible, and scalable solution for workflow orchestration.

## 2. DAGs (Directed Acyclic Graphs)

### 1. What is a DAG in Airflow?

**Answer:** A DAG (Directed Acyclic Graph) is a collection of tasks organized to reflect their relationships and dependencies. It defines:

- **Tasks**: Individual units of work
- **Dependencies**: Order in which tasks execute
- **Schedule**: When and how often the workflow runs

The "directed" means edges have direction (task A → task B), and "acyclic" means no circular dependencies (prevents infinite loops).

### 2. Why must a DAG be acyclic?

**Answer:** A DAG must be acyclic to:

- **Prevent infinite loops**: Circular dependencies would cause tasks to wait indefinitely
- **Ensure deterministic execution**: Clear start and end points
- **Enable scheduling**: The scheduler needs to determine execution order
- **Guarantee completion**: All tasks must eventually finish

If task A depends on B, and B depends on C, which depends on A, the workflow cannot be resolved.

### 3. How do you define a DAG in Airflow?

**Answer:** Two main approaches:

```python
# Method 1: Context Manager (recommended)
from airflow import DAG
from datetime import datetime

with DAG(
    'my_dag',
    start_date=datetime(2025, 1, 1),
    schedule='@daily'
) as dag:
    task1 = BashOperator(task_id='task1', bash_command='echo "Hello"')
    
# Method 2: Standard Constructor
dag = DAG(
    'my_dag',
    start_date=datetime(2025, 1, 1),
    schedule='@daily'
)
task1 = BashOperator(task_id='task1', bash_command='echo "Hello"', dag=dag)
```

### 4. What are the important parameters when creating a DAG?

**Answer:** Key parameters:

- **dag_id**: Unique identifier for the DAG
- **start_date**: When the DAG should begin scheduling
- **schedule** (or schedule_interval): Frequency of execution (@daily, @hourly, cron expression)
- **catchup**: Whether to backfill past runs (default: True)
- **default_args**: Dictionary of default parameters for all tasks
- **max_active_runs**: Maximum concurrent DAG runs
- **dagrun_timeout**: Maximum time a DAG run should take
- **tags**: Labels for organizing DAGs
- **description**: Human-readable description

### 5. What is the difference between `schedule_interval` and `start_date`?

**Answer:**

- **start_date**: The timestamp from which the scheduler begins creating DAG runs. First DAG run executes for the interval starting at start_date
- **schedule_interval**: Defines the frequency/interval between DAG runs

Example: If start_date is 2025-01-01 and schedule is @daily, the first run executes on 2025-01-02 for the 2025-01-01 interval. The execution_date represents the interval start, not when it runs.

### 6. How do you handle dynamic DAG generation?

**Answer:** Generate DAGs programmatically using Python:

```python
# Generate multiple similar DAGs
for dept in ['sales', 'marketing', 'engineering']:
    dag_id = f'{dept}_pipeline'
    
    with DAG(dag_id, start_date=datetime(2025, 1, 1), schedule='@daily') as dag:
        task = PythonOperator(
            task_id=f'process_{dept}',
            python_callable=process_data,
            op_kwargs={'department': dept}
        )
    globals()[dag_id] = dag  # Register DAG
```

Best practices:
- Use configuration files (YAML/JSON) to define DAG parameters
- Keep DAG generation logic simple to avoid scheduler delays
- Limit the number of dynamically generated DAGs

### 7. What are DAG runs and task instances?

**Answer:**

- **DAG Run**: A single execution of a DAG for a specific execution date. Represents one complete workflow execution
  - Example: daily_pipeline for 2025-01-01
  
- **Task Instance**: A specific execution of a task within a DAG run
  - Example: extract_task in daily_pipeline for 2025-01-01

Relationship: One DAG run contains multiple task instances (one per task in the DAG).

### 8. How do you set default arguments for a DAG?

**Answer:**

```python
from datetime import datetime, timedelta

default_args = {
    'owner': 'data_team',
    'depends_on_past': False,
    'email': ['alerts@company.com'],
    'email_on_failure': True,
    'email_on_retry': False,
    'retries': 3,
    'retry_delay': timedelta(minutes=5),
    'execution_timeout': timedelta(hours=1)
}

with DAG(
    'my_dag',
    default_args=default_args,
    start_date=datetime(2025, 1, 1)
) as dag:
    # All tasks inherit default_args unless overridden
    pass
```

Benefits: Reduces code duplication and ensures consistent behavior across tasks.

### 9. What is `catchup` in Airflow and when would you disable it?

**Answer:** `catchup` determines whether Airflow should automatically create and execute DAG runs for intervals between start_date and the current date.

- **catchup=True** (default): Backfills all missed intervals
- **catchup=False**: Only schedules from current time forward

Disable catchup when:
- DAG processes only the latest data
- Backfilling historical data is not needed
- You're developing/testing a new DAG
- Pipeline runs only on real-time/current data

### 10. How do you pass parameters to a DAG at runtime?

**Answer:** Multiple methods:

```python
# 1. DAG Configuration (conf parameter)
# Trigger with: airflow dags trigger my_dag --conf '{"key": "value"}'
def my_task(**context):
    dag_run = context['dag_run']
    param = dag_run.conf.get('key', 'default')

# 2. Params (with validation in Airflow 2.2+)
from airflow.models.param import Param

with DAG(
    'my_dag',
    params={
        'environment': Param('dev', type='string', enum=['dev', 'prod']),
        'batch_size': Param(100, type='integer')
    }
) as dag:
    pass

# 3. Variables (for static configuration)
from airflow.models import Variable
config = Variable.get('my_config', deserialize_json=True)
```

## 3. Operators

### 1. What is an Operator in Airflow?

**Answer:** An Operator is a template for a predefined task in Airflow. It defines what work will be done, not when or how. Operators are atomic, idempotent units of work that:

- Encapsulate a single task's logic
- Are instantiated to create tasks in a DAG
- Have an execute() method that performs the actual work
- Can be built-in or custom-developed

### 2. What are the most commonly used built-in operators?

**Answer:**

- **PythonOperator**: Executes a Python function
- **BashOperator**: Runs bash commands
- **EmailOperator**: Sends emails
- **SQLOperator**: Executes SQL queries
- **SimpleHttpOperator**: Makes HTTP requests
- **DummyOperator/EmptyOperator**: Placeholder for grouping/organizing
- **BranchPythonOperator**: Implements conditional logic
- **TriggerDagRunOperator**: Triggers another DAG
- **S3Operators**: AWS S3 operations (upload, download, delete)
- **KubernetesPodOperator**: Runs containers in Kubernetes

### 3. What is the difference between PythonOperator and BashOperator?

**Answer:**

| Aspect | PythonOperator | BashOperator |
|--------|---------------|--------------|
| Language | Executes Python functions | Executes shell commands |
| Environment | Runs in Airflow's Python env | Runs in system shell |
| Error Handling | Python exceptions | Exit codes (0=success, non-0=failure) |
| Data Passing | Can return values to XCom | Stdout to XCom |
| Use Case | Python logic, API calls | File operations, CLI tools |

### 4. When would you use a DummyOperator?

**Answer:** DummyOperator (now EmptyOperator in Airflow 2.x) is used for:

- **Grouping tasks**: Creating logical groups in complex DAGs
- **Start/end markers**: Explicit DAG entry and exit points
- **Branching join points**: Combining multiple branches
- **Visual organization**: Improving DAG readability
- **Placeholder**: During development before implementing actual logic

```python
start = EmptyOperator(task_id='start')
end = EmptyOperator(task_id='end')

[task1, task2, task3] >> end
```

### 5. What is BranchPythonOperator and how does it work?

**Answer:** BranchPythonOperator enables conditional execution by choosing which downstream task(s) to execute based on logic.

```python
def choose_branch(**context):
    if context['execution_date'].day % 2 == 0:
        return 'even_day_task'
    return 'odd_day_task'

branch = BranchPythonOperator(
    task_id='branch',
    python_callable=choose_branch
)

even_task = BashOperator(task_id='even_day_task', bash_command='echo "Even"')
odd_task = BashOperator(task_id='odd_day_task', bash_command='echo "Odd"')

branch >> [even_task, odd_task]
```

The callable must return task_id(s) to execute. Unselected branches are marked as "skipped".

### 6. How do you create a custom operator?

**Answer:** Extend BaseOperator and implement the execute() method:

```python
from airflow.models import BaseOperator
from airflow.utils.decorators import apply_defaults

class MyCustomOperator(BaseOperator):
    @apply_defaults
    def __init__(self, my_param, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.my_param = my_param
    
    def execute(self, context):
        self.log.info(f"Processing {self.my_param}")
        # Your logic here
        return result  # Automatically pushed to XCom
```

Custom operators are useful for reusable, complex logic specific to your organization.

### 7. What is the difference between an Operator and a Sensor?

**Answer:**

| Operator | Sensor |
|----------|--------|
| Performs an action | Waits for a condition |
| Executes once | Polls repeatedly |
| Returns immediately | Has timeout/poke_interval |
| Example: Run query | Example: Wait for file |
| Success when task completes | Success when condition met |

Sensors are specialized operators that inherit from BaseSensorOperator.

### 8. What are the main sensor operators and their use cases?

**Answer:**

- **FileSensor**: Waits for a file to exist in a filesystem
- **S3KeySensor**: Waits for a file in S3
- **SqlSensor**: Waits for query to return rows
- **HttpSensor**: Waits for HTTP endpoint to return expected response
- **ExternalTaskSensor**: Waits for another DAG's task to complete
- **DateTimeSensor**: Waits until a specific datetime
- **TimeDeltaSensor**: Waits for a time duration
- **PythonSensor**: Custom condition via Python function

### 9. How does the PythonOperator execute functions?

**Answer:** PythonOperator calls a Python callable with context:

```python
def my_function(param1, **context):
    # Access context variables
    execution_date = context['execution_date']
    dag_run = context['dag_run']
    task_instance = context['task_instance']
    
    # Your logic
    result = process(param1)
    return result  # Automatically pushed to XCom

task = PythonOperator(
    task_id='my_task',
    python_callable=my_function,
    op_kwargs={'param1': 'value'},
    provide_context=True  # Deprecated in 2.x, always provided
)
```

The function executes in the same Python environment as the worker.

### 10. What is the SubDagOperator and when should you avoid using it?

**Answer:** SubDagOperator creates a nested DAG within another DAG for organizing complex workflows.

**Avoid SubDagOperator because:**
- **Deprecated**: Removed in Airflow 2.0+ in favor of TaskGroups
- **Performance issues**: Each SubDAG creates a full DAG run, consuming resources
- **Scheduler overhead**: Increases metadata database load
- **Deadlocks**: Can cause scheduling bottlenecks
- **Complex debugging**: Harder to troubleshoot nested structures

**Use TaskGroups instead:**
```python
from airflow.utils.task_group import TaskGroup

with TaskGroup('group1') as tg1:
    task1 = BashOperator(task_id='task1', bash_command='echo 1')
    task2 = BashOperator(task_id='task2', bash_command='echo 2')
```

## 4. Task Dependencies and Relationships

### 1. How do you define task dependencies in Airflow?

**Answer:** Multiple methods to define dependencies:

```python
# Method 1: Bitshift operators (most common)
task1 >> task2  # task1 runs before task2
task2 << task1  # same as above

# Method 2: set_downstream/set_upstream
task1.set_downstream(task2)
task2.set_upstream(task1)

# Method 3: Lists for multiple dependencies
task1 >> [task2, task3]  # task2 and task3 run after task1
[task1, task2] >> task3  # task3 runs after both task1 and task2

# Method 4: Chain (linear dependencies)
from airflow.models.baseoperator import chain
chain(task1, task2, task3, task4)  # task1 >> task2 >> task3 >> task4
```

### 2. What is the difference between `>>` and `<<` operators?

**Answer:**

- **`>>`** (right bitshift): Sets downstream dependency
  - `task1 >> task2` means task2 depends on task1
  - Read as "task1 then task2"
  
- **`<<`** (left bitshift): Sets upstream dependency
  - `task2 << task1` means task2 depends on task1
  - Read as "task2 after task1"

Both achieve the same result; `>>` is more commonly used for readability as it follows the natural execution order.

### 3. How do you create parallel tasks in Airflow?

**Answer:** Tasks are parallel when they have no dependencies between them:

```python
# All three tasks run in parallel
task1 = BashOperator(task_id='task1', bash_command='echo 1')
task2 = BashOperator(task_id='task2', bash_command='echo 2')
task3 = BashOperator(task_id='task3', bash_command='echo 3')

# Fan-out pattern: one task triggers multiple parallel tasks
start >> [task1, task2, task3]

# Fan-in pattern: multiple tasks converge to one
[task1, task2, task3] >> end
```

Parallelism is controlled by executor capacity and DAG/task concurrency settings.

### 4. What are the different ways to set task dependencies?

**Answer:**

```python
# 1. Simple linear
task1 >> task2 >> task3

# 2. Fan-out (one-to-many)
task1 >> [task2, task3, task4]

# 3. Fan-in (many-to-one)
[task1, task2, task3] >> task4

# 4. Complex dependencies
task1 >> task2
task1 >> task3
[task2, task3] >> task4

# 5. Cross-dependencies using chain
from airflow.models.baseoperator import chain, cross_downstream
cross_downstream([task1, task2], [task3, task4])
# Creates: task1 >> task3, task1 >> task4, task2 >> task3, task2 >> task4

# 6. Using depends_on_past
task = BashOperator(
    task_id='task',
    bash_command='echo "hi"',
    depends_on_past=True  # Depends on previous run's success
)
```

### 5. How do you implement branching logic in a DAG?

**Answer:** Use BranchPythonOperator or @task.branch decorator:

```python
# Method 1: BranchPythonOperator
def branch_func(**context):
    if context['dag_run'].conf.get('environment') == 'prod':
        return 'prod_task'
    return 'dev_task'

branch = BranchPythonOperator(
    task_id='branch',
    python_callable=branch_func
)

prod_task = BashOperator(task_id='prod_task', bash_command='echo "prod"')
dev_task = BashOperator(task_id='dev_task', bash_command='echo "dev"')

branch >> [prod_task, dev_task]

# Method 2: TaskFlow API (Airflow 2.0+)
@task.branch
def branch_task():
    return 'prod_task' if check_condition() else 'dev_task'
```

### 6. What is `set_upstream()` and `set_downstream()`?

**Answer:** Methods to programmatically set task dependencies:

```python
# set_downstream: this task runs before the argument
task1.set_downstream(task2)  # Equivalent to: task1 >> task2

# set_upstream: this task runs after the argument
task2.set_upstream(task1)    # Equivalent to: task2 << task1

# Useful in loops or dynamic task generation
tasks = [create_task(i) for i in range(5)]
for i in range(len(tasks) - 1):
    tasks[i].set_downstream(tasks[i + 1])
```

These methods are useful when building dependencies programmatically or when readability benefits from method calls over operators.

### 7. How do you handle conditional task execution?

**Answer:** Several approaches:

```python
# 1. BranchPythonOperator (skip unselected paths)
def choose_path(**context):
    if condition:
        return 'task_a'
    return 'task_b'

# 2. ShortCircuitOperator (stop entire downstream if False)
from airflow.operators.python import ShortCircuitOperator

check = ShortCircuitOperator(
    task_id='check',
    python_callable=lambda: should_continue()
)

# 3. Trigger Rules (control when task runs based on upstream states)
task = BashOperator(
    task_id='task',
    bash_command='echo "hi"',
    trigger_rule='one_success'  # Run if any upstream succeeds
)

# 4. TaskFlow with conditional returns
@task
def conditional_task():
    if condition:
        process_data()
```

### 8. What is task fan-out and fan-in?

**Answer:**

**Fan-out**: One task triggers multiple parallel downstream tasks
```python
start = EmptyOperator(task_id='start')
parallel_tasks = [BashOperator(task_id=f'task_{i}', bash_command=f'echo {i}') 
                  for i in range(5)]
start >> parallel_tasks  # One-to-many
```

**Fan-in**: Multiple parallel tasks converge to a single downstream task
```python
end = EmptyOperator(task_id='end')
parallel_tasks >> end  # Many-to-one
```

**Combined pattern** (common in ETL):
```python
extract >> [transform_1, transform_2, transform_3] >> load
```

### 9. How do you trigger multiple tasks based on a condition?

**Answer:**

```python
# Return list of task_ids from branch function
def choose_tasks(**context):
    tasks_to_run = []
    if context['dag_run'].conf.get('run_task1'):
        tasks_to_run.append('task1')
    if context['dag_run'].conf.get('run_task2'):
        tasks_to_run.append('task2')
    return tasks_to_run if tasks_to_run else ['default_task']

branch = BranchPythonOperator(
    task_id='branch',
    python_callable=choose_tasks
)

task1 = BashOperator(task_id='task1', bash_command='echo 1')
task2 = BashOperator(task_id='task2', bash_command='echo 2')
default = BashOperator(task_id='default_task', bash_command='echo default')

branch >> [task1, task2, default]
```

### 10. What are trigger rules and what types are available?

**Answer:** Trigger rules determine when a task should run based on upstream task states.

**Available trigger rules:**

- **all_success** (default): All upstream tasks succeeded
- **all_failed**: All upstream tasks failed
- **all_done**: All upstream tasks completed (any state)
- **all_skipped**: All upstream tasks skipped
- **one_success**: At least one upstream task succeeded
- **one_failed**: At least one upstream task failed
- **none_failed**: No upstream task failed (success or skipped)
- **none_failed_min_one_success**: No failures and at least one success
- **none_skipped**: No upstream task skipped
- **always**: Always run regardless of upstream state

```python
cleanup = BashOperator(
    task_id='cleanup',
    bash_command='rm -rf /tmp/data',
    trigger_rule='all_done'  # Run even if upstream tasks failed
)
```

## 5. XComs (Cross-Communication)

### 1. What are XComs in Airflow?

**Answer:** XComs (Cross-Communications) are a mechanism for tasks to exchange small amounts of data. Key characteristics:

- Store key-value pairs in the metadata database
- Enable task-to-task communication within a DAG run
- Scoped to a specific DAG run and task instance
- Accessible via task_instance.xcom_push() and xcom_pull()
- Automatically created when tasks return values
- Not designed for large data transfers

### 2. How do you push and pull data using XComs?

**Answer:**

```python
# Push explicitly
def push_function(**context):
    context['task_instance'].xcom_push(key='my_key', value='my_value')

# Push implicitly via return
def return_function():
    return {'data': 'value'}  # Automatically pushed with key='return_value'

# Pull in downstream task
def pull_function(**context):
    ti = context['task_instance']
    
    # Pull from specific task
    value = ti.xcom_pull(task_ids='push_task', key='my_key')
    
    # Pull return value
    data = ti.xcom_pull(task_ids='return_task')  # key defaults to 'return_value'
    
    # Pull from multiple tasks
    values = ti.xcom_pull(task_ids=['task1', 'task2'])

# TaskFlow API (Airflow 2.0+) - automatic XCom handling
@task
def push_task():
    return {'key': 'value'}

@task
def pull_task(data):  # Automatically pulls from upstream
    print(data)  # {'key': 'value'}

pull_task(push_task())
```

### 3. What are the limitations of XComs?

**Answer:**

- **Size limitations**: Stored in database; large data causes performance issues
  - Recommended max: 48KB (database-dependent)
- **Database overhead**: Each XCom is a database row, impacting performance
- **Serialization**: Only JSON-serializable data by default (or pickled)
- **No cross-DAG communication**: XComs are scoped to a single DAG run
- **Persistence**: Retained in database, requiring cleanup for old data
- **Not for files**: Use external storage (S3, GCS) for file transfers
- **Pickling security**: Pickle backend has security risks with untrusted data

### 4. When should you avoid using XComs?

**Answer:** Avoid XComs for:

- **Large datasets**: Use external storage (S3, GCS, databases) instead
- **Files/binaries**: Store in object storage, pass paths via XCom
- **DataFrames**: Save to storage, pass location
- **Cross-DAG communication**: Use external state management
- **Long-term storage**: XComs are for workflow state, not data persistence
- **High-frequency updates**: Database performance impact

**Better alternatives:**
```python
# Instead of XCom for large data
def process_data():
    df = get_large_dataframe()
    s3_path = upload_to_s3(df)
    return s3_path  # Pass location, not data

def consume_data(**context):
    s3_path = context['task_instance'].xcom_pull(task_ids='process_data')
    df = read_from_s3(s3_path)
```

### 5. How do you pass data between tasks effectively?

**Answer:** Best practices:

```python
# 1. Small metadata via XCom
@task
def get_date():
    return datetime.now().strftime('%Y-%m-%d')

# 2. File paths/URIs for large data
@task
def extract_data():
    data = fetch_large_dataset()
    path = f's3://bucket/data/{execution_date}.parquet'
    save_to_s3(data, path)
    return path

@task
def transform_data(path):
    data = read_from_s3(path)
    # Process data
    return output_path

# 3. Database tables for structured data
def extract():
    df = get_data()
    df.to_sql('staging_table', con=engine)
    return {'table': 'staging_table', 'rows': len(df)}

# 4. TaskFlow API for automatic passing
@task
def add(x, y):
    return x + y

@task
def multiply(x, y):
    return x * y

result = multiply(add(2, 3), 4)  # Automatic XCom handling
```

### 6. What is the difference between return value and xcom_push?

**Answer:**

**Return value:**
- Automatically pushed to XCom with key `'return_value'`
- Simpler, more Pythonic
- Single value per task
- Preferred method in modern Airflow

**xcom_push:**
- Explicit method call
- Can push multiple key-value pairs
- More control over XCom keys
- Useful when returning isn't possible (e.g., in operators)

```python
# Return value (implicit push)
def implicit():
    return 'data'  # Key: 'return_value'

# Explicit push (multiple values)
def explicit(**context):
    ti = context['task_instance']
    ti.xcom_push(key='metric1', value=100)
    ti.xcom_push(key='metric2', value=200)
    ti.xcom_push(key='status', value='success')

# Pull
value = ti.xcom_pull(task_ids='implicit')  # Gets 'return_value' key
metric1 = ti.xcom_pull(task_ids='explicit', key='metric1')
```

### 7. How are XComs stored in the metadata database?

**Answer:** XComs are stored in the `xcom` table with:

**Schema:**
- `key`: String identifier (default: 'return_value')
- `value`: Serialized data (JSON or Pickle)
- `task_id`: Source task identifier
- `dag_id`: DAG identifier
- `execution_date`: DAG run execution date
- `timestamp`: When XCom was created

**Storage behavior:**
- Each xcom_push creates a row
- Indexed by (dag_id, task_id, execution_date, key)
- Values serialized using JSON by default (configurable)
- Old XComs can be cleaned up via retention policies

### 8. What is the maximum recommended size for XCom data?

**Answer:**

**Recommended limits:**
- **48 KB**: General guideline for safe XCom size
- **1 MB**: Absolute maximum (database dependent)

**Why limits matter:**
- **Database performance**: Large blobs slow queries
- **Memory**: XComs loaded into worker memory
- **Serialization overhead**: JSON/Pickle processing time
- **Network transfer**: Between database and workers

**Database-specific limits:**
- **PostgreSQL**: TEXT field, practically unlimited but performance degrades
- **MySQL**: BLOB field, 64 KB default (can be larger)
- **SQLite**: No strict limit but not for production

**Best practice:** Keep XComs under 1 KB for metadata only. Use external storage for anything larger.

## 6. Executors

### 1. What is an Executor in Airflow?

**Answer:** An Executor is the component responsible for running task instances. It defines the mechanism and environment where tasks execute. The executor:

- Receives tasks from the scheduler
- Determines how and where to run them
- Reports task status back to the scheduler
- Manages worker processes/containers
- Controls parallelism and resource allocation

### 2. What are the different types of executors available?

**Answer:**

- **SequentialExecutor**: Runs one task at a time, single-threaded
- **LocalExecutor**: Runs tasks in parallel on the same machine using multiprocessing
- **CeleryExecutor**: Distributed execution using Celery workers across multiple machines
- **KubernetesExecutor**: Spins up a pod per task in Kubernetes
- **CeleryKubernetesExecutor**: Hybrid of Celery and Kubernetes
- **DaskExecutor**: Uses Dask for distributed computing
- **LocalKubernetesExecutor**: Hybrid of Local and Kubernetes (deprecated)
- **DebugExecutor**: For testing DAGs in a debugger

### 3. What is the SequentialExecutor and when is it used?

**Answer:** SequentialExecutor runs tasks one at a time in sequential order.

**Characteristics:**
- Single-threaded execution
- No parallelism
- Uses SQLite by default
- Minimal resource requirements

**When to use:**
- Development and testing only
- Learning Airflow
- Debugging DAG logic
- CI/CD pipeline testing

**Never use in production** because:
- No task parallelism
- Poor performance
- SQLite limitations
- Cannot scale

### 4. What is the LocalExecutor and how does it differ from SequentialExecutor?

**Answer:**

| Feature | SequentialExecutor | LocalExecutor |
|---------|-------------------|---------------|
| Parallelism | No | Yes |
| Execution | Sequential | Parallel (multiprocessing) |
| Database | SQLite supported | PostgreSQL/MySQL required |
| Performance | Slow | Much faster |
| Resource usage | Minimal | Moderate |
| Production use | No | Small-to-medium workloads |
| Scaling | Cannot scale | Limited to single machine |

**LocalExecutor** uses Python's multiprocessing to run tasks in parallel on a single machine. Good for moderate workloads that fit on one server.

### 5. What is the CeleryExecutor and when should you use it?

**Answer:** CeleryExecutor uses Celery, a distributed task queue, to run tasks across multiple worker machines.

**Architecture:**
- Scheduler pushes tasks to message broker (Redis/RabbitMQ)
- Worker nodes pull tasks from broker
- Workers execute tasks and report status

**When to use:**
- High task volume requiring horizontal scaling
- Need for distributed execution
- Multiple machines available for workers
- Long-running tasks
- Production environments with high concurrency

**Requirements:**
- Message broker (Redis or RabbitMQ)
- Result backend (database or Redis)
- Multiple worker machines
- More complex setup and maintenance

### 6. What is the KubernetesExecutor and what are its benefits?

**Answer:** KubernetesExecutor launches a Kubernetes pod for each task instance.

**Benefits:**
- **Isolation**: Each task runs in its own pod
- **Resource efficiency**: Pods created on-demand and terminated after completion
- **Custom environments**: Different tasks can use different Docker images
- **Scalability**: Leverages Kubernetes autoscaling
- **Resource control**: Specify CPU/memory per task
- **No worker management**: Kubernetes handles pod lifecycle

**Use cases:**
- Cloud-native deployments
- Tasks with varying resource requirements
- Need for task isolation
- Dynamic scaling requirements
- Multi-tenant environments

**Configuration:**
```python
task = PythonOperator(
    task_id='my_task',
    python_callable=my_function,
    executor_config={
        "KubernetesExecutor": {
            "image": "custom-image:latest",
            "request_memory": "2G",
            "request_cpu": "1"
        }
    }
)
```

### 7. How do you configure different executors?

**Answer:** Configure in `airflow.cfg` or via environment variables:

```ini
# airflow.cfg
[core]
executor = LocalExecutor

# For CeleryExecutor
[celery]
broker_url = redis://localhost:6379/0
result_backend = db+postgresql://user:pass@localhost/airflow

# For KubernetesExecutor
[kubernetes]
namespace = airflow
worker_container_repository = apache/airflow
worker_container_tag = 2.7.0
```

**Environment variable approach:**
```bash
export AIRFLOW__CORE__EXECUTOR=KubernetesExecutor
export AIRFLOW__CELERY__BROKER_URL=redis://localhost:6379/0
```

### 8. What are the trade-offs between different executor types?

**Answer:**

| Executor | Pros | Cons | Best For |
|----------|------|------|----------|
| Sequential | Simple, minimal setup | No parallelism, slow | Development only |
| Local | Parallel, simple setup | Single machine limit | Small production |
| Celery | Distributed, scalable | Complex setup, requires broker | High-volume production |
| Kubernetes | Isolated, auto-scaling, efficient | K8s complexity, startup overhead | Cloud-native, varying workloads |

**Decision factors:**
- **Scale**: Sequential < Local < Celery/Kubernetes
- **Complexity**: Sequential < Local < Kubernetes < Celery
- **Resource efficiency**: Kubernetes > Local > Celery > Sequential
- **Isolation**: Kubernetes > Celery > Local > Sequential
- **Cost**: Sequential < Local < Celery ≈ Kubernetes

### 9. Can you use multiple executors in a single Airflow instance?

**Answer:** 

**Airflow 2.0+**: Yes, through the **CeleryKubernetesExecutor**, which allows tasks to use either Celery or Kubernetes executors.

```python
# Task uses Celery executor
celery_task = PythonOperator(
    task_id='celery_task',
    python_callable=fast_function,
    queue='celery'
)

# Task uses Kubernetes executor
k8s_task = PythonOperator(
    task_id='k8s_task',
    python_callable=resource_intensive_function,
    queue='kubernetes',
    executor_config={
        "KubernetesExecutor": {
            "request_memory": "4G"
        }
    }
)
```

**Before Airflow 2.0**: Only one executor per Airflow instance. Required separate Airflow deployments for different executors.

## 7. Scheduler

### 1. What is the role of the Airflow Scheduler?

**Answer:** The Scheduler is the brain of Airflow, responsible for:

- **DAG parsing**: Reads and parses DAG files from the DAG directory
- **Creating DAG runs**: Generates DAG run instances based on schedules
- **Task scheduling**: Determines which tasks are ready to run
- **Task submission**: Sends tasks to the executor
- **State monitoring**: Tracks task and DAG run states
- **Dependency resolution**: Ensures tasks run in correct order
- **Heartbeat monitoring**: Checks task instance health

It continuously monitors the metadata database and DAG folder to keep workflows running on schedule.

### 2. How does the Scheduler determine which tasks to run?

**Answer:** The Scheduler uses multiple criteria:

1. **Schedule interval**: Checks if DAG run should be created based on timing
2. **Dependencies**: Verifies all upstream tasks completed successfully
3. **Trigger rules**: Evaluates task's trigger_rule conditions
4. **State**: Only schedules tasks in "None" or "scheduled" state
5. **DAG/task concurrency**: Respects max_active_runs, max_active_tasks limits
6. **Pool slots**: Ensures pool capacity available
7. **Priority weight**: Orders tasks when multiple are ready
8. **Executor capacity**: Queues tasks if executor at max capacity

**Process:**
```
Parse DAGs → Create DAG runs → Check dependencies → Check constraints 
→ Submit to executor → Monitor state
```

### 3. What is the difference between the Scheduler and the Executor?

**Answer:**

| Scheduler | Executor |
|-----------|----------|
| **What**: Determines which tasks to run | **How**: Runs the tasks |
| Monitors DAG runs and task states | Executes task instances |
| Creates task instances | Manages worker processes/pods |
| Checks dependencies and schedules | Reports completion status |
| Single component per Airflow | Can have multiple workers |
| Lives on scheduler machine | Can be distributed |

**Relationship:** Scheduler is the brain (decides), Executor is the muscle (executes).

### 4. How does the Scheduler handle task retries?

**Answer:** When a task fails, the Scheduler:

1. **Checks retry configuration**: Looks at task's `retries` parameter
2. **Increments try_number**: Tracks current attempt count
3. **Applies retry_delay**: Waits specified duration before retry
4. **Exponential backoff** (optional): Increases delay with each retry
5. **Re-schedules task**: Sets task state to "up_for_retry"
6. **Executor re-runs**: Task executed again when retry_delay expires
7. **Max retries**: Marks as "failed" if all retries exhausted

```python
task = BashOperator(
    task_id='task',
    bash_command='risky_command.sh',
    retries=3,
    retry_delay=timedelta(minutes=5),
    retry_exponential_backoff=True,
    max_retry_delay=timedelta(hours=1)
)
```

### 5. What happens if the Scheduler goes down?

**Answer:**

**Immediate impact:**
- New DAG runs are not created
- Tasks are not scheduled
- Running tasks continue to execute
- Executor keeps running current tasks
- Webserver remains accessible (read-only state)

**Recovery:**
- Restart Scheduler
- Automatically catches up on missed schedules (if catchup=True)
- Resumes normal operation
- No data loss (state in database)

**High Availability (Airflow 2.0+):**
- Multiple schedulers can run simultaneously
- Automatic failover if one scheduler fails
- Distributed scheduling for better performance

### 6. How do you optimize Scheduler performance?

**Answer:** Multiple strategies:

**1. DAG parsing optimization:**
```python
# Reduce DAG file complexity
# Avoid heavy imports at module level
# Use dynamic DAG generation sparingly

# Configure parsing interval
[scheduler]
min_file_process_interval = 30  # Parse DAGs every 30 seconds
dag_dir_list_interval = 300      # Scan directory every 5 minutes
```

**2. Database optimization:**
- Use connection pooling
- Add database indexes
- Regular cleanup of old metadata
- Use PostgreSQL over MySQL

**3. Scheduler configuration:**
```ini
[scheduler]
max_threads = 2              # Parsing threads
scheduler_heartbeat_sec = 5  # Faster task detection
parsing_processes = 2        # Parallel DAG parsing
```

**4. DAG design:**
- Reduce number of DAGs
- Minimize task count per DAG
- Use appropriate schedule intervals
- Disable unnecessary DAGs

**5. Hardware:**
- More CPU cores for parsing
- Faster disk I/O for database
- Sufficient RAM

### 7. What is the Scheduler's relationship with the metadata database?

**Answer:** The Scheduler heavily relies on the metadata database:

**Read operations:**
- DAG and task metadata
- Task instance states
- DAG run information
- Connections and variables
- Pool and queue configurations

**Write operations:**
- Creates new DAG runs
- Updates task instance states
- Records task logs and XComs
- Stores execution metadata

**Performance considerations:**
- Scheduler queries database every heartbeat (default: 5 seconds)
- Database performance directly impacts scheduling speed
- Connection pooling critical for performance
- Database locks can cause scheduling delays
- Regular cleanup necessary to prevent table bloat

**Critical tables:**
- `dag_run`: DAG run instances
- `task_instance`: Task execution records
- `dag`: DAG metadata
- `xcom`: Cross-task communication
- `job`: Scheduler and executor jobs

### 8. How frequently does the Scheduler parse DAG files?

**Answer:** Controlled by configuration parameters:

```ini
[scheduler]
# Minimum interval between parsing same DAG file
min_file_process_interval = 30  # seconds (default)

# How often to scan DAG directory for new/changed files
dag_dir_list_interval = 300     # seconds (default: 5 minutes)
```

**Parsing behavior:**
- **Initial parse**: All DAGs parsed on Scheduler startup
- **Periodic re-parsing**: Each DAG re-parsed based on min_file_process_interval
- **File monitoring**: Directory scanned per dag_dir_list_interval
- **Modification detection**: Uses file modification time
- **Multiple schedulers**: Coordinate via database to avoid duplicate parsing

**Optimization tips:**
- Increase intervals for stable DAGs to reduce CPU usage
- Decrease for development/testing environments
- Use `airflow dags list-import-errors` to check for issues
- Keep DAG files lightweight to speed parsing

## 8. Hooks and Connections

### 1. What are Hooks in Airflow?

**Answer:** Hooks are interfaces to external systems and services. They provide a consistent way to interact with databases, APIs, cloud services, and other platforms.

**Key characteristics:**
- Reusable connection logic
- Abstract away authentication and connection details
- Used by Operators to interact with external systems
- Handle connection lifecycle (open, execute, close)
- Support connection pooling and retry logic

**Example:**
```python
from airflow.providers.postgres.hooks.postgres import PostgresHook

hook = PostgresHook(postgres_conn_id='my_postgres_conn')
records = hook.get_records("SELECT * FROM users")
```

### 2. How do Hooks differ from Operators?

**Answer:**

| Hooks | Operators |
|-------|-----------|
| **Interface** to external systems | **Task** that performs work |
| Reusable connection logic | Encapsulates business logic |
| Low-level, programmatic use | High-level, declarative |
| Return data/connections | Execute and manage state |
| Used within operators/functions | Used in DAG definitions |
| Example: PostgresHook | Example: PostgresOperator |

**Relationship:**
```python
# Operator uses Hook internally
class PostgresOperator(BaseOperator):
    def execute(self, context):
        hook = PostgresHook(postgres_conn_id=self.postgres_conn_id)
        hook.run(self.sql)

# Direct Hook usage in Python function
def my_function():
    hook = PostgresHook(postgres_conn_id='postgres_default')
    return hook.get_first("SELECT COUNT(*) FROM users")[0]
```

### 3. What are some commonly used Hooks?

**Answer:**

**Database Hooks:**
- PostgresHook, MySqlHook, SqliteHook
- SnowflakeHook, RedshiftHook, BigQueryHook

**Cloud Provider Hooks:**
- S3Hook, GCSHook, AzureBlobStorageHook
- AwsGlueHook, DataprocHook

**API Hooks:**
- HttpHook, SlackHook
- JiraHook, SalesforceHook

**Other:**
- SparkSubmitHook, HiveHook
- DockerHook, KubernetesHook
- SFTPHook, FTPHook

### 4. How do you create a custom Hook?

**Answer:** Extend BaseHook and implement connection logic:

```python
from airflow.hooks.base import BaseHook

class CustomApiHook(BaseHook):
    def __init__(self, custom_conn_id='custom_default'):
        self.conn_id = custom_conn_id
        self.connection = self.get_connection(custom_conn_id)
    
    def get_conn(self):
        """Return connection object"""
        import requests
        session = requests.Session()
        session.headers.update({
            'Authorization': f'Bearer {self.connection.password}'
        })
        return session
    
    def get_data(self, endpoint):
        """Custom method to fetch data"""
        conn = self.get_conn()
        url = f"{self.connection.host}/{endpoint}"
        response = conn.get(url)
        response.raise_for_status()
        return response.json()

# Usage
hook = CustomApiHook(custom_conn_id='my_api')
data = hook.get_data('users')
```

### 5. What are Connections in Airflow?

**Answer:** Connections store credentials and configuration for external systems. They contain:

- **Connection ID**: Unique identifier
- **Connection Type**: postgres, http, aws, etc.
- **Host**: Server address
- **Schema/Database**: Database name
- **Login**: Username
- **Password**: Password/API key
- **Port**: Connection port
- **Extra**: JSON for additional parameters

Connections decouple credentials from code, enabling environment-specific configurations.

### 6. How do you configure and manage Connections?

**Answer:** Multiple methods:

**1. Airflow UI:**
- Admin → Connections → Add Connection
- Fill in connection details
- Click Save

**2. Airflow CLI:**
```bash
airflow connections add 'my_postgres' \
    --conn-type 'postgres' \
    --conn-host 'localhost' \
    --conn-login 'airflow' \
    --conn-password 'password' \
    --conn-port 5432 \
    --conn-schema 'airflow_db'
```

**3. Environment Variables:**
```bash
export AIRFLOW_CONN_MY_POSTGRES='postgresql://airflow:password@localhost:5432/airflow_db'
```

**4. Secrets Backend:**
```python
# Configure in airflow.cfg
[secrets]
backend = airflow.providers.amazon.aws.secrets.secrets_manager.SecretsManagerBackend
backend_kwargs = {"connections_prefix": "airflow/connections"}
```

**5. Programmatically:**
```python
from airflow.models import Connection
from airflow.settings import Session

conn = Connection(
    conn_id='my_conn',
    conn_type='postgres',
    host='localhost',
    login='user',
    password='pass',
    port=5432
)
session = Session()
session.add(conn)
session.commit()
```

### 7. Where are Connections stored?

**Answer:**

**Default storage:** Metadata database in the `connection` table

**Alternative backends:**
- **Environment variables**: AIRFLOW_CONN_* format
- **AWS Secrets Manager**: Encrypted cloud storage
- **Google Secret Manager**: GCP encrypted storage
- **Azure Key Vault**: Azure encrypted storage
- **HashiCorp Vault**: Enterprise secret management
- **Local secrets backend**: File-based secrets

**Storage hierarchy** (priority order):
1. Secrets backend (if configured)
2. Environment variables
3. Metadata database

Passwords are encrypted using Fernet key when stored in database.

### 8. How do you securely manage credentials in Airflow?

**Answer:** Best practices:

**1. Use Secrets Backends:**
```ini
# airflow.cfg
[secrets]
backend = airflow.providers.amazon.aws.secrets.secrets_manager.SecretsManagerBackend
backend_kwargs = {"connections_prefix": "airflow/connections"}
```

**2. Enable Fernet encryption:**
```bash
# Generate Fernet key
python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"

# Set in airflow.cfg
[core]
fernet_key = your_generated_key
```

**3. Environment variables for sensitive values:**
```bash
export AIRFLOW__CORE__SQL_ALCHEMY_CONN=postgresql://user:${DB_PASSWORD}@host/db
```

**4. Use Connections instead of hardcoding:**
```python
# Bad
password = "hardcoded_password"

# Good
hook = PostgresHook(postgres_conn_id='postgres_conn')
```

**5. Rotate credentials regularly**

**6. Restrict UI access** with RBAC

**7. Use IAM roles** for cloud services instead of keys

**8. Mask sensitive logs:**
```python
[core]
hide_sensitive_var_conn_fields = True
```

### 9. What is the relationship between Hooks and Connections?

**Answer:**

**Connections** store credentials → **Hooks** use them to connect

```python
# 1. Connection defined (UI, CLI, or env var)
# conn_id: 'my_db'
# conn_type: 'postgres'
# host: 'db.example.com'
# login: 'user'
# password: 'secret'

# 2. Hook retrieves and uses connection
hook = PostgresHook(postgres_conn_id='my_db')

# 3. Hook internally does:
connection = BaseHook.get_connection('my_db')
conn_object = psycopg2.connect(
    host=connection.host,
    user=connection.login,
    password=connection.password,
    database=connection.schema
)
```

**Flow:** Connection (stored credentials) → Hook (connection interface) → External System

Hooks abstract the connection logic, while Connections store the configuration.

## 9. Variables and Configurations

### 1. What are Variables in Airflow?

**Answer:** Variables are key-value pairs stored in Airflow's metadata database for storing configuration and state information accessible across DAGs and tasks.

**Characteristics:**
- Global scope (accessible from any DAG)
- Stored in metadata database
- Can be managed via UI, CLI, or code
- Support JSON serialization for complex data
- Encrypted if sensitive (when key contains certain keywords)

### 2. How do you create and access Variables?

**Answer:**

**Create:**
```python
# Via code
from airflow.models import Variable
Variable.set("my_key", "my_value")
Variable.set("config", {"env": "prod", "region": "us-east-1"}, serialize_json=True)

# Via CLI
# airflow variables set my_key my_value
# airflow variables set config '{"env": "prod"}' --json

# Via UI: Admin → Variables → Add Variable
```

**Access:**
```python
# Method 1: Variable.get()
from airflow.models import Variable
value = Variable.get("my_key")
config = Variable.get("config", deserialize_json=True)
default_value = Variable.get("missing_key", default_var="default")

# Method 2: In templates (Jinja)
bash_task = BashOperator(
    task_id='task',
    bash_command='echo {{ var.value.my_key }}'
)

# Method 3: JSON variables
bash_task = BashOperator(
    task_id='task',
    bash_command='echo {{ var.json.config.env }}'
)
```

### 3. What is the difference between Variables and XComs?

**Answer:**

| Variables | XComs |
|-----------|-------|
| Global across DAGs | Scoped to DAG run |
| Persistent until deleted | Cleaned up with DAG run |
| Configuration/settings | Task-to-task data passing |
| Set manually or programmatically | Set by task execution |
| Few, long-lived values | Many, transient values |
| Example: API endpoints, config | Example: Processing results |

**Use Variables for:** Environment configs, feature flags, API keys
**Use XComs for:** Passing data between tasks in a workflow

### 4. How are Variables stored?

**Answer:**

**Storage location:** `variable` table in metadata database

**Schema:**
- `key`: Variable name (unique)
- `val`: Variable value (text)
- `is_encrypted`: Boolean flag
- `description`: Optional description

**Encryption:** Variables with keys containing keywords (password, secret, token, etc.) are automatically encrypted using Fernet key.

**Retrieval:** Each Variable.get() queries the database, which can impact performance if called frequently.

### 5. What are environment variables in Airflow?

**Answer:** Environment variables configure Airflow behavior and can override airflow.cfg settings.

**Format:** `AIRFLOW__{SECTION}__{KEY}`

```bash
# Override configuration
export AIRFLOW__CORE__EXECUTOR=LocalExecutor
export AIRFLOW__CORE__LOAD_EXAMPLES=False
export AIRFLOW__DATABASE__SQL_ALCHEMY_CONN=postgresql://user:pass@localhost/airflow

# Set Airflow home
export AIRFLOW_HOME=/path/to/airflow

# Access in DAGs
import os
env = os.getenv('ENVIRONMENT', 'dev')
```

**Hierarchy:** Environment variables > airflow.cfg > defaults

### 6. How do you manage configurations across different environments?

**Answer:** Multiple strategies:

**1. Environment variables:**
```bash
# dev.env
export AIRFLOW__CORE__EXECUTOR=SequentialExecutor
export DB_HOST=localhost

# prod.env
export AIRFLOW__CORE__EXECUTOR=CeleryExecutor
export DB_HOST=prod-db.company.com
```

**2. Airflow Variables with environment prefix:**
```python
env = os.getenv('ENVIRONMENT', 'dev')
db_host = Variable.get(f"{env}_db_host")
```

**3. Separate airflow.cfg files:**
```bash
# Use different config per environment
AIRFLOW_CONFIG=/path/to/prod/airflow.cfg airflow webserver
```

**4. Secrets backends per environment:**
```ini
# Dev uses local
[secrets]
backend = airflow.secrets.local_filesystem.LocalFilesystemBackend

# Prod uses AWS
[secrets]
backend = airflow.providers.amazon.aws.secrets.secrets_manager.SecretsManagerBackend
```

**5. DAG configuration files:**
```python
import yaml
env = os.getenv('ENV', 'dev')
with open(f'config/{env}.yaml') as f:
    config = yaml.safe_load(f)
```

### 7. What is the airflow.cfg file?

**Answer:** `airflow.cfg` is the main configuration file for Airflow, containing settings for all components.

**Key sections:**
- **[core]**: Fundamental settings (executor, DAG folder, timezone)
- **[database]**: Database connection settings
- **[webserver]**: Web UI configuration
- **[scheduler]**: Scheduler parameters
- **[celery]**: Celery executor settings
- **[kubernetes]**: Kubernetes executor settings
- **[logging]**: Log configuration
- **[smtp]**: Email settings

**Location:** `$AIRFLOW_HOME/airflow.cfg` (default: ~/airflow/)

**Example settings:**
```ini
[core]
dags_folder = /opt/airflow/dags
executor = LocalExecutor
load_examples = False
default_timezone = America/New_York

[webserver]
web_server_port = 8080
workers = 4

[scheduler]
max_threads = 2
catchup_by_default = False
```

### 8. How do you override default Airflow configurations?

**Answer:** Three methods in priority order:

**1. Environment variables (highest priority):**
```bash
export AIRFLOW__CORE__EXECUTOR=KubernetesExecutor
export AIRFLOW__WEBSERVER__WEB_SERVER_PORT=9090
```

**2. airflow.cfg file:**
```ini
[core]
executor = LocalExecutor

[webserver]
web_server_port = 8080
```

**3. Default values (lowest priority):** Built into Airflow code

**Override patterns:**
```bash
# Command line override
airflow config get-value core executor

# Runtime programmatic override (not recommended)
from airflow.configuration import conf
conf.set('core', 'executor', 'LocalExecutor')

# Docker container
docker run -e AIRFLOW__CORE__EXECUTOR=CeleryExecutor apache/airflow

# Kubernetes ConfigMap
apiVersion: v1
kind: ConfigMap
metadata:
  name: airflow-config
data:
  AIRFLOW__CORE__EXECUTOR: "KubernetesExecutor"
```

**Best practice:** Use environment variables for environment-specific settings, airflow.cfg for defaults.

## 10. Monitoring and Logging

### 1. How do you monitor DAG execution in Airflow?

**Answer:** Multiple monitoring approaches:

**1. Airflow UI:**
- DAG overview page (success/failure rates)
- Tree view (historical runs)
- Graph view (task dependencies and states)
- Gantt chart (task duration and overlap)
- Task duration graph

**2. REST API:**
```python
import requests
response = requests.get(
    'http://airflow:8080/api/v1/dags/my_dag/dagRuns',
    auth=('username', 'password')
)
```

**3. CLI:**
```bash
airflow dags list
airflow dags state my_dag 2025-01-01
airflow tasks states-for-dag-run my_dag 2025-01-01
```

**4. Database queries:**
```sql
SELECT state, COUNT(*) 
FROM dag_run 
WHERE dag_id = 'my_dag' 
GROUP BY state;
```

**5. Metrics and monitoring tools:**
- StatsD integration
- Prometheus exporter
- Datadog, New Relic integrations

### 2. What information is available in the Airflow UI?

**Answer:**

**DAG Views:**
- **Grid View**: Task states across multiple runs
- **Graph View**: Task dependencies and current states
- **Calendar View**: Success/failure patterns over time
- **Task Duration**: Historical task execution times
- **Gantt Chart**: Task parallelization and timing
- **Code**: DAG source code

**DAG Details:**
- Schedule interval and next run time
- Last run status
- Task success/failure statistics
- Recent task instances

**System Information:**
- Active DAG runs
- Task instance states
- Pool utilization
- Configuration settings
- Connection and Variable management

**Admin Features:**
- User management (RBAC)
- Connection configuration
- Variable management
- XCom browser
- Job status (scheduler, workers)

### 3. How do you access task logs?

**Answer:**

**1. Airflow UI:**
- Click on task in Graph/Grid view
- Select "Log" button
- View real-time streaming logs

**2. CLI:**
```bash
airflow tasks logs my_dag my_task 2025-01-01
airflow tasks logs my_dag my_task 2025-01-01 --try-number 2
```

**3. Direct file access:**
```bash
# Local logs
cat $AIRFLOW_HOME/logs/my_dag/my_task/2025-01-01/1.log

# Navigate to log directory
cd $AIRFLOW_HOME/logs/dag_id/task_id/execution_date/
```

**4. REST API:**
```python
response = requests.get(
    'http://airflow:8080/api/v1/dags/my_dag/dagRuns/run_id/taskInstances/task_id/logs/1',
    auth=('user', 'pass')
)
```

**5. Remote logging (S3, GCS):**
```python
from airflow.providers.amazon.aws.hooks.s3 import S3Hook
hook = S3Hook()
log = hook.read_key('logs/dag/task/date/try.log', 'bucket')
```

### 4. Where are Airflow logs stored by default?

**Answer:**

**Default location:** `$AIRFLOW_HOME/logs/`

**Directory structure:**
```
logs/
├── dag_id/
│   └── task_id/
│       └── execution_date/
│           └── try_number.log
└── scheduler/
    └── latest/
        └── scheduler.log
```

**Example:**
```
$AIRFLOW_HOME/logs/
├── my_etl_dag/
│   ├── extract_data/
│   │   └── 2025-01-01T00:00:00+00:00/
│   │       ├── 1.log
│   │       └── 2.log (retry)
│   └── transform_data/
│       └── 2025-01-01T00:00:00+00:00/
│           └── 1.log
└── scheduler/
    └── latest/
```

**Component-specific logs:**
- **Scheduler**: `logs/scheduler/`
- **Webserver**: stdout/stderr or configured location
- **Workers**: `logs/` (task execution logs)

### 5. How do you configure remote logging?

**Answer:**

**AWS S3:**
```ini
# airflow.cfg
[logging]
remote_logging = True
remote_base_log_folder = s3://my-bucket/airflow/logs
remote_log_conn_id = aws_default
encrypt_s3_logs = True
```

**Google Cloud Storage:**
```ini
[logging]
remote_logging = True
remote_base_log_folder = gs://my-bucket/airflow/logs
remote_log_conn_id = google_cloud_default
```

**Azure Blob Storage:**
```ini
[logging]
remote_logging = True
remote_base_log_folder = wasb-logs://logs-container/airflow/logs
remote_log_conn_id = azure_default
```

**Benefits:**
- Centralized log storage
- Logs persist beyond task/pod lifecycle
- Accessible from multiple schedulers/workers
- Scalable storage
- Integration with log analysis tools

**Setup steps:**
1. Configure cloud storage connection
2. Update airflow.cfg
3. Install provider package (e.g., `apache-airflow-providers-amazon`)
4. Restart Airflow components

### 6. What metrics should you monitor in production Airflow?

**Answer:**

**Performance Metrics:**
- **Scheduler heartbeat**: Scheduler health and responsiveness
- **DAG processing time**: Time to parse and process DAGs
- **Task queue size**: Backlog of tasks waiting to execute
- **Task execution time**: Duration trends for tasks
- **Pool slot usage**: Resource utilization

**Reliability Metrics:**
- **Task success rate**: Percentage of successful tasks
- **DAG run success rate**: Percentage of successful DAG runs
- **Task failure count**: Failed tasks over time
- **Retry count**: Tasks requiring retries
- **SLA misses**: Tasks exceeding SLA thresholds

**Resource Metrics:**
- **Database connection pool**: Available vs. used connections
- **Worker count**: Active workers vs. required
- **CPU and memory usage**: Resource consumption
- **Disk space**: Available storage for logs
- **Database size**: Metadata database growth

**System Health:**
- **Scheduler uptime**: Availability of scheduler
- **Worker availability**: Active vs. offline workers
- **Database response time**: Query performance
- **DAG file parse errors**: Broken DAGs

### 7. How do you set up alerting in Airflow?

**Answer:**

**1. Email alerts:**
```python
default_args = {
    'email': ['team@company.com'],
    'email_on_failure': True,
    'email_on_retry': False,
    'email_on_success': False
}

# SMTP configuration in airflow.cfg
[smtp]
smtp_host = smtp.gmail.com
smtp_port = 587
smtp_user = alerts@company.com
smtp_password = password
smtp_mail_from = airflow@company.com
```

**2. Callbacks:**
```python
def failure_callback(context):
    # Send to Slack, PagerDuty, etc.
    send_slack_notification(context)

task = PythonOperator(
    task_id='task',
    python_callable=my_function,
    on_failure_callback=failure_callback,
    on_success_callback=success_callback,
    on_retry_callback=retry_callback
)
```

**3. SLA misses:**
```python
def sla_miss_callback(dag, task_list, blocking_task_list, slas, blocking_tis):
    send_alert(f"SLA missed for tasks: {task_list}")

dag = DAG(
    'my_dag',
    sla_miss_callback=sla_miss_callback
)

task = BashOperator(
    task_id='task',
    bash_command='sleep 10',
    sla=timedelta(seconds=5)  # Alert if exceeds 5 seconds
)
```

**4. External monitoring:**
```python
# StatsD metrics
from airflow.stats import Stats
Stats.incr('custom_metric.task_failure')

# Prometheus exporter
# Install: pip install apache-airflow[prometheus]
```

**5. Third-party integrations:**
- Slack via SlackWebhookOperator
- PagerDuty via custom callbacks
- Datadog, New Relic APM
- CloudWatch, Stackdriver

### 8. What is the role of the Webserver component?

**Answer:** The Webserver provides the user interface and API for interacting with Airflow.

**Responsibilities:**
- **UI rendering**: Displays DAG graphs, logs, metrics
- **User authentication**: Login and RBAC enforcement
- **REST API**: Programmatic access to Airflow
- **DAG management**: Trigger runs, pause/unpause DAGs
- **Configuration**: Manage connections, variables, pools
- **Monitoring**: Real-time status and historical data

**Does NOT:**
- Execute tasks (that's the executor)
- Schedule DAGs (that's the scheduler)
- Parse DAG files (scheduler does this)

**Architecture:**
- Runs as separate process from scheduler
- Uses Flask framework
- Can run multiple instances with load balancer
- Reads from metadata database (no writes to task states)

**Configuration:**
```ini
[webserver]
web_server_port = 8080
workers = 4  # Gunicorn workers
worker_class = sync
expose_config = False  # Hide config in UI
```

### 9. How do you troubleshoot failed tasks?

**Answer:**

**Step 1: Check task logs**
```bash
# UI: Click task → View Log
# CLI:
airflow tasks logs dag_id task_id execution_date
```

**Step 2: Review task instance details**
- Check error message in UI
- Review task duration (timeout?)
- Check try number (retries exhausted?)

**Step 3: Examine dependencies**
```bash
# Check upstream task states
airflow tasks states-for-dag-run dag_id execution_date
```

**Step 4: Test task locally**
```bash
# Run task in isolation
airflow tasks test dag_id task_id execution_date
```

**Step 5: Check resources**
- Database connection limits
- Memory/CPU availability
- Pool capacity
- Executor queue size

**Step 6: Review recent changes**
- Code changes in DAG
- Configuration updates
- Dependency version changes

**Step 7: Common issues:**
- Import errors in DAG file
- Connection misconfiguration
- Resource exhaustion
- External service failures
- Timeout exceeded
- Incorrect trigger rule

**Step 8: Fix and retry**
```bash
# Clear failed task to retry
airflow tasks clear dag_id --task-regex task_id --start-date 2025-01-01
```

## 11. Error Handling and Retries

### 1. How do you handle task failures in Airflow?

**Answer:** Multiple strategies:

**1. Automatic retries:**
```python
task = BashOperator(
    task_id='task',
    bash_command='risky_command.sh',
    retries=3,
    retry_delay=timedelta(minutes=5)
)
```

**2. Callbacks:**
```python
def on_failure(context):
    # Alert team, log to external system
    send_alert(context['task_instance'].task_id)

task = PythonOperator(
    task_id='task',
    python_callable=function,
    on_failure_callback=on_failure
)
```

**3. Trigger rules:**
```python
cleanup = BashOperator(
    task_id='cleanup',
    bash_command='cleanup.sh',
    trigger_rule='all_done'  # Run even if upstream failed
)
```

**4. Exception handling in code:**
```python
def my_function():
    try:
        risky_operation()
    except SpecificError as e:
        log.error(f"Error: {e}")
        # Handle gracefully or raise
        raise
```

**5. DAG-level failure handling:**
```python
dag = DAG(
    'my_dag',
    on_failure_callback=dag_failure_callback,
    default_args={'retries': 2}
)
```

### 2. What are retry parameters and how do you configure them?

**Answer:**

**Key retry parameters:**

```python
task = BashOperator(
    task_id='task',
    bash_command='command.sh',
    
    # Number of retry attempts
    retries=3,
    
    # Delay between retries
    retry_delay=timedelta(minutes=5),
    
    # Enable exponential backoff
    retry_exponential_backoff=True,
    
    # Maximum retry delay
    max_retry_delay=timedelta(hours=1),
    
    # Execution timeout
    execution_timeout=timedelta(hours=2)
)
```

**DAG-level defaults:**
```python
default_args = {
    'retries': 2,
    'retry_delay': timedelta(minutes=5)
}

dag = DAG('my_dag', default_args=default_args)
```

**Configuration hierarchy:**
- Task-level overrides DAG-level defaults
- DAG-level overrides global config

### 3. What is the difference between `retries` and `retry_delay`?

**Answer:**

**retries:**
- Number of times to retry a failed task
- Type: Integer
- Default: 0 (no retries)
- Example: `retries=3` means up to 3 retry attempts after initial failure

**retry_delay:**
- Time to wait before each retry
- Type: timedelta
- Default: timedelta(seconds=300) (5 minutes)
- Example: `retry_delay=timedelta(minutes=10)` waits 10 min between retries

**Combined example:**
```python
task = BashOperator(
    task_id='task',
    bash_command='flaky_api_call.sh',
    retries=3,              # Try up to 3 more times
    retry_delay=timedelta(minutes=5)  # Wait 5 min between each
)
# Execution timeline: Fail → Wait 5min → Retry 1 → Fail → Wait 5min → Retry 2 → etc.
```

### 4. What is `retry_exponential_backoff`?

**Answer:** Exponential backoff increases retry delay exponentially with each attempt, preventing overwhelming of failing systems.

**Formula:** `retry_delay * (2 ^ (retry_number - 1))`

```python
task = BashOperator(
    task_id='task',
    bash_command='api_call.sh',
    retries=4,
    retry_delay=timedelta(minutes=1),
    retry_exponential_backoff=True,
    max_retry_delay=timedelta(hours=1)
)

# Retry timeline:
# Attempt 1 fails → Wait 1 min (1 * 2^0)
# Attempt 2 fails → Wait 2 min (1 * 2^1)
# Attempt 3 fails → Wait 4 min (1 * 2^2)
# Attempt 4 fails → Wait 8 min (1 * 2^3)
```

**Benefits:**
- Reduces load on failing external systems
- Increases chance of recovery for transient issues
- More efficient than fixed delays

**Use max_retry_delay** to cap maximum wait time.

### 5. How do you implement custom error handling logic?

**Answer:**

**1. Python function with try/except:**
```python
def process_with_error_handling(**context):
    try:
        result = risky_operation()
        return result
    except ConnectionError:
        # Retry later by raising exception
        raise
    except ValueError as e:
        # Log and skip
        logging.error(f"Invalid data: {e}")
        return None  # Success with no data
    except Exception as e:
        # Alert team
        send_alert(f"Unexpected error: {e}")
        raise
```

**2. Custom operator:**
```python
class RobustOperator(BaseOperator):
    def execute(self, context):
        try:
            return self.perform_operation()
        except SpecificError as e:
            self.handle_specific_error(e, context)
        finally:
            self.cleanup()
```

**3. Callback functions:**
```python
def custom_failure_handler(context):
    task = context['task_instance']
    if task.try_number == task.max_tries:
        # Final failure
        escalate_to_oncall(task)
    else:
        # Still retrying
        log_to_monitoring(task)

task = PythonOperator(
    task_id='task',
    python_callable=function,
    on_failure_callback=custom_failure_handler
)
```

**4. ShortCircuitOperator for conditional skip:**
```python
def should_continue(**context):
    if not data_available():
        return False  # Skip downstream
    return True

gate = ShortCircuitOperator(
    task_id='check_data',
    python_callable=should_continue
)
```

### 6. What are SLAs in Airflow?

**Answer:** SLA (Service Level Agreement) is the maximum expected execution time for a task. Airflow sends alerts when tasks exceed their SLA.

```python
from datetime import timedelta

task = BashOperator(
    task_id='task',
    bash_command='long_process.sh',
    sla=timedelta(hours=2)  # Alert if exceeds 2 hours
)

# DAG-level SLA callback
def sla_miss_callback(dag, task_list, blocking_task_list, slas, blocking_tis):
    message = f"SLA missed for {task_list} in DAG {dag.dag_id}"
    send_pager_duty_alert(message)

dag = DAG(
    'my_dag',
    sla_miss_callback=sla_miss_callback
)
```

**Key points:**
- SLA measured from **execution_date**, not start time
- SLA miss doesn't stop task execution
- Used for monitoring, not enforcement
- Email sent to `email` contacts by default
- Custom callbacks for advanced alerting

### 7. How do you configure timeout for tasks?

**Answer:**

**execution_timeout:**
```python
from datetime import timedelta

task = PythonOperator(
    task_id='task',
    python_callable=my_function,
    execution_timeout=timedelta(hours=1)  # Kill if exceeds 1 hour
)
```

**Different timeout types:**

**1. Task execution timeout:**
```python
task = BashOperator(
    task_id='task',
    bash_command='sleep 100',
    execution_timeout=timedelta(seconds=30)  # Kills after 30s
)
```

**2. DAG run timeout:**
```python
dag = DAG(
    'my_dag',
    dagrun_timeout=timedelta(hours=4)  # Entire DAG must complete in 4h
)
```

**3. Sensor timeout:**
```python
sensor = FileSensor(
    task_id='wait_for_file',
    filepath='/data/file.csv',
    timeout=3600,  # 1 hour
    poke_interval=60  # Check every minute
)
```

**Behavior:**
- Task killed when timeout exceeded
- Marked as "failed"
- Retries apply (if configured)
- Use for preventing hung tasks

### 8. What is `on_failure_callback` and `on_success_callback`?

**Answer:** Callback functions executed when task fails or succeeds.

**on_failure_callback:**
```python
def task_failure_alert(context):
    task_instance = context['task_instance']
    exception = context.get('exception')
    
    message = f"""
    Task Failed:
    - DAG: {context['dag'].dag_id}
    - Task: {task_instance.task_id}
    - Execution Date: {context['execution_date']}
    - Try Number: {task_instance.try_number}
    - Error: {exception}
    """
    
    send_slack_alert(message)
    create_jira_ticket(task_instance.task_id)

task = PythonOperator(
    task_id='critical_task',
    python_callable=important_function,
    on_failure_callback=task_failure_alert
)
```

**on_success_callback:**
```python
def task_success_handler(context):
    task_instance = context['task_instance']
    
    # Log metrics
    duration = (task_instance.end_date - task_instance.start_date).total_seconds()
    log_metric('task_duration', duration)
    
    # Notify stakeholders
    send_email(f"Task {task_instance.task_id} completed successfully")

task = PythonOperator(
    task_id='task',
    python_callable=function,
    on_success_callback=task_success_handler
)
```

**Additional callbacks:**
- **on_retry_callback**: Called before retry attempts
- **on_execute_callback**: Called before task execution
- **sla_miss_callback**: When SLA exceeded

### 9. How do you send alerts when tasks fail?

**Answer:**

**1. Email (built-in):**
```python
default_args = {
    'email': ['team@company.com', 'manager@company.com'],
    'email_on_failure': True
}

# Configure SMTP in airflow.cfg
[smtp]
smtp_host = smtp.gmail.com
smtp_port = 587
smtp_user = alerts@company.com
smtp_password = app_password
```

**2. Slack:**
```python
from airflow.providers.slack.operators.slack_webhook import SlackWebhookOperator

def slack_failure_alert(context):
    slack_msg = f"""
    :red_circle: Task Failed
    *DAG*: {context['dag'].dag_id}
    *Task*: {context['task_instance'].task_id}
    *Execution Time*: {context['execution_date']}
    """
    
    SlackWebhookOperator(
        task_id='slack_alert',
        http_conn_id='slack_webhook',
        message=slack_msg
    ).execute(context)

task = PythonOperator(
    task_id='task',
    python_callable=function,
    on_failure_callback=slack_failure_alert
)
```

**3. PagerDuty:**
```python
def pagerduty_alert(context):
    import requests
    
    payload = {
        'routing_key': 'your_routing_key',
        'event_action': 'trigger',
        'payload': {
            'summary': f"Airflow task {context['task_instance'].task_id} failed",
            'severity': 'error',
            'source': 'airflow'
        }
    }
    
    requests.post('https://events.pagerduty.com/v2/enqueue', json=payload)
```

**4. Custom webhook:**
```python
def webhook_alert(context):
    import requests
    
    data = {
        'dag_id': context['dag'].dag_id,
        'task_id': context['task_instance'].task_id,
        'execution_date': str(context['execution_date']),
        'error': str(context.get('exception'))
    }
    
    requests.post('https://your-webhook.com/alerts', json=data)
```

### 10. What is task state and what are the different task states?

**Answer:** Task state represents the current status of a task instance.

**All task states:**

- **none**: Task not yet queued
- **scheduled**: Queued, waiting for execution
- **queued**: Sent to executor
- **running**: Currently executing
- **success**: Completed successfully
- **failed**: Failed (no more retries)
- **up_for_retry**: Failed but will retry
- **up_for_reschedule**: Sensor in reschedule mode
- **upstream_failed**: Upstream task failed
- **skipped**: Skipped by branching or short-circuit
- **removed**: Task removed from DAG
- **deferred**: Waiting for external trigger (async)
- **restarting**: Worker lost, being rescheduled

**State transitions:**
```
none → scheduled → queued → running → success
                              ↓
                            failed → up_for_retry → queued → running
                              ↓
                          (max retries) → failed
```

**Query states:**
```python
from airflow.models import TaskInstance
from airflow.utils.state import State

# Check task state
ti = TaskInstance(task=task, execution_date=date)
if ti.state == State.FAILED:
    # Handle failure
```

## 12. Best Practices and Performance

### 1. What are the best practices for writing efficient DAGs?

**Answer:**

**1. Keep DAG files lightweight:**
```python
# Bad: Heavy computation at module level
df = pd.read_csv('large_file.csv')  # Runs on every parse

# Good: Computation inside tasks
@task
def load_data():
    return pd.read_csv('large_file.csv')
```

**2. Use appropriate schedule intervals:**
```python
# Don't over-schedule
dag = DAG('hourly_dag', schedule='@hourly')  # Only if needed hourly
```

**3. Set proper concurrency:**
```python
dag = DAG(
    'my_dag',
    max_active_runs=3,  # Limit concurrent DAG runs
    max_active_tasks=10  # Limit parallel tasks
)
```

**4. Use pools for resource management:**
```python
task = BashOperator(
    task_id='db_task',
    bash_command='query.sh',
    pool='database_pool'  # Limit concurrent DB connections
)
```

**5. Implement idempotency:**
```python
# Ensure tasks can be rerun safely
def idempotent_task():
    # Use upsert instead of insert
    df.to_sql('table', engine, if_exists='replace')
```

**6. Use TaskFlow API for simplicity:**
```python
@task
def extract():
    return data

@task
def transform(data):
    return processed_data

transform(extract())
```

### 2. How do you avoid anti-patterns in Airflow?

**Answer:**

**Anti-patterns to avoid:**

**1. Top-level code in DAG files:**
```python
# Bad: Runs on every parse
db_connection = create_db_connection()
data = fetch_data()

# Good: Inside tasks
@task
def fetch_data():
    db_connection = create_db_connection()
    return fetch_data()
```

**2. Using datetime.now():**
```python
# Bad: Non-deterministic
start_date = datetime.now()

# Good: Use context or fixed date
start_date = datetime(2025, 1, 1)
# Or in task:
execution_date = context['execution_date']
```

**3. Large XCom data:**
```python
# Bad: Passing DataFrames via XCom
return large_dataframe

# Good: Pass file paths
path = save_to_s3(large_dataframe)
return path
```

**4. Using SubDagOperator:**
```python
# Bad: SubDAG (deprecated)
subdag = SubDagOperator(...)

# Good: TaskGroup
with TaskGroup('group') as tg:
    task1 >> task2
```

**5. Excessive retries without backoff:**
```python
# Bad: Hammering failing service
retries=100, retry_delay=timedelta(seconds=1)

# Good: Exponential backoff
retries=5, retry_delay=timedelta(minutes=5), retry_exponential_backoff=True
```

**6. Not setting execution_timeout:**
```python
# Bad: Task can run forever
task = PythonOperator(...)

# Good: Set timeout
task = PythonOperator(..., execution_timeout=timedelta(hours=1))
```

### 3. What is idempotency and why is it important in Airflow?

**Answer:** Idempotency means a task produces the same result regardless of how many times it's executed with the same inputs.

**Why important:**
- **Retries**: Tasks may fail and retry multiple times
- **Backfills**: Historical data processing
- **Recovery**: Rerunning failed workflows
- **Data consistency**: Prevents duplicate or corrupted data

**Non-idempotent example:**
```python
# Bad: Multiple runs create duplicates
def append_data():
    df = get_data()
    df.to_sql('table', engine, if_exists='append')  # Duplicates on retry
```

**Idempotent examples:**
```python
# Good: Replace/upsert pattern
def replace_data():
    df = get_data()
    df.to_sql('table', engine, if_exists='replace')  # Same result on retry

# Good: Delete then insert
def upsert_data(**context):
    date = context['execution_date'].date()
    delete_query = f"DELETE FROM table WHERE date = '{date}'"
    engine.execute(delete_query)
    df = get_data()
    df.to_sql('table', engine, if_exists='append')

# Good: Check before action
def conditional_insert():
    if not record_exists():
        insert_record()
```

### 4. How do you make your Airflow tasks idempotent?

**Answer:**

**1. Use execution_date for partitioning:**
```python
@task
def process_data(**context):
    date = context['execution_date'].date()
    # Process only data for this specific date
    output_path = f's3://bucket/data/date={date}/'
    # Overwrite partition
    df.write.mode('overwrite').save(output_path)
```

**2. Delete before insert:**
```python
def load_data(**context):
    date = context['execution_date'].date()
    # Remove existing data for this date
    hook = PostgresHook()
    hook.run(f"DELETE FROM table WHERE date = '{date}'")
    # Insert fresh data
    hook.insert_rows('table', rows)
```

**3. Use MERGE/UPSERT:**
```python
def upsert_data():
    query = """
    MERGE INTO target t
    USING source s ON t.id = s.id
    WHEN MATCHED THEN UPDATE SET ...
    WHEN NOT MATCHED THEN INSERT ...
    """
    hook.run(query)
```

**4. Conditional execution:**
```python
def idempotent_file_creation(**context):
    date = context['execution_date'].date()
    filepath = f'/data/{date}.csv'
    
    if os.path.exists(filepath):
        os.remove(filepath)  # Remove if exists
    
    create_file(filepath)
```

**5. Use unique identifiers:**
```python
def process_with_id(**context):
    run_id = f"{context['dag_run'].run_id}_{context['task_instance'].task_id}"
    # Use run_id for deduplication
```

### 5. How do you optimize DAG performance?

**Answer:**

**1. Parallelization:**
```python
# Process multiple partitions in parallel
@task
def process_partition(partition_id):
    return process(partition_id)

# Fan-out pattern
results = [process_partition(i) for i in range(10)]
```

**2. Reduce task count:**
```python
# Bad: One task per file
for file in files:
    task = PythonOperator(task_id=f'process_{file}', ...)

# Good: Batch processing
@task
def process_files():
    for file in files:
        process(file)
```

**3. Use appropriate executors:**
```python
# KubernetesExecutor for resource-intensive tasks
task = PythonOperator(
    task_id='heavy_task',
    executor_config={
        'KubernetesExecutor': {
            'request_memory': '4Gi',
            'request_cpu': '2'
        }
    }
)
```

**4. Optimize database queries:**
```python
# Use indexes, limit results
hook.get_records("SELECT * FROM large_table WHERE date = %s LIMIT 1000", (date,))
```

**5. Enable smart sensors (Airflow 2.0+):**
```ini
[sensors]
use_smart_sensor = True
```

**6. Tune scheduler:**
```ini
[scheduler]
max_threads = 4
min_file_process_interval = 60
```

### 6. What are the recommendations for DAG file organization?

**Answer:**

**1. Directory structure:**
```
dags/
├── common/
│   ├── __init__.py
│   ├── operators.py
│   ├── hooks.py
│   └── utils.py
├── etl/
│   ├── daily_sales_dag.py
│   ├── user_events_dag.py
│   └── inventory_dag.py
├── ml/
│   ├── model_training_dag.py
│   └── prediction_dag.py
└── config/
    ├── dev.yaml
    └── prod.yaml
```

**2. Naming conventions:**
```python
# Clear, descriptive names
dag_id = 'sales_etl_daily'  # Good
dag_id = 'dag1'  # Bad
```

**3. Separate concerns:**
```python
# common/operators.py
class CustomOperator(BaseOperator):
    pass

# etl/sales_dag.py
from common.operators import CustomOperator
```

**4. Use configuration files:**
```python
import yaml

with open('config/prod.yaml') as f:
    config = yaml.safe_load(f)

dag = DAG(
    'my_dag',
    default_args=config['default_args']
)
```

**5. One DAG per file (usually):**
```python
# sales_dag.py contains only sales_dag
# users_dag.py contains only users_dag
```

**6. Version control:**
- Keep all DAGs in Git
- Use branches for development
- Tag releases
- Code review for changes

### 7. How do you handle large data processing in Airflow?

**Answer:**

**1. Don't process data in Airflow:**
```python
# Bad: Loading large data into Airflow worker
df = pd.read_csv('huge_file.csv')

# Good: Trigger external processing
SparkSubmitOperator(
    task_id='process_data',
    application='spark_job.py'
)
```

**2. Use appropriate tools:**
```python
# Spark for big data
spark_task = SparkSubmitOperator(...)

# Snowflake for warehouse processing
snowflake_task = SnowflakeOperator(
    sql="INSERT INTO target SELECT * FROM source WHERE ..."
)
```

**3. Pass references, not data:**
```python
@task
def extract_data():
    df = get_large_dataframe()
    s3_path = upload_to_s3(df)
    return s3_path  # Return path, not data

@task
def transform_data(s3_path):
    df = read_from_s3(s3_path)
    # Process
```

**4. Partition data:**
```python
@task
def process_partition(partition_date):
    # Process only one day's data
    df = read_partition(partition_date)
    process(df)

# Create tasks for each partition
dates = get_date_range()
[process_partition(date) for date in dates]
```

**5. Use KubernetesPodOperator:**
```python
k8s_task = KubernetesPodOperator(
    task_id='process_large_data',
    image='my-processing-image',
    cmds=['python', 'process.py'],
    resources={
        'request_memory': '16Gi',
        'request_cpu': '8'
    }
)
```

### 8. What should you avoid putting in DAG definition files?

**Answer:**

**Avoid:**

**1. Heavy computations:**
```python
# Bad: Runs on every parse
result = expensive_calculation()
```

**2. Database/API calls:**
```python
# Bad: Called during parsing
users = db.query("SELECT * FROM users")
```

**3. File I/O:**
```python
# Bad: Reads file on parse
config = json.load(open('config.json'))

# Better: Use Variables
config = Variable.get('config', deserialize_json=True)
```

**4. datetime.now():**
```python
# Bad: Non-deterministic
start_date = datetime.now()

# Good: Fixed or use context
start_date = datetime(2025, 1, 1)
```

**5. Variable/Connection queries in loops:**
```python
# Bad: Multiple DB queries
for i in range(100):
    val = Variable.get(f'var_{i}')

# Good: Single query
config = Variable.get('all_configs', deserialize_json=True)
```

**6. Complex logic:**
```python
# Bad: Complex business logic at top level
if condition:
    for item in items:
        # Complex processing

# Good: Move to functions/tasks
```

**Safe to include:**
- DAG definition
- Task definitions
- Import statements
- Static configuration
- Helper function definitions (not calls)

### 9. How do you manage DAG complexity?

**Answer:**

**1. Use TaskGroups:**
```python
with TaskGroup('extract_group') as extract:
    extract_db = PythonOperator(...)
    extract_api = PythonOperator(...)

with TaskGroup('transform_group') as transform:
    clean = PythonOperator(...)
    aggregate = PythonOperator(...)

extract >> transform
```

**2. Break into smaller DAGs:**
```python
# Instead of one massive DAG, create multiple
# - data_extraction_dag
# - data_transformation_dag
# - data_loading_dag

# Coordinate with TriggerDagRunOperator or Datasets
```

**3. Dynamic task generation:**
```python
def create_tasks(sources):
    tasks = []
    for source in sources:
        task = PythonOperator(
            task_id=f'process_{source}',
            python_callable=process,
            op_kwargs={'source': source}
        )
        tasks.append(task)
    return tasks
```

**4. Use helper functions:**
```python
def create_etl_pipeline(source, destination):
    extract = PythonOperator(...)
    transform = PythonOperator(...)
    load = PythonOperator(...)
    return extract >> transform >> load

pipeline1 = create_etl_pipeline('source1', 'dest1')
pipeline2 = create_etl_pipeline('source2', 'dest2')
```

**5. Documentation:**
```python
dag = DAG(
    'complex_dag',
    description='Detailed description of DAG purpose',
    doc_md="""
    # Complex DAG Documentation
    
    ## Purpose
    Processes daily sales data...
    """
)
```

### 10. What are the guidelines for task granularity?

**Answer:**

**Right-sized tasks:**

**Too granular (bad):**
```python
read_file = PythonOperator(task_id='read')
parse_json = PythonOperator(task_id='parse')
validate = PythonOperator(task_id='validate')
filter = PythonOperator(task_id='filter')
# 100+ tiny tasks
```

**Too coarse (bad):**
```python
# One task does everything
do_everything = PythonOperator(task_id='all')
# Hard to debug, retry, or parallelize
```

**Good balance:**
```python
extract = PythonOperator(task_id='extract')  # Get data
transform = PythonOperator(task_id='transform')  # Process data
load = PythonOperator(task_id='load')  # Save results
```

**Guidelines:**

**1. Logical boundaries:**
- Separate concerns (extract, transform, load)
- Different systems (DB read vs API call)

**2. Failure isolation:**
- Can fail independently
- Meaningful retry boundaries

**3. Reusability:**
- Can be reused in other workflows
- Modular components

**4. Parallelization:**
- Tasks that can run concurrently are separate

**5. Monitoring:**
- Each task has clear success/failure state
- Easy to identify bottlenecks

**Rule of thumb:** 5-20 tasks per DAG is typical. If more, consider TaskGroups or multiple DAGs.

## 13. Metadata Database

### 1. What is the metadata database in Airflow?

**Answer:** The metadata database is the central repository storing all Airflow state and configuration. It's the source of truth for DAG definitions, task states, execution history, connections, variables, and user information.

### 2. What information is stored in the metadata database?

**Answer:** Key data includes:
- **DAG metadata**: DAG definitions, schedules, owners
- **DAG runs**: Execution instances with states and timestamps  
- **Task instances**: Individual task executions and states
- **Connections**: Credentials for external systems
- **Variables**: Key-value configuration pairs
- **XComs**: Inter-task communication data
- **Pools**: Resource allocation configurations
- **Users and roles**: RBAC information
- **Job history**: Scheduler and executor activity
- **Logs**: Task execution logs metadata
- **SLA misses**: SLA violation records

### 3. Which databases are supported as metadata backends?

**Answer:**
- **PostgreSQL** (recommended for production)
- **MySQL** (supported, some limitations)
- **SQLite** (development/testing only)

**Not supported in production:**
- SQLite (no concurrent writes, not scalable)

**Cloud options:**
- Amazon RDS (PostgreSQL/MySQL)
- Google Cloud SQL
- Azure Database

### 4. Why should you avoid using SQLite in production?

**Answer:**
- **No concurrency**: Single-writer limitation
- **File-based**: Not suitable for distributed systems
- **No HA**: Single point of failure
- **Performance**: Poor with multiple users
- **Locking issues**: Frequent database locks
- **No remote access**: Local file only
- **Limited by Airflow**: Forced to use SequentialExecutor

**Use PostgreSQL instead** for production deployments.

### 5. How does Airflow interact with the metadata database?

**Answer:** Airflow components continuously interact with the database:
- **Scheduler**: Reads DAG definitions, writes task states, queries for tasks to schedule
- **Webserver**: Reads all data for UI display
- **Executor**: Updates task states
- **Workers**: Read task details, update execution status, write XComs

Uses **SQLAlchemy ORM** for database operations with connection pooling for performance.

### 6. What happens if the metadata database is unavailable?

**Answer:**
- **Scheduler stops**: Cannot schedule new tasks
- **Tasks fail**: Running tasks may fail when trying to update status
- **Webserver fails**: UI becomes inaccessible or shows errors
- **No new DAG runs**: Workflow execution halts

**Recovery:** Restart components once database is restored; state preserved.

**Mitigation:** Use database HA (replication, failover) and regular backups.

### 7. How do you backup the metadata database?

**Answer:**
```bash
# PostgreSQL backup
pg_dump -h localhost -U airflow airflow_db > airflow_backup.sql

# Restore
psql -h localhost -U airflow airflow_db < airflow_backup.sql

# Automated backups
# 1. Database provider's automated backups (RDS, Cloud SQL)
# 2. Scheduled backup scripts
# 3. Continuous replication to standby
```

**Best practices:**
- Daily automated backups
- Test restore procedures
- Store backups off-site
- Retain multiple backup versions
- Include in disaster recovery plan

### 8. What are the performance considerations for the metadata database?

**Answer:**
- **Connection pooling**: Configure adequate pool size
- **Indexes**: Ensure proper indexes on frequently queried columns
- **Database size**: Regular cleanup of old data prevents bloat
- **IOPS**: Ensure sufficient disk I/O capacity
- **Query optimization**: Monitor slow queries
- **Vacuum/analyze**: Regular maintenance (PostgreSQL)
- **Resource allocation**: Adequate CPU/memory for database server
- **Network latency**: Low latency between Airflow and database

## 14. Triggers and Sensors

### 1. What are Sensors in Airflow?

**Answer:** Sensors are special operators that wait for a specific condition to be met before proceeding. They continuously check (poke) for a condition and succeed when the condition is true.

**Characteristics:**
- Poll at regular intervals (poke_interval)
- Have timeout limits
- Can run in poke or reschedule mode
- Block worker slots while waiting (poke mode)
- Used for external dependencies

### 2. What is the difference between a Sensor and a regular Operator?

**Answer:**
| Sensor | Regular Operator |
|--------|------------------|
| Waits for condition | Executes action |
| Polls repeatedly | Runs once |
| Has poke_interval | No polling |
| Success when condition met | Success when completes |
| Can timeout waiting | Execution timeout only |

### 3. What is `poke_interval` and `timeout` in Sensors?

**Answer:**
- **poke_interval**: Seconds between condition checks (default: 60)
- **timeout**: Maximum seconds to wait before failing (default: 604800 = 7 days)

```python
sensor = FileSensor(
    task_id='wait_for_file',
    filepath='/data/file.csv',
    poke_interval=30,  # Check every 30 seconds
    timeout=3600       # Fail after 1 hour
)
```

### 4. What are the different sensor modes (poke vs reschedule)?

**Answer:**
**Poke mode (default):**
- Continuously occupies a worker slot
- Sleeps between pokes
- Faster response when condition met
- Resource-intensive for long waits

**Reschedule mode:**
- Frees worker slot between pokes
- Re-queued for each check
- Better for long wait times
- Slight delay in detection

```python
sensor = FileSensor(
    task_id='wait',
    filepath='/data/file.csv',
    mode='reschedule',  # or 'poke'
    poke_interval=300
)
```

### 5. When should you use reschedule mode?

**Answer:** Use reschedule when:
- Wait time is long (hours/days)
- Many concurrent sensors
- Limited worker resources
- Condition checks are infrequent

Use poke when:
- Quick wait times (minutes)
- Fast response needed
- Sufficient worker capacity

### 6. What are some commonly used Sensors?

**Answer:**
- **FileSensor**: Wait for file existence
- **S3KeySensor**: Wait for S3 object
- **HttpSensor**: Wait for HTTP endpoint response
- **SqlSensor**: Wait for SQL query results
- **ExternalTaskSensor**: Wait for another DAG's task
- **DateTimeSensor**: Wait until specific datetime
- **TimeDeltaSensor**: Wait for duration
- **PythonSensor**: Custom condition logic

### 7. How do you create a custom Sensor?

**Answer:**
```python
from airflow.sensors.base import BaseSensorOperator

class CustomSensor(BaseSensorOperator):
    def __init__(self, check_param, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.check_param = check_param
    
    def poke(self, context):
        # Return True when condition met, False otherwise
        return self.check_condition(self.check_param)
    
    def check_condition(self, param):
        # Your logic here
        return True  # or False

# Usage
sensor = CustomSensor(
    task_id='custom_wait',
    check_param='value',
    poke_interval=60,
    timeout=3600
)
```

### 8. What is the FileSensor and when would you use it?

**Answer:** FileSensor waits for a file to appear in the filesystem.

```python
wait_for_file = FileSensor(
    task_id='wait_for_csv',
    filepath='/data/daily_sales.csv',
    fs_conn_id='fs_default',
    poke_interval=60,
    timeout=3600
)
```

**Use cases:**
- Waiting for file uploads from external systems
- File-based batch processing triggers
- Data availability checks before processing
- Integration with file-based workflows

### 9. What is the ExternalTaskSensor?

**Answer:** ExternalTaskSensor waits for a task in another DAG to complete.

```python
wait_for_upstream = ExternalTaskSensor(
    task_id='wait_for_etl',
    external_dag_id='upstream_dag',
    external_task_id='final_task',
    execution_date_fn=lambda dt: dt,  # Same execution date
    timeout=3600
)
```

**Use cases:**
- DAG dependencies
- Cross-workflow coordination
- Sequential pipeline orchestration
- Ensuring upstream data availability

### 10. How do Sensors impact scheduler performance?

**Answer:**
**Negative impacts:**
- **Worker slot occupation** (poke mode): Blocks resources while waiting
- **Frequent database queries**: Each poke checks database
- **Scheduler overhead**: Managing sensor states
- **Task queue buildup**: Many sensors can delay other tasks

**Mitigation strategies:**
- Use reschedule mode for long waits
- Increase poke_interval for less frequent checks
- Use smart sensors (Airflow 2.2+) to consolidate sensor checks
- Limit concurrent sensors with pools
- Consider event-driven alternatives (deferrable operators in Airflow 2.2+)

## 15. Advanced Topics

### 1. What are Pools in Airflow and why are they used?

**Answer:** Pools limit concurrency for a group of tasks, controlling resource usage.

```python
# Create pool via UI or CLI
# airflow pools set database_pool 5 "Database connection pool"

task = PythonOperator(
    task_id='db_query',
    python_callable=query_db,
    pool='database_pool',  # Limited to 5 concurrent tasks
    pool_slots=2  # This task uses 2 slots
)
```

**Use cases:**
- Limit database connections
- Control API rate limits
- Manage external resource access
- Prevent resource exhaustion

### 2. How do you implement priority weighting for tasks?

**Answer:**
```python
critical_task = PythonOperator(
    task_id='critical',
    python_callable=function,
    priority_weight=10,  # Higher = higher priority
    weight_rule='downstream'  # or 'upstream', 'absolute'
)

normal_task = PythonOperator(
    task_id='normal',
    python_callable=function,
    priority_weight=1
)
```

**Weight rules:**
- **downstream**: Sum of task + all downstream weights
- **upstream**: Sum of task + all upstream weights
- **absolute**: Only task's own weight

### 3. What are DAG dependencies and how do you implement them?

**Answer:** DAG dependencies ensure one DAG completes before another starts.

```python
# Method 1: ExternalTaskSensor
wait = ExternalTaskSensor(
    task_id='wait_for_upstream_dag',
    external_dag_id='upstream_dag',
    external_task_id=None,  # Wait for entire DAG
    execution_date_fn=lambda dt: dt
)

# Method 2: TriggerDagRunOperator
trigger = TriggerDagRunOperator(
    task_id='trigger_downstream',
    trigger_dag_id='downstream_dag'
)

# Method 3: Datasets (Airflow 2.4+)
from airflow.datasets import Dataset

# Producer DAG
@task(outlets=[Dataset('s3://bucket/data')])
def produce():
    save_data()

# Consumer DAG
with DAG('consumer', schedule=[Dataset('s3://bucket/data')]):
    process_data()
```

### 4. What is the TriggerDagRunOperator?

**Answer:** Operator that triggers another DAG's execution.

```python
trigger = TriggerDagRunOperator(
    task_id='trigger_ml_pipeline',
    trigger_dag_id='ml_training_dag',
    conf={'model_version': '2.0'},  # Pass configuration
    wait_for_completion=True,  # Wait for triggered DAG to finish
    poke_interval=60
)
```

**Use cases:**
- Sequential DAG execution
- Event-driven workflows
- Modular pipeline orchestration

### 5. How do you implement dynamic task generation?

**Answer:**
```python
# Method 1: Loop
for i in range(5):
    task = PythonOperator(
        task_id=f'task_{i}',
        python_callable=process,
        op_kwargs={'partition': i}
    )

# Method 2: TaskFlow
@task
def process_item(item):
    return process(item)

items = ['a', 'b', 'c']
results = [process_item(item) for item in items]

# Method 3: Dynamic from config
import yaml
config = yaml.safe_load(open('config.yaml'))

for source in config['sources']:
    PythonOperator(
        task_id=f'extract_{source}',
        python_callable=extract,
        op_kwargs={'source': source}
    )
```

### 6. What are Airflow Plugins?

**Answer:** Plugins extend Airflow's functionality by adding custom components.

**Plugin components:**
- Custom operators
- Custom hooks
- Custom sensors
- Macros and functions
- UI views and menu links
- Blueprints

**Structure:**
```python
# plugins/my_plugin.py
from airflow.plugins_manager import AirflowPlugin

class MyPlugin(AirflowPlugin):
    name = "my_plugin"
    operators = [MyCustomOperator]
    hooks = [MyCustomHook]
    macros = [my_macro]
```

### 7. How do you create and use custom plugins?

**Answer:**
```python
# plugins/my_plugin.py
from airflow.plugins_manager import AirflowPlugin
from airflow.models import BaseOperator

class MyOperator(BaseOperator):
    def execute(self, context):
        # Implementation
        pass

class MyPlugin(AirflowPlugin):
    name = "my_plugin"
    operators = [MyOperator]

# Usage in DAG
from airflow.operators.my_plugin import MyOperator

task = MyOperator(task_id='my_task')
```

Place in `$AIRFLOW_HOME/plugins/` directory. Airflow automatically discovers and loads plugins.

### 8. What is TaskFlow API and how does it differ from traditional operators?

**Answer:** TaskFlow API (@task decorator) simplifies DAG authoring with Python functions.

**Traditional:**
```python
def extract():
    return data

def transform(data):
    return processed

extract_task = PythonOperator(task_id='extract', python_callable=extract)
transform_task = PythonOperator(
    task_id='transform',
    python_callable=transform,
    op_kwargs={'data': "{{ task_instance.xcom_pull(task_ids='extract') }}"}
)
extract_task >> transform_task
```

**TaskFlow:**
```python
@task
def extract():
    return data

@task
def transform(data):
    return processed

transform(extract())  # Automatic XCom and dependency
```

**Benefits:**
- Cleaner syntax
- Automatic XCom handling
- Type hints support
- Less boilerplate
- Better IDE support

### 9. What are Datasets in Airflow 2.0+?

**Answer:** Datasets (Airflow 2.4+) enable data-aware scheduling where DAGs trigger based on data updates rather than time.

```python
from airflow.datasets import Dataset

# Producer DAG - updates dataset
@task(outlets=[Dataset('s3://bucket/sales_data')])
def update_sales_data():
    # Update data
    save_to_s3()

# Consumer DAG - triggers when dataset updated
with DAG(
    'process_sales',
    schedule=[Dataset('s3://bucket/sales_data')],  # Data-driven schedule
    start_date=datetime(2025, 1, 1)
):
    @task
    def process():
        # Process when sales data updated
        pass

# Multiple dataset dependencies
with DAG(
    'combined',
    schedule=[Dataset('s3://sales'), Dataset('s3://inventory')]  # AND logic
):
    process()
```

### 10. How do you implement data-aware scheduling?

**Answer:**
```python
from airflow.datasets import Dataset

# Define datasets
sales_dataset = Dataset('s3://bucket/sales/')
inventory_dataset = Dataset('s3://bucket/inventory/')

# Producer DAG 1
with DAG('update_sales', schedule='@hourly') as dag1:
    @task(outlets=[sales_dataset])
    def update_sales():
        # Update sales data
        pass

# Producer DAG 2  
with DAG('update_inventory', schedule='@daily') as dag2:
    @task(outlets=[inventory_dataset])
    def update_inventory():
        # Update inventory
        pass

# Consumer DAG - runs when BOTH datasets updated
with DAG(
    'generate_report',
    schedule=[sales_dataset, inventory_dataset],  # Waits for both
    start_date=datetime(2025, 1, 1)
) as consumer_dag:
    @task
    def generate_report():
        # Process both datasets
        pass
```

Benefits: Event-driven workflows, better data lineage, reduced unnecessary runs.

## 16. Airflow 2.x Features

### 1. What are the major differences between Airflow 1.x and 2.x?

**Answer:** Key changes:
- **Scheduler HA**: Multiple schedulers supported
- **TaskFlow API**: Simplified DAG authoring with decorators
- **Full REST API**: Complete programmatic access
- **Improved UI**: Modern React-based interface
- **Provider packages**: Modular operator/hook packages
- **Smart sensors**: Consolidated sensor execution
- **Better security**: Enhanced RBAC, secrets backends
- **Performance**: Faster scheduler, reduced DB load
- **Simplified installation**: Separate constraints files
- **Removed SubDAGs**: Replaced with TaskGroups
- **Python 3.6+**: Dropped Python 2 support

### 2. What is the TaskFlow API?

**Answer:** TaskFlow API uses Python decorators to define tasks, simplifying DAG creation and automatic XCom handling.

```python
from airflow.decorators import dag, task
from datetime import datetime

@dag(schedule='@daily', start_date=datetime(2025, 1, 1))
def my_workflow():
    @task
    def extract():
        return {'data': [1, 2, 3]}
    
    @task
    def transform(data: dict):
        return {'processed': sum(data['data'])}
    
    @task
    def load(result: dict):
        print(f"Result: {result}")
    
    load(transform(extract()))

dag = my_workflow()
```

### 3. How do decorators work in Airflow 2.0?

**Answer:** Decorators convert Python functions into Airflow tasks:

```python
# @task decorator
@task
def my_function(x, y):
    return x + y

# Equivalent to:
my_function_task = PythonOperator(
    task_id='my_function',
    python_callable=my_function,
    op_kwargs={'x': 1, 'y': 2}
)

# Other decorators:
@task.virtualenv(requirements=['pandas==1.3.0'])
def process_with_pandas():
    import pandas as pd
    # Process data

@task.docker(image='python:3.9')
def run_in_docker():
    # Runs in Docker container

@task.branch
def choose_branch():
    return 'task_a'  # or 'task_b'
```

### 4. What are the benefits of using the TaskFlow API?

**Answer:**
- **Cleaner syntax**: Less boilerplate code
- **Automatic XCom**: Data passing handled automatically
- **Type hints**: Better IDE support and validation
- **Implicit dependencies**: Dependencies inferred from function calls
- **Pythonic**: Feels like regular Python code
- **Easier testing**: Can test functions independently
- **Automatic task_id**: Generated from function name
- **Better error messages**: More informative debugging

### 5. What is the Scheduler HA feature in Airflow 2.0?

**Answer:** High Availability allows multiple scheduler instances to run simultaneously.

**Benefits:**
- **Fault tolerance**: Automatic failover if scheduler crashes
- **Load distribution**: Multiple schedulers share workload
- **Zero downtime**: Updates without stopping scheduling
- **Improved performance**: Parallel DAG parsing

**Configuration:**
```bash
# Start multiple schedulers
airflow scheduler  # Instance 1
airflow scheduler  # Instance 2 (on another machine)
```

**How it works:**
- Schedulers coordinate via database
- Row-level locking prevents conflicts
- Heartbeat mechanism detects failed schedulers
- Work automatically redistributed

### 6. What improvements were made to the UI in Airflow 2.0?

**Answer:**
- **Modern framework**: React-based (from Flask-Admin)
- **Grid view**: Replaced tree view with better visualization
- **Auto-refresh**: Real-time updates without manual refresh
- **Improved performance**: Faster rendering, pagination
- **Calendar view**: Visual success/failure patterns
- **Better navigation**: Improved search and filtering
- **Dark mode**: UI theme options
- **Enhanced graph view**: Better task dependency visualization
- **Task duration**: Historical performance charts
- **Responsive design**: Better mobile support

### 7. How has the REST API evolved in Airflow 2.0?

**Answer:**
**Airflow 1.x:**
- Experimental API
- Limited endpoints
- Inconsistent responses
- Not officially supported

**Airflow 2.0+:**
- **Stable REST API**: Full CRUD operations
- **OpenAPI 3.0**: Standard specification
- **Complete coverage**: All Airflow operations
- **Authentication**: Proper auth mechanisms
- **Versioned**: /api/v1/ endpoints

```python
# Examples
import requests

base_url = 'http://airflow:8080/api/v1'
auth = ('username', 'password')

# List DAGs
response = requests.get(f'{base_url}/dags', auth=auth)

# Trigger DAG
requests.post(
    f'{base_url}/dags/my_dag/dagRuns',
    auth=auth,
    json={'conf': {'key': 'value'}}
)

# Get task instance
requests.get(
    f'{base_url}/dags/my_dag/dagRuns/run_id/taskInstances/task_id',
    auth=auth
)
```

### 8. What is the role of providers in Airflow 2.0?

**Answer:** Provider packages are separate, independently-versioned packages containing operators, hooks, and sensors for specific services.

**Benefits:**
- **Modular**: Install only what you need
- **Independent releases**: Update providers without upgrading Airflow
- **Faster fixes**: Provider bugs fixed independently
- **Smaller core**: Airflow core is lighter
- **Community maintained**: Easier contributions

**Examples:**
```bash
# Install specific providers
pip install apache-airflow-providers-amazon
pip install apache-airflow-providers-google
pip install apache-airflow-providers-postgres

# List installed providers
airflow providers list

# Provider packages include:
# - amazon: AWS services (S3, EMR, Redshift)
# - google: GCP services (GCS, BigQuery, Dataproc)
# - postgres: PostgreSQL hook and operator
# - http: HTTP operators
# - slack: Slack integration
```

**Usage:**
```python
from airflow.providers.amazon.aws.operators.s3 import S3CreateBucketOperator
from airflow.providers.google.cloud.operators.bigquery import BigQueryInsertJobOperator
```

## 17. Security

### 1. How do you secure an Airflow deployment?

**Answer:** Multi-layered approach:
- **Authentication**: Verify user identity
- **Authorization**: RBAC for access control
- **Encryption**: Fernet key for sensitive data
- **Secrets management**: External secrets backends
- **Network security**: HTTPS, firewall rules
- **Database security**: Encrypted connections
- **Audit logging**: Track user actions
- **Regular updates**: Apply security patches

### 2. What authentication methods does Airflow support?

**Answer:**
- **Database**: Username/password in metadata DB
- **LDAP**: Enterprise directory integration
- **OAuth**: Google, GitHub, Azure AD
- **Kerberos**: Hadoop ecosystem integration
- **Multi-factor authentication** (via extensions)
- **Custom**: Implement custom auth backend

Configuration example:
```ini
[webserver]
authenticate = True
auth_backend = airflow.contrib.auth.backends.password_auth

[ldap]
uri = ldap://ldap.company.com
```

### 3. How do you implement role-based access control (RBAC)?

**Answer:** RBAC is enabled by default in Airflow 2.0+:

**Built-in roles:**
- **Admin**: Full access
- **User**: View and trigger DAGs
- **Op**: Modify DAG runs and tasks
- **Viewer**: Read-only access
- **Public**: Minimal access

**Custom roles:**
```python
# Via UI: Security → List Roles → Create
# Or programmatically:
from airflow.www.security import AirflowSecurityManager

security_manager = AirflowSecurityManager()
security_manager.add_role('DataEngineer')
security_manager.add_permission_to_role('DataEngineer', 'can_edit on DAGs')
```

**Permissions:**
- DAG-level: Control which users see specific DAGs
- Resource-level: Connections, Variables, Pools
- Action-level: create, read, update, delete

### 4. How do you encrypt sensitive information in Airflow?

**Answer:** Use Fernet encryption:

**Setup:**
```bash
# Generate Fernet key
python -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"

# Set in airflow.cfg
[core]
fernet_key = your_generated_key_here
```

**What gets encrypted:**
- Connection passwords
- Variable values (if key contains password, secret, token, etc.)
- Sensitive configuration

**Rotating keys:**
```ini
[core]
fernet_key = new_key,old_key  # Comma-separated for rotation
```

### 5. What is Fernet key and why is it important?

**Answer:** Fernet is a symmetric encryption specification ensuring secure storage of credentials.

**Importance:**
- Encrypts passwords in database
- Required for production deployments
- Protects against database breaches
- Enables secure credential storage

**Without Fernet:**
- Passwords stored as plaintext
- Major security vulnerability
- Compliance violations

**Best practices:**
- Generate strong key
- Store securely (environment variable, secrets manager)
- Rotate periodically
- Never commit to version control

### 6. How do you manage secrets in Airflow?

**Answer:**
**1. Secrets Backends** (recommended):
```ini
# AWS Secrets Manager
[secrets]
backend = airflow.providers.amazon.aws.secrets.secrets_manager.SecretsManagerBackend
backend_kwargs = {"connections_prefix": "airflow/connections", "variables_prefix": "airflow/variables"}

# HashiCorp Vault
[secrets]
backend = airflow.providers.hashicorp.secrets.vault.VaultBackend
backend_kwargs = {"url": "http://vault:8200", "token": "your_token"}

# Google Secret Manager
[secrets]
backend = airflow.providers.google.cloud.secrets.secret_manager.CloudSecretManagerBackend
backend_kwargs = {"project_id": "your-project"}
```

**2. Environment variables**:
```bash
export AIRFLOW_CONN_POSTGRES='postgresql://user:pass@host/db'
export AIRFLOW_VAR_API_KEY='secret_key'
```

**3. Encrypted Variables**: Auto-encrypted if key contains sensitive keywords

### 7. What are Secrets Backends?

**Answer:** External systems for storing and retrieving secrets instead of the metadata database.

**Supported backends:**
- AWS Secrets Manager
- Google Cloud Secret Manager
- Azure Key Vault
- HashiCorp Vault
- Custom backends

**Benefits:**
- Centralized secret management
- Better security controls
- Audit capabilities
- Rotation support
- Compliance alignment

### 8. How do you integrate Airflow with external secret management systems?

**Answer:**
```python
# AWS Secrets Manager
# 1. Install provider
# pip install apache-airflow-providers-amazon

# 2. Configure airflow.cfg
[secrets]
backend = airflow.providers.amazon.aws.secrets.secrets_manager.SecretsManagerBackend
backend_kwargs = {
    "connections_prefix": "airflow/connections",
    "variables_prefix": "airflow/variables",
    "region_name": "us-east-1"
}

# 3. Store secrets in AWS
# Secret name: airflow/connections/my_postgres
# Secret value: postgresql://user:pass@host:5432/db

# 4. Use in Airflow
hook = PostgresHook(postgres_conn_id='my_postgres')  # Auto-retrieved

# Custom backend
from airflow.secrets import BaseSecretsBackend

class CustomBackend(BaseSecretsBackend):
    def get_connection(self, conn_id):
        # Fetch from your system
        pass
    
    def get_variable(self, key):
        # Fetch variable
        pass
```

## 18. Deployment and Scaling

### 1. How do you deploy Airflow in production?

**Answer:** Common deployment patterns:

**1. Traditional VM deployment:**
- Separate machines for webserver, scheduler, workers
- Load balancer for webserver HA
- Shared file system for DAGs and logs
- External database (RDS, Cloud SQL)

**2. Docker/Docker Compose:**
- Containerized components
- Easy local development
- Portable across environments

**3. Kubernetes:**
- Helm chart deployment
- Auto-scaling capabilities
- Cloud-native approach

**4. Managed services:**
- AWS MWAA (Managed Workflows for Apache Airflow)
- Google Cloud Composer
- Astronomer

**Production checklist:**
- Use PostgreSQL/MySQL (not SQLite)
- Enable Fernet encryption
- Configure remote logging
- Set up monitoring and alerts
- Use proper executor (Celery/Kubernetes)
- Implement RBAC
- Regular backups
- HTTPS for webserver

### 2. What are the considerations for scaling Airflow?

**Answer:**
**Horizontal scaling:**
- Add more worker nodes (CeleryExecutor)
- Multiple scheduler instances (Airflow 2.0+)
- Webserver behind load balancer

**Database scaling:**
- Connection pooling
- Read replicas for webserver
- Database performance tuning

**Resource allocation:**
- Pool configuration for resource limits
- Task concurrency limits
- DAG-level parallelism controls

**Performance optimization:**
- Minimize DAG complexity
- Reduce parsing frequency
- Use appropriate poke intervals for sensors
- Implement smart sensors

### 3. How do you containerize Airflow using Docker?

**Answer:**
```dockerfile
# Dockerfile
FROM apache/airflow:2.7.0-python3.9

# Install additional requirements
COPY requirements.txt .
RUN pip install -r requirements.txt

# Copy DAGs
COPY dags/ ${AIRFLOW_HOME}/dags/
```

```yaml
# docker-compose.yml
version: '3'
services:
  postgres:
    image: postgres:13
    environment:
      POSTGRES_USER: airflow
      POSTGRES_PASSWORD: airflow
      POSTGRES_DB: airflow

  airflow-init:
    image: apache/airflow:2.7.0
    command: db init
    environment:
      AIRFLOW__DATABASE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres/airflow

  webserver:
    image: apache/airflow:2.7.0
    command: webserver
    ports:
      - "8080:8080"
    depends_on:
      - postgres

  scheduler:
    image: apache/airflow:2.7.0
    command: scheduler
    depends_on:
      - postgres
```

### 4. What is the official Airflow Helm chart?

**Answer:** Official Kubernetes deployment using Helm package manager.

```bash
# Add Airflow Helm repository
helm repo add apache-airflow https://airflow.apache.org
helm repo update

# Install Airflow
helm install airflow apache-airflow/airflow \
  --namespace airflow \
  --create-namespace \
  --set executor=KubernetesExecutor
```

**Key features:**
- Production-ready configuration
- Multiple executor support
- Auto-scaling capabilities
- Integrated monitoring
- PersistentVolume for logs
- Customizable via values.yaml

### 5. How do you deploy Airflow on Kubernetes?

**Answer:**
```yaml
# values.yaml for Helm chart
executor: KubernetesExecutor

postgresql:
  enabled: false  # Use external DB

config:
  core:
    dags_folder: /opt/airflow/dags
  webserver:
    expose_config: false
  
dags:
  gitSync:
    enabled: true
    repo: https://github.com/company/airflow-dags
    branch: main
    
resources:
  limits:
    cpu: 2000m
    memory: 4Gi
  requests:
    cpu: 1000m
    memory: 2Gi

# Deploy
helm upgrade --install airflow apache-airflow/airflow -f values.yaml
```

**Benefits:**
- Pod-per-task isolation
- Auto-scaling
- Resource optimization
- Easy rollbacks
- Cloud-native

### 6. What are the resource requirements for running Airflow?

**Answer:**
**Minimum (development):**
- 2 CPU cores
- 4 GB RAM
- 20 GB disk

**Small production:**
- Scheduler: 2 cores, 4 GB RAM
- Webserver: 1 core, 2 GB RAM
- Worker: 2 cores, 4 GB RAM (per worker)
- Database: 2 cores, 4 GB RAM
- Total: ~8 cores, 16 GB RAM

**Large production:**
- Multiple schedulers: 4+ cores, 8+ GB each
- Webserver pool: 2+ cores, 4+ GB each
- Workers: Scale based on workload
- Database: 8+ cores, 16+ GB RAM

**Factors affecting requirements:**
- Number of DAGs
- Task concurrency
- DAG complexity
- Data volume
- Executor type

### 7. How do you implement high availability for Airflow components?

**Answer:**
**Scheduler HA (Airflow 2.0+):**
```bash
# Run multiple schedulers
scheduler-1: airflow scheduler
scheduler-2: airflow scheduler
```

**Webserver HA:**
```nginx
# Load balancer (Nginx)
upstream airflow {
    server webserver-1:8080;
    server webserver-2:8080;
}

server {
    listen 80;
    location / {
        proxy_pass http://airflow;
    }
}
```

**Database HA:**
- Master-replica configuration
- Automatic failover
- Connection pooling

**Worker HA:**
- Auto-scaling groups
- Health checks
- Automatic replacement

**Storage HA:**
- Shared file system (NFS, EFS)
- Object storage for logs (S3, GCS)

### 8. What are the networking considerations for distributed Airflow?

**Answer:**
**Required connectivity:**
- All components → Database
- Workers → DAG folder (shared storage or git-sync)
- Workers → Log storage
- Scheduler → Executor queue (Celery: Redis/RabbitMQ)
- Users → Webserver

**Security:**
- Database connections over TLS
- HTTPS for webserver
- VPC/private networks for components
- Firewall rules limiting access

**Performance:**
- Low latency to database critical
- High bandwidth for log retrieval
- Minimize cross-region traffic

**Ports:**
- Webserver: 8080 (customizable)
- Flower (Celery): 5555
- Database: 5432 (PostgreSQL), 3306 (MySQL)
- Redis: 6379

### 9. How do you manage multiple environments (dev, staging, prod)?

**Answer:**
**1. Separate Airflow instances:**
```bash
# Different namespaces in Kubernetes
kubectl create namespace airflow-dev
kubectl create namespace airflow-prod
```

**2. Environment-specific configurations:**
```python
import os
env = os.getenv('ENVIRONMENT', 'dev')

if env == 'prod':
    executor = 'KubernetesExecutor'
    schedule = '@daily'
else:
    executor = 'LocalExecutor'
    schedule = None  # Manual trigger only

dag = DAG('my_dag', schedule=schedule)
```

**3. Environment variables:**
```bash
# dev.env
AIRFLOW__CORE__EXECUTOR=SequentialExecutor
DATABASE_URL=postgresql://localhost/airflow_dev

# prod.env
AIRFLOW__CORE__EXECUTOR=CeleryExecutor
DATABASE_URL=postgresql://prod-db/airflow_prod
```

**4. Separate databases:**
- Dev: Local PostgreSQL
- Staging: Shared RDS (smaller instance)
- Prod: Dedicated RDS (HA configuration)

**5. DAG promotion:**
```bash
# Git branches
git checkout develop  # Development
git checkout staging  # Staging
git checkout main     # Production

# CI/CD pipeline promotes code through environments
```

**6. Connection/Variable management:**
- Use same connection IDs across environments
- Different values per environment (via secrets backend)
- Environment-specific Variables

## 19. Integration and External Systems

### 1. How does Airflow integrate with Apache Spark?

**Answer:** Multiple integration methods:

**1. SparkSubmitOperator:**
```python
spark_task = SparkSubmitOperator(
    task_id='spark_job',
    application='/path/to/spark_job.py',
    conn_id='spark_default',
    conf={'spark.executor.memory': '4g'},
    application_args=['arg1', 'arg2']
)
```

**2. SSH to Spark cluster:**
```python
ssh_task = SSHOperator(
    task_id='run_spark',
    ssh_conn_id='spark_cluster',
    command='spark-submit --master yarn app.py'
)
```

**3. Cloud-specific operators:**
- DataprocSubmitJobOperator (GCP)
- EmrAddStepsOperator (AWS)

### 2. What is the SparkSubmitOperator?

**Answer:** Operator that submits Spark jobs to a cluster.

```python
from airflow.providers.apache.spark.operators.spark_submit import SparkSubmitOperator

spark = SparkSubmitOperator(
    task_id='process_data',
    application='/opt/spark/jobs/etl.py',
    conn_id='spark_default',
    total_executor_cores=4,
    executor_memory='4g',
    driver_memory='2g',
    name='airflow-spark-job',
    conf={
        'spark.sql.shuffle.partitions': '200',
        'spark.executor.instances': '3'
    },
    application_args=['--input', 's3://bucket/data', '--output', 's3://bucket/results']
)
```

**Connection configuration:** Define Spark connection in Airflow UI with master URL (spark://host:7077, yarn, local[*]).

### 3. How do you integrate Airflow with AWS services?

**Answer:**
```python
# S3
from airflow.providers.amazon.aws.operators.s3 import S3CreateBucketOperator
create_bucket = S3CreateBucketOperator(task_id='create_bucket', bucket_name='my-bucket')

# EMR
from airflow.providers.amazon.aws.operators.emr import EmrCreateJobFlowOperator
create_cluster = EmrCreateJobFlowOperator(task_id='create_emr', job_flow_overrides={...})

# Glue
from airflow.providers.amazon.aws.operators.glue import GlueJobOperator
glue = GlueJobOperator(task_id='glue_job', job_name='my_glue_job')

# Redshift
from airflow.providers.amazon.aws.transfers.s3_to_redshift import S3ToRedshiftOperator
copy_to_redshift = S3ToRedshiftOperator(
    task_id='load_redshift',
    s3_bucket='my-bucket',
    s3_key='data.csv',
    schema='public',
    table='my_table',
    redshift_conn_id='redshift_default'
)

# Lambda
from airflow.providers.amazon.aws.operators.lambda_function import LambdaInvokeFunctionOperator
invoke = LambdaInvokeFunctionOperator(task_id='invoke_lambda', function_name='my_function')
```

### 4. What operators are available for Google Cloud Platform?

**Answer:**
```python
# BigQuery
from airflow.providers.google.cloud.operators.bigquery import BigQueryInsertJobOperator
bq = BigQueryInsertJobOperator(
    task_id='query',
    configuration={'query': {'query': 'SELECT * FROM dataset.table', 'useLegacySql': False}}
)

# Cloud Storage
from airflow.providers.google.cloud.operators.gcs import GCSCreateBucketOperator
gcs = GCSCreateBucketOperator(task_id='create_bucket', bucket_name='my-bucket')

# Dataproc
from airflow.providers.google.cloud.operators.dataproc import DataprocSubmitJobOperator
dataproc = DataprocSubmitJobOperator(task_id='spark_job', job={...}, region='us-central1')

# Cloud Functions
from airflow.providers.google.cloud.operators.functions import CloudFunctionsInvokeFunctionOperator
invoke = CloudFunctionsInvokeFunctionOperator(task_id='invoke', function_id='my_function')
```

### 5. How do you integrate Airflow with databases?

**Answer:**
```python
# PostgreSQL
from airflow.providers.postgres.operators.postgres import PostgresOperator
postgres = PostgresOperator(
    task_id='run_query',
    postgres_conn_id='postgres_default',
    sql='INSERT INTO table SELECT * FROM source WHERE date = %(date)s',
    parameters={'date': '2025-01-01'}
)

# MySQL
from airflow.providers.mysql.operators.mysql import MySqlOperator
mysql = MySqlOperator(task_id='query', mysql_conn_id='mysql_default', sql='SELECT * FROM users')

# Generic SQL
from airflow.providers.common.sql.operators.sql import SQLExecuteQueryOperator
sql = SQLExecuteQueryOperator(task_id='query', conn_id='db_conn', sql='SELECT 1')

# Using Hooks for complex operations
from airflow.providers.postgres.hooks.postgres import PostgresHook
def complex_db_operation():
    hook = PostgresHook(postgres_conn_id='postgres_default')
    with hook.get_conn() as conn:
        with conn.cursor() as cursor:
            cursor.execute("BEGIN")
            cursor.execute("INSERT ...")
            cursor.execute("UPDATE ...")
            conn.commit()
```

### 6. How does Airflow work with data warehouses like Snowflake or Redshift?

**Answer:**
**Snowflake:**
```python
from airflow.providers.snowflake.operators.snowflake import SnowflakeOperator
snowflake = SnowflakeOperator(
    task_id='load_data',
    snowflake_conn_id='snowflake_default',
    sql="""
        COPY INTO my_table
        FROM s3://bucket/data/
        FILE_FORMAT = (TYPE = 'CSV')
    """
)
```

**Redshift:**
```python
from airflow.providers.amazon.aws.transfers.s3_to_redshift import S3ToRedshiftOperator
load = S3ToRedshiftOperator(
    task_id='s3_to_redshift',
    s3_bucket='my-bucket',
    s3_key='data/',
    schema='public',
    table='target_table',
    copy_options=['CSV', 'IGNOREHEADER 1']
)
```

### 7. How do you use Airflow with Kubernetes?

**Answer:**
**KubernetesPodOperator:**
```python
from airflow.providers.cncf.kubernetes.operators.kubernetes_pod import KubernetesPodOperator

k8s_task = KubernetesPodOperator(
    task_id='run_in_k8s',
    name='my-pod',
    namespace='default',
    image='python:3.9',
    cmds=['python', '-c'],
    arguments=['print("Hello from Kubernetes")'],
    resources={
        'request_cpu': '100m',
        'request_memory': '256Mi',
        'limit_cpu': '500m',
        'limit_memory': '512Mi'
    },
    env_vars={'ENV': 'production'},
    is_delete_operator_pod=True
)
```

### 8. What is the KubernetesPodOperator?

**Answer:** Operator that runs a Docker container in a Kubernetes pod.

**Benefits:**
- Task isolation (separate pod per task)
- Custom Docker images per task
- Resource allocation (CPU/memory limits)
- Different Python versions/dependencies
- Automatic cleanup

**Use cases:**
- Running tasks with conflicting dependencies
- Resource-intensive operations
- Tasks requiring specific environments
- Isolation for security

### 9. How do you integrate Airflow with messaging systems like Kafka?

**Answer:**
**Consuming from Kafka:**
```python
from airflow.providers.apache.kafka.operators.consume import ConsumeFromTopicOperator
consume = ConsumeFromTopicOperator(
    task_id='consume_messages',
    topics=['my_topic'],
    kafka_config_id='kafka_default',
    max_messages=100
)
```

**Producing to Kafka:**
```python
from airflow.providers.apache.kafka.operators.produce import ProduceToTopicOperator
produce = ProduceToTopicOperator(
    task_id='send_message',
    topic='my_topic',
    kafka_config_id='kafka_default',
    producer_function='my_module.produce_messages'
)
```

**Custom integration:**
```python
from kafka import KafkaConsumer, KafkaProducer

@task
def process_kafka_messages():
    consumer = KafkaConsumer('topic', bootstrap_servers=['localhost:9092'])
    for message in consumer:
        process(message.value)
```

### 10. How do you trigger Airflow DAGs from external systems?

**Answer:**
**1. REST API:**
```bash
curl -X POST "http://airflow:8080/api/v1/dags/my_dag/dagRuns" \
  -H "Content-Type: application/json" \
  -u "username:password" \
  -d '{"conf": {"key": "value"}}'
```

**2. CLI:**
```bash
airflow dags trigger my_dag --conf '{"param": "value"}'
```

**3. Python client:**
```python
import requests
response = requests.post(
    'http://airflow:8080/api/v1/dags/my_dag/dagRuns',
    auth=('user', 'pass'),
    json={'conf': {'environment': 'prod'}}
)
```

**4. File-based trigger:**
```python
# Use FileSensor to detect trigger files
sensor = FileSensor(
    task_id='wait_for_trigger',
    filepath='/triggers/start_dag.txt'
)
```

**5. Message queue:**
```python
# Listen to Kafka/RabbitMQ and trigger via API
def kafka_listener():
    for message in consumer:
        trigger_dag_via_api(message.value)
```

## 20. Scenario-Based Questions

### 1. How would you migrate a legacy cron-based pipeline to Airflow?

**Answer:**
**Step 1: Inventory existing cron jobs**
```bash
crontab -l > existing_jobs.txt
```

**Step 2: Map cron schedules to Airflow schedules**
```python
# Cron: 0 2 * * * /path/to/script.sh
# Airflow: schedule='0 2 * * *'

dag = DAG('migrated_job', schedule='0 2 * * *', start_date=datetime(2025, 1, 1))
```

**Step 3: Convert scripts to tasks**
```python
# Original: bash script
# Airflow version:
task = BashOperator(task_id='legacy_script', bash_command='/path/to/script.sh')
```

**Step 4: Add dependencies**
```python
# If job1 runs before job2
job1 >> job2
```

**Step 5: Add monitoring and error handling**
```python
default_args = {
    'email': ['team@company.com'],
    'email_on_failure': True,
    'retries': 2
}
```

**Step 6: Test thoroughly, then phase out cron jobs gradually**

### 2. How would you handle a situation where a task needs to run only if the previous day's task succeeded?

**Answer:**
```python
from airflow.sensors.external_task import ExternalTaskSensor

# Wait for yesterday's run to succeed
wait_for_yesterday = ExternalTaskSensor(
    task_id='wait_yesterday',
    external_dag_id='my_dag',  # Same DAG
    external_task_id='process_data',
    execution_date_fn=lambda dt: dt - timedelta(days=1),
    timeout=600,
    mode='reschedule'
)

today_task = PythonOperator(
    task_id='process_data',
    python_callable=process
)

wait_for_yesterday >> today_task
```

**Alternative using depends_on_past:**
```python
task = PythonOperator(
    task_id='process_data',
    python_callable=process,
    depends_on_past=True  # Only run if previous run succeeded
)
```

### 3. How would you implement a pipeline that processes files as they arrive?

**Answer:**
**Option 1: FileSensor with reschedule mode**
```python
@dag(schedule='@continuous', start_date=datetime(2025, 1, 1))
def file_processing_dag():
    wait = FileSensor(
        task_id='wait_for_file',
        filepath='/data/incoming/*.csv',
        poke_interval=30,
        mode='reschedule'
    )
    
    @task
    def process_file():
        # Process and move file
        pass
    
    wait >> process_file()
```

**Option 2: Event-driven with external trigger**
```python
# Watchdog script triggers DAG via API when file arrives
import requests
from watchdog.observers import Observer

def on_file_created(event):
    requests.post(
        'http://airflow:8080/api/v1/dags/process_file/dagRuns',
        auth=('user', 'pass'),
        json={'conf': {'filepath': event.src_path}}
    )
```

**Option 3: Deferrable operators (Airflow 2.2+)**
```python
from airflow.sensors.filesystem import FileSensor

sensor = FileSensor(
    task_id='wait',
    filepath='/data/*.csv',
    deferrable=True  # Frees worker slot
)
```

### 4. How would you design a DAG that handles incremental data loads?

**Answer:**
```python
@dag(schedule='@daily', start_date=datetime(2025, 1, 1), catchup=True)
def incremental_load():
    @task
    def extract_incremental(**context):
        # Use execution_date for incremental logic
        date = context['execution_date'].date()
        
        sql = f"""
        SELECT * FROM source_table
        WHERE updated_date = '{date}'
        """
        return fetch_data(sql)
    
    @task
    def load_incremental(data, **context):
        date = context['execution_date'].date()
        
        # Delete existing data for this date (idempotency)
        delete_sql = f"DELETE FROM target WHERE date = '{date}'"
        execute(delete_sql)
        
        # Insert new data
        insert_data(data)
    
    load_incremental(extract_incremental())
```

**With partitioning:**
```python
@task
def load_to_partitioned_table(**context):
    date = context['ds']  # YYYY-MM-DD
    df = get_data()
    df.write.mode('overwrite').parquet(f's3://bucket/data/date={date}/')
```

### 5. How would you troubleshoot a DAG that is running slower than expected?

**Answer:**
**Step 1: Identify bottleneck**
- Check Gantt chart for long-running tasks
- Review task duration trends

**Step 2: Analyze specific issues**
```python
# Check if tasks are queued
airflow tasks states-for-dag-run my_dag execution_date

# Review scheduler performance
# Check min_file_process_interval, parsing_processes
```

**Step 3: Common fixes**
- Increase parallelism: `max_active_tasks`
- Add more workers
- Optimize slow SQL queries
- Use pools to manage resources
- Reduce task granularity (too many small tasks)
- Enable task parallelization

**Step 4: Database optimization**
- Check connection pool size
- Add indexes
- Vacuum/analyze tables

**Step 5: Code optimization**
```python
# Avoid heavy top-level code
# Use appropriate executors
# Minimize XCom usage
```

### 6. How would you handle timezone-related issues in Airflow?

**Answer:**
**Set explicit timezone:**
```ini
# airflow.cfg
[core]
default_timezone = America/New_York
```

**Use pendulum for timezone-aware dates:**
```python
import pendulum

dag = DAG(
    'my_dag',
    start_date=pendulum.datetime(2025, 1, 1, tz='America/New_York'),
    schedule='0 9 * * *'  # 9 AM New York time
)
```

**Access execution_date correctly:**
```python
def my_task(**context):
    # execution_date is UTC by default
    exec_date_utc = context['execution_date']
    
    # Convert to local timezone
    local_tz = pendulum.timezone('America/New_York')
    exec_date_local = exec_date_utc.in_timezone(local_tz)
```

**Handle DST transitions:**
```python
# Use @daily instead of cron for daily jobs
# Airflow handles DST automatically
dag = DAG('my_dag', schedule='@daily')
```

### 7. How would you implement a data quality check in an Airflow pipeline?

**Answer:**
```python
@dag(schedule='@daily', start_date=datetime(2025, 1, 1))
def etl_with_quality_checks():
    @task
    def extract():
        return get_data()
    
    @task
    def quality_check(data):
        # Row count check
        if len(data) == 0:
            raise ValueError("No data extracted")
        
        # Null check
        if data.isnull().sum().sum() > 0:
            raise ValueError("Null values found")
        
        # Schema validation
        expected_columns = ['id', 'name', 'date']
        if not all(col in data.columns for col in expected_columns):
            raise ValueError("Schema mismatch")
        
        return data
    
    @task
    def load(data):
        save_to_database(data)
    
    # Use ShortCircuitOperator for conditional continuation
    @task.short_circuit
    def validate_count(data):
        return len(data) > 100  # Only continue if enough data
    
    data = extract()
    validated = quality_check(data)
    load(validated)
```

**Using Great Expectations:**
```python
from airflow.providers.great_expectations.operators.great_expectations import GreatExpectationsOperator

quality_check = GreatExpectationsOperator(
    task_id='validate_data',
    data_context_root_dir='/path/to/great_expectations',
    checkpoint_name='my_checkpoint'
)
```

### 8. How would you orchestrate a machine learning pipeline using Airflow?

**Answer:**
```python
@dag(schedule='@weekly', start_date=datetime(2025, 1, 1))
def ml_pipeline():
    @task
    def extract_training_data(**context):
        # Get data for last week
        start_date = context['execution_date'] - timedelta(days=7)
        end_date = context['execution_date']
        return fetch_data(start_date, end_date)
    
    @task
    def preprocess(data):
        # Feature engineering
        processed = transform(data)
        path = save_to_s3(processed, 'preprocessed/')
        return path
    
    @task
    def train_model(data_path):
        # Train model (could use KubernetesPodOperator for resources)
        model = train(load_from_s3(data_path))
        model_path = save_model(model, 's3://models/')
        return model_path
    
    @task
    def evaluate_model(model_path):
        metrics = evaluate(load_model(model_path))
        if metrics['accuracy'] < 0.8:
            raise ValueError("Model accuracy too low")
        return metrics
    
    @task
    def deploy_model(model_path, metrics):
        # Deploy to production
        deploy_to_sagemaker(model_path)
        log_metrics(metrics)
    
    data = extract_training_data()
    processed = preprocess(data)
    model = train_model(processed)
    metrics = evaluate_model(model)
    deploy_model(model, metrics)
```

**Using specialized operators:**
```python
# SageMaker
from airflow.providers.amazon.aws.operators.sagemaker import SageMakerTrainingOperator

train = SageMakerTrainingOperator(
    task_id='train_model',
    config={
        'TrainingJobName': 'my-job',
        'AlgorithmSpecification': {...},
        'ResourceConfig': {...}
    }
)
```

### 9. How would you handle backfilling data for a specific date range?

**Answer:**
```bash
# CLI backfill
airflow dags backfill my_dag \
    --start-date 2025-01-01 \
    --end-date 2025-01-31 \
    --reset-dagruns  # Clear existing runs

# With specific task
airflow dags backfill my_dag \
    --start-date 2025-01-01 \
    --end-date 2025-01-31 \
    --task-regex 'extract.*'  # Only extract tasks

# Programmatic backfill
from airflow.models import DagRun
from airflow import settings

session = settings.Session()
dag_runs = session.query(DagRun).filter(
    DagRun.dag_id == 'my_dag',
    DagRun.execution_date >= datetime(2025, 1, 1),
    DagRun.execution_date <= datetime(2025, 1, 31)
).all()

for run in dag_runs:
    run.state = 'failed'  # Mark for rerun
session.commit()
```

**DAG design for backfilling:**
```python
dag = DAG(
    'my_dag',
    schedule='@daily',
    start_date=datetime(2024, 1, 1),
    catchup=True,  # Enable backfilling
    max_active_runs=5  # Limit concurrent backfill runs
)
```

### 10. How would you design a fault-tolerant data pipeline using Airflow?

**Answer:**
```python
@dag(schedule='@daily', start_date=datetime(2025, 1, 1))
def fault_tolerant_pipeline():
    # Retries with exponential backoff
    default_args = {
        'retries': 3,
        'retry_delay': timedelta(minutes=5),
        'retry_exponential_backoff': True,
        'max_retry_delay': timedelta(hours=1)
    }
    
    @task(default_args=default_args)
    def extract(**context):
        date = context['ds']
        try:
            data = fetch_data(date)
            # Save checkpoint
            save_checkpoint(data, f'/checkpoints/extract_{date}.pkl')
            return data
        except Exception as e:
            # Try checkpoint recovery
            if checkpoint_exists(f'/checkpoints/extract_{date}.pkl'):
                return load_checkpoint(f'/checkpoints/extract_{date}.pkl')
            raise
    
    @task(default_args=default_args)
    def transform(data, **context):
        # Idempotent transformation
        date = context['ds']
        # Process in chunks for fault tolerance
        results = []
        for chunk in chunk_data(data, size=1000):
            results.append(process_chunk(chunk))
        return results
    
    @task(default_args=default_args)
    def load(data, **context):
        date = context['ds']
        # Idempotent load: delete before insert
        delete_query = f"DELETE FROM table WHERE date = '{date}'"
        execute(delete_query)
        insert_data(data)
    
    # Cleanup task always runs
    @task(trigger_rule='all_done')
    def cleanup(**context):
        date = context['ds']
        remove_checkpoint(f'/checkpoints/extract_{date}.pkl')
        remove_temp_files(date)
    
    data = extract()
    transformed = transform(data)
    load(transformed)
    cleanup()
```

**Additional fault tolerance:**
- Use pools to limit resource usage
- Implement health checks
- Set execution_timeout
- Use ExternalTaskSensor for dependencies
- Enable HA for Airflow components
- Monitor with alerts

### 11-15. Additional Scenario Questions

**11. How would you implement a pipeline that depends on multiple external data sources?**

Use multiple ExternalTaskSensors or FileSensors in parallel, then join with a dummy task.

**12. How would you optimize a DAG with hundreds of tasks?**

Use dynamic task mapping, TaskGroups for organization, consider breaking into multiple DAGs, use appropriate parallelism settings.

**13. How would you implement circuit breaker patterns in Airflow?**

Use Variables to track failures, ShortCircuitOperator to stop execution after threshold, callbacks to reset circuit state.

**14. How would you handle a scenario where upstream data is delayed?**

Use Sensors with appropriate timeout, implement SLA callbacks for alerts, design idempotent tasks for safe retries, consider reschedule mode.

**15. How would you coordinate workflows across multiple Airflow instances?**

Use REST API to trigger DAGs, share state via external database or message queue, use object storage (S3/GCS) for data passing, implement webhooks for notifications.

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
10. [Top 20 Airflow Interview Questions 2025](https://mindmajix.com/airflow-interview-questions)