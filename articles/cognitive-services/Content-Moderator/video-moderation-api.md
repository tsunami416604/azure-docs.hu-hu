---
title: 'Gyors útmutató: A C# - Content Moderator videótartalmak ellenőrzése'
titlesuffix: Azure Cognitive Services
description: Hogyan ellenőrizheti a Moderator tartalom SDK használatának a C# felnőtt vagy pikáns anyag videó tartalmát
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: sajagtap
ms.openlocfilehash: cb97eebcf398137653988ab3b6ef663f987fb57a
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310504"
---
# <a name="quickstart-check-video-content-in-c"></a>Gyors útmutató: Ellenőrizze a C# videotartalmaikat

Online megtekintők még ma, népszerű és regionális közösségimédia-webhelyek és alkalmazások készítése a videó nézetek több milliárd. Alkalmazása machine learning-alapú szolgáltatások által észlelt potenciális felnőtt és szexuális tartalom csökkenthető a moderálás munkája költségét.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="sign-up-for-the-content-moderator-media-processor-preview"></a>Iratkozzon fel a Content Moderator médiafeldolgozót (előzetes verzió)

### <a name="create-an-azure-media-services-account"></a>Azure Media Services-fiók létrehozása

A Content Moderator videó funkció érhető el a nyilvános előzetes verzió **médiafeldolgozót** az Azure Media Services (AMS) díjmentesen. [Az Azure Media Services-fiók létrehozása](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) az Azure-előfizetésében.

