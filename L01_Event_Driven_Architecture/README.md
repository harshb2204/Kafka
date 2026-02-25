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