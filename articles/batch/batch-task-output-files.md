---
title: Kimeneti adatok megőrzése az Azure Storage számára a Batch service API-val - Azure Batch
description: Ismerje meg, hogyan használhatja a Batch szolgáltatás API-t a batch feladat és a kimeneti adatok azure storage-ba való megőrzése.
services: batch
author: LauraBrenner
manager: evansma
editor: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 03/05/2019
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 11bd8bc427dd3da35ec5aa0f728f6b04b7d4527d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022851"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Feladatadatok megőrzése az Azure Storage számára a Batch szolgáltatás API-jával

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

A Batch szolgáltatás API támogatja a továbbra is megőrzött kimeneti adatokat az Azure Storage feladatok és feladatkezelő feladatok, amelyek futnak a virtuális gép konfigurációját tartalmazó készleteken. Amikor hozzáad egy feladatot, megadhatja az Azure Storage-tároló, mint a feladat kimenetének célhelye. A Batch szolgáltatás ezután minden kimeneti adatot ír a tárolóba, amikor a feladat befejeződött.

A Batch szolgáltatás API-jának a feladatkimenet megőrzéséhez való használatának előnye, hogy nem kell módosítania a feladatot futtató alkalmazást. Ehelyett az ügyfélalkalmazás néhány módosításával megőrizheti a feladat kimenetét ugyanabból a kódból, amely létrehozza a feladatot.

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Mikor használhatom a Batch szolgáltatás API-ját a feladatkimenet megőrzéséhez?

Az Azure Batch több módon is biztosít feladatkimenet megőrzéséhez. A Batch szolgáltatás API-jának használata egy kényelmes megközelítés, amely a legjobban megfelel az alábbi forgatókönyvek:

- Szeretne kódot írni a feladat kimenetének megőrzéséhez az ügyfélalkalmazáson belül, a feladat által futtatott alkalmazás módosítása nélkül.
- Meg szeretné tartani a kötegelt feladatok és a feladatkezelő feladatok kimenetét a virtuális gép konfigurációjával létrehozott készletekben.
- Szeretné megtartani a kimenetet egy tetszőleges nevű Azure Storage-tárolóba.
- Meg szeretné tartani a kimenetet egy Azure Storage-tárolón, amely a [Batch File Conventions szabvány](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions)nak megfelelően van elnevezve. 

Ha a forgatókönyv eltér a fent felsoroltaktól, előfordulhat, hogy más megközelítést kell figyelembe vennie. Például a Batch szolgáltatás API jelenleg nem támogatja a streamelési kimenet az Azure Storage-ba, amíg a feladat fut. Kimenet streameléséhez használja a .NET számára elérhető Batch File Conventions könyvtárat. Más nyelvek esetén saját megoldást kell megvalósítania. A feladat kimenetének megőrzésére vonatkozó egyéb lehetőségekről a [Feladat és a feladat kimenetének megőrzése az Azure Storage-ba](batch-task-output.md)című témakörben talál további információt.

## <a name="create-a-container-in-azure-storage"></a>Tároló létrehozása az Azure Storage-ban

