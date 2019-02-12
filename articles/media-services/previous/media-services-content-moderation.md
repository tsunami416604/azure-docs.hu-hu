---
title: Lehetséges felnőtt és szexuális tartalom észleléséhez használni az Azure Media Content Moderator |} A Microsoft Docs
description: Videomoderálás segítségével észlelheti a potenciális felnőtt és szexuális tartalom videókat.
services: media-services
documentationcenter: ''
author: sanjeev3
manager: mikemcca
editor: ''
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/08/2019
ms.author: sajagtap
ms.openlocfilehash: aba7d2ff73fc1fdca6f57742582b38662177012d
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55995118"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Az Azure Media Content Moderator segítségével lehetséges a felnőtt és szexuális tartalom észlelése 

## <a name="overview"></a>Áttekintés
A **Azure Media a Content Moderator** médiafeldolgozót. (pont) lehetővé teszi, hogy gépi támogatású képmoderálás a videókhoz. Például érdemes észlelheti a videók lehetséges felnőtt és szexuális tartalom, és tekintse át a megjelölt tartalom az emberi moderálás csoportok.

A **Azure Media a Content Moderator** felügyeleti csomag jelenleg előzetes verzióban érhető el.

Ez a cikk kapcsolatos részleteket nyújt **Azure Media a Content Moderator** , és bemutatja, hogyan használja a Media Services SDK a .NET-hez.

## <a name="content-moderator-input-files"></a>Content Moderator a bemeneti fájlok
Videofájlok. Jelenleg a következő formátumok támogatottak: MP4 MOV és WMV.

## <a name="content-moderator-output-files"></a>Content Moderator kimeneti fájlok
A JSON-formátumban a moderált kimenet helyességének automatikusan észleli és kulcsképek tartalmazza. A kulcskép megbízhatósági pontszámok lehetséges felnőtt vagy pikáns tartalom küld vissza a rendszer. Ezek a logikai jelző. e felülvizsgálat ajánlott is tartalmazzák. A felülvizsgálati javaslat jelző hozzá van rendelve az értékeket a felnőtt és szexuális pontszámok belső küszöbértékek alapján.

## <a name="elements-of-the-output-json-file"></a>A kimenet JSON-fájl elemeinek

A feladatot hoz létre egy JSON kimeneti észlelt helyességének és kulcsképek és attól, hogy tartalmaznak felnőtt vagy pikáns tartalmak metaadatait tartalmazó fájl.

A kimenet JSON az alábbi elemeket tartalmazza:

### <a name="root-json-elements"></a>Legfelső szintű JSON-elemek

| Elem | Leírás |
| --- | --- |
| version |A Content Moderator verzióját. |
| időskálára |"Órajel során végbemenő" a videó másodpercenként. |
| offset |Időeltolás az időbélyegek esetében. Videó API-k 1.0-s verziójában ez az érték mindig 0 lesz. Ez az érték a későbbiekben változhatnak. |
| Képkockasebesség |Képkockák másodpercenkénti száma a videóban. |
| Szélesség |A kimeneti képkocka, képpontban szélességét.|
| Magasság |A magasságát (képpontban) a kimeneti videót keret.|
| totalDuration |Az ajánlat időtartamát a bemeneti videóhoz, a "órajelben." |
| [töredékek száma](#fragments-json-elements) |A metaadatok nevű töredék különböző részekre van darabolásos fel. Minden részlet egy az automatikusan észlelt képernyőkép-készítés kezdő, időtartamának, időszakának száma és esemény. |

### <a name="fragments-json-elements"></a>Töredékek JSON-elemek

|Elem|Leírás|
|---|---|
| start |A kezdési idejét az első esemény a "órajelben." |
| időtartam |A "órajelben.", a részlet hossza |
| interval |Az időszak mindegyik esemény bejegyzése a részlet a "órajelben." |
| [események](#events-json-elements) |Minden egyes esemény egy videoklip lejátszása jelöli, és minden klip tartalmaz kulcsképek észlelt, és nyomon követett időt töltött belül. Az események egy tömb. A külső tömb egy időintervallumot jelöl. A belső tömb 0 vagy több eseményből áll, amelyek az adott időpontban történtek.|

### <a name="events-json-elements"></a>Események JSON-elemek

|Elem|Leírás|
|---|---|
| reviewRecommended | `true` vagy `false` attól függően, hogy a **adultScore** vagy **racyScore** haladhatja meg a belső küszöbértékeket. |
| adultScore | Felnőtt tartalom pontszámmal való, 0,00 való 0.99 terjedő skálán konfidencia-pontszám. |
| racyScore | Megbízhatósági pontszám lehetséges fajgyűlölő tartalom 0,00 való 0.99 terjedő skálán. |
| index | az első keret-től a keret index index utolsó keret index. |
| időbélyeg | A keret "órajelben." a helye |
| shotIndex | A szülő elejtett index. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Tartalom moderálása rövid és a minta kimenete

### <a name="task-configuration-preset"></a>A feladat konfigurációs (előre)
A feladat létrehozásakor **Azure Media a Content Moderator**, meg kell adnia egy konfigurációs készletet. A következő konfigurációs készlet csak a tartalom-jóváhagyás van.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>.NET-kódmintát

A következő .NET-kódmintát a Media Services .NET SDK használatával a Content Moderator feladat futtatása. Vesz igénybe egy media services eszköz, amely tartalmazza a videó kell mérsékelni a bemenetként.
Tekintse meg a [a rövid videó a Content Moderator](../../cognitive-services/Content-Moderator/video-moderation-api.md) a teljes forráskódot, és a Visual Studio-projekt.


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

For the full source code and the Visual Studio project, check out the [Content Moderator video quickstart](../../cognitive-services/Content-Moderator/video-moderation-api.md).

### JSON output

The following example of a Content Moderator JSON output was truncated.

> [!NOTE]
> Location of a keyframe in seconds = timestamp/timescale

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

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Az Azure Media Services analitikai funkcióinak áttekintése](media-services-analytics-overview.md)

[Az Azure Médiaelemzés bemutatók](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>További lépések

További tudnivalók a Content Moderator [videó moderálás és felülvizsgálati megoldás](../../cognitive-services/Content-Moderator/video-moderation-human-review.md).

A teljes forráskódot, és a Visual Studio-projektet a a [videomoderálás rövid](../../cognitive-services/Content-Moderator/video-moderation-api.md). 

Ismerje meg, hogyan hozhat létre [videót felülvizsgálatok](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) a moderált kimenetből és [szövegekben mérsékelt](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) a .NET-ben.

Tekintse meg a részletes .NET [moderálás és felülvizsgálati oktatóvideó](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md). 
