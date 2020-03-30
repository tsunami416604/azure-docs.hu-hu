---
title: Szűrők létrehozása az Azure Media Services 3-as v3-as ával .NET SDK
description: Ez a témakör azt ismerteti, hogyan hozhat létre szűrőket, hogy az ügyfél használhassa őket az adatfolyam adott szakaszainak streameléséhez. A Media Services dinamikus jegyzékeket hoz létre a szelektív streamelés eléréséhez.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75779246"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Szűrők létrehozása a Media Services .NET SDK szolgáltatással

Amikor a tartalmat az ügyfelek (élő események streamelése vagy igény szerinti videó) az ügyfél szükség lehet nagyobb rugalmasságot, mint amit az alapértelmezett eszköz jegyzékfájljában leírt. Az Azure Media Services lehetővé teszi, hogy fiókszűrőket és eszközszűrőket határozzon meg a tartalomhoz. 

A szolgáltatás részletes leírását és a használatának forgatókönyveit a Dinamikus jegyzékek és [szűrők című témakörben tetszésszerint.](filters-concept.md) [Dynamic Manifests](filters-dynamic-manifest-overview.md)

Ez a témakör azt mutatja be, hogy a Media Services .NET SDK használatával hogyan definiálható szűrő egy igény szerinti videóeszközhöz, és hogyan hozhat létre [számlaszűrőket](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) és [eszközszűrőket](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

> [!NOTE]
> Ügyeljen arra, hogy tekintse át [a presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Előfeltételek 

- Tekintse át [a szűrőket és a dinamikus jegyzékeket.](filters-dynamic-manifest-overview.md)
- [Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md). Győződjön meg arról, hogy nem emlékszik az erőforráscsoport nevére és a Media Services-fiók nevére. 
- Az [API-k eléréséhez](access-api-cli-how-to.md) szükséges információk beszerezni
- Tekintse [át a feltöltést, a kódolást és az adatfolyamot az Azure Media Services használatával,](stream-files-tutorial-with-api.md) és tekintse meg a [.NET SDK használatának megkezdését](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>Szűrő definiálása  

A .NET-ben [a FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) és [filterTrackPropertyCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) osztályok segítségével konfigurálhatja a pályabeállításokat. 

A következő kód egy szűrőt határoz meg, amely tartalmazza az EK-3 hangsávokat és a 0-1000000 tartományban lévő bitrátát.

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

## <a name="create-account-filters"></a>Fiókszűrők létrehozása

A következő kód bemutatja, hogyan hozhat létre a .NET programmal olyan fiókszűrőt, amely tartalmazza a fent meghatározott összes [pályakijelölést.](#define-a-filter) 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Eszközszűrők létrehozása

A következő kód bemutatja, hogyan hozhat létre a .NET értékszűrőt, amely tartalmazza a [fent meghatározott](#define-a-filter)összes pályaválasztást . 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Szűrők társítása a streamelési lokátorral

Megadhatja az eszköz- vagy fiókszűrők listáját, amely a streamelési lokátorra vonatkozna. A [dinamikus csomagzó (Streamelési végpont)](dynamic-packaging-overview.md) alkalmazza ezt a szűrőlistát az ügyfél által az URL-ben megadott szűrőkkel együtt. Ez a kombináció [dinamikus jegyzéket](filters-dynamic-manifest-overview.md)hoz létre, amely a Streamelési lokátoron megadott URL+ szűrők szűrőin alapul. Javasoljuk, hogy használja ezt a funkciót, ha szűrőket szeretne alkalmazni, de nem szeretné elérhetővé tenni az URL-címben szereplő szűrőneveket.

A következő C# kód bemutatja, hogyan `StreamingLocator.Filters`hozhat létre streamelési lokátort, és adja meg. Ez egy nem kötelező `IList<string>` tulajdonság, amely egy szűrőneveket vesz fel.

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
      
## <a name="stream-using-filters"></a>Adatfolyam szűrők használatával

Miután definiálja a szűrőket, az ügyfelek használhatják őket a streamelési URL-címben. A szűrők adaptív sávszélességű streamelési protokollokra alkalmazhatók: Apple HTTP Live Streaming (HLS), MPEG-DASH és Smooth Streaming.

Az alábbi táblázat néhány példát mutat be a szűrőkkel ellátott URL-címekre:

|Protocol (Protokoll)|Példa|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>További lépések

[Videók streamelése](stream-files-tutorial-with-api.md) 


