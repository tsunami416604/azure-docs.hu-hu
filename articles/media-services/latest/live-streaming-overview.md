---
title: Az Azure Media Services segítségével élő Streamelés áttekintése |} A Microsoft Docs
description: Ez a témakör nyújt áttekintést élő adatfolyam továbbítása az Azure Media Services v3 használatával.
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
ms.date: 10/16/2018
ms.author: juliako
ms.openlocfilehash: 533aa505c38d3cbfb46d70acecd43cc66614b13d
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378136"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Élő Stream a az Azure Media Services v3

Ha az Azure Media Services események élő adatfolyamainak továbbítása a következő összetevőket gyakran játszik szerepet:

* Egy kamera, amely az eseményt közvetíti.
* Egy élő videókódoló, amely átalakítja a jeleket a kamerához (vagy egy másik eszköz, például a hordozható számítógépen) a Lve Streamelési szolgáltatásnak küldött Streamek. A jeleket hirdetési SCTE – 35 és Ad-kötegek is tartalmazhat. 
* A Media Services élő Streamelési szolgáltatás lehetővé teszi a betöltési, előzetes verzió, csomag, jegyezze fel, titkosítására, és a tartalom az ügyfelek részére, vagy egy CDN-re további terjesztés céljából.

Ez a cikk részletes áttekintést nyújt, és fő összetevőit az élő adások online közvetítése a Media Services részt ábrákért tartalmazza.

## <a name="overview-of-main-components"></a>Fő összetevőinek áttekintése

