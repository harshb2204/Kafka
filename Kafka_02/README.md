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