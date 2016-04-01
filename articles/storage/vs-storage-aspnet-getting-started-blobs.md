<properties
    pageTitle="開始使用 Blob 儲存體和 Visual Studio 已連接服務 (ASP.NET) | Microsoft Azure"
    description="在使用 Visual Studio 已連接服務連接到儲存體帳戶之後，如何於 Visual Studio 的 ASP.NET專案中開始使用 Azure Blob 儲存體"
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

# 開始使用 Blob 儲存體和 Visual Studio 已連接服務 (ASP.NET)

## 概觀

本文說明如何開始使用 Azure Blob 儲存體，您建立或使用 Visual Studio 參考 Azure 儲存體帳戶中的 ASP.NET 應用程式之後 **新增連接的服務** 對話方塊。 本文將示範如何建立 Blob 容器並執行其他常見工作，例如上傳、列出、下載和刪除 Blob。 這些範例均以 C# 撰寫，並
使用 [Azure Storage Client Library for.NET](https://msdn.microsoft.com/library/azure/dn261237.aspx)。

 - 有關使用 Azure Blob 儲存體的一般資訊，請參閱 [如何使用 Blob 儲存體.NET](storage-dotnet-how-to-use-blobs.md)。
 - 如需 ASP.NET 專案的詳細資訊，請參閱 [ASP.NET](http://www.asp.net)。


Azure 二進位大型物件 (Microsoft Azure Blob) 儲存是一項儲存大量非結構化資料的服務，全球任何地方都可透過 HTTP 或 HTTPS 來存取這些資料。 單一 Blob 可以是任何大小。 Blob 可以是影像、音訊和視訊檔、原始資料及文件檔案。

就像檔案在資料夾中一樣，儲存體 Blob 位於容器中。 在您已建立儲存體帳戶之後，您會在儲存體中建立一個或多個容器。 例如，在稱為 “Scrapbook” 的儲存體中，您可以在此儲存體中建立稱為 “images” 的 blob 容器來儲存圖片，以及建立另一個稱為 “audio” 的 blob 容器來儲存音訊檔。 建立容器之後，就可以將個別的 Blob 檔案上傳至這些容器。




## 在程式碼中存取 blob 容器

若要以程式設計方式存取 ASP.NET 專案中的 Blob，您需要加入下列項目 (如果尚不存在)。

1. 將下列程式碼命名空間宣告，新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 C# 檔案內的頂端。

        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Auth;
        using Microsoft.WindowsAzure.Storage.Blob;


2. 取得 **CloudStorageAccount** 物件，代表儲存體帳戶資訊。 使用下列程式碼，從 Azure 服務組態取得您的儲存體連接字串和儲存體帳戶資訊。

        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    > [AZURE.NOTE] 使用下列各節中的所有先前的程式碼的程式碼開頭。

3. 取得 **CloudBlobClient** 物件以參考儲存體帳戶中的現有容器。

        // Create a blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [AZURE.NOTE] 有些對外呼叫 Azure 儲存體執行 ASP.NET 5 中的 Api 是非同步。 請參閱 [使用 Async 和 Await 進行非同步程式設計](http://msdn.microsoft.com/library/hh191443.aspx) 如需詳細資訊。


## 在程式碼中建立 blob 容器

您也可以使用 **CloudBlobClient** 儲存體帳戶中建立容器物件。 您是新增呼叫至 **CreateIfNotExistsAsync** 上述程式碼，如下列範例所示。

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();

## 將 Blob 上傳至容器

Azure Blob 儲存體支援區塊 Blob 和頁面 Blob。  在大多數情況下，建議使用區塊 Blob 的類型。

若要將檔案上傳至區塊 Blob，請取得容器參照，並使用
該參照來取得區塊 Blob 參照。 擁有 Blob 參照後，即可
藉由呼叫它的資料流 **UploadFromStream** 方法。 此操作會建立 Blob (如果其並不存在)
或覆寫 Blob (如果其已存在)。 下列範例顯示如何將 Blob 上傳到容器，並假設已建立該容器。

    // Get a CloudBlobContainer named 'container' as described in "Access blob containers in code."

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## 列出容器中的 Blob

若要列出容器中的 blob，使用 **ListBlobs** 方法來擷取 blob 和/或目錄
目錄。 若要針對
傳回 **IListBlobItem**, ，您必須進行轉換， **CloudBlockBlob**,，
**CloudPageBlob**, ，或 **CloudBlobDirectory** 物件。  如果不清楚類型，可使用
類型檢查來決定要將其轉換至何種類型。  下列程式碼會
示範如何擷取和輸出
 **相片** 容器。

    // Get a CloudBlobContainer named 'container' as described in "Access blob containers in code."

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, false))
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

