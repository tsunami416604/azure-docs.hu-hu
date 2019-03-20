---
title: Töltse le a számítógép - az Azure Media Services objektumai |} A Microsoft Docs
description: Megtudhatja, hogyan eszközök letöltése a számítógépre. Kódminták nyelven írták C# , és használja a Media Services SDK a .NET-hez.
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
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58182659"
---
# <a name="how-to-deliver-an-asset-by-download"></a>Útmutató: Továbbítás letöltésen adategység keresztül  
Ez a cikk ismerteti a médiatartalmak ellátásához feltöltése Media Services-továbbításának beállítások. A Media Services tartalomkézbesítés az számos olyan alkalmazási esetekben. Kódolás, után töltse le a létrehozott médiatartalmak ellátásához, vagy a streamelési lokátorok használatával érheti el őket. A jobb teljesítmény és méretezhetőség érdekében hogy is tartalmat továbbít egy Content Delivery Network (CDN) használatával.

Ez a példa bemutatja, hogyan töltheti le a médiatartalmak ellátásához a Media Services szolgáltatásból a helyi számítógépen. A kód a feladatok Feladatazonosító és hozzáfér a Media Services-fiókjához társított lekérdezi a **OutputMediaAssets** gyűjteményt (amely az eredmények feladat futtatása közül egy vagy több kimeneti médiatartalmak készletét). Ez a példa bemutatja, hogyan töltheti le a feladat kimeneti médiatartalmak, de ugyanezzel a módszerrel, más eszközök letöltésére is alkalmazhat.

>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Az ugyanazon házirend-azonosító akkor használja, ha Ön mindig ugyanazokat a napokat / hozzáférési engedélyeket, például olyan továbbra is helyben hosszú ideje (nem feltöltött szabályzatokat) keresők szabályzatai. További információkért tekintse meg [ezt](media-services-dotnet-manage-entities.md#limit-access-policies) a cikket.

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
[Tartalomközvetítés](media-services-deliver-streaming-content.md)

