---
title: Egyéni átalakítás kódolása a Media Services v3 Azure CLI használatával | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan használhatja az Azure Media Services v3-as egyéni átalakításkódolásához az Azure CLI használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 05/14/2019
ms.author: juliako
ms.openlocfilehash: 7c1b446ccf04199449f012e738f6a03660735f50
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382953"
---
# <a name="how-to-encode-with-a-custom-transform---azure-cli"></a>Kódolás egyéni átalakítással - Azure CLI

Az Azure Media Services használatával történő kódolás során gyorsan elkezdheti az ajánlott beépített készletek egyikét, amelyek az iparági bevált gyakorlatokon alapulnak, ahogy azt a [streamelési fájlok](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) rövid útmutatója is mutatja. Egyéni készletet is létrehozhat az adott forgatókönyv vagy eszköz követelményeinek megcélzásához.

## <a name="considerations"></a>Megfontolandó szempontok

Egyéni készletek létrehozásakor a következő szempontok érvényesek:

* Az AVC-tartalom magasságának és szélességének minden értékének a 4 többszörösének kell lennie.
* Az Azure Media Services v3-as részében az összes kódolási bitsebesség bitper másodpercben van. Ez eltér a v2 API-kkal rendelkező készletektől, amelyek kilobit/második egységként használták a kilobitokat/ a második at. Ha például a v2-ben a bitráta 128 (kilobit/másodperc), akkor a v3-ban 128000 (bit/másodperc) lesz megadva.

## <a name="prerequisites"></a>Előfeltételek

[Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md).

Győződjön meg arról, hogy nem emlékszik az erőforráscsoport nevére és a Media Services-fiók nevére.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Egyéni készlet definiálása

A következő példa egy új átalakítás kérelemtörzsét határozza meg. Meghatározzuk a kimenetek egy készletét, amelyet az átalakítás során szeretnénk létrehozni.

Ebben a példában először a hangkódoláshoz adunk hozzá egy AacAudio réteget, és két H264Video réteget a videó kódoláshoz. A videorétegeken címkéket rendelünk hozzá, hogy azok használhatók legyenek a kimeneti fájlnevekben. Ezután azt szeretnénk, hogy a kimenet is tartalmazza a miniatűröket. Az alábbi példában png formátumú képeket adunk meg, amelyek a bemeneti videó felbontásának 50%-ában, és a bemeneti videó hosszának {25%, 50%, 75} - létrehozásához. Végül, mi határozza meg a formátumot a kimeneti fájlokat - az egyik a videó + audio, és egy másik a miniatűrök. Mivel több H264Layers-ünk van, olyan makrókat kell használnunk, amelyek rétegenként egyedi neveket hoznak létre. Használhatjuk vagy `{Label}` `{Bitrate}` makrót, a példa az előbbit mutatja.

Ezt az átalakítást egy fájlba fogjuk menteni. Ebben a példában elnevezzük a fájlt `customPreset.json`.

```json
{
    "@odata.type": "#Microsoft.Media.StandardEncoderPreset",
    "codecs": [
        {
            "@odata.type": "#Microsoft.Media.AacAudio",
            "channels": 2,
            "samplingRate": 48000,
            "bitrate": 128000,
            "profile": "AacLc"
        },
        {
            "@odata.type": "#Microsoft.Media.H264Video",
            "keyFrameInterval": "PT2S",
            "stretchMode": "AutoSize",
            "sceneChangeDetection": false,
            "complexity": "Balanced",
            "layers": [
                {
                    "width": "1280",
                    "height": "720",
                    "label": "HD",
                    "bitrate": 3400000,
                    "maxBitrate": 3400000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                },
                {
                    "width": "640",
                    "height": "360",
                    "label": "SD",
                    "bitrate": 1000000,
                    "maxBitrate": 1000000,
                    "bFrames": 3,
                    "slices": 0,
                    "adaptiveBFrame": true,
                    "profile": "Auto",
                    "level": "auto",
                    "bufferWindow": "PT5S",
                    "referenceFrames": 3,
                    "entropyMode": "Cabac"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Media.PngImage",
            "stretchMode": "AutoSize",
            "start": "25%",
            "step": "25%",
            "range": "80%",
            "layers": [
                {
                    "width": "50%",
                    "height": "50%"
                }
            ]
        }
    ],
    "formats": [
        {
            "@odata.type": "#Microsoft.Media.Mp4Format",
            "filenamePattern": "Video-{Basename}-{Label}-{Bitrate}{Extension}",
            "outputFiles": []
        },
        {
            "@odata.type": "#Microsoft.Media.PngFormat",
            "filenamePattern": "Thumbnail-{Basename}-{Index}{Extension}"
        }
    ]
}
```

## <a name="create-a-new-transform"></a>Új átalakítás létrehozása  

Ebben a példában létrehozunk egy **átalakítást,** amely a korábban definiált egyéni készleten alapul. Átalakítás létrehozásakor először ellenőrizze, hogy létezik-e már ilyen. Ha létezik az átalakítás, használja fel újra. A `show` következő parancs `customTransformName` visszaadja az átalakítást, ha létezik:

```azurecli-interactive
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

A következő Azure CLI parancs létrehozza az átalakítást az egyéni készlet (korábban definiált) alapján.

```azurecli-interactive
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

Ahhoz, hogy a Media Services alkalmazza az átalakítást a megadott videóra vagy hangra, el kell küldenie egy feladatot az adott átalakítás alatt. Egy teljes példa, amely bemutatja, hogyan küldhet el egy feladatot egy átalakítás alatt, lásd: [Rövid útmutató: Videofájlok streamelése - Azure CLI](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Lásd még

[Azure CLI](/cli/azure/ams)
