# Azure Data Pipeline Project

## Table of Contents

- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Project Overview](#project-overview)
- [Architecture Diagram](#architecture-diagram)
- [Setup Instructions](#setup-instructions)
  - [Azure Setup](#azure-setup)
    - [1. Create a Resource Group](#1-create-a-resource-group)
    - [2. Create an Event Hubs Namespace and Event Hub](#2-create-an-event-hubs-namespace-and-event-hub)
    - [3. Get the Event Hub Connection String](#3-get-the-event-hub-connection-string)
    - [4. Create an Azure SQL Database](#4-create-an-azure-sql-database)
    - [5. Configure the SQL Database Firewall](#5-configure-the-sql-database-firewall)
  - [Local Machine Setup](#local-machine-setup)
    - [1. Install Python and Required Libraries](#1-install-python-and-required-libraries)
    - [2. Clone the Repository](#2-clone-the-repository)
    - [3. Update Configuration Files](#3-update-configuration-files)
- [Running the Project](#running-the-project)
  - [1. Running the Data Producer](#1-running-the-data-producer)
  - [2. Running the Data Consumer](#2-running-the-data-consumer)
  - [3. Verifying Data in SQL Database](#3-verifying-data-in-sql-database)
- [Code Files](#code-files)
  - [data_producer.py](#data_producerpy)
  - [data_consumer.py](#data_consumerpy)
  - [schema_creator.py](#schema_creatorpy)
- [Conclusion](#conclusion)
- [Acknowledgments](#acknowledgments)

---

## Introduction

This project demonstrates how to build a data pipeline using Azure services. It covers data production, ingestion, storage, processing, and visualization using Azure Event Hubs, Azure SQL Database, and Python.

## Prerequisites

- An **Azure account** with sufficient permissions.
- **Python 3.x** installed on your local machine.
- Basic knowledge of Python programming.
- Familiarity with Azure services (Event Hubs, SQL Database).
- **SQL Server Management Studio (SSMS)** or **Azure Data Studio** (optional, for verifying data).

## Project Overview

The data pipeline consists of the following components:

1. **Data Producer**: A Python script that generates data (both from a CSV file and live generated data) and sends it to Azure Event Hubs.

2. **Message Queue**: Azure Event Hubs acts as a message queue to ingest and process streaming data.

3. **Serverless SQL Database**: An Azure SQL Database (serverless tier) to store the data consumed from Event Hubs.

4. **Data Consumer**: A Python script that reads data from Event Hubs and inserts it into the SQL Database.

5. **Schema Creator**: A Python script to generate SQL table schemas based on the data structure.

## Architecture Diagram

![Architecture Diagram](images/architecture_diagram.png)

*(Include an image named `architecture_diagram.png` in the `images` directory of your repository.)*

## Setup Instructions

### Azure Setup

#### 1. Create a Resource Group

1. Log into the [Azure Portal](https://portal.azure.com/).
2. Search for **Resource Groups** in the search bar.
3. Click **Create**.
4. Enter the following details:
   - **Subscription**: Select your subscription.
   - **Resource group**: `DataPipelineRG`
   - **Region**: Choose a region close to you (e.g., `East US`).
5. Click **Review + create** and then **Create**.

#### 2. Create an Event Hubs Namespace and Event Hub

1. In the Azure Portal, search for **Event Hubs**.
2. Click **Create**.
3. Fill in the following details:
   - **Subscription**: Your subscription.
   - **Resource group**: `DataPipelineRG`.
   - **Namespace name**: A unique name (e.g., `datapipeline-namespace`).
   - **Location**: Same as your resource group.
   - **Pricing tier**: **Basic**.
4. Click **Review + create** and then **Create**.
5. After the namespace is created, navigate to it.
6. Under **Entities**, select **Event Hubs**.
7. Click **+ Event Hub** to create a new Event Hub.
8. Enter the **Name**: `samplehub`.
9. Click **Create**.

#### 3. Get the Event Hub Connection String

1. In your Event Hubs namespace, go to **Shared access policies** under **Settings**.
2. Click on **RootManageSharedAccessKey**.
3. Copy the **Connection string–primary key**.

#### 4. Create an Azure SQL Database

1. In the Azure Portal, search for **Azure SQL**.
2. Click **Create** under **SQL databases**.
3. Fill in the following details:
   - **Subscription**: Your subscription.
   - **Resource group**: `DataPipelineRG`.
   - **Database name**: `DataPipelineDB`.
   - **Server**: Click **Create new** and fill in:
     - **Server name**: A unique name (e.g., `datapipeline-server`).
     - **Server admin login**: `sqladmin`.
     - **Password**: A strong password (e.g., `YourStrongPassword123`)
     - **Location**: Same as your resource group.
   - **Compute + storage**: Click **Configure database** and select:
     - **Service tier**: **General Purpose**
     - **Compute tier**: **Serverless**
     - **Autoscaling**: Set the min and max vCores as desired.
     - **Auto-pause delay**: Set to **1 hour** to minimize costs.
   - Click **Apply**.
4. Click **Review + create** and then **Create**.

#### 5. Configure the SQL Database Firewall

1. Navigate to your SQL server (e.g., `datapipeline-server`).
2. Under **Security**, click on **Networking**.
3. Under **Firewall rules**, click **Add your client IPv4 address** to allow your local machine to connect.
4. Click **Save**.

### Local Machine Setup

#### 1. Install Python and Required Libraries

1. **Python 3.x**:
   - Download and install from [python.org](https://www.python.org/downloads/).
2. **Azure Event Hubs SDK**:
   - Open a terminal or command prompt.
   - Install using pip:
     ```bash
     pip install azure-eventhub
     ```
3. **pyodbc** (for SQL Server connectivity):
   ```bash
   pip install pyodbc
   ```
4. **ODBC Driver for SQL Server**:
   - **Windows**: Download and install the [ODBC Driver for SQL Server](https://www.microsoft.com/en-us/download/details.aspx?id=56567).
   - **Mac/Linux**: Follow instructions [here](https://docs.microsoft.com/en-us/sql/connect/odbc/download-odbc-driver-for-sql-server).

#### 2. Clone the Repository

Clone this repository to your local machine:

```bash
git clone https://github.com/yourusername/azure-data-pipeline.git
```

*(Replace `https://github.com/yourusername/azure-data-pipeline.git` with the actual URL of your repository.)*

#### 3. Update Configuration Files

Navigate to the cloned repository and update the configuration placeholders in the code files.

- **Replace placeholders** like `<YOUR_CONNECTION_STRING>`, `<YOUR_EVENT_HUB_NAME>`, `<YOUR_SERVER_NAME>`, `<YOUR_DATABASE_NAME>`, `<YOUR_USERNAME>`, and `<YOUR_PASSWORD>` with your actual values.

---

## Running the Project

### 1. Running the Data Producer

The data producer script generates data and sends it to Azure Event Hubs.

#### Steps:

1. **Ensure the CSV File is Available**:

   - Place your CSV file (e.g., `data.csv`) in the same directory as `data_producer.py`.
   - If using a different file name or path, update the `csv_file` variable in `data_producer.py`.

2. **Run the Script**:

   ```bash
   python data_producer.py
   ```

3. **Select the Data Source**:

   - When prompted, enter:
     - `1` to send **CSV Data**.
     - `2` to send **Live Generated Data**.
     - `3` to send **Both CSV and Live Data Alternately**.

### 2. Running the Data Consumer

The data consumer script reads data from Azure Event Hubs and inserts it into the Azure SQL Database.

#### Steps:

1. **Run the Script**:

   ```bash
   python data_consumer.py
   ```

2. **Select the Data Type to Consume**:

   - When prompted, enter:
     - `1` to consume **CSV Data**.
     - `2` to consume **Live Generated Data**.
     - `3` to consume **Both CSV and Live Data**.

### 3. Verifying Data in SQL Database

You can verify that data has been inserted into your Azure SQL Database.

#### Using SQL Server Management Studio (SSMS):

1. **Connect to the SQL Server**:

   - **Server name**: `<YOUR_SERVER_NAME>.database.windows.net`
   - **Authentication**: SQL Server Authentication
   - **Login**: `<YOUR_USERNAME>`
   - **Password**: `<YOUR_PASSWORD>`

2. **Run a Query**:

   - Open a new query window.
   - Execute:
     ```sql
     SELECT * FROM GeneratedCSVTable;
     SELECT * FROM GeneratedLiveTable;
     ```

---

## Conclusion

By following this guide, you have set up a complete data pipeline using Azure services. You can now generate data, send it through Event Hubs, store it in an Azure SQL Database, and consume it using Python scripts.

## Acknowledgments

- **Azure Documentation**: For detailed guides on using Azure services.
- **Python Community**: For developing libraries like `azure-eventhub` and `pyodbc`.
- **Microsoft**: For providing tools like SSMS and Azure services.

---

**Disclaimer**: Ensure that all sensitive information such as passwords and connection strings are kept secure. Do not share them publicly or commit them to public repositories.

---
