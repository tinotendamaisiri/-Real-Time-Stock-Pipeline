# Enterprise-Grade Near Real-Time Stock Market Analytics Pipeline (AWS)

A production-style, event-driven, serverless stock analytics
architecture built on AWS.\
Designed to demonstrate scalable streaming ingestion, structured
processing, historical analytics, and automated alerting using managed
cloud services.

------------------------------------------------------------------------

## 🏗 Architecture Diagram

```{=html}
<!-- 
PASTE YOUR ARCHITECTURE DIAGRAM BELOW

Example:
![Architecture Diagram](docs/architecture.png)
-->
```

------------------------------------------------------------------------

# Executive Summary

This project implements a near real-time stock market data analytics
pipeline using AWS managed services and modern cloud-native design
principles.

The system ingests streaming stock data, processes and enriches it,
stores both structured and raw data layers, enables historical SQL
querying, and performs automated trend detection with alert
notifications.

The architecture emphasizes:

-   Event-driven design
-   Serverless compute
-   Low-latency data access
-   Separation of raw and processed data layers
-   Cost efficiency
-   Scalable infrastructure patterns

------------------------------------------------------------------------

# Business Problem

Manual stock monitoring and spreadsheet-based analysis are inefficient
and non-scalable.\
Real-time market awareness requires:

-   Continuous ingestion of price data
-   Fast anomaly detection
-   Trend identification
-   Automated alerting
-   Historical data retention for analytics

This project demonstrates how to implement such a system using AWS fully
managed services with minimal operational overhead.

------------------------------------------------------------------------

# Architecture Overview

## Data Flow

1.  Stock data fetched via `yfinance`
2.  Data streamed into Amazon Kinesis Data Streams
3.  Lambda processes streaming records
4.  Raw data archived in Amazon S3
5.  Structured data written to DynamoDB
6.  Athena queries historical stock data from S3
7.  DynamoDB Streams trigger trend-analysis Lambda
8.  SNS publishes alerts (Email/SMS)

------------------------------------------------------------------------

# AWS Services & Design Rationale

  -----------------------------------------------------------------------
  Service           Purpose           Architectural Value
  ----------------- ----------------- -----------------------------------
  Amazon Kinesis    Real-time         Decouples producers from consumers
                    ingestion         

  AWS Lambda        Event-driven      Eliminates server management
                    processing        

  Amazon DynamoDB   Low-latency       Optimized for time-series queries
                    storage           

  Amazon S3         Raw data lake     Durable, scalable storage layer

  Amazon Athena     Serverless SQL    Enables analytics without DB
                    querying          provisioning

  Amazon SNS        Alert             Event-driven notifications
                    distribution      

  IAM               Access control    Secure service interactions
  -----------------------------------------------------------------------

------------------------------------------------------------------------

# System Components

## 1️⃣ Streaming Layer -- Amazon Kinesis

Stock price data is streamed into:

    stock-market-stream

Design Considerations:

-   Partition key based on stock symbol
-   Configurable batch size for Lambda triggers
-   Scalable shard architecture

------------------------------------------------------------------------

## 2️⃣ Processing Layer -- AWS Lambda

Lambda performs:

-   Base64 decoding of Kinesis records
-   Raw JSON archival to S3
-   Price delta computation
-   Percentage movement calculation
-   Moving average computation
-   Anomaly detection (\>5% threshold)
-   Structured write to DynamoDB

------------------------------------------------------------------------

## 3️⃣ Structured Data Store -- DynamoDB

Table Name:

    stock-market-data

Primary Key Design:

-   Partition Key: symbol (String)
-   Sort Key: timestamp (String)

Design Benefits:

-   Efficient time-series querying
-   Scalable read/write throughput
-   Millisecond latency access

------------------------------------------------------------------------

## 4️⃣ Raw Data Lake -- Amazon S3

All incoming stock records are stored in:

    s3://stock-market-data-bucket/raw-data/{symbol}/{timestamp}.json

Benefits:

-   Long-term archival
-   Athena-compatible querying
-   Separation of raw vs processed layers

------------------------------------------------------------------------

## 5️⃣ Historical Analytics -- Amazon Athena

Glue Data Catalog Database:

    stock_data_db

Table:

    stock_data_table

Example Query:

``` sql
SELECT symbol, price, previous_close,
       (price - previous_close) AS price_change
FROM stock_data_table
ORDER BY price_change DESC
LIMIT 5;
```

------------------------------------------------------------------------

## 6️⃣ Trend Detection & Alerting -- DynamoDB Streams + SNS

Moving Average Strategy:

-   SMA-5 (Short-Term Average)
-   SMA-20 (Long-Term Average)

Trend Logic:

-   SMA-5 crosses above SMA-20 → Uptrend (Buy signal)
-   SMA-5 crosses below SMA-20 → Downtrend (Sell signal)

If crossover is detected:

-   SNS publishes alert
-   Email/SMS notification sent

------------------------------------------------------------------------

# Security & IAM Design

-   Separate IAM roles for streaming Lambda and trend-analysis Lambda
-   Principle of least privilege recommended
-   CloudWatch logging enabled

------------------------------------------------------------------------

# Cost Optimization Strategy

-   Serverless compute (no EC2)
-   On-demand DynamoDB
-   Athena pay-per-scan pricing
-   Controlled record streaming interval (30 seconds)
-   Designed lab cost: \~\$1--\$2

------------------------------------------------------------------------

# Limitations

-   Near real-time (30-second ingestion interval)
-   Not suitable for high-frequency trading
-   Simplified anomaly detection logic

------------------------------------------------------------------------

# Future Enhancements

-   CI/CD automation (GitHub Actions / CodePipeline)
-   Parquet storage optimization
-   Advanced ML-based anomaly detection
-   Real-time dashboard integration
-   Multi-symbol parallel streaming

------------------------------------------------------------------------

# Conclusion

This project demonstrates how to architect a scalable, cost-efficient,
near real-time analytics system using AWS managed services.

It showcases practical cloud engineering skills in streaming data
pipelines, event-driven compute, time-series storage, analytics
querying, and automated alerting.

------------------------------------------------------------------------

## 👤 Author

Tinotenda Maisiri\
Cloud & Data Systems Engineer
