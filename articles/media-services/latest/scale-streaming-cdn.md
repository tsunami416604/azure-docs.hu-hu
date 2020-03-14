---
title: Streaming skálázás a CDN-vel
titleSuffix: Azure Media Services
description: Ismerkedjen meg egy folyamatos átviteli szolgáltatással, amely közvetlenül az ügyfél vagy egy Content Delivery Network (CDN) számára biztosítja a tartalmat.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2020
ms.author: juliako
ms.openlocfilehash: e918f7ee64d4bc49d5da80bf9a3e7595555296dc
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79203695"
---
# <a name="scaling-streaming-with-cdn"></a>Streamelés méretezése CDN-nel

Az Azure tartalomkézbesítési hálózat CDN a tartalmakat világszerte, stratégiai alapon elhelyezett fizikai csomópontokon gyorsítótárazva globális megoldást kínál a fejlesztők számára a tartalmak nagy sávszélességű gyors kézbesítéséhez a felhasználók felé.  

A CDN gyorsítótárba helyezi a tartalmat egy Media Services [streaming Endpoint (forrás)](streaming-endpoint-concept.md) alapján, adatfolyam-protokoll, bitráta, tároló formátum és TITKOSÍTÁS/DRM használatával. A codec-Streaming Protocol-Container Format-bitráta-encryption minden kombinációjára külön CDN-gyorsítótár lesz. 

A népszerű tartalmat a rendszer közvetlenül a CDN-gyorsítótárból fogja kiszolgálni, feltéve, hogy a videó töredékét gyorsítótárazza. Az élő tartalom valószínűleg gyorsítótárazva lesz, mert általában sok ember nézi pontosan ugyanazt a dolgot. Az igény szerinti tartalom egy kicsit bonyolultabb lehet, mert olyan tartalmakat is használhat, amelyek népszerűek és nem. Ha több millió videós eszközt is tartalmaz, ahol egyikük sem népszerű (csak egy vagy két nézők hetente), de több ezer ember nézi a különböző videókat, a CDN sokkal kevésbé lesz hatékony. 

Azt is meg kell fontolnia, hogyan működik az adaptív adatfolyam. A rendszer minden egyes videó részletét gyorsítótárazza saját entitásként. Képzelje el például, hogy az első alkalommal figyel egy bizonyos videót. Ha a megjelenítő kihagyja a körülbelül néhány másodpercet, és ott van, csak a videó töredékek vannak társítva a CDN-ben a megfigyelt személyhez. Az adaptív streaming esetében általában 5 – 7 különböző videó-bitrátát használhat. Ha egy személy figyeli az egyik bitrátát, és egy másik személy más bitrátát figyel, akkor a CDN-ben külön-külön vannak gyorsítótárazva. Még akkor is, ha két személy nézi ugyanazt a sávszélességet, különböző protokollokon keresztül lehet adatfolyamként szolgálni. Az egyes protokollokat (HLS, MPEG-DASH, Smooth Streaming) külön gyorsítótárazza a rendszer. Így az egyes bitráták és protokollok külön vannak gyorsítótárazva, és csak a kért videó-töredékek vannak gyorsítótárazva.

Annak eldöntése során, hogy engedélyezi-e a CDN-t a Media Services [streaming végponton](streaming-endpoint-concept.md), vegye figyelembe a várt megjelenítők számát. A CDN csak akkor segít, ha sok megjelenítőt kíván a tartalomra számítani. Ha a megtekintők maximális párhuzamossága 500-nál kisebb, javasoljuk, hogy tiltsa le a CDN-t, mivel a CDN a legjobban a párhuzamosságot követi. 

