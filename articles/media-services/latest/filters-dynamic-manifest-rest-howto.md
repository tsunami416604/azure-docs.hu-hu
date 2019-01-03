---
title: Szűrők létrehozása az Azure Media Services REST API-val |} A Microsoft Docs
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
ms.date: 12/17/2018
ms.author: juliako
ms.openlocfilehash: 32b9664d12d6fe3a44329665c730dbc8709430f2
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/20/2018
ms.locfileid: "53650841"
---
# <a name="creating-filters-with-media-services-rest-api"></a>Szűrők létrehozása a Media Services REST API-val

Az ügyfelek számára (élő eseményeket vagy igény szerinti Videószolgáltatás streaming) tartalomtovábbításkor az ügyfél igényelhet az alapértelmezett eszköz jegyzékfájl leírtnál nagyobb rugalmasságot. Az Azure Media Services lehetővé teszi, hogy meghatározza a fiók és a tartalom eszköz szűrőket. További információkért lásd: [szűrők és dinamikus jegyzékek](filters-dynamic-manifest-overview.md).

Ez a témakör bemutatja, hogyan videó igény szerint eszköz a kapcsolódó szűrő megadásához és a REST API-k használatával létrehozhat [Fiókszűrők](https://docs.microsoft.com/rest/api/media/accountfilters) és [eszköz szűrők](https://docs.microsoft.com/rest/api/media/assetfilters). 

## <a name="prerequisites"></a>Előfeltételek 

Az ebben a témakörben leírt lépések elvégzéséhez kell:

- Felülvizsgálat [szűrők és dinamikus jegyzékek](filters-dynamic-manifest-overview.md).
- [Postman konfigurálása az Azure Media Services REST API-hívások](media-rest-apis-with-postman.md).

    Kövesse a témakör az utolsó lépés [lekérése az Azure AD-Token](media-rest-apis-with-postman.md#get-azure-ad-token). 

## <a name="define-a-filter"></a>Szűrő megadásához.  

Az alábbiakban a **kérelem törzse** példa, amely meghatározza a jegyzékfájl hozzáadott követése kiválasztási feltételek. Ez a szűrő tartalmazza, bármilyen, amelyek EC-3 hangsáv és bármely videó nyomon követi a 0-1000000 sávszélességű rendelkező tartományt.

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

## <a name="create-account-filters"></a>Fiók szűrők létrehozása

Válassza ki a letöltött a Postman-gyűjtemény, **Fiókszűrők**->**létrehozás vagy frissítés egy szűrő**.

A **PUT** HTTP-kérési metódust hasonlít:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/accountFilters/{filterName}?api-version=2018-07-01
```

Válassza ki a **törzs** lapra, és illessze be a json-kód, [korábban meghatározott](#define-a-filter).

Kattintson a **Küldés** gombra. 

A szűrő létrejött.

További információkért lásd: [létrehozásának vagy frissítésének](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate). Lásd még [JSON példák a szűrők](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Az eszközintelligencia-szűrők létrehozásához  

Válassza a "Media Services v3" Postman-gyűjtemény letöltött, **eszközök**->**létrehozásának vagy frissítésének eszköz szűrő**.

A **PUT** HTTP-kérési metódust hasonlít:

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{accountName}/assets/{assetName}/assetFilters/{filterName}?api-version=2018-07-01
```

Válassza ki a **törzs** lapra, és illessze be a json-kód, [korábban meghatározott](#define-a-filter).

Kattintson a **Küldés** gombra. 

Az eszközintelligencia-szűrő létrehozása.

További információ a létrehozása vagy frissítése az eszközintelligencia szűrők: [létrehozásának vagy frissítésének](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate). Lásd még [JSON példák a szűrők](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter). 

## <a name="next-steps"></a>További lépések

[Stream-videók](stream-files-tutorial-with-rest.md) 
