🏥 **HealthMonitor** – End-to-End Healthcare Data Pipeline
HealthMonitor is a clean, modular, and extensible healthcare data engineering project designed to simulate, ingest, process, and prepare patient vitals data using a modern streaming data pipeline. The project focuses on real-world data engineering practices such as streaming ingestion, data validation, medallion architecture (Bronze–Silver–Gold), and analytics-ready outputs.

**Architecture Overview**

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/43b224b1-0bb7-44c3-81a0-a64d32e83802" />


## Component Breakdown

### Synthetic Vital Generator (Python)

A Python-based simulator generates continuous patient vital-sign readings for multiple patients.

It produces:
- Heart rate  
- Oxygen saturation (SpO₂)  
- Body temperature  
- Respiratory rate  
- Event timestamp  

The generator introduces controlled randomness and occasional anomalies to reflect real-world sensor behavior.  
Each record is formatted as JSON and published to **Google Cloud Pub/Sub**.

---

### Pub/Sub (Ingestion Layer)

**Google Cloud Pub/Sub** serves as the real-time ingestion layer.

It provides scalable, low-latency message delivery and decouples data producers from downstream consumers, enabling reliable ingestion of high-velocity healthcare data streams.

---

### Dataflow (Apache Beam) Streaming Pipeline

The main processing layer is implemented using **Apache Beam on Cloud Dataflow**.

The pipeline:
- Reads streaming messages from Pub/Sub  
- Parses and validates JSON payloads  
- Cleans and standardizes vital-sign data  
- Computes a patient risk score  
- Assigns a health status label (`STABLE`, `ELEVATED`, `CRITICAL`)  
- Writes enriched records to **BigQuery** using streaming inserts  

This enables continuous real-time monitoring of patient vitals.

---

### BigQuery (Analytical Storage)

**BigQuery** stores curated and analytics-ready patient vital data.

#### Example Schema

| Column      | Type      |
|------------|-----------|
| patient_id | STRING    |
| heart_rate | FLOAT     |
| spo2       | FLOAT     |
| temperature| FLOAT     |
| resp_rate  | FLOAT     |
| risk_score | INTEGER   |
| status     | STRING    |
| ts         | TIMESTAMP |
| ingest_ts  | TIMESTAMP |

BigQuery supports real-time querying, trend analysis, and patient-level aggregation.

---


### Live Dashboard (Looker Studio)

🔗 **Interactive Dashboard:**  
https://lookerstudio.google.com/reporting/d41bed9b-46a1-4990-836d-f3d080171d21

This Looker Studio report connects directly to BigQuery and provides real-time visualization of patient vitals, risk levels, and trends. It enables patient-level monitoring, anomaly detection, and quick assessment of health status using streaming data.

<img width="1145" height="780" alt="Screenshot 2025-12-29 at 1 13 12 AM" src="https://github.com/user-attachments/assets/737cec6b-c4f1-4c59-888d-160e981436e7" />

### ⚙️ Setup Instructions
Authenticate with GCP:
```bash
gcloud auth application-default login
```

### Step 1: Enable Required GCP Services

Enable the required Google Cloud services:

```bash
gcloud services enable pubsub.googleapis.com dataflow.googleapis.com bigquery.googleapis.com
```
### Step 2: Create Pub/Sub Resources

Create a Pub/Sub topic for ingesting patient vitals:

```bash
gcloud pubsub topics create patient-vitals-topic
```
### Step 3: Configure Environment Variables

Create `.env` files in the following directories.

#### `data_simulator/.env`

```env
PROJECT_ID=your-gcp-project-id
TOPIC_NAME=patient-vitals-topic
PUBLISH_INTERVAL_SECONDS=2
```
dataflow/.env

PROJECT_ID=your-gcp-project-id
TOPIC_NAME=patient-vitals-topic
BQ_DATASET=healthmonitor
BQ_TABLE=patient_vitals
REGION=us-central1
### Step 4: Create BigQuery Dataset and Table

Create the dataset:

```bash
bq mk healthmonitor
```
```Create the table to store processed patient vitals:
bq mk healthmonitor.patient_vitals \
patient_id:STRING,heart_rate:FLOAT,spo2:FLOAT,temperature:FLOAT,resp_rate:FLOAT,risk_score:INTEGER,status:STRING,ts:TIMESTAMP,ingest_ts:TIMESTAMP
```
### Step 5: Run the Dataflow Streaming Pipeline

Start the Apache Beam streaming pipeline on Cloud Dataflow:

```bash
python dataflow/streaming_medallion_pipeline.py
```
This job reads streaming messages from Pub/Sub, processes patient vitals, and streams enriched records into BigQuery.
### Step 6: Start the Data Simulator

In a separate terminal window, run:

```bash
python data_simulator/patient_vitals_simulator.py
```
### Step 7: Verify Output

Verify that data is flowing through the system correctly.

#### BigQuery validation

```sql
SELECT *
FROM `healthmonitor.patient_vitals`
ORDER BY ingest_ts DESC;
```
You should see continuously updating records with patient vitals, risk scores, and status labels.
**Dataflow monitoring**
Navigate to Cloud Dataflow in the GCP Console
Confirm the job status is Running
Ensure no errors are reported
**Dashboard**
Open the Looker Studio dashboard for live monitoring:
https://lookerstudio.google.com/reporting/d41bed9b-46a1-4990-836d-f3d080171d21

