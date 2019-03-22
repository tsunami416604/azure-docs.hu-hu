---
title: Media Encoder Standard-formátumok és -kodekek – Azure
description: Ez a témakör áttekintést nyújt a Media Encoder Standard-formátumok és -kodekek.
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
ms.date: 02/10/2019
ms.author: juliako;anilmur
ms.openlocfilehash: 4c7e0e091aeecb205c63b8fa7e97d7592f15b856
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58084378"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Media Encoder Standard-formátumok és -kodekek

Ez a cikk megtalálhatja a legismertebb importálható és exportálható fájlformátumokat is használhatja az [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Információk segítségével egyéni beállításkészletek létrehozása **StandardEncoderPreset**, lásd: [hozzon létre egy egyéni előbeállítás átalakító](customize-encoder-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Bemeneti tároló/fájlformátum

| Fájlformátum (fájlkiterjesztés) | Támogatott |
| --- | --- |
| FLV (H.264 és AAC kodekkel) (.flv) |Igen |
| MXF    (.mxf) |Igen |
| GXF    (.gxf) |Igen |
| MPEG2 PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Igen |
| Windows Media Video (WMV) / ASP (.wmv, .asf) |Igen |
| AVI (tömörítetlen 8 bites/10 bit) (.avi) |Igen |
| MP4 (.mp4, .m4a, .m4v) / ISMV (.isma, .ismv) |Igen |
| [A Microsoft Digital Video Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr ms) |Igen |
| Matroska/WebM (.mkv) |Igen |
| WAVE/WAV (.wav) |Igen |
| QuickTime (.mov) |Igen |

> [!NOTE]
> Egy lista a leggyakrabban előforduló fájlkiterjesztéseket újabb verziója. Media Encoder Standard támogatja sokan mások is (például: .m2ts, .mpeg2video és .qt formátumokat). Ha egy fájl kódolása próbál, és a formátum nem támogatott kapcsolatos hibaüzenet kap, adjon visszajelzéseket [Itt](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).
> 
> 

### <a name="audio-formats-in-input-containers"></a>Hangformátumok a bemeneti tárolók
Media Encoder Standard támogatja a következő hangformátumok a bemeneti tárolók végrehajtása:

* MXF, GXF és QuickTime fájlok, amelyek hangsávval rendelkező időosztásos sztereó vagy 5.1 minták

vagy

* MXF, GXF és QuickTime-fájlok, a hanganyag van külön PCM nyomon követi, de a csatornaleképezés (sztereó vagy 5.1) a fájlok metaadataiból kitűnő

## <a name="input-video-codecs"></a>Bemeneti videókodekek
| Bemeneti videókodekek | Támogatott |
| --- | --- |
| AVC 8 bites/10 bites-, akár 4:2:2, ideértve az avcintrát is |8 bites 4:2:0. és 4:2:2 |
| Avid DNxHD (MXF-ben) |Igen |
| DVCPro/DVCProHD (in MXF) |Igen |
| Digital video (DV) (a AVI-fájlokban) |Igen |
| JPEG 2000 |Igen |
| MPEG-2 (akár a 422-es Profilig és a magas szintig; az XDCAM, XDCAM HD, XDCAM IMX, CableLabs® és D10 például) |Akár 422-es Profilig |
| MPEG-1 |Igen |
| VC-1/WMV9 |Igen |
| Canopus HQ/HQX |Nem |
| MPEG-4 Part 2 |Igen |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Igen |
| Tömörítetlen YUV420 vagy mezzanine |Igen |
| Az Apple ProRes 422 |Igen |
| Az Apple ProRes 422 LT |Igen |
| Az Apple ProRes 422 HQ |Igen |
| Az Apple ProRes Proxy |Igen |
| Az Apple ProRes 4444 |Igen |
| Az Apple ProRes 4444 XQ |Igen |
| HEVC/H.265| Fő profil|

## <a name="input-audio-codecs"></a>Bemeneti hangkodekek
| Bemeneti hangkodekek | Támogatott |
| --- | --- |
| AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1) |Igen |
| MPEG Layer 2 |Igen |
| MP3 (MPEG-1-3 hangréteg) |Igen |
| Windows Media Audio |Igen |
| WAV/PCM |Igen |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Igen |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Igen |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Igen |
| AMR (adaptív Többsebességű) |Igen |
| AES (SMPTE 331 M és 302 M, AES3-2003) |Nem |
| Dolby® E |Nem |
| Dolby® digitális (AC3) |Nem |
| Dolby® digitális plusz (E-AC3) |Nem |

## <a name="output-formats-and-codecs"></a>Kimeneti formátumai és kodekei
Az alábbi táblázat a kodekeket és fájlformátumot exportálási által támogatott.

| Fájlformátum | Videó kodek | Hang kodek |
| --- | --- | --- |
| MP4 <br/><br/>(beleértve a többszörös átviteli sebességű MP4-tárolók) |A H.264 (magas, fő és alapkonfiguráció-profilok) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |A H.264 (magas, fő és alapkonfiguráció-profilok) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>További lépések

[Hozzon létre egy-egy átalakítási egyéni előbeállítás](customize-encoder-presets-how-to.md)
