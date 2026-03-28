# 📚 Chapter 7 - Ingestion
> **Book:** Fundamentals of Data Engineering — Joe Reis & Matt Housley  
> **Status:** ✅ Complete  
> **Date:** March 2026

---

## 🎯 What This Chapter Is About

Chapter 7 covers **data ingestion** — the process of moving data from source systems into storage. Ingestion is where data engineers begin actively designing pipeline activities. Without reliable ingestion, nothing downstream can work.

> **Key idea:** Ingestion is the plumbing of data engineering. Monitor it carefully — bad or missing data silently corrupts reports and models before anyone notices.

---

## 📖 What Is Data Ingestion?

```
Source System → [INGESTION] → Storage → Transformation → Serving
```

**Data ingestion** = moving data from point A to point B.

**Data integration** = combining data from multiple sources into a new dataset (covered in Chapter 8).

**Data pipeline** = the combination of architecture, systems, and processes that move data through the stages of the data engineering lifecycle.

> 💡 A data pipeline is deliberately flexible — it could be a traditional ETL system or a complex cloud pipeline pulling from 100 sources and training ML models. It should fit any need along the lifecycle.

---

## 📋 Key Engineering Considerations

Before building any ingestion system, ask these questions:

| Question | Why It Matters |
|---|---|
| What is the use case? | Determines ingestion frequency and format |
| Can I reuse existing data? | Avoids duplicate ingestion and storage costs |
| Where is the data going? | Determines destination format requirements |
| How often should it update? | Batch vs streaming decision |
| What volume is expected? | Affects scalability design |
| What format is the data in? | Affects serialization choices |
| Is the source data of good quality? | Determines data quality checks needed |
| Does streaming need in-flight processing? | Affects pipeline architecture |

---

## 📦 Bounded vs Unbounded Data

```
REALITY (unbounded)          BATCH SYSTEM (bounded)
Events happen continuously → Artificially cut into discrete chunks
24/7/365                     Daily, hourly, weekly batches
```

**Key mantra:** *All data is unbounded until it is bounded.*

- **Unbounded data** = data as it exists in reality — continuously flowing events
- **Bounded data** = data bucketed across a boundary such as time or size
- Batch ingestion artificially bounds naturally unbounded data
- Streaming ingestion preserves the unbounded nature of data

---

## ⏱️ Ingestion Frequency

The spectrum from slow to fast:

```
Annual batch → Monthly → Daily → Hourly → Micro-batch → Real-time streaming
     (slow)                                                      (fast)
```

| Frequency | Description | Use Case |
|---|---|---|
| **Batch** | Large chunks on a schedule | Daily reporting, ML training |
| **Micro-batch** | Very small batches every few seconds/minutes | Near real-time dashboards |
| **Real-time** | Events processed as they arrive | IoT, fraud detection, live analytics |

> 💡 **"Real-time" is never truly real-time.** Every system has some latency. The more accurate term is **near real-time**. We use real-time and streaming interchangeably.

Even with streaming ingestion, batch processing downstream is still very common. Engineers choose where batch boundaries will occur in the lifecycle.

---

## 🔄 Synchronous vs Asynchronous Ingestion

### Synchronous Ingestion ❌ (avoid where possible)
```
Step A → Step B → Step C
(If A fails, B and C cannot start)
```
- Tightly coupled — one failure stops everything
- Common in older ETL systems
- A 24-hour pipeline that fails at hour 23 must restart from the beginning
- **Anti-pattern:** avoid where possible

### Asynchronous Ingestion ✅ (preferred)
```
Events → Buffer → Processing (many parallel workers)
```
- Loosely coupled — each event processed independently
- A failure in one event does not affect others
- Uses message queues and buffers to handle rate spikes
- Much more resilient and scalable

**Example asynchronous architecture on AWS:**
```
Web App → Kinesis Data Streams → Apache Beam → Kinesis Firehose → S3
```

---

## 📤 Serialization and Deserialization

- Serialization = encoding data into a standard format for transmission
- Deserialization = decoding data at the destination
- Always verify your destination can deserialize what your source serializes
- Mismatched serialization formats are a common cause of silent data pipeline failures

---

## 📊 Throughput and Scalability

- Ingestion should never be a bottleneck — but it often is in practice
- Design systems to scale up AND scale down based on demand
- Build in **buffering** to handle burst data — prevents data loss during rate spikes
- Use managed services that handle scaling automatically where possible
- Don't reinvent the wheel — managed ingestion tools handle most of this for you

---

## 🛡️ Reliability and Durability

