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
  - Snowflake notebook - tool for executing SQL, Python and building data pipelines
  - Like a Jupyter notebook, allows us to execute individual cells, executing small chunks of the code at a time 
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
- typically used to capture logic that perform a specific computation i.e. a calculation, or format a value
- update the logic of the UDF in one place vs. several
- Key points about UDFs:
  - you can write UDFs in many different supported languages
  - UDFs are typically intended to return a single value - making them useful for very specific tasks
- More complex logic would use stored procedures
- Typical steps for creating and using UDFs:
  - Define the function
  - Write its logic
  - Invoke the UDF during transformations / other data processing
- **UDFs are created by:**
  - specifying the function keyword
  - the schema where it will live
  - the name of the funciton
  - specify that the function will accept an argument of type NUMBER
  - specify that the function will return a value of type NUMBER
  - AS keyword points to the definition of the UDF
  - two sets of dollar signs ($) act as opening and closing delimiters
  - the function logic should be encapsulated between these two sets of delimiters
- UDFs can be used to derive new columns based on existing columns
- by capturing logic in a UDF we can reuse it throughout the transformations in our data pipeline and easily mange it from a centralized definition

### Efficient Transformations w/ Streams
- we want to maximize efficiency of data transformations by only processing rows with changes
- streams are objects in Snowflake that are able to keep precise track of all the changes that have been made to a table / view
- if we create a stream to track a table, we can consume the stream to view the latest changes made to that table
- we consume the stream in a DML statement
- after consuming the stream, it essentially resets and starts recording subsequent changes from that point forward
- streams are great for processing incremental changes to tables and makes transformations more efficient
- we can extend this behavior for more complex transformations
  - we can perform an aggregation on only the new rows rather than reprocessing a whole table
  - we can then combine that value with whatever previously aggregated value as calculated for the table
  - we create streams to keep our views/tables up to date:
  ```
  CREATE OR REPLACE STREAM stream_name ON TABLE database.schema.table_name;
  ```
  - the stream will contain metadata:
    - metadata$action - information on the type of operation done to the table 
    - metadata$isupdate - information on whether this row was part of an update operation
    - metadata$row_id
- streams do not return deleted actions
- streams allow us to process incremental changes to a table or view
- they give us a lot of granular and efficient control over our transformations

### 
    
