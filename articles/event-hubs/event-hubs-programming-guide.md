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

支援事件中樞的.NET 類別屬於 Microsoft.ServiceBus.dll 組件。 要參考服務匯流排 API，並設定應用程式以使用所有服務匯流排相依性，最簡單方法是下載服務匯流排 NuGet 封裝。 如需詳細資訊，請參閱 [使用 NuGet 服務匯流排封裝](https://msdn.microsoft.com/library/azure/dn741354.aspx)。 或者，您可以使用 [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) Visual Studio 中。 若要這樣做，請發出下列命令在 [Package Manager Console](http://docs.nuget.org/docs/start-here/using-the-package-manager-console) 視窗:

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

這個方法會使用 App.config 檔案之 `appSettings` 區段中的服務匯流排連線資訊。 如需 `appSettings` XML 用來儲存服務匯流排連接資訊，請參閱文件 [microsoft.servicebus.messaging.eventhubclient.create (system.string)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx) 方法。

另一個選項是從連接字串建立用戶端。 這個選項適合用來搭配 Azure 背景工作角色，因為您可以將字串儲存在背景工作的組態屬性中。 例如：

```
EventHubClient.CreateFromConnectionString("your_connection_string");
```

連接字串的格式與在前述方法之 App.config 檔案中的格式相同：

```
Endpoint=sb://[namespace].servicebus.windows.net/;SharedAccessKeyName=Manage;SharedAccessKey=[key]
```

最後，您也可建立 [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) 物件從 [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) 執行個體，如下列範例所示。

```
var factory = MessagingFactory.CreateFromConnectionString("your_connection_string");
var client = factory.CreateEventHubClient("MyEventHub");
```

請務必注意額外 [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) 從傳訊 factory 執行個體建立的物件將會重複使用相同的基礎 TCP 連線。 因此，這些物件對用戶端的輸送量將受到限制。  [建立](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.create.aspx) 方法會重複使用單一的傳訊處理站。 如果您需要極高輸送量的單一寄件者，則您可以建立多個訊息中心和一個 [EventHubClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx) 從每個傳訊處理站物件。

## 將事件傳送到事件中樞

將事件傳送至事件中心的建立 [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) 執行個體，並將它透過 [傳送](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.send.aspx) 方法。 這個方法會採用單一 [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) 例項的參數，並以同步方式傳送至事件中心。

## 事件序列化

 [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) 類別具有 [四個多載建構函式](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) ，有許多不同的參數，例如物件和序列化程式、 位元組陣列或資料流。 您也可具現化 [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) 類別，並在之後設定內文資料流。 使用 JSON 和 [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx), ，您可以使用 **encoding.utf8.getbytes ()** 擷取 JSON 編碼字串的位元組陣列。

## 資料分割索引鍵

 [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) 類別具有 [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) 屬性，可讓傳送者指定雜湊來產生資料分割指派的值。 使用資料分割索引鍵，可確保所有具有相同索引鍵的事件均傳送到事件中樞內的同一個資料分割。 常見的資料分割索引鍵包含使用者工作階段識別碼和唯一的傳送者識別碼。  [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx) 是選擇性屬性，可供使用時 [microsoft.servicebus.messaging.eventhubclient.send (microsoft.servicebus.messaging.eventdata)](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) 或 [Microsoft.servicebus.messaging.eventhubclient.sendasync](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) 方法。 如果您未提供值給 [PartitionKey](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.partitionkey.aspx), ，傳送的事件散佈至資料分割使用循環配置資源模型。

## 批次事件傳送作業

分批傳送事件能大幅增加輸送量。  [SendBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) 方法會採用 **IEnumerable** 型別的參數 [EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.aspx) ，並將整個批次成為不可部分完成的作業傳送至事件中心。

```
public void SendBatch(IEnumerable<EventData> eventDataList);
```

請務必注意，單一批次不能超過每個事件 256 KB 的限制。 此外，批次中的每個訊息都會使用相同的身分識別。 確保批次未超過最大事件大小是傳送者的責任。 如果是的話，用戶端 **傳送** 會產生錯誤。

## 以非同步方式傳送和大規模傳送

您也可以利用非同步方式將事件傳送到事件中樞。 以非同步方式傳送可以增加用戶端傳送事件的速率。 同時 [傳送](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.send.aspx) 和 [SendBatch](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.sendbatch.aspx) 中傳回的非同步版本的方法有 [工作](https://msdn.microsoft.com/library/system.threading.tasks.task.aspx) 物件。 雖然這項技術可以增加輸送量，不過如果實作不當，可能會造成用戶端在受到事件中樞服務節流的情況下繼續傳送事件，導致用戶端發生失敗或遺失訊息。 此外，您可以使用 [RetryPolicy](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.cliententity.retrypolicy.aspx) 控制用戶端重試選項在用戶端上的屬性。

## 建立資料分割的傳送者

雖然資料分割索引鍵是最常用來將事件傳送到事件中樞的方法，不過在某些情況下，您可能會想要將事件直接傳送到指定資料分割。 例如：

```
var partitionedSender = client.CreatePartitionedSender(description.PartitionIds[0]);
```

[CreatePartitionedSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.createpartitionedsender.aspx) 傳回 [EventHubSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubsender.aspx) 物件，您可以用來將事件發行至指定的事件中心分割。

## 事件取用者

事件中樞有兩個主要的事件取用模型: 直接接收者和較高層級的抽象，例如 [EventProcessorHost](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost.aspx)。 直接接收者負責自行協調消費者群組內之資料分割的存取。

### 直接消費者

讀取取用者群組內的資料分割最直接的方式是使用 [EventHubReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubreceiver.aspx) 類別。 若要建立這個類別的執行個體，您必須使用的執行個體 [EventHubConsumerGroup](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubconsumergroup.aspx) 類別。 在以下範例中，您必須在建立消費者群組的接收者時指定資料分割識別碼。

```
EventHubConsumerGroup group = client.GetDefaultConsumerGroup();
var receiver = group.CreateReceiver(client.GetRuntimeInformation().PartitionIds[0]);
```

 [CreateReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubconsumergroup.createreceiver.aspx) 方法有數個多載，可加強控制所建立的讀取器。 這些方法包括將位移指定為字串或時間戳記，以及讓您指定要在傳回的資料流中包括指定位移，或是在位移之後開始。 建立接收者後，您可以開始接收有關傳回之物件的事件。  [接收](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubreceiver.receive.aspx) 方法有四個多載來控制接收操作參數，例如批次大小和等待時間。 若要增加消費者的輸送量，您可以使用這些方法的非同步版本。 例如：

```
bool receive = true;
string myOffset;
while(receive)
{
    var message = receiver.Receive();
    myOffset = message.Offset;
    string body = Encoding.UTF8.GetString(message.GetBytes());
    Console.WriteLine(String.Format("Received message offset: {0} \nbody: {1}", myOffset, body));
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

