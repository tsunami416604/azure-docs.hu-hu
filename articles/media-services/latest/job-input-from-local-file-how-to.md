---
title: Hozzon létre egy Azure Media Services feladat bemeneti helyi fájlból |} Microsoft Docs
description: Ez a témakör bemutatja, hogyan hozzon létre egy feladat bemeneti helyi fájlból.
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
ms.openlocfilehash: 6ca2da7fc43e509bec357ae47ada3a2f57e79e78
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-job-input-from-a-local-file"></a>Hozzon létre egy feladat bemeneti helyi fájlból

A Media Services v3 amikor a videók feldolgozásához feladatok rendelkezik állapítható meg, hogy a Media Services hol található a bemeneti videó. A bemeneti videó tárolható Media Service eszközként, ebben az esetben hozzon létre egy bemeneti eszköz, a (helyi vagy az Azure Blob Storage tárolóban tárolt) fájl alapján. Ez a témakör bemutatja, hogyan hozzon létre egy feladat bemeneti helyi fájlból. Tekintse meg a teljes például [github minta](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>.NET-minta

A következő kód bemutatja, hogyan hozzon létre egy bemeneti eszközt, és a feladat használható a bemeneti adatok. A CreateInputAsset függvény a következő műveleteket hajtja végre:

* Az eszköz létrehozása 
* Lekérdezi egy írható [SAS URL-cím](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) az eszközhöz [storage tárolója](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container)
* Feltölti az SAS URL-cím segítségével Storage tárolóba

```csharp
private static Asset CreateInputAsset(IAzureMediaServicesClient client, string resourceGroupName, string accountName, string assetName, string fileToUpload)
{
    // Call Media Services API to create an Asset.
    // This method creates a container in storage for the Asset.
    // The files (blobs) associated with the asset will be stored in this container.
    Asset asset = client.Assets.CreateOrUpdate(resourceGroupName, accountName, assetName, new Asset());

    // Use Media Services API to get back a response that contains
    // SAS URL for the Asset container into which to upload blobs.
    // That is where you would specify read-write permissions 
    // and the exparation time for the SAS URL.
    var response = client.Assets.ListContainerSas(resourceGroupName, accountName, assetName, new ListContainerSasInput()
    {
        Permissions = AssetContainerPermission.ReadWrite,
        ExpiryTime = DateTimeOffset.Now.AddHours(4)
    });

    var sasUri = new Uri(response.AssetContainerSasUrls.First());
    
    // Use Storage API to get a reference to the Asset container
    // that was created by calling Asset's CreateOrUpdate method.  
    CloudBlobContainer container = new CloudBlobContainer(sasUri);
    var blob = container.GetBlockBlobReference(Path.GetFileName(fileToUpload));
    
    // Use Strorage API to upload the file into the container in storage.
    blob.UploadFromFile(fileToUpload);

    return asset;
}

private static Job SubmitJob(IAzureMediaServicesClient client, string transformName, string jobName, string outputAssetName)
{
    string inputAssetName = Guid.NewGuid().ToString() + "-input";
    JobInput jobInput = new JobInputAsset(assetName: inputAssetName);

    CreateInputAsset(client, inputAssetName, "ignite.mp4")

    JobOutput[] jobOutputs =
    {
        new JobOutputAsset(outputAssetName),
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

[Hozzon létre egy feladat bemeneti egy HTTP (s) URL-címről](job-input-from-http-how-to.md).
