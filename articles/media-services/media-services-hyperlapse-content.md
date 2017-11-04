---
title: "Médiafájlok feldolgozása az Azure Media hyperlapse használatával |} Microsoft Docs"
description: "Az Azure Media Hyperlapse zökkenőmentes idő letelt videók első, aki vagy művelet – kamera tartalmat hoz létre. Ez a témakör bemutatja, hogyan Media Indexer használatára."
services: media-services
documentationcenter: 
author: asolanki
manager: johndeu
editor: 
ms.assetid: 37d54db6-9cf3-4ae9-b3c6-0d29c744e965
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/02/2017
ms.author: adsolank
ms.openlocfilehash: 02f634c2af04b6b372642ab0e6a17a5d29f16450
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="hyperlapse-media-files-with-azure-media-hyperlapse"></a>Médiafájlok feldolgozása az Azure Media hyperlapse használatával
Az Azure Media Hyperlapse egy Media processzor (MP), amely zökkenőmentes idő letelt videók hoz első, aki vagy művelet – kamera tartalomról.  A felhő alapú testvérének [Microsoft Research asztali Hyperlapse Pro és Hyperlapse Mobile phone-alapú](http://aka.ms/hyperlapse), az Azure Media Services Microsoft Hyperlapse használja az Azure Media Services Media feldolgozása a jelentős mértékű vízszintes skálázása és parallelize platform tömeges Hyperlapse feldolgozása.

> [!IMPORTANT]
> Microsoft Hyperlapse legmegfelelőbb első, aki a tartalomhoz a mozgóátlag fényképezőgép célja.  Bár továbbra is-kamerák felvételei is működik, a teljesítmény és az Azure Media Hyperlapse Media processzor-minőségi nem garantálható tartalmat más típusú.  További információk a Microsoft Hyperlapse az Azure Media Services, és tekintse meg néhány Mintavideók, tekintse meg a [bevezető blogbejegyzés](http://aka.ms/azurehyperlapseblog) a nyilvános előzetes verziójához.
> 
> 

Egy Azure Media Hyperlapse feladat végrehajtásához szükséges, adjon meg egy MP4, MOV vagy WMV objektumfájlt együtt, amely meghatározza, melyik keretek videó idő letelt konfigurációs fájlt, és milyen sebességgel (pl. első 10 000 keretet 2 x).  Az eredménye egy stabil és az idő letelt verzióinak a bemeneti videó.

A legújabb Azure Media Hyperlapse frissítéseket, lásd: [Media Services blogok](https://azure.microsoft.com/blog/topics/media-services/).

## <a name="hyperlapse-an-asset"></a>Hyperlapse egy eszköz
Először szüksége lesz Azure Media Services töltse fel a kívánt bemeneti fájl.  Adminisztrációjával kapcsolatos feltöltését és kielégíteni tartalomkezelési fogalmak kapcsolatos további tudnivalókért olvassa el a [tartalomkezelési cikk](media-services-portal-vod-get-started.md).

### <a id="configuration"></a>Hyperlapse konfigurációs beállításkészlet
Ha a tartalom a Media Services-fiókját, szüksége lesz a konfigurációs készlet létrehozásához.  Az alábbi táblázat ismerteti a felhasználó által megadott mezőket:

| Mező | Leírás |
| --- | --- |
| StartFrame |A keret, amelyre a Microsoft Hyperlapse feldolgozási el kell kezdődnie. |
| NumFrames |Keretek feldolgozni száma |
| Gyorsaság |A tényező, amellyel a bemeneti videó felgyorsítása érdekében. |

A következő egy példa egy szabványos konfigurációs fájl XML- és a JSON:

**XML-készletet:**

    <?xml version="1.0" encoding="utf-16"?>
    <Preset xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" Version="1.0" xmlns="http://www.windowsazure.com/media/encoding/Preset/2014/03">
        <Sources>
            <Source StartFrame="0" NumFrames="10000" />
        </Sources>
        <Options>
            <Speed>12</Speed>
        </Options>
    </Preset>

**JSON-készletet:**

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

### <a id="sample_code"></a>Microsoft Hyperlapse az AMS .NET SDK-val
A következő metódus feltölt egy médiafájlt eszközként, és létrehoz egy feladatot az Azure Media Hyperlapse Media processzorral rendelkező.

> [!NOTE]
> Egy CloudMediaContext már rendelkezik a neve "context" a kód működéséhez a hatókörben.  Ezzel kapcsolatos további tudnivalókért olvassa el a [tartalomkezelési cikk](media-services-dotnet-get-started.md).
> 
> [!NOTE]
> A karakterlánc-argumentum "hyperConfig" kellene lennie a szabványos konfigurációs készlet JSON vagy XML fent leírt módon.
> 
> 

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

### <a id="file_types"></a>Támogatott fájltípusok
* MP4
* MOV
* WMV

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-links"></a>Kapcsolódó hivatkozások
[Azure Media Services elemző áttekintése](media-services-analytics-overview.md)

[Az Azure Médiaelemzés bemutatók](http://azuremedialabs.azurewebsites.net/demos/Analytics.html)

