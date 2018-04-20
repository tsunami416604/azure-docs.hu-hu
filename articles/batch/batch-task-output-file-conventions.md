---
title: Feladat- és kimeneti, a fájl egyezmények könyvtárhoz Azure Storage .NET - Azure Batch fennállnak |} Microsoft Docs
description: Útmutató Azure Batch fájl egyezmények .NET-keretrendszerhez készült Azure Storage feladat és a feladat kimenete kötegelt, és a megőrzött eredményének megtekintéséhez az Azure portálon.
services: batch
documentationcenter: .net
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bbfb40b3740f9ea43df327a01ba6f4cf52d80457
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2018
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net-to-persist"></a>A kötegelt fájl egyezmények könyvtárhoz megőrizni a .NET-keretrendszerhez készült Azure Storage feladat- és adatok megőrzése 

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Egy módja feladat adatok megőrzéséhez a [Azure Batch fájl egyezmények .NET-keretrendszerhez készült][nuget_package]. A fájl egyezmények könyvtár egyszerűbbé teszi a tevékenység kimeneti adatok Azure Storage tárolására és beolvasására azt. Használhatja a fájl egyezmények könyvtárra, a feladat- és ügyfél-kód &mdash; tárolásakor fájlok feladat kódját, és a Ügyfélkód listában, és kérheti le azokat. A feladat kódot is használatával a szalagtár fölérendelt feladatot, kimeneti többek között a egy [függőségek feladat](batch-task-dependencies.md) forgatókönyv. 

A fájl egyezmények könyvtárhoz kimenete fájlok lekéréséhez, keresse meg a fájlok egy adott feladat vagy tevékenység ehhez listázza azokat azonosítója és a cél. Nem kell tudja, hogy a nevek vagy a fájlok helyét. Például a fájl egyezmények könyvtár használata egy adott tevékenység köztes fájlok listáját, vagy egy adott feladat preview fájl.

> [!TIP]
> 2017-05-01 verziójától kezdve, a Batch szolgáltatás API támogatja tárolásakor kimeneti adatok Azure Storage és a virtuálisgép-konfiguráció létre készletek futó feladat manager feladatok. A Batch szolgáltatás API megőrizni a kimenet a kód, amely feladatot hoz létre, és a fájl egyezmények könyvtár alternatívájaként szolgál egyszerű módszert kínál. A kötegelt ügyfélalkalmazások megőrizni a kimenet anélkül, hogy frissítse az alkalmazást, amely a feladat futásának módosíthatja. További információkért lásd: [megőrző feladat adatok Azure Storage a kötegelt API szolgáltatás](batch-task-output-files.md).
> 
> 

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Ha használja a fájl egyezmények könyvtár megőrizni a feladat kimenete?

Az Azure Batch megőrizni a feladat kimenete egynél több módot nyújt. A fájl egyezmények ezekkel az esetekkel a legmegfelelőbb:

- A feladat futásának megőrizni a fájlok a fájl egyezmények szalagtárat használó alkalmazás számára a kód könnyen módosíthatja.
- A feladat futása közben szeretné adatfolyam adatok Azure Storage.
- Szeretné megőrizni a készletek a felhőalapú szolgáltatás konfigurációja vagy a virtuálisgép-konfiguráció adatait.
- Keresse meg és töltse le a tevékenység kimeneti fájlok azonosító vagy célú kell az ügyfélalkalmazás vagy egyéb feladatok, a feladat. 
- A feladat kimenetének megtekintése az Azure portálon szeretné.

Ha adott esetben eltér a fent felsorolt, esetleg érdemes lehet egy másik módszert. Más beállításokat a tárolásakor feladat kimenetének további információkért lásd: [megőrizni a feladat- és kimeneti Azure Storage](batch-task-output.md). 

## <a name="what-is-the-batch-file-conventions-standard"></a>Mi az a kötegelt fájl egyezmények szabványos?

A [kötegelt fájl egyezmények standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) egy elnevezési sémát biztosít a cél-tárolók és a blob elérési utak, amelyhez a kimeneti fájlok készültek. Azure Storage, amely igazodik fájl egyezmények normál megőrzött fájlok automatikusan elérhető megtekinthető az Azure portálon. A portál tudomást az elnevezési konvenciót, és így megjeleníthetik a fájlokat, csatlakozhat hozzá.

