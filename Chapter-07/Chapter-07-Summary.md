# Chapter 7 - Ingestion

## Definition
Data ingestion is the process of moving data from one place to another.
Specifically it means moving data from source systems into storage
as an intermediate step in the data engineering lifecycle.
A data pipeline is the combination of architecture, systems, and processes
that move data through the stages of the data engineering lifecycle.

## Key Engineering Considerations
- What is the use case for this data?
- Can I reuse existing data instead of ingesting duplicates?
- Where is the data going and in what format?
- How often should it be updated?
- What is the expected data volume?
- Is the source data of good quality for downstream use?
- Does streaming data need in-flight processing?

## Bounded vs Unbounded Data
- Unbounded - data as it exists in reality, continuously flowing
- Bounded - data bucketed across a boundary such as time
- All data is unbounded until it is bounded
- Batch ingestion artificially bounds naturally unbounded data

## Ingestion Frequency Spectrum
- Batch - data processed in large chunks on a schedule (daily, weekly)
- Micro-batch - small batches processed every few seconds or minutes
- Real-time (streaming) - events processed continuously as they arrive

## Synchronous vs Asynchronous Ingestion
- Synchronous - stages are tightly coupled, one failure stops everything
- Asynchronous - stages are independent, events processed as they arrive
- Always prefer asynchronous ingestion for resilience and scalability

## Batch Ingestion Patterns
- Snapshot extraction - full copy of source data every run
- Differential extraction - only changed records since last run (more efficient)
- File-based export - source exports files, ingestion system picks them up
- ETL - Extract Transform Load (transform before loading)
- ELT - Extract Load Transform (load first, transform in destination)
- Data migration - moving large volumes of data to a new system

## Streaming Ingestion Considerations
- Schema evolution - fields may be added or removed without warning
- Late arriving data - events may arrive after expected ingestion time
- Ordering - distributed systems may deliver messages out of order
- Replay - ability to reprocess historical events from a point in time
- TTL (Time to Live) - how long events are retained before expiring
- Dead-letter queue - stores events that failed to ingest for later diagnosis
- Message size - ensure your streaming platform supports your max event size

## Ways to Ingest Data
- Direct database connection via JDBC or ODBC
- Change Data Capture (CDC) - batch or continuous
- APIs - REST, GraphQL, webhooks
- Message queues and event streaming platforms
- Managed data connectors (Fivetran, Airbyte)
- Object storage file exchange
- EDI - electronic data interchange (older file-based exchange)
- Shell scripting and CLI tools
- Web scraping
- Transfer appliances for very large migrations (100TB or more)
- Data sharing platforms

## Change Data Capture (CDC)
- Batch CDC - query for records changed since last run using updated_at field
- Continuous CDC - reads database write-ahead log for every change as it happens
- Continuous CDC supports near real-time ingestion and analytics
- CDC consumes database resources — test before enabling on production systems

## Key Insight
Ingestion is the plumbing of data engineering.
Without reliable ingestion pipelines, nothing downstream can work.
Monitor ingestion carefully — bad or missing data silently corrupts
reports, models, and business decisions before anyone notices.

## Key Terms I Learned
- Data ingestion - moving data from source systems into storage
- Data pipeline - architecture, systems, and processes that move data through the lifecycle
- Bounded data - data with a defined boundary such as a time window
- Unbounded data - continuous flowing data with no defined boundary
- Batch ingestion - processing data in large chunks on a schedule
- Micro-batch - very small batches processed at short intervals
- Synchronous ingestion - tightly coupled stages where failure stops everything
- Asynchronous ingestion - loosely coupled stages that process events independently
- Snapshot extraction - full copy of source data on each run
- Differential extraction - only changes since the last run
- Dead-letter queue - stores events that failed to ingest
- TTL - Time to Live, how long events are retained before expiring
- Replay - reprocessing historical events from a point in time
- Schema registry - metadata repository that tracks schema versions for streaming
- CDC - Change Data Capture, tracking every change in a source database
- JDBC - Java Database Connectivity, standard database connection protocol
- ODBC - Open Database Connectivity, standard database connection protocol
- Transfer appliance - physical device for migrating very large datasets
- Datastrophe - a data disaster caused by bad data driving wrong business decisions
- Managed connector - third party service that handles ingestion connections for you

## What This Means for Me
At AHRI I already build ETL pipelines using Python, SQL, and Apache Airflow.
I need to move toward more asynchronous patterns and implement proper
monitoring and data quality checks at the ingestion stage.
Setting up CDC from clinical databases would allow near real-time analytics
which is one of the skills Entelect specifically mentioned wanting to see.
I should also evaluate managed connectors like Airbyte or Fivetran
to reduce the undifferentiated heavy lifting of maintaining custom connectors.
