# 📚 Chapter 8 - Queries, Modeling, and Transformation
> **Book:** Fundamentals of Data Engineering — Joe Reis & Matt Housley  
> **Status:** ✅ Complete  
> **Date:** March 2026

---

## 🎯 What This Chapter Is About

Chapter 8 covers the transformation stage — where raw data becomes **useful**. It covers queries, data modeling patterns, and transformation techniques that turn raw data ingredients into something consumable by analysts, data scientists, and the business.

> **Key idea:** Always model your data at the lowest grain possible. You can always aggregate up but you can never restore lost detail.

---

## 🗣️ SQL Language Types

| Type | Full Name | Purpose | Common Commands |
|---|---|---|---|
| **DDL** | Data Definition Language | Define database objects | CREATE, DROP, ALTER |
| **DML** | Data Manipulation Language | Manipulate data | SELECT, INSERT, UPDATE, DELETE, MERGE |
| **DCL** | Data Control Language | Control access | GRANT, REVOKE, DENY |
| **TCL** | Transaction Control Language | Control transactions | COMMIT, ROLLBACK |

---

## 🔍 How a Query Works

When you run a SQL query, here is what happens under the hood:

```
1. Parse SQL → check syntax, validate objects, check permissions
2. Convert to bytecode → machine-readable format
3. Query optimizer → reorders and optimises steps for efficiency
4. Execute → produces results
```

The **query optimizer** is the key. It determines the most efficient execution path — reordering joins, applying predicates early, choosing indexes, and minimising data scanned.

---

## ⚡ Query Performance Tips

### Optimise Your JOIN Strategy
- **Pre-join data** that is frequently joined — avoid repeating expensive operations
- Use **CTEs** (Common Table Expressions) instead of nested subqueries for readability
- Be aware of **row explosion** — many-to-many joins that multiply rows unexpectedly
- Apply filters **early** to reduce data before joining

### Use the Explain Plan
- `EXPLAIN` command shows how the database will execute your query
- Monitor: disk, memory, network, query execution time, data scanned, data shuffled
- Use this to identify bottlenecks and poorly performing queries

### Avoid Full Table Scans
- Select only the columns you need — especially in columnar databases
- Use **partitioning** — split tables by date or other fields to reduce data scanned
- Use **clustering** — sort data within partitions to colocate similar values

### Understand Commits and Consistency
- Know whether your database is ACID compliant
- Understand dirty reads — reading uncommitted changes
- Understand snapshot isolation — BigQuery, Snowflake read from a committed snapshot
- **Vacuum** dead records regularly — keeps query plans accurate and tables clean

### Leverage Caching
- Many OLAP databases cache query results
- A cold query runs from scratch — a warm query returns cached results instantly
- Materialized views are another form of caching — precomputed results stored for reuse

---

## 🌊 Streaming Query Patterns

### Fast Follower CDC
```
Production Database → CDC → Analytics Database (slight lag)
```
- Analytics database follows production in near real time
- Protects production from heavy analytical query load
- BigQuery and Apache Druid combine streaming buffer with columnar storage

### Kappa Architecture
- Treats ALL data as events stored in a streaming log
- Data can be queried directly from streaming storage
- Supports both real-time queries AND historical batch queries
- Uses Kafka KSQL, Spark, or Flink for complex queries

### Windows in Streaming Queries

Windows are **small batches triggered dynamically from the data itself:**

| Window Type | How It Works | Use Case |
|---|---|---|
| **Session** | Groups events close together, closes on inactivity gap | User session analytics |
| **Fixed-time (Tumbling)** | Fixed time periods, no overlap | Metrics every 20 seconds |
| **Sliding** | Overlapping windows of fixed length | Rolling averages |

**Watermarks** — a threshold that determines whether late-arriving data falls within a window or is considered late. Critical for accurate streaming analytics.

### Combining Streams
- **Enrichment** — join a stream with external data to add context to events
- **Stream-to-stream joining** — join two streams using a buffer retention interval
- **Streaming DAG** — define complex multi-stream flows as code (Apache Pulsar)

---

## 🏗️ Data Modeling

### What Is a Data Model?
A data model represents the way data relates to the real world. It captures:
- Business logic and rules
- Relationships between entities
- Definitions of key business terms

> 💡 **Grain** = the resolution at which data is stored. Always model at the **lowest grain possible**. Facts should be at the individual event level, not aggregated.

### Three Levels of Data Models

| Level | What It Contains |
|---|---|
| **Conceptual** | Business logic, entities, relationships — often shown as an ER diagram |
| **Logical** | Adds data types, primary keys, foreign keys, more detail |
| **Physical** | Specific databases, schemas, tables, configuration details |

---

## 📐 Normalization

Normalization removes data redundancy and enforces referential integrity. The DRY principle applied to databases.

| Normal Form | Rule |
|---|---|
| **Denormalized** | No normalization — nested and redundant data allowed |
| **1NF** | Each column has a single value. Table has a unique primary key |
| **2NF** | 1NF + no partial dependencies |
| **3NF** | 2NF + no transitive dependencies — each table contains only relevant fields |

