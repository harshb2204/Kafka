
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
