# End-to-End Azure Data Engineering Project 
In today's data-driven world, organizations are increasingly relying on data engineering to harness the power of their data. This project demonstrates an end-to-end data engineering solution built on Microsoft Azure, leveraging its robust suite of services to extract, load, transform, and analyze data. The goal is to turn raw structured data into actionable insights that drive business decisions.

This project is designed to simulate a real-world scenario where data is ingested from multiple sources, processed, stored, and analyzed to provide meaningful insights. By utilizing Azure's scalable and secure cloud infrastructure, we ensure that the solution is not only efficient but also cost-effective and future-proof.

## Project Overview & Architecture:
This end-to-end Azure Data Engineering project focuses on building a scalable and efficient data pipeline to integrate, process, and analyze structured data from two disparate sources, **BICYCLE MANUFACTURING** data (12,000 records) stored in an on-premises SQL Server and **OLYMPIC GAMES** data (300 MB) hosted in a GitHub repository. Using Azure Data Factory with a **self-hosted integration runtime**, the pipeline extracts and migrates data to the cloud via **ELT (Extract, Load, Transform)** methodology, leveraging **scheduled triggers** for automation and **monitoring** capabilities to ensure pipeline reliability. The project follows the **Medallion architecture** (Bronze, Silver, and Gold layers) to ensure data quality and scalability. 

