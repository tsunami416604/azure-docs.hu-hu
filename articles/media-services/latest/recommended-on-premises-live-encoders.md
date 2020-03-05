---
title: Media Services által ajánlott élő adatfolyam-kódolók – Azure | Microsoft Docs
description: Ismerkedjen meg a Media Services által ajánlott élő streaming helyszíni kódolókkal
services: media-services
keywords: kódolás; kódolók; adathordozó
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 02/10/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 60f9209129c75e329b283045d19b4b5140b40ec2
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2020
ms.locfileid: "78268190"
---
# <a name="recommended-on-premises-live-streaming-encoders"></a>Ajánlott helyszíni élő adatfolyam-kódolók

Azure Media Services egy [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) (csatorna) az élő közvetítésre szánt tartalom feldolgozásához szükséges folyamatot jelöli. Az élő esemény két módon fogadja az élő bemeneti adatfolyamokat.

* A helyszíni élő kódoló egy többszörös sávszélességű RTMP vagy Smooth Streaming (darabolt MP4) streamet küld az élő eseményre, amely nincs engedélyezve az élő kódolás végrehajtásához Media Services. A betöltött adatfolyamok további feldolgozás nélkül haladnak át az élő eseményeken. Ezt a metódust **áteresztőnek**nevezzük. Azt javasoljuk, hogy az élő kódoló többszörös átviteli sebességű streameket küldjön egyetlen sávszélességű adatfolyamként egy átmenő élő esemény helyett, hogy az adaptív sávszélességű adatfolyamot továbbítsa az ügyfélnek. 

    Ha több sávszélességű adatfolyamot használ az átmenő élő eseményhez, szinkronizálni kell a video GOP-méretet és a különböző bitráták videós darabjait, hogy elkerülje a nem várt viselkedést a lejátszási oldalon.

  > [!NOTE]
  > Az átmenő módszer használata a leggazdaságosabb módja az élő közvetítésnek.
 
* A helyszíni élő kódoló egyetlen sávszélességű streamet küld az élő eseménynek, amely lehetővé teszi, hogy az Media Services az alábbi formátumok valamelyikével végezzen élő kódolást: RTMP vagy Smooth Streaming (töredezett MP4). Az élő esemény ezután a bejövő egyszeri átviteli sebességű adatfolyam élő kódolását egy többszörös sávszélességű (adaptív) videó streamre hajtja végre.

A Media Services élő kódolásával kapcsolatos részletes információkért lásd: [élő adatfolyamok Media Services v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Kódolóval kapcsolatos követelmények

A kódolók csak HTTPS-vagy RTMP-protokollok használata esetén támogatják a TLS 1,2-et.

## <a name="live-encoders-that-output-rtmp"></a>Az RTMP kimenetét futtató élő kódolók

A Media Services a következő, RTMP kimenetű élő kódolók használatát javasolja. A támogatott URL-sémák `rtmp://` vagy `rtmps://`.

Amikor RTMP-vel streamel, ellenőrizze a tűzfal és/vagy a proxy beállításaiban, hogy az 1935-ös és az 1936-os kimenő TCP-portok nyitva vannak-e.<br/><br/>
Amikor RTMPS-sel streamel, ellenőrizze a tűzfal és/vagy a proxy beállításaiban, hogy a 2935-ös és a 2936-os kimenő TCP-portok nyitva vannak-e.

> [!NOTE]
> A kódolók számára a TLS 1,2-et a RTMP protokoll használatakor kell támogatni.

- Adobe Flash Media Live Encoder 3.2
- [Cambria élő 4,3](https://www.capellasystems.net/products/cambria-live/)
- Elemi élő (2.14.15 és újabb verzió)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Wirecast (13.0.2 vagy újabb verzió) a TLS 1,2-követelmény miatt
- Wirecast-k (csak RTMP támogatott)
- Teradek Slice 756
- TriCaster 8000
- Tricaster Mini HD-4
- VMIX
- xStream
- [FFmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) 7. hős és Hero 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Feldarabolt MP4 kimenetű élő kódolók

Media Services javasolja a következő élő kódolók egyikének használatát, amelyekben a többszörös sávszélességű Smooth Streaming (töredékes MP4) kimenetként van használatban. A támogatott URL-sémák `http://` vagy `https://`.

> [!NOTE]
> HTTPS protokollok használata esetén a kódolóknak támogatniuk kell a TLS 1,2-et.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemi élő (a TLS 1,2-követelmény miatti 2.14.15 és újabb verzió)
- Envivio 4Caster C4 Gen III 
- Imagine Communications Selenio MCP3
- Media Excel Hero Live és Hero 4K (UHD/HEVC)
- [FFmpeg](https://www.ffmpeg.org)

> [!TIP]
>  Ha több nyelven végez élő eseményeket (például egy angol hangsávot és egy spanyol hangsávot), akkor ezt a Media Excel Live encoderben állíthatja be úgy, hogy az élő hírcsatornát továbbítsa egy átmenő élő eseményre.

## <a name="configuring-on-premises-live-encoder-settings"></a>Helyszíni élő kódoló beállításainak konfigurálása

Arról, hogy milyen beállítások érvényesek az élő esemény típusára, lásd: [élő események típusai összehasonlítás](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Lejátszási követelmények

A tartalom lejátszásához a hang-és video streamnek is jelen kell lennie. A csak videó stream lejátszása nem támogatott.

### <a name="configuration-tips"></a>Konfigurációs tippek

- Amikor csak lehetséges, hardveresen rögzített beállítású internet kapcsolat használatára.
- A sávszélesség-követelmények meghatározásakor a továbbítási bitrátát dupla értékre kell kiszámítani. Bár nem kötelező, ez az egyszerű szabály segít csökkenteni a hálózati torlódás hatását.
- Szoftveralapú kódolók használatáról, amikor el minden felesleges programot zárja be.
- A kódoló konfigurációjának a megkezdése után történő módosítása negatív hatással van az eseményre. A konfigurációs változások hatására az esemény instabillá válhat. 
- Győződjön meg arról, hogy elegendő időt ad az esemény beállítására. A nagy léptékű események esetében javasoljuk, hogy a telepítőt egy órával az esemény előtt indítsa el.

## <a name="see-also"></a>Lásd még

[Helyszíni kódoló partner lesz](become-on-premises-encoder-partner.md)

## <a name="next-steps"></a>Következő lépések

[Élő közvetítés a Media Services v3-val](live-streaming-overview.md)
