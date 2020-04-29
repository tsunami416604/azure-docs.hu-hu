---
title: Az Azure Media Content Moderator használata a lehetséges felnőtt és a zamatos tartalmak észleléséhez | Microsoft Docs
description: Az Azure Media Content Moderator Media Processor segít felderíteni a videókban rejlő lehetséges felnőtt és zamatos tartalmakat.
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
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: 83fe7867a3128ac82597c028452863a1ad681ace
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "77914317"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Az Azure Media Content Moderator használata a lehetséges felnőtt és a zamatos tartalmak észleléséhez 

> [!NOTE]
> Az **Azure media Content moderator** adathordozó-processzora megszűnik. A lejárati dátumért tekintse meg az [örökölt összetevőkkel](legacy-components.md) foglalkozó témakört.

## <a name="overview"></a>Áttekintés
Az **Azure media Content moderator** Media Processor (mp) lehetővé teszi, hogy a videókhoz gépi támogatással rendelkező moderálást használjon. Például előfordulhat, hogy szeretné észlelni az esetleges felnőtt vagy kényes tartalmakat a videókban, majd egy emberi moderátorcsapattal kívánja felülvizsgáltatni a megjelölt tartalmakat.

Az **Azure Media Content moderator** mp jelenleg előzetes verzióban érhető el.

Ez a cikk az **Azure Media Content moderator** adatait ismerteti, és bemutatja, hogyan használható a Media Services SDK for .net szolgáltatással.

## <a name="content-moderator-input-files"></a>Bemeneti fájlok Content Moderator
Videofájlok. Jelenleg a következő formátumok támogatottak: MP4, MOV és WMV.

## <a name="content-moderator-output-files"></a>Kimeneti fájlok Content Moderator
A JSON formátum moderált kimenete automatikusan észlelt képeket és kulcsképeket tartalmaz. A kulcsképek a lehetséges felnőtt vagy a zamatos tartalmak megbízhatósági pontszámával lesznek visszaadva. Egy logikai jelzőt is tartalmaznak, amely azt jelzi, hogy a felülvizsgálat ajánlott-e. A felülvizsgálati javaslat jelölője a felnőtt és a zamatos pontszámok belső küszöbértékei alapján rendel hozzá értékeket.

## <a name="elements-of-the-output-json-file"></a>A kimeneti JSON-fájl elemei

A feladat létrehoz egy JSON kimeneti fájlt, amely az észlelt felvételekből és a kulcsképekből származó metaadatokat tartalmaz, valamint hogy felnőtt vagy zamatos tartalmat tartalmaz-e.

A kimeneti JSON a következő elemeket tartalmazza:

### <a name="root-json-elements"></a>Gyökér JSON-elemek

| Elem | Leírás |
| --- | --- |
| version |A Content Moderator verziója. |
| időskála |A videó másodpercenkénti száma. |
| offset |Időeltolás az időbélyegek esetében. A video API-k 1,0-es verziójában ez az érték mindig 0 lesz. Ez az érték a jövőben változhat. |
| frameráta |Képkockák másodpercenkénti száma a videóban. |
| szélesség |A kimeneti videó keretének szélessége képpontban megadva|
| magasság |A kimeneti videó keretének magassága képpontban megadva|
| totalDuration |A bemeneti videó időtartama "ketyeg". |
| [töredékek](#fragments-json-elements) |A metaadatok a töredékek nevű különböző szegmensekben vannak kiosztva. Az egyes töredékek egy kezdő, időtartam, intervallum és esemény (ek) alapú, automatikusan észlelt shot. |

### <a name="fragments-json-elements"></a>Töredékek JSON-elemek

|Elem|Leírás|
|---|---|
| start |Az első esemény kezdő időpontja "ketyeg". |
| duration |A töredék hossza "ketyeg". |
| interval |Az egyes események bejegyzésének intervalluma a töredéken belül "ketyeg". |
| [események](#events-json-elements) |Minden esemény egy klipet jelöl, és minden klip az adott időtartamon belül észlelt és nyomon követett képkockákat tartalmaz. Az események tömbje. A külső tömb egy időintervallumot jelöl. A belső tömb 0 vagy több eseményből áll, amelyek az adott időpontban történtek.|

### <a name="events-json-elements"></a>Események JSON-elemek

|Elem|Leírás|
|---|---|
| reviewRecommended | `true`vagy `false` attól függően, hogy a **AdultScore** vagy a **racyScore** túllépi-e a belső küszöbértékeket. |
| adultScore | A lehetséges felnőtt tartalmak megbízhatósági pontszáma 0,00 és 0,99 közötti skálán. |
| racyScore | A lehetséges, a 0,00 és 0,99 közötti skálán megjelenő, a lehetséges zamatos tartalmak megbízhatósági pontszáma. |
| index | egy skálán lévő keret indexe az első keret indexéről az utolsó keret indexére. |
| időbélyeg | A keret helye "ketyeg". |
| shotIndex | A fölérendelt felvétel indexe. |


## <a name="content-moderation-quickstart-and-sample-output"></a>A tartalom moderálása és a minta kimenete

### <a name="task-configuration-preset"></a>Feladat konfigurációja (előre beállított)
Az **Azure Media Content Moderatort**tartalmazó feladatok létrehozásakor meg kell adnia egy konfigurációs beállításkészletet. A következő konfigurációs beállításkészlet csak a tartalom moderálására szolgál.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>.NET-kód minta

A következő .NET-mintakód a Media Services .NET SDK-t használja a Content Moderator feladatok futtatásához. Egy Media Services-eszközt vesz igénybe, mint a moderálni kívánt videót tartalmazó bemenet.
Tekintse meg a teljes forráskódra és a Visual Studio-projektre vonatkozó [Content moderator videót](../../cognitive-services/Content-Moderator/video-moderation-api.md) .


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

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Azure Media Services Analytics áttekintése](media-services-analytics-overview.md)

[Azure Media Analytics bemutatók](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>További lépések

További információ a Content Moderator [videós moderálási és felülvizsgálati megoldásáról](../../cognitive-services/Content-Moderator/video-moderation-human-review.md).

Szerezze be a teljes forráskódot és a Visual Studio-projektet a [videó moderálási](../../cognitive-services/Content-Moderator/video-moderation-api.md)rövid útmutatójában. 

Megtudhatja, hogyan hozhat elő [videó-felülvizsgálatokat](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) a moderált kimenetből és a .net-beli [mérsékelt átiratokból](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) .

Tekintse meg a .NET [videó részletes moderálási és felülvizsgálati oktatóanyagát](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md). 
