<properties 
    pageTitle="如何搭配使用服務匯流排主題與 Python | Microsoft Azure" 
    description="了解如何從 Python 使用 Azure 服務匯流排主題和訂閱。" 
    services="service-bus" 
    documentationCenter="python" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags 
    ms.service="service-bus" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="na" 
    ms.devlang="python" 
    ms.topic="article" 
    ms.date="10/08/2015" 
    ms.author="sethm"/>


# 如何使用服務匯流排主題和訂用帳戶

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本文說明如何使用服務匯流排主題和訂用帳戶。 在 Python 中並使用這些範例 [Python Azure 封裝 []][]。 所涵蓋的案例包括**建立主題和訂用帳戶**、**建立訂用帳戶篩選器**、**傳送訊息至主題**、**接收訂用帳戶的訊息**，及**刪除主題和訂用帳戶**。 如需主題和訂閱的詳細資訊，請參閱 [下一步](#next-steps) 一節。

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

**附註:** 如果您需要安裝 Python 或 [Python Azure 封裝 []][], ，請參閱 [Python 安裝指南](../python-how-to-install.md)。

## 建立主題

**ServiceBusService** 物件可讓您使用主題。 將下列內容新增至您想要在其中以程式設計方式存取服務匯流排之任何 Python 檔案內的頂端附近：

```
from azure.servicebus import ServiceBusService, Message, Topic, Rule, DEFAULT_RULE_NAME
```

下列程式碼將建立 **ServiceBusService** 物件。 取代 `mynamespace`, ，`sharedaccesskeyname`, ，和 `sharedaccesskey` 使用實際的命名空間、 共用存取簽章 (SAS) 金鑰名稱和索引鍵的值。

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

您可以取得 SAS 金鑰名稱的值，並從 [Azure 傳統入口網站 []][] **連接資訊** 視窗。

```
bus_service.create_topic('mytopic')
```

**create\_topic** also supports additional options, which enable you to override default topic settings such as message time to live or maximum topic size. The following example sets the maximum topic size to 5 GB, and a time to live (TTL) value of 1 minute:
```
topic_options = Topic()
topic_options.max_size_in_megabytes = '5120'
topic_options.default_message_time_to_live = 'PT1M'

bus_service.create_topic('mytopic', topic_options)
```

## Create subscriptions

Subscriptions to topics are also created with the **ServiceBusService** object. Subscriptions are named and can have an optional filter that restricts the set of messages delivered to the subscription's virtual queue.

> [AZURE.NOTE] Subscriptions are persistent and will continue to exist until either they, or the topic to which they are subscribed, are deleted.

### Create a subscription with the default (MatchAll) filter

The **MatchAll** filter is the default filter that is used if no filter is specified when a new subscription is created. When the **MatchAll** filter is used, all messages published to the topic are placed in the subscription's virtual queue. The following example creates a subscription named 'AllMessages' and uses the default **MatchAll**
filter.
```
bus_service.create_subscription('mytopic', 'AllMessages')
```

### Create subscriptions with filters

You can also define filters that enable you to specify which messages sent to a topic should show up within a specific topic subscription.

The most flexible type of filter supported by subscriptions is a **SqlFilter**, which implements a subset of SQL92. SQL filters operate on the properties of the messages that are published to the topic. For more information about the expressions that can be used with a SQL filter, see the [SqlFilter.SqlExpression][] syntax.

You can add filters to a subscription by using the **create\_rule** method of the **ServiceBusService** object. This method allows you to add new filters to an existing subscription.

> [AZURE.NOTE] Because the default filter is applied automatically to all new subscriptions, you must first remove the default filter or the **MatchAll** will override any other filters you may specify. You can remove the default rule by using the **delete\_rule** method of the **ServiceBusService** object.

The following example creates a subscription named `HighMessages` with a **SqlFilter** that only selects messages that have a custom **messagenumber** property greater than 3:
```
bus_service.create_subscription('mytopic', 'HighMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber > 3'

bus_service.create_rule('mytopic', 'HighMessages', 'HighMessageFilter', rule)
bus_service.delete_rule('mytopic', 'HighMessages', DEFAULT_RULE_NAME)
```

Similarly, the following example creates a subscription named `LowMessages` with a **SqlFilter** that only selects messages that have a **messagenumber** property less than or equal to 3:
```
bus_service.create_subscription('mytopic', 'LowMessages')

rule = Rule()
rule.filter_type = 'SqlFilter'
rule.filter_expression = 'messagenumber <= 3'

bus_service.create_rule('mytopic', 'LowMessages', 'LowMessageFilter', rule)
bus_service.delete_rule('mytopic', 'LowMessages', DEFAULT_RULE_NAME)
```

Now, when a message is sent to `mytopic` it is always delivered to receivers subscribed to the **AllMessages** topic subscription, and selectively delivered to receivers subscribed to the **HighMessages** and **LowMessages** topic subscriptions (depending on the message content).

## Send messages to a topic

To send a message to a Service Bus topic, your application must use the **send\_topic\_message** method of the **ServiceBusService** object.

The following example demonstrates how to send five test messages to `mytopic`. Note that the **messagenumber** property value of each message varies on the iteration of the loop (this determines which subscriptions receive it):
```
for i in range(5):
    msg = 訊息 ('Msg {0}'.format(i).encode('utf-8')、 custom_properties = {'messagenumber': 我})
    bus_service.send_topic_message('mytopic', msg)
```

Service Bus topics support a maximum message size of 256 MB (the header, which includes the standard and custom application properties, can have a maximum size of 64 MB). There is no limit on the number of messages held in a topic but there is a cap on the total size of the messages held by a topic. This topic size is defined at creation time, with an upper limit of 5 GB. For more information about quotas, see [Azure Queues and Service Bus queues][].

## Receive messages from a subscription

Messages are received from a subscription using the **receive\_subscription\_message** method on the **ServiceBusService** object:
```
msg = bus_service.receive_subscription_message ('mytopic'，'LowMessages'，peek_lock = False)
print(msg.body)
```

Messages are deleted from the subscription as they are read when the parameter **peek\_lock** is set to **False**. You can read (peek) and lock the message without deleting it from the queue by setting the parameter **peek\_lock** to **True**.

The behavior of reading and deleting the message as part of the receive operation is the simplest model, and works best for scenarios in which an application can tolerate not processing a message in the event of a failure. To understand this, consider a scenario in which the consumer issues the receive request and then crashes before processing it. Because Service Bus will have marked the message as being consumed, then when the application restarts and begins consuming messages again, it will have missed the message that was consumed prior to the crash.

If the **peek\_lock** parameter is set to **True**, the receive becomes a two stage operation, which makes it possible to support applications that cannot tolerate missing messages. When Service Bus receives a request, it finds the next message to be consumed, locks it to prevent other consumers receiving it, and then returns it to the application. After the application finishes processing the message (or stores it reliably for future processing), it completes the second stage of the receive process by calling **delete** method on the **Message** object. The **delete** method marks the message as being consumed and removes it from the subscription.
```
msg = bus_service.receive_subscription_message('mytopic', 'LowMessages', peek_lock=True)
print(msg.body)

msg.delete()
```

## How to handle application crashes and unreadable messages

Service Bus provides functionality to help you gracefully recover from errors in your application or difficulties processing a message. If a receiver application is unable to process the message for some reason, then it can call the **unlock** method on the **Message** object. This will cause Service Bus to unlock the message within the subscription and make it available to be received again, either by the same consuming application or by another consuming application.

There is also a timeout associated with a message locked within the subscription, and if the application fails to process the message before the lock timeout expires (for example, if the application crashes), then Service Bus unlocks the message automatically and makes it available to be received again.

In the event that the application crashes after processing the message but before the **delete** method is called, then the message will be redelivered to the application when it restarts. This is often called **At Least Once Processing**, that is, each message will be processed at least once but in certain situations the same message may be redelivered. If the scenario cannot tolerate duplicate processing, then application developers should add additional logic to their application to handle duplicate message delivery. This is often achieved using the **MessageId** property of the message, which will remain constant across delivery attempts.

## Delete topics and subscriptions

Topics and subscriptions are persistent, and must be explicitly deleted either through the [Azure classic portal][] or programmatically. The following example shows how to delete the topic named `mytopic`:
```
bus_service.delete_topic('mytopic')
```

Deleting a topic also deletes any subscriptions that are registered with the topic. Subscriptions can also be deleted independently. The following code shows how to delete a subscription named `HighMessages` from the `mytopic` topic:
```
bus_service.delete_subscription('mytopic', 'HighMessages')
```

## 後續步驟

了解基本的服務匯流排主題之後，請參考下列連結以取得更多資訊。

-   請參閱 [佇列、 主題和訂用帳戶的 []][]。
-   請參考 [[SqlFilter.SqlExpression]][]。


[azure classic portal]: http://manage.windowsazure.com 
[python azure package]: https://pypi.python.org/pypi/azure 
[queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md 
[sqlfilter.sqlexpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx 
[azure queues and service bus queues]: service-bus-azure-and-service-bus-queues-compared-contrasted.md#capacity-and-quotas 

