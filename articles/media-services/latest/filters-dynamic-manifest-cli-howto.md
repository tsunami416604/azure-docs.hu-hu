---
title: Szűrők létrehozása az Azure Media Services használatával a CLI használatával| Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan cli használatával szűrőket hozhat létre az Azure Media Services v3 használatával.
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
ms.date: 06/13/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 74516aa921e45917f327a193a1c972b021c9c8ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74896071"
---
# <a name="creating-filters-with-cli"></a>Szűrők létrehozása CLI-vel 

Amikor a tartalmat az ügyfelek (élő események streamelése vagy az igény szerinti videó), az ügyfél szükség lehet nagyobb rugalmasságot, mint amit az alapértelmezett eszköz jegyzékfájljában leírt. Az Azure Media Services lehetővé teszi, hogy fiókszűrőket és eszközszűrőket határozzon meg a tartalomhoz. 

A szolgáltatás részletes leírását és a használatának forgatókönyveit a Dinamikus jegyzékek és [szűrők című témakörben tetszésszerint.](filters-concept.md) [Dynamic Manifests](filters-dynamic-manifest-overview.md)

Ez a témakör bemutatja, hogyan konfigurálhat szűrőt egy igény szerinti videóeszközhöz, és hogyan használhatja a CLI-t a Media Services v3-hoz [fiókszűrők](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) és [eszközszűrők](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest)létrehozásához. 

> [!NOTE]
> Ügyeljen arra, hogy tekintse át [a presentationTimeRange](filters-concept.md#presentationtimerange).

## <a name="prerequisites"></a>Előfeltételek 

- [Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md). Győződjön meg arról, hogy nem emlékszik az erőforráscsoport nevére és a Media Services-fiók nevére. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-filter"></a>Szűrő definiálása 

A következő példa a végleges jegyzékhez hozzáadott pályakijelölési feltételeket határozza meg. Ez a szűrő tartalmazza az EC-3-as hangsávokat és a 0-1000000 tartományban lévő bitrátát.

> [!TIP]
> Ha **szűrőket** kíván definiálni a REST-ben, vegye figyelembe, hogy a "Tulajdonságok" burkoló JSON-objektumot kell megadnia.  

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

## <a name="create-account-filters"></a>Fiókszűrők létrehozása

A következő [az ams fiókszűrő](https://docs.microsoft.com/cli/azure/ams/account-filter?view=azure-cli-latest) parancs létrehoz egy fiókszűrőt a [korábban definiált](#define-a-filter)szűrősáv-kijelölésekkel. 

A parancs lehetővé teszi `--tracks` egy választható paraméter átadást, amely a jsont tartalmazza, amely a pályaválasztásokat jelöli.  A JSON fájlból való betöltéséhez használja a @{file} elemet. Ha az Azure CLI-t helyileg használja, adja meg a teljes fájlelérési utat:

```azurecli
az ams account-filter create -a amsAccount -g resourceGroup -n filterName --tracks @tracks.json
```

Lásd még a [JSON-példákat a szűrőkhöz.](https://docs.microsoft.com/rest/api/media/accountfilters/createorupdate#create-an-account-filter)

## <a name="create-asset-filters"></a>Eszközszűrők létrehozása

A következő [az ams eszközszűrő](https://docs.microsoft.com/cli/azure/ams/asset-filter?view=azure-cli-latest) parancs létrehoz egy eszközszűrőt a [korábban definiált](#define-a-filter)szűrősáv-kijelölésekkel . 

```azurecli
az ams asset-filter create -a amsAccount -g resourceGroup -n filterName --asset-name assetName --tracks @tracks.json
```

Lásd még a [JSON-példákat a szűrőkhöz.](https://docs.microsoft.com/rest/api/media/assetfilters/createorupdate#create-an-asset-filter)

## <a name="associate-filters-with-streaming-locator"></a>Szűrők társítása a streamelési lokátorral

Megadhatja az eszköz- vagy fiókszűrők listáját, amely a streamelési lokátorra vonatkozna. A [dinamikus csomagzó (Streamelési végpont)](dynamic-packaging-overview.md) alkalmazza ezt a szűrőlistát az ügyfél által az URL-ben megadott szűrőkkel együtt. Ez a kombináció [dinamikus jegyzéket](filters-dynamic-manifest-overview.md)hoz létre, amely a Streamelési lokátoron megadott URL+ szűrők szűrőin alapul. Javasoljuk, hogy használja ezt a funkciót, ha szűrőket szeretne alkalmazni, de nem szeretné elérhetővé tenni az URL-címben szereplő szűrőneveket.

A következő CLI-kód bemutatja, hogyan `filters`hozhat létre streamelési lokátort, és megadhatja a . Ez egy választható tulajdonság, amely az eszközszűrő nevek és/vagy számlaszűrőnevek szóközre tagolt listáját veszi fel.

```azurecli
az ams streaming-locator create -a amsAccount -g resourceGroup -n streamingLocatorName \
                                --asset-name assetName \                               
                                --streaming-policy-name policyName \
                                --filters filterName1 filterName2
                                
```

## <a name="stream-using-filters"></a>Adatfolyam szűrők használatával

Miután definiálja a szűrőket, az ügyfelek használhatják őket a streamelési URL-címben. A szűrők adaptív sávszélességű streamelési protokollokra alkalmazhatók: Apple HTTP Live Streaming (HLS), MPEG-DASH és Smooth Streaming.

Az alábbi táblázat néhány példát mutat be a szűrőkkel ellátott URL-címekre:

|Protocol (Protokoll)|Példa|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="next-step"></a>Következő lépés

[Videók streamelése](stream-files-tutorial-with-api.md) 

## <a name="see-also"></a>Lásd még

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
