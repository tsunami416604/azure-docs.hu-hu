---
title: Töltse le a számítógép - Azure Media Services eszközök |} Microsoft Docs
description: Ismerje meg a kívánt eszközök letöltése a számítógépre. Kódminták C# nyelven íródtak, és a Media Services SDK használata a .NET-hez.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: 8908a1dd-3ffb-4f18-955d-4c8e2d82fc5d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: ed53fe191dcf740f949b2d9cdcc3c97e30d85544
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="how-to-deliver-an-asset-by-download"></a>Útmutató: egy eszköz letöltés biztosításához
A cikk ismerteti a Media Services feltöltött adathordozó eszközök kézbesítéséhez beállítások. Számos alkalmazás-forgatókönyveket a Media Services tartalom biztosíthat. Kódolás, töltse le a generált adathordozó eszközök vagy a streamelési lokátor segítségével elérhet. Jobb teljesítmény és méretezhetőség is biztosíthat tartalmat egy Content Delivery Network (CDN) használatával.

Ez a példa bemutatja, hogyan adathordozó eszközök letölteni a Media Services a helyi számítógépen. A kód lekéri a Feladatazonosító és fér hozzá a Media Services-fiókjához társított feladatok a **OutputMediaAssets** gyűjtemény (amely a készlet egy vagy több kimeneti adathordozó eszközök fut egy feladat annak az eredménye). A példa bemutatja, hogyan töltheti le kimeneti adathordozó eszközök egy feladatot, de ugyanezt a megközelítést, más eszközök letöltésére is alkalmazhat.

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Az azonos házirend-azonosító akkor használja, ha mindig használja az ugyanazon nap / hozzáférési engedélyek, például a lokátorokat, amelyek célja, hogy továbbra is érvényben hosszú ideje (nem feltöltés házirendek) házirendek. További információkért tekintse meg [ezt](media-services-dotnet-manage-entities.md#limit-access-policies) a cikket.

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


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Adatfolyam-továbbítási tartalom](media-services-deliver-streaming-content.md)

