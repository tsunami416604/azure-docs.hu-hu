---
title: A gyorsítótárazás működése |} A Microsoft Docs
description: A folyamat az adatok helyi tárolására, hogy a jövőben kér számára, hogy gyorsabban hozzáférhető adatok gyorsítótárazása.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/30/2018
ms.author: magattus
ms.openlocfilehash: 563c073e781e2a2bee88b4ecdcdc82541c21ec4f
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092390"
---
# <a name="how-caching-works"></a>A gyorsítótárazás működése

Ez a cikk általános gyorsítótárazási fogalmak áttekintése, és hogyan [Azure Content Delivery Network (CDN)](cdn-overview.md) használja a teljesítmény növelése érdekében gyorsítótárat. Ha szeretné a CDN-végponton a gyorsítótárazási viselkedés testreszabása, lásd: hogyan [vezérlő Azure CDN gyorsítótárazási viselkedésének gyorsítótár-szabályokkal](cdn-caching-rules.md) és [Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési karakterláncokkal vezérlése](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Bevezetés a gyorsítótár

A folyamat az adatok helyi tárolására, hogy a jövőben kér számára, hogy gyorsabban hozzáférhető adatok gyorsítótárazása. A leggyakoribb típus gyorsítótárazása, a webes böngésző gyorsítótárazás, a webes a böngésző adatpéldányt tárol statikus helyileg egy helyi merevlemezen. Gyorsítótárazás révén a webböngésző kerülje el, hogy több adatváltásra a kiszolgálóra, és ehelyett eléréséhez ugyanazokat az adatokat helyben, így mentése időt és erőforrásokat. Gyorsítótárazás kiválóan alkalmas a kisebb, statikus adatok, például a statikus képeket, a CSS-fájlok és a JavaScript-fájlok helyi kezeléséhez.

Ehhez hasonlóan gyorsítótárazás használják a felhasználó közelében peremhálózati kiszolgálókon content delivery network kérelmek vissza a forrás utazik, és csökkenti a késéseket végfelhasználói elkerülése érdekében. Egy webes böngésző gyorsítótárát, amely, kizárólag egyetlen felhasználó használatos ellentétben a CDN a megosztott gyorsítótár rendelkezik. A CDN a megosztott gyorsítótárban egy fájl egy felhasználó által kért elérhetők később a más felhasználók, amelyek jelentősen csökkenti a forráskiszolgálóra jutó kérések száma.

Nem gyorsítótárazható, dinamikus erőforrások, amelyek gyakran módosítják, vagy egy adott felhasználóhoz egyedi. Ilyen típusú erőforrások, azonban kihasználhatják a dinamikuswebhely-gyorsításként (DSA) optimalizálás a az Azure Content Delivery Network a teljesítmény.

Gyorsítótárazás akkor fordulhat elő, több szinten, a forráskiszolgáló és a végfelhasználói között:

- Webkiszolgáló: megosztott gyorsítótár használ (a több felhasználó).
- A tartalomkézbesítési hálózat: megosztott gyorsítótár használ (a több felhasználó).
- Szolgáltatás internetszolgáltató: megosztott gyorsítótár használ (a több felhasználó).
- Webböngésző: egy privát gyorsítótárat használ (az egy felhasználó).

Minden gyorsítótár általában kezeli a saját erőforrás frissesség, és érvényesítési hajt végre, ha egy fájl elavult. Ez a viselkedés a HTTP-specifikáció gyorsítótárazás van definiálva [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Erőforrás frissessége

Mivel a gyorsítótárazott erőforrások lehet elavult, vagy elavult (ellentétben a megfelelő erőforrást a forrás-kiszolgálón), fontos bármely gyorsítótárazást vezérlő, tartalom frissítésekor. Szeretné menteni az idő és a sávszélesség-felhasználás, minden alkalommal, amikor érhető el a gyorsítótárazott erőforrások nem képest van a verzióra, a forráskiszolgálón. Ehelyett mindaddig, amíg a gyorsítótárazott erőforrások friss kell tekinteni, azt adatforrásmérete legfrissebb verzióját, és közvetlenül az ügyfélnek küldött. A gyorsítótárazott erőforrások friss lehet, ha az életkor kisebb, mint az életkor vagy egy gyorsítótár-beállításban megadott időtartam minősül. Például ha egy böngészőben újra betölti egy weblap, azt ellenőrzi, hogy az egyes gyorsítótárazott erőforrások a merevlemezen friss és betölti azt. Ha az erőforrás nem áll friss (elavult), egy naprakész példánnyal betöltése a kiszolgálóról.

### <a name="validation"></a>Ellenőrzés

Erőforrás régi minősül, ha a forráskiszolgáló érvényesíti, vagyis, határozza meg, hogy a gyorsítótárban lévő adatok továbbra is megegyezik-e a forráskiszolgálón mi kéri a rendszer. Ha a fájl módosult a forráskiszolgálón, a gyorsítótár frissíti a verziót az erőforrás. Ellenkező esetben ha az erőforrás friss, az adatok kézbesíti a rendszer közvetlenül a gyorsítótárból, először ellenőrzése nélkül.

### <a name="cdn-caching"></a>A CDN-gyorsítótárazás

Gyorsítótárazás szerves ahhoz, ahogyan a CDN működésének rövidíthetők a szállítási és statikus objektumokat, például képek, a betűtípusok és a videók a forrás-terhelés csökkentésére. CDN gyorsítótárazási statikus erőforrásokat külön-külön tárolódnak, stratégiai alapon elhelyezett kiszolgálókon, amelyek egy felhasználóhoz több helyi és a következő előnyöket nyújtja:

- Mivel a legtöbb webes forgalom statikus (a példában, képek, betűtípusok és videók), a CDN-gyorsítótárazás csökkenti a hálózati késést váltással tartalom közelebb a felhasználóra, így csökken az adatok halad távolság.

- Munkahelyi használatát a CDN-kiürítés, gyorsítótárazás csökkentheti a hálózati forgalom és a forrás-kiszolgálót érő terhelést. Költség- és erőforrás-követelmények az alkalmazás így csökkenti, akkor is, ha nagyszámú felhasználó.

Hogyan gyorsítótárazás megvalósítása hasonló egy webböngészőben, szabályozhatja, hogyan menjen végbe a gyorsítótárazás egy CDN-ben gyorsítótárirányelv-fejlécek küldésével. Gyorsítótárirányelv-fejlécek használata HTTP-fejléceket, amely általában a forráskiszolgáló által hozzáadott. Bár ezek a fejlécek a legtöbb eredetileg is gyorsítótárazás az ügyfelek böngészőin cím, akkor most is használhatók köztes gyorsítótárait, például a CDN által. 

Két fejlécek gyorsítótár frissessége meghatározásához használható: `Cache-Control` és `Expires`. `Cache-Control` több naprakész és elsőbbséget élvez `Expires`, ha létezik mindkettő. Emellett két típusa van (érvényesítők néven) érvényesítéséhez használt fejlécek: `ETag` és `Last-Modified`. `ETag` több naprakész és elsőbbséget élvez `Last-Modified`, ha mindkettő meg vannak adva.  

## <a name="cache-directive-headers"></a>Gyorsítótárirányelv-fejlécek

> [!IMPORTANT]
> Alapértelmezés szerint egy Azure CDN-végponthoz, DSA-hoz optimalizált figyelmen kívül hagyja a gyorsítótárirányelv-fejlécek és megkerüli a gyorsítótárazás. A **Azure CDN Standard verizon** és **Azure CDN Akamai Standard** -profilok segítségével beállíthatja, hogy hogyan egy Azure CDN-végponthoz kezeli a következő fejlécek [CDN gyorsítótárazási szabályok](cdn-caching-rules.md)engedélyezni a gyorsítótárazást. A **verizon Azure CDN Premium** profilok csak, használhatja a [szabálymotorral](cdn-rules-engine.md) engedélyezni a gyorsítótárazást.

Az Azure CDN támogatja a következő a HTTP-gyorsítótárirányelv-fejlécek, amelyek meghatározzák a gyorsítótárazás időtartama és a gyorsítótár-megosztás.

**A Cache-Control:**
- A tartalom több felügyeleti lehetőséget biztosítanak a webes közzétevők és céljából vonatkozó korlátozások a HTTP 1.1-ben bevezetett a `Expires` fejléc.
- Felülbírálja a `Expires` fejléc, ha mindkét, és `Cache-Control` vannak definiálva.
- A CDN összes jelenléti Pontjára, hogy az ügyfél HTTP-kérelem használatakor `Cache-Control` figyelmen kívül hagyja az összes Azure CDN-profilok, alapértelmezés szerint.
- Ha egy HTTP-válasz a CDN jelenléti pontra irányíthatja az ügyfél használja:
     - **Az Azure CDN Standard/prémium verizon** és **Azure CDN Standard a Microsoft** támogatja az összes `Cache-Control` irányelveknek.
     - **Az Azure CDN Akamai Standard** támogatja a csak a következő `Cache-Control` irányelvek; az összes többi figyelmen kívül hagyja:
         - `max-age`: A gyorsítótár tud tárolni a tartalmat a másodpercben megadott. Például: `Cache-Control: max-age=5`. Ez az irányelv meghatározza a maximális időt, a tartalom friss kell tekinteni.
         - `no-cache`: A tartalom gyorsítótárazása, de a tartalom ellenőrzése minden alkalommal, mielőtt elküldené a gyorsítótárból. Egyenértékű `Cache-Control: max-age=0`.
         - `no-store`: Soha nem gyorsítótár tartalma. Távolítsa el a tartalmat, ha korábban időtartamnál.

**Lejárat:**
- A HTTP 1.0; bevezetett örökölt fejléc támogatott a visszamenőleges kompatibilitás.
- Egy dátum-alapú lejárati időt használ pontossága. 
- Hasonló `Cache-Control: max-age`.
- Mikor használható `Cache-Control` nem létezik.

**Direktiva Pragma:**
   - Nem fogadja el az Azure CDN, alapértelmezés szerint.
   - A HTTP 1.0; bevezetett örökölt fejléc támogatott a visszamenőleges kompatibilitás.
   - Használja a következő irányelvnek ügyfél fejléc: `no-cache`. Ez az irányelv arra utasítja, hogy az erőforrás egy frissen telepített verzióját a kiszolgálón.
   - `Pragma: no-cache` egyenértékű `Cache-Control: no-cache`.

## <a name="validators"></a>Érvényesítők

Amikor a gyorsítótár elavult, HTTP-gyorsítótár érvényesítőket a verzióra a forráskiszolgáló a fájl a gyorsítótárazott verziót összehasonlítására szolgálnak. **Az Azure CDN Standard/prémium verizon** egyaránt támogatja a `ETag` és `Last-Modified` érvényesítők alapértelmezés szerint közben **Azure CDN Standard a Microsoft** és **Azure CDN StandardAkamai** támogatja a csak `Last-Modified` alapértelmezés szerint.

**Az ETag:**
- **Az Azure CDN Standard/prémium verizon** támogatja `ETag` alapértelmezés szerint közben **Azure CDN Standard a Microsoft** és **Azure CDN Akamai Standard** viszont nem.
- `ETag` határozza meg, hogy minden fájl és a egy fájl verziója esetében egyedi karakterlánc. Például: `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- A HTTP 1.1-es verziójában bevezetett, és több aktuális, mint `Last-Modified`. Akkor hasznos, ha az utolsó módosítás dátuma tekintve nehéz lenne meghatározni.
- Támogatja az erős érvényesítése és a gyenge érvényesítése; az Azure CDN azonban csak erős érvényesítési támogatja. Erős ellenőrzés céljából, a két erőforrás semmilyen felelősséget kell bájt-az-bájtos azonos. 
- Gyorsítótár ellenőrzi a fájlt használó `ETag` küldésével egy `If-None-Match` egy vagy több fejlécet `ETag` érvényesítőket a kérésben. Például: `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Ha a kiszolgáló verziója megegyezik egy `ETag` érvényesítő a listán, elküldi a 304 (nem módosított) állapotkódot válaszában. Eltérő verzió esetén a kiszolgáló állapotkód válaszol a 200 (OK) és a frissített erőforrás.

**Utolsó módosítás:**
- A **Azure CDN Standard/prémium verizon** csak `Last-Modified` akkor használatos, ha `ETag` nem része a HTTP-válasz. 
- Megadja a dátum és a forráskiszolgáló által meghatározott, az erőforrás utolsó módosításának időpontja. Például: `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Gyorsítótár ellenőrzi, hogy egy fájl a `Last-Modified` küldésével egy `If-Modified-Since` a dátumot és időpontot a kérelem fejlécében. A forráskiszolgáló hasonlítja össze, hogy a dátum a `Last-Modified` fejléc a legújabb erőforrás. Az erőforrás nem lett módosítva a megadott idő óta, ha a kiszolgáló válaszában 304 (nem módosított) állapotkódot adja vissza. Ha az erőforrás módosult, a kiszolgáló állapotának visszaadása a kód 200 (OK) és a frissített erőforrás.

## <a name="determining-which-files-can-be-cached"></a>Gyorsítótárazható, hogy mely fájlok meghatározása

Nem minden erőforrás gyorsítótárazható. Az alábbi táblázat bemutatja, milyen erőforrásokat gyorsítótárazható, HTTP-válaszának típusa alapján. Erőforrások, amelyek nem felelnek meg az alábbi feltételek mindegyike HTTP-válaszok kiadásaiban nem gyorsítótárazható. A **verizon Azure CDN Premium** csak, használhatja a rules engine testreszabásához néhány ezeket a feltételeket.

|                   | A Microsoft Azure CDN          | Az Azure CDN a Verizontól | Az Azure CDN az Akamaitól        |
|-------------------|-----------------------------------|------------------------|------------------------------|
| HTTP-állapotkódok | 200-as, 203, 206, 300, 301, 410, 416 | 200                    | 200-as, 203, 300, 301, 302, 401-es |
| HTTP-metódusok      | GET, HEAD                         | GET                    | GET                          |
| Fájlméret-korlátozások  | 300 GB                            | 300 GB                 | – Általános webes kézbesítés optimalizálása: 1,8 GB<br />– Online médialejátszás optimalizálása: 1,8 GB<br />– Nagyméretű fájlok optimalizálása: 150 GB |

A **Azure CDN Standard a Microsoft** gyorsítótárazásának működését az erőforrás, a forráskiszolgáló támogatnia kell az összes fő és GET HTTP-kérések és a tartalomhossz értékeket meg kell egyeznie a fő- és GET HTTP válaszokat az az eszköz. A HEAD-kérések a forráskiszolgáló támogatnia kell a HEAD kérésre, és az azonos fejléccel kell válaszolnia, mintha egy GET kéréssel, korábban fogadott.

## <a name="default-caching-behavior"></a>Alapértelmezett gyorsítótárazási viselkedés

A következő táblázat ismerteti az alapértelmezett gyorsítótárazási viselkedésének az Azure CDN termékek és az optimalizálást.

|    | A Microsoft: Általános webes kézbesítés | Verizon: Általános webes kézbesítés | Verizon: DSA | Akamai: Általános webes kézbesítés | Akamai: DSA | Akamai: Nagyméretű fájl letöltése | Akamai: általános vagy VOD médiatartalmak streamelése |
|------------------------|--------|-------|------|--------|------|-------|--------|
| **Fogadja el a forrás**       | Igen    | Igen   | Nem   | Igen    | Nem   | Igen   | Igen    |
| **A CDN-gyorsítótárazás időtartama** | 2 nap |7 nap | None | 7 nap | None | 1 nap | 1 év |

**Fogadja el a forrás**: Megadja, hogy tartsa tiszteletben a [gyorsítótárirányelv-fejlécek támogatott](#http-cache-directive-headers) Ha léteznek az a HTTP-válasz a forráskiszolgálóról.

**A CDN-gyorsítótárazás időtartama**: meghatározza az időt, amelynek egy erőforrást a rendszer gyorsítótárazza az Azure CDN-en. Azonban ha **fogadja el a forrás** Igen értékű, és a forráskiszolgáló a HTTP-válasz tartalmazza a gyorsítótárirányelv-fejléc `Expires` vagy `Cache-Control: max-age`, az Azure CDN használja helyette a fejléc által megadott időtartam értéket. 

## <a name="next-steps"></a>További lépések

- Ismerje meg, hogyan testre szabhatja, és felülbírálják az alapértelmezett gyorsítótárazási viselkedés a CDN-en keresztül gyorsítótár-szabályokkal, tekintse meg [vezérlő Azure CDN gyorsítótárazási viselkedésének gyorsítótár-szabályokkal](cdn-caching-rules.md). 
- Lekérdezési karakterláncok gyorsítótárazásának működése szabályozható használatával kapcsolatban lásd: [Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési karakterláncokkal vezérlése](cdn-query-string.md).



