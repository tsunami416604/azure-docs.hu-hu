---
title: A gyorsítótárazás működése | Microsoft dokumentumok
description: A gyorsítótárazás az adatok helyi tárolásának folyamata, így az adatokra vonatkozó jövőbeli kérelmek gyorsabban elérhetők.
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
ms.date: 04/30/2018
ms.author: allensu
ms.openlocfilehash: d0c438aee7f56e96feb7167fad718fd9519a9f76
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253713"
---
# <a name="how-caching-works"></a>A gyorsítótárazás működése

Ez a cikk áttekintést nyújt az általános gyorsítótárazási fogalmakról, és arról, hogy az [Azure Content Delivery Network (CDN)](cdn-overview.md) hogyan használja a gyorsítótárazást a teljesítmény javítása érdekében. Ha a CDN-végpont gyorsítótárazási viselkedésének testreszabásáról szeretne többet megtudni, olvassa el az [Azure CDN-gyorsítótárazási viselkedésvezérlési viselkedést gyorsítótárazási szabályokkal](cdn-caching-rules.md) és [az Azure CDN-gyorsítótárazás viselkedésének vezérlése lekérdezési karakterláncokkal című témakört.](cdn-query-string.md)

## <a name="introduction-to-caching"></a>Bevezetés a gyorsítótárazásba

A gyorsítótárazás az adatok helyi tárolásának folyamata, így az adatokra vonatkozó jövőbeli kérelmek gyorsabban elérhetők. A leggyakoribb gyorsítótárazási, webböngésző-gyorsítótárazási típusban a webböngésző helyileg tárolja a statikus adatok másolatát a helyi merevlemezen. A gyorsítótárazás használatával a webböngésző elkerülheti, hogy több oda-vissza utat érjen el a kiszolgálóra, és ehelyett ugyanazokat az adatokat helyileg érheti el, így időt és erőforrásokat takarít meg. A gyorsítótárazás kiválóan alkalmas kisméretű statikus adatok, például statikus képek, CSS-fájlok és JavaScript-fájlok helyi kezelésére.

Hasonlóképpen a gyorsítótárazást a felhasználóhoz közeli peremhálózati kiszolgálókon lévő tartalomkézbesítési hálózat használja, hogy elkerülje a kezdőrendszerbe visszaérkező kérelmeket, és csökkentse a végfelhasználói késés csökkentését. A webböngésző-gyorsítótárral ellentétben, amelyet csak egyetlen felhasználó használ, a CDN megosztott gyorsítótárral rendelkezik. A CDN megosztott gyorsítótárában az egyik felhasználó által kért fájlt később más felhasználók is elérhetik, ami jelentősen csökkenti az eredeti kiszolgálóra irányuló kérelmek számát.

Az olyan dinamikus erőforrások, amelyek gyakran változnak, vagy az egyes felhasználók számára egyediek, nem gyorsítótárazhatók. Az ilyen típusú erőforrások azonban kihasználhatják a dinamikus webhelygyorsítási (DSA) optimalizálás előnyeit az Azure Content Delivery Network teljesítménybeli fejlesztések érdekében.

A gyorsítótárazás több szinten is előfordulhat az eredeti kiszolgáló és a végfelhasználó között:

- Webkiszolgáló: Megosztott gyorsítótárat használ (több felhasználó számára).
- Tartalomkézbesítési hálózat: Megosztott gyorsítótárat használ (több felhasználó számára).
- Internetszolgáltató : Megosztott gyorsítótárat használ (több felhasználó számára).
- Webböngésző: Privát gyorsítótárat használ (egy felhasználó számára).

