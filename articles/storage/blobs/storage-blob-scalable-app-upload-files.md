---
title: "Töltse fel a nagy adatmennyiségek véletlenszerű Azure Storage párhuzamosan |} Microsoft Docs"
description: "Töltse fel a nagy adatmennyiségek véletlenszerű párhuzamosan egy Azure Storage-fiókot az Azure SDK használata"
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
ms.openlocfilehash: c587a5fb23ad72b4a20475a83ad6ac4764717c42
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2017
---
# <a name="upload-large-amounts-of-random-data-in-parallel-to-azure-storage"></a>Töltse fel a nagy adatmennyiségek véletlenszerű párhuzamosan az Azure storage

Ez az oktatóanyag a két egy sorozat része. Ez az oktatóanyag bemutatja, hogy az alkalmazás, amely nagy mennyiségű adatot véletlenszerű feltölt egy Azure storage-fiókot.

A második rész a sorozat, megismerheti, hogyan:

> [!div class="checklist"]
> * A kapcsolati karakterlánc konfigurálása
> * Az alkalmazás létrehozása
> * Az alkalmazás futtatása
> * Ellenőrizze a kapcsolatok száma

Azure blob Storage tárolóban tárolja az adatokat egy méretezhető szolgáltatást biztosít. Annak érdekében, hogy az alkalmazás van, mint performant lehető, hogyan működik a blob storage ajánlott ismeretét. Az Azure-blobokat határértékeit ismerete fontos, ismerje meg, ezek a korlátozások bővebben látogasson el a: [blob storage méretezhetőségi célok](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).

