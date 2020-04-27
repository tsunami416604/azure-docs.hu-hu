---
title: Videó kivágása a Azure Media Services kódolásakor
description: Ez a témakör azt ismerteti, hogyan lehet videót alklipet használni Azure Media Services a .NET SDK-val való kódoláskor
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/09/2019
ms.author: juliako
ms.openlocfilehash: 3d584ee742aa93cdecf4b04d942afb2ed83a7357
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "67305124"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Videó kivágása Media Services-.NET-kódolással

Ha [feladatokkal](https://docs.microsoft.com/rest/api/media/jobs)kódolja a videót, levágja vagy alklipet készíthet. Ez a funkció a [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) -előállítók vagy a [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) -előállítók használatával létrehozott bármely [átalakítással](https://docs.microsoft.com/rest/api/media/transforms) működik.

A következő C#-példa egy olyan feladatot hoz létre, amely egy adott eszközön található videót metszi el, mivel kódolási feladatot küld. 

## <a name="prerequisites"></a>Előfeltételek

A jelen témakörben ismertetett lépések végrehajtásához a következőket kell tennie:

- [Azure Media Services-fiók létrehozása](create-account-cli-how-to.md)
- Hozzon létre egy átalakítót és egy bemeneti és kimeneti eszközöket. A .NET-oktatóanyag használatával megtekintheti, hogyan hozhat létre átalakítót és bemeneti és kimeneti eszközöket a [feltöltési, a kódolási és a stream-videóban](stream-files-tutorial-with-api.md) .
- Tekintse át a [kódolási koncepcióval](encoding-concept.md) foglalkozó témakört.

## <a name="example"></a>Példa

```csharp
/// <summary>
/// Submits a request to Media Services to apply the specified Transform to a given input video.
/// </summary>
/// <param name="client">The Media Services client.</param>
/// <param name="resourceGroupName">The name of the resource group within the Azure subscription.</param>
/// <param name="accountName"> The Media Services account name.</param>
/// <param name="transformName">The name of the transform.</param>
/// <param name="jobName">The (unique) name of the job.</param>
/// <param name="inputAssetName">The name of the input asset.</param>
/// <param name="outputAssetName">The (unique) name of the  output asset that will store the result of the encoding job. </param>
// <SubmitJob>
private static async Task<Job> JobWithBuiltInStandardEncoderWithSingleClipAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName,
    string jobName,
    string inputAssetName,
    string outputAssetName)
{
    var jobOutputs = new List<JobOutputAsset>
    {
        new JobOutputAsset(state: JobState.Queued, progress: 0, assetName: outputAssetName)
    };

    var clipStart = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 20)
    };

    var clipEnd = new AbsoluteClipTime()
    {
        Time = new TimeSpan(0, 0, 30)
    };

    var jobInput = new JobInputAsset(assetName: inputAssetName, start: clipStart, end: clipEnd);

    Job job = await client.Jobs.CreateAsync(
        resourceGroupName,
        accountName,
        transformName,
        jobName,
        new Job(input: jobInput, outputs: jobOutputs.ToArray(), name: jobName)
        {
            Description = $"A Job with transform {transformName} and single clip.",
            Priority = Priority.Normal,
        });

    return job;
}
```

## <a name="next-steps"></a>További lépések

[Kódolás egyéni átalakítással](customize-encoder-presets-how-to.md) 
