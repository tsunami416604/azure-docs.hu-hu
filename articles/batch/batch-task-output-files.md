---
title: Megőrizni a feladat- és kimeneti Azure Storage Azure Batch szolgáltatás API-val |} Microsoft Docs
description: Megtudhatja, hogyan használja a Batch szolgáltatás API megőrizni a kötegelt feladat és a feladat kimenete Azure Storage.
services: batch
author: tamram
manager: timlt
editor: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 06/16/2017
ms.author: tamram
ms.openlocfilehash: fa124109bfc9d333469c255c50df0af96a26bebf
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Azure Storage szolgáltatás kötegelt API-val feladat adatok megőrzése

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

2017-05-01 verziójától kezdve, a Batch szolgáltatás API támogatja tárolásakor kimeneti adatok Azure Storage és a virtuálisgép-konfiguráció készletek futó feladat manager feladatok. Ha hozzáad egy feladatot, a feladat kimenete a cél az Azure Storage megadhatja egy tárolót. A Batch szolgáltatás majd ír kimeneti adatokat tároló, amikor a feladat már befejeződött.

A Batch szolgáltatás API használatával megőrizni a feladat kimenete előnye, hogy nem kell módosítani, hogy a feladat fut. alkalmazást. Néhány egyszerű módosításokkal az ügyfélalkalmazáshoz, ehelyett a kód, amely létrehozza a feladat kimenete a feladat megőrzéséhez is.   

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Ha használja a Batch szolgáltatás API megőrizni a feladat kimenete?

Az Azure Batch megőrizni a feladat kimenete egynél több módot nyújt. A Batch szolgáltatás API használata egy kényelmes módszert alkalmaz, amely leginkább megfelelő a következő használati helyzetekben:

- Szeretné megőrizni az ügyfél alkalmazáson belül a feladat kimenete az alkalmazást, amely a feladat futásának módosítása nélkül kód írása.
- Szeretné megőrizni a kötegelt és létrehozni a virtuális gép konfigurációs készletek feladat manager feladatok kimenete.
- Szeretné megőrizni a kimenet egy Azure Storage-tárolóban egy tetszőleges nevet.
- Szeretné megőrizni a kimenet a következők szerint az Azure Storage tárolót a [kötegelt fájl egyezmények standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). 

Ha adott esetben eltér a fent felsorolt, esetleg érdemes lehet egy másik módszert. Például a Batch szolgáltatás API jelenleg nem támogatja az adatfolyam kimeneti Azure Storage a feladat futása közben. A kimeneti adatfolyam érdekében a kötegelt fájl egyezmények szalagtár használata esetén elérhető, a .NET-hez. Egyéb nyelvek lesz szüksége a saját megoldás megvalósítási. Más beállításokat a tárolásakor feladat kimenetének további információkért lásd: [megőrizni a feladat- és kimeneti Azure Storage](batch-task-output.md). 

## <a name="create-a-container-in-azure-storage"></a>A tároló létrehozása az Azure Storage

