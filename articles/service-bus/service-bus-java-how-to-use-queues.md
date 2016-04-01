<properties
    pageTitle="如何搭配使用服務匯流排佇列與 Java | Microsoft Azure"
    description="了解如何使用 Azure 中的服務匯流排佇列。 程式碼範例以 Java 撰寫。"
    services="service-bus"
    documentationCenter="java"
    authors="sethmanheim"
    manager="timlt"
    />

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="10/07/2015"
    ms.author="sethm"/>

# 如何使用服務匯流排佇列

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

本文說明如何使用服務匯流排佇列。 這些範例
以 Java 撰寫並使用 [Azure SDK for Java][]。 Auch die Eigenschaften
涵蓋的案例包括 **建立佇列**, ，**傳送和接收
訊息**, ，和 **刪除佇列**。

[AZURE.INCLUDE [service-bus-java-how-to-create-queue](../../includes/service-bus-java-how-to-create-queue.md)]

## 設定應用程式以使用服務匯流排

請確定您已安裝 [Azure SDK for Java][] 再建置此範例。 如果您使用 Eclipse，您可以安裝 [Azure Toolkit for Eclipse][] 包含 Azure SDK for Java。 您可以加入 **Microsoft Azure Libraries for Java** 至您的專案 ︰

![](media/service-bus-java-how-to-use-queues/eclipselibs.png)

在 Java 檔案頂端新增下列 `import` 陳述式：

```
// Include the following imports to use Service Bus APIs
import com.microsoft.windowsazure.services.servicebus.*;
import com.microsoft.windowsazure.services.servicebus.models.*;
import com.microsoft.windowsazure.core.*;
import javax.xml.datatype.*;
```

## 建立佇列

服務匯流排佇列的管理作業可透過
**ServiceBusContract** 類別。 A **ServiceBusContract** 物件
可使用含有權杖權限加以管理的
SAS 權杖具有權限加以管理，而 **ServiceBusContract** 類別
唯一的通訊點。

 **ServiceBusService** 類別提供方法來建立、 列舉
