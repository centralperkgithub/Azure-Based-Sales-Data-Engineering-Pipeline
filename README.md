<img width="1790" height="427" alt="image" src="https://github.com/user-attachments/assets/fe6b7c00-103e-4741-b0da-cc4cdeeff640" />

# End-to-End Azure Data Engineering Project: Retail Solutions

[![Made with Azure](https://img.shields.io/badge/Made%20with-Azure-0078D4?logo=microsoft-azure)](#)
[![Databricks](https://img.shields.io/badge/Databricks-ready-EF3E2E?logo=databricks&logoColor=white)](#)
[![Power BI](https://img.shields.io/badge/Power%20BI-Dashboard%20Ready-F2C811?logo=power-bi&logoColor=black)](#)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

A hands-on, end-to-end **Azure Data Engineering** solution designed for a retail client. It collects data from multiple sources, cleans and organizes it using the **Medallion Architecture**, and produces **Power BI** dashboards for actionable insights.

---

## 🧭 Mind Map
The project structure and main steps are shown in this mind map:

<img width="4323" height="5935" alt="NotebookLM Mind Map (1)" src="https://github.com/user-attachments/assets/ef092723-612a-40fd-b8d0-c67d7b50bee8" />


---

## 📚 Table of Contents
- [Business Problem & Project Goal](#business-problem--project-goal)
- [Key Technologies & Tools](#key-technologies--tools)
- [Work Methodology](#work-methodology)
- [Business Requirement Overview](#business-requirement-overview)
- [Medallion Architecture](#medallion-architecture)
- [Project Structure](#project-structure)
- [Project Setup Steps](#project-setup-steps)
- [Data Pipeline Steps](#data-pipeline-steps)
- [Sample Schemas & Code](#sample-schemas--code)
- [Dashboards & Insights](#dashboards--insights)
- [Run Locally (Dev Flow)](#run-locally-dev-flow)
- [Contributing](#contributing)
- [License](#license)

---

## 🧩 Business Problem & Project Goal

**Business Problem**
- Retail data (sales, products, stores, customers) is scattered across SQL tables and APIs.
- Business teams need unified, reliable, and timely insights (e.g., top products, sales trends).

**Project Goal**
- Build a scalable **Azure** data solution that **ingests**, **cleans**, and **models** retail data into analytics-ready tables and **Power BI** dashboards.

---

## 🛠️ Key Technologies & Tools
- **Azure Data Factory (ADF):** Orchestrate ingestion & transformations.
- **Azure Data Lake Storage Gen2 (ADLS):** Central storage for all layers.
- **Azure Databricks (PySpark):** Transformations, joins, aggregations.
- **Delta Lake:** Reliable storage format with ACID & time travel.
- **Power BI:** Interactive reporting and KPI dashboards.
- **Azure SQL Database:** Structured source data (products, stores, transactions).
- **API (JSON via GitHub):** Customer data source (simulated).

---

## 🧪 Work Methodology
1. **Understand Client Needs:** Define business questions (e.g., sales by product, store performance).
2. **Assess Data Sources:** Inventory tables/APIs and data quality.
3. **Design Solution:** Medallion architecture, security, cost & SLA.
4. **Build & Orchestrate:** ADF pipelines + Databricks notebooks.
5. **Validate & Iterate:** Data quality checks, reconcile with source.
6. **Visualize:** Power BI dashboards over Gold layer.

---

## 🏛️ Medallion Architecture

```
flowchart LR
  A[Sources: Azure SQL, API/JSON] --> B[Bronze (Raw)]
  B --> C[Silver (Cleaned & Conformed)]
  C --> D[Gold (Curated Marts & KPIs)]
  D --> E[Power BI Dashboards]
````

* **Bronze:** Land raw, schema-on-read, minimal changes.
* **Silver:** Clean types, dedupe, standardize columns & joins.
* **Gold:** Aggregated facts, dimensional models, KPI tables.

---

## 📁 Project Structure

```
azure-retail-solutions/
├─ adf/
│  ├─ linkedServices/
│  ├─ datasets/
│  ├─ pipelines/
│  │  ├─ pl_ingest_sql_to_bronze.json
│  │  ├─ pl_ingest_api_to_bronze.json
│  │  └─ pl_transform_bronze_to_silver_gold.json
├─ databricks/
│  ├─ notebooks/
│  │  ├─ 01_bronze_ingest_sql.py
│  │  ├─ 02_bronze_ingest_api.py
│  │  ├─ 03_silver_conformations.py
│  │  └─ 04_gold_aggregations.py
│  └─ clusters/cluster_config.json
├─ lake/
│  ├─ bronze/ (raw landing)
│  ├─ silver/ (cleaned)
│  └─ gold/   (curated)
├─ powerbi/
│  └─ Retail_Solutions_Dashboard.pbix
├─ data-sample/
│  ├─ products.csv
│  ├─ stores.csv
│  ├─ transactions.csv
│  └─ customers.json
├─ docs/
│  ├─ architecture.md
│  └─ data_dictionary.md
├─ .env.example
├─ README.md
└─ LICENSE
```

---

## 🚀 Project Setup Steps

1. **Azure SQL Database:** Create and load sample tables (Products, Stores, Transactions).
2. **ADLS Gen2:** Create containers/folders for **bronze**, **silver**, **gold**.
3. **API Source:** Host **customers.json** on GitHub (raw URL) to simulate API.
4. **ADF:**

   * Create Linked Services: SQL DB, ADLS, HTTP (GitHub).
   * Create Datasets & Pipelines (ingest SQL/API → bronze).
5. **Databricks:**

   * Create cluster (DBR 13.x+ recommended).
   * Import notebooks and configure **spark.sql.ansi.enabled=true**.
6. **Power BI:**

   * Connect to **gold** Delta/CSV exports.
   * Build KPIs, trends, and dimensional visuals.

> ✅ Tip: For **Databricks Community Edition**, export **gold** as CSV for Power BI Desktop.

---

## 🔄 Data Pipeline Steps

1. **Ingest (Bronze):**

   * ADF **Copy Activity** from Azure SQL & HTTP (GitHub raw JSON) → ADLS **/bronze**.
2. **Clean & Join (Silver):**

   * Databricks: cast types, trim/standardize, dedupe, join dimensions.
3. **Aggregate (Gold):**

   * Build fact and dimension tables (star schema), materialize KPIs.
4. **Visualize:**

   * Power BI: KPIs (Total Sales, Txn Count), trends, product/store analysis.

---

## 🧾 Sample Schemas & Code

### Azure SQL – Sample DDL

```sql
CREATE TABLE dbo.Products(
  ProductID INT PRIMARY KEY,
  ProductName NVARCHAR(120) NOT NULL,
  Category NVARCHAR(80),
  UnitPrice DECIMAL(18,2),
  IsActive BIT DEFAULT 1
);

CREATE TABLE dbo.Stores(
  StoreID INT PRIMARY KEY,
  StoreName NVARCHAR(120),
  City NVARCHAR(80),
  Region NVARCHAR(80)
);

CREATE TABLE dbo.Transactions(
  TxnID BIGINT PRIMARY KEY,
  TxnDate DATETIME2 NOT NULL,
  StoreID INT NOT NULL,
  ProductID INT NOT NULL,
  Quantity INT NOT NULL,
  UnitPrice DECIMAL(18,2) NOT NULL,
  CONSTRAINT FK_T_Store FOREIGN KEY(StoreID) REFERENCES dbo.Stores(StoreID),
  CONSTRAINT FK_T_Product FOREIGN KEY(ProductID) REFERENCES dbo.Products(ProductID)
);
```

### Databricks – Bronze → Silver (PySpark)

```python
from pyspark.sql.functions import col, trim, to_date

bronze_txn = spark.read.format("json").load("abfss://lake@<account>.dfs.core.windows.net/bronze/transactions/")
silver_txn = (
    bronze_txn
    .withColumn("TxnDate", to_date(col("TxnDate")))
    .withColumn("Quantity", col("Quantity").cast("int"))
    .withColumn("UnitPrice", col("UnitPrice").cast("decimal(18,2)"))
    .dropDuplicates(["TxnID"])
)

silver_txn.write.format("delta").mode("overwrite").save("abfss://lake@<account>.dfs.core.windows.net/silver/transactions")
```

### Databricks – Silver → Gold (Aggregations)

```python
from pyspark.sql.functions import sum as _sum, countDistinct, col

silver_txn = spark.read.format("delta").load("abfss://lake@<account>.dfs.core.windows.net/silver/transactions")
silver_prod = spark.read.format("delta").load("abfss://lake@<account>.dfs.core.windows.net/silver/products")
silver_store = spark.read.format("delta").load("abfss://lake@<account>.dfs.core.windows.net/silver/stores")

fact_sales = (
    silver_txn
    .join(silver_prod, "ProductID")
    .join(silver_store, "StoreID")
    .withColumn("SalesAmount", col("Quantity") * col("UnitPrice"))
)

gold_daily_sales = (
    fact_sales.groupBy("TxnDate")
    .agg(_sum("SalesAmount").alias("TotalSales"),
         _sum("Quantity").alias("TotalQty"),
         countDistinct("TxnID").alias("TxnCount"))
)

gold_daily_sales.write.format("delta").mode("overwrite").save("abfss://lake@<account>.dfs.core.windows.net/gold/daily_sales")
```

### ADF – Minimal Copy Activity (Snippet)

```json
{
  "name": "pl_ingest_sql_to_bronze",
  "properties": {
    "activities": [
      {
        "name": "copy_transactions",
        "type": "Copy",
        "typeProperties": {
          "source": { "type": "SqlSource" },
          "sink":   { "type": "DelimitedTextSink" }
        },
        "inputs":  [{ "referenceName": "ds_sql_transactions", "type": "DatasetReference" }],
        "outputs": [{ "referenceName": "ds_bronze_transactions", "type": "DatasetReference" }]
      }
    ]
  }
}
```

---

## 📊 Dashboards & Insights

* **KPI Cards:** Total Sales, Transactions, Avg Order Value.
* **Sales Trends:** Daily/Monthly sales line charts.
* **Product Analysis:** Top products by revenue/quantity.
* **Store Analysis:** Sales by store/region; map visuals.
* **Customer Insights (if available):** RFM buckets, repeat rate.

---

## 💻 Run Locally (Dev Flow)

### Prerequisites

* Azure Subscription (ADF, ADLS Gen2, SQL DB, Databricks)
* Power BI Desktop (or Service)
* Python 3.10+ (optional for helper scripts)
* Service principal or managed identity for ADF → ADLS auth

### Environment Variables

Create `.env` (or use Azure Key Vault) based on `.env.example`:

```
AZURE_STORAGE_ACCOUNT=
AZURE_DATALAKE_FILESYSTEM=
AZURE_SQL_SERVER=
AZURE_SQL_DATABASE=
AZURE_SQL_USERNAME=
AZURE_SQL_PASSWORD=
GITHUB_CUSTOMERS_JSON_RAW_URL=
```

### Steps

1. **Provision** ADLS, ADF, Databricks, SQL DB.
2. **Load** sample data into SQL (`data-sample/*.csv`).
3. **Configure** ADF linked services & publish pipelines.
4. **Run** Databricks notebooks to build **silver** and **gold**.
5. **Connect** Power BI to **gold** tables (Delta or CSV export).
6. **Publish** the dashboard to your workspace.

---

## 🤝 Contributing

Contributions are welcome!

1. Fork the repo  2) Create a feature branch  3) Open a PR with clear description & screenshots.



---


```
```

