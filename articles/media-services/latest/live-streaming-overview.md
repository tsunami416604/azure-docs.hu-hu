---
title: Élő közvetítés áttekintése az Azure Media Services v3 szolgáltatással | Microsoft dokumentumok
description: Ez a cikk áttekintést nyújt az Azure Media Services v3 használatával történő élő streamelésről.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/18/2020
ms.author: juliako
ms.openlocfilehash: e2c4e5b6c10b06d82a1933962cb2d97e031876a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80068028"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Élő közvetítés az Azure Media Services v3-as oldalával

Az Azure Media Services lehetővé teszi, hogy élő eseményeket biztosítson ügyfeleinek az Azure-felhőben. Az élő események Media Services szolgáltatással való streameléséhez a következőkre van szükség:  

- Az élő esemény rögzítésére használt kamera.<br/>A beállítási ötletek, nézd meg [egyszerű és hordozható esemény video gear setup]( https://link.medium.com/KNTtiN6IeT).

    Ha nem fér hozzá a fényképezőgéphez, az olyan eszközök, mint a [Telestream Wirecast,](https://www.telestream.net/wirecast/overview.htm) élő hírfolyamot hozhatnak létre egy videofájlból.
- Élő videokódoló, amely a kamerából (vagy más eszközről, például laptopról) érkező jeleket a Media Services szolgáltatásnak küldött hozzájárulási hírcsatornává alakítja. A hozzájárulási hírcsatorna tartalmazhat hirdetésekhez kapcsolódó jeleket, például SCTE-35 jelölőket.<br/>Az ajánlott élő streamelési kódolók listáját az [élő streamelési kódolók között láthatja.](recommended-on-premises-live-encoders.md) Is, nézd meg ezt a blogot: [Élő streaming termelés OBS](https://link.medium.com/ttuwHpaJeT).
- A Media Services összetevői, amelyek lehetővé teszik az élő esemény betöltését, előnézetét, csomagolását, rögzítését, titkosítását és közvetítését az ügyfeleknek, vagy egy CDN-nek további terjesztés céljából.

Ez a cikk áttekintést és útmutatást nyújt a Media Services szolgáltatással való élő közvetítésről és más releváns cikkekre mutató hivatkozásokról.
 
> [!NOTE]
> Az Azure [Portal](https://portal.azure.com/) segítségével kezelheti a v3-as [élő eseményeket,](live-events-outputs-concept.md)tekintse meg a v3 [Assets nézetet,](assets-concept.md)és információkat kaphat az API-k eléréséről. Az összes többi felügyeleti feladathoz (például átalakítások és feladatok) használja a [REST API-t,](https://docs.microsoft.com/rest/api/media/)a [CLI-t](https://aka.ms/ams-v3-cli-ref)vagy a támogatott [SDK-k egyikét.](media-services-apis-overview.md#sdks)

## <a name="dynamic-packaging"></a>Dinamikus csomagolás

A Media Services segítségével kihasználhatja a [Dinamikus csomagolás](dynamic-packaging-overview.md)előnyeit, amely lehetővé teszi az élő közvetítések előnézetét és közvetítését [MPEG DASH, HLS és Smooth Streaming formátumokban](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) a szolgáltatásnak küldött hozzájárulási hírcsatornából. A megtekintők lejátszhatják az élő közvetítést bármely HLS, DASH vagy Smooth Streaming kompatibilis lejátszóval. Az [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) segítségével webes vagy mobilalkalmazásokban is közvetítheti az adatfolyamot a protokollok bármelyikében.

## <a name="dynamic-encryption"></a>Dinamikus titkosítás

A dinamikus titkosítás lehetővé teszi, hogy dinamikusan titkosítsa élő vagy igény szerinti tartalmait az AES-128 vagy a három fő digitális jogkezelési (DRM) rendszer bármelyikével: a Microsoft PlayReady, a Google Widevine és az Apple FairPlay rendszerekkel. A Media Services szolgáltatást is biztosít az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licencek hivatalos ügyfelek nek történő kézbesítéséhez. További információ: [Dinamikus titkosítás](content-protection-overview.md).

> [!NOTE]
> A Widevine a Google Inc. által nyújtott szolgáltatás, amely a Google, Inc. szolgáltatási feltételei és adatvédelmi irányelvei szerint működik.

## <a name="dynamic-manifest"></a>Dinamikus jegyzékfájl

A dinamikus szűrés a játékosoknak küldött számok, formátumok, bitráták és bemutatóidő-ablakok számának szabályozására szolgál. További információt a [szűrők és a dinamikus jegyzékek című témakörben talál.](filters-dynamic-manifest-overview.md)

## <a name="live-event-types"></a>Élő eseménytípusok

Az [élő események](https://docs.microsoft.com/rest/api/media/liveevents) az élő videóadatok betöltését és feldolgozását végzik. Az élő esemény beállítható *áthaladásra* (egy helyszíni élő kódoló több bitráta-adatfolyamot küld) vagy *élő kódolásra* (a helyszíni élő kódoló egyetlen átviteli adatfolyamot küld). A Media Services v3-as verzióját élő közvetítésével kapcsolatos részletek az [Élő események és az Élő kimenetek témakörben talál.](live-events-outputs-concept.md)

### <a name="pass-through"></a>Továbbítás

![átmenő típusú](./media/live-streaming/pass-through.svg)

Az átmenő **élő esemény**használatakor a helyszíni élő kódolóra támaszkodva több bitráta-videoadat-adatfolyamot hozhat létre, és azt az élő eseményhez való hozzájárulásként (RTMP vagy töredezett MP4 bemeneti protokoll használatával) küldi el. Az élő esemény ezután további átkódolás nélkül továbbítja a bejövő videostreameket a dinamikus csomagolóba (Streaming Endpoint). Az ilyen átmenő élő esemény hosszú ideig futó élő eseményekre vagy 24x365 lineáris élő közvetítésre van optimalizálva. 

### <a name="live-encoding"></a>Live Encoding  

![live encoding](./media/live-streaming/live-encoding.svg)

Ha felhőalapú kódolást használ a Media Services szolgáltatással, konfigurálja a helyszíni élő kódolót úgy, hogy egyetlen bitráta-videót küldjön a live esemény (RTMP vagy töredezett MP4 bemeneti protokoll) hozzájárulási hírcsatornájaként (legfeljebb 32 Mbps összesítés). A Live Event átkódolja a bejövő egybitrát stream [több bitráta videó stream](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) különböző felbontásban, hogy javítsa a kézbesítést, és elérhetővé teszi a lejátszáseszközök keresztül ipari szabvány protokollok, mint az MPEG-DASH, Apple HTTP Live Streaming (HLS), és a Microsoft Smooth Streaming. 

### <a name="live-transcription-preview"></a>Élő átírás (előzetes verzió)

Az élő transzkripció olyan funkció, amelyet olyan élő eseményekkel használhat, amelyek átmenő vagy élő kódolásúak. További információ: [live transcription](live-transcription.md). Ha ez a funkció engedélyezve van, a szolgáltatás a Cognitive Services [Beszéd-szöveg](../../cognitive-services/speech-service/speech-to-text.md) szolgáltatását használja a bejövő hang kimondott szavainak szöveggé történő átírásához. Ez a szöveg ezután elérhetővé válik a videó és audió mpeg-DASH és HLS protokollokban történő kézbesítésre.

> [!NOTE]
> Jelenleg az élő átírás az USA nyugati részén 2 előzetes funkcióként érhető el.

## <a name="live-streaming-workflow"></a>Élő közvetítési munkafolyamat

A Media Services 3-as v3-as szolgáltatásának élő streamelési munkafolyamatának megértéséhez először át kell tekintenie és meg kell értenie az alábbi fogalmakat: 

- [Végpontok streamelése](streaming-endpoint-concept.md)
- [Élő események és élő kimenetek](live-events-outputs-concept.md)
- [Streamelési lokátorok](streaming-locators-concept.md)

### <a name="general-steps"></a>Általános lépések

1. A Media Services-fiókban győződjön meg arról, hogy a **streamelési végpont** (Origin) fut. 
2. Élő [esemény](live-events-outputs-concept.md)létrehozása . <br/>Az esemény létrehozásakor megadhatja az automatikus indítást. Azt is megteheti, hogy elindítja az eseményt, amikor készen áll a streamelés megkezdésére.<br/> Ha az automatikus indítás értéke igaz, az élő esemény közvetlenül a létrehozás után indul el. A számlázás akkor kezdődik, amikor az élő esemény elindul. A további számlázás leállításához kifejezetten meg kell hívnia a Stop on the Live Event erőforrást. További információt az [Élő esemény állapotaés a számlázás](live-event-states-billing.md)című témakörben talál.
3. A betöltési URL-cím(ek) betöltése és a helyszíni kódoló konfigurálása az URL-cím használatával a hozzájárulási hírcsatorna küldéséhez.<br/>Lásd [az ajánlott élő kódolókat](recommended-on-premises-live-encoders.md).
4. Az előnézeti URL-cím beérkezésével ellenőrizheti, hogy a kódoló bemenete ténylegesen meg érkezett-e.
5. Hozzon **Asset** létre egy új eszközobjektumot. 

    Minden élő kimenet egy eszközhöz van társítva, amelyet a videó rögzítésére használ a társított Azure blob storage tárolóba. 
6. Hozzon létre egy **élő kimenetet,** és használja a létrehozott eszköznevet, hogy az adatfolyam archiválható legyen az eszközbe.

    Az élő kimenetek létrehozásakor kezdődnek, és törléskor leállnak. Az élő kimenet törlésekor nem törli a mögöttes eszközt és tartalmat az eszközben.
7. Hozzon létre egy **streamelési lokátort** a [beépített streamelési házirend-típusokkal.](streaming-policy-concept.md)

    Az élő kimenet közzétételéhez létre kell hoznia egy streamelési lokátort a társított eszközhöz. 
8. Sorolja fel az elérési utakat a **streamelési lokátor,** hogy visszaszerezze az URL-eket használni (ezek determinisztikus).
9. A **streamelési végpont** (Origin) állomásnevének beszereznie, amelyből streamelni szeretne.
10. Kombinálja a 8.
11. Ha le szeretné állítani az **élő esemény** láthatóvá tételét, le kell állítania az esemény streamelését, és törölnie kell a **Streamelési lokátort.**
12. Ha befejezte az esemény streamelését, és törölni szeretné a korábban kiosztott erőforrásokat, kövesse az alábbi eljárást.

    * Állítsa le a stream továbbítását a kódolóban.
    * Állítsa le az élő eseményt. Az élő esemény leállítása után nem számítunk fel díjat. A betöltési URL-cím nem módosul, ezért a csatorna ismételt elindításához nem szükséges újrakonfigurálni a kódolót.
    * A streamvégpontot is leállíthatja, kivéve, ha szeretné elérhetővé tenni az élő esemény archívumát igényalapú streamingre. Ha az élő esemény leállított állapotban van, nem számítunk fel díjat.

Az az eszköz, amelyaz élő kimenet archiválása, automatikusan igény szerinti eszközlesz, amikor az élő kimenet törlődik. Az élő események leállítása előtt törölnie kell az összes élő kimenetet. Használhatja a választható [jelzőremoveOutputsOnStop](https://docs.microsoft.com/rest/api/media/liveevents/stop#request-body) automatikusan eltávolítja az élő kimenetek stop. 

> [!TIP]
> Lásd: [Élő streamelési oktatóanyag](stream-live-tutorial-with-api.md), a cikk megvizsgálja a kódot, amely megvalósítja a fent leírt lépéseket.

## <a name="other-important-articles"></a>Egyéb fontos cikkek

- [Ajánlott élő kódolók](recommended-on-premises-live-encoders.md)
- [Egy felhőalapú DVR használata](live-event-cloud-dvr.md)
- [Az Élő eseménytípusok jellemzőinek összehasonlítása](live-event-types-comparison.md)
- [Állapotok és számlázás](live-event-states-billing.md)
- [Késés](live-event-latency.md)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

Tekintse meg a [gyakran ismételt kérdésekről szóló](frequently-asked-questions.md#live-streaming) cikket.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="next-steps"></a>További lépések

* [Élő közvetítés – rövid útmutató] (live-events-wirecast-quickstart.md(
* [Élő közvetítésoktató anyaga](stream-live-tutorial-with-api.md)
* [Áttelepítési útmutató a Media Services 2-es v3-as ról a 3-as vagy a 3-as](migrate-from-v2-to-v3.md)
