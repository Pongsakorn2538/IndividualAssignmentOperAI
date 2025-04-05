# Apache Kafka Setup Documentation

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

1. **Prerequisites**
    - Download Java Development Kit (JDK)
        - Install JDK 8 or higher: Download from the Oracle website or use OpenJDK.
        - Set JAVA_HOME Environment Variable:
            - Right-click on This PC or My Computer and select Properties.
            - Click on Advanced system settings.
            - Click on Environment Variables.
            - Under System variables, click New:
                - Variable name: JAVA_HOME
                - Variable value: Path to your JDK installation (e.g., C:\ProgramFiles\Java\jdk-17.0.1)
            - Edit the Path variable:
                - Add %JAVA_HOME%\bin to the list.
    - Create kafka folder
        - Go to C:\
        - Create new folder and name it as "kafka"

2. **Download Apache Kafka**
    - Visit the [Apache Kafka Downloads page.](https://kafka.apache.org/downloads)
    - Download the latest binary release (e.g., kafka_2.13-4.0.0.tgz).
    - Extract the downloaded .tgz file by using 7-Zip or a similar tool.
    - Double-click into the extracted folder (e.g., folder name: kafka_2.13-4.0.0)
    - Double-click into the inside single folder (e.g., single folder name: kafka_2.13-4.0.0)
    - Move all the inside folder to the kafka directory created in step 1 (e.g., C:\kafka)

3. **Generate Cluster ID**
    - Open Command Prompt (CMD) in administrator mode by 
        - go to window
        - in search box type cmd
        - once cmd program appear, right click and click "Run as administrator"
    - Change current directory to C:\kafka by the following command:
        - cd C:\kafka
    - Generate Cluster ID by the following command:
        - ./bin/kafka-storage.sh random-uuid
    - Format Log Directories by the following command:
        - ./bin/kafka-storage.sh format -t <CLUSTER_ID> -c config/kraft/server.properties
        - replace CLUSTER_ID by the cluster id from the previous step 
        - e.g. ./bin/kafka-storage.sh format -t c9a5c1a2-d6f2-4c11-8f7f-7db0015e8ab3 -c config/kraft/server.properties

4. **Configuration on broker.properties**
    - Go to C:\kafka\config
    - Open file server.properties with text editor (name of the file will only server)
    - Change the file according to the following:
        - node.id=0
        - process.roles=broker,controller
        - log.dirs=C:/tmp/kraft-combined-logs
        - listeners=PLAINTEXT://localhost:9092,CONTROLLER://localhost:9093
        - advertised.listeners=PLAINTEXT://localhost:9092
        - controller.listener.names=CONTROLLER
        - inter.broker.listener.name=PLAINTEXT
        - controller.quorum.voters=0@localhost:9093
        - auto.create.topics.enable=true
        - num.network.threads=3
        - num.io.threads=8

5. **Create tmp folder for kraft-combined-logs**
    - Go to C:\
    - Create folder name tmp 

6. **Start Kafka in KRaft mode**
    - Open CMD in administrator mode
    - Open kafka in KRaft mode from the following command:
        - .\bin\windows\kafka-server-start.bat .\config\broker.properties
    - If you see "[KafkaServer id=0] started" on the CMD then it means the system fully running Kafka 4.0.0 in KRaft mode

7. **Create kafka Topic**
    - Open CMD in 
    - Create kafka topic from the following command:
        - .\bin\windows\kafka-topics.bat --create --topic <TopicName> --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
        - e.g. .\bin\windows\kafka-topics.bat --create --topic uci_air_quality_data --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
    
---
    
## Challenges

1. **Shutdown broker because all log dirs in C:/tmp/kraft-combined-logs have failed**
    - Challenge: This happends because Apache Kafka cannot write to or access the directory specified for storing log data
    - Resolutions: Make sure to open CMD in in administrator mode
2. **Kafka stops replicate message from producer.py**
    - Challenge: When using the previous version of Kafka (kafka_2.13-3.5.1), the producer.py can connect to the database and sending the message to kafka but kafka didn't replicate the message. As a result, comsumer.py cannot consume any message from kafka.
    - Resolutions: Upgraded to kafka_2.13-4.0.0 using KRaft mode (ZooKeeper-free), which resolved message replication and delivery issues, likely caused by outdated broker configurations in 3.5.1.
3. **Kafka Topic Mismatch**
    - Challenge: The producer was sending messages to a topic that had not been explicitly created beforehand. As a result, Kafka silently dropped the messages, and the consumer could not find or subscribe to the intended topic.
    - Resolution: add auto.create.topics.enable=true into broker.properties
4. **Python Kafka library does not recognize the Kafka broker version**
    - Challenge: The Python script using the kafka-python library attempted to connect to a Kafka broker running version 2.8.0 or higher. However, the client library did not recognize this version and raised an UnrecognizedBrokerVersion error because it lacked support for newer Kafka protocol versions.
    - Resolution: Use the Confluent Kafka client from the following step
        - Open CMD in administrator mode
        - Run the following command in CMD
            - pip install confluent-kafka 
        - in python file, import the confluent-kafka library from the following code:
            - from confluent_kafka import Producer
        - in python file, create the configuration and initialize the producer:
            - conf = {'bootstrap.servers': '127.0.0.1:9092'}
            - producer = Producer(conf)