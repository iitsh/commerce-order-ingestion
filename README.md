# Real-Time E-Commerce Order Ingestion Platform

A lightweight, real-time event-driven data pipeline built with Apache Kafka (KRaft mode) and Python. This project demonstrates transitioning a legacy batch-processed order system into an event-driven architecture to enable instant inventory reservation.

## Business Problem

A traditional e-commerce platform processed customer orders via nightly batch jobs. This caused a 24-hour delay in updating inventory systems, frequently leading to:

- Overselling of out-of-stock items during peak sales events
- Delayed order fulfillment due to batch job backlogs

### Solution

By introducing an event-driven architecture using Apache Kafka, order events published by the web application are made immediately available to downstream services, such as inventory management, with sub-second latency.

## System Architecture

```text
+-------------------+             +-----------------------+             +-----------------------+
|  Web Storefront   |             |     Apache Kafka      |             |   Inventory Service   |
|    (Producer)     | ========>   |   (Broker in KRaft)   | ========>   |      (Consumer)       |
|  order_producer   |  [Events]   |     Topic: orders     |  [Polls]    |  inventory_consumer   |
+-------------------+             +-----------------------+             +-----------------------+
                                        Partitions: 1
                                    Replication Factor: 1
```

## Tech Stack

- Streaming platform: Apache Kafka 7.5 (KRaft mode)
- Language: Python 3.10+
- Kafka client library: `confluent-kafka`
- Containerization: Docker and Docker Compose

## Key Kafka Concepts Demonstrated

- **Event producers**: asynchronous event generation using delivery callbacks to confirm message publication without blocking execution
- **Event consumers**: continuous message polling (`poll()`) and offset tracking managed via consumer groups
- **KRaft architecture**: running a modern Kafka cluster without ZooKeeper dependencies
- **At-least-once delivery semantics**: message delivery reliability ensured through producer acknowledgement handling (`acks`)
- **Decoupled architecture**: downstream consumers can go offline for maintenance without losing incoming web orders

## How to Run Locally

### Prerequisites

- [Docker Desktop](https://www.docker.com/products/docker-desktop/) installed and running
- [Python 3.10+](https://www.python.org/) installed

### 1. Clone the repository

```bash
git clone https://github.com/iitsh/commerce-order-ingestion.git
cd commerce-order-ingestion
```

### 2. Start the Kafka cluster

```bash
cd docker
docker compose up -d
cd ..
```

### 3. Install Python dependencies

```bash
pip install -r producer/requirements.txt
```

### 4. Run the pipeline

Open two separate terminal windows.

Terminal 1 (producer):

```bash
python producer/order_producer.py
```

Terminal 2 (consumer):

```bash
python consumer/inventory_consumer.py
```
