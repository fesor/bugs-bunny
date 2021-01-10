Bugs Bunny
================

This repository contains set of examples as well as the facade over Bunny to help 
with reliable message delivery.

**Assumptions:**
 - Both producer and consumer of messages are synchronous. Php still not a good fit for async workload.
 - You have rabbitmq [cluster of 3+ nodes](https://www.rabbitmq.com/clustering.html#node-count) with [`pause-minority` policy](https://www.rabbitmq.com/partitions.html#automatic-handling)
 - You rely on [competing consumers](https://docs.microsoft.com/en-us/azure/architecture/patterns/competing-consumers) and you don't care about order in which messages are going to be processed
 - Your application is running in Kubernates

**What we need:**
 - Reconnect and retry with back-off when a node is not available
 - Ability to manually send heartbeat frame in case of long-running task to keep connection alive
 - Store outbound messages in buffer until broker confirms delivery
 - Retry publishing if broker rejected a message

## Examples

Examples rely on toxiproxy

