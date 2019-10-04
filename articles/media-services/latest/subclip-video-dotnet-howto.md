---
title: Részklip egy videót, ha az Azure Media Services encoding
description: Ez a témakör ismerteti, hogyan részklip egy videót, amikor a kódolás az Azure Media Services .NET SDK használatával
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
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67305124"
---
# <a name="subclip-a-video-when-encoding-with-media-services---net"></a>Részklip egy videót, amikor a kódolás a Media Services – .NET

Trim vagy a videó részklip használatával kódolás esetén egy [feladat](https://docs.microsoft.com/rest/api/media/jobs). Ez a funkció együttműködik bármely [átalakítása](https://docs.microsoft.com/rest/api/media/transforms) épül fel, amelyek segítségével a [BuiltInStandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) készleteket, vagy a [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset) készletek.

A következő C# példa létrehoz egy feladatot, amely egy videót egy adategység levágja, a kódolási feladatokat a helyrendszerekre. 

## <a name="prerequisites"></a>Előfeltételek

Az ebben a témakörben leírt lépések elvégzéséhez kell:

- [Az Azure Media Services-fiók létrehozása](create-account-cli-how-to.md)
- Hozzon létre egy-egy átalakítási és a egy bemeneti és kimeneti objektumok. Láthatja, hogyan hozhat létre egy-egy átalakítási és a bemeneti és kimeneti objektumok a [feltöltése, kódolása és streamelése a .NET használatával kíván videókat](stream-files-tutorial-with-api.md) oktatóanyag.
- Tekintse át a [kódolás fogalom](encoding-concept.md) témakör.

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

[Az egyéni átalakítási kódolása](customize-encoder-presets-how-to.md) 
