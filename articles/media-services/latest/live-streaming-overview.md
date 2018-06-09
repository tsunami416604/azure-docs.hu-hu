---
title: Élő adatfolyam-továbbítási az Azure Media Services áttekintése |} Microsoft Docs
description: Ez a témakör lehetőséget biztosít, áttekintést live streaming v3 Azure Media Services használatával.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/06/2018
ms.author: juliako
ms.openlocfilehash: b8c9375d8ad915200cbc8b2e1a62979fd1b7d179
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "35238283"
---
# <a name="live-streaming-with-azure-media-services-v3"></a>Az élő streamelés az Azure Media Services v3

Ha az Azure Media Services események élő adatfolyamainak továbbítása a következő összetevők általában érintett:

* Egy kamera, amely az eseményt közvetíti.
* Egy élő videókódoló, amely átalakítja a jeleket a kamera (vagy egy másik eszköz, például a hordozható) továbbítódnak a Media Services élő adatfolyam-szolgáltatásra. A jelek SCTE-35 és az Ad-jelzéssel hirdetési is tartalmazhatják. 
* A Media Services élő adatfolyam-továbbítási szolgáltatás lehetővé teszi betöltési, tekintse meg, csomag, jegyezze fel, titkosításához és szórási a tartalom az ügyfelek számára, vagy egy CDN további terjesztéshez.

Ez a cikk részletes áttekintést és diagramokat fő összetevőből Media Services élő Stream továbbítása részt tartalmaz.

## <a name="overview-of-main-components"></a>Fő összetevők áttekintése