| Concept | What It Means |
|---|---|
| **Reliability** | High uptime and proper failover for ingestion systems |
| **Durability** | Data is not lost or corrupted during ingestion |

Some data sources (IoT devices, caches) do not retain data if not ingested immediately. Once lost, it is gone forever. Evaluate the cost and risk of data loss and build appropriate redundancy.

**Key trade-off:** More redundancy = more cost. Find the right balance for your use case.

---

## 📨 Payload Characteristics

Every data payload has these characteristics:

| Characteristic | What It Means |
|---|---|
| **Kind** | Type (tabular, image, text) and format (CSV, Parquet, JPG) |
| **Shape** | Dimensions — rows and columns, image pixels, audio channels |
| **Size** | Number of bytes — can range from bytes to terabytes |
| **Schema** | Structure — field names and data types |
| **Metadata** | Data about the data — timestamps, source, version |

---

## 🔀 Push vs Pull vs Poll

| Pattern | How It Works | Example |
|---|---|---|
| **Push** | Source sends data to the target | Webhooks, CDC event streams |
| **Pull** | Target fetches data from the source | ETL batch queries, API calls |
| **Poll** | Target periodically checks the source for changes | Checking a folder for new files |

---

## 📦 Batch Ingestion Patterns

### Snapshot vs Differential Extraction

| | Full Snapshot | Differential (Incremental) |
|---|---|---|
| **What it does** | Copies entire dataset every run | Only copies changes since last run |
| **Pros** | Simple, always complete | Less data, faster, cheaper |
| **Cons** | Expensive, large data volumes | More complex to implement |

### File-Based Export and Ingestion
- Source system exports data to files
- Ingestion system picks up the files
- Common file exchange methods: S3, SFTP, SCP, EDI
- Good for security — source controls what data is exported
- Common formats: CSV (error-prone), Parquet, Avro, ORC (preferred)

### ETL vs ELT
| | ETL | ELT |
|---|---|---|
| **Transform when?** | Before loading | After loading |
| **Where?** | External transformation system | Inside the destination (cloud warehouse) |
| **Best for** | Legacy on-premises systems | Modern cloud data warehouses |

### Inserts, Updates, and Batch Size
- Columnar databases perform poorly with many small inserts
- Always prefer fewer, larger batch operations over many small ones
- Know the optimal update patterns for your specific database technology
- BigQuery: use stream buffer instead of individual SQL inserts
- Apache Druid and Pinot: built for high insert rates

### Data Migration
- Moving data to a new database or environment in bulk
- Schema management is the biggest challenge — schemas rarely match perfectly
- Always test with a sample before running the full migration
- Use object storage as an intermediate staging area
- Moving pipeline connections to the new system is often harder than moving the data itself

---

## 🌊 Streaming Ingestion Considerations

### Schema Evolution
Fields may be added, removed, or changed without warning. Handle this by:
- Using a **schema registry** to track schema versions
- Setting up a **dead-letter queue** for events that fail to process
- Communicating proactively with upstream teams about planned changes

### Late-Arriving Data
- Events may arrive after their expected ingestion time
- Always distinguish between **event time** (when it happened) and **ingestion time** (when you received it)
- Set a cutoff time after which late data is no longer processed

### Ordering and Multiple Delivery
- Distributed streaming systems may deliver messages **out of order**
- Messages may be delivered **more than once** (at-least-once delivery)
- Design your processing to be **idempotent** — processing the same event twice produces the same result

### Replay
- Ability to reprocess historical events from any point in time
- Critical for backfilling missing data or fixing processing errors
- Supported by: Kafka, Amazon Kinesis, Google Pub/Sub

### TTL (Time to Live)
- How long events are retained before automatically expiring
- Too short → events disappear before processing
- Too long → backlog of unprocessed messages builds up

| Platform | Max Retention |
|---|---|
| Google Pub/Sub | 7 days |
| Amazon Kinesis | 365 days |
| Apache Kafka | Indefinite (limited by disk) |

### Dead-Letter Queue
```
Incoming events
      ↓
[INGESTION SYSTEM]
      ↓                    ↓
Good events → Consumer    Bad events → Dead-Letter Queue
                                            ↓
                                    Engineer diagnoses and fixes
```
- Segregates problematic events from good events
- Prevents bad events from blocking the pipeline
- Engineers can reprocess events after fixing the underlying cause

---

## 🔌 Ways to Ingest Data

### Direct Database Connection (JDBC / ODBC)
- JDBC = Java Database Connectivity
- ODBC = Open Database Connectivity
- Both provide standard interfaces to query databases
- Support parallel connections to increase throughput
- Being replaced by native file export (Parquet, ORC) and direct REST APIs for many use cases

