## 佈建 Azure 儲存體帳戶
由於模擬裝置會在 Azure 儲存體 Blob 中上傳檔案，因此您必須擁有 Azure 儲存體帳戶。 您可以使用現有的語言，或依照 [About Azure Storage] 來建立新的。 記下儲存體帳戶連接字串。

## 將 Azure Blob URI 傳送至模擬裝置

在本節中，您將修改 **SendCloudtoDevice** 包含 Azure 建立 [IoT 中心與傳送定域機組-裝置訊息] 的主控台應用程式使用共用的存取簽章 blob URI。 如此一來，雲端後端只會將 Blob 的寫入存取權授與雲端到裝置訊息的收件者。

1. 在 Visual Studio 中，以滑鼠右鍵按一下 **SendCloudtoDevice** 專案，然後再按一下 **管理 NuGet 封裝...**。 

    此時會顯示 [管理 NuGet 封裝] 視窗。

2. 搜尋 `WindowsAzure.Storage`, ，按一下 [ **安裝**, ，並接受使用規定。 

    這會下載、 安裝，並將參考加入 [Microsoft Azure 儲存體 SDK](https://www.nuget.org/packages/WindowsAzure.Storage/)。

3. 在 **Program.cs** 檔案中，在檔案頂端新增下列陳述式 ︰

        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

4. 在 **程式** 類別中，新增下列類別欄位，以取代您的儲存體帳戶的連接字串。

        static string storageConnectionString = "{storage connection string}";

    然後，新增下列方法 (您可以取代任何 blob 容器名稱，本教學課程會使用 **iothubfileuploadtutorial**):
   
        private static async Task<string> GenerateBlobUriAsync()
        {
            var storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            var blobClient = storageAccount.CreateCloudBlobClient();
            var blobContainer = blobClient.GetContainerReference("iothubfileuploadtutorial");
            await blobContainer.CreateIfNotExistsAsync();

            var blobName = String.Format("deviceUpload_{0}", Guid.NewGuid().ToString());
            CloudBlockBlob blob = blobContainer.GetBlockBlobReference(blobName);

            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy();
            sasConstraints.SharedAccessStartTime = DateTime.UtcNow.AddMinutes(-5);
            sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
            sasConstraints.Permissions = SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Write;
            string sasBlobToken = blob.GetSharedAccessSignature(sasConstraints);

            return blob.Uri + sasBlobToken;
        }

    這個方法會建立新的 blob 參考，並產生共用的存取簽章 URI 中所述 [建立並使用與 Blob 服務 SAS](https://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-shared-access-signature-part-2/)。 請注意，上述方法產生的簽章 URI 的有效時間為 24 小時。 如果目標裝置需要更多時間來上傳檔案 (例如裝置不常連接，或上傳大型檔案的連線品質不可靠)，建議您考慮使用到期時間更長的簽章。

5. 修改 **SendCloudToDeviceMessageAsync** 方式如下 ︰

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message();
            commandMessage.Properties["command"] = "FileUpload";
            commandMessage.Properties["fileUri"] = await GenerateBlobUriAsync();
            commandMessage.Ack = DeliveryAcknowledgement.Full;

            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    這個方法會傳送雲端到裝置訊息，其中包含兩個應用程式屬性：一個會將此訊息識別為上傳檔案的命令，另一個則保留 Blob URI。 這也會要求完整的傳遞通知。 請注意，這兩個應用程式屬性中的資訊可能會在訊息本文中序列化，但這樣一來，將資訊序列化及還原序列化就需要額外的處理程序。

<!-- Links -->

[About Azure Storage]: https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/#create-a-storage-account

[IoT Hub Developer Guide - C2D]: iot-hub-devguide.md#c2d
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Transient Fault Handling]: https://msdn.microsoft.com/en-us/library/hh680901(v=pandp.50).aspx
[Get started with IoT Hub]: iot-hub-csharp-csharp-getstarted.md

<!-- Images -->









