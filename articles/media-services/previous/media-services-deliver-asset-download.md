---
title: Media Services eszközök letöltése a számítógépre – Azure | Microsoft Docs
description: További információ az eszközöknek a számítógépre való letöltéséről. A kód minták C# nyelven íródtak, és a .NET-hez készült Media Services SDK-t használják.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 21fcc6ae09718ffbb22e1d438926586dd3cde71d
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "61465660"
---
# <a name="how-to-deliver-an-asset-by-download"></a>Útmutató: eszköz kézbesítése letöltéssel  
Ez a cikk a Media Servicesba feltöltött média-eszközök kézbesítésének lehetőségeit tárgyalja. Számos alkalmazási forgatókönyvben Media Services tartalmat is eljuttathat. A kódolást követően töltse le a generált adathordozó-eszközöket, vagy egy streaming Locator használatával hozzáférjen hozzájuk. A jobb teljesítmény és méretezhetőség érdekében Content Delivery Network (CDN) használatával is elvégezheti a tartalom továbbítását.

Ez a példa azt mutatja be, hogyan tölthetők le a média eszközei Media Servicesről a helyi számítógépre. A kód lekérdezi a Media Services-fiókhoz társított feladatokat a feladat azonosítója alapján, és hozzáfér a **OutputMediaAssets** -gyűjteményhez (amely egy vagy több kimeneti adathordozó-eszköz készlete, amely egy feladat futtatását eredményezi). Ebből a példából megtudhatja, hogyan töltheti le a kimeneti adathordozó-eszközöket a feladatokból, de ugyanezt a módszert alkalmazhatja más eszközök letöltésére is.

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Használja ugyanazt a házirend-azonosítót, ha mindig ugyanazokat a napokat/hozzáférési engedélyeket használja, például olyan lokátorokra vonatkozó házirendeket, amelyek hosszú ideig maradnak érvényben (nem feltöltési szabályzatok). További információkért tekintse meg [ezt](media-services-dotnet-manage-entities.md#limit-access-policies) a cikket.

```csharp
    // Download the output asset of the specified job to a local folder.
    static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
    {
        // This method illustrates how to download a single asset. 
        // However, you can iterate through the OutputAssets
        // collection, and download all assets if there are many. 

        // Get a reference to the job. 
        IJob job = GetJob(jobId);

        // Get a reference to the first output asset. If there were multiple 
        // output media assets you could iterate and handle each one.
        IAsset outputAsset = job.OutputMediaAssets[0];

        // Create a SAS locator to download the asset
        IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
        ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);

        BlobTransferClient blobTransfer = new BlobTransferClient
        {
            NumberOfConcurrentTransfers = 20,
            ParallelTransferThreadCount = 20
        };

        var downloadTasks = new List<Task>();
        foreach (IAssetFile outputFile in outputAsset.AssetFiles)
        {
            // Use the following event handler to check download progress.
            outputFile.DownloadProgressChanged += DownloadProgress;

            string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);

            Console.WriteLine("File download path:  " + localDownloadPath);

            downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));

            outputFile.DownloadProgressChanged -= DownloadProgress;
        }

        Task.WaitAll(downloadTasks.ToArray());

        return outputAsset;
    }

    static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
    {
        Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
    }
```


## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Továbbítási tartalom továbbítása](media-services-deliver-streaming-content.md)

