
# Hourly Data Pipeline using AWS & Snowflake

This project demonstrates a serverless, production-ready data pipeline that extracts data from an AWS DynamoDB table every hour, transforms it using AWS Lambda, and ingests it into Snowflake for analytical use. The pipeline is fully automated and designed to be cost-efficient, scalable, and easy to maintain.

---

## 🧠 Project Overview

- **Goal**: Automate hourly data ingestion from DynamoDB into Snowflake.
- **Use Case**: Enable timely analytics and reporting for business users using a fresh data snapshot every hour.
- **Architecture**: Built using AWS EventBridge, Lambda, DynamoDB, and Snowflake Snowpipe.

## 📦 Project Components

This repo contains 3 key scripts, supporting a two-step Lambda-based pipeline:

| File Name                  | Purpose                                                         |
|----------------------------|-----------------------------------------------------------------|
| `dynamoDB file create.py`  | First Lambda: Fetches weather data from an API and stores it in DynamoDB |
| `dynamodbtos3.py`          | Second Lambda: Listens to DynamoDB Streams and pushes data to S3         |
| `snowflake.sql`            | Snowflake SQL script to connect to S3 and ingest data via Snowpipe/API   |

---

## 🚀 How to Run the Pipeline

### 🧱 Step 1: Create and Configure DynamoDB

- Create a **DynamoDB table** to store incoming weather data.
- Enable **Streams** with `NEW_IMAGE` configuration to capture every insert.
- Add basic sample data or test using the first Lambda function.

---

### 🧠 Step 2: First Lambda Function — `dynamoDB file create.py`

- This function fetches weather data via an external API.
- It writes the data directly into the DynamoDB table.
- Attach the following permissions to the function’s role:
  - `AmazonDynamoDBFullAccess`
- Configure **Amazon EventBridge** to trigger this function on a schedule (e.g., every hour or based on external conditions).

---

### 🔁 Step 3: Second Lambda Function — `dynamodbtos3.py`

- This function is triggered by **DynamoDB Streams**.
- It processes the stream events and writes transformed data to S3 in CSV/JSON format.
- Permissions required:
  - `AmazonDynamoDBFullAccess`
  - `AmazonS3FullAccess`
- Ensure this function is configured with the **Stream ARN** of the DynamoDB table as its trigger.

---

### 🔐 Step 4: IAM Role for Snowflake Access

- Create an **IAM role** in AWS with `AmazonS3ReadOnlyAccess`.
- Add a trust relationship to allow Snowflake (via External ID or IAM User) to assume this role.
- Use the IAM role's **ARN** and S3 bucket path in your Snowflake configuration.

---

### ❄️ Step 5: Snowflake Ingestion — `snowflake.sql`

- Open Snowflake and create a **stage** referencing the S3 bucket.
- Create a **file format** matching the data output (CSV/JSON).
- Use a **Snowpipe** or `COPY INTO` command to load the data into a target table.

> ⚙ Update the script with:
> - Your **S3 bucket URL**
> - Your **IAM Role ARN**
> - Desired **target table** and format

---

## 🔄 Workflow Summary

1. EventBridge triggers Lambda #1 → fetches weather data → stores in DynamoDB.
2. DynamoDB Stream triggers Lambda #2 → transforms and pushes to S3.
3. Snowflake periodically loads data from S3 using the provided SQL script.

---

## 📋 Requirements

- Python 3.10x
- AWS Account with IAM access
- Snowflake account with external stage support
- API key for weather data (if applicable)

---

## 🧪 Testing Tips

- Use **CloudWatch Logs** to monitor both Lambda executions.
- Ensure DynamoDB Stream records appear as expected.
- Verify S3 output file format matches the Snowflake file format config.
- Run `SELECT * FROM your_table` in Snowflake to confirm ingestion.




