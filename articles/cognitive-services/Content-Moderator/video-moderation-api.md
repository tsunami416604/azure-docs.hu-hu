---
title: Az Azure Content moderátor - videó moderálás |} Microsoft Docs
description: Videó moderálás segítségével ellenőrizze a lehetséges felnőtt és ellopható tartalmát.
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/02/2018
ms.author: sajagtap
ms.openlocfilehash: ef58f5990d4a0a19ab2b8c61b42ab2a0754dc6fa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349350"
---
# <a name="video-moderation"></a>Videomoderálás

Jelenleg online megjelenítők népszerű és regionális közösségi webhelyek és növelése videó nézetek milliárd hoz létre. Gépi tanulásra alapú szolgáltatások előre jelezni lehetséges felnőtt és ellopható tartalom alkalmazásával csökkenthető a moderálás próbálkozások költségét.

## <a name="sign-up-for-the-content-moderator-media-processor-public-preview"></a>Iratkozzon fel a tartalom moderátor media processzor (nyilvános előzetes verzió)

### <a name="create-a-free-azure-account"></a>Hozzon létre egy ingyenes Azure-fiókot

[Kezdje itt](https://azure.microsoft.com/free/) egy ingyenes Azure-fiók létrehozásához, ha Ön nem rendelkezik ilyennel.

### <a name="create-an-azure-media-services-account"></a>Azure Media Services-fiók létrehozása

A tartalom moderátor videó funkció érhető el nyilvános által megtekinthető villámnézetként **media processzor** az Azure Media Services (AMS) díjmentesen.

[Azure Media Services-fiók létrehozása](https://docs.microsoft.com/azure/media-services/media-services-portal-create-account) az Azure-előfizetésben.

### <a name="get-azure-active-directory-credentials"></a>Azure Active Directory hitelesítő adatainak lekérése

   1. Olvassa el a [Azure Media Services portál cikk](https://docs.microsoft.com/azure/media-services/media-services-portal-get-started-with-aad) való használata az Azure-portálon az Azure AD hitelesítési hitelesítő adatok elkérése céljából.
   1. Olvassa el a [Azure Media Services .NET cikk](https://docs.microsoft.com/azure/media-services/media-services-dotnet-get-started-with-aad) megtudhatja, hogyan használható az Azure Active Directory hitelesítő adatok a .NET SDK-val.

   > [!NOTE]
   > A gyors üzembe helyezés a mintakódot használja a **szolgáltatás egyszerű hitelesítési** mindkét cikkeiben metódust.

Miután a AMS hitelesítő adatait, két módon lehet próbálja meg a tartalom moderátor media processzor.

## <a name="use-azure-media-services-explorer"></a>Az Azure Media Services-kezelővel

Használja az interaktív [Azure Media Services (AMS) explorer](https://azure.microsoft.com/blog/managing-media-workflows-with-the-new-azure-media-services-explorer-tool/) keresse meg az AMS-fiók, videók feltöltéséhez, és a tartalom moderátor media processzorral rendelkező vizsgálata. [Töltse le és telepítse azt](https://github.com/Azure/Azure-Media-Services-Explorer/releases) a Githubból, és [keresse meg a forráskód](http://github.com/Azure/Azure-Media-Services-Explorer) való mélyedjen el az AMS SDK használatával.

![A tartalom moderátor Azure Media Services explorer](images/ams-explorer-content-moderator.PNG)

## <a name="net-quickstart-with-visual-studio-and-c"></a>A Visual Studio és a C# .NET gyors üzembe helyezés

1. Adjon hozzá egy új **Konzolalkalmazás (.NET-keretrendszer)** -projektet a megoldásban.

   A mintakód a nevet a projektnek **VideoModeration**.

1. Jelölje ki a projektet a megoldásban egyetlen kiindulási projektként.

### <a name="install-required-packages"></a>Szükséges csomagok telepítése

A következő érhető el a NuGet-csomagok telepítése [NuGet](https://www.nuget.org/).

- windowsazure.mediaservices
- windowsazure.mediaservices.Extensions

### <a name="update-the-programs-using-statements"></a>Frissítés a program által utasítások segítségével.

Módosítsa a program által using utasításokat.

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


### <a name="initialize-application-specific-settings"></a>Az alkalmazás-specifikus beállításokat inicializálása

A következő statikus mezők hozzáadása a **Program** osztály a program.cs fájlban.

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

### <a name="create-a-preset-file-json"></a>Hozzon létre egy előre definiált fájlt (json)

Hozzon létre egy JSON-fájlt a verziószámú az aktuális könyvtárban található.

    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";
    //Example file content:
    //        {
    //             "version": "2.0"
    //        }
    private static readonly string CONTENT_MODERATOR_PRESET_FILE = "preset.json";

### <a name="add-the-following-code-to-the-main-method"></a>Adja hozzá a következő kódot a fő metódus

A fő metódus először létrehoz egy Azure Media környezet és az Azure Storage környezettel abban az esetben, ha a videók a blob Storage tárolóban vannak.
A fennmaradó kódot egy helyi mappába, blob vagy egy Azure storage tárolóban több blobok videó keres.
Minden beállítás próbálkozna, a más sornyi kód fűzött megjegyzések.

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

### <a name="add-the-code-to-create-an-azure-media-context"></a>Adja hozzá a kódot az Azure Media környezet létrehozása

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

### <a name="add-the-code-to-create-an-azure-storage-context"></a>Adja hozzá a kódot az Azure Storage-környezet létrehozása
A tároló hitelesítő adatait, alapján létrehozott adattároló-környezet a blob-tároló elérésére használhat.

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

### <a name="add-the-code-to-create-azure-media-assets-from-local-file-and-blob"></a>Adja hozzá a kódot az Azure Media eszközök létrehozása helyi fájl és a blob
A tartalom moderátor media processzor futó feladatok **eszközök** az Azure Media Services platform belül.
Ezek a módszerek az eszközöket egy helyi fájlból vagy egy társított blob létrehozni.

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

### <a name="add-the-code-to-scan-a-collection-of-videos-as-blobs-within-a-container"></a>Adja hozzá a kódot a vizsgálat olyan tárolóban (a BLOB) videók gyűjteménye

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

### <a name="add-the-method-to-run-the-content-moderator-job"></a>Adja hozzá a metódust a tartalom moderátor feladat futtatása

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

### <a name="add-a-couple-of-helper-functions"></a>Néhány segítő funkciók hozzáadása

Ezek a módszerek töltse le a tartalom moderátor kimeneti fájlt (JSON) az Azure Media Services eszköz, és a moderálás feladat állapotának nyomon követése, hogy a program egy futási állapotának bejelentkezhetnek a konzol segítségével.

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

### <a name="run-the-program-and-review-the-output"></a>Futtassa a programot, és tekintse át a kimenetet

A tartalom moderálás feladat befejezése után vizsgálja meg a JSON-választ. A következő elemekből áll:

- Videó összefoglaló információk
- **Pillanatfelvételek** mint "**töredék**"
- **Kulcs keretek** mint "**események**" rendelkező egy **reviewRecommended "(= IGAZ vagy hamis)"** jelző alapján **felnőtt** és **Racy** pontszámok
- **Start**, **időtartam**, **Teljesidőtartam**, és **időbélyeg** a "ticks". Nullával való osztás **időskálára** lekérdezni számát másodpercben.
 
> [!NOTE]

> - `adultScore` a lehetséges jelenléte és előrejelzés pontszám tekinthetők a ivarilag explicit vagy bizonyos esetekben felnőtt tartalom jelöli.
> - `racyScore` a lehetséges jelenléte és előrejelzés pontszám ivarilag kétértelmű vagy bizonyos esetekben érett tekinthetők tartalmat jelöli.
> - `adultScore` és `racyScore` 0 és 1 közé esnek. Minél nagyobb a pontszám, annál magasabb a modell becslése, hogy a kategória alkalmazhatók. Ez az előnézet manuálisan kódolt eredményekkel helyett statisztikai modell támaszkodik. Azt javasoljuk, hogy a saját tartalom határozza meg, hogyan legyen a különböző kategóriájú az igényeinek megfelelően tesztelték.
> - `reviewRecommended` IGAZ vagy hamis függően a belső pontszám küszöbértékek. Az ügyfelek fel kell mérnie hogy ezt az értéket, vagy adja meg a tartalom házirendek alapján egyéni küszöbértékeket.
>

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

## <a name="next-steps"></a>További lépések

Megtudhatja, hogyan készítése [ellenőrzi, hogy a videó](video-reviews-quickstart-dotnet.md) a moderálás kimenetből.

Adja hozzá [Beszélgetés szövegének moderálás](video-transcript-moderation-review-tutorial-dotnet.md) számára a videó értékelést.

Tekintse meg a részletes útmutatót a hogyan hozhat létre egy [végezze el a videó és Beszélgetés szövegének moderálás megoldás](video-transcript-moderation-review-tutorial-dotnet.md).

[Töltse le a Visual Studio megoldás](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) ennél és egyéb tartalom moderátor quickstarts a .NET-hez.
