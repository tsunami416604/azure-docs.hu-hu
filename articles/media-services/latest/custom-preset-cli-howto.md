---
title: Kódolás a Media Services v3 CLI – az Azure használatával egyéni átalakító |} A Microsoft Docs
description: Ez a témakör bemutatja, hogyan használható az Azure Media Services v3 kódolása a CLI-vel Egyéni átalakító.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 03/12/2019
ms.author: juliako
ms.openlocfilehash: b0329c8c0bfa56e5ae36c5f4223edca91e6510a0
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58755606"
---
# <a name="how-to-encode-with-a-custom-transform---cli"></a>Az egyéni átalakítási – parancssori felület kódolása

Ha az Azure Media Services encoding, ismerkedhet meg gyorsan az, ahogyan az ágazatban kialakult bevált gyakorlaton alapuló ajánlott a beépített beállítások egyikét a [fájlok Streamelési](stream-files-cli-quickstart.md#create-a-transform-for-adaptive-bitrate-encoding) a rövid útmutató. Az egyedi, amelyekre az adott forgatókönyv vagy eszközkövetelmények készletek is létrehozható.

## <a name="considerations"></a>Megfontolandó szempontok

Egyéni készletek létrehozásakor a következő szempontokat kell figyelembe venni:

* Minden értékét magasságát és szélességét AVC tartalom kezelésére kell költeni 4 többszörösének kell lennie.
* Az Azure Media Services v3-as a kódolási bitsebességre való átkódolása összes bit / másodperc. Ez különbözik a készletek a v2 API-kkal használt egységet kilobit/másodperc. Például ha az átviteli sebesség a v2-ben van megadva, 128 (kilobit/másodperc), a v3-as volna meg akkor 128000 (bit/másodperc).

## <a name="prerequisites"></a>Előfeltételek 

[A Media Services-fiók létrehozása](create-account-cli-how-to.md). <br/>Ellenőrizze, hogy ne felejtse el az erőforráscsoport nevét és a Media Services-fiók nevét. 

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="define-a-custom-preset"></a>Egyéni előbeállítás definiálása

Az alábbi példa egy új átalakítás kérelem törzsét határozza meg. Azt adja meg, amelyet szeretnénk hozható létre, ha az átalakítás szolgál csoportját. 

Ebben a példában először hozzáadunk egy AacAudio réteget a hang kódolása és két H264Video réteget a videó kódolásához. A videó rétegekben hogy címkék hozzárendelésére, hogy a kimeneti fájl nevében szereplő használható. Ezután szeretnénk a kimenetet a miniatűrök is tartalmazhatnak. Az alábbi példában képek PNG formátumú, a bemeneti videó felbontása 50 %-át, vagy három időbélyegeket – {25 %-kal, 50 %-os, 75}, a bemeneti videó hosszától egy előállított adjuk meg. Végül adjuk meg a formátum a kimeneti fájlok – egy videó és hang-, a másik pedig a miniatűrök. Mivel több H264Layers rendelkezünk, egyedi nevek rétegenként eredményező makrók van. Azt is, vagy használja a `{Label}` vagy `{Bitrate}` makró-, a példa bemutatja a korábbi.

Az átalakítási fájlba mentése fogjuk. Ebben a példában azt a nevet a fájlnak `customPreset.json`. 

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
                            "filenamePattern": "Video-{Basename}-{Label}{Extension}",
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

## <a name="create-a-new-transform"></a>Hozzon létre egy új átalakítás  

Ebben a példában létrehozunk egy **átalakítása** , amely a korábban meghatározott egyéni előbeállítás alapul. Egy-egy átalakítási létrehozásakor először ellenőrizze Ha egy már létezik. Ha az átalakítás létezik, akkor újra felhasználhatja. A következő `show` parancsot adja vissza a `customTransformName` átalakítása, ha létezik:

```cli
az ams transform show -a amsaccount -g amsResourceGroup -n customTransformName
```

A következő CLI-paranccsal megtekintheti az átalakítás a (korábban meghatározott) egyéni előbeállítás alapján hoz létre. 

```cli
az ams transform create -a amsaccount -g amsResourceGroup -n customTransformName --description "Basic Transform using a custom encoding preset" --preset customPreset.json
```

A Media Services a alkalmazni az átalakítás a megadott videó vagy hang kell egy adott átalakítási feladat elküldéséhez. Egy teljes példa bemutatja, hogyan lehet elküldeni egy egy-egy átalakítási feladat, lásd: [a rövid útmutató: Stream-videó fájlok – parancssori felület](stream-files-cli-quickstart.md).

## <a name="see-also"></a>Lásd még

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
