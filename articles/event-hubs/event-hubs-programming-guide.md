<properties 
   pageTitle="Azure 事件中樞的程式設計指南 | Microsoft Azure"
   description="說明使用 Azure.NET SDK 設計 Azure 事件中樞。"
   services="event-hubs"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/30/2015"
   ms.author="sethm" />


# 事件中樞程式設計指南

本主題說明使用 Azure.NET SDK 設計 Azure 事件中樞的相關資訊。 它假設使用者對事件中樞已有初步了解。 概念概觀事件中樞，請參閱 [事件中樞概觀](event-hubs-overview.md)。

## 事件發佈者

將事件傳送到事件中樞可藉由使用 HTTP POST 或透過 AMQP 1.0 連線來完成。 使用選擇取決於應用的特定案例。 AMQP 1.0 連線是以服務匯流排中的代理連線形式計量，其較適合經常出現大量訊息且需要低延遲的案例，因為它們可提供持續的傳訊通道。

事件中心會建立和管理使用 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 類別。 當使用.NET managed Api 時，用於將資料發行至事件中心的主要建構是 [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) 和 [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) 類別。  [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) 類別提供的事件傳送到事件中心的 AMQP 通訊通道。  [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) 類別代表事件，用來將訊息發佈到事件中心。 這個類別包含主體、一些中繼資料，以及有關事件的標頭資訊。 其他屬性會加入到 [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx)物件通過事件中心。

## 開始使用

