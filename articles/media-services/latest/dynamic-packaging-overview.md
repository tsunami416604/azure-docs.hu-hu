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
ms.date: 02/19/2019
ms.author: juliako
ms.openlocfilehash: d1d07402bca5f01cf63d0b039c085e46bb0f0d62
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447922"
---
# <a name="dynamic-packaging"></a>Dinamikus csomagolás

A Microsoft Azure Media Services is használható, hogy sok adatforrás fájl médiaformátumok, adatfolyam-továbbítási formátumokba, media, és a content protection formátumok számos különböző ügyfél-technológiák (például iOS- és XBOX). Ezek az ügyfelek különböző protokollok ismertetése, például iOS-HTTP Live Streaming (HLS) formátumban és Xbox igényelnek, Smooth Streaming igényel-e. Ha rendelkezik egy adaptív sávszélességű (többszörös sávszélességű MP4) készletét MP4 (ISO alap 14496-12) médiafájlok vagy egy adaptív sávszélességű Smooth Streaming-fájlsorozattá szolgálja ki, hogy ismerje a HLS, MPEG DASH és Smooth Streaming-ügyfelek kívánt, érdemes előnyeit adathordozó A dinamikus csomagolás szolgáltatásokat.

A dinamikus csomagolás használatával szüksége, hozzon létre egy objektumot az adaptív sávszélességű MP4-fájlokat tartalmazó. Ezt követően a jegyzék vagy töredékrész kérelem, az Igényalapú Streamelési megadott formátumnak megfelelően kiszolgáló biztosítja, hogy Önnek a streamet a kiválasztott protokollal. Így elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services szolgáltatás elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ.

Az alábbi ábrán látható, a hagyományos kódolás és a statikus csomagolás munkafolyamat.

![Statikus kódolás](./media/dynamic-packaging-overview/media-services-static-packaging.png)

Az alábbi ábrán látható, a dinamikus csomagolás munkafolyamat.

![A dinamikus kódolás](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="dynamic-packaging-workflow"></a>A dinamikus csomagolás munkafolyamat

1. Töltse fel egy bemeneti fájlt (úgynevezett mezzanine-fájlt). Ha például H.264, MP4 vagy WMV (a támogatott formátumok listáját lásd: [a Media Encoder Standard által támogatott formátumok](media-encoder-standard-formats.md).
2. Kódolja a mezzanine-fájlt a H.264 MP4 adaptív sávszélességű csoportok.
3. Tegye közzé az adategységet, amely tartalmazza az adaptív sávszélességű MP4 típusú beállításkészlettel.
4. A streamelési URL-címek elérését, és a tartalmak streamelésére hozhat létre.

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>A dinamikus becsomagolás által támogatott hangkodekek

A dinamikus csomagolás támogatja az MP4-fájlokat tartalmazó hang a kódolt [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 az AAC-HE), [Dolby digitális Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 vagy E-AC3), vagy [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, veszteségmentes DTS HD).

> [!NOTE]
> A dinamikus csomagolás nepodporuje tartalmazó fájlokat [Dolby digitális](https://en.wikipedia.org/wiki/Dolby_Digital) (nem örökölt kodekkel) (AC3) hang.

## <a name="next-steps"></a>További lépések

[Feltöltés, kódolás, stream-videók](stream-files-tutorial-with-api.md)
