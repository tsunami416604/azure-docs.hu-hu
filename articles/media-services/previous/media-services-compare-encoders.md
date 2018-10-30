---
title: Az igény szerinti médiakódolók Azure összehasonlítása |} A Microsoft Docs
description: Ez a témakör összehasonlítja a kódolási képességeit **Media Encoder Standard** és **Media Encoder Premium munkafolyamat**.
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
ms.date: 10/24/2018
ms.author: juliako;anilmur
ms.openlocfilehash: c08759f4682c6010c2338ff7aaf61cda92eb0484
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50232087"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Az igény szerinti médiakódolók Azure összehasonlítása

Ez a témakör összehasonlítja a kódolási képességeit **Media Encoder Standard** és **Media Encoder Premium munkafolyamat**.

## <a name="video-and-audio-processing-capabilities"></a>Video- és feldolgozási képességek

Az alábbi táblázat összehasonlítja a funkciók között a Media Encoder Standard (MES) és a Media Encoder Premium munkafolyamat (mepw) használatával. 

|Képesség|Media Encoder Standard|Media Encoder Premium-munkafolyamat|
|---|---|---|
|Feltételes logika alkalmazására kódolás közben<br/>(például, ha a bemeneti HD, majd 5.1 hang kódolása)|Nem|Igen|
|Kódolt feliratok|Nem|[Igen](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® szakmai hangerő javítása](http://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> a párbeszéd Intelligence™|Nem|Igen|
|Vonja kötésre, más néven inverz filmátírási|Alapszintű|Szórási minősége|
|Észlelése és eltávolítása a fekete szegély <br/>(pillarboxes letterboxes)|Nem|Igen|
|Miniatűrkép-készítés|[Igen](media-services-dotnet-generate-thumbnail-with-mes.md)|[Igen](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Kivágás/vágás és videók vágása|[Igen](media-services-advanced-encoding-with-mes.md#trim_video)|Igen|
|Az audio- és hangátfedések|[Igen](media-services-advanced-encoding-with-mes.md#overlay)|[Igen](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Grafikai átfedések|Kép forrásokból|Képi és szöveges forrásokból|
|Több többnyelvű hang nyomon követi|Korlátozott|[Igen](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Számlázási egyes kódoló által használt mérőszám
| Médiafeldolgozó neve | Díjszabásról | Megjegyzések |
| --- | --- | --- |
| **Media Encoder Standard** |KÓDOLÓ |Kódolás, feladatok számítunk fel a teljes időtartam percben, kimenetként pedig az a megadott előállított médiafájlok alapján [Itt][1], KÓDOLÓ oszlop alatt. |
| **Media Encoder Premium-munkafolyamat** |PRÉMIUM SZINTŰ KÓDOLÓ |Kódolás, feladatok számítunk fel a teljes időtartam percben, kimenetként pedig az a megadott előállított médiafájlok alapján [Itt][1], a prémium szintű KÓDOLÓ oszlop alatt. |

## <a name="input-containerfile-formats"></a>Bemeneti tároló/fájlformátum
| Bemeneti tároló/fájlformátum | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| Adobe® Flash® F4V |Igen |Igen |
| MXF/SMPTE 377M |Igen |Igen |
| GXF |Igen |Igen |
| MPEG-2 Transport Streams |Igen |Igen |
| MPEG-2 Program Streamek |Igen |Igen |
| MPEG-4/MP4 |Igen |Igen |
| Windows Media/ASP |Igen |Igen |
| AVI (tömörítetlen 8 bites/10 bites) |Igen |Igen |
| 3GPP/3GPP2 |Igen |Nem |
| Smooth Streaming fájlformátum (PIFF 1.3) |Igen |Nem |
| [A Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Igen |Nem |
| Matroska vagy WebM |Igen |Nem |
| QuickTime (.mov) |Igen |Nem |

## <a name="input-video-codecs"></a>Bemeneti videókodekek
| Bemeneti Videókodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| AVC 8 bites/10 bites-, akár 4:2:2, ideértve az avcintrát is |8 bites 4:2:0. és 4:2:2 |Igen |
| Avid DNxHD (MXF-ben) |Igen |Igen |
| DVCPro/DVCProHD (MXF-ben) |Igen |Igen |
| JPEG2000 |Igen |Igen |
| MPEG-2 (akár a 422-es Profilig és a magas szintig; az XDCAM, XDCAM HD, XDCAM IMX, CableLabs® és D10 például) |Akár 422-es Profilig |Igen |
| MPEG-1 |Igen |Igen |
| Windows Media Video/VC-1 |Igen |Igen |
| Canopus HQ/HQX |Nem |Nem |
| MPEG-4: 2. rész |Igen |Nem |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Igen |Nem |
| Az Apple ProRes 422 |Igen |Nem |
| Az Apple ProRes 422 LT |Igen |Nem |
| Az Apple ProRes 422 HQ |Igen |Nem |
| Az Apple ProRes Proxy |Igen |Nem |
| Az Apple ProRes 4444 |Igen |Nem |
| Az Apple ProRes 4444 XQ |Igen |Nem |
| HEVC/H.265|Fő profil|Elsődleges és a 10-es főbb profil|

## <a name="input-audio-codecs"></a>Bemeneti hangkodekek
| Bemeneti hangkodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| AES (SMPTE 331 M és 302 M, AES3-2003) |Nem |Igen |
| Dolby® E |Nem |Igen |
| Dolby® digitális (AC3) |Nem |Igen |
| Dolby® digitális plusz (E-AC3) |Nem |Igen |
| AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1) |Igen |Igen |
| MPEG 2 réteg |Igen |Igen |
| MP3 (MPEG-1-3 hangréteg) |Igen |Igen |
| Windows Media hang |Igen |Igen |
| A PCM/WAV |Igen |Igen |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Igen |Nem |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Igen |Nem |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Igen |Nem |

## <a name="output-containerfile-formats"></a>Kimeneti tároló/fájlformátum
| Kimeneti tároló/fájlformátum | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| Adobe® Flash® F4V |Nem |Igen |
| MXF (OP1a, XDCAM és AS02) |Nem |Igen |
| DPP (beleértve a AS11) |Nem |Igen |
| GXF |Nem |Igen |
| MPEG-4/MP4 |Igen |Igen |
| MPEG-TS |Igen |Igen |
| Windows Media/ASP |Nem |Igen |
| AVI (tömörítetlen 8 bites/10 bites) |Nem |Igen |
| Smooth Streaming fájlformátum (PIFF 1.3) |Nem |Igen |

## <a name="output-video-codecs"></a>Kimeneti videókodekek
| Kimeneti Videókodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| AVC (H.264; 8 bites; akár nagy profilnál szintű 5.2-es; 4 K Ultranagy HD; AVC belüli) |Csak 8 bites 4:2:0 |Igen |
| HEVC (H.265; 8- és 10 bites;)  |Nem |Igen |
| Avid DNxHD (MXF-ben) |Nem |Igen |
| MPEG-2 (akár a 422-es Profilig és a magas szintig; az XDCAM, XDCAM HD, XDCAM IMX, CableLabs® és D10 például) |Nem |Igen |
| MPEG-1 |Nem |Igen |
| Windows Media Video/VC-1 |Nem |Igen |
| JPEG-miniatűr létrehozása |Igen |Igen |
| Miniatűrök létrehozása a PNG |Igen |Igen |
| BMP miniatűr létrehozása |Igen |Nem |

## <a name="output-audio-codecs"></a>Kimeneti hangkodekek
| Kimeneti hangkodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| AES (SMPTE 331 M és 302 M, AES3-2003) |Nem |Igen |
| Dolby® digitális (AC3) |Nem |Igen |
| Dolby® Digital Plus (E-AC3) legfeljebb 7.1 |Nem |Igen |
| AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1) |Igen |Igen |
| MPEG 2 réteg |Nem |Igen |
| MP3 (MPEG-1-3 hangréteg) |Nem |Igen |
| Windows Media hang |Nem |Igen |

>[!NOTE]
>Dolby® Digital (AC3) való kódol, ha a kimeneti csak az ISO MP4-fájlokat lehet írni.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Speciális kódolási feladatokat végez, és a Media Encoder Standard-beállításkészletek testreszabása](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