### Change Data Capture (CDC)

**Batch CDC:**
- Query for records changed since last run using an `updated_at` field
- Simple but misses intermediate changes (e.g., 5 updates in 24 hours → only last one captured)

**Continuous CDC:**
- Reads the database **write-ahead log** for every change as it happens
- Captures full change history — every insert, update, delete
- Near real-time — changes available in seconds
- Most common tool: **Debezium** (open source, works with Kafka)

```
PostgreSQL write-ahead log → Debezium → Apache Kafka → Target system
```

**CDC considerations:**
- Consumes database resources (CPU, memory, disk, network)
- Always test on a non-production system first
- Use a read replica for batch CDC to avoid overloading production

### APIs
- REST — most common, stateless HTTP-based
- GraphQL — flexible queries across multiple data models
- Webhooks — source pushes events to your endpoint (reverse API)
- gRPC — Google's efficient binary RPC framework

**API ingestion advice:**
- Use client libraries where available — remove boilerplate
- Use managed connectors (Fivetran, Airbyte) for common APIs
- Build custom connectors only for APIs with no existing support
- Use version control, CI/CD, and testing for all custom API code

### Managed Data Connectors
Purpose-built services that handle ingestion connections for you:

| Tool | Type | Best For |
|---|---|---|
| **Fivetran** | Commercial SaaS | Fully managed, hundreds of connectors |
| **Airbyte** | Open source / Commercial | Self-hosted or managed |
| **Stitch** | Commercial SaaS | Simple cloud-based ingestion |
| **Matillion** | Commercial | ETL/ELT for cloud data warehouses |

> 💡 **Always use managed connectors where available.** Building and maintaining custom connectors is undifferentiated heavy lifting.

### Message Queues and Event Streaming
- Kafka, Kinesis, Pub/Sub — consume events from producers
- Both source systems AND ingestion systems simultaneously
- Pull subscriptions are the default for most data engineering applications
- Push subscriptions available in Pub/Sub and RabbitMQ

### Object Storage File Exchange
- Most secure and scalable method for file-based ingestion
- Supports files of any type and size
- Use signed URLs for temporary access sharing

### EDI (Electronic Data Interchange)
- Older file exchange methods — email, flash drive, FTP
- Still exists at many organisations with legacy systems
- Automate where possible — set up email triggers to save files to object storage

### Shell and CLI Tools
- Shell scripting still widely used for ingestion workflows
- Cloud vendors provide robust CLI tools (AWS CLI, gcloud CLI)
- Suitable for smaller data sources where simpler approaches work
- Graduate to proper orchestration as complexity grows

### Webhooks
- Source system calls YOUR endpoint when events occur
- You are responsible for receiving, storing, and processing events
- Build on managed services: Lambda (receive) → Kinesis (buffer) → S3 (store)

### Web Scraping
- Automatically extracts data from web pages
- Legal and ethical grey area — check terms of service first
- Don't create DoS attacks — pace your scraping appropriately
- HTML structures change frequently — high maintenance burden
- Always ask: is this data available through an official API instead?

### Transfer Appliances
For massive data migrations (100 TB or more):
- Order a physical storage device from a cloud provider
- Load your data onto the device and ship it back
- Cloud provider uploads the data for you
- Much faster and cheaper than transferring 100TB over the internet

| Service | Provider | Scale |
|---|---|---|
| AWS Snowball | Amazon | Up to petabytes |
| AWS Snowmobile | Amazon | Exabytes (comes in a semitrailer!) |
| Azure Data Box | Microsoft | Up to petabytes |

---

## 👥 Whom You'll Work With

### Upstream: Software Engineers
- The biggest disconnect in data engineering is between software engineers and data engineers
- Software engineers see data engineers as downstream consumers of their "data exhaust"
- Fix this by **inviting software engineers to be stakeholders** in data engineering outcomes
- Improve communication — most schema changes and data quality issues can be prevented through early communication

### Downstream: Data Consumers
- Data scientists, analysts, business users, executives
- Don't only focus on sophisticated projects — automating basic ingestion for marketing or finance teams has huge business value
- Honest, early communication with stakeholders prevents surprises

---

## 🔒 Undercurrents Applied to Ingestion

### Security
- Data moving between systems creates security vulnerabilities
- Keep data within a VPC where possible — never expose on the public internet
- Use VPN or dedicated private connections between cloud and on-premises
- Always encrypt data in transit
- Evaluate: do you actually NEED the sensitive data you are ingesting? If not, drop it before storage

