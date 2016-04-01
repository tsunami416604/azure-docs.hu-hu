<properties
    pageTitle="搭配 .NET 使用服務匯流排主題 | Microsoft Azure"
    description="了解如何在 Azure 搭配 .NET 使用服務匯流排主題及訂用帳戶。 程式碼範例專為 .NET 應用程式撰寫。"
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
2.  搜尋 「 服務匯流排 」 並選取 **Microsoft Azure
    服務匯流排** 項目。 按一下 [ **安裝** 以完成安裝，
    然後關閉下列對話方塊。

    ![][7]

您現在可以開始對服務匯流排撰寫程式碼。

## 設定服務匯流排連線字串

服務匯流排使用連接字串來儲存端點和認證。 您可以將連接字串置於設定檔中，而非硬式編碼它：

- 使用 Azure 雲端服務時，建議您使用 Azure 服務組態系統 (.csdef 和 .cscfg 檔案) 來儲存連接字串。
- 使用 Azure 網站或 Azure 虛擬機器時，建議您使用 .NET 組態系統 (例如 Web.config 檔案) 來儲存連接字串。

在這兩種情況下，您都可以使用 `CloudConfigurationManager.GetSetting` 方法擷取連接字串，如本文稍後所示範。

### 在使用雲端服務時設定連接字串

服務設定機制為 Azure 雲端服務專案所獨有，可讓您動態變更組態設定從 [Azure 傳統入口網站][] 不需重新部署您的應用程式。 例如，加入 `Setting` 標籤到您的服務定義 (***.csdef**) 檔案中下一個範例所示。

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

使用網站或虛擬機器時，建議您使用 .NET 組態系統 (例如，Web.config)。 您可以使用 `<appSettings>` 元素儲存連接字串。

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

請使用下例，假設組態設定如上一節。

```
// Create the topic if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic("TestTopic");
}
```

有的多載 [Createqueue](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createtopic.aspx) 方法可讓您調整主題的屬性，例如，若要設定預設存留時間 (TTL) 值套用至傳送至主題的訊息。 這些設定會套用使用 [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx) 類別。 下列範例示範如何建立名為 **TestTopic** 最大值為 5 GB 和預設訊息 TTL 為 1 分鐘。

```
// Configure Topic Settings.
TopicDescription td = new TopicDescription("TestTopic");
td.MaxSizeInMegabytes = 5120;
td.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new Topic with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.TopicExists("TestTopic"))
{
    namespaceManager.CreateTopic(td);
}
```

> [AZURE.NOTE] 您可以使用 [TopicExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.topicexists.aspx) 方法 [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 物件來檢查命名空間內是否已存在具有指定名稱的主題。

## 建立訂閱

您也可以建立主題訂用帳戶使用 [`NamespaceManager`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx) 類別。 訂閱是具名的，它們能擁有選用的篩選器，
限制傳遞至訂閱虛擬佇列的訊息集合。

### 使用預設 (MatchAll) 篩選器建立訂閱

 **MatchAll** 篩選器是預設篩選器，如果沒有指定篩選時建立新的訂閱。 當您使用 **MatchAll** 篩選條件，所有發佈至主題的訊息都會被置於訂閱的虛擬佇列。 下列範例會建立名為"AllMessages"的訂閱，並使用預設 **MatchAll** 篩選器。

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

### 使用篩選器建立訂用帳戶

您也可以設定篩選器，讓您指定傳送至主題的哪些訊息應出現在特定主題訂用帳戶中。

訂閱所支援的篩選器的最具彈性的型別是 [SqlFilter][] 類別，實作 SQL92 的子集。 SQL 篩選器會對發佈至主題之訊息的屬性運作。 如需可與 SQL 篩選運算式的詳細資訊，請參閱 [SqlFilter.SqlExpression][] 語法。

下列範例會建立名為 **HighMessages** 與 [SqlFilter][] 物件，只選取自訂的訊息 **MessageNumber** 屬性大於 3。

```
// Create a "HighMessages" filtered subscription.
SqlFilter highMessagesFilter =
   new SqlFilter("MessageNumber > 3");

namespaceManager.CreateSubscription("TestTopic",
   "HighMessages",
   highMessagesFilter);
```

同樣地，下列範例會建立名為 **LowMessages** 與 [SqlFilter][] sqlfilter 只選取 **MessageNumber** 屬性小於或等於 3。

```
// Create a "LowMessages" filtered subscription.
SqlFilter lowMessagesFilter =
   new SqlFilter("MessageNumber <= 3");

namespaceManager.CreateSubscription("TestTopic",
   "LowMessages",
   lowMessagesFilter);
```

現在當訊息傳送至 `TestTopic`, ，它一定會傳遞到已訂閱 **AllMessages** 主題訂用帳戶，並選擇性地傳遞至已訂閱 **HighMessages** 和 **LowMessages** 主題訂閱的接收者 （視訊息內容而定）。

## 傳送訊息至主題

若要傳送訊息至服務匯流排主題，應用程式建立 [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) 物件使用的連接字串。

下列程式碼示範如何建立 [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) 物件 **TestTopic** 主題稍早使用建立 [`CreateFromConnectionString`](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.createfromconnectionstring.aspx) API 呼叫。

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

TopicClient Client =
    TopicClient.CreateFromConnectionString(connectionString, "TestTopic");

Client.Send(new BrokeredMessage());
```

傳送至服務匯流排主題的訊息是 [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) 類別。 [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) 物件具有一組
標準屬性 (例如 [標籤](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) 和 [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx))、 用來保存自訂應用程式特定屬性的字典和一堆任意的應用程式資料。 應用程式可以設定訊息本文，任何可序列化物件傳遞至建構函式 [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx) 物件，並適當 **DataContractSerializer** 再用來序列化物件。 或者， **System.IO.Stream** 可以提供。

下列範例示範如何傳送五則測試訊息至 **TestTopic** [TopicClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx) 上述程式碼範例中所取得的物件。 請注意， [MessageNumber](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.properties.aspx) 每個訊息的屬性值而異迴圈的反覆項目 （這可判斷哪些訂閱會接收）。

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set additional custom app-specific property.
  message.Properties["MessageNumber"] = i;

  // Send message to the topic.
  Client.Send(message);
}
```

服務匯流排主題支援 [256 KB 的訊息大小上限](service-bus-quotas.md) （包含標準和自訂應用程式屬性標頭可以容納 64 KB 的大小上限）。 主題中所保存的訊息數目沒有限制，但主題所保存的訊息大小總計會有最高限制。 此主題大小會在建立時定義，上限是 5 GB。 如果啟用分割，上限會更高。 如需詳細資訊，請參閱 [分割訊息實體](https://msdn.microsoft.com/library/azure/dn520246.aspx)。

## 如何自訂用帳戶接收訊息

自訂閱接收訊息的建議的方式是使用 [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) 物件。 [SubscriptionClient](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.aspx) 物件可以在兩個不同的模式下運作 ︰ [ReceiveAndDelete 和 PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)。

當使用 **ReceiveAndDelete** 模式中，接收是一次性作業; 也就是當服務匯流排在訂閱中收到訊息的讀取的要求，它會標示為已取用的訊息並傳回應用程式。 **ReceiveAndDelete** 模式是最簡單的模型，且最適用於應用程式容許在不處理訊息發生失敗時的案例。 若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。 因為服務匯流排會將訊息標示為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

在 **PeekLock** 模式 （此為預設模式），接收程序會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。 當服務匯流排收到要求時，
它會尋找要取用的下一個訊息、 其鎖定以防止其他取用者接收該訊息，並將它傳回應用程式。 藉由呼叫應用程式完成處理訊息 （或可靠地儲存供未來處理） 之後，完成接收程序的第二個階段 [完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 所接收訊息。 當服務匯流排看到 **完成** 呼叫時，它將訊息標示為已取用，並將它從訂閱移除。

下列範例示範如何可以接收與處理訊息使用預設 **PeekLock** 模式。 若要指定不同 [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) 值，您可以使用另一個多載 [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.createfromconnectionstring.aspx)。 這個範例會使用 [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) 回呼來處理送達的訊息
到 **HighMessages** 訂用帳戶。

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

SubscriptionClient Client =
    SubscriptionClient.CreateFromConnectionString
            (connectionString, "TestTopic", "HighMessages");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

Client.OnMessage((message) =>
{
    try
    {
        // Process message from subscription.
        Console.WriteLine("\n**High Messages**");
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Message Number: " +
            message.Properties["MessageNumber"]);

        // Remove message from subscription.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in subscription.
        message.Abandon();
    }
}, options);
```

這個範例會設定 [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptionclient.onmessage.aspx) 回呼使用 [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx) 物件。 [自動完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx) 設為 **false** 以手動控制何時要呼叫 [完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 所接收訊息。 [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) 設為 1 分鐘，讓用戶端呼叫逾時和用戶端建立新的呼叫，以檢查訊息之前，等候一分鐘的訊息。 這個屬性值會減少用戶端無法擷取訊息所出現的收費呼叫次數。

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。 如果接收應用程式無法處理訊息，因為某種原因，則它可以呼叫 [放棄](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) 方法上接收的訊息 （改為
 [完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 方法)。 這導致服務匯流排將訂用帳戶中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與訂用帳戶內鎖定訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

該應用程式當機之前處理訊息之後 [完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx) 發出要求，將訊息傳遞給應用程式重新啟動時。 這通常稱為 *至少之後處理*; 也就是說，每個訊息會至少處理一次，但在某些情況下可能會有相同的訊息
訊息。 如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。 通常您可使用訊息的
[MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) 會保持不變各個傳遞嘗試的訊息屬性。

## 刪除主題和訂用帳戶

下列範例示範如何刪除的主題 **TestTopic** 從 **HowToSample** 服務命名空間。

```
// Delete Topic.
namespaceManager.DeleteTopic("TestTopic");
```

刪除主題也將會刪除對主題註冊的任何訂用帳戶。 您也可以個別刪除訂用帳戶。 下列程式碼示範如何刪除名為 **HighMessages** 從 **TestTopic** 主題。

```
namespaceManager.DeleteSubscription("TestTopic", "HighMessages");
```

## 後續步驟

了解基本的服務匯流排主題和訂用帳戶之後，請參考下列連結以取得更多資訊。

-   請參閱 [佇列、 主題和訂閱][]。
-   API 參考 [SqlFilter][]。
-   建置工作應用程式傳送和接收服務匯流排佇列的訊息 ︰ [服務匯流排代理傳訊.NET 教學課程][]。
-   服務匯流排範例 ︰ 從下載 [Azure 範例][] 或參閱 [概觀](service-bus-samples.md)。

  [Azure classic portal]: http://manage.windowsazure.com

  [7]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/getting-started-multi-tier-13.png

  [Queues, Topics, and Subscriptions]: service-bus-queues-topics-subscriptions.md
  [SqlFilter]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.aspx
  [SqlFilter.SqlExpression]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sqlfilter.sqlexpression.aspx
  [Service Bus Brokered Messaging .NET Tutorial]: service-bus-brokered-tutorial-dotnet.md
  [Azure Samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2