A Media Services szolgáltatásban a [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) események feladata a streamelt élő tartalmak feldolgozása. A videókhoz bemeneti végpontot biztosít (betöltési URL-címe), majd adja át a helyszíni élő kódoló. A videókhoz RTMP vagy Smooth Streaming formátumban az élő kódoló a bemeneti élő Streamek fogad, és lehetővé teszi egy vagy több keresztül [Streamvégpontok](https://docs.microsoft.com/rest/api/media/streamingendpoints). A [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) segítségével szabályozható a közzététel, rögzítése és DVR Időablakok beállításai az élő adatfolyam. A videókhoz is biztosít egy előzetes verziójú végpont (előzetes verzió URL-cím), amellyel és ellenőrzéséhez a stream előnézetének a feldolgozás folytatása és a továbbítás előtti. 

A Media Services modullal járulékos konfigurálás **dinamikus csomagolási**, amely lehetővé teszi, hogy előzetes verzió és a tartalom MPEG DASH, HLS, Smooth Streaming adatfolyam-továbbítási formátumokba anélkül manuálisan csomagolni ezekbe a streamformátumokba. Kísérletezhet vissza bármely HLS, DASH vagy Smooth kompatibilis lejátszók. Is [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) a stream kipróbálásához.

A Media Services lehetővé teszi, hogy a tartalom, dinamikusan titkosítja (**a dinamikus titkosítás**) Advanced Encryption Standard (AES-128) vagy a három fő digitális jogkezelési (technológia DRM) felügyeleti rendszerek bármelyike: a Microsoft PlayReady, A Google Widevine, és az Apple fairplay által. Media Services is biztosít a modult az AES-kulcsok és a DRM (PlayReady, Widevine és FairPlay) licenceket az arra jogosult ügyfelek.

Igény szerint is alkalmazhat **dinamikus szűrés**, amely nyomon követi, formátumát, bitsebességre való átkódolása, a játékosok megismerése által küldött meg, hány használható. A Media Services-beillesztését is támogatja.

### <a name="new-live-encoding-improvements"></a>Új, élő kódolási fejlesztései

A következő új fejlesztéseket elkészült, a legújabb kiadásban.

- Az új közel valós idejű módban élő (10 másodperc – végpontok).
- Továbbfejlesztett RMTP-támogatása (fokozott stabilitás és további forráskód kódoló).
- Biztonságos RTMPS betöltését.

    Amikor létrehoz egy mostantól videókhoz 4 betöltési URL-címeket. A 4 betöltési URL-címek olyan majdnem teljesen megegyezik, rendelkezik a azonos streamelési token (alkalmazásazonosító), csak a port száma rész nem egyezik. Az URL-címek kettő elsődleges és tartalék RTMPS számára.   
- 24 órás átkódolása támogatása. 
- Továbbfejlesztett támogatást ad jelzés RTMP SCTE35 keresztül.

## <a name="liveevent-types"></a>Videókhoz típusok

A [videókhoz](https://docs.microsoft.com/rest/api/media/liveevents) két típus egyike lehet: valós idejű kódolás és a csatlakoztatott. 

### <a name="live-encoding-with-media-services"></a>Valós idejű kódolás a Media Services használatával

![Live encoding](./media/live-streaming/live-encoding.png)

A helyszíni élő kódoló egy egyféle sávszélességű adatfolyamot küld a videókhoz, amelyen engedélyezve van a valós idejű kódolás a Media Services a következő protokoll: RTMP vagy Smooth Streaming (darabolt MP4). Majd elvégzi a videókhoz, valós idejű kódolás a bejövő egyfajta sávszélességű adatfolyamot többféle sávszélességű (adaptív) video-adatfolyamot. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

Az ilyen típusú videókhoz létrehozásakor adja meg a **alapszintű** (LiveEventEncodingType.Basic).

### <a name="pass-through"></a>Továbbítás

![az átmenő](./media/live-streaming/pass-through.png)

Az átmenő hosszú ideig futó élő Streamek vagy 24 x 7 lineáris valós idejű kódolás helyszíni élő kódoló használatával van optimalizálva. A helyszíni kódolót küld többszörös átviteli sebességű **RTMP** vagy **Smooth Streaming** (töredékes MP4), amely konfigurálva van a videókhoz való **átmenő** kézbesítési. A **átmenő** kézbesítési akkor, ha a feldolgozott adatfolyamok haladnak keresztül **videókhoz**s további feldolgozás nélkül. 

Átmenő LiveEvents 4K megoldási is támogatja, és HEVC halad keresztül való együttes Smooth Streaming betöltési protokollt. 

Az ilyen típusú videókhoz létrehozásakor adja meg a **None** (LiveEventEncodingType.None).

> [!NOTE]
> Valamely áteresztő módszer használata a leggazdaságosabb megoldás, ha hosszú időn át több eseményt is közvetít élő adatfolyamként, és már befektetett helyszíni kódolókba. További információt a [díjszabás](https://azure.microsoft.com/pricing/details/media-services/) nyújt.
> 

## <a name="liveevent-types-comparison"></a>Videókhoz típusok összehasonlítása 

Az alábbi táblázat a videókhoz kétféle funkcióit hasonlítja össze.

| Szolgáltatás | Az átmenő videókhoz | Alapszintű videókhoz |
| --- | --- | --- |
| Egyféle sávszélességű bemeneti bitsebességekre a felhőben van kódolva. |Nem |Igen |
| Maximális felbontás, a rétegek száma |4Kp30  |720p, 6 rétegek 30 képkocka/s |
| A bemeneti protokollok |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Ár |Tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/media-services/) , majd kattintson a "Élő videó" lap |Tekintse meg a [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximálisan engedélyezett futási idő |A hét minden napján, 24 órában elérhető |A hét minden napján, 24 órában elérhető |
| Befutók beillesztésének támogatását |Nem |Igen |
| API-n keresztül jelzés ad támogatása|Nem |Igen |
| Ad-n keresztül SCTE35 inband jelzés támogatása|Igen |Igen |
| Az átmenő CEA 608/708 feliratok |Igen |Igen |
| Lehetővé teszi a csatorna közreműködői rövid leállások helyreállítása |Igen |Nem (videókhoz elkezdi slating másodperc 6 + bemeneti adatok nélkül)|
| Nem egységes bemeneti GOPs támogatása |Igen |Nem – bemeneti ki kell javítani GOPs. 2 mp |
| Változó keret arány bevitel támogatása |Igen |Nem – bemeneti képkockasebessége kell rögzíteni.<br/>Kisebb módosításokat kívánalmakhoz, például magas mozgásban lévő adatoknak egyaránt jelenetek során. De kódoló nem dobható el, a 10 képkockák másodpercenkénti száma. |
| Automatikus – gyors, amikor a bevitel videókhoz hírcsatorna elvész. |Nem |Ha nem fut LiveOutput 12 óra elteltével |

## <a name="liveevent-states"></a>Videókhoz állapotok 

A videókhoz aktuális állapotát. A lehetséges értékek:

|Állapot|Leírás|
|---|---|
|**Leállítva**| Ez a videókhoz kezdeti állapotát a létrehozása után (kivéve, ha az autostart kiválasztott meg.) Ebben az állapotban nem számlázási akkor fordul elő. Ebben az állapotban a videókhoz tulajdonságai frissíthetők, de a streamelés nem engedélyezett.|
|**Indítása**| A videókhoz indítása folyamatban van. Ebben az állapotban nem számlázási akkor fordul elő. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett. Ha hiba történik, a videókhoz leállított állapotra adja vissza.|
|**Fut**| A videókhoz élő Streamek feldolgozására alkalmas állapotban. Azt a használat számlázási most már van. A videókhoz további számlázás elkerülése érdekében le kell állítania.|
|**Leállítása**| A videókhoz leállítás alatt van. Nincs számlázási ez átmeneti állapot történik. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.|
|**Törlése**| A videókhoz törlése folyamatban van. Nincs számlázási ez átmeneti állapot történik. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.|

## <a name="liveoutput"></a>LiveOutput

A [LiveOutput](https://docs.microsoft.com/rest/api/media/liveoutputs) segítségével szabályozható a közzététel, rögzítése és DVR Időablakok beállításai az élő adatfolyam. A videókhoz és LiveOutput kapcsolat hasonlít a hagyományos televíziózáshoz, ahol egy csatornát (videókhoz) tartalmaz egy állandó stream a tartalom és (LiveOutput) programot az adott videókhoz néhány időzített esemény hatókörét.
Megadhatja, hogy meg szeretné őrizni a felvett tartalmát a LiveOutput vonatkozó beállításával órák számát az **ArchiveWindowLength** tulajdonság. **ArchiveWindowLength** egy ISO 8601 timespan időtartama, az archiválási időszak hossza (digitális videót Recorder vagy DVR). Ez az érték 5 perc és 25 óra közötti lehet. 

**ArchiveWindowLength** is engedélyezi az ügyfelek maximális számát is kérhet időbeli az aktuális élő pozíciótól futtassanak. LiveOutputs futtathatja a megadott időn keresztül, de az időszak hossza mögé helyezett tartalmat a rendszer folyamatosan elveti. Ez a tulajdonság azt is meghatározza, mennyi az ügyfél jegyzékfájljai.

Minden egyes LiveOutput társítva van egy [eszköz](https://docs.microsoft.com/rest/api/media/assets), és a egy tárolóba, a Media Services-fiókhoz csatolva az Azure Storage-adatait rögzíti. A LiveOutput közzétételéhez létre kell hoznia egy [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) a társított objektumhoz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

A videókhoz legfeljebb három egyidejűleg zajló LiveOutputs támogatja, így egy bejövő streamből több archívumot is létrehozhat. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Például az üzleti igény szerint a közvetítést élő lineáris hírcsatorna 24 x 7, de szeretne létrehozni az ügyfeleknek kínált igény szerinti tartalom utólagos megtekintésre a nap folyamán programok "felvételek".  Ebben a forgatókönyvben, először hozzon létre egy elsődleges LiveOutput egy rövid archiválási ablak 1 óra vagy kevesebb, az ügyfelek számára az elsődleges élő adatfolyamként történő tökéletesítéséhez. Hozzon létre egy StreamingLocator ez LiveOutput kívánja, majd közzéteszi az alkalmazás vagy webhely, a "Live" hírcsatorna.  Mivel a hírcsatorna fut, programozott módon létrehozhat egy második egyidejű LiveOutput elején show (vagy biztosít néhány leírók 5 perccel később az azokat.) A második LiveOutput 5 perc után a program vagy az esemény véget ér állítható le, és ezt követően létrehozhat egy új StreamingLocator közzétenni ezt a programot az alkalmazáskatalógusban igény szerinti eszközként.  Megismételheti ezt a folyamatot többször is feldolgozza a más program határok vagy emeli ki, hogy azonnali, igény szerinti megosztani kívánt, miközben a "Live", az első LiveOutput hírcsatorna továbbra is a lineáris hírcsatorna szórási.  Ezenkívül a dinamikus szűrő támogatás vághatja a fő- és tail az archívum a LiveOutput, amely a "átfedés safety" bevezetni, a programok között, és a egy pontosabb elejéről és végéről, a tartalom eléréséhez is igénybe vehet. Archivált tartalmat is küldheti el egy [átalakítása](https://docs.microsoft.com/rest/api/media/transforms) és pontos részklipkészítés keret több kimeneti formátumba, más szolgáltatások szindikálási használható.

## <a name="streamingendpoint"></a>Streamvégpontok

Ha elvégezte a stream és a LiveEvent összekapcsolását, elindíthatja a streamelési eseményt. Ehhez létre kell hoznia egy adategységet, egy LiveOutputot és egy StreamingLocatort. Ezzel archiválja a streamet, és tegye elérhetővé a nézők keresztül a [Streamvégpontok](https://docs.microsoft.com/rest/api/media/streamingendpoints).

A Media Services-fiók létrehozásakor egy alapértelmezett streamvégpontot a fiókhoz leállítva állapotban van hozzáadva. A tartalom streamelésének megkezdéséhez, és a dinamikus csomagolás és a dinamikus titkosítás kihasználásához, a tartalomstreameléshez használt streamvégpont, ahonnan tartalomstreameléshez rendelkezik, a futó állapotot.

## <a name="billing"></a>Számlázás

A videókhoz kezdődik, számlázási, amint állapotában "Fut" értékre vált. Le szeretné állítani a számlázás a videókhoz, akkor állítsa le a videókhoz.

> [!NOTE]
> Amikor **LiveEventEncodingType** a a [videókhoz](https://docs.microsoft.com/rest/api/media/liveevents) van beállítva a Basic, a Media Services fog automatikus gyors bármely videókhoz, amelyek továbbra is a "Fut" állapotban van 12 óra után a bemeneti hírcsatorna elvész, és nincsenek nem LiveOutputs futtatását. Azonban továbbra is díjköteles alkalommal a "Fut" állapotban volt a videókhoz.
>

Az alábbi táblázat bemutatja, hogyan videókhoz állapotok leképezése a számlázási mód.

| Videókhoz állapota | Ennyi az egész számlázás? |
| --- | --- |
| Indítás |Nem (átmeneti állapot) |
| Fut |IGEN |
| Leállítás |Nem (átmeneti állapot) |
| Leállítva |Nem |

## <a name="next-steps"></a>További lépések

[Élő adatfolyam-továbbítási oktatóanyag](stream-live-tutorial-with-api.md)