A database is considered **normalized** when it reaches 3NF.

---

## 🏭 Batch Data Modeling Techniques

### Inmon (1990)

**Philosophy:** Normalize everything first, serve analytics from data marts.

**Data warehouse definition:** Subject-oriented, integrated, nonvolatile, time-variant.

```
Source Systems → ETL → 3NF Data Warehouse → ETL → Data Marts → Reports
```

**Characteristics:**
- Highly normalized (3NF) central data warehouse
- Single source of truth for the entire organization
- Departments served through separate data marts
- Strict emphasis on ETL before loading

**Best for:** Large enterprises that need a complete, integrated, organization-wide data model.

---

### Kimball (Early 1990s)

**Philosophy:** Bottom-up — model data directly for analytics. Denormalize for speed.

```
Source Systems → ETL → Star Schema (Facts + Dimensions) → BI Reports
```

**Two table types:**

**Fact Tables:**
- Contain quantitative, event-related data
- Immutable and append-only
- Narrow and long — few columns, many rows
- Always at the lowest grain
- All values are numbers — no strings
- Reference dimension tables through foreign keys

**Dimension Tables:**
- Provide context for facts — the what, where, and when
- Wide and short — many columns, fewer rows
- Denormalized — duplicate data is acceptable
- Examples: date dimension, customer dimension, product dimension

**Star Schema:**
```
           [Date Dimension]
                  |
[Customer Dim] ─ [FACT TABLE] ─ [Product Dim]
                  |
           [Channel Dim]
```

**Slowly Changing Dimensions (SCD):**

| Type | How Changes Are Handled |
|---|---|
| **Type 1** | Overwrite existing record — no history retained |
| **Type 2** | Insert new record, flag old one — full history retained ✅ Most common |
| **Type 3** | Add new field for change — limited history |

**Conformed dimensions** = dimensions shared and reused across multiple star schemas.

**Best for:** Department-level analytics, BI reporting, fast query performance.

---

### Data Vault (1990s, Dan Linstedt)

**Philosophy:** Source-aligned, insert-only, ultra-flexible. No concept of good or bad data.

**Three table types:**

| Table | Purpose | Contains |
|---|---|---|
| **Hub** | Stores business keys | Hash key, load date, record source, business key |
| **Link** | Tracks relationships between hubs | Hash keys from connected hubs |
| **Satellite** | Stores descriptive attributes | Hub hash key, load date, attributes |

```
[Hub: Products] ─── [Link: OrderProduct] ─── [Hub: Orders]
       |                                              |
[Satellite: ProductDetails]              [Satellite: OrderDetails]
```

**Characteristics:**
- Insert-only — data is never changed after loading
- Highly agile — adding new sources just means adding new hubs and links
- Business logic is applied at query time, not at load time
- Often used as a landing zone, with Kimball star schema for serving

**Best for:** Environments with rapidly changing source systems, auditability requirements.

---

### Wide Denormalized Tables

**Philosophy:** Throw everything into one big table and query it.

- Hundreds of columns, often with nested JSON data
- No strict modeling — fast to load, flexible to query
- Works well in columnar databases where nulls are free
- Suitable for streaming data and fast-moving schemas
- Good for early-stage companies that want quick insights

**Best for:** Exploratory analytics, streaming data, early-stage companies.

---

### Modeling Streaming Data

No consensus approach exists yet. Key principles from streaming experts:
- Keep a **flexible schema** — don't enforce rigid models
- Assume source systems provide correct data as it exists today
- Store recent streaming and historical data together for combined queries
- Anticipate schema changes and design for them

---

## 🔧 Transformation Patterns

### Batch Transformations

**Distributed Joins:**

| Join Type | When Used | Performance |
|---|---|---|
| **Broadcast join** | One table is small enough to fit on a single node | Fast — low resource |
| **Shuffle hash join** | Both tables are large — redistributed by key | Slower — high resource |

**ETL vs ELT:**

| | ETL | ELT |
|---|---|---|
| **Transform when?** | Before loading | After loading |
| **Where?** | External system | Inside the warehouse |
| **Best for** | Legacy on-premises | Modern cloud warehouses |

**Update Patterns:**

| Pattern | What It Does | When To Use |
|---|---|---|
| **Truncate and reload** | Wipe and rebuild the entire table | Full refresh, simple pipelines |
| **Insert only** | Append new records only | Historical audit trails |
| **Soft delete** | Mark records as deleted, don't remove | When history matters |
| **Hard delete** | Permanently remove records | GDPR compliance, performance |
| **Upsert/Merge** | Update existing + insert new | Incremental updates, CDC |

> ⚠️ **Warning:** Avoid running many small inserts or merges on columnar databases. Always batch your updates. Near-real-time merges will destroy most columnar databases.

**MapReduce:**
- Map tasks process individual data blocks in parallel
- Shuffle redistributes data by key across nodes
- Reduce aggregates results on each node
- The foundation of all distributed data processing — Spark, BigQuery, Snowflake all build on these concepts

