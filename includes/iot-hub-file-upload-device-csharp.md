## 從模擬裝置上傳檔案

在本節中，您將修改在 [透過 IoT 中心傳送雲端對裝置的訊息] 中建立的模擬裝置應用程式，以接收來自 IoT 中心的雲端對裝置訊息。

1. 在 Visual Studio 中，以滑鼠右鍵按一下 **SimulatedDevice** 專案，然後再按一下 **管理 NuGet 封裝...**。 

    此時會顯示 [管理 NuGet 封裝] 視窗。

2. 搜尋 `WindowsAzure.Storage`, ，按一下 [ **安裝**, ，並接受使用規定。 

    這會下載、 安裝，並將參考加入 [Microsoft Azure 儲存體 SDK](https://www.nuget.org/packages/WindowsAzure.Storage/)。

3. 在 **Program.cs** 檔案中，在檔案頂端新增下列陳述式 ︰

        using System.IO;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. 在 **程式** 類別中變更 **ReceiveC2dAsync** 方法如下 ︰
         
        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                if (receivedMessage.Properties.ContainsKey("command") && receivedMessage.Properties["command"] == "FileUpload")
                {
                    UploadFileToBlobAsync(receivedMessage);
                    continue;
                }

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();
            }
        }

    這可讓 **ReceiveC2dAsync** 區分訊息 `command` 屬性設定為 `FileUpload`, ，這會由 **UploadFileToBlobAsync** 方法。

    新增以下方法來處理檔案上傳命令。
   
        private static async Task UploadFileToBlobAsync(Message fileUploadCommand)
        {
            var fileUri = fileUploadCommand.Properties["fileUri"];
            var blob = new CloudBlockBlob(new Uri(fileUri));

            byte[] data = new byte[10 * 1024 * 1024];
            Random rng = new Random();
            rng.NextBytes(data);

            MemoryStream msWrite = new MemoryStream(data);
            msWrite.Position = 0;
            using (msWrite)
            {
                await blob.UploadFromStreamAsync(msWrite);
            }
            Console.ForegroundColor = ConsoleColor.Yellow;
            Console.WriteLine("Uploaded file to: {0}", fileUri);
            Console.ResetColor();

            await deviceClient.CompleteAsync(fileUploadCommand);
        }

    此方法會使用 Azure Storage SDK，將隨機產生的 10Mb Blob 上傳至指定的 URI。 請參閱 [Azure Storage - How to use blobs] 如需有關如何將 blob 上傳。

> [AZURE.NOTE] 請注意已上傳 blob 時，才能模擬裝置的這項實作完成定域機組對裝置訊息的方式。 此方法可簡化後端中已上傳檔案的處理作業，因為傳遞通知即代表上傳的檔案已可供處理。 中所述 [IoT 中心開發人員指南][IoT Hub Developer Guide - C2D], ，不過一則訊息，則不會完成之前 *可見度逾時* （通常是 1 分鐘） 會放回裝置佇列和 **ReceiveAsync()** 方法，它會再次接收。 在檔案上傳時間較長的情況下，可能較適合為模擬裝置將目前的上傳工作保持長期存放。 這可讓模擬裝置在檔案上傳完畢之前，完成雲端對裝置的訊息，然後傳送雲端到裝置訊息，以對後端通知工作已完成。

<!-- Links -->
[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure Storage - How to use blobs]: https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/#upload-a-blob-into-a-container

<!-- Images -->







