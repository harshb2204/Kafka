# Kafka Configuration Guide

## Kafka Cluster Setup and Testing

This guide covers the complete setup and testing process for a Kafka cluster using KRaft mode.

### 1. Cluster ID and Storage Formatting

First, you need to format the Kafka storage with a cluster ID:

```bash
# Your Kafka cluster ID
.\bin\windows\kafka-storage.bat random-uuid


# Format the storage with the cluster ID
.\bin\windows\kafka-storage.bat format -t <CLUSTER_ID> -c .\config\kraft\server.properties
```

### 2. Start Kafka Server

After formatting the storage, start the Kafka server:

```bash
.\bin\windows\kafka-server-start.bat .\config\kraft\server.properties
```

 **Server should start successfully**

### 3. Create and Test Topics

#### Create a Test Topic

In the same terminal (or a new one), create a test topic:

```bash
.\bin\windows\kafka-topics.bat --create --topic test-topic --bootstrap-server localhost:9092 --partitions 1 --replication-factor 1
```

**Expected Output:**
```
Created topic test-topic.
```

#### List Topics

Verify the topic was created successfully:

```bash
.\bin\windows\kafka-topics.bat --list --bootstrap-server localhost:9092
```

**Expected Output:**
```
test-topic
```

### 4. Send and Receive Messages

#### Start Consumer (Wait for Messages)

Open a new terminal and start the consumer:

```bash
.\bin\windows\kafka-console-consumer.bat --topic test-topic --from-beginning --bootstrap-server localhost:9092
```

#### Start Producer (Send Messages)

In another terminal, start the producer:

```bash
.\bin\windows\kafka-console-producer.bat --topic test-topic --bootstrap-server localhost:9092
```

Type a message:
```
hello kafka
```

**Result:** You'll immediately see the message appear in the consumer terminal.

### 5. Complete Testing Workflow

1. **Format Storage** → `kafka-storage.bat format`
2. **Start Server** → `kafka-server-start.bat`
3. **Create Topic** → `kafka-topics.bat --create`
4. **List Topics** → `kafka-topics.bat --list`
5. **Start Consumer** → `kafka-console-consumer.bat`
6. **Start Producer** → `kafka-console-producer.bat`
7. **Send Message** → Type message in producer
8. **Verify** → Message appears in consumer

### Important Notes

- **Cluster ID:** `cD26WP2hTpu-zREDTi7RIQ` (keep this for your cluster)
- **Bootstrap Server:** `localhost:9092`
- **Topic Name:** `test-topic`
- **Partitions:** 1
- **Replication Factor:** 1 (for single-node setup)

### Troubleshooting

- Ensure Kafka server is running before creating topics
- Use separate terminals for consumer and producer
- Check that port 9092 is not blocked by firewall
- Verify the cluster ID matches in your configuration


## Kafbat UI
java -Dspring.config.additional-location=file:C:\kafka\application-local.yml --add-opens java.rmi/javax.rmi.ssl=ALL-UNNAMED -jar C:\kafka\api-v1.3.0.jar