Az Azure Storage feladatkimenetének megőrzéséhez létre kell hoznia egy tárolót, amely a kimeneti fájlok célhelyeként szolgál. A feladat futtatása előtt hozza létre a tárolót, lehetőleg a feladat elküldése előtt. A tároló létrehozásához használja a megfelelő Azure Storage-ügyfélkódtár vagy SDK. Az Azure Storage API-król az [Azure Storage dokumentációjában](https://docs.microsoft.com/azure/storage/)olvashat bővebben.

Ha például az alkalmazást C#-ban írja, használja az [Azure Storage ügyfélkódtárját a .NET számára.](https://www.nuget.org/packages/WindowsAzure.Storage/) A következő példa bemutatja, hogyan hozhat létre tárolót:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>Megosztott hozzáférési aláírás beszereznie a tárolóhoz

A tároló létrehozása után szerezzen be egy közös hozzáférési aláírást (SAS) a tárolóírási hozzáféréssel. A SAS delegált hozzáférést biztosít a tárolóhoz. A SAS hozzáférést biztosít egy megadott engedélykészlettel és egy megadott időintervallumon keresztül. A Batch szolgáltatásnak írási engedéllyel rendelkező SAS-ra van szüksége a feladatkimenet tárolóba írásához. A SAS szolgáltatásról további információt a [SAS \(\) használata az Azure Storage-ban című témakörben talál.](../storage/common/storage-dotnet-shared-access-signature-part-1.md)

Amikor az Azure Storage API-k használatával SAS-t kap, az API egy SAS-jogkivonat-karakterláncot ad vissza. Ez a jogkivonat-karakterlánc tartalmazza a SAS összes paraméterét, beleértve az engedélyeket és a SAS érvényességi időközét. A SAS használatával egy tároló azure storage-ban, hozzá kell fűzni a SAS-jogkivonat-karakterlánc ot az erőforrás URI-hoz. The resource URI, together with the appended SAS token, provides authenticated access to Azure Storage.

A következő példa bemutatja, hogyan lehet beszerezni egy csak írható SAS-jogkivonat-karakterláncot a tárolóhoz, majd hozzáfűzi a SAS-t a tároló URI-jához:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Kimeneti fájlok megadása a feladatkimenethez

Ha kimeneti fájlokat szeretne megadni egy feladathoz, hozzon létre [egy OutputFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) objektumokból álló gyűjteményt, és rendelje hozzá a [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) tulajdonsághoz a feladat létrehozásakor.

A következő C# kód példa létrehoz egy feladatot, `output.txt`amely véletlenszerű számokat ír egy fájlba, amelynek neve . A példa létrehoz egy `output.txt` kimeneti fájlt a tárolóba írandó. A példa kimeneti fájlokat is létrehoz minden `std*.txt` olyan naplófájlhoz, `stderr.txt`amely megfelel a fájlmintának (_pl._ és `stdout.txt` a ) A tároló URL-címéhez szükség van a sas, amely korábban létrehozott a tárolóhoz. A Batch szolgáltatás a SAS segítségével hitelesíti a tárolóhoz való hozzáférést:

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

### <a name="specify-a-file-pattern-for-matching"></a>Fájlminta megadása az egyeztetéshez

Kimeneti fájl megadásakor a [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) tulajdonsággal adhat meg egy egyező fájlmintát. A fájlminta megegyezhet a nulla fájllal, egyetlen fájllal vagy a feladat által létrehozott fájlkészletével.

A **FilePattern** tulajdonság támogatja a szabványos `*` fájlrendszer helyettesítő karaktereket, például `**` (nem rekurzív egyezések esetén) és (rekurzív egyezések esetén). A fenti kódminta például megadja a `std*.txt` nem rekurzív módon egyező fájlmintát:

`filePattern: @"..\std*.txt"`

Egyetlen fájl feltöltéséhez adjon meg egy helyettesítő karakterek nélküli fájlmintát. A fenti kódminta például megadja a `output.txt`következő fájlmintát:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Feltöltési feltétel megadása

A [OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) tulajdonság lehetővé teszi a kimeneti fájlok feltételes feltöltését. Gyakori forgatókönyv, hogy feltölt egy fájlkészletet, ha a feladat sikeres, és egy másik fájlkészletet, ha nem sikerül. Előfordulhat például, hogy részletes naplófájlokat szeretne feltölteni, ha a feladat sikertelen, és nem nulla kilépési kóddal lép ki. Hasonlóképpen előfordulhat, hogy csak akkor szeretné feltölteni az eredményfájlokat, ha a feladat sikeres, mivel ezek a fájlok hiányozhatnak vagy hiányosak lehetnek, ha a feladat sikertelen.

A fenti kódminta az **UploadCondition** tulajdonságot TaskCompletion tulajdonságra **állítja.** Ez a beállítás azt adja meg, hogy a program a feladatok befejezése után töltse fel a fájlt, függetlenül a kilépési kód értékétől.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

További beállításokat a [OutputFileUploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) enum című témakörben találja.

### <a name="disambiguate-files-with-the-same-name"></a>Az azonos nevű fájlok kicsúbásítása

A feladatban lévő feladatok azonos nevű fájlokat hozhatnak létre. Például, `stdout.txt` `stderr.txt` és jönnek létre minden feladatot, hogy fut egy feladatban. Mivel minden feladat a saját környezetében fut, ezek a fájlok nem ütköznek a csomópont fájlrendszerén. Ha azonban több feladatból tölt fel fájlokat egy megosztott tárolóba, az azonos nevű fájlokat kell eloszlatnia.

A [OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) tulajdonság a kimeneti fájlok célblobját vagy virtuális könyvtárát adja meg. A **Path** tulajdonság használatával elnevezheti a blobot vagy a virtuális könyvtárat oly módon, hogy az azonos nevű kimeneti fájlok egyedi neve az Azure Storage-ban. A feladatazonosító használata az elérési úton jó módja az egyedi nevek biztosításának és a fájlok egyszerű azonosításának.

Ha a **FilePattern** tulajdonság helyettesítő kifejezésre van állítva, akkor a mintának megfelelő összes fájl feltöltődik a **Path** tulajdonság által megadott virtuális könyvtárba. Ha például a `mycontainer`tároló a , `mytask`a feladatazonosítója `..\std*.txt`, és a fájlminta a , majd az Azure Storage kimeneti fájljainak abszolút URI-i hasonlóak lesznek:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Ha a **FilePattern** tulajdonság egyetlen fájlnévnek felel meg, ami azt jelenti, hogy nem tartalmaz helyettesítő karaktereket, akkor a **Path** tulajdonság értéke megadja a teljesen minősített blob nevet. Ha több feladat egyetlen fájljával ütközik az elnevezési ütközések, akkor a fájlnév részeként adja meg a virtuális könyvtár nevét a fájlok félrekuktusához. Állítsa be például, hogy a **Path** tulajdonság tartalmazza a feladatazonosítót, a határolókaraktert (általában egy perjelet) és a fájlnevet:

`path: taskId + @"/output.txt"`

A feladatok kimeneti fájljainak abszolút URI-i hasonlóak lesznek:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Az Azure Storage virtuális könyvtárairól a [Blobok listázása egy tárolóban](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container)című témakörben talál további információt.

## <a name="diagnose-file-upload-errors"></a>Fájlfeltöltési hibák diagnosztizálása

Ha a kimeneti fájlok feltöltése az Azure Storage-ba sikertelen, majd a feladat átkerül a **Befejezett** állapotba, és a [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) tulajdonság be van állítva. Vizsgálja meg a **FailureInformation** tulajdonságot, és állapítsa meg, hogy milyen hiba történt. Itt például egy hiba történik a fájlfeltöltéskor, ha a tároló nem található:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

A Batch minden fájlfeltöltéskor két naplófájlt ír `fileuploadout.txt` a `fileuploaderr.txt`számítási csomópontra, és . Ezeket a naplófájlokat megvizsgálhatja, ha többet szeretne megtudni egy adott hibáról. Azokban az esetekben, amikor a fájl feltöltését soha nem kísérelték meg, például azért, mert maga a feladat nem futtatva nem tudott futni, akkor ezek a naplófájlok nem fognak létezni.

## <a name="diagnose-file-upload-performance"></a>Fájlfeltöltési teljesítmény diagnosztizálása

A `fileuploadout.txt` fájlnaplók feltöltési folyamatait. Megvizsgálhatja ezt a fájlt, ha többet szeretne megtudni arról, hogy mennyi ideig tart a fájlfeltöltések. Ne feledje, hogy a feltöltési teljesítmény számos tényezőt eredményez, beleértve a csomópont méretét, a feltöltés időpontjában a csomóponton végzett egyéb tevékenységeket, azt, hogy a céltároló ugyanabban a régióban van-e, mint a Batch készlet, hány csomópontot töltenek fel a tárfiók ban egy időben, és így tovább.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>A Batch service API használata a Batch File Conventions szabványsal

Ha a Batch szolgáltatás API-jával megőrzi a feladatkimenetet, tetszés szerint elnevezheti a céltárolót és a blobokat. Azt is választhatja, hogy a [Batch File Conventions szabvány](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions)nak megfelelően nevezze el őket. A fájlkonvenciók szabvány határozza meg a céltároló és blob az Azure Storage-ban egy adott kimeneti fájl neve alapján a feladat és a feladat. Ha a fájlkonvenciók szabványt használja a kimeneti fájlok elnevezéséhez, akkor a kimeneti fájlok megtekinthetők az [Azure Portalon.](https://portal.azure.com)

Ha c#-ban fejleszt, használhatja a [.NET Batch File Conventions könyvtárába épített módszereket.](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files) Ez a könyvtár létrehozza a megfelelően elnevezett tárolók és blob elérési utak az Ön számára. Meghívhat például az API-t, hogy a feladat neve alapján megkapja a megfelelő nevet a tárolóhoz:

```csharp
string containerName = job.OutputStorageContainerName();
```

A [CloudJobExtensions.GetOutputStorageStorageUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) metódus használatával egy megosztott hozzáférésű aláírás (SAS) URL-címet adhat vissza, amely a tárolóba írásra szolgál. Ezután adja át ezt a SAS-t a [OutputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) konstruktornak.

Ha a C#-tól eltérő nyelven fejleszt, akkor saját magának kell végrehajtania a Fájlegyezmények szabványt.

## <a name="code-sample"></a>Kódminta

A [PersistOutputs][github_persistoutputs] mintaprojekt az [egyik Az Azure Batch-kód minták][github_samples] a GitHubon. Ez a Visual Studio-megoldás bemutatja, hogyan használható a Batch ügyfélkódtár a .NET a feladatkimenet tartós tárolón való megőrzéséhez. A minta futtatásához hajtsa végre az alábbi lépéseket:

1. Nyissa meg a projektet a **Visual Studio 2019-ben.**
2. Adja hozzá a Batch és **storage-fiók hitelesítő adatait** az **AccountSettings.settings-hoz** a Microsoft.Azure.Batch.Samples.Common projektben.
3. **Build (de** nem fut) a megoldást. Ha a rendszer kéri, állítsa vissza a NuGet-csomagokat.
4. Az Azure Portal használatával töltsön fel egy [alkalmazáscsomagot](batch-application-packages.md) a **PersistOutputsTask számára.** A `PersistOutputsTask.exe` .zip csomagba foglalja bele a és a függő szerelvényeket, állítsa az alkalmazásazonosítót "PersistOutputsTask"-ra, az alkalmazáscsomag verzióját pedig "1.0"-ra.
5. **Indítsa el** (futtassa) a **PersistOutputs projektet.**
6. Amikor a rendszer kéri, hogy válassza ki a minta futtatásához használandó adatmegőrzési technológiát, írja be a **2** értéket a minta futtatásához a Batch szolgáltatás API-jával a feladatkimenet megőrzéséhez.
7. Ha szükséges, futtassa újra a mintát, **3** megadásával a kimenet a Batch szolgáltatás API-val, valamint a céltároló és a blob elérési útja elnevezéséhez a fájlkonvenciók szabvány nak megfelelően.

## <a name="next-steps"></a>További lépések

- A .NET fájlkonvenciók könyvtárával a [feladat- és feladatadatok megőrzése az Azure Storage szolgáltatásba a Batch File Conventions könyvtárral](batch-task-output-file-conventions.md)című témakörben talál további információt.
- Az Azure Batch-ben a kimeneti adatok megőrzésének egyéb megközelítéseiről a [Feladat és a feladat kimenetének megőrzése az Azure Storage-ba című témakörben talál.](batch-task-output.md)

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
