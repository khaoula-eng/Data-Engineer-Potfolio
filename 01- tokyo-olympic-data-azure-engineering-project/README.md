# 📊 Tokyo Data Pipeline on Azure

This project demonstrates an end-to-end Big Data pipeline on Microsoft Azure, leveraging Olympic Tokyo data. It covers ingestion, processing, transformation, and visualization using a full stack of Azure services.

# 🌐 Architecture Overview

<img width="1820" height="559" alt="image" src="https://github.com/user-attachments/assets/8058f216-87f5-4451-88de-194bccde9717" />


# 🧰 Tech Stack
Azure Storage Account (Raw data storage)

Azure Data Factory (ETL Orchestration)

Azure Databricks (Data transformation with PySpark)

Azure Synapse Analytics (Data warehousing)

Tableau (Data visualization)


# 📂 Project Structure

## 📥 Part 1: Data Source & Storage Setup

The dataset used in this project was obtained from an **open-source Kaggle dataset**.  
📎 **Kaggle Link**: [Original Tokyo Olympic Data - Open Source]([https://www.kaggle.com/your-dataset-link](https://www.kaggle.com/datasets/arjunprasadsarkhel/2021-olympics-in-tokyo)) 

### 1. Create an Azure Storage Account

Set up a **Storage Account** in your Azure subscription to host your raw and processed data.

- Go to the Azure Portal → Storage Accounts → `+ Create`
- Choose a unique name, resource group, and region
- Enable **Hierarchical Namespace** (if you’re using Data Lake Gen2)

### 2. Create Containers for Organizing Data

Inside the Storage Account, create containers:

- `raw-data` → for the raw Kaggle dataset
- `Transformed-data` → for the output after Databricks processing

### 3. Upload Data Only via Data Factory Flow

> ⚠️ **Important:**  
Do **not** manually upload files into the containers.  
The dataset will be uploaded **automatically by the Azure Data Factory pipeline**, which ensures data flow tracking and orchestration.

The ADF pipeline is triggered to:
- Check if data exists in the source (e.g., local or blob)
- Ingest it to the Storage Account containers
- Notify or trigger the Databricks job for transformation

---

## 📥 Part 2: Data Ingestion – HTTP (CSV) to Azure Blob Storage

This project ingests open-source Tokyo Olympic datasets in **CSV format** from a public **HTTP API endpoint** directly into **Azure Blob Storage** using **Azure Data Factory (ADF)**.

Although the API returns static CSV files, it behaves as a data service accessible over HTTP, making it suitable for pipeline ingestion.

This ingestion is the first step of the pipeline and ensures data is pulled in a structured, automated, and scalable way.

---

### 🧬 Pipeline Flow Overview

The flow of this ingestion process is as follows:

<img width="1508" height="494" alt="image" src="https://github.com/user-attachments/assets/cc851952-5001-4bd6-8eb4-967ba983de1c" />

## 🔥 Part 3: Data Transformation with Azure Databricks

In this stage, we process the ingested Tokyo CSV data using **Azure Databricks** to prepare it for analysis. Since the dataset is mostly clean, transformations are minimal but crucial to align data schemas and formats.

---

### 🛠 Setup: Create Compute & Cluster

1. **Create an Azure Databricks workspace** in your Azure portal.
2. Navigate to **Clusters** and create a new cluster with:
   - Appropriate **cluster size** based on your data volume (e.g., Standard_DS3_v2).
   - Set the **Databricks Runtime version** (e.g., 13.x LTS).
   - Enable **Auto-termination** to save costs.
3. Start the cluster and wait until its status changes to **Running**.

---

### 🔐 Secure Connection: App Registration & Secrets

To connect Azure Data Factory or other services securely to your Databricks cluster:

1. Register an **Azure AD application** in Azure Active Directory:
   - Note the **Application (client) ID** and **Directory (tenant) ID**.
2. Create a **client secret** for the application and save it securely.
3. Assign the app **Contributor** or required roles on the Databricks workspace.
4. Use these credentials (app ID, secret key, tenant ID) in your connection strings or linked services.

---

### 🧪 Running Data Transformations with PySpark

- The PySpark script responsible for data transformation is located in this repository:  
  `Tokyo Olympic Transformation.ipynb`  

- Main transformations include:
  - Reading the CSV data from the mounted Azure Blob Storage.
  - Minimal cleaning (data is clean, do not need alot of transformation).
  - Saving the cleaned data back to a storage location accessible by Synapse.

## 📊 Part 4: Data Analytics with Azure Synapse
After the data is transformed and saved in Azure Data Lake Gen2, it becomes ready for analytical queries and reporting. This step uses Azure Synapse Analytics, which acts as a powerful data warehouse and analytics layer.

✅ Steps to Set Up Azure Synapse Analytics

Create a Synapse Workspace:

- In the Azure Portal, search for Azure Synapse Analytics and click + Create.
- Provide a name, region, and storage account (same as your data lake).
- Link the Data Lake Storage:
- Use the Linked Services tab to connect Synapse with your Azure Data Lake.
- Ensure your transformed data container (transformed-data) is accessible.
- Create External Tables or Views:
Use Synapse SQL Pools or Serverless SQL Pools to define external tables.
Point them to the transformed CSV or Parquet files.

Query and Analyze:

Use Synapse Studio to write and run SQL queries on the transformed data.
You can also run Spark jobs, notebooks, or pipelines for further analysis.

<img width="1352" height="748" alt="image" src="https://github.com/user-attachments/assets/248ffd29-1f3c-4351-8dd9-b17da2b91b02" />

This Synapse query calculates the total number of medals won by each country, enabling quick comparison of national performances in the Tokyo Olympics.


![SQL script 1 (1)](https://github.com/user-attachments/assets/f7dd24df-da48-4d7b-92da-bce4de56c165)

This Synapse query calculates the total number of medals won by each country, enabling quick comparison of national performances in the Tokyo Olympics; while the built-in chart provides a quick view, additional dashboards in Power BI, Tableau, or Looker Studio offer richer interactivity, customization, and advanced visual storytelling.

## 📈 Part 5: Dashboards and Visualization
 The final step of the pipeline involves visualizing the analytics-ready data. You can connect Power BI, Looker Studio, or Tableau directly to Azure Synapse Analytics or to Azure Data Lake.

🔌 Connection Options
Power BI: Use the Azure Synapse Analytics connector to load data models and build interactive dashboards.
Supports both DirectQuery and Import modes.

Looker Studio: Connect to data exposed through REST APIs or BigQuery connectors (if cross-platform).

Tableau: Use the Azure Synapse connector or load data via ODBC.


### 🔁 Recap: End-to-End Pipeline Flow

Here’s how the complete pipeline works:
- Data Source: Public HTTP API (CSV format)
- Ingestion: Azure Data Factory pulls data into Azure Data Lake Gen2 (raw-data)
- Transformation: Azure Databricks processes and cleans the data → saved to transformed-data
- Analytics: Azure Synapse Analytics queries and analyzes the data
- Visualization: Power BI / Tableau / Looker Studio dashboards consume the final datasets





