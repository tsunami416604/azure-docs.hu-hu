---
title: Médiafájlok feldolgozása az Azure Media Hyperlapse |} A Microsoft Docs
description: Az Azure Media Hyperlapse zökkenőmentes idő lejárt videók első, aki vagy művelet – kamera tartalmat hoz létre. Ez a témakör bemutatja, hogyan használja a Media Indexer.
services: media-services
documentationcenter: ''
author: asolanki
manager: johndeu
editor: ''
ms.assetid: 37d54db6-9cf3-4ae9-b3c6-0d29c744e965
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: adsolank
ms.openlocfilehash: a57194a0c7b0cd82ce3d9426211b0cf6fc0d1182
ms.sourcegitcommit: f331186a967d21c302a128299f60402e89035a8d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58188050"
---
# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Médiafájlok feldolgozása az Azure Media hyperlapse használatával

> [!NOTE]
> Az előzetes verziójú funkció az Azure Media Services hamarosan megszűnik. 2018. December 19., kezdve a Media Services fog már nem győződjön meg arról, módosításokat és fejlesztéseket Media hyperlapse használatával való. 2019. március 29., a kivont és már nem elérhető lesz.

Az Azure Media Hyperlapse egy adathordozó processzor (pont), amely zökkenőmentes idő lejárt videók létrehozza az első, aki vagy művelet – kamera tartalmak.  A felhő alapú testvérének [a Microsoft Research asztali Hyperlapse Pro és a Hyperlapse Mobile phone-alapú](https://aka.ms/hyperlapse), a Microsoft Hyperlapse for Azure Media Services már használja az Azure Media Services Media feldolgozási hatalmas mennyiségű horizontális skálázása és párhuzamosíthatja platform tömeges feldolgozása hyperlapse használatával.

> [!IMPORTANT]
> A Microsoft Hyperlapse működnek a legjobban az első, aki tartalmat egy mozgó kamera célja. Bár továbbra is – kamerák felvételei továbbra is működik, a teljesítmény- és minőségteszteléseket, az Azure Media Hyperlapse Médiafeldolgozót nem lehet garantálni a más típusú tartalom.
> 
> 

Az Azure Media Hyperlapse feladat alatt, adjon meg egy MP4, MOV vagy WMV objektumfájl együtt egy konfigurációs fájl, amely meghatározza a videó mely keretek legyen ideje lejárt, és milyen sebessége (pl. első 10 000 keretet, 2 x).  A bemeneti videó egy stabil és idő lejárt megjelenítés kimenete.

## <a name="hyperlapse-an-asset"></a>Egy eszköz hyperlapse használatával
Először kell a kívánt bemeneti fájl feltöltése az Azure Media Services.  A feltölteni és tartalomkezelési fogalmak kapcsolatos további információkért olvassa el a [tartalomkezelési cikk](media-services-portal-vod-get-started.md).

### <a id="configuration"></a>A Hyperlapse konfigurációs készlet
Ha tartalmait a Media Services-fiókban, szüksége lesz a konfigurációs készlet létrehozásához.  Az alábbi táblázat azt ismerteti, hogy a felhasználó által megadott mezőket:

| Mező | Leírás |
| --- | --- |
| StartFrame |A keret, amelyre a Microsoft Hyperlapse feldolgozást kell kezdődnie. |
| NumFrames |A feldolgozása keretek számát |
| Sebesség |A szolgáltatás, amellyel a bemeneti videó felgyorsítása érdekében. |

Az alábbiakban látható egy példa egy megfelelő konfigurációs fájl XML és JSON:

**XML-készletet:**
```xml
    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="https://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="https://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>
```

**JSON-készletet:**
```json
    {
        "Version":1.0,
        "Sources": [
            {
                "StartFrame":0,
                "NumFrames":2147483647
            }
        ],
        "Options": {
            "Speed":1,
            "Stabilize":false
        }
    }
```

### <a id="sample_code"></a> A Microsoft Hyperlapse az AMS .NET SDK-val
A következő metódust feltölt egy médiafájlt eszközként, és létrehoz egy feladatot az Azure Media Hyperlapse médiaprocesszorral.

> [!NOTE]
> Már egy CloudMediaContext a hatóköre, a neve "környezet" működik ez a kód számára.  Ezzel kapcsolatos további információkért olvassa el a [tartalomkezelési cikk](media-services-dotnet-get-started.md).
> 
> [!NOTE]
> A karakterlánc-argumentum "hyperConfig" kellene lennie a megfelelő konfiguráció előbeállítást JSON vagy XML a fent leírtak szerint.
> 
> 

```csharp
        static bool RunHyperlapseJob(string input, string output, string hyperConfig)
        {
            // create asset with input file
            IAsset asset = context
            .Assets
            .CreateAssetAndUploadSingleFile(input, "My Hyperlapse Input", AssetCreationOptions.None);

            // grab instances of Azure Media Hyperlapse MP
            IMediaProcessor mp = context
            .MediaProcessors
            .GetLatestMediaProcessorByName("Azure Media Hyperlapse");

            // create Job with Hyperlapse task
            IJob job = context
            .Jobs
            .Create(String.Format("Hyperlapse {0}", input));

            if (String.IsNullOrEmpty(hyperConfig))
            {
            // config cannot be empty
            return false;
            }

            hyperConfig = File.ReadAllText(hyperConfig);

            ITask hyperlapseTask = job.Tasks.AddNew("Hyperlapse task",
            mp,
            hyperConfig,
            TaskOptions.None);
            hyperlapseTask.InputAssets.Add(asset);
            hyperlapseTask.OutputAssets.AddNew("Hyperlapse output",
            AssetCreationOptions.None);

            job.Submit();

            // Create progress printing and querying tasks
            Task progressPrintTask = new Task(() =>
            {

            IJob jobQuery = null;
            do
            {
                var progressContext = context;
                jobQuery = progressContext.Jobs
                .Where(j => j.Id == job.Id)
                .First();
                Console.WriteLine(string.Format("{0}\t{1}\t{2}",
                DateTime.Now,
                jobQuery.State,
                jobQuery.Tasks[0].Progress));
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
                return false;                  
            }

        DownloadAsset(job.OutputMediaAssets.First(), output);
        return true;
    }

    static void DownloadAsset(IAsset asset, string outputDirectory)
    {
        foreach (IAssetFile file in asset.AssetFiles)
        {
            file.Download(Path.Combine(outputDirectory, file.Name));
        }
    }


    static IAsset CreateAssetAndUploadSingleFile(string filePath, string assetName, AssetCreationOptions options)
    {
        IAsset asset = context.Assets.Create(assetName, options);

        var assetFile = asset.AssetFiles.Create(Path.GetFileName(filePath));
        assetFile.Upload(filePath);

        return asset;
    }

    static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = context.MediaProcessors
        .Where(p => p.Name == mediaProcessorName)
        .ToList()
        .OrderBy(p => new Version(p.Version))
        .LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor",
                                                       mediaProcessorName));

        return processor;
    }
```

### <a id="file_types"></a>Támogatott fájltípusok
* MP4
* MOV
* WMV

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Az Azure Media Services analitikai funkcióinak áttekintése](media-services-analytics-overview.md)

[Az Azure Médiaelemzés bemutatók](https://azuremedialabs.azurewebsites.net/demos/Analytics.html)

