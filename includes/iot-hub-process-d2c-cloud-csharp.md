## 處理裝置到雲端訊息

在本節中，您將建立 Windows 主控台應用程式，可處理來自 IoT 中心的裝置到雲端訊息。 Iot 中心都會公開 [事件中樞][Event Hubs Overview]-讀取裝置對雲端訊息相容的端點。 本教學課程使用 [EventProcessorHost] 至主控台應用程式以處理這些訊息。 如需有關如何處理訊息的事件中心您可以參考 [Get Started with Event Hubs] 教學課程。

實作可靠的資料點訊息儲存體或互動式訊息轉送時的主要挑戰在於，事件中樞的事件處理會依靠訊息取用者檢查其進度。 此外，為了達到高輸送量，讀取事件中樞時，檢查點應該在大型批次進行，以建立重複處理大量訊息的可能性。 在本教學課程中，我們會示範如何將 Azure 儲存體寫入及服務匯流排重複資料刪除視窗與事件處理器主機檢查點同步處理。

我們為了可靠地寫入訊息至 Azure 儲存體，讓使用 [區塊 blob][Azure Block Blobs] 個別區塊認可的功能。 事件處理器會累積記憶體中的訊息，直到執行檢查點的時機為止 (也就是在累積的緩衝區大於 4 Mb 的區塊大小上限之後，或在超過服務匯流排重複資料刪除時間視窗之後)。 在檢查之前，新區塊便會認可至 blob。

事件處理器會使用事件中樞訊息位移做為區塊識別碼。 這樣可以在新區塊認可至儲存體之前執行重複資料刪除檢查，查看認可的區塊和檢查點之間是否有損毀。

> [AZURE.NOTE] 本教學課程使用單一儲存體帳戶來寫入從 IoT 中心擷取的所有訊息。 請參閱 [Azure Storage scalability Guidelines] 來決定是否需要多個 Azure 儲存體帳戶在您的方案。

為了避免互動式訊息處理程序中出現重複項目，我們利用服務匯流排的重複資料刪除功能。 藉由使用唯一的 `MessageId` 為每個互動式訊息加上時間戳記，服務匯流排可以確保，在指定的重複資料刪除時間視窗中，不會有兩個具有相同 `MessageId` 的訊息傳遞給接收者。 這個重複資料刪除功能加上服務匯流排佇列所提供的各訊息完成語意，可以更加容易地執行可靠的互動式訊息處理。

若要確保沒有任何訊息提交到重複資料刪除視窗外，我們要將事件處理器主機的檢查點機制與服務匯流排佇列的重複資料刪除視窗同步處理。 每次超過時間視窗時 (在本教學課程為 1 小時) 至少強制執行檢查點一次即可完成。

> [AZURE.NOTE] 本教學課程使用單一磁碟分割的服務匯流排佇列互動的所有訊息從 IoT 中心都擷取程序。 根據您的方案需求，請參閱 [Service Bus documentation] 如需有關如何使用服務匯流排佇列。

### 佈建 Azure 儲存體帳戶和服務匯流排佇列
若要使用 [EventProcessorHost], ，您必須擁有 Azure 儲存體帳戶。 您可以使用現有的語言，或依照 [About Azure Storage] 來建立新的。 記下儲存體帳戶連接字串。

您也需要服務匯流排佇列以啟用可靠的互動式訊息處理。 您可以建立佇列以程式設計方式使用 1 小時重複資料刪除視窗中所述 [如何使用服務匯流排佇列][Service Bus Queue], ，或使用 [Azure portal], ，遵循下列步驟:

