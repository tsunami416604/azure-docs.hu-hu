---
title: A Batch szolgáltatásban API – Azure Batch az Azure Storage-feladatok és tevékenységek kimenetének megőrzése |} A Microsoft Docs
description: Ismerje meg, hogyan kötegelt feladat és a feladat kimenetének az Azure Storage megőrzése Batch szolgáltatás API használatával.
services: batch
author: laurenhughes
manager: jeconnoc
editor: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 77d8504dba180cfb56807344e659d6b22231f931
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56866640"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>A feladat adatmegőrzésre, az Azure Storage a Batch szolgáltatás API-val

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

A Batch szolgáltatás API támogatja a kimeneti adatok megtartását az Azure Storage-feladatok és a Feladatkezelő tevékenységekről, amely a virtuális gép konfigurációja rendelkező készletek futtassa. Amikor hozzáad egy feladatot, is megadhat egy tárolót az Azure Storage-ban a feladat kimenetének célhelyeként. A Batch szolgáltatás a feladat befejezésekor majd ír kimeneti adatokat tároló.

A Batch szolgáltatás API használatával a feladat kimenetének megőrzése előnye, hogy, nem szükséges módosítania az alkalmazást, amelyet a tevékenység fut-e. Ehelyett az ügyfélalkalmazásnak néhány módosításokkal megőrizheti belül ugyanazt a kódot, amely létrehozza a feladat kimenete a feladatot.

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Ha használja a Batch szolgáltatás API, a feladat kimenetének megőrzése?

Az Azure Batch segítségével több feladat kimenetének megőrzése. A Batch szolgáltatás API-val egy kényelmes megközelítést, amely a leginkább megfelelő megoldást ezekben a forgatókönyvekben a következő:

- Szeretné megőrizni az ügyfél alkalmazáson belül a tevékenység kimenetének, hogy a feladat fut az alkalmazás módosítása nélkül kód írása.
- Szeretné megőrizni a kimenet a Batch-feladatok és a virtuálisgép-konfigurációval létrehozott készletek a Feladatkezelő tevékenységekről.
- Szeretné megőrizni a kimenet egy Azure Storage-tárolóhoz, az egy tetszőleges nevet.
- Egy Azure Storage-tárolóba, a következők szerint nevű kimenet megőrzése szeretné a [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). 

> [!NOTE]
> A Batch szolgáltatás API nem támogatja a felhő-konfigurációval létrehozott készletek futtatott feladatok az adatok megtartását. Persisting feladat fut a cloud services-konfiguráció készletek kimenete kapcsolatos információkért lásd: [Adatmegőrzésre feladatok és tevékenységek az Azure Storage a .NET-hez megőrizni a Batch File Conventions-könyvtárral való](batch-task-output-file-conventions.md).

Ha a forgatókönyv eltér a fent felsoroltak, szükség lehet érdemes lehet eltérő megközelítést. Például a Batch szolgáltatás API-ja jelenleg nem támogatja az Azure Storage-streamelési kimeneti a feladat futása közben. A kimeneti stream, érdemes lehet a Batch File Conventions-könyvtárral, a .NET-hez elérhető. Más nyelven kell a saját megoldást valósíthat meg. További megőrzése tevékenység kimenetének lehetőségekről további információkért lásd: [az Azure Storage-feladatok és tevékenységek kimenetének megőrzése](batch-task-output.md).

## <a name="create-a-container-in-azure-storage"></a>Hozzon létre egy tárolót az Azure Storage-ban

