---
title: "Media Encoder prémium szintű munkafolyamat-formátumok és kodekek |} Microsoft Docs"
description: "Ez a témakör áttekintést nyújt a Media Encoder prémium munkafolyamat formátumok formátumok és kodekek"
services: media-services
documentationcenter: 
author: juliako
manager: erik43
editor: 
ms.assetid: b197fce8-3b9b-4189-8d08-486810c0426f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: juliako;anilmur
ms.openlocfilehash: eb1cadec240dc7f6e3ac5b8932d66c3d55c76e42
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2018
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Media Encoder prémium szintű munkafolyamat-formátumok és kodekek
> [!NOTE]
> Prémium szintű kódolás kérdéseket tesz fel, e-mailben mepd@microsoft.com.
> 
> A jelen témakörben bemutatott Media Encoder prémium munkafolyamat media processzor Kínában nem érhető el. 
> 
> 

Ez a dokumentum a bemeneti és kimeneti fájlformátumokat és a nyilvános előzetes verziója által támogatott kodekek listáját tartalmazza a **Media Encoder prémium munkafolyamat** kódoló.

[Media Encoder prémium Worflow bemeneti formátumok és kodekek](#input_formats)

[Media Encoder prémium Worflow kimeneti formátumok és kodekek](#output_formats)

**Media Encoder prémium munkafolyamat** támogatja a kódolt feliratok a leírt [ez](#closed_captioning) szakasz. 

## <a id="input_formats"></a>Media Encoder prémium munkafolyamat bemeneti formátumok és kodekek
A következő szakasz a kodekeket és fájlformátumot, amely a media processzor támogatja a bemeneti adatként.

### <a name="input-containerfile-formats"></a>Adjon meg tároló/fájlformátum
* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* MPEG-2 Transport adatfolyamok
* MPEG-2 Program adatfolyamok
* MPEG-4/MP4
* Windows Media/ASF
* AVI (tömörítetlen 8 bit/10 bit)

### <a name="input-video-codecs"></a>A bemeneti videó kodekek
* 8 bit/10-bites, legfeljebb 4 AVC: 2:2, beleértve a AVCIntra
* (A MXF) Avid DNxHD
* DVCPro/DVCProHD (a MXF)
* JPEG2000
* MPEG-2 (legfeljebb 422 profil és a magas szintű; például XDCAM, XDCAM HD, XDCAM IMX, CableLabs® és D10 Variant típusú adatok is beleértve)
* MPEG-1
* Windows Media videó/VC-1

### <a name="input-audio-codecs"></a>Bemeneti hang kodekek
* AES (SMPTE 331 M és 302 M, AES3-2003)
* Dolby® E
* Dolby® digitális (AC3)
* AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1)
* 2. réteg MPEG
* MP3 (MPEG-1 hang réteg 3)
* Windows Media hang
* WAV/PCM

## <a id="output_format"></a>Media Encoder prémium munkafolyamat kimeneti formátumok és kodekek
A következő szakasz a kodekeket és fájlformátumot, a media processzor kimeneteként támogatott.

### <a name="output-containerfile-formats"></a>Kimeneti tároló/fájlformátum
* Adobe® Flash® F4V
* MXF (OP1a, XDCAM és AS02)
* DPP (beleértve a AS11)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (tömörítetlen 8 bit/10 bit)
* Zökkenőmentes adatfolyam fájlformátum (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Kimeneti videó kodekek
* AVC (H.264; 8 bites; nagy profil, akár szinten 5.2-es; 4 KB-os Ultra HD; AVC belüli)
* (A MXF) Avid DNxHD
* DVCPro/DVCProHD (a MXF)
* MPEG-2 (legfeljebb 422 profil és a magas szintű; például XDCAM, XDCAM HD, XDCAM IMX, CableLabs® és D10 Variant típusú adatok is beleértve)
* MPEG-1
* Windows Media videó/VC-1
* JPEG miniatűr létrehozása

### <a name="output-audio-codecs"></a>Kimeneti hang kodekek
* AES (SMPTE 331 M és 302 M, AES3-2003)
* Dolby® digitális (AC3)
* Dolby® digitális Plus (E-AC3) legfeljebb 7.1
* AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1)
* 2. réteg MPEG
* MP3 (MPEG-1 hang réteg 3)
* Windows Media hang

>[!NOTE]
>Ha Ön a kódolás kimenete Dolby® digitális (AC3), a kimeneti csak írható ISO-MP4-fájlokat.

## <a id="closed_captioning"></a>Feliratok támogatása
A betöltési, **Media Encoder prémium munkafolyamat** támogatja:

1. SCC fájlok
2. Fájlok SMPTE-TT
3. CEA-608/CEA-708 – felhasználói adatként (SEI üzenetek H.264 elemi adatfolyam, ATSC/53, SCTE20) vagy sor MXF/GXF fájlok kiegészítő adatok
4. STL alcíme fájlok

A kimenetet az alábbi beállítások érhetők el:

1. CEA-608 való CEA-708 fordítás
2. CEA-608/CEA-708 továbbítása (SEI üzenetek H.264 elemi adatfolyam beágyazva, vagy végzik a kiegészítő adatok MXF-fájlokban szereplő)
3. SCC
4. SMPTE túllépte az időkorlátot szöveg (a forrás CEA-608 / SMPTE RP2052; DFXP fájl létrehozása is beleértve)
5. SRT alcíme fájl
6. DVB alcíme adatfolyamok

Megjegyzés: nem a fenti formátumokban használható összes keresztül Azure Media Services adatfolyamként történő kézbesítéshez.

## <a name="known-issues"></a>Ismert problémák
Ha a bemeneti videó nem tartalmaz kódolt a kimeneti eszköz továbbra is tartalmazni fog egy üres TTML fájlt. 

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

