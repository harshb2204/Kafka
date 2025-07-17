# Internal Architecture 

## Partitioning Logic

A producer can use a partition key to direct messages to a specific partition. A partition key can be any value that can be derived from the application context. A unique device ID or a user ID will make a good partition key. If a producer doesn't specify a partition key when producing a record, Kafka will use a round-robin partition assignment.

Messages with the same key are always written to the same partition, and Kafka ensures that consumers receive them in the order they were produced. However, if no partition key is used, the ordering of records can not be guaranteed within a given partition.

![Partitioning Logic](../Diagrams/partition.png)

## Reading Records from Partitions

Unlike the other pub/sub implementations, Kafka doesn't push messages to consumers. Instead, consumers have to pull messages off Kafka topic partitions. A consumer connects to a partition in a broker, reads the messages in the order in which they were written.

By remembering the offset of the last consumed message for each partition, a consumer can join a partition at the point in time they choose and resume from there. That is particularly useful for a consumer to resume reading after recovering from a crash.

But this may create a problem where multiple consumers instances of the same type read the record of a Kafka topic. To avoid this, Kafka has a concept called Consumer Groups.

## Consumer Groups

The consumer group concept ensures that a message is only ever read by a single consumer in the group.

When a consumer group consumes the partitions of a topic, Kafka makes sure that each partition is consumed by exactly one consumer in the group.

