## What is EDA( Event Driven Architecture)
Considered it as a system design style where: 
- Services do not call each other directly. 
- Instead, Services emit events and other services react to those events. 



### What is event?
- A fact that something happened in the past.

Its properties:

| Property | Description | Example |
| :--- | :--- | :--- |
| Immutable | Can not be changed once created | OrderCreated at 10:30PM |
| Past Tense | Describes what happened, not what to do | OrderCreated, Not Place Order |
| Self-Contained | Has all info needed to understand it. | Contains orderId, amount, timestamp. |

![](/Diagrams/restdistributedmicroservices.png)

### Problems with Synchronous Chain:

1. **Availability**: All services must be available at the same time. Any Service down, request will fail.
2. **Latency Accumulation**: Total Latency = Sum of all service latencies.
3. **Cascading Failure**: One slow service can bring down other services and entire flow.
4. **Tight Coupling**: In above example, Order Service knows about Payment, Inventory and Notification service. Their endpoint, how to invoke, their response structure etc.
5. **Scaling issue**: We can not scale one service independently based on load.


![](/Diagrams/eventdrivenarchitecture.png)

- We have an event router in this architecture(can be anyone).
- User places an order. Check inventory in real time, if present save Order and make status as pending then publish an event to the event router that the order is created. It then sends a response to the user that the order is created.
- Now the second part which doesnt have to be real time. The event router will push the 
OrderCreated event to all those who are interested. It will push to the paymentservice and 
the inventory service. 
- The payment service will process the payment and the inventory service will update the inventory. Once done they will also publish the event that their work is done. 
- Now in third part whoever is interested in the PaymentSuccess event will get pushed that event. Here the notification service will get that event and send an email to the user that the order is created and payment is successful. It will also send it to order service and update the status to completed.

### Advantages of Event Driven Architecture:

1. **Loose Couple**: No direct REST dependency.
2. **Better Scalability**: Services can scale independently.
3. **Better Resilience**: Temporary issue do not break the whole system.
4. **Replay**: We can re-process old events.
5. **Improvement in latency.**


### Components of EDA
![](/Diagrams/edacomponents.png)

### How do events move through EDA
1. Push Model
![](/Diagrams/pushmodel.png)

2. Pull Model
![](/Diagrams/pullmodel.png)

### EDA Models: Pub/Sub vs Streaming

#### Pub/Sub:
- Events are published to active consumers and then forgotten.
- If new consumer joined say tomorrow, it wont get yesterday messages.

**Used when:**
- We care about just delivery.
- Not about storing history.

**Example:**
- RabbitMQ Exchange

#### Streaming:
- Events are appended in logs forever or retention based.

**Used when:**
- We want replay.
- We want multiple consumer

If new consumer joined say tomorrow, it can read from latest offset or read from day1 (if retention allows)

**Example:**
- Kafka
