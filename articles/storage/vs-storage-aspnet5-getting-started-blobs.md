<properties
    pageTitle="開始使用 Blob 儲存體和 Visual Studio 已連接服務 (ASP.NET 5) | Microsoft Azure"
    description="在使用 Visual Studio 已連接服務建立儲存體帳戶之後，如何在 Visual Studio ASP.NET 5 專案中開始使用 Azure Blob 儲存體"
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

# 開始使用 Azure Blob 儲存體和 Visual Studio 已連接服務 (ASP.NET 5)

##概觀

本文說明當您使用 Visual Studio 的 [新增已連接服務] 對話方塊，建立或參考了 ASP.NET 5 專案中的 Azure 儲存體帳戶之後，如何開始在 Visual Studio 使用 Azure Blob 儲存體。

Azure 二進位大型物件 (Microsoft Azure Blob) 儲存是一項儲存大量非結構化資料的服務，全球任何地方都可透過 HTTP 或 HTTPS 來存取這些資料。 單一 Blob 可以是任何大小。 Blob 可以是影像、音訊和視訊檔、原始資料及文件檔案。 這篇文章說明如何使用 Visual Studio 建立 Azure 儲存體帳戶之後開始使用 blob 儲存體 **新增連接的服務** ASP.NET 5 專案中的對話方塊。

就像檔案在資料夾中一樣，儲存體 Blob 位於容器中。 在您已建立儲存體之後，您會在儲存體中建立一個或多個容器。 例如，在稱為 “Scrapbook” 的儲存體中，您可以在此儲存體中建立稱為 “images” 的容器來儲存圖片，以及建立另一個稱為 “audio” 的容器來儲存音訊檔。 建立容器之後，就可以將個別的 Blob 檔案上傳至這些容器。 請參閱 [如何使用 blob 儲存體.NET](storage-dotnet-how-to-use-blobs.md "如何使用 blob 儲存體.NET") 如需有關以程式設計方式操作 blob。



##在程式碼中存取 blob 容器

若要以程式設計方式存取 ASP.NET 5 專案中的 Blob，您需要加入下列項目 (如果尚不存在)。

1. 將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C# 檔案內的頂端。

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. 取得 **CloudStorageAccount** 物件，代表儲存體帳戶資訊。 使用下列程式碼，從 Azure 服務組態取得您的儲存體連接字串和儲存體帳戶資訊。

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **注意:** 所有上述程式碼中的程式碼開頭使用以下各節。


3. 使用 **CloudBlobClient** 物件來取得 **CloudBlobContainer** 現有儲存體帳戶中容器的參考。

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");



##在程式碼中建立容器

您也可以使用 **CloudBlobClient** 儲存體帳戶中建立容器。 您只需要為新增呼叫至 **CreateIfNotExistsAsync** 如下列程式碼所示:

    // Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “my-new-container.”
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


**注意:** Azure 儲存體向 ASP.NET 5 中執行呼叫的 Api 是非同步。 請參閱 [使用 Async 和 Await 進行非同步程式設計](http://msdn.microsoft.com/library/hh191443.aspx) 如需詳細資訊。 以下程式碼假設使用非同步程式設計方法。

若要讓所有人都能使用容器中的檔案，您可以使用下列程式碼將容器設定為公用容器。

    await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

##將 Blob 上傳至容器

若要將 Blob 檔案上傳至容器，請取得容器參考，並使用該參考來取得 Blob 參考。 擁有 blob 參照後，您可以上傳任何資料流，藉由呼叫 **UploadFromStreamAsync** 方法。 如果 Blob 不存在，此操作會建立 Blob，若已存在，則予以覆寫。 下列範例顯示如何將 Blob 上傳到容器，並假設已建立該容器。

    // Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##列出容器中的 Blob
若要列出容器中的 Blob，請先取得容器參照。 然後，您可以呼叫容器的 **ListBlobsSegmentedAsync** 方法來擷取 blob 和/或目錄。 若要存取一組豐富的屬性和方法傳回 **IListBlobItem**, ，您必須進行轉換， **CloudBlockBlob**, ，**CloudPageBlob**, ，或 **CloudBlobDirectory** 物件。 如果不知道 Blob 類型，可使用類型檢查來決定要將其轉換成何種類型。 下列程式碼示範如何擷取和輸出容器中每個項目的 URI。

    BlobContinuationToken token = null;
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
            token = resultSegment.ContinuationToken;

            foreach (IListBlobItem item in resultSegment.Results)
            {
                if (item.GetType() == typeof(CloudBlockBlob))
                {
                    CloudBlockBlob blob = (CloudBlockBlob)item;
                    Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);
                }

                else if (item.GetType() == typeof(CloudPageBlob))
                {
                    CloudPageBlob pageBlob = (CloudPageBlob)item;

                    Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);
                }

                else if (item.GetType() == typeof(CloudBlobDirectory))
                {
                    CloudBlobDirectory directory = (CloudBlobDirectory)item;

                    Console.WriteLine("Directory: {0}", directory.Uri);
                }
            }
        } while (token != null);

還有其他方法可列出 Blob 容器的內容。 請參閱 [如何使用 blob 儲存體.NET](storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container) 如需詳細資訊。

##下載 Blob
若要下載 blob，請先取得 blob 參考，然後呼叫 **DownloadToStreamAsync** 方法。 下列範例會使用 **DownloadToStreamAsync** 方法，以將 blob 內容傳送至資料流物件，然後您可以儲存為本機檔案。

    // Get a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save the blob contents to a file named “myfile”.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        await blockBlob.DownloadToStreamAsync(fileStream);
    }

還有其他方法可將 Blob 儲存為檔案。 請參閱 [如何使用 blob 儲存體.NET](storage-dotnet-how-to-use-blobs.md/#download-blobs) 如需詳細資訊。

##刪除 Blob
若要刪除 blob，請先取得 blob 參考，然後呼叫 **DeleteAsync** 方法。

    // Get a reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    await blockBlob.DeleteAsync();

## 後續步驟

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

