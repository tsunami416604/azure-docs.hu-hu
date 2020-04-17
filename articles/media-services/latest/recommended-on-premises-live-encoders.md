---
title: A Media Services által ajánlott élő közvetítés-kódolók - Azure | Microsoft dokumentumok
description: További információ a Media Services által ajánlott, helyszíni élő streamelési kódolókról
services: media-services
keywords: kódolás;kódolók;adathordozó
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 04/16/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: bbd2929e245fa5fc01245e7bd02e537db8d5ef36
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536335"
---
# <a name="tested-on-premises-live-streaming-encoders"></a>Tesztelt helyszíni élő streamelési kódolók

Az Azure Media Servicesben egy [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) (csatorna) egy élő közvetítési tartalom feldolgozásához. Az élő esemény kétféleképpen fogad élő bemeneti adatfolyamokat.

* A helyszíni élő kódoló többátviteli sebességű RTMP- vagy sima streamelési (töredezett MP4) adatfolyamot küld az élő eseményre, amely nincs engedélyezve a Media Services élő kódolásához. A bevitt adatfolyamok további feldolgozás nélkül haladnak át az élő eseményeken. Ezt a módszert **pass-through-nak nevezzük.** Azt javasoljuk, hogy az élő kódoló küldjön többsávszélességű adatfolyamok helyett egy egysávszélességű stream egy áthaladási élő esemény, amely lehetővé teszi az adaptív sávszélességű streamelés az ügyfél számára. 

    Ha többbitráta-adatfolyamot használ az átmenő élő eseményhez, a videó GOP-méretét és a különböző bitrátákon lévő videotöredékeket szinkronizálni kell, hogy elkerülje a váratlan viselkedést a lejátszási oldalon.

  > [!TIP]
  > Az áthaladási módszer használata a leggazdaságosabb módja az élő közvetítésnek.
 
* A helyszíni élő kódoló egy egybitrátús adatfolyamot küld az élő eseményre, amely a Media Services szolgáltatással élő kódolást engedélyez a következő formátumok egyikén: RTMP vagy Smooth Streaming (töredezett MP4). Az élő esemény ezután élő kódolást hajt végre a bejövő egysávszélességű adatfolyamról egy többsávszélességű (adaptív) videoadatfolyamba.

Ez a cikk ismerteti a helyszíni élő streamelő kódolókat. A helyszíni élő kódoló ellenőrzésére vonatkozó tudnivalókért tekintse [meg a helyszíni kódoló ellenőrzése](become-on-premises-encoder-partner.md)

A Media Services szolgáltatással való élő kódolásról a [Media Services 3-as oldalával című élő közvetítés](live-streaming-overview.md)című témakörben talál részletes információt.

## <a name="encoder-requirements"></a>Kódoló követelmények

A kódolóknak HTTPS vagy RTMPS protokollok használata esetén támogatniuk kell a TLS 1.2-t.

## <a name="live-encoders-that-output-rtmp"></a>RTMP-t kibocsátó élő kódolók

A Media Services a következő, RTMP kimenetű élő kódolók használatát javasolja. A támogatott URL-sémák vagy `rtmp://` . `rtmps://`

Amikor RTMP-vel streamel, ellenőrizze a tűzfal és/vagy a proxy beállításaiban, hogy az 1935-ös és az 1936-os kimenő TCP-portok nyitva vannak-e.<br/><br/>
Amikor RTMPS-sel streamel, ellenőrizze a tűzfal és/vagy a proxy beállításaiban, hogy a 2935-ös és a 2936-os kimenő TCP-portok nyitva vannak-e.

> [!NOTE]
> A kódolóknak támogatniuk kell a TLS 1.2 protokollt RTMPS protokollok használata esetén.

