---
title: Az Azure Media Services dinamikus becsomagolást áttekintő |} A Microsoft Docs
description: A témakör áttekintést nyújt a dinamikus csomagolás, a Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/17/2019
ms.author: juliako
ms.openlocfilehash: 21b2d9bdef5e6974126c78b1005bd4786182c881
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56405721"
---
# <a name="dynamic-packaging"></a>Dinamikus csomagolás

A Microsoft Azure Media Services is használhatók, hogy sok adatforrás fájl médiaformátumok, adatfolyam-továbbítási formátumokba, media, és a content protection-ügyfél technológiák széles formátumok (például iOS, az XBOX, a Silverlight, a Windows 8). Ezek az ügyfelek ismerje meg a különböző protokollok, például az IOS-es szükséges egy HTTP Live Streaming (HLS) V4 formátumban, és a Silverlight és az Xbox igényelnek, Smooth Streaming. Ha rendelkezik egy adaptív sávszélességű (többszörös sávszélességű MP4) készletét MP4 (ISO alap 14496-12) médiafájlok vagy egy adaptív sávszélességű Smooth Streaming-fájlsorozattá kiszolgálása ügyfeleknek, hogy MPEG-DASH, HLS vagy Smooth Streaming ismertetése kívánt, érdemes előnyeit adathordozó A dinamikus csomagolás szolgáltatásokat.

A dinamikus csomagolás az összes, az kell, hogy hozzon létre egy objektumot, amely egy adaptív sávszélességű MP4-fájlokat vagy Smooth Streaming-fájlsorozattá készletét tartalmazza. Ezt követően a jegyzék vagy töredékrész kérelem, az Igényalapú Streamelési megadott formátumnak megfelelően kiszolgáló biztosítja, hogy Önnek a streamet a kiválasztott protokollal. Így elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services szolgáltatás elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ.

Az alábbi ábrán látható, a hagyományos kódolás és a statikus csomagolás munkafolyamat.

![Statikus kódolás](./media/dynamic-packaging-overview/media-services-static-packaging.png)

Az alábbi ábrán látható, a dinamikus csomagolás munkafolyamat.

![A dinamikus kódolás](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Gyakori forgatókönyv

1. Töltse fel egy bemeneti fájlt (úgynevezett mezzanine-fájlt). Ha például H.264, MP4 vagy WMV (a támogatott formátumok listáját lásd: [a Media Encoder Standard által támogatott formátumok](media-encoder-standard-formats.md).
2. Kódolja a mezzanine-fájlt a H.264 MP4 adaptív sávszélességű csoportok.
3. Tegye közzé az adategységet, amely tartalmazza az adaptív sávszélességű MP4 típusú beállításkészlettel.
4. A streamelési URL-címek elérését, és a tartalmak streamelésére hozhat létre.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>A dinamikus becsomagolás által támogatott hangkodekek

A dinamikus csomagolás MP4-fájlok (vagy Smooth Streaming-fájlok) kódolású hangot tartalmazó támogatja. a [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 az AAC-HE), [Dolby digitális Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 vagy E-AC3), vagy [ DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, veszteségmentes DTS HD).

> [!Note]
> A dinamikus csomagolás nepodporuje tartalmazó fájlokat [Dolby digitális](https://en.wikipedia.org/wiki/Dolby_Digital) (nem örökölt kodekkel) (AC3) hang.

## <a name="next-steps"></a>További lépések

[Feltöltés, kódolás, stream-videók](stream-files-tutorial-with-api.md)
