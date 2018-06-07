---
title: A felhőben az Azure Media Services kódolási |} Microsoft Docs
description: Ez a témakör kódolási folyamatát mutatja be, amikor az Azure Media Services használatával
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
ms.openlocfilehash: e1c7536c59b110ae3dd753ff5f4b01195f8dadca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655165"
---
# <a name="encoding-with-azure-media-services"></a>Az Azure Media Services kódolási

Az Azure Media Services lehetővé teszi a kiváló minőségű digitális médiafájlok kódolja a böngészők és az eszközök széles lejátszható formátumokba. Például érdemes a tartalom Apple HLS vagy MPEG DASH formázza az adatfolyamhoz. A Media Services lehetővé teszi a video- vagy tartalom elemzése. Ez a témakör nyújtanak útmutatást a tartalmaknak a Media Services v3 kódolással.

A Media Services v3 kódolására, létrehozásához szükséges átalakító és egy feladatot. Átalakítás a kódolási beállításait és a kimeneti módszereivel határozza meg, és a folyamat egy példányát a módszereivel. További információkért lásd: [átalakítja és feladatai](transform-concept.md)

Amikor az Azure Media Services kódolási funkciójához készletek használatával kérje meg a kódoló a bemeneti médiafájljait feldolgozásának módja. Például megadhatja képernyőfelbontást és/vagy a kívánt hang csatornák számát a kódolt tartalomban. 

Ismerkedés gyorsan ágazatban kialakult bevált gyakorlaton alapuló ajánlott a beépített beállítások egyike, vagy választhatja azt is, az egyedi, amelyekre az adott forgatókönyv vagy az eszköz igények szerint készletek létrehozásához. 

A kódoló adatait találja a [OpenAPI Specification](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2018-03-30-preview). 

## <a name="built-in-presets"></a>Beépített készletek

A Media Services jelenleg a következő beépített kódolási beállítások támogatja:  

|**Készlet neve**|**Forgatókönyv**|**Részletek**|
|---|---|---|
|**AudioAnalyzerPreset**|Hang elemzése|Az előre definiált az Eszközintelligencia-alapú elemzése műveleteket, köztük a beszédfelismerés írjanak elő egy előre definiált készletet alkalmaz. Az előre definiált jelenleg egyetlen hang nyomon a tartalom feldolgozása.<br/>A hang tartalom nyelvi adhat meg a bemeneti "címke-régió nyelvi" BCP-47 formátumban (például "hu-hu"). A támogatott nyelvek listája, "en-US", "en-GB", "es-ES", "es MX", fr-FR, it-IT, ja-JP, pt-BR, zh-CN.|
|**VideoAnalyzerPreset**|Hang- és elemzése|Elemzések (gazdag metaadatai) a hang- és videofunkcióinak, és adja kimenetként, egy JSON formátumú fájlba. Megadhatja, hogy csak kiolvasni kívánt hang insights videofájl feldolgozása közben. További információkért lásd: [elemzés videó](analyze-videos-tutorial-with-api.md).|
|**BuiltInStandardEncoderPreset**|Streamelés|A bemeneti videó az adás kódolási beállításkészlet beépített használt. <br/>A következő készletek jelenleg támogatottak:<br/>**EncoderNamedPreset.AdaptiveStreaming** (ajánlott). További információkért lásd: [automatikus előállítása érdekében egy sávszélességű létra](autogen-bitrate-ladder.md).<br/>**EncoderNamedPreset.AACGoodQualityAudio** -kódolású 192 Kbit/s, csak sztereó hang tartalmazó egyetlen MP4 fájlt hoz létre.<br/>**EncoderNamedPreset.H264MultipleBitrate1080p** – létrejön egy 8 GOP igazított MP4-fájlok 400 kbit/s és sztereó AAC hang 6000 kbps kezdve. Megoldás 1080p kezdődik, és le 360 p kerül.<br/>**EncoderNamedPreset.H264MultipleBitrate720p** – létrejön egy 6 GOP igazított MP4-fájlok 400 kbit/s és sztereó AAC hang 3400 kbps kezdve. Megoldás 720 p elindul, és le 360 p kerül.<br/>**EncoderNamedPreset.H264MultipleBitrateSD** – létrejön egy 5 GOP igazított MP4-fájlok 400 kbit/s és sztereó AAC hang 1600 kbps kezdve. Megoldás 480p kezdődik, és le 360 p kerül.<br/><br/>További információkért lásd: [Uploading, kódolási és adatfolyam-fájlok](stream-files-tutorial-with-api.md).|
|**StandardEncoderPreset**|Streamelés|Standard Encoder bemeneti videó kódolás esetén használandó beállításokat ismerteti. <br/>Ezzel a beállított átalakító készletek testreszabásához. További információkért lásd: [átalakító készletek testreszabása](customize-encoder-presets-how-to.md).|

## <a name="custom-presets"></a>Egyéni készletek

A Media Services teljes mértékben támogatja az adott kódolási igények és követelményei beállításkészletekkel szereplő összes érték testreszabása. Használja a **StandardEncoderPreset** beállított átalakító készletek testreszabásához. A részletes magyarázatokat és példa, lásd: [kódoló készletek testreszabása](customize-encoder-presets-how-to.md).

## <a name="scaling-encoding-in-v3"></a>Kódolás a v3 skálázás

Jelenleg a felhasználóknak kell RUs beállításához használja az Azure-portálon vagy az AMS v2 API-k (a [media feldolgozási skálázás](../previous/media-services-scale-media-processing-overview.md). 

## <a name="next-steps"></a>További lépések

### <a name="tutorials"></a>Oktatóanyagok

A következő tutorals a tartalmaknak a Media Services kódolással megjelenítése:

* [Töltse fel, kódolásához és streameléséhez Azure Media Services használatával](stream-files-tutorial-with-api.md)
* [Az Azure Media Services videók elemzése](analyze-videos-tutorial-with-api.md)

### <a name="code-samples"></a>Kódminták

A következő mintakódok kódot tartalmaznak, amely bemutatja, hogyan kódolása a Media Services:

* [.NET Core](https://github.com/Azure-Samples/media-services-v3-dotnet-core-tutorials/tree/master/NETCore)
* [CLI 2.0](https://github.com/Azure/azure-docs-cli-python-samples/tree/master/media-services)

### <a name="sdks"></a>SDK-k

A tartalom kódolása a következő támogatott Media Services v3 SDK-k bármelyikét használhatja.

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
* [REST](https://docs.microsoft.com/rest/api/media/transforms)
* [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)
* [Java](https://docs.microsoft.com/java/api/overview/azure/mediaservices)
* [Python](https://docs.microsoft.com/python/api/overview/azure/media-services?view=azure-python)

