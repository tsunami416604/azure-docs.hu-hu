---
title: Kódolás a felhőben, a Media Services – Azure |} A Microsoft Docs
description: Ez a témakör ismerteti a kódolási folyamatot az Azure Media Services használata közben
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 12/08/2018
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 7c16fc61a8654fa6b7236b9c5252ed4874787d50
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53141747"
---
# <a name="encoding-with-media-services"></a>Kódolás a Media Services használatával

Az Azure Media Services lehetővé teszi, hogy az kiváló minőségű médiafájlt kódolandó, hogy a böngészők és eszközök széles lejátszhatók. Például előfordulhat, hogy az Apple HLS vagy MPEG DASH formátumában szeretné streamelni a tartalmakat. Ez a témakör nyújt útmutatást tartalmait a Media Services v3 kódolással.

Kódolás a Media Services v3-as, hozzon létre egy-egy átalakítási és a egy feladatot kell. Egy-egy átalakítási határozza meg a recept, a kódolási beállítások és kimenetek, és a feladat a recept egy példányát. További információkért lásd: [átalakítások és feladatok](transform-concept.md)

A Media Services encoding, amikor a szolgáltatás használatával ossza meg a kódoló a bemeneti fájlok feldolgozásának módja. Például megadhatja a videó felbontást és/vagy a kívánt hang csatornák száma kódolt tartalmában. 

Ismerkedhet meg gyorsan az ágazatban kialakult bevált gyakorlaton alapuló ajánlott a beépített beállítások egyikét, vagy dönthet úgy, amelyekre az adott forgatókönyv vagy eszközkövetelmények beállított egyéni. További információkért lásd: [Egyéni átalakító Encode](customize-encoder-presets-how-to.md). 

## <a name="built-in-presets"></a>Beépített készletek

A Media Services jelenleg a következő beépített kódolási beállításokat támogatja:  

|**Készlet neve**|**Forgatókönyv**|**Részletek**|
|---|---|---|
|**BuiltInStandardEncoderPreset**|Streamelés|Használja a bemeneti videó a standard szintű Encoder kódolási előbeállítást beépített beállításához. <br/>A következő készletek jelenleg támogatja:<br/>**EncoderNamedPreset.AdaptiveStreaming** (ajánlott). További információkért lásd: [skála automatikus generálásához](autogen-bitrate-ladder.md).<br/>**EncoderNamedPreset.AACGoodQualityAudio** -kódolású 192 Kb/s, csak sztereó hang tartalmazó egyetlen MP4-fájl eredményez.<br/>**EncoderNamedPreset.H264MultipleBitrate1080p** – 8 Képcsoporttal igazított MP4-fájlokat, és a 6000 KB/s 400 kb/s és sztereó AAC hang eredményez. Megoldás 1080p-nél kezdődik, és lefelé 360 p felbontású kerül.<br/>**EncoderNamedPreset.H264MultipleBitrate720p** -6 Képcsoporttal igazított MP4-fájlokat, és a 3400 kbps 400 kb/s és sztereó AAC hang eredményez. Feloldási 720 p elindul, és lefelé 360 p felbontású kerül.<br/>**EncoderNamedPreset.H264MultipleBitrateSD** – 5 Képcsoporttal igazított MP4-fájlokat, és a 1600 KB/s 400 kb/s és sztereó AAC hang eredményez. Megoldás 480p-nél kezdődik, és lefelé 360 p felbontású kerül.<br/><br/>További információkért lásd: [feltöltése, kódolása és fájlok folyamatos](stream-files-tutorial-with-api.md).|
|**StandardEncoderPreset**|Streamelés|Kódolás a bemeneti videó a standard szintű Encoder használt beállításokat ismerteti. <br/>Ezzel a készlet, átalakító készletek testreszabásához. További információkért lásd: [átalakító beállításkészletek testreszabása](customize-encoder-presets-how-to.md).|

## <a name="custom-presets"></a>Egyéni beállításkészletek

A Media Services teljes körűen támogatja az adott kódolási igények és követelmények készletek található értékek némelyike testreszabása. Használja a **StandardEncoderPreset** készlet, átalakító készletek testreszabásához. A részletes magyarázatokat és a példában: [kódoló beállításkészletek testreszabása](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>V3 a kódolás méretezése

Jelenleg a felhasználóknak kell az Azure portal vagy a Media Services v2 API-k használatával állítsa be a kérelemegység (leírtak szerint [médiafeldolgozás skálázás](../previous/media-services-scale-media-processing-overview.md). 

## <a name="next-steps"></a>További lépések

### <a name="tutorials"></a>Oktatóanyagok

A következő oktatóanyag bemutatja, hogyan kódolás a Media Services használatával:

* [Feltöltése, kódolása és streamelése a Media Services használatával](stream-files-tutorial-with-api.md)

### <a name="code-samples"></a>Kódminták

A következő Kódminták kódot tartalmaznak, amely bemutatja, hogyan kódolás a Media Services:

* [.NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore)
* [Azure CLI](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)

### <a name="sdks"></a>SDK-k

A következő támogatott Media Services v3 SDK-k valamelyikét használhatja a tartalmak kódolásához.

* [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [REST](https://docs.microsoft.com/rest/api/media/transforms)
* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices)
* [Python](https://docs.microsoft.com/python/api/overview/azure/media-services?view=azure-python)

