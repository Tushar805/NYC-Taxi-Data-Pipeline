# 🚖 NYC Taxi Data Engineering Pipeline

An end-to-end Azure Data Engineering project built using **Azure Data Factory, Azure Data Lake, Azure Databricks, Delta Lake, and Power BI** to process and analyze NYC Green Taxi trip data for 2024 using a modern Medallion Architecture.

---

# 📌 Project Overview

This project demonstrates a real-world cloud-based data engineering workflow where NYC Taxi trip data is automatically ingested from public APIs into Azure Data Lake, transformed using Databricks & PySpark, and served to Power BI for analytics.

The pipeline follows the **Bronze → Silver → Gold** Medallion Architecture pattern and uses **Parquet** and **Delta Lake** formats for scalable and optimized data processing.

---

# 🚀 Key Features

- Dynamic ingestion pipeline using ADF
- Automated monthly data loading from APIs
- Medallion Architecture implementation
- Recursive file processing using PySpark
- Delta Lake external tables with Unity Catalog
- Delta Versioning & Time Travel
- Secure ADLS access using Service Principal & Managed Identity
- Power BI integration using Databricks Partner Connect

---

# 🏗️ Architecture Flow

```text
NYC TLC Open Data API
        ↓
Azure Data Factory (Dynamic Pipeline)
        ↓
ADLS Gen2 - Bronze Layer (Raw Parquet)
        ↓
Azure Databricks + PySpark
        ↓
ADLS Gen2 - Silver Layer (Transformed Parquet)
        ↓
Delta Lake External Tables
        ↓
ADLS Gen2 - Gold Layer
        ↓
Power BI Analytics & Reporting
```

---

# 🧰 Technologies Used

| Tool / Service | Purpose |
|---|---|
| Azure Data Factory | Dynamic data ingestion |
| Azure Data Lake Gen2 | Data storage |
| Azure Databricks | Data transformation |
| PySpark | Data processing |
| Delta Lake | Versioned storage format |
| Unity Catalog | External table management |
| Power BI | Reporting & dashboards |
| Azure Entra ID | Security & authentication |

---

# 📂 Dataset Information

### Source
NYC Taxi & Limousine Commission (TLC)

🔗 https://www.nyc.gov/site/tlc/about/tlc-trip-record-data.page

### Dataset Used
- Green Taxi Trip Data (2024)
- Monthly Parquet files
- Taxi Zone Lookup CSV

### Sample API URL

```text
https://d37ci6vzurychx.cloudfront.net/trip-data/green_tripdata_2024-01.parquet
```

---

# 🥉 Bronze Layer

- Created dynamic ADF pipeline using `ForEach`
- Used parameterized relative URLs
- Automatically ingested all 12 months of 2024 data
- Stored raw parquet files in ADLS Bronze container

### Dynamic URL Logic

```sql
@if(
  less(item(),10),
  concat('trip-data/green_tripdata_2024-0', string(item()), '.parquet'),
  concat('trip-data/green_tripdata_2024-', string(item()), '.parquet')
)
```

---

# 🥈 Silver Layer

Data transformed using PySpark in Azure Databricks.

### Transformations Performed

- Recursive parquet file loading
- Manual DDL schema creation
- Column renaming
- Date, month & year extraction
- Zone splitting using `split()` function
- CSV → Parquet conversion
- Cleaned and transformed datasets written to Silver layer

### Example Transformations

```python
df_trip = df_trip.withColumn('trip_date',to_date('lpep_pickup_datetime'))\
                  .withColumn('trip_year',year('lpep_pickup_datetime'))\
                  .withColumn('trip_month',month('lpep_pickup_datetime'))
```

---

# 🥇 Gold Layer

Created Delta Lake external tables using Unity Catalog.

### Gold Tables

- `gold.trip_zone`
- `gold.trip_type`
- `gold.trip2024data`

### Features Implemented

- Delta Lake format
- External tables
- Unity Catalog integration
- Delta transaction logs
- Versioning & Time Travel

---

# ⏳ Delta Lake Time Travel

Implemented Delta Lake versioning to restore historical table states.

### Operations Performed

- UPDATE
- DELETE
- DESCRIBE HISTORY
- RESTORE TO VERSION

### Example

```sql
RESTORE gold.trip_zone TO VERSION AS OF 0
```

---

# 📊 Power BI Integration

Connected Databricks Delta tables to Power BI using:

- Databricks Partner Connect
- Access Tokens
- Downloaded Connection Files

This enabled analytics and dashboard creation directly on Gold layer Delta tables.

---

# 📁 Project Structure

```bash
nyc-taxi-data-pipeline/
│
├── notebooks/
│   ├── silver_transformation_notebook.py
│   └── gold_transformation_notebook.py
│
├── datasets/
│   ├── taxi_zone_lookup.csv
│   └── trip_type.csv
│
├── sceenshots/
│
└── README.md
```

---

# 📈 Outcomes

- Automated ingestion of 12 months of NYC taxi data
- Built scalable Medallion Architecture pipeline
- Processed large parquet datasets using Spark
- Implemented secure cloud-based storage access
- Created Delta Lake tables with versioning support
- Enabled BI reporting using Power BI integration

