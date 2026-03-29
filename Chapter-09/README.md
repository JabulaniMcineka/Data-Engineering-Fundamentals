# 📚 Chapter 9 - Serving Data for Analytics, Machine Learning, and Reverse ETL
> **Book:** Fundamentals of Data Engineering — Joe Reis & Matt Housley  
> **Status:** ✅ Complete  
> **Date:** March 2026

---

## 🎯 What This Chapter Is About

Chapter 9 covers **serving data** — the final stage of the data engineering lifecycle. This is where data becomes useful, delivered to analysts, data scientists, and business users for analytics, ML, and reverse ETL.

> **Key idea:** Trust is the root of all data serving. The fanciest architecture means nothing if users do not trust the data. Trust takes years to build and minutes to destroy.

---

## 🌟 General Considerations Before Serving

### 🤝 Trust
The single most important factor in serving data.

```
High quality data + No trust = Unused data
Average quality data + High trust = Valuable data
```

**How to build trust:**
- Run data validation and data observability continuously
- Visually inspect and confirm results with stakeholders
- Set clear SLAs and SLOs — and consistently meet them
- Communicate proactively when issues arise
- Maintain an active feedback loop with users

**SLA vs SLO:**
| | SLA | SLO |
|---|---|---|
| **What it is** | A contract — what users can expect | A measurement — how you track performance |
| **Example** | "Data will be reliable and high quality" | "Pipelines will have 99% uptime, 95% of data free of defects" |

---

### 🎯 Use Case and Users

Always start with the **use case** and work backward to the technology:

1. Who will use the data?
2. How will they use it?
3. What action will the data trigger?
4. Can that action be automated?

> 💡 Always ask: "What action will this data trigger, and who will perform it?" Prioritise use cases with the highest possible ROI.

---

### 📦 Data Products

**Definition:** A data product is a product that facilitates an end goal through the use of data.

**Key principles:**
- Build for adoption — understand the "job to be done" by the user
- Good data products create positive feedback loops — more usage = more value
- Nothing ruins adoption faster than unwanted utility or untrustworthy data
- Know whether your customer is **internal** (analysts, scientists) or **external** (app users)

**Questions to ask when building a data product:**
- What does the user hope to accomplish?
- What is the expected ROI?
- How will you measure adoption and usage?
- How will users report problems and request improvements?

---

### 🔧 Self-Service Analytics

Self-service = giving users the ability to build their own reports and analyses.

**Why it often fails in practice:**
- Executives want simple, clear dashboards — not self-service tools
- Analysts already use SQL — self-service BI adds little value for them
- Most users lack the data skills to use self-service effectively

**When it works:**
- Audience is data-literate (e.g., executives with data background)
- Users have been trained on the tool
- Strong guardrails prevent incorrect results

---

### 📖 Data Definitions and Logic

**Data definition** = the agreed meaning of data across the organisation.
**Data logic** = the formulas for deriving metrics from data.

```
Example:
- What is a "customer"? Someone who bought in the last 30 days? Last 12 months?
- What is "gross profit"? Revenue minus which expenses exactly?
```

**How to formalise definitions:**
- Document in a **data catalog**
- Encode in **data models** (Chapter 8)
- Centralise in a **semantic or metrics layer**
- Avoid tribal knowledge — unwritten rules that live only in people's heads

---

### 🕸️ Data Mesh and Serving

In a data mesh:
- Every domain team is responsible for **serving their own data** to other teams
- Teams must prepare data for consumption — analytics, dashboards, BI, ML
- Teams also run their own self-service analytics
- Data consumed from other teams may feed into software and ML features

---

## 📊 Analytics

### Business Analytics
Uses historical and current data for **strategic decisions**.

**Three main formats:**

| Format | What It Is | Tools |
|---|---|---|
| **Dashboard** | Visual summary of core metrics and KPIs | Tableau, Looker, Power BI, Superset |
| **Report** | Data-driven investigation into a specific question | SQL, Excel, Python notebooks |
| **Ad hoc analysis** | Deep dive into a specific issue or question | SQL, Python, R |

