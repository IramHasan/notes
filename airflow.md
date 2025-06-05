## What is Airflow
- platform for developing, scheduling, and monitoring batch oriented workflows
- A web-based UI to visualize, manage, and debug workflows
- Airflow can be run as a distributed system capable of handling massive workflows

## Workflows as code
- defined entirely in Python
- Dynamic - pipelines are defined in code, enabling dynamic dag generation and parameterization
- Extensible - includes a wide range of built-in operators and can be extended to fit needs
- Leverages Jinja templating engine, allowing for customization

- DAG - the model that encapsulates everything that is needed to execute a workflow
- DAG Attributes
  - **Schedule:** When the workflow should run
  - **Tasks:** tasks are discrete units of work that are run on workers
  - **Task Dependencies:** The order and conditions under which tasks execute
  - **Callbacks:** Actions to take when the entire workflow completes
  - Additional Params: operational details
  - `>>` used to set the dependencies between tasks
- A dag is how Airflow will represent a workflow
- We can use operators such as `BashOperator` to define tasks
- We can use the `@task` decorator to define a Python function within airflow (airflow sdk)
- Airflow will parse the script, schedule tasks, and execute them in a defined order
- Airflow can run a spark job, move files between storage buckets, send a notification email
- We can see the runs of a dag throughout time
- Visual representation of the dag runs for monitoring and troubleshooting 
- DAGs must start at one end and come to a completion - Directed Acyclic Graph 

## Tasks
- task - node in your DAG - unit of work and best practice is to keep tasks atomic
- A **task instance** represents a specific run of a task - A DAG + task + point in time 
- A version of a task that ran historically - what was the task instance from 3 weeks ago @ midnight
- tasks are meant to be specialized units of work responsible for doing specific things 
- Operators exist for AWS services, Snowflake, Azure, Celery, etc. 

## 

## Why Airflow?
- platform for orchestrating batch workflows
- flexible framework with a wide range of built-in operators (easy integration)
- VERY good at transforming and moving data across an existing ecosystem (ML Ops)
- BnB ETL/ELT
- Great for operationalized analytics
- Integrates well with all processing and data validation tools

## NOT for Airflow
- not for continuously running, event-driven, or streaming workloads
- compliments streaming like Apache Kafka
- not for UI based development - Airflow requires some level of coding

## Airflow Infrastructure Core Components
- **Web Server** - Flask server running with Gunicorn serving the Airflow UI (web interface)
- **Scheduler** - Daemon responsible for scheduling jobs
- **Database** - A database where all metadata are stored (maintains state / history)
- **Executor** - Defines how tasks are executed (task itself running i.e. using Celery)
- **Worker** - Process executing the tasks, defined by executor (what the executors run inside)
- **Triggerer** - Process running asyncio to support defferable operators (leveraging Airflow sensors 
 
## Difference between Airflow and Kafka 
- Airflow is a batched process scheduler to manage tasks processes through a workflow
- Kafka a message bus meant for tracking lots of data that is moving quickly, acting as a dynamic buffer for dynamic fast moving data
- Kafka is meant for real-time data throughput to go between something that is omitting data and something that is reading it acting as a buffer
- Airflow is a data orchestration tool that goes in batches to undergo scheduled transformations, dates, and changes to data that has already landed
- Data from Kafka queue that writes to a store can be picked up by Airflow and transferred to Snowflake 

## Airflow 
## Quick Start
