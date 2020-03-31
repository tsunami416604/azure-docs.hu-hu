---
title: Media Encoder Prémium munkafolyamat-formátumok és kodekek | Microsoft dokumentumok
description: Ez a témakör áttekintést nyújt a Media Encoder Premium workflow formátumok formátumok és kodekek
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 87cd7c63939331190530a46071a6b4c40480562f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269756"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Media Encoder Prémium munkafolyamat-formátumok és kodekek

> [!NOTE]
> A témakörben tárgyalt Media Encoder Premium Workflow médiaprocesszor kínában nem érhető el. 

Ez a dokumentum a **Media Encoder Premium Workflow** kódoló nyilvános előzetes verziójával támogatott bemeneti és kimeneti fájlformátumok és kodekek listáját tartalmazza.

[Media Encoder Prémium munkafolyamat bemeneti formátumok és kodekek](#input_formats)

Media Encoder Prémium munkafolyamat kimeneti formátumok és kodekek

**A Media Encoder Premium Workflow** támogatja az [ebben](#closed_captioning) a szakaszban ismertetett feliratozást. 

## <a name="media-encoder-premium-workflow-input-formats-and-codecs"></a><a id="input_formats"></a>Media Encoder Prémium munkafolyamat bemeneti formátumok és kodekek

A következő szakasz azokat a kodekeket és fájlformátumokat sorolja fel, amelyeket ez a médiaprocesszor bemenetként támogat.

### <a name="input-containerfile-formats"></a>Bemeneti tároló/fájlformátum

* Adobe® Flash® F4V
* MXF/SMPTE 377m
* GXF
* MPEG-2 átviteli adatfolyamok
* MPEG-2 programadatfolyamok
* MPEG-4/MP4
* Windows Media/ASF
* AVI (tömörítetlen 8bit/10bit)

### <a name="input-video-codecs"></a>Bemeneti videókodekek

* 8 bites/10 bites AVC, akár 4:2:2-esig, ideértve az AVCIntrát is
* Avid DNxHD (MXF-ben)
* DVCPro/DVCProHD (MXF-ben)
* HEVC/H.265, Fő és Fő 10 Profil
* JPEG2000
* MPEG-2 (422 profilig és magas szintűig; beleértve az olyan változatokat, mint az XDCAM, XDCAM HD, XDCAM IMX, CableLabs® és D10)
* MPEG-1
* Windows Media Videó/VC-1

### <a name="input-audio-codecs"></a>Bemeneti hangkodekek

* AES (SMPTE 331M és 302 M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1-esig)
* MPEG 2 réteg
* MP3 (MPEG-1, 3 hangréteg)
* Windows Media Audio
* WAV/PCM

## <a name="media-encoder-premium-workflow-output-formats-and-codecs"></a><a id="output_format"></a>Media Encoder Prémium munkafolyamat kimeneti formátumok és kodekek

A következő szakasz azokat a kodekeket és fájlformátumokat sorolja fel, amelyeket az adathordozó-feldolgozó kimeneteként támogatnak.

### <a name="output-containerfile-formats"></a>Kimeneti tároló/fájlformátumok

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM és AS02)
* DPP (beleértve az AS11-et)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (tömörítetlen 8bit/10bit)
* Zökkenőmentes streamelési fájlformátum (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Kimeneti videó kodekek

* AVC (H.264; 8 bites; nagy profilú, 5.2-es szintű; 4K Ultra HD; AVC Intra)
* Avid DNxHD (MXF-ben)
* DVCPro/DVCProHD (MXF-ben)
* MPEG-2 (422 profilig és magas szintűig; beleértve az olyan változatokat, mint az XDCAM, XDCAM HD, XDCAM IMX, CableLabs® és D10)
* MPEG-1
* Windows Media Videó/VC-1
* JPEG bélyegkép létrehozása
* HEVC (H.265; 8 bites és 10 bites, Fő és Fő 10 profil)


### <a name="output-audio-codecs"></a>Kimeneti hangkodekek

* AES (SMPTE 331M és 302 M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) akár 7,1
* AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1-esig)
* MPEG 2 réteg
* MP3 (MPEG-1, 3 hangréteg)
* Windows Media Audio

>[!NOTE]
>Ha dolby® Digital (AC3) kódolásra kódol, a kimenet csak ISO MP4 fájlba írható.

## <a name="support-for-closed-captioning"></a><a id="closed_captioning"></a>A feliratozás támogatása

Betöltéskor a **Media Encoder Premium Workflow** a következőket támogatja:

1. SCC-fájlok
2. SMPTE-TT fájlok
3. CEA-608/CEA-708 – felhasználói adatként (H.264 elemi streamek SEI üzenetei, ATSC/53, SCTE20) vagy MXF/GXF-fájlokkiegészítő adataiként
4. STL felirat fájlok

Kimenetkor a következő lehetőségek állnak rendelkezésre:

1. CEA-608 a CEA-708 fordítás
2. CEA-608/CEA-708 áthalad (beágyazott SEI üzenetek H.264 elemi patakok, vagy végzett kiegészítő adatok MXF fájlok)
3. Scc
4. SMPTE időzített szöveg (a CEA-608 forrásból SMPTE RP2052;beleértve a DFXP-fájlok létrehozását is)
5. SRT Felirat fájl
6. DVB feliratfolyamok

> [!NOTE]
> Nem minden a fenti kimeneti formátumok támogatottak az Azure Media Services streamelésútján történő kézbesítéséhez.

## <a name="known-issues"></a>Ismert problémák

Ha a bemeneti videó nem tartalmaz feliratozást, a kimeneti eszköz továbbra is tartalmaz egy üres TTML-fájlt. 

## <a name="need-help"></a>Segítségre van szüksége?

Támogatási jegyet úgy nyithat meg, hogy az [Új támogatási kérelemre](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) navigál.
## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

