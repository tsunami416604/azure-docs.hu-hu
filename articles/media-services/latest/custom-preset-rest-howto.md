---
title: Egyéni átalakítás kódolása a Media Services v3 REST használatával – Azure | Microsoft dokumentumok
description: Ez a témakör bemutatja, hogyan használhatja az Azure Media Services v3-as egyéni átalakítás t rest használatával.
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
ms.openlocfilehash: 30e22cb786e5dc2a667fe41ca8edf398cf0b7613
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "65761797"
---
# <a name="how-to-encode-with-a-custom-transform---rest"></a>Hogyan kódolunk egyéni átalakítással - REST

Az Azure Media Services használatával történő kódolás során gyorsan elkezdheti az ajánlott beépített készletek egyikét, amelyek az iparági bevált gyakorlatokon alapulnak, ahogy azt a [Streamelési fájlok](stream-files-tutorial-with-rest.md#create-a-transform) oktatóanyaga is mutatja. Egyéni készletet is létrehozhat az adott forgatókönyv vagy eszköz követelményeinek megcélzásához.

## <a name="considerations"></a>Megfontolandó szempontok

Egyéni készletek létrehozásakor a következő szempontok érvényesek:

* Az AVC-tartalom magasságának és szélességének minden értékének a 4 többszörösének kell lennie.
* Az Azure Media Services v3-as részében az összes kódolási bitsebesség bitper másodpercben van. Ez eltér a v2 API-kkal rendelkező készletektől, amelyek kilobit/második egységként használták a kilobitokat/ a második at. Ha például a v2-ben a bitráta 128 (kilobit/másodperc), akkor a v3-ban 128000 (bit/másodperc) lesz megadva.

## <a name="prerequisites"></a>Előfeltételek 

- [Hozzon létre egy Media Services-fiókot](create-account-cli-how-to.md). <br/>Győződjön meg arról, hogy nem emlékszik az erőforráscsoport nevére és a Media Services-fiók nevére. 
- [A Postman konfigurálása az Azure Media Services REST API-hívásaihoz.](media-rest-apis-with-postman.md)<br/>Győződjön meg arról, hogy kövesse az [Azure AD-token beszerezni című](media-rest-apis-with-postman.md#get-azure-ad-token)témakör utolsó lépését. 

## <a name="define-a-custom-preset"></a>Egyéni készlet definiálása

A következő példa egy új átalakítás kérelemtörzsét határozza meg. Meghatározzuk a kimenetek egy készletét, amelyet az átalakítás során szeretnénk létrehozni. 

Ebben a példában először a hangkódoláshoz adunk hozzá egy AacAudio réteget, és két H264Video réteget a videó kódoláshoz. A videorétegeken címkéket rendelünk hozzá, hogy azok használhatók legyenek a kimeneti fájlnevekben. Ezután azt szeretnénk, hogy a kimenet is tartalmazza a miniatűröket. Az alábbi példában png formátumú képeket adunk meg, amelyek a bemeneti videó felbontásának 50%-ában, és a bemeneti videó hosszának {25%, 50%, 75} - létrehozásához. Végül, mi határozza meg a formátumot a kimeneti fájlokat - az egyik a videó + audio, és egy másik a miniatűrök. Mivel több H264Layers-ünk van, olyan makrókat kell használnunk, amelyek rétegenként egyedi neveket hoznak létre. Használhatjuk vagy `{Label}` `{Bitrate}` makrót, a példa az előbbit mutatja.

```json
{
    "properties": {
        "description": "Basic Transform using a custom encoding preset",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
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
            }
        ]
    }
}

```

## <a name="create-a-new-transform"></a>Új átalakítás létrehozása  

Ebben a példában létrehozunk egy **átalakítást,** amely a korábban definiált egyéni készleten alapul. Átalakítás létrehozásakor először a [Get](https://docs.microsoft.com/rest/api/media/transforms/get) parancsot kell használnia annak ellenőrzésére, hogy létezik-e már ilyen. Ha létezik az átalakítás, használja fel újra. 

A letöltött postás gyűjteményében válassza az **Átalakítások és feladatok**->**létrehozása vagy az átalakítás frissítése lehetőséget.**

A **PUT** HTTP kérelem módszer hasonló a következőkhöz:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName?api-version={{api-version}}
```

Válassza a **Törzs** lapot, és cserélje le a törzset a [korábban megadott](#define-a-custom-preset)jsonkódra. Ahhoz, hogy a Media Services alkalmazza az átalakítást a megadott videóra vagy hangra, el kell küldenie egy feladatot az adott átalakítás alatt.

Válassza a **Küldés**lehetőséget. 

Ahhoz, hogy a Media Services alkalmazza az átalakítást a megadott videóra vagy hangra, el kell küldenie egy feladatot az adott átalakítás alatt. Egy teljes példa, amely bemutatja, hogyan kell benyújtani a munkát egy átalakítás alatt, [lásd: Bemutató: Videofájlok streamelése - REST](stream-files-tutorial-with-rest.md).

## <a name="next-steps"></a>További lépések

Lásd [a többi REST-műveletet](https://docs.microsoft.com/rest/api/media/)
