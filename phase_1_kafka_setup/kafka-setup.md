# Apache Kafka Setup Documentation (KRaft Mode)

## Introduction

This document outlines the steps taken to set up Apache Kafka version 4.0.0 in **KRaft mode (no ZooKeeper)**. It also includes key configuration details, encountered challenges and resolutions.

---

## Environment Details

- **Kafka Version:** 4.0.0
- **Mode:** KRaft (ZooKeeper-less)
- **OS:** Windows 11 Pro
- **Java Version:** OpenJDK 21

---

## Kafka Setup Process

1. **Download Apache Kafka**
   ```bash
   wget https://downloads.apache.org/kafka/4.0.0/kafka_2.13-4.0.0.tgz
   tar -xzf kafka_2.13-4.0.0.tgz
   cd kafka_2.13-4.0.0



## Challenges