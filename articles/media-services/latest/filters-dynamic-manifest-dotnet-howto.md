---
title: Szűrők létrehozása az Azure Media Services .NET SDK-val
description: Ez a témakör azt ismerteti, hogyan hozhatók létre szűrők, így az ügyfél használhatja őket stream konkrét szakaszokra datového proudu. A Media Services dinamikus jegyzékek érdekében, ami a szelektív hoz létre.
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
ms.openlocfilehash: 2bcb8762b94347f4409507fb89a18cb6c9d0dacd
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66494296"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Szűrők létrehozása a Media Services .NET SDK-val

Az ügyfelek számára (élő eseményeket vagy igény szerinti Videószolgáltatás streaming) tartalomtovábbításkor az ügyfél igényelhet az alapértelmezett eszköz jegyzékfájl leírtnál nagyobb rugalmasságot. Az Azure Media Services lehetővé teszi, hogy meghatározza a fiók és a tartalom eszköz szűrőket. 

Ez a szolgáltatás és a felhasználási forgatókönyvek részletes ismertetését lásd: [dinamikus jegyzékfájlok](filters-dynamic-manifest-overview.md) és [szűrők](filters-concept.md).

Ez a témakör bemutatja, hogyan videó igény szerint eszköz a kapcsolódó szűrő megadásához, és hozzon létre a Media Services .NET SDK használatával [Fiókszűrők](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) és [eszköz szűrők](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

> [!NOTE]
> Mindenképpen tekintse át [presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Előfeltételek 

- Felülvizsgálat [szűrők és dinamikus jegyzékek](filters-dynamic-manifest-overview.md).
- [A Media Services-fiók létrehozása](create-account-cli-how-to.md). Ellenőrizze, hogy ne felejtse el az erőforráscsoport nevét és a Media Services-fiók nevét. 
- Szükséges információk [API-k elérése](access-api-cli-how-to.md)
- Felülvizsgálat [feltöltése, kódolása és streamelése az Azure Media Services használatával](stream-files-tutorial-with-api.md) megtekintéséhez hogyan [.NET SDK használatának megkezdéséhez](stream-files-tutorial-with-api.md#start_using_dotnet)

## <a name="define-a-filter"></a>Szűrő megadásához.  

A .NET, a kijelölések nyomon követése konfigurálása [FilterTrackSelection](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackselection?view=azure-dotnet) és [FilterTrackPropertyCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.filtertrackpropertycondition?view=azure-dotnet) osztályokat. 

Az alábbi kód meghatároz egy szűrőt, amely tartalmazza a bármely, amelyek EC-3 hangsáv és bármely videó nyomon követi a 0-1000000 sávszélességű rendelkező tartományt.

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

## <a name="create-account-filters"></a>Fiók szűrők létrehozása

A következő kód bemutatja, hogyan hozhat létre egy szűrő, amely tartalmazza az összes nyomon követése beállításokat a .NET használatával [fent meghatározott](#define-a-filter). 

```csharp
AccountFilter accountFilterParams = new AccountFilter(tracks: includedTracks);
client.AccountFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, "accountFilterName1", accountFilter);
```

## <a name="create-asset-filters"></a>Az eszközintelligencia-szűrők létrehozásához

A következő kód bemutatja, hogyan hozhat létre egy eszköz szűrő, amely tartalmazza az összes nyomon követése beállításokat a .NET használatával [fent meghatározott](#define-a-filter). 

```csharp
AssetFilter assetFilterParams = new AssetFilter(tracks: includedTracks);
client.AssetFilters.CreateOrUpdate(config.ResourceGroup, config.AccountName, encodedOutputAsset.Name, "assetFilterName1", assetFilterParams);
```

## <a name="associate-filters-with-streaming-locator"></a>Streamelési lokátor szűrők társítása

Megadhatja, hogy az eszköz vagy a fiók szűrők, a Streamelési lokátor is érvényesek listáját. A [dinamikus Packager (folyamatos átviteli végponton)](dynamic-packaging-overview.md) vonatkozik ez a lista azokat az URL-címet adja meg az ügyfél és-szűrők. Állít elő, ez a kombináció egy [dinamikus Manifest](filters-dynamic-manifest-overview.md), amely alapján az URL-címben szűrők + szűrők megad a Streamelési lokátor. Azt javasoljuk, hogy a szolgáltatás használata, ha alkalmazza a szűrőket, de nem szeretné elérhetővé tenni az URL-szűrő nevét.

A következő C# kód bemutatja, hogyan hozhat létre egy Streamelési Lokátort, és adja meg `StreamingLocator.Filters`. Ezt a tulajdonságot nem kötelező, amely egy `IList<string>` szűrő nevét.

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
      
## <a name="stream-using-filters"></a>Stream-szűrők használata

Miután meghatározott szűrőket, az ügyfelek lehetett használni őket a streamelési URL-CÍMÉT. Az adaptív sávszélességű streamelési protokollok szűrőket is lehet alkalmazni: Apple HTTP Live Streaming (HLS), MPEG-DASH és Smooth Streaming.

Az alábbi táblázatban néhány példa az URL-címek szűrőket jeleníti meg:

|Protocol|Példa|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>További lépések

[Stream-videók](stream-files-tutorial-with-api.md) 


