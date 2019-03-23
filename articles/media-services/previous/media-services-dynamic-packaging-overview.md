---
title: Az Azure Media Services dinamikus becsomagolást áttekintő |} A Microsoft Docs
description: A témakör áttekintést nyújt a dinamikus csomagolás.
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
ms.date: 03/21/2019
ms.author: juliako
ms.openlocfilehash: e27b52c96f524f25aab18f45cf72c43884b7640d
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58350754"
---
# <a name="dynamic-packaging"></a>Dinamikus csomagolás

> [!div class="op_single_selector" title1="Select the version of Media Services that you are using:"]
> * [3-as verzió](../latest/dynamic-packaging-overview.md)
> * [2-es verzió](media-services-dynamic-packaging-overview.md)

A Microsoft Azure Media Services is használhatók, hogy sok adatforrás fájl médiaformátumok, adatfolyam-továbbítási formátumokba, media, és a content protection-ügyfél technológiák széles formátumok (például iOS, az XBOX, a Silverlight, a Windows 8). Ezek az ügyfelek ismerje meg a különböző protokollok, például az IOS-es szükséges egy HTTP Live Streaming (HLS) V4 formátumban, és a Silverlight és az Xbox igényelnek, Smooth Streaming. Ha rendelkezik egy adaptív sávszélességű (többszörös sávszélességű MP4) készletét MP4 (ISO alap 14496-12) médiafájlok vagy egy adaptív sávszélességű Smooth Streaming-fájlsorozattá kiszolgálása ügyfeleknek, hogy MPEG-DASH, HLS vagy Smooth Streaming ismertetése kívánt, érdemes előnyeit adathordozó A dinamikus csomagolás szolgáltatásokat.

A dinamikus csomagolás az összes, az kell, hogy hozzon létre egy objektumot, amely egy adaptív sávszélességű MP4-fájlokat vagy Smooth Streaming-fájlsorozattá készletét tartalmazza. Ezt követően a jegyzék vagy töredékrész kérelem, az Igényalapú Streamelési megadott formátumnak megfelelően kiszolgáló biztosítja, hogy Önnek a streamet a kiválasztott protokollal. Így elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services szolgáltatás elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ.

Az alábbi ábrán látható, a hagyományos kódolás és a statikus csomagolás munkafolyamat.

![Statikus kódolás](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Az alábbi ábrán látható, a dinamikus csomagolás munkafolyamat.

![A dinamikus kódolás](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Gyakori forgatókönyv

1. Töltse fel egy bemeneti fájlt (úgynevezett mezzanine-fájlt). Ha például H.264, MP4 vagy WMV (a támogatott formátumok listáját lásd: [a Media Encoder Standard által támogatott formátumok](media-services-media-encoder-standard-formats.md).
2. Kódolja a mezzanine-fájlt a H.264 MP4 adaptív sávszélességű csoportok.
3. Tegye közzé az adategységet, amely tartalmazza az adaptív sávszélességű MP4 típusú beállításkészlettel az On-Demand-kereső létrehozásával.
4. A streamelési URL-címek elérését, és a tartalmak streamelésére hozhat létre.

## <a name="preparing-assets-for-dynamic-streaming"></a>A dinamikus streamelési eszközök előkészítése

Az eszköz előkészítése dinamikus streaming, a következő lehetőségek állnak rendelkezésére:

- [Feltölthet egy fő fájlt](media-services-dotnet-upload-files.md).
- [A Media Encoder standard szintű encoder használatával adatkészletből H.264 MP4 adaptív sávszélességű hoznak létre](media-services-dotnet-encode-with-media-encoder-standard.md).
- [A tartalom Stream](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>A dinamikus becsomagolás által támogatott hangkodekek

A dinamikus csomagolás támogatja az MP4-fájlokat, amelyek tartalmazzák a hang a kódolt [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, v2 az AAC-HE), [Dolby digitális Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 vagy E-AC3), Dolby Atmos, vagy [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (A DTS Express, DTS LBR, DTS HD, veszteségmentes DTS HD). Dolby Atmos tartalom Streamelési szabványok, például az MPEG-DASH protokollal gyakori Streamelési formátum (CSF) vagy a közös Media alkalmazás formátum (CMAF) töredékes MP4 vagy via HTTP Live Streaming (HLS) rendelkező CMAF támogatott.

> [!NOTE]
> A dinamikus csomagolás nepodporuje tartalmazó fájlokat [Dolby digitális](https://en.wikipedia.org/wiki/Dolby_Digital) (nem örökölt kodekkel) (AC3) hang.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

