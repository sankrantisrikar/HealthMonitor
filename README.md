**HealthMonitor:** Real-Time Patient Vital Monitoring System on Google Cloud
HealthMonitor is a real-time patient vital-sign monitoring system built on Google Cloud Platform (GCP). It simulates live patient data (right now we’re generating our own “live” data with some beautiful inconsistencies 😅 but it really works with real sensor data if you feed it).

The vitals are streamed into Pub/Sub, processed through a Dataflow (Apache Beam) streaming pipeline that computes risk scores and status labels, and stored in BigQuery for real-time alerting, analytics, and ICU-style dashboards.

<img width="1416" height="618" alt="image" src="https://github.com/user-attachments/assets/c4818152-26a7-4862-a1b9-5ddd7d51fe87" />
