---
title: Tartalom streamelése CDN-integrációval
titleSuffix: Azure Media Services
description: Ismerje meg a CDN-integrációval történő streamelési tartalmakat, valamint az előzetes betöltést és az Origin-Assist CDN-Prefetch-et.
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
ms.openlocfilehash: 4ed8ada306720b7a8b44ddd59cefe399238c906a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128070"
---
# <a name="stream-content-with-cdn-integration"></a>Tartalom streamelése CDN-integrációval

Az Azure tartalomkézbesítési hálózat CDN a tartalmakat világszerte, stratégiai alapon elhelyezett fizikai csomópontokon gyorsítótárazva globális megoldást kínál a fejlesztők számára a tartalmak nagy sávszélességű gyors kézbesítéséhez a felhasználók felé.  

A CDN a Media Services [streaming végpontjáról (forrásából)](streaming-endpoint-concept.md) streamelt tartalmat kodekenként, streamelési protokollonként, bitrátánként, tárolóformátumonként és titkosításon/DRM-enként gyorsítótárazza. A codec-streaming protokoll-tároló formátum-bitrate-titkosítás minden egyes kombinációjához külön CDN-gyorsítótár lesz.

A népszerű tartalom közvetlenül a CDN-gyorsítótárból kerül kiszolgálásra mindaddig, amíg a videotöredék gyorsítótárba kerül. Az élő tartalom valószínűleg a gyorsítótárba kerül, mert általában sokan nézik ugyanazt a dolgot. Az igény szerinti tartalom egy kicsit trükkösebb lehet, mert lehet, hogy van olyan tartalom, amely népszerű, és van, ami nem. Ha több millió videó eszközöd van, ahol egyikük sem népszerű (csak heti egy vagy két néző), de több ezer ember nézi az összes különböző videót, a CDN sokkal kevésbé lesz hatékony.

Azt is meg kell vizsgálnia, hogyan működik az adaptív streamelés. Minden egyes videótöredék saját entitásként van gyorsítótárazva. Képzeld el például, hogy először néz meg egy bizonyos videót. Ha a néző csak néhány másodpercet néz itt-ott, csak a figyelt személy által figyelt videótöredékek gyorsítótárazódnak a CDN-ben. Az adaptív streamelésnek köszönhetően általában 5–7 különböző bitrátával rendelkezik. Ha egy személy egy bitrátát figyel, egy másik pedig egy másik bitrátát, akkor mindegyik külön van gyorsítótárazva a CDN-ben. Még ha két ember ugyanazt a bitrátát nézi is, akkor is különböző protokollokon keresztül streamelhetnek. Minden protokoll (HLS, MPEG-DASH, Smooth Streaming) külön-külön van gyorsítótárazva. Így minden bitráta és protokoll külön-külön gyorsítótárazva van, és csak a kért videotöredékek kerülnek gyorsítótárba.

Annak eldöntésekor, hogy engedélyezi-e a CDN-t a Media Services [adatfolyam-lejátszási végpontján,](streaming-endpoint-concept.md)vegye figyelembe a várható megtekintők számát. A CDN csak akkor segít, ha sok megtekintőt vár a tartalomhoz. Ha a nézők maximális egyidejűsége 500-nál alacsonyabb, ajánlott letiltani a CDN-t, mivel a CDN a legjobban az egyidejűségnél méretezhető.