在上述範例所示，blob 服務會為具備容器中之目錄的概念
概念。 正因如此，您能夠以更像資料夾的結構
組織 Blob。 例如，假設此容器中有名為
名為 **相片**。

    photo1.jpg
    2010/architecture/description.txt
    2010/architecture/photo3.jpg
    2010/architecture/photo4.jpg
    2011/architecture/photo5.jpg
    2011/architecture/photo6.jpg
    2011/architecture/description.txt
    2011/photo7.jpg

當您呼叫 **ListBlobs** 'photos' 容器 （如上例所示），傳回的集合
將包含 **CloudBlobDirectory** 和 **CloudBlockBlob** 物件
其分別代表最上層所包含的目錄和 Blob。 下列範例顯示產生的輸出。

    Directory: https://<accountname>.blob.core.windows.net/photos/2010/
    Directory: https://<accountname>.blob.core.windows.net/photos/2011/
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


您可以選擇性地設定 **UseFlatBlobListing** 參數 **ListBlobs** 方法
**true**。 這會導致每個 blob 各當成 **CloudBlockBlob**, ，不論目錄為何。  下列範例示範如何呼叫 **ListBlobs**。

    // Loop over items within the container and output the length and URI.
    foreach (IListBlobItem item in container.ListBlobs(null, true))
    {
       ...
    }

而下一個範例顯示此結果。

    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
    Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
    Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
    Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
    Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
    Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
    Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
    Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg



## 下載 Blob

若要下載 blob，請使用 **DownloadToStream** 方法。 下列
範例會使用 **DownloadToStream** 方法將 blob
內容移轉到您可以永久儲存到本機檔案的串流物件。

    // Get a CloudBlobContainer named 'container' as described in "Access blob containers in code"

    // Retrieve a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

您也可以使用 **DownloadToStream** 下載 blob 的內容當成文字字串的方法。

    // Get a CloudBlobContainer named 'container' as described in "Access blob containers in code"

    // Retrieve a reference to a blob named "myblob.txt"
    CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

    string text;
    using (var memoryStream = new MemoryStream())
    {
        blockBlob2.DownloadToStream(memoryStream);
        text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
    }

## 刪除 Blob

若要刪除 blob，請使用 **刪除** 方法。

    // Get a CloudBlobContainer named 'container' as described in "Access blob containers in code"

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## 以非同步方式分頁列出 Blob

如果您要列出大量的 Blob，或是要控制在單一列出作業中傳回的結果數，您可以在結果頁面中列出 Blob。 下列範例說明如何以非同步方式分頁傳回結果，如此不會因為等待傳回大型結果集而中斷執行。

此範例說明一般 blob 列出方式，但您也可以藉由設定執行階層式列出， **useFlatBlobListing** 參數 **ListBlobsSegmentedAsync** 方法 **false**。

範例方法會呼叫非同步方法，因此必須前面加上 **非同步** 關鍵字，且必須傳回 **工作** 物件。 指定的 await 關鍵字 **ListBlobsSegmentedAsync** 方法會擱置範例方法的執行，直到列出工作完成為止。

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## 後續步驟

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]