**Data serving for business analytics:**
- Usually served in **batch mode** from a data warehouse or data lake
- New data available every second, minute, hour, or day depending on use case
- Ingestion frequency sets the ceiling for downstream update frequency

---

### Operational Analytics
Uses **real-time data for immediate action**.

```
Business Analytics = Actionable insights (longer time horizon)
Operational Analytics = Immediate action (right now)
```

**Examples:**
- Real-time application monitoring dashboards
- Live factory floor quality control
- Alert systems for fraud, security, or anomalies

**Key features:**
- Near real-time data freshness
- Automated triggers and alerts
- Streaming data pipelines rather than batch

> 💡 **The line between business and operational analytics is blurring.** As streaming becomes cheaper and easier, companies increasingly want both real-time dashboards AND historical trend analysis in the same tool.

---

### Embedded Analytics
User-facing analytics **built into applications**.

**Examples:**
- Smart thermostat app showing real-time energy usage
- Ecommerce seller dashboard showing live sales and inventory
- Recruiting platform showing candidate statistics in real time

**Three performance requirements for embedded analytics:**

| Requirement | What It Means |
|---|---|
| **Low data latency** | Users expect near real-time data updates |
| **Fast query performance** | Dashboard refreshes must happen in seconds |
| **High concurrency** | Many customers running queries simultaneously |

---

## 🤖 Machine Learning

Data engineers support ML by providing:
- Clean, high-quality training data
- Feature engineering pipelines
- Feature stores for managing and sharing ML features
- Infrastructure for scalable model training

### What a Data Engineer Should Know About ML

| Concept | Why It Matters |
|---|---|
| Supervised vs unsupervised learning | Determines what data preparation is needed |
| Classification vs regression | Affects feature engineering requirements |
| Batch vs online training | Determines pipeline architecture |
| Classical ML vs deep learning | Classical often works better with less data |
| GPU vs CPU | GPU needed for deep learning, not for classical ML |
| Data cascades | Bad data compounds through pipeline stages — hard to detect |
| Feature stores | Central repository for ML features — reduces duplication |

> 💡 **Data cascades** = when poor quality data at one stage creates compounding errors downstream through multiple ML pipeline stages. Very hard to detect and fix.

---

## 🔄 Reverse ETL

**Definition:** Taking processed data from the output of the data engineering lifecycle and feeding it back into source systems.

```
Source System (CRM) → Ingest → Data Warehouse → Model/Transform → Reverse ETL → Source System (CRM)
```

**Real-world example:**
1. Pull customer data from CRM into data warehouse
2. Train a lead scoring model on the data
3. Store scored leads back in data warehouse
4. Use Reverse ETL to push scored leads back into CRM
5. Salespeople see scored leads directly in their CRM tool — no friction

**Why Reverse ETL matters:**
- Reduces friction for end users — data appears where they already work
- Enables closed-loop systems — insights feed back into the systems that generated the data
- Growing in importance as companies rely more on SaaS platforms

**⚠️ Warning:** Reverse ETL creates **feedback loops**. A bug in your model can cause runaway effects. Always add monitoring and guardrails.

**Popular tools:** Hightouch, Census

---

## 🔌 Ways to Serve Data

### File Exchange
- Email, SFTP, object storage
- Suitable for simple sharing and partner organisations
- Upgrade to object storage (data lake) for scalability
- Best for: small datasets, one-off sharing, external partners

### Databases (OLAP)
- Analysts and data scientists connect to query data warehouse or data lake
- Fine-grained permission controls — table, column, row level
- High performance for complex analytical queries
- BI systems either store a local copy (Tableau) or push queries to the database (Looker)
- Best for: SQL-based analytics, ML feature extraction, standard reporting

### Streaming Systems
- Operational analytics databases combining streaming buffer with columnar storage
- Apache Druid, Apache Pinot, BigQuery streaming buffer
- Support both historical queries AND up-to-the-second data in one system
- Best for: operational dashboards, real-time monitoring, IoT analytics

