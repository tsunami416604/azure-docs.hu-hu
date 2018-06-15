---
title: Azure Media Services tartalmakat a .NET használatával közzététele |} Microsoft Docs
description: Megtudhatja, hogyan hozhat létre egy keresőt a streamelési URL-cím létrehozásához használt. Kódminták C# nyelven íródtak, és a Media Services SDK használata a .NET-hez.
author: juliako
manager: cfowler
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako
ms.openlocfilehash: 224c9cf5ef9925645de1d94dc5bc03c15ba91432
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33788514"
---
# <a name="publish-azure-media-services-content-using-net"></a>Azure Media Services tartalmakat a .NET használatával közzététele
> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portál](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Áttekintés
Egy adaptív sávszélességű MP4 típusú beállításkészlettel egy adatfolyam-továbbítási OnDemand-kereső létrehozásával, és a streamelési URL-cím összeállítása is adatfolyam. A [egy eszköz kódolás](media-services-encode-asset.md) a témakör bemutatja, hogyan kódolása egy adaptív sávszélességű MP4 állítsa be. 

> [!NOTE]
> Ha a tartalom titkosított, objektumtovábbítási szabályzat konfigurálása (leírtak szerint [ez](media-services-dotnet-configure-asset-delivery-policy.md) témakör) egy lokátor létrehozása előtt. 
> 
> 

OnDemand-lokátor segítségével is MP4-fájlokat fokozatosan letölthető mutató URL-címek létrehozása.  

Ez a témakör bemutatja, hogyan hozzon létre egy OnDemand-lokátor tegye közzé az adategységet, és egy Smooth, MPEG DASH vagy HLS streamelési URL-címek létrehozása. Azt is bemutatja, működés közbeni hozhat létre a progresszív letöltési URL-címeket. 

## <a name="create-an-ondemand-streaming-locator"></a>Hozzon létre egy OnDemand-lokátor
Az adatfolyam-továbbítási OnDemand-kereső létrehozása és URL-címek lekérése, meg kell tegye a következőket:

1. Ha a tartalom titkosított, adja meg a hozzáférési házirendek.
2. Hozzon létre egy OnDemand-lokátor.
3. Ha azt tervezi, hogy adatfolyamként küldje el, beolvasása a folyamatos átviteli jegyzékfájl (.ism) az eszközt. 
   
   Ha azt tervezi, fokozatosan letölteni, beolvasása az eszköz a MP4-fájlok nevét.  
4. A jegyzékfájl vagy MP4-fájlok összeállítása a URL-címek. 


>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Az azonos házirend-azonosító akkor használja, ha mindig használja az ugyanazon nap / hozzáférési engedélyek. Például házirendek, amelyek célja, hogy továbbra is érvényben hosszú ideje (nem feltöltés házirendek) lokátorokat. További információ [ebben](media-services-dotnet-manage-entities.md#limit-access-policies) a témakörben érhető el.

### <a name="use-media-services-net-sdk"></a>Használja a Media Services .NET SDK-val
Adatfolyam-továbbítási URL-címek létrehozása 

```csharp
    private static void BuildStreamingURLs(IAsset asset)
    {

        // Create a 30-day readonly access policy. 
          // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create a locator to the streaming content on an origin. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get a reference to the streaming manifest file from the  
        // collection of files in the asset. 
        var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                                    EndsWith(".ism")).
                                    FirstOrDefault();

        // Create a full URL to the manifest file. Use this for playback
        // in streaming media clients. 
        string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
        Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
        Console.WriteLine(urlForClientStreaming);
        Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
        Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
        Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
        Console.WriteLine();
    }
```

A kimenetek:

    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


> [!NOTE]
> Is SSL-kapcsolaton keresztül adatfolyam formájában a tartalmat. Hajtsa végre ezt a módszert használja, ellenőrizze, hogy a HTTPS adatfolyam-továbbítási URL-címek kezdődik. Jelenleg az AMS nem támogatja az SSL az egyéni tartomány.
> 
> 

Progresszív letöltési URL-címeket összeállítása 

```csharp
    private static void BuildProgressiveDownloadURLs(IAsset asset)
    {
        // Create a 30-day readonly access policy. 
        IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
            TimeSpan.FromDays(30),
            AccessPermissions.Read);

        // Create an OnDemandOrigin locator to the asset. 
        ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
            policy,
            DateTime.UtcNow.AddMinutes(-5));

        // Display some useful values based on the locator.
        Console.WriteLine("Streaming asset base path on origin: ");
        Console.WriteLine(originLocator.Path);
        Console.WriteLine();

        // Get MP4 files.
        IEnumerable<IAssetFile> mp4AssetFiles = asset
            .AssetFiles
            .ToList()
            .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Create a full URL to the MP4 files. Use this to progressively download files.
        foreach (var pd in mp4AssetFiles)
            Console.WriteLine(originLocator.Path + pd.Name);
    }
```
A kimenetek:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>Media Services .NET SDK-bővítmények használata
Az alábbi kód olyan, egy kereső létrehozása, és amelyek adaptív streameléshez a Smooth Streaming, HLS, és MPEG-DASH URL-címek létrehozása .NET SDK bővítmények módszereket hív meg.
```csharp
    // Create a loctor.
    _context.Locators.Create(
        LocatorType.OnDemandOrigin,
        inputAsset,
        AccessPermissions.Read,
        TimeSpan.FromDays(30));

    // Get the streaming URLs.
    Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
    Uri hlsUri = inputAsset.GetHlsUri();
    Uri mpegDashUri = inputAsset.GetMpegDashUri();

    Console.WriteLine(smoothStreamingUri);
    Console.WriteLine(hlsUri);
    Console.WriteLine(mpegDashUri);
```

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések
* [Töltse le az eszközök](media-services-deliver-asset-download.md)
* [Objektumtovábbítási szabályzat konfigurálása](media-services-dotnet-configure-asset-delivery-policy.md)

