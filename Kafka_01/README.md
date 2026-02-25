
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