### Query Federation
- Pull data from multiple sources in a single query
- Tools: Trino (Starburst), Presto, Dremio
- Good for: ad hoc exploratory analysis, combining siloed data
- Caution: can overload production source systems if not managed carefully

```
Analyst Query → Federation Engine → [Data Warehouse + MySQL + S3 + PostgreSQL]
                                             ↑ all in one query
```

### Data Sharing
- Share data through cloud platforms with fine-grained access control
- Platforms: Snowflake, BigQuery, Redshift, Azure Synapse
- Turns serving into a security and access control problem
- Consumers run their own queries — engineers just control access
- Best for: sharing across teams, partner organisations, public data

### Serving in Notebooks
- Data scientists use Jupyter or JupyterLab for exploration and model development
- Connect to databases, data lakes, or APIs programmatically
- When local memory runs out: move to cloud notebooks → Dask/Ray/Spark → SageMaker/Vertex AI
- Data engineers set up cloud infrastructure and help manage notebook environments

> ⚠️ **Security risk:** Credentials are frequently hardcoded in notebooks and leaked into Git repositories. Set standards — use credential managers, never embed passwords in code.

---

## 🧮 Semantic and Metrics Layers

A **metrics layer** (also called semantic layer or headless BI) centralises business logic for reuse.

**Problem it solves:**
```
Without metrics layer:
- Analyst 1 calculates "revenue" one way in their SQL
- Analyst 2 calculates "revenue" a different way
- Reports don't match — trust is destroyed

With metrics layer:
- "Revenue" is defined once in a central place
- All queries reference the same definition
- Reports are consistent — trust is maintained
```

**Key tools:**

| Tool | What It Does |
|---|---|
| **dbt** | Defines SQL transformations and business logic as code — runs in the transform layer |
| **Looker/LookML** | Defines metrics, generates SQL, pushes queries to the database |
| **Metricflow** | Open source metrics layer (from dbt Labs) |
| **Apache Superset** | Open source BI with semantic layer support |

---

## 👥 Whom You'll Work With

| Stakeholder | Your Role |
|---|---|
| **Data Analysts** | Provide clean, trusted data for their queries and reports |
| **Data Scientists** | Provide training data, feature pipelines, and notebook infrastructure |
| **ML Engineers** | Collaborate on feature stores, model serving, and retraining pipelines |
| **Business users** | Provide dashboards, reports, and self-service access |
| **Executives** | Provide high-level KPI dashboards with trusted metrics |

> **Important:** The data engineer is responsible for **producing high-quality data products** — NOT for interpreting the results. That is the analyst's job.

---

## 🔒 Undercurrents Applied to Serving

### Security
- The serving stage has the **largest security surface** of all lifecycle stages
- Apply principle of least privilege — give only the access required for the job
- Most users should have **read-only** access — not write or update
- In multitenant environments, use filtered views to prevent data leakage
- Audit data product usage — stop sharing products nobody uses
- Fine-grained access control is a **key enabler** of better analytics — not an impediment

### Data Management
- Ensure users have access to **high-quality, trustworthy data**
- Use synthetic or anonymized data where privacy regulations apply
- Maintain a semantic layer for consistent business definitions
- Support active feedback loops — users report problems, you improve the data

### DataOps — What to Monitor at the Serving Stage

| Metric | Why It Matters |
|---|---|
| Data health and downtime | Users notice when data is missing or stale |
| Query and dashboard latency | Slow dashboards destroy trust and adoption |
| Data quality scores | Catch data defects before users do |
| Security and access patterns | Detect unauthorised access or anomalies |
| Model versions being served | Ensure correct model version is in production |
| SLO compliance | Track whether you are meeting your commitments |

### Data Architecture
- Feedback loops at the serving stage must be **fast and tight**
- Encourage data scientists off local machines and onto cloud environments
- Build dev, test, and production environments for analytics and ML workflows
- For embedded analytics, work with app developers to ensure fast, cost-effective queries

