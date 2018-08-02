---
title: Nagy mennyiségű véletlenszerű adat párhuzamos feltöltése az Azure Storage-ba | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogy az Azure SDK hogyan használható nagy mennyiségű véletlenszerű adat párhuzamos feltöltésére egy Azure Storage-fiókba
services: storage
author: roygara
ms.service: storage
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: rogarana
ms.custom: mvc
ms.component: blobs
ms.openlocfilehash: 557dd1d89fc05d82f1839a7b02356857f41164c6
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39399736"
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Nagy mennyiségű véletlenszerű adat párhuzamos feltöltése az Azure Storage-ba

Ez az oktatóanyag egy sorozat második része. Ez az oktatóanyag egy olyan alkalmazás üzembe helyezését mutatja be, amely nagy mennyiségű véletlenszerű adat feltöltését végzi egy Azure tárfiókba.

A sorozat második részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Kapcsolati sztring konfigurálása
> * Az alkalmazás létrehozása
> * Az alkalmazás futtatása
> * A kapcsolatok számának ellenőrzése

Az Azure Blob Storage skálázható szolgáltatást biztosít adatainak tárolásához. Ahhoz, hogy az alkalmazás a lehető legjobb teljesítménnyel működhessen, ajánlatos megismerkedni a Blob Storage működésével. Fontos ismerni az Azure blobok korlátait, amelyekről további információt a [Blob Storage skálázhatósági céljait](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets) ismertető részben találhat.

