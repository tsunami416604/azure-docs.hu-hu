---
title: Videoklip részklipje az Azure Media Services használatával történő kódolássorán
description: Ez a témakör azt ismerteti, hogyan lehet egy videót részklipként használni, amikor az Azure Media Services szolgáltatással .NET SDK használatával kódol.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67305124"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Videoklip a Media Services szolgáltatással történő kódoláshoz – .NET

A videót levághatja vagy részcsípheti, ha [a feladatot használva](https://docs.microsoft.com/rest/api/media/jobs)kódolja. Ez a funkció minden [olyan átalakítással](https://docs.microsoft.com/rest/api/media/transforms) működik, amely a [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) készletekkel vagy a [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) készletekkel épül fel.

A következő C# példa létrehoz egy feladatot, amely levágja a videót egy eszköz, mert küld egy kódolási feladat. 

## <a name="prerequisites"></a>Előfeltételek

A témakörben ismertetett lépések végrehajtásához a következőket kell elvégeznie:

- [Azure Media Services-fiók létrehozása](create-account-cli-how-to.md)
- Hozzon létre egy átalakítást, valamint egy bemeneti és kimeneti eszközöket. A .NET oktatóanyag használatával megtekintheti, hogyan hozhat létre átalakítást, valamint bemeneti és kimeneti eszközöket a [Videók feltöltése, kódolása és streamelése segítségével.](stream-files-tutorial-with-api.md)
- Tekintse át a [Kódolási koncepció](encoding-concept.md) témakörét.

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

[Hogyan kódolj egyéni átalakítással](customize-encoder-presets-how-to.md) 
