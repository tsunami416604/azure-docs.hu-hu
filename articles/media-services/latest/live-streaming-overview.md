---
title: Az élő közvetítés áttekintése Azure Media Services v3-vel | Microsoft Docs
description: Ez a cikk áttekintést nyújt az élő közvetítésről Azure Media Services v3 használatával.
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
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: 5883c1aa20af106dd39bffc95036ee90f312ffea
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051589"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Élő közvetítés a Azure Media Services v3-val

A Azure Media Services lehetővé teszi, hogy élő eseményeket nyújtson az ügyfeleknek az Azure-felhőben. Az élő események Media Servicessal való továbbításához a következőkre lesz szüksége:  

- Az élő esemény rögzítésére szolgáló kamera.<br/>A beállítási ötletekért tekintse meg az [egyszerű és a hordozható eseményekre vonatkozó videós eszközöket]( https://link.medium.com/KNTtiN6IeT).

    Ha nem rendelkezik hozzáféréssel egy kamerához, az eszközök (például a [Wirecast](https://www.telestream.net/wirecast/overview.htm) ) segítségével élő hírcsatornát hozhatnak elő a videofájl használatával.
- Egy élő videó kódoló, amely egy kamerából (vagy egy másik eszközről, például egy laptopból) származó jeleket alakít át egy Media Servicesba küldendő közreműködői adatcsatornába. A hozzájárulási hírcsatorna tartalmazhat a hirdetésekkel kapcsolatos jeleket, például a SCTE-35 jelölőket.<br/>A javasolt élő adatfolyam-kódolók listáját lásd: [élő adatfolyam](recommended-on-premises-live-encoders.md)-kódolók. Továbbá tekintse meg ezt a blogot: [Élő streaming-termelés a OBS](https://link.medium.com/ttuwHpaJeT)-mel.
- A Media Services összetevői, amelyek lehetővé teszik az élő események betöltését, beolvasását, becsomagolását, rögzítését, titkosítását és továbbítását az ügyfelek számára, illetve a CDN-t további terjesztés céljából.

Ez a cikk áttekintést nyújt az élő közvetítésről Media Services és más kapcsolódó cikkekre mutató hivatkozásokat tartalmaz.
 
> [!NOTE]
> Jelenleg az Azure Portal használatával nem felügyelheti a v3 verziójú erőforrásokat. Használja a [REST API-t](https://aka.ms/ams-v3-rest-ref), a [parancssori felületet](https://aka.ms/ams-v3-cli-ref) vagy valamelyik támogatott [SDK-t](media-services-apis-overview.md#sdks).

## <a name="dynamic-packaging"></a>Dinamikus csomagolás

A Media Services segítségével kihasználhatja a [dinamikus csomagolás](dynamic-packaging-overview.md)előnyeit, így az élő streameket az MPEG Dash-ben [, a HLS-ban Smooth streaming és](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) a szolgáltatásba küldött közreműködői adatcsatornán keresztül is megtekintheti és közvetítheti. A megtekintők az élő streamet bármilyen HLS, KÖTŐJELtel vagy Smooth Streaming kompatibilis játékossal játszhatják fel. A webes vagy mobil alkalmazásaiban [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html) használhatja az adatfolyam továbbítására ezen protokollok bármelyikében.

## <a name="dynamic-encryption"></a>Dinamikus titkosítás

A dinamikus titkosítás lehetővé teszi, hogy az élő vagy igény szerinti tartalmakat az AES-128 vagy a három nagyobb digitális jogkezelési (DRM) rendszerű rendszeren keresztül dinamikusan titkosítsa: Microsoft PlayReady, Google Widevine és Apple FairPlay. Media Services is biztosít a modult az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licenceket az arra jogosult ügyfelek. További információ: [dinamikus titkosítás](content-protection-overview.md).

## <a name="dynamic-manifest"></a>Dinamikus jegyzékfájl

A dinamikus szűréssel szabályozható a játékosok számára eljuttatott zeneszámok, formátumok, bitráták és a megjelenítési idő Windows-száma. További információ: [szűrők és dinamikus jegyzékfájlok](filters-dynamic-manifest-overview.md).

## <a name="live-event-types"></a>Élő események típusai

Az [élő események](https://docs.microsoft.com/rest/api/media/liveevents) az élő videóadatok betöltését és feldolgozását végzik. Az élő esemény a következő két típus egyike lehet: átmenő és élő kódolás. A Media Services v3 élő közvetítésével kapcsolatos részletekért lásd: [élő események és élő kimenetek](live-events-outputs-concept.md).

### <a name="pass-through"></a>Továbbítás

![átmenő típusú](./media/live-streaming/pass-through.svg)

A továbbítás **élő eseményének**használatakor a helyszíni élő kódoló több bitráta-videó stream létrehozásához és az élő eseményhez való hozzájáruláshoz (RTMP vagy darabolt MP4 bemeneti protokoll használatával) küldje el. Az élő esemény ezután a bejövő videó streameket a dinamikus csomagolóba (streaming Endpoint) továbbítja a további átkódolás nélkül. Egy ilyen átmenő élő esemény a hosszan futó élő eseményekre vagy 24x365 lineáris élő közvetítésre van optimalizálva. 

### <a name="live-encoding"></a>Live Encoding  

![live encoding](./media/live-streaming/live-encoding.svg)

Ha Media Services használatával Felhőbeli kódolást használ, a helyszíni élő kódolót úgy kell konfigurálnia, hogy egyetlen bitráta-videót küldjön az élő eseményhez (az RTMP vagy a darabolt MP4 bemeneti protokoll használatával). Az élő esemény átkódolja a bejövő egyszeri sávszélességű adatfolyamot [több bitrátás videó streamre](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming) különböző felbontások mellett, így javíthatja a továbbítást, és elérhetővé teszi a lejátszási eszközök számára az iparági szabványnak megfelelő protokollok (például MPEG-Dash, Apple HTTP Live Streaming (HLS) és a Microsoft Smooth Streaming. 

## <a name="live-streaming-workflow"></a>Élő adatfolyam-továbbítási munkafolyamat

Az Media Services v3 élő közvetítési munkafolyamatának megismeréséhez először tekintse át és Ismerje meg a következő fogalmakat: 

- [Streamvégpontok](streaming-endpoint-concept.md)
- [Élő események és élő kimenetek](live-events-outputs-concept.md)
- [Streamelési lokátorok](streaming-locators-concept.md)

### <a name="general-steps"></a>Általános lépések

1. Győződjön meg arról, hogy a Media Services-fiókban fut a **folyamatos átviteli végpont** (forrás). 
2. Hozzon létre egy [élő eseményt](live-events-outputs-concept.md). <br/>Az esemény létrehozásakor megadhatja az automatikus indítást. Azt is megteheti, hogy elindíthatja az eseményt, amikor készen áll a folyamatos átvitelre.<br/> Ha az Automatikus indítás értéke TRUE (igaz), akkor az élő esemény a létrehozás után rögtön elindul. A számlázás azonnal elindul, amint az élő esemény elindul. A további számlázás leállításához explicit módon hívnia kell az élő esemény erőforrásának leállítását. További információ: [élő események állapota és számlázása](live-event-states-billing.md).
3. Töltse le a betöltési URL-címeket, és konfigurálja a helyszíni kódolót úgy, hogy az URL-cím használatával küldje el a hozzájárulási csatornát.<br/>Lásd: [ajánlott élő kódolók](recommended-on-premises-live-encoders.md).
4. Szerezze be az előnézeti URL-címet, és annak ellenőrzéséhez, hogy a kódolóból érkező adatok fogadása ténylegesen megtörténik-e.
5. Hozzon létre egy új objektum objektumot. 

    Minden élő kimenet egy adategységhez van társítva, amelyet a videó a társított Azure Blob Storage-tárolóba való rögzítéséhez használ. 
6. Hozzon létre egy **élő kimenetet** , és használja a létrehozott eszköz nevét, hogy az adatfolyam archiválható legyen az objektumba.

    Az élő kimenetek a létrehozás után kezdődnek, és a törléskor leállnak. Ha törli az élő kimenetet, nem törli az objektum mögöttes eszközét és tartalmát.
7. Hozzon létre egy **streaming** -keresőt a [beépített folyamatos átviteli házirend](streaming-policy-concept.md)-típusokkal.

    Az élő kimenet közzétételéhez létre kell hoznia egy folyamatos átviteli lokátort a társított objektumhoz. 
8. A **streaming-lokátor** elérési útjának listázása a használni kívánt URL-címek visszaszerzéséhez (ezek a determinisztikus).
9. Szerezze be annak a **streaming** -végpontnak (forrásnak) az állomásnevét, amelyről a streamet szeretné továbbítani.
10. A teljes URL-cím lekéréséhez kombinálja a 8. lépésből származó URL-címet a 9. lépésben szereplő állomásnévvel.
11. Ha szeretné megszüntetni az **élő esemény** megkeresését, le kell állítania az eseményt, és törölnie kell a **folyamatos átviteli lokátort**.
12. Ha befejezte az esemény streamelését, és törölni szeretné a korábban kiosztott erőforrásokat, kövesse az alábbi eljárást.

    * Állítsa le a stream továbbítását a kódolóban.
    * Állítsa le az élő eseményt. Az élő esemény leállítását követően nem számítunk fel díjat. A betöltési URL-cím nem módosul, ezért a csatorna ismételt elindításához nem szükséges újrakonfigurálni a kódolót.
    * A streamvégpontot is leállíthatja, kivéve, ha szeretné elérhetővé tenni az élő esemény archívumát igényalapú streamingre. Ha az élő esemény leállított állapotban van, nem számítunk fel díjat.

Az élő esemény automatikusan átalakítja az eseményeket az igény szerinti tartalomba, ha leállt. Ha már leállította és törölte is az eseményt, a felhasználók igény szerinti videóként le tudják játszani az archivált tartalmat mindaddig, amíg az objektumot nem törli. Olyan objektumot nem lehet törölni, amelyet használ egy esemény. Először az eseményt kell törölni.

> [!TIP]
> Lásd: [élő közvetítés oktatóanyaga](stream-live-tutorial-with-api.md), a cikk megvizsgálja a fent ismertetett lépéseket megvalósító kódot.

## <a name="other-important-articles"></a>Egyéb fontos cikkek

- [Ajánlott élő kódolók](recommended-on-premises-live-encoders.md)
- [Egy felhőalapú DVR használata](live-event-cloud-dvr.md)
- [Élő események típusai szolgáltatás összehasonlítása](live-event-types-comparison.md)
- [Állapotok és számlázás](live-event-states-billing.md)
- [Késés](live-event-latency.md)

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>További lépések

* [Élő közvetítés – oktatóanyag](stream-live-tutorial-with-api.md)
* [Áttelepítési útmutató Media Services v2-ről v3-re való áthelyezéshez](migrate-from-v2-to-v3.md)