### Data Management
- Ingestion is the starting point for data lineage and cataloging
- Handle schema changes proactively — communicate with upstream teams
- Apply tokenization and hashing at ingestion time for sensitive data
- Implement "touchless production" — develop and test on simulated data, not live sensitive data
- "Broken glass" process: require two-person approval for production access to sensitive data

### DataOps
- Monitor ingestion pipelines from day one — don't wait until deployment
- Track: uptime, latency, data volumes, event creation time, ingestion time, process time
- Know the normal data volumes so you can detect anomalies
- Build automated data quality checks into the ingestion stage
- Have an incident response plan — what happens when a source system goes offline?

> ⚠️ **Datastrophe:** A data disaster caused by bad data driving wrong business decisions. Silent data quality failures can go undetected for months.

### Orchestration
- Start simple (cron jobs) but graduate to proper orchestration as complexity grows
- True orchestration schedules complete task graphs — not just individual tasks
- Airflow, Dagster, Prefect: all support complex dependency graphs for ingestion pipelines

### Software Engineering
- Write decoupled code — avoid tight dependencies on source or destination systems
- Use version control and code review for all ingestion code
- Implement proper testing — including data quality tests
- Consider managed services first — build custom code only where it adds value

---

## 📝 Key Terms Learned

| Term | Simple Meaning |
|---|---|
| **Data ingestion** | Moving data from source systems into storage |
| **Data pipeline** | Architecture, systems, and processes moving data through the lifecycle |
| **Bounded data** | Data with a defined boundary such as a time window |
| **Unbounded data** | Continuous flowing data with no defined boundary |
| **Batch ingestion** | Processing data in large chunks on a schedule |
| **Micro-batch** | Very small batches processed at short intervals |
| **Synchronous ingestion** | Tightly coupled — one failure stops everything |
| **Asynchronous ingestion** | Loosely coupled — events processed independently |
| **Snapshot extraction** | Full copy of source data on every run |
| **Differential extraction** | Only changes since the last run |
| **Dead-letter queue** | Stores events that failed to ingest for later diagnosis |
| **TTL** | Time to Live — how long events are retained before expiring |
| **Replay** | Reprocessing historical events from a point in time |
| **Schema registry** | Metadata repository tracking schema versions for streaming |
| **CDC** | Change Data Capture — tracking every change in a source database |
| **JDBC** | Java Database Connectivity — standard database connection protocol |
| **ODBC** | Open Database Connectivity — standard database connection protocol |
| **Transfer appliance** | Physical device for migrating very large datasets |
| **Datastrophe** | A data disaster caused by bad data driving wrong decisions |
| **Managed connector** | Third-party service handling ingestion connections |
| **Idempotent** | Processing the same event multiple times produces the same result |
| **Write-ahead log** | Database log recording every change before it is applied |
| **Debezium** | Popular open source CDC tool that reads database logs |

---

## 🙋 How This Applies to Me

| Chapter Concept | My Action Plan |
|---|---|
| CDC | Implement continuous CDC from AHRI clinical databases using Debezium |
| Asynchronous ingestion | Refactor synchronous pipelines to use event-driven patterns |
| Managed connectors | Evaluate Airbyte for replacing custom API connectors at AHRI |
| Monitoring | Add ingestion monitoring to all existing Airflow pipelines |
| Data quality checks | Build data quality tests into ingestion stage using Great Expectations |
| Schema registry | Study Confluent Schema Registry for Kafka-based pipelines |
| Dead-letter queue | Add dead-letter queues to all streaming ingestion pipelines |
| Touchless production | Implement simulated data for development and testing at AHRI |
| Orchestration | Continue building on Airflow — add dependency graphs for ingestion |
| Transfer appliances | Know when to use them for large migrations |

---

## 🔗 Resources
- [Fundamentals of Data Engineering — Google Drive](https://drive.google.com/file/d/1pu_JTMF5jQdDrtvjzSGaQNBpFiFglnl_/view?usp=drive_link)
- [Debezium CDC Documentation](https://debezium.io/documentation/)
- [Airbyte — Open Source Data Integration](https://airbyte.com)
- [Fivetran — Managed Data Connectors](https://www.fivetran.com)
- [Apache Kafka Documentation](https://kafka.apache.org/documentation/)
- [Great Expectations — Data Quality](https://greatexpectations.io)
- [Apache Airflow Documentation](https://airflow.apache.org/docs/)

---

*Part of my self-study journey toward becoming a Data Engineer — documented on GitHub as proof of learning.*