支援事件中樞的.NET 類別屬於 Microsoft.ServiceBus.dll 組件。 要參考服務匯流排 API，並設定應用程式以使用所有服務匯流排相依性，最簡單方法是下載服務匯流排 NuGet 封裝。 如需詳細資訊，請參閱 [使用 NuGet 服務匯流排封裝](https://msdn.microsoft.com/library/azure/dn741354.aspx)。 或者，您可以使用 [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) Visual Studio 中。 若要這樣做，請發出下列命令在 [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) 視窗:

```powershell
Install-Package WindowsAzure.ServiceBus
```

## 建立事件中心

您可以使用 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 類別來建立事件中心。 例如：

```c
var manager = new Microsoft.ServiceBus.NamespaceManager("mynamespace.servicebus.windows.net");
var description = manager.CreateEventHub("MyEventHub");
```

在大部分情況下，建議您使用 [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx) 方法，以避免服務重新啟動時產生例外狀況。 例如：

```
var description = manager.CreateEventHubIfNotExists("MyEventHub");
```

所有事件中樞建立作業，包括 [CreateEventHubIfNotExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createeventhubifnotexists.aspx), ，需要 **管理** 之命名空間的權限。 如果您想要限制發佈者或取用者應用程式的權限，可以在使用有限權限認證時，於實際執行程式碼中避免這些建立作業呼叫。

[EventHubDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubdescription.aspx) 類別包含有關事件中樞，包括授權規則、 訊息保留間隔、 資料分割識別碼、 狀態和路徑的詳細資料。 您可以使用這個類別來更新事件中樞上的中繼資料。

## 建立事件中樞用戶端

與事件中樞互動的主要類別是 [Microsoft.ServiceBus.Messaging.EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx)。 這個類別提供傳送者和接收者功能。 您可以具現化這個類別使用 [建立](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx) 方法，如下列範例所示。

```
var client = EventHubClient.Create(description.Path);
```

這個方法會使用服務匯流排連線資訊，在 App.config 檔案中，在 `appSettings` 一節。 如需 `appSettings` XML 用來儲存服務匯流排連接資訊，請參閱文件 [microsoft.servicebus.messaging.eventhubclient.create (system.string)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx) 方法。

另一個選項是從連接字串建立用戶端。 這個選項適合用來搭配 Azure 背景工作角色，因為您可以將字串儲存在背景工作的組態屬性中。 例如：

```
EventHubClient.CreateFromConnectionString("your_connection_string");
```

The connection string will be in the same format as it appears in the App.config file for the previous methods:
```
Endpoint=sb://[namespace].servicebus.windows.net/SharedAccessKeyName = 管理;SharedAccessKey = [金鑰]
```

Finally, it is also possible to create an [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) object from a [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) instance, as shown in the following example.
```
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory。CreateEventHubClient("MyEventHub")
```

It is important to note that additional [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) objects created from a messaging factory instance will reuse the same underlying TCP connection. Therefore, these objects have a client-side limit on throughput. The [Create](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx) method reuses a single messaging factory. If you need very high throughput from a single sender, then you can create multiple message factories and one [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) object from each messaging factory.

## Send events to an Event Hub

You send events to an Event Hub by creating an [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) instance and sending it via the [Send](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.send.aspx) method. This method takes a single [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) instance parameter and synchronously sends it to an Event Hub.

## Event serialization

The [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) class has [four overloaded constructors](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) that take a variety of parameters, such as an object and serializer, a byte array, or a stream. It is also possible to instantiate the [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) class and set the body stream afterwards. When using JSON with [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx), you can use **Encoding.UTF8.GetBytes()** to retrieve the byte array for a JSON-encoded string.

## Partition key

The [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) class has a [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) property that enables the sender to specify a value that is hashed to produce a partition assignment. Using a partition key ensures that all the events with the same key are sent to the same partition in the Event Hub. Common partition keys include user session IDs and unique sender IDs. The [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) property is optional and can be provided when using the [Microsoft.ServiceBus.Messaging.EventHubClient.Send(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) or [Microsoft.ServiceBus.Messaging.EventHubClient.SendAsync(Microsoft.ServiceBus.Messaging.EventData)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) methods. If you do not provide a value for [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx), sent events are distributed to partitions using a round-robin model.

## Batch event send operations

Sending events in batches can dramatically increase throughput. The [SendBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) method takes an **IEnumerable** parameter of type [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) and sends the entire batch as an atomic operation to the Event Hub.
```
public void SendBatch (IEnumerable<EventData> eventDataList);
```

It is important to note that a single batch must not exceed the 256 KB limit of an event. Additionally, each message in the batch uses the same publisher identity. It is the responsibility of the sender to ensure that the batch does not exceed the maximum event size. If it does, a client **Send** error is generated.

## Send asynchronously and send at scale

You can also send events to an Event Hub asynchronously. Sending asynchronously can increase the rate at which a client is able to send events. Both the [Send](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.send.aspx) and [SendBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) methods are available in asynchronous versions that return a [Task](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) object. While this technique can increase throughput, it can also cause the client to continue to send events even while it is being throttled by the Event Hubs service and can result in the client experiencing failures or lost messages if not properly implemented. In addition, you can use the [RetryPolicy](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.cliententity.retrypolicy.aspx) property on the client to control client retry options.

## Create a partition sender

Although it is most common to send events to an Event Hub with a partition key, in some cases you might want to send events directly to a given partition. For example:
```
var partitionedSender = 用戶端。CreatePartitionedSender (的描述。PartitionIds[0])
```

[CreatePartitionedSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.createpartitionedsender.aspx) returns an [EventHubSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubsender.aspx) object that you can use to publish events to a specific Event Hub partition.

## Event consumers

Event Hubs has two primary models for event consumption: direct receivers and higher-level abstractions, such as [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx). Direct receivers are responsible for their own coordination of access to partitions within a consumer group.

### Direct consumer

The most direct way to read from a partition within a consumer group is to use the [EventHubReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubreceiver.aspx) class. To create an instance of this class, you must use an instance of the [EventHubConsumerGroup](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubconsumergroup.aspx) class. In the following example, the partition ID must be specified when creating the receiver for the consumer group.
```
EventHubConsumerGroup 群組 = 用戶端。GetDefaultConsumerGroup()
var 接收者 = 群組。CreateReceiver (用戶端。GetRuntimeInformation()。PartitionIds[0])
```

The [CreateReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubconsumergroup.createreceiver.aspx) method has several overloads that facilitate control over the reader being created. These methods include specifying an offset as either a string or timestamp, and the ability to specify whether to include this specified offset in the returned stream, or start after it. After you create the receiver, you can start receiving events on the returned object. The [Receive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubreceiver.receive.aspx) method has four overloads that control the receive operation parameters, such as batch size and wait time. You can use the asynchronous versions of these methods to increase the throughput of a consumer. For example:
```
bool 接收 = true;
字串 myOffset;
while(receive)
{
    var 訊息 = 收件者。Receive ();
    myOffset = 訊息。位移。
    字串主體 = Encoding.UTF8.GetString (訊息。GetBytes())
    Console.WriteLine (String.Format (「 已接收訊息位移: {0} \nbody: \{1\}"，myOffset，本文));
}
```

對特定資料分割來說，訊息的接收順序為傳送到事件中樞時的順序。 位移是一種字串語彙基元，它能用來識別資料分割中的訊息。

請務必注意，消費者群組內的單一資料分割一律不能擁有超過 5 個已連接的並行讀取器。 當讀取器連接或中斷連線時，其工作階段可能會維持作用中狀態達數分鐘之久，服務才能辨識出它們已經中斷連線。 在這段時間內，讀取器可能會無法重新連接資料分割。 寫入事件中樞直接接收者的完整範例，請參閱 [服務匯流排事件中樞直接接收者](https://code.msdn.microsoft.com/Event-Hub-Direct-Receivers-13fa95c6) 範例。

### 事件處理器主機

[EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) 類別處理來自事件中心資料。 在 .NET 平台上建置事件讀取器時，您應該使用這項實作。 [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) 提供事件處理器實作安全執行緒、 多處理序、 安全的執行階段環境，也提供檢查點和資料分割租用管理。

若要使用 [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) 類別時，您可以實作 [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx)。 這個介面包含三個方法：

- [OpenAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.openasync.aspx)

- [CloseAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.closeasync.aspx)

- [ProcessEventsAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.processeventsasync.aspx)

若要啟動的事件處理，具現化 [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx), ，為事件中樞提供適當的參數。 然後呼叫 [RegisterEventProcessorAsync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.registereventprocessorasync.aspx) 註冊您 [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx) 與執行階段實作。 此時，主機會嘗試使用「窮盡」演算法取得事件中樞內每個磁碟分割的租用。 這些租用將延續一段指定時間，然後必須更新。 本案例中，當新節點上線時，背景工作執行個體會保留租用，而每當取得更多租用的嘗試發生時，負載會隨著在節點之間轉移。

![事件處理器主機](./media/event-hubs-programming-guide/IC759863.png)

經過一段時間後，均衡的局面將會出現。 此動態功能可讓您將 CPU 架構自動調整套用至消費者，以便進行向上和向下調整。 由於事件中樞沒有直接的訊息計數概念，因此平均 CPU 使用率通常是測量後端或消費者規模最合適的機制。 如果發佈者發佈的事件數量開始超出消費者的處理能力，消費者上增加的 CPU 可用來引發背景工作執行個體計數自動調整。

[EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx) 類別也實作以 Azure 儲存體為基礎的檢查點機制。 這項機制能儲存每個磁碟分割的位移，方便各個消費者判斷前一個消費者的最後一個檢查點。 由於資料分割會透過租用在節點之間轉換，因此這是能促進負載移位的同步處理機制。

## 發佈者撤銷

除了進階執行階段功能的 [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx), ，事件中樞啟用發行者撤銷，以防止特定發行者將事件傳送到事件中心。 當發佈者的權杖遭到洩露，或軟體更新造成發佈者出現不當行為時，這些功能特別有用。 在這些情況下，您可以封鎖發佈者 SAS 權杖中的發佈者身分識別，避免它們發佈事件。

如需有關發行者撤銷，以及如何將傳送至事件中心為 「 發行者 」 的詳細資訊，請參閱 [服務匯流排事件中樞大規模安全發佈](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-99ce67ab) 範例。

## 後續步驟

若要深入了解事件中樞案例，請造訪下列連結：

- [事件中樞 API 概觀](event-hubs-api-overview.md)
- [事件中心概觀](event-hubs-overview.md)
- [事件中樞程式碼範例](http://code.msdn.microsoft.com/site/search?query=event 中樞 & f [0]。值 = 事件中樞與 f [0]。輸入 = SearchText ac = 5)
- [事件處理器主機 API 參考](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx)





