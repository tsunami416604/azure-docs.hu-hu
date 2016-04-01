<properties 
   pageTitle="撰寫使用服務匯流排佇列的應用程式 | Microsoft Azure"
   description="如何撰寫使用服務匯流排，且以佇列為基礎的簡單應用程式。"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/16/2015"
   ms.author="sethm" />

# 建立使用服務匯流排佇列的應用程式

本主題說明服務匯流排佇列，並示範如何撰寫使用服務匯流排、且以佇列為基礎的簡單應用程式。

請考量到零售業務的案例，其中個別銷售點 (POS) 終端機的銷售資料，必須路由傳送至庫存管理系統，讓系統使用該資料來判斷何時必須補充庫存。 這項解決方案使用服務匯流排傳訊，以在終端機與庫存管理系統之間通訊，如下圖所示：

![Service-Bus-Queues-Img1](./media/service-bus-create-queues/IC657161.gif)

每個 POS 終端機報告其銷售資料，藉由傳送訊息至 **DataCollectionQueue**。 這些訊息會保留在此佇列中，直到庫存管理系統擷取為止。 此模式通常稱為 *非同步傳訊*, ，因為 POS 終端機不需要等待回覆庫存管理系統進行處理。

## 為何使用佇列？

在我們查看建立此應用程式所需的程式碼之前，請考慮在此案例中使用佇列的優點，而不是讓 POS 終端機直接 (同步) 和庫存管理系統通訊。

### 暫時分離

使用非同步傳訊模式時，生產者和取用者不需要同時在線上。 傳訊基礎結構可確實儲存訊息，直到取用方準備接收這些訊息為止。 如此一來，分散式應用程式的元件即使主動中斷連線 (例如基於維護理由) 或由於元件損毀而意外中斷，也不會影響整個系統。 此外，取用方應用程式可能只需要在一天中的特定時間上線即可。 例如，在此零售案例中，庫存管理系統可能只需要在營業日結束後上線即可。

### 負載調節

在許多應用程式中，系統負載會隨時間改變，而處理每個工作單位所需的時間卻通常固定不變。 有佇列做為訊息生產者與取用者之間的中間者後，就只需佈建取用方應用程式 (背景工作) 來因應平均負載而非尖峰負載。 佇列的深度會隨著連入負載的改變而增加和縮短。 就處理應用程式負載所需的基礎結構數量而言，如此可直接節省金錢。

![Service-Bus-Queues-Img2](./media/service-bus-create-queues/IC657162.gif)

### 負載平衡

當負載增加時，可以新增更多的背景工作處理序，以讀取背景工作佇列中的訊息。 每個訊息僅由其中一個背景工作程序處理。 此外，這個提取型負載平衡機制，可讓背景工作電腦獲得最佳利用，即使背景工作電腦的處理能力有所不同也一樣，因為背景工作電腦將以自己的最大速率提取訊息。 此模式通常稱為競爭取用者 (英文)模式。

![Service-Bus-Queues-Img3](./media/service-bus-create-queues/IC657163.gif)

### 鬆散結合

使用訊息佇列，在訊息產生者與取用者之間居中協調，可提供元件之間的內建鬆散結合。 因為產生者和取用者不知道彼此的存在，所以取用者可以升級，而不會對產生者造成任何影響。 此外，傳訊拓撲可自行發展，且不會影響現有的端點。 當我們談到發佈/訂閱時，會討論更多這部分的內容。

## 示範程式碼

下一節會示範如何使用服務匯流排來建置此應用程式。

### 註冊服務匯流排帳戶和訂用帳戶

您需要 Azure 帳戶，才能開始使用服務匯流排。 如果您還沒有其中一個，您可以註冊免費試用版 [這裡](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A85619ABF)。

### 建立服務命名空間

當您有訂用帳戶後，便可建立新的命名空間。 您必須為所有服務匯流排帳戶的新命名空間指定唯一名稱。 每個命名空間會做為一組服務匯流排實體的容器。 如需詳細資訊，請參閱 [How To ︰ 建立或修改服務匯流排服務命名空間](https://msdn.microsoft.com/library/azure/hh690931.aspx)。

### 安裝 NuGet 封裝

若要使用服務匯流排服務命名空間，應用程式必須參考服務匯流排組件，也就是 Microsoft.ServiceBus.dll。 您可以找到此組件的 Microsoft Azure SDK 和下載位於 [Azure SDK 下載頁面](http://azure.microsoft.com/downloads/)。 但要取得服務匯流排 API，並對應用程式進行設定，以使用所有服務匯流排相依性的最簡單方法，便是使用服務匯流排 NuGet 封裝。 如需使用 NuGet 和服務匯流排套件的詳細資訊，請參閱 [使用 NuGet 服務匯流排封裝](https://msdn.microsoft.com/library/dn741354.aspx)。

### 建立佇列

服務匯流排訊息實體 （佇列和發佈/訂閱主題） 都是經由管理作業 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 類別。 服務匯流排會使用 [共用存取簽章 (SAS)](service-bus-sas-overview.md) 架構安全性模型。  [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) 類別代表安全性權杖提供者傳回部分知名權杖提供者的內建原廠方法。 我們將使用 [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) 方法來保存 SAS 認證。  [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 基底位址的服務匯流排命名空間和權杖提供者，然後建構執行個體。

 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 類別提供方法來建立、 列舉及刪除訊息實體。 如下所示的程式碼如何 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 建立並可用來建立執行個體 **DataCollectionQueue** 佇列。

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", 
                "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
 
TokenProvider tokenProvider = 
    TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = 
    new NamespaceManager(uri, tokenProvider);
namespaceManager.CreateQueue("DataCollectionQueue");
```

請注意，有的多載 [CreateQueue](https://msdn.microsoft.com/library/azure/hh322663.aspx) 啟用調整佇列的屬性的方法。 例如，您可以為傳送到佇列的訊息，設定預設的存留時間 (TTL) 值。

### 將訊息傳送到佇列

服務匯流排實體，在執行階段作業例如，傳送和接收訊息，應用程式必須先建立 [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) 物件。 類似於 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 類別， [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) 從服務命名空間和權杖提供者的基底位址建立執行個體。

```
 BrokeredMessage bm = new BrokeredMessage(salesData);
 bm.Label = "SalesReport";
 bm.Properties["StoreName"] = "Redmond";
 bm.Properties["MachineID"] = "POS_1";
```

訊息傳送給及接收來自服務匯流排佇列，是執行個體 [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) 類別。 這個類別包含一組標準屬性 (例如 [標籤](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) 和 [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx))、 用來保存應用程式屬性的字典和一堆任意的應用程式資料。 應用程式可以設定任何可序列化物件傳入訊息內文 (下列範例會傳入 **SalesData** 物件，代表來自 POS 終端機的銷售資料)，它將用 [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) 來序列化物件。 或者， [資料流](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) 可以提供物件。

最簡單的方式，將訊息傳送至指定的佇列，在我們的案例 **DataCollectionQueue**, ，是使用 [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) 建立 [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) 物件，以直接從 [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) 執行個體。

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### 從佇列接收訊息

從佇列接收訊息的最簡單方式是使用 [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) 物件可以直接從建立 [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) 使用 [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx)。 訊息接收者可以在兩個不同的模式下運作 ︰ **ReceiveAndDelete** 和 **PeekLock**。  [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) 建立訊息接收者時，做為參數設定 [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx) 呼叫。


當使用 **ReceiveAndDelete** 模式中，接收是一次性作業; 也就是服務匯流排收到要求時，它會標示為已取用的訊息並傳回應用程式。 **ReceiveAndDelete** 模式是最簡單的模型，且最適用於應用程式容許在發生失敗時不處理訊息的案例。 若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。 因為服務匯流排會將訊息標示為已取用，所以當應用程式重新啟動並開始再次取用訊息時，它將會遺漏當機前已取用的訊息。

在 **PeekLock** 模式中，接收會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。 當服務匯流排收到要求時，它會尋找要取用的下一則訊息、將其鎖定以防止其他取用者接收此訊息，然後將它傳回應用程式。 藉由呼叫應用程式完成處理訊息 （或可靠地儲存供未來處理） 之後，完成接收程序的第二個階段 [完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 所接收訊息。 當服務匯流排看到 [完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 呼叫時，它會將標示為已取用的訊息。

可能會有兩種不同的結果。 首先，如果應用程式無法處理訊息，因為某些原因，它可以呼叫 [放棄](https://msdn.microsoft.com/library/azure/hh181837.aspx) 所接收訊息 (而不是 [完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx))。 這會讓服務匯流排將訊息解除鎖定，讓相同的取用者或其他競爭取用者重新接收此訊息。 其次，鎖定有相關聯的逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會將訊息解除鎖定，並讓訊息可以被重新接收。

請注意，如果應用程式當機之後處理訊息前 [完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 發出要求，將訊息傳遞給應用程式重新啟動時。 這通常稱為*至少一次*處理。 也就是說，每則訊息至少會被處理一次，但在特定狀況下，可能會重新傳遞相同訊息。 如果案例無法容許重複處理，那應用程式中需要有其他邏輯才能偵測重複項目。 這可根據 [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) 訊息屬性。 這個屬性的值在各個傳遞嘗試上均會維持不變。 這稱為 「 *正好一次* 處理。

如下所示的程式碼會接收並處理訊息使用 **PeekLock** 模式中，這是預設值，如果沒有 [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) 明確提供值。

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionQueue");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

### 使用佇列用戶端

本節稍早建立的範例 [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) 和 [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) 物件直接從 [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) 來傳送和分別從佇列接收訊息。 替代方法是使用 [QueueClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx) 類別支援傳送和接收作業，除了更進階的功能，例如工作階段。

```
QueueClient queueClient = factory.CreateQueueClient("DataCollectionQueue");
queueClient.Send(bm);
            
BrokeredMessage message = queueClient.Receive();

try
{
    ProcessMessage(message);
    message.Complete();
}
catch (Exception e)
{
    message.Abandon();
} 
```

## 後續步驟

現在您已了解基本的佇列，請參閱 [建立應用程式使用服務匯流排主題和訂閱](service-bus-create-topics-subscriptions.md) 繼續討論使用發佈/訂閱功能的服務匯流排代理訊息。

