<properties
    pageTitle="開始使用佇列儲存體和 Visual Studio 已連接服務 (WebJob 專案) | Microsoft Azure"
    description="在使用 Visual Studio 已連接服務連接至儲存體帳戶之後，如何於 WebJob 專案中開始使用 Azure 佇列儲存體。"
    services="storage"
    documentationCenter=""
    authors="TomArcher"
    manager="douge"
    editor=""/>

<tags
    ms.service="storage"
    ms.workload="web"
    ms.tgt_pltfrm="vs-getting-started"
    ms.devlang="na"
    ms.topic="article"
    ms.date="12/16/2015"
    ms.author="tarcher"/>

# 開始使用 Azure 佇列儲存體和 Visual Studio 已連接服務 (WebJob 專案)

## 概觀

本文將說明如何開始使用您建立或使用 Visual Studio 中參考 Azure 儲存體帳戶之後，Visual Studio Azure WebJob 專案中使用 Azure 佇列儲存體  **新增連接的服務** 對話方塊。 當您儲存體帳戶加入 WebJob 專案使用 Visual Studio **新增連接的服務** ] 對話方塊中，適當的 Azure 儲存體 NuGet 封裝安裝、 適當的.NET 參考會加入至專案，並儲存體帳戶的連接字串會在 App.config 檔案中更新。  

本文提供了 C# 程式碼範例，示範如何透過 Azure 佇列儲存體服務使用 Azure WebJobs SDK 1.x 版。

