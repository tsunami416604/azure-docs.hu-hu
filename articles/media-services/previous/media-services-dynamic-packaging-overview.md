---
title: Az Azure Media Services dinamikus csomagolásának áttekintése | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt a Microsoft Azure Media Services dinamikus csomagolásáról.
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
ms.openlocfilehash: 079094965775c140c0343da98e40fd008995d45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74901187"
---
# <a name="dynamic-packaging"></a>Dinamikus csomagolás

> [!div class="op_single_selector" title1="Válassza ki a Media Services használt verzióját:"]
> * [3-as verzió](../latest/dynamic-packaging-overview.md)
> * [2-es verzió](media-services-dynamic-packaging-overview.md)

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Nézze meg a legújabb verziót, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Lásd még: [migrálási útmutató a v2-től a v3-ig](../latest/migrate-from-v2-to-v3.md)

A Microsoft Azure Media Services számos médiaforrás-fájlformátum, médiaadatfolyam-adatfolyam-formátum és tartalomvédelmi formátum szállítására használható számos ügyféltechnológiához (például iOS, XBOX, Silverlight, Windows 8). Ezek az ügyfelek különböző protokollokat értenek, például az iOS-hez HTTP Live Streaming (HLS) V4 formátum szükséges, a Silverlight és az Xbox pedig sima streamelést igényel. Ha adaptív sávszélességű (többsávszélességű) MP4 (ISO Base Media 14496-12) fájlokkal vagy olyan adaptív sávszélességű Smooth Streaming fájlokkal rendelkezik, amelyeket az MPEG DASH, HLS vagy Smooth Streaming szolgáltatást megértő ügyfelek számára szeretne kiszolgálni, Szolgáltatások dinamikus csomagolás.

A dinamikus csomagolás minden, meg kell, hogy hozzon létre egy eszköz, amely tartalmazza egy sor adaptív sávszélességű MP4-fájlok vagy adaptív sávszélességű Smooth Streaming fájlokat. Ezután a jegyzékfájl ban vagy a töredékkérelemben megadott formátum alapján az igény szerinti streamelési kiszolgáló biztosítja, hogy a kiválasztott protokollban megkapja az adatfolyamot. Így elég egyetlen tárolási formátumban tárolni a fájlokat (és kifizetni a tárhelyüket), a Media Services szolgáltatás elkészíti és kiszolgálja az ügyféltől érkező kérésnek megfelelő választ.

Az alábbi ábra a hagyományos kódolási és statikus csomagolási munkafolyamatot mutatja be.

![Statikus kódolás](./media/media-services-dynamic-packaging-overview/media-services-static-packaging.png)

Az alábbi ábra a dinamikus csomagolási munkafolyamatot mutatja be.

![Dinamikus kódolás](./media/media-services-dynamic-packaging-overview/media-services-dynamic-packaging.png)

## <a name="common-scenario"></a>Gyakori forgatókönyv

1. Töltsön fel egy bemeneti fájlt (úgynevezett félemeleten). Például H.264, MP4 vagy WMV (a támogatott formátumok listájáért lásd: [A Media Encoder Standard által támogatott formátumok](media-services-media-encoder-standard-formats.md).
2. A mezzanine fájlt H.264 MP4 adaptív sávszélességű bitráta-készletekbe kódolhatja.
3. Tegye közzé az adaptív sávszélességű MP4-értéket tartalmazó eszközt az igény szerinti lokátor létrehozásával.
4. A streamelési URL-címeket a tartalom eléréséhez és streameléséhez hozhatja létre.

## <a name="preparing-assets-for-dynamic-streaming"></a>Eszközök előkészítése dinamikus streamelésre

Az eszköz dinamikus streamelésre való előkészítéséhez a következő lehetőségek közül választhat:

- [Főfájl feltöltése](media-services-dotnet-upload-files.md).
- [H.264 MP4 adaptív sávszélességű átviteli sávszélesség-kódolókészletek létrehozásához használja a Media Encoder Standard kódolót.](media-services-dotnet-encode-with-media-encoder-standard.md)
- [A tartalom streamelése](media-services-deliver-content-overview.md).

## <a name="audio-codecs-supported-by-dynamic-packaging"></a>Dinamikus csomagolással támogatott hangkodekek

A Dynamic Packaging támogatja az MP4 fájlokat, amelyek [AAC-vel](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus)(Enhanced AC-3 vagy E-AC3), Dolby Atmos vagy [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD Lossless) kódolva tartalmaznak hangot. A Dolby Atmos-tartalom streamelése olyan szabványok esetében támogatott, mint az MPEG-DASH protokoll, amely vagy a Common Streaming Format (CSF) vagy a Common Media Application Format (CMAF) töredezett MP4, valamint http Live Streaming (HLS) protokollon keresztül a CMAF-fel.

> [!NOTE]
> A Dynamic Packaging nem támogatja a [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) hangot tartalmazó fájlokat (ez egy örökölt kodek).

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

