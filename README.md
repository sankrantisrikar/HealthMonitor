🏥 HealthMonitor – End-to-End Healthcare Data Pipeline
HealthMonitor is a clean, modular, and extensible healthcare data engineering project designed to simulate, ingest, process, and prepare patient vitals data using a modern streaming data pipeline. The project focuses on real-world data engineering practices such as streaming ingestion, data validation, medallion architecture (Bronze–Silver–Gold), and analytics-ready outputs.
The project will be fully dockerized in later stages to ensure easy reproducibility and portability. Dockerization allows professors, reviewers, or collaborators to run the entire pipeline locally with minimal setup—without worrying about Python versions, dependencies, or environment conflicts.

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

### Dashboard Layer (Looker)

**Looker** connects directly to BigQuery to provide a real-time monitoring dashboard.

## Live Dashboard (Looker Studio)

🔗 **Interactive Dashboard:**  
https://lookerstudio.google.com/reporting/d41bed9b-46a1-4990-836d-f3d080171d21

This Looker Studio report connects directly to BigQuery and provides real-time visualization of patient vitals, risk levels, and trends. It enables patient-level monitoring, anomaly detection, and quick assessment of health status using streaming data.

<img width="1145" height="780" alt="Screenshot 2025-12-29 at 1 13 12 AM" src="https://github.com/user-attachments/assets/737cec6b-c4f1-4c59-888d-160e981436e7" />
