# Global Climate Change Analysis — Apache Spark
**Data Engineering | Distributed Processing | Machine Learning**

## Project Overview
This project presents an analysis of global climate data using Apache Spark within a distributed cluster provisioned via Docker. The objective is to demonstrate the efficiency of in-memory Big Data processing for large-scale climate datasets, addressing analytical questions regarding warming trends and statistical correlations.

---

## Data Architecture and Engineering

### Processed Datasets
Two main datasets were processed:
*   `GlobalLandTemperaturesByCity.csv` (Berkeley Earth / Kaggle): 8,599,212 monthly temperature records by city since 1750.
*   `owid-co2-data.csv` (Our World in Data): CO2 emissions by country and year.

### ETL Pipeline and Optimization
*   The ETL pipeline applied uncertainty filters (< 1.5°C) and temporal bounds (year >= 1900), reducing the raw data to 4,746,210 records post-cleaning.
*   Applying the `.cache()` method to persist the DataFrame in memory resulted in a 34x speed increase for analytical queries.
*   The final dataset was exported in the columnar Parquet format, partitioned by the Country column to enhance read efficiency.

### Distributed Cluster
The architecture followed a Master/Worker model (Spark Standalone) orchestrated via Docker Compose, consisting of four containers:
*   **spark-master:** Coordinates workers and distributes tasks.
*   **spark-worker-1 & 2:** Execute DataFrame partitions in parallel (2 cores / 2GB RAM each).
*   **jupyter-spark:** Acts as the application Driver (4GB RAM).

---

## Key Analytical Insights

The processing generated answers to 8 complex analytical questions. Key highlights include:

*   **Historical Global Warming:** The global average temperature increased from 17.49°C in the 1900s to 18.63°C in the 2010s, totaling a 1.14°C increase over 110 years.
*   **Recent Thermal Acceleration:** Canada led the ranking with an acceleration of +0.94°C between the 2000s and 2010s, followed by Iraq and Georgia.
*   **Extreme Instability:** Cities with the highest temperature standard deviation are located in Russia and northern China, led by Blagoveshchensk (16.34°C standard deviation).
*   **Predictive Forecasting (Spark MLlib):** Using Linear Regression on the Brazilian historical data, the model projected average temperature trends for the 2024–2028 period.

---

## Local Execution Guide

### Prerequisites
- Docker Desktop installed and running
- ~4GB of available RAM for the cluster
- Both datasets downloaded into the `data/` directory

### 1. Data Preparation
*   Download the temperature dataset (`GlobalLandTemperaturesByCity.csv`) from Kaggle and place it in `spark-project/data/`.
*   Download the CO2 dataset from the Our World in Data GitHub repository (`owid-co2-data.csv`) and place it in the same directory.

### 2. Starting the Cluster
In the terminal, create the `data`, `notebooks`, and `output` directories, then execute:

```bash
docker-compose up -d
```

Verify that all 4 services are active using `docker-compose ps`.

### 3. Interface Access
| Interface | URL |
|---|---|
| **Jupyter Lab (Driver)** | http://localhost:8888 |
| **Spark Master UI** | http://localhost:8080 |
| **Spark App UI (DAG)** | http://localhost:4040 |
| **Workers** | http://localhost:8081 and 8082 |

*(The Jupyter token can be retrieved via `docker-compose logs jupyter`).*

### 4. Execution
1. Open Jupyter Lab and navigate to `work/climate_analysis.ipynb`.
2. Execute the cells sequentially. The analytical charts will be automatically exported to the `output/` directory.
3. To shut down the cluster: `docker-compose down`.
