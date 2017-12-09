---
title: "Szűrők létrehozása az Azure Media Services .NET SDK-val"
description: "Ez a témakör ismerteti, az ügyfél használhassa őket adott szakaszaival adatfolyam adatfolyam-szűrők létrehozásához. A Media Services dinamikus jegyzékfájlokban eléréséhez a szelektív streaming hoz létre."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 2f6894ca-fb43-43c0-9151-ddbb2833cafd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 12/07/2017
ms.author: juliako;cenkdin
ms.openlocfilehash: 8ffd310573d0800593bd9d93d74da4bcece61fa4
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="creating-filters-with-azure-media-services-net-sdk"></a>Szűrők létrehozása az Azure Media Services .NET SDK-val
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

2.17 kiadástól kezdve a Media Services lehetővé teszi az eszközök szűrőit. Ezek a szűrők, amelyek lehetővé teszik a felhasználói számára többek között a megteheti a kiszolgálóoldali szabályok: lejátszás videó (és nem a teljes videó lejátszása) részt vagy a hang- és interpretációk, amelyet a felhasználói eszköz kezelni tud (ahelyett, hogy csak egy részét minden a interpretációk társított adategységet). Ez a szűrés a eszközök sorrendekben **dinamikus Manifest**khoz, az ügyfél kérésre videó adatfolyam jönnek létre a megadott szűrő alapján.

Részletesebb szűrőket és dinamikus Manifest kapcsolatos adatokat, lásd: [dinamikus jelentkezik áttekintése](media-services-dynamic-manifest-overview.md).

Ez a cikk bemutatja, hogyan létrehozása, frissítése és törlése a szűrők a Media Services .NET SDK használatával. 

Vegye figyelembe, ha frissíti a szűrőt, streamvégpontra frissítéséhez a szabályok akár két percet is igénybe vehet. Ha a tartalom állítása és kiszolgálása között szűrővel (és proxyk és a CDN a gyorsítótárba helyezett gyorsítótárak), player hibák frissítése a szűrő eredményezhet. A gyorsítótár tartalmának mindig a szűrő frissítése után. Ha ezt a beállítást nem lehetséges, érdemes lehet egy másik szűrőt. 

## <a name="types-used-to-create-filters"></a>-Szűrők létrehozásához használt
A következő típusok használhatók szűrők létrehozásakor: 

* **IStreamingFilter**.  Ez a típus alapján a következő REST API [szűrő](https://docs.microsoft.com/rest/api/media/operations/filter)
* **IStreamingAssetFilter**. Ez a típus alapján a következő REST API [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* **PresentationTimeRange**. Ez a típus alapján a következő REST API [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** és **IFilterTrackPropertyCondition**. Ezek a típusok a következő REST API-k alapuló [FilterTrackSelect és FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Létrehozás/frissítés/olvasás/törlés globális szűrők
A következő kód bemutatja, hogyan .NET segítségével létrehozhatja, frissítheti, olvassa el, és törli az eszköz szűrők.

    string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();

    List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();

    FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
    filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
    filterTrackSelectStatements.Add(filterTrackSelectStatement);

    // Create
    IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);

    // Update
    filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
    filter.Update();

    // Read
    var filterUpdated = _context.Filters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filter.Delete();


## <a name="createupdatereaddelete-asset-filters"></a>Létrehozás/frissítés/olvasás/törlés eszköz szűrők
A következő kód bemutatja, hogyan .NET segítségével létrehozhatja, frissítheti, olvassa el, és törli az eszköz szűrők.

    string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
    var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);

    string filterName = "AssetFilter_" + Guid.NewGuid().ToString();


    // Create
    IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
                                        new PresentationTimeRange(), 
                                        new List<FilterTrackSelectStatement>());

    // Update
    filter.PresentationTimeRange = 
            new PresentationTimeRange(start: 6000000000, end: 72000000000);

    filter.Update();

    // Read
    asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
    var filterUpdated = asset.AssetFilters.FirstOrDefault();
    Console.WriteLine(filterUpdated.Name);

    // Delete
    filterUpdated.Delete();




## <a name="build-streaming-urls-that-use-filters"></a>Build a streaming URL-szűrők használata
Információ közzétételére, és az eszközök: [ügyfelek áttekintés a tartalom továbbítása](media-services-deliver-content-overview.md).

A következő példák bemutatják, hogyan szűrők hozzáadása a streamelési URL-címeket.

**MPEG DASH** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP élő adatfolyam-továbbítási (HLS) V4**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP élő adatfolyam-továbbítási (HLS) V3**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Dinamikus jegyzékfájlokban áttekintése](media-services-dynamic-manifest-overview.md)

