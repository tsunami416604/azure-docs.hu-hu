---
title: Media Encoder Standard formátumok és kodekek
description: Ez a témakör áttekintést nyújt a Media Encoder Standard formátumok és kodekek.
services: media-services
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.assetid: f334b1ce-2f56-4968-a019-f0a2b0016d9f
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: juliako;anilmur
ms.openlocfilehash: 181a1b8ad6403045264ddc0bd502273f36df3eff
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34638330"
---
# <a name="media-encoder-standard-formats-and-codecs"></a>Media Encoder Standard-formátumok és -kodekek
Ez a dokumentum a leggyakrabban használt importálási és exportálási fájlformátumot is használhatja a Media Encoder Standard listáját tartalmazza.

## <a name="input-containerfile-formats"></a>Adjon meg tároló/fájlformátum
| Fájlformátumok (fájlkiterjesztések) | Támogatott |
| --- | --- | --- | --- |
| (A H.264 és AAC kodekek) FLV (.flv) |Igen |
| MXF (.mxf) |Igen |
| GXF (.gxf) |Igen |
| MPEG2 PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |Igen |
| Windows Media videó (WMV) / ASP (.wmv, .asf) |Igen |
| AVI (tömörítetlen 8 bit/10 bit) (.avi) |Igen |
| MP4 (.mp4, .m4a, .m4v) / ISMV (.isma, .ismv) |Igen |
| [A Microsoft digitális videót Recording(DVR-MS)](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |Igen |
| Matroska/WebM (.mkv) |Igen |
| WAVE/WAV (.wav) |Igen |
| QuickTime (.mov) |Igen |

> [!NOTE]
> Újabb van a gyakrabban észlelt kiterjesztések listája. Media Encoder Standard támogatja számos más (például: .m2ts, .mpeg2video, .qt). Ha egy fájl kódolása próbál, és egy hibaüzenet jelenik meg a formátum nem támogatott kapcsolatos, visszajelzést a [Itt](https://feedback.azure.com/forums/169396-media-services/category/144411-encoding-and-processing/).
> 
> 

### <a name="audio-formats-in-input-containers"></a>A bemeneti tárolók hang formátumok
Media Encoder Standard támogatja a következő hang formátumú bemeneti tárolókban lévő elvégzéséhez:

* MXF, GXF és QuickTime fájlok, amelyek zeneszámok időosztásos sztereó vagy 5.1 minták

vagy

* Amennyiben a hang külön PCM nyomon követi, de a csatorna hozzárendelését (sztereó vagy 5.1) végzik MXF GXF és QuickTime fájlok is származtatható a fájlok metaadatait

A csatorna explicit vagy felhasználó által megadott leképezést támogatást nyújtanak a közeljövőben.

## <a name="input-video-codecs"></a>A bemeneti videó kodekek
| A bemeneti videó kodekek | Támogatott |
| --- | --- | --- | --- |
| 8 bit/10-bites, legfeljebb 4 AVC: 2:2, beleértve a AVCIntra |8 bites 4:2:0. és 4:2:2. régiója |
| (A MXF) Avid DNxHD |Igen |
| DVCPro/DVCProHD (a MXF) |Igen |
| Digitális videót (DV) (AVI-fájlokban szereplő) |Igen |
| JPEG 2000 |Igen |
| MPEG-2 (legfeljebb 422 profil és a magas szintű; például XDCAM, XDCAM HD, XDCAM IMX, CableLabs® és D10 Variant típusú adatok is beleértve) |Akár 422 profil |
| MPEG-1 |Igen |
| VC-1/WMV9 |Igen |
| Canopus HQ/HQX |Nem |
| 2. rész MPEG-4 |Igen |
| [Theora](https://en.wikipedia.org/wiki/Theora) |Igen |
| Tömörítetlen YUV420 vagy mezzanine |Igen |
| Apple ProRes 422 |Igen |
| Apple ProRes 422 LT |Igen |
| Apple ProRes 422 HQ |Igen |
| Apple ProRes Proxy |Igen |
| Apple ProRes 4444 |Igen |
| Apple ProRes 4444 XQ |Igen |
| HEVC/H.265| Fő profil|

## <a name="input-audio-codecs"></a>Bemeneti hang kodekek
| Bemeneti hang kodekek | Támogatott |
| --- | --- | --- | --- |
| AAC (AAC-LC, AAC-HE és AAC-HEv2; akár 5.1) |Igen |
| 2. réteg MPEG |Igen |
| MP3 (MPEG-1 hang réteg 3) |Igen |
| Windows Media hang |Igen |
| WAV/PCM |Igen |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |Igen |
| [Opus](http://go.microsoft.com/fwlink/?LinkId=822667) |Igen |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |Igen |
| AMR (adaptív több gyakoriság) |Igen |
| AES (SMPTE 331 M és 302 M, AES3-2003) |Nem |
| Dolby® E |Nem |
| Dolby® digitális (AC3) |Nem |
| Dolby® digitális plusz (E-AC3) |Nem |

## <a name="output-formats-and-codecs"></a>Kimeneti formátumok és kodekek
A következő táblázat a kodekeket és a fájl formátumot, az Exportálás támogatott.

| Fájlformátum | Videó kodek | Hang kodek |
| --- | --- | --- |
| MP4 <br/><br/>(beleértve a többszörös sávszélességű MP4-tárolók) |H.264 (a nagy, a fő és a Baseline profilok) |AAC-LC, HE-AAC v1, HE-AAC 2 |
| MPEG2 – TS |H.264 (a nagy, a fő és a Baseline profilok) |AAC-LC, HE-AAC v1, HE-AAC 2 |

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Lásd még
[Az Azure Media Services kódolási igény tartalom](media-services-encode-asset.md)

[A Media Encoder Standard kódolással](media-services-dotnet-encode-with-media-encoder-standard.md)

