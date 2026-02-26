
## What is Kafka?

It’s a distributed event streaming platform that allow us to: 
- Publish events 
- Store events 
- Subscribe to events 

### Producer
- Producer is an application that publishes events to Kafka. 
- Producer does not care who will consume this event. 
![](/Diagrams/producer.png)

### Topic
- Considered Topic as category or Folder to which events are published. 
- Many Producers can write to same topic. 

![](/Diagrams/topic.png)


### Partition
A Physical, ordered and append-only log that is part of a Topic. 
A topic can have many partitions. 
![](/Diagrams/partition2.png)
![](/Diagrams/partition3.png)

- Each partition maintains its *next offset counter*.
- When a new Event arrives:
    - Kafka assign next offset to that event.
    - And append the event to the log.
- Within a single partition- Order is guaranteed
    - Means events are written sequentially and read in increasing offset manner.
    - Kafka do not re-order the event in partition.
- But do not guarantees ORDER globally (across multiple partitions of the Topic)
    - For ex:
        - In Partition0 (P0) : we know that Event3 happened after Event0 because offset2 comes after offset1 in that partition.
        - But I can not guaranteed that, Event4 in Partition1 have happened after (or before) Event0 in Partition0. Because offset belongs to per partition.

![](/Diagrams/appendonlylog1.png)
![](/Diagrams/appendonlylog2.png)
![](/Diagrams/segments.png)

**Why segments?**
- A consumer might want to read from a specific range, e.g., Offset 550 to 800 in `Partition0`.
- If logs are divided into multiple small segment files instead of one giant file, searching becomes much faster.
- Kafka can directly jump to the relevant file, for example: `Segment2: 000500.log`.

**Index Files:**
- We can control segment size (e.g., 1GB), but sequential searching even within a 1GB file can be slow.
- To solve this, Kafka maintains an **index file** per segment to speed up lookups.
![](/Diagrams/indexfile.png)

- Not every offset is indexed.
- Instead, it indexes every N bytes (after N bytes are written to the log file, it creates one index row).
- This saves memory and enables faster lookups.

**Example:**
- **Partition0** of `Order-events`:
    - Offset0: 300 bytes
    - Offset1: 500 bytes
    - Offset2: 1000 bytes
    - Offset3: 2000 bytes
    - Offset4: 500 bytes
- **Segment size**: 1GB
- **Index Interval**: 4096 bytes (meaning an index record is created only after 4096 bytes of data are written to the log).
![](/Diagrams/segmentsandindex.png)

### Partitioning Algorithms
The producer can pick one of different algorithms to decide which partition of a topic an event should be sent to:

#### 1. Key-based Partitioning
All events for the same **KEY** will go to the same partition, which ensures that ordering is guaranteed for that key.
![](/Diagrams/keybasedpartitioning.png)

#### 2. Round-Robin Partitioning
Even distribution but ordering is not guaranteed.
Related events may go to different partitions.
![](/Diagrams/roundrobinpartitioning.png)

#### 3. Custom Partitioning
We can also write our own custom logic, for example:
- country is "India" -> go to Partition0
- country is "US" -> go to Partition1



### Broker
- A broker is single Kafka server instance.
- A Broker is the one which actually stores data and serves clients (Producer and Consumer).
- A Broker stores some partitions of some topics.

> [!IMPORTANT]
> **Topics and Partitions are distributed across multiple brokers.**
> This is why a single broker stores only some partitions of some topics.
> - It does not hold all topics.
> - It does not hold all partitions of a topic.
