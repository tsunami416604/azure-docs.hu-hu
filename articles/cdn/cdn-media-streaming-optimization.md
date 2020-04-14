---
title: Médiaadatfolyam-továbbítás optimalizálása az Azure CDN-nel
description: Optimalizálja a folyamatos átvitelű médiafájlokat a zökkenőmentes kézbesítés érdekében
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: allensu
ms.openlocfilehash: 2931dffaaab2d06b2c06f03770a66d78d6466787
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260479"
---
# <a name="media-streaming-optimization-with-azure-cdn"></a>Médiaadatfolyam-továbbítás optimalizálása az Azure CDN-nel 
 
A nagy felbontású videó használata növekszik az interneten, ami nehézségeket okoz a nagy fájlok hatékony kézbesítéséhez. Az ügyfelek elvárják a videoigény vagy az élő videoeszközök zökkenőmentes lejátszását a világ különböző hálózatain és ügyfelein. A médiaadatfolyam-továbbítási fájlok gyors és hatékony kézbesítési mechanizmusa kritikus fontosságú a zökkenőmentes és élvezetes fogyasztói élmény biztosítása érdekében.  

Az élő médiatartalmakat különösen nehéz kézbesíteni a nagy méretek és az egyidejű megtekintők száma miatt. A hosszú késések miatt a felhasználók elmennek. Mivel az élő közvetítéseket nem lehet előre gyorsítótárazni, és a nagy késések nem elfogadhatók a megtekintők számára, a videótöredékeket időben kell kézbesíteni. 

A kérelem minták streamelésis nyújt néhány új kihívásokat. Amikor egy népszerű élő közvetítés vagy egy új sorozat igény szerinti videóra kerül kiadásra, több ezer és több millió néző kérheti egyszerre a közvetítést. Ebben az esetben az intelligens kérelmek összevonása elengedhetetlen ahhoz, hogy ne terhelje túl az eredeti kiszolgálókat, ha az eszközök még nincsenek gyorsítótárazva.
 

## <a name="media-streaming-optimizations-for-azure-cdn-from-microsoft"></a>Médiaadatfolyam-továbbítás optimalizálása a Microsoft Azure CDN-jéhez

**A Microsoft-végpontok Azure CDN Standard** szolgáltatása közvetlenül az általános webes kézbesítésoptimalizálási típus használatával biztosítja a folyamatos átvitelű médiaeszközöket. 

A Microsoft **Azure CDN Standard** médiaadatfolyam-továbbítási optimalizálása hatékony az egyes médiatöredékeket tartalmazó élő vagy igény szerinti videoadatfolyamok számára. Ez a folyamat eltér a progresszív letöltésvagy bájttartomány-kérelmek használatával átadott egyetlen nagy eszköztől. A médiakézbesítés e stílusáról az [Azure CDN-lel kapcsolatos nagyfájlletöltés-optimalizálás](cdn-large-file-optimization.md)című témakörben talál további információt.

Az általános médiakézbesítési vagy igény szerinti médiakézbesítési optimalizálási típusok az Azure Content Delivery Network (CDN) háttéroptimalizálással ellátott háttéroptimalizálással gyorsabbmédia-kellékeket biztosítanak. Emellett az idő múlásával megtanult ajánlott eljárások alapján a médiaeszközök konfigurációit is használják.

### <a name="partial-cache-sharing"></a>Részleges gyorsítótár-megosztás
A részleges gyorsítótár-megosztás lehetővé teszi, hogy a CDN részben gyorsítótárazott tartalmat szolgáljon ki az új kérelmeknek. Ha például a CDN-hez intézett első kérelem gyorsítótár-tévesztést eredményez, a rendszer elküldi a kérelmet az eredeti nek. Bár ez a hiányos tartalom betöltődik a CDN-gyorsítótárba, a CDN-hez érkező egyéb kérések megkezdhetik az adatok beolvasását. 


## <a name="media-streaming-optimizations-for-azure-cdn-from-verizon"></a>Az Azure CDN médiaadatfolyam-optimalizálása a Verizontól

