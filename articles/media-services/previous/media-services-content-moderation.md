---
title: Az Azure Media tartalommoderátor használata a lehetséges felnőtteknek és pikáns tartalmaknak a észlelésére | Microsoft dokumentumok
description: Az Azure Media Content Moderator médiaprocesszor segít észlelni a potenciális felnőtt és pikáns tartalmakat a videókban.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914317"
---
# <a name="use-azure-media-content-moderator-to-detect-possible-adult-and-racy-content"></a>Az Azure Media tartalommoderátor használata a lehetséges felnőtteknek és pikáns tartalmaknak a felismeréséhez 

> [!NOTE]
> Az **Azure Media tartalommoderátor** médiaprocesszora megszűnik. A kivonási dátum, lásd a [régi összetevők](legacy-components.md) témakörben.

## <a name="overview"></a>Áttekintés
Az **Azure Media Media Content Moderator** médiaprocesszor (MP) lehetővé teszi, hogy gépi támogatású moderálás a videók. Például előfordulhat, hogy szeretné észlelni az esetleges felnőtt vagy kényes tartalmakat a videókban, majd egy emberi moderátorcsapattal kívánja felülvizsgáltatni a megjelölt tartalmakat.

Az **Azure Media tartalommoderátor** mp jelenleg előzetes verzióban.

Ez a cikk az **Azure Media tartalommoderátorával** kapcsolatos részleteket tartalmaz, és bemutatja, hogyan használható a Media Services SDK-val a .

## <a name="content-moderator-input-files"></a>Tartalommoderátor bemeneti fájljai
Videofájlok. Jelenleg a következő formátumok támogatottak: MP4, MOV és WMV.

## <a name="content-moderator-output-files"></a>Tartalommoderátor kimeneti fájljai
A JSON formátummoderált kimenet automatikusan észlelt felvételeket és kulcsképeket tartalmaz. A kulcsképek megbízhatósági pontszámokkal kerülnek vissza a lehetséges felnőtt vagy pikáns tartalomhoz. Logikai jelzőt is tartalmaznak, amely jelzi, hogy ajánlott-e a felülvizsgálat. A felülvizsgálati javaslat jelző van rendelve értékek alapján a belső küszöbértékek felnőtt és pikáns pontszámok.

## <a name="elements-of-the-output-json-file"></a>A kimeneti JSON-fájl elemei

A feladat létrehoz egy JSON kimeneti fájlt, amely metaadatokat tartalmaz az észlelt felvételekről és kulcsképekről, valamint arról, hogy felnőtteknek vagy pikáns tartalomnak vannak-e tartalmaznia.

A kimeneti JSON a következő elemeket tartalmazza:

### <a name="root-json-elements"></a>Gyökér JSON-elemek

| Elem | Leírás |
| --- | --- |
| version |A tartalommoderátor verziója. |
| Időskála |"Kullancsok" másodpercenként a videó. |
| offset |Időeltolás az időbélyegek esetében. A videoAPI-k 1.0-s verziójában ez az érték mindig 0 lesz. Ez az érték a jövőben változhat. |
| Frameráta |Képkockák másodpercenkénti száma a videóban. |
| szélesség |A kimeneti videoképszélesség képpontban.|
| magasság |A kimeneti videoképmagasság képpontban.|
| totalDuration |A bemeneti videó időtartama a "kullancsokban". |
| [Töredékek](#fragments-json-elements) |A metaadatok különböző szegmensekbe, úgynevezett töredékekbe vannak felosztva. Minden töredék egy automatikusan észlelt felvétel, amely nek kezdete, időtartama, időköze és esemény(e)i vannak. |

### <a name="fragments-json-elements"></a>Töredékek JSON elemek

|Elem|Leírás|
|---|---|
| start |Az első esemény kezdési időpontja a "kullancsokban". |
| duration |A töredék hossza a "kullancsokban". |
| interval |A töredéken belüli egyes eseménybejegyzések időköze a "ticks" mezőben. |
| [események](#events-json-elements) |Minden esemény egy klipet jelöl, és minden egyes klip tartalmazza az adott időtartamon belül észlelt és nyomon követett kulcsképeket. Ez egy sor esemény. A külső tömb egy időintervallumot jelöl. A belső tömb 0 vagy több eseményből áll, amelyek az adott időpontban történtek.|

### <a name="events-json-elements"></a>Események JSON-elemek

|Elem|Leírás|
|---|---|
| felülvizsgálatAjánlott | `true`vagy `false` attól függően, hogy a **adultScore** vagy **a racyScore** meghaladja-e a belső küszöbértékeket. |
| felnőttPontszám | A lehetséges felnőtt tartalom megbízhatósági pontszáma 0,00 és 0,99 között. |
| racyScore (racyScore) | A lehetséges pikáns tartalom megbízhatósági pontszáma 0,00 és 0,99 között. |
| Index | a keret indexét az első képkockaindextől az utolsó képkockaindexig terjedő skálán. |
| időbélyeg | A keret helye a "kullancsokban". |
| shotIndex | A szülőlövés indexe. |


## <a name="content-moderation-quickstart-and-sample-output"></a>Tartalommoderálás rövid útmutató és mintakimenet

### <a name="task-configuration-preset"></a>Feladat konfigurációja (készlet)
Amikor feladatot hoz létre az **Azure Media tartalommoderátorával,** meg kell adnia egy konfigurációs készletet. A következő konfigurációs készlet csak a tartalom moderálásához.

    {
      "version":"2.0"
    }

### <a name="net-code-sample"></a>.NET-kódminta

A következő .NET-kódminta a Media Services .NET SDK segítségével futtat egy tartalommoderátori feladatot. A videó moderált bemeneteként egy médiaszolgáltatási eszközt vesz igénybe.
Tekintse meg a [Tartalommoderátor rövid útmutatóját](../../cognitive-services/Content-Moderator/video-moderation-api.md) a teljes forráskódról és a Visual Studio-projektről.


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
[Az Azure Media Services Analytics áttekintése](media-services-analytics-overview.md)

[Az Azure Media Analytics bemutatói](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

## <a name="next-steps"></a>További lépések

További információ a Tartalommoderátor [videómoderálási és felülvizsgálati megoldásáról.](../../cognitive-services/Content-Moderator/video-moderation-human-review.md)

A teljes forráskód és a Visual Studio-projekt beszerezhető a [videómoderálási rövid útmutatóból.](../../cognitive-services/Content-Moderator/video-moderation-api.md) 

További információ [arról,](../../cognitive-services/Content-Moderator/video-reviews-quickstart-dotnet.md) hogyan hozhat létre videoértékeléseket a moderált kimenetből, és [hogyan készíthet mérsékelt átiratokat](../../cognitive-services/Content-Moderator/video-transcript-reviews-quickstart-dotnet.md) a .NET-ben.

Nézze meg a részletes .NET [videó moderálás és felülvizsgálat bemutató](../../cognitive-services/Content-Moderator/video-transcript-moderation-review-tutorial-dotnet.md). 
