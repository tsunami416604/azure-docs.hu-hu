---
title: Azure Media Services-tartalom közzététele a .NET használatával | Microsoft dokumentumok
description: Ismerje meg, hogyan hozhat létre egy lokátort, amely egy streamelési URL-cím létrehozásához használható. A kódminták C# nyelven íródnak, és a Media Services SDK-t használják a .NET-hez.
author: juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: c53b1f83-4cb1-4b09-840f-9c145b7d6f8d
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: b1d0c070a9196eaa9a2706a607baa9a2926e2db4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67051744"
---
# <a name="publish-media-services-content-using-net"></a>Media Services-tartalom közzététele a .NET használatával  
> [!div class="op_single_selector"]
> * [Többi](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portál](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Áttekintés
Adaptív sávszélességű MP4-készletet streamelhet egy OnDemand streamelési lokátor létrehozásával és egy streamelési URL-cím létrehozásával. Az [eszköztémakör kódolása](media-services-encode-asset.md) bemutatja, hogyan kódoljon adaptív sávszélességű MP4-készletbe. 

> [!NOTE]
> Ha a tartalom titkosítva van, konfigurálja az eszközkézbesítési házirendet [(a](media-services-dotnet-configure-asset-delivery-policy.md) jelen témakörben leírtak szerint) a lokátor létrehozása előtt. 
> 
> 

Az OnDemand streamelési lokátorsegítségével olyan URL-címeket is létrehozhat, amelyek fokozatosan letölthető MP4-fájlokra mutatnak.  

Ez a témakör bemutatja, hogyan hozhat létre egy OnDemand streamelési lokátort az eszköz közzétételéhez és egy sima, MPEG DASH és HLS streamelési URL-címek létrehozásához. Azt is mutatja, forró építeni progresszív letöltési URL-eket. 

## <a name="create-an-ondemand-streaming-locator"></a>OnDemand streamelési lokátor létrehozása
Az OnDemand streamelési lokátor létrehozásához és az URL-címek lekéréséhez a következő műveleteket kell végeznie:

1. Ha a tartalom titkosított, adjon meg egy hozzáférési szabályzatot.
2. Hozzon létre egy OnDemand streamelési lokátort.
3. Ha azt tervezi, hogy streamelés, a streamelési jegyzékfájl (.ism) az eszköz. 
   
   Ha azt tervezi, hogy fokozatosan letölti, kap a nevét MP4 fájlokat az eszköz.  
4. URL-címeket hozhat létre a jegyzékfájlba vagy mp4-fájlokba. 


>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Használja ugyanazt a házirend-azonosítót, ha mindig ugyanazokat a napokat / hozzáférési engedélyeket használja. Például a helymeghatározók házirendjei, amelyek célja, hogy hosszú ideig érvényben maradjanak (nem feltöltési szabályzatok). További információ [ebben](media-services-dotnet-manage-entities.md#limit-access-policies) a témakörben érhető el.

### <a name="use-media-services-net-sdk"></a>A Media Services .NET SDK használata
Streamelési URL-ek létrehozása 

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
        var manifestFile = asset.AssetFiles.ToList().Where(f => f.Name.ToLower().
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

Az outputok:

    URL to manifest for client streaming using Smooth Streaming protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
    URL to manifest for client streaming using HLS protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
    URL to manifest for client streaming using MPEG DASH protocol:
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)


> [!NOTE]
> A tartalmat SSL-kapcsolaton keresztül is streamelheti. Ehhez a módszerhez győződjön meg arról, hogy a streamelési URL-ek HTTPS-lel kezdődnek. Jelenleg az AMS nem támogatja az SSL-t egyéni tartományokkal.
> 
> 

Progresszív letöltési URL-címek létrehozása 

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
Az outputok:

    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
    http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4

    . . . 

### <a name="use-media-services-net-sdk-extensions"></a>A Media Services .NET SDK-bővítmények használata
A következő kód meghívja a .NET SDK-bővítmények metódusait, amelyek lokátort hoznak létre, és zökkenőmentes streamelési, HLS- és MPEG-DASH URL-címeket hoznak létre az adaptív streameléshez.
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

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="next-steps"></a>További lépések
* [Kellékek letöltése](media-services-deliver-asset-download.md)
* [Objektumtovábbítási szabályzat konfigurálása](media-services-dotnet-configure-asset-delivery-policy.md)

