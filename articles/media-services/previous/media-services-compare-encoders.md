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
ms.openlocfilehash: f42361df438a434548b3bc9394c007ef8d4c6eb0
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87038970"
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Az Azure igény szerinti adathordozó-kódolóinak összehasonlítása  

Ez a témakör a **Media Encoder standard** és **Media Encoder Premium workflow**kódolási képességeit hasonlítja össze.

## <a name="video-and-audio-processing-capabilities"></a>Videó-és hangfeldolgozási képességek

Az alábbi táblázat a Media Encoder Standard (MES) és a Media Encoder Premium Workflow (MEPW) közötti funkcionalitást hasonlítja össze. 

|Képesség|Media Encoder Standard|Media Encoder Premium-munkafolyamat|
|---|---|---|
|Feltételes logika alkalmazása kódolás közben<br/>(Ha például a bemenet HD, akkor kódolja a 5,1 hangot)|Nem|Yes|
|Kódolt feliratok|No|[Igen](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[A Dolby &reg; Professional-hangerő javítása](https://professional.dolby.com/product/broadcast/vm600/)<br/> a párbeszéd intelligenciával&trade;|Nem|Yes|
|Kiküszöbölhető, inverz telecine|Alapszintű|Szórási minőség|
|Fekete szegélyek észlelése és eltávolítása <br/>(pillarboxes, postafiókok)|Nem|Yes|
|Miniatűr létrehozása|[Igen](media-services-dotnet-generate-thumbnail-with-mes.md)|[Igen](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Videók levágása/vágása és összefűzése|[Igen](media-services-advanced-encoding-with-mes.md#trim_video)|Yes|
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
| 3GPP/3GPP2 |Yes |Nem |
| Smooth Streaming fájl formátuma (PIFF 1,3) |Yes |Nem |
| [Microsoft digitális videó rögzítése (DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) |Yes |Nem |
| Matroska/WebM |Yes |Nem |
| QuickTime (.mov) |Yes |Nem |

## <a name="input-video-codecs"></a>Bemeneti videó codec-je
| Bemeneti videókodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| 8 bites/10 bites AVC, akár 4:2:2-esig, ideértve az AVCIntrát is |8 bites 4:2:0-es és 4:2:2-es |Yes |
| Avid DNxHD (MXF-ben) |Igen |Igen |
| DVCPro/DVCProHD (MXF-ben) |Igen |Igen |
| JPEG2000 |Igen |Igen |
| MPEG-2 (akár 422-es profil és magas szintű, beleértve a XDCAM, a XDCAM HD, a XDCAM IMX, a CableLabs és a D10 változatokat &reg; ) |Akár 422-es profilig |Yes |
| MPEG-1 |Igen |Igen |
| Windows Media Video/VC-1 |Igen |Igen |
| Canopus HQ/HQX |Nem |Nem |
| MPEG-4 Part 2 |Yes |No |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Yes |No |
| Apple ProRes 422 |Yes |No |
| Apple ProRes 422 LT |Yes |No |
| Apple ProRes 422 HQ |Yes |No |
| Apple ProRes Proxy |Yes |No |
| Apple ProRes 4444 |Yes |No |
| Apple ProRes 4444 XQ |Yes |No |
| HEVC/H. 265|Fő profil|Fő és fő 10 profil|

## <a name="input-audio-codecs"></a>Bemeneti hangkodekek
| Bemeneti hangkodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| AES (SMPTE 331M és 302 M, AES3-2003) |No |Yes |
| Dolby &reg; E |No |Yes |
| Dolby &reg; Digital (AC3) |No |Yes |
| Dolby &reg; Digital Plus (E-AC3) |No |Yes |
| AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1-esig) |Igen |Igen |
| MPEG 2 réteg |Igen |Igen |
| MP3 (MPEG-1, 3 hangréteg) |Igen |Igen |
| Windows Media Audio |Igen |Igen |
| WAV/PCM |Igen |Igen |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Yes |No |
| [Opus](https://en.wikipedia.org/wiki/Opus_\(audio_format\)) |Yes |No |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Yes |No |

## <a name="output-containerfile-formats"></a>Kimeneti tároló/fájlformátumok
| Kimeneti tároló/fájlformátumok | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| Adobe &reg; Flash- &reg; F4V |No |Yes |
| MXF (OP1a, XDCAM és AS02) |No |Yes |
| DPP (beleértve a AS11-t) |Nem |Yes |
| GXF |Nem |Yes |
| MPEG-4/MP4 |Igen |Igen |
| MPEG – TS |Igen |Igen |
| Windows Media/ASF |Nem |Yes |
| AVI (tömörítetlen 8bit/10bit) |Nem |Yes |
| Smooth Streaming fájl formátuma (PIFF 1,3) |Nem |Yes |

## <a name="output-video-codecs"></a>Kimeneti videó codec-je
| Kimeneti videó codec-je | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| AVC (H. 264; 8 bites; legfeljebb magas profil, 5,2-as szint; 4K Ultra HD; AVC-n belüli) |Csak 8 bites 4:2:0 |Yes |
| HEVC (H. 265; 8 bites és 10 bites;)  |Nem |Yes |
| Avid DNxHD (MXF-ben) |Nem |Yes |
| MPEG-2 (akár 422-es profil és magas szintű, beleértve a XDCAM, a XDCAM HD, a XDCAM IMX, a CableLabs és a D10 változatokat &reg; ) |Nem |Yes |
| MPEG-1 |Nem |Yes |
| Windows Media Video/VC-1 |Nem |Yes |
| JPEG-miniatűr létrehozása |Igen |Igen |
| PNG-miniatűr létrehozása |Igen |Igen |
| A BMP-miniatűr létrehozása |Yes |Nem |

## <a name="output-audio-codecs"></a>Kimeneti hangkodekek
| Kimeneti hangkodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| AES (SMPTE 331M és 302 M, AES3-2003) |Nem |Yes |
| Dolby &reg; Digital (AC3) |Nem |Yes |
| Dolby &reg; Digital Plus (E-AC3) akár 7,1 |Nem |Yes |
| AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1-esig) |Igen |Igen |
| MPEG 2 réteg |Nem |Yes |
| MP3 (MPEG-1, 3 hangréteg) |Nem |Yes |
| Windows Media Audio |Nem |Yes |

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
