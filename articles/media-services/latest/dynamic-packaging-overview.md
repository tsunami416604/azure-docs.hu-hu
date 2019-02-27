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
ms.date: 02/25/2019
ms.author: juliako
ms.openlocfilehash: f20a242fc5f674738cde6af7d2797205f8298514
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56871315"
---
# <a name="dynamic-packaging"></a>Dinamikus csomagolás

A Microsoft Azure Media Services is használható, hogy sok adatforrás fájl médiaformátumok, adatfolyam-továbbítási formátumokba, media, és a content protection formátumok számos különböző ügyfél-technológiák (például iOS- és XBOX). Ezek az ügyfelek különböző protokollok ismertetése, például iOS-HTTP Live Streaming (HLS) formátumban és Xbox igényelnek, Smooth Streaming igényel-e. Ha rendelkezik egy adaptív sávszélességű (többszörös sávszélességű MP4) készletét MP4 (ISO alap 14496-12) médiafájlok vagy egy adaptív sávszélességű Smooth Streaming-fájlsorozattá szolgálja ki, hogy ismerje a HLS, MPEG DASH és Smooth Streaming-ügyfelek kívánt, igénybe veheti a dinamikus Csomag. A csomagolási képernyőfelbontást független, SD/HD/UHD - 4K támogatottak.

[Streamvégpontok](streaming-endpoint-concept.md) a médiatartalmak eredményjelző ügyfél lejátszók Media Services dinamikus csomagolási szolgáltatás. A dinamikus csomagolás funkciója, amely az összes Streamelési végpontokkal (Standard vagy prémium szintű) standard származik. Részeként van ennek a funkciónak a Media Services v3 kapcsolódó költségek. A dinamikus csomagolás használatával szüksége az Alkalmazásjegyzék-fájl az adaptív sávszélességű MP4-fájlokat tartalmazó objektumot. Ezt követően a jegyzék vagy töredékrész kérelemben megadott formátumnak megfelelően, Önnek a streamet a kiválasztott protokollal. Így elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services szolgáltatás elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ.

A Media Services szolgáltatásban a dinamikus csomagolás vannak streamelési igény szerinti vagy élő szolgál.

Az alábbi ábrán látható, a dinamikus csomagolás munkafolyamat igényalapú streameléshez.

![A dinamikus kódolás](./media/dynamic-packaging-overview/media-services-dynamic-packaging.svg)

## <a name="common-video-on-demand-workflow"></a>Közös videó igény szerinti munkafolyamat

Az alábbiakban látható egy közös Media Services adatfolyam-továbbítási munkafolyamat, a dinamikus csomagolás szolgál.

1. Töltse fel egy bemeneti fájlt (úgynevezett mezzanine-fájlt). Ha például H.264, MP4 vagy WMV (a támogatott formátumok listáját lásd: [a Media Encoder Standard által támogatott formátumok](media-encoder-standard-formats.md).
2. Kódolja a mezzanine-fájlt a H.264 MP4 adaptív sávszélességű csoportok.
3. Tegye közzé az adategységet, amely tartalmazza az adaptív sávszélességű MP4 típusú beállításkészlettel.
4. Hozhat létre, amelyek különböző formátumokban (HLS, Dash és Smooth Streaming) cél URL-címeket. A folyamatos átviteli végponton lenne gondoskodik a megfelelő jegyzékfájlban és a kérelmek ezeket különböző formátumokban kiszolgáló. Példa:

    - HLS: `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`
    - Vonal: `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)`
    - Zökkenőmentes: `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`

## <a name="video-codecs-supported-by-dynamic-packaging"></a>A dinamikus becsomagolás által támogatott videókodekek

Támogatja a dinamikus csomagolás MP4-fájlokat, amelyek tartalmazzák a videó kódolású [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC vagy AVC1), [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 vagy hvc1).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>A dinamikus becsomagolás által támogatott hangkodekek

A dinamikus csomagolás támogatja az MP4-fájlokat, amelyek tartalmazzák a hang a kódolt [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 az AAC-HE), [Dolby digitális Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 vagy E-AC3), vagy [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, veszteségmentes DTS HD).

> [!NOTE]
> A dinamikus csomagolás nepodporuje tartalmazó fájlokat [Dolby digitális](https://en.wikipedia.org/wiki/Dolby_Digital) (nem örökölt kodekkel) (AC3) hang.

## <a name="next-steps"></a>További lépések

[Feltöltés, kódolás, stream-videók](stream-files-tutorial-with-api.md)

