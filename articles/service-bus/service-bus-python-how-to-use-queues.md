<properties 
    pageTitle="如何將服務匯流排佇列搭配 Python 使用 | Microsoft Azure" 
    description="了解如何從 Python 使用 Azure 服務匯流排佇列。" 
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



# 如何使用服務匯流排佇列

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

本文說明如何使用服務匯流排佇列。 在 Python 中並使用這些範例 [Python Azure 封裝 []][]。 所涵蓋的案例包括**建立佇列、傳送並接收訊息**，以及**刪除佇列**。

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]
> [AZURE.NOTE] 若要安裝 Python 或 [Python Azure 封裝 []][], ，請參閱 [Python 安裝指南](../python-how-to-install.md)。

## 建立佇列

**ServiceBusService** 物件可讓您使用佇列。 將下列程式碼新增至您想要在其中以程式設計方式存取服務匯流排之任何 Python 檔案內的頂端附近：

```
from azure.servicebus import ServiceBusService, Message, Queue
```

下列程式碼將建立 **ServiceBusService** 物件。 取代 `mynamespace`, ，`sharedaccesskeyname`, ，和 `sharedaccesskey` 與您的命名空間、 共用的存取簽章 (SAS) 金鑰名稱和值。

```
bus_service = ServiceBusService(
    service_namespace='mynamespace',
    shared_access_key_name='sharedaccesskeyname',
    shared_access_key_value='sharedaccesskey')
```

SAS 金鑰名稱和值的值位於 [Azure 傳統入口網站 []][] 連接資訊，或在 Visual Studio **屬性** 窗格時 (如前一節所示)，伺服器總管] 中選取服務匯流排命名空間。

```
bus_service.create_queue('taskqueue')
```

**create_queue** also supports additional options, which enable you to override default queue settings such as message time to live (TTL) or maximum queue size. The following example sets the maximum queue size to 5GB, and the TTL value to 1 minute:
```
queue_options = Queue()
queue_options.max_size_in_megabytes = '5120'
queue_options.default_message_time_to_live = 'PT1M'

bus_service.create_queue('taskqueue', queue_options)
```

## Send messages to a queue

To send a message to a Service Bus queue, your application calls the **send\_queue\_message** method on the **ServiceBusService** object.

The following example demonstrates how to send a test message to the queue named *taskqueue using* **send\_queue\_message**:
```
msg = Message(b'Test Message')
bus_service.send_queue_message('taskqueue', msg)
```

Service Bus queues support a maximum message size of 256 KB (the header, which includes the standard and custom application properties, can have a maximum size of 64 KB). There is no limit on the number of messages held in a queue but there is a cap on the total size of the messages held by a queue. This queue size is defined at creation time, with an upper limit of 5 GB. For more information about quotas, see [Azure Queues and Service Bus queues][].

## Receive messages from a queue

Messages are received from a queue using the **receive\_queue\_message** method on the **ServiceBusService** object:
```
msg = bus_service.receive_queue_message ('taskqueue'，peek_lock = False)
print(msg.body)
```

Messages are deleted from the queue as they are read when the parameter **peek\_lock** is set to **False**. You can read (peek) and lock the message without deleting it from the queue by setting the parameter **peek\_lock** to **True**.

The behavior of reading and deleting the message as part of the receive operation is the simplest model, and works best for scenarios in which an application can tolerate not processing a message in the event of a failure. To understand this, consider a scenario in which the consumer issues the receive request and then crashes before processing it. Because Service Bus will have marked the message as being consumed, then when the application restarts and begins consuming messages again, it will have missed the message that was consumed prior to the crash.

If the **peek\_lock** parameter is set to **True**, the receive becomes a two stage operation, which makes it possible to support applications that cannot tolerate missing messages. When Service Bus receives a request, it finds the next message to be consumed, locks it to prevent other consumers receiving it, and then returns it to the application. After the application finishes processing the message (or stores it reliably for future processing), it completes the second stage of the receive process by calling the **delete** method on the **Message** object. The **delete** method will mark the message as being consumed and remove it from the queue.
```
msg = bus_service.receive_queue_message('taskqueue', peek_lock=True)
print(msg.body)

msg.delete()
```

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。 如果接收者應用程式因為某些原因無法處理訊息，它可以在 **Message** 物件上呼叫 **unlock** 方法。 這將導致服務匯流排將佇列中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與在佇列內鎖定之訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

如果應用程式在處理訊息之後，尚未呼叫 **delete** 方法時當機，則會在應用程式重新啟動時將訊息重新傳遞給該應用程式。 這通常稱為**至少處理一次**，也就是說，每個訊息至少會被處理一次，但在特定狀況下，可能會重新傳遞相同訊息。 如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。 通常您可使用訊息的 **MessageId** 屬性來達到此目的，該屬性將在各個傳遞嘗試中會保持不變。

## 後續步驟

了解基本的服務匯流排佇列之後，請參考下列連結以取得更多資訊。

-   請參閱 [佇列、 主題和訂用帳戶的 []][]。


[azure classic portal]: http://manage.windowsazure.com 
[python azure package]: https://pypi.python.org/pypi/azure 
[queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md 
[azure queues and service bus queues]: service-bus-azure-and-service-bus-queues-compared-contrasted.md#capacity-and-quotas 

