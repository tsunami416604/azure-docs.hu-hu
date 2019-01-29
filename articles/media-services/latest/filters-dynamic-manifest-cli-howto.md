---
title: Szűrők létrehozása az Azure Media Services a CLI használatával |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan hozhat létre szűrők a Media Services a parancssori felület használatával.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 2ba3de32f4ec3b9f6faf1d5a51da9c1c91e4a2e4
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099306"
---
# <a name="creating-filters-with-cli"></a>Szűrők létrehozása a CLI-vel 

Kézbesítse a tartalmakat az ügyfelek számára (élő eseményeket vagy igény szerinti Videószolgáltatás streaming), amikor az ügyfél az alapértelmezett eszköz jegyzékfájl leírtnál nagyobb rugalmasságot előfordulhat, hogy kell. Az Azure Media Services lehetővé teszi, hogy meghatározza a fiók és a tartalom eszköz szűrőket. További információkért lásd: [szűrők és dinamikus jegyzékek](filters-dynamic-manifest-overview.md).

Ez a témakör bemutatja, hogyan beállít egy szűrőt az igény szerinti videó eszköz és a CLI a Media Services v3 használatával létrehozhat [Fiókszűrők](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) és [eszköz szűrők](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="prerequisites"></a>Előfeltételek 

- [A Media Services-fiók létrehozása](create-account-cli-how-to.md). Ellenőrizze, hogy ne felejtse el az erőforráscsoport nevét és a Media Services-fiók nevét. 
- Felülvizsgálat [szűrők és dinamikus jegyzékek](filters-dynamic-manifest-overview.md).

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Szűrő megadásához. 

Az alábbi példa meghatározza a végső jegyzékfájl hozzáadott követése kiválasztási feltételek. Ez a szűrő tartalmazza, bármilyen, amelyek EC-3 hangsáv és bármely videó nyomon követi a 0-1000000 sávszélességű rendelkező tartományt.

REST, a megadott szűrők közé tartozik a "Tulajdonságok" burkolót JSON-objektum.  

```json
[
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
                "operation": "NotEqual"
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
```

## <a name="create-account-filters"></a>Fiók szűrők létrehozása

A következő [az ams-fiók-szűrő](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) parancs létrehoz egy szűrő szűrő nyomon követése beállításokat, amelyek korábban [korábban meghatározott](#define-a-filter). 

A parancs lehetővé teszi, hogy egy nem kötelező adnia `--tracks` JSON a track beállításokat jelölő paraméterben.  Használat @{file} betölteni a JSON-fájlból. Ha az Azure CLI helyileg használ, adja meg a fájl teljes elérési útja:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Lásd még [JSON példák a szűrők](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Az eszközintelligencia-szűrők létrehozásához

A következő [az ams az eszközintelligencia-szűrő](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) parancs létrehoz egy adategység-szűrő szűrő nyomon követése beállításokat, amelyek korábban [korábban meghatározott](#define-a-filter). 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Lásd még [JSON példák a szűrők](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).

## <a name="next-step"></a>Következő lépés

[Stream-videók](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Lásd még

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