Ez a témakör a [CDN-integráció](#enable-azure-cdn-integration)engedélyezését tárgyalja. Ismerteti az előzetes betöltést (aktív gyorsítótárazást) és az [Origin-Assist CDN-Prefetch](#origin-assist-cdn-prefetch) koncepciót is.

## <a name="considerations"></a>Megfontolandó szempontok

* A [streamelési végpont](streaming-endpoint-concept.md) `hostname` és a streamelési URL ugyanaz marad, függetlenül attól, hogy engedélyezi-e a CDN.The streaming endpoint and the streaming URL is the streamed URL is the not whether or not you enable CDN.
* Ha a tartalom CDN-nel vagy anélkül is tesztelhető, hozzon létre egy másik streamelési végpontot, amely nincs engedélyezve a CDN- ben.

## <a name="enable-azure-cdn-integration"></a>Az Azure CDN-integráció engedélyezése

> [!IMPORTANT]
> A CDN nem engedélyezhető próba- vagy diákAzure-fiókokhoz.
>
> A CDN-integráció a szövetségi kormány és Kína régiók kivételével minden Azure-adatközpontban engedélyezve van.

Miután egy streamelési végpont cdn engedélyezve van, van egy meghatározott várakozási idő a Media Services előtt DNS-frissítés történik a streamelési végpont CDN-végpontra való leképezése előtt.

Ha később le szeretné tiltani/engedélyezni szeretné a CDN-t, a streamelési végpontnak **leállított** állapotban kell lennie. Akár két órát is igénybe vehet, amíg az Azure CDN-integráció engedélyezve van, és a módosítások az összes CDN-POP-ban aktívak lesznek. Azonban elindíthatja a streamelési végpontot, és megszakítás nélkül streamelheti a streamelési végpontot. Az integráció befejezése után az adatfolyam a CDN-ről kerül kézbesítésre. A kiépítési időszak ban a streamelési végpont lesz a **kezdő** állapotban, és előfordulhat, hogy a csökkenő teljesítmény figyelhető meg.

A Standard streamelési végpont létrehozásakor alapértelmezés szerint a Standard Verizon konfigurálva van. A Premium Verizon vagy standard Akamai szolgáltatók konfigurálhatók REST API-k használatával.

Az Azure Media Services és az Azure CDN integrációja a **Verizon Azure CDN-en** valósul meg a szabványos streamelési végpontokhoz. A prémium szintű streamelési végpontok konfigurálhatók az összes **Azure CDN-díjszabási csomag és szolgáltató**használatával.

> [!NOTE]
> Az Azure CDN-ről a [CDN áttekintése](../../cdn/cdn-overview.md)című témakörben olvashat részletesen.

## <a name="determine-if-a-dns-change-was-made"></a>Annak megállapítása, hogy történt-e DNS-módosítás

Megállapíthatja, hogy a DNS-módosítás streamelési végponton történt-e (a <https://www.digwebinterface.com>forgalom az Azure CDN-re irányul) a használatával. Ha azureedge.net tartománynevet lát az eredmények között, a forgalom a CDN-re mutat.

## <a name="origin-assist-cdn-prefetch"></a>Origin-Assist CDN-előzetes betöltés

A CDN-gyorsítótárazás reaktív folyamat. Ha a CDN meg tudja jósolni, hogy mi lesz a következő objektum, a CDN proaktív módon kérheti és gyorsítótárathatja a következő objektumot. Ezzel a folyamattal az összes (vagy a legtöbb) objektumra gyorsítótár-találatot érhet el, ami javítja a teljesítményt.

Az előzetes betöltés koncepciója arra törekszik, hogy tárgyakat helyezzen el az "internet szélén", arra számítva, hogy ezeket a játékos hamarosan kérni fogja, ezáltal csökkentve az időt, hogy ezt a tárgyat a játékosnak szállítsa.

E cél elérése érdekében a streamelési végpontnak (eredet) és a CDN-nek több módon is kéz a kézben kell működnie:

- A Media Services eredete kell a "intelligencia" (Origin-Assist), hogy tájékoztassa a CDN a következő objektum ot előre betöltésére.
- A CDN végzi az előtöltést és a gyorsítótárazást (CDN-előhívási rész). CDN is szüksége van az "intelligencia", hogy tájékoztassa az eredetét, hogy ez egy prefetch vagy rendszeres fetch, kezelni a 404 válaszokat, és egy módja annak, hogy elkerüljék a végtelen előre fetch hurok.

### <a name="benefits"></a>Előnyök

Az *Origin-Assist CDN-Prefetch* funkció előnyei a következők:

- Az előzetes betöltés javítja a videolejátszás minőségét azáltal, hogy lejátszás közben előre pozícionálja a várható videoszegmenseket a szélek szélén, csökkenti a lenézést a megtekintő számára, és javítja a videoszegmensek letöltési idejét. Ez gyorsabb videoindítási időt és alacsonyabb újrapufferelési eseményeket eredményez.
- Ez a fogalom általános CDN-eredetű forgatókönyvre vonatkozik, és nem korlátozódik a médiára.
- Akamai hozzáadta ezt a funkciót [az Akamai Cloud Embed (ACE)](https://learn.akamai.com/en-us/products/media_delivery/cloud_embed.html)szolgáltatáshoz.

> [!NOTE]
> Ez a funkció még nem alkalmazható a Media Services adatfolyam-lejátszási végpontjával integrált Akamai CDN-re. Azonban elérhető a Media Services ügyfelek számára, akik már meglévő Akamai szerződéssel rendelkeznek, és egyéni integrációt igényelnek az Akamai CDN és a Media Services eredete között.

### <a name="how-it-works"></a>Működés

A CDN-támogatás a `Origin-Assist CDN-Prefetch` fejlécekhez (élő és videoon-on-demand streameléshez egyaránt) az Onkamai CDN-nel közvetlen szerződéssel rendelkező ügyfelek számára érhető el. A szolgáltatás az Akamai CDN és a Media Services eredete közötti alábbi HTTP-fejléccseréket foglalja magában:

|HTTP FEJLÉC|Értékek|Küldő|Fogadó|Cél|
| ---- | ---- | ---- | ---- | ----- |
|`CDN-Origin-Assist-Prefetch-Enabled` | 1 (alapértelmezett) vagy 0 |Tartalomkézbesítési hálózat (CDN)|Forrás|Annak jelzése, hogy a CDN előhívás engedélyezve van.|
|`CDN-Origin-Assist-Prefetch-Path`| Példa: <br/>Töredékek(video=1400000000,formátum=mpd-idő-cmaf)|Forrás|Tartalomkézbesítési hálózat (CDN)|A CDN előzetes betöltési útvonalának biztosítása.|
|`CDN-Origin-Assist-Prefetch-Request`|1 (előzetes betöltési kérelem) vagy 0 (normál kérés)|Tartalomkézbesítési hálózat (CDN)|Forrás|A CDN-től érkező kérés jelzése előzetes betöltés.|

Ha működés közben szeretné látni a fejléccsere egy részét, próbálkozzon az alábbi lépésekkel:

1. A Postman vagy a cURL segítségével kérelmet nyújthat be a Media Services eredetéhez egy hang- vagy videoszegmensvagy -töredék esetében. Győződjön meg arról, hogy hozzáadja a fejlécet `CDN-Origin-Assist-Prefetch-Enabled: 1` a kérelemhez.
2. A válaszban a relatív `CDN-Origin-Assist-Prefetch-Path` elérési úttal rendelkező fejlécnek kell lennie az értékeként.

### <a name="supported-streaming-protocols"></a>Támogatott streamelési protokollok

A `Origin-Assist CDN-Prefetch` szolgáltatás a következő streamelési protokollokat támogatja az élő és az igény szerinti streameléshez:

* HLS v3
* HLS v4
* HLS CMAF
* DASH (CSF)
* DASH (CMAF)
* Zökkenőmentes streamelés

### <a name="faqs"></a>Gyakori kérdések

* Mi a teendő, ha egy előzetes betöltési elérési út URL-címe érvénytelen, így a CDN előzetes betöltése 404-es beolvasást kap?

    A CDN csak 10 másodpercig (vagy más konfigurált értékig) gyorsítótáraz 404-es választ.

* Tegyük fel, hogy igény szerinti videóval rendelkezik. Ha a CDN-prefetch engedélyezve van, ez a funkció azt jelenti, hogy ha egy ügyfél kéri az első videoszegmenst, az előzetes betöltés egy hurkot indít el az összes további videószegmens egyidejű előzetes betöltéséhez?

    Nem, a CDN-előhívás csak az ügyfél által kezdeményezett kérés/válasz után történik. A CDN-prefetch-et soha nem indítja el egy előzetes betöltés, hogy elkerülje az előzetes betöltési hurkot.

* Az Origin-Assist CDN-Prefetch funkció mindig be van kapcsolva? Hogyan lehet be- és kikapcsolni?

    Ez a funkció alapértelmezés szerint ki van kapcsolva. Az ügyfeleknek be kell kapcsolniuk az Akamai API-n keresztül.

* Az élő közvetítés esetén mi történne az Origin-Assist szolgáltatásban, ha a következő szegmens vagy töredék még nem lenne elérhető?

    Ebben az esetben a Media Services `CDN-Origin-Assist-Prefetch-Path` eredete nem biztosít fejlécet, és a CDN-előhívás nem történik meg.

* Hogyan `Origin-Assist CDN-Prefetch` működik a dinamikus jegyzékfájlszűrők?

    Ez a szolgáltatás a jegyzékfájl-szűrőtől függetlenül működik. Amikor a következő töredék egy szűrőablakon kívül van, az URL-címe továbbra is megtalálható lesz a nyers ügyféljegyzékbe való belenézésével, majd CDN előzetes betöltési válaszfejlécként visszatérve. Így a CDN megkapja a DASH/HLS/Smooth manifest-ből kiszűrt töredék URL-jét. Azonban a játékos soha nem fog get kérelmet cdn letölteni, hogy a töredék, mert ez a töredék nem szerepel a DASH / HLS / Smooth nyilvánvaló birtokában a játékos (a játékos nem tudja, hogy a töredék létezését).

* Lehet DASH MPD / HLS lejátszási lista / Smooth manifest lehet előre beolvasni?

    Nem, dash MPD, HLS master playlist, HLS variáns lejátszási lista vagy sima jegyzékurl nem kerül be a betöltés előtti fejlécbe.

* Relatív vagy abszolút az előzetes BEtöltési URL-ek?

    Bár az Akamai CDN mindkettőt engedélyezi, a Media Services eredete csak relatív URL-címeket biztosít az előzetes betöltési útvonalhoz, mivel az abszolút URL-ek használata nem jár nyilvánvaló előnnyel.

* Működik ez a szolgáltatás drm-védelemmel ellátott tartalommal?

    Igen, mivel ez a funkció http-szinten működik, nem dekódolja vagy elemzi a szegmenseket/töredékeket. Nem számít, hogy a tartalom titkosított-e vagy sem.

* Működik ez a szolgáltatás a kiszolgálóoldali hirdetésbeillesztéssel (SSAI)?
    
    Az eredeti/fő tartalom (az eredeti videótartalom a hirdetés beillesztése előtt) működik, mivel az SSAI nem módosítja a forrástartalom időbélyegét a Media Services eredetéből. Az, hogy ez a funkció működik-e a hirdetés tartalmával, attól függ, hogy a hirdetés eredete támogatja-e az Origin-Assist szolgáltatást. Ha például a hirdetéstartalma az Azure Media Servicesben is megtalálható (azonos vagy különálló eredetű), a hirdetéstartalmat is előre lekéri a program.

* Működik ez a funkció uhd/HEVC tartalommal?

    Igen.

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdéseket tehet fel, visszajelzést adhat, frissítéseket kaphat

Tekintse meg az [Azure Media Services közösségi](media-services-community.md) cikket, ahol különböző módokon tehet fel kérdéseket, küldhet visszajelzést, és kaphat frissítéseket a Media Services szolgáltatásról.

## <a name="next-steps"></a>További lépések

* Győződjön meg arról, hogy tekintse át a [streamelési végpont (forrás)](streaming-endpoint-concept.md) dokumentumot.
* A [tárházban található minta](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs) bemutatja, hogyan indíthatja el az alapértelmezett streamelési végpontot a .NET értékkel.