Ez a témakör a [CDN-integráció](#enable-azure-cdn-integration)engedélyezését ismerteti. Ismerteti továbbá az előzetes beolvasást (az aktív gyorsítótárazást) és az [Origin-Assist CDN-prefektusi](#origin-assist-cdn-prefetch) koncepciót.

## <a name="considerations"></a>Megfontolások

* A [streaming endpoint](streaming-endpoint-concept.md) `hostname` és a folyamatos átviteli URL-cím ugyanaz marad, függetlenül attól, hogy engedélyezi-e a CDN-t.
* Ha a tartalmat CDN használatával vagy anélkül szeretné tesztelni, hozzon létre egy másik, CDN-t nem támogató streaming-végpontot.

## <a name="enable-azure-cdn-integration"></a>Azure CDN integráció engedélyezése

> [!IMPORTANT]
> A CDN nem engedélyezhető próbaverziós vagy tanulói Azure-fiókokhoz.
>
> A CDN-integráció az összes Azure-adatközpontban engedélyezve van, kivéve a szövetségi kormányzati és a kínai régiókat.

Ha egy streaming-végpontot engedélyeztek a CDN szolgáltatással, akkor a Media Services várakozási idő van a DNS-frissítés végrehajtása előtt, hogy leképezje a folyamatos átviteli végpontot a CDN-végpontra.

Ha később le kívánja tiltani/engedélyezni szeretné a CDN-t, a folyamatos átviteli végpontnak **leállított** állapotban kell lennie. Akár két óráig is eltarthat, amíg a Azure CDN integrációja engedélyezve lesz, és a módosítások az összes CDN-pop-ban aktívak lesznek. Az adatfolyam-végpontot és az adatfolyamot megszakítások nélkül is elindíthatja a folyamatos átviteli végponton, és az integráció befejezése után a stream a CDN-ből lesz továbbítva. A kiépítési időszak alatt a folyamatos átviteli végpont a **kezdeti** állapotba kerül, és megfigyelheti a teljesítmény romlását.

A standard folyamatos átviteli végpont létrehozásakor a rendszer alapértelmezés szerint a standard Verizon-t konfigurálja. A Premium Verizon vagy a standard szintű Akamai-szolgáltatók a REST API-k használatával konfigurálhatók.

A Azure Media Services integrációja Azure CDN-mel a Verizon standard streaming-végpontok **Azure CDNján** valósul meg. A prémium szintű streaming végpontok az összes **Azure CDN árképzési szint és szolgáltató**használatával konfigurálhatók. 

> [!NOTE]
> A Azure CDNről a [CDN áttekintése](../../cdn/cdn-overview.md)című témakörben olvashat bővebben.

## <a name="determine-if-a-dns-change-was-made"></a>Annak megállapítása, hogy történt-e DNS-módosítás

Megadhatja, hogy a rendszer a DNS-módosítást egy streaming végponton hajtotta-e végre (a forgalom a Azure CDNra van irányítva) https://www.digwebinterface.comhasználatával. Ha a azureedge.net tartományneveket lát az eredmények között, a forgalom most a CDN-re mutat.

## <a name="origin-assist-cdn-prefetch"></a>Forrás – Assist CDN – prefektus

A CDN-gyorsítótárazás egy reaktív folyamat. Ha a CDN megjósolhatja a következő objektum kérését, a CDN proaktív módon kérheti le és gyorsítótárazhatja a következő objektumot. Ezzel a folyamattal elérheti az objektumok összes (vagy legtöbb) gyorsítótár-találatát, így javíthatja a teljesítményt.

Az előhívási elv arra törekszik, hogy az objektumokat az Internet peremén helyezi el, és azt szeretné, hogy a játékos hamarosan kéri ezeket a kéréseket, így csökkentve az adott objektumnak a lejátszóra való továbbításának idejét.

Ennek a célnak a megvalósításához a streaming-végpontnak (forrás) és a CDN-nek együtt kell működnie: 

- A Media Services forrásnak az "intelligencia" (forrás-assziszt) értékkel kell rendelkeznie ahhoz, hogy tájékoztassa a CDN-t a következő objektumról, és 
- A CDN elvégzi a kilépést és a gyorsítótárazást (CDN-prefektusi rész). A CDN-nek szüksége van az "intelligencia" kifejezésre is, hogy tájékoztassa a forrást arról, hogy a rendszer kikéri-e a bevezetőt, a 404-es válaszokat és a végtelen előhívási hurok elkerülését.

### <a name="benefits"></a>Előnyök

A *forrás – támogatás CDN-kiindulási* funkció előnyei a következők:

- A prefektus javítja a videolejátszás minőségét: előre pozícionált, előre jelzett videó szegmensek a lejátszás során, csökkentve a késést a megjelenítőnél, és javíthatja a videó-szegmensek letöltési idejét. Ennek eredményeképpen gyorsabb a videó indítási ideje és az alacsonyabb újrapufferelési események.
- Ez a koncepció az általános CDN-eredetű forgatókönyvre vonatkozik, és nem korlátozódik az adathordozóra.
- A Akamai hozzáadta ezt a szolgáltatást a [Akamai Cloud embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html)szolgáltatáshoz.

> [!NOTE]
> Ez a funkció még nem alkalmazható a Media Services streaming-végponttal integrált Akamai CDN-hez. Azonban elérhető Media Services ügyfelek számára, akik már meglévő Akamai-szerződéssel rendelkeznek, és egyéni integrációt igényelnek a Akamai CDN és a Media Services-forrás között.

### <a name="how-it-works"></a>Működés

A Akamai CDN-vel közvetlen szerződéssel rendelkező ügyfelek számára a CDN-támogatás a *forrás-assziszt CDN-* hez (az élő és a videó igény szerinti közvetítéséhez egyaránt). A szolgáltatás a következő HTTP-fejléceket foglalja magában a Akamai CDN és a Media Services-forrás között:

|HTTP-fejléc|Értékek|Küldő|Fogadó|Cél|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-Assist-prefektus-enabled | 1 (alapértelmezett) vagy 0 |Tartalomkézbesítési hálózat (CDN)|Forrás|Annak jelzése, hogy a CDN engedélyezve van-e|
|CDN-Origin-Assist-prefektus-Path| Példa: <br/>Töredékek (videó = 1400000000, Format = mpd-Time-CMAF)|Forrás|Tartalomkézbesítési hálózat (CDN)|A CDN elérési útjának biztosítása|
|CDN – forrás – assziszt – prefektus – kérelem|1 (kirendelési kérelem) vagy 0 (normál kérelem)|Tartalomkézbesítési hálózat (CDN)|Forrás|A CDN kérelmének jelzése|

A következő lépésekkel tekintheti meg a fejléc-Exchange működés közbeni részét:

1. A Poster vagy a curl használatával küldjön egy kérést a Media Servicesi forrásra egy hang-vagy video-szegmens vagy-töredék számára. Ügyeljen arra, hogy adja hozzá a CDN-Origin-Assist-prefektus-enabled: 1 fejlécet a kérelemben.
2. A válaszban látnia kell a CDN-Origin-Assist-prefektus-Path fejlécet a relatív elérési úttal, mint az értékét.

### <a name="supported-streaming-protocols"></a>Támogatott streaming protokollok 

A *forrás-assziszt CDN-kiindulási* szolgáltatás a következő folyamatos átviteli protokollokat támogatja az élő és igény szerinti folyamatos átvitelhez:

* HLS v3
* HLS v4
* HLS CMAF
* KÖTŐJEL (CSF)
* KÖTŐJEL (CMAF)
* Smooth streaming

### <a name="faqs"></a>Gyakori kérdések

* Mi a teendő, ha a visszahívási útvonal URL-címe érvénytelen, hogy a CDN-kiküldés 404? 

    A CDN 10 másodpercig (vagy más konfigurált érték) csak a 404-es választ gyorsítótárazza.
* Tegyük fel, hogy van egy igény szerinti videója. Ha CDN – a prefektus engedélyezve van, ez a funkció azt feltételezi, hogy ha egy ügyfél az első videós szegmenst kéri, a kiküldés egy hurkot indít el, hogy az összes további videó-szegmens ugyanazzal a sávszélességgel kezdődjön? 

    Nem, CDN – a prefektus csak az ügyfél által kezdeményezett kérelem/válasz után hajtható végre. CDN – a prefektust soha nem indítják el, hogy elkerülje a Meghívási ciklust. 
* A forrás-assziszt CDN-szolgáltatás mindig be van kapcsolva? Hogyan lehet be-és kikapcsolni? 

    Ez a funkció alapértelmezésben ki van kapcsolva. Az ügyfeleknek be kell kapcsolniuk a Akamai API-n keresztül.
* Élő közvetítés esetén mi történne a forrás-assziszttal, ha a következő szegmens vagy töredék még nem érhető el? 

    Ebben az esetben a Media Services a forrás nem fog megjelenni a CDN-Origin-Assist-prefektus-Path fejlécben, és a CDN-a prefektusi szolgáltatás nem fog történni.
* Hogyan működik a *forrás-és a CDN – a kisegítő* szolgáltatás dinamikus jegyzékfájl-szűrőkkel? 

    Ez a szolgáltatás a jegyzékfájltól függetlenül működik. Ha a következő részlet kívül esik egy szűrő ablakon, az URL-cím továbbra is a nyers ügyfél-jegyzékfájlba kerül. Ez azt jelenti, hogy a CDN lekéri a KÖTŐJELből/HLS/simított jegyzékfájlból kiszűrt töredékek URL-címét. A játékos azonban soha nem kap kérést a CDN-nek a töredék beolvasására, mivel ez a töredék nem szerepel a játékos által tárolt DASH/HLS/Smooth jegyzékfájlban (a játékos nem ismeri a töredék létezését).
* A DASH MPD/HLS lejátszási lista/Smooth manifest is előhívható?

    Nem, DASH MPD, HLS Master Playlist, HLS Variant Playlist vagy Smooth manifest URL nincs hozzáadva a prefektusi fejléchez.
* Relatív vagy abszolút a prefektus URL-címei? 

    Habár a Akamai CDN mindkét lehetőséget lehetővé teszi, a Media Services-forrás csak relatív URL-címeket biztosít a visszahívási útvonalhoz, mert nincs látható előny az abszolút URL-címek használata esetén.
* Működik ez a funkció a DRM-védelemmel ellátott tartalommal?

    Igen, mivel ez a funkció HTTP-szinten működik, nem dekódol, és nem elemez egyetlen szegmenst vagy töredéket sem. Nem érdekli, hogy a tartalom titkosítva van-e.
* Együttműködik a szolgáltatás kiszolgálóoldali ad-beszúrással (SSAI)?
    
    Az eredeti/fő tartalom esetében (az eredeti videotartalom az ad beszúrása előtt) működik, mivel a SSAI nem változtatja meg a forrás tartalmának időbélyegét a Media Services forrásból. Azt határozza meg, hogy a funkció az ad-tartalommal működik-e, attól függ, hogy az ad-forrás támogatja-e a forrást Ha például az ad-tartalmak Azure Media Services (azonos vagy különálló eredetű) is futnak, az ad-tartalmak is beolvasva lesznek.
* Működik ez a funkció a UHD/HEVC-tartalommal?

    Igen.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="next-steps"></a>Következő lépések

* Ügyeljen arra, hogy SLA az [adatfolyam-végpont (forrás)](streaming-endpoint-concept.md) dokumentumát.
* Az [ebben a tárházban található](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) minta bemutatja, hogyan indíthatja el az alapértelmezett adatfolyam-végpontot a .net-tel.
