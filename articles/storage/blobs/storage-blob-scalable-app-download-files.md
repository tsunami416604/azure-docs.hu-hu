---
title: "Töltse le a nagy adatmennyiségek véletlenszerű Azure Storage-ból |} Microsoft Docs"
description: "A nagy adatmennyiségek véletlenszerű letöltését egy Azure Storage-fiókot az Azure SDK használata"
services: storage
documentationcenter: 
author: georgewallace
manager: jeconnoc
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 12/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 46b0cf3666088175372b6a2e73b3dd421a4bff8b
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Az Azure storage nagy adatmennyiségek véletlenszerű letöltése

Ez az oktatóanyag három egy sorozat része. Ez az oktatóanyag bemutatja, hogyan töltheti le az Azure storage nagy mennyiségű adat.

A sorozat három része a megismerheti, hogyan:

> [!div class="checklist"]
> * Az alkalmazás frissítése
> * Az alkalmazás futtatása
> * Ellenőrizze a kapcsolatok száma

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez végrehajtotta az előző tárolási oktatóanyag: [töltse fel a nagy adatmennyiségek véletlenszerű párhuzamosan az Azure storage][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>A virtuális géppé távoli

 A virtuális gép létrehozásához a távoli asztali munkamenetgazda, a következő paranccsal a helyi számítógépen. Cserélje le a virtuális gép nyilvános IP-címét. Ha a rendszer erre kéri, adja meg a virtuális gép létrehozásakor használt hitelesítő adatokat.

```
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>Az alkalmazás frissítése

Az előző oktatóanyagban csak feltöltött fájlok a tárfiókhoz. Nyissa meg `D:\git\storage-dotnet-perf-scale-app\Program.cs` egy szövegszerkesztőben. Cserélje le a `Main` metódus a következő példával. Ez a példa megjegyzéseit a feltöltési feladat ki, és uncomments a letöltési feladat és a feladat törli a tárfiókot, ha a teljes tartalmát.

```csharp
public static void Main(string[] args)
{
    Console.WriteLine("Azure Blob storage performance and scalability sample");
    // Set threading and default connection limit to 100 to ensure multiple threads and connections can be opened.
    // This is in addition to parallelism with the storage client library that is defined in the functions below.
    ThreadPool.SetMinThreads(100, 4);
    ServicePointManager.DefaultConnectionLimit = 100; // (Or More)

    bool exception = false;
    try
    {
        // Call the UploadFilesAsync function.
        UploadFilesAsync().GetAwaiter().GetResult();

        // Uncomment the following line to enable downloading of files from the storage account.  This is commented out
        // initially to support the tutorial at https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-scaleable-app-download-files.
        // DownloadFilesAsync().GetAwaiter().GetResult();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.  This is commented out initialy
        // As the tutorial at https://docs.microsoft.com/en-us/azure/storage/blobs/storage-blob-scaleable-app-download-files has you upload only for one tutorial and download for the other. 
        if (!exception)
        {
            // DeleteExistingContainersAsync().GetAwaiter().GetResult();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

Az alkalmazás frissítését követően kell építenie az alkalmazást újra. Nyissa meg a `Command Prompt` , és keresse meg `D:\git\storage-dotnet-perf-scale-app`. Építse újra az alkalmazás futtatásával `dotnet build` az alábbi példában látható módon:

```
dotnet build
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Most, hogy az alkalmazást újra lett építve az az idő a frissített kód az alkalmazás futtatásához. Ha nem már meg van nyitva, nyissa meg a `Command Prompt` , és keresse meg `D:\git\storage-dotnet-perf-scale-app`.

Típus `dotnet run` az alkalmazás futtatásához.

```
dotnet run
```

Az alkalmazás olvassa a tárolókban található a megadott tárfiók a **storageconnectionstring**. Azt a blobok 10 egy idő az telepítéseket a [ListBlobsSegmented](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmented?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmented_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) módszer a tárolókban, és letölti azokat a helyi számítógép-használatával a [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) metódus.
Az alábbi táblázat a [BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet) meghatározott minden egyes BLOB letöltés.

|Tulajdonság|Érték|Leírás|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| igaz| Ez a tulajdonság letiltja az MD5 kivonatoló feltöltött tartalmat ellenőrzése. Gyorsabb átvitelt letiltja az MD5 ellenőrzést hoz létre. Azonban nem erősíti meg a érvényességi vagy a átvitt fájlok integritásában. |
|[StorBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| hamis| Ez a tulajdonság határozza meg, ha az MD5 kivonatoló kiszámítása és tárolja.   |

A `DownloadFilesAsync` feladat a következő példa látható:

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestionOptions on the download, including disabling MD5 hash validation for this example, this improves the download speed.
    BlobRequestOptions options = new BlobRequestOptions
    {
        DisableContentMD5Validation = true,
        StoreBlobContentMD5 = false
    };

    // Retrieve the list of containers in the storage account.  Create a directory and configure variables for use later.
    BlobContinuationToken continuationToken = null;
    List<CloudBlobContainer> containers = new List<CloudBlobContainer>();
    do
    {
        var listingResult = await blobClient.ListContainersSegmentedAsync(continuationToken);
        continuationToken = listingResult.ContinuationToken;
        containers.AddRange(listingResult.Results);
    }
    while (continuationToken != null);

    var directory = Directory.CreateDirectory("download");
    BlobResultSegment resultSegment = null;
    Stopwatch time = Stopwatch.StartNew();

    // Download the blobs
    try
    {
        List<Task> tasks = new List<Task>();
        int max_outstanding = 100;
        int completed_count = 0;

        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        // Iterate through the containers
        foreach (CloudBlobContainer container in containers)
        {
            do
            {
                // Return the blobs from the container lazily 10 at a time.
                resultSegment = await container.ListBlobsSegmentedAsync(null, true, BlobListingDetails.All, 10, continuationToken, null, null);
                continuationToken = resultSegment.ContinuationToken;
                {
                    foreach (var blobItem in resultSegment.Results)
                    {

                        if (((CloudBlob)blobItem).Properties.BlobType == BlobType.BlockBlob)
                        {
                            // Get the blob and add a task to download the blob asynchronously from the storage account.
                            CloudBlockBlob blockBlob = container.GetBlockBlobReference(((CloudBlockBlob)blobItem).Name);
                            Console.WriteLine("Downloading {0} from container {1}", blockBlob.Name, container.Name);
                            await sem.WaitAsync();
                            tasks.Add(blockBlob.DownloadToFileAsync(directory.FullName + "\\" + blockBlob.Name, FileMode.Create, null, options, null).ContinueWith((t) =>
                            {
                                sem.Release();
                                Interlocked.Increment(ref completed_count);
                            }));

                        }
                    }
                }
            }
            while (continuationToken != null);
        }

        // Creates an asynchronous task that completes when all the downloads complete.
        await Task.WhenAll(tasks);
    }
    catch (Exception e)
    {
        Console.WriteLine("\nError encountered during transfer: {0}", e.Message);
    }

    time.Stop();
    Console.WriteLine("Download has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());
    Console.ReadLine();
}
```

### <a name="validate-the-connections"></a>A kapcsolatok ellenőrzése

A fájlok letöltése folyamatban van, amíg a tárfiókhoz ellenőrizheti az egyidejű kapcsolatok száma. Nyissa meg a `Command Prompt` és típus `netstat -a | find /c "blob:https"`. Ez a parancs megjeleníti az éppen megnyitott használatával kapcsolatok számát `netstat`. A következő példa bemutatja egy hasonló kimeneti látottaknak futtatásakor az oktatóanyag magát. A példában látható, mivel volt több mint 280 kapcsolatok, nyissa meg, ha a véletlenszerű fájlok letöltését a tárfiók.

```
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>Következő lépések

A sorozat három része megtanulta, nagy adatmennyiségek véletlenszerű letölti a tárfiókból, például:

> [!div class="checklist"]
> * Az alkalmazás futtatása
> * Ellenőrizze a kapcsolatok száma

Előzetes ellenőrzése a portálon átviteli sebesség és a késleltetés metrikák adatsorozat négy részét.

> [!div class="nextstepaction"]
> [Ellenőrizze az átviteli sebesség és a késleltetés metrikákat a portálon](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md