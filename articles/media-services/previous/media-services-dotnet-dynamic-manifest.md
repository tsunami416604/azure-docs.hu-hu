---
title: Szűrők létrehozása az Azure Media Services .NET SDK-val
description: Ez a témakör azt ismerteti, hogyan hozhatók létre szűrők, így az ügyfél használhatja őket stream konkrét szakaszokra datového proudu. A Media Services dinamikus jegyzékek érdekében, ami a szelektív hoz létre.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 2f6894ca-fb43-43c0-9151-ddbb2833cafd
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 02/09/2019
ms.author: juliako;cenkdin
ms.openlocfilehash: 5988ad92c9395332163182cb6995781d08bd5957
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56236906"
---
# <a name="creating-filters-with-media-services-net-sdk"></a>Szűrők létrehozása a Media Services .NET SDK használatával 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [REST](media-services-rest-dynamic-manifest.md)
> 
> 

2.17 kiadástól kezdve, a Media Services lehetővé teszi az eszközök szűrőket határozhat meg. Ezeket a szűrőket, amelyek lehetővé teszik az ügyfelek úgy dönteni, hogy többek között a kiszolgálóoldali szabályok: lejátszási csak egy részét (helyett egész videó lejátszásának), videó vagy hang- és verzió, amely a felhasználói eszköz képes kezelni (nem pedig csak egy részhalmazát adja meg az összes a beállításkészletben az eszközhöz társított). Ez a szűrés az eszközök a gazdafájlon keresztül **dinamikus Manifest**, amelyek létrejönnek a videó továbbításához a felhasználói kérésre megadott szűrő(k) alapján.

Részletesebb szűrők és dinamikus Manifest kapcsolatos információkért lásd: [dinamikus jegyzékfájlok áttekintése](media-services-dynamic-manifest-overview.md).

Ez a cikk bemutatja, hogyan létrehozása, frissítése és törlése a szűrők a Media Services .NET SDK használatával. 

Vegye figyelembe, ha frissíti a szűrőt, streamvégpont szabályok frissítése akár két percet is igénybe vehet. Ha a tartalom kiszolgálása szűrővel (és a proxyk és a CDN gyorsítótárazza a gyorsítótárak), player hibák frissítése ezzel a szűrővel eredményezhet. Mindig törölje a gyorsítótárat a szűrő frissítése után. Ha ezt a beállítást nem lehetséges, fontolja meg egy másik szűrővel. 

## <a name="types-used-to-create-filters"></a>Szűrők létrehozásához használt típusok
A következő típusok használhatók a szűrők létrehozásakor: 

* **IStreamingFilter**.  Ez a típus a következő REST API alapján [szűrő](https://docs.microsoft.com/rest/api/media/operations/filter)
* **IStreamingAssetFilter**. Ez a típus a következő REST API alapján [AssetFilter](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* **PresentationTimeRange**. Ez a típus a következő REST API alapján [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** és **IFilterTrackPropertyCondition**. Ezek a típusok alapján a következő REST API-k [FilterTrackSelect és FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Globális szűrők létrehozása/frissítése/olvasása/törlése
A következő kód bemutatja, hogyan .NET létrehozása, frissítése, olvassa el, és törölni az eszközintelligencia szűrők.

```csharp
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
```

## <a name="createupdatereaddelete-asset-filters"></a>Szűrők létrehozása/frissítése/olvasása és törlése eszköz
A következő kód bemutatja, hogyan .NET létrehozása, frissítése, olvassa el, és törölni az eszközintelligencia szűrők.

```csharp
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

```


## <a name="build-streaming-urls-that-use-filters"></a>Build a streaming URL-címeket, a szűrők használata
Hogyan tehet közzé, és az eszközök a további információkért lásd: [tartalom továbbítása az ügyfelek áttekintése](media-services-deliver-content-overview.md).

Az alábbi példák bemutatják, hogyan szűrők felvétele a streamelési URL-címeket.

**MPEG DASH** 

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[A dinamikus jegyzékek áttekintése](media-services-dynamic-manifest-overview.md)