![image](https://github.com/user-attachments/assets/a170aa45-c999-4c04-a8b9-a2be026912cd)

Data transformation is performed in **Databricks**, utilizing clusters with optimized compute power, **broadcast joins** for efficient data merging, and advanced functions like **window(), withColumn(), regexp_extract(), and sort()** for data enrichment and cleansing. The transformed data is stored in **Parquet format** for efficient storage and querying. The processed data is then loaded into **Azure Synapse Analytics**, where **serverless SQL pools** are used to create schemas, views, and external tables. Access is granted to the admin, and the final curated data is stored in the **Gold layer** for business consumption. 

The GitHub repository integration ensures seamless access to Olympic Games data, while cost monitoring is implemented across all Azure services to optimize resource utilization and maintain budget control. This project demonstrates expertise in data wrangling, ELT processes, data orchestration, warehousing, and visualization, serving as a comprehensive portfolio piece.

## Key Objectives of the Project:
The primary objective of this end-to-end Azure Data Engineering project is to design, implement, and optimize a robust data pipeline that seamlessly integrates, processes, and transforms disparate data sources specifically, bicycle manufacturing data from an on-premises SQL Server and Olympic Games data from a GitHub repository. Leveraging Azure Data Factory with a self-hosted integration runtime, the project will demonstrate efficient data extraction, wrangling, and migration from on-prem and cloud-based sources. By incorporating parameterized values, lookup activities, and For-Each iteration conditions, the pipeline will be optimized for performance and scalability.

Further, the integration of Databricks notebooks will enable advanced data processing, transformation, and enrichment, ensuring the data is ready for analytical and business intelligence purposes. The project will culminate in the creation of a centralized data warehouse, serving as a single source of truth for downstream applications. Finally, the data will be visualized using Power BI, providing actionable insights to end-users.

This project serves as a comprehensive portfolio piece, showcasing expertise in end-to-end data engineering workflows, including data preprocessing, ETL/ELT processes, data warehousing, orchestration, monitoring, and visualization. It highlights the ability to address real-world business challenges by delivering scalable, efficient, and insightful data solutions, making it a valuable resource for data engineering professionals and enthusiasts alike.

## On-Prem & Azure Cloud Services Used:
To achieve the above objectives, this project leverages the following Azure services:

- **SQL Server Management Studio (SSMS):** Used to connect the on-premises SQL Server DB for integration into the Azure data pipeline.
- **Microsoft Integration Runtime Configuration Manager:** Used to set up and manage the self-hosted integration runtime, enabling secure and seamless connectivity.
- **Azure Resource Group:** Used to organize and manage all Azure resources in a single logical unit for streamlined deployment, monitoring, and cost management.
- **Azure Data Lake Gen2:** A secure storage layer to store raw, processed, and transformed data in Parquet format, supporting efficient data analytics and processing.
- **Azure Data Factory (ADF):** Used for data orchestration and ETL (Extract, Transform, Load) processes.
- **Azure Databricks:** A unified analytics platform for advanced data transformations, cleaning, and enrichment using Apache Spark.
- **Azure Synapse Analytics:** A limitless analytics service that brings together big data and data warehousing.
- **Power BI:** A business analytics tool to create interactive dashboards and reports.
- **Azure Monitor:** Used for monitoring and logging the performance of the data pipeline.
- **Azure Entra ID:** An identity and access management, ensuring secure authentication and authorization for users and services accessing Azure resources.
- **Azure Key Vault:** Used to securely store and manage sensitive information such as connection strings, API keys, and credentials, ensuring data security and compliance.

## Steps Taken:
### Step 1: Prepare Data Sources
On-Premises SQL Server:
- Install SQL Server Management Studio (SSMS) to manage the database.
- Made the data set available to be used for further processing and analysis.

![image](https://github.com/user-attachments/assets/38be04bc-0a43-4dab-a2d4-f9538aa17265)

GitHub Repository:
- Ensure the dataset is hosted in a GitHub repository (e.g., CSV files).

Install Self-Hosted Integration Runtime:
- Download and install the Microsoft Integration Runtime Configuration Manager on the on-premises machine.

![image](https://github.com/user-attachments/assets/cbd75d96-c104-4131-b6d3-afb032f06990)

### Step 2: Setup Azure Environment
- Go to Azure Portal → Search for "Resource Groups" → Create a new Resource Group.
- Choose a region (e.g., UK West, East US) based on compliance and latency.

![image](https://github.com/user-attachments/assets/4c1b3300-f211-4e4a-8ed1-e59aa6be1851)

### Step 3: Set up Azure Data Lake Gen2
Create a Storage Account:
- In the Azure portal, navigate to Storage Accounts and create a new account.
- Enable Hierarchical Namespace to use it as a Data Lake Gen2.

Create Containers: Create three containers in the storage account:
- **Bronze**: For raw data.
- **Silver**: For processed data.
- **Gold**: For curated data.
- **Parameter**: For git.json file used to pass .csv files parameter.

### Step 4: Configure Data Connectivity (On-Prem SQL/GitHub to Azure Data Lake)
On-Prem SQL to Bronze Layer:
- Set Up Self-Hosted Integration Runtime (SHIR) for On-Premises Data.
- In Azure Data Factory (ADF) → Go to Manage → Integration Runtimes
- Create Self-Hosted Runtime → Download and install on the on-prem SQL Server machine.
- Register SHIR and test connectivity.

![image](https://github.com/user-attachments/assets/40a00002-e0c0-4dfc-8581-818d9e087f8f)

GitHub to Bronze Layer:
- In Azure Data Factory, the lookup activity will parse the relative URL from the GitHub repository.
- It points to the sink folder and the files within. For each loop container consists of a dynamic copy data activity.
- This will extract all the data present in the GitHub folders and pull the .csv files inside it and load them into ADLS.  

![image](https://github.com/user-attachments/assets/234cc74f-9b9d-4d0c-bd12-2a1532abc5dd)

### Step 5: Build Data Pipeline in Azure Data Factory
Create Linked Services:
- On-Premises SQL Server: Use the self-hosted runtime for connectivity.
- Azure Data Lake Gen2: Connect to the storage account.
- HTTPS: This linked service connects GitHub's raw files with Data lake storage.

Create a Data Pipeline for Ingestion:
- Use Lookup Activity to fetch and parse the relative URL from the GitHub repository as well as read the schema of the tables from the sql server.
- Use For Each Loop Activity to iterate over records dynamically and run on sucess of lookup activity. Embedd a dynamic copy data activity inside the container.
- Use Copy Data Activity to move data dynamically using parametirized values from SQL Server/Git repository to Azure Data Lake Storage (ADLS).
- Store raw data in ADLS (Bronze Layer).

![image](https://github.com/user-attachments/assets/6298fe9a-c339-4cc7-908c-e48599cb8c56)
![image](https://github.com/user-attachments/assets/248437b0-ba71-4e10-bac6-140a784f80ec)

Schedule Triggers:
- Set up a scheduled trigger to run the pipelines daily or as needed.

![image](https://github.com/user-attachments/assets/d519f6da-110a-40d3-af6c-2187e2793556)

Monitor Pipelines:
- Use Azure Monitor to track pipeline performance and troubleshoot issues.

### Step 6: Data Transformation in Azure Databricks
- Set Up Databricks Workspace
> Navigate to Azure Databricks → Create a new Databricks Workspace
- Connect Databricks to ADLS
- Mount ADLS Storage in Databricks:

![image](https://github.com/user-attachments/assets/cf133bbe-c8c5-4f5a-b4ce-124205bf21a8)

### Step 7: Transform Data Using PySpark
Set Up Clusters: Create a cluster with optimized compute power for data processing.

**Bicycle Manufacturing Notebook:** (_Refer to code in the Repo_)
> Data Ingestion:
> -  Read raw data from the bronze container in Data Lake Gen2.

> Data Transformation:
> -  Perform transformations using PySpark
> -  Clean and enrich data.
> -  Apply withcolumn, regexp_replace, agg, groupBy, and sort functions for advanced processing.
> -  Create basic visuals for the transformed data frames. 

> Data Storage:
> -  Save the transformed data in Parquet format to the silver container.

**Olympic Games Notebook:** (_Refer to code in the Repo_)
> Data Ingestion
> -  Read raw data from the bronze container in Data Lake Gen2.
> -  Requires Mounting of ADLS Storage container.

![image](https://github.com/user-attachments/assets/3a960600-d3a9-42a7-a432-a19492c90b1b)

> Data Transformation
> -  Perform transformations using PySpark.
> -  Clean and enrich data.
> -  Use  pyspark.sql.functions (broadcast join) for efficient merging of datasets.
> -  Create newly derived dataframes via suitable and meaningful transformations. 
> -  Apply window, join, to_timestamp, and rank functions for advanced processing and filtering.

> Data Storage
> Save the transformed data in Parquet format to the silver container.

### Step 8: Load Data into Azure Synapse Analytics
Create an Azure Synapse Analytics Workspace:
- Navigate to Azure Synapse Analytics → Create a new Synapse Workspace
- Enable serverless SQL pools to query ADLS data without provisioning a dedicated SQL instance.

![image](https://github.com/user-attachments/assets/ee48270e-fbfe-45e1-ba30-d6ea3f2c8d1e)

Set Up Serverless SQL Pool:
- Create a serverless SQL pool to query data directly from Data Lake Gen2.

Create Schemas and Tables:
- Use SQL scripts to create schemas and external tables pointing to the silver and gold containers.

Load Data into Gold Layer:
- Use Synapse scripts to load the final curated data into the gold container.

Create Views:
- Create views for business users to access the curated data.

### Step 9: Data Visualization with Power BI
- Download Power BI Desktop & connect the data source to Azure Synapse
- Open Power BI → Click Get Data
- Choose Azure Synapse Analytics
- Connect to Serverless SQL Pool
- Build Dashboards and Reports
- Create visualizations based on Synapse tables

- Olympic Games Dashboard on Power BI Desktop
  ![image](https://github.com/user-attachments/assets/ba385654-181d-4525-99ac-d34110ef8eeb)

- Bicycle Manufacturing Company Dashboard on Power BI Desktop
  ![image](https://github.com/user-attachments/assets/57fa7200-b614-4708-b58b-1129db5cca8f)


### Step 10: Implement Monitoring and Alerting Using Azure Monitor
Set Up Azure Entra ID:
- Configure role-based access control (RBAC) for users and services.

![image](https://github.com/user-attachments/assets/e9a088b8-8d5f-483b-915c-6fd2da9afab1)

Store Secrets in Azure Key Vault:
- Store connection strings, API keys, and credentials securely in Azure Key Vault.

![image](https://github.com/user-attachments/assets/15cf01ad-f66b-4164-943d-31cfcb9b0813)

Monitor Costs:
- Use Azure Cost Management to track and optimize resource usage.

![image](https://github.com/user-attachments/assets/6e943d06-0737-4a21-80b7-c7624aaacc12)

Enable Azure Monitor:
- Set up alerts and logs for pipeline failures or performance issues.
- In Azure Monitor → Configure Alerts for:
- ADF Pipeline Failures, Track ADF pipeline execution logs
- Databricks Notebook Errors

![image](https://github.com/user-attachments/assets/6e322fbd-de3a-4163-a239-7c3e422b64cd)

### Step 11: Testing & Validation
Test the End-to-End Pipeline:
- Run the entire automated pipeline with scheduled/tumbling window triggers and validate data at each stage (bronze, silver, gold).

Verify Data Quality:
- Ensure data is accurate, complete, and consistent. To check, perform E2E testing with source & target data.

Optimize Performance:
- Fine-tune Databricks clusters, ADF pipelines, and Synapse queries for better performance.

### Step 12: Deleting Resource Group
Deleting resources after completing the project is a critical step to avoid unnecessary costs and maintain a clean Azure environment. 
However, it should be done carefully to ensure that no important data or configurations are lost.

### Challenges Faced

- Integrating data from multiple sources (e.g., on-premises SQL Server and cloud-based GitHub repositories) due to differences in data formats & structures.
- Ensuring data quality and consistency across different data sources.
- Managing access for on-premise and cloud users with security credentials in Entra ID and key vault.
- Setting parameters for dynamic parsing and migration of source files to sink folder using JSON script.
- Mounting the storage account to azure data lake bronze container.
- Creating external tables in synapse analytics using CETAS which required scope credential, data source link, file formats etc.
- Integration of various azure services and applications, their linkage for effecient assembly.
- My experience with Power BI Desktop for data visualization is currently rusty. 



