---
title: "Azure Media tartalom moderátor segítségével lehetséges felnőtt és ellopható tartalomtípusok |} Microsoft Docs"
description: "Videó moderálás segítségével azonosíthatók a potenciális felnőtt és ellopható tartalom videók."
services: media-services
documentationcenter: 
author: sanjeev3
manager: mikemcca
editor: 
ms.assetid: a245529f-3150-4afc-93ec-e40d8a6b761d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/06/2018
ms.author: sajagtap
ms.openlocfilehash: 43e22e553b5243d6edc413c7a667089793f95396
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Azure Media tartalom moderátor segítségével lehetséges felnőtt és ellopható tartalomtípusok

## <a name="overview"></a>Áttekintés
A **Azure Media tartalom moderátor** media processzor (MP) lehetővé teszi, hogy a videók gép támogatású moderálás használhat. Például előfordulhat, hogy kívánt lehetséges felnőtt és ellopható tartalom észlelése a videók, és tekintse át a megjelölt tartalom emberi moderálás csapat által.

A **Azure Media tartalom moderátor** felügyeleti csomag jelenleg előzetes verzió.

Ez a cikk részletezi kapcsolatos **Azure Media tartalom moderátor** és a .NET-keretrendszerhez készült Media Services SDK-val való használatát ismerteti.

## <a name="content-moderator-input-files"></a>A bemeneti fájlok moderátor tartalom
Videofájlok lejátszását. Jelenleg a következő formátumok használhatók: MP4 MOV és WMV.

## <a name="content-moderator-output-files"></a>Tartalom moderátor kimeneti fájlok
A JSON formátumban moderált kimeneti pillanatfelvételek automatikusan észleli és kulcsképek tartalmaz. A kulcsképek az vetett bizalmat pontszámok lehetséges ellopható vagy felnőtt tartalom adja vissza. Is, a logikai jelző, amely azt jelzi, hogy áttekintése ajánlott. A felülvizsgálati javaslat jelző hozzá van rendelve a felnőtt és ellopható pontszámok belső küszöbértékek alapján érték.

## <a name="elements-of-the-output-json-file"></a>A kimeneti JSON-fájl elemeinek

A feladat észlelt pillanatfelvételek és kulcsképek, és hogy felnőtt vagy ellopható adatokat tartalmaznak metaadatokat tartalmazó JSON kimeneti fájlt hoz létre.

A kimeneti JSON az alábbi elemeket tartalmazza:

### <a name="root-json-elements"></a>Legfelső szintű JSON-elemek szerepelnek

| Elem | Leírás |
| --- | --- |
| verzió: |A tartalom moderátor verzióját. |
| időskálára |A videó másodpercenként "ticks". |
| offset |Eltolódás az időbélyegekhez. Videó API-k 1.0-s verziójában ez az érték mindig 0 lesz. Ez az érték a későbbiekben változhat. |
| képkockasebességhez |A videó képkockasebessége. |
| width |A kimeneti videó keret, képpontban szélességét.|
| Magassága |A kimeneti videó keret, képpontban magasságát.|
| Teljesidőtartam |A bemeneti videóhoz, a "ticks." időtartama |
| [töredék](#fragments-json-elements) |A metaadatok töredék nevű különböző részekre van darabolásos fel. Minden egyes töredék egy automatikusan észleli hibaüzenetet a start, időtartam, időszakának száma és esemény. |

### <a name="fragments-json-elements"></a>Töredék JSON-elemek szerepelnek

|Elem|Leírás|
|---|---|
| start |A kezdési időt az első esemény a "ticks." |
| Időtartam |A hosszát a töredéke a "ticks." |
| interval |Az időszak mindegyik esemény bejegyzése a töredéke a "ticks." |
| [események](#events-json-elements) |Minden esemény klip jelöli, és minden egyes klip kulcsképek észlelt, és adott időtartamig belül követhetők tartalmazza. Események egy tömb. A külső tömb egy időköz jelöli. A belső tömb 0 vagy több olyan eseményeket, amelyek ezen a ponton az időben történtek áll.|

### <a name="events-json-elements"></a>Események JSON-elemek szerepelnek

|Elem|Leírás|
|---|---|
| reviewRecommended | `true`vagy `false` attól függően, hogy a **adultScore** vagy **racyScore** haladhatja meg a belső küszöbértékeket. |
| adultScore | 0,00 való 0,99 terjedő skálán lehetséges felnőtt tartalom abban, hogy pontszámot. |
| racyScore | 0,00 való 0,99 terjedő skálán lehetséges ellopható tartalom abban, hogy pontszámot. |
| index | a keret az első keret közötti skálán index index utolsó keret index. |
| időbélyeg | A hely a keret a "ticks." |
| shotIndex | A szülő index létrehozása. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Tartalom moderálás gyors üzembe helyezés és a minta kimenet

### <a name="task-configuration-preset"></a>A feladat konfigurációja (beállítás)
A feladat létrehozásakor **Azure Media tartalom moderátor**, meg kell adnia egy konfigurációs készletet. A következő konfigurációs készlet folyamat csak a tartalom moderálás.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>.NET-példakód

A következő .NET kódminta a Media Services .NET SDK használatával tartalom moderátor feladat futtatása. Tart egy media services eszköz, amely tartalmazza a videót, amely kell mérsékelni bemeneteként.
Tekintse meg a [tartalom moderátor videó gyors üzembe helyezés](../cognitive-services/Content-Moderator/video-moderation-api.md) a teljes forráskód és a Visual Studio-projektet.


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

A teljes forráskód és a Visual Studio-projekt, tekintse meg a [tartalom moderátor videó gyors üzembe helyezés](../cognitive-services/Content-Moderator/video-moderation-api.md).

### <a name="json-output"></a>JSON kimeneti

A következő példa egy tartalom moderátor JSON kimeneti csonkolódott.

> [!NOTE]
> Másodpercben keyframe helyét timestamp/időskálára =

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


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Azure Media Services elemző áttekintése](media-services-analytics-overview.md)

[Az Azure Médiaelemzés bemutatók](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>További lépések

További információ a tartalom moderátor [videó moderálás, és nézze meg megoldás](../cognitive-services/Content-Moderator/video-moderation-human-review.md).

A teljes forráskód és a Visual Studio-projektet az beszerzése a [videó moderálás gyors üzembe helyezés](../cognitive-services/Content-Moderator/video-moderation-api.md). 

Megtudhatja, hogyan készítése [ellenőrzi, hogy a videó](../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) a moderált kimenetből és [közepes szintű ki](../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) a .NET.

Tekintse meg a részletes .NET [videó moderálás, és nézze meg az oktatóanyag](../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md). 
