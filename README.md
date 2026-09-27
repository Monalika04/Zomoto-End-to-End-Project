# Zomato AI Data Engineering

An end-to-end data engineering project that turns large-scale Zomato-style food delivery data into trusted analytics and AI-powered applications.

## Business Problem

A food-delivery business generates millions of orders, order items, and customer reviews. Raw CSV files are difficult to manage, reporting needs clean and consistent data, and business users need an easier way to explore reviews and warehouse data.

**Goal:** build one reliable platform for ingestion, transformation, analytics, automation, and AI-assisted data access.

## Architecture

![Zomato AI Data Engineering Architecture](docs/architecture.png)

```text
CSV Data
   ↓
Amazon S3
   ↓
Snowflake RAW
   ↓
dbt STAGING
   ↓
dbt MARTS
   ↓
Analytics + AI
   ├── Review Enrichment
   ├── RAG Review Chat
   └── Text-to-SQL
           ↓
      Streamlit / Snowsight
```

**Apache Airflow** orchestrates the pipeline.

## What I Built

| Layer | Implementation |
|---|---|
| Data Lake | Amazon S3 |
| Data Warehouse | Snowflake |
| Transformation | dbt |
| Orchestration | Apache Airflow |
| Large-scale processing | Incremental MERGE models |
| Historical tracking | SCD Type 2 |
| Data quality | dbt tests + reconciliation |
| AI | Grok API |
| Applications | Streamlit + Snowsight |

## Data Flow

**1. Ingestion**  
Seven CSV datasets are uploaded to S3: restaurants, users, food, menu, orders, order_items, and reviews.

**2. Warehouse**  
Snowflake loads the raw data through Storage Integration and `COPY INTO`.

**3. Transformation**  
dbt builds clean staging models and Gold-layer dimensions, facts, and business marts.

**4. Analytics**  
The marts support revenue, restaurant, delivery, order, and review analysis.

**5. AI**  
Grok enriches reviews, powers RAG-based review Q&A, and converts natural-language questions into SQL.

## AI Capabilities

### Review Enrichment

```text
Review → Grok API → Structured insights → Snowflake
```

Used to extract information such as sentiment and topics from free-text reviews.

### RAG Review Chat

```text
Reviews → Embeddings → Vector Store → Retrieval → Grok → Answer + Sources
```

Allows users to ask questions about customer reviews.

### Text-to-SQL

```text
Business Question → Grok → SQL → SELECT-only Guard → Snowflake → Result
```

Allows non-SQL users to query analytical marts using natural language.

## Pipeline Orchestration

```text
upload_raw
    ↓
dbt_build_core
    ↓
enrich_reviews
    ↓
dbt_build_ai
```

The Airflow DAG runs these steps as a repeatable batch workflow.

## Dataset Scale

- **10M** orders
- **~23M** order items
- **300K** reviews
- 7 source CSV datasets
- Approximately **2.3 GB** total data

## Key Business Questions

The platform supports questions such as:

- Which cities generate the most revenue?
- What is the average order value?
- Which restaurants perform best?
- Where are delivery times highest?
- What are customers saying in their reviews?
- What review topics occur most frequently?
- Can a business user ask the warehouse a question without writing SQL?

## Repository Structure

```text
├── airflow/          # Airflow DAG + Docker
├── zomato/           # dbt models
├── ai/               # Grok enrichment, RAG, Text-to-SQL
├── snowflake/        # Warehouse setup SQL
├── aws/iam/          # S3/Snowflake IAM configuration
├── docs/             # Architecture
├── data/             # Local dataset, not committed
└── README.md
```

## Tech Stack

`Python` · `Pandas` · `Amazon S3` · `Snowflake` · `dbt` · `Apache Airflow` · `Docker` · `Grok API` · `Streamlit` · `AWS IAM` · `Git`

## Run

### dbt

```bash
cd zomato
dbt debug
dbt build --exclude tag:ai
```

### Airflow

```bash
cd airflow
cp example.env .env
docker compose build
docker compose up -d
```

Open `http://localhost:8080` and trigger `zomato_batch`.

### AI Applications

```bash
python ai/enrich_reviews.py
streamlit run ai/rag_chat.py
streamlit run ai/text_to_sql.py
```

Configure Snowflake credentials and the Grok API key through environment variables. Never commit credentials or `.env` files.

## Key Engineering Concepts

`Data Lake` · `Medallion Architecture` · `Dimensional Modeling` · `Incremental Processing` · `MERGE` · `SCD Type 2` · `Data Quality` · `ETL/ELT` · `Airflow Orchestration` · `RAG` · `Text-to-SQL`

## Outcome

The project combines **Data Engineering + Analytics Engineering + AI** into one end-to-end platform that takes raw food-delivery data and makes it available for reliable business analysis and AI-assisted exploration.
