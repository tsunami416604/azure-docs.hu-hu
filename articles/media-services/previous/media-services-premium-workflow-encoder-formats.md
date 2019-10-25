---
title: Media Encoder Premium Workflow formátumok és kodekek | Microsoft Docs
description: Ez a témakör áttekintést nyújt Media Encoder Premium Workflow formátumok formátumáról és kodekekről
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
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792584"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Formátumok és kodekek Media Encoder Premium Workflow

> [!NOTE]
> A jelen témakörben tárgyalt Media Encoder Premium Workflow adathordozó-feldolgozó Kínában nem érhető el. 

Ez a dokumentum tartalmazza a bemeneti és kimeneti fájlformátumokat és kodekeket, amelyeket a **Media Encoder Premium workflow** kódoló nyilvános előzetes verziója támogat.

[Bemeneti formátumok és kodekek Media Encoder Premium Workflow](#input_formats)

Kimeneti formátumok és kodekek Media Encoder Premium Workflow

A **Media Encoder Premium workflow** támogatja a [jelen](#closed_captioning) szakaszban leírt kódolt feliratozást. 

## <a id="input_formats"></a>Bemeneti formátumok és kodekek Media Encoder Premium Workflow

A következő szakasz azokat a kodekeket és fájlformátumokat sorolja fel, amelyeket a Media Processor bemenetként támogat.

### <a name="input-containerfile-formats"></a>Bemeneti tároló/fájlformátumok

* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* MPEG-2 átviteli streamek
* MPEG-2 program streamek
* MPEG-4/MP4
* Windows Media/ASF
* AVI (tömörítetlen 8bit/10bit)

### <a name="input-video-codecs"></a>Bemeneti videó codec-je

* AVC 8 bites/10 bites, legfeljebb 4:2:2, beleértve a következőt: AVCIntra
* Avid DNxHD (MXF)
* DVCPro/munkáját (a MXF-ben)
* HEVC/H. 265, Main és Main 10 profil
* JPEG2000
* MPEG-2 (akár 422-es profil és magas szintű, beleértve a XDCAM, a XDCAM HD, a XDCAM IMX, a CableLabs® és a D10 változatokat)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Bemeneti hangkodekek

* AES (SMPTE 331M és 302M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-s és AAC-HEv2; akár 5,1)
* 2\. MPEG-réteg
* MP3 (MPEG-1 hangréteg 3)
* Windows Media hang
* WAV/PCM

## <a id="output_format"></a>Kimeneti formátumok és kodekek Media Encoder Premium Workflow

A következő szakasz azokat a kodekeket és fájlformátumokat sorolja fel, amelyeket a rendszer a Media Processor kimenetként támogat.

### <a name="output-containerfile-formats"></a>Kimeneti tároló/fájlformátumok

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM és AS02)
* DPP (beleértve a AS11-t)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (tömörítetlen 8bit/10bit)
* Smooth Streaming fájl formátuma (PIFF 1,3)
* MPEG – TS 

### <a name="output-video-codecs"></a>Kimeneti videó codec-je

* AVC (H. 264; 8 bites; legfeljebb magas profil, 5,2-as szint; 4K Ultra HD; AVC-n belüli)
* Avid DNxHD (MXF)
* DVCPro/munkáját (a MXF-ben)
* MPEG-2 (akár 422-es profil és magas szintű, beleértve a XDCAM, a XDCAM HD, a XDCAM IMX, a CableLabs® és a D10 változatokat)
* MPEG-1
* Windows Media Video/VC-1
* JPEG-miniatűr létrehozása
* HEVC (H. 265; 8 bites és 10 bites, fő és fő 10 profil)


### <a name="output-audio-codecs"></a>Kimeneti hangkodekek

* AES (SMPTE 331M és 302M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby® Digital Plus (E-AC3) akár 7,1
* AAC (AAC-LC, AAC-s és AAC-HEv2; akár 5,1)
* 2\. MPEG-réteg
* MP3 (MPEG-1 hangréteg 3)
* Windows Media hang

>[!NOTE]
>Ha a Dolby® Digital (AC3) kódolást végez, a kimenet csak ISO MP4-fájlba írható.

## <a id="closed_captioning"></a>A kódolt feliratok támogatása

Betöltéskor **Media Encoder Premium workflow** a következőt támogatja:

1. SCC-fájlok
2. SMPTE-TT-fájlok
3. CEA-608/CEA-708 – felhasználói adatként (H. 264 elemi streamek, ATSC/53, SCTE20) származó, illetve kiegészítő adatként elvégezve a MXF/GXF-fájlokban.
4. STL alcím-fájlok

A kimeneten a következő lehetőségek érhetők el:

1. CEA-608 – CEA-708 fordítás
2. CEA-608/CEA-708 továbbítása (a H. 264 elemi streamek SEI-üzeneteibe ágyazva, vagy kiegészítő adatként MXF-fájlokban történik)
3. SCC
4. SMPTE időzített szöveg (forrás CEA-608/SMPTE RP2052, beleértve a DFXP-fájl létrehozását)
5. SRT alcím fájl
6. DVB alcíme streamek

> [!NOTE]
> A fenti kimeneti formátumok nem mindegyike támogatott a Azure Media Services adatfolyamon keresztüli továbbítására.

## <a name="known-issues"></a>Ismert problémák

Ha a bemeneti videó nem tartalmaz kódolt feliratozást, a kimeneti eszköz továbbra is tartalmaz egy üres TTML-fájlt. 

## <a name="need-help"></a>Segítség

A támogatási jegy megnyitásához lépjen az [új támogatási kérelemre](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) .
## <a name="media-services-learning-paths"></a>Media Services képzési tervek

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