- Adobe Flash Media Live Encoder 3.2
- [Cambria Élő 4,3](https://www.capellasystems.net/products/cambria-live/)
- Elemental Live (2.14.15-ös és újabb verzió)
- Haivision KB
- Haivision Makito X HEVC
- OBS Studio
- Switcher Studio (iOS)
- Telestream Wirecast (13.0.2-es vagy újabb verzió a TLS 1.2 követelmény miatt)
- Telestream Wirecast S (csak RTMP támogatott)
- Teradek Slice 756
- TriCaster 8000
- VMIX
- xStream
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro között](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hero 7 és Hero 8
- [Restream.io](https://restream.io/)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Töredezett MP4-et kibocsátó élő kódolók

A Media Services az alábbi élő kódolók egyikének használatát javasolja, amelyek többátviteli sebességű smooth streamelést (töredezett MP4) használnak kimenetként. A támogatott URL-sémák vagy `http://` . `https://`

> [!NOTE]
> A kódolóknak támogatniuk kell a TLS 1.2 protokollt HTTPS protokollok használata esetén.

- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Elemental Live (2.14.15-ös és újabb verzió a TLS 1.2 követelmény miatt)
- Envivio 4Caster C4 Gen III 
- Képzeld el, Kommunikáció Selenio MCP3
- Media Excel Hero Live és Hero 4K (UHD/HEVC)
- [Ffmpeg](https://www.ffmpeg.org)

> [!TIP]
>  Ha több nyelven (például egy angol és egy spanyol hangsávon) közvetít élő eseményeket, ezt a Media Excel élő kódolójával valósíthatja meg, amely úgy van beállítva, hogy az élő közvetítést egy átmenő élő eseményre küldje.

## <a name="configuring-on-premises-live-encoder-settings"></a>Helyszíni élő kódoló beállításainak konfigurálása

Ha tudni szeretné, hogy milyen beállítások érvényesek az élő eseménytípusra, olvassa el az [Élő eseménytípusok összehasonlítása című témakört.](live-event-types-comparison.md)

### <a name="playback-requirements"></a>Lejátszási követelmények

A tartalom lejátszásához hang- és videoadatfolyamnak is jelen kell lennie. A csak videoadatfolyam lejátszása nem támogatott.

### <a name="configuration-tips"></a>Konfigurációs tippek

- Lehetőség szerint használjon vezetékes internetkapcsolatot.
- A sávszélesség-követelmények meghatározásakor duplázza meg a streamelési bitrátákat. Bár nem kötelező, ez az egyszerű szabály segít a hálózati torlódások hatásának csökkentésében.
- Szoftveralapú kódolók használata esetén zárja be a szükségtelen programokat.
- A kódoló konfigurációjának módosítása a lenyomás megkezdése után negatív hatással van az eseményre. A konfigurációs módosítások az esemény instabillá válását okozhatják. 
- Győződjön meg arról, hogy elegendő időt ad magának az esemény beállításához. Nagy méretű események esetén azt javasoljuk, hogy a beállítást egy órával az esemény előtt indítsd el.
- Használja a H.264 video- és AAC audio kodek kimenetet.
- Győződjön meg arról, hogy van kulcsképkocka vagy GOP időbeli igazítás a videó minőségei között.
- Győződjön meg arról, hogy minden egyes videóminőséghez egyedi adatfolyam-név szerepel.
- Az optimális adaptív bitráta-teljesítmény érdekében használjon szigorú CBR kódolást.

> [!IMPORTANT]
> Nézze meg a fizikai állapotát a gép (CPU / Memória / stb), mint a töredékek feltöltése a felhőbe jár CPU és IO műveleteket. Ha módosítja a beállításokat a kódoló, biztos, hogy állítsa vissza a csatornák / élő esemény a változás hatályba.

## <a name="see-also"></a>Lásd még

[Élő közvetítés a Media Services v3-as ával](live-streaming-overview.md)

## <a name="next-steps"></a>További lépések

[A kódoló ellenőrzése](become-on-premises-encoder-partner.md)
