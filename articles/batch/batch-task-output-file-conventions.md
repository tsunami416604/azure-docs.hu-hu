---
title: A File Conventions-könyvtárral az Azure Storage-feladatok és tevékenységek kimenetének megőrzése a .NET-keretrendszerhez – Azure Batch |} A Microsoft Docs
description: Ismerje meg, hogyan használhatja az Azure Batch File Conventions .NET-kódtára kötegelt feladat és a feladat kimenetének az Azure Storage megőrzése, és a megőrzött kimenet megtekintéséhez az Azure Portalon.
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
ms.openlocfilehash: 4d86a8fcd1dc85ccacea91afe36cb39dabe10464
ms.sourcegitcommit: 7827d434ae8e904af9b573fb7c4f4799137f9d9b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39117594"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>A Batch File Conventions-könyvtárral az Azure Storage-feladatok és tevékenységek adatok megőrzése a .NET-hez 

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Feladat adatmegőrzésre egyik módja a [Azure Batch File Conventions .NET-kódtára][nuget_package]. A File Conventions-könyvtárral egyszerűsíti az Azure Storage-feladat kimeneti adatok tárolásához és lekéréséhez. A feladat és az ügyfél code File Conventions-könyvtárral is használhat &mdash; feladat kódot a fájlokat, és az Ügyfélkód listázása, és kérheti le azokat. A feladat kód segítségével is a tár felsőbb rétegbeli feladatok kimenetének lekéréséhez például a egy [tevékenységfüggőségek](batch-task-dependencies.md) forgatókönyv. 

A File Conventions-könyvtárral kimeneti fájlok lekérése, keresse meg a fájlok egy adott feladat vagy tevékenység azonosítója és célú listázásával. Ön nem ismernie kell a neveket vagy a fájlok helyét. Például használhatja a File Conventions-könyvtárral köztes fájlok egy adott tevékenység listáját, vagy egy adott feladat egy előzetes fájlt.

> [!TIP]
> 2017-05-01-es verzióval kezdődően a Batch szolgáltatás API támogatja a kimeneti adatok megtartását az Azure Storage-feladatok és a Feladatkezelő tevékenységekről, amely a virtuálisgép-konfigurációval létrehozott készletek futtassa. A Batch szolgáltatás API megőrizni a kód, amely létrehoz egy feladatot, és a File Conventions-könyvtárral alternatívájaként funkcionál kimenete egyszerű módszert kínál. Kimenet megőrzése anélkül, hogy az alkalmazást, amelyet a tevékenység fut. frissítse a Batch ügyfélalkalmazások módosíthatja. További információkért lásd: [megőrzése feladat adatokat az Azure Storage a Batch szolgáltatás API](batch-task-output-files.md).
> 
> 

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Mikor használni, feladat kimenet megőrzése File Conventions-könyvtárral?

Az Azure Batch segítségével több feladat kimenetének megőrzése. A File Conventions el ezekben a forgatókönyvekben a legmegfelelőbb:

- Egyszerűen módosíthatja a kódot, amely a feladat fut, a File Conventions-könyvtárral használ a fájlok megőrzéséhez.
- Érdemes az Azure Storage-adatok streamelése a feladat futása közben.
- Szeretné megőrizni az adatokat a felhőszolgáltatás-konfigurációt vagy a virtuálisgép-konfiguráció létrehozott készletek.
- Keresse meg és töltse le a feladat kimeneti fájlok azonosítója vagy célú kell az ügyfélalkalmazás vagy egyéb feladatok, a feladat. 
- Feladat kimenetének megtekintése az Azure Portalon szeretné.

Ha a forgatókönyv eltér a fent felsoroltak, szükség lehet érdemes lehet eltérő megközelítést. További megőrzése tevékenység kimenetének lehetőségekről további információkért lásd: [az Azure Storage-feladatok és tevékenységek kimenetének megőrzése](batch-task-output.md). 

## <a name="what-is-the-batch-file-conventions-standard"></a>Mi a standard szintű Batch File Conventions?

A [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions) egy elnevezési sémát kínál a cél-tárolók és a blob elérési útjának, amelyhez írja a kimeneti fájlt. Az Azure Storage, amelyek igazodnak a File Conventions standard megőrzött fájlok megtekintése az Azure Portalon automatikusan elérhető. A portál felismeri az elnevezési konvenciót, és így tudja megjeleníteni, amelyek igazodnak, fájlok.