**A Verizon Azure CDN Standard és** a **Verizon-végpontok Azure CDN Premium** szolgáltatása közvetlenül az általános webes kézbesítésoptimalizálási típus használatával biztosítja a folyamatos átvitelű médiaeszközöket. A CDN néhány szolgáltatása alapértelmezés szerint közvetlenül segíti a médiaeszközök kézbesítését.

### <a name="partial-cache-sharing"></a>Részleges gyorsítótár-megosztás

A részleges gyorsítótár-megosztás lehetővé teszi, hogy a CDN részben gyorsítótárazott tartalmat szolgáljon ki az új kérelmeknek. Ha például a CDN-hez intézett első kérelem gyorsítótár-tévesztést eredményez, a rendszer elküldi a kérelmet az eredeti nek. Bár ez a hiányos tartalom betöltődik a CDN-gyorsítótárba, a CDN-hez érkező egyéb kérések megkezdhetik az adatok beolvasását. 

### <a name="cache-fill-wait-time"></a>Gyorsítótár-töltésvárakozási idő

 A gyorsítótár kitöltési várakozási ideje szolgáltatás arra kényszeríti a peremkiszolgálót, hogy az erőforrásra vonatkozó további kérelmeket tartalmazza, amíg a HTTP-válaszfejlécek meg nem érkeznek az eredeti kiszolgálóról. Ha az eredeti ből származó HTTP-válaszfejlécek az időzítő lejárta előtt érkeznek meg, a várakoztatott összes kérelem a növekvő gyorsítótárból jelenik meg. Ugyanakkor a gyorsítótárat az eredetadatok töltik ki. Alapértelmezés szerint a gyorsítótár kitöltési várakozási ideje 3000 ezredmásodpercre van állítva. 

 
## <a name="media-streaming-optimizations-for-azure-cdn-from-akamai"></a>Az Azure CDN médiaadatfolyam-optimalizálása az Akamai-tól
 
**Az Akamai Azure CDN Standard** szolgáltatása olyan funkciót kínál, amely hatékonyan biztosítja a streamelési médiaeszközöket a felhasználók számára a világ minden táján. A szolgáltatás csökkenti a késéseket, mert csökkenti az eredeti kiszolgálók terhelését. Ez a funkció a standard Akamai tarifacsomaggal érhető el. 

Az **Akamai-tól származó Azure CDN Standard** médiaadatfolyam-továbbítási optimalizálása hatékony az egyes médiatöredékeket használó élő vagy igény szerinti videoadatfolyam-továbbítási tartalmakhoz. Ez a folyamat eltér a progresszív letöltésvagy bájttartomány-kérelmek használatával átadott egyetlen nagy eszköztől. Az ilyen médiakézbesítési stílusról a [Nagy fájloptimalizálás](cdn-large-file-optimization.md)című témakörben talál további információt.

Az általános médiakézbesítési vagy igény szerinti videofájlok kézbesítési optimalizálási típusai egy háttérszintű optimalizálással ellátott CDN-t használnak a médiaeszközök gyorsabb biztosításához. Emellett az idő múlásával megtanult ajánlott eljárások alapján a médiaeszközök konfigurációit is használják.

### <a name="configure-an-akamai-cdn-endpoint-to-optimize-media-streaming"></a>Akamai CDN-végpont konfigurálása a médiaadatfolyam-továbbítás optimalizálásához
 
Konfigurálhatja a tartalomkézbesítési hálózat (CDN) végpontot a nagy fájlok kézbesítésének optimalizálásához az Azure Portalon keresztül. Ehhez használhatja a REST API-kat vagy az ügyfél SDK-kat is. A következő lépések az Azure Portalon keresztül mutatják be a folyamatot **egy Akamai-profilból származó Azure CDN-szabvány esetén:**

1. Új végpont hozzáadásához az Akamai **CDN-profillapon** válassza **a Végpont**lehetőséget.
  
    ![Új végpont](./media/cdn-media-streaming-optimization/cdn-new-akamai-endpoint.png)

