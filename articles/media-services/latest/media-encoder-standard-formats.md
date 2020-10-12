---
title: Standard kódoló-formátumok és-kodekek – Azure
description: Ez a cikk a leggyakoribb importálási és exportálási fájlformátumok listáját tartalmazza, amelyeket a StandardEncoderPreset használhat.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.reviewer: anilmur
ms.openlocfilehash: f6e9c0a3d9100bc447314f5874107363c6bf5d8a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "89289443"
---
# <a name="standard-encoder-formats-and-codecs"></a>Szabványos kódoló-formátumok és-kodekek

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Ez a cikk a leggyakoribb importálási és exportálási fájlformátumok listáját tartalmazza, amelyeket a [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset)használhat. Az egyéni előállítók **StandardEncoderPreset**használatával történő létrehozásával kapcsolatos információkért lásd az [átalakítás egyéni beállításkészlettel történő létrehozását](customize-encoder-presets-how-to.md)ismertető témakört.

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
| [Microsoft Digital Video Recording(DVR-MS)](/previous-versions/windows/desktop/mstv/about-the-dvr-ms-file-format) (.dvr-ms) |Igen |
| Matroska/WebM (.mkv) |Igen |
| WAVE/WAV (.wav) |Igen |
| QuickTime (.mov) |Igen |

### <a name="audio-formats-in-input-containers"></a>Hangformátumok bemeneti tárolókban

A standard szintű kódoló támogatja a következő hangformátumok átvitelét a bemeneti tárolókban:

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
| HEVC/H. 265| Fő profil|

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

## <a name="next-steps"></a>Következő lépések

[Átalakítás létrehozása egyéni beállításkészlettel](customize-encoder-presets-how-to.md)
