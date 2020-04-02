---
title: Kimeneti adatok megőrzése az Azure Storage számára a .NET fájlkonvenciók könyvtárával – Azure Batch
description: Megtudhatja, hogy miként használhatja az Azure Batch File Conventions library for .NET használatával a batch feladat & az Azure Storage-kimenet megőrzéséhez, és tekintse meg ezt a kimenetet az Azure Portalon.
services: batch
documentationcenter: .net
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: labrenne
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e331dcfc9acf7a5e8a83be788b566cf92eaeb8f4
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548047"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>Feladat- és feladatadatok megőrzése az Azure Storage szolgáltatásban a .NET batch file konvenciói könyvtárával

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

A feladatadatok megőrzésének egyik módja az [Azure Batch-fájlkonvenciók könyvtárának használata a .NET számára.][nuget_package] A fájlkonvenciók könyvtár leegyszerűsíti a feladatkimeneti adatok azure storage-ba való tárolásának és lekérésének folyamatát. A fájlkonvenciók könyvtárat feladat- és &mdash; ügyfélkódban is használhatja a fájlok megőrzéséhez, az ügyfélkódban pedig a listalistához és a beolvasáshoz. A feladatkód a tár segítségével is lekérheti a felső-folyási tevékenységek kimenetét, például egy [tevékenységfüggőségi](batch-task-dependencies.md) forgatókönyvben.

A fájlkonvenciók könyvtárral rendelkező kimeneti fájlok lekéréséhez megkeresheti egy adott feladat vagy feladat fájljait, ha azonosító és cél szerint listázja őket. Nem kell tudnia a fájlok nevét vagy helyét. A Fájlkonvenciók könyvtár segítségével például felsorolhatja az adott feladathoz szükséges összes köztes fájlt, vagy beszerezhet egy előnézeti fájlt egy adott feladathoz.

> [!TIP]
> A 2017-05-01-es verziótól kezdve a Batch szolgáltatás API-ja támogatja a továbbra is megőrzött kimeneti adatokat az Azure Storage számára a virtuális gép konfigurációjával létrehozott készleteken futó feladatokhoz és feladatkezelő feladatokhoz. A Batch szolgáltatás API-ja egyszerű módot biztosít a kimenet megőrzésére a kódon belül, amely létrehoz egy feladatot, és a fájlkonvenciók könyvtár alternatívájaként szolgál. Módosíthatja a Batch-ügyfélalkalmazásokat a kimenet megőrzése érdekében anélkül, hogy frissítenie kellene a feladatot futtató alkalmazást. További információ: [Feladatadatok megőrzése az Azure Storage-ba a Batch service API-val.](batch-task-output-files.md)

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>Mikor használhatom a Fájlkonvenciók könyvtárat a feladatkimenet megőrzésére?

Az Azure Batch több módon is biztosít feladatkimenet megőrzéséhez. A fájlegyezmények a legalkalmasabbak az alábbi forgatókönyvekhez:

- Könnyedén módosíthatja annak az alkalmazásnak a kódját, amelyen a feladat fut, hogy a Fájlkonvenciók könyvtár használatával megtartsa a fájlokat.
- Adatokat szeretne streamelni az Azure Storage-ba, amíg a feladat még fut.
- Meg szeretné tartani az adatokat a felhőszolgáltatás-konfigurációval vagy a virtuálisgép-konfigurációval létrehozott készletekből.
- Az ügyfélalkalmazásnak vagy a feladat ban lévő egyéb feladatoknak azonosító vagy cél szerint kell megtalálniuk és letölteniük a feladatkimeneti fájlokat.
- A feladatkimenetet az Azure Portalon szeretné megtekinteni.

Ha a forgatókönyv eltér a fent felsoroltaktól, előfordulhat, hogy más megközelítést kell figyelembe vennie. A feladat kimenetének megőrzésére vonatkozó egyéb lehetőségekről a [Feladat és a feladat kimenetének megőrzése az Azure Storage-ba](batch-task-output.md)című témakörben talál további információt.

## <a name="what-is-the-batch-file-conventions-standard"></a>Mi a batch file egyezmények szabvány?

A [Batch File Conventions szabvány](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files) elnevezési sémát biztosít a céltárolók és blob elérési utak, amelyekhez a kimeneti fájlok vannak írva. Az Azure Storage-ban megőrzött, a fájlkonvenciók szabványnak megfelelő fájlok automatikusan megtekinthetők az Azure Portalon. A portál tisztában van az elnevezési konvencióval, így megjelenítheti a hozzá tartozó fájlokat.

A .NET fájlkonvenciók könyvtára automatikusan elnevezi a tárolótárolókat és a feladatkimeneti fájlokat a Fájlkonvenciók szabványnak megfelelően. A fájlkonvenciók könyvtár is rendelkezik módszerek lekérdezése kimeneti fájlokat az Azure Storage-ban a feladat azonosító, feladat azonosító vagy a cél szerint.

