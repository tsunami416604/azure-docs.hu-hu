---
title: Szűrők létrehozása Azure Media Services v3 .NET SDK-val
description: Ez a témakör azt ismerteti, hogyan hozhatók létre szűrők, hogy az ügyfél egy stream adott szakaszait továbbítsa. A Media Services dinamikus jegyzékfájlokat hoz létre a szelektív streaming eléréséhez.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/03/2019
ms.author: juliako
ms.openlocfilehash: ef04b1b7b5030189482e89e26e4565397cbdd7c8
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75779246"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Szűrők létrehozása Media Services .NET SDK-val

Ha a tartalmat az ügyfeleknek (élő vagy igény szerinti közvetítéssel közvetíti), az ügyfélnek nagyobb rugalmasságra lehet szüksége, mint amit az alapértelmezett eszköz jegyzékfájljában ismertetünk. A Azure Media Services segítségével meghatározhatja a tartalomhoz tartozó fiókok szűrőit és a hozzájuk tartozó szűrőket. 

A funkció részletes ismertetését és a használatban lévő forgatókönyveket lásd: [dinamikus jegyzékfájlok](filters-dynamic-manifest-overview.md) és [szűrők](filters-concept.md).

Ez a témakör azt mutatja be, hogyan használható a Media Services .NET SDK egy szűrő megadására az igény szerinti videóhoz, valamint a [fiókok](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) és az adategységek [szűrők](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet)létrehozásához. 

> [!NOTE]
> Ügyeljen rá, hogy ellenőrizze a [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Előfeltételek 

- Tekintse át [a szűrőket és a dinamikus jegyzékfájlokat](filters-dynamic-manifest-overview.md).
- [Hozzon létre egy Media Services fiókot](create-account-cli-how-to.md). Ügyeljen rá, hogy jegyezze fel az erőforráscsoport nevét és a Media Services fiók nevét. 
- Az API-k [eléréséhez](access-api-cli-how-to.md) szükséges információk beolvasása
- Tekintse át a [feltöltést, a kódolást és a streamet a Azure Media Services használatával, és](stream-files-tutorial-with-api.md) tekintse meg a [.net SDK használatának megkezdéséhez](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>Szűrő definiálása  

A .NET-ben a [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) és a [FilterTrackPropertyCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) osztályok használatával követheti nyomon a beállításokat. 

A következő kód egy olyan szűrőt határoz meg, amely minden olyan hangsávot tartalmaz, amely EC-0-1000000 3.

```csharp
var audioConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Audio", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.FourCC, "EC-3", FilterTrackPropertyCompareOperation.Equal)
};

var videoConditions = new List<FilterTrackPropertyCondition>()
{
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Type, "Video", FilterTrackPropertyCompareOperation.Equal),
    new FilterTrackPropertyCondition(FilterTrackPropertyType.Bitrate, "0-1000000", FilterTrackPropertyCompareOperation.Equal)
};

List<FilterTrackSelection> includedTracks = new List<FilterTrackSelection>()
{
    new FilterTrackSelection(audioConditions),
    new FilterTrackSelection(videoConditions)
};
```

## <a name="create-account-filters"></a>Fiókok szűrőinek létrehozása

A következő kód bemutatja, hogyan használható a .NET egy olyan fiók szűrő létrehozásához, amely tartalmazza a [fent megadott](#define-a-filter)összes nyomkövetési beállítást. 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Eszközcsoport-szűrők létrehozása

A következő kód bemutatja, hogyan használható a .NET egy olyan objektum-szűrő létrehozásához, amely tartalmazza a [fent megadott](#define-a-filter)összes követési kijelölést. 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Szűrők hozzárendelése a folyamatos átviteli Lokátorhoz

Megadhatja az eszköz vagy a fiók szűrőinek listáját, amely a folyamatos átviteli Lokátorra vonatkozik. A [dinamikus csomagoló (streaming Endpoint)](dynamic-packaging-overview.md) a szűrők ezen listáját alkalmazza, az ügyfél által megadott URL-címen. Ez a kombináció létrehoz egy [dinamikus jegyzékfájlt](filters-dynamic-manifest-overview.md), amely a streaming keresőben megadott URL + szűrők szűrői alapján történik. Azt javasoljuk, hogy használja ezt a funkciót, ha szűrőket kíván alkalmazni, de nem szeretné kitenni a szűrő nevét az URL-címben.

A következő C#-kód azt mutatja be, hogyan hozható létre adatfolyam-kereső, és hogyan adható meg `StreamingLocator.Filters` . Ez egy opcionális tulajdonság, amely `IList<string>` a szűrők nevét veszi figyelembe.

```csharp
IList<string> filters = new List<string>();
filters.Add("filterName");

StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly,
        Filters = filters
    });
```
      
## <a name="stream-using-filters"></a>Stream szűrők használatával

A szűrők meghatározása után az ügyfelek a streaming URL-ben használhatják őket. A szűrők alkalmazhatók az adaptív sávszélességű adatfolyam-továbbítási protokollokra: Apple HTTP Live Streaming (HLS), MPEG-DASH és Smooth Streaming.

Az alábbi táblázat néhány példát mutat be a szűrőket tartalmazó URL-címekre:

|Protokoll|Példa|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>További lépések

[Stream-videók](stream-files-tutorial-with-api.md) 


