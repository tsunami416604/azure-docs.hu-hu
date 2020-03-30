---
title: Szűrők létrehozása az Azure Media Services .NET SDK-val
description: Ez a témakör azt ismerteti, hogyan hozhat létre szűrőket, hogy az ügyfél használhassa őket az adatfolyam adott szakaszainak streameléséhez. A Media Services dinamikus jegyzékeket hoz létre a szelektív streamelés eléréséhez.
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
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: cenkdin
ms.openlocfilehash: c60b223f91a151bf63cabc5e95816f2545022503
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016608"
---
# <a name="creating-filters-with-media-services-net-sdk"></a>Szűrők létrehozása a Media Services .NET SDK szolgáltatással 
> [!div class="op_single_selector"]
> * [.NET](media-services-dotnet-dynamic-manifest.md)
> * [Többi](media-services-rest-dynamic-manifest.md)
> 
> 

A 2.17-es kiadástól kezdve a Media Services lehetővé teszi, hogy szűrőket határozzon meg az eszközökhöz. Ezek a szűrők olyan kiszolgálóoldali szabályok, amelyek lehetővé teszik az ügyfelek számára, hogy olyan műveleteket válasszanak, mint például: a videónak csak egy részét játssza le (a teljes videó lejátszása helyett), vagy csak a hang- és videointerpretációk egy részét adja meg, amelyet az ügyfél eszköze kezelni tud (ahelyett, hogy az eszközhöz társított összes interpretáció). Az eszközök szűrése dinamikus **jegyzékeken**keresztül érhető el, amelyek az ügyfél kérésére jönnek létre a videó adott szűrő(k) alapján történő streamelésére.

A szűrőkkel és a dinamikus jegyzékfájlokkal kapcsolatos további információkért lásd: [Dinamikus jegyzékek – áttekintés.](media-services-dynamic-manifest-overview.md)

Ez a cikk azt mutatja be, hogy a Media Services .NET SDK használatával hogyan hozhat létre, frissíthető és törölhet szűrőket. 

Vegye figyelembe, ha frissíti a szűrőt, akár két percet is igénybe vehet, amíg a streamelési végpont frissíti a szabályokat. Ha a tartalmat ezzel a szűrővel szolgálták ki (proxykban és CDN-gyorsítótárakban gyorsítótárazva), a szűrő frissítése a lejátszó meghibásodását eredményezheti. A szűrő frissítése után mindig törölje a gyorsítótárat. Ha ez a beállítás nem lehetséges, fontolja meg egy másik szűrő használatát. 

## <a name="types-used-to-create-filters"></a>Szűrők létrehozásához használt típusok
A szűrők létrehozásakor a következő típusok at használjuk: 

* **IStreamingFilter**.  Ez a típus a következő REST API szűrőn [alapul:](https://docs.microsoft.com/rest/api/media/operations/filter)
* **IStreamingAssetFilter**. Ez a típus a következő REST API AssetFilter szűrőn [alapul:](https://docs.microsoft.com/rest/api/media/operations/assetfilter)
* **PresentationTimeRange .** Ez a típus a következő REST API [PresentationTimeRange](https://docs.microsoft.com/rest/api/media/operations/presentationtimerange)
* **FilterTrackSelectStatement** és **IFilterTrackPropertyCondition**. Ezek a típusok a következő REST [API-kfilterTrackSelect és FilterTrackPropertyCondition](https://docs.microsoft.com/rest/api/media/operations/filtertrackselect)

## <a name="createupdatereaddelete-global-filters"></a>Globális szűrők létrehozása/frissítése/olvasása/törlése
A következő kód bemutatja, hogyan hozhat létre, frissítheti, olvashatja és törölheti az eszközszűrőket a .NET értékszűrőivel.

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

## <a name="createupdatereaddelete-asset-filters"></a>Eszközszűrők létrehozása/frissítése/olvasása/törlése
A következő kód bemutatja, hogyan hozhat létre, frissítheti, olvashatja és törölheti az eszközszűrőket a .NET értékszűrőivel.

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


## <a name="build-streaming-urls-that-use-filters"></a>Szűrőket használó streamelési URL-ek létrehozása
Az eszközök közzétételéről és kézbesítésének módjáról a [Tartalom kézbesítése az ügyfeleknek – áttekintés című témakörben olvashat.](media-services-deliver-content-overview.md)

Az alábbi példák bemutatják, hogyan adhat hozzá szűrőket a streamelési URL-címekhez.

**MPEG DASH** 

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

    http:\//testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még:
[Dinamikus jegyzékek – áttekintés](media-services-dynamic-manifest-overview.md)

