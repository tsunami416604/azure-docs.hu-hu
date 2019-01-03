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
ms.date: 11/28/2018
ms.author: juliako
ms.openlocfilehash: 31b49d882c1affbbef84bb6f4e8989d30fa320fa
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53650967"
---
# <a name="create-filters-with-media-services-net-sdk"></a>Szűrők létrehozása a Media Services .NET SDK-val

Az ügyfelek számára (élő eseményeket vagy igény szerinti Videószolgáltatás streaming) tartalomtovábbításkor az ügyfél igényelhet az alapértelmezett eszköz jegyzékfájl leírtnál nagyobb rugalmasságot. Az Azure Media Services lehetővé teszi, hogy meghatározza a fiók és a tartalom eszköz szűrőket. További információkért lásd: [szűrők és dinamikus jegyzékek](filters-dynamic-manifest-overview.md).

Ez a témakör bemutatja, hogyan videó igény szerint eszköz a kapcsolódó szűrő megadásához, és hozzon létre a Media Services .NET SDK használatával [Fiókszűrők](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.accountfilter?view=azure-dotnet) és [eszköz szűrők](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.models.assetfilter?view=azure-dotnet). 

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

## <a name="next-steps"></a>További lépések

[Stream-videók](stream-files-tutorial-with-api.md) 


