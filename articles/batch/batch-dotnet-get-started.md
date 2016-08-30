<properties
    pageTitle="Oktatóprogram – Ismerkedés az Azure Batch .NET-es kódtárával | Microsoft Azure"
    description="Egy egyszerű forgatókönyvvel megismerheti az Azure Batch alapvető fogalmait és azt, hogyan fejleszthet a Batch szolgáltatáshoz"
    services="batch"
    documentationCenter=".net"
    authors="mmacy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="batch"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-compute"
    ms.date="05/27/2016"
    ms.author="marsma"/>

# Ismerkedés az Azure Batch .NET-es kódtárával

> [AZURE.SELECTOR]
- [.NET](batch-dotnet-get-started.md)
- [Python](batch-python-tutorial.md)

Ebben a cikkben megismerheti az [Azure Batch][azure_batch] alapjait és a [Batch .NET][net_api] kódtárat, ahogy egy C# mintaalkalmazást tárgyalunk meg lépésről lépésre. Megismerjük, hogyan használja ez a mintaalkalmazás a Batch szolgáltatást párhuzamos számítási feladatok feldolgozásához a felhőben, valamint hogyan használja az [Azure-tárolót](../storage/storage-introduction.md) a fájlok előkészítéséhez és lekéréséhez. Megtanulhatja a Batch-alkalmazások munkafolyamatainak gyakori technikáit. A Batch fő összetevőivel, például a tevékenységekkel, feladatokkal, készletekkel és számítási csomópontokkal kapcsolatos alapvető ismereteket is elsajátíthatja.

![Batch-megoldás munkafolyamata (alapszintű)][11]<br/>

## Előfeltételek

Ez a cikk a C# és a Visual Studio gyakorlati ismeretét feltételezi. Azt is feltételezi, hogy az Azure, a Batch és a Storage alábbi fióklétrehozási követelmények teljesülnek.

### Fiókok

