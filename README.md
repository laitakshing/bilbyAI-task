# Pipeline Overview
![image](https://github.com/user-attachments/assets/6d336602-06c8-4bd4-9f4f-2b72d8ce3cba)


# Proposed Final Pipeline Summary:
1.	Batch Ingestion
Cloud Composer triggers → Cloud Run scripts (API/Web Scraping) → Raw data GCS Bucket
2.	Manual Uploads
Files → Cloud Storage → Pub/Sub trigger → Cloud Functions (cleanup/validation) → Raw data GCS Bucket
3.	Data Processing (Shared)
Raw data GCS Bucket → Pub/Sub → Vertex AI (NLP model inference) → Cleaned data GCS Bucket → BigQuery External Tables
4.	Data Consumption
BigQuery → Cloud Run REST API → End-user applications/dashboards
5.	CI/CD & IaC
GitHub Actions for automated CI/CD, Terraform for infrastructure provisioning.
6.	Monitoring & Alerting
Cloud Monitoring for metrics, logging & alerting via Slack integration.


### Architecture Overview 

| **Components**         | **Chosen Solution**                     | **Remarks**                              |
|------------------------|-----------------------------------------|------------------------------------------|
| Compute Resources      | Cloud Run, Cloud Functions              | Flexibility and auto-scaling             |
| Storage                | Cloud Storage, BigQuery (External)      | Cost-effective, scalable                 |
| ML Serving             | Vertex AI Prediction                    | Managed, auto-scaling ML service         |
| Orchestration          | Cloud Composer (Airflow)                | Robust orchestration                     |
| API for Data Access    | Cloud Run REST API                      | Extracted entities available via an API  |
| Monitoring             | Cloud Monitoring, Slack Alerts          | Integrated, proactive monitoring         |
| CI/CD                  | GitHub Actions, Terraform               | Automation & Infra-as-Code               |
| IaC                    | Terraform                               | Widely adopted, easy integration         |

# Details:
## 1. Batch Pipeline (API Calls and Web Scraping)

  •	Scripts Deployment: Both API call and web scraping scripts are containerized and hosted on Google Cloud Run.
  
  •	Data Collection: Aggregated data from external sources stored in Cloud Storage (GCS) (raw-data bucket).
  
  •	Data Processing: New files in raw-data trigger Pub/Sub events for NLP inference on Vertex AI.
  
  •	Data Storage: NLP output stored in structured GCS bucket (cleaned-entity-data).
  
  •	Data Analysis: Accessed via BigQuery external tables.
  
  •	Data Consumption: RESTful API hosted on Cloud Run for user access.

## 2. Manual Upload Pipeline (File Upload)

•	Initial Upload: Manual uploads trigger Pub/Sub events.

•	Data Validation: Cloud Functions perform initial cleaning and validation.

•	Integration: Cleaned data stored in raw-data bucket, triggering NLP processing on Vertex AI.

•	Output: Entity extraction results stored in cleaned-entity-data bucket.

•	Analytics: Accessible through BigQuery external tables.

•	API Exposure: Data served via RESTful API on Cloud Run.

# Infrastructure & Operations

## Orchestration

•	Managed by Cloud Composer (Airflow), handling pipeline orchestration, scheduling, monitoring, and error-handling.

## CI/CD Pipeline

GitHub Actions handles CI/CD:

  • Testing: Automated via pytest.

  • Code Quality: Checked using flake8.

  • Security: Automated vulnerability scanning.

  • Deployment: Automated deployment to Cloud Run and Vertex AI via Terraform.

## Monitoring & Alerting

Cloud Monitoring integrated with Slack alerts for:

  • Vertex AI endpoint latency.

  • Pipeline execution failures.

  • Resource usage anomalies.

  • Airflow DAG errors.

## Disaster Recovery & Reliability

• Cloud Storage: Regional replication for resilience.

• BigQuery: Regular dataset snapshots.

• Robust configurations for Vertex AI and Cloud Run for high availability.

## Security Measures

• Detailed IAM roles and permissions.

• Encryption at rest and in transit.

• Network-level security via VPC and firewall rules.

## Cost & Efficiency Considerations

• GCS lifecycle management for storage cost optimization.

• BigQuery table partitioning and clustering.

• Auto-scaling parameters for Cloud Run and Vertex AI for optimal cost-performance balance.
- **Cloud Run**:
  - `min-instances: 0`, `max-instances: 10`, `concurrency: 80` to optimize cost vs performance.
- **Vertex AI**:
  - `minReplicaCount: 0`, `maxReplicaCount: 5` with autoscaling based on request volume.
