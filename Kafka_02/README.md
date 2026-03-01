## Consumer and Consumer Groups
![](/Diagrams/consumergroups1.png)

Rule:

1. In one consumer group: same partition is not read by multiple consumers.

   **Scenario 1: Consumers = Partitions**

   - **Topic**: `order-events` (3 partitions)
   - **Consumer Group**: `notification-service` (3 consumers)

   **Assignment**

   - Consumer 1 → Partition 0
   - Consumer 2 → Partition 1
   - Consumer 3 → Partition 2

   **Scenario 2: Consumers < Partitions**

   - **Topic**: `order-events` (6 partitions)
   - **Consumer Group 1**: `notification-service` (3 consumers)

   **Assignment**

   - Consumer 1 → Partition 0, Partition 3
   - Consumer 2 → Partition 1, Partition 4
   - Consumer 3 → Partition 2, Partition 5

   **Scenario 3: Consumers > Partitions**

   - **Topic**: `order-events` (3 partitions)
   - **Consumer Group 1**: `notification-service` (5 consumers)

   **Assignment**

   - Consumer 1 → Partition 0
   - Consumer 2 → Partition 1
   - Consumer 3 → Partition 2
   - Consumer 4 → IDLE (no partition)
   - Consumer 5 → IDLE (no partition)

2. Multiple consumer groups: same partition can be read by multiple consumers present in different groups.

   - **Topic**: `order-events`

   - **Consumer Group 1**: `notification-service`
     - Consumer 1 → Partition 0

   - **Consumer Group 2**: `analytics-service`
     - Consumer 2 → Partition 0

   - **Consumer Group 3**: `audit-service`
     - Consumer 3 → Partition 0

![](/Diagrams/consumergroups2.png)
![](/Diagrams/consumergroups3.png)
![](/Diagrams/consumergroups4.png)

## Committed offset strategy

This committed offset strategy helps when a consumer has crashed and a new consumer comes up.

1. The system finds out the partition number of topic `_consumer_offsets`.
2. Example: `hash(notification_group_id) % 50 = 23`.
3. The consumer asks the broker that holds Partition 23 of topic `_consumer_offsets`.
4. **Goal:** Till what point are events processed for e.g. TopicA–Partition2?
5. The broker internally maintains a key–value map from the `_consumer_offsets` metadata file.
   - **Key:** topic, partition  
   - **Value:** committed offset
6. The broker checks the map and returns the committed offset value.

## Offset commit strategies

### Strategy 1: auto-commit

`auto.commit.interval.ms=5000`

Means auto-commit every 5 seconds.

But this strategy is risky; consider this example:

- Time 0s: consumer polls, gets events 0–99  
- Time 1s: consumer processing events...  
- Time 5s: auto-commit triggers → commits offset 99  
- Time 6s: consumer crashes (only processed 50 events)  
- Time 7s: consumer restarts from offset 100  

Result: events 51–99 are **lost** (at-most-once delivery).

### Strategy 2: manual commit

1. Poll events 0–99  
2. Process **all** events  
3. Commit offset 99 (wait for confirmation)  
4. Kafka confirms commit  
5. Continue to the next batch  

If a crash happens:

- Before commit: events reprocessed (at-least-once)  
- After commit: events not reprocessed

## Kafka cluster

A Kafka cluster is a group of brokers working together to provide:

- **Scalability:** distribute load across multiple servers  
- **Fault tolerance:** continue operation even if brokers fail  
- **High availability:** no single point of failure

![](/Diagrams/kafkacluster.png)

## Leader–Follower partition

**Rule:** For each partition → one broker is the **Leader** and the others are **Followers**.

**Example**

- **Topic:** `order-events`
- **Partitions:** 3 (P0, P1, P2)
- **Replication factor:** 2

So for each partition there is one leader and one follower:

- **P0** → one leader, one follower  
- **P1** → one leader, one follower  
- **P2** → one leader, one follower  

All of these are distributed among the brokers.

**Leader responsibility**

- Handle all Producer writes  
- Handle all Consumer reads  
- Maintain partition logs  
- Coordinate with followers  


![](/Diagrams/kafkaleaderfollowercluster.png)

**Follower responsibility**

- Replicate data from the Leader  
- Stay in sync with the Leader  
- Ready to become Leader if the current leader fails  
- Do **not** serve client (Producer or Consumer) requests  



