---
title: Szűrők létrehozása az Azure Media Services v3 REST API-jával
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
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: f9134dd3bc926e6e2f454e5187e03365e91ed22a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75780334"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Szűrők létrehozása a Media Services REST API-val

Amikor a tartalmat az ügyfelek (élő események streamelése vagy igény szerinti videó) az ügyfél szükség lehet nagyobb rugalmasságot, mint amit az alapértelmezett eszköz jegyzékfájljában leírt. Az Azure Media Services lehetővé teszi, hogy fiókszűrőket és eszközszűrőket határozzon meg a tartalomhoz. 

A szolgáltatás részletes leírását és a használatának forgatókönyveit a Dinamikus jegyzékek és [szűrők című témakörben tetszésszerint.](filters-concept.md) [Dynamic Manifests](filters-dynamic-manifest-overview.md)

Ez a témakör bemutatja, hogyan definiálható szűrő egy igény szerinti videóeszközhöz, és hogyan hozhat létre REST API-kat [számlaszűrők](https://docs.microsoft.com/rest/api/media/accountfilters) és [eszközszűrők](https://docs.microsoft.com/rest/api/media/assetfilters)létrehozásához. 

> [!NOTE]
> Ügyeljen arra, hogy tekintse át [a presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Előfeltételek 

A témakörben ismertetett lépések végrehajtásához a következőket kell elvégeznie:

- Tekintse át [a szűrőket és a dinamikus jegyzékeket.](filters-dynamic-manifest-overview.md)
- [A Postman konfigurálása az Azure Media Services REST API-hívásaihoz.](media-rest-apis-with-postman.md)

    Győződjön meg arról, hogy kövesse az [Azure AD-token beszerezni című](media-rest-apis-with-postman.md#get-azure-ad-token)témakör utolsó lépését. 

## <a name="define-a-filter"></a>Szűrő definiálása  

A következő a **kérelem törzs** példa, amely meghatározza a pálya kiválasztási feltételeket, amelyek hozzáadódnak a jegyzékhez. Ez a szűrő tartalmazza az EC-3-as hangsávokat és a 0-1000000 tartományban lévő bitrátát.

```json
{
    "properties": {
        "tracks": [
          {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Audio",
                        "operation": "Equal"
                    },
                    {
                        "property": "FourCC",
                        "value": "EC-3",
                        "operation": "Equal"
                    }
                ]
            },
            {
                "trackSelections": [
                    {
                        "property": "Type",
                        "value": "Video",
                        "operation": "Equal"
                    },
                    {
                        "property": "Bitrate",
                        "value": "0-1000000",
                        "operation": "Equal"
                    }
                ]
            }
        ]
     }
}
```

## <a name="create-account-filters"></a>Fiókszűrők létrehozása

A letöltött postás gyűjteményében válassza a **Fiókszűrők**->**létrehozása fiókszűrő létrehozása vagy frissítése**lehetőséget.

A **PUT** HTTP kérelem módszer hasonló a következőkhöz:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Válassza a **Törzs** lapot, és illessze be a [korábban megadott](#define-a-filter)jsonkódot.

Válassza a **Küldés**lehetőséget. 

A szűrő létrejött.

További információt a [Létrehozás vagy frissítés](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate)című témakörben talál. Lásd még a [JSON-példákat a szűrőkhöz.](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter)

## <a name="create-asset-filters"></a>Eszközszűrők létrehozása  

A letöltött "Media Services v3" Postman gyűjteményben válassza **az Eszközök**->**létrehozása eszközszűrő létrehozása vagy frissítése**lehetőséget.

A **PUT** HTTP kérelem módszer hasonló a következőkhöz:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Válassza a **Törzs** lapot, és illessze be a [korábban megadott](#define-a-filter)jsonkódot.

Válassza a **Küldés**lehetőséget. 

Az eszközszűrő létrejött.

Az eszközszűrők létrehozásáról és frissítésével kapcsolatos további tudnivalókért olvassa el a Létrehozás vagy frissítés című [témakört.](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate) Lásd még a [JSON-példákat a szűrőkhöz.](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter) 

## <a name="associate-filters-with-streaming-locator"></a>Szűrők társítása a streamelési lokátorral

Megadhatja az eszköz- vagy fiókszűrők listáját, amely a streamelési lokátorra vonatkozna. A [dinamikus csomagzó (Streamelési végpont)](dynamic-packaging-overview.md) alkalmazza ezt a szűrőlistát az ügyfél által az URL-ben megadott szűrőkkel együtt. Ez a kombináció [dinamikus jegyzéket](filters-dynamic-manifest-overview.md)hoz létre, amely a Streamelési lokátoron megadott URL+ szűrők szűrőin alapul. Javasoljuk, hogy használja ezt a funkciót, ha szűrőket szeretne alkalmazni, de nem szeretné elérhetővé tenni az URL-címben szereplő szűrőneveket.

Szűrők létrehozásához és társításához a REST használatával a [Streamelési lokátorok – Api létrehozása](https://docs.microsoft.com/rest/api/media/streaminglocators/create) és a kérelem `properties.filters` [törzsében](https://docs.microsoft.com/rest/api/media/streaminglocators/create#request-body)megadott használatával.
                                
## <a name="stream-using-filters"></a>Adatfolyam szűrők használatával

Miután definiálja a szűrőket, az ügyfelek használhatják őket a streamelési URL-címben. A szűrők adaptív sávszélességű streamelési protokollokra alkalmazhatók: Apple HTTP Live Streaming (HLS), MPEG-DASH és Smooth Streaming.

Az alábbi táblázat néhány példát mutat be a szűrőkkel ellátott URL-címekre:

|Protocol (Protokoll)|Példa|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-steps"></a>További lépések

[Videók streamelése](stream-files-tutorial-with-rest.md) 
