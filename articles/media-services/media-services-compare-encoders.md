---
title: "Az igény szerinti media kódolók Azure összehasonlítása |} Microsoft Docs"
description: "Ez a témakör összehasonlítja a kódolási képességeit ** Media Encoder Standard ** és ** Media Encoder prémium munkafolyamat **."
services: media-services
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.assetid: a79437c0-4832-423a-bca8-82632b2c47cc
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: juliako
ms.openlocfilehash: 3bba48d5425ae47c72aca893af7c11fe270c2929
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="comparison-of-azure-on-demand-media-encoders"></a>Az igény szerinti media kódolók Azure összehasonlítása

Ez a témakör összehasonlítja a kódolási képességeit **Media Encoder Standard** és **Media Encoder prémium munkafolyamat**.

## <a name="video-and-audio-processing-capabilities"></a>Video- és feldolgozási képességek

Az alábbi táblázat összehasonlítja a Media Encoder Standard (MES) és a Media Encoder prémium munkafolyamat (MEPW) között a funkció. 

|Képesség|Media Encoder Standard|Media Encoder Premium-munkafolyamat|
|---|---|---|
|Alkalmazza a feltételes logikához kódolás közben<br/>(például, ha a bemeneti HD, majd 5.1 hang kódolása)|Nem|Igen|
|Kódolt feliratok|Nem|[Igen](media-services-premium-workflow-encoder-formats.md#closed_captioning)|
|[Dolby® szakmai hangerő javítása](http://www.dolby.com/us/en/technologies/dolby-professional-loudness-solutions.pdf)<br/> a párbeszéd Intelligence™|Nem|Igen|
|Vonja kötésre, más néven inverz filmátírási|Basic|Szórási minősége|
|Észlelni és eltávolítani fekete szegélyek <br/>(pillarboxes, letterboxes)|Nem|Igen|
|A miniatűr létrehozása|[Igen](media-services-dotnet-generate-thumbnail-with-mes.md)|[Igen](media-services-media-encoder-premium-workflow-tutorials.md#thumbnails_to__multibitrate_MP4)|
|Kivágás/tisztítás és a videók varrással|[Igen](media-services-advanced-encoding-with-mes.md#trim_video)|Igen|
|Átfedések hang vagy videó|[Igen](media-services-advanced-encoding-with-mes.md#overlay)|[Igen](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-1--overlay-an-image-on-top-of-the-video)|
|A képek átfedések|Kép forrásokból|A képnek és szövegnek források|
|Több hang nyelvi nyomon követi|Korlátozott|[Igen](media-services-media-encoder-premium-workflow-multiplefilesinput.md#example-2--multiple-audio-language-encoding)|

## <a id="billing"></a>Minden egyes kódoló által használt számlázási mérő
| Adathordozó processzor neve | Adni | Megjegyzések |
| --- | --- | --- |
| **Media Encoder Standard** |KÓDOLÓ |Kódolás, feladatok és díjkötelesnek számít a teljes időtartam (percben), az előállított kimeneti, kamatláb médiafájlok alapján [Itt][1], a KÓDOLÓ oszlopban. |
| **Media Encoder Premium-munkafolyamat** |PRÉMIUM SZINTŰ KÓDOLÁS |Kódolás, feladatok és díjkötelesnek számít a teljes időtartam (percben), az előállított kimeneti, kamatláb médiafájlok alapján [Itt][1], a prémium szintű KÓDOLÁS oszlopban. |

## <a name="input-containerfile-formats"></a>Adjon meg tároló/fájlformátum
| Adjon meg tároló/fájlformátum | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| Adobe® Flash® F4V |Igen |Igen |
| MXF/SMPTE 377M |Igen |Igen |
| GXF |Igen |Igen |
| MPEG-2 Transport adatfolyamok |Igen |Igen |
| MPEG-2 Program adatfolyamok |Igen |Igen |
| MPEG-4/MP4 |Igen |Igen |
| Windows Media/ASP |Igen |Igen |
| AVI (tömörítetlen 8 bit/10 bit) |Igen |Igen |
| 3GPP/3GPP2 |Igen |Nem |
| Zökkenőmentes adatfolyam fájlformátum (PIFF 1.3) |Igen |Nem |
| [A Microsoft digitális videót Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) |Igen |Nem |
| Matroska/WebM |Igen |Nem |
| QuickTime (.mov) |Igen |Nem |

## <a name="input-video-codecs"></a>A bemeneti videó kodekek
| A bemeneti videó kodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| 8 bit/10-bites, legfeljebb 4 AVC: 2:2, beleértve a AVCIntra |8 bites 4:2:0. és 4:2:2. régiója |Igen |
| (A MXF) Avid DNxHD |Igen |Igen |
| DVCPro/DVCProHD (a MXF) |Igen |Igen |
| JPEG2000 |Igen |Igen |
| MPEG-2 (legfeljebb 422 profil és a magas szintű; például XDCAM, XDCAM HD, XDCAM IMX, CableLabs® és D10 Variant típusú adatok is beleértve) |Akár 422 profil |Igen |
| MPEG-1 |Igen |Igen |
| Windows Media videó/VC-1 |Igen |Igen |
| Canopus HQ/HQX |Nem |Nem |
| 2. rész MPEG-4 |Igen |Nem |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Igen |Nem |
| Apple ProRes 422 |Igen |Nem |
| Apple ProRes 422 LT |Igen |Nem |
| Apple ProRes 422 HQ |Igen |Nem |
| Apple ProRes Proxy |Igen |Nem |
| Apple ProRes 4444 |Igen |Nem |
| Apple ProRes 4444 XQ |Igen |Nem |

## <a name="input-audio-codecs"></a>Bemeneti hang kodekek
| Bemeneti hang kodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| AES (SMPTE 331 M és 302 M, AES3-2003) |Nem |Igen |
| E Dolby® |Nem |Igen |
| Dolby® digitális (AC3) |Nem |Igen |
| Dolby® digitális plusz (E-AC3) |Nem |Igen |
| AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1) |Igen |Igen |
| 2. réteg MPEG |Igen |Igen |
| MP3 (MPEG-1 hang réteg 3) |Igen |Igen |
| Windows Media hang |Igen |Igen |
| WAV/PCM |Igen |Igen |
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
| AVI (tömörítetlen 8 bit/10 bit) |Nem |Igen |
| Zökkenőmentes adatfolyam fájlformátum (PIFF 1.3) |Nem |Igen |

## <a name="output-video-codecs"></a>Kimeneti videó kodekek
| Kimeneti videó kodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| AVC (H.264; 8 bites; nagy profil, akár szinten 5.2-es; 4 KB-os Ultra HD; AVC belüli) |4. csak 8 bit: 2:0 |Igen |
| (A MXF) Avid DNxHD |Nem |Igen |
| MPEG-2 (legfeljebb 422 profil és a magas szintű; például XDCAM, XDCAM HD, XDCAM IMX, CableLabs® és D10 Variant típusú adatok is beleértve) |Nem |Igen |
| MPEG-1 |Nem |Igen |
| Windows Media videó/VC-1 |Nem |Igen |
| JPEG miniatűr létrehozása |Igen |Igen |
| PNG miniatűr létrehozása |Igen |Igen |
| BMP miniatűr létrehozása |Igen |Nem |

## <a name="output-audio-codecs"></a>Kimeneti hang kodekek
| Kimeneti hang kodekek | Media Encoder Standard | Media Encoder Premium-munkafolyamat |
| --- | --- | --- |
| AES (SMPTE 331 M és 302 M, AES3-2003) |Nem |Igen |
| Dolby® digitális (AC3) |Nem |Igen |
| Dolby® digitális Plus (E-AC3) legfeljebb 7.1 |Nem |Igen |
| AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1) |Igen |Igen |
| 2. réteg MPEG |Nem |Igen |
| MP3 (MPEG-1 hang réteg 3) |Nem |Igen |
| Windows Media hang |Nem |Igen |

>[!NOTE]
>Ha Ön a kódolás kimenete Dolby® digitális (AC3), a kimeneti csak írható ISO-MP4-fájlokat.

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="related-articles"></a>Kapcsolódó cikkek
* [Speciális feladatokra kódolási Media Encoder Standard készletek testreszabásával.](media-services-custom-mes-presets-with-dotnet.md)
* [Kvóták és korlátozások](media-services-quotas-and-limitations.md)

<!--Reference links in article-->
[1]: http://azure.microsoft.com/pricing/details/media-services/
