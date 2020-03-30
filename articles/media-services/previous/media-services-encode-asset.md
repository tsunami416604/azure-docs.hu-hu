---
title: Az Azure igény szerinti médiakódolóinak áttekintése | Microsoft dokumentumok
description: Az Azure Media Services több lehetőséget biztosít a felhőben lévő médiakódoláshoz. Ez a cikk áttekintést nyújt az Azure igény szerinti médiakódolóiról.
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
ms.date: 06/25/2019
ms.author: juliako
ms.openlocfilehash: d5d5b8a7328ee82e94d494795617832cb0258667
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251101"
---
# <a name="overview-of-azure-on-demand-media-encoders"></a>Az Igény szerinti Azure-médiakódolók áttekintése 

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

Az Azure Media Services több lehetőséget biztosít a felhőben lévő médiakódoláshoz.

A Media Services szolgáltatással való indításkor fontos megérteni a kodekek és a fájlformátumok közötti különbséget.
Kodekek a szoftver, amely végrehajtja a tömörítési / kicsomagolási algoritmusok, míg a fájlformátumok tárolók, hogy tartsa a tömörített videó.

A Media Services dinamikus csomagolást biztosít, amely lehetővé teszi az adaptív sávszélességű MP4 vagy Smooth Streaming kódolású tartalom kézbesítését a Media Services által támogatott streamelési formátumokban (MPEG DASH, HLS, Smooth Streaming), anélkül, hogy újra be kellene csomagolnia ezeket a streamelési formátumokat.

Amikor a Media Services-fiók jön létre egy **alapértelmezett** streamelési végpont ot a fiók **leállított** állapotban. A tartalom streamelésének megkezdéséhez, valamint a dinamikus csomagolás és a dinamikus titkosítás kihasználásához a tartalomstreameléshez használt streamvégpontnak **Fut** állapotban kell lennie. A streamelési végpontok számlázása akkor történik, amikor a végpont **futó** állapotban van.

A Media Services a jelen cikkben ismertetett alábbi igény szerinti kódolókat támogatja:

* [Media Encoder Standard](media-services-encode-asset.md#media-encoder-standard)
* [Media Encoder Premium-munkafolyamat](media-services-encode-asset.md#media-encoder-premium-workflow)

Ez a cikk rövid áttekintést nyújt az igény szerinti médiakódolókról, és részletesebb információkat tartalmazó cikkekre mutató hivatkozásokat tartalmaz. A témakör a kódolók összehasonlítását is tartalmazza.

Alapértelmezés szerint minden Media Services-fiókegyszerre egy aktív kódolási feladatot használhat. Lefoglalhatja azokat a kódolási egységeket, amelyek lehetővé teszik, hogy egyszerre több kódolási feladat fusson, egyet-egyet minden megvásárolt kódolási fenntartott egységhez. További információt a [Kódolási egységek méretezése című témakörben talál.](media-services-scale-media-processing-overview.md)

## <a name="media-encoder-standard"></a>Media Encoder Standard

### <a name="how-to-use"></a>A használat módja
[Kódolás a Media Encoder Standard segítségével](media-services-dotnet-encode-with-media-encoder-standard.md)

### <a name="formats"></a>Formátumok
[Formátumok és kodekek](media-services-media-encoder-standard-formats.md)

### <a name="presets"></a>Készletek
A Media Encoder Standard az [itt](https://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409)leírt kódolókészletek egyikével van konfigurálva.

### <a name="input-and-output-metadata"></a>Bemeneti és kimeneti metaadatok
A kódolók bemeneti metaadatait [itt](media-services-input-metadata-schema.md)ismerteti.

A kódolók kimeneti metaadatait [itt](media-services-output-metadata-schema.md)ismerteti.

### <a name="generate-thumbnails"></a>Miniatűrök létrehozása
További információt a [Bélyegképek létrehozása a Media Encoder Standard használatával című témakörben talál.](media-services-advanced-encoding-with-mes.md#thumbnails)

### <a name="trim-videos-clipping"></a>Videók vágása (vágás)
További információt a [Videók vágása a Media Encoder Standard használatával című témakörben talál.](media-services-advanced-encoding-with-mes.md#trim_video)

### <a name="create-overlays"></a>Fedvények létrehozása
További információt az [Átfedések létrehozása a Media Encoder Standard használatával című témakörben talál.](media-services-advanced-encoding-with-mes.md#overlay)

### <a name="see-also"></a>Lásd még
[A Media Services blogja](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/)

## <a name="media-encoder-premium-workflow"></a>Media Encoder Premium-munkafolyamat
### <a name="overview"></a>Áttekintés
[Prémium kódolás bemutatása az Azure Media Services szolgáltatásban](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/)

### <a name="how-to-use"></a>A használat módja
A Media Encoder Premium Workflow összetett munkafolyamatokkal van konfigurálva. A munkafolyamat-fájlok a [Munkafolyamat-tervező](media-services-workflow-designer.md) eszközzel hozhatók létre és frissíthetők.

[Prémium kódolás használata az Azure Media Servicesszolgáltatásban](https://azure.microsoft.com/blog/2015/03/06/how-to-use-premium-encoding-in-azure-media-services/)

### <a name="known-issues"></a>Ismert problémák
Ha a bemeneti videó nem tartalmaz feliratozást, a kimeneti eszköz továbbra is tartalmaz egy üres TTML-fájlt.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Speciális kódolási feladatok végrehajtása a Media Encoder Standard készletek testreszabásával](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
