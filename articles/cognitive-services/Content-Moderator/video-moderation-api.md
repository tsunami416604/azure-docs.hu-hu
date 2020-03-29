---
title: Videótartalom elemzése kifogásolható anyagokhoz C# - Tartalommoderátor
titleSuffix: Azure Cognitive Services
description: Videótartalom elemzése különböző kifogásolható anyagokhoz a tartalommoderátor SDK használatával a .
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 71858755fe31823d4d7ef8623b915db851530116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72755238"
---
# <a name="analyze-video-content-for-objectionable-material-in-c"></a>Videótartalom elemzése kifogásolható anyagokhoz C-ben #

Ez a cikk információkat és kódmintákat tartalmaz, amelyek segítenek a [.NET tartalommoderátor SDK használatával](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) a videotartalmak felnőtt vagy pikáns tartalom ának vizsgálatához.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené. 

## <a name="prerequisites"></a>Előfeltételek
- A [Visual Studio 2015 vagy 2017](https://www.visualstudio.com/downloads/) bármely kiadása.

## <a name="set-up-azure-resources"></a>Az Azure-erőforrások beállítása

A tartalommoderátor videómoderálási képessége ingyenes nyilvános előzetes **médiafeldolgozóként** érhető el az Azure Media Services (AMS) szolgáltatásban. Az Azure Media Services egy speciális Azure-szolgáltatás a videotartalmak tárolására és streamelésére. 

### <a name="create-an-azure-media-services-account"></a>Azure Media Services-fiók létrehozása

Kövesse az [Azure Media Services-fiók létrehozása](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) című, az AMS-re való előfizetéshez és a társított Azure-tárfiók létrehozásához című útmutatóutasításait. Ebben a tárfiókban hozzon létre egy új Blob storage-tárolót.

### <a name="create-an-azure-active-directory-application"></a>Azure Active Directory-alkalmazás létrehozása

Nyissa meg az új AMS-előfizetést az Azure Portalon, és válassza az **API-hozzáférést** az oldalsó menüből. Válassza **a Csatlakozás az Azure Media Services szolgáltatáshoz egyszerű szolgáltatással**lehetőséget. Figyelje meg a **REST API végpontmezőjének** értékét; erre később szüksége lesz.

Az **Azure AD alkalmazás** szakaszban válassza az **Új létrehozása lehetőséget,** és nevezze el az új Azure AD-alkalmazásregisztrációt (például "VideoModADApp"). Kattintson **a Mentés gombra,** és várjon néhány percet, amíg az alkalmazás konfigurálva van. Ezután meg kell jelennie az új alkalmazás regisztrációja az **Azure AD alkalmazás** szakasza az oldal.

Jelölje ki az alkalmazásregisztrációt, és kattintson az **alatta lévő Alkalmazás kezelése** gombra. Jegyezze fel az **alkalmazásazonosító** mező értékét; erre később szüksége lesz. Válassza a **Beállítások** > **kulcsok**lehetőséget, és adja meg egy új kulcs leírását (például "VideoModKey"). Kattintson a **Mentés gombra,** majd figyelje meg az új kulcsértéket. Másolja ezt a karakterláncot, és mentse el biztonságos helyre.

A fenti folyamat alaposabb forgatókönyvét az [Azure AD-hitelesítés sel való ismerkedés című témakörben ismeri el.](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad)

Miután ezt megtette, a videomoderálás médiaprocesszorát kétféleképpen használhatja.

## <a name="use-azure-media-services-explorer"></a>Az Azure Media Services Explorer használata

Az Azure Media Services Explorer egy felhasználóbarát előtér az AMS. Ezzel böngészhet AMS-fiókjában, videókat tölthet fel, és tartalmakat szannak a Tartalommoderátor médiaprocesszorával. Töltse le és telepítse a [GitHubról,](https://github.com/Azure/Azure-Media-Services-Explorer/releases)vagy tekintse meg az [Azure Media Services Explorer blogbejegyzést](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) további információkért.

![Az Azure Media Services intézője tartalommoderátorral](images/ams-explorer-content-moderator.PNG)

## <a name="create-the-visual-studio-project"></a>A Visual Studio-projekt létrehozása

1. A Visual Studio-ban hozzon létre egy új **Konzolalkalmazás-projektet (.NET Framework),** és nevezze el **VideoModeration**néven. 
1. Ha más projektek is vannak a megoldásban, válassza ki ezt a projektet az egyedüli kezdőprojektként.
1. Szerezze be a szükséges NuGet-csomagokat. A Solution Explorer (Megoldáskezelő) felületén kattintson a jobb gombbal a projektre és válassza a **Manage NuGet Packages** (NuGet-csomagok kezelése) lehetőséget, majd keresse meg és telepítse a következő csomagokat:
    - windowsazure.mediaservices
    - windowsazure.mediaservices.extensions

## <a name="add-video-moderation-code"></a>Videómoderációs kód hozzáadása

Következő lépésként másolja és illessze be az ebben az útmutatóban található kódot a projektjébe egy alapszintű tartalommoderálási forgatókönyv megvalósításához.

### <a name="update-the-programs-using-statements"></a>A program „using” utasításainak frissítése

Adja hozzá az alábbi `using` utasításokat a _Program.cs_ fájl elejéhez.

```csharp
using System;
using System.Linq;
using System.Threading.Tasks;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.IO;
using System.Threading;
using Microsoft.WindowsAzure.Storage.Blob;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using System.Collections.Generic;
```

### <a name="set-up-resource-references"></a>Erőforrás-hivatkozások beállítása

Adja hozzá a következő **Program** statikus mezőket _a programosztályhoz Program.cs._ Ezek a mezők az AMS-előfizetéshez való csatlakozáshoz szükséges információkat tartalmazják. Töltse ki őket a fenti lépésekben kapott értékekkel. Vegye `CLIENT_ID` figyelembe, hogy az **Azure AD-alkalmazás alkalmazásazonosító** értéke, és `CLIENT_SECRET` az adott alkalmazáshoz létrehozott "VideoModKey" értéke.

```csharp
// declare constants and globals
private static CloudMediaContext _context = null;
private static CloudStorageAccount _StorageAccount = null;

// Azure Media Services (AMS) associated Storage Account, Key, and the Container that has 
// a list of Blobs to be processed.
static string STORAGE_NAME = "YOUR AMS ASSOCIATED BLOB STORAGE NAME";
static string STORAGE_KEY = "YOUR AMS ASSOCIATED BLOB STORAGE KEY";
static string STORAGE_CONTAINER_NAME = "YOUR BLOB CONTAINER FOR VIDEO FILES";

private static StorageCredentials _StorageCredentials = null;

// Azure Media Services authentication. 
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID";
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";

// JSON settings file
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

```

Ha helyi videofájlt szeretne használni (legegyszerűbb eset), adja hozzá a projekthez, `INPUT_FILE` és adja meg az elérési útját értékként (a relatív elérési utak a végrehajtási könyvtárhoz viszonyítva vannak).

A _preset.json_ fájlt az aktuális könyvtárban is létre kell hoznia, és ezzel meg kell adnia egy verziószámot. Példa:

```JSON
{
    "version": "2.0"
}
```

### <a name="load-the-input-videos"></a>A bemeneti videó(k) betöltése

A **programosztály** **fő** metódusa létrehoz egy Azure Media-környezetet, majd egy Azure Storage-környezetet (abban az esetben, ha a videók blob storage-ban vannak). A fennmaradó kód egy videót egy helyi mappából, blobból vagy több blobból vizsgál egy Azure storage-tárolóban. Megpróbálhatja az összes lehetőséget a többi kódsor megjegyzésével.

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
// RunContentModeratorJobOnBlobs();
```

### <a name="create-an-azure-media-context"></a>Azure Media-környezet létrehozása

Adja hozzá a következő módszert a **Program** osztályhoz. Ez az AMS-hitelesítő adatokat használja az AMS-sel való kommunikáció engedélyezéséhez.

```csharp
// Creates a media context from azure credentials
static void CreateMediaContext()
{
    // Get Azure AD credentials
    var tokenCredentials = new AzureAdTokenCredentials(AZURE_AD_TENANT_NAME,
        new AzureAdClientSymmetricKey(CLIENT_ID, CLIENT_SECRET),
        AzureEnvironments.AzureCloudEnvironment);

    // Initialize an Azure AD token
    var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

    // Create a media context
    _context = new CloudMediaContext(new Uri(REST_API_ENDPOINT), tokenProvider);
}
```

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Adja hozzá a kódot egy Azure Storage-környezet létrehozásához

Adja hozzá a következő módszert a **Program** osztályhoz. A tárolási hitelesítő adatokból létrehozott tárolási környezet használatával érheti el a blob storage.You use the Storage Context, created from your storage, to access your blob storage.

```csharp
// Creates a storage context from the AMS associated storage name and key
static void CreateStorageContext()
{
    // Get a reference to the storage account associated with a Media Services account. 
    if (_StorageCredentials == null)
    {
        _StorageCredentials = new StorageCredentials(STORAGE_NAME, STORAGE_KEY);
    }
    _StorageAccount = new CloudStorageAccount(_StorageCredentials, false);
}
```

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Adja hozzá a kódot az Azure Media Assets helyi fájlból és blobból való létrehozásához

A tartalommoderátor médiafeldolgozó feladatokat futtat az Azure Media Services platformon belüli **eszközökön.**
Ezek a módszerek létre az eszközök egy helyi fájlból vagy egy társított blob.

```csharp
// Creates an Azure Media Services Asset from the video file
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

// Creates an Azure Media Services asset from your blog storage
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Adja hozzá a kódot a tárolón belüli videók gyűjteményének (blobként) beszkafításához

```csharp
// Runs the Content Moderator Job on all Blobs in a given container name
static void RunContentModeratorJobOnBlobs()
{
    // Get the reference to the list of Blobs. See the following method.
    var blobList = GetBlobsList();

    // Iterate over the Blob list items or work on specific ones as needed
    foreach (var sourceBlob in blobList)
    {
        // Create an Asset
        IAsset asset = _context.Assets.CreateFromBlob((CloudBlockBlob)sourceBlob,
                            _StorageCredentials, AssetCreationOptions.None);
        asset.Update();

        // Submit to Content Moderator
        RunContentModeratorJob(asset);
    }
}

// Get all blobs in your container
static IEnumerable<IListBlobItem> GetBlobsList()
{
    // Get a reference to the Container within the Storage Account
    // that has the files (blobs) for moderation
    CloudBlobClient CloudBlobClient = _StorageAccount.CreateCloudBlobClient();
    CloudBlobContainer MediaBlobContainer = CloudBlobClient.GetContainerReference(STORAGE_CONTAINER_NAME);

    // Get the reference to the list of Blobs 
    var blobList = MediaBlobContainer.ListBlobs();
    return blobList;
}
```

### <a name="add-the-method-to-run-the-content-moderator-job"></a>A tartalommoderátori feladat futtatásához a metódus hozzáadása

```csharp
// Run the Content Moderator job on the designated Asset from local file or blob storage
static void RunContentModeratorJob(IAsset asset)
{
    // Grab the presets
    string configuration = File.ReadAllText(CONTENT_MODERATOR_PRESET_FILE);

    // grab instance of Azure Media Content Moderator MP
    IMediaProcessor mp = _context.MediaProcessors.GetLatestMediaProcessorByName(MEDIA_PROCESSOR);

    // create Job with Content Moderator task
    IJob job = _context.Jobs.Create(String.Format("Content Moderator {0}",
            asset.AssetFiles.First() + "_" + Guid.NewGuid()));

    ITask contentModeratorTask = job.Tasks.AddNew("Adult and racy classifier task",
            mp, configuration,
            TaskOptions.None);
    contentModeratorTask.InputAssets.Add(asset);
    contentModeratorTask.OutputAssets.AddNew("Adult and racy classifier output",
        AssetCreationOptions.None);

    job.Submit();


    // Create progress printing and querying tasks
    Task progressPrintTask = new Task(() =>
    {
        IJob jobQuery = null;
        do
        {
            var progressContext = _context;
            jobQuery = progressContext.Jobs
            .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}",
                DateTime.Now,
                jobQuery.State));
                Thread.Sleep(10000);
            }
            while (jobQuery.State != JobState.Finished &&
            jobQuery.State != JobState.Error &&
            jobQuery.State != JobState.Canceled);
    });
    progressPrintTask.Start();

    Task progressJobTask = job.GetExecutionProgressTask(
    CancellationToken.None);
    progressJobTask.Wait();

    // If job state is Error, the event handling 
    // method for job progress should log errors.  Here we check 
    // for error state and exit if needed.
    if (job.State == JobState.Error)
    {
        ErrorDetail error = job.Tasks.First().ErrorDetails.First();
        Console.WriteLine(string.Format("Error: {0}. {1}",
        error.Code,
        error.Message));
    }

    DownloadAsset(job.OutputMediaAssets.First(), OUTPUT_FOLDER);
}
```

### <a name="add-helper-functions"></a>Segítő függvények hozzáadása

Ezek a módszerek letöltik a tartalommoderátor kimeneti fájlját (JSON) az Azure Media Services-eszközből, és segítenek nyomon követni a moderálási feladat állapotát, hogy a program naplózhassa a futó állapotot a konzolra.

```csharp
static void DownloadAsset(IAsset asset, string outputDirectory)
{
    foreach (IAssetFile file in asset.AssetFiles)
    {
        file.Download(Path.Combine(outputDirectory, file.Name));
    }
}

// event handler for Job State
static void StateChanged(object sender, JobStateChangedEventArgs e)
{
    Console.WriteLine("Job state changed event:");
    Console.WriteLine("  Previous state: " + e.PreviousState);
    Console.WriteLine("  Current state: " + e.CurrentState);
    switch (e.CurrentState)
    {
        case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("Job finished.");
            break;
        case JobState.Canceling:
        case JobState.Queued:
        case JobState.Scheduled:
        case JobState.Processing:
            Console.WriteLine("Please wait...\n");
            break;
        case JobState.Canceled:
            Console.WriteLine("Job is canceled.\n");
            break;
        case JobState.Error:
            Console.WriteLine("Job failed.\n");
            break;
        default:
            break;
    }
}
```

### <a name="run-the-program-and-review-the-output"></a>A program futtatása és a kimenet áttekintése

A Tartalommoderálási feladat befejezése után elemezze a JSON-választ. A következő elemekből áll:

- Videóinformációk összegzése
- **Lövések** a "**töredékek**"
- **A kulcskeretek** "**események**" **és véleményezésajánlott" (= igaz vagy hamis)"** **jelzőként, felnőtt** és **pikáns** pontszámok alapján
- **start**, **duration**, **totalDuration**és **timestamp** a "ticks". A szám másodpercben való lefoglalásához osszuk el az **időskálát.**
 
> [!NOTE]
> - `adultScore`az olyan tartalom potenciális jelenlétét és előrejelzési pontszámát jelöli, amely bizonyos helyzetekben szexuálisan explicitnek vagy felnőttnek tekinthető.
> - `racyScore`az olyan tartalom potenciális jelenlétét és előrejelzési pontszámát jelöli, amely bizonyos helyzetekben szexuálisan szuggesztívnek vagy érettnek tekinthető.
> - `adultScore`és `racyScore` 0 és 1 között van. Minél magasabb a pontszám, annál magasabb a modell azt jósolja, hogy a kategória alkalmazható lehet. Ez az előzetes verzió a manuálisan kódolt eredmények helyett egy statisztikai modellen alapul. Javasoljuk, hogy a saját tartalommal tesztelje annak meghatározásához, hogy az egyes kategóriák hogyan igazodjanak az Ön igényeihez.
> - `reviewRecommended`vagy igaz, vagy hamis a belső pontszám küszöbértékek. Az ügyfeleknek fel kell mérniük, hogy ezt az értéket használják-e, vagy a tartalmi irányelveik alapján döntenek-e az egyéni küszöbértékek ről.

```json
{
"version": 2,
"timescale": 90000,
"offset": 0,
"framerate": 50,
"width": 1280,
"height": 720,
"totalDuration": 18696321,
"fragments": [
{
    "start": 0,
    "duration": 18000
},
{
    "start": 18000,
    "duration": 3600,
    "interval": 3600,
    "events": [
    [
        {
        "reviewRecommended": false,
        "adultScore": 0.00001,
        "racyScore": 0.03077,
        "index": 5,
        "timestamp": 18000,
        "shotIndex": 0
        }
    ]
    ]
},
{
    "start": 18386372,
    "duration": 119149,
    "interval": 119149,
    "events": [
    [
        {
        "reviewRecommended": true,
        "adultScore": 0.00000,
        "racyScore": 0.91902,
        "index": 5085,
        "timestamp": 18386372,
        "shotIndex": 62
        }
    ]
    ]
}
]
}
```

## <a name="next-steps"></a>További lépések

További információ [arról,](video-reviews-quickstart-dotnet.md) hogyan hozhat létre videoértékeléseket a moderálási kimenetből.

Adja hozzá [az átirat moderálását](video-transcript-moderation-review-tutorial-dotnet.md) a videóértékelésekhez.

Nézze meg a részletes bemutató, hogyan kell építeni egy [teljes videó és átirat moderálás megoldás](video-transcript-moderation-review-tutorial-dotnet.md).

[Töltse le ehhez a Visual Studio-megoldáshoz](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) és a .NET tartalommoderátor egyéb rövid útmutatóihoz.
