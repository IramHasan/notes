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

### Stored Procedures
- when we want to go beyond specific computations or capture and reuse more complex logic we use stored procedures
- more complex logic that may require a stored procedure includes:
  - executing a series of operations (multiple SQL statements, transactional logic)
  - they may be used to generate reports
  - execute specific critical business logic
- a set of instructions that typically logic that is more complex and broder in scope than a UDF
- stored procedures can define and call on UDFs within them
- the pattern for working with stored procedures is similar to UDFs:
  - define the procedure
  - definition can be written in SQL, Python, Scala, Java, Javascript
  - call the stored procedure using the call syntax and specifying the name of the procedure
- you can write and call stored procedures directly within Snowflake or in a development environment
- stored procedures can be used to process a stream
- we create the stored procedure
```
CREATE OR REPLACE PROCEDURE procedure_path.procedure_name() 
RETURNS VALUE i.e. STRING
LANGUAGE i.e. PYTHON
RUNTIME_VERSION = X.XX i.e. '3.10'
HANDLER = reference_to_function_containing_logic_for_procedure i.e. 'process_order_header_stream'
PACKAGES = packages-for-logic i.e. 'snowflake-snowpark-python
```
- again we use a double dollar sign delimiter to contain all the logic / code for the stored procedure
- we may query the stream to look for all new `INSERT` actions against our table
- we can filter for specific information such as a specific ID
- we can perform transformations such as aggregations or creating new columns to write to a new table 
- we can automate these procedures to run on a periodic cadence i.e. every 24 hours
- stored procedures make it easy to take large chunks of logic, place them in a centralized location and then reuse them as needed
- common functions of stored procedures:
  - perform aggregations
  - update records
  - perform complex procedural logic
- they become even more powerful when automated
- stored procedures are used to store and define complex logic that can be easily reused at scale

### Dynamic Tables 
- streams allow us to focus on processing incremental changes to an underlying table
- we can focus on specific types of changes to the table as well i.e. `DELETE`, `INSERT`, etc.
- we still need to make choices on what we will do with the data tracked by the stream
- dynamic table - a special type of table in Snowflake that automatically updates itself with the latest data using a predefined query
- helps ensure that the table always reflects the most current information from its source tables
- don't need to write any additional logic to process the changes or types of chanegs to the underlyic table
- allows us to reach a desired end state with our data really quickly
- easily configure the freshness for a dynamic table
- we can specify how often a dynamic table should be refreshed using:
```
LAG = <duration>
```
- dynamic tables can help simplify our data pipeline architecture when refreshing data
- dynamic tables make it reall easy to specify freshness - how often our table should update
- we can use dynamic tables to replace a combination of streams, stored procedures, and tasks
```
CREATE OR REPLACE DYNAMIC TABLE DATABASE.SCHEMA.TABLE_NAME
WAREHOUSE = "WAREHOUSE_NAME"
TARGET_LAG = DURATION i.e. '1 minute'
AS
<QUERY>
```
- we can process incremental changes to data with streams and stored procedures
- we can use dynamic tables for automatic processing of changes to underlying data
- these approaches have benefits and tradeoffs
- in practice, we have source tables with raw data being updated on a continuous basis likely due to an automated process
- if we need fine-grain tracking we must utilize a stream, if we need to perform complex logic based on those details
- combining streams with stored procedures and tasks empowers them by extending what can be done in our procedural logic and giving more control over the refresh schedule
- if we have relatively stable tables, that just require a target data freshness
- dynamic tables are a great way of automating transformations without the overhead of streams or tasks

### Data Transformations in VS Code 
- Snowflake extension for VS Code 
- execute SQL and Python against our Snowflake environment directly from VS Code
- This allows us to connect to our Snowflake environment either using our account URL or the configuration setup in our Snowflake CLI
- We can execute code against the data in the Snowflake environment directly from VS Code
- there is a query history pane, updating the queries executed within VS Code
- we also have an object explorer to help us find the databases, tables, and views being generated

### Best Practices for Data Transformations
- Use SQL, Snowpark
- UDFs for calculations
- stored procedures for complex procedural logic
- streams for change tracking and incremental data processing
- dynamic tables for automatic transformations - set a desired end state by associating transformation logic and a refresh rate for the transformations

## Module 4
### Delivery of Data Products
- the goal of a data pipeline is to take raw data as the input and output something of value in the form of dashboards, serving data to web applications, or feeding other data systems
- delivering a data product is an outcome, but not a final stage
- we maintain a pipeline to account for:
  - changes to source data
  - new sources of data
  - new pipeline requirements
### Data Sharing on Snowflake Marketplace
- the product being delivered when data sharing is the dataset(s)
- we can share data sets through creating public listings in Snowflake's marketplace
- we can create private listings to share with specific consumers
- `Provider Studio` in Snowflake, is where we can create a new listing for a dataset
- We can configure the consumers we want to share the data with
- A very powerful way of delivering a data product to consumers
### Streamlit in Snowflake Applications
- an open source python library for building data apps in python
- apps built with streamlit are easy to deploy and share
- readily available in Snowflake - Streamlit in Snowflake
- We can use Streamlit components with custom user interfaces to build complex data and AI applications
  - we can capture user input in the form of dropdowns, text inputs, radio buttons
  - bring in open source packages to be used in the application
  - build RAG-based chatbots that talk to data in natural language
- Streamlit is natively available within Snowflake, known as "Streamlit in Snowflake"
- We can build data apps using just Python, all within the account
- We can easily share these apps with users in your account
### Snowflake Native Applications
- Snowflake Native Applications are fully contained applications that run entirely  within a Snowflake account
- Apps are built by application providers and listed on the Snowflake Marketplace
- App consumers can discover install and run Snowflake Native Apps in their Snowflake account
- Built with Snowflake as the core application stack - SQL, Snowpark, Streamlit, Snowflake
- Leverage Snowpark container services to run a container image within Snowflake Native App
- Application providers bundle the application into an application package to be distributed on the Snowflake marketplace w/ a billing model
- Consumers are able to manage any and all permissions from within the application and power the app using their compute resources
- Application is able to act on objects within the consumers account provided that the consumer has granted permissions to access those objects
- we can share insights on proprietary data safely and easily to the consumer using a SNA
#### Streamlit in Snowflake apps
- typically bound to accounts (account-specific)
- deployed in a specific snowflake account and shared with team members in that account
#### Snowflake Native Applications
- Listed on the Snowflake Marketplace
- Installed and run in consumer accounts (powered by consumer resources)\
### Best Practices for Data Product Delivery
- High quality transformed data sets is the final product that we are delivering
- Snowflake marketplace can go beyond sharing those objects in your account to sharing the data to users in Snowflake
- Streamlit in Snowflake
- Snowflake Native Applications
- Pipelines are heavily dependant on usecase
- Snowflake is flexible for sharing products
- We want to build continuous end-to-end data pipelines
## Module 5
### What is orchestration
- The key aspect of building a data pipeline that levels up the power and robustness of a pipeline is automation
- ITD framework makes it easy to understand the core moving parts of a data pipeline
- Automation is a key concept that breathes life into a data pipeline
- Automation underpins all aspects of the pipeline
- Example of commonly automated things:
  - data ingestion processes
  - data transformations
  - streams for incremental data processing
- Data products reap the benefits of fresh and up to date data from automation
- An ML model may not want to drift from the up to date data so to maintain it we must provide it with up to date data
- How to harmoniously orchestrate the automation of hundreds of different things
### Automation with tasks

### Orchestration with DAGs
### Recap and best practicies for pipeline orchestration
