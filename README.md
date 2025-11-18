# Forecasting Affluence in SNCF Train Stations

End-of-module project – **Big Data / Modern Data Engineering**  
Master 2 – Statistics for Evaluation and Forecasting – University of Reims Champagne-Ardenne  
Academic year 2025–2026

## 🎯 Objective

The goal of this project is to build a **daily crowd forecasting system for SNCF train stations** using open data (traffic, weather, calendar, etc.).  
The project follows the **full lifecycle of a data engineering & data science project**:

1. Ingest data from several open APIs  
2. Store raw and processed data in a **Data Lake** and then in a **Data Warehouse**  
3. Clean, validate and prepare the data  
4. Perform feature engineering and modeling with **PySpark**  
5. Evaluate model performance and visualize the results  
6. (Optional) Fully industrialize the solution on **Google Cloud Platform (GCP)**

---

## 🏗️ Overall Architecture

The project is based on a modern **Data Lake + Data Warehouse** architecture:

- **Data Lake**: raw storage in **Google Cloud Storage (GCS)**
- **Data Warehouse**: star-schema modeling in **BigQuery**
- **Processing & Modeling**: **PySpark / Spark MLlib**
- **Orchestration** (optional): **Airflow / Cloud Composer**
- **Visualization & App** (optional): **Streamlit** application deployed on **Cloud Run** or **App Engine**

Logical flow:

1. APIs → ingestion scripts → **GCS (raw zone)**
2. GCS → ETL/ELT jobs → **BigQuery (star schema)**
3. BigQuery → **PySpark** → training dataset
4. PySpark → **forecasting model + metrics**
5. Results → **visualization / application** (dashboards, web app)

---

## 📊 Data Sources

The project uses only open data sources:

- **SNCF station traffic / crowds**
  - Source: **SNCF Open Data API**
  - Content: number of passengers per station and per day

- **Weather data**
  - Source: **Météo France API**
  - Content: temperature, precipitation, and other weather variables by area and date

- **Calendar & public holidays**
  - Source: **French Government APIs (API Gouv)**
  - Content: public holidays, school holidays, calendar information

These sources are joined to build a daily dataset at the **(station, date)** level.

---

## 🗃️ Data Model (BigQuery – Star Schema)

The cleaned and historized data is stored in BigQuery using a **star schema**:

- **Fact table**
  - `fact_affluence`  
    - Daily station-level crowd / passenger counts  
    - Foreign keys to dimension tables

- **Dimension tables (examples)**
  - `dim_station`: `station_id`, name, region, city, station type, number of lines, terminus flag…
  - `dim_date`: `date`, day of week, month, weekend flag, public holiday flag, school holidays…
  - `dim_weather`: `date`, `station_id` (or area), average temperature, precipitation, and other indicators

Additional dimensions can be added if needed (e.g. `dim_line`).

---

## 🔁 Data Pipeline

### 1. Data Ingestion

- Regular calls to the different APIs (SNCF, Météo France, API Gouv)
- Responses are serialized (JSON/CSV/Parquet) and stored in **GCS (raw zone)**
- Files are organized by logical and time-based folders, for example:

  
