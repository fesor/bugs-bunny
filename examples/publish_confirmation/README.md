Publish Confirmation
========================

Scenario: at the end of the operation, we want to publish a message to RabbitMQ, and we want to make sure that it was delivered.

Naive solution would be:

```
$channel = $client->channel();

$channel->publish($message, [], 'my-exchange');
// end of script
```

First, there're no guarantees that the message will actually be delivered to the broker. Broker may also reject our message
due to disconnect from a cluster (when `pause-minority` option is chosen). Or simply because you reached the queue's length limit.
Even more, we want to make sure that message was persisted on disk before we may exit from the script.

We need [confirm select](https://rabbitmq.docs.pivotal.io/35/rabbit-web-docs/confirms.html) mode for a channel and track
delivery status.

**Note**: We are ignoring questions around unroutable messages for now. Check [this article](https://jack-vanlightly.com/blog/2017/3/12/how-to-deal-with-unroutable-messages-rabbitmq-publishing-part-3) if you want more details.

```php
$channel = $client->channel();
$channel->confirmSelect(
    /**
     * @param MethodBasicNackFrame|MethodBasicAckFrame $frame
     */
    function ($frame) {
        // frame will contain `$frame->deliveryTag` and may have `multiple` flag to be true
        // multiple flag would indicate that there are more messages available
        if ($frame instanceof MethodBasicNackFrame) {
          // Handle nack
        } else {
          // handle ack
        }
    }
);

$deliveryTag = $channel->publish($message, [], 'my-exchange');
```

References:

- [Confirms (aka Publisher Acknowledgements)](https://rabbitmq.docs.pivotal.io/35/rabbit-web-docs/confirms.html)
- [Types of Publishing Failures - RabbitMq Publishing Part 1](https://jack-vanlightly.com/blog/2017/3/10/rabbitmq-the-different-failures-on-basicpublish)
- [Sending Messages in Bulk and Tracking Delivery Status - RabbitMq Publishing Part 2](https://jack-vanlightly.com/blog/2017/3/11/sending-messages-in-bulk-and-tracking-delivery-status-rabbitmq-publishing-part-2)
