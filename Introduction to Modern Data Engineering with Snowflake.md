# Introduction to Modern Data Engineering with Snowflake
## Module 3
### Data Transformations
- raw data won't have the insights we are looking for
- before extracting insights we must transform data to an end state usable for analytics
- data transformations are formed against the raw data to get closer to our desired insights
- common data transformations:
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
- perform data transformations in python, Java, Scala
- configure the runtimes for languages i.e. using different versions of Python
- DataFrame API for processing data and performing data transformations
- **Flow:** create dataframes using the data &rarr; perform transformations &rarr; write results to new views / tables
- Similar to Pyspark / other dataframe APIs
- data transformations can be pushed to Snowflake's compute engine
- Snowpark code can be written in:
  - scripts / code base 
  - Snowflake notebook
  - IDE using Snowflake extension
