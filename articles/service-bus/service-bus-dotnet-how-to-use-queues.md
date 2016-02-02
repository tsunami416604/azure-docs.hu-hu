<properties
    pageTitle="如何使用服務匯流排佇列 (.NET) | Microsoft Azure"
    description="了解如何使用 Azure 中的服務匯流排佇列。程式碼範例是以 C# 撰寫並使用 .NET API。"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="10/07/2015"
    ms.author="sethm"/>


# 如何使用服務匯流排佇列

[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

本文說明如何使用服務匯流排佇列。 這些範例均以 C# 撰寫並使用 .NET API。 本文說明的案例包括建立佇列和傳送並接收訊息。 如需佇列的詳細資訊，請參閱 [後續步驟](#next-steps) 一節。

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## 設定應用程式以使用服務匯流排

當您建立使用服務匯流排的應用程式時，您必須新增服務匯流排組件的參照，並包含對應的命名空間。

## 新增服務匯流排 NuGet 封裝

要取得服務匯流排 API，並對應用程式進行設定，以使用所有服務匯流排相依性的最簡單方法，便是使用服務匯流排 **NuGet** 封裝。 NuGet Visual Studio 擴充功能可讓您輕易地安裝和更新 Visual Studio 和 Visual Studio Express 中的程式庫和工具。 服務匯流排 NuGet 封裝是最簡單的方式
若要取得服務匯流排 API，並設定您的應用程式使用所有服務匯流排相依性。

若要在應用程式中安裝 NuGet 封裝，請執行下列動作：

1.  在 [方案總管] 中，以滑鼠右鍵按一下 [喜好設定]****，然後按一下 [Manage NuGet Packages]****。
2.  搜尋「服務匯流排」並選取 [Microsoft Azure 服務匯流排]**** 項目。 按一下 [安裝]**** 完成安裝作業，然後關閉此對話方塊。

    ![][7]

您現在可以開始對服務匯流排撰寫程式碼。

## 設定服務匯流排連線字串

服務匯流排使用連接字串來儲存端點和認證。 您可以將連接字串置於設定檔中，而非硬式編碼它：

- 使用 Azure 雲端服務時，建議您使用 Azure 服務組態系統 (.csdef 和 .cscfg 檔案) 來儲存連接字串。
- 使用 Azure 網站或 Azure 虛擬機器時，建議您使用 .NET 組態系統 (例如 Web.config 檔案) 來儲存連接字串。

在這兩種情況下，您可以擷取連接字串使用 [CloudConfigurationManager.GetSetting][getsetting] 方法，如本文稍後所示。

### 在使用雲端服務時設定連接字串

服務設定機制為 Azure 雲端服務專案所獨有，可讓您動態變更組態設定從 [Azure 傳統入口網站 []][] 不需重新部署您的應用程式。 例如，加入 `設定` 標示您的服務定義 (.csdef) 檔案中下一個範例所示。

```
<ServiceDefinition name="Azure1">
...
    <WebRole name="MyRole" vmsize="Small">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
...
</ServiceDefinition>
```
接著您可以在服務組態 (.cscfg) 檔案中指定值，如下個範例所示。

```
<ServiceConfiguration serviceName="Azure1">
...
    <Role name="MyRole">
        <ConfigurationSettings>
            <Setting name="Microsoft.ServiceBus.ConnectionString"
                     value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
        </ConfigurationSettings>
    </Role>
...
</ServiceConfiguration>
```

使用從 Azure 傳統入口網站擷取的共用存取簽章 (SAS) 金鑰名稱和金鑰值，如上一節所述。

### 在使用網站或 Azure 虛擬機器時設定連接字串

使用網站或虛擬機器時，建議您使用 .NET 組態系統 (例如，**Web.config**)。您可以儲存連接字串使用 `< g s >` 項目。

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

使用從 Azure 傳統入口網站擷取的 SAS 名稱和金鑰值，如上一節所述。

## 建立佇列

您可以執行管理作業使用的服務匯流排佇列的 [NamespaceManager []][] 類別。 此類別提供建立、列舉及刪除佇列的方法。

這個範例會建構 [NamespaceManager []][] 物件使用的 Azure [CloudConfigurationManager []][] 類別與連接字串，其中包含基底位址的服務匯流排服務命名空間和適當的 SAS 認證與權限加以管理。 這是下列範例所示表單的連接字串。

````
Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey
````

Use the following example, given the configuration settings in the previous section.
````
如果它尚未存在，請建立佇列。
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue("TestQueue");
}
```

There are overloads of the [CreateQueue](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createqueue.aspx) method that enable you to tune properties
of the queue (for example, to set the default time-to-live (TTL) value to be applied to messages sent to the queue). These settings are applied using the [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx) class. The following example shows how to create a queue named `TestQueue` with a maximum size of 5 GB and a default message TTL of 1 minute.
```
配置佇列設定。
QueueDescription qd = new QueueDescription("TestQueue");
qd.MaxSizeInMegabytes = 5120;
qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

使用自訂設定建立新的佇列。
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue(qd);
}
```

> [AZURE.NOTE] You can use the [QueueExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.queueexists.aspx) method on [NamespaceManager][] objects to check if a queue with a specified name already exists within a service namespace.

## Send messages to a queue

To send a message to a Service Bus queue, your application creates a
[QueueClient][] object using the connection string.

The following code demonstrates how to create a [QueueClient][] object for the `TestQueue` queue you just created using the [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx) API call.
```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