2. Az **Optimalizált legördülő** listában válassza **az Igény szerinti videómédia-adatfolyam-továbbítás** lehetőséget az igény szerinti videoeszközökhöz. Ha élő és igény szerinti videoadatfolyam-továbbítást végez, válassza az **Általános médiaadatfolyam-továbbítás**lehetőséget.

    ![Kijelölt adatfolyam](./media/cdn-media-streaming-optimization/02_Creating.png) 
 
A végpont létrehozása után alkalmazza az optimalizálást az összes olyan fájlra, amely megfelel bizonyos feltételeknek. A következő szakasz ezt a folyamatot ismerteti. 

### <a name="caching"></a>Gyorsítótárazás

Ha **az Akamai Azure CDN Standard** észleli, hogy az eszköz egy streamelési jegyzékfájl vagy töredék, az általános webes kézbesítéstől eltérő gyorsítótárazási lejárati időket használ. (Lásd a teljes listát az alábbi táblázatban.) Mint mindig, a gyorsítótár-vezérlő vagy az eredetiből küldött Expires fejlécek tiszteletben vannak. Ha az eszköz nem médiaeszköz, gyorsítótárazza az általános webes kézbesítés lejárati ideje használatával.

A rövid negatív gyorsítótárazási idő akkor hasznos az origin tehermentesítéshez, ha sok felhasználó olyan töredéket kér, amely még nem létezik. Erre példa egy élő adatfolyam, ahol a csomagok nem érhetők el az adott második forrásból. A hosszabb gyorsítótárazási időköz is segít kiszervezi a kérelmeket az eredeti, mert a videó tartalom általában nem módosul.
 

|   | Általános webes kézbesítés | Általános médiaadatfolyam-továbbítás | Igény szerinti videomédia-adatfolyam-továbbítás  
--- | --- | --- | ---
Gyorsítótárazás: Pozitív <br> HTTP 200, 203, 300, <br> 301, 302 és 410 | 7 nap |365 nap | 365 nap   
Gyorsítótárazás: Negatív <br> HTTP 204, 305, 404, <br> és 405 | None | 1 másodperc | 1 másodperc
 
### <a name="deal-with-origin-failure"></a>A származás hibájának kezelése  

Az általános médiakézbesítés és az igény szerinti videomédia-kézbesítés is rendelkezik az eredeti időtúldíjakkal és a tipikus kérelemmintákra vonatkozó gyakorlati tanácsokon alapuló újrapróbálkozási naplóval. Például mivel az általános médiakézbesítés az élő és az igény szerinti videomédia-kézbesítésre történik, az élő közvetítés időérzékeny jellege miatt rövidebb kapcsolati időtúlfelvételt használ.

Amikor egy kapcsolat túllépi az időtúltöltést, a CDN többször újrapróbálkozik, mielőtt "504 – Átjáró időtúllépés" hibaüzenetet küldne az ügyfélnek. 

Ha egy fájl megfelel a fájltípus és a méretfeltételek listájának, a CDN a médiaadatfolyam-továbbítás viselkedését használja. Ellenkező esetben általános webes kézbesítést használ.
   
### <a name="conditions-for-media-streaming-optimization"></a>A médiaadatfolyam-továbbítás optimalizálásának feltételei 

Az alábbi táblázat felsorolja a médiaadatfolyam-továbbítás optimalizálásához teljesítendő feltételeket: 
 
Támogatott streamelési típusok | Fájlkiterjesztések  
--- | ---  
Apple HLS | m3u8, m3u, m3ub, kulcs, ts, aac
Adobe HDS | f4m, f4x, drmmeta, bootstrap, f4f,<br>Seg-Frag URL-struktúra <br> (megfelelő regex: ^(/.*)Seq(\d+)-Frag(\d+)
Kötőjel | mpd, kötőjel, divx, ismv, m4s, m4v, mp4, mp4v, <br> sidx, webm, mp4a, m4a, isma
Zökkenőmentes streamelés | /manifest/, /QualityLevels/Fragments/
  
 
