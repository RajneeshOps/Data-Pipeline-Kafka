# Kafka-End-to-End-Project

## Introduction
In this Proof of Concept (PoC), we will demonstrate an end-to-end data engineering pipeline for real-time stock market data using **Apache Kafka**. The project uses a stock market app simulation to produce data in real-time, which is then consumed by an AWS-based ecosystem that stores, catalogs, and queries the data for further analysis.

This document walks through the technical implementation steps, from setting up Kafka on AWS EC2 instances to integrating with AWS Glue and Athena for querying the ingested data.

---

## Architecture Overview

The architecture for this project consists of the following components:

- **Stock Market Data Simulator (Producer)**: A Python-based stock market simulation app generates real-time stock market data and uses Kafka to produce this data.
- **Kafka (Message Broker)**: Kafka acts as the message broker running on an EC2 instance, which processes and stores real-time streams of data from the producer.
- **AWS (Consumers)**: The real-time stock data is consumed and stored in Amazon S3, cataloged with AWS Glue, and queried using Amazon Athena.

![Architecture Diagram](insert_link_to_architecture_diagram_here)

---

## Project Prerequisites

### Environment Setup
- **EC2 Instance (Ubuntu/ Amazon Linux)**: An AWS EC2 instance for running Kafka.
- **Java**: Ensure that Java is installed (required by Kafka).

    ```bash
    sudo apt install java-1.8.0-openjdk
    java -version
    ```

- **Apache Kafka**: Download and install Kafka on your EC2 instance.

    ```bash
    wget https://downloads.apache.org/kafka/3.3.1/kafka_2.12-3.3.1.tgz
    tar -xvf kafka_2.12-3.3.1.tgz
    cd kafka_2.12-3.3.1
    ```

---

## Kafka Setup

### Step 1: Starting ZooKeeper
Kafka requires ZooKeeper for managing its clusters. Start ZooKeeper with the following command:

```bash
bin/zookeeper-server-start.sh config/zookeeper.properties
```

### Step 2: Starting Kafka Broker
```bash
bin/kafka-server-start.sh config/server.properties
```

***Make sure to modify server.properties to point to your public EC2 IP for external access.***
```bash
sudo vi config/server.properties
# Modify 'ADVERTISED_LISTENERS' to the public IP of the EC2 instance
```

### Kafka Topic Creation
To create a topic for our stock market data, use the following command:
```bash
bin/kafka-topics.sh --create --topic stock_market_data --bootstrap-server {EC2_Public_IP:9092} --replication-factor 1 --partitions 1
```

### Producer Setup
```bash
# Command to start the producer
bin/kafka-console-producer.sh --topic stock_market_data --bootstrap-server {EC2_Public_IP:9092}
```

### Consumer Setup
```bash
bin/kafka-console-consumer.sh --topic stock_market_data --bootstrap-server {EC2_Public_IP:9092}
```

### AWS Integration for Data Processing
***Amazon S3 (Storage)***
The consumer will push the stock market data into an S3 bucket for further analysis. Data can be stored as CSV or Parquet format.

### AWS Glue (Data Cataloging)
Set up an AWS Glue crawler to scan the S3 bucket and catalog the stock market data. This enables us to query the data using AWS Athena.

### Amazon Athena (Query Engine)
Use Amazon Athena to query the stock market data stored in S3. With Glue providing the schema, Athena allows us to run SQL queries on the ingested real-time data.

### Conclusion
This PoC demonstrates the real-time data pipeline for stock market data using Kafka as the backbone for streaming data and AWS services for storage, cataloging, and querying. By leveraging Kafka, we can ensure high-throughput, fault-tolerant data streams, while AWS Glue and Athena enable scalable and serverless data analytics.
