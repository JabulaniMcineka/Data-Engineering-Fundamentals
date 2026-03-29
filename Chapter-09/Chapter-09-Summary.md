# Chapter 9 - Serving Data for Analytics, Machine Learning, and Reverse ETL

## Definition
Serving is the final stage of the data engineering lifecycle.
It is where data becomes useful — delivered to analysts, data scientists,
business users, and automated systems for analytics, ML, and reverse ETL.
Above all else, serving data is about TRUST. If users do not trust the data,
they will not use it, and the entire data engineering effort is wasted.

## General Considerations Before Serving

### Trust
- Trust is the most critical variable in data serving
- High quality data that nobody trusts is worthless
- Build trust through data validation, data observability, and SLAs
- Once trust is lost it is extremely difficult to regain

### Use Case and Users
- Always start with the use case and work backward to the technology
- Ask: who will use the data, how will they use it, and what action will it trigger?
- Can the action triggered by the data be automated?
- Prioritize use cases with the highest ROI

### Data Products
- A data product facilitates an end goal through the use of data
- Good data products have positive feedback loops — more usage generates more value
- Always understand the job the user is hiring your data product to do
- Build for adoption — data products nobody uses are wasted effort

### Self-Service
- Truly successful self-service analytics is rare in practice
- Works best for data-literate audiences who want to explore data themselves
- Most organisations rely on analysts and data engineers to build products

### Data Definitions and Logic
- Document all business definitions — what is a customer, what is a sale?
- Formalise business logic in data models and semantic layers
- Avoid tribal knowledge — definitions passed around informally cause inconsistency
- Use a semantic or metrics layer to write business logic once and reuse everywhere

## Three Ways to Serve Data

### 1. Analytics
Three types of analytics:

| Type | Focus | Speed | Example |
|---|---|---|---|
| **Business Analytics** | Historical trends, strategic decisions | Batch | Daily sales dashboard |
| **Operational Analytics** | Immediate action on current data | Real-time | Application monitoring |
| **Embedded Analytics** | User-facing analytics inside apps | Near real-time | Smart thermostat app |

Business analytics tools: Tableau, Looker, Power BI, Apache Superset

### 2. Machine Learning
Data engineers provide ML engineers and data scientists with:
- Clean, high quality training data
- Feature engineering pipelines
- Feature stores for ML feature management
- Infrastructure for scalable model training

Key ML concepts a data engineer should know:
- Supervised vs unsupervised learning
- Classification vs regression
- Batch training vs online training
- When to use GPU vs CPU
- Data cascades and their impact on models

### 3. Reverse ETL
- Take processed data from the data warehouse and feed it BACK into source systems
- Example: scored leads from a model loaded back into a CRM for salespeople
- Example: optimised ad bids computed in warehouse loaded back into Google Ads
- Creates feedback loops — always add monitoring and guardrails to prevent runaway loops
- Popular tools: Hightouch, Census

## Ways to Serve Data

| Method | When To Use |
|---|---|
| File exchange | Simple sharing, partner organisations, small datasets |
| Databases (OLAP) | SQL queries, analytics, ML feature extraction |
| Streaming systems | Real-time analytics, operational monitoring |
| Query federation | Ad hoc analysis across multiple source systems |
| Data sharing | Cloud platform sharing — Snowflake, BigQuery, Redshift |
| Notebooks | Data science exploration, feature engineering, model training |

## Semantic and Metrics Layers
- A metrics layer maintains and computes business logic in a reusable way
- Write once, use anywhere — avoids inconsistent ETL scripts
- dbt - defines complex SQL transformations and business logic as code
- Looker/LookML - defines metrics, generates SQL queries, pushes down to database
- Solves the question every organisation asks: "Are these numbers correct?"

## Key Insight
Data serving is the final chance to ensure data is correct before it reaches users.
A data engineer is responsible for producing the highest quality data products possible.
The data engineer is NOT responsible for interpreting the results — that is the analyst's job.
Always think about the feedback loop — serving data reveals what needs to be improved upstream.

## Key Terms I Learned
- SLA - Service Level Agreement, what users can expect from your data product
- SLO - Service Level Objective, how performance is measured against the SLA
- Data product - a product that facilitates an end goal through the use of data
- Self-service analytics - giving users the ability to build reports themselves
- Business analytics - historical data for strategic decisions
- Operational analytics - real-time data for immediate action
- Embedded analytics - user-facing analytics inside applications
- Reverse ETL - loading processed data back into source systems
- Semantic layer - centralised business logic definitions reusable across tools
- Metrics layer - tool for maintaining and computing business logic
- Feature store - system for managing ML features across models
- Query federation - querying multiple external data sources from one engine
- Data sharing - sharing data through cloud platforms with fine-grained access control
- Data observability - ongoing monitoring of data and data processes
- Data downtime - period when data is missing, incomplete, or inaccurate
- Feedback loop - downstream usage of data that flows back to improve upstream systems

## What This Means for Me
Serving data is the part of the lifecycle closest to business value.
At AHRI I build Power BI dashboards which is business analytics.
I need to get better at:
- Building a semantic layer or using dbt to formalise business logic
- Setting up data observability tools to monitor data quality continuously
- Understanding ML serving patterns for model deployment
- Designing reverse ETL workflows to feed insights back into source systems
At Entelect I will need to serve data reliably to analysts and data scientists
while ensuring trust, quality, and proper access controls at every step.
