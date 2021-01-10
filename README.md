Bugs Bunny
================

This repository contains a set of examples as well as the facade over Bunny to help
with reliable message delivery.

**Assumptions:**
- Both producer and consumer of messages are synchronous. Php is still not a good fit for the async workload.
- You have rabbitmq [cluster of 3+ nodes](https://www.rabbitmq.com/clustering.html#node-count) with [`pause-minority` policy](https://www.rabbitmq.com/partitions.html#automatic-handling)
- You rely on [competing consumers](https://docs.microsoft.com/en-us/azure/architecture/patterns/competing-consumers), and you don't care about the order in which messages are going to be processed
- Queue/Exchange bindings are static. i.e., there are no dynamic routing rules applied so we could avoid problems that there is [no consumer for a given routing key](https://www.rabbitmq.com/reliability.html#routing)
- Your application is running in Kubernetes

**What we need:**
- Reconnect and retry with back-off when a node is not available
- Ability to manually send heartbeat frame in case of a long-running task to keep the connection alive
- Store outbound messages in the buffer until the broker confirms delivery
- Retry publishing if broker rejected a message

## Examples

Examples rely on [toxiproxy](https://github.com/Shopify/toxiproxy). I was also lazy to provide PHP image.
To simplify infrastructure, only RabbitMQ runs in docker and network conditions simulated by toxiproxy,
which should be installed on your machine.

 - [WIP] [Publish Delivery Tracking](./examples/publish_confirmation/)

## Reference Materials

-  [Jack Vanlightly's blog](https://jack-vanlightly.com/blog/tag/RabbitMQ) contains a lot of information on reliability in messaging systems