Ha a .NET-től eltérő nyelven fejleszt, a fájlkonvenciók szabványt saját maga is megvalósíthatja az alkalmazásban. További információ: [Implement the Batch File Conventions standard](batch-task-output.md#implement-the-batch-file-conventions-standard).

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Azure Storage-fiók összekapcsolása a Batch-fiókkal

A kimeneti adatok megőrzése az Azure Storage-ban a fájlkonvenciók könyvtár használatával, először csatolnia kell egy Azure Storage-fiókot a Batch-fiókhoz. Ha még nem tette meg, kapcsolja össze a Storage-fiókot a Batch-fiókkal az [Azure Portal](https://portal.azure.com)használatával:

1. Az Azure portálon lépjen Batch-fiókjára.
1. A **Beállítások csoportban**válassza **a Tárfiók lehetőséget.**
1. Ha még nem rendelkezik Storage-fiókkal a Batch-fiókhoz társítva, kattintson **a Tárfiók (Nincs)** elemre.
1. Válasszon ki egy tárfiókot az előfizetés listájából. A legjobb teljesítmény érdekében használjon egy Azure Storage-fiókot, amely ugyanabban a régióban van, mint a Batch-fiók, ahol a feladatok futnak.

## <a name="persist-output-data"></a>Kimeneti adatok megőrzése

A feladat és a feladat kimeneti adatok megőrzése a fájlkonvenciók könyvtár, hozzon létre egy tárolót az Azure Storage-ban, majd mentse a kimenetet a tárolóba. Használja az [Azure Storage-ügyfélkódakódban a .NET az Azure Storage-ügyfélkódban](https://www.nuget.org/packages/WindowsAzure.Storage) a feladatkimenet feltöltéséhez.

A tárolók és blobok Azure Storage-ban való használatáról az [Azure Blob storage](../storage/blobs/storage-dotnet-how-to-use-blobs.md)használatával .

> [!WARNING]
> A fájlkonvenciók könyvtárában megőrzött összes feladat- és feladatkimenet ugyanabban a tárolóban van tárolva. Ha nagy számú feladat próbálja meg egyszerre megtartani a fájlokat, előfordulhat, hogy az Azure Storage szabályozási korlátokat kényszerít. A sávszélesség-szabályozási korlátokról a [Blob storage teljesítmény- és méretezhetőségi ellenőrzőlistája című témakörben talál](../storage/blobs/storage-performance-checklist.md)további információt.

### <a name="create-storage-container"></a>Storage-tároló létrehozása

Az Azure Storage feladatkimenetének megőrzéséhez először hozzon létre egy tárolót a [CloudJob][net_cloudjob]hívásával. [PrepareOutputStorageAsync][net_prepareoutputasync]. Ez a bővítménymetódus paraméterként egy [CloudStorageAccount][net_cloudstorageaccount] objektumot vesz fel. Létrehoz egy tárolót a fájlkonvenciók szabvány nak megfelelően, hogy annak tartalmát az Azure Portal és a cikk későbbi részében tárgyalt lekérési módszerek felderítheti.

Általában helyezze el a kódot, hogy hozzon létre egy tárolót az ügyfélalkalmazásban &mdash; az alkalmazás, amely létrehozza a készletek, feladatok és feladatok.

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

### <a name="store-task-outputs"></a>Feladatkimenetek tárolása

Most, hogy készített egy tárolót az Azure Storage-ban, a feladatok mentheti a kimenetet a tárolóba a File Conventions könyvtárban található [TaskOutputStorage][net_taskoutputstorage] osztály használatával.

A feladatkódban hozzon létre egy [TaskOutputStorage][net_taskoutputstorage] objektumot, majd amikor a feladat befejeződött, hívja meg a [TaskOutputStorage][net_taskoutputstorage]-ot. [SaveAsync][net_saveasync] metódust az Azure Storage kimenetének mentéséhez.

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

A `kind` [TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage)paramétere. [A SaveAsync](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) metódus kategorizálja a megőrzött fájlokat. Négy előre definiált [TaskOutputKind][net_taskoutputkind] `TaskOutput`típus `TaskPreview` `TaskLog`létezik: , , , és `TaskIntermediate.` egyéni kimeneti kategóriákat is definiálhat.

Ezek a kimeneti típusok lehetővé teszik annak megadását, hogy milyen típusú kimeneteket kell felsorolni, amikor később lekérdezi a Batch-et egy adott feladat megőrzött kimeneteihez. Más szóval, amikor egy feladat kimeneteit sorolja fel, szűrheti a listát az egyik kimeneti típusra. Például: "Add ide a *109-es*feladat *előzetes* kimenetét." A kimenetek listázásáról és lekérésével kapcsolatos további információk a cikk későbbi kimenetének lekérése című részében jelennek meg.

> [!TIP]
> A kimeneti fajta azt is meghatározza, hogy az Azure Portalon hol jelenik meg egy adott fájl: *TaskOutput*-kategorizált fájlok jelennek meg **a Feladatkimeneti fájlok**csoportban , *a TaskLog-fájlok* pedig a **Feladatnaplók**csoportban.

### <a name="store-job-outputs"></a>Tárolt feladat kimenetek

A feladatkimenetek tárolása mellett a teljes feladathoz társított kimeneteket is tárolhatja. Egy mozgókép-renderelési feladat egyesítési feladatában például a teljesen renderelt mozgóképet feladatkimenetként is megtarthatja. A feladat befejezése után az ügyfélalkalmazás listázhatja és lekérheti a feladat kimeneteit, és nem kell lekérdeznie az egyes feladatokat.

A feladat kimenetének tárolása a [JobOutputStorage][net_joboutputstorage]hívásával. [SaveAsync][net_joboutputstorage_saveasync] metódust, és adja meg a [JobOutputKind][net_joboutputkind] és a fájlnév:

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

A feladatkimenetek **TaskOutputKind** típusához ugyanúgy használhatja a [JobOutputKind][net_joboutputkind] típust, amely a feladat megőrzött fájljait kategorizálja. Ez a paraméter lehetővé teszi, hogy később lekérdezegy (lista) egy adott típusú kimenet. A **JobOutputKind** típus tartalmazza a kimeneti és az előzetes verzió kategóriák, és támogatja az egyéni kategóriák létrehozását.

### <a name="store-task-logs"></a>Feladatnaplók tárolása

A mellett, hogy egy feladatot vagy feladatot egy feladat vagy feladat befejezésekor tartós tárolóvá kell &mdash; tenni, `stdout.txt` előfordulhat, hogy meg kell persistnia a feladatnaplófájlok végrehajtása során frissített fájlokat, vagy `stderr.txt`például. Ebből a célból az Azure Batch File Conventions könyvtár biztosítja a [TaskOutputStorage.][net_taskoutputstorage] [SaveTrackedAsync][net_savetrackedasync] metódus. A [SaveTrackedAsync][net_savetrackedasync]segítségével nyomon követheti a csomóponton lévő fájl frissítéseit (a megadott időközönként), és megőrizheti az Azure Storage frissítéseit.

A következő kódrészletben a [SaveTrackedAsync][net_savetrackedasync] használatával `stdout.txt` 15 másodpercenként frissítjük az Azure Storage-ot a feladat végrehajtása során:

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

A megjegyzéssel `Code to process data and produce output file(s)` végzett szakasz annak a kódnak a helyőrzője, amelyet a feladat általában végrehajtana. Például előfordulhat, hogy olyan kódot, amely letölti az adatokat az Azure Storage-ból, és átalakítást vagy számítást hajt végre rajta. A kódrészlet fontos része, hogy bemutatja, hogyan csomagolhatja be az ilyen kódot egy `using` blokkba, hogy rendszeresen frissítsen egy fájlt a [SaveTrackedAsync segítségével.][net_savetrackedasync]

A csomópontügynök egy olyan program, amely a készlet minden csomópontján fut, és biztosítja a parancs-és vezérlő felületet a csomópont és a Batch szolgáltatás között. A `Task.Delay` hívás a `using` blokk végén szükséges annak biztosításához, hogy a csomópontügynöknek legyen ideje a szabvány tartalmát kiürülni a csomópont stdout.txt fájljába. E késleltetés nélkül kilehet hagyni a kimenet utolsó néhány másodpercét. Előfordulhat, hogy nem minden fájl esetében van szükség erre a késleltetésre.

> [!NOTE]
> Ha engedélyezi a fájlkövetést a **SaveTrackedAsync használatával,** csak a nyomon követett fájlhoz *való hozzáfűzések* maradnak meg az Azure Storage-hoz. Ezt a módszert csak nem forgó naplófájlok vagy más, a fájl végére történő hozzáfűzési műveletekhez írt fájlok nyomon követésére használja.

## <a name="retrieve-output-data"></a>Kimeneti adatok beolvasása

Ha lekéri a megőrzött kimenetet az Azure Batch-fájlkonvenciók könyvtár használatával, ezt feladat- és feladatközpontú módon teszi. Kérheti a kimenetet az adott feladathoz vagy feladathoz anélkül, hogy ismernie kellene az elérési útját az Azure Storage-ban, vagy akár a fájlnevét. Ehelyett feladat- vagy feladatazonosító szerint kérhet kimeneti fájlokat.

A következő kódrészlet végighalad a feladat feladatain, kinyomtat néhány információt a feladat kimeneti fájljairól, majd letölti a fájlokat a Storage-ból.

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

Az Azure Portal on feladat kimeneti fájlokat és naplókat, amelyek megmaradnak egy csatolt Azure Storage-fiók a [Batch File egyezmények szabvány.](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions) Ezeket a konvenciókat saját maga is megvalósíthatja az Ön által választott nyelven, vagy használhatja a Fájlkonvenciók könyvtárat a .NET alkalmazásokban.

Ahhoz, hogy engedélyezze a kimeneti fájlok megjelenítését a portálon, meg kell felelnie az alábbi követelményeknek:

1. Azure Storage-fiók összekapcsolása a Batch-fiókkal.
1. A kimenetek megőrzésekénések esetén tartsa be a tárolótárolók és -fájlok előre meghatározott elnevezési konvencióit. Ezeknek az egyezményeknek a meghatározását a [README][github_file_conventions_readme]fájlkonvenciók könyvtárban találja. Ha az [Azure Batch-fájl konvenciók][nuget_package] könyvtár at a kimenet megőrzése, a fájlok megmaradnak a fájlkonvenciók szabvány szerint.

A feladatkimeneti fájlok és naplók megtekintéséhez az Azure Portalon keresse meg azt a feladatot, amelynek kimenete érdekli, majd kattintson a **Mentett kimeneti fájlok** vagy a **Mentett naplók**elemre. Ezen a képen a "007" azonosítóval ellátott feladat **mentett kimeneti fájljai** láthatók:

![Feladatkimenetek panel az Azure Portalon][2]

## <a name="code-sample"></a>Kódminta

A [PersistOutputs][github_persistoutputs] mintaprojekt az [egyik Az Azure Batch-kód minták][github_samples] a GitHubon. Ez a Visual Studio-megoldás bemutatja, hogyan használhatja az Azure Batch File Conventions könyvtárat a feladatkimenet tartós tároláshoz való megőrzéséhez. A minta futtatásához hajtsa végre az alábbi lépéseket:

1. Nyissa meg a projektet a **Visual Studio 2019-ben.**
2. Adja hozzá a Batch és **storage-fiók hitelesítő adatait** az **AccountSettings.settings-hoz** a Microsoft.Azure.Batch.Samples.Common projektben.
3. **Build (de** nem fut) a megoldást. Ha a rendszer kéri, állítsa vissza a NuGet-csomagokat.
4. Az Azure Portal használatával töltsön fel egy [alkalmazáscsomagot](batch-application-packages.md) a **PersistOutputsTask számára.** A `PersistOutputsTask.exe` .zip csomagba foglalja bele a és a függő szerelvényeket, állítsa az alkalmazásazonosítót "PersistOutputsTask"-ra, az alkalmazáscsomag verzióját pedig "1.0"-ra.
5. **Indítsa el** (futtassa) a **PersistOutputs projektet.**
6. Amikor a rendszer kéri a minta futtatásához használandó adatmegőrzési technológia kiválasztását, írja be az **1** értéket a minta futtatásához a Fájlkonvenciók könyvtár használatával a feladatkimenet megőrzéséhez. 

## <a name="next-steps"></a>További lépések

### <a name="get-the-batch-file-conventions-library-for-net"></a>A .NET kötegfájl-konvenciók könyvtárának beszereznie

A .NET batch file konvenciói könyvtára elérhető a [NuGet][nuget_package]. A könyvtár új módszerekkel bővíti a [CloudJob][net_cloudjob] és [a CloudTask][net_cloudtask] osztályokat. Lásd még a Fájlkonvenciók könyvtár [referenciadokumentációját.](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files)

A fájlkonvenciók könyvtár [forráskódja][github_file_conventions] elérhető a GitHubon a Microsoft Azure SDK for .NET repository-ban. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>A továbbra is megőrzött kimeneti adatok egyéb megközelítései

- A továbbra is fennálló feladat- és feladatadatok áttekintése a feladat [és a feladat kimenetének megőrzése az Azure Storage-ba](batch-task-output.md) című témakörben olvashat.
- Tekintse [meg a feladatadatok megőrzése az Azure Storage-ban a Batch szolgáltatás API-val](batch-task-output-files.md) megtudhatja, hogyan használhatja a Batch szolgáltatás API-t a kimeneti adatok megőrzéséhez.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://docs.microsoft.com/java/api/com.microsoft.azure.storage.cloudstorageaccount
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/batch/Microsoft.Azure.Batch.Conventions.Files/README.md
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
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "Mentett kimeneti fájlok és mentett naplók választók a portálon"
[2]: ./media/batch-task-output/task-output-02.png "Feladatkimenetek panel az Azure Portalon"
