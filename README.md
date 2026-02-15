
# Crypto Data Pipeline (Medallion Architecture)

This repository demonstrates a **production-grade data pipeline** for cryptocurrency market data using **Azure Databricks**, **ADLS Gen2**, and **Delta Lake**, following the **Medallion Architecture** (Bronze → Silver → Gold).

---

## 📂 Architecture Overview

- **Bronze Layer**  
  - Raw ingestion of JSON data from external APIs.  
  - Stored in ADLS Gen2 (`bronze` container).  
  - No transformations applied.  

- **Silver Layer**  
  - Cleansed and normalized data.  
  - Transformations include:
    - Flattening nested structs (e.g., `roi` → `roi_currency`, `roi_percentage`, `roi_times`).  
    - Casting string dates into proper `timestamp` types.  
    - Handling nulls with defaults.  
    - Adding ingestion timestamp for lineage.  
    - Filtering invalid rows.  
    - Normalizing column names (snake_case).  
  - Stored as **Delta tables** in ADLS Gen2 (`silver` container).  

- **Gold Layer**  
  - Analyst-friendly, business-ready tables.  
  - Transformations include:
    - **Volume-to-MarketCap Ratio**  
    - **Opening & Closing Price Calculation**  
    - **Market Dominance Percentage**  
  - Partitioned by `symbol` for query performance.  
  - Stored as **Delta tables** in ADLS Gen2 (`gold` container).  

---

## ⚙️ Authentication

This pipeline uses **SAS token authentication** for secure access to ADLS Gen2.  
Configuration in Databricks:

```python
spark.conf.set(
    f"fs.azure.sas.{container_name}.{storage_account_name}.blob.core.windows.net",
    sas_token
)

<img width="1298" height="325" alt="image" src="https://github.com/user-attachments/assets/fad5d954-a060-4b89-b835-a2292f84cc5f" />

