---
title: Azure on demand adathordozó-kódolók összehasonlítása | Microsoft Docs
description: Ez a témakör a **Media Encoder standard** és **Media Encoder Premium workflow**kódolási képességeit hasonlítja össze.
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
ms.openlocfilehash: ca17ffdc2a54b25bbfd855834a366ed381a54678
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86530388"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Az Azure igény szerinti adathordozó-kódolóinak összehasonlítása  

Ez a témakör a **Media Encoder standard** és **Media Encoder Premium workflow**kódolási képességeit hasonlítja össze.

## <a name="video-and-audio-processing-capabilities"></a>Videó-és hangfeldolgozási képességek

Az alábbi táblázat a Media Encoder Standard (MES) és a Media Encoder Premium Workflow (MEPW) közötti funkcionalitást hasonlítja össze. 

|Képesség|Media Encoder Standard|Media Encoder Premium-munkafolyamat|
|---|---|---|
|Feltételes logika alkalmazása kódolás közben<br/>(Ha például a bemenet HD, akkor kódolja a 5,1 hangot)|Nem|Igen|
|Kódolt feliratok|Nem|[Igen](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[A Dolby &reg; Professional-hangerő javítása](https://professional.dolby.com/product/broadcast/vm600/)<br/> a párbeszéd intelligenciával&trade;|Nem|Igen|
|Kiküszöbölhető, inverz telecine|Alapszintű|Szórási minőség|
|Fekete szegélyek észlelése és eltávolítása <br/>(pillarboxes, postafiókok)|Nem|Igen|
|Miniatűr létrehozása|[Igen](media-services-dotnet-generate-thumbnail-with-mes.md)|[Igen](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Videók levágása/vágása és összefűzése|[Igen](media-services-advanced-encoding-with-mes.md#trim_video)|Igen|
|Hang-vagy videó átfedései|[Igen](media-services-advanced-encoding-with-mes.md#overlay)|[Igen](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|Képek átfedései|Képforrásokból|Képekből és szöveges forrásokból|
|Több hang nyelvi nyomon követése|Korlátozott|[Igen](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a name="billing-meter-used-by-each-encoder"></a><a id="billing"></a>Az egyes kódolók által használt számlázási fogyasztásmérő
| Adathordozó-feldolgozó neve | Érvényes díjszabás | Jegyzetek |
| --- | --- | --- |
| **Media Encoder Standard** |ENCODER |A kódolási feladatokat a rendszer az [itt][1]megadott sebességgel az összes, a kimenetként létrehozott médiafájl teljes időtartama (percben) alapján, a kódoló oszlop alatt számítja fel. |
| **Media Encoder Premium-munkafolyamat** |PRÉMIUM SZINTŰ KÓDOLÓ |A kódolási feladatokat a rendszer a prémium szintű KÓDOLÓ oszlopban [lévő, a][1]kimenetként előállított médiafájlok teljes időtartama (percben) alapján számítja fel. |

## <a name="input-containerfile-formats"></a>Bemeneti tároló/fájlformátumok
| Bemeneti tároló/fájlformátum | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| Adobe &reg; Flash- &reg; F4V |Igen |Igen |
| MXF/SMPTE 377M |Igen |Igen |
| GXF |Igen |Igen |
| MPEG-2 átviteli streamek |Igen |Igen |
| MPEG-2 program streamek |Igen |Igen |
| MPEG-4/MP4 |Igen |Igen |
| Windows Media/ASF |Igen |Igen |
| AVI (tömörítetlen 8bit/10bit) |Igen |Igen |
| 3GPP/3GPP2 |Igen |Nem |
| Smooth Streaming fájl formátuma (PIFF 1,3) |Igen |Nem |
| [Microsoft digitális videó rögzítése (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Igen |Nem |
| Matroska/WebM |Igen |Nem |
| QuickTime (.mov) |Igen |Nem |

## <a name="input-video-codecs"></a>Bemeneti videó codec-je
| Bemeneti videókodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| 8 bites/10 bites AVC, akár 4:2:2-esig, ideértve az AVCIntrát is |8 bites 4:2:0-es és 4:2:2-es |Igen |
| Avid DNxHD (MXF-ben) |Igen |Igen |
| DVCPro/DVCProHD (MXF-ben) |Igen |Igen |
| JPEG2000 |Igen |Igen |
| MPEG-2 (akár 422-es profil és magas szintű, beleértve a XDCAM, a XDCAM HD, a XDCAM IMX, a CableLabs és a D10 változatokat &reg; ) |Akár 422-es profilig |Igen |
| MPEG-1 |Igen |Igen |
| Windows Media Video/VC-1 |Igen |Igen |
| Canopus HQ/HQX |Nem |Nem |
| MPEG-4 Part 2 |Igen |Nem |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Igen |Nem |
| Apple ProRes 422 |Igen |Nem |
| Apple ProRes 422 LT |Igen |Nem |
| Apple ProRes 422 HQ |Igen |Nem |
| Apple ProRes Proxy |Igen |Nem |
| Apple ProRes 4444 |Igen |Nem |
| Apple ProRes 4444 XQ |Igen |Nem |
| HEVC/H. 265|Fő profil|Fő és fő 10 profil|

## <a name="input-audio-codecs"></a>Bemeneti hangkodekek
| Bemeneti hangkodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| AES (SMPTE 331M és 302 M, AES3-2003) |Nem |Igen |
| Dolby &reg; E |Nem |Igen |
| Dolby &reg; Digital (AC3) |Nem |Igen |
| Dolby &reg; Digital Plus (E-AC3) |Nem |Igen |
| AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1-esig) |Igen |Igen |
| MPEG 2 réteg |Igen |Igen |
| MP3 (MPEG-1, 3 hangréteg) |Igen |Igen |
| Windows Media Audio |Igen |Igen |
| WAV/PCM |Igen |Igen |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Igen |Nem |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Igen |Nem |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Igen |Nem |

## <a name="output-containerfile-formats"></a>Kimeneti tároló/fájlformátumok
| Kimeneti tároló/fájlformátumok | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| Adobe &reg; Flash- &reg; F4V |Nem |Igen |
| MXF (OP1a, XDCAM és AS02) |Nem |Igen |
| DPP (beleértve a AS11-t) |Nem |Igen |
| GXF |Nem |Igen |
| MPEG-4/MP4 |Igen |Igen |
| MPEG – TS |Igen |Igen |
| Windows Media/ASF |Nem |Igen |
| AVI (tömörítetlen 8bit/10bit) |Nem |Igen |
| Smooth Streaming fájl formátuma (PIFF 1,3) |Nem |Igen |

## <a name="output-video-codecs"></a>Kimeneti videó codec-je
| Kimeneti videó codec-je | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| AVC (H. 264; 8 bites; legfeljebb magas profil, 5,2-as szint; 4K Ultra HD; AVC-n belüli) |Csak 8 bites 4:2:0 |Igen |
| HEVC (H. 265; 8 bites és 10 bites;)  |Nem |Igen |
| Avid DNxHD (MXF-ben) |Nem |Igen |
| MPEG-2 (akár 422-es profil és magas szintű, beleértve a XDCAM, a XDCAM HD, a XDCAM IMX, a CableLabs és a D10 változatokat &reg; ) |Nem |Igen |
| MPEG-1 |Nem |Igen |
| Windows Media Video/VC-1 |Nem |Igen |
| JPEG-miniatűr létrehozása |Igen |Igen |
| PNG-miniatűr létrehozása |Igen |Igen |
| A BMP-miniatűr létrehozása |Igen |Nem |

## <a name="output-audio-codecs"></a>Kimeneti hangkodekek
| Kimeneti hangkodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| AES (SMPTE 331M és 302 M, AES3-2003) |Nem |Igen |
| Dolby &reg; Digital (AC3) |Nem |Igen |
| Dolby &reg; Digital Plus (E-AC3) akár 7,1 |Nem |Igen |
| AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1-esig) |Igen |Igen |
| MPEG 2 réteg |Nem |Igen |
| MP3 (MPEG-1, 3 hangréteg) |Nem |Igen |
| Windows Media Audio |Nem |Igen |

>[!NOTE]
>Ha &reg; a Dolby Digital (AC3) kódolást végez, a kimenet csak ISO MP4-fájlba írható.

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Speciális kódolási feladatok végrehajtása a Media Encoder Standard-készletek testreszabásával](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: https://azure.microsoft.com/pricing/details/media-services/