Azure 佇列儲存體是一項儲存大量訊息的服務，全球任何地方都可利用 HTTP 或 HTTPS 並透過驗證的呼叫來存取這些訊息。 單一佇列訊息的大小上限為 64 KB，而一個佇列可以包含數百萬個訊息，以儲存體帳戶的總容量為限。 請參閱 [如何使用佇列儲存體.NET](storage-dotnet-how-to-use-queues.md) 如需詳細資訊。 如需 ASP.NET 的詳細資訊，請參閱 [ASP.NET](http://www.asp.net)。



## 如何在接收到佇列訊息時觸發函數

若要撰寫 WebJobs SDK 在收到佇列訊息時所呼叫的函式，使用 **QueueTrigger** 屬性。 屬性建構函式採用字串參數，來指定要輪詢的佇列名稱。 您也可以 [動態設定佇列名稱](how-to-set-configuration-options)。

### 字串佇列訊息

在下列範例中，佇列包含字串訊息，因此 **QueueTrigger** 套用至字串參數，名為 **logMessage** 其中包含佇列訊息的內容。 函式 [記錄訊息寫入儀表板](#how-to-write-logs)。


        public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            logger.WriteLine(logMessage);
        }

除了 **字串**, ，參數可能是位元組陣列， **CloudQueueMessage** 物件或您定義的 POCO。

### POCO [(純舊 CLR 物件](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) 佇列訊息

在下列範例中，佇列訊息會包含適用於 JSON **BlobInformation** 物件，其中包括 **BlobName** 屬性。 SDK 會自動將物件還原序列化。

        public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
        {
            logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
        }

SDK 會使用 [Newtonsoft.Json NuGet 封裝](http://www.nuget.org/packages/Newtonsoft.Json) 序列化和還原序列化訊息。 如果您在不使用 WebJobs SDK 的程式中建立佇列訊息，您可以撰寫和下面範例類似的程式碼來建立 SDK 能夠剖析的 POCO 佇列訊息。

        BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

### Async 函數

下列 async 函數 [記錄檔寫入儀表板](#how-to-write-logs)。

        public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
        {
            await logger.WriteLineAsync(logMessage);
        }

Async 函數可能需要 [取消語彙基元](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), ，將 blob 複製下列範例所示。 (如需說明 **queueTrigger** 預留位置，請參閱 [Blob](#how-to-read-and-write-blobs-and-tables-while-processing-a-queue-message) 一節。)

        public async static Task ProcessQueueMessageAsyncCancellationToken(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
            CancellationToken token)
        {
            await blobInput.CopyToAsync(blobOutput, 4096, token);
        }

## 適用於 QueueTrigger 屬性的型別

您可以使用 **QueueTrigger** 具有下列類型 ︰

* **字串**
* 序列化為 JSON 的 POCO 型別
* **byte]**
* **CloudQueueMessage**

## 輪詢演算法

SDK 會實作隨機指數型倒退演算法，以降低閒置佇列輪詢對儲存體交易成本的影響。  找到訊息時，SDK 會等待兩秒，然後檢查的另一個訊息；當找不到任何訊息時，它會等候大約四秒，然後再試一次。 連續嘗試取得佇列訊息失敗後，等候時間會持續增加，直到它到達等待時間上限 (預設值為一分鐘)。 [最長等待時間是可以設定](#how-to-set-configuration-options)。

## 多個執行個體

如果您的 Web 應用程式在多個執行個體上執行，則連續 WebJob 將在每部機器上執行，而且每部機器將會等待觸發程序並嘗試執行函數。 在某些案例中，這會導致部分函數處理相同的資料兩次，因此函數應是以等冪的方式 (寫入，因此使用相同輸入資料重複呼叫函數才不會產生重複的結果)。  

## 平行執行

如果您有多個函數在不同的佇列上接聽，則同時接收到訊息時，SDK 會以平行方式呼叫它們。

收到單一佇列的多個訊息時也是如此。 根據預設，SDK 會一次取得一批 (16 個) 佇列訊息，並執行以平行方式處理它們的函數。 [批次大小是可以設定](#how-to-set-configuration-options)。 當要處理的訊息數目減少到批次大小 (該批訊息數目) 的一半時，SDK 就會取得另一批訊息並開始處理那些訊息。 因此，每個函數並行處理之訊息的上限數目為批次大小 (該批訊息數目) 的 1.5 倍。 這項限制個別套用至每個函式具有 **QueueTrigger** 屬性。 如果您不想平行執行在單一佇列中收到的訊息，請將批次大小設定為 1。

## 取得佇列或佇列訊息中繼資料

您可以透過新增參數至方法簽章來取得下列訊息屬性：

* **DateTimeOffset** expirationTime
* **DateTimeOffset** insertionTime
* **DateTimeOffset** nextVisibleTime
* **字串** queueTrigger （包含訊息文字）
* **字串** 識別碼
* **字串** popReceipt
* **int** dequeueCount

如果您想要直接使用 Azure 儲存體 API，您也可以加入 **CloudStorageAccount** 參數。

下列範例會將此中繼資料全部寫入至 INFO 應用程式記錄檔。 在範例中，logMessage 和 queueTrigger 包含佇列訊息的內容。

        public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
            DateTimeOffset expirationTime,
            DateTimeOffset insertionTime,
            DateTimeOffset nextVisibleTime,
            string id,
            string popReceipt,
            int dequeueCount,
            string queueTrigger,
            CloudStorageAccount cloudStorageAccount,
            TextWriter logger)
        {
            logger.WriteLine(
                "logMessage={0}\n" +
            "expirationTime={1}\ninsertionTime={2}\n" +
                "nextVisibleTime={3}\n" +
                "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
                "queue endpoint={7} queueTrigger={8}",
                logMessage, expirationTime,
                insertionTime,
                nextVisibleTime, id,
                popReceipt, dequeueCount,
                cloudStorageAccount.QueueEndpoint,
                queueTrigger);
        }

以下是範例程式碼所寫入的範例記錄：

        logMessage=Hello world!
        expirationTime=10/14/2014 10:31:04 PM +00:00
        insertionTime=10/7/2014 10:31:04 PM +00:00
        nextVisibleTime=10/7/2014 10:41:23 PM +00:00
        id=262e49cd-26d3-4303-ae88-33baf8796d91
        popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
        dequeueCount=1
        queue endpoint=https://contosoads.queue.core.windows.net/
        queueTrigger=Hello world!

## 正常關機

連續的 WebJob 中執行的函數可以接受 **CancellationToken** 參數可讓作業系統在 WebJob 即將終止時通知函數。 您可以使用此通知來確保函數不會在讓資料維持不一致狀態的情況下意外終止。

下列範例示範如何透過函數檢查即將終止的 WebJob。

    public static void GracefulShutdownDemo(
                [QueueTrigger("inputqueue")] string inputText,
                TextWriter logger,
                CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(1000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }

**注意 ︰** 儀表板可能不會正確顯示的狀態和已關閉的函式的輸出。

如需詳細資訊，請參閱 [WebJobs 正常關機](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR)。   

## 如何在處理佇列訊息時建立佇列訊息

若要撰寫會建立新的佇列訊息的函數，使用 **佇列** 屬性。 像 **QueueTrigger**, ，您可以傳入佇列名稱做為字串，也可以 [動態設定佇列名稱](#how-to-set-configuration-options)。

### 字串佇列訊息

下面的非同步程式碼範例會在名稱為 "outputqueue" 的佇列中建立一個新的佇列訊息，其內容與名為 "inputqueue" 的佇列中收到的佇列訊息相同。 (如需非同步函式會使用 **IAsyncCollector<T>** 如本節稍後所示。)


        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] out string outputQueueMessage )
        {
            outputQueueMessage = queueMessage;
        }

### POCO [(純舊 CLR 物件](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) 佇列訊息

若要建立包含 poco 物件，而不是字串的佇列訊息，請將 POCO 型別傳遞做為輸出參數 **佇列** 屬性建構函式。

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
            [Queue("outputqueue")] out BlobInformation blobInfoOutput )
        {
            blobInfoOutput = blobInfoInput;
        }

SDK 會自動將物件序列化為 JSON。 即使物件是空值，也一律會建立佇列訊息。

### 建立多個訊息或使用非同步函式

若要建立多個訊息，請將輸出佇列的參數類型 **ICollector<T>** 或 **IAsyncCollector<T>**, ，如下列範例所示。

        public static void CreateQueueMessages(
            [QueueTrigger("inputqueue")] string queueMessage,
            [Queue("outputqueue")] ICollector<string> outputQueueMessage,
            TextWriter logger)
        {
            logger.WriteLine("Creating 2 messages in outputqueue");
            outputQueueMessage.Add(queueMessage + "1");
            outputQueueMessage.Add(queueMessage + "2");
        }

每個佇列訊息就會立即建立時 **新增** 方法呼叫。

### 適用於 Queue 屬性的型別

您可以使用 **佇列** 下列參數型別屬性 ︰

* **字串** （如果參數值為非 null，函式結束時，會建立佇列訊息）
* **out byte []** (作用就像是 **字串**)
* **out CloudQueueMessage** (作用就像是 **字串**)
* **out POCO** （可序列化的型別，如果建立訊息使用 null 物件的函式結束時，參數是 null）
* **ICollector**
* **IAsyncCollector**
* **CloudQueue** （適用於建立訊息以手動方式直接使用 Azure 儲存體 API）

### 在函式主體中使用 WebJobs SDK 屬性

如果您需要執行一些工作函式中的，然後再使用 WebJobs SDK 屬性，例如 **佇列**, ，**Blob**, ，或 **資料表**, ，您可以使用 **IBinder** 介面。

下列範例會使用輸入佇列訊息，並在輸出佇列中建立含有相同內容的新訊息。 輸出佇列名稱會由函數主體中的程式碼設定。

        public static void CreateQueueMessage(
            [QueueTrigger("inputqueue")] string queueMessage,
            IBinder binder)
        {
            string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
            QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
            CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
            outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
        }

 **IBinder** 介面也可搭配 **資料表** 和 **Blob** 屬性。

## 如何在處理佇列訊息時讀取和寫入 Blob 和資料表

 **Blob** 和 **資料表** 屬性可讓您讀取和寫入 blob 和資料表。 本節中的範例適用於 Blob。 程式碼範例示範如何建立或更新 blob 時觸發程序，請參閱 [如何透過 WebJobs SDK 使用 Azure blob 儲存體](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), ，以及讀取和寫入資料表的程式碼範例，請參閱 [如何透過 WebJobs SDK 使用 Azure 資料表儲存體](websites-dotnet-webjobs-sdk-storage-tables-how-to.md)。

### 觸發 Blob 作業的字串佇列訊息

對於包含字串的佇列訊息 **queueTrigger** 是您可以使用中的預留位置 **Blob** 屬性的 **blobPath** 參數，其中包含訊息的內容。

下列範例會使用 **資料流** 物件來讀取及寫入 blob。 佇列訊息提供位於 textblobs 容器中 Blob 的名稱。 會在相同的容器中建立 Blob 的複本，並在其名稱中附加 "-new"。

        public static void ProcessQueueMessage(
            [QueueTrigger("blobcopyqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

 **Blob** 屬性建構函式採用 **blobPath** 參數來指定容器和 blob 名稱。 如需此預留位置的詳細資訊，請參閱 [如何透過 WebJobs SDK 使用 Azure blob 儲存體](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md),，

當這個屬性裝飾 **資料流** 物件，另一個建構函式參數指定 **FileAccess** 為讀取、 寫入或讀取/寫入模式。

下列範例會使用 **CloudBlockBlob** 物件來刪除 blob。 佇列訊息就是 Blob 的名稱。

        public static void DeleteBlob(
            [QueueTrigger("deleteblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
        {
            blobToDelete.Delete();
        }

### POCO [(純舊 CLR 物件](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) 佇列訊息

Poco 物件佇列訊息中儲存為 JSON，您可以使用該名稱在物件內容的預留位置 **佇列** 屬性的 **blobPath** 參數。 您也可以使用 [佇列中繼資料屬性名稱](#get-queue-or-queue-message-metadata) 做為預留位置。

下列範例會將 Blob 複製到具有不同副檔名的新 Blob。 佇列訊息就是 **BlobInformation** 物件，其中包含 **BlobName** 和 **BlobNameWithoutExtension** 屬性。 做為預留位置的 blob 路徑中使用屬性名稱 **Blob** 屬性。

        public static void CopyBlobPOCO(
            [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
            [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

SDK 會使用 [Newtonsoft.Json NuGet 封裝](http://www.nuget.org/packages/Newtonsoft.Json) 序列化和還原序列化訊息。 如果您在不使用 WebJobs SDK 的程式中建立佇列訊息，您可以撰寫和下面範例類似的程式碼來建立 SDK 能夠剖析的 POCO 佇列訊息。

        BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
        var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
        logQueue.AddMessage(queueMessage);

如果您需要先執行部分工作函式中的將 blob 繫結至物件，您可以使用函式主體中的屬性 [如先前所示的 Queue 屬性](#use-webjobs-sdk-attributes-in-the-body-of-a-function)。

###可以與 Blob 屬性搭配使用的型別

 **Blob** 屬性可以搭配下列類型 ︰

* **資料流** （讀取或寫入，可使用 FileAccess 建構函式參數所指定）
* **TextReader**
* **TextWriter**
* **字串** （讀取）
* **字串** （寫入; 字串參數為非 null，當函式傳回時，才會建立 blob）
* POCO (讀取)
* out POCO (寫入；一律會建立 Blob，當函式傳回時，如果 POCO 參數為 Null，就建立為 Null 物件)
* **CloudBlobStream** （寫入）
* **ICloudBlob** （讀取或寫入）
* **CloudBlockBlob** （讀取或寫入）
* **CloudPageBlob** （讀取或寫入）

##如何處理有害訊息

其內容會導致函數失敗的訊息稱為 *有害訊息*。 當函數失敗時不會刪除佇列訊息，最後會再度挑選到該訊息，造成重複循環。 SDK 可在有限的反覆次數之後自動中斷循環，或者您可以手動中斷循環。

### 自動處理有害訊息

SDK 將會呼叫函數最多 5 次以處理佇列訊息。 如果第五次嘗試失敗，訊息便會移到有害佇列中。 [重試次數上限是可設定](#how-to-set-configuration-options)。

有害佇列名為 *{originalqueuename}*-有害。 您可以撰寫函數，透過記錄或傳送通知表示需要手動處理，來處理有害佇列中的訊息。

在下列範例中 **CopyBlob** 函式將會失敗，當佇列訊息包含不存在的 blob 名稱。 發生時，就會從 copyblobqueue 佇列將訊息移至 copyblobqueue-poison 佇列。  **接著 ProcessPoisonMessage** 接著會記錄有害訊息。

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
        {
            blobInput.CopyTo(blobOutput, 4096);
        }

        public static void ProcessPoisonMessage(
            [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
        {
            logger.WriteLine("Failed to copy blob, name=" + blobName);
        }

下圖顯示這些函數處理有害訊息之後的主控台輸出。

![主控台輸出中的有害訊息處理](./media/vs-storage-webjobs-getting-started-queues/poison.png)

### 手動處理有害訊息

您可以透過新增取得的次數訊息已被挑選來處理 **int** 參數命名為 **dequeueCount** 到函式。 然後您可以檢查函數程式碼中的清除佇列計數，並在數目超出臨界值時自行執行有害訊息處理，如下面的範例所示。

        public static void CopyBlob(
            [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
            [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
            [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
            TextWriter logger)
        {
            if (dequeueCount > 3)
            {
                logger.WriteLine("Failed to copy blob, name=" + blobName);
            }
            else
            {
            blobInput.CopyTo(blobOutput, 4096);
            }
        }

## 如何設定組態選項

您可以使用 **JobHostConfiguration** 型別來設定下列組態選項 ︰

* 在程式碼中設定 SDK 連接字串。
* 設定 **QueueTrigger** 設定，例如最大清除佇列計數。
* 從組態取得佇列名稱。

###在程式碼中設定 SDK 連接字串

在程式碼中設定 SDK 連接字串可讓您在組態檔或環境變數中使用您自己的連接字串名稱，如下面範例所示。

        static void Main(string[] args)
        {
            var _storageConn = ConfigurationManager
                .ConnectionStrings["MyStorageConnection"].ConnectionString;

            var _dashboardConn = ConfigurationManager
                .ConnectionStrings["MyDashboardConnection"].ConnectionString;

            var _serviceBusConn = ConfigurationManager
                .ConnectionStrings["MyServiceBusConnection"].ConnectionString;

            JobHostConfiguration config = new JobHostConfiguration();
            config.StorageConnectionString = _storageConn;
            config.DashboardConnectionString = _dashboardConn;
            config.ServiceBusConnectionString = _serviceBusConn;
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### 設定 QueueTrigger 設定

您可以配置會套用至佇列訊息處理的下列設定：

- 挑選以同時平行執行的佇列訊息數目上限 (預設值為 16)。
- 將佇列訊息傳送到有害佇列之前的重試次數上限 (預設值為 5)。
- 佇列為空的時，要再次輪詢前的等待時間上限 (預設值為 1 分鐘)。

下列範例示範如何配置這些設定：

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.Queues.BatchSize = 8;
            config.Queues.MaxDequeueCount = 4;
            config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

### 在程式碼中設定 WebJobs SDK 建構函式參數的值

有時候您不想要採取硬式編碼的方式，而是在程式碼中指定佇列名稱、Blob 名稱或容器或資料表名稱。 例如，您可能想要指定的佇列名稱 **QueueTrigger** 在組態檔或環境變數。

您可以這麼做是要傳入 **NameResolver** 物件傳遞給 **JobHostConfiguration** 型別。 包含以 WebJobs SDK 屬性建構函式參數，以百分比 （%） 符號括住的特殊預留位置和 **NameResolver** 程式碼會指定實際要用以取代那些預留位置的值。

例如，假設您想要在測試環境中使用名為 logqueuetest 的佇列，和在生產環境中使用名為 logqueueprod 的佇列。 您想要指定的名稱中的項目而不是使用硬式編碼的佇列名稱， **appSettings** 會有實際佇列名稱的集合。 如果 **appSettings** 索引鍵為 logqueue，您的函式看起來如下所示。

        public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
        {
            Console.WriteLine(logMessage);
        }

您 **NameResolver** 類別接著可以取得佇列名稱，從 **appSettings** 如下列範例所示 ︰

        public class QueueNameResolver : INameResolver
        {
            public string Resolve(string name)
            {
                return ConfigurationManager.AppSettings[name].ToString();
            }
        }

您傳遞 **NameResolver** 類別傳入 **JobHost** 物件在下列範例所示。

        static void Main(string[] args)
        {
            JobHostConfiguration config = new JobHostConfiguration();
            config.NameResolver = new QueueNameResolver();
            JobHost host = new JobHost(config);
            host.RunAndBlock();
        }

**注意 ︰** 佇列、 資料表和 blob 名稱每次呼叫函式，但應用程式啟動時才解析 blob 容器名稱。 您無法在執行工作時，變更 Blob 容器名稱。

## 如何手動觸發函數

若要手動觸發函式，使用 **呼叫** 或 **CallAsync** 方法 **JobHost** 物件和 **NoAutomaticTrigger** 屬性在函式，如下列範例所示。

        public class Program
        {
            static void Main(string[] args)
            {
                JobHost host = new JobHost();
                host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
            }

            [NoAutomaticTrigger]
            public static void CreateQueueMessage(
                TextWriter logger,
                string value,
                [Queue("outputqueue")] out string message)
            {
                message = value;
                logger.WriteLine("Creating queue message: ", message);
            }
        }

## 如何寫入記錄檔

儀表板會在兩個地方顯示記錄檔：WebJob 的頁面與特定 WebJob 引動過程的頁面。

![WebJob 頁面中的記錄檔](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

![函式引動過程頁面中的記錄檔](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

從主控台的方法，讓您呼叫的函式中或在輸出 **main （)** 方法會顯示在 webjob 的 [儀表板] 頁面中，而不是特定方法引動過程的頁面。 您從方法簽章的參數所取得 TextWriter 物件的輸出會顯示在方法引動過程的 [儀表板] 頁面。

因為主控台屬於單一執行緒，無法同時執行許多工作函式，所以主控台輸出無法連結到特定的方法引動過程。 這就是為什麼 SDK 提供使用它自己專屬的記錄寫入器物件的每個函式引動過程。

要寫入 [應用程式追蹤記錄](web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), ，使用 **Console.Out** （建立標示為 INFO 的記錄檔） 和 **Console.Error** （建立標示為 ERROR 的記錄檔）。 另一個方法是使用 [Trace 或 TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), 、 提供詳細資訊、 警告及嚴重層級，除了資訊與錯誤。 視您設定 Azure 網頁應用程式的方式而定，應用程式追蹤記錄檔會出現在網頁應用程式記錄檔、Azure 資料表或 Azure Blob 中。 所有主控台輸出的應用程式記錄檔裡最近的 100 筆記錄也同樣會顯示在 WebJob 的 [儀表板] 頁面，而不是函式引動過程的頁面。

只有當程式是以 Azure WebJob 執行時，主控台輸出才會顯示在儀表板，而不是在本機或在某些其他環境中執行時。

您可以停用記錄 [將儀表板連接字串設定為 null](#how-to-set-configuration-options)。

下列範例示範寫入記錄檔的數種方式：

        public static void WriteLog(
            [QueueTrigger("logqueue")] string logMessage,
            TextWriter logger)
        {
            Console.WriteLine("Console.Write - " + logMessage);
            Console.Out.WriteLine("Console.Out - " + logMessage);
            Console.Error.WriteLine("Console.Error - " + logMessage);
            logger.WriteLine("TextWriter - " + logMessage);
        }

WebJobs SDK 儀表板中，從輸出 **TextWriter** 物件顯示當您移至特定頁面函式引動過程，並按一下 [ **切換輸出**:

![按一下函式引動過程連結](./media/vs-storage-webjobs-getting-started-queues/dashboardinvocations.png)

![函式引動過程頁面中的記錄檔](./media/vs-storage-webjobs-getting-started-queues/dashboardlogs.png)

WebJobs SDK 儀表板中的主控台的最新的 100 行則會顯示輸出當您移至頁面進行 WebJob （不適用於叫用函數），並按一下 **切換輸出**。

![按一下切換輸出](./media/vs-storage-webjobs-getting-started-queues/dashboardapplogs.png)

在連續 WebJob 中，應用程式記錄檔顯示在資料/工作/continuous/*{webjobname}*/job_log.txt web 應用程式檔案系統中的。

        [09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
        [09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
        [09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

在 Azure Blob 中，應用程式記錄檔看起來像這樣：
        2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
        2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
        2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

而在 Azure 資料表 **Console.Out** 和 **Console.Error** 記錄檔看起來像這樣 ︰

![在資料表中的資訊記錄檔](./media/vs-storage-webjobs-getting-started-queues/tableinfo.png)

![在資料表中的錯誤記錄檔](./media/vs-storage-webjobs-getting-started-queues/tableerror.png)

##後續步驟

本文提供的程式碼範例示範如何處理使用 Azure 佇列的常見案例。 如需如何使用 Azure WebJobs 和 WebJobs SDK 的詳細資訊，請參閱 [Azure WebJobs 建議資源](http://go.microsoft.com/fwlink/?linkid=390226)。