A [partíció elnevezése](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#subheading47) is egy fontos szempont a nagy teljesítményű, blobokat használó alkalmazások tervezésekor. Az Azure Storage egy tartományalapú particionálási sémát használ a skálázáshoz és a terheléselosztáshoz. Ez a konfiguráció azt jelenti, hogy a hasonló elnevezési konvenciókkal vagy előtagokkal rendelkező fájlok ugyanarra a partícióra kerülnek. Ez a logika magában foglalja a tároló nevét, amelybe a fájlokat feltöltik. Ebben az oktatóanyagban olyan fájlokat használ, amelyek nevek helyett GUID-azonosítókkal, valamint véletlenszerűen létrehozott tartalmakkal rendelkeznek. Ezeket azután öt különböző, véletlenszerű nevű tárolóba töltjük fel.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez el kell végeznie az előző tárolási oktatóanyagot: [Virtuális gép és tárfiók létrehozása skálázható alkalmazások számára][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>A virtuális gép távoli vezérlése

Használja az alábbi parancsot a helyi gépén, ha egy távoli asztali kapcsolatot szeretne létrehozni a virtuális géphez. Cserélje le az IP-címet a virtuális gépe nyilvános IP-címére. Ha a rendszer erre kéri, adja meg a virtuális gép létrehozásakor használt hitelesítő adatokat.

```
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>Kapcsolati sztring konfigurálása

Az Azure Portalon lépjen a tárfiókra. Válassza a **Hozzáférési kulcsok** lehetőséget a tárfiók **Beállítások** területén. Másolja ki az elsődleges vagy a másodlagos kulcs **kapcsolati sztringjét**. Jelentkezzen be az előző oktatóanyagban létrehozott virtuális gépbe. Nyissa meg rendszergazdaként a **parancssort** és futtassa a `setx` parancsot a `/m` kapcsolóval. Ez a parancs egy gépbeállításhoz tartozó környezeti változó értékét menti. A környezeti változó nem érhető el, amíg újra be nem tölti a **parancssort**. Cserélje le a **\<storageConnectionString\>** kifejezést a következő mintában:

```
setx storageconnectionstring "<storageConnectionString>" /m
```

Ha végzett, nyissa meg újból a **parancssort**, lépjen be a `D:\git\storage-dotnet-perf-scale-app` mappába, és gépelje be a következő parancsot az alkalmazás buildeléséhez: `dotnet build`.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Nyissa meg a `D:\git\storage-dotnet-perf-scale-app` címet.

Az alkalmazás futtatásához írja be a következőt: `dotnet run`. A `dotnet` parancs első futtatásakor feltölti a helyi csomaggyorsítótárat, hogy javítsa a visszaállítási sebességet és, hogy offline hozzáférést tegyen lehetővé. A parancs végrehajtása akár egy percet is igénybe vehet, és csak egyszer kell végrehajtani.

```
dotnet run
```

Az alkalmazás öt, véletlenszerűen elnevezett tárolót hoz létre, és megkezdi az átmeneti könyvtárban lévő fájlok feltöltését a tárfiókba. Az alkalmazás a szálak minimális számát 100-ra állítja, a [DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit(v=vs.110).aspx) változó értékét pedig 100-ra annak érdekében, hogy az alkalmazás futtatása közben nagy számú párhuzamosan futó kapcsolat legyen futtatható.

A szálkezelési és a kapcsolati korlátozások beállításán felül az [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) metódus [BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet) beállítása úgy van konfigurálva, hogy párhuzamosságot használjon és letiltsa az MD5-kivonat érvényesítését. A fájlok feltöltése 100 MB méretű blokkokban történik. Ez a konfiguráció jobb teljesítményt biztosít, a használata azonban gyengén teljesítő hálózaton nem ajánlott, mert hiba esetén a teljes 100 MB-os blokk feltöltését újra kell kezdeni.

|Tulajdonság|Érték|Leírás|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.paralleloperationthreadcount?view=azure-dotnet)| 8| A beállítás feltöltéskor blokkokra töri a blobot. A legjobb teljesítmény érdekében ennek az értéknek a magok számának 8-szorosának kell lennie. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| true| Ez a tulajdonság letiltja a feltöltött tartalom MD5-kivonat ellenőrzését. A gyorsabb átvitel érdekében tiltsa le az MD5-ellenőrzést. Így azonban nem biztosított a folyamatban lévő átvitelben érintett fájlok érvényessége vagy integritása.   |
|[StoreBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| false| Ez a tulajdonság határozza meg, hogy az MD5 kivonatoló kiszámítása és fájlban való tárolása megtörtént-e.   |
| [RetryPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.retrypolicy?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_RetryPolicy)| 2-second backoff with 10 max retry |A kérések újrapróbálkozási szabályzatát határozza meg. Kapcsolódási hiba esetén a rendszer újra próbálkozik, ebben a példában az [ExponentialRetry](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.exponentialretry?view=azure-dotnet) szabályzat 2 másodperces leállításra és legfeljebb 10 újrapróbálkozásra van konfigurálva. Ez a beállítás akkor fontos, ha az alkalmazás lassan eléri a [blob storage skálázhatósági célértékét](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).  |

A következő példában az `UploadFilesAsync` feladat látható:

```csharp
private static async Task UploadFilesAsync()
{
    // Create random 5 characters containers to upload files to.
    CloudBlobContainer[] containers = await GetRandomContainersAsync();
    var currentdir = System.IO.Directory.GetCurrentDirectory();

    // path to the directory to upload
    string uploadPath = currentdir + "\\upload";
    Stopwatch time = Stopwatch.StartNew();
    try
    {
        Console.WriteLine("Iterating in directory: {0}", uploadPath);
        int count = 0;
        int max_outstanding = 100;
        int completed_count = 0;

        // Define the BlobRequestionOptions on the upload.
        // This includes defining an exponential retry policy to ensure that failed connections are retried with a backoff policy. As multiple large files are being uploaded
        // large block sizes this can cause an issue if an exponential retry policy is not defined.  Additionally parallel operations are enabled with a thread count of 8
        // This could be should be multiple of the number of cores that the machine has. Lastly MD5 hash validation is disabled for this example, this improves the upload speed.
        BlobRequestOptions options = new BlobRequestOptions
        {
            ParallelOperationThreadCount = 8,
            DisableContentMD5Validation = true,
            StoreBlobContentMD5 = false
        };
        // Create a new instance of the SemaphoreSlim class to define the number of threads to use in the application.
        SemaphoreSlim sem = new SemaphoreSlim(max_outstanding, max_outstanding);

        List<Task> tasks = new List<Task>();
        Console.WriteLine("Found {0} file(s)", Directory.GetFiles(uploadPath).Count());

        // Iterate through the files
        foreach (string path in Directory.GetFiles(uploadPath))
        {
            // Create random file names and set the block size that is used for the upload.
            var container = containers[count % 5];
            string fileName = Path.GetFileName(path);
            Console.WriteLine("Uploading {0} to container {1}.", path, container.Name);
            CloudBlockBlob blockBlob = container.GetBlockBlobReference(fileName);

            // Set block size to 100MB.
            blockBlob.StreamWriteSizeInBytes = 100 * 1024 * 1024;
            await sem.WaitAsync();

            // Create tasks for each file that is uploaded. This is added to a collection that executes them all asyncronously.  
            tasks.Add(blockBlob.UploadFromFileAsync(path, null, options, null).ContinueWith((t) =>
            {
                sem.Release();
                Interlocked.Increment(ref completed_count);
            }));
            count++;
        }

        // Creates an asynchronous task that completes when all the uploads complete.
        await Task.WhenAll(tasks);

        time.Stop();

        Console.WriteLine("Upload has been completed in {0} seconds. Press any key to continue", time.Elapsed.TotalSeconds.ToString());

        Console.ReadLine();
    }
    catch (DirectoryNotFoundException ex)
    {
        Console.WriteLine("Error parsing files in the directory: {0}", ex.Message);
    }
    catch (Exception ex)
    {
        Console.WriteLine(ex.Message);
    }
}
```

Az alábbi példa a Windowson futtatott alkalmazás kimenetének egy része.

```
Created container https://mystorageaccount.blob.core.windows.net/9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Created container https://mystorageaccount.blob.core.windows.net/bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Created container https://mystorageaccount.blob.core.windows.net/9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Created container https://mystorageaccount.blob.core.windows.net/47646f1a-c498-40cd-9dae-840f46072180
Created container https://mystorageaccount.blob.core.windows.net/38b2cdab-45fa-4cf9-94e7-d533837365aa
Iterating in directory: D:\git\storage-dotnet-perf-scale-app\upload
Found 50 file(s)
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\1d596d16-f6de-4c4c-8058-50ebd8141e4d.txt to container 9efa7ecb-2b24-49ff-8e5b-1d25e5481076.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\242ff392-78be-41fb-b9d4-aee8152a6279.txt to container bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\38d4d7e2-acb4-4efc-ba39-f9611d0d55ef.txt to container 9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\45930d63-b0d0-425f-a766-cda27ff00d32.txt to container 47646f1a-c498-40cd-9dae-840f46072180.
Starting upload of D:\git\storage-dotnet-perf-scale-app\upload\5129b385-5781-43be-8bac-e2fbb7d2bd82.txt to container 38b2cdab-45fa-4cf9-94e7-d533837365aa.
...
Upload has been completed in 142.0429536 seconds. Press any key to continue
```

### <a name="validate-the-connections"></a>A kapcsolatok ellenőrzése

A fájlok feltöltése közben ellenőrizheti a tárfiók egyidejű kapcsolatainak számát. Nyissa meg a **parancssort**, és írja be a `netstat -a | find /c "blob:https"` parancsot. Ez a parancs a `netstat` használatával mutatja a jelenleg megnyitott kapcsolatok számát. A következő példa egy ahhoz hasonló kimenetet mutat be, mint amit akkor láthat, ha saját maga futtatja az oktatóanyagot. Ahogy a példában látható, 800 kapcsolat volt megnyitva a fájlok tárfiókba történő véletlenszerű feltöltése közben. Ez az érték a feltöltés futtatása során folyamatosan változik. Blokkrészek párhuzamos feltöltésével a tartalmak áthelyezéséhez szükséges idő jelentősen csökken.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>További lépések

A sorozat második részében megismerkedett a nagy mennyiségű véletlenszerű adat tárfiókba történő párhuzamos feltöltésével, többek között a következőkkel:

> [!div class="checklist"]
> * Kapcsolati sztring konfigurálása
> * Az alkalmazás létrehozása
> * Az alkalmazás futtatása
> * A kapcsolatok számának ellenőrzése

A sorozat harmadik részében nagy mennyiségű adatot fog letölteni egy tárfiókból.

> [!div class="nextstepaction"]
> [Nagy mennyiségű nagyméretű fájl párhuzamos feltöltése egy tárfiókba](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md
