---
title: Media Services által ajánlott élő adatfolyam-kódolók – Azure | Microsoft Docs
description: Ismerkedjen meg a Media Services által ajánlott élő streaming helyszíni kódolókkal
services: media-services
keywords: kódolás; kódolók; adathordozó
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 02/04/2020
ms.topic: article
ms.service: media-services
ms.openlocfilehash: bccdb49c22bce983fe8cb2aba1387c4b1645b62c
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132709"
---
# <a name="recommended-live-streaming-encoders"></a>Ajánlott élő adatfolyam-kódolók

Azure Media Services egy [élő esemény](https://docs.microsoft.com/rest/api/media/liveevents) (csatorna) az élő közvetítésre szánt tartalom feldolgozásához szükséges folyamatot jelöli. Az élő esemény két módon fogadja az élő bemeneti adatfolyamokat.

* A helyszíni élő kódoló egy többszörös sávszélességű RTMP vagy Smooth Streaming (darabolt MP4) streamet küld az élő eseményre, amely nincs engedélyezve az élő kódolás végrehajtásához Media Services. A betöltött adatfolyamok további feldolgozás nélkül haladnak át az élő eseményeken. Ezt a metódust **áteresztőnek**nevezzük. Azt javasoljuk, hogy az élő kódoló többszörös átviteli sebességű streameket küldjön egyetlen sávszélességű adatfolyamként egy átmenő élő esemény helyett, hogy az adaptív sávszélességű adatfolyamot továbbítsa az ügyfélnek.

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

## <a name="becoming-an-on-premises-encoder-partner"></a>Helyszíni kódoló partnervé válás

Azure Media Services helyszíni kódoló partnerként a kódolót a nagyvállalati ügyfeleknek ajánljuk, Media Services népszerűsíti a terméket. Helyszíni kódoló partnernek való megfeleléshez ellenőriznie kell a helyszíni kódoló kompatibilitását Media Services használatával. Ehhez végezze el a következő ellenőrzéseket.

### <a name="pass-through-live-event-verification"></a>Áteresztő élő esemény ellenőrzése

1. A Media Services-fiókjában ellenőrizze, hogy fut-e a **folyamatos átviteli végpont** . 
2. Hozzon létre és indítsa el az **átmenő** élő eseményt. <br/> További információ: [élő események állapota és számlázása](live-event-states-billing.md).
3. Töltse le a betöltési URL-címeket, és konfigurálja a helyszíni kódolót úgy, hogy az URL-cím használatával egy többszörös sávszélességű élő streamet küldjön Media Services.
4. Szerezze be az előnézeti URL-címet, és annak ellenőrzéséhez, hogy a kódolóból érkező adatok fogadása ténylegesen megtörténik-e.
5. Hozzon létre **egy új objektum** objektumot.
6. Hozzon létre egy **élő kimenetet** , és használja a létrehozott eszköz nevét.
7. Hozzon létre egy **streaming-keresőt** a beépített **folyamatos átviteli házirend** -típusokkal.
8. A **streaming-lokátor** elérési útjának listázása a használni kívánt URL-címek visszaszerzéséhez.
9. Szerezze be annak a streaming- **végpontnak** az állomásnevét, amelyről a streamet továbbítani kívánja.
10. A teljes URL-cím lekéréséhez kombinálja a 8. lépésből álló URL-címet a 9. lépésben szereplő állomásnévvel.
11. Az élő kódolót körülbelül 10 percen belül futtathatja.
12. Állítsa le az élő eseményt. 
13. Az archivált eszközöket úgy is használhatja, mint a [Azure Media Player](https://aka.ms/azuremediaplayer) , így biztosítva, hogy a lejátszás nem tartalmaz minden minőségi szinten látható hibát. Vagy tekintse meg és ellenőrizze az előnézeti URL-cím használatával az élő munkamenet során.
14. Jegyezze fel az eszköz AZONOSÍTÓját, a közzétett streaming URL-címet az élő archívumhoz, valamint az élő kódoló beállításait és verzióját.
15. Az élő esemény állapotának alaphelyzetbe állítása az egyes minták létrehozása után.
16. Ismételje meg az 5 – 15. lépést a kódoló által támogatott összes konfiguráció esetében (az ad-jelzések, a feliratok vagy a különböző kódolási sebességek nélkül).

### <a name="live-encoding-live-event-verification"></a>Élő események élő kódolásának ellenőrzése

1. A Media Services-fiókjában ellenőrizze, hogy fut-e a **folyamatos átviteli végpont** . 
2. Hozza létre és indítsa el az élő **kódolás** élő eseményét. <br/> További információ: [élő események állapota és számlázása](live-event-states-billing.md).
3. Szerezze be a betöltési URL-címeket, és konfigurálja a kódolót úgy, hogy egyetlen sávszélességű élő streamet továbbítson Media Services.
4. Szerezze be az előnézeti URL-címet, és annak ellenőrzéséhez, hogy a kódolóból érkező adatok fogadása ténylegesen megtörténik-e.
5. Hozzon létre **egy új objektum** objektumot.
6. Hozzon létre egy **élő kimenetet** , és használja a létrehozott eszköz nevét.
7. Hozzon létre egy **streaming-keresőt** a beépített **folyamatos átviteli házirend** -típusokkal.
8. A **streaming-lokátor** elérési útjának listázása a használni kívánt URL-címek visszaszerzéséhez.
9. Szerezze be annak a streaming- **végpontnak** az állomásnevét, amelyről a streamet továbbítani kívánja.
10. A teljes URL-cím lekéréséhez kombinálja a 8. lépésből álló URL-címet a 9. lépésben szereplő állomásnévvel.
11. Az élő kódolót körülbelül 10 percen belül futtathatja.
12. Állítsa le az élő eseményt.
13. Az archivált eszközöket úgy is használhatja, mint a [Azure Media Player](https://aka.ms/azuremediaplayer) , így biztosítva, hogy a lejátszásban ne legyen látható hibák az összes minőségi szinten. Vagy tekintse meg és ellenőrizze az előnézeti URL-cím használatával az élő munkamenet során.
14. Jegyezze fel az eszköz AZONOSÍTÓját, a közzétett streaming URL-címet az élő archívumhoz, valamint az élő kódoló beállításait és verzióját.
15. Az élő esemény állapotának alaphelyzetbe állítása az egyes minták létrehozása után.
16. Ismételje meg az 5 – 15. lépést a kódoló által támogatott összes konfiguráció esetében (az ad-jelzések, a feliratok vagy a különböző kódolási sebességek nélkül).

### <a name="longevity-verification"></a>Élettartam ellenőrzése

Kövesse az [élő esemény ellenőrzésének](#pass-through-live-event-verification) megfelelő lépéseket, kivéve a 11. lépést. <br/>10 perc helyett futtassa az élő kódolót egy hétig vagy tovább. Az élő adatfolyamok időről időre (vagy archivált eszközre) való megtekintésével ellenőrizheti, hogy a lejátszás nem rendelkezik-e látható hibákkal. [Azure Media Player](https://aka.ms/azuremediaplayer)

### <a name="email-your-recorded-settings"></a>A rögzített beállítások elküldése e-mailben

Végezetül, a rögzített beállítások és az élő archiválási paraméterek elküldése e-mailben Azure Media Services a amshelp@microsoft.com értesítésként, hogy az összes önellenőrzési ellenőrzés el lett-e adva. Adja meg a kapcsolattartási adatait is a követő feladatokhoz. A folyamattal kapcsolatos bármilyen kérdéssel kapcsolatba léphet a Azure Media Services csapatával.

## <a name="next-steps"></a>Következő lépések

[Élő közvetítés a Media Services v3-val](live-streaming-overview.md)
