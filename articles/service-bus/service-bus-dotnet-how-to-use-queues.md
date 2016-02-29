<properties
    pageTitle="如何使用服務匯流排佇列 (.NET) | Microsoft Azure"
    description="了解如何使用 Azure 中的服務匯流排佇列。 程式碼範例是以 C# 撰寫並使用 .NET API。"
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

服務匯流排 **NuGet** 封裝是最簡單的方法來取得服務匯流排 API，並設定您的應用程式使用所有服務匯流排相依性。 NuGet Visual Studio 擴充功能可讓您輕易地安裝和更新 Visual Studio 和 Visual Studio Express 中的程式庫和工具。 服務匯流排 NuGet 封裝是最簡單的方式
若要取得服務匯流排 API，並設定您的應用程式使用所有服務匯流排相依性。

若要在應用程式中安裝 NuGet 封裝，請執行下列動作：

1.  在 [方案總管] 中，以滑鼠右鍵按一下 **參考**, ，然後按一下 [ **管理 NuGet 封裝**。
2.  搜尋 「 服務匯流排 」 並選取 **Microsoft Azure 服務匯流排** 項目。 按一下 [ **安裝** 完成安裝，然後關閉此對話方塊。

    ![][7]

您現在可以開始對服務匯流排撰寫程式碼。

## 設定服務匯流排連線字串

服務匯流排使用連接字串來儲存端點和認證。 您可以將連接字串置於設定檔中，而非硬式編碼它：

- 使用 Azure 雲端服務時，建議您使用 Azure 服務組態系統 (.csdef 和 .cscfg 檔案) 來儲存連接字串。
- 使用 Azure 網站或 Azure 虛擬機器時，建議您使用 .NET 組態系統 (例如 Web.config 檔案) 來儲存連接字串。

在這兩種情況下，您可以擷取連接字串使用 [CloudConfigurationManager.GetSetting][GetSetting] 方法，如本文稍後所示。

### 在使用雲端服務時設定連接字串

服務設定機制為 Azure 雲端服務專案所獨有，可讓您動態變更組態設定從 [Azure 傳統入口網站][] 不需重新部署您的應用程式。 例如，在您的服務定義 (.csdef) 檔案中加入 `Setting` 標籤，如下個範例所示。

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

使用網站或虛擬機器時，建議您使用.NET 設定系統 (例如， **Web.config**)。 您可以使用 `<appSettings>` 元素儲存連接字串。

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

您可以執行管理作業使用的服務匯流排佇列的 [NamespaceManager][] 類別。 此類別提供建立、列舉及刪除佇列的方法。

這個範例會建構 [NamespaceManager][] 物件使用的 Azure [CloudConfigurationManager][] 類別與連接字串，其中包含基底位址的服務匯流排服務命名空間和適當的 SAS 認證與權限加以管理。 這是下列範例所示表單的連接字串。

````
Endpoint=sb://yourServiceNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedSecretValue=yourKey
````

請使用下例，假設組態設定如上一節。

```
// Create the queue if it does not exist already.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue("TestQueue");
}
```

有的多載 [CreateQueue](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.createqueue.aspx) 方法可讓您調整屬性
(例如，若要設定預設存留時間 (TTL) 值套用至傳送至佇列的訊息) 的佇列。 這些設定會套用使用 [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx) 類別。 下列範例將說明如何使用大小上限為 5 GB 和預設訊息 TTL 為 1 分鐘的設定，來建立名為 `TestQueue` 的佇列。

```
// Configure queue settings.
QueueDescription qd = new QueueDescription("TestQueue");
qd.MaxSizeInMegabytes = 5120;
qd.DefaultMessageTimeToLive = new TimeSpan(0, 1, 0);

// Create a new queue with custom settings.
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

var namespaceManager =
    NamespaceManager.CreateFromConnectionString(connectionString);

if (!namespaceManager.QueueExists("TestQueue"))
{
    namespaceManager.CreateQueue(qd);
}
```

> [AZURE.NOTE] 您可以使用 [QueueExists](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.queueexists.aspx) 方法 [NamespaceManager][] 物件來檢查服務命名空間內是否已存在具有指定名稱的佇列。

## 傳送訊息至佇列

若要傳送訊息至服務匯流排佇列，應用程式會使用連接字串取得
[QueueClient][] 物件使用的連接字串。

下列程式碼示範如何建立 [QueueClient][] 物件 `TestQueue` 剛才建立使用的佇列 [CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx) API 呼叫。

```
string connectionString =
    CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

QueueClient Client =
    QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

Client.Send(new BrokeredMessage());
```

訊息傳送至，並收到來自服務匯流排佇列都是執行個體的 [BrokeredMessage][] 類別。 [BrokeredMessage][] 物件具有一組標準屬性 (例如 [標籤](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) 和 [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx))，字典
用來保存自訂應用程式特定的屬性，以及一堆任意的應用程式資料。 應用程式可以設定訊息本文，任何可序列化物件傳遞給建構函式的[BrokeredMessage][] 物件，並適當 **DataContractSerializer** 再用來序列化物件。 或者，您可以提供 **System.IO.Stream** 物件。

下列範例示範如何傳送五則測試訊息至 `TestQueue` [QueueClient][] 上述程式碼範例中所取得的物件。

```
for (int i=0; i<5; i++)
{
  // Create message, passing a string message for the body.
  BrokeredMessage message = new BrokeredMessage("Test message " + i);

  // Set some addtional custom app-specific properties.
  message.Properties["TestProperty"] = "TestValue";
  message.Properties["Message number"] = i;

  // Send message to the queue.
  Client.Send(message);
}
```

服務匯流排佇列支援 [256 Kb 的訊息大小上限](service-bus-quotas.md) (包含標準和自訂應用程式屬性標頭可以容納 64 KB 的大小上限)。 佇列中所保存的訊息數目沒有限制，但佇列所保存的訊息大小總計會有最高限制。 此佇列大小會在建立時定義，上限是 5 GB。 如果啟用分割，上限會更高。 如需詳細資訊，請參閱 [分割訊息實體](service-bus-partitioning.md)。

## 如何從佇列接收訊息

從佇列接收訊息的建議的方式是使用 [QueueClient][] 物件。 [QueueClient][] 物件可以在兩個不同的模式下運作: [ReceiveAndDelete 和 PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)。

當使用 **ReceiveAndDelete** 模式中，接收是一次性作業; 也就是說，當服務匯流排佇列中收到訊息的讀取的要求，它將訊息標示為已取用，並傳回應用程式。 **ReceiveAndDelete** 是簡單的模型，且最適用於應用程式容許在不處理訊息發生失敗時的案例。 若要了解這一點，請考慮取用者發出接收要求，接著系統在處理此要求之前當機的案例。 因為服務匯流排會標示
訊息為已取用，當應用程式重新啟動並開始重新取用訊息時，它將會遺漏當機前已取用的訊息。

在 **PeekLock** 模式 (此為預設模式)，接收會變成兩階段作業，因此可以支援無法容許遺漏訊息的應用程式。 當服務匯流排收到要求時，
它會尋找要取用的下一個訊息、 其鎖定以防止其他取用者接收該訊息，並將它傳回應用程式。 藉由呼叫應用程式完成處理訊息 (或可靠地儲存供未來處理) 之後，完成接收程序的第二個階段 [完成][] 所接收訊息。 當服務匯流排看到 [完成][] 呼叫時，它會將標示為已取用，訊息，並將它從佇列移除。

下列範例示範如何可以接收與處理訊息使用預設 **PeekLock** 模式。 若要指定不同 [ReceiveMode](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx) 值，您可以使用另一個多載
[CreateFromConnectionString](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.createfromconnectionstring.aspx)。 這個範例會使用 [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) 回呼
若要處理的訊息抵達 `TestQueue`。

```
string connectionString =
  CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
QueueClient Client =
  QueueClient.CreateFromConnectionString(connectionString, "TestQueue");

// Configure the callback options.
OnMessageOptions options = new OnMessageOptions();
options.AutoComplete = false;
options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

// Callback to handle received messages.
Client.OnMessage((message) =>
{
    try
    {
        // Process message from queue.
        Console.WriteLine("Body: " + message.GetBody<string>());
        Console.WriteLine("MessageID: " + message.MessageId);
        Console.WriteLine("Test Property: " +
        message.Properties["TestProperty"]);

        // Remove message from queue.
        message.Complete();
    }
    catch (Exception)
    {
        // Indicates a problem, unlock message in queue.
        message.Abandon();
    }
}, options);
```

這個範例會設定 [OnMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.onmessage.aspx) 回呼使用 [OnMessageOptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.aspx) 物件。 [自動完成](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autocomplete.aspx)
設定為 **false** 啟用手動控制何時要呼叫 [完成][] 所接收訊息。 [AutoRenewTimeout](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.onmessageoptions.autorenewtimeout.aspx) 設為 1 分鐘，讓用戶端呼叫逾時和用戶端建立新的呼叫，以檢查訊息之前，等候一分鐘的訊息。 這個屬性值會減少用戶端無法擷取訊息所出現的收費呼叫次數。

## 如何處理應用程式當機與無法讀取的訊息

服務匯流排提供一種功能，可協助您從應用程式的錯誤或處理訊息的問題中順利復原。 如果接收者應用程式無法處理訊息，因為某種原因，則它可以呼叫 [放棄](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.abandon.aspx) 所接收訊息的方法 (而不是 [完成][] 方法)。 這將導致服務匯流排將佇列中的訊息解除鎖定，讓此訊息可以被相同取用應用程式或其他取用應用程式重新接收。

與佇列內鎖定訊息相關的還有逾時，如果應用程式無法在鎖定逾時到期之前處理訊息 (例如，如果應用程式當機)，則服務匯流排會自動解除鎖定訊息，並讓訊息可以被重新接收。

該應用程式當機之前處理訊息之後 [完成][] 發出要求，將訊息傳遞給應用程式重新啟動時。 這通常稱為 **至少處理一次**; 也就是說，每個訊息會至少處理一次，但在某些情況下可能會重新傳遞相同訊息。 如果案例無法容許重複處理，則應用程式開發人員應在其應用程式中加入其他邏輯，以處理重複的訊息傳遞。 這通常您可使用 [MessageId](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx) 訊息，各個傳遞嘗試保持不變的屬性。

## 後續步驟

了解基本的服務匯流排佇列之後，請參考下列連結以取得更多資訊。

-   了解服務匯流排訊息中的實體 [佇列、 主題和訂閱][]。
-   建置工作應用程式傳送和接收訊息的服務匯流排佇列 [服務匯流排代理訊息.NET 教學課程][]。
-   下載服務匯流排範例從 [Azure 範例][] 或參閱 [的服務匯流排範例概觀][]。

  [Azure classic portal]: http://manage.windowsazure.com
  [7]: ./media/service-bus-dotnet-how-to-use-queues/getting-started-multi-tier-13.png
  [Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
  [Service Bus brokered messaging .NET tutorial]: service-bus-brokered-tutorial-dotnet.md
  [Azure samples]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [overview of Service Bus samples]: service-bus-samples.md
  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [Complete]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.complete.aspx
