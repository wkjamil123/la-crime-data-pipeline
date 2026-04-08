# LA Crime Data Pipeline & Distributed Analytics System

## Overview
This project implements a distributed data pipeline for the Los Angeles Crime Dataset (2020–Present), consisting of over one million records.

The system demonstrates how multiple technologies can be integrated across the data lifecycle to support scalable ingestion, distributed processing, and query-optimized analytics.

**Technologies used:** MongoDB, Apache Spark, and Apache Cassandra.

---

## Dataset
- File: `Crime_Data_from_2020_to_2024.csv` (available at https://catalog.data.gov/dataset/crime-data-from-2020-to-present) 
- Source: Los Angeles crime data (2020–Present)  
- Contains over 1 million records including crime type, location, victim demographics, and timestamps  

---

## Architecture

The pipeline follows a multi-stage architecture:

### 1. Data Ingestion (MongoDB)
Raw CSV crime data is ingested into MongoDB to support flexible schema storage and reprocessing.

### 2. Data Processing (Apache Spark)
Apache Spark is used for distributed data cleaning and transformation, including:
- Timestamp parsing  
- Column standardization  
- Handling missing or invalid values  

### 3. Analytical Storage (Apache Cassandra)
Processed data is stored in Cassandra using a query-driven schema design optimized for analytical workloads.

---

## Tech Stack
- Python (PySpark)  
- Apache Spark  
- Apache Cassandra  
- MongoDB  
- Docker  

---

## Repository Structure

```
la-crime-data-pipeline/
│
├── notebooks/
│   ├── LA Crime Data Pipeline.ipynb
│   └── LA Crime Data Pipeline - Original Notebook.ipynb
│
├── report/
│   ├── Los Angeles Crime Data Pipeline Project.pdf
│   └── Los Angeles Crime Data Pipeline Project.docx
│
└── README.md
```

---

## Cassandra Database Setup

Apache Cassandra serves as the analytical storage layer. Tables are designed using a **query-first approach**, meaning schemas are optimized for expected query patterns rather than normalization.

### Access Cassandra (Docker)
```bash
docker exec -it cassandra-container cqlsh
```

### Create Keyspace
```sql
CREATE KEYSPACE la_crime
WITH replication = {'class': 'SimpleStrategy', 'replication_factor': 1};

USE la_crime;
```

### Create Tables

#### Crimes by ID (Primary Lookup)
```sql
CREATE TABLE crimes_by_id (
    dr_no TEXT PRIMARY KEY,
    date_occ TIMESTAMP,
    area_name TEXT,
    crime_code TEXT,
    crime_desc TEXT,
    victim_age INT,
    victim_sex TEXT,
    weapon_desc TEXT
);
```

#### Crimes by Area and Date (Time-Series Analysis)
```sql
CREATE TABLE crimes_by_area_date (
    area_name TEXT,
    date_occ TIMESTAMP,
    dr_no TEXT,
    crime_desc TEXT,
    victim_age INT,
    PRIMARY KEY (area_name, date_occ, dr_no)
) WITH CLUSTERING ORDER BY (date_occ DESC);
```

#### Crimes by Type (Trend Analysis)
```sql
CREATE TABLE crimes_by_type (
    crime_desc TEXT,
    date_occ TIMESTAMP,
    dr_no TEXT,
    area_name TEXT,
    PRIMARY KEY (crime_desc, date_occ, dr_no)
);
```

#### Daily Crime Counts (Pre-Aggregated)
```sql
CREATE TABLE crimes_daily_count (
    date_occ DATE,
    total_crimes INT,
    PRIMARY KEY (date_occ)
);
```

---

## Pipeline Execution (Summary)

The pipeline consists of:

1. Loading raw CSV data into MongoDB  
2. Processing and cleaning data using Apache Spark  
3. Writing transformed data into Cassandra tables  
4. Running analytical queries using Cassandra or Spark SQL  

> Note: This project requires a configured environment (MongoDB, Cassandra, Spark).  
> The notebook focuses on pipeline logic and will not run end-to-end without setup.

---

## Example Analytical Use Cases
- Analyze crime trends across geographic areas over time  
- Identify patterns in crime types and frequency  
- Explore relationships between crime characteristics (e.g., victim demographics, weapon use)  
- Query daily aggregated crime counts for time-series analysis  

---

## Key Takeaways
- Demonstrates end-to-end data pipeline design using distributed systems  
- Highlights tradeoffs between schema flexibility (MongoDB) and performance (Cassandra)  
- Shows practical use of Apache Spark for large-scale data processing  
- Emphasizes query-driven data modeling for scalable analytics  

---

**Full Report:**  
[Los Angeles Crime Data Pipeline Project](./report/Los Angeles Crime Data Pipeline Project.pdf)

---

## Future Improvements
- Incorporate real-time streaming (e.g., Kafka)  
- Add geospatial analysis capabilities  
- Deploy on cloud infrastructure (AWS/GCP)  
- Build interactive dashboards for visualization  

---

## Author
Waleed Jamil  
Syracuse University – Applied Data Science