1. 按一下 [ **新增** 左上角，然後在 **應用程式服務**, ，然後 **服務匯流排**, ，然後 **佇列**, ，然後 **自訂**, ，選取名稱 **d2ctutorial**, ，然後選取 [1 小時內的重複資料刪除視窗。

    ![][30]

2. 按一下 [佇列名稱 (**d2ctutorial**)，然後 **設定**, ，並建立兩個共用的存取原則，一個名為 **傳送** 與 **傳送** 權限，以及一個名 **接聽** 與 **接聽** 權限。 按一下 [ **儲存** 在底部，完成時。

    ![][31]

3. 按一下 [ **儀表板** 上方，然後在 **連接資訊** 下方，記下兩個連接字串。

    ![][32]

### 建立事件處理器

1. 在目前的 Visual Studio 方案中，按一下 [ **檔案-> [新增專案]-> [** 來建立新的 Visual C# 桌面應用程式專案使用 **主控台應用程式** 專案範本。 將專案命名為 **ProcessDeviceToCloudMessages**。

    ![][10]

2. 在 [方案總管] 中，以滑鼠右鍵按一下方案，以及 [ **管理 NuGet 封裝**。

     **管理 NuGet 封裝** ] 對話方塊隨即出現。

3. 搜尋 `WindowsAzure.ServiceBus`, ，按一下 [ **安裝**, ，並接受使用規定。

    這會下載、 安裝，並將參考加入 [Azure 服務匯流排 NuGet 封裝](https://www.nuget.org/packages/WindowsAzure.ServiceBus), ，與其所有相依性。

4. 搜尋 `Microsoft Azure Service Bus Event Hub - EventProcessorHost`, ，按一下 [ **安裝**, ，並接受使用規定。

    這會下載、安裝並新增 [Azure Service Bus Event Hub - EventProcessorHost NuGet 封裝](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost)的參考與其所有相依性。

5. 以滑鼠右鍵按一下 **ProcessDeviceToCloudMessages** 專案，然後按一下 **新增**, ，然後按一下 [ **類別**。 將新類別 **StoreEventProcessor**, ，然後按一下 [ **確定** 建立類別。

6. 在 StoreEventProcessor.cs 檔案開頭處新增下列陳述式：

        using System.IO;
        using System.Diagnostics;
        using System.Security.Cryptography;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

    接著，將該類別的主體取代為下列程式碼：

        class StoreEventProcessor : IEventProcessor
        {
            private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
            public static string StorageConnectionString;
            public static string ServiceBusConnectionString;

            private CloudBlobClient blobClient;
            private CloudBlobContainer blobContainer;
            private QueueClient queueClient;

            private long currentBlockInitOffset;
            private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

            private Stopwatch stopwatch;
            private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

            public StoreEventProcessor()
            {
                var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
                blobClient = storageAccount.CreateCloudBlobClient();
                blobContainer = blobClient.GetContainerReference("d2ctutorial");
                queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString, "d2ctutorial");
            }

            Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
            {
                Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
                return Task.FromResult<object>(null);
            }

            Task IEventProcessor.OpenAsync(PartitionContext context)
            {
                Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

                if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
                {
                    currentBlockInitOffset = 0;
                }
                stopwatch = new Stopwatch();
                stopwatch.Start();

                return Task.FromResult<object>(null);
            }

            async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
            {
                foreach (EventData eventData in messages)
                {
                    byte[] data = eventData.GetBytes();

                    if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
                    {
                        var messageId = (string) eventData.SystemProperties["message-id"];

                        var queueMessage = new BrokeredMessage(new MemoryStream(data));
                        queueMessage.MessageId = messageId;
                        queueMessage.Properties["messageType"] = "interactive";
                        await queueClient.SendAsync(queueMessage);

                        WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
                        continue;
                    }

                    if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
                    {
                        await AppendAndCheckpoint(context);
                    }
                    await toAppend.WriteAsync(data, 0, data.Length);

                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
                }
            }

            private async Task AppendAndCheckpoint(PartitionContext context)
            {
                var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
                var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
                toAppend.Seek(0, SeekOrigin.Begin);
                byte[] md5 = MD5.Create().ComputeHash(toAppend);
                toAppend.Seek(0, SeekOrigin.Begin);

                var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
                var currentBlob = blobContainer.GetBlockBlobReference(blobName);

                if (await currentBlob.ExistsAsync())
                {
                    await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
                    var blockList = await currentBlob.DownloadBlockListAsync();
                    var newBlockList = new List<string>(blockList.Select(b => b.Name));

                    if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
                    {
                        newBlockList.Add(blockId);
                        WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
                    }
                    else
                    {
                        WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
                    }
                    await currentBlob.PutBlockListAsync(newBlockList);
                }
                else
                {
                    await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
                    var newBlockList = new List<string>();
                    newBlockList.Add(blockId);
                    await currentBlob.PutBlockListAsync(newBlockList);

                    WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
                }

                toAppend.Dispose();
                toAppend = new MemoryStream(MAX_BLOCK_SIZE);

                // checkpoint.
                await context.CheckpointAsync();
                WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

                currentBlockInitOffset = long.Parse(context.Lease.Offset);
                stopwatch.Restart();
            }

            private void WriteHighlightedMessage(string message)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(message);
                Console.ResetColor();
            }
        }

    這個類別會呼叫 **EventProcessorHost** 處理裝置對雲端訊息來自 [IoT 中心，而且會實作可靠地儲存主要的邏輯中的 blob 容器轉送互動式訊息至服務匯流排佇列的訊息。
     `OpenAsync()` 方法，初始化  `currentBlockInitOffset` 變數會追蹤目前正在讀取此事件處理器的第一個訊息的位移 (請記住，每個處理器會負責單一磁碟分割)。

    `ProcessEventsAsync()` 方法會從以下列方式處理的 IoT 中心接收一批次的訊息。 互動式訊息會傳送至服務匯流排佇列，而資料點訊息會附加至稱為 `toAppend` 的 memoryBuffer。 萬一記憶體緩衝區達到區塊限制 (也就是 4 MB)，或者距離上一個檢查點的時間已超過服務匯流排重複資料刪除時間視窗 (在本教學課程為 1 小時)，都會觸發檢查點。

    方法 `AppendAndCheckpoint()` 會先為要附加的區塊產生 blockId。 由於 Azure 儲存體需要所有區塊識別碼都具有相同的長度，所以位移會以前置的零來填補 (`currentBlockInitOffset.ToString("0000000000000000000000000")`)。 如果具有此識別碼的區塊已在 blob 中，此方法會利用目前的區塊覆寫它。

> [AZURE.NOTE] 若要簡化的程式碼，此教學課程會使用每個資料分割的單一 blob 檔案儲存的訊息。 實際的解決方案會實作檔案復原，使用的方法是在檔案到達特定大小時 (請注意，Azure blob 大小上限為 195 GB)，或在某段時間之後 (例如 `fileName_{partitionId}_{date-time}`) 建立其他檔案。

7. 在 **程式** 類別中，新增下列 `using` 上方的陳述式:

        using Microsoft.ServiceBus.Messaging;

    然後，修改 **Main** 方法 **程式** 類別如下所示，以取代 IoT 中心 **iothubowner** 連接字串 (從 [開始使用 IoT 中心] 教學課程)，儲存體連接字串，並使用服務匯流排連線字串 **傳送** 名為佇列的權限 **d2ctutorial**:

        static void Main(string[] args)
        {
            string iotHubConnectionString = "{iot hub connection string}";
            string iotHubD2cEndpoint = "messages/events";
            StoreEventProcessor.StorageConnectionString = "{storage connection string}";
            StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
            Console.WriteLine("Registering EventProcessor...");
            eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }

> [AZURE.NOTE] 為了簡單起見，本教學課程使用的單一執行個體 [EventProcessorHost]。 請參閱 [Event Hubs Programming Guide] 和 [處理序裝置對雲端訊息] 教學課程，如需有關處理裝置對雲端的訊息。

## 接收互動式訊息
在本節中，您將撰寫 Windows 主控台應用程式，它會接收來自服務匯流排佇列的互動式訊息。 請參閱 [建置多層式應用程式使用服務匯流排][] 如需有關如何使用服務匯流排的方案架構設計人員。

1. 在目前的 Visual Studio 方案中，建立新的 Visual C# 桌面應用程式專案使用 **主控台應用程式** 專案範本。 將專案命名為 **ProcessD2cInteractiveMessages**。

2. 在 [方案總管] 中，以滑鼠右鍵按一下方案，以及 [ **Manage NuGet Packages for Solution...**。

    此時會顯示 [管理 NuGet 封裝] 視窗。

3. 搜尋 `WindowsAzure.Service Bus`, ，按一下 [ **安裝**, ，並接受使用規定。

    這會下載、 安裝，並將參考加入 [Azure 服務匯流排](https://www.nuget.org/packages/WindowsAzure.ServiceBus), ，與其所有相依性。

4. 新增下列 `using` 上方的陳述式 **Program.cs** 檔案:

        using System.IO;
        using Microsoft.ServiceBus.Messaging;

5. 最後，加入下列幾行以 **Main** 方法中，連接字串取代為 **接聽** 名為佇列的權限 **d2ctutorial**:

        Console.WriteLine("Process D2C Interactive Messages app\n");

        string connectionString = "{service bus listen connection string}";
        QueueClient Client = QueueClient.CreateFromConnectionString(connectionString, "d2ctutorial");

        OnMessageOptions options = new OnMessageOptions();
        options.AutoComplete = false;
        options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

        Client.OnMessage((message) =>
        {
            try
            {
                var bodyStream = message.GetBody<Stream>();
                bodyStream.Position = 0;
                var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

                Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

                message.Complete();
            }
            catch (Exception)
            {
                message.Abandon();
            }
        }, options);

        Console.WriteLine("Receiving interactive messages from SB queue...");
        Console.WriteLine("Press any key to exit.");
        Console.ReadLine();

<!-- Links -->

[About Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account

[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[Get Started with Event Hubs]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry

[Azure Storage scalability Guidelines]: ../storage/storage-scalability-targets.md

[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx

[Service Bus documentation]: ../service-bus/service-bus-dotnet-how-to-use-queues.md

[Event Hubs Overview]: ../event-hubs/event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3

[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Event Hubs Programming Guide]: ../event-hubs/event-hubs-programming-guide.md

[Azure preview portal]: https://portal.azure.com/

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[Azure Portal]: https://manage.windowsazure.com/

[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues.md

[Build multi-tier applications with Service Bus]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md


<!-- Images -->
[10]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png

[30]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue2.png
[31]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue3.png
[32]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue4.png

