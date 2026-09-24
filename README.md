# Metadata-Driven ETL Framework using Azure Data Factory

## 📌 Project Overview

This project demonstrates a **metadata-driven ETL framework built using Azure Data Factory (ADF)** to automate data ingestion, incremental processing, schema handling, transformation, and monitoring.

The framework is designed to minimize hardcoded pipeline logic by using metadata, parameters, dynamic expressions, and reusable ADF components.

The project also demonstrates ingestion from both **file-based sources and REST APIs**, dynamic schema mapping, transformation using **ADF Mapping Data Flows**, and storage of curated data in **Delta format**.

---

## 🎯 Project Objectives

The main objectives of this project are to:

* Build a reusable metadata-driven ETL framework.
* Implement incremental data loading.
* Dynamically process multiple files using ADF activities.
* Ingest data from a REST API using HTTP GET.
* Implement dynamic schema mapping.
* Transform and store data in Delta format.
* Implement pipeline orchestration using Execute Pipeline.
* Implement failure notifications using Azure Logic Apps.
* Integrate the ADF project with GitHub for source control.
* Create a maintainable and reusable ETL architecture.

---

# 🏗️ Architecture

```
                    SOURCE SYSTEMS
                          |
             ┌────────────┴────────────┐
             |                         |
       File-based Source          REST API
             |                         |
             └────────────┬────────────┘
                          ↓
                  Azure Data Factory
                          |
                  Metadata / Config
                          |
                    Parent Pipeline
                          |
                 Execute Pipeline
                          |
                  Child Pipeline
                          |
              ┌───────────┴───────────┐
              ↓                       ↓
       Get Metadata               REST GET
              ↓                       ↓
          ForEach                API Response
              ↓                       ↓
        Copy Activity          Schema Mapping
              |                       |
              └───────────┬───────────┘
                          ↓
                  Mapping Data Flow
                          ↓
                    Transformation
                          ↓
                    Delta Tables
                          ↓
                   Data Consumption

                Failure / Exception
                          ↓
                    Azure Logic Apps
                          ↓
                  Email Notification
```

---

# 🔑 Key Features

## 1. Metadata-Driven Incremental Loading

The pipeline uses metadata and runtime values to determine which files need to be processed.

The incremental loading process is implemented using:

* Get Metadata Activity
* ForEach Activity
* Copy Activity
* Last `updated_date` tracking
* Execute Pipeline Activity
* Dynamic expressions

### High-level flow

```text
Parent Pipeline
      |
      ↓
Execute Pipeline
      |
      ↓
Get Metadata
      |
      ↓
Identify files
      |
      ↓
ForEach
      |
      ↓
Check updated_date
      |
      ↓
Copy only required data
      |
      ↓
Update last updated_date
```
<img width="924" height="253" alt="incremental loading" src="https://github.com/user-attachments/assets/c8a68392-f48f-4c75-9ece-ae8062ab269e" />

The pipeline avoids unnecessarily reprocessing previously processed data.

---

# 2. Parent and Child Pipeline Orchestration

The project separates orchestration from processing logic.

```text
Parent Pipeline
      |
      ├── Configuration / Control
      |
      └── Execute Pipeline
                |
                ↓
          Child Pipeline
                |
          Data Processing
```
<img width="804" height="331" alt="execute pipeline" src="https://github.com/user-attachments/assets/f5fb0eb9-c382-4faa-af01-7926c893b4db" />

The **Execute Pipeline Activity** is used to invoke the reusable child pipeline.

This approach allows the processing logic to be reused from multiple orchestration pipelines.

---

# 3. REST API Data Ingestion

The framework also supports ingestion from a REST API.

The REST source is accessed using the **HTTP GET method**.

```text
ADF REST Linked Service
          |
          ↓
     REST Dataset
          |
          ↓
     Copy Activity
          |
          ↓
      ADLS Storage
```
<img width="1214" height="367" alt="rest api" src="https://github.com/user-attachments/assets/cbf92314-2544-45d6-931c-773be840c7cf" />

The pipeline dynamically handles API responses and passes the data into the downstream transformation process.

### Concepts demonstrated

* REST Linked Service
* REST Dataset
* GET request
* Copy Activity
* API response handling
* Dynamic configuration

---

# 4. Dynamic Schema Mapping

The project demonstrates dynamic mapping between source and destination schemas.

Instead of manually configuring every column mapping, the pipeline uses dynamic mapping/configuration to enforce the expected target schema.

Example:

```text
Source
----------------
customer_id
customer_name
email
updated_date

        ↓

Dynamic Mapping

        ↓

Target
----------------
customer_id
customer_name
email
updated_date
```
<img width="635" height="329" alt="Dynamic Mapping" src="https://github.com/user-attachments/assets/c3ff86b7-46d6-4d49-8cae-bae52030734d" />

This helps reduce manual configuration when processing multiple datasets with similar structures.

---

# 5. Mapping Data Flow

ADF Mapping Data Flow is used for transformation and preparation of the ingested data.

High-level flow:

```text
Source
  ↓
Transformation
  ↓
Schema Enforcement
  ↓
Target
```
<img width="1354" height="261" alt="Delta table" src="https://github.com/user-attachments/assets/b0e7ceb1-a671-4dec-9ac8-0fd4f4a4528a" />

The transformed data is written in **Delta format** for downstream consumption.

---

# 6. Delta Table Storage

The final transformed data is stored as Delta tables.

