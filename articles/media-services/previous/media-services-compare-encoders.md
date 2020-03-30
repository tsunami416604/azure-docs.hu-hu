---
title: Az Igény szerinti Azure-médiakódolók összehasonlítása | Microsoft dokumentumok
description: Ez a témakör a Media **Encoder Standard** és a **Media Encoder Premium Workflow**kódolási képességeit hasonlítja össze.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 4767f7bb5ba02c838c0e21721e55a6564a14acd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "69016659"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Az Igény szerinti Azure-médiakódolók összehasonlítása  

Ez a témakör a Media **Encoder Standard** és a **Media Encoder Premium Workflow**kódolási képességeit hasonlítja össze.

## <a name="video-and-audio-processing-capabilities"></a>Video- és hangfeldolgozási lehetőségek

Az alábbi táblázat a Media Encoder Standard (MES) és a Media Encoder Premium Workflow (MEPW) funkcióit hasonlítja össze. 

|Képesség|Media Encoder Standard|Media Encoder Premium-munkafolyamat|
|---|---|---|
|Feltételes logika alkalmazása kódolás közben<br/>(például, ha a bemenet HD, akkor kódolni 5.1 audio)|Nem|Igen|
|Feliratozás|Nem|[Igen](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® Professzionális hangosság korrekciója](https://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> a Párbeszéd Hírszerzésével™|Nem|Igen|
|De-interlacing, inverz telecine|Basic|Közvetítés minősége|
|Fekete szegélyek észlelése és eltávolítása <br/>(pillarboxok, postafiókok)|Nem|Igen|
|Miniatűr létrehozása|[Igen](media-services-dotnet-generate-thumbnail-with-mes.md)|[Igen](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Videók vágása/vágása és varrása|[Igen](media-services-advanced-encoding-with-mes.md#trim_video)|Igen|
|Hang- vagy videoátfedések|[Igen](media-services-advanced-encoding-with-mes.md#overlay)|[Igen](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|A grafikák átfedései|Képforrásokból|Kép- és szövegforrásokból|
|Több hangnyelvi sáv|Korlátozott|[Igen](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a name="billing-meter-used-by-each-encoder"></a><a id="billing"></a>Az egyes kódolók által használt számlázási mérő
| Médiaprocesszor neve | Alkalmazandó árképzés | Megjegyzések |
| --- | --- | --- |
| **Media Encoder Standard** |Kódoló |A kódolási feladatok at a kimenetként előállított összes médiafájl teljes időtartama alapján számítjuk fel percben, az [itt][1]megadott sebességgel, a KÓDOLÓ oszlopban. |
| **Media Encoder Premium-munkafolyamat** |PRÉMIUM KÓDOLÓ |A kódolási feladatok a kimenetként előállított összes médiafájl teljes időtartama alapján, percben, az [itt][1]megadott sebességgel, a PREMIUM KÓDOLÁSi oszlopban kerülnek felszámolásra. |

## <a name="input-containerfile-formats"></a>Beviteli tároló/fájlformátumok
| Bemeneti tároló/fájlformátum | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| Adobe® Flash® F4V |Igen |Igen |
| MXF/SMPTE 377m |Igen |Igen |
| GXF |Igen |Igen |
| MPEG-2 átviteli adatfolyamok |Igen |Igen |
| MPEG-2 programadatfolyamok |Igen |Igen |
| MPEG-4/MP4 |Igen |Igen |
| Windows Media/ASF |Igen |Igen |
| AVI (tömörítetlen 8bit/10bit) |Igen |Igen |
| 3GPP/3GPP2 |Igen |Nem |
| Zökkenőmentes streamelési fájlformátum (PIFF 1.3) |Igen |Nem |
| [Microsoft digitális videofelvétel (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Igen |Nem |
| Matroska/WebM |Igen |Nem |
| QuickTime (.mov) |Igen |Nem |

## <a name="input-video-codecs"></a>Videokodekek bevitele
| Bemeneti videókodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| 8 bites/10 bites AVC, akár 4:2:2-esig, ideértve az AVCIntrát is |8 bites 4:2:0-es és 4:2:2-es |Igen |
| Avid DNxHD (MXF-ben) |Igen |Igen |
| DVCPro/DVCProHD (MXF-ben) |Igen |Igen |
| JPEG2000 |Igen |Igen |
| MPEG-2 (422 profilig és magas szintűig; beleértve az olyan változatokat, mint az XDCAM, XDCAM HD, XDCAM IMX, CableLabs® és D10) |Akár 422-es profilig |Igen |
| MPEG-1 |Igen |Igen |
| Windows Media Videó/VC-1 |Igen |Igen |
| Canopus HQ/HQX |Nem |Nem |
| MPEG-4 Part 2 |Igen |Nem |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Igen |Nem |
| Apple ProRes 422 |Igen |Nem |
| Apple ProRes 422 LT |Igen |Nem |
| Apple ProRes 422 HQ |Igen |Nem |
| Apple ProRes Proxy |Igen |Nem |
| Apple ProRes 4444 |Igen |Nem |
| Apple ProRes 4444 XQ |Igen |Nem |
| HEVC/H.265|Fő profil|Fő és Fő 10 Profil|

## <a name="input-audio-codecs"></a>Hangkodekek bevitele
| Bemeneti hangkodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| AES (SMPTE 331M és 302 M, AES3-2003) |Nem |Igen |
| Dolby® E |Nem |Igen |
| Dolby® Digital (AC3) |Nem |Igen |
| Dolby® Digital (E-AC3) |Nem |Igen |
| AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1-esig) |Igen |Igen |
| MPEG 2 réteg |Igen |Igen |
| MP3 (MPEG-1, 3 hangréteg) |Igen |Igen |
| Windows Media Audio |Igen |Igen |
| WAV/PCM |Igen |Igen |
| [Flac](https://en.wikipedia.org/wiki/FLAC)</a> |Igen |Nem |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Igen |Nem |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Igen |Nem |

## <a name="output-containerfile-formats"></a>Kimeneti tároló/fájlformátumok
| Kimeneti tároló/fájlformátumok | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| Adobe® Flash® F4V |Nem |Igen |
| MXF (OP1a, XDCAM és AS02) |Nem |Igen |
| DPP (beleértve az AS11-et) |Nem |Igen |
| GXF |Nem |Igen |
| MPEG-4/MP4 |Igen |Igen |
| MPEG-TS |Igen |Igen |
| Windows Media/ASF |Nem |Igen |
| AVI (tömörítetlen 8bit/10bit) |Nem |Igen |
| Zökkenőmentes streamelési fájlformátum (PIFF 1.3) |Nem |Igen |

## <a name="output-video-codecs"></a>Kimeneti videokodekek
| Kimeneti videó kodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| AVC (H.264; 8 bites; nagy profilú, 5.2-es szintű; 4K Ultra HD; AVC Intra) |Csak 8 bites 04:2:0 |Igen |
| HEVC (H.265; 8 és 10 bites;)  |Nem |Igen |
| Avid DNxHD (MXF-ben) |Nem |Igen |
| MPEG-2 (422 profilig és magas szintűig; beleértve az olyan változatokat, mint az XDCAM, XDCAM HD, XDCAM IMX, CableLabs® és D10) |Nem |Igen |
| MPEG-1 |Nem |Igen |
| Windows Media Videó/VC-1 |Nem |Igen |
| JPEG bélyegkép létrehozása |Igen |Igen |
| PNG-miniatűr létrehozása |Igen |Igen |
| BMP miniatűr létrehozása |Igen |Nem |

## <a name="output-audio-codecs"></a>Kimeneti hangkodekek
| Kimeneti hangkodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| AES (SMPTE 331M és 302 M, AES3-2003) |Nem |Igen |
| Dolby® Digital (AC3) |Nem |Igen |
| Dolby® Digital Plus (E-AC3) akár 7,1 |Nem |Igen |
| AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1-esig) |Igen |Igen |
| MPEG 2 réteg |Nem |Igen |
| MP3 (MPEG-1, 3 hangréteg) |Nem |Igen |
| Windows Media Audio |Nem |Igen |

>[!NOTE]
>Ha dolby® Digital (AC3) kódolásra kódol, a kimenet csak ISO MP4 fájlba írható.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Speciális kódolási feladatok végrehajtása a Media Encoder Standard készletek testreszabásával](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
