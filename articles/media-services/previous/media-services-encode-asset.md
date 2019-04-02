---
title: Áttekintése és összehasonlítása az Azure igény szerinti médiakódolók a |} A Microsoft Docs
description: Ez a témakör áttekintése és összehasonlítása az Azure igény szerinti médiakódolók a biztosít.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: juliako
ms.openlocfilehash: a976b7c1f697c09082ca0f7978bb23bb4e467e5d
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58801696"
---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Áttekintése és összehasonlítása az Azure igény szerinti médiakódolók a 

## <a name="encoding-overview"></a>Kódolási áttekintése

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Próbálja ki a legújabb verziót, ami a [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még [v3 a v2 migrálási útmutató](../latest/migrate-from-v2-to-v3.md)

Az Azure Media Services media felhőalapú kódolási több lehetőséget biztosít.

A Media Services kezdi, amikor fontos kodekeket és a fájl formátumok közötti különbségek megértése.
A kodekeket, amely megvalósítja a tömörítés és kibontás algoritmusok, mivel fájlformátumok olyan tárolók, amelyek a tömörített videó tartsa a szoftvert.

Media Services dinamikus csomagolást, amely lehetővé teszi az adaptív sávszélességű MP4 vagy Smooth Streaming-kódolású tartalmak streamformátumok valamelyikében (MPEG DASH, HLS, Smooth Streaming), a Media Services által támogatott biztosít anélkül, hogy kellene csomagolni ezekbe adatfolyam-továbbítási formátumokba.

Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

A Media Services az igény szerinti kódolók ebben a cikkben ismertetett a következőket támogatja:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Media Encoder Premium-munkafolyamat](media-services-encode-asset.md#media-encoder-premium-workflow)

Ez a cikk rövid áttekintést nyújt az igény szerinti médiakódolók, és hivatkozásokat tartalmaz a cikkek, amelyek segítségével a további tájékoztatáshoz. A témakör emellett a kódolók összehasonlítása.

Alapértelmezés szerint minden Media Services-fiók egy aktív kódolási feladat rendelkezhet egyszerre. Kódolási egységek, amelyek lehetővé teszik, hogy több kódolási feladat fut egyidejűleg, egy az egyes kódolási szolgáltatás számára fenntartott egység megvásárlása tartható fenn. További információ: [kódolási egységek méretezése](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard
### <a name="how-to-use"></a>A használat módja
[Kódolása a Media Encoder standarddel](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formátumok
[Formátumai és kodekei](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Előzetes beállítások
Media Encoder Standard használatával lett konfigurálva a kódoló készletek leírt egyik [Itt](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Bemeneti és kimeneti metaadatok
A kódolók bemeneti metaadatok leírt [Itt](media-services-input-metadata-schema.md).

A kódolók kimeneti metaadatok leírt [Itt](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Miniatűrök létrehozása
További információ: [Media Encoder Standard használatával miniatűrök létrehozása](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Trim videók (a vágás)
További információ: [azokat a videókat a Media Encoder Standard használatával hogyan](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Átfedések létrehozása
További információ: [hogyan hozhat létre Media Encoder Standard használatával átfedések](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Lásd még
[A Media Services blogja](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Media Encoder Premium-munkafolyamat
### <a name="overview"></a>Áttekintés
[Introducing Premium Encoding in Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>A használat módja
Media Encoder Premium munkafolyamat komplex munkafolyamatok használatával van konfigurálva. Munkafolyamat-fájlok sikerült létrehozni és frissíteni, használja a [munkafolyamat-tervezővel](media-services-workflow-designer.md) eszközt.

[Prémium szintű kódolás az Azure Media Services használatával](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Ismert problémák
Ha a bemeneti videó nem tartalmazza a feliratozás, a kimeneti adategység továbbra is fájlt fogja tartalmazni, egy üres TTML.


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Speciális kódolási feladatokat végez, és a Media Encoder Standard-beállításkészletek testreszabása](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