Minden gyorsítótár általában kezeli a saját erőforrás frissességét, és érvényesítést hajt végre, ha egy fájl elavult. Ezt a viselkedést a HTTP-gyorsítótárazási specifikáció [(RFC 7234)](https://tools.ietf.org/html/rfc7234)határozza meg.

### <a name="resource-freshness"></a>Erőforrás frissessége

Mivel a gyorsítótárazott erőforrás potenciálisan elavult vagy elavult (az eredeti kiszolgálón lévő megfelelő erőforráshoz képest), fontos, hogy a gyorsítótárazási mechanizmus szabályozza a tartalom frissítését. Az idő- és sávszélesség-felhasználás érdekében a gyorsítótárazott erőforrás nem lesz összehasonlítva az eredeti kiszolgálón lévő verzióval minden alkalommal, amikor hozzáfér. Ehelyett mindaddig, amíg a gyorsítótárazott erőforrás frissnek tekinthető, a rendszer a legfrissebb verziónak tekinti, és közvetlenül az ügyfélnek küldi el. A gyorsítótárazott erőforrás akkor tekinthető frissnek, ha a kora kisebb, mint a gyorsítótár-beállítás által meghatározott kor vagy időszak. Ha például egy böngésző újratölt egy weboldalt, ellenőrzi, hogy a merevlemezen lévő minden gyorsítótárazott erőforrás friss-e, és betölti-e azt. Ha az erőforrás nem friss (elavult), a kiszolgálóról egy naprakész másolat töltődik be.

### <a name="validation"></a>Ellenőrzés

Ha egy erőforrás elavultnak minősül, a rendszer kéri, hogy ellenőrizze azt, azaz határozza meg, hogy a gyorsítótárban lévő adatok továbbra is megegyeznek-e az eredeti kiszolgálón lévő adatokkal. Ha a fájlt módosították az eredeti kiszolgálón, a gyorsítótár frissíti az erőforrás verzióját. Ellenkező esetben, ha az erőforrás friss, az adatok közvetlenül a gyorsítótárból érkeznek anélkül, hogy először ellenőrizné.

### <a name="cdn-caching"></a>CDN gyorsítótárazás

A gyorsítótárazás szerves része annak, ahogyan a CDN működik, hogy felgyorsítsa a kézbesítést, és csökkentse a statikus eszközök, például képek, betűtípusok és videók kezdőterhelését. A CDN-gyorsítótárazásban a statikus erőforrásokat a stratégiailag elhelyezett kiszolgálókon szelektíven tárolják, amelyek helyibbek a felhasználó számára, és a következő előnyöket biztosítják:

- Mivel a legtöbb webes forgalom statikus (például képek, betűtípusok és videók), a CDN-gyorsítótárazás csökkenti a hálózati késést azáltal, hogy a tartalmat közelebb helyezi a felhasználóhoz, így csökkenti az adatok által megtett távolságot.

- A cdn-re való kiszervezéssel a gyorsítótárazás csökkentheti a hálózati forgalmat és a forráskiszolgáló terhelését. Ezzel csökkenti az alkalmazás költség- és erőforrás-igényét, még akkor is, ha nagy számú felhasználó van.

A gyorsítótárazás webböngészőben való megvalósításához hasonlóan a gyorsítótárazás cdn-ben való végrehajtásának módját is szabályozhatja a gyorsítótár-irányelv fejléceinek elküldésével. A gyorsítótár-direktíva fejlécek HTTP-fejlécek, amelyeket általában az eredeti kiszolgáló ad hozzá. Bár a legtöbb ilyen fejlécet eredetileg az ügyfélböngészőkgyorsítótárazás kezelésére tervezték, most antól az összes köztes gyorsítótár, például a CDN-ek is használják őket. 

Két fejléc használható a gyorsítótár frissességének meghatározására: `Cache-Control` és `Expires`. `Cache-Control`aktuálisabb, és elsőbbséget `Expires`élvez , ha mindkettő létezik. Az érvényesítéshez kétféle fejlécet is használnak (ezt `ETag` validatornak nevezik): és `Last-Modified`. `ETag`aktuálisabb, és elsőbbséget `Last-Modified`élvez , ha mindkettő definiálva van.  

## <a name="cache-directive-headers"></a>Gyorsítótár-irányelv fejlécei

> [!IMPORTANT]
> Alapértelmezés szerint egy Azure CDN-végpont, amely dsa-ra van optimalizálva figyelmen kívül hagyja a gyorsítótár-direktíva fejlécek és megkerüli a gyorsítótárazást. A **Verizon Azure CDN Standard** és az **Akamai-profilokból származó Azure CDN Standard esetében módosíthatja,** hogy az Azure CDN-végpont hogyan kezelje ezeket a fejléceket a [CDN-gyorsítótárazási szabályok](cdn-caching-rules.md) használatával a gyorsítótárazás engedélyezéséhez. Csak **a Verizon-profilokból származó Azure CDN Premium** esetén a [szabálymotor](cdn-rules-engine.md) segítségével engedélyezheti a gyorsítótárazást.

Az Azure CDN a következő HTTP-gyorsítótár-direktíva fejléceket támogatja, amelyek meghatározzák a gyorsítótár időtartamát és a gyorsítótár megosztását.

**Gyorsítótár-vezérlés:**
- A HTTP 1.1-ben vezették be, hogy a webes megjelenítők `Expires` jobban kézben tarthassák a tartalmukat, és hogy kezeljék a fejléc korlátait.
- Felülírja `Expires` a fejlécet, `Cache-Control` ha mindkettő, és definiálva van.
- Ha az ügyfél és a CDN POP `Cache-Control` HTTP-kérelemben használatos, alapértelmezés szerint az összes Azure CDN-profil figyelmen kívül hagyja.
- Ha az ügyfél http-válaszában használja a CDN POP-ra:
     - **Az Azure CDN Standard/Premium a Verizontól** és `Cache-Control` a Microsoft Azure **CDN Standard szolgáltatása** minden direktívát támogat.
     - **Az Akamai Azure CDN Standard szolgáltatása** csak a következő `Cache-Control` irányelveket támogatja; az összes többi figyelmen kívül hagyja:
         - `max-age`: A gyorsítótár a megadott számú másodpercig képes tárolni a tartalmat. Például: `Cache-Control: max-age=5`. Ez az irányelv meghatározza, hogy a tartalom legfeljebb mennyi ideig tekinthető frissnek.
         - `no-cache`: Gyorsítótárazzaaa a tartalmat, de ellenőrizze a tartalmat minden alkalommal, mielőtt a gyorsítótárból kézbesítené. Egyenértékű `Cache-Control: max-age=0`a.
         - `no-store`: Soha ne gyorsítótárazza a tartalmat. Távolítsa el a tartalmat, ha azt korábban már tárolták.

**Lejár:**
- A HTTP 1.0-ban bevezetett örökölt fejléc; támogatott a visszamenőleges kompatibilitás érdekében.
- Második pontossággal használ dátumalapú lejárati időt. 
- Hasonló `Cache-Control: max-age`a hoz.
- Akkor `Cache-Control` használják, ha nem létezik.

**Pragma:**
   - Az Azure CDN alapértelmezés szerint nem tiszteli.
   - A HTTP 1.0-ban bevezetett örökölt fejléc; támogatott a visszamenőleges kompatibilitás érdekében.
   - Ügyfélkérelem-fejlécként használatos a `no-cache`következő direktívával: . Ez az irányelv arra utasítja a kiszolgálót, hogy az erőforrás új verzióját adja ki.
   - `Pragma: no-cache`egyenértékű a. `Cache-Control: no-cache`

## <a name="validators"></a>Validátorok

Ha a gyorsítótár elavult, a HTTP-gyorsítótár-érvényesítőket a rendszer a fájl gyorsítótárazott verziójának és az eredeti kiszolgálón lévő verzió összehasonlításához használja. **A Verizon Azure CDN Standard/Premium** szolgáltatása alapértelmezés szerint mind a `ETag` `Last-Modified` validátorokat, mind a Microsoft **azure-beli CDN Standard,** az **Akamai Azure CDN Standard** szolgáltatása pedig alapértelmezés szerint csak `Last-Modified` támogatja.

**Etag:**
- **A Verizon Azure CDN Standard/Premium** szolgáltatása alapértelmezés szerint támogatja, `ETag` míg a Microsoft Azure **CDN Standard,** az **Akamai Azure CDN Standard** pedig nem.
- `ETag`olyan karakterláncot határoz meg, amely a fájl minden fájljára és verziójára egyedi. Például: `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- A HTTP 1.1-ben vezették `Last-Modified`be, és aktuálisabb, mint a . Akkor hasznos, ha az utolsó módosítás dátumát nehéz meghatározni.
- Támogatja az erős és a gyenge érvényesítést; azonban az Azure CDN támogatja csak erős érvényesítés. Az erős érvényesítéshez a két erőforrás-ábrázolásnak bájt-for-byte azonosnak kell lennie. 
- A gyorsítótár ellenőrzi a `ETag` fájlt, `If-None-Match` amely úgy használja, hogy fejlécet küld egy vagy több `ETag` érvényesítővel a kérelemben. Például: `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Ha a kiszolgáló verziója `ETag` megegyezik a listán szereplő érvényesítővel, válaszában a 304-es (nem módosított) állapotkódot küldi. Ha a verzió eltérő, a kiszolgáló a 200-as állapotkóddal (OK) és a frissített erőforrással válaszol.

**Utolsó módosítás:**
- Az **Azure CDN Standard/Premium csak a Verizon,** `Last-Modified` akkor, ha `ETag` nem része a HTTP-válasz. 
- Megadja azt a dátumot és időpontot, amikor az eredeti kiszolgáló megállapította, hogy az erőforrást utoljára módosították. Például: `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- A gyorsítótár ellenőrzi a `Last-Modified` fájlt `If-Modified-Since` egy fejléc küldésével, amelynek dátuma és időpontja a kérelemben. Az eredeti kiszolgáló összehasonlítja `Last-Modified` ezt a dátumot a legújabb erőforrás fejlécével. Ha az erőforrás nem módosult a megadott idő óta, a kiszolgáló válaszában a 304-es (nem módosított) állapotkódot adja vissza. Ha az erőforrást módosították, a kiszolgáló a 200-as állapotkódot (OK) és a frissített erőforrást adja vissza.

## <a name="determining-which-files-can-be-cached"></a>A gyorsítótárazható fájlok meghatározása

Nem minden erőforrás gyorsítótárazható. Az alábbi táblázat bemutatja, hogy milyen erőforrások gyorsítótárazhatók a HTTP-válaszok típusától függően. A HTTP-válaszokkal szállított, nem az összes feltételnek nem megfelelő erőforrások nem gyorsítótárazhatók. Az **Azure CDN Premium csak a Verizon,** használhatja a szabályok motor testreszabásához néhány ilyen feltételeket.

|                   | Azure CDN a Microsofttól          | Azure CDN a Verizontól | Azure CDN az Akamai-tól        |
|-------------------|-----------------------------------|------------------------|------------------------------|
| HTTP-állapotkódok | 200, 203, 206, 300, 301, 410, 416 | 200                    | 200, 203, 300, 301, 302, 401 |
| HTTP-metódusok      | KAP, FEJ                         | GET                    | GET                          |
| Fájlméret-korlátok  | 300 GB                            | 300 GB                 | - Általános webes kézbesítés optimalizálás: 1,8 GB<br />- Média streaming optimalizálás: 1,8 GB<br />- Nagy fájl optimalizálás: 150 GB |

Az **Azure CDN Standard a Microsoft** gyorsítótárazás dolgozni egy erőforrást, az eredeti kiszolgáló támogatnia kell minden HEAD és GET HTTP-kérelmek és a tartalom-hossz értékek et meg kell egyeznie minden HEAD és GET HTTP válaszok az eszközhöz. HEAD-kérelem esetén az eredeti kiszolgálónak támogatnia kell a HEAD-kérelmet, és ugyanazzal a fejléccel kell válaszolnia, mintha GET-kérelmet kapott volna.

## <a name="default-caching-behavior"></a>Alapértelmezett gyorsítótárazási viselkedés

Az alábbi táblázat ismerteti az Azure CDN-termékek alapértelmezett gyorsítótárazási viselkedését és azok optimalizálását.

|    | Microsoft: Általános webes kézbesítés | Verizon: Általános webes kézbesítés | Verizon: DSA | Akamai: Általános webes szállítás | Akamai: DSA | Akamai: Nagy fájl letöltés | Akamai: általános vagy VOD média streaming |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Becsület eredete**       | Igen    | Igen   | Nem   | Igen    | Nem   | Igen   | Igen    |
| **A CDN gyorsítótár ának időtartama** | 2 nap |7 nap | None | 7 nap | None | 1 nap | 1 év |

**Honor origin**: Megadja, hogy a támogatott cache-directive fejlécek, ha léteznek a HTTP-válasz az eredeti kiszolgáló.

**CDN-gyorsítótár időtartama:** Megadja azt az időt, amíg egy erőforrás gyorsítótárazva van az Azure CDN-en. Ha azonban a **Honor eredete** Igen, és az eredeti kiszolgálóHTTP-válasza tartalmazza a gyorsítótár-direktíva fejlécet, `Expires` vagy `Cache-Control: max-age`az Azure CDN a fejléc által megadott időtartam-értéket használja. 

## <a name="next-steps"></a>További lépések

- A CDN alapértelmezett gyorsítótárazási viselkedésének gyorsítótárazási szabályokon keresztüli testreszabásáról és felülbírálásáról a [Gyorsítótárazási szabályokkal az Azure CDN-gyorsítótárazási viselkedésének szabályozása](cdn-caching-rules.md)című témakörben olvashat. 
- A gyorsítótárazási viselkedés vezérléséhez a lekérdezési karakterláncok használatáról az [Azure CDN-gyorsítótárazási viselkedés vezérlése lekérdezési karakterláncokkal(Control) (CdN- hitelesítésvezérlés) témakörben](cdn-query-string.md)olvashat.



