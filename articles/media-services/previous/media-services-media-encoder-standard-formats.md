---
title: Media Encoder Standard formátumok és kodekek – Azure
description: Ez a cikk áttekintést nyújt Media Encoder Standard formátumokról és kodekekről.
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
ms.date: 03/18/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: e2ccce13e4ef09426d0f3a02dcbce2f330b0ead8
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78366842"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Media Encoder Standard-formátumok és -kodekek

> [!div class="op_single_selector" title1="Válassza ki a használt Media Services verzióját:"]
> * [2-es verzió](media-services-media-encoder-standard-formats.md)
> * [3-as verzió](../latest/media-encoder-standard-formats.md)

Ez a dokumentum a leggyakoribb importálási és exportálási fájlformátumok listáját tartalmazza, amelyeket Media Encoder Standard használhat.

## <a name="input-containerfile-formats"></a>Bemeneti tároló/fájlformátumok
| Fájlformátumok (fájlkiterjesztések) | Támogatott |
| --- | --- |
| FLV (H. 264 és AAC kodekekkel) (. flv) |Igen |
| MXF    (.mxf) |Igen |
| GXF    (.gxf) |Igen |
| MPEG2-PS, MPEG2-TS, 3GP (. TS,. ps,. 3GP,. 3GPP,. mpg) |Igen |
| Windows Media Video (WMV)/ASF (. wmv,. asf) |Igen |
| AVI (tömörítetlen 8bit/10bit) (. avi) |Igen |
| MP4 (. MP4,. M4A,. m4v)/ISMV (. ISMA,. ISMV) |Igen |
| [Microsoft digitális videó rögzítése (DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (. DVR-MS) |Igen |
| Matroska/WebM (. mkv) |Igen |
| WAVE/WAV (.wav) |Igen |
| QuickTime (. mov) |Igen |

> [!NOTE]
> A fenti lista a gyakrabban előforduló fájlkiterjesztések listáját tartalmazza. A Media Encoder Standard számos más támogatást támogat (például:. m2ts,. mpeg2video,. Qt). Ha egy fájlt próbál kódolni, és a nem támogatott formátumra vonatkozó hibaüzenetet kap, [itt](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/)adja meg a visszajelzését.
> 
> 

### <a name="audio-formats-in-input-containers"></a>Hangformátumok bemeneti tárolókban
Media Encoder Standard támogatja a következő hangformátumok átvitelét a bemeneti tárolókban:

* MXF-, GXF-és QuickTime-fájlok, amelyek hang-és hangsávokat tartalmazhatnak az összekapcsolt sztereó vagy 5,1-mintákkal

vagy

* MXF-, GXF-és QuickTime-fájlok, amelyekben a hang különálló PCM-sávokként van elvégezve, de a csatorna leképezése (sztereó vagy 5,1) a fájl metaadataiból következtethető le.

## <a name="input-video-codecs"></a>Bemeneti videó codec-je
| Bemeneti videó codec-je | Támogatott |
| --- | --- |
| AVC 8 bites/10 bites, legfeljebb 4:2:2, beleértve a következőt: AVCIntra |8 bites 4:2:0 és 4:2:2 |
| Avid DNxHD (MXF) |Igen |
| DVCPro/DVCProHD (in MXF) |Igen |
| Digitális videó (DV) (AVI-fájlokban) |Igen |
| JPEG 2000 |Igen |
| MPEG-2 (akár 422-es profil és magas szintű, beleértve a XDCAM, a XDCAM HD, a XDCAM IMX, a CableLabs® és a D10 változatokat) |Akár 422-es profil |
| MPEG-1 |Igen |
| VC-1/WMV9 |Igen |
| Canopus HQ/HQX |Nem |
| 2\. MPEG-4 rész |Igen |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Igen |
| YUV420 tömörítetlen vagy köztes |Igen |
| Apple ProRes 422 |Igen |
| Apple ProRes 422 LT |Igen |
| Apple ProRes 422 – HQ |Igen |
| Apple ProRes proxy |Igen |
| Apple ProRes 4444 |Igen |
| Apple ProRes 4444 XQ |Igen |
| HEVC/H. 265| Fő és fő 10 (&#42;) profilok<br/>A 10 fő profil támogatása a 8bit 4:2:0-tartalomhoz készült. |

## <a name="input-audio-codecs"></a>Bemeneti hangkodekek
| Bemeneti hangkodekek | Támogatott |
| --- | --- |
| AAC (AAC-LC, AAC-s és AAC-HEv2; akár 5,1) |Igen |
| 2\. MPEG-réteg |Igen |
| MP3 (MPEG-1 hangréteg 3) |Igen |
| Windows Media Audio |Igen |
| WAV/PCM |Igen |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Igen |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Igen |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Igen |
| AMR (adaptív Multi-Rate) |Igen |
| AES (SMPTE 331M és 302M, AES3-2003) |Nem |
| Dolby® E |Nem |
| Dolby® Digital (AC3) |Nem |
| Dolby® Digitális Plus (E-AC3) |Nem |

## <a name="output-formats-and-codecs"></a>Kimeneti formátumok és kodekek
A következő táblázat az exportáláshoz támogatott kodekeket és fájlformátumokat sorolja fel.

| Fájlformátum | Videó kodek | Hangkodek |
| --- | --- | --- |
| MP4 <br/><br/>(többek között a többszörös sávszélességű MP4-tárolók) |H. 264 (magas, fő és alapkonfigurációs profilok) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H. 264 (magas, fő és alapkonfigurációs profilok) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még
[Igény szerinti tartalom kódolása Azure Media Services](media-services-encode-asset.md)

[Kódolás a Media Encoder Standard használatával](media-services-dotnet-encode-with-media-encoder-standard.md)