A feladat kimenetének Azure Storage megőrizni, szüksége létrehozni egy tárolót, amely a kimeneti fájlok célhelyként szolgál. A tároló létrehozása, a feladat a feladat mentése előtt minél futtatása előtt. A tároló létrehozásához használja a megfelelő Azure Storage ügyféloldali kódtár vagy az SDK-val. Azure Storage API-k kapcsolatos további információkért tekintse meg a [Azure Storage-dokumentációt](https://docs.microsoft.com/azure/storage/).

Például, ha az alkalmazás írása C# nyelven íródtak, használja a [Azure Storage ügyféloldali kódtára a .NET](https://www.nuget.org/packages/WindowsAzure.Storage/). A következő példa bemutatja, hogyan hozhat létre tárolót:

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await conainer.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>A tároló egy közös hozzáférésű jogosultságkódot beolvasása

Miután létrehozta a tárolót, lekérése a közös hozzáférésű jogosultságkód (SAS) írási hozzáféréssel a tárolóhoz. SAS-kód a tároló delegált hozzáférést biztosít. A SAS hozzáférést biztosít az egy megadott készlet azon engedélyek és a megadott időtartam alatt. A Batch szolgáltatás írási engedélyekkel a feladat kimenetének írni a tároló SAS-kód szükséges. További információ a SAS: [használata közös hozzáférésű jogosultságkód \(SAS\) az Azure Storage](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

Ha az Azure Storage API-kkal SAS-kód lekérése, a API SAS token karakterláncot ad vissza. A lexikális elem karakterlánca a SAS, beleértve az engedélyeket és a SAS érvényességi időtartama összes paramétereket tartalmaz. A SAS használatával érhető el egy Azure Storage-tároló, meg kell a SAS-token karakterlánc hozzáfűzése az erőforrás URI azonosítója. Az erőforrás URI azonosítója, együtt a hozzáfűzött SAS-jogkivonat hitelesített hozzáférést biztosít az Azure Storage.

A következő példa bemutatja, hogyan kérhet egy csak írható SAS-token karakterlánc ahhoz a tárolóhoz, majd a biztonsági Társítások hozzáfűzi a tároló URI:

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken; 
```

## <a name="specify-output-files-for-task-output"></a>Adja meg a feladat kimenete a kimeneti fájlok

Adja meg a kimeneti fájlokat egy tevékenység, hozzon létre egy gyűjteményt a [eredményfájl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) objektumokat, majd rendelje hozzá a [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) tulajdonságot, ha a feladat létrehozásához. 

A .NET alábbi példakód létrehoz egy feladatot, amely véletlenszerű számok nevű fájlba írja `output.txt`. A parancs létrehozza a kimeneti fájl `output.txt` kellene írni a tárolóhoz. A példa is hoz létre a kimeneti fájlok bármely naplófájlok, amely megfelel a fájl mintának `std*.txt` (_pl._, `stdout.txt` és `stderr.txt`). A tároló URL-cím az SA-k létrejött a tároló korábban szükséges. A Batch szolgáltatás a SAS használatával hitelesíti a tárolóhoz való hozzáférést: 

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

### <a name="specify-a-file-pattern-for-matching"></a>Az egyező fájl minta

Kimeneti fájlt ad meg, ha a [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) az egyező fájl minta tulajdonságot. A fájlminta előfordulhat, hogy felel meg, nulla fájlok, egyetlen fájl vagy a fájlokat, a feladat által létrehozott.

A **FilePattern** tulajdonság támogatja a szabványos fájlrendszere helyettesítő karaktereket, mint `*` (a nem rekurzív megegyezik) és `**` (a rekurzív megegyezik). Például adja meg a fenti kódminta egyező fájlminta `std*.txt` nem rekurzív módon: 

`filePattern: @"..\std*.txt"`

Egyetlen fájl feltöltéséhez, adja meg a fájl minta nem használható helyettesítő. Például adja meg a fenti kódminta egyező fájlminta `output.txt`:

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>Adjon meg egy feltöltési állapot

A [OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) tulajdonság lehetővé teszi a kimeneti fájlok feltételes feltöltése. Egy általános forgatókönyv egy fájlt, ha a feladat sikeres, és egy másik készlet fájlok feltöltéséhez, ha a hiba. Érdemes lehet például részletes naplófájlok feltöltéséhez, csak akkor, ha a feladat sikertelen lesz, és egy nem nulla kilépési kód kilép. Hasonlóképpen érdemes lehet eredmény fájlok feltöltése csak akkor, ha a feladat sikeres, ezeket a fájlokat esetleg hiányzik vagy nem teljes, ha a feladat sikertelen.

A fenti készletek kódminta a **UploadCondition** tulajdonságot **TaskCompletion**. Ez a beállítás megadja, hogy a fájl tölthető fel a kilépési kód értékétől függetlenül a feladatok befejezése után. 

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

Egyéb beállításokat, tekintse meg a [OutputFileUploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) felsorolás.

### <a name="disambiguate-files-with-the-same-name"></a>Ilyen nevű fájlok félreérthetőség megszüntetéséhez

A feladatok a feladatok készíthet a névvel rendelkező fájlokat. Például `stdout.txt` és `stderr.txt` feladatokban futó minden feladatot hoz létre. Mivel minden tevékenység saját környezetében fut, ezeket a fájlokat a fájlrendszerben a csomópont nem ütköznek. Azonban a megosztott tárolóhoz több feladat fájlok feltöltésekor lesz szüksége a félreérthetőség megszüntetéséhez azonos nevű fájlt.

A [OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) tulajdonság határozza meg a cél blob vagy virtuális könyvtárat a kimeneti fájlokat. Használhatja a **elérési** tulajdonság a blob vagy virtuális könyvtárat úgy, hogy ugyanazzal a névvel a fájlok az Azure Storage egyedi neve. A tevékenység azonosítója az elérési út használata egy jó módszer egyedi nevek és könnyebb a fájlok azonosításához.

Ha a **FilePattern** tulajdonsága helyettesítő karakteres kifejezést, majd az összes fájl, amely megfelel a mintának feltöltötte-e a megadott virtuális könyvtár a **elérési** tulajdonság. Például, ha a tároló `mycontainer`, a feladat-azonosító `mytask`, és a fájl minta `..\std*.txt`, akkor az Azure Storage kimeneti fájlokat abszolút URI-azonosítók hasonló lesz:

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

Ha a **FilePattern** tulajdonsága egyezik egyetlen fájlnév, ami azt jelenti, nem tartalmaz helyettesítő karaktereket, majd az értékét a **elérési** tulajdonság határozza meg a teljesen minősített blob neve. Ha várhatóan több feladat egyetlen fájl ütközik elnevezési, majd adja meg a félreérthetőség megszüntetéséhez azokat a fájlokat a fájlnév részét képező virtuális könyvtár nevét. Például a **elérési** tulajdonság a tevékenység azonosítója, az elválasztó karaktert (általában egy perjel) és a fájl nevét:

`path: taskId + @"/output.txt"`

Az abszolút URI-azonosítók a kimeneti fájlok feladatokhoz hasonló lesz:

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

További információ a virtuális könyvtárak az Azure Storage: [a tárolóban lévő blobok listázása](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container).


## <a name="diagnose-file-upload-errors"></a>Fájl feltöltése hibák

Ha kimeneti fájlok feltöltése az Azure Storage nem sikerül, akkor a feladat áthelyezése a **befejezve** állapot és a [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) tulajdonság értéke. Vizsgálja meg a **FailureInformation** tulajdonság határozza meg, milyen hiba történt. Például itt található, amely a fájl feltöltése akkor fordul elő, ha a tároló nem található. hiba: 

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

Minden fájl feltöltése a Batch ír két naplófájljait a számítási csomópont `fileuploadout.txt` és `fileuploaderr.txt`. További információt a konkrét hiba ezekben a naplófájlokban ellenőrizheti. Azokban az esetekben, ahol a fájl feltöltése soha nem kíséreltek meg, például mert maga a feladat futtatása nem sikerült majd a naplófájlok nem létezik.

## <a name="diagnose-file-upload-performance"></a>Diagnosztizálhatja fájl feltöltése

A `fileuploadout.txt` fájl feltöltési folyamatáról naplózza. Ez a fájl, ha többet szeretne arról, hogy mennyi ideig tart a fájlfeltöltéseket ellenőrizheti. Ne feledje, hogy a teljesítmény, például a csomópont, más tevékenység a csomóponton a feltöltés időpontjában mérete, a céltároló ugyanabban a régióban, mint a Batch-készlet, hány csomópontok a stora feltöltő-e feltölteni számos meghatározó tényező GE fiók ugyanannyi időt vesz igénybe, és így tovább.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>A Batch szolgáltatás API használata a kötegelt fájl egyezmények standard

Megőrizni a feladat kimenete a Batch szolgáltatás API-t, ha a cél tároló nevére, és blobok azonban van lehetősége. Ahhoz, hogy megnevezzen őket is beállíthatja a [kötegelt fájl egyezmények standard](https://github.com/Azure/azure-sdk-for-net/tree/vs17Dev/src/SDKs/Batch/Support/FileConventions#conventions). A fájl egyezmények standard céltárolója és az Azure Storage blob a megadott kimeneti fájl nevét a feladat- és alapján nevét határozza meg. Ha a fájl egyezmények standard kimeneti fájlok elnevezési, akkor a kimeneti fájlok megtekintését a rendszer a [Azure-portálon](https://portal.azure.com).

A C# fejleszt, ha a beépített módszereket használhatják-e a [kötegelt fájl egyezmények .NET-keretrendszerhez készült](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files). Ezt a szalagtárat létrehozza a megfelelően elnevezett tároló és a blob elérési út. Például hívása az API-t a helyes nevét, a tároló, a feladat neve alapján:

```csharp
string containerName = job.OutputStorageContainerName();
```

Használhatja a [CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) vissza egy közös hozzáférésű jogosultságkód (SAS) URL-címet, a tárolóhoz írásához használt módszer. A SAS-t, majd átadhatók a [OutputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) konstruktor.

C# nyelven fejleszt, ha szüksége lesz a saját maga a fájl egyezmények standard végrehajtásához.

## <a name="code-sample"></a>Kódminta

A [PersistOutputs] [ github_persistoutputs] mintaprojektet egyike a [Azure Batch-Kódminták] [ github_samples] a Githubon. A Visual Studio megoldás bemutatja, hogyan kell a kötegelt ügyféloldali kódtára a .NET használatával továbbra is fennáll feladatkimenet tartós tárhelyre. A minta futtatásához kövesse az alábbi lépéseket:

1. Nyissa meg a projektet a **Visual Studio 2015-ös vagy újabb**.
2. A kötegelt és a tárolási **fiók hitelesítő adatait** való **AccountSettings.settings** a Microsoft.Azure.Batch.Samples.Common projektben.
3. **Build** (de ne futtassa) a megoldás. Ha a rendszer kéri, állítsa vissza a NuGet-csomagok.
4. Töltse fel az Azure-portál használatával egy [alkalmazáscsomag](batch-application-packages.md) a **PersistOutputsTask**. Tartalmazza a `PersistOutputsTask.exe` és a függő szerelvényeket a .zip-csomagja "PersistOutputsTask" az alkalmazás-Azonosítót, és az alkalmazáscsomag verzióját "1.0".
5. **Start** (Futtatás) a **PersistOutputs** projekt.
6. Amikor a rendszer kéri a minta futtatásához használni, adja meg a adatmegőrzési technológiát válasszon **2** a Batch szolgáltatás API használatával megőrizni a feladat kimenete a minta futtatásához.
7. Ha szükséges, a minta futtatásához újra, írja be **3** megőrizni a kimenet a Batch szolgáltatás API-t és a tároló és a blob elérési utat célként a fájl egyezmények szabvány neve.

## <a name="next-steps"></a>További lépések

- A .NET-hez lévő objektumán feladat kimenete a fájl egyezmények könyvtárhoz további információkért lásd: [megőrizni a feladat- és adatok Azure Storage a kötegelt fájl egyezmények .NET-keretrendszerhez készült megőrizni ](batch-task-output-file-conventions.md).
- Más megoldások a kimeneti adatait az Azure Batch információkért lásd: [megőrizni a feladat- és kimeneti Azure Storage](batch-task-output.md).

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
