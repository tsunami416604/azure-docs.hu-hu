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
ms.date: 10/30/2018
ms.author: juliako
ms.openlocfilehash: 6695e17ec3dd9bf9ebeeb44b34e9688d3e5a1625
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249747"
---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Áttekintése és összehasonlítása az Azure igény szerinti médiakódolók a
## <a name="encoding-overview"></a>Kódolási áttekintése
Az Azure Media Services media felhőalapú kódolási több lehetőséget biztosít.

A Media Services kezdi, amikor fontos kodekeket és a fájl formátumok közötti különbségek megértése.
A kodekeket, amely megvalósítja a tömörítés és kibontás algoritmusok, mivel fájlformátumok olyan tárolók, amelyek a tömörített videó tartsa a szoftvert.

Media Services dinamikus csomagolást, amely lehetővé teszi az adaptív sávszélességű MP4 vagy Smooth Streaming-kódolású tartalmak streamformátumok valamelyikében (MPEG DASH, HLS, Smooth Streaming), a Media Services által támogatott biztosít anélkül, hogy kellene csomagolni ezekbe adatfolyam-továbbítási formátumokba.

> [!NOTE]
> Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. 

A Media Services az igény szerinti kódolók ebben a cikkben ismertetett a következőket támogatja:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Media Encoder Premium-munkafolyamat](media-services-encode-asset.md#media-encoder-premium-workflow)

Ez a cikk rövid áttekintést nyújt az igény szerinti médiakódolók, és hivatkozásokat tartalmaz a cikkek, amelyek segítségével a további tájékoztatáshoz. A témakör emellett a kódolók összehasonlítása.

>[!NOTE]
>Alapértelmezés szerint minden Media Services-fiók egy aktív kódolási feladat rendelkezhet egyszerre. Kódolási egységek, amelyek lehetővé teszik, hogy több kódolási feladat fut egyidejűleg, egy az egyes kódolási szolgáltatás számára fenntartott egység megvásárlása tartható fenn. További információ: [kódolási egységek méretezése](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard
### <a name="how-to-use"></a>A használat módja
[Kódolása a Media Encoder standarddel](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formátumok
[Formátumai és kodekei](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Készletek
Media Encoder Standard használatával lett konfigurálva a kódoló készletek leírt egyik [Itt](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

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
[1]: http://azure.microsoft.com/pricing/details/media-services/
