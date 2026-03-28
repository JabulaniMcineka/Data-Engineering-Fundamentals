# Chapter 2 - The Data Engineering Lifecycle

## Definition
The data engineering lifecycle turns raw data ingredients
into useful products, ready for analysts, data scientists,
and business users to consume.

## The 5 Lifecycle Stages
1. Generation - data is created by apps, sensors, and databases
2. Storage - data is saved and kept safe
3. Ingestion - data is collected and moved into our systems
4. Transformation - data is cleaned, shaped, and made useful
5. Serving - data is delivered to analysts, scientists, and the business

## The 6 Undercurrents
These run beneath every stage of the lifecycle:
- Security - protect data, apply least privilege access
- Data Management - govern, quality check, and track data
- DataOps - automate, monitor, and respond to incidents
- Data Architecture - design systems that scale with the business
- Orchestration - coordinate and schedule all pipeline jobs
- Software Engineering - write clean, production-grade code

## Key Insights
- Storage touches EVERY stage - it is the foundation of the lifecycle
- Batch ingestion is simpler and works for most use cases
- Bad data is a silent killer - always monitor your data quality
- Orchestration tools like Airflow coordinate jobs using DAGs

## Batch vs Streaming
- Batch - collect data in large chunks on a schedule (simpler, cheaper)
- Streaming - collect data continuously in real time (complex, costly)
- Start with batch. Only use streaming when the business truly needs it.

## New Terms I Learned
- DAG - a map of job dependencies, what runs after what
- ETL - Extract, Transform, Load
- CDC - Change Data Capture, tracking changes in source databases
- Data Lineage - the audit trail of where data came from
- DataOps - Agile and DevOps principles applied to data pipelines
- Orchestration - coordinating and scheduling all data pipeline jobs

## What This Means for Me
I need to understand the full data lifecycle, not just the tools.
Data engineering is about managing data from source to serving,
with security, quality, and automation running underneath everything.
At Entelect, I will work across these stages and grow into
understanding how each one connects to deliver value to the business.
