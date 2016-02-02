<properties
    pageTitle="搭配 .NET 使用服務匯流排主題 | Microsoft Azure"
    description="了解如何在 Azure 搭配 .NET 使用服務匯流排主題及訂用帳戶。程式碼範例專為 .NET 應用程式撰寫。"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="get-started-article"
    ms.date="10/15/2015"
    ms.author="sethm"/>


# 如何使用服務匯流排主題和訂用帳戶

[AZURE.INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

本文說明如何使用服務匯流排主題和訂用帳戶。 這些範例均以 C# 撰寫並使用 .NET API。 所涵蓋的案例包括建立主題和訂閱、建立訂閱篩選器、傳送訊息至主題、接收訂閱的訊息及刪除主題和訂閱。 如需主題和訂閱的詳細資訊，請參閱 [後續步驟](#Next-steps) 一節。

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

[AZURE.INCLUDE [howto-service-bus-topics](../../includes/howto-service-bus-topics.md)]

## 設定應用程式以使用服務匯流排

當您建立使用服務匯流排的應用程式時，您必須新增服務匯流排組件的參照，並包含對應的命名空間。

## 取得服務匯流排 NuGet 封裝

要取得服務匯流排 API，並對應用程式進行設定，以使用所有服務匯流排相依性的最簡單方法，便是使用服務匯流排 NuGet 封裝。 NuGet Visual Studio 擴充功能可讓您輕易地安裝和更新 Visual Studio 和 Visual Studio Express 中的程式庫和工具。 服務匯流排 NuGet 封裝是最簡單的方式
若要取得服務匯流排 API，並設定您的應用程式使用所有服務匯流排相依性。

若要在應用程式中安裝 NuGet 封裝，請執行下列動作：

1.  在 [方案總管] 中，以滑鼠右鍵按一下 **參考**, ，然後按一下
    **管理 NuGet 封裝**。
2.  搜尋 「 服務匯流排 」 並選取 * * Microsoft Azure
    服務匯流排 * * 項目。 按一下 [ **安裝** 以完成安裝，
    然後關閉下列對話方塊。

    ![][7]

您現在可以開始對服務匯流排撰寫程式碼。

## 設定服務匯流排連線字串

服務匯流排使用連接字串來儲存端點和認證。 您可以將連接字串置於設定檔中，而非硬式編碼它：

- 使用 Azure 雲端服務時，建議您使用 Azure 服務組態系統 (.csdef 和 .cscfg 檔案) 來儲存連接字串。
- 使用 Azure 網站或 Azure 虛擬機器時，建議您使用 .NET 組態系統 (例如 Web.config 檔案) 來儲存連接字串。

在這兩種情況下，您都可以使用 `CloudConfigurationManager.GetSetting` 方法擷取連接字串，如本文稍後所示範。

### 在使用雲端服務時設定連接字串

服務設定機制為 Azure 雲端服務專案所獨有，可讓您動態變更組態設定從 [Azure 傳統入口網站 []][] 不需重新部署您的應用程式。 例如，加入 `設定` 服務定義的標籤 (***.csdef**) 檔案中下一個範例所示。

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

接著您可以在服務組態 (.cscfg) 檔案中指定值。

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

### 在使用 Azure 網站或 Azure 虛擬機器時設定連接字串

使用網站或虛擬機器時，建議您使用 .NET 組態系統 (例如，Web.config)。您可以儲存連接字串使用 `< g s >` 項目。

```
<configuration>
    <appSettings>
        <add key="Microsoft.ServiceBus.ConnectionString"
             value="Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey" />
    </appSettings>
</configuration>
```

使用從 Azure 傳統入口網站擷取的 SAS 名稱和金鑰值，如上一節所述。

## 建立主題

您可以執行管理作業，服務匯流排主題和訂閱使用 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 類別。 此類別提供建立、列舉及刪除主題的方法。

下列範例會建構 `NamespaceManager` 物件使用的 Azure `CloudConfigurationManager` 類別
使用連接字串，其中包含服務匯流排服務命名空間和適當的基底位址
具備管理此連接字串權限的適當認證 SAS 認證與權限來加以管理。 這個連接字串的格式如下。

```
Endpoint=sb://<yourServiceNamespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey
```

Use the following example, given the configuration settings in the previous section.
```
如果它尚未存在，請建立主題。
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

There are overloads of the [CreateTopic](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createtopic.aspx) method that enable you to tune properties of the topic, for example, to set the default time-to-live (TTL) value to be applied to messages sent to the topic. These settings are applied by using the [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) class. The following example shows how to create a topic named **TestTopic** with a maximum size of 5 GB and a default message TTL of 1 minute.
```
設定主題設定。
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

使用自訂設定建立新的主題。
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [AZURE.NOTE] You can use the [TopicExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.topicexists.aspx) method on [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) objects to check whether a topic with a specified name already exists within a namespace.

## Create a subscription

You can also create topic subscriptions using the [`NamespaceManager`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) class. Subscriptions are named and can have an optional filter that
restricts the set of messages passed to the subscription's virtual queue.

### Create a subscription with the default (MatchAll) filter

The **MatchAll** filter is the default filter that is used if no filter is specified when a new subscription is created. When you use the **MatchAll** filter, all messages published to the topic are placed in the subscription's virtual queue. The following example creates a subscription named "AllMessages" and uses the default **MatchAll** filter.
```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.SubscriptionExists("TestTopic", "AllMessages"))
{
    namespaceManager.CreateSubscription("TestTopic", "AllMessages");
}
```

### Create subscriptions with filters

You can also set up filters that enable you to specify which messages sent to a topic should appear within a specific topic subscription.

The most flexible type of filter supported by subscriptions is the [SqlFilter][] class, which implements a subset of SQL92. SQL filters operate on the properties of the messages that are published to the topic. For more information about the expressions that can be used with a SQL filter, see the [SqlFilter.SqlExpression][] syntax.

The following example creates a subscription named **HighMessages** with a [SqlFilter][] object that only selects messages that have a custom **MessageNumber** property greater than 3.
```
建立"HighMessages"經篩選的訂閱。
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

Similarly, the following example creates a subscription named **LowMessages** with a [SqlFilter][] that only selects messages that have a **MessageNumber** property less than or equal to 3.
```
建立"LowMessages"經篩選的訂閱。
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

Now when a message is sent to `TestTopic`, it is always delivered to receivers subscribed to the **AllMessages** topic subscription, and selectively delivered to receivers subscribed to the **HighMessages** and **LowMessages** topic subscriptions (depending on the message content).

## Send messages to a topic

To send a message to a Service Bus topic, your application creates a [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) object using the connection string.

The following code demonstrates how to create a [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) object for the **TestTopic** topic created earlier using the [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx) API call.
```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

Messages sent to Service Bus topics are instances of the [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) class. [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) objects have a set of
standard properties (such as [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) and [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), a dictionary that is used to hold custom application-specific properties, and a body of arbitrary application data. An application can set the body of the message by passing any serializable object to the constructor of the [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) object, and the appropriate **DataContractSerializer** is then used to serialize the object. Alternatively, a **System.IO.Stream** can be provided.

The following example demonstrates how to send five test messages to the **TestTopic** [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) object obtained in the previous code example. Note that the [MessageNumber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) property value of each message varies depending on the iteration of the loop (this determines which subscriptions receive it).
```
for (int 我 = 0; 我 < 5; i + +)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  設定額外的自訂應用程式特定屬性。
  message.Properties["MessageNumber"] = i;

  傳送訊息至主題。
  Client.Send(message);
}
```

Service Bus topics support a [maximum message size of 256 KB](service-bus-quotas.md) (the header, which includes the standard and custom application properties, can have a maximum size of 64 KB). There is no limit on the number of messages held in a topic but there is a cap on the total size of the messages held by a topic. This topic size is defined at creation time, with an upper limit of 5 GB. If partitioning is enabled, the upper limit is higher. For more information, see [Partitioning Messaging Entities](https://msdn.microsoft.com/library/azure/dn520246.aspx).

## How to receive messages from a subscription

The recommended way to receive messages from a subscription is to use a [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) object. [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) objects can work in two different modes: [ReceiveAndDelete and PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx).

When using the **ReceiveAndDelete** mode, receive is a single-shot operation; that is, when Service Bus receives a read request for a message in a subscription, it marks the message as being consumed and returns it to the application. **ReceiveAndDelete** mode is the simplest model and works best for scenarios in which an application can tolerate not processing a message in the event of a failure. To understand this, consider a scenario in which the consumer issues the receive request and then crashes before processing it. Because Service Bus will have marked the message as consumed, when the application restarts and begins consuming messages again, it will have missed the message that was consumed prior to the crash.

In **PeekLock** mode (which is the default mode), the receive process becomes a two-stage operation which makes it possible to support applications that cannot tolerate missing messages. When Service Bus receives a request,
it finds the next message to be consumed, locks it to prevent other consumers receiving it, and then returns it to the application. After the application finishes processing the message (or stores it reliably for future processing), it completes the second stage of the receive process by calling [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) on the received message. When Service Bus sees the **Complete** call, it marks the message as being consumed and removes it from the subscription.

The following example demonstrates how messages can be received and processed using the default **PeekLock** mode. To specify a different [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) value, you can use another overload for [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx). This example uses the [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) callback to process messages as they arrive
into the **HighMessages** subscription.
```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

設定回撥選項。
OnMessageOptions 選項 = 新 OnMessageOptions();
選項。AutoComplete = false;
選項。AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) = >
{
    try
    {
        從訂用帳戶處理訊息。
        Console.WriteLine ("\n**高訊息**」)。
        Console.WriteLine (「 內文:"+ 訊息。GetBody<string>())。
        Console.WriteLine (「 訊息識別碼:"+ 訊息。訊息識別碼)。
        Console.WriteLine (」 訊息數目:"+
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }

}，選項)。
```

This example configures the [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) callback using an [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx) object. [AutoComplete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) is set to **false** to enable manual control of when to call [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) on the received message. [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) is set to 1 minute, which causes the client to wait for up to one minute for a message before the call times out and the client makes a new call to check for messages. This property value reduces the number of times the client makes chargeable calls that do not retrieve messages.

## How to handle application crashes and unreadable messages

Service Bus provides functionality to help you gracefully recover from errors in your application or difficulties processing a message. If a receiving application is unable to process the message for some reason, then it can call the [Abandon](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) method on the received message (instead
of the [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) method). This causes Service Bus to unlock the message within the subscription and make it available to be received again, either by the same consuming application or by another consuming application.

There is also a time-out associated with a message locked within the subscription, and if the application fails to process the message before the lock time-out expires (for example, if the application crashes), then Service Bus unlocks the message automatically and makes it available to be received again.

In the event that the application crashes after processing the message but before the [Complete](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) request is issued, the message will be redelivered to the application when it restarts. This is often called *At Least Once processing*; that is, each message is processed at least once but in certain situations the same message may be
redelivered. If the scenario cannot tolerate duplicate processing, then application developers should add additional logic to their application to handle duplicate message delivery. This is often achieved using the
[MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) property of the message, which will remain constant across delivery attempts.

## Delete topics and subscriptions

The following example demonstrates how to delete the topic **TestTopic** from the **HowToSample** service namespace.
```
刪除的主題。
namespaceManager.DeleteTopic("TestTopic");
```

Deleting a topic also deletes any subscriptions that are registered with the topic. Subscriptions can also be deleted independently. The following code demonstrates how to delete a subscription named **HighMessages** from the **TestTopic** topic.
```
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## 後續步驟

了解基本的服務匯流排主題和訂用帳戶之後，請參考下列連結以取得更多資訊。

-   請參閱 [佇列、 主題和訂用帳戶的 []][]。
-   API 參考 [SqlFilter []][]。
-   建置工作應用程式傳送和接收服務匯流排佇列的訊息: [服務匯流排代理傳訊.NET 教學課程 []][]。
-   服務匯流排範例: 從下載 [Azure 範例 []][] 或參閱 [概觀](service-bus-samples.md)。


[azure classic portal]: http://manage.windowsazure.com 
[7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png 
[queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md 
[sqlfilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx 
[sqlfilter.sqlexpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx 
[service bus brokered messaging .net tutorial]: service-bus-brokered-tutorial-dotnet.md 
[azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2 