```text
Raw Data
   ↓
ADF
   ↓
Data Flow
   ↓
Transformation
   ↓
Delta Table
```
<img width="1595" height="363" alt="result delta" src="https://github.com/user-attachments/assets/bf2b22a4-5d66-4b2b-b903-d6c8118f91c9" />

Using Delta format provides a reliable structure for storing curated data and prepares the data for further analytics and processing.

---

# 7. Error Handling and Notifications

The project integrates ADF with **Azure Logic Apps** for failure notifications.

```text
ADF Pipeline
     |
     ↓
Activity Failure
     |
     ↓
Logic Apps
     |
     ↓
Email Notification
```
<img width="1296" height="671" alt="LogicApp" src="https://github.com/user-attachments/assets/2125ea24-9045-4d8a-9617-b27936edcf0f" />

The notification workflow can provide information such as:

* Pipeline name
* Failed activity
* Execution status
* Error information
* Pipeline run details

This allows failures to be identified without continuously monitoring the ADF portal.

---

# 8. GitHub Integration

The Azure Data Factory project is integrated with GitHub for source control.

The repository maintains ADF artifacts such as:

```text
Pipelines
Datasets
Linked Services
Data Flows
Triggers
Parameters
```

This provides version control and makes the project easier to maintain and reproduce.

---

# 🛠️ Technologies Used

| Technology            | Purpose                        |
| --------------------- | ------------------------------ |
| Azure Data Factory    | Pipeline orchestration and ETL |
| ADLS Gen2             | Data storage                   |
| REST API              | External data ingestion        |
| ADF Mapping Data Flow | Data transformation            |
| Delta                 | Curated data storage           |
| Azure Logic Apps      | Failure notifications          |
| GitHub                | Source control                 |
| Dynamic Expressions   | Runtime parameterization       |
| Metadata              | Pipeline configuration         |

---

# 🔄 End-to-End Pipeline Flow

The complete process can be summarized as:

```text
1. Pipeline Trigger
       ↓
2. Read Metadata
       ↓
3. Execute Child Pipeline
       ↓
4. Identify New/Updated Data
       ↓
5. ForEach Source/File
       ↓
6. Copy Data
       ↓
7. Update Last Updated Date
       ↓
8. REST API Ingestion
       ↓
9. Dynamic Schema Mapping
       ↓
10. Mapping Data Flow
       ↓
11. Transform Data
       ↓
12. Write Delta Table
       ↓
13. Monitor Pipeline
       ↓
14. Failure → Logic Apps → Email
```

---

# 📊 Incremental Loading Approach

The pipeline maintains the last processed `updated_date`.

For example:

```text
Last successful load:
2026-09-23 10:00:00
```

When the next pipeline runs, records/files updated after the stored timestamp are considered for processing.

```text
Source
--------------------------------
updated_date

2026-09-22  → Already processed
2026-09-23  → Already processed
2026-09-24  → New/updated
2026-09-24  → New/updated
```
<img width="924" height="253" alt="incremental loading" src="https://github.com/user-attachments/assets/3e460e9e-42f7-4dfa-96bf-966ec5dfaf3f" />


After successful processing, the latest `updated_date` is stored for the next execution.

This allows the pipeline to avoid processing the same data repeatedly.

---

# 🧩 Important ADF Concepts Demonstrated

This project provides hands-on experience with:

### Pipeline Orchestration

* Parent pipeline
* Child pipeline
* Execute Pipeline Activity

### Data Ingestion

* Copy Activity
* Get Metadata Activity
* REST API ingestion
* HTTP GET

### Dynamic Processing

* Parameters
* Variables
* Dynamic expressions
* ForEach
* Dynamic schema mapping

### Transformation

* Mapping Data Flow
* Schema enforcement
* Delta output

### Incremental Processing

* `updated_date`
* Last processed value
* Incremental file/data identification

### Monitoring

* Pipeline execution monitoring
* Failure handling
* Logic Apps notifications

### DevOps

* GitHub integration
* Source control
* Versioning of ADF artifacts

---

# 🚀 Key Learning Outcomes

Through this project, I gained practical experience in:

* Designing reusable ADF pipelines.
* Implementing metadata-driven processing.
* Building incremental data ingestion workflows.
* Working with REST APIs in ADF.
* Using dynamic expressions and runtime configuration.
* Implementing dynamic schema mapping.
* Building Mapping Data Flows.
* Writing transformed data in Delta format.
* Implementing pipeline orchestration using parent/child pipelines.
* Integrating ADF with Logic Apps for operational notifications.
* Managing ADF artifacts through GitHub.

---

# 🔮 Future Enhancements

Potential enhancements to the framework include:

* Implementing a centralized SQL metadata/control table.
* Adding watermark management through a control table.
* Implementing retry and restart mechanisms.
* Adding Azure Key Vault for secrets management.
* Implementing Managed Identity authentication.
* Adding Azure Monitor and Log Analytics.
* Adding automated CI/CD deployment.
* Adding data-quality validation and quarantine handling.
* Extending the framework to support multiple source systems.
* Adding Databricks/PySpark processing for large-scale transformations.

---

# 👩‍💻 Project Summary

This project demonstrates how Azure Data Factory can be used to build a **reusable, configurable, and maintainable ETL framework** rather than creating separate hardcoded pipelines for every dataset.

The combination of metadata-driven processing, incremental loading, REST API ingestion, dynamic schema mapping, Data Flow transformations, Delta storage, Logic Apps notifications, and GitHub integration provides an end-to-end example of an Azure-based data engineering workflow.
