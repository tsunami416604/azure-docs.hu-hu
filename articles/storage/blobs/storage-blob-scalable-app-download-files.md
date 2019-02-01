---
title: Nagy mennyiségű véletlenszerű adat letöltése az Azure Storage-ból | Microsoft Docs
description: Megismerkedhet nagy mennyiségű véletlenszerű adat letöltésével egy Azure Storage-fiókból az Azure SDK használatával
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: b2c5e2d54c0323e984a48f2540a7df9a3b64b49b
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55511173"
---
# <a name="download-large-amounts-of-random-data-from-azure-storage"></a>Nagy mennyiségű véletlenszerű adat letöltése az Azure Storage-ból

Ez az oktatóanyag egy sorozat harmadik része. Ez az oktatóanyag azt mutatja be, hogyan tölthető le nagy mennyiségű adat az Azure Storage-ból.

A sorozat harmadik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az alkalmazás frissítése
> * Az alkalmazás futtatása
> * A kapcsolatok számának ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez el kell végeznie az előző tárolási oktatóanyagot: [Töltse fel nagy mennyiségű véletlenszerű adat párhuzamos az Azure storage-][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>A virtuális gép távoli vezérlése

 Használja az alábbi parancsot a helyi gépén, ha egy távoli asztali munkamenetet szeretne létrehozni a virtuális géppel. Cserélje le az IP-címet a virtuális gépe nyilvános IP-címére. Ha a rendszer erre kéri, adja meg a virtuális gép létrehozásakor használt hitelesítő adatokat.

```
mstsc /v:<publicIpAddress>
```

## <a name="update-the-application"></a>Az alkalmazás frissítése

Az előző oktatóanyagban csak a tárfiókba töltött fel fájlokat. Nyissa meg a következőt egy szövegszerkesztőben: `D:\git\storage-dotnet-perf-scale-app\Program.cs`. Cserélje le a `Main` metódust az alábbi mintára. Ez a példa megjegyzésként szerepelteti a feltöltési feladatot, a letöltési feladatot és a művelet befejeződésekor a tárfiók tartalmának törlési feladatát azonban nem.

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
        // initially to support the tutorial at https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files.
        // DownloadFilesAsync().GetAwaiter().GetResult();
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
        exception = true;
    }
    finally
    {
        // The following function will delete the container and all files contained in them.  This is commented out initially
        // As the tutorial at https://docs.microsoft.com/azure/storage/blobs/storage-blob-scalable-app-download-files has you upload only for one tutorial and download for the other. 
        if (!exception)
        {
            // DeleteExistingContainersAsync().GetAwaiter().GetResult();
        }
        Console.WriteLine("Press any key to exit the application");
        Console.ReadKey();
    }
}
```

Az alkalmazás frissítését követően újra létre kell hoznia az alkalmazást. Nyisson meg egy `Command Prompt` elemet, és keresse meg a következőt: `D:\git\storage-dotnet-perf-scale-app`. Az alkalmazás újbóli létrehozása a `dotnet build` futtatásával történik, az alábbi példában látható módon:

```
dotnet build
```

## <a name="run-the-application"></a>Az alkalmazás futtatása

Most, hogy az alkalmazás újra létre lett hozva, futtathatja az alkalmazást a frissített kóddal. Ha még nem tette meg, nyisson meg egy `Command Prompt` elemet, és keresse meg a következőt: `D:\git\storage-dotnet-perf-scale-app`.

Az alkalmazás futtatásához írja be a következőt: `dotnet run`.

```
dotnet run
```

Az alkalmazás a **storageconnectionstring** karakterlánccal megadott tárfiókban lévő tárolókat olvassa. Az alkalmazás egyszerre 10 blobon halad végig a [ListBlobsSegmented](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobssegmented?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlobContainer_ListBlobsSegmented_System_String_System_Boolean_Microsoft_WindowsAzure_Storage_Blob_BlobListingDetails_System_Nullable_System_Int32__Microsoft_WindowsAzure_Storage_Blob_BlobContinuationToken_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) metódussal a tárolókban, majd letölti azokat a helyi gépre a [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_) metódussal.
Az alábbi táblázatban az egyes letöltött blobokhoz megadott [BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet) paraméter látható.

|Tulajdonság|Érték|Leírás|
|---|---|---|
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| true| Ez a tulajdonság letiltja a feltöltött tartalom MD5-kivonat ellenőrzését. A gyorsabb átvitel érdekében tiltsa le az MD5-ellenőrzést. Így azonban nem biztosított a folyamatban lévő átvitelben érintett fájlok érvényessége vagy integritása. |
|[StoreBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false| Ez a tulajdonság határozza meg, hogy az MD5 kivonatoló kiszámítása és tárolása megtörtént-e.   |

A következő példában az `DownloadFilesAsync` feladat látható:

```csharp
private static async Task DownloadFilesAsync()
{
    CloudBlobClient blobClient = GetCloudBlobClient();

    // Define the BlobRequestOptions on the download, including disabling MD5 hash validation for this example, this improves the download speed.
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

A fájlok letöltése közben ellenőrizheti a tárfiók egyidejű kapcsolatainak számát. Nyisson meg egy `Command Prompt` elemet, és írja be a következőt: `netstat -a | find /c "blob:https"`. Ez a parancs a `netstat` használatával mutatja a jelenleg megnyitott kapcsolatok számát. A következő példa egy ahhoz hasonló kimenetet mutat be, mint amit akkor láthat, ha saját maga futtatja az oktatóanyagot. Ahogy a példában látható, több mint 280 kapcsolat volt megnyitva a véletlenszerű fájlok tárfiókból történő letöltése közben.

```
C:\>netstat -a | find /c "blob:https"
289

C:\>
```

## <a name="next-steps"></a>További lépések

A sorozat harmadik részében megismerkedett a nagy mennyiségű véletlenszerű adat tárfiókból történő letöltésével, például a következőkkel:

> [!div class="checklist"]
> * Az alkalmazás futtatása
> * A kapcsolatok számának ellenőrzése

A sorozat negyedik részében az átviteli sebességgel és a késéssel kapcsolatos mérőszámok portálon történő ellenőrzésével ismerkedhet meg.

> [!div class="nextstepaction"]
> [Átviteli sebességgel és késéssel kapcsolatos mérőszámok ellenőrzése a portálon](storage-blob-scalable-app-verify-metrics.md)

[previous-tutorial]: storage-blob-scalable-app-upload-files.md
