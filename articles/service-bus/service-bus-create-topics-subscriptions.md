<properties 
   pageTitle="建立使用服務匯流排主題和訂用帳戶的應用程式 | Microsoft Azure"
   description="介紹服務匯流排主題和訂用帳戶所提供的發佈/訂閱功能。"
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

# 建立使用服務匯流排主題和訂用帳戶的應用程式

Azure 服務匯流排支援一套以雲端為基礎、訊息導向的中介軟體技術，包括可靠的訊息佇列和持久的發佈/訂閱訊息。 這篇文章是根據所提供的資訊 [建立應用程式使用服務匯流排佇列，](service-bus-create-queues.md) ，並提供服務匯流排主題所提供的發佈/訂閱功能的簡介。

## 不斷演變的零售案例

這篇文章會繼續使用中的零售案例 [建立使用服務匯流排佇列的應用程式](service-bus-create-queues.md)。 請回想一下先前提過的，個別銷售點 (POS) 終端機的銷售資料，必須路由傳送至庫存管理系統，讓系統使用該資料來判斷何時必須補充庫存。 每個 POS 終端機報告其銷售資料，藉由傳送訊息至 **DataCollectionQueue** 佇列，其中它們保留，直到接收到庫存管理系統，如下所示:

![Service-Bus1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

為了進一步演變此案例，我們已將新的要求加入至系統：商店老闆想要能夠即時監視商店的銷售業績。

為了滿足這項要求，系統必須「支開」銷售資料流。 我們還是需要 POS 終端機傳送的每則訊息像之前一樣，傳送至庫存管理系統，但我們想要每則訊息的另一個複本，以便對商店老闆呈現儀表板檢視。

在任何情況下，您需要每個訊息以供多個合作對象，您可以使用服務匯流排 *主題*。 主題提供發佈/訂閱模式，亦即每則發佈的訊息會提供給向主題註冊的一或多個訂用帳戶。 相較之下，佇列是由單一取用者收到每則訊息。

訊息傳送至主題的方式會與傳送至佇列的方式相同。 不過，訊息不是直接從主題處接收；而是從訂用帳戶接收的。 您可以將主題的訂用帳戶視為虛擬佇列，可接收已傳送到該主題的訊息複本。 從訂用帳戶接收訊息的方式與從佇列接收訊息的方式相同。

回到零售案例中，主題會取代佇列，而新增的訂用帳戶將由庫存管理系統元件使用。 系統現在看起來會像下面這樣：

![Service-Bus2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

這裡的組態會與先前以佇列為基礎的設計的運作方式完全相同。 也就是說，傳送至主題的訊息會路由傳送至 **清查** 訂用帳戶，從中 **庫存管理系統** 取用。

為了支援管理儀表板，我們在主題上建立了第二個訂用帳戶，如下所示：

![Service-Bus3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

使用此設定時，每個訊息 POS 終端機開放給兩者 **儀表板** 和 **清查** 訂閱。

## 示範程式碼

[建立使用服務匯流排佇列的應用程式](service-bus-create-queues.md) 說明如何註冊服務匯流排的帳戶，並建立服務命名空間。 若要使用服務匯流排命名空間，應用程式必須參考服務匯流排組件，也就是 Microsoft.ServiceBus.dll。 若要參考服務匯流排相依性最簡單的方式是安裝服務匯流排 [nuget](https://www.nuget.org/packages/WindowsAzure.ServiceBus/)。 您也可以在 Azure SDK 中找到此組件。 下載位於 [Azure SDK 下載頁面](http://azure.microsoft.com/downloads/)。

### 建立主題和訂用帳戶

服務匯流排訊息實體 (佇列和發佈/訂閱主題) 都是經由管理作業 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 類別。 需要適當的認證，才能建立 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 特定命名空間的執行個體。 服務匯流排會使用 [共用存取簽章 (SAS)](service-bus-sas-overview.md) 架構安全性模型。  [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) 類別代表安全性權杖提供者傳回部分知名權杖提供者的內建原廠方法。 我們將使用 [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) 方法來保存 SAS 認證。  [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 基底位址的服務匯流排命名空間和權杖提供者，然後建構執行個體。

 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 類別提供方法來建立、 列舉及刪除訊息實體。 如下所示的程式碼如何 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 建立並可用來建立執行個體 **DataCollectionTopic** 主題。

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
     
TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);
 
namespaceManager.CreateTopic("DataCollectionTopic");
```

請注意，有的多載 [Createqueue](https://msdn.microsoft.com/library/azure/hh293080.aspx) 方法可讓您設定主題的屬性。 例如，您可以為傳送到主題的訊息，設定預設的存留時間 (TTL) 值。 接下來，加入 **清查** 和 **儀表板** 訂閱。

```
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### 將訊息傳送到主題

服務匯流排實體，在執行階段作業例如，傳送和接收訊息，應用程式必須先建立 [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) 物件。 類似於 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 類別， [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) 從服務命名空間和權杖提供者的基底位址建立執行個體。

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

若要傳送訊息，以及收到來自服務匯流排主題，是執行個體 [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) 類別。 這個類別包含一組標準屬性 (例如 [標籤](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) 和 [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx))、 用來保存應用程式屬性的字典和一堆任意的應用程式資料。 應用程式可以設定任何可序列化物件傳入訊息內文 (下列範例會傳入 **SalesData** 物件，代表來自 POS 終端機的銷售資料)，它將用 [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) 來序列化物件。 或者， [資料流](https://msdn.microsoft.com/library/azure/system.io.stream.aspx) 可以提供物件。

```
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

將訊息傳送至主題的最簡單方式是使用 [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) 建立 [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx) 物件，以直接從 [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) 執行個體。

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### 自訂閱接收訊息

類似於使用的訂閱，您可以使用從接收訊息的佇列， [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) 您直接從建立物件 [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) 使用 [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx)。 您可以使用其中兩個不同接收模式 (**ReceiveAndDelete** 和 **PeekLock**) 中所述， [建立使用服務匯流排佇列的應用程式](service-bus-create-queues.md)。

請注意，當您建立 [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) 訂閱， *entityPath* 參數的形式是 `topicPath/subscriptions/subscriptionName`。 因此，若要建立 [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx) 的 **清查** 訂閱 **DataCollectionTopic** 主題， *entityPath* 必須設為 `DataCollectionTopic/subscriptions/Inventory`。 程式碼看起來會像下面這樣：

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionTopic/subscriptions/Inventory");
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

## 訂用帳戶篩選

到目前為止，本文中傳送至主題的所有訊息，都會提供給所有已註冊的訂用帳戶。 請注意「都會提供」這幾個字。 雖然服務匯流排訂用帳戶可看見所有傳送至主題的訊息，但您只能將部分訊息複製到虛擬訂用帳戶佇列。 這透過訂閱 *篩選*。 當您建立訂閱時，您可以提供篩選條件運算式中的訊息，這兩個系統屬性的屬性上運作的 SQL92 樣式述詞形式 (比方說， [標籤](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)) 和應用程式屬性，例如 **StoreName** 前一個範例中。

若要演變案例來說明這點，要將第二間商店加入我們的零售案例。 兩間商店的所有 POS 終端機的銷售資料，還是必須路由傳送至集中的庫存管理系統，但使用儀表板工具的店經理只注意到商店的銷售業績。 您可以用訂用帳戶篩選來達到此目的。 請注意，當 POS 終端機發佈訊息時，這些設定 **StoreName** 訊息上的應用程式屬性。 提供兩個存放區，例如 **Redmond** 和 **西雅圖**, ，POS 終端機 Redmond 存放區中的戳記其銷售資料訊息 **StoreName** 等於 **Redmond**, ，而西雅圖儲存 POS 終端機使用 **StoreName** 等於 **西雅圖**。 Redmond 商店的店經理只想要查看其 POS 終端機的資料。 系統看起來會像下面這樣：

![Service-Bus4](./media/service-bus-create-topics-subscriptions/IC657167.gif)

若要設定此路由傳送，您建立 **儀表板** 訂用帳戶，如下所示:

```
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

與此訂閱篩選條件，訊息之 **StoreName** 屬性設定為 **Redmond** 將複製的虛擬佇列 **儀表板** 訂用帳戶。 但還有更多其他訂用帳戶篩選。 除了能夠在訊息傳遞到訂用帳戶的虛擬佇列時修改訊息屬性外，應用程式還可以在每個訂用帳戶中擁有多個篩選規則。

## 摘要

所有使用中所述佇列的原因 [建立應用程式使用服務匯流排佇列，](service-bus-create-queues.md) 也可套用到主題，特別是:

- 暫時分離 – 訊息產生者和取用者不需要同時在線上。

- 負載調節 – 負載尖峰會由主題加以順暢化，讓取用應用程式針對平均負載 (而非尖峰負載) 佈建。

- 負載平衡 – 類似於佇列，您可以有多個競爭取用者接聽單一訂用帳戶，並將每則訊息遞交給其中一個取用者，進而平衡負載。

- 鬆散結合 – 您可以在不影響現有端點的情況下發展傳訊網路；例如，新增訂用帳戶或變更主題的篩選，以允許新的取用者。

## 後續步驟

請參閱 [建立應用程式使用服務匯流排主題和訂閱](service-bus-create-topics-subscriptions.md) 以了解如何使用 POS 零售案例中的佇列。
