**HealthMonitor:** Real-Time Patient Vital Monitoring System on Google Cloud
HealthMonitor is a real-time patient vital-sign monitoring system built on Google Cloud Platform (GCP). It simulates live patient data (right now we’re generating our own “live” data with some beautiful inconsistencies 😅 but it really works with real sensor data if you feed it).

The vitals are streamed into Pub/Sub, processed through a Dataflow (Apache Beam) streaming pipeline that computes risk scores and status labels, and stored in BigQuery for real-time alerting, analytics, and ICU-style dashboards.

**Architecture Overview**
<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/2d630453-6582-440c-9bff-716a4762d3ff" />

# Component Breakdown

##  Synthetic Vital Generator (Python) 
A Python script generates periodic vital-sign readings for multiple patients.

It produces realistic values for:

- Heart rate  
- Oxygen saturation (SpO₂)  
- Temperature  
- Respiratory rate  
- Timestamp  

This data includes natural randomness — and occasionally amusing inconsistencies 😅 — to mimic real-world sensor noise.

Each reading is formatted as JSON and published to **Pub/Sub**.

---

##  Pub/Sub (Ingestion Layer)  
Pub/Sub acts as the real-time message ingestion system.

It provides:

- Scalable message delivery  
- Low latency  
- Guaranteed ordering (per publisher)  
- Ability to connect multiple downstream consumers  

---

##  Dataflow (Apache Beam) Streaming Pipeline
The core processing engine of HealthMonitor.

The pipeline:

- ✔ Reads messages from Pub/Sub  
- ✔ Parses and validates JSON  
- ✔ Computes a **risk score**  
- ✔ Assigns a status label:  
  - **STABLE**  
  - **ELEVATED**  
  - **CRITICAL**  
- ✔ Writes enriched records to **BigQuery**  

This enables continuous real-time monitoring of patient vitals.

---

##  BigQuery (Analytical Storage)  
A central dataset stores curated, structured patient vital data.

### **Schema Example**

| Column        | Type       |
|---------------|------------|
| patient_id    | INTEGER    |
| heart_rate    | FLOAT      |
| spo2          | FLOAT      |
| temperature   | FLOAT      |
| resp_rate     | FLOAT      |
| risk_score    | INTEGER    |
| status        | STRING     |
| ts            | TIMESTAMP  |
| ingest_ts     | TIMESTAMP  |

Use BigQuery for:

- Identifying high-risk patients  
- Tracking health trends  
- Reviewing ICU events  

---

##  Dashboard Layer (Power BI)  
Connect BigQuery to Power BI to build a real-time ICU dashboard with:

- Heart-rate trends  
- SpO₂ changes  
- Temperature spikes  
- Critical condition alerts  
- Live patient monitoring panels

--- 

**Features**
- Real-time vital ingestion
- Simulated data (sensor-ready architecture)
- Apache Beam streaming transformations
- Risk scoring classification
- BigQuery analytical storage
- Power BI ICU dashboard
  
---

**Future Enhancements**
- Integrate real sensor devices
- Add SMS/email alerts for CRITICAL events
- Implement ML anomaly detection
- Multi-patient ICU room visualization
- Add REST API for patient metadata


