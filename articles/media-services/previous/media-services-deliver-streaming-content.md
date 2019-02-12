---
title: .NET-tel az Azure Media Services-tartalom közzététele |} A Microsoft Docs
description: Ismerje meg, hogyan hozhat létre, amellyel streamelési lokátor. Kódminták nyelven írták C# , és használja a Media Services SDK a .NET-hez.
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
ms.date: 02/09/2019
ms.author: juliako
ms.openlocfilehash: 8e34d8cfbcd655dbb49279a0cefd63818963652a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55999181"
---
# <a name="publish-media-services-content-using-net"></a>.NET-tel, a Media Services tartalom közzététele  
> [!div class="op_single_selector"]
> * [REST](media-services-rest-deliver-streaming-content.md)
> * [.NET](media-services-deliver-streaming-content.md)
> * [Portál](media-services-portal-publish.md)
> 
> 

## <a name="overview"></a>Áttekintés
Az adaptív sávszélességű MP4 típusú beállításkészlettel streamelési OnDemand-kereső létrehozásával, és a streamelési URL-cím létrehozásához streamelheti. A [adategység kódolása](media-services-encode-asset.md) a témakör bemutatja, hogyan kódolandó egy adaptív sávszélességű MP4-beállítása. 

> [!NOTE]
> Objektumtovábbítási szabályzat konfigurálása, ha a tartalom titkosított, (leírtak szerint [ez](media-services-dotnet-configure-asset-delivery-policy.md) témakör) egy lokátor létrehozása előtt. 
> 
> 

OnDemand-lokátor segítségével hozhat létre MP4-fájlokat fokozatosan letölthető mutató URL-címeket is.  

Ez a témakör bemutatja, hogyan hozhat létre egy OnDemand-lokátor tegye közzé az adategységet, és a egy Smooth, MPEG DASH vagy HLS streamelési URL-címek hozhat létre. Azt is bemutatja, gyakori elérésű hozhat létre a progresszív letöltési URL-címeket. 

## <a name="create-an-ondemand-streaming-locator"></a>OnDemand-lokátor létrehozása
Az OnDemand a streamelési lokátorok létrehozásához, és URL-címek lekérése, tegye a következőket kell:

1. Ha a tartalom titkosított, adja meg a hozzáférési házirend.
2. OnDemand-lokátor létrehozása.
3. Ha azt tervezi, adatfolyam, lekérése a streamelési jegyzékfájl (.ism) az eszközben. 
   
   Ha azt tervezi, fokozatosan letölteni, első MP4-fájlok az eszközben nevei.  
4. Hozhat létre az URL-címek az Alkalmazásjegyzék-fájl vagy MP4-fájlokat. 


>[!NOTE]
>A különböző AMS-szabályzatok (például a Locator vagy a ContentKeyAuthorizationPolicy) esetében a korlát 1 000 000 szabályzat. Az ugyanazon házirend-azonosító akkor használja, ha Ön mindig ugyanazokat a napokat / hozzáférési engedélyeket. Ha például keresők szabályzatai, amelyek célja továbbra is helyben hosszú ideje (nem feltöltött szabályzatokat). További információ [ebben](media-services-dotnet-manage-entities.md#limit-access-policies) a témakörben érhető el.

### <a name="use-media-services-net-sdk"></a>Használja a Media Services .NET SDK-val
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
> SSL-kapcsolaton keresztül is streamelheti a tartalmat. Hajtsa végre ezt a módszert, ellenőrizze, hogy a streamelési URL-címek kezdő a HTTPS. Jelenleg az AMS nem támogatja az SSL egyéni tartománnyal rendelkező.
> 
> 

A progresszív letöltési URL-címek létrehozása 

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

### <a name="use-media-services-net-sdk-extensions"></a>A Media Services .NET SDK-bővítmények használata
Az alábbi kód meghívja a .NET SDK-val bővítmények módszerek lokátorok létrehozásához és a Smooth Streaming, HLS és MPEG-DASH URL-címeket létrehozni adaptív streameléshez.
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
* [Eszközök letöltése](media-services-deliver-asset-download.md)
* [Objektumtovábbítási szabályzat konfigurálása](media-services-dotnet-configure-asset-delivery-policy.md)

