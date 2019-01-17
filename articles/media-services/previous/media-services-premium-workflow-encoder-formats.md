---
title: Media Encoder Premium munkafolyamat formátumai és kodekei |} A Microsoft Docs
description: Ez a témakör áttekintést nyújt a Media Encoder Premium munkafolyamat formátumai formátumai és kodekei
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
ms.date: 01/15/2019
ms.author: juliako;anilmur
ms.openlocfilehash: 5059e4c00fc3bcbee76f8f5a3746b4d8783d901b
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359272"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Media Encoder Premium munkafolyamat formátumai és kodekei

> [!NOTE]
> Ha kérdése van a prémium szintű kódoló, e-mailben mepd@microsoft.com.
> 
> Kínában nem érhető el ebben a témakörben tárgyalt Media Encoder Premium munkafolyamat médiafeldolgozót. 
> 
> 

Ez a dokumentum a bemeneti és kimeneti formátumok és -kodekek, a nyilvános előzetes verziója által támogatott listáját tartalmazza a **Media Encoder Premium munkafolyamat** kódoló.

[A Media Encoder Premium Worflow bemeneti formátumai és Kodekei](#input_formats)

[A Media Encoder Premium Worflow kimeneti formátumai és Kodekei](#output_formats)

**Media Encoder Premium munkafolyamat** támogatja a feliratozás ismertetett [ez](#closed_captioning) szakaszban. 

## <a id="input_formats"></a>Media Encoder Premium munkafolyamat formátumai és Kodekei bemeneti

A következő szakaszban azok az kodekek és fájlformátumot, amely bemenetként támogatja a médiafeldolgozót.

### <a name="input-containerfile-formats"></a>Bemeneti tároló/fájlformátum

* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* MPEG-2 Transport Streams
* MPEG-2 Program Streamek
* MPEG-4/MP4
* Windows Media/ASF
* AVI (tömörítetlen 8 bites/10 bites)

### <a name="input-video-codecs"></a>Bemeneti Videókodekek

* AVC 8 bites/10 bites-, akár 4:2:2, ideértve az avcintrát is
* Avid DNxHD (MXF-ben)
* DVCPro/DVCProHD (in MXF)
* HEVC/H.265, a fő és a 10-es főbb profil
* JPEG2000
* MPEG-2 (akár a 422-es Profilig és a magas szintig; az XDCAM, XDCAM HD, XDCAM IMX, CableLabs® és D10 például)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Bemeneti hangkodekek

* AES (SMPTE 331 M és 302 M, AES3-2003)
* Dolby® E
* Dolby® digitális (AC3)
* AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1)
* MPEG Layer 2
* MP3 (MPEG-1-3 hangréteg)
* Windows Media Audio
* WAV/PCM

## <a id="output_format"></a>A Media Encoder Premium munkafolyamat kimeneti formátumai és Kodekei

A következő szakasz a kodekeket és fájlformátumot kimeneteként kapott médiafeldolgozót. a támogatott sorolja fel.

### <a name="output-containerfile-formats"></a>Kimeneti tároló/fájlformátum

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM és AS02)
* DPP (beleértve a AS11)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (tömörítetlen 8 bites/10 bites)
* Smooth Streaming fájlformátum (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Kimeneti Videókodekek

* AVC (H.264; 8 bites; akár nagy profilnál szintű 5.2-es; 4 K Ultranagy HD; AVC belüli)
* Avid DNxHD (MXF-ben)
* DVCPro/DVCProHD (in MXF)
* MPEG-2 (akár a 422-es Profilig és a magas szintig; az XDCAM, XDCAM HD, XDCAM IMX, CableLabs® és D10 például)
* MPEG-1
* Windows Media Video/VC-1
* JPEG-miniatűr létrehozása
* HEVC (H.265; 8 és 10 bites, a fő és a fő 10-es profil)

  Támogatási HDR 10 bizonyos esetekben érhető el, forduljon mepd@microsoft.com további információ


### <a name="output-audio-codecs"></a>Kimeneti hangkodekek

* AES (SMPTE 331 M és 302 M, AES3-2003)
* Dolby® digitális (AC3)
* Dolby® Digital Plus (E-AC3) legfeljebb 7.1
* AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1)
* MPEG Layer 2
* MP3 (MPEG-1-3 hangréteg)
* Windows Media Audio

>[!NOTE]
>Dolby® Digital (AC3) való kódol, ha a kimeneti csak az ISO MP4-fájlokat lehet írni.

## <a id="closed_captioning"></a>Kódolt feliratok támogatása

A betöltési, **Media Encoder Premium munkafolyamat** támogatja:

1. SCC fájlok
2. Fájlok SMPTE-TT
3. CEA-608/CEA-708 – felhasználói adatok (H.264 elemi adatfolyamok, ATSC/53, SCTE20 SEI üzenetek), vagy sor az MXF/GXF fájlok kiegészítő adatok
4. Alcím fájlok STL

A kimenetben a következő beállítások érhetők el:

1. CEA-608 való CEA-708 fordítás
2. CEA-608/CEA-708 továbbítása (H.264 elemi adatfolyamok SEI üzenetek a beágyazott vagy végezni, kiegészítő adatok MXF-fájlokban)
3. SCC
4. SMPTE túllépte az időkorlátot szöveg (a forrás CEA-608 SMPTE RP2052 kiszolgálónként algoritmuspéldákat DFXP fájl létrehozása)
5. SRT felirat
6. DVB alcím Streamek

> [!NOTE]
> Nem, a fenti kimeneti formátumok használható összes az Azure Media Services streaming-n keresztül történő továbbítását.

## <a name="known-issues"></a>Ismert problémák

Ha a bemeneti videó nem tartalmazza a feliratozás, a kimeneti adategység továbbra is fájlt fogja tartalmazni, egy üres TTML. 

## <a name="media-services-learning-paths"></a>Media Services képzési tervek

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

