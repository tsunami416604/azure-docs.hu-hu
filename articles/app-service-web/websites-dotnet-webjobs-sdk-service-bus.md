<properties 
    pageTitle="如何透過 WebJobs SDK 使用 Azure 服務匯流排" 
    description="瞭解如何搭配使用 Azure 服務匯流排佇列和有關 WebJobs SDK 的主題。" 
    services="app-service\web, service-bus" 
    documentationCenter=".net" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="dotnet" 
    ms.topic="article" 
    ms.date="09/22/2015" 
    ms.author="tdykstra"/>


# 如何透過 WebJobs SDK 使用 Azure 服務匯流排

## 概觀

本指南提供 C# 程式碼範例，示範如何在建立或更新 Azure Blob 時觸發程序。 此程式碼範例使用 [WebJobs SDK](websites-dotnet-webjobs-sdk.md) 版本 1.x。

本指南假設您知道 [如何在 Visual Studio 中建立 WebJob 專案與連接字串指向儲存體帳戶](websites-dotnet-webjobs-sdk-get-started.md)。

程式碼片段只會顯示函式，不會建立程式碼 `JobHost` 物件，如此範例所示:

        static void Main(string[] args)
        {
            JobHost host = new JobHost();
            host.RunAndBlock();
        }

## <a id="prerequisites"></a> 必要條件

若要使用服務匯流排，您必須安裝 [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) 除了其他的 WebJobs SDK 封裝之外的 NuGet 封裝。

除了儲存體連接字串之外，您也必須設定 AzureWebJobsServiceBus 連接字串。 您可以 `connectionStrings` 區段的 App.config 檔案中，如下列範例所示:

        <connectionStrings>
            <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
            <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
        </connectionStrings>

如需範例專案的 App.config 檔案中包含服務匯流排連接字串設定，請參閱 [服務匯流排範例](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus)。

連接字串也可以設定在 Azure 執行階段環境中，然後在 Azure 中執行 WebJob 時覆寫 App.config 設定如需詳細資訊，請參閱 [開始使用 WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md#configure-the-web-app-to-use-your-azure-sql-database-and-storage-account)。

## <a id="trigger"></a> 如何在收到服務匯流排佇列訊息時觸發函數

若要撰寫 WebJobs SDK 在收到佇列訊息時所呼叫的函式，使用 `ServiceBusTrigger` 屬性。 屬性建構函式會接受一個參數，以指定要輪詢的佇列名稱。

### ServicebusTrigger 的運作方式

SDK 收到訊息時在 `PeekLock` 模式和呼叫 `完成` 在函數成功完成時的訊息或呼叫 `放棄` 在函數失敗時。 如果函數執行時間超過 `PeekLock` 鎖定逾時，會自動更新。

服務匯流排會處理自己的有害佇列，因此，不會受到 WebJobs SDK 所控制，也無法在其中進行設定。

### 字串佇列訊息

下列程式碼範例會讀取包含字串的佇列訊息，並將字串寫入 WebJobs SDK 儀表板。

        public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
            TextWriter logger)
        {
            logger.WriteLine(message);
        }

**附註:** 如果您要在不使用 WebJobs SDK 的應用程式中建立佇列訊息，請務必將 [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) 為"text/plain"。

### POCO 佇列訊息

SDK 會自動還原序列化包含 JSON 的 poco 物件佇列訊息 [(純舊 CLR 物件](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) 型別。 下列程式碼範例會讀取佇列訊息，其中包含 `BlobInformation` 該物件具有 `BlobName` 屬性:

        public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
            TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

示範如何使用 POCO 的屬性來處理 blob 和資料表在相同的函式的程式碼範例，請參閱 [這篇文章的儲存體佇列版本](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs)。

如果您建立佇列訊息的程式碼不使用 WebJobs SDK，請使用類似下列範例的程式碼：

        var client = QueueClient.CreateFromConnectionString(ConfigurationManager.ConnectionStrings["AzureWebJobsServiceBus"].ConnectionString, "blobadded");
        BlobInformation blobInformation = new BlobInformation () ;
        var message = new BrokeredMessage(blobInformation);
        client.Send(message);

### ServiceBusTrigger 使用的類型

除了 `字串` 和 POCO 型別，您可以使用 `ServiceBusTrigger` 屬性搭配位元組陣列或 `BrokeredMessage` 物件。

## <a id="create"></a> 如何建立服務匯流排佇列訊息

若要撰寫的函數，會建立新佇列訊息，請使用 `ServiceBus` 屬性，並在佇列名稱中傳遞至屬性建構函式。


### 在同步函數中建立單一佇列訊息

下列程式碼範例會使用輸出參數，使用與在名為 "inputqueue" 的佇列中收到的訊息相同的內容，在名為 "outputqueue" 的佇列中建立新訊息。

        public static void CreateQueueMessage(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] out string outputQueueMessage)
        {
            outputQueueMessage = queueMessage;
        }

用來建立單一佇列訊息的輸出參數可以是下列任一類型：

* `字串`
* `byte]`
* `BrokeredMessage`
* 您定義的可序列化 POCO 類型。 自動序列化為 JSON。

若為 POCO 類型參數，一定會在函式結束時建立佇列訊息；如果參數是 Null，SDK 會建立在接收和還原序列化訊息將傳回 Null 的佇列訊息。 針對其他類型，如果參數為 Null，就不會建立任何佇列訊息。

### 建立多個佇列訊息或在非同步函式中

若要建立多個訊息，請使用 `ServiceBus` 屬性搭配 `設為 < T >` 或 `IAsyncCollector < T >`, ，如下列程式碼範例所示:

        public static void CreateQueueMessages(
            [ServiceBusTrigger("inputqueue")] string queueMessage,
            [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

呼叫 `Add` 方法時，即會立即建立每個佇列訊息。

## <a id="topics"></a>如何使用服務匯流排主題

若要撰寫 SDK 在服務匯流排主題上收到訊息時所呼叫的函式，使用 `ServiceBusTrigger` 屬性搭配採用主題名稱和訂用帳戶名稱，如下列程式碼範例所示的建構函式:

        public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
            TextWriter logger)
        {
            logger.WriteLine("Topic message: " + message);
        }

若要在主題上建立一則訊息，請使用 `ServiceBus` 屬性搭配主題名稱相同的方式使用佇列名稱。

## <a id="queues"></a>儲存體佇列做法文章所涵蓋的相關的主題

特定服務匯流排無關的 WebJobs SDK 案例的相關資訊，請參閱 [如何透過 WebJobs SDK 使用 Azure 佇列儲存體](websites-dotnet-webjobs-sdk-storage-queues-how-to.md)。

該文章涵蓋下列主題：

* Async 函數
* 多個執行個體
* 正常關機
* 在函式主體中使用 WebJobs SDK 屬性
* 在程式碼中設定 SDK 連接字串
* 在程式碼中設定 WebJobs SDK 建構函式參數的值
* 手動觸發函式
* 寫入記錄檔

## <a id="nextsteps"></a> 後續步驟

本指南提供了程式碼範例，示範如何處理使用 Azure Service Bus 的常見案例。 如需如何使用 Azure WebJobs 和 WebJobs SDK 的詳細資訊，請參閱 [Azure WebJobs 建議資源](http://go.microsoft.com/fwlink/?linkid=390226)。






