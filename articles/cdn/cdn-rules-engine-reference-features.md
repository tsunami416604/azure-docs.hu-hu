---
title: Az Azure CDN szabályok motor szolgáltatások |} Microsoft Docs
description: Az Azure CDN referenciadokumentációt szabályok adatbázismotor-szolgáltatások.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: v-deasim
ms.openlocfilehash: fe1f61c7242cf4213b19e9496d557ae7a2253fe8
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="azure-cdn-rules-engine-features"></a>Az Azure CDN szabályok motor-funkciók
Ez a cikk részletes leírását tartalmazza az elérhető szolgáltatások az Azure Content Delivery Network (CDN) [szabálymotor](cdn-rules-engine.md).

A szabály harmadik része a szolgáltatást. A szolgáltatás feltételek egyeznek által azonosított kéréstípus alkalmazott művelet típusa határozza meg.

## <a name="access-features"></a>Hozzáférési funkciókat

Ezek a szolgáltatások célja, hogy a tartalomhoz való hozzáférés szabályozása.

Name (Név) | Cél
-----|--------
[Megtagadja a hozzáférést (403)](#deny-access-403) | Meghatározza, hogy minden kérésnél 403 Tiltott választ utasítja el.
[Jogkivonat hitelesítési](#token-auth) | Azt határozza meg, hogy jogkivonat-alapú hitelesítés kérelemre vonatkozik.
[Jogkivonat hitelesítési Megtagadás kódot](#token-auth-denial-code) | Határozza meg a választ a felhasználó eredményül, amikor egy kérelem jogkivonat-alapú hitelesítés miatt megtagadva.
[Jogkivonat hitelesítési nagybetűket URL-címe](#token-auth-ignore-url-case) | Meghatározza, hogy jogkivonat-alapú hitelesítés által készített URL-cím összehasonlítás kis-és nagybetűket.
[Jogkivonat hitelesítési paraméter](#token-auth-parameter) | Meghatározza, hogy van-e a jogkivonat-alapú hitelesítés lekérdezési karakterlánc paraméter neve.


## <a name="caching-features"></a>Gyorsítótár-funkciók

Ezeket a szolgáltatásokat úgy tervezték, hogy mikor és hogyan a tartalom gyorsítótárazva van testreszabása.

Name (Név) | Cél
-----|--------
[Sávszélesség-paraméterek](#bandwidth-parameters) | Meghatározza, hogy aktívak-e a sávszélesség szabályozási paraméterek (például ec_rate és ec_prebuf).
[Sávszélesség-szabályozás](#bandwidth-throttling) | Azelőtt gyorsítja fel a válasz a pont nyújtó jelenléti (POP) által nyújtott sávszélesség.
[Gyorsítótár megkerülése](#bypass-cache) | Meghatározza, hogy a kérelem kell gyorsítótárazásának mellőzése.
[A Cache-Control fejléc kezelése](#cache-control-header-treatment) | A következő generációja meghatározza `Cache-Control` fejlécek által a POP külső maximális-életkora szolgáltatás aktív esetén.
[Gyorsítótár-kulcs lekérdezési karakterlánc](#cache-key-query-string) | Meghatározza, hogy a gyorsítótár-kulcsot tartalmaz, vagy nem tartalmazza a kéréshez társított lekérdezési karakterlánc-paraméterrel.
[Gyorsítótár-kulcs átdolgozás](#cache-key-rewrite) | Felülírja a kérelemhez társított gyorsítótár-kulcs.
[Fejezze be a gyorsítótár kitöltés](#complete-cache-fill) | Meghatározza, hogy mi történik, amikor egy kérelem egy részleges gyorsítótár-tévesztései eredményez az POP.
[Fájltípusok tömörítése](#compress-file-types) | Határozza meg a tömörített fájlok formátumú fájlokat a kiszolgálón.
[Alapértelmezett belső maximális életkora](#default-internal-max-age) | Meghatározza, hogy az alapértelmezett maximális-életkora időköz POP származási kiszolgáló gyorsítótár ismételt érvényesítése.
[Fejléc kezelés lejár](#expires-header-treatment) | A következő generációja meghatározza `Expires` fejlécek által a POP, ha a külső maximális-életkora szolgáltatás aktív.
[Külső maximális-kor](#external-max-age) | Határozza meg a böngészőben POP a gyorsítótár ismételt érvényesítése maximális-életkora intervallumát.
[Kényszerített belső maximális életkora](#force-internal-max-age) | Meghatározza, hogy a maximális-életkora időköz POP származási kiszolgáló gyorsítótár ismételt érvényesítése.
[H.264 támogatási (HTTP a progresszív letöltés)](#h264-support-http-progressive-download) | Meghatározza, hogy a tartalom adatfolyamként történő küldéséhez használható H.264 fájlformátumok típusú.
[No-Cache kérés elfogadása](#honor-no-cache-request) | Meghatározza, hogy egy HTTP-ügyfél no-cache kérelmeket a rendszer az eredeti kiszolgálóra továbbítja.
[Figyelmen kívül hagyja az eredeti No-Cache](#ignore-origin-no-cache) | Meghatározza, hogy a CDN figyelmen kívül hagyja az egyes irányelvek egy forráskiszolgálóról és kiszolgálása között.
[Hagyja figyelmen kívül Unsatisfiable tartományok](#ignore-unsatisfiable-ranges) | Meghatározza a választ, az ügyfelek számára eredményül, amikor egy kérelem egy 416 kért tartomány nem teljesíthető állapotkódot állítja elő.
[Belső maximális elavult](#internal-max-stale) | Mennyi ideig későbbi, mint a normál lejárati időpont vezérlők gyorsítótárazott eszköz előfordulhat, hogy szolgálható ki POP Ha a csatlakozási pont nem tudja kísérelje meg újra az eredeti kiszolgálóra a gyorsítótárazott objektum érvényesítését.
[A részleges gyorsítótári megosztása](#partial-cache-sharing) | Azt határozza meg, hogy egy kérelem hozhat létre a részlegesen gyorsítótárazott tartalmat.
[Gyorsítótárazott tartalom prevalidate](#prevalidate-cached-content) | Meghatározza, hogy a gyorsítótárazott tartalom abban az esetben jogosult a korai ismételt érvényesítése a TTL lejárata előtt.
[Nulla bájtos gyorsítótárban levő fájlok frissítése](#refresh-zero-byte-cache-files) | Meghatározza, hogy a 0 bájtos gyorsítótár eszköz egy HTTP-ügyfél kérelmet a rendszer hogyan kezelje a POP által.
[Állítsa be a gyorsítótárazható állapotkódok](#set-cacheable-status-codes) | Meghatározza a gyorsítótárazott tartalom eredményező állapotkódok készletét.
[Hiba történt a régi Tartalomkézbesítési](#stale-content-delivery-on-error) | Meghatározza, hogy lejárt a gyorsítótárazott tartalmat a rendszer hiba esetén a gyorsítótár ismételt érvényesítése során vagy a felhasználói forráskiszolgálóról a kért tartalom lekérése közben.
[Elavult Revalidate közben](#stale-while-revalidate) | Így a POP osztja ki a kérelmező elavult ügyfél kerül sor az ismételt érvényesítése közben javítja a teljesítményt.

## <a name="comment-feature"></a>Megjegyzés szolgáltatást

Ez a funkció a szabályban további információkkal szolgál.

Name (Név) | Cél
-----|--------
[Megjegyzés](#comment) | Lehetővé teszi, hogy a Megjegyzés a szabályban lehet hozzáadni.
 
## <a name="header-features"></a>Fejléc szolgáltatások

Ezek a Funkciók hozzáadása, módosítása és a kérés vagy válasz fejlécek törlése tervezték.

Name (Név) | Cél
-----|--------
[Kor válaszfejléc](#age-response-header) | Meghatározza, hogy egy kora válasz fejléce szerepel-e a válaszként küldött a kérelmező.
[Gyorsítótár válaszfejlécek hibakeresése](#debug-cache-response-headers) | Meghatározza, hogy választ magukban az X-EK-Debug válaszfejléc, amely információt nyújt a gyorsítótár-házirend a kért objektum.
[Ügyfél fejléc módosítása](#modify-client-request-header) | Felülírja, hozzáfűzi vagy fejléc töröl egy kérelmet.
[Módosítsa az ügyfél válaszfejléc](#modify-client-response-header) | Felülírja, hozzáfűzi vagy fejléc töröl egy választ.
[Ügyfél IP-egyéni fejléc beállítása](#set-client-ip-custom-header) | Lehetővé teszi, hogy fel kell venni a kérésre egyéni fejléc a kérelmet küldő ügyfél IP-címét.


## <a name="logging-features"></a>Naplózási szolgáltatások

Ezek a szolgáltatások úgy tervezték, hogy testre szabhatja a nyers naplófájlok tárolt adatokat.

Name (Név) | Cél
-----|--------
[1. egyéni mező](#custom-log-field-1) | Meghatározza, hogy a formátum és a tartalom, amely hozzá van rendelve a egyéni mező egy nyers naplófájlban.
[Napló lekérdezési karakterlánc](#log-query-string) | Határozza meg, hogy e lekérdezési karakterláncként van együtt a belépési naplók URL-címet.


<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

### Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled
 

### Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin-features"></a>Forrás-funkciók

Ezek a szolgáltatások úgy tervezték, hogy szabályozza, hogy a CDN hogyan kommunikál az eredeti kiszolgálóra.

Name (Név) | Cél
-----|--------
[Életben tartási kérelmek maximális száma](#maximum-keep-alive-requests) | Keep-Alive kapcsolat kérelmek maximális számát határozza meg, mielőtt le van zárva.
[Proxy különleges fejlécek](#proxy-special-headers) | A rendszer továbbítja a POP az eredeti kiszolgálóra CDN-specifikus kérelemfejléc csoportját határozza meg.


## <a name="specialty-features"></a>Speciális funkciók

Ezek a funkciók összetettebb funkciók haladó felhasználóknak adjon meg.

Name (Név) | Cél
-----|--------
[Kérelmeznék HTTP-metódus](#cacheable-http-methods) | Határozza meg, hogy a hálózat gyorsítótárazható további HTTP-metódus.
[Kérelmeznék kérelem törzse mérete](#cacheable-request-body-size) | Határozza meg a küszöbérték meghatározásához, hogy a FELADÁS egy vagy több válasz gyorsítótárazható.
[Felhasználói változó](#user-variable) | Csak belső használatra.

 
## <a name="url-features"></a>URL-cím szolgáltatások

Ezek a szolgáltatások lehetővé kérést átirányítja vagy egy másik URL-re írni.

Name (Név) | Cél
-----|--------
[Átirányítások követése](#follow-redirects) | Azt határozza meg, hogy az a hely egy ügyfél eredeti kiszolgáló által visszaadott fejlécében megadott állomásnév kérelmek átirányíthatók.
[Az átirányítási URL-címe](#url-redirect) | A hely fejléce kérelmek irányítja át.
[URL-cím átdolgozás](#url-rewrite)  | Felülírja a kérelem URL-CÍMÉT.



## <a name="azure-cdn-rules-engine-features-reference"></a>Az Azure CDN szabályok motor szolgáltatások referencia

---
### <a name="age-response-header"></a>Kor válaszfejléc
**Cél**: meghatározza, hogy egy kora válasz fejléce szerepel-e a válaszként küldött a kérelmező.
Érték|Eredmény
--|--
Engedélyezve | A Korszűrő válaszfejléc a választ küldött a kérelmező tartalmazza.
Letiltva | A Korszűrő válaszfejléc a választ küldött a kérelmező nem tartozik.

**Alapértelmezett viselkedés**: letiltva.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-parameters"></a>Sávszélesség-paraméterek
**Cél:** határozza meg, hogy aktívak-e a sávszélesség szabályozási paraméterek (például ec_rate és ec_prebuf).

Sávszélesség-szabályozási paraméterek meghatározásához, hogy az ügyfél által kért adatátviteli sebesség a egyéni mértékben.

Érték|Eredmény
--|--
Engedélyezve|Lehetővé teszi a sávszélesség-szabályozási kérések tiszteletben POP.
Letiltva|A kapcsolódási pontokat figyelmen kívül hagyja a sávszélesség-szabályozási paraméterek okoz. A kért tartalom általában kiszolgált (Ez azt jelenti, hogy a sávszélesség szabályozása nélkül).

**Alapértelmezés:** engedélyezve van.
 
[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-throttling"></a>Sávszélesség-szabályozás
**Cél:** azelőtt gyorsítja fel a válasz a POP által biztosított sávszélesség.

A következők mindegyikét meg kell határozni, megfelelően állítsa be a sávszélesség-szabályozás.

Beállítás|Leírás
--|--
Kilobájt / másodperc|Ez a beállítás értékre a maximális sávszélesség (Kb / s), amely segítségével a választ.
Prebuf másodpercben|Ez a beállítás a POP várja meg, amíg folyamatban van a sávszélesség a másodpercek számát beállítása Ebben az időszakban nem korlátozott sávszélesség az a célja, megakadályozhatja, hogy a media player a sávszélesség-szabályozás miatt szaggatott vagy pufferelési problémákat észlelő.

**Alapértelmezés:** letiltva.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bypass-cache"></a>Gyorsítótár megkerülése
**Cél:** határozza meg, hogy a kérelem kell gyorsítótárazásának mellőzése.

Érték|Eredmény
--|--
Engedélyezve|Hatására az összes kérelmet, az eredeti kiszolgálóra elhagyása, még akkor is, ha a tartalom korábban POP kerül a gyorsítótárba.
Letiltva|POP azt eredményezi, a gyorsítótár üzletszabályzata előírja a válaszfejlécek definiált gyorsítótár eszközökhöz.

**Alapértelmezés:**

- **A HTTP nagy:** letiltva

<!---
- **ADN:** Enabled

--->

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-http-methods"></a>Kérelmeznék HTTP-metódus
**Cél:** határozza meg, hogy a hálózat gyorsítótárazható további HTTP-metódus.

Kapcsolatos információkat:

- Ez a szolgáltatás azt feltételezi, hogy GET válaszok mindig gyorsítótárazza. Ennek eredményeképpen az első HTTP-metódus nem lehet megtalálható ez a funkció beállítása során.
- Ez a funkció csak a POST HTTP-metódus támogatja. Gyorsítótárazása POST válasz úgy, hogy ez a funkció `POST`.
- Alapértelmezés szerint csak kérelmeket amelynek törzs 14 Kb-nál kisebb gyorsítótárba kerüljenek-e. A szolgáltatással kérelmeznék kérelem törzse mérete törzs kérések maximális méretének beállítása.

**Alapértelmezés:** csak GET válaszok gyorsítótárba kerüljenek-e.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-request-body-size"></a>Kérelmeznék kérelem törzse mérete
**Cél:** határozza meg a küszöbérték meghatározásához, hogy a FELADÁS egy vagy több válasz gyorsítótárazható.

Ez a küszöbérték megadása a kérelem maximális mérete határozza meg. Egy nagyobb kérelemtörzset tartalmazó kérelmek nem lettek gyorsítótárazva.

Kapcsolatos információkat:

- A szolgáltatás csak akkor alkalmazható, ha a POST válaszok jogosultak a gyorsítótárazás. A szolgáltatással kérelmeznék HTTP módszerek POST kérelem gyorsítótárazás engedélyezéséhez.
- A kérelem törzsében van figyelembe venni:
    - x-www-form-urlencoded értékek
    - Egyedi gyorsítótár-kulcs biztosítása
- A nagy méretű kérelem maximális mérete meghatározása hatással lehetnek az adatok kézbesítési teljesítményére.
    - **Javasolt érték:** 14 Kb
    - **Minimális érték:** 1 Kb

**Alapértelmezés:** 14 Kb

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-control-header-treatment"></a>A Cache-Control fejléc kezelése
**Cél:** generációja meghatározza `Cache-Control` fejlécek által a POP, ha a külső maximális-életkora szolgáltatás aktív.

Az ilyen típusú konfigurációs eléréséhez legkönnyebben helyezhető el a külső maximális életkora és a Cache-Control fejléc-kezelés szolgáltatások ugyanabban az utasításban.

Érték|Eredmény
--|--
Felülírás|Biztosítja, hogy a következő műveletek történnek:<br/> -Felülírja a `Cache-Control` fejléc az eredeti kiszolgálón állítja elő. <br/>-Hozzáadja a `Cache-Control` fejléc hozott létre a külső maximális-életkora szolgáltatást, hogy a válasz.
Átengedéses|Biztosítja, hogy a `Cache-Control` a külső maximális-életkora szolgáltatás által előállított fejléc soha nem kerül a választ. <br/> Ha a forráskiszolgáló hoz létre egy `Cache-Control` fejléc, haladnak keresztül a végfelhasználók. <br/> Ha a forráskiszolgáló nem hoz egy `Cache-Control` fejlécben, akkor ez a beállítás okozhat a válasz fejléce nem tartalmazza a `Cache-Control` fejléc.
Ha hiányoznak hozzáadása|Ha egy `Cache-Control` fejléc nem érkezett meg a forráskiszolgálóról, akkor ez a beállítás nagyobb a `Cache-Control` a külső maximális-életkora szolgáltatás által előállított fejléc. Ez a beállítás akkor hasznos ahhoz, hogy az összes eszköz hozzárendelve egy `Cache-Control` fejléc.
Eltávolítás| Ez a beállítás biztosítja, hogy egy `Cache-Control` fejléc nem része a válasz fejrészét. Ha egy `Cache-Control` fejléc már hozzá van rendelve, akkor a rendszer eltávolítja a válasz fejrészét.

**Alapértelmezés:** felülírásához.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-query-string"></a>Gyorsítótár-kulcs lekérdezési karakterlánc
**Cél:** meghatározza, hogy a gyorsítótár-kulcsot tartalmaz, vagy nem tartalmazza a kéréshez társított lekérdezési karakterlánc-paraméterrel.

Kapcsolatos információkat:

- Adjon meg egy vagy több lekérdezési karakterlánc paraméter neve, és minden paraméternév külön az egy szóköz.
- Ez a szolgáltatás határozza meg, hogy lekérdezési karakterlánc paraméterei vannak, illetve tiltani szeretné a gyorsítótár-kulcs. További információ az egyes lehetőségek az alábbi táblázatban.

Típus|Leírás
--|--
 Belefoglalás|  Azt jelzi, hogy minden egyes megadott paraméter szerepelnie kell a gyorsítótár-kulcsot. Egy egyedi gyorsítótár-kulcs előállítási minden kérelemhez, amely a lekérdezési karakterlánc paraméterként, ez a szolgáltatás definiált egyedi értéket tartalmaz. 
 Az összes  |Azt jelzi, hogy egyedi gyorsítótár-kulcs az egyes az eszköz egyedi lekérdezési karakterláncot tartalmazó kérelmek. Az ilyen típusú konfigurációs általában nem ajánlott, mert csekély találatot eredményező gyorsítótárbeli kereséseinek vezethet. Találatot eredményező gyorsítótárbeli kereséseinek száma kevés fokozott terhelést jelent a az eredeti kiszolgálóra, mert azt kell további-kérelmek kiszolgálását. Ez a konfiguráció másolatot készít a gyorsítótár-viselkedést "egyedi-gyorsítótár" a lekérdezési karakterlánc-gyorsítótár oldalon néven ismert. 
 Kizárás | Azt jelzi, hogy a gyorsítótár-kulcs csak a megadott paraméterek nem. Minden más lekérdezési karakterlánc-paraméter szerepel a gyorsítótár-kulcsot. 
 Az összes kihagyása  |Azt jelzi, hogy az összes lekérdezési karakterlánc paraméterei nem tartoznak a gyorsítótár-kulcsot. Ez a konfiguráció másolatot készít a "standard-gyorsítótár" alapértelmezett gyorsítótárazásának a lekérdezési karakterlánc-gyorsítótár oldalon.  

A szabályok motor testreszabása, amelyben lekérdezési karakterláncok gyorsítótárazása megvalósítása módon teszi lehetővé. Megadhatja például, hogy csak az egyes helyek vagy fájltípusok lekérdezési karakterláncok gyorsítótárazása történik.

A "no-cache" lekérdezési karakterláncot a lekérdezés-karakterlánc-gyorsítótár oldalon gyorsítótárazásának duplikálásához hozzon létre egy szabályt, amely egy URL-cím lekérdezés helyettesítő egyezés feltételt, valamint a Mellőzés gyorsítótár szolgáltatás tartalmaz. Állítsa be az URL-cím lekérdezés helyettesítő egyezés feltétel csillagot (*).

>[!IMPORTANT] 
> Token engedélyezési bármilyen útvonalat ehhez a fiókhoz engedélyezett, ha a standard-gyorsítótár üzemmódban az egyetlen mód a lekérdezési karakterláncok gyorsítótárazása használható. További információkért lásd: [Az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési karakterláncokkal](cdn-query-string-premium.md).

#### <a name="sample-scenarios"></a>Mintaforgatókönyvek

A következő példa a szolgáltatáshoz tartozó biztosít egy kérelemmintát és az alapértelmezett gyorsítótár-kulcs:

- **Mintakérelem:** http://wpc.0001.&lt; Tartomány&gt;/800001/Origin/folder/asset.htm?sessionid=1234 & nyelvi = EN & userid = 01
- **Alapértelmezett gyorsítótár-kulcs:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Belefoglalás

Mintakonfiguráció:

- **Típus:** tartalmazza
- **Paraméterek:** nyelv

Ez a fajta konfiguráció a következő lekérdezési karakterlánc paraméter gyorsítótár-kulcsot hoz létre:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Az összes

Mintakonfiguráció:

- **Típus:** az összes

Ez a fajta konfiguráció a következő lekérdezési karakterlánc paraméter gyorsítótár-kulcsot hoz létre:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Kizárás

Mintakonfiguráció:

- **Típus:** kizárása
- **Paraméterek:** sessionid userid

Ez a fajta konfiguráció a következő lekérdezési karakterlánc paraméter gyorsítótár-kulcsot hoz létre:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Az összes kihagyása

Mintakonfiguráció:

- **Típus:** összes kizárása

Ez a fajta konfiguráció a következő lekérdezési karakterlánc paraméter gyorsítótár-kulcsot hoz létre:

    /800001/Origin/folder/asset.htm

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-rewrite"></a>Gyorsítótár-kulcs átdolgozás
**Cél:** újraírja a kérelemhez társított gyorsítótár-kulcs.

A gyorsítótár kulcsának pedig a relatív azonosító egy eszköz gyorsítótárazás céljára. Ez azt jelenti a kiszolgálók ellenőrizze az elérési úttal megfelelően eszköz gyorsítótárazott verzió a gyorsítótár-kulcs által definiált konfigurációjának kialakításához.

Ez a szolgáltatás konfigurálása a következő beállításokat is meghatározhat:

Beállítás|Leírás
--|--
Eredeti elérési útja| A relatív elérési út megadása, amelynek gyorsítótárkulcshoz rendszer újraírja kérelmek típusú. Relatív elérési útnak egy alap forrás elérési útvonalának kiválasztásával, és egy Reguláriskifejezés-mintának majd meghatározása definiálhatók.
Új elérési útja|Adja meg az új gyorsítótár-kulcs relatív elérési útja. Relatív elérési útnak egy alap forrás elérési útvonalának kiválasztásával, és egy Reguláriskifejezés-mintának majd meghatározása definiálhatók. A relatív elérési út dinamikusan összeállított használatával [HTTP változók](cdn-http-variables.md).
**Alapértelmezés:** egy kérelem gyorsítótár-kulcs határozza meg a kérelem URI-azonosítója.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="comment"></a>Megjegyzés
**Cél:** lehetővé teszi, hogy a Megjegyzés a szabályban lehet hozzáadni.

Kiegészítő információt nyújt az általános célú miért egy adott feltétel felel meg, vagy a szolgáltatás hozzáadva a szabály egy szabály vagy a funkció használatát is.

Kapcsolatos információkat:

- Legfeljebb 150 karakter adható meg.
- Csak alfanumerikus karaktereket használjon.
- Ez a funkció nem befolyásolja a szabály viselkedését. Csupán célja, hogy adjon meg egy olyan területre, ahol megadhatja a későbbi használatra és, amely segíthet a szabály hibaelhárítási.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="complete-cache-fill"></a>Fejezze be a gyorsítótár kitöltés
**Cél:** határozza meg, mi történik, amikor egy kérelem egy részleges gyorsítótár-tévesztései a POP eredményez.

A részleges gyorsítótár-tévesztései ismerteti a gyorsítótár állapota az adott eszköz, amely nem teljesen le vannak töltve a POP számára. Ha az eszköz csak részlegesen gyorsítótárazott a POP, majd az adott eszköz számára a következő kérés továbbítja újra az eredeti kiszolgálóra.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
A részleges gyorsítótár-tévesztései rendszerint azért fordul elő, miután a felhasználó megszakít egy letölthető vagy eszközök kizárólag kért tartomány HTTP-kérelmek használatával. Ez a funkció akkor a leghasznosabb nagy eszközök, amelyek általában nincsenek letöltve (például videók) szolgáltatásra. Ennek köszönhetően ez a funkció alapértelmezés szerint engedélyezve van a HTTP nagy platformon. Le van tiltva az összes többi platformon.

Tartsa meg az alapértelmezett konfigurációt, a HTTP nagy platform, mivel csökkenti az ügyfél eredeti kiszolgálóra, és növeli a sebesség, amellyel az ügyfelek a tartalom letöltése.

Érték|Eredmény
--|--
Engedélyezve|Visszaállítja az alapértelmezett viselkedés. Az alapértelmezett viselkedés a forráskiszolgálóról az eszköz a háttérben történő kezdeményezése a POP kényszerítése. Mely után az eszköz a helyi gyorsítótárban a POP-ra lesz.
Letiltva|POP megakadályozza az az eszköz a háttérben történő végrehajtásához. Az eredménye, hogy az adott régió eszköz a következő kérés hatására kérheti a felhasználói forráskiszolgálóról kapcsolódási pont.

**Alapértelmezés:** engedélyezve van.

#### <a name="compatibility"></a>Kompatibilitás
Mely gyorsítótárában beállítások követi módon, mert ez a funkció nem rendelhető hozzá a következő feltételek egyeznek: 
- SZÁMOT
- Ügyfél IP-címe
- Cookie-k paraméter
- Cookie-k paraméter Regex
- Ország
- Eszköz
- Peremhálózati Cname
- Hivatkozó tartomány
- Kérelem fejléc szövegkonstans
- Kérelem fejléc Regex
- Kérelem fejléc helyettesítő
- Kérési módszer
- Kérelem séma
- Lekérdezés-szövegkonstans URL-címe
- Lekérdezés Regex URL-címe
- Lekérdezés helyettesítő URL-címe
- URL-cím lekérdezési paraméter

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="compress-file-types"></a>Fájltípusok tömörítése
**Cél:** formátumú fájlokat a tömörített fájlok meghatározása a kiszolgálón.

Egy fájl formátum használatával az Internet az adathordozó típusát (például Content-Type) adható meg. Internet médiatípus nincs platformfüggetlen metaadatok, amely lehetővé teszi a kiszolgálók egy adott eszköz a fájlformátum azonosításához. Közös Internet adathordozó-típusok listája lejjebb tekinthetők meg.

Internet adathordozó-típus|Leírás
--|--
egyszerű szöveg|Egyszerű szöveges fájlokról
text/html| HTML-fájlok
Text/css|Egymásra épülő stíluslap (CSS)
application/x-javascript|Javascript
alkalmazás/javascript|Javascript
Kapcsolatos információkat:

- Adjon meg egy szóköz egyenként a határoló többféle adathordozó-típust. 
- Ez a funkció csak tömöríti az eszközök, amelynek mérete legalább 1 MB. A kiszolgálók nem tömöríti nagyobb eszközök.
- Bizonyos típusú tartalmakra, például a video-lemezképek és lejátszása eszközök (például JPG, MP3, MP4 stb.), már tömörített. Ilyen típusú eszközök további tömörítés nem jelentősen romolhat, fájlméret, mert javasoljuk, hogy nem engedélyezi a tömörítési rajtuk.
- Helyettesítő karakterek, például a csillag, nem támogatottak.
- Csak ez a szolgáltatás hozzáadni egy szabályt, győződjön meg arról, hogy állítsa a tömörítési letiltott beállítás a tömörítési oldalon, a platform, amelyekre ez a szabály vonatkozik.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="custom-log-field-1"></a>1. egyéni mező
**Cél:** meghatározza, hogy a formátum és a tartalom, amely a nyers naplófájl egyéni napló mezője lesz hozzárendelve.

Az egyéni mező határozza meg, mely kérés- és a fejléc értékei tárolódnak a naplófájlok teszi lehetővé.

Alapértelmezés szerint az egyéni mező neve "x-ec_custom-1." Ez a mező nevét testre szabható a nyers naplófájl-beállítások lapon.

A kérés- és válaszfejlécekről megadásának formátumát az alábbiak szerint:

Fejléc típusa|Formátum|Példák
-|-|-
Fejléc|%{[RequestHeader]()}[i]() | %{Accept-Encoding}i <br/> {Hivatkozó} i <br/> %{Authorization}i
Válaszfejléc|%{[ResponseHeader]()}[o]()| %{Age}o <br/> %{Content-Type}o <br/> A(z) % {cookie-k} o

Kapcsolatos információkat:

- Egy egyéni napló mező fejlécmezők és egyszerű szöveg tetszőleges kombinációját tartalmazhatja.
- Az ebben a mezőben érvényes karakterek a következők: alfanumerikus (0-9, a – z és A-Z), kötőjeleket, kettőspontokat, pontosvesszővel kell elválasztani, aposztrófot, vesszővel válassza el egymástól, időszakok, aláhúzásjeleket, egyenlőségjelet, kerek zárójeleket tartalmazhatnak, zárójelek és szóközöket. A százalékos szimbólum és a kapcsos zárójelek csak akkor vannak engedélyezve, ha egy fejlécmező meghatározására szolgál.
- A megadott fejléc mezők helyesírás meg kell egyeznie a kívánt kérelem/válasz-fejléc nevét.
- Ha meg szeretné határozni több fejlécek, a elválasztó ezzel jelezheti minden fejléc. Használhat például rövidítése minden fejléc:
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o 

**Alapértelmezett érték:** -

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Gyorsítótár válaszfejlécek hibakeresése
**Cél:** határozza meg, hogy a válasz tartalmazhatnak [X-EK-Debug válaszfejlécek](cdn-http-debug-headers.md), amely információt nyújt a gyorsítótár-házirend a kért objektum.

Hibakeresési gyorsítótári választ fejlécek fog szerepelni a válasz a következő két teljesülése esetén:

- A gyorsítótár válaszfejlécek Debug funkció engedélyezve van a megadott kérésre.
- A megadott kérelem a debug gyorsítótár válaszfejlécek a válaszban szereplő csoportját határozza meg.

Hibakeresési gyorsítótári választ fejlécek kérheti a a kérelem a következő fejléc és a megadott irányelveket is beleértve:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Példa**

X-EK-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Érték|Eredmény
-|-
Engedélyezve|Hibakeresési gyorsítótár válaszfejlécek kérelmek visszaadható egy választ, amely az X-EK-Debug fejlécet tartalmaz.
Letiltva|Az X-EK-Debug válaszfejléc nem kerülnek be a válasz.

**Alapértelmezés:** letiltva.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Alapértelmezett belső maximális életkora
**Cél:** meghatározza, hogy az alapértelmezett maximális-életkora időköz POP származási kiszolgáló gyorsítótár ismételt érvényesítése. Ez azt jelenti mennyi ideig POP előtt adja meg, hogy ellenőrzi, hogy a gyorsítótárazott eszköz megfelel-e az eszköz a forrás kiszolgálón tárolt.

Kapcsolatos információkat:

- Ez a művelet csak akkor kerül sor a válaszok, egy forráskiszolgálóról, amely nem társít egy maximális-életkora arra utal, hogy az a `Cache-Control` vagy `Expires` fejléc.
- Ez a művelet nem kerül sor eszközök fontosságúnak ítélt nem gyorsítótárazható.
- Ez a művelet nem befolyásolja a POP-gyorsítótár revalidations böngészőben. Az ilyen típusú revalidations határozza meg a `Cache-Control` vagy `Expires` a böngésző, amely testre szabható a külső maximális-életkora szolgáltatással küldött fejléceket.
- Ez a művelet eredménye nem rendelkezik a válaszfejlécek és a tartalom a tartalom POP által visszaadott megfigyelhető hatással, de POP számára az eredeti kiszolgálóra küldött ismételt érvényesítése forgalom mennyisége hatással lehet.
- Ez a szolgáltatás által konfigurálása:
    - Az állapotkód: egy alapértelmezett belső maximális-életkora alkalmazhassa kiválasztása.
    - Adja meg egy egész számot, és jelölje be a kívánt időegység (például másodperc, perc, óra, stb.). Ez az érték határozza meg az alapértelmezett belső maximális-életkora időköz.

- Egy alapértelmezett belső maximális-életkora 7 napos időszak a kéréseket, amely nincs hozzárendelve egy maximális-életkora arra utal, hogy a beállítást "Ki" időegységét rendeli a `Cache-Control` vagy `Expires` fejléc.

**Alapértelmezett érték:** 7 nap

#### <a name="compatibility"></a>Kompatibilitás
Mely gyorsítótárában beállítások követi módon, mert ez a funkció nem rendelhető hozzá a következő feltételek egyeznek: 
- SZÁMOT
- Ügyfél IP-címe
- Cookie-k paraméter
- Cookie-k paraméter Regex
- Ország
- Eszköz
- Peremhálózati Cname
- Hivatkozó tartomány
- Kérelem fejléc szövegkonstans
- Kérelem fejléc Regex
- Kérelem fejléc helyettesítő
- Kérési módszer
- Kérelem séma
- Lekérdezés-szövegkonstans URL-címe
- Lekérdezés Regex URL-címe
- Lekérdezés helyettesítő URL-címe
- URL-cím lekérdezési paraméter

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="deny-access-403"></a>Megtagadja a hozzáférést (403)
**Cél**: meghatározza, hogy minden kérésnél 403 Tiltott választ utasítja el.

Érték | Eredmény
------|-------
Engedélyezve| Hatására az összes kérelmet, amelyek teljesítik a megfelelő – 403 Tiltott választ ad vissza lesznek utasítva.
Letiltva| Visszaállítja az alapértelmezett viselkedés. Az alapértelmezett viselkedés a forrás nyomtatókiszolgálón visszaadott válasz típusának meghatározása.

**Alapértelmezett viselkedés**: letiltva

> [!TIP]
   > Egy lehetséges a funkció használata társítsa a kérelem fejléc egyezik feltételt letiltja a HTTP-hivatkozó kérelmei által használt beágyazott hivatkozások a tartalmakhoz való hozzáférést.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="expires-header-treatment"></a>Fejléc kezelés lejár
**Cél:** generációja meghatározza `Expires` fejlécek által a POP, ha a külső maximális-életkora szolgáltatás aktív.

Az ilyen típusú konfigurációs eléréséhez legkönnyebben helyezhető el a külső maximális életkora és a lejárati fejléc-kezelés szolgáltatások ugyanabban az utasításban.

Érték|Eredmény
--|--
Felülírás|Biztosítja, hogy a következő műveletek akkor kerül sor:<br/>-Felülírja a `Expires` fejléc az eredeti kiszolgálón állítja elő.<br/>-Hozzáadja a `Expires` fejléc hozott létre a külső maximális-életkora szolgáltatást, hogy a válasz.
Átengedéses|Biztosítja, hogy a `Expires` a külső maximális-életkora szolgáltatás által előállított fejléc soha nem kerül a választ. <br/> Ha a forráskiszolgáló hoz létre egy `Expires` fejléc, akkor továbbítja a felhasználó. <br/>Ha a forráskiszolgáló nem hoz egy `Expires` fejlécben, akkor ez a beállítás okozhat a válasz fejléce nem tartalmazza egy `Expires` fejléc.
Ha hiányoznak hozzáadása| Ha egy `Expires` fejléc nem érkezett meg a forráskiszolgálóról, akkor ez a beállítás nagyobb a `Expires` a külső maximális-életkora szolgáltatás által előállított fejléc. Ez a beállítás akkor hasznos ahhoz, hogy az összes eszköz kioszt egy `Expires` fejléc.
Eltávolítás| Biztosítja, hogy egy `Expires` fejléc nem része a válasz fejrészét. Ha egy `Expires` fejléc már hozzá van rendelve, akkor a rendszer eltávolítja a válasz fejrészét.

**Alapértelmezés:** felülírása

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="external-max-age"></a>Külső maximális-kor
**Cél:** határozza meg a böngészőben POP a gyorsítótár ismételt érvényesítése maximális-életkora intervallumát. Ez azt jelenti mennyi ideig, mielőtt egy böngésző adja meg, hogy egy eszköz POP az új verzióhoz tartozó ellenőrizheti.

A funkció engedélyezése hoz létre `Cache-Control: max-age` és `Expires` a POP fejléceket, és küldje el a HTTP-ügyfél. Alapértelmezés szerint ezek a fejlécek felülírja ezeket a fejléceket hozta létre az eredeti kiszolgálóra. Azonban a Cache-Control fejléc kezelését és a lejárati fejléc-kezelés szolgáltatások használható útválasztását ezen viselkedés megváltoztatásához.

Kapcsolatos információkat:

- Ez a művelet nem befolyásolja POP származási kiszolgáló gyorsítótár revalidations. Az ilyen típusú revalidations határozza meg a `Cache-Control` és `Expires` fejlécek kapott a forráskiszolgálóról, és az alapértelmezett belső maximális-kor és a kényszerített belső maximális életkora szolgáltatásokkal testre szabható.
- Adja meg egy egész számot, majd válassza a kívánt időegység (például másodperc, perc, óra, stb.) Ez a szolgáltatás konfigurálása
- Ez a funkció beállítása a negatív értékre után a POP küldése egy `Cache-Control: no-cache` és egy `Expires` minden a böngészőnek adott válaszban a korábban beállított idő. Bár egy HTTP-ügyfél nem lesz gyorsítótárazza a választ, ez a beállítás lesz nem vonatkozik a POP-gyorsítótárazza a választ a forráskiszolgálóról.
- Az attribútum "off" időegységét letiltja ezt a szolgáltatást. A `Cache-Control` és `Expires` gyorsítótárazza a választ az eredeti kiszolgáló fejlécek továbbítja a böngészőben.

**Alapértelmezés:** kikapcsolása

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="follow-redirects"></a>Átirányítások követése
**Cél:** határozza meg, hogy az a hely egy ügyfél eredeti kiszolgáló által visszaadott fejlécében megadott állomásnév kérelmek átirányíthatók.

Kapcsolatos információkat:

- Kérelmek csak átirányíthatók szegélyhez ugyanahhoz a platformhoz megfelelő CNAME.

Érték|Eredmény
-|-
Engedélyezve|Kérelmek átirányíthatók.
Letiltva|Kérelmek nem irányítja át.

**Alapértelmezés:** letiltva.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="force-internal-max-age"></a>Kényszerített belső maximális életkora
**Cél:** meghatározza, hogy a maximális-életkora időköz POP származási kiszolgáló gyorsítótár ismételt érvényesítése. Ez azt jelenti mennyi ideig POP előtt adja meg, hogy ellenőrizheti, hogy a gyorsítótárazott eszköz megfelel-e az eszköz a forrás kiszolgálón tárolt.

Kapcsolatos információkat:

- Ez a szolgáltatás felül fogja írni a meghatározott maximális-életkora időköz `Cache-Control` vagy `Expires` fejlécek egy forráskiszolgálóról jön létre.
- Ez a funkció nem befolyásolja a POP-gyorsítótár revalidations böngészőben. Az ilyen típusú revalidations határozza meg a `Cache-Control` vagy `Expires` fejlécek, a böngésző kap.
- Ez a szolgáltatás nem rendelkezik a választ igénylő kézbesíti az POP megfigyelhető hatással. Azonban a POP számára az eredeti kiszolgálóra küldött ismételt érvényesítése forgalom mennyisége hatással lehet.
- Ez a szolgáltatás által konfigurálása:
    - Válassza az állapotkódot, amely egy belső maximális életkora alkalmazza.
    - Adja meg egy egész számot, és kiválasztja a kívánt időegység (például másodperc, perc, óra, stb.). Ez az érték határozza meg a kérelem maximális-életkora időköz.

- Az attribútum "off" időegységét letiltja ezt a szolgáltatást. Belső maximális-életkora időközönkénti nem rendeli hozzá a kért eszközök. Ha az eredeti fejléc nem tartalmaz a gyorsítótárazási információkat, majd az eszköz a gyorsítótárba fognak kerülni az alapértelmezett belső maximális-életkora funkció az aktív beállításnak megfelelően.

**Alapértelmezés:** kikapcsolása

#### <a name="compatibility"></a>Kompatibilitás
Mely gyorsítótárában beállítások követi módon, mert ez a funkció nem rendelhető hozzá a következő feltételek egyeznek: 
- SZÁMOT
- Ügyfél IP-címe
- Cookie-k paraméter
- Cookie-k paraméter Regex
- Ország
- Eszköz
- Peremhálózati Cname
- Hivatkozó tartomány
- Kérelem fejléc szövegkonstans
- Kérelem fejléc Regex
- Kérelem fejléc helyettesítő
- Kérési módszer
- Kérelem séma
- Lekérdezés-szövegkonstans URL-címe
- Lekérdezés Regex URL-címe
- Lekérdezés helyettesítő URL-címe
- URL-cím lekérdezési paraméter

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="h264-support-http-progressive-download"></a>H.264 támogatási (HTTP a progresszív letöltés)
**Cél:** meghatározza, hogy a tartalom adatfolyamként történő küldéséhez használható H.264 fájlformátumok típusú.

Kapcsolatos információkat:

- A kívánt fájlkiterjesztéseket beállítás engedélyezett H.264 fájlnév-kiterjesztések egy szóközökkel elválasztott kötetnevek kulcstulajdonságokat határozza meg. A kívánt fájlkiterjesztéseket beállítás felülírja az alapértelmezett viselkedés. Ha ezt a beállítást, többek között azokat a fájlkiterjesztéseket karbantartásához MP4 és F4V támogatása. 
- Egy időszakot minden fájlnév-kiterjesztés megadása (például _.mp4_, _.f4v_).

**Alapértelmezés:** alapértelmezés szerint HTTP progresszív letöltés MP4 és F4V media támogatja.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="honor-no-cache-request"></a>No-Cache kérés elfogadása
**Cél:** határozza meg, hogy egy HTTP-ügyfél által no-cache kérelmeket továbbítja az eredeti kiszolgálóra.

No-cache kérelmet akkor fordul elő, ha a HTTP-ügyfél küld egy `Cache-Control: no-cache` és/vagy `Pragma: no-cache` a HTTP-kérelem fejléce.

Érték|Eredmény
--|--
Engedélyezve|Lehetővé teszi, hogy egy HTTP-ügyfél no-cache kéri, hogy az eredeti kiszolgálóra továbbítja, és az eredeti kiszolgálóra visszatérhet a válaszfejlécek és a szervezet a POP keresztül vissza a HTTP-ügyfél.
Letiltva|Visszaállítja az alapértelmezett viselkedés. Az alapértelmezett viselkedés célja no-cache kérelmeket az eredeti kiszolgálóra történő továbbítását.

Minden éles forgalomhoz lehetőleg Ez a szolgáltatás le van tiltva alapértelmezett állapotában hagyja. Ellenkező esetben származási kiszolgálók fog védelme nem biztosítható a végfelhasználók számára, akik véletlenül indíthat sok no-cache kérelem weblapok frissítésekor vagy a a számos népszerű médialejátszókhoz, amely minden videó kérelemmel no-cache fejléc küldése van kódolva. Ez a funkció azonban bizonyos nem éles átmeneti vagy tesztelés könyvtárak, annak érdekében, hogy a forráskiszolgálóról igény szerinti lekérése friss tartalom alkalmazandó hasznos lehet.

A gyorsítótár állapotát, a kérelmeket, amelyek miatt ez a funkció az eredeti kiszolgálóra továbbítható érték jelentett `TCP_Client_Refresh_Miss`. A gyorsítótár állapotok jelentést, amely nem érhető el az alapvető jelentéskészítési modul hasonlóan, a gyorsítótár állapot szerint statisztikai információkat nyújt. Ez a jelentés lehetővé teszi, hogy számát és százalékos továbbított kérelmek nyomon követhetők a miatt ez a funkció az eredeti kiszolgálóra.

**Alapértelmezés:** letiltva.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-origin-no-cache"></a>Figyelmen kívül hagyja az eredeti No-Cache
**Cél:** határozza meg, hogy a CDN figyelmen kívül hagyja a következő irányelvek kiszolgált egy forráskiszolgálóról:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Kapcsolatos információkat:

- Ez a szolgáltatás konfigurálja, amelynek a fenti irányelvek figyelmen kívül hagyja az állapotkódnak szóközökkel elválasztott kötetnevek listáját meghatározásával.
- Ez a szolgáltatás érvényes állapotkódjai készletét vannak: 200, 203, 300, 301, 302, 305, 307, 400, 401-es, 402, 403-as, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502-es, 503-as, 504, és 505.
- Üres értékre állításával tiltsa le ezt a szolgáltatást.

**Alapértelmezés:** az alapértelmezett viselkedést, hogy a fenti irányelvek figyelembe veszi.

#### <a name="compatibility"></a>Kompatibilitás
Mely gyorsítótárában beállítások követi módon, mert ez a funkció nem rendelhető hozzá a következő feltételek egyeznek: 
- SZÁMOT
- Ügyfél IP-címe
- Cookie-k paraméter
- Cookie-k paraméter Regex
- Ország
- Eszköz
- Peremhálózati Cname
- Hivatkozó tartomány
- Kérelem fejléc szövegkonstans
- Kérelem fejléc Regex
- Kérelem fejléc helyettesítő
- Kérési módszer
- Kérelem séma
- Lekérdezés-szövegkonstans URL-címe
- Lekérdezés Regex URL-címe
- Lekérdezés helyettesítő URL-címe
- URL-cím lekérdezési paraméter

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-unsatisfiable-ranges"></a>Hagyja figyelmen kívül Unsatisfiable tartományok 
**Cél:** meghatározza, hogy az ügyfelek számára megjelenített fog, amikor egy kérelem egy 416 kért tartomány nem teljesíthető állapotkódot állítja elő a választ.

Alapértelmezés szerint ez az állapot kód érték érkezett vissza a megadott bájttartomány-kérelem nem tud teljesíteni POP és egy If tartományon kívüli kérelmet fejlécmező nem volt megadva.

Érték|Eredmény
-|-
Engedélyezve|Megakadályozza, hogy a POP egy 416 kért tartomány nem teljesíthető állapotkód: Érvénytelen bájttartomány-kérelem válaszol. Ehelyett a kiszolgálók biztosítanak a kért eszköz, és egy 200 OK vissza az ügyfélnek.
Letiltva|Visszaállítja az alapértelmezett viselkedés. Az alapértelmezett viselkedés a 416 kért tartomány nem teljesíthető állapotkód: tiszteletben.

**Alapértelmezés:** letiltva.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="internal-max-stale"></a>Belső maximális elavult
**Cél:** vezérlők mennyi ideig későbbi, mint a normál lejárati időpont gyorsítótárazott eszköz előfordulhat, hogy szolgálható ki POP, ha a csatlakozási pont nem tudja kísérelje meg újra az eredeti kiszolgálóra a gyorsítótárazott objektum érvényesítését.

Normál esetben ha az eszköz maximális-életkora időtartam lejár, a POP elküld ismételt érvényesítése kérelmet az eredeti kiszolgálóra. A forrás server lesz majd vagy 304 válaszol ahhoz, hogy megkapja a POP friss nem módosított címbérlet a gyorsítótárazott eszköz – a 200 OK a POP biztosításához a gyorsítótárazott eszköz frissített verzióra.

A csatlakozási pont nem tud kapcsolatot az eredeti kiszolgálóra közben ilyen egy ismételt érvényesítése, majd a belső maximális elavult funkció szabályozza e, és mennyi ideig, ha a POP továbbra is a most már elavult eszköz szolgál.

Vegye figyelembe, hogy ez alatt az időtartam alatt kezdődik, amikor az eszköz maximális-életkora lejár, nem a sikertelen ismételt érvényesítése esetén. A maximális idő, amikor egy eszköz sikeres ismételt érvényesítése nélkül szolgáltatható ezért maximális-kor és a max elavult kombinációja által megadott idő. Például ha egy eszköz került a gyorsítótárba, 9:00 30 perc maximális életkora és 15 percenként maximális elavult, majd egy sikertelen ismételt érvényesítése kísérlet 9:44 eredményeznének fogadása a elavult gyorsítótárazott objektumhoz, amíg egy sikertelen ismételt érvényesítése kísérlet 9:46 eredményeznének a en felhasználó d felhasználói 504-es számú átjáró időtúllépése fogadásakor.

Bármely ezt a szolgáltatást felváltotta a konfigurált értéket `Cache-Control: must-revalidate` vagy `Cache-Control: proxy-revalidate` fejlécek kapott a forráskiszolgálóról. Ha ezeket a fejléceket fogadásakor. a forráskiszolgálóról, ha egy eszköz kezdetben gyorsítótárazza, majd a POP nem teljesíti a gyorsítótárazott elavult eszköz. Ebben az esetben ha a csatlakozási pont nem tudja kísérelje meg újra érvényesítését az eredeti amikor az eszköz maximális-életkora időköz lejárt, a POP hibaüzenetet adja vissza egy 504-es számú átjáró időtúllépése.

Kapcsolatos információkat:

- Ez a szolgáltatás által konfigurálása:
    - Az állapotkódot, amelynek maximális elavult alkalmazandó kiválasztása.
    - Adja meg egy egész számot, és jelölje be a kívánt időegység (például másodperc, perc, óra, stb.). Ez az érték határozza meg a belső maximális elavult alkalmazni fogja őket.

- Az attribútum "off" időegységét letiltja ezt a szolgáltatást. A gyorsítótárazott eszköz nem tudja megjeleníteni a normál lejárati ideje túl.

**Alapértelmezés:** két percen belül megkezdődik

#### <a name="compatibility"></a>Kompatibilitás
Mely gyorsítótárában beállítások követi módon, mert ez a funkció nem rendelhető hozzá a következő feltételek egyeznek: 
- SZÁMOT
- Ügyfél IP-címe
- Cookie-k paraméter
- Cookie-k paraméter Regex
- Ország
- Eszköz
- Peremhálózati Cname
- Hivatkozó tartomány
- Kérelem fejléc szövegkonstans
- Kérelem fejléc Regex
- Kérelem fejléc helyettesítő
- Kérési módszer
- Kérelem séma
- Lekérdezés-szövegkonstans URL-címe
- Lekérdezés Regex URL-címe
- Lekérdezés helyettesítő URL-címe
- URL-cím lekérdezési paraméter

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="log-query-string"></a>Napló lekérdezési karakterlánc
**Cél:** határozza meg, hogy egy lekérdezési karakterlánc belépési naplók URL-cím mellett kell tárolni.

Érték|Eredmény
-|-
Engedélyezve|A hozzáférési napló URL-címek rögzítésekor lehetővé teszi, hogy a lekérdezési karakterláncok tárolására. Ha egy URL-címe nem tartalmazhat lekérdezési karakterláncot, majd ezt a beállítást nem lesz hatása.
Letiltva|Visszaállítja az alapértelmezett viselkedés. Az alapértelmezett viselkedés URL-címek egy hozzáférési napló rögzítésekor a lekérdezési karakterláncok figyelmen kívül.

**Alapértelmezés:** letiltva.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="maximum-keep-alive-requests"></a>Életben tartási kérelmek maximális száma
**Cél:** életben tartási kapcsolat kérelmek maximális számát határozza meg, mielőtt le van zárva.

A kérelmek maximális számát beállítása alacsony értékre nem ajánlott, és teljesítménycsökkenést eredményezhet.

Kapcsolatos információkat:

- Adja meg ennek az értéknek egész számként.
- Nem tartalmaznak vesszőt és pontot a megadott értékkel.

**Alapértelmezett érték:** 10 000 kérelem

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-request-header"></a>Ügyfél fejléc módosítása
**Cél:** minden kérelmet tartalmaz kérelemfejléc, amely azt írja le. Ez a szolgáltatás következő lehetőségek közül választhat:

- Hozzáfűzendő, vagy az fejléc rendelt érték. Ha a megadott kérelemfejlécet nem létezik, majd ezt a szolgáltatást fog vegye fel a kérelmet.
- A kérelem fejléc törlése.

Az eredeti kiszolgálóra továbbított kérelmeket Ez a szolgáltatás által végrehajtott módosítások fogja tartalmazni.

A következő műveletek valamelyikét hajthatja végre, a fejléc:

Beállítás|Leírás|Példa
-|-|-
Hozzáfűzés|A megadott értékét a rendszer hozzáadja a meglévő kérelem fejléc értékének végéhez.|**A kérelem fejléc értéke (ügyfél):**<br/>Érték1<br/>**A kérelem fejléc értéke (szabálymotor):**<br/>Érték2 <br/>**Új kérelem fejléc értéke:** <br/>Value1Value2
Felülírás|A kérelem fejléc értéke lesz a megadott értékre.|**A kérelem fejléc értéke (ügyfél):**<br/>Érték1<br/>**A kérelem fejléc értéke (szabálymotor):**<br/>Érték2<br/>**Új kérelem fejléc értéke:**<br/> Érték2 <br/>
Törlés|Törli a megadott kérelemfejlécet.|**A kérelem fejléc értéke (ügyfél):**<br/>Érték1<br/>**Ügyfél kérelem fejléc konfigurációjának módosítása:**<br/>Törölje a szóban forgó kérelemfejlécet.<br/>**Eredmény:**<br/>A megadott kérelemfejlécet a rendszer nem továbbítja az eredeti kiszolgálóra.

Kapcsolatos információkat:

- Győződjön meg arról, hogy a meghatározott a beállítás értéke a kívánt kérelemfejléc pontos egyezést.
- Eset nem veszi figyelembe fejléc azonosítása céljából. Például a következő lehetőségek bármelyikét a `Cache-Control` fejlécnév azonosításához használható:
    - a cache-control
    - A CACHE-CONTROL
    - a cachE-Control
- A fejléc neve megadásakor csak alfanumerikus karaktereket, kötőjelek és aláhúzásjelek használja.
- Fejléc törlése megakadályozza azt a POP által az eredeti kiszolgálóra történő továbbítását.
- A következő fejlécek fenntartva, ezért ezt a funkciót nem lehet módosítani:
    - továbbított
    - gazdagép
    - keresztül
    - Figyelmeztetés
    - x-továbbított-számára
    - Az összes fejléc neve az "x-EK" vannak fenntartva.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-response-header"></a>Módosítsa az ügyfél válaszfejléc
Minden válasz válaszfejlécek azt leíró tartalmaz. Ez a szolgáltatás következő lehetőségek közül választhat:

- Hozzáfűzendő, vagy az egy válaszfejléc rendelt érték. Ha a megadott válaszfejlécet nem létezik, majd a szolgáltatás felveszi a válaszhoz.
- A válasz egy válaszfejléc törlése.

Alapértelmezés szerint válasz fejléce értékek meg vannak határozva az eredeti kiszolgálóra és a POP.

A következő műveletek egyikét a válaszfejléc hajtható végre:

Beállítás|Leírás|Példa
-|-|-
Hozzáfűzés|A megadott értékét a rendszer hozzáadja a meglévő válasz fejléc értékének végéhez.|**Válasz állomásfejléc-érték (ügyfél):**<br />Érték1<br/>**Válasz állomásfejléc-érték (szabálymotor):**<br/>Érték2<br/>**Új válasz állomásfejléc-érték:**<br/>Value1Value2
Felülírás|A válasz fejléc értéke lesz a megadott értékre.|**Válasz állomásfejléc-érték (ügyfél):**<br/>Érték1<br/>**Válasz állomásfejléc-érték (szabálymotor):**<br/>Érték2 <br/>**Új válasz állomásfejléc-érték:**<br/>Érték2 <br/>
Törlés|Törli a megadott válaszfejlécet.|**Válasz állomásfejléc-érték (ügyfél):**<br/>Érték1<br/>**Ügyfél válasz fejléce konfigurációjának módosítása:**<br/>Törölje a szóban forgó válaszfejlécet.<br/>**Eredmény:**<br/>A megadott válaszfejlécet a rendszer nem továbbítja a kérelmezőnek.

Kapcsolatos információkat:

- Győződjön meg arról, hogy a meghatározott a beállítás értéke a kívánt választ fejléc pontos egyezést. 
- Eset nem veszi figyelembe fejléc azonosítása céljából. Például a következő lehetőségek bármelyikét a `Cache-Control` fejlécnév azonosításához használható:
    - a cache-control
    - A CACHE-CONTROL
    - a cachE-Control
- A fejléc törlése megakadályozza a kérelmező történő továbbítását.
- A következő fejlécek fenntartva, ezért ezt a funkciót nem lehet módosítani:
    - fogadja el-kódolás
    - kora
    - kapcsolat
    - tartalom kódolása
    - tartalom hosszúságú
    - tartalom tartományon.
    - dátum
    - kiszolgáló
    - pótkocsi
    - Transfer-encoding
    - Frissítés
    - eltérő
    - keresztül
    - Figyelmeztetés
    - Az összes fejléc neve az "x-EK" vannak fenntartva.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="partial-cache-sharing"></a>A részleges gyorsítótári megosztása
**Cél:** határozza meg, hogy egy kérelem hozhat létre a részlegesen gyorsítótárazott tartalmat.

A részleges gyorsítótári felhasználhatja az az adott tartalomhoz új kérelmeinek teljesítéséhez, amíg a kért tartalom gyorsítótárazva van, teljes mértékben.

Érték|Eredmény
-|-
Engedélyezve|Kérelmek hozhat létre a részlegesen gyorsítótárazott tartalmat.
Letiltva|Kérelmek csak hozhat létre a kért tartalom egy teljes mértékben gyorsítótárazott verziója.

**Alapértelmezés:** letiltva.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="prevalidate-cached-content"></a>Gyorsítótárazott tartalom prevalidate
**Cél:** meghatározza, hogy a gyorsítótárazott tartalmat legyen abban az esetben jogosult a korai ismételt érvényesítése a TTL lejárata előtt.

Adja meg az idő során, amely jogosult a korai ismételt érvényesítése lesz a kért tartalom TTL lejárta előtt.

Kapcsolatos információkat:

- Élettartam lejárt kiválasztása "Off" időegységét van szüksége a gyorsítótárazott tartalom követően kerül sor ismételt érvényesítése. Idő nem adható meg, és a rendszer figyelmen kívül hagyja.

**Alapértelmezés:** ki. Ismételt érvényesítése kerül sor a gyorsítótárazott tartalom TTL lejárata után.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="proxy-special-headers"></a>Proxy különleges fejlécek
**Cél:** meghatározza a készletét [Verizon-specifikus HTTP-kérelmek fejléceinek](cdn-verizon-http-headers.md) , hogy a rendszer továbbítja a POP az eredeti kiszolgálóra.

Kapcsolatos információkat:

- Minden egyes CDN-specifikus kérelemfejléc, ez a szolgáltatás meghatározott továbbíthatja a rendszer az eredeti kiszolgálóra. A kizárt fejlécek nem továbbítják.
- CDN-specifikus fejléc továbbított érdekében listáról való eltávolításához szóközökkel elválasztott lista fejlécmező található.

A következő HTTP-fejléceket az alapértelmezett listában szerepelnek:
- Ezzel
- X-továbbított-számára
- X továbbított protokoll
- X-állomás
- X-Midgress
- X-átjáró – lista
- X-EK-neve
- Gazdagép

**Alapértelmezés:** összes CDN-specifikus kérelemfejléc a rendszer az eredeti kiszolgálóra továbbítja.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="refresh-zero-byte-cache-files"></a>Nulla bájtos gyorsítótárban levő fájlok frissítése
**Cél:** határozza meg, hogyan kezeli a 0 bájtos gyorsítótár eszköz egy HTTP-ügyfél kérelmet a POP.

Érvényes értékek a következők:

Érték|Eredmény
--|--
Engedélyezve|Hatására a POP refetch az eszköz a forráskiszolgálóról.
Letiltva|Visszaállítja az alapértelmezett viselkedés. Az alapértelmezett viselkedés be érvényes gyorsítótár eszközöket kérelem kiszolgálásához.
Ez a funkció nincs szükség a megfelelő gyorsítótárazás és a továbbítási, de hasznos megoldás lehet. Például a dinamikus tartalom generátorokat származási kiszolgálókon véletlenül eredményezhet 0 bájtos választ küldi el a POP. A rendszer jellemzően gyorsítótárzza válaszok típusai alapján a POP. Ha tudja, hogy, hogy a 0 bájtos válasz soha nem egy érvényes válasz 

az ilyen tartalmat majd ezt a szolgáltatást előfordulhat, hogy ilyen típusú eszközök nem lehetséges a kiszolgálása az ügyfelek számára.

**Alapértelmezés:** letiltva.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-cacheable-status-codes"></a>Állítsa be a gyorsítótárazható állapotkódok
**Cél:** meghatározza a gyorsítótárazott tartalom eredményező állapotkódok készletét.

Alapértelmezés szerint gyorsítótárazás csak a 200 OK válaszok engedélyezték.

Adja meg a kívánt állapotkódok szóközökkel elválasztott kötetnevek készlete.

Kapcsolatos információkat:

- A forrás No-Cache figyelmen kívül hagyása funkció engedélyezéséhez. Ha ez a funkció nincs engedélyezve, majd 200 OK válaszok előfordulhat, hogy nem kerülnek a gyorsítótárba.
- Ez a szolgáltatás érvényes állapotkódjai készletét vannak: 203, 300, 301, 302, 305, 307, 400, 401-es, 402, 403-as, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502-es, 503-as, 504, és 505.
- Ez a funkció nem használható a 200 OK állapotkód eredményező válaszok gyorsítótárazásának letiltása.

**Alapértelmezés:** csak 200 OK állapotkód eredményező válaszok esetében a gyorsítótárazás engedélyezve van.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-client-ip-custom-header"></a>Ügyfél IP-egyéni fejléc beállítása
**Cél:** ad hozzá egy egyéni fejlécet, amely azonosítja a kérést küldő ügyfél IP-címet a kérelmet.

A fejléc neve beállítás határozza meg az ügyfél IP-cím tároló egyéni kérelemfejléc nevét.

Ez a funkció lehetővé teszi, hogy az ügyfél eredeti kiszolgálóra megállapítása a ügyfél IP-címek egyéni fejléc keresztül. Ha a kérés van kiszolgálása a gyorsítótárból, majd az eredeti kiszolgálóra nem figyelmeztet az ügyfél IP-cím. Ezért ajánlott, hogy ez a szolgáltatás használható-e, amely nem gyorsítótárazott eszközökkel.

Győződjön meg arról, hogy a megadott fejléc neve nem egyezik a következő bármelyike:

- Standard kérelem nevével. Szokásos fejlécben neveinek listáját található [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Fenntartott nevek:
    - továbbítja a
    - gazdagép
    - eltérő
    - keresztül
    - Figyelmeztetés
    - x-továbbított-számára
    - Az összes fejléc neve az "x-EK" vannak fenntartva.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-content-delivery-on-error"></a>Hiba történt a régi Tartalomkézbesítési
**Cél:** határozza meg, hogy lejárt a gyorsítótárazott tartalom kézbesíti a rendszer hiba esetén a gyorsítótár ismételt érvényesítése során vagy a felhasználói forráskiszolgálóról a kért tartalom lekérése közben.

Érték|Eredmény
-|-
Engedélyezve|Elavult tartalom kiszolgált a kérelmezőnek egy egy eredeti kiszolgálóhoz való kapcsolódáskor hiba esetén.
Letiltva|Hiba az eredeti kiszolgálóra továbbítja a kérelmező.

**Alapértelmezés:** letiltva

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-while-revalidate"></a>Elavult Revalidate közben
**Cél:** kiszolgálására elavult tartalom a kérelmező, amíg ismételt érvényesítése akkor történik meg a POP engedélyezésével javítja a teljesítményt.

Kapcsolatos információkat:

- A szolgáltatás működését a kiválasztott időegység függően változik.
    - **Időegység:** adja meg az az időtartam, és jelölje ki a idő (például másodperc, perc, óra, stb.) elavult tartalomkézbesítési engedélyezése. Az ilyen típusú telepítés lehetővé teszi, hogy a CDN és a, hogy mennyi idő kérhet kiterjesztése tartalom érvényesítése a következő képlettel megkövetelése előtt: **TTL** + **elavult közben kísérelje meg újra érvényesítését idő** 
    - **OFF:** Select "Kikapcsolva" ismételt érvényesítése előtt kérelmet megkövetelése az elavult tartalom kezdeményezheti.
        - Ne adjon meg az időtartam, mert törlendő, és figyelmen kívül hagyja.

**Alapértelmezés:** ki. Ismételt érvényesítése szükséges, mielőtt a kért tartalom szolgálhatók ki.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth"></a>Jogkivonat hitelesítési
**Cél:** határozza meg, hogy jogkivonat-alapú hitelesítési kérelemre alkalmazandó.

Ha a jogkivonat-alapú hitelesítés engedélyezve van, adjon meg egy titkosított jogkivonatot, és a token által meghatározott követelményeknek csak kérelmek lesz érvényes.

A token értékeinek titkosítására és visszafejtésére használt titkosítási kulcs az elsődleges kulcs és a biztonsági mentés kulcs jogkivonat hitelesítési lapján található beállítások határozzák meg. Ne feledje, hogy a titkosítási kulcsok platform-specifikus.

**Alapértelmezés:** letiltva.

Ez a szolgáltatás élvez kivételével az URL-újraíró szolgáltatás legtöbb szolgáltatását.

Érték | Eredmény
------|---------
Engedélyezve | A kért tartalom jogkivonat-alapú hitelesítéssel védi. Csak ad meg egy érvényes tokent, és megfeleljenek az ügyfelek kérelmeinek szembeni szerződéses kötelezettségeket. FTP-tranzakciók jogkivonat-alapú hitelesítés nem tartoznak.
Letiltva| Visszaállítja az alapértelmezett viselkedés. Az alapértelmezés lesz annak meghatározásához, hogy egy kérelem védve legyenek a jogkivonat-alapú hitelesítés konfigurálásának engedélyezése.

#### <a name="compatibility"></a>Kompatibilitás
Ne használjon jogkivonat hitelesítési egy mindig egyezik a feltétellel. 

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-denial-code"></a>Jogkivonat hitelesítési Megtagadás kódot
**Cél:** határozza meg, amikor a jogkivonat-alapú hitelesítés miatt megtagadva a kérés egy felhasználó számára visszaadott válasz típusú.

A rendelkezésre álló válaszkódot a következő táblázatban láthatók.

Válaszkód|Válasz neve|Leírás
-------------|-------------|--------
301|Végleg áthelyezése|Ez az állapot kód jogosulatlan felhasználók hely fejlécben megadott URL-címre irányítja át.
302|Sikeres keresés|Ez az állapot kód jogosulatlan felhasználók hely fejlécben megadott URL-címre irányítja át. Ezzel az állapotkóddal az iparági szabványos módjáról irányítja át a felhasználókat a rendszer.
307|Ideiglenes átirányítás|Ez az állapot kód jogosulatlan felhasználók hely fejlécben megadott URL-címre irányítja át.
401|Nem engedélyezett|Ez az állapot kód kombinálás a WWW-Authenticate válaszfejléc lehetővé teszi egy felhasználót a hitelesítéshez.
403|Tiltott|Ez az üzenet a szabványos 403-as tiltott állapotüzenetet egy jogosulatlan felhasználó által látható, amikor megpróbál hozzáférni a védett tartalom.
404|Nem található a fájl|Ez az állapot kód azt jelzi, hogy a HTTP-ügyfél képes kommunikálni a kiszolgálóval volt, de a kért tartalomhoz nem található.

#### <a name="compatibility"></a>Kompatibilitás
Ne használja a hitelesítés megtagadása Tokenkódot egy mindig egyezik a feltétellel. Ehelyett használja a **egyéni Megtagadás kezelése** szakasz a **jogkivonat hitelesítési** oldalán a **kezelése** portal. További információkért lásd: [tokent használó hitelesítés biztonságossá tétele az Azure CDN eszközök](cdn-token-auth.md).

#### <a name="url-redirection"></a>Átirányítási URL-címe

Ez a szolgáltatás URL-cím átirányítását egy felhasználó által definiált URL-is támogatja, amikor beállítják, hogy egy 3xx állapot kóddal tér vissza. A felhasználó által definiált URL-cím adható meg az alábbi lépések elvégzésével:

1. Válassza ki a hitelesítés megtagadása Tokenkódot szolgáltatás 3xx válaszkód.
2. "Hely" Válassza ki a kötelező fejlécet beállítást.
3. A választható állomásfejléc-érték beállítása a kívánt URL-címre.

Ha az egy URL-címe nincs definiálva a következő 3xx állapotkódot, majd a szabványos válasz lap egy 3xx status kód visszakerül a felhasználó.

Átirányítási URL-címe esetén csak 3xx válaszkódot alkalmazható.

A választható állomásfejléc-érték a beállítás lehetővé teszi, alfanumerikus karaktereket, idézőjelek és szóközöket.

#### <a name="authentication"></a>Hitelesítés

Ez a funkció támogatja a funkció a WWW-Authenticate fejléc szerepeljen válaszol a jogkivonat-alapú hitelesítés által védett tartalom jogosulatlan kérelmet. Ha a WWW-Authenticate fejléc már be lett állítva a "basic" a konfigurációt, majd a jogosulatlan felhasználók bekéri a fiók hitelesítő adatait.

A fenti konfigurációban érheti el az alábbi lépések elvégzésével:

1. Jelöljön ki a "401-es", a hitelesítés megtagadása Tokenkódot szolgáltatás válaszkódja.
2. Válassza ki a "WWW-Authenticate" a Fejlécnév nem kötelező beállítás.
3. A Fejlécérték nem kötelező beállítás "alapszintű."

A WWW-Authenticate fejléc értéke csak a 401-es válaszkódot.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-ignore-url-case"></a>Jogkivonat hitelesítési nagybetűket URL-címe
**Cél:** meghatározza, hogy jogkivonat-alapú hitelesítés által készített URL-cím összehasonlítás kis-és nagybetűket.

Ez a szolgáltatás által érintett paraméterei a következők:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Érvényes értékek a következők:

Érték|Eredmény
---|----
Engedélyezve|Hatására a POP és nagybetűket, jogkivonat-alapú hitelesítési paraméterek URL-címek összehasonlításakor.
Letiltva|Visszaállítja az alapértelmezett viselkedés. Az alapértelmezett viselkedés van URL-cím összehasonlításhoz Token hitelesítés érdekében kis-és nagybetűket.

**Alapértelmezés:** letiltva.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-parameter"></a>Jogkivonat hitelesítési paraméter
**Cél:** határozza meg, hogy van-e a jogkivonat-alapú hitelesítés lekérdezési karakterlánc paraméter neve.

Kapcsolatos információkat:

- Az érték opció meghatározza a lekérdezési karakterlánc paraméterének neve, amelyen keresztül a jogkivonat adható meg.
- A beállítás értéke nem állítható be "ec_token."
- Győződjön meg arról, hogy a meghatározott érték beállítás neve URL-cím csak érvényes karaktereket tartalmaz.

Érték|Eredmény
----|----
Engedélyezve|Az érték a beállítás határozza meg a lekérdezési karakterlánc paraméterének neve, amelyen keresztül a jogkivonatok definiálni kell.
Letiltva|A jogkivonat a kérelem URL-címében nem definiált lekérdezési karakterlánc paraméterként adható meg.

**Alapértelmezés:** letiltva. A jogkivonat a kérelem URL-címében nem definiált lekérdezési karakterlánc paraméterként adható meg.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-redirect"></a>Az átirányítási URL-címe
**Cél:** átirányítja a felhasználókat a hely fejléce kérelmeket.

Ez a szolgáltatás konfigurációját igényli, a következő beállítások megadása:

Beállítás|Leírás
-|-
Kód|Válassza ki a kérelmezőnek visszaadott válaszának kódja.
Forrás & minta| Ezek a beállítások megadása a kérelem URI-minta, amely azonosítja a típusát, hogy előfordulhat, hogy átirányítja kérelem. A rendszer átirányítja az URL-cím megfelel a következő feltételek közül mind egyetlen kérelmeket: <br/> <br/> **Forrás (vagy a tartalom-hozzáférési pont):** válasszon, amely azonosítja az eredeti kiszolgálóra relatív elérési utat. Ez az elérési út a _/XXXX/_ szakasz és a végpont neve. <br/><br/> **Forrás (minta):** egy mintát, amely azonosítja a kérelmek relatív elérési utat kell megadni. A reguláris kifejezési minta definiálnia kell egy elérési utat, amely elindítja közvetlenül után a korábban kiválasztott tartalom-hozzáférési pont (lásd fent). <br/> -Győződjön meg arról, hogy a kérelem URI (Ez azt jelenti, hogy a forrás & mintát) korábban megadott feltételek nem ütközik a szolgáltatáshoz tartozó bármely egyezés egyiknek. <br/> -Adjon meg egy minta; a mintát üres értéket használja, ha az összes karakterlánc teljesül.
Cél| Határozza meg azt az URL-címet, amelyre a fenti kérelmek irányítja. <br/><br/> Az URL-cím használatával dinamikusan össze: <br/> -A reguláris kifejezési minta <br/>- [HTTP-változók](cdn-http-variables.md) <br/><br/> Helyettesítse be a cél minta $ használatával a forrás mintában rögzített értékek_n_ ahol _n_ azonosítja a sorrendet, amelyben rögzítésének értéket. $1 például közben $2 második értékét jelöli az adatforrás a mintában rögzített első értékét jelöli. <br/> 
Ajánlott egy abszolút URL-CÍMÉT használja. Egy relatív URL-cím használatát előfordulhat, hogy CDN URL-címének átirányítása elérési út érvénytelen.

**Mintaforgatókönyv**

Ez a példa bemutatja, hogyan kell CNAME URL-címet, amely a CDN alap URL-cím él átirányítási: http://marketing.azureedge.net/brochures

Kérelmek jogosult irányítja át a alap peremhálózati CNAME URL-címe: http://cdn.mydomain.com/resources

Az URL-cím átirányítást a következő konfigurációs keresztül valósítható meg: ![](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Kulcs mutat:**

- Az átirányítási URL-cím szolgáltatást határozza meg a kérelem URL-címek, amelyek irányítja. Ennek eredményeképpen további egyezés feltételek esetén nincs szükség. Bár a egyeznek az állapot "Always" van definiálva, csak az ügyfél "marketing" eredeti "brosúrák" mappájába pont kérelmek irányítja. 
- Minden egyező kérések a szélén CNAME URL-címet a cél-beállítás irányítja. 
    - A minta #1. forgatókönyv: 
        - Mintakérelem (CDN URL): http://marketing.azureedge.net/brochures/widgets.pdf 
        - A kérelem URL-címe (után átirányítási): http://cdn.mydomain.com/resources/widgets.pdf  
    - A minta #2. forgatókönyv: 
        - Mintakérelem (peremhálózati CNAME URL): http://marketing.mydomain.com/brochures/widgets.pdf 
        - Kérelem URL-CÍMÉT (után átirányítási): http://cdn.mydomain.com/resources/widgets.pdf mintaforgatókönyv
    - A minta #3. forgatókönyv: 
        - Mintakérelem (peremhálózati CNAME URL): http://brochures.mydomain.com/campaignA/final/productC.ppt 
        - A kérelem URL-címe (után átirányítási): http://cdn.mydomain.com/resources/campaignA/final/productC.ppt  
- A kérelem rendszer (% {séma}) változó rendszer elkészítéséhez használja a cél beállítás, amely biztosítja, hogy a kérelem sémát az átirányítást követően változatlan marad.
- A rögzítette a kérelem URL-szegmensek lesz hozzáfűzve az új URL-cím segítségével "$1."

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-rewrite"></a>URL-cím átdolgozás
**Cél:** újraírja a kérelem URL-CÍMÉT.

Kapcsolatos információkat:

- Ez a szolgáltatás konfigurációját igényli, a következő beállítások megadása:

Beállítás|Leírás
-|-
 Forrás & minta | Ezek a beállítások határozzák meg a kérelem URI-minta, amely azonosítja a típusát, hogy előfordulhat, hogy be kell írni kérelem. URL-cím megfelel a következő feltételek közül mind egyetlen kérelmek felülíródik: <br/><br/>  - **Forrás (vagy a tartalom-hozzáférési pont):** válasszon, amely azonosítja az eredeti kiszolgálóra relatív elérési utat. Ez az elérési út a _/XXXX/_ szakasz és a végpont neve. <br/><br/> - **Forrás (minta):** egy mintát, amely azonosítja a kérelmek relatív elérési utat kell megadni. A reguláris kifejezési minta definiálnia kell egy elérési utat, amely elindítja közvetlenül után a korábban kiválasztott tartalom-hozzáférési pont (lásd fent). <br/> Győződjön meg arról, hogy a kérelem URI (Ez azt jelenti, hogy a forrás & mintát) korábban megadott feltételek nem ütközik a egyezés feltételek, a szolgáltatáshoz tartozó definiálva. Adjon meg egy minta; a mintát üres értéket használja, ha az összes karakterlánc teljesül. 
 Cél  |Adja meg, amelyhez a fenti kérelmek felülíródik által relatív URL-címe: <br/>    1. A tartalom-hozzáférési pont, amely azonosítja az eredeti kiszolgálóra kiválasztása. <br/>    2. Annak meghatározása, egy relatív elérési út használatával: <br/>        -A reguláris kifejezési minta <br/>        - [HTTP-változók](cdn-http-variables.md) <br/> <br/> Helyettesítse be a cél minta $ használatával a forrás mintában rögzített értékek_n_ ahol _n_ azonosítja a sorrendet, amelyben rögzítésének értéket. $1 például közben $2 második értékét jelöli az adatforrás a mintában rögzített első értékét jelöli. 
 Ez a funkció lehetővé teszi, hogy a POP az URL-cím újraírása egy hagyományos átirányítás végrehajtása nélkül. Ez azt jelenti, hogy a kérelmező megkapja a azonos válaszkód, mintha csak egy átírt URL-CÍMÉT kellett lett kérve.

**Példa: 1. forgatókönyv**

Ez a példa bemutatja, hogyan CNAME URL-címet, amely a CDN alap URL-cím él átirányítására: http://marketing.azureedge.net/brochures/

Kérelmek jogosult irányítja át a alap peremhálózati CNAME URL-címe: http://MyOrigin.azureedge.net/resources/

Az URL-cím átirányítást a következő konfigurációs keresztül valósítható meg: ![](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Mintaforgatókönyv 2**

A példa bemutatja, hogyan átirányítására él kisbetűssé reguláris kifejezésekkel nagybetűs CNAME URL-CÍMÉT.

Az URL-cím átirányítást a következő konfigurációs keresztül valósítható meg: ![](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)


**Kulcs mutat:**

- Az URL-újraíró szolgáltatást határozza meg a kérelem URL-címek, amelyek felülíródik. Ennek eredményeképpen további egyezés feltételek esetén nincs szükség. Bár a egyeznek az állapot "Always" van definiálva, csak azok a kérelmek, amelyek a felhasználói "marketing" eredeti "brosúrák" mappa felülíródik.

- A rögzítette a kérelem URL-szegmensek lesz hozzáfűzve az új URL-cím segítségével "$1."

#### <a name="compatibility"></a>Kompatibilitás
Ez a funkció tartalmazza a megfelelő feltételek, amelyeknek teljesülniük kell ahhoz olyan kérésekre alkalmazható. Ütköző feltételek beállítása elkerülése érdekében ez a funkció nem kompatibilis a következő feltételek egyeznek:

- SZÁMOT
- CDN-forrása
- Ügyfél IP-címe
- Ügyfél forrása
- Kérelem séma
- URL-cím elérési út könyvtár
- URL-cím elérési út bővítmény
- URL-cím elérési út fájlnév
- URL-cím elérési út szövegkonstans
- URL-cím elérési út Regex
- URL-cím elérési út helyettesítő karakter
- Lekérdezés-szövegkonstans URL-címe
- URL-cím lekérdezési paraméter
- Lekérdezés Regex URL-címe
- Lekérdezés helyettesítő URL-címe

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="user-variable"></a>Felhasználói változó
**Cél:** csak belső használatra.

[Lap tetejére](#azure-cdn-rules-engine-features)

</br>

## <a name="next-steps"></a>További lépések
* [Szabálymotor-referencia](cdn-rules-engine-reference.md)
* [Szabálymotor feltételes kifejezései](cdn-rules-engine-reference-conditional-expressions.md)
* [Szabálymotor egyezési feltételei](cdn-rules-engine-reference-match-conditions.md)
* [A szabályok használata HTTP működés felülbírálásához](cdn-rules-engine.md)
* [Az Azure CDN áttekintése](cdn-overview.md)
