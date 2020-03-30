---
title: A Media Services-kellékek letöltése a számítógépre - Azure | Microsoft dokumentumok
description: További információ arról, hogy miként tölthet le kellékeket a számítógépre. A kódminták C# nyelven íródnak, és a Media Services SDK-t használják a .NET-hez.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "61465660"
---
# <a name="how-to-deliver-an-asset-by-download"></a>Hogyan: Eszköz kézbesítése letöltéssel  
Ez a cikk a Media Servicesszolgáltatásba feltöltött médiaeszközök kézbesítésének lehetőségeit ismerteti. A Media Services-tartalmakat számos alkalmazási forgatókönyvben is kézbesítheti. Kódolás után töltse le a létrehozott médiaeszközöket, vagy érje el őket egy streamelési lokátor használatával. A jobb teljesítmény és méretezhetőség érdekében tartalomkézbesítési hálózat (CDN) használatával is kézbesíthet tartalmakat.

Ez a példa bemutatja, hogyan tölthet le médiaeszközöket a Media Services szolgáltatásból a helyi számítógépre. A kód lekérdezi a Media Services-fiókhoz társított feladatokat feladatazonosító szerint, és hozzáfér az **OutputMediaAssets** gyűjteményéhez (amely egy vagy több kimeneti adathordozó-eszköz készlete, amely egy feladat futtatásából származik). Ez a példa bemutatja, hogyan tölthet le kimeneti médiaelemeket egy feladatból, de ugyanazt a megközelítést alkalmazhatja más eszközök letöltéséhez.

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Használja ugyanazt a házirend-azonosítót, ha mindig ugyanazokat a napokat / hozzáférési engedélyeket használja, például a helymeghatározókra vonatkozó házirendeket, amelyek hosszú ideig a helyükön maradnak (nem feltöltési házirendek). További információt [ebben a cikkben](media-services-dotnet-manage-entities.md#limit-access-policies) talál.

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
[Streamelési tartalom biztosítása](media-services-deliver-streaming-content.md)