[Partíció elnevezési](../common/storage-performance-checklist.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#subheading47) van egy másik fontos tényező, egy nagy teljesítményű alkalmazás blobs használata tervezésekor. Az Azure storage egy tartomány-alapú particionálási sémát használja a méretezés és a terhelés elosztása. Ez a konfiguráció azt jelenti, hogy nyissa meg, hogy hasonló elnevezési konvenciókat vagy az előtagok fájlok egyazon partícióra kerüljenek. A működési elvet alatt feltölteni a fájlok a tároló nevét tartalmazza. Ebben az oktatóanyagban rendelkező GUID neveket is szerint véletlenszerűen generált tartalmat fájlok használata. Majd feltölti öt különböző tárolók véletlenszerű nevekkel.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez végrehajtotta az előző tárolási oktatóanyag: [hozzon létre egy virtuális gép és a méretezhető alkalmazások tárfiók][previous-tutorial].

## <a name="remote-into-your-virtual-machine"></a>A virtuális géppé távoli

A helyi számítógépen a következő parancs segítségével hozzon létre egy távoli asztali munkamenetet a virtuális gép. Cserélje le a virtuális gép nyilvános IP-címét. Amikor a rendszer kéri, adja meg a virtuális gép létrehozásakor használt hitelesítő adatokat.

```
mstsc /v:<publicIpAddress>
```

## <a name="configure-the-connection-string"></a>A kapcsolati karakterlánc konfigurálása

Az Azure portálon lépjen a tárfiókhoz. Válassza ki **hívóbetűk** alatt **beállítások** tárfiókba. Másolás a **kapcsolati karakterlánc** az az elsődleges vagy másodlagos kulcsot. Jelentkezzen be a virtuális gép az előző oktatóanyag létrehozott. Nyissa meg a **parancssor** rendszergazdaként, futtassa a `setx` parancsot a `/m` kapcsoló, a parancs menti egy gép beállítást környezeti változót. A környezeti változó nem érhető el, amíg Ön töltse be újra a **parancssor**. Cserélje le  **\<storageConnectionString\>**  a következő mintában:

```
setx storageconnectionstring "<storageConnectionString>" /m
```

Amikor befejeződött, nyissa meg a másik **parancssor**, keresse meg `D:\git\storage-dotnet-perf-scale-app` és írja be `dotnet build` hozható létre az alkalmazás.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Navigáljon a `D:\git\storage-dotnet-perf-scale-app`.

Típus `dotnet run` az alkalmazás futtatásához. Az első futtatásakor `dotnet` feltölt a helyi csomag gyorsítótár visszaállítási sebesség növelése és a kapcsolat nélküli hozzáférés engedélyezése. Ez a parancs végrehajtásához egy percre foglal, és csak egyszer történik.

```
dotnet run
```

Az alkalmazás öt véletlenszerűen tárolók hoz létre, és megkezdi az átmeneti könyvtárban található fájlok feltöltése a tárfiókba. Az alkalmazás 100 állítja a minimális szálak és a [DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit(v=vs.110).aspx) 100 használatával biztosítható, hogy számos egyidejű kapcsolatok mikor engedélyezése az alkalmazás futtatása.

Szálkezelési és a kapcsolat korlátbeállításai mellett a [BlobRequestOptions](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions?view=azure-dotnet) a a [UploadFromStreamAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.uploadfromstreamasync?view=azure-dotnet) metódus párhuzamossági használja, és tiltsa le az MD5 kivonatoló érvényesítése vannak konfigurálva. A fájlok feltöltése a 100 MB-os blokkok, ez a konfiguráció jobb teljesítményt nyújt, de lehet, hogy költséges használata egy rosszul végrehajtása hálózati, mintha az egész 100 MB-os letiltása sikertelen. a rendszer ismét megkísérli.

|Tulajdonság|Érték|Leírás|
|---|---|---|
|[ParallelOperationThreadCount](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.paralleloperationthreadcount?view=azure-dotnet)| 8| A beállítás feltöltésekor a rendszer blokkolja bontja a blob. A legnagyobb teljesítmény érdekében ezt az értéket magok száma 8 alkalommal kell lennie. |
|[DisableContentMD5Validation](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.disablecontentmd5validation?view=azure-dotnet)| igaz| Ez a tulajdonság letiltja az MD5 kivonatoló feltöltött tartalmat ellenőrzése. Gyorsabb átvitelt letiltja az MD5 ellenőrzést hoz létre. Azonban nem erősíti meg a érvényességi vagy a átvitt fájlok integritásában.   |
|[StorBlobContentMD5](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.storeblobcontentmd5?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_StoreBlobContentMD5)| hamis| Ez a tulajdonság határozza meg, ha az MD5 kivonatoló kiszámítása és a fájl tárolja.   |
| [A RetryPolicy](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.retrypolicy?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_RetryPolicy)| 2 másodperces leállítási 10 maximális ismételt próbálkozás után |Meghatározza, hogy a kérelmek az újrapróbálkozási házirendet. Kapcsolódási hibák újra, akkor a ebben a példában egy [ExponentialRetry](/dotnet/api/microsoft.windowsazure.storage.retrypolicies.exponentialretry?view=azure-dotnet) konfigurálta a 2-második leállásait és 10 az újrapróbálkozások maximális számát. Ez a beállítás akkor fontos, amikor az alkalmazás lekérdezi megközelíti a elérte-e a [blob storage méretezhetőségi célok](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#azure-blob-storage-scale-targets).  |

A `UploadFilesAsync` feladat a következő példa látható:

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
        Console.WriteLine("Iterating in directiory: {0}", uploadPath);
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

A következő példa egy olyan csonkolt alkalmazás kimenet, a Windows rendszeren futó.

```
Created container https://mystorageaccount.blob.core.windows.net/9efa7ecb-2b24-49ff-8e5b-1d25e5481076
Created container https://mystorageaccount.blob.core.windows.net/bbe5f0c8-be9e-4fc3-bcbd-2092433dbf6b
Created container https://mystorageaccount.blob.core.windows.net/9ac2f71c-6b44-40e7-b7be-8519d3ba4e8f
Created container https://mystorageaccount.blob.core.windows.net/47646f1a-c498-40cd-9dae-840f46072180
Created container https://mystorageaccount.blob.core.windows.net/38b2cdab-45fa-4cf9-94e7-d533837365aa
Iterating in directiory: D:\git\storage-dotnet-perf-scale-app\upload
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

Amíg a fájlok feltöltése a tárfiókhoz ellenőrizheti létesített egyidejű kapcsolatok számát. Nyissa meg a **parancssor** és típus `netstat -a | find /c "blob:https"`. Ez a parancs megjeleníti az éppen megnyitott használatával kapcsolatok számát `netstat`. A következő példa bemutatja egy hasonló kimeneti látottaknak futtatásakor az oktatóanyag magát. A példában látható, mivel volt 800 kapcsolatok, nyissa meg, ha a véletlenszerű fájlok feltöltése a tárfiókba. Ez az érték egész fut a feltöltés módosítja. A párhuzamos blokk adattömbök feltöltésével jóval kevesebb a tartalom átviteléhez szükséges időt.

```
C:\>netstat -a | find /c "blob:https"
800

C:\>
```

## <a name="next-steps"></a>Következő lépések

Az adatsorozat két részén megismerte nagy adatmennyiségek véletlenszerű feltöltése a tárfiókba párhuzamosan, például hogy miként:

> [!div class="checklist"]
> * A kapcsolati karakterlánc konfigurálása
> * Az alkalmazás létrehozása
> * Az alkalmazás futtatása
> * Ellenőrizze a kapcsolatok száma

Előzetes a nagy mennyiségű adat letöltését a tárfiók adatsorozat három részét.

> [!div class="nextstepaction"]
> [Nagy mennyiségű tárfiókba párhuzamosan nagy fájlok feltöltése](storage-blob-scalable-app-download-files.md)

[previous-tutorial]: storage-blob-scalable-app-create-vm.md