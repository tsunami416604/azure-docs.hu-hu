---
title: "Az Azure Media Services dinamikus becsomagolás áttekintése |} Microsoft Docs"
description: "A témakör által biztosított és a dinamikus becsomagolás áttekintése."
author: Juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 0d9e4f54-5daa-45c1-bfaa-cf09ca89b812
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 2d212599302fced3f60085ab30cdeaefc1ee2e6a
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="dynamic-packaging"></a>Dinamikus csomagolás
## <a name="overview"></a>Áttekintés
Microsoft Azure Media Services segítségével kézbesítése sok media forrás fájlformátumokat, media adatfolyam-továbbítási formátumokba, és a tartalomvédelem formátumokat számos különböző ügyfél technológiák (például iOS, XBOX, a Silverlight, a Windows 8). Ezek az ügyfelek ismertetése különböző protokollok, például iOS igényel-e egy HTTP-Live Streaming (HLS) V4 formátum, és a Silverlight- és Xbox igényelnek, Smooth Streaming. Ha rendelkezik egy adaptív sávszélességű (többféle sávszélességű) készletét MP4 (ISO talál médiafájlok 14496-12) vagy egy adaptív sávszélességű Smooth Streaming-fájlsorozattá osztja ki az ügyfelek, MPEG DASH, HLS vagy Smooth Streaming megértéséhez kívánt, a Media Services dinamikus becsomagolás kell venni.

A dinamikus csomagolás szükség, ha egy eszköz, amely tartalmazza az adaptív sávszélességű MP4-vagy adaptív sávszélességű Smooth Streaming-fájlsorozattá. Ezt követően a jegyzék vagy töredék kérelem, az Igényalapú Streamelési megadott formátumnak megfelelően kiszolgáló biztosítja az adatfolyam kapni a kiválasztott protokollal. Így elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services szolgáltatás elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ.

Az alábbi ábrán látható, a hagyományos kódolás és a statikus csomagolás munkafolyamat.

![Statikus kódolás](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Az alábbi ábrán látható, a dinamikus becsomagolás munkafolyamat.

![Dinamikus kódolás](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)


## <a name="common-scenario"></a>Egy gyakori alaphelyzete
1. Töltse fel a bemeneti fájl (néven mezzazine-fájlt). Például H.264, MP4 vagy WMV (a támogatott formátumok listája: [formátumokat támogatja a Media Encoder Standard](media-services-media-encoder-standard-formats.md).
2. Kódolja a mezzanine fájl H.264 MP4 adaptív sávszélességű részhalmazához.
3. Tegye közzé az adategységet, amely tartalmazza az adaptív sávszélességű MP4 típusú beállításkészlettel az On-Demand-kereső létrehozásával.
4. Az adatfolyam-továbbítási URL-címek elérését, és a tartalmak felépítéséhez.

## <a name="preparing-assets-for-dynamic-streaming"></a>Dinamikus streaming eszközök előkészítése
Az eszköz előkészítése dinamikus streaming két lehetőség közül választhat:

1. [Töltse fel a fő fájlt](media-services-dotnet-upload-files.md).
2. [Használja a Media Encoder Standard encoder H.264 MP4 adaptív sávszélességű készlet feladatvégrehajtás](media-services-dotnet-encode-with-media-encoder-standard.md).
3. [A tartalmak](media-services-deliver-content-overview.md).

## <a id="unsupported_formats"></a>Dinamikus becsomagolás által nem támogatott
A következő forrás formátumok nem támogatja a dinamikus csomagolás.

* Dolby digitális mp4-fájlokat.
* Dolby digitális zökkenőmentes fájlokat.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