### <a name="get-azure-active-directory-credentials"></a>Azure Active Directory hitelesítő adatainak lekérése

   1. Olvassa el a [Azure Media Services portál cikk](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad) megtudhatja, hogyan használhatja az Azure Portalon az Azure Active Directory hitelesítő adatok beolvasása.
   1. Olvassa el a [Azure Media Services .NET-cikk](https://docs.microsoft.com/azure/media-services/media-services-dotnet-get-started-with-aad) , az Azure Active Directory hitelesítő adatok használata a .NET SDK használatával.

   > [!NOTE]
   > Ebben a rövid útmutatóban található mintakódot használja a **egyszerű szolgáltatásnév hitelesítése** is a fenti cikkekben leírt módszer.

Miután az AMS hitelesítő adatait, két módon szeretné kipróbálni a Content Moderator médiafeldolgozót.

## <a name="use-azure-media-services-explorer"></a>Az Azure Media Services Explorer használata

Interaktív [Azure Media Services (AMS) explorer](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) keresse meg az AMS-fiók, videókat tölthet fel, és a Content Moderator médiaprocesszorral vizsgálata. [Töltse le és telepítse azt](https://github.com/Azure/Azure-Media-Services-Explorer/releases) a Githubról, és [keresse meg a forráskódot](http://github.com/Azure/Azure-Media-Services-Explorer) helyezésétől AMS SDK-val.

![Content moderator használatát az Azure Media Services explorer](images/ams-explorer-content-moderator.PNG)

## <a name="quickstart-with-visual-studio-and-c"></a>A Visual Studio és C# Gyorsútmutató

1. Adjon hozzá egy új **Konzolalkalmazás (.NET-keretrendszer)** projektet a megoldáshoz.

   A mintakód adja a projektnek **VideoModeration**.

1. Válassza ki ezt a projektet a megoldás egyedüli kezdőprojektjeként.

### <a name="install-required-packages"></a>Szükséges csomagok telepítése

Elérhető a következő NuGet-csomagok telepítése [NuGet](https://www.nuget.org/).

- windowsazure.mediaservices
- windowsazure.mediaservices.Extensions

### <a name="update-the-programs-using-statements"></a>A program „using” utasításainak frissítése

Adja hozzá a következő `using` utasításokat.

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

### <a name="initialize-application-specific-settings"></a>Alkalmazásra jellemző beállítások inicializálása

Adja hozzá a következő statikus mezőket a **Program** osztály _Program.cs_.

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

// Azure Media Services authentication. See the quickstart for how to get these.
private const string AZURE_AD_TENANT_NAME = "microsoft.onmicrosoft.com";
private const string CLIENT_ID = "YOUR CLIENT ID"
private const string CLIENT_SECRET = "YOUR CLIENT SECRET";

// REST API endpoint, for example "https://accountname.restv2.westcentralus.media.azure.net/API".      
private const string REST_API_ENDPOINT = "YOUR API ENDPOINT";

// Content Moderator Media Processor Nam
private const string MEDIA_PROCESSOR = "Azure Media Content Moderator";

// Input and Output files in the current directory of the executable
private const string INPUT_FILE = "VIDEO FILE NAME";
private const string OUTPUT_FOLDER = "";
```

### <a name="create-a-preset-file-json"></a>Hozzon létre egy előre megadott fájl (json)

Hozzon létre egy JSON-fájl verziószámával az aktuális könyvtárban található.

```csharp
//Example file content:
//        {
//             "version": "2.0"
//        }
private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";
```

### <a name="add-the-following-code-to-the-main-method"></a>Adja hozzá a következő kódot a fő módszer

A fő módszert először létrehoz egy Azure Media-környezetet és egy Azure Storage-környezetet arra az esetre, a videók a blob storage-ban. A fennmaradó kódját egy helyi mappába, a blob vagy egy Azure storage-tároló belül több blobok videó átvizsgálja. Minden beállítás meg a többi sornyi kód megjegyzéseket úgy próbálhatja ki.

```csharp
// Create Azure Media Context
CreateMediaContext();

// Create Storage Context
CreateStorageContext();

// Use a file as the input.
// IAsset asset = CreateAssetfromFile();

// -- OR ---

// Or a blob as the input
// IAsset asset = CreateAssetfromBlob((CloudBlockBlob)GetBlobsList().First());

// Then submit the asset to Content Moderator
// RunContentModeratorJob(asset);

//-- OR ----

// Just run the content moderator on all blobs in a list (from a Blob Container)
RunContentModeratorJobOnBlobs();
```

### <a name="add-the-code-to-create-an-azure-media-context"></a>Adja hozzá a kódot egy Azure Media-környezet létrehozása

```csharp
/// <summary>
/// Creates a media context from azure credentials
/// </summary>
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

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Adja hozzá a kódot egy Azure Storage-környezet létrehozása
A tárolási környezet, a Storage hitelesítő adatai alapján létrehozott segítségével a blobtároló eléréséhez.

```csharp
/// <summary>
/// Creates a storage context from the AMS associated storage name and key
/// </summary>
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

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Adja hozzá a kódot az Azure Media objektumok létrehozásához a helyi fájl és blob
A Content Moderator médiafeldolgozót feladatokat futtat **eszközök** az Azure Media Services platformon.
Ezek a metódusok az adategységek létrehozása egy helyi fájlból vagy egy társított blob.

```csharp
/// <summary>
/// Creates an Azure Media Services Asset from the video file
/// </summary>
/// <returns>Asset</returns>
static IAsset CreateAssetfromFile()
{
    return _context.Assets.CreateFromFile(INPUT_FILE, AssetCreationOptions.None); ;
}

/// <summary>
/// Creates an Azure Media Services asset from your blog storage
/// </summary>
/// <param name="Blob"></param>
/// <returns>Asset</returns>
static IAsset CreateAssetfromBlob(CloudBlockBlob Blob)
{
    // Create asset from the FIRST blob in the list and return it
    return _context.Assets.CreateFromBlob(Blob, _StorageCredentials, AssetCreationOptions.None);
}
```

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Adja hozzá a kódot egy tárolón belül a videók (blobként) gyűjtemény beolvasása

```csharp
/// <summary>
/// Runs the Content Moderator Job on all Blobs in a given container name
/// </summary>
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

/// <summary>
/// Get all blobs in your container
/// </summary>
/// <returns></returns>
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

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Adja hozzá a metódust a Content Moderator feladat futtatása

```csharp
/// <summary>
/// Run the Content Moderator job on the designated Asset from local file or blob storage
/// </summary>
/// <param name="asset"></param>
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

### <a name="add-helper-functions"></a>Segédfüggvények hozzáadása

Ezek a metódusok töltse le a Content Moderator kimeneti fájlját (JSON) az Azure Media Services-objektum, és a moderálás feladat állapotának nyomon követéséhez, úgy, hogy a program egy futó állapotát a konzol jelentkezhetnek.

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

A tartalom-jóváhagyás feladat befejezése után elemezheti a JSON-választ. A következő elemekből áll:

- Videó összefoglaló információk
- **Helyességének** mint "**töredék**"
- **Kulcs keretek** , "**események**" az egy **reviewRecommended "(= true vagy false)"** jelző alapján **felnőtt** és **Racy** pontszámok
- **Indítsa el**, **időtartama**, **totalDuration**, és **időbélyeg** "órajel során végbemenő" szerepelnek. Nullával való osztás **időskálára** beolvasása a másodpercek száma.
 
> [!NOTE]
> - `adultScore` a lehetséges jelenléte és előrejelzési pontszámot is figyelembe kell venni, nyíltan explicit vagy bizonyos esetekben felnőtt tartalom jelöli.
> - `racyScore` a lehetséges jelenléte és előrejelzési pontszám a tartalom, amely nyíltan kétértelmű vagy bizonyos esetekben érett tekinthetők jelöli.
> - `adultScore` és `racyScore` 0 és 1 között kell lennie. Minél nagyobb a pontszám, annál nagyobb a modell becslése, hogy a kategóriához lehet alkalmazni. Ebben az előzetes verzióban manuálisan kódolt kimenetek helyett egy statisztikai modellt használ. Azt javasoljuk, hogy a saját határozza meg, hogyan az egyes kategóriák az igényeinek megfelelően igazítja a tartalmat a teszteléshez.
> - `reviewRecommended` IGAZ vagy hamis attól függően, a belső pontszám küszöbértékek. Ügyfelek fel kell mérnie, hogy e használja ezt az értéket, vagy hozza meg, a tartalom házirendek alapján egyedi küszöbértékeket.

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

Ismerje meg, hogyan hozhat létre [videót felülvizsgálatok](video-reviews-quickstart-dotnet.md) a moderálás kimenetből.

Adjon hozzá [átiratok moderálás](video-transcript-moderation-review-tutorial-dotnet.md) a videót felülvizsgálatok.

Tekintse meg a részletes arra vonatkozó útmutató, hogyan hozhat létre egy [végezze el a videót és a szöveges moderálás megoldás](video-transcript-moderation-review-tutorial-dotnet.md).

[Töltse le a Visual Studio-megoldás](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) ennél és a többi a Content Moderator rövid útmutató a .NET-hez.