Client.Send(new BrokeredMessage());
```

Messages sent to, and received from Service Bus queues are instances of the [BrokeredMessage][] class. [BrokeredMessage][] objects have a set of standard properties (such as [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) and [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), a dictionary
that is used to hold custom application specific properties, and a body of arbitrary application data. An application can set the body of the message by passing any serializable object into the constructor of the[BrokeredMessage][] object, and the appropriate **DataContractSerializer** is then used to serialize the object. Alternatively, you can provide a **System.IO.Stream** object.

The following example demonstrates how to send five test messages to the `TestQueue` [QueueClient][] object obtained in the previous code example.
```
for (int 我 = 0; 我 < 5; i + +)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  設定一些額外的自訂應用程式特定屬性。
  message.Properties["TestProperty"] = "TestValue";
  message.Properties["Message number"] = i;

  傳送訊息至佇列。
  Client.Send(message);
}
```

Service Bus queues support a [maximum message size of 256 Kb](service-bus-quotas.md) (the header, which includes  the standard and custom application properties, can have a maximum size of 64 KB). There is no limit on the number of messages held in a queue but there is a cap on the total size of the messages held by a queue. This queue size is defined at creation time, with an upper limit of 5 GB. If partitioning is enabled, the upper limit is higher. For more information, see [Partitioning Messaging Entities](service-bus-partitioning.md).

## How to receive messages from a queue

The recommended way to receive messages from a queue is to use a [QueueClient][] object. [QueueClient][] objects can work in two different modes: [ReceiveAndDelete and PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

When using the **ReceiveAndDelete** mode, the receive is a single-shot operation - that is, when Service Bus receives a read request for a message in a queue, it marks the message as consumed, and returns it to the application. **ReceiveAndDelete** is the simplest model and works best for scenarios in which an application can tolerate not processing a message in the event of a failure. To understand this, consider a scenario in which the consumer issues the receive request and then crashes before processing it. Because Service Bus will have marked
the message as consumed, when the application restarts and begins consuming messages again, it will have missed the message that was consumed prior to the crash.

In **PeekLock** mode (which is the default mode), the receive becomes a two-stage operation, which makes it possible to support applications that cannot tolerate missing messages. When Service Bus receives a request,
it finds the next message to be consumed, locks it to prevent other consumers receiving it, and then returns it to the application. After the application finishes processing the message (or stores it reliably for future processing), it completes the second stage of the receive process by calling [Complete][] on the received message. When Service Bus sees the [Complete][] call, it marks the message as consumed, and removes it from the queue.

The following example demonstrates how messages can be received and processed using the default **PeekLock** mode. To specify a different [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) value, you can use another overload of
[CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx). This example uses the [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) callback
to process messages as they arrive into `TestQueue`.
```
string connectionString =
  CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
QueueClient Client =
  QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

設定回撥選項。
OnMessageOptions 選項 = 新 OnMessageOptions();
選項。AutoComplete = false;
選項。AutoRenewTimeout = TimeSpan.FromMinutes(1);

處理收到的訊息的回呼。
Client.OnMessage((message) = >
{
    try
    {
        從佇列處理訊息。
        Console.WriteLine (「 內文:"+ 訊息。GetBody<string>())。
        Console.WriteLine (「 訊息識別碼:"+ 訊息。訊息識別碼)。
        Console.WriteLine (「 測試屬性:"+
        message.Properties["TestProperty"]);

        // Remove message from queue.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in queue.
        message.Abandon();
    }

}，選項)。
```

這個範例會設定 [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) 回呼使用 [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx) 物件。 [自動完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx)
設定為 **false** 啟用手動控制何時要呼叫 [完成 []][] 所接收訊息。 [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) 設為 1 分鐘，讓用戶端呼叫逾時和用戶端建立新的呼叫，以檢查訊息之前，等候一分鐘的訊息。 這個屬性值會減少用戶端無法擷取訊息所出現的收費呼叫次數。

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。 如果接收者應用程式無法處理訊息，因為某種原因，則它可以呼叫 [放棄](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) 所接收訊息的方法 (而不是 [完成 []][] 方法)。 這將導致服務匯流排將佇列中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與佇列內鎖定訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

該應用程式當機之前處理訊息之後 [完成 []][] 發出要求，將訊息傳遞給應用程式重新啟動時。 這通常稱為**至少處理一次**；也就是說，每個訊息至少會被處理一次，但在特定狀況下，可能會重新傳遞相同訊息。 如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。 這通常您可使用 [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) 訊息，各個傳遞嘗試保持不變的屬性。

## 後續步驟

了解基本的服務匯流排佇列之後，請參考下列連結以取得更多資訊。

-   了解服務匯流排訊息中的實體 [佇列、 主題和訂用帳戶的 []][]。
-   建置工作應用程式傳送和接收訊息的服務匯流排佇列 [服務匯流排代理傳訊.NET 教學課程 []][]。
-   下載服務匯流排範例從 [Azure 範例 []][] 或參閱 [服務匯流排的概觀範例 []][]。


[azure classic portal]: http://manage.windowsazure.com 
[7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png 
[queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md 
[service bus brokered messaging .net tutorial]: service-bus-brokered-tutorial-dotnet.md 
[azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2 
[overview of service bus samples]: service-bus-samples.md 
[getsetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx 
[cloudconfigurationmanager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager 
[namespacemanager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx 
[brokeredmessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx 
[queueclient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx 
[complete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx 