Az Azure Storage-feladat kimeneti megőrizni, kell hozzon létre egy tároló, amely kiszolgálja a kimeneti fájlok célhelyeként. Hozza létre a tárolót, a feladat, mielőtt a feladat elküldése lehetőleg futtatása előtt. A tároló létrehozásához használja a megfelelő Azure Storage ügyféloldali kódtár vagy az SDK-t. Az Azure Storage API-k kapcsolatos további információkért lásd: a [Azure Storage-dokumentáció](https://docs.microsoft.com/azure/storage/).

Például, ha az alkalmazás a C#-ban, használja a [Azure Storage ügyféloldali kódtára a .NET-hez](https://www.nuget.org/packages/WindowsAzure.Storage/). Az alábbi példa bemutatja, hogyan hozhat létre egy tárolót:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>A tároló egy közös hozzáférésű jogosultságkód igénylése

Miután létrehozta a tárolót, ismerje meg a tároló közös hozzáférésű jogosultságkód (SAS) írási hozzáférése. SAS a tároló delegált hozzáférést biztosít. A SAS egy meghatározott engedélyekkel és a egy adott idő alatt a hozzáférést. A Batch szolgáltatás a feladat kimenetének írni a tároló írási jogosultsággal rendelkező SAS van szüksége. SAS kapcsolatos további információkért lásd: [a közös hozzáférésű jogosultságkódot \(SAS\) az Azure Storage-ban](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Ha egy SAS-t az Azure Storage API-k, az API-t egy SAS-token karakterláncot adja vissza. A jogkivonat-karakterláncot a SAS, beleértve az engedélyeket és a SAS érvényességi időtartama az összes paramétert tartalmaz. A SAS használatával az Azure Storage-tároló eléréséhez, fűzze hozzá a SAS jogkivonat-karakterláncot az erőforrás URI-t kell. Az erőforrás URI-t, a hozzáfűzött SAS-jogkivonat együtt hitelesített hozzáférést biztosít az Azure Storage.

Az alábbi példa bemutatja, hogyan tehet szert egy csak írási SAS jogkivonat-karakterláncot a tároló, majd az SAS fűz a tároló URI:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>Adja meg a feladat kimenetét a kimeneti fájlok

Adja meg a kimeneti fájlokat egy feladathoz, hozzon létre egy gyűjteményt, [OutputFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) objektumokat, majd rendelje hozzá a [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) tulajdonság a feladat létrehozásakor.

A következő C# példakód létrehoz egy feladatot, amely véletlenszerű számok nevű fájlba írja `output.txt`. A példa létrehoz egy kimeneti fájl, amely `output.txt` a tárolóra írható. A példában is létrehoz minden fájlminta egyező naplófájl a kimeneti fájlok `std*.txt` (_például_, `stdout.txt` és `stderr.txt`). A tároló URL-címe van szüksége a létrehozott SAS korábban a tárolót. A Batch szolgáltatás a SAS használatával hitelesíti a hozzáférést a tárolóhoz:

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

### <a name="specify-a-file-pattern-for-matching"></a>Adjon meg egy Fájlmintát egyeztetéséhez

Ha megad egy kimeneti fájl, használhatja a [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) tulajdonsággal ad meg egy Fájlmintát egyeztetéséhez. A fájl minta előfordulhat, hogy egyezik nulla fájlokat, egy adott fájl vagy a tevékenység által létrehozott fájlokat.

A **FilePattern** a tulajdonság támogatja a szabványos fájlrendszer helyettesítő karakterek például `*` (a nem rekurzív megegyezik) és `**` (a rekurzív egyezik). Például adja meg a fenti kódmintában a fájl egyeztetendő mappaminta `std*.txt` nem rekurzív módon:

`filePattern: @"..\std*.txt"`

Egyetlen fájl feltöltéséhez, adja meg egy Fájlmintát nincs helyettesítő karaktereket. Például adja meg a fenti kódmintában a fájl egyeztetendő mappaminta `output.txt`:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Adjon meg egy feltöltési feltétel

A [OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) tulajdonság lehetővé teszi, hogy a kimeneti fájlok feltételes feltöltése. Gyakran előfordul, hogy töltse fel egy fájlt, ha a feladat sikeres volt, és fájlokat külön készletét, ha sikertelen. Például érdemes, részletes naplófájlok feltöltése csak akkor lehetséges, ha a feladat sikertelen lesz, és kilép egy nullától eltérő kilépési kódot. Hasonlóképpen érdemes eredmény fájlok feltöltése csak akkor, ha a feladat sikeres volt, mivel ezeket a fájlokat esetleg hiányoznak vagy hiányosak, ha a feladat sikertelen.

A fenti csoportok kódmintát a **UploadCondition** tulajdonságot **TaskCompletion**. Ez a beállítás megadja, hogy a fájl tölthető fel. a kilépési kód értékét függetlenül a feladatok befejezése után.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Az egyéb beállításokat, tekintse meg a [OutputFileUploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) enum.

### <a name="disambiguate-files-with-the-same-name"></a>Elem egyértelműségének biztosításához az azonos nevű fájl

A feladat tevékenységei készíthetnek, ugyanazzal a névvel rendelkező fájlokat. Ha például `stdout.txt` és `stderr.txt` minden feladat fut egy feladat jön létre. Mivel mindegyik tevékenység a saját környezetében fut, ezeket a fájlokat a fájlrendszerben a csomópont nem ütköznek. Azonban amikor feltölti a fájlokat, ha több tevékenység egy megosztott tárolóba, kell értelmezni az azonos nevű fájl.

A [OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) tulajdonság határozza meg a cél blob vagy virtuális könyvtárat a kimeneti fájlokat. Használhatja a **elérési út** tulajdonság a blob vagy úgy, hogy ugyanazzal a névvel a fájlok egyedi neve az Azure Storage szolgáltatásban virtuális könyvtár neve. A feladat azonosítója az elérési út használata segítségével győződjön meg, hogy egyedi neveket, és könnyedén megállapíthatja a fájlokat.

Ha a **FilePattern** tulajdonsága helyettesítő karakteres kifejezést, majd az összes fájl, amely megfelel a mintának a megadott virtuális könyvtár feltöltésekor a rendszer a **elérési út** tulajdonság. Például, ha a tároló `mycontainer`, a feladat-azonosító `mytask`, és a fájl minta `..\std*.txt`, majd a abszolút URI-k, a kimeneti fájlok az Azure Storage hasonló lesz:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Ha a **FilePattern** tulajdonsága egyezik meg egyetlen fájl nevét, ami azt jelenti, nem tartalmaz helyettesítő karaktereket, majd az értékét a **elérési út** tulajdonság határozza meg a teljes blob neve. Ha várhatóan nem felel meg, ha több tevékenység egyetlen fájl a elnevezési, majd a félreérthetőség ezeket a fájlokat a fájlnév részét képező virtuális könyvtár neve is. Például állítsa be a **elérési út** tulajdonság tartalmazza a feladat azonosítója, az elválasztó karaktert (általában perjellel) és a fájl nevét:

`path: taskId + @"/output.txt"`

Az abszolút URI-k, a kimeneti fájlok feladatokhoz tartozó hasonló lesz:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Az Azure Storage szolgáltatásban virtuális könyvtárak kapcsolatos további információkért lásd: [a tárolóban lévő blobok listázása](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).

## <a name="diagnose-file-upload-errors"></a>Fájl feltöltése hibák diagnosztizálása

Kimeneti fájlok feltöltése az Azure Storage sikertelen, akkor a tevékenység áthelyezése a **befejezve** állapota és a [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) tulajdonsága. Vizsgálja meg a **FailureInformation** tulajdonság határozza meg, milyen hiba történt. Például a következő hiba, amely a fájl feltöltése akkor fordul elő, ha a tároló nem található:

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Minden fájl feltöltése a Batch két jelentkezzen be a fájlokat a számítási csomópontra ír `fileuploadout.txt` és `fileuploaderr.txt`. További információ a konkrét hiba a naplófájlok ellenőrizheti. Azokban az esetekben, ahol a fájl feltöltése soha nem kísérlet történt, például mert maga a feladat nem sikerült futtatni majd ezek a naplófájlok nem létezik.

## <a name="diagnose-file-upload-performance"></a>Fájlok feltöltése teljesítménye diagnosztizálása

A `fileuploadout.txt` fájlba naplózza a feltöltési folyamat. Ehhez a fájlhoz, tudjon meg többet arról, hogy mennyi ideig tart a fájlok feltöltése ellenőrizheti. Ne feledje, hogy számos befolyásoló meghatározó tényező feltölteni a teljesítményt, például a csomópont, más tevékenység a csomóponton a feltöltés időpontjában mérete, a cél tároló és a Batch-készlet, hány csomóponttal tölt fel, a stora ugyanabban a régióban van-e a GE-fiókját az egy időben, és így tovább.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>A Batch szolgáltatás API használata a Batch File Conventions standard

Feladat kimenete a Batch szolgáltatás API-val szűnik meg, ha a cél tároló nevet adhat, és azonban, például blobok. Azt is beállíthatja a adja nekik a következők szerint a [Batch File Conventions standard](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions). A File Conventions szabvány meghatározza, hogy a cél tároló és a feladatok és tevékenységek nevei alapján adott kimeneti fájl az Azure Storage-blob neve. Ha a File Conventions standard kimeneti fájlok elnevezési használja, akkor a kimeneti fájlok megtekintését a a [az Azure portal](https://portal.azure.com).

C# nyelven fejleszt, használhatja a beépített módszereket a [Batch File Conventions .NET-kódtára](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Ebben a könyvtárban az Ön hozza létre a megfelelően elnevezett tárolót és a blob elérési útjának. Például meghívhatja az API-t a tároló, a feladat neve alapján a helyes nevét:

```csharp
string containerName = job.OutputStorageContainerName();
```

Használhatja a [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) metódussal adja vissza egy közös hozzáférésű jogosultságkód (SAS) URL-címet, amellyel a tároló írni. Ezután továbbíthatja ezt az SAS a [OutputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) konstruktor.

C# nyelven fejleszt, szüksége lesz a File Conventions standard megvalósításához, saját magának.

## <a name="code-sample"></a>Kódminta

A [PersistOutputs] [ github_persistoutputs] mintaprojektet az egyik a [Azure Batch-kódminta] [ github_samples] a Githubon. A Visual Studio-megoldás bemutatja, hogyan tartós tárolási, a feladat kimenetének megőrzése a Batch .NET-hez készült ügyféloldali kódtár használatával. A minta futtatásához kövesse az alábbi lépéseket:

1. Nyissa meg a projektet a **Visual Studio 2017**.
2. Adja hozzá a Batch- és Storage **fiók hitelesítő adatai** való **AccountSettings.settings** Microsoft.Azure.Batch.Samples.Common projektben.
3. **Build** (de ne futtassa) a megoldás. Ha a rendszer kéri, állítsa vissza a NuGet-csomagok.
4. Töltse fel az Azure portal használatával egy [alkalmazáscsomag](batch-application-packages.md) a **PersistOutputsTask**. Tartalmazza a `PersistOutputsTask.exe` és annak függő szerelvényei a .zip csomag "PersistOutputsTask" kívánt alkalmazás Azonosítóját és az "1.0" alkalmazáscsomag-verzió.
5. **Indítsa el** (Futtatás) a **PersistOutputs** projekt.
6. Amikor a rendszer kéri, válassza ki a minta futtatásához használni, adja meg a megőrzést technológiát **2** , futtassa a mintát, a feladat kimenetének megőrzése a Batch szolgáltatás API segítségével.
7. Ha szükséges, futtassa újra a mintakódot, belépő **3** megőrizni a kimenet a Batch szolgáltatás API-val és a tároló és blobnév célútvonalat File Conventions szabvány szerinti nevet.

## <a name="next-steps"></a>További lépések

- A .NET-hez a File Conventions-könyvtárral megőrzése feladat kimenete a további információkért lásd: [megőrizheti a feladatok és tevékenységek adatokat, a Batch File Conventions-könyvtárral az Azure Storage .NET-keretrendszerhez készült](batch-task-output-file-conventions.md).
- Egyéb megközelítések a kimeneti adatok megtartását az Azure Batchben a további információkért lásd: [az Azure Storage-feladatok és tevékenységek kimenetének megőrzése](batch-task-output.md).

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
