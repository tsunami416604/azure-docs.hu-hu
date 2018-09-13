---
title: A felhőben az Azure Media Services Encoding |} A Microsoft Docs
description: Ez a témakör ismerteti a kódolási folyamatot az Azure Media Services használata közben
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/21/2018
ms.author: juliako
ms.openlocfilehash: 69c5516ee503d774b143bb2d83f09ea863a00b31
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35927539"
---
# <a name="encoding-with-azure-media-services"></a>Az Azure Media Services Encoding

Az Azure Media Services lehetővé teszi, hogy az kiváló minőségű médiafájlt kódolandó, hogy a böngészők és eszközök széles lejátszhatók. Például előfordulhat, hogy az Apple HLS vagy MPEG DASH formátumában szeretné streamelni a tartalmakat. A Media Services emellett lehetővé teszi a video- vagy tartalmak elemzését. Ez a témakör ad útmutatást tartalmait a Media Services v3 kódolással.

Kódolás a Media Services v3-as, hozzon létre egy-egy átalakítási és a egy feladatot kell. Egy-egy átalakítási határozza meg a recept, a kódolási beállítások és kimenetek, és a feladat a recept egy példányát. További információkért lásd: [átalakítások és feladatok](transform-concept.md)

Az Azure Media Services encoding, ha a szolgáltatás használatával ossza meg a kódoló a bemeneti fájlok feldolgozásának módja. Például megadhatja a videó felbontást és/vagy a kívánt hang csatornák száma kódolt tartalmában. 

Ismerkedhet meg gyorsan az ágazatban kialakult bevált gyakorlaton alapuló ajánlott a beépített beállítások egyikét, vagy dönthet úgy, amelyekre az adott forgatókönyv vagy eszközkövetelmények beállított egyéni. 

A kódoló az információkat talál a [OpenAPI-specifikáció](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2018-03-30-preview). 

## <a name="built-in-presets"></a>Beépített készletek

A Media Services jelenleg a következő beépített kódolási beállításokat támogatja:  

|**Készlet neve**|**Forgatókönyv**|**Részletek**|
|---|---|---|
|**AudioAnalyzerPreset**|Hang elemzése|A készlet egy előre meghatározott készletével AI-alapú elemzési műveleteket, köztük a lejegyzés vonatkozik. A készlet jelenleg egyetlen hangsávra tartalom feldolgozását támogatja.<br/>A hang hasznos nyelvét a bemeneti adatok a "nyelvi címke-régió" BCP-47 formátumban is megadhat (például "hu-hu"). Támogatott nyelvek listáját is, "en-US", "en-GB", "es-ES", "es-MX", "fr-FR", it-IT, ja-JP, pt-BR, zh-CN.|
|**VideoAnalyzerPreset**|Audio- és elemzése|Insights (bőséges metaadatok) kigyűjti a hang- és video, és kiírja egy JSON-formátumú fájlt. Megadhatja, hogy csak szeretné hang információk kinyerése érdekében videofájl feldolgozásakor. További információkért lásd: [elemzés videó](analyze-videos-tutorial-with-api.md).|
|**BuiltInStandardEncoderPreset**|Streamelés|Használja a bemeneti videó a standard szintű Encoder kódolási előbeállítást beépített beállításához. <br/>A következő készletek jelenleg támogatja:<br/>**EncoderNamedPreset.AdaptiveStreaming** (ajánlott). További információkért lásd: [skála automatikus generálásához](autogen-bitrate-ladder.md).<br/>**EncoderNamedPreset.AACGoodQualityAudio** -kódolású 192 Kb/s, csak sztereó hang tartalmazó egyetlen MP4-fájl eredményez.<br/>**EncoderNamedPreset.H264MultipleBitrate1080p** – 8 Képcsoporttal igazított MP4-fájlokat, és a 6000 KB/s 400 kb/s és sztereó AAC hang eredményez. Megoldás 1080p-nél kezdődik, és lefelé 360 p felbontású kerül.<br/>**EncoderNamedPreset.H264MultipleBitrate720p** -6 Képcsoporttal igazított MP4-fájlokat, és a 3400 kbps 400 kb/s és sztereó AAC hang eredményez. Feloldási 720 p elindul, és lefelé 360 p felbontású kerül.<br/>**EncoderNamedPreset.H264MultipleBitrateSD** – 5 Képcsoporttal igazított MP4-fájlokat, és a 1600 KB/s 400 kb/s és sztereó AAC hang eredményez. Megoldás 480p-nél kezdődik, és lefelé 360 p felbontású kerül.<br/><br/>További információkért lásd: [feltöltése, kódolása és fájlok folyamatos](stream-files-tutorial-with-api.md).|
|**StandardEncoderPreset**|Streamelés|Kódolás a bemeneti videó a standard szintű Encoder használt beállításokat ismerteti. <br/>Ezzel a készlet, átalakító készletek testreszabásához. További információkért lásd: [átalakító beállításkészletek testreszabása](customize-encoder-presets-how-to.md).|

## <a name="custom-presets"></a>Egyéni beállításkészletek

A Media Services teljes körűen támogatja az adott kódolási igények és követelmények készletek található értékek némelyike testreszabása. Használja a **StandardEncoderPreset** készlet, átalakító készletek testreszabásához. A részletes magyarázatokat és a példában: [kódoló beállításkészletek testreszabása](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>V3 a kódolás méretezése

Jelenleg a felhasználóknak kell az Azure portal vagy az AMS v2 API-k használatával állítsa be a kérelemegység (leírtak szerint [médiafeldolgozás skálázás](../previous/media-services-scale-media-processing-overview.md). 

## <a name="next-steps"></a>További lépések

### <a name="tutorials"></a>Oktatóanyagok

A következő tutorals bemutatják, hogyan kódolás a Media Services használatával:

* [Feltöltése, kódolása és streamelése az Azure Media Services használatával](stream-files-tutorial-with-api.md)
* [Az Azure Media Services videók elemzése](analyze-videos-tutorial-with-api.md)

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

