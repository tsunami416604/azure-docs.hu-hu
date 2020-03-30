---
title: Azure Media Services-feladat létrehozása több átalakítási kimenettel
description: Ez a témakör bemutatja, hogyan hozhat létre egy Azure Media Services-feladatot több átalakítási kimenettel.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/17/2020
ms.author: juliako
ms.openlocfilehash: dbbeeb33ee46b37ec920fe598483c332d3439689
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77563140"
---
# <a name="create-a-job-with-multiple-transform-outputs"></a>Több átalakítási kimenettel rendelkező feladat létrehozása

Ez a témakör bemutatja, hogyan hozhat létre átalakítást két átalakítási kimenettel. Az első azt kéri, hogy a bemenetet adaptív sávszélességű streameléshez kell kódolni egy beépített [AdaptiveStreaming](encoding-concept.md#builtinstandardencoderpreset) készlettel. A második kéri, hogy a bemeneti videóban lévő hangjelet az [AudioAnalyzerPreset](analyzing-video-audio-files-concept.md#built-in-presets)segítségével dolgozzák fel. Az átalakítás létrehozása után elküldhetsz egy feladatot, amely ennek megfelelően dolgozza fel a videót. Mivel ebben a példában két átalakítási kimenetet adunk meg, két feladatkimenetet kell megadnunk. Választhat, hogy mindkét projektkimenetet ugyanarra az eszközre irányítja (ahogy az alább látható), vagy az eredményeket külön eszközökbe írhatja.
 

> [!TIP]
> A fejlesztés megkezdése előtt tekintse át [a Fejlesztés a Media Services v3 API-jával](media-services-apis-overview.md) című irányelveket (beleértve az API-k elérésére, elnevezési konvenciókra stb.)

## <a name="create-a-transform"></a>Átalakítás létrehozása

A következő kód bemutatja, hogyan hozhat létre egy átalakító, amely két kimenetet hoz létre.

```csharp
private static async Task<Transform> GetOrCreateTransformAsync(
    IAzureMediaServicesClient client,
    string resourceGroupName,
    string accountName,
    string transformName)
{
    // Does a Transform already exist with the desired name? Assume that an existing Transform with the desired name
    // also uses the same recipe or Preset for processing content.
    Transform transform = await client.Transforms.GetAsync(resourceGroupName, accountName, transformName);

    if (transform == null)
    {
        // You need to specify what you want it to produce as an output
        TransformOutput[] output = new TransformOutput[]
        {
            new TransformOutput
            {
                Preset = new BuiltInStandardEncoderPreset()
                {
                    // This sample uses the built-in encoding preset for Adaptive Bitrate Streaming.
                    PresetName = EncoderNamedPreset.AdaptiveStreaming
                }
            },
            // Create an analyzer preset with video insights.
            new TransformOutput(new AudioAnalyzerPreset("en-US"))
        };

        // Create the Transform with the output defined above
        transform = await client.Transforms.CreateOrUpdateAsync(resourceGroupName, accountName, transformName, output);
    }

    return transform;
}
```
## <a name="submit-a-job"></a>Feladat beküldése

Hozzon létre egy feladatot egy HTTPS URL-bemenettel és két feladat kimenettel.

```csharp
private static async Task<Job> SubmitJobAsync(IAzureMediaServicesClient client,
    string resourceGroup,
    string accountName,
    string transformName)
{
    // Output from the encoding Job must be written to an Asset, so let's create one
    string outputAssetName1 = $"output-" + Guid.NewGuid().ToString("N");
    Asset outputAsset = await client.Assets.CreateOrUpdateAsync(resourceGroup, accountName, outputAssetName1, new Asset());

    // This example shows how to encode from any HTTPs source URL - a new feature of the v3 API.  
    // Change the URL to any accessible HTTPs URL or SAS URL from Azure.
    JobInputHttp jobInput =
        new JobInputHttp(files: new[] { "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4" });

    JobOutput[] jobOutputs =
    {
        // Since we are specifying two Transform Outputs, two Job Outputs are needed.
        // In this example, the first Job Output is for the results from adaptive bitrate encoding,
        // and the second is for the results from audio analysis. In this example, both are written to the
        // same output Asset. Or, you can specify different Assets.
        
        new JobOutputAsset(outputAsset.Name),
        new JobOutputAsset(outputAsset.Name)

    };

    // In this example, we are using a unique job name.
    //
    // If you already have a job with the desired name, use the Jobs.Get method
    // to get the existing job. In Media Services v3, Get methods on entities returns null 
    // if the entity doesn't exist (a case-insensitive check on the name).
    Job job;
    try
    {
        string jobName = $"job-" + Guid.NewGuid().ToString("N");
        job = await client.Jobs.CreateAsync(
                    resourceGroup,
                    accountName,
                    transformName,
                    jobName,
                    new Job
                    {
                        Input = jobInput,
                        Outputs = jobOutputs,
                    });
    }
    catch (Exception exception)
    {
        if (exception.GetBaseException() is ApiErrorException apiException)
        {
            Console.Error.WriteLine(
                $"ERROR: API call failed with error code '{apiException.Body.Error.Code}' and message '{apiException.Body.Error.Message}'.");
        }
        throw exception;
    }

    return job;
}
```
## <a name="job-error-codes"></a>Feladathibakódok

Lásd: [Hibakódok](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>További lépések

[Az Azure Media Services v3-as mintái a .NET használatával](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/) 
