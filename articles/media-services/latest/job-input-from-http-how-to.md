---
title: Azure Media Services feladat bemeneti készíteni egy HTTP (s) URL-címe |} Microsoft Docs
description: Ez a témakör bemutatja, hogyan hozható létre egy feladat bemeneti a URL-címének http (s).
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: a47dc3e4939dd27d7ac11be823a09b8a6cba1f60
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-job-input-from-an-https-url"></a>Hozzon létre egy feladat bemeneti az URL-címének http (s)

A Media Services v3 amikor a videók feldolgozásához feladatok rendelkezik állapítható meg, hogy a Media Services hol található a bemeneti videó. A beállítások egyik adja meg az URL-címének http (s) (a példa szerint) bemeneti feladatként. Tekintse meg a teljes például [github minta](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>.NET-minta

A következő kód bemutatja, hogyan hozzon létre egy HTTPS URL-címet adjon meg egy feladatot.

```csharp
private static Job SubmitJob(IAzureMediaServicesClient client, string transformName, string jobName)
{
    Asset outputAsset = client.Assets.CreateOrUpdate(Guid.NewGuid().ToString() + "-output", new Asset());

    JobInputHttp jobInput = 
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });

    JobOutput[] jobOutputs =
    {
        new JobOutputAsset(outputAsset.Name),
    };

    Job job = client.Jobs.CreateOrUpdate(
        transformName,
        jobName,
        new Job
        {
            Input = jobInput,
            Outputs = jobOutputs,
        });

    return job;
}
```

## <a name="next-steps"></a>További lépések

[Hozzon létre egy feladat bemeneti helyi fájlból](job-input-from-local-file-how-to.md).