和刪除佇列。 下列範例將說明如何 **ServiceBusService** 物件
可用來建立名為 「 HowToSample 」 的命名空間名稱為 「 TestQueue 」 的佇列 ︰

        Configuration config =
            ServiceBusConfiguration.configureWithSASAuthentication(
                    "HowToSample",
                    "RootManageSharedAccessKey",
                    "SAS_key_value",
                    ".servicebus.windows.net"
                    );

    ServiceBusContract service = ServiceBusService.create(config);
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    try
    {
        CreateQueueResult result = service.createQueue(queueInfo);
    }
    catch (ServiceException e)
    {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

有方法 **QueueInfo** 與佇列的屬性
調整 (例如 ︰ 若要設定的預設存留時間 (TTL) 值是
套用至訊息傳送至佇列。） 下列範例將示範如何
若要建立名為的佇列 `TestQueue` 5 GB 的大小上限 ︰

    long maxSizeInMegabytes = 5120;
    QueueInfo queueInfo = new QueueInfo("TestQueue");
    queueInfo.setMaxSizeInMegabytes(maxSizeInMegabytes);
    CreateQueueResult result = service.createQueue(queueInfo);

請注意，您可以使用 **listQueues** 方法 **ServiceBusContract**
若要檢查內是否已存在具有指定名稱的佇列物件
服務命名空間。

## 傳送訊息至佇列

若要傳送訊息至服務匯流排佇列，您的應用程式會取得
**ServiceBusContract** 物件。 下列程式碼說明如何傳送
訊息 `TestQueue` 中先前建立的佇列 `HowToSample` 命名空間 ︰

    try
    {
        BrokeredMessage message = new BrokeredMessage("MyMessage");
        service.sendQueueMessage("TestQueue", message);
    }
    catch (ServiceException e)
    {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

訊息傳送至，並收到來自服務匯流排佇列都是執行個體的 [BrokeredMessage][] 類別。 [BrokeredMessage][] 物件具有一組標準屬性 (例如 [標籤](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) 和 [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx))，字典
用來保存自訂應用程式特定的屬性，以及一堆任意的應用程式資料。 應用程式可以設定訊息本文，任何可序列化物件傳遞給建構函式的 [BrokeredMessage][], ，以及適當的序列化程式將會再用來序列化物件。 或者，您可以提供 **java。IO。InputStream** 物件。

下列範例說明如何將五個測試訊息傳送至上述程式碼片段中所取得的
`TestQueue` **MessageSender** 我們在上述程式碼片段中取得 ︰

    for (int i=0; i<5; i++)
    {
         // Create message, passing a string message for the body.
         BrokeredMessage message = new BrokeredMessage("Test message " + i);
         // Set an additional app-specific property.
         message.setProperty("MyProperty", i);
         // Send message to the queue
         service.sendQueueMessage("TestQueue", message);
    }

服務匯流排佇列支援 256 Kb 的訊息大小上限 (包含
標準和自訂應用程式屬性的標頭，可以容納
64 KB 的大小上限)。 主題中的訊息數目沒有限制，
但是佇列所保有的訊息總大小有
上限。 此佇列大小會在建立時定義，
上限是 5 GB。

## 從佇列接收訊息

從佇列接收訊息的主要方式是使用
**ServiceBusContract** 物件。 接收的訊息可在兩種不同的
不同的模式 ︰ **ReceiveAndDelete** 和 **PeekLock**。

當使用 **ReceiveAndDelete** 模式中，接收是一次性
作業；也就是說，當服務匯流排收到訊息的讀取要求時，
訊息在佇列中，它將訊息標示為已取用，並傳回
該應用程式。 **ReceiveAndDelete** 模式 （此為預設值
模式） 是最簡單的模型，最適合的案例
應用程式容許不處理訊息中的事件
發生失敗。 若要了解，請考慮案例取用者
發出接收要求，然後在處理之前當機。
因為服務匯流排將訊息標示為已取用，
當應用程式重新啟動並開始取用訊息，它
將會遺漏當機前已取用的訊息。

在 **PeekLock** 模式中，接收會變成兩階段作業，因此可以
可以支援無法容許遺漏訊息的應用
程式。 當服務匯流排收到要求時，它會尋找下一個要
要取用的訊息並加以鎖定，以免其他取用者接收該訊息，然後
將它傳回應用程式。 在應用程式完成
訊息處理 (或可靠地儲存此訊息以供未來處理) 之後，它可在接收的訊息上
呼叫以完成接收程序的第二個階段 **刪除**
方法，以完成接收程序的第二個階段。 當服務匯流排看到 **刪除** 呼叫時，它
會將標示為已取用的訊息，並從佇列中移除。

下列範例示範可以收到訊息的方式，
使用處理 **PeekLock** 模式 （不是預設模式）。 下列範例
下面一個無限迴圈，並處理訊息抵達
""TestQueue:

        try
    {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        opts.setReceiveMode(ReceiveMode.PEEK_LOCK);

        while(true)  {
             ReceiveQueueMessageResult resultQM =
                    service.receiveQueueMessage("TestQueue", opts);
            BrokeredMessage message = resultQM.getValue();
            if (message != null && message.getMessageId() != null)
            {
                System.out.println("MessageID: " + message.getMessageId());
                // Display the queue message.
                System.out.print("From queue: ");
                byte[] b = new byte[200];
                String s = null;
                int numRead = message.getBody().read(b);
                while (-1 != numRead)
                {
                    s = new String(b);
                    s = s.trim();
                    System.out.print(s);
                    numRead = message.getBody().read(b);
                }
                System.out.println();
                System.out.println("Custom Property: " +
                    message.getProperty("MyProperty"));
                // Remove message from queue.
                System.out.println("Deleting this message.");
                //service.deleteMessage(message);
            }  
            else  
            {
                System.out.println("Finishing up - no more messages.");
                break;
                // Added to handle no more messages.
                // Could instead wait for more messages to be added.
            }
        }
    }
    catch (ServiceException e) {
        System.out.print("ServiceException encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }
    catch (Exception e) {
        System.out.print("Generic exception encountered: ");
        System.out.println(e.getMessage());
        System.exit(-1);
    }

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，協助您從應用程式的
錯誤或處理訊息的問題中順利復原。 如果
接收者應用程式因故無法處理訊息，
它可以呼叫 **unlockMessage** 所接收訊息的方法
(而不是 **deleteMessage** 方法)。 這將導致服務匯流排
解除鎖定佇列中的訊息，讓它可以被
相同取用應用程式或其他取用應用程式
重新接收。

與佇列內鎖定訊息相關的還有逾時，
如果應用程式無法在鎖定逾時到期之前
處理訊息 (例如，如果應用程式當機)，則服務
匯流排會自動解除鎖定訊息，並讓訊息可以被
重新接收。

如果應用程式在處理訊息之後，
之前 **deleteMessage** 發出要求，然後將訊息
重新啟動時將訊息重新傳遞給該應用程式。 這通常
呼叫 **至少處理一次**, ，也就是將會是每個訊息
至少會被處理一次，但在特定狀況下，可能會重新傳遞相同
訊息。 如果案例無法容許重複處理，
則應用程式開發人員應將其他邏輯加入至其
應用程式來處理重複的訊息傳遞。 通常您可使用
使用 **getMessageId** 仍會對訊息的方法
將在各個傳遞嘗試中保持不變。

## 後續步驟

現在您已了解服務匯流排佇列的基本概念，請參閱 [佇列、 主題和訂閱][] 如需詳細資訊。

如需詳細資訊，請參閱 [Java 開發人員中心](/develop/java/)。


  [Azure SDK for Java]: http://azure.microsoft.com/develop/java/
  [Azure Toolkit for Eclipse]: https://msdn.microsoft.com/library/azure/hh694271.aspx
  [Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx


