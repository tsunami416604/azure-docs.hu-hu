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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79251023"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Media Encoder Standard-formátumok és -kodekek

> [!div class="op_single_selector" title1="Válassza ki a használt Media Services verzióját:"]
> * [2-es verzió](media-services-media-encoder-standard-formats.md)
> * [3-as verzió](../latest/media-encoder-standard-formats.md)

Ez a dokumentum a leggyakoribb importálási és exportálási fájlformátumok listáját tartalmazza, amelyeket Media Encoder Standard használhat.

## <a name="input-containerfile-formats"></a>Bemeneti tároló/fájlformátumok
| Fájlformátum (fájlkiterjesztés) | Támogatott |
| --- | --- |
| FLV (H.264 és AAC kodekkel) (.flv) |Igen |
| MXF (.mxf) |Igen |
| MXF (.mxf) |Igen |
| MPEG2 PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Igen |
| Windows Media Video (WMV) / ASP (.wmv, .asf) |Igen |
| AVI (8 bit-es/10 bit-es, tömörítettlen) (.avi) |Igen |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |Igen |
| [Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Igen |
| Matroska/WebM (.mkv) |Igen |
| WAVE/WAV (.wav) |Igen |
| QuickTime (.mov) |Igen |

> [!NOTE]
> A fenti lista a leggyakrabban előforduló fájlkiterjesztéseket tartalmazza. A Media Encoder Standard számos más támogatást támogat (például:. m2ts,. mpeg2video,. Qt). Ha egy fájlt próbál kódolni, és a nem támogatott formátumra vonatkozó hibaüzenetet kap, [itt](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/)adja meg a visszajelzését.
> 
> 

### <a name="audio-formats-in-input-containers"></a>Hangformátumok bemeneti tárolókban
Media Encoder Standard támogatja a következő hangformátumok átvitelét a bemeneti tárolókban:

* MXF-, GXF-és QuickTime-fájlok, amelyek hang-és hangsávokat tartalmazhatnak az összekapcsolt sztereó vagy 5,1-mintákkal

vagy

* Olyan MXF-, GXF- és QuickTime-fájlok, melyekben külön PCM sávban található a hang, de csatornaleképezés (sztereó vagy 5.1) a fájlok metaadataiból származtatható

## <a name="input-video-codecs"></a>Bemeneti videó codec-je
| Bemeneti videó codec-je | Támogatott |
| --- | --- |
| 8 bites/10 bites AVC, akár 4:2:2-esig, ideértve az AVCIntrát is |8 bites 4:2:0-es és 4:2:2-es |
| Avid DNxHD (MXF-ben) |Igen |
| DVCPro/DVCProHD (MXF-ben) |Igen |
| Digital video (DV) (AVI-fájlokban) |Igen |
| JPEG 2000 |Igen |
| MPEG-2 (akár a 422-es profilig és a magas szintig; az XDCAM, XDCAM HD, XDCAM IMX, CableLabs® és D10 típusú változatokat is beleértve) |Akár 422-es profilig |
| MPEG-1 |Igen |
| VC-1/WMV9 |Igen |
| Canopus HQ/HQX |Nem |
| MPEG-4 Part 2 |Igen |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Igen |
| Tömörítetlen YUV420 vagy mezzanine |Igen |
| Apple ProRes 422 |Igen |
| Apple ProRes 422 LT |Igen |
| Apple ProRes 422 HQ |Igen |
| Apple ProRes Proxy |Igen |
| Apple ProRes 4444 |Igen |
| Apple ProRes 4444 XQ |Igen |
| HEVC/H. 265| Fő és fő 10 (&#42;) profilok<br/>A 10 fő profil támogatása a 8bit 4:2:0-tartalomhoz készült. |

## <a name="input-audio-codecs"></a>Bemeneti hangkodekek
| Bemeneti hangkodekek | Támogatott |
| --- | --- |
| AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1-esig) |Igen |
| MPEG 2 réteg |Igen |
| MP3 (MPEG-1, 3 hangréteg) |Igen |
| Windows Media Audio |Igen |
| WAV/PCM |Igen |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Igen |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Igen |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Igen |
| AMR (adaptív többsebességű) |Igen |
| AES (SMPTE 331M és 302 M, AES3-2003) |Nem |
| Dolby® E |Nem |
| Dolby® Digital (AC3) |Nem |
| Dolby® Digital (E-AC3) |Nem |

## <a name="output-formats-and-codecs"></a>Kimeneti formátumok és kodekek
A következő táblázat az exportáláshoz támogatott kodekeket és fájlformátumokat sorolja fel.

| Fájlformátum | Videó kodek | Hangkodek |
| --- | --- | --- |
| MP4 <br/><br/>(többek között a többszörös sávszélességű MP4-tárolók) |H. 264 (magas, fő és alapkonfigurációs profilok) |AAC-LC, it-AAC v1, it-AAC v2 |
| MPEG2 – TS |H. 264 (magas, fő és alapkonfigurációs profilok) |AAC-LC, it-AAC v1, it-AAC v2 |

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még
[Igény szerinti tartalom kódolása Azure Media Services](media-services-encode-asset.md)

[Kódolás a Media Encoder Standard használatával](media-services-dotnet-encode-with-media-encoder-standard.md)

