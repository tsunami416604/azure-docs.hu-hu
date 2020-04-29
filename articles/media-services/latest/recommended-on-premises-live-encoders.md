---
title: Media Services által ajánlott élő adatfolyam-kódolók – Azure | Microsoft Docs
description: Ismerkedjen meg a Media Services által ajánlott élő streaming helyszíni kódolókkal
services: media-services
keywords: kódolás; kódolók; adathordozó
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 04/16/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 0676b6b183c64dcd0fb15b87de48a4afed3a0011
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81641802"
---
# <a name="tested-on-premises-live-streaming-encoders"></a>Helyszíni élő adatfolyam-kódolók tesztelése

Azure Media Services egy [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) (csatorna) az élő közvetítésre szánt tartalom feldolgozásához szükséges folyamatot jelöli. Az élő esemény két módon fogadja az élő bemeneti adatfolyamokat.

* A helyszíni élő kódoló egy többszörös sávszélességű RTMP vagy Smooth Streaming (darabolt MP4) streamet küld az élő eseményre, amely nincs engedélyezve az élő kódolás végrehajtásához Media Services. A betöltött adatfolyamok további feldolgozás nélkül haladnak át az élő eseményeken. Ezt a metódust **áteresztőnek**nevezzük. Azt javasoljuk, hogy az élő kódoló többszörös átviteli sebességű streameket küldjön egyetlen sávszélességű adatfolyamként egy átmenő élő esemény helyett, hogy az adaptív sávszélességű adatfolyamot továbbítsa az ügyfélnek. 

    Ha több sávszélességű adatfolyamot használ az átmenő élő eseményhez, szinkronizálni kell a video GOP-méretet és a különböző bitráták videós darabjait, hogy elkerülje a nem várt viselkedést a lejátszási oldalon.

  > [!TIP]
  > Az átmenő módszer használata a leggazdaságosabb módja az élő közvetítésnek.
 
* A helyszíni élő kódoló egyetlen sávszélességű streamet küld az élő eseménynek, amely lehetővé teszi, hogy az Media Services az alábbi formátumok valamelyikével végezzen élő kódolást: RTMP vagy Smooth Streaming (töredezett MP4). Az élő esemény ezután a bejövő egyszeri átviteli sebességű adatfolyam élő kódolását egy többszörös sávszélességű (adaptív) videó streamre hajtja végre.

Ez a cikk a tesztelt helyszíni élő adatfolyam-kódolókat ismerteti. A helyszíni élő kódoló ellenőrzésével kapcsolatos útmutatásért lásd [a helyszíni kódoló ellenőrzése](become-on-premises-encoder-partner.md) című témakört.

A Media Services élő kódolásával kapcsolatos részletes információkért lásd: [élő adatfolyamok Media Services v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Kódolóval kapcsolatos követelmények

A kódolók csak HTTPS-vagy RTMP-protokollok használata esetén támogatják a TLS 1,2-et.

## <a name="live-encoders-that-output-rtmp"></a>Az RTMP kimenetét futtató élő kódolók

A Media Services a következő, RTMP kimenetű élő kódolók használatát javasolja. A támogatott URL- `rtmp://` sémák `rtmps://`a vagy a.

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
- VMIX
- xStream
- [FFmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) 7. hős és Hero 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Feldarabolt MP4 kimenetű élő kódolók

Media Services javasolja a következő élő kódolók egyikének használatát, amelyekben a többszörös sávszélességű Smooth Streaming (töredékes MP4) kimenetként van használatban. A támogatott URL- `http://` sémák `https://`a vagy a.

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

- Lehetőség szerint használjon vezetékes internetkapcsolatot.
- A sávszélesség-követelmények meghatározásakor a továbbítási bitrátát dupla értékre kell kiszámítani. Bár nem kötelező, ez az egyszerű szabály segít csökkenteni a hálózati torlódás hatását.
- A szoftveres alapú kódolók használatakor zárjunk be minden szükségtelen programot.
- A kódoló konfigurációjának a megkezdése után történő módosítása negatív hatással van az eseményre. A konfigurációs változások hatására az esemény instabillá válhat. 
- Győződjön meg arról, hogy elegendő időt ad az esemény beállítására. A nagy léptékű események esetében javasoljuk, hogy a telepítőt egy órával az esemény előtt indítsa el.
- Használja a H. 264 videót és az AAC audio codec kimenetét.
- Győződjön meg arról, hogy a videó tulajdonságai között van-e kulcsfontosságú keret vagy GOP időbeli igazítás.
- Győződjön meg arról, hogy minden videó minőségének egyedi az adatfolyam neve.
- Az optimális adaptív sávszélességű teljesítmény érdekében ajánlott a szigorú CBR-kódolás használata.

> [!IMPORTANT]
> Tekintse meg a gép fizikai állapotát (CPU/memória/etc) a felhőbe való feltöltéshez, amely processzor-és IO-műveleteket is tartalmaz. Ha módosítja a kódoló bármelyik beállítását, állítsa vissza a csatorna/élő esemény alaphelyzetbe állítását, hogy a módosítás érvénybe lépjen.

## <a name="see-also"></a>Lásd még

[Élő közvetítés a Media Services v3-val](live-streaming-overview.md)

## <a name="next-steps"></a>További lépések

[A kódoló ellenőrzése](become-on-premises-encoder-partner.md)
