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
ms.openlocfilehash: 5aa617edf13aee9c5899a59c46aeb729f202719f
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53744207"
---
# <a name="creating-filters-with-cli"></a>Szűrők létrehozása a CLI-vel 

Kézbesítse a tartalmakat az ügyfelek számára (élő eseményeket vagy igény szerinti Videószolgáltatás streaming), amikor az ügyfél az alapértelmezett eszköz jegyzékfájl leírtnál nagyobb rugalmasságot előfordulhat, hogy kell. Az Azure Media Services lehetővé teszi, hogy meghatározza a fiók és a tartalom eszköz szűrőket. További információkért lásd: [szűrők és dinamikus jegyzékek](filters-dynamic-manifest-overview.md).

Ez a témakör bemutatja, hogyan beállít egy szűrőt az igény szerinti videó eszköz és a CLI a Media Services v3 használatával létrehozhat [Fiókszűrők](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) és [eszköz szűrők](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest). 

## <a name="prerequisites"></a>Előfeltételek 

- Telepítse és a parancssori Felületet helyileg használja, ez a cikk az Azure CLI 2.0-s vagy újabb verziójára van szükség. A rendelkezésére álló verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, olvassa el [az Azure CLI telepítését](/cli/azure/install-azure-cli) ismertető cikket. 

    Jelenleg nem minden [Media Services v3 CLI](https://aka.ms/ams-v3-cli-ref) parancsok működnek az Azure Cloud shellben. Javasoljuk, hogy a parancssori Felületet helyileg használja.
- [A Media Services-fiók létrehozása](create-account-cli-how-to.md). Ellenőrizze, hogy ne felejtse el az erőforráscsoport nevét és a Media Services-fiók nevét. 
- Felülvizsgálat [szűrők és dinamikus jegyzékek](filters-dynamic-manifest-overview.md).

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

A következő parancsot egy lehetőség lehetővé teszi, hogy `--tracks` , amely fogad egy fájlt. Ha az Azure CLI helyileg használ, adja meg a fájl teljes elérési útja:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @c:\tracks.json
```

Ha az Azure Cloud Shellt használja, töltse fel a fájlt a Cloud Shellben (Keresés a rendszerhéj-ablak tetején, a feltöltési/letöltési fájlok gombra). Ezután hivatkozhat a fájlt ehhez hasonló:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Lásd még [JSON példák a szűrők](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create_an_account_filter).

## <a name="create-asset-filters"></a>Az eszközintelligencia-szűrők létrehozásához

A következő [az ams az eszközintelligencia-szűrő](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) parancs létrehoz egy adategység-szűrő szűrő nyomon követése beállításokat, amelyek korábban [korábban meghatározott](#define-a-filter). 

> [!TIP]
> Információ megadása a fájl nevét, helyét az előző szakaszban.

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Lásd még [JSON példák a szűrők](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create_an_asset_filter).

## <a name="next-step"></a>Következő lépés

[Stream-videók](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Lásd még

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
