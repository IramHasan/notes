# Introduction to Modern Data Engineering with Snowflake
![image](https://github.com/user-attachments/assets/fdbb4bd0-53ca-4863-afd2-8d96d5edcb6d)

## Module 3
### Data Transformations
- raw data won't have the insights we are looking for
- before extracting insights we must transform data to an end state usable for analytics
- data transformations are formed against the raw data to get closer to our desired insights
- **common data transformations:**
  - address missing / incorrect data
  - fix formatting
  - aggregations
  - derived columns
  - derived tables / views
- most common methods of data transformations in Snowflake:
  - SQL
  - Snowpark
- write functions / logic invoked to aid transformations at scale
  - user defined functions
  - stored procedures
- streams offer efficient transformations
- dynamic tables offer automatic transformations
### Data Transformations with SQL
- Snowflake supports the common standardized version of ANSI SQL
- Snowflake has its own SQL dialect w/ variations
- Data tranformations in SQL can be done by writing SQL in a workbook and executing blocks to:
  - explore data
  - pull data from the market place to compare external factors
  - draw insights
  - create views for analysis
- raw data can be left completely untouched and instead apply transformations to create new objects i.e. materialized views
- allows re-use of raw data as much as possible without compromising its integrity 
### Data Transformations with Snowpark 
- perform data transformations in Python, Java, Scala
- configure the runtimes for languages i.e. using different versions of Python
- DataFrame API for processing data and performing data transformations
- **Flow:** create dataframes using the data &rarr; perform transformations &rarr; write results to new views / tables
- Similar to Pyspark / other dataframe APIs
- data transformations can be pushed to Snowflake's compute engine
- Snowpark code can be written in:
  - scripts / code base 
  - Snowflake notebook
  - IDE using Snowflake extension
- we can create Snowflake notebooks or import ipynb files into Snowflake to utilize our warehouse's computing ability
- we can search for snowpark-python to install the correct packages in Snowflake's package picker
- start an active Snowpark session using `get_active_session()` command - allows us to start a Snowflake session without manually configuring a connections file
```
from snowflake.snowpark.context import get_active_session
session = get_active_session()
```
- we can load data directly from our tables and views by passing them into our session objects
```
dataframe = session.table("database.schema_name")
```
- we can now directly apply filters/transformations to specific columns within our dataframe in a very straight forward manner
- the Dataframe API allows for:
  - easy aggregations
  - grouping by multiple columns
  - creating aggregation columns
  - sorting on top of the aggregation
- we can create Dataframes that are then converted into views easily:
```
dataframe.create_or_replace_view("database.view_name")
```
### User Defined Functions (UDFs)
- performing manual data transformations does not scale
- in practice, we need reusable chunks of code in a centralized fashion to ensure consistency of pipelines
- UDFs - write own custom logic to be reused in queries and data transformations
- typically used to capture logic 