### Orchestration
- Serving is downstream of every other stage — orchestration complexity is highest here
- Key decision: **centralised vs decentralised orchestration**
  - Centralised: easier to coordinate, but one bad DAG can bring everything down
  - Decentralised: teams manage their own flows, but cross-team coordination is harder
- Whoever owns orchestration must enforce standards, testing, and gatekeeping

### Software Engineering
- Analytics as code: dbt, LookML, Jinja SQL
- Understand how programmatic SQL layers compile and perform against your database
- Set up CI/CD pipelines for analytics and ML code — not just application code
- Data engineers set up the infrastructure, data scientists and analysts operate within it

---

## 🔁 The Feedback Loop

Serving is not the end — it is where the next iteration begins:

```
Build → Deploy → Users Find Issues → Feedback → Improve → Build Again
```

> A good data engineer is always open to feedback and continuously improving their craft. Serving data reveals what needs to be fixed upstream.

---

## 📝 Key Terms Learned

| Term | Simple Meaning |
|---|---|
| **SLA** | Service Level Agreement — what users can expect |
| **SLO** | Service Level Objective — how performance is measured |
| **Data product** | A product that facilitates a goal through data |
| **Self-service analytics** | Users build their own reports without engineering help |
| **Business analytics** | Historical data for strategic decisions |
| **Operational analytics** | Real-time data for immediate action |
| **Embedded analytics** | User-facing analytics inside applications |
| **Reverse ETL** | Loading processed data back into source systems |
| **Semantic layer** | Centralised business logic definitions reusable across tools |
| **Metrics layer** | Tool for maintaining and computing business logic |
| **Feature store** | System for managing ML features across models |
| **Query federation** | Querying multiple external sources from one engine |
| **Data sharing** | Sharing data through cloud platforms with access controls |
| **Data observability** | Ongoing monitoring of data and data processes |
| **Data downtime** | Period when data is missing, incomplete, or inaccurate |
| **Data cascade** | Compounding data quality errors through ML pipeline stages |
| **Feedback loop** | Downstream usage flows back to improve upstream systems |
| **Headless BI** | Metrics layer that separates business logic from the BI tool |
| **LookML** | Looker's language for defining business metrics and data models |

---

## 🙋 How This Applies to Me

| Chapter Concept | My Action Plan |
|---|---|
| Trust and SLAs | Define SLAs for AHRI data products and monitor compliance |
| Business analytics | Already building Power BI dashboards — formalise the metrics layer |
| Operational analytics | Study real-time dashboard patterns for clinical monitoring |
| dbt | Learn dbt for formalising business logic in the transform layer |
| Reverse ETL | Study Hightouch or Census for future AHRI use cases |
| Feature stores | Learn about Feast or Databricks Feature Store for ML projects |
| Data observability | Implement Great Expectations for data quality monitoring |
| Semantic layer | Define standard business metrics for AHRI in dbt |
| Notebook security | Implement credential management standards for AHRI data science team |
| Feedback loops | Set up regular feedback sessions with AHRI analysts and stakeholders |

---

## 🔗 Resources
- [Fundamentals of Data Engineering — Google Drive](https://drive.google.com/file/d/1pu_JTMF5jQdDrtvjzSGaQNBpFiFglnl_/view?usp=drive_link)
- [dbt Documentation](https://docs.getdbt.com)
- [Looker/LookML Documentation](https://cloud.google.com/looker/docs/lookml-terms-and-concepts)
- [Hightouch — Reverse ETL](https://hightouch.com)
- [Great Expectations — Data Observability](https://greatexpectations.io)
- [Feast — Open Source Feature Store](https://feast.dev)
- [Apache Superset](https://superset.apache.org)
- [Tableau](https://www.tableau.com)
- [Power BI](https://powerbi.microsoft.com)

---

*Part of my self-study journey toward becoming a Data Engineer — documented on GitHub as proof of learning.*
