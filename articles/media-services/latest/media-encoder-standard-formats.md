---
title: Szabványos kódolóformátumok és kodekek – Azure
description: Ez a cikk a StandardEncoderPreset-tel használható leggyakoribb importálási és exportálási fájlformátumok listáját tartalmazza.
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
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: f1d4d4f4006702ebe0d057e56cf24a022e73b83e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251361"
---
# <a name="standard-encoder-formats-and-codecs"></a>Szabványos kódoló formátumok és kodekek

Ez a cikk a [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset)alkalmazással használható leggyakoribb importálási és exportálási fájlformátumok listáját tartalmazza. Az egyéni készletek **StandardEncoderPreset**használatával történő létrehozásáról további információt az [Átalakítás létrehozása egyéni készlettel](customize-encoder-presets-how-to.md)című témakörben talál.

## <a name="input-containerfile-formats"></a>Beviteli tároló/fájlformátumok

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

### <a name="audio-formats-in-input-containers"></a>Hangformátumok beviteli tárolókban

A standard kódoló a következő hangformátumokat támogatja a beviteli tárolókban:

* MXF, GXF és QuickTime fájlok, amelyek hangsávot tartalmaznak átlapolt sztereó vagy 5.1-es mintával

vagy

* Olyan MXF-, GXF- és QuickTime-fájlok, melyekben külön PCM sávban található a hang, de csatornaleképezés (sztereó vagy 5.1) a fájlok metaadataiból származtatható

## <a name="input-video-codecs"></a>Videokodekek bevitele
| Videokodekek bevitele | Támogatott |
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
| HEVC/H.265| Fő profil|

## <a name="input-audio-codecs"></a>Hangkodekek bevitele
| Bemeneti hangkodekek | Támogatott |
| --- | --- |
| AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1-esig) |Igen |
| MPEG 2 réteg |Igen |
| MP3 (MPEG-1, 3 hangréteg) |Igen |
| Windows Media Audio |Igen |
| WAV/PCM |Igen |
| [Flac](https://en.wikipedia.org/wiki/FLAC)</a> |Igen |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |Igen |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Igen |
| AMR (adaptív többsebességű) |Igen |
| AES (SMPTE 331M és 302 M, AES3-2003) |Nem |
| Dolby® E |Nem |
| Dolby® Digital (AC3) |Nem |
| Dolby® Digital (E-AC3) |Nem |

## <a name="output-formats-and-codecs"></a>Kimeneti formátumok és kodekek
Az alábbi táblázat az exportra támogatott kodekeket és fájlformátumokat sorolja fel.

| Fájlformátum | Videó kodek | Hang kodek |
| --- | --- | --- |
| MP4 <br/><br/>(beleértve a többbitrátásos MP4-tárolókat is) |H.264 (magas, fő és alapvonal profilok) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (magas, fő és alapvonal profilok) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>További lépések

[Átalakítás létrehozása egyéni készlettel](customize-encoder-presets-how-to.md)
