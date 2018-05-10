---
title: Áttekintés és az igény szerinti media kódolók Azure összehasonlítása |} Microsoft Docs
description: Ez a témakör minden áttekintése és az igény szerinti media kódolók Azure összehasonlítása.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: e6bfc068-fa46-4d68-b1ce-9092c8f3a3c9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2017
ms.author: juliako
ms.openlocfilehash: 6e44cff930f943f5cf30fb5335ad6a7fd1d08ab5
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="overview-and-comparison-of-azure-on-demand-media-encoders"></a>Áttekintés és az igény szerinti media kódolók Azure összehasonlítása
## <a name="encoding-overview"></a>Kódolási áttekintése
Az Azure Media Services a felhőben media kódolás több lehetőséget biztosít.

A Media Services kezdte meg, fontos kodekeket és a fájl formátumok közötti különbségek megértése.
A kodekeket a szoftver, amely megvalósítja a tömörítési/kitömörítés algoritmusok, mivel fájlformátumok tárolói tartsa a tömörített videó.

Media Services dinamikus becsomagolást, amely lehetővé teszi, hogy az adaptív sávszélességű MP4 vagy Smooth Streaming-kódolású tartalmak (MPEG DASH, HLS, Smooth Streaming), a Media Services által támogatott streamformátumok biztosít anélkül, hogy át kellene őket csomagolni ezekbe a streamformátumokba.

>[!NOTE]
>Az AMS-fiók létrehozásakor a rendszer hozzáad egy **alapértelmezett** streamvégpontot a fiókhoz **Leállítva** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. Előnyeit [dinamikus becsomagolás](media-services-dynamic-packaging-overview.md), végre kell hajtani a következőket:
>
>Emellett kódolja a forrásfájlt adaptív sávszélességű MP4-fájlok vagy az adaptív sávszélességű Smooth Streaming-fájlsorozattá (a kódolás lépéseit egy az oktatóanyag későbbi részében).

A Media Services a következő igény szerinti kódolók ebben a cikkben ismertetett támogatja:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Media Encoder Premium-munkafolyamat](media-services-encode-asset.md#media-encoder-premium-workflow)

Ez a cikk rövid áttekintést nyújt az igény szerinti media kódolók, és hivatkozásokat ad a cikket, amely részletesebb információkkal. A témakör is tartalmaz a kódolók összehasonlítása.

>[!NOTE]
>Alapértelmezés szerint minden Media Services-fiók lehet aktív kódolási feladat is egyszerre. Kódolási egység, amely lehetővé teszi több kódolási feladat fut egyidejűleg, egy minden egyes megvásárolt kódolási fenntartott egységnek a foglalhat. További információ: [kódolási egységek méretezése](media-services-scale-media-processing-overview.md).

## <a name="media-encoder-standard"></a>Media Encoder Standard
### <a name="how-to-use"></a>A használat módja
[A Media Encoder Standard kódolással](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formázza
[Formátumok és kodekek](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Készletek
Media Encoder Standard segítségével konfigurálható: az egyik a kódoló készletek leírt [Itt](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409).

### <a name="input-and-output-metadata"></a>Bemeneti és kimeneti metaadatok
A bemeneti kódolók metaadatok leírását [Itt](media-services-input-metadata-schema.md).

A kódolók kimeneti metaadatok leírását [Itt](media-services-output-metadata-schema.md).

### <a name="generate-thumbnails"></a>Indexképének létrehozására
További információ: [létrehozásával Media Encoder Standard használatával miniatűrök](media-services-advanced-encoding-with-mes.md#thumbnails).

### <a name="trim-videos-clipping"></a>Trim (vágás) videók
További információ: [hogyan lehet levágni a Media Encoder Standard használatával videók](media-services-advanced-encoding-with-mes.md#trim_video).

### <a name="create-overlays"></a>Átfedések létrehozása
További információ: [létrehozása a Media Encoder Standard használatával átfedések](media-services-advanced-encoding-with-mes.md#overlay).

### <a name="see-also"></a>Lásd még
[A Media Services blog](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Media Encoder Premium-munkafolyamat
### <a name="overview"></a>Áttekintés
[Prémium szintű kódolás az Azure Media Services bemutatása](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>A használat módja
Media Encoder prémium munkafolyamat bonyolult munkafolyamatok segítségével lett konfigurálva. Munkafolyamat-fájlok létrehozása sikerült, és a frissítve a [munkafolyamat-Tervező](media-services-workflow-designer.md) eszköz.

[Prémium szintű kódolás az Azure Media Services használatával](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Ismert problémák
Ha a bemeneti videó nem tartalmaz kódolt a kimeneti eszköz továbbra is tartalmazni fog egy üres TTML fájlt.


## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Speciális feladatokra kódolási Media Encoder Standard készletek testreszabásával.](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