A Media Services szolgáltatásban [LiveEvents](https://docs.microsoft.com/rest/api/media/liveevents) felelősek az élő adatfolyam-tartalmak feldolgozása. Egy LiveEvent biztosít bemeneti végpontja (feldolgozó URL-CÍMÉT), akkor adja meg a helyszíni élő kódoló. A LiveEvent bemeneti élő adatfolyamokat fogad az élő kódoló RTMP vagy Smooth Streaming formátumban, és lehetővé teszi az adatfolyamként történő egy vagy több keresztül [Streamvégpontok](https://docs.microsoft.com/rest/api/media/streamingendpoints). A [LiveOutput](https://docs.microsoft.com/en-us/rest/api/media/liveoutputs) teszi lehetővé a közzétételt, a rögzítés és a DVR Időablakok beállításai az élő adatfolyam. A LiveEvent előnézeti végpont (előzetes verzió URL-cím), amelyekkel előzetes megtekintéséhez és az adatfolyam érvényesítése előtt további feldolgozás és a szállítási is biztosít. 

A Media Services biztosít **dinamikus becsomagolás**, amelyik lehetővé teszi, és a tartalmat a MPEG DASH, HLS, Smooth Streaming adatfolyam-továbbítási formátumokba nélkül manuálisan csomagolja újra az ezekbe a formátumokba. Lejátszhat vissza a bármely HLS, DASH vagy Smooth kompatibilis játékosok. Is [Azure Media Player](http://amp.azure.net/libs/amp/latest/docs/index.html) a stream kipróbálásához.

A Media Services lehetővé teszi, hogy a dinamikusan titkosított tartalom (**a dinamikus titkosítás**) Advanced Encryption Standard (AES-128) vagy a három fő digitális tartalomvédelmi (DRM) felügyeleti rendszerek: Microsoft PlayReady, Google Widevine, és az Apple FairPlay. A Media Services is biztosít egy szolgáltatás, amelynek segítségével a AES-kulcsok és DRM hitelesített ügyfelek (PlayReady, Widevine és FairPlay) licenceket.

Igény szerint is alkalmazhat **dinamikus szűrés**, amely segítségével nyomon követi, formátumok, bitrates, amely a játékosok küldtünk a számát. A Media Services ad-beszúrási is támogatja.


## <a name="liveevent-types"></a>LiveEvent típusok

A [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) két típus egyike lehet: élő kódolás és a csatlakoztatott. 

### <a name="live-encoding-with-media-services"></a>A Media Services kódolási élő

![Élő kódolás](./media/live-streaming/live-encoding.png)

Egy helyszíni élő kódoló egy egyféle sávszélességű adatfolyamokat küld a LiveEvent, hogy engedélyezve van-e élő kódolásra Media Services a következő protokollok valamelyikével: RTMP vagy Smooth Streaming (töredezett MP4). A LiveEvent majd élő kódolás útján a bejövő egyfajta sávszélességű adatfolyamot többféle bitrátájúvá (adaptív) video-adatfolyamot. Kérés esetén a Media Services továbbítja az adatfolyamot az ügyfeleknek.

Az ilyen típusú LiveEvent létrehozásakor meg **alapvető** (LiveEventEncodingType.Basic).

### <a name="pass-through"></a>Továbbítás

![Áteresztő](./media/live-streaming/pass-through.png)

Áteresztő hosszan futó élő adatfolyamok vagy 24 x 7 lineáris élő kódolás használatával egy helyszíni élő kódoló van optimalizálva. A helyszíni kódolók többféle sávszélességű **RTMP** vagy **Smooth Streaming** (töredékes MP4) számára a konfigurált LiveEvent **áteresztő** kézbesítését. A **áteresztő** kézbesítési akkor, ha a feldolgozott adatfolyamok továbbítása **LiveEvent**s további feldolgozás nélkül. 

Áteresztő LiveEvents 4 KB-os megoldás is támogatja, és a Smooth Streaming használata esetén továbbíthatja HEVC betöltési protokollt. 

Az ilyen típusú LiveEvent létrehozásakor meg **nincs** (LiveEventEncodingType.None).

> [!NOTE]
> Valamely áteresztő módszer használata a leggazdaságosabb megoldás, ha hosszú időn át több eseményt is közvetít élő adatfolyamként, és már befektetett helyszíni kódolókba. További információt a [díjszabás](https://azure.microsoft.com/pricing/details/media-services/) nyújt.
> 

## <a name="liveevent-types-comparison"></a>LiveEvent típusok összehasonlítása 

Az alábbi táblázat összehasonlítja a két típusú LiveEvent szolgáltatásokat.

| Szolgáltatás | Áteresztő LiveEvent | Alapszintű LiveEvent |
| --- | --- | --- |
| Egyszeres sávszélességű bemeneti van kódolva több bitrates a felhőben |Nem |Igen |
| Maximális felbontás, a rétegek száma |4Kp30  |720p, 6 rétegek 30 fps |
| Bemeneti protokollok |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Ár |Tekintse meg a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/media-services/) , majd kattintson a "Live videó" lap |Tekintse meg a [árképzést ismertető oldalra](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximálisan engedélyezett futási idő |A hét minden napján, 24 órában elérhető |A hét minden napján, 24 órában elérhető |
| Táblagépükkel beszúrása támogatása |Nem |Igen |
| API-n keresztül jelzés ad támogatása|Nem |Igen |
| Az Active Directory keresztül SCTE35 sávon belül jelzés támogatása|Igen |Igen |
| Áteresztő CEA 608/708 feliratok |Igen |Igen |
| A hírcsatorna hozzájárulás a rövid leállások esetén lehetősége |Igen |Nem (LiveEvent megkezdődik slating másodperc 6 + bemeneti adatok nélkül)|
| Nem egységes bemeneti GOPs támogatása |Igen |Nem – bemeneti javítani kell a 2 mp GOPs |
| Változó keretének aránya bemeneti támogatása |Igen |Nem – bemeneti képkockasebessége kell meghatározni.<br/>Kisebb módosításokat elviseli, például magas mozgásérzékelési színfalak során. De kódoló nem dobható el, hogy 10 képkockák másodpercenkénti száma. |
| Automatikus-kikapcsolása, ha a bemeneti LiveEvent hírcsatorna elvész. |Nem |Ha nem fut LiveOutput 12 óra elteltével |

## <a name="liveevent-states"></a>LiveEvent állapotok 

Egy LiveEvent aktuális állapotát. A lehetséges értékek:

|Állapot|Leírás|
|---|---|
|**Leállt**| A létrehozása után a LiveEvent kezdeti állapotában ez (kivéve, ha az automatikus indítási jelöltek meg.) Ebben az állapotban nem számlázási következik be. Ebben az állapotban lévő LiveEvent tulajdonságait is lehet frissíteni, de streaming nem engedélyezett.|
|**Indítása**| A LiveEvent indítása folyamatban van. Ebben az állapotban nem számlázási következik be. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett. Ha hiba lép fel, a LiveEvent a leállított állapotba tér vissza.|
|**Fut**| A LiveEvent élő adatstreamek feldolgozására képes. Azt a használat számlázási most van. Le kell állítania, hogy megakadályozza a további számlázási LiveEvent.|
|**Leállítása**| A LiveEvent leáll. Ebben az átmeneti állapotban nem számlázási következik be. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.|
|**Törlése**| A LiveEvent törlése folyamatban van. Ebben az átmeneti állapotban nem számlázási következik be. Ebben az állapotban sem a frissítés, sem a streamelés nem engedélyezett.|

## <a name="liveoutput"></a>LiveOutput

A [LiveOutput](https://docs.microsoft.com/en-us/rest/api/media/liveoutputs) teszi lehetővé a közzétételt, a rögzítés és a DVR Időablakok beállításai az élő adatfolyam. A LiveEvent és LiveOutput multiplicitású hasonló a hagyományos televíziózáshoz, ahol egy csatorna (LiveEvent) rendelkezik a tartalom állandó adatfolyam és egy programot (LiveOutput) az adott LiveEvent néhány időzített esemény hatókörét.
Megadhatja, hogy hány órán át szeretné megőrizni a felvett tartalmát a LiveOutput a úgy, hogy a **ArchiveWindowLength** tulajdonság. **ArchiveWindowLength** az ISO 8601 timespan időtartama az archiválási időtartam (digitális videót író vagy DVR) van. Ez az érték 5 perc és 25 óra közötti lehet. 

**ArchiveWindowLength** is határozzák meg, hogy idő ügyfelek maximális száma is kérhet az idő az aktuális élő pozíciótól. LiveOutputs hosszabbak lehetnek a megadott időn, de a rendszer folyamatosan elveti azokat a tartalmakat, amelyek korábbiak a időtartamnál. Ez a tulajdonság azt is meghatározza, mennyi ideig növelhető az ügyfél jegyzékfájljai.

Minden egyes LiveOutput társítva van egy [eszköz](https://docs.microsoft.com/rest/api/media/assets), és egy tárolóba, a Media Services-fiókhoz csatolva az Azure Storage-adatait rögzíti. A LiveOutput közzétételéhez létre kell hoznia egy [StreamingLocator](https://docs.microsoft.com/rest/api/media/streaminglocators) a társított objektumhoz. Ez a lokátor teszi lehetővé az ügyfeleknek megadható streamelő URL-cím összeállítását.

Egy LiveEvent támogatja legfeljebb három egyidejűleg zajló LiveOutputs, így egy bejövő streamből több archívumot is létrehozhat. Ez lehetővé teszi az események különféle részeinek szükség szerinti közzétételét és archiválását. Például az üzleti igény szerint 24 x 7 élő lineáris adatcsatornára közvetíti, de ügyfelek biztosítson az igény szerinti tartalomterjesztésről utólagos megtekinthető, napjainkat programok "felvétel" létrehozásához.  Ebben a forgatókönyvben, először létre kell hoznia egy elsődleges LiveOutput, 1 órás archiválási rövid ablakot vagy kevesebb, az ügyfelek számára az elsődleges élő adatfolyamként történő hangolását. Ehhez hozzon létre egy StreamingLocator a LiveOutput, és az alkalmazás vagy webhely, mint a "Live" adatcsatorna történő közzétételhez.  Az adatcsatorna fut, mint programozott módon létrehozhat egy második egyidejű LiveOutput egy megjelenítése (vagy arra, hogy néhány leírók 5 perccel lehet levágni a későbbi.) kezdetén A második LiveOutput 5 perc, a program vagy az esemény-előfizetés lejárta után állítható le, és ezután létrehozhat egy új StreamingLocator a program közzététele az alkalmazáskatalógusban igény eszközként.  Az eljárás megismétlésével többször más program határ jelölőnégyzetét, vagy azokat a emeli ki, hogy igény szerint azonnal megosztani kívánt, miközben a "Live" hírcsatornát, az első LiveOutput lineáris adatcsatorna szórási továbbra is.  Ezenkívül kihasználhatja a dinamikus szűrő támogatás vághatja a head és kiegészítő az archívum a LiveOutput tartalmazza, akkor a "hozzon létre biztonsági" a programok között, és egy pontosabb kezdő és záró a tartalom eléréséhez. Archivált tartalmat is küldheti el egy [átalakítási](https://docs.microsoft.com/rest/api/media/transforms) kódolás vagy keret pontos subclipping több kimeneti formátumban, más szolgáltatások szindikálási használható.

## <a name="streamingendpoint"></a>StreamingEndpoint

Miután elvégezte a stream, a LiveEvent összekapcsolását, megkezdheti a streamelési eseményt egy eszköz, LiveOutput és StreamingLocator létrehozásával. Ezzel archiválja a streamet, és tegye elérhetővé segítségével megjelenítőként a [StreamingEndpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints).

A Media Services-fiók létrehozásakor egy alapértelmezett streamvégpontból bekerül a fiók a leállított állapotban. Indítsa el a tartalom streaming és a dinamikus csomagolás és a dinamikus titkosítás, a streamvégpontján, amelyből el kívánja adatfolyamot-nek a futó állapotú.

## <a name="billing"></a>Számlázás

Egy LiveEvent megkezdődik a számlázás, amint állapotában átmenetek "Fut". A számlázási LiveEvent leállítani, akkor állítsa le a LiveEvent.

> [!NOTE]
> Ha **LiveEventEncodingType** a a [LiveEvent](https://docs.microsoft.com/rest/api/media/liveevents) be van állítva a Basic, a Media Services fog automatikus gyors bármely LiveEvent, amely továbbra is szerepel a "Fut" állapotú 12 óra után a bemeneti hírcsatorna elvész, és nincsenek nem LiveOutputs futnak. Azonban továbbra is a számlázás történik a "Fut" állapotú volt a LiveEvent alkalommal.
>

Az alábbi táblázat bemutatja, hogyan LiveEvent állapotok hozzárendelését a számlázási módot.

| LiveEvent állapota | Ennyi az egész számlázási? |
| --- | --- |
| Indítás |Nem (átmeneti állapot) |
| Fut |IGEN |
| Leállítás |Nem (átmeneti állapot) |
| Leállítva |Nem |

## <a name="next-steps"></a>További lépések

[Élő adatfolyam-továbbítási oktatóanyag](stream-live-tutorial-with-api.md)