- **Azure-fiók**: Ha még nincs Azure-előfizetése, [hozzon létre egy ingyenes Azure-fiókot][azure_free_account].
- **Batch-fiók**: Ha már rendelkezik Azure-előfizetéssel, [hozzon létre egy Azure Batch-fiókot](batch-account-create-portal.md).
- **Tárfiók**: Lásd a [Tudnivalók az Azure Storage-fiókokról](../storage/storage-create-storage-account.md) cikk [Tárfiók létrehozása](../storage/storage-create-storage-account.md#create-a-storage-account) szakaszát.

> [AZURE.IMPORTANT] A Batch jelenleg *csak* az **Általános célú** tárfióktípust támogatja, amelynek leírása a [Tudnivalók az Azure Storage-fiókokról](../storage/storage-create-storage-account.md) fejezet 5., [Tárfiók létrehozása](../storage/storage-create-storage-account.md#create-a-storage-account) című szakaszában található.

### Visual Studio

A mintaprojekt összeállításához **Visual Studio 2013** vagy **Visual Studio 2015** szükséges. A Visual Studio ingyenes és próbaverzióit a [Visual Studio 2015-termékek áttekintésében][visual_studio] találja.

### *DotNetTutorial* kódminta

A [DotNetTutorial][github_dotnettutorial] minta a GitHubon lévő [azure-batch-samples][github_samples] tárban található sok kódminta egyike. A minta letöltéséhez kattintson a **Download ZIP** (ZIP letöltése) gombra a tár kezdőlapján, vagy kattintson az [azure-batch-samples-master.zip][github_samples_zip] közvetlen letöltésére szolgáló hivatkozásra. Amikor kibontotta a ZIP-fájl tartalmát, a következő mappában találja a megoldást:

`\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial`

### Azure Batch Explorer (nem kötelező)

Az [Azure Batch Explorer][github_batchexplorer] egy ingyenes segédprogram, amely a GitHub [azure-batch-samples][github_samples] tárában szerepel. Bár ezen oktatóprogram elvégzéséhez nem szükséges, hasznos a Batch-megoldások fejlesztésekor és hibakeresésekor.

## DotNetTutorial mintaprojekt áttekintése

A *DotNetTutorial* kódminta egy Visual Studio 2013-megoldás, amely két projektből áll: a **DotNetTutorial** és a **TaskApplication** projektből.

- A **DotNetTutorial** az az ügyfélalkalmazás, amely a Batch és a Storage szolgáltatásokkal együttműködve végez párhuzamos számítási feladatokat a számítási csomópontokon (virtuális gépeken). A DotNetTutorial a helyi munkaállomáson fut.

- A **TaskApplication** program fut az Azure számítási csomópontjain a tényleges munka elvégzéséhez. A mintában a `TaskApplication.exe` elemzi az Azure Storage-ból letöltött fájl (a bemeneti fájl) szövegét. Ezután egy szövegfájlt hoz létre (a kimeneti fájlt), amely a bemeneti fájlban megjelenő első három szó listáját tartalmazza. A kimeneti fájl létrehozása után a TaskApplication feltölti a fájlt az Azure Storage-ba. Így az ügyfélalkalmazás letöltheti azt. A TaskApplication párhuzamosan fut több számítási csomóponton a Batch szolgáltatásban.

A következő diagram az ügyfélalkalmazás által végzett elsődleges műveleteket, a *DotNetTutorial* oktatóprogramot és a tevékenységek által végrehajtott *TaskApplication* alkalmazást ábrázolja. Ez az alapvető munkafolyamat számos, a Batch használatával létrehozott számítási megoldásra jellemző. Bár nem mutatja be a Batch szolgáltatásban elérhető összes funkciót, majdnem mindegyik Batch-forgatókönyv hasonló folyamatokat tartalmaz.

![Példa Batch-munkafolyamat][8]<br/>

[**1. lépés**](#step-1-create-storage-containers) **Tárolók** létrehozása az Azure Blob Storage-ban.<br/>
[**2. lépés**](#step-2-upload-task-application-and-data-files) Tevékenység-alkalmazásfájlok és bemeneti fájlok feltöltése tárolókba.<br/>
[**3. lépés**](#step-3-create-batch-pool) Batch-**készlet** létrehozása.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**3a.** A **StartTask** készlet letölti a tevékenység bináris fájljait (TaskApplication) a csomópontokra, amikor a készlethez csatlakoznak.<br/>
[**4. lépés**](#step-4-create-batch-job) Batch-**feladat** létrehozása.<br/>
[**5. lépés**](#step-5-add-tasks-to-job) Adjon hozzá **tevékenységeket** a feladathoz.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**5a.** A feladtok végrehajtásának ütemezése a csomópontokon.<br/>
    &nbsp;&nbsp;&nbsp;&nbsp;**5b.** Mindegyik tevékenység letölti a bemeneti adatait az Azure Storage-ból, majd elkezdi a végrehajtást.<br/>
[**6. lépés**](#step-6-monitor-tasks) Tevékenységek figyelése.<br/>
  &nbsp;&nbsp;&nbsp;&nbsp;**6a.** A befejezett tevékenységek feltöltik a kimeneti adataikat az Azure Storage-ba.<br/>
[**7. lépés**](#step-7-download-task-output) Tevékenység kimenetének letöltése a Storage-ból.

Ahogy említettük, nem minden Batch-megoldás végzi el pontosan ezeket a lépéseket, és sok más lépést is tartalmazhatnak, de a *DotNetTutorial* mintaalkalmazás bemutatja a Batch-megoldások általános folyamatait.

## A *DotNetTutorial* mintaprojekt felépítése

A minta sikeres futtatása előtt a Batch- és a Storage-fiók hitelesítő adatait is meg kell adnia a *DotNetTutorial*-projekt `Program.cs` fájljában. Ha még nem tette meg, nyissa meg a megoldást a Visual Studióban. Ehhez kattintson duplán a `DotNetTutorial.sln` megoldásfájlra. Vagy nyissa meg a Visual Studióból a **File > Open > Project/Solution** (Fájl > Megnyitás > Projekt/Megoldás) elemre kattintva.

Nyissa meg a *DotNetTutorial* projekt `Program.cs` elemét. Ezután adja hozzá a fájl elejének közelében megadott hitelesítő adatokat:

```
// Update the Batch and Storage account credential strings below with the values
// unique to your accounts. These are used when constructing connection strings
// for the Batch and Storage client objects.

// Batch account credentials
private const string BatchAccountName = "";
private const string BatchAccountKey  = "";
private const string BatchAccountUrl  = "";

// Storage account credentials
private const string StorageAccountName = "";
private const string StorageAccountKey  = "";
```

> [AZURE.IMPORTANT] Ahogy korábban említettük, most meg kell adnia egy **Általános célú** Azure Storage-tárfiók hitelesítő adatait. A Batch-alkalmazások a Blob Storage-et fogják használni az **Általános célú** tárfiókon belül. Ne olyan Storage-fiók hitelesítő adatait adja meg, amelyet a *Blob Storage* fióktípus kiválasztásával hozott létre.

A Batch- és a Storage-fiók hitelesítő adatai a szolgáltatások fiókpaneljein találhatók az [Azure Portalon][azure_portal]:

![Batch hitelesítő adatai a portálon][9]
![Storage hitelesítő adatai a portálon][10]<br/>

Most, hogy frissítette a projektet a hitelesítő adataival, kattintson a jobb gombbal a megoldásra a Solution Explorerben (Megoldáskezelőben), és kattintson a **Build Solution** (Megoldás fordítása) parancsra. Erősítse meg a NuGet-csomagok visszaállítását, ha a rendszer erre kéri.

> [AZURE.TIP] Ha a NuGet-csomagokat nem állítja vissza automatikusan a rendszer, vagy ha hibákat lát a csomagok visszaállításával kapcsolatban, győződjön meg arról, hogy a [NuGet-csomagkezelő][nuget_packagemgr] telepítve van. Ezután engedélyezze a hiányzó csomagok letöltését. A csomagletöltés engedélyezéséről lásd: [Csomag-visszaállítás engedélyezése a fordítás során][nuget_restore].

A következő szakaszokban a mintaalkalmazást felosztjuk azokra a lépésekre, amelyeket végrehajt a számítási feladatok feldolgozásához a Batch szolgáltatásban, és részletesen tárgyaljuk ezeket a lépéseket. Javasoljuk, hogy a cikk további részének feldolgozása közben használja a Visual Studióban megnyitott megoldást, mert a minta nem minden kódsoráról esik szó a cikkben.

Az 1. lépés elkezdéséhez keresse meg a *DotNetTutorial* projekt `Program.cs` fájljának `MainAsync` metódusát. Az alábbi lépések mindegyike nagyjából a `MainAsync` metódushívásainak sorrendjét követi.

## 1. lépés: Storage-tárolók létrehozása

![Tárolók létrehozása az Azure Storage-ban][1]
<br/>

A Batch beépített támogatást tartalmaz az Azure Storage használatához. A Storage-fiókban lévő tárolók biztosítják a Batch-fiókban futó tevékenységekhez szükséges fájlokat. A tárolók a tevékenységek által létrehozott kimeneti adatok tárolásához is helyet biztosítanak. A *DotNetTutorial*-ügyfélalkalmazás először három tárolót hoz létre az [Azure Blob Storage](../storage/storage-introduction.md)-ban:

- **application**: Ez a tároló tárolja a tevékenységek által futtatott alkalmazást, valamint annak függőségeit, például a DLL-eket.
- **input**: A tevékenységek az *input* tárolóból töltik le a feldolgozni kívánt adatfájlokat.
- **output**: Amikor a tevékenységek befejezik a bemeneti fájl feldolgozását, feltöltik az eredményeket a *output* tárolóba.

A Storage-fiókkal való kommunikációhoz és a tárolók létrehozásához a [.NET-hez készült Azure Storage ügyféloldali kódtárat][net_api_storage] használjuk. Létrehozunk egy hivatkozást a fiókra a [CloudStorageAccount][net_cloudstorageaccount] fiókkal, és ebből létrehozunk egy [CloudBlobClient][net_cloudblobclient] hivatkozást:

```
// Construct the Storage account connection string
string storageConnectionString = String.Format(
    "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
    StorageAccountName,
    StorageAccountKey);

// Retrieve the storage account
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageConnectionString);

// Create the blob client, for use in obtaining references to blob storage containers
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
```

A `blobClient` hivatkozást használjuk a teljes alkalmazásban, és számos metódusnak továbbítjuk paraméterként. Erre a közvetlenül a fentiek után található kódblokkban talál példát, ahol a `CreateContainerIfNotExistAsync` metódust hívjuk a tárolók tényleges létrehozásához.

```
// Use the blob client to create the containers in Azure Storage if they don't
// yet exist
const string appContainerName    = "application";
const string inputContainerName  = "input";
const string outputContainerName = "output";
await CreateContainerIfNotExistAsync(blobClient, appContainerName);
await CreateContainerIfNotExistAsync(blobClient, inputContainerName);
await CreateContainerIfNotExistAsync(blobClient, outputContainerName);
```

```
private static async Task CreateContainerIfNotExistAsync(
    CloudBlobClient blobClient,
    string containerName)
{
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        if (await container.CreateIfNotExistsAsync())
        {
                Console.WriteLine("Container [{0}] created.", containerName);
        }
        else
        {
                Console.WriteLine("Container [{0}] exists, skipping creation.",
                    containerName);
        }
}
```

A tárolók létrehozása után az alkalmazás feltöltheti a tevékenységek által használandó fájlokat.

> [AZURE.TIP] A [How to use Blob Storage from .NET](../storage/storage-dotnet-how-to-use-blobs.md) (A Blob Storage használata .NET-ből) szakasz hasznos áttekintést nyújt az Azure Storage-tárolók és blobok használatáról. A Batch használatának elkezdésekor ez az egyik legfontosabb forrásanyag.

## 2. lépés: Tevékenységalkalmazás- és adatfájlok feltöltése

![Tevékenységalkalmazás- és bemeneti (adat-) fájlok feltöltése tárolókba][2]
<br/>

A fájl feltöltése műveletben a *DotNetTutorial* először az **alkalmazás** és a **bemeneti** fájlok elérési útjainak gyűjteményeit határozza meg a helyi gép alapján. Ezután feltölti ezeket a fájlokat az előző lépésben létrehozott tárolókba.

```
// Paths to the executable and its dependencies that will be executed by the tasks
List<string> applicationFilePaths = new List<string>
{
    // The DotNetTutorial project includes a project reference to TaskApplication,
    // allowing us to determine the path of the task application binary dynamically
    typeof(TaskApplication.Program).Assembly.Location,
    "Microsoft.WindowsAzure.Storage.dll"
};

// The collection of data files that are to be processed by the tasks
List<string> inputFilePaths = new List<string>
{
    @"..\..\taskdata1.txt",
    @"..\..\taskdata2.txt",
    @"..\..\taskdata3.txt"
};

// Upload the application and its dependencies to Azure Storage. This is the
// application that will process the data files, and will be executed by each
// of the tasks on the compute nodes.
List<ResourceFile> applicationFiles = await UploadFilesToContainerAsync(
    blobClient,
    appContainerName,
    applicationFilePaths);

// Upload the data files. This is the data that will be processed by each of
// the tasks that are executed on the compute nodes within the pool.
List<ResourceFile> inputFiles = await UploadFilesToContainerAsync(
    blobClient,
    inputContainerName,
    inputFilePaths);
```

A `Program.cs` fájlban két olyan metódus van, amely részt vesz a feltöltési folyamatban:

- `UploadFilesToContainerAsync`: Ez a metódus az (alábbiakban részletezett) [ResourceFile][net_resourcefile] objektumok gyűjteményét adja vissza, és belsőleg meghívja az `UploadFileToContainerAsync` metódust, hogy feltöltse a *filePaths* paraméterben átadott fájlokat.
- `UploadFileToContainerAsync`: Ez a metódus végzi a tényleges fájlfeltöltést, és ez hozza létre a [ResourceFile][net_resourcefile] objektumokat. A fájl feltöltése után közös hozzáférésű jogosultságkódot (SAS) szerez be a fájlhoz, és visszaadja az azt jelölő ResourceFile objektumot. A közös hozzáférésű jogosultságkódok ismertetése is az alábbiakban olvasható.

```
private static async Task<ResourceFile> UploadFileToContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string filePath)
{
        Console.WriteLine(
            "Uploading file {0} to container [{1}]...", filePath, containerName);

        string blobName = Path.GetFileName(filePath);

        CloudBlobContainer container = blobClient.GetContainerReference(containerName);
        CloudBlockBlob blobData = container.GetBlockBlobReference(blobName);
        await blobData.UploadFromFileAsync(filePath, FileMode.Open);

        // Set the expiry time and permissions for the blob shared access signature.
        // In this case, no start time is specified, so the shared access signature
        // becomes valid immediately
        SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
        {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(2),
                Permissions = SharedAccessBlobPermissions.Read
        };

        // Construct the SAS URL for blob
        string sasBlobToken = blobData.GetSharedAccessSignature(sasConstraints);
        string blobSasUri = String.Format("{0}{1}", blobData.Uri, sasBlobToken);

        return new ResourceFile(blobSasUri, blobName);
}
```

### ResourceFiles

A [ResourceFile][net_resourcefile] tevékenységeket biztosít a Batch-ben az Azure Storage azon fájljának URL-jével, amely a számítási csomópontra lesz letöltve a feladat futtatása előtt. A [ResourceFile.BlobSource][net_resourcefile_blobsource] tulajdonság határozza meg a fájl teljes URL-jét, ahogyan az az Azure Storage-ban található. Az URL a fájl biztonságos elérését nyújtó közös hozzáférésű jogosultságkódot (SAS-t) is tartalmazhat. A Batch .NET-en belüli legtöbb tevékenység tartalmaz egy *ResourceFiles* tulajdonságot, beleértve a következőket:

- [CloudTask][net_task]
- [StartTask][net_pool_starttask]
- [JobPreparationTask][net_jobpreptask]
- [JobReleaseTask][net_jobreltask]

A DotNetTutorial-mintaalkalmazás nem használja a JobPreparationTask és a JobReleaseTask tevékenységtípust, de ezekről további információt talál a [Run job preparation and completion tasks on Azure Batch compute nodes](batch-job-prep-release.md) (Feladat-előkészítési és -befejezési műveletek futtatása Azure Batch számítási csomópontjain) című szakaszban talál.

### Közös hozzáférésű jogosultságkód (SAS)

A közös hozzáférésű jogosultságkódok olyan karakterláncok, amelyek – egy URL-címbe belefoglalva – biztonságos hozzáférést nyújtanak a tárolókhoz és a blobokhoz az Azure Storage-ban. A DotNetTutorial-alkalmazás a blobok és a tárolók közös hozzáférésű jogosultságkód URL-jeit is használja, és bemutatja, hogyan szerezheti be a közös hozzáférésű jogosultságkódok karakterláncait a Storage szolgáltatásból.

- **Blob közös hozzáférésű jogosultságkódjai**: A DotNetTutorial-oktatóprogramban a készlet StartTask tevékenysége blobok közös hozzáférésű jogosultságkódjait használja az alkalmazás bináris fájljainak és bemeneti adatfájljainak a tárolóból való letöltéséhez (lásd az alábbi 3. lépést). A DotNetTutorial `Program.cs` fájljában lévő `UploadFileToContainerAsync` metódus tartalmazza a kódot, amely lekéri az egyes blobok közös hozzáférésű jogosultságkódját. Ezt a [CloudBlob.GetSharedAccessSignature][net_sas_blob] meghívásával végzi el.

- **Tároló közös hozzáférésű jogosultságkódjai**: Amikor az egyes tevékenységek befejezik a munkájukat a számítási csomóponton, feltöltik a kimeneti fájljukat az Azure Storage *output* tárolójába. Ehhez a TaskApplication egy tároló közös hozzáférésű jogosultságkódját használja, amely írási hozzáférést nyújt a tárolóhoz az elérési út részeként a fájl feltöltésekor. A tároló közös hozzáférésű jogosultságkódjának beszerzése hasonlóan végezhető, mint a blob közös hozzáférésű jogosultságkódjának beszerzése. A DotNetTutorial-oktatóprogramban láthatja, hogy a `GetContainerSasUrl` segédmetódus ehhez a [CloudBlobContainer.GetSharedAccessSignature][net_sas_container] metódust hívja meg. A „6. lépés: Tevékenységek figyelése” című szakaszban olvashat többet arról, hogyan használja a TaskApplication a tárolók közös hozzáférésű jogosultságkódját.

> [AZURE.TIP] A tárfiókon lévő adatok biztonságos hozzáférésének biztosításával kapcsolatos további információkért tekintse meg a közös hozzáférésű jogosultságkódokkal kapcsolatos két részből álló sorozatot: [Part 1: Understanding the shared access signature (SAS) model](../storage/storage-dotnet-shared-access-signature-part-1.md) (1. rész: A közös hozzáférésű jogosultságkód (SAS) modell ismertetése) és a [Part 2: Create and use a shared access signature (SAS) with the Blob service](../storage/storage-dotnet-shared-access-signature-part-2.md) (2. rész: Közös hozzáférésű jogosultságkód (SAS) létrehozása és használata a Blob szolgáltatással).

## 3. lépés: Batch-készlet létrehozása

![Batch-készlet létrehozása][3]
<br/>

Miután feltölti az alkalmazást és az adatfájlokat a Storage-fiókra, a *DotNetTutorial* a Batch .NET-kódtár használatával kezdi el a kommunikációt a Batch szolgáltatással. Ehhez először egy [BatchClient][net_batchclient] jön létre:

```
BatchSharedKeyCredentials cred = new BatchSharedKeyCredentials(
    BatchAccountUrl,
    BatchAccountName,
    BatchAccountKey);

using (BatchClient batchClient = BatchClient.Open(cred))
{
    ...
```

Ezután a számítási csomópontok készlete jön létre a Batch-fiókban a `CreatePoolAsync` hívásával. `CreatePoolAsync` a [BatchClient.PoolOperations.CreatePool][net_pool_create] metódussal hoz létre ténylegesen egy készletet a Batch szolgáltatásban.

```
private static async Task CreatePoolAsync(
    BatchClient batchClient,
    string poolId,
    IList<ResourceFile> resourceFiles)
{
    Console.WriteLine("Creating pool [{0}]...", poolId);

    // Create the unbound pool. Until we call CloudPool.Commit() or CommitAsync(),
    // no pool is actually created in the Batch service. This CloudPool instance is
    // therefore considered "unbound," and we can modify its properties.
    CloudPool pool = batchClient.PoolOperations.CreatePool(
            poolId: poolId,
            targetDedicated: 3,           // 3 compute nodes
            virtualMachineSize: "small",  // single-core, 1.75 GB memory, 224 GB disk
            cloudServiceConfiguration:
                new CloudServiceConfiguration(osFamily: "4")); // Win Server 2012 R2

    // Create and assign the StartTask that will be executed when compute nodes join
    // the pool. In this case, we copy the StartTask's resource files (that will be
    // automatically downloaded to the node by the StartTask) into the shared
    // directory that all tasks will have access to.
    pool.StartTask = new StartTask
    {
        // Specify a command line for the StartTask that copies the task application
        // files to the node's shared directory. Every compute node in a Batch pool
        // is configured with several pre-defined environment variables that you can
        // reference by using commands or applications run by tasks.

        // Since a successful execution of robocopy can return a non-zero exit code
        // (e.g. 1 when one or more files were successfully copied) we need to
        // manually exit with a 0 for Batch to recognize StartTask execution success.
        CommandLine = "cmd /c (robocopy %AZ_BATCH_TASK_WORKING_DIR% %AZ_BATCH_NODE_SHARED_DIR%) ^& IF %ERRORLEVEL% LEQ 1 exit 0",
        ResourceFiles = resourceFiles,
        WaitForSuccess = true
    };

    await pool.CommitAsync();
}
```

Amikor létrehoz egy készletet a [CreatePool][net_pool_create] használatával, meghatározza a paraméterek számát, például a számítási csomópontok számát, a [csomópontok méretét](../cloud-services/cloud-services-sizes-specs.md) és a csomópontok operációs rendszerét. A *DotNetTutorial* oktatóanyagban a [CloudServiceConfiguration][net_cloudserviceconfiguration] konfigurációval a Windows Server 2012 R2 rendszert határozzuk meg a [Cloud Servicesből](../cloud-services/cloud-services-guestos-update-matrix.md). Ehelyett a [VirtualMachineConfiguration][net_virtualmachineconfiguration] is használható, amellyel a Piactérről származó lemezképekből létrehozott csomópontkészleteket hozhat létre, amelyek tartalmaz Windows- és Linux-rendszerképeket is tartalmaznak– további információért lásd: [Linux számítási csomópontok biztosítása Azure Batch-készletekben](batch-linux-nodes.md).

> [AZURE.IMPORTANT] A Batchben lévő számítási erőforrások díjkötelesek. A költségek minimalizálása érdekében a `targetDedicated` értékét 1-re csökkentheti a minta futtatása előtt.

A fizikai csomópontok tulajdonságai mellett egy [StartTask][net_pool_starttask] is megadható a készlethez. A StartTask mindegyik csomóponton fut, amikor a csomópont csatlakozik a készlethez, valamint a csomópont minden újraindításakor. A StartTask különösen hasznos az alkalmazások számítási csomópontokra való telepítéséhez a tevékenységek futtatása előtt. Ha például a tevékenységek Python-szkriptekkel dolgoznak fel adatokat, a StartTask használatával telepítheti a Pythont a számítási csomópontokra.

Ebben a mintaalkalmazásban a StartTask átmásolja a Storage-ból letöltött fájlokat (amelyek a [StartTask][net_starttask].[ResourceFiles][net_starttask_resourcefiles] tulajdonsággal határozhatók meg) a StartTask munkakönyvtárból abba a megosztott könyvtárba, amelyet a csomóponton futó *összes* tevékenység elér. Ez lényegében mindegyik csomóponton a megosztott könyvtárba másolja a(z) `TaskApplication.exe` fájlt és annak függőségeit, amikor a csomópont csatlakozik a készlethez, hogy a csomóponton futó összes feladat elérje a fájlt.

> [AZURE.TIP] Az Azure Batch **alkalmazáscsomagok** szolgáltatása egy másik módot nyújt arra, hogy az alkalmazást egy készletben lévő számítási csomópontokra helyezze. Részletekért lásd: [Alkalmazástelepítés Azure Batch-alkalmazáscsomagokkal](batch-application-packages.md).

A fenti kódrészletben a StartTask *CommandLine* tulajdonságában lévő két környezeti változó használatát is érdemes kiemelni: az `%AZ_BATCH_TASK_WORKING_DIR%` és az `%AZ_BATCH_NODE_SHARED_DIR%` változóé. A Batch-készletben lévő összes számítási csomópont konfigurálása automatikusan történik a Batch-készletre jellemző környezeti változók számával. A tevékenységek által futtatott összes folyamat hozzáféréssel rendelkezik ezekhez a környezeti változókhoz.

> [AZURE.TIP] A Batch-készletekben található számítási csomópontokon elérhető környezeti változókkal és a feladatok munkakönyvtárával kapcsolatban [Az Azure Batch szolgáltatásainak áttekintése](batch-api-basics.md) című témakör „Környezeti beállítások feladatok esetén” és „Fájlok és könyvtárak” szakaszában tekinthet meg további információt.

## 4. lépés: Batch-feladat létrehozása

![Batch-feladat létrehozása][4]<br/>

A Batch-feladatok lényegében számítási csomópontok készletéhez társított tevékenységek gyűjteményei. Ezeket nemcsak a tevékenységek rendszerezéséhez és nyomon követéséhez használhatja a kapcsolódó számítási feladatokban, hanem bizonyos kényszerek betartatásához is – mint például a feladat (és így a tevékenységei) maximális futásideje, valamint a feladat prioritása a Batch-fiókban lévő egyéb feladatokhoz képest. Ebben a példában azonban a feladat csak a 3. lépésben létrehozott készlettel van társítva. Nincsenek konfigurálva további tulajdonságok.

Mindegyik Batch-feladat egy adott készlettel van társítva. Ez a társítás jelzi, hogy a feladat tevékenységei melyik csomópontokon futnak.  Ezt a [CloudJob.PoolInformation][net_job_poolinfo] tulajdonsággal határozhatja meg, ahogyan az alábbi kódrészletben is láthatja.

```
private static async Task CreateJobAsync(
    BatchClient batchClient,
    string jobId,
    string poolId)
{
    Console.WriteLine("Creating job [{0}]...", jobId);

    CloudJob job = batchClient.JobOperations.CreateJob();
    job.Id = jobId;
    job.PoolInformation = new PoolInformation { PoolId = poolId };

    await job.CommitAsync();
}
```

Most, hogy létrejött egy feladat, hozzáadhatók a munka végrehajtásáért felelős feladatok.

## 5. lépés: Tevékenységek hozzáadása a feladathoz

![Tevékenységek hozzáadása a feladathoz][5]<br/>
*(1) A feladathoz hozzáadja a tevékenységeket, (2) ütemezi a tevékenységeket a csomópontokon való futáshoz, és (3) a tevékenységek letöltik a feldolgozni kívánt adatfájlokat*

Egy feladat tényleges elvégzéséhez tevékenységeket kell hozzáadnia a feladathoz. Mindegyik [CloudTask][net_task] egy parancssori tulajdonsággal és [ResourceFiles][net_task_resourcefiles] használatával konfigurálható (mint a készlet StartTask tevékenysége is), amelyet a tevékenység letölt a csomópontra a parancssora automatikus futtatása előtt. A *DotNetTutorial*-mintaprojektben mindegyik tevékenység csak egy fájlt dolgoz fel. A ResourceFiles gyűjtemény így egyetlen elemet tartalmaz.

```
private static async Task<List<CloudTask>> AddTasksAsync(
    BatchClient batchClient,
    string jobId,
    List<ResourceFile> inputFiles,
    string outputContainerSasUrl)
{
    Console.WriteLine("Adding {0} tasks to job [{1}]...", inputFiles.Count, jobId);

    // Create a collection to hold the tasks that we'll be adding to the job
    List<CloudTask> tasks = new List<CloudTask>();

    // Create each of the tasks. Because we copied the task application to the
    // node's shared directory with the pool's StartTask, we can access it via
    // the shared directory on the node that the task runs on.
    foreach (ResourceFile inputFile in inputFiles)
    {
        string taskId = "topNtask" + inputFiles.IndexOf(inputFile);
        string taskCommandLine = String.Format(
            "cmd /c %AZ_BATCH_NODE_SHARED_DIR%\\TaskApplication.exe {0} 3 \"{1}\"",
            inputFile.FilePath,
            outputContainerSasUrl);

        CloudTask task = new CloudTask(taskId, taskCommandLine);
        task.ResourceFiles = new List<ResourceFile> { inputFile };
        tasks.Add(task);
    }

    // Add the tasks as a collection, as opposed to issuing a separate AddTask call
    // for each. Bulk task submission helps to ensure efficient underlying API calls
    // to the Batch service.
    await batchClient.JobOperations.AddTaskAsync(jobId, tasks);

    return tasks;
}
```

> [AZURE.IMPORTANT] Amikor környezeti változókat érnek el (például az `%AZ_BATCH_NODE_SHARED_DIR%` változót) vagy a csomópont `PATH` elemében nem található alkalmazást hajtanak végre, a feladat parancssorait a `cmd /c` előtaggal kell ellátni. Ez explicit módon futtatja a parancsértelmezőt, és arra utasítja, hogy befejeződjön a parancs végrehajtása után. Ez a követelmény nem szükséges, ha a tevékenységek a csomóponti `PATH` helyen futtatják az alkalmazást (például *robocopy.exe* vagy *powershell.exe*), és nem használnak környezeti változókat.

A fenti kódrészletben a `foreach` hurokban láthatja, hogy a tevékenység parancssora úgy van felépítve, hogy három parancssori argumentumot továbbítson a *TaskApplication.exe* fájlba:

1. Az **első argumentum** a feldolgozandó fájl elérési útja. Ez a fájl helyi elérési útja, ahogyan az a csomóponton szerepel. Az `UploadFileToContainerAsync` elemben lévő ResourceFile objektum fenti létrehozásakor a fájlnevet használtuk ehhez a tulajdonsághoz (a ResourceFile konstruktor paramétereként). Ez azt jelzi, hogy a fájl ugyanabban a könyvtárban található, mint a *TaskApplication.exe*.

2. A **második argumentum** meghatározza, hogy az első *N* szót a kimeneti fájlba kell írni. A mintában ez nem módosítható, így az első három szó kerül a kimeneti fájlba.

3. A **harmadik argumentum** az a közös hozzáférésű jogosultságkód (SAS), amely írási hozzáférést biztosít a **kimeneti** tárolóhoz az Azure Storage-ban. A *TaskApplication.exe* ezt a közös hozzáférésű jogosultságkód URL-t használja, amikor a kimeneti fájlt az Azure Storage-ba tölti fel. Ennek kódját az `UploadFileToContainer` metódusban találja, a TaskApplication projekt `Program.cs` fájljában:

```
// NOTE: From project TaskApplication Program.cs

private static void UploadFileToContainer(string filePath, string containerSas)
{
        string blobName = Path.GetFileName(filePath);

        // Obtain a reference to the container using the SAS URI.
        CloudBlobContainer container = new CloudBlobContainer(new Uri(containerSas));

        // Upload the file (as a new blob) to the container
        try
        {
                CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
                blob.UploadFromFile(filePath, FileMode.Open);

                Console.WriteLine("Write operation succeeded for SAS URL " + containerSas);
                Console.WriteLine();
        }
        catch (StorageException e)
        {

                Console.WriteLine("Write operation failed for SAS URL " + containerSas);
                Console.WriteLine("Additional error information: " + e.Message);
                Console.WriteLine();

                // Indicate that a failure has occurred so that when the Batch service
                // sets the CloudTask.ExecutionInformation.ExitCode for the task that
                // executed this application, it properly indicates that there was a
                // problem with the task.
                Environment.ExitCode = -1;
        }
}
```

## 6. lépés: Tevékenységek figyelése

![Tevékenységek figyelése][6]<br/>
*Az ügyfélalkalmazás (1) megfigyeli a tevékenységek elvégzésének és sikerességének állapotát, és (2) a tevékenységek eredményadatokat töltenek fel az Azure Storage-ba*

A feladatokhoz hozzáadott tevékenységet a rendszer automatikusan várólistára helyezi, és ütemezi a futtatásukat az adott feladathoz társított készleten belüli számítási csomópontokon. A Batch a megadott beállítások alapján elvégzi a tevékenységek várólistára helyezésével, ütemezésével, újrapróbálásával kapcsolatos minden feladatot, valamint a tevékenységekhez kapcsolódó egyéb rendszergazdai teendőket. A tevékenységek végrehajtása sokféleképpen megfigyelhető. A DotNetTutorial egy egyszerű példát mutat be, amely csak a tevékenységek befejezéséről, illetve a meghiúsult vagy a sikeres állapotról küld jelentést.

DotNetTutorial `Program.cs` fájljában lévő `MonitorTasks` metódusban a Batch .NET három alapvető fogalmára kell részletesen kitérnünk. Az alábbiakban láthatja ezeket a megjelenésük sorrendjében:

1. **ODATADetailLevel**: Az [ODATADetailLevel][net_odatadetaillevel] meghatározása a listaműveletekben (például a feladatok tevékenységlistáinak beszerzéséhez) elengedhetetlen a Batch-alkalmazások teljesítményének biztosítása érdekében. Ha bármilyen állapotfigyelést szeretne végezni a Batch-alkalmazásaiban, olvassa el [az Azure Batch szolgáltatás hatékony lekérdezését](batch-efficient-list-queries.md) ismertető cikket.

2. **TaskStateMonitor**: A [TaskStateMonitor][net_taskstatemonitor] segédprogramokat biztosít a Batch .NET-alkalmazásoknak a feladatállapotok megfigyeléséhez. A `MonitorTasks` metódusban a *DotNetTutorial* megvárja, amíg az összes tevékenység eléri a [TaskState.Completed][net_taskstate] állapotot egy adott időkorláton belül. Ezután befejezi a feladatot.

3. **TerminateJobAsync**: A feladatok [JobOperations.TerminateJobAsync][net_joboperations_terminatejob] (vagy a blokkoló JobOperations.TerminateJob) használatával való leállítása befejezettként jelöli meg a feladatot. Ez elengedhetetlen, ha a Batch-megoldás [JobReleaseTask][net_jobreltask] tevékenységet használ. Ez egy speciális típusú feladat, amelyről a [feladatok előkészítési és befejezési tevékenységeit](batch-job-prep-release.md) ismertető szakaszban olvashat.

A *DotNetTutorial* `Program.cs` fájljának `MonitorTasks` metódusa az alábbiakban látható:

```
private static async Task<bool> MonitorTasks(
    BatchClient batchClient,
    string jobId,
    TimeSpan timeout)
{
    bool allTasksSuccessful = true;
    const string successMessage = "All tasks reached state Completed.";
    const string failureMessage = "One or more tasks failed to reach the Completed state within the timeout period.";

    // Obtain the collection of tasks currently managed by the job. Note that we use
    // a detail level to specify that only the "id" property of each task should be
    // populated. Using a detail level for all list operations helps to lower
    // response time from the Batch service.
    ODATADetailLevel detail = new ODATADetailLevel(selectClause: "id");
    List<CloudTask> tasks =
        await batchClient.JobOperations.ListTasks(JobId, detail).ToListAsync();

    Console.WriteLine("Awaiting task completion, timeout in {0}...", timeout.ToString());

    // We use a TaskStateMonitor to monitor the state of our tasks. In this case, we
    // will wait for all tasks to reach the Completed state.
    TaskStateMonitor taskStateMonitor = batchClient.Utilities.CreateTaskStateMonitor();
    bool timedOut = await taskStateMonitor.WaitAllAsync(
        tasks,
        TaskState.Completed,
        timeout);

    if (timedOut)
    {
        allTasksSuccessful = false;

        await batchClient.JobOperations.TerminateJobAsync(jobId, failureMessage);

        Console.WriteLine(failureMessage);
    }
    else
    {
        await batchClient.JobOperations.TerminateJobAsync(jobId, successMessage);

        // All tasks have reached the "Completed" state. However, this does not
        // guarantee that all tasks were completed successfully. Here we further
        // check each task's ExecutionInfo property to ensure that it did not
        // encounter a scheduling error or return a non-zero exit code.

        // Update the detail level to populate only the task id and executionInfo
        // properties. We refresh the tasks below, and need only this information
        // for each task.
        detail.SelectClause = "id, executionInfo";

        foreach (CloudTask task in tasks)
        {
            // Populate the task's properties with the latest info from the Batch service
            await task.RefreshAsync(detail);

            if (task.ExecutionInformation.SchedulingError != null)
            {
                // A scheduling error indicates a problem starting the task on the
                // node. It is important to note that the task's state can be
                // "Completed," yet the task still might have encountered a
                // scheduling error.

                allTasksSuccessful = false;

                Console.WriteLine(
                    "WARNING: Task [{0}] encountered a scheduling error: {1}",
                    task.Id,
                    task.ExecutionInformation.SchedulingError.Message);
            }
            else if (task.ExecutionInformation.ExitCode != 0)
            {
                // A non-zero exit code may indicate that the application executed by
                // the task encountered an error during execution. As not every
                // application returns non-zero on failure by default (e.g. robocopy),
                // your implementation of error checking may differ from this example.

                allTasksSuccessful = false;

                Console.WriteLine("WARNING: Task [{0}] returned a non-zero exit code - this may indicate task execution or completion failure.", task.Id);
            }
        }
    }

    if (allTasksSuccessful)
    {
        Console.WriteLine("Success! All tasks completed successfully within the specified timeout period.");
    }

    return allTasksSuccessful;
}
```

## 7. lépés: Feladat kimenetének letöltése

![Feladat kimenetének letöltése a Storage-ból][7]<br/>

Most, hogy a feladat befejeződött, a tevékenységek kimenete letölthető az Azure Storage-ból. Ez a `DownloadBlobsFromContainerAsync` hívásával végezhető el a *DotNetTutorial* `Program.cs` fájljában:

```
private static async Task DownloadBlobsFromContainerAsync(
    CloudBlobClient blobClient,
    string containerName,
    string directoryPath)
{
        Console.WriteLine("Downloading all files from container [{0}]...", containerName);

        // Retrieve a reference to a previously created container
        CloudBlobContainer container = blobClient.GetContainerReference(containerName);

        // Get a flat listing of all the block blobs in the specified container
        foreach (IListBlobItem item in container.ListBlobs(
                    prefix: null,
                    useFlatBlobListing: true))
        {
                // Retrieve reference to the current blob
                CloudBlob blob = (CloudBlob)item;

                // Save blob contents to a file in the specified folder
                string localOutputFile = Path.Combine(directoryPath, blob.Name);
                await blob.DownloadToFileAsync(localOutputFile, FileMode.Create);
        }

        Console.WriteLine("All files downloaded to {0}", directoryPath);
}
```

> [AZURE.NOTE] A *DotNetTutorial* alkalmazásban a `DownloadBlobsFromContainerAsync` hívása határozza meg, hogy a fájlokat a `%TEMP%` mappába kell letölteni. Ezt a kimeneti helyet nyugodtan módosíthatja.

## 8. lépés: Tárolók törlése

Mivel az Azure Storage-ban lévő adatok díjkötelesek, mindig célszerű eltávolítani azokat a blobokat, amelyekre már nincs szükség a Batch-feladatokhoz. A DotNetTutorial `Program.cs` fájljában ez a `DeleteContainerAsync` segítőmetódus három hívásával végezhető el:

```
// Clean up Storage resources
await DeleteContainerAsync(blobClient, appContainerName);
await DeleteContainerAsync(blobClient, inputContainerName);
await DeleteContainerAsync(blobClient, outputContainerName);
```

Maga a metódus csak beszerez egy hivatkozást, amely a tárolóra mutat, majd meghívja a [CloudBlobContainer.DeleteIfExistsAsync][net_container_delete] metódust:

```
private static async Task DeleteContainerAsync(
    CloudBlobClient blobClient,
    string containerName)
{
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);

    if (await container.DeleteIfExistsAsync())
    {
        Console.WriteLine("Container [{0}] deleted.", containerName);
    }
    else
    {
        Console.WriteLine("Container [{0}] does not exist, skipping deletion.",
            containerName);
    }
}
```

## 9. lépés: A feladat és a készlet törlése

Az utolsó lépésben a rendszer arra kéri a felhasználót, hogy törölje a DotNetTutorial-alkalmazás által létrehozott feladatot és készletet. Bár magukért a munkákért és feladatokért nem kell fizetnie, a számítási csomópontokért *igen*. Ezért ajánlott csak szükség szerint lefoglalni a csomópontokat. A nem használt készletek törlése a karbantartási folyamat része lehet.

A BatchClient [JobOperations][net_joboperations] és [PoolOperations][net_pooloperations] művelete is rendelkezik megfelelő törlési metódusokkal, amelyeket a rendszer meghív, ha a felhasználó megerősíti a törlést:

```
// Clean up the resources we've created in the Batch account if the user so chooses
Console.WriteLine();
Console.WriteLine("Delete job? [yes] no");
string response = Console.ReadLine().ToLower();
if (response != "n" && response != "no")
{
    await batchClient.JobOperations.DeleteJobAsync(JobId);
}

Console.WriteLine("Delete pool? [yes] no");
response = Console.ReadLine();
if (response != "n" && response != "no")
{
    await batchClient.PoolOperations.DeletePoolAsync(PoolId);
}
```

> [AZURE.IMPORTANT] Ne feledje, hogy a számítási erőforrások díjkötelesek – a nem használt készletek törlése minimalizálja a költségeket. Azt is vegye figyelembe, hogy a készletek törlése törli a készleten belüli összes számítási csomópontot, és hogy a csomópontokon lévő adatok nem állíthatók helyre a készlet törlése után.

## A *DotNetTutorial*-minta futtatása

A mintaalkalmazás futtatásakor a konzol kimenete az alábbihoz hasonló lesz. A futtatás során szünet történik a következőnél a készlet számítási csomópontjainak indításakor: `Awaiting task completion, timeout in 00:30:00...`. A [Batch Explorer][github_batchexplorer] használatával figyelheti meg a készletet, a számítási csomópontokat, a feladatokat és a tevékenységeket a végrehajtás alatt és után. Az [Azure Portallal][azure_portal] vagy az egyik [elérhető Azure Storage-tallózóval][storage_explorers] tekintheti meg az alkalmazás által létrehozott tárolási erőforrásokat (tárolókat és blobokat).

A jellemző végrehajtási idő **körülbelül 5 perc**, ha az alapértelmezett konfigurációban futtatja az alkalmazást.

```
Sample start: 1/8/2016 09:42:58 AM

Container [application] created.
Container [input] created.
Container [output] created.
Uploading file C:\repos\azure-batch-samples\CSharp\ArticleProjects\DotNetTutorial\bin\Debug\TaskApplication.exe to container [application]...
Uploading file Microsoft.WindowsAzure.Storage.dll to container [application]...
Uploading file ..\..\taskdata1.txt to container [input]...
Uploading file ..\..\taskdata2.txt to container [input]...
Uploading file ..\..\taskdata3.txt to container [input]...
Creating pool [DotNetTutorialPool]...
Creating job [DotNetTutorialJob]...
Adding 3 tasks to job [DotNetTutorialJob]...
Awaiting task completion, timeout in 00:30:00...
Success! All tasks completed successfully within the specified timeout period.
Downloading all files from container [output]...
All files downloaded to C:\Users\USERNAME\AppData\Local\Temp
Container [application] deleted.
Container [input] deleted.
Container [output] deleted.

Sample end: 1/8/2016 09:47:47 AM
Elapsed time: 00:04:48.5358142

Delete job? [yes] no: yes
Delete pool? [yes] no: yes

Sample complete, hit ENTER to exit...
```

## Következő lépések

Nyugodtan módosítsa a *DotNetTutorial* és a *TaskApplication* elemeket a különböző számítási forgatókönyvekkel való kísérletezéshez. Próbáljon meg például végrehajtási késleltetést adni a *TaskApplication* elemhez, például a [Thread.Sleep][net_thread_sleep] paranccsal, így hosszan futó feladatokat szimulálhat, és megfigyelheti őket a Batch Explorer *Hőtérkép* funkciójával. Próbáljon meg több tevékenységet hozzáadni, vagy módosítani a számítási csomópontok számát. Adja hozzá az ellenőrizni kívánt logikát, és engedélyezze meglévő készlet használatát a futtatási idő csökkentéséhez (*tipp*: tekintse meg az `ArticleHelpers.cs` témakört a [Microsoft.Azure.Batch.Samples.Common][github_samples_common] projektben az [azure-batch-samples][github_samples] mintákban).

Most, hogy megismerte a Batch-megoldások alapvető munkafolyamatát, a Batch szolgáltatás további funkcióit is áttekintheti.

- Ha korábban nem használta a szolgáltatást, olvassa el [az Azure Batch szolgáltatásainak áttekintését](batch-api-basics.md) tartalmazó cikket.
- Kezdje tanulmányozni a többi Batch-fejlesztéssel kapcsolatos cikket a [Batch képzési terv][batch_learning_path] **Fejlesztés részletesebben** részében.
- Az „első N szó” számítási feladat Batch használatával való feldolgozásának egy másik megvalósítását a [TopNWords][github_topnwords] mintában találja.

[azure_batch]: https://azure.microsoft.com/services/batch/
[azure_free_account]: https://azure.microsoft.com/free/
[azure_portal]: https://portal.azure.com
[batch_explorer_blog]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch_learning_path]: https://azure.microsoft.com/documentation/learning-paths/batch/
[blog_linux]: http://blogs.technet.com/b/windowshpc/archive/2016/03/30/introducing-linux-support-on-azure-batch.aspx
[github_batchexplorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[github_dotnettutorial]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/DotNetTutorial
[github_samples]: https://github.com/Azure/azure-batch-samples
[github_samples_common]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/Common
[github_samples_zip]: https://github.com/Azure/azure-batch-samples/archive/master.zip
[github_topnwords]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/TopNWords
[net_api]: http://msdn.microsoft.com/library/azure/mt348682.aspx
[net_api_storage]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudblobclient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobclient.aspx
[net_cloudblobcontainer]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudserviceconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudserviceconfiguration.aspx
[net_container_delete]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.deleteifexistsasync.aspx
[net_job]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_job_poolinfo]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.protocol.models.cloudjob.poolinformation.aspx
[net_joboperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.joboperations
[net_joboperations_terminatejob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.joboperations.terminatejobasync.aspx
[net_jobpreptask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobpreparationtask.aspx
[net_jobreltask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.jobreleasetask.aspx
[net_node]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.aspx
[net_odatadetaillevel]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.odatadetaillevel.aspx
[net_pool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_pool_create]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.pooloperations.createpool.aspx
[net_pool_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.starttask.aspx
[net_pooloperations]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.pooloperations
[net_resourcefile]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.aspx
[net_resourcefile_blobsource]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.resourcefile.blobsource.aspx
[net_sas_blob]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblob.getsharedaccesssignature.aspx
[net_sas_container]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.cloudblobcontainer.getsharedaccesssignature.aspx
[net_starttask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.aspx
[net_starttask_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.starttask.resourcefiles.aspx
[net_task]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_task_resourcefiles]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.resourcefiles.aspx
[net_taskstate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.common.taskstate.aspx
[net_taskstatemonitor]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.taskstatemonitor.aspx
[net_thread_sleep]: https://msdn.microsoft.com/library/274eh01d(v=vs.110).aspx
[net_virtualmachineconfiguration]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.virtualmachineconfiguration.aspx
[nuget_packagemgr]: https://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
[nuget_restore]: https://docs.nuget.org/consume/package-restore/msbuild-integrated#enabling-package-restore-during-build
[storage_explorers]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx
[visual_studio]: https://www.visualstudio.com/products/vs-2015-product-editions

[1]: ./media/batch-dotnet-get-started/batch_workflow_01_sm.png "Tárolók létrehozása az Azure Storage-ban"
[2]: ./media/batch-dotnet-get-started/batch_workflow_02_sm.png "Tevékenységalkalmazás- és bemeneti (adat-) fájlok feltöltése tárolókba"
[3]: ./media/batch-dotnet-get-started/batch_workflow_03_sm.png "Batch-készlet létrehozása"
[4]: ./media/batch-dotnet-get-started/batch_workflow_04_sm.png "Batch-feladat létrehozása"
[5]: ./media/batch-dotnet-get-started/batch_workflow_05_sm.png "Tevékenységek hozzáadása a feladathoz"
[6]: ./media/batch-dotnet-get-started/batch_workflow_06_sm.png "Tevékenységek figyelése"
[7]: ./media/batch-dotnet-get-started/batch_workflow_07_sm.png "Feladat kimenetének letöltése a Storage-ból"
[8]: ./media/batch-dotnet-get-started/batch_workflow_sm.png "Batch-megoldás munkafolyamata (teljes diagram)"
[9]: ./media/batch-dotnet-get-started/credentials_batch_sm.png "A Batch hitelesítő adatai a portálon"
[10]: ./media/batch-dotnet-get-started/credentials_storage_sm.png "A Storage hitelesítő adatai a portálon"
[11]: ./media/batch-dotnet-get-started/batch_workflow_minimal_sm.png "Batch-megoldás munkafolyamata (minimális diagram)"



<!--HONumber=jun16_HO2-->


