# Chapter 8 - Queries, Modeling, and Transformation

## Definition
Queries, modeling, and transformation are how raw data becomes useful.
A query retrieves data. A transformation persists and reshapes data for downstream use.
A data model represents the way data relates to the real world and captures
business logic, definitions, and workflows in a structured way.

## SQL Language Types
- DDL - Data Definition Language - CREATE, DROP, UPDATE database objects
- DML - Data Manipulation Language - SELECT, INSERT, UPDATE, DELETE data
- DCL - Data Control Language - GRANT, REVOKE access permissions
- TCL - Transaction Control Language - COMMIT, ROLLBACK transactions

## Query Performance Tips
- Use explain plan to understand how the database executes your query
- Avoid full table scans - select only the columns and rows you need
- Pre-join frequently joined data to avoid repeating expensive operations
- Use CTEs instead of nested subqueries for readability and performance
- Partition and cluster large tables to reduce data scanned
- Leverage cached query results to avoid rerunning expensive queries
- Vacuum dead records regularly to keep query plans accurate
- Understand how your database handles commits and consistency

## Streaming Query Patterns
- Fast follower CDC - analytics database follows production database in near real time
- Kappa architecture - treat all data as events, query directly from streaming storage
- Windows - small batches triggered dynamically from the data itself
  - Session window - groups events close together, closes on inactivity gap
  - Fixed-time (tumbling) window - fixed time periods on a fixed schedule
  - Sliding window - overlapping windows of fixed length
- Watermarks - thresholds for determining late-arriving data in windows
- Stream enrichment - join a stream with other data to enhance events
- Stream-to-stream joining - join two streams using a buffer retention interval

## Data Modeling Approaches

### Normalization (Inmon-style)
- 1NF - each column has a single value, table has a unique primary key
- 2NF - no partial dependencies
- 3NF - no transitive dependencies, each table contains only relevant fields
- Goal: remove redundancy and enforce referential integrity (DRY principle)

### Inmon
- Data warehouse is subject-oriented, integrated, nonvolatile, time-variant
- Highly normalized 3NF data warehouse — single source of truth
- ETL data from source systems into normalized warehouse
- Serve departments through downstream data marts
- Bottom line: normalize first, serve analytics from data marts

### Kimball
- Bottom-up approach — model data directly for analytics use
- Two table types: facts (quantitative events) and dimensions (context)
- Star schema — one fact table surrounded by dimension tables
- Fewer joins than Inmon — faster query performance
- Slowly Changing Dimensions (SCD) track changes over time
  - Type 1 - overwrite existing record (no history)
  - Type 2 - insert new record, flag old one (full history)
  - Type 3 - add new field for change (limited history)
- Bottom line: denormalize for speed, model for business analytics

### Data Vault
- Separates structural aspects from attributes
- Insert-only, source-aligned, no notion of good or bad data
- Three table types: hubs (business keys), links (relationships), satellites (attributes)
- Very agile and flexible — handles schema changes well
- Bottom line: great for raw data landing zone, combine with Kimball for serving

### Wide Denormalized Tables
- One large table with hundreds of columns, often with nested data
- No strict modeling — load fast and query flexible
- Works well in columnar databases where nulls are free
- Better for streaming data and fast-moving schemas
- Bottom line: simple and fast, but loses strict business logic

## Transformation Patterns

### Batch Transformations
- Distributed joins: broadcast join (small table) or shuffle hash join (large tables)
- ETL - transform before loading into the warehouse
- ELT - load first, transform inside the warehouse
- Update patterns:
  - Truncate and reload - wipe table and reload from scratch
  - Insert only - append new records, never update
  - Delete - hard delete (remove) or soft delete (flag as deleted)
  - Upsert/merge - update existing records, insert new ones
- MapReduce - map tasks, shuffle, reduce — basis of all distributed processing
- Data wrangling - cleaning and shaping messy, malformed data

### Materialized Views and Virtualization
- Views - saved queries that act like tables, recomputed each time
- Materialized views - precomputed views stored for fast access
- Federated queries - query external sources from inside your data warehouse
- Data virtualization - query engine that reads from external sources without storing data

### Streaming Transformations
- Enrich streams with data from other sources
- Stream-to-stream joins using buffer windows
- Streaming DAGs - define complex multi-stream flows as code
- Micro-batch vs true streaming - choose based on latency requirements

## Key Insight
Data modeling is not optional. Without a deliberate data model,
you end up with a data swamp — redundant, mismatched, and wrong data.
Always model your data at the lowest grain possible.
You can always aggregate up but you cannot restore lost detail.

## Key Terms I Learned
- DDL - Data Definition Language, defines database objects
- DML - Data Manipulation Language, manipulates data in objects
- DCL - Data Control Language, controls access permissions
- TCL - Transaction Control Language, controls transactions
- Explain plan - shows how the query optimizer will execute a query
- Vacuuming - removing dead records to improve query performance
- Star schema - Kimball fact table surrounded by dimension tables
- Fact table - quantitative event data at the lowest grain
- Dimension table - descriptive context for facts
- SCD - Slowly Changing Dimension, tracks changes to dimension records
- Conformed dimension - shared dimension used across multiple star schemas
- Data vault - insert-only source-aligned data model with hubs, links, satellites
- CTE - Common Table Expression, reusable named subquery within a query
- Broadcast join - small table sent to all nodes to join with large distributed table
- Shuffle hash join - large tables redistributed by key across nodes for joining
- Upsert - update existing records or insert new ones
- Materialized view - precomputed view stored for fast repeated access
- Federated query - query that reads from external data sources
- Data virtualization - querying external sources without storing data internally
- Watermark - threshold for determining late-arriving data in streaming windows
- Streaming DAG - directed acyclic graph of stream processing steps
- Row explosion - unintended multiplication of rows during many-to-many joins

## What This Means for Me
At AHRI I already use ETL pipelines and SQL transformations.
I need to improve my knowledge of dimensional modelling (Kimball)
which Entelect specifically asked about.
I should practice building star schemas with fact and dimension tables.
dbt is the modern tool for ELT transformations and I should learn it deeply
as it is becoming the standard in data engineering.
Understanding when to use ELT vs ETL and how to optimise
SQL queries in columnar databases like Snowflake and BigQuery
will be critical for working at Entelect.