A fájl egyezmények .NET-keretrendszerhez készült automatikusan nevet, a tároló- és tevékenység kimeneti fájlokat a szabványos fájl konvenciók szerint. A fájl egyezmények könyvtár is lekérdezése az Azure Storage megfelelően Feladatazonosító, Tevékenységazonosító vagy célja a kimenő fájlok metódusokat biztosít.   

Ha .NET eltérő nyelvű fejleszt, is létrehozható fájl egyezmények normál saját magának az alkalmazásban. További információkért lásd: [kapcsolatos a kötegelt fájl egyezmények standard](batch-task-output.md#about-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Egy Azure Storage-fiók összekapcsolása a Batch-fiók

Megőrizni a kimeneti adatok Azure Storage a fájl egyezmények kódtár használatával, először a Batch-fiókhoz kell kapcsolni egy Azure Storage-fiókot. Még nem tette meg, ha egy Storage-fiók összekötése a Batch-fiók használatával a [Azure-portálon](https://portal.azure.com):

1. Az Azure portálon lépjen Batch-fiókjára. 
2. A **beállítások**, jelölje be **Tárfiók**.
3. Ha még nem rendelkezik egy tárfiókot, a Batch-fiókhoz társított, kattintson a **(nincs) Storage-fiók**.
4. Válasszon egy tárfiókot a listából az előfizetéséhez. A legjobb teljesítmény érdekében használjon egy Azure Storage-fiókot, amely ugyanabban a régióban a Batch-fiók, amelyen fut a feladat.

## <a name="persist-output-data"></a>Kimeneti adatok megőrzése

Megőrizni a feladat- és kimeneti adatokat a fájl egyezmények könyvtárhoz, az Azure Storage tárolókat hozhat létre, majd kimenetét mentse a tárolóhoz. Használja a [Azure Storage ügyféloldali kódtára a .NET](https://www.nuget.org/packages/WindowsAzure.Storage) a feladat kódban a feladat kimenete feltölteni a tárolóba. 

A tárolók és blobok az Azure Storage használatáról további információk: [az Azure Blob storage .NET használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Feladat- és mindegyikhez állandó, a fájl egyezmények könyvtárban tárolják ugyanabban a tárolóban. Ha nagyszámú feladatok megőrizni a fájlok egy időben, próbálja [szabályozás korlátok tárolási](../storage/common/storage-performance-checklist.md#blobs) is kényszeríthető.
> 
> 

### <a name="create-storage-container"></a>A tároló létrehozása

A feladat kimenetének Azure Storage megőrizni, először létre kell hoznia egy tárolót meghívásával [CloudJob][net_cloudjob].[ PrepareOutputStorageAsync][net_prepareoutputasync]. A bővítmény metódus egy [CloudStorageAccount] [ net_cloudstorageaccount] objektum paraméterként. Hoz, hogy a tartalma az Azure portál által felderíthető fájl egyezmények szabvány szerinti nevű tárolót, és az a cikk későbbi részében ismertetett lekérdezési módszerek.

Általában helyezi-e a kód egy tároló létrehozásához az ügyfélalkalmazás az &mdash; az a készletek, a feladatok és a feladatok létrehozó alkalmazást.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>Tárolási feladatok kimenete

Most, hogy az Azure Storage tárolója előkészítése után, feladatokat is mentése kimeneti a tároló segítségével a [TaskOutputStorage] [ net_taskoutputstorage] osztály a fájl egyezmények könyvtárban található.

A feladat kódban, először létre kell hoznia egy [TaskOutputStorage] [ net_taskoutputstorage] objektumot, majd a tevékenységeket a feladat befejezése után hívja a [TaskOutputStorage][net_taskoutputstorage].[ SaveAsync] [ net_saveasync] mentése az Azure Storage metódust.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

A `kind` paramétere a [TaskOutputStorage](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx).[ SaveAsync](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx) metódus kategorizálja a megőrzött fájlok. Nincsenek négy előre definiált [TaskOutputKind] [ net_taskoutputkind] típusok: `TaskOutput`, `TaskPreview`, `TaskLog`, és `TaskIntermediate.` kimeneti egyéni kategóriáinak is meghatározhat.

Kimeneti típusaival engedélyezi, hogy adja meg, milyen típusú kimenetek előfordulhat, hogy később kötegelt a megőrzött egy adott feladat kimenetének a listát. Ez azt jelenti Ha egy feladat kimenetének felsorolja, végezhet listáról a kimeneti típusú. Például ", adja meg a *előzetes* tevékenység kimeneti *109*." Több listázása és kimenetének beolvasása megjelenik [lekérnie a kimenetet](#retrieve-output) a cikk későbbi.

> [!TIP]
> A kimeneti típust is meghatározza, hogy az Azure portálon egy adott fájl helyére: *TaskOutput*-kategorizált fájlok alatt szerepelhet **tevékenység kimeneti fájlok**, és *TaskLog* fájlok jelennek meg a **naplók feladat**.
> 
> 

### <a name="store-job-outputs"></a>Feladat kimenetének tárolásához

Tárolás, a feladat kimenetének létrehozása mellett egy teljes feladattal társított kimenetek is tárolhatja. Például az egyesítés a feladatban movie megjelenítési feladat, sikerült megőrizni a teljes megjelenített movie a feladat kimenete. Ha a feladat befejeződött, az ügyfélalkalmazás listában, és a feladat kimenetének beolvasása, és nem kell az egyes feladatok lekérdezni.

Tárolja a feladat kimenetére meghívásával a [JobOutputStorage][net_joboutputstorage].[ SaveAsync] [ net_joboutputstorage_saveasync] metódust, és adja meg a [JobOutputKind] [ net_joboutputkind] és fájlnév:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

Például a **TaskOutputKind** típus feladatot kimenetek, használja a [JobOutputKind] [ net_joboutputkind] kategorizálásának egy feladat típusa a megőrzött fájlok. Ez a paraméter lehetővé teszi később (lista) egy adott típusú kimeneti. A **JobOutputKind** típusú kimeneti és a kép tartalmazza, és támogatja az egyéni kategóriák.

### <a name="store-task-logs"></a>A feladat naplók tárolására

Mellett megőrzése a tartós tárolási egy fájlt egy feladat vagy a feladat befejezését, szükség lehet megőrizni a feladat végrehajtása közben fájlok &mdash; naplófájlok vagy `stdout.txt` és `stderr.txt`, például. Erre a célra az Azure Batch fájl egyezmények kódtár biztosít a [TaskOutputStorage][net_taskoutputstorage].[ SaveTrackedAsync] [ net_savetrackedasync] metódust. A [SaveTrackedAsync][net_savetrackedasync], nyomon követheti a frissítések a csomópont (a megadott időközönként) fájlba, és ezek a frissítések Azure Storage megőrzéséhez.

A következő kódrészletet használjuk [SaveTrackedAsync] [ net_savetrackedasync] frissítése `stdout.txt` az Azure Storage 15 másodpercenként a feladat végrehajtása során:

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

A megjegyzésként szakasz `Code to process data and produce output file(s)` helyőrzőjeként szolgál a kódot, amely a feladat általában hajtaná végre. Például lehetséges, hogy kódot, amely letölti az adatokat az Azure Storage és átalakítás vagy számítási végez azt. A fontos része a részlet van bemutatásához, hogyan akkor futtathatja a kódok egy `using` blokk rendszeresen frissíteni a fájl [SaveTrackedAsync][net_savetrackedasync].

A csomópont-ügynök egy olyan program, a készlet minden egyes csomópontján fut, és a parancs-és-ellenőrzés felületet, a csomópont és a Batch szolgáltatás között. A `Task.Delay` tekintendő, amely szükséges a végén `using` blokk ellenőrizze, hogy a csomópont-ügynök van-e az idő a standard out tartalmát kiüríteni a stdout.txt fájlba a csomóponton. Ez a késleltetés nélkül is lehet, amelyeken a kimenet utolsó néhány másodpercig. Ez a késés nem lehet szükség az összes fájl.

> [!NOTE]
> Ha engedélyezi a nyomkövetési fájl **SaveTrackedAsync**, csak *hozzáfűzi* a nyomon követett fájl megmaradnak az Azure-tárhelyre. Ezt a módszert csak a nyomkövetési naplófájlok nem elforgatása vagy egyéb fájlok, a műveletek hozzáfűzésére a fájl végére írni.
> 
> 

## <a name="retrieve-output-data"></a>Kimeneti adatainak beolvasása

Beolvasni a megőrzött kimeneti az Azure Batch fájl egyezmények kódtár használatával, ha ezt teszi feladat - és a feladat-központú módon. A kimeneti kérhet a megadott feladat vagy a feladatot anélkül, hogy az elérési Azure Storage, vagy még a fájl nevét. Ehelyett kérheti a kimeneti fájlok feladat, vagy sikertelen feladat-azonosítót.

A következő kódrészletet telepítéseket egy feladat tevékenységeit, kiírja a kimeneti fájlok a következő feladathoz kapcsolatos információkat és tölti le a fájlok a tároló.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="view-output-files-in-the-azure-portal"></a>Az Azure portálon kimeneti fájlok megtekintése

Az Azure-portálon jeleníti meg a tevékenység kimeneti fájlok és a naplófájlokat, amelyek megmaradnak a kapcsolt Azure Storage használatával a [kötegelt fájl egyezmények standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). Megvalósíthat szabályoknak saját kezűleg a a egy nyelvet a választott, vagy a fájl egyezmények könyvtár a .NET-alkalmazásokban használható.

Ahhoz, hogy a kimeneti fájlok, a portál megjelenését, az alábbi követelményeknek kell megfelelnie:

1. [Egy Azure Storage-fiók csatolása](#requirement-linked-storage-account) a Batch-fiókhoz.
2. Előre definiált tároló- és a fájlok elnevezési szabályai csatlakozhat, ha kimenetek megőrzése. Ezek egyezmények meghatározása a fájl egyezmények könyvtárban található [információs][github_file_conventions_readme]. Ha használja a [Azure Batch fájl egyezmények] [ nuget_package] megőrizni a kimeneti könyvtár fájl egyezmények szabvány szerinti őrződnek meg a fájlok.

Az Azure portálon tevékenység kimeneti fájlok és a naplók megtekintéséhez nyissa meg a tevékenység, amelynek kimenete érdekli, majd kattintson **mentett kimeneti fájlok** vagy **mentett naplókat**. A képen látható a **mentett kimeneti fájlok** "007" azonosítójú feladat számára:

![A feladat kimenetének panel az Azure-portálon][2]

## <a name="code-sample"></a>Kódminta

A [PersistOutputs] [ github_persistoutputs] mintaprojektet egyike a [Azure Batch-Kódminták] [ github_samples] a Githubon. A Visual Studio megoldás bemutatja, hogyan használható az Azure Batch fájl egyezmények könyvtár továbbra is fennáll feladatkimenet tartós tárhelyre. A minta futtatásához kövesse az alábbi lépéseket:

1. Nyissa meg a projektet a **Visual Studio 2015-ös vagy újabb**.
2. A kötegelt és a tárolási **fiók hitelesítő adatait** való **AccountSettings.settings** a Microsoft.Azure.Batch.Samples.Common projektben.
3. **Build** (de ne futtassa) a megoldás. Ha a rendszer kéri, állítsa vissza a NuGet-csomagok.
4. Töltse fel az Azure-portál használatával egy [alkalmazáscsomag](batch-application-packages.md) a **PersistOutputsTask**. Tartalmazza a `PersistOutputsTask.exe` és a függő szerelvényeket a .zip-csomagja "PersistOutputsTask" az alkalmazás-Azonosítót, és az alkalmazáscsomag verzióját "1.0".
5. **Start** (Futtatás) a **PersistOutputs** projekt.
6. Amikor a rendszer kéri a minta futtatásához használni, adja meg a adatmegőrzési technológiát válasszon **1** megőrizni a feladat kimenete a fájl egyezmények könyvtár használata a minta futtatásához. 

## <a name="next-steps"></a>További lépések

### <a name="get-the-batch-file-conventions-library-for-net"></a>A kötegelt fájl egyezmények könyvtár beolvasása a .NET-hez

A kötegelt fájl egyezmények .NET-keretrendszerhez készült érhető el a [NuGet][nuget_package]. A könyvtár bővíti a [CloudJob] [ net_cloudjob] és [CloudTask] [ net_cloudtask] osztályok új metódusával. Lásd még: a [dokumentáció](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) a fájl egyezmények könyvtára.

A [forráskód] [ github_file_conventions] a fájl egyezmények könyvtár érhető el a Microsoft Azure SDK for .NET tárház a githubon. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Más megoldások tárolásakor kimeneti adatok felfedezése

- Lásd: [megőrizni a feladat- és kimeneti Azure Storage](batch-task-output.md) áttekintését a feladat és a feladat adatait.
- Lásd: [megőrző feladat adatok Azure Storage a kötegelt API szolgáltatás](batch-task-output-files.md) a kimeneti adatok megőrzéséhez a Batch szolgáltatás API használata.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: http://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Mentett kimeneti fájlokat és mentett naplókat választók portálon"
[2]: ./media/batch-task-output/task-output-02.png "A feladat kimenetének panel az Azure-portálon"