A .NET-keretrendszerhez készült File Conventions-könyvtárral automatikusan nevet, a storage-tárolók és a feladat kimeneti fájlokat a File Conventions szabvány szerint. A File Conventions-könyvtárral is biztosít metódusokat lekérdezni a kimeneti fájlok megfelelően Feladatazonosító, tevékenység azonosítója vagy célú Azure Storage-ban.   

Ha .NET eltérő nyelvű fejleszt, is meg lehet valósítani a File Conventions standard saját maga az alkalmazás. További információkért lásd: [kapcsolatban a Batch File Conventions standard](batch-task-output.md#about-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Azure Storage-fiókot kapcsol a Batch-fiókhoz

Megőrizheti a kimeneti adatokat az Azure Storage használatával a File Conventions-könyvtárral, először a Batch-fiókhoz kell kapcsolni egy Azure Storage-fiókot. Ha még nem tette meg, összekapcsolása egy Storage-fiókot a Batch-fiók használatával a [az Azure portal](https://portal.azure.com):

1. Az Azure portálon lépjen Batch-fiókjára. 
2. A **beállítások**válassza **Tárfiók**.
3. Ha Ön még nem rendelkezik a Batch-fiókhoz társított Storage-fiókot, kattintson a **Storage-fiók (nincs)**.
4. Tárfiók kiválasztása a listából az előfizetéshez. A legjobb teljesítmény érdekében használja az Azure Storage-fiók, amely ugyanabban a régióban a Batch-fiókot, ahol a feladatok futnak.

## <a name="persist-output-data"></a>Kimeneti adatok megőrzése

Feladatok és tevékenységek kimeneti adatok a File Conventions-könyvtárral is fennállnak, hozzon létre egy tárolót az Azure Storage-ban, majd kimenetét mentse el a tárolóhoz. Használja a [Azure Storage ügyféloldali kódtára a .NET-hez](https://www.nuget.org/packages/WindowsAzure.Storage) a feladat kódban, hogy a tevékenység kimenetének feltöltése a tárolóba. 

Tárolók és blobok az Azure Storage használatával kapcsolatos további információkért lásd: [.NET használatával az Azure Blob storage használatának első lépései](../storage/blobs/storage-dotnet-how-to-use-blobs.md).

> [!WARNING]
> Összes feladatok és tevékenységek kimenetének a File Conventions könyvtár ugyanabban a tárolóban tárolják az állandó. Feladatok nagy számú megpróbál egy időben, a fájlok megőrzéséhez [szabályozási korlátait tárolási](../storage/common/storage-performance-checklist.md#blobs) is kényszeríthető.
> 
> 

### <a name="create-storage-container"></a>Storage-tároló létrehozása

Az Azure Storage-feladat kimenetének megőrzése, először hozzon létre egy tárolót meghívásával [CloudJob][net_cloudjob].[ PrepareOutputStorageAsync][net_prepareoutputasync]. Ez a bővítmény metódus egy [CloudStorageAccount] [ net_cloudstorageaccount] paraméterként objektum. Létrehoz egy tárolót a File Conventions szabvány szerint, hogy a tartalmát az Azure portal által felderíthető és a cikk későbbi részében ismertetett lekérés módszerek.

Általában helyezni a kódot egy tároló létrehozásához az ügyfélalkalmazásban található &mdash; az alkalmazás, amely létrehozza a készletek, feladatok és tevékenységek.

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

### <a name="store-task-outputs"></a>Store feladatok kimenete

Most, hogy előkészítette az Azure Storage-tároló, feladatok mentheti kimeneti a tároló használatával a [TaskOutputStorage] [ net_taskoutputstorage] osztály a File Conventions-könyvtárral található.

A feladat-kódba először hozzon létre egy [TaskOutputStorage] [ net_taskoutputstorage] objektumot, majd amikor a feladat befejezését a munkahelyi hívja a [TaskOutputStorage] [ net_taskoutputstorage]. [SaveAsync] [ net_saveasync] kimenetét mentése az Azure Storage-metódus.

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

A `kind` paraméterében a [TaskOutputStorage](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx).[ SaveAsync](https://msdn.microsoft.com/library/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx) metódus kategorizálja a megőrzött fájlok. Az előre meghatározott négy [TaskOutputKind] [ net_taskoutputkind] típusok: `TaskOutput`, `TaskPreview`, `TaskLog`, és `TaskIntermediate.` azt is megadhatja a kimeneti egyéni kategóriákkal.

Ezek a típusok kimeneti adja meg, milyen típusú listázásához, előfordulhat, hogy később a Batch a megőrzött egy adott feladat kimenetének a kimenetek teszi lehetővé. Más szóval amikor a tevékenység kimeneti listázza, szűrheti listájában a kimeneti típus egyikét. Például ", adja meg a *előzetes* tevékenység kimeneti *109*." További információ az ajánlati és kimenetek beolvasása megjelenik [beolvasni a kimeneti](#retrieve-output) a cikkben.

> [!TIP]
> A kimenet típusa is meghatározza, hogy az Azure Portalon egy adott fájl helyére: *TaskOutput*-kategorizált fájlok meg fog jelenni **tevékenység kimeneti fájlok**, és *TaskLog* fájlok meg fog jelenni **naplók feladat**.
> 
> 

### <a name="store-job-outputs"></a>Feladatkimenetek Store

Tárolás, a feladat kimeneti mellett a kimeneteket, egy teljes feladathoz hozzárendelt is tárolhatja. Például az egyesítés a feladatban movie renderelési feladat sikerült arcot a teljes körűen megjelenített filmet a feladat kimeneteként. Amikor a feladat befejeződött, az ügyfélalkalmazás listában, és beolvasni a feladat a kimeneteket, és nem kell az egyes feladatok lekérdezése.

Feladat kimenetének Store meghívásával a [JobOutputStorage][net_joboutputstorage].[ SaveAsync] [ net_joboutputstorage_saveasync] metódust, és adja meg a [JobOutputKind] [ net_joboutputkind] és fájlnév:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

A a **TaskOutputKind** írja be a feladat kimeneti, használja a [JobOutputKind] [ net_joboutputkind] írja be a projekt csoportosítását a megőrzött fájlok. Ez a paraméter lehetővé teszi újabb lekérdezés (lista) egy adott típusú kimenet. A **JobOutputKind** típusú kimenet és az előzetes verzió is beleértve, és egyéni kategóriákkal létrehozását támogatja.

### <a name="store-task-logs"></a>A feladat naplók Store

Mellett megőrzése egy fájlt a tartós tárolási, a feladat vagy a feladat befejezésekor, szükség lehet egy feladat végrehajtása közben frissített fájlok megtartása &mdash; naplófájlok vagy `stdout.txt` és `stderr.txt`, például. Erre a célra az Azure Batch File Conventions-könyvtárral biztosít a [TaskOutputStorage][net_taskoutputstorage].[ SaveTrackedAsync] [ net_savetrackedasync] metódust. A [SaveTrackedAsync][net_savetrackedasync], frissítések nyomon követése egy fájlba a csomóponton (időközönként egy Ön által megadott), és ezeket a frissítéseket az Azure Storage megőrzése.

A következő kódrészletet a használunk [SaveTrackedAsync] [ net_savetrackedasync] frissíteni `stdout.txt` az Azure Storage-15 másodpercenként a feladat végrehajtása közben:

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

A megjegyzésekkel szakasz `Code to process data and produce output file(s)` a kódot, amely a feladat általában végre helyőrzője. Előfordulhat például, hogy kódot, amely letölti az adatokat az Azure Storage-ból, és hajtja végre az átalakítási mód vagy a számítási rajta. Ez a kódrészlet fontos része a bemutatásához, hogyan futtathatja az ilyen kód egy `using` rendszeresen frissíteni az olyan fájlt blokk [SaveTrackedAsync][net_savetrackedasync].

A csomóponti ügynök egy olyan program, a készlet minden csomópontján lefut, és a csomópont és a Batch szolgáltatás között a parancs és vezérlés felületet biztosít. A `Task.Delay` hívás szükség a végén `using` letiltása, hogy a csomóponti ügynök idő standard kimeneti tartalmának ürítése a csomóponton a stdout.txt fájl legyen-e. Ez a késleltetés nélkül, hagyja ki a kimenet az elmúlt pár másodperc. Ez a késleltetés nem lehet szükség az összes fájl.

> [!NOTE]
> Ha engedélyezi a nyomkövetési fájl **SaveTrackedAsync**, csak *hozzáfűzi* a nyomon követett fájl tárolja az Azure Storage. Ezt a módszert csak a naplófájlok nem forgó vagy egyéb fájlok, a fájl végén műveletek hozzáfűzésére írt nyomon követése.
> 
> 

## <a name="retrieve-output-data"></a>Kimeneti adatainak beolvasása

A megőrzött kimenet az Azure Batch File Conventions-könyvtárral használatával kérheti le, ha mégis így a feladat - és feladat-központú módon. A kimenet kérheti a megadott feladat vagy a feladat nem ismeri az Azure Storage, vagy még a fájl nevét a elérési útvonalát. Ehelyett kérheti a kimeneti fájlok feladat vagy feladat-azonosítót.

Az alábbi kódrészlet végighalad a feladatok, a feladat kimeneti fájljait némi információt jelenít meg, és ezután letölti a fájlokat a Storage-ból.

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

## <a name="view-output-files-in-the-azure-portal"></a>Kimeneti fájlok megtekintése az Azure Portalon

Az Azure portal megjeleníti a feladat kimeneti fájljait, és naplóival, amelyeket a rendszer megőrzi a egy társított Azure Storage-fiók használatával a [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). Meg lehet valósítani a konvenciók saját maga a nyelv az Ön által választott, vagy használhatja a File Conventions-könyvtárral, a .NET-alkalmazásokban.

Ahhoz, hogy a megjelenített, a kimeneti fájlok a portálon, akkor a következő követelményeknek kell megfelelnie:

1. [Az Azure Storage-fiókot kapcsol](#requirement-linked-storage-account) a Batch-fiókjához.
2. Amikor kimenetek megőrzése tartaniuk az előre meghatározott, a Storage-tárolók és a fájlok elnevezési konvencióinak. A File Conventions-könyvtárral találhatja meg a definíció az alábbi egyezmények [információs][github_file_conventions_readme]. Használatakor a [Azure Batch File Conventions] [ nuget_package] kódtárat a kimenet megőrzése File Conventions szabvány szerinti tárolja a fájlokat.

A feladat kimeneti fájljait és naplók megtekintése az Azure Portalon, keresse meg a feladat kimenete is érdeklik, majd kattintson vagy **mentett kimeneti fájlok** vagy **mentett naplók**. Az ábra a **mentett kimeneti fájlok** "007" Azonosítójú feladat számára:

![A feladat kimenetek panel az Azure Portalon][2]

## <a name="code-sample"></a>Kódminta

A [PersistOutputs] [ github_persistoutputs] mintaprojektet az egyik a [Azure Batch-kódminta] [ github_samples] a Githubon. A Visual Studio-megoldás bemutatja, hogyan használhatja az Azure Batch File Conventions-könyvtárral tartós tárolási, a feladat kimenetének megőrzése. A minta futtatásához kövesse az alábbi lépéseket:

1. Nyissa meg a projektet a **Visual Studio 2017**.
2. Adja hozzá a Batch- és Storage **fiók hitelesítő adatai** való **AccountSettings.settings** Microsoft.Azure.Batch.Samples.Common projektben.
3. **Build** (de ne futtassa) a megoldás. Ha a rendszer kéri, állítsa vissza a NuGet-csomagok.
4. Töltse fel az Azure portal használatával egy [alkalmazáscsomag](batch-application-packages.md) a **PersistOutputsTask**. Tartalmazza a `PersistOutputsTask.exe` és annak függő szerelvényei a .zip csomag "PersistOutputsTask" kívánt alkalmazás Azonosítóját és az "1.0" alkalmazáscsomag-verzió.
5. **Indítsa el** (Futtatás) a **PersistOutputs** projekt.
6. Amikor a rendszer kéri, válassza ki a minta futtatásához használni, adja meg a megőrzést technológiát **1** , futtassa a mintát, feladat kimenet megőrzése File Conventions-könyvtárral segítségével. 

## <a name="next-steps"></a>További lépések

### <a name="get-the-batch-file-conventions-library-for-net"></a>A Batch File Conventions-könyvtárral lekérése a .NET-hez

A .NET-hez készült Batch File Conventions-könyvtárral érhető el az [NuGet][nuget_package]. A könyvtár kibővíti a [CloudJob] [ net_cloudjob] és [CloudTask] [ net_cloudtask] osztályok új módszerekkel. További tájékoztatás a [referenciadokumentációt](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files) for File Conventions-könyvtárral.

A [forráskódját] [ github_file_conventions] a File Conventions számára érhető el kódtár a Microsoft Azure SDK for .NET tárházban a githubon. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>Egyéb megközelítések megőrzése kimeneti adatainak felfedezése

- Lásd: [az Azure Storage-feladatok és tevékenységek kimenetének megőrzése](batch-task-output.md) áttekintését, feladat és a feladat adatait.
- Lásd: [megőrzése feladat adatokat az Azure Storage a Batch szolgáltatás API](batch-task-output-files.md) megtudhatja, hogyan a Batch szolgáltatás API-ja segítségével megőrizheti a kimeneti adatokat.

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

[1]: ./media/batch-task-output/task-output-01.png "Mentett kimeneti fájlok és a mentett naplókat választók portálon"
[2]: ./media/batch-task-output/task-output-02.png "A feladat kimenetek panel az Azure Portalon"