### Materialized Views and Virtualization

**Views:**
- Saved queries that act like tables
- Recomputed every time they are queried — no precomputation
- Used for: security, deduplication, common access patterns

**Materialized Views:**
- Precomputed views stored for fast repeated access
- Updated automatically when source data changes
- De facto transformation step managed by the database
- Query optimizers can use them even for queries that don't directly reference them

**Federated Queries:**
- Query external databases from inside your data warehouse
- Example: Snowflake external tables reading directly from S3
- Useful for combining data from multiple sources without ingesting all of it

**Data Virtualization:**
- Query engine that reads from external sources without storing data internally
- Examples: Trino (Starburst), Presto
- Good for combining data across organizational silos
- Don't use against production databases — it will overload them

### Streaming Transformations

- **Enrichment** — add context to events from external data sources
- **Streaming DAGs** — define complex multi-stream processing flows as code
- **Micro-batch vs true streaming:**
  - Micro-batch (Spark Streaming) — runs every few seconds to minutes, simpler
  - True streaming (Flink, Beam) — processes one event at a time, lower latency
  - Choose based on latency requirements and team expertise

---

## 🛠️ Key Tools

| Tool | What It Does |
|---|---|
| **dbt** | ELT transformation tool — write SQL transformations as code with version control |
| **Apache Spark** | Distributed processing engine for large-scale batch and streaming transformations |
| **Apache Flink** | True streaming processing engine |
| **Apache Beam** | Unified batch and streaming programming model |
| **Trino/Presto** | Data virtualization — federated queries across multiple sources |
| **Apache Airflow** | Orchestrates transformation pipelines as DAGs |

---

## 📝 Key Terms Learned

| Term | Simple Meaning |
|---|---|
| **DDL** | Data Definition Language — defines database objects |
| **DML** | Data Manipulation Language — manipulates data |
| **DCL** | Data Control Language — controls access permissions |
| **TCL** | Transaction Control Language — controls transactions |
| **Explain plan** | Shows how the query optimizer will execute a query |
| **Vacuuming** | Removing dead records to improve query performance |
| **Star schema** | Kimball fact table surrounded by dimension tables |
| **Fact table** | Quantitative event data at the lowest grain |
| **Dimension table** | Descriptive context for facts |
| **SCD** | Slowly Changing Dimension — tracks changes to dimension records |
| **Conformed dimension** | Shared dimension used across multiple star schemas |
| **Data vault** | Insert-only source-aligned model with hubs, links, satellites |
| **CTE** | Common Table Expression — reusable named subquery within a query |
| **Broadcast join** | Small table sent to all nodes to join with large distributed table |
| **Shuffle hash join** | Large tables redistributed by key across nodes for joining |
| **Upsert** | Update existing records or insert new ones |
| **Materialized view** | Precomputed view stored for fast repeated access |
| **Federated query** | Query that reads from external data sources |
| **Data virtualization** | Querying external sources without storing data internally |
| **Watermark** | Threshold for determining late-arriving data in streaming windows |
| **Streaming DAG** | Directed acyclic graph of stream processing steps |
| **Row explosion** | Unintended multiplication of rows in many-to-many joins |
| **Grain** | The resolution at which data is stored — should be as low as possible |
| **Wide table** | Highly denormalized table with hundreds of columns |
| **MapReduce** | Map + shuffle + reduce — basis of all distributed data processing |

---

## 🙋 How This Applies to Me

| Chapter Concept | My Action Plan |
|---|---|
| Kimball star schema | **Priority** — build a star schema project for my portfolio |
| dbt | Learn dbt deeply — the standard tool for ELT transformations |
| Dimensional modelling | Study Kimball Group resources (recommended by Entelect) |
| SCD Type 2 | Practice implementing slowly changing dimensions |
| Query optimisation | Run explain plans on existing AHRI queries and tune them |
| Data vault | Understand the concept — good for flexible source systems like AHRI |
| Streaming transformations | Study Apache Flink for future real-time pipeline projects |
| Materialized views | Use in Snowflake or BigQuery for performance optimisation |
| Normalization | Practice 3NF modelling on clinical datasets at AHRI |
| ELT with dbt | Replace manual SQL transformation scripts with dbt models |

---

## 🔗 Resources
- [Fundamentals of Data Engineering — Google Drive](https://drive.google.com/file/d/1pu_JTMF5jQdDrtvjzSGaQNBpFiFglnl_/view?usp=drive_link)
- [Kimball Group — Dimensional Modelling](https://www.kimballgroup.com)
- [dbt Documentation](https://docs.getdbt.com)
- [The Data Warehouse Toolkit — Ralph Kimball](https://www.kimballgroup.com/data-warehouse-business-intelligence-resources/books/data-warehouse-dw-toolkit/)
- [Apache Spark Documentation](https://spark.apache.org/docs/latest/)
- [Apache Flink Documentation](https://flink.apache.org/docs/)
- [Trino Documentation](https://trino.io/docs/current/)

---

*Part of my self-study journey toward becoming a Data Engineer — documented on GitHub as proof of learning.*
