---
title: Azure CDN a Verizon Premium szabályok motor funkciók | Microsoft dokumentumok
description: Az Azure CDN referenciadokumentációja a Verizon Premium szabályok motorfunkcióiból.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: 373e7838327d11b1b54278ee0c16c6e6ae554b0b
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253492"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Az Azure CDN a Verizon Premium szabályai motorfunkcióktól

Ez a cikk az Azure Content Delivery Network (CDN) Rules Engine elérhető funkcióinak részletes [leírását tartalmazza.](cdn-verizon-premium-rules-engine.md)

A szabály harmadik része a funkció. A szolgáltatás határozza meg, hogy milyen típusú művelet et alkalmaz a kérelem típusára, amelyet egyező feltételek készlete azonosít.

## <a name="access-features"></a>Access-szolgáltatások

Ezek a szolgáltatások a tartalomhoz való hozzáférés szabályozására szolgálnak.

Név | Cél
-----|--------
[Hozzáférés megtagadása (403)](#deny-access-403) | Azt határozza meg, hogy az összes kérelmet a rendszer 403-as Tiltott válaszsal utasítsa-e el.
[Token hitelesítés](#token-auth) | Azt határozza meg, hogy a rendszer alkalmazza-e a tokenalapú hitelesítést egy kérelemre.
[Token hitelesítési megtagadási kódja](#token-auth-denial-code) | Azt határozza meg, hogy a rendszer milyen típusú választ ad vissza a felhasználónak, ha a kérést jogkivonatalapú hitelesítés miatt megtagadják.
[Token hitelesítése URL-eset figyelmen kívül hagyása](#token-auth-ignore-url-case) | Azt határozza meg, hogy a tokenalapú hitelesítés által végzett URL-összehasonlítások nem érzékenyek-e a kis- és nagybetűkre.
[Token hitelesítési paramétere](#token-auth-parameter) | Azt határozza meg, hogy a tokenalapú hitelesítéslekérdezési karakterlánc paramétert át kell-e nevezni.

## <a name="caching-features"></a>Gyorsítótárazási szolgáltatások

Ezek a szolgáltatások a tartalom gyorsítótárazásának időpontjára és módjára szabhatók meg.

Név | Cél
-----|--------
[Sávszélesség-paraméterek](#bandwidth-parameters) | Azt határozza meg, hogy a sávszélesség-szabályozási paraméterek (például ec_rate és ec_prebuf) aktívak-e.
[Sávszélesség-szabályozás](#bandwidth-throttling) | Szabályozza a jelenléti pont (POP) által biztosított válasz sávszélességét.
[Gyorsítótár megkerülése](#bypass-cache) | Azt határozza meg, hogy a kérés nek meg kell-e kerülnie a gyorsítótárazást.
[Gyorsítótár-vezérlés fejlécének kezelése](#cache-control-header-treatment) | Szabályozza `Cache-Control` a fejlécek létrehozását a POP által, ha a Külső max-Age szolgáltatás aktív.
[Cache-Key lekérdezési karakterlánc](#cache-key-query-string) | Azt határozza meg, hogy a gyorsítótár-kulcs tartalmazza-e vagy kizárja-e a kérelemhez társított lekérdezési karakterlánc-paramétereket.
[Cache-Key újraírása](#cache-key-rewrite) | Átírja a kérelemhez társított gyorsítótár-kulcsot.
[Teljes gyorsítótár-kitöltés](#complete-cache-fill) | Azt határozza meg, hogy mi történik, ha egy kérelem részleges gyorsítótár-tévesztést eredményez a POP-on.
[Fájltípusok tömörítése](#compress-file-types) | A kiszolgálón tömörített fájlok fájlformátumait határozza meg.
[Alapértelmezett belső maximális életkor](#default-internal-max-age) | A POP és az eredeti kiszolgáló gyorsítótárának újraérvényesítéséjének alapértelmezett maximális életkori intervallumát határozza meg.
[Lejáratfejléc-kezelés](#expires-header-treatment) | Szabályozza a `Expires` fejlécek POP általi létrehozását, ha a Külső max-Age szolgáltatás aktív.
[Külső max-age](#external-max-age) | A böngésző és a POP-gyorsítótár újraérvényesítésének maximális életkori intervallumát határozza meg.
[Belső max-kor kényszerítése](#force-internal-max-age) | A POP és az eredeti kiszolgáló gyorsítótárának újraérvényesítésének maximális életkori intervallumát határozza meg.
[H.264 támogatás (HTTP Progresszív Letöltés)](#h264-support-http-progressive-download) | A tartalom streameléséhez használható H.264 fájlformátumok típusát határozza meg.
[Tiszteletbeli gyorsítótár nélküli kérelem](#honor-no-cache-request) | Azt határozza meg, hogy a HTTP-ügyfél gyorsítótárnélküli kérelmeit a rendszer továbbítja-e az eredeti kiszolgálónak.
[Az Origin no-cache figyelmen kívül hagyása](#ignore-origin-no-cache) | Azt határozza meg, hogy a CDN figyelmen kívül hagy-e bizonyos, eredeti kiszolgálóról kiszolgált direktívákat.
[Unsatisfiable tartományok figyelmen kívül hagyása](#ignore-unsatisfiable-ranges) | Azt a választ határozza meg, amelyet a rendszer akkor ad vissza az ügyfeleknek, amikor egy kérelem egy 416 kért tartomány nem satisfiable állapotkódot hoz létre.
[Belső max-állott](#internal-max-stale) | Azt határozza meg, hogy a gyorsítótárazott eszköz mennyi ideig marad el a szokásos lejárati idő után, ha a POP nem tudja újraérvényesíteni a gyorsítótárazott eszközt az eredeti kiszolgálóval.
[Részleges gyorsítótár-megosztás](#partial-cache-sharing) | Azt határozza meg, hogy egy kérelem képes-e részlegesen gyorsítótárazott tartalmat generálni.
[Gyorsítótárazott tartalom előzetes érvényesítése](#prevalidate-cached-content) | Azt határozza meg, hogy a gyorsítótárazott tartalom jogosult-e a ttl lejárta előtti korai újraérvényesítésre.
[Nulla bájtos gyorsítótárfájlok frissítése](#refresh-zero-byte-cache-files) | Azt határozza meg, hogy a POP-ok hogyan kezelik a HTTP-ügyfél 0 bájtos gyorsítótár-eszközre vonatkozó kérelmét.
[Gyorsítótárazható állapotkódok beállítása](#set-cacheable-status-codes) | Azállapotkódok készletét határozza meg, amelyek gyorsítótárazott tartalmat eredményezhetnek.
[Elavult tartalomkézbesítés hiba esetén](#stale-content-delivery-on-error) | Azt határozza meg, hogy a rendszer a gyorsítótár-újraérvényesítés során vagy a kért tartalom nak az ügyfél forráskiszolgálójáról történő beolvasásakor a rendszer kézbesíti-e a lejárt gyorsítótárazott tartalmat.
[Elavult, míg újraérvényesíti](#stale-while-revalidate) | Javítja a teljesítményt azáltal, hogy lehetővé teszi a POP-ok számára, hogy elavult ügyfelet szolgáljanak ki a kérelmezőszámára, miközben az újraérvényesítés megtörténik.

## <a name="comment-feature"></a>Megjegyzés funkció

Ez a szolgáltatás úgy van kialakítva, hogy további információkat szolgáltasson egy szabályon belül.

Név | Cél
-----|--------
[Megjegyzés](#comment) | Lehetővé teszi egy jegyzet hozzáadását egy szabályhoz.

## <a name="header-features"></a>Fejlécfunkciók

Ezek a szolgáltatások fejlécek hozzáadására, módosítására vagy törlésére szolgálnak a kérelemből vagy válaszból.

Név | Cél
-----|--------
[Korválasz fejléce](#age-response-header) | Azt határozza meg, hogy az Életkorra adott válaszfejléc szerepel-e a kérelmezőnek küldött válaszban.
[Gyorsítótár-válaszok hibakeresése](#debug-cache-response-headers) | Azt határozza meg, hogy a válasz tartalmazhatja-e az X-EC-Debug válaszfejlécet, amely a kért eszköz gyorsítótár-házirendjével kapcsolatos információkat tartalmaz.
[Ügyfélkérelem fejlécének módosítása](#modify-client-request-header) | Felülírja, hozzáfűzi vagy törli a fejlécet egy kérelemből.
[Ügyfélválasz fejlécének módosítása](#modify-client-response-header) | Fejléc felülírása, hozzáfűzése vagy törlése a válaszból.
[Ügyfél IP-címének egyéni fejlécének beállítása](#set-client-ip-custom-header) | Lehetővé teszi, hogy a kérelmező ügyfél IP-címe egyéni kérelemfejlécként kerülő hozzá a kérelemhez.

## <a name="logging-features"></a>Naplózási funkciók

Ezek a szolgáltatások a nyers naplófájlokban tárolt adatok testreszabására szolgálnak.

Név | Cél
-----|--------
[1. egyéni naplómező](#custom-log-field-1) | Meghatározza a nyers naplófájl egyéni naplómezőjéhez rendelt formátumot és tartalmat.
[Lekérdezési karakterlánc naplózása](#log-query-string) | Azt határozza meg, hogy a rendszer a hozzáférési naplókban lévő URL-címmel együtt tárolja-e a lekérdezési karakterláncot.


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

## <a name="origin-features"></a>Az Origin funkciói

Ezek a szolgáltatások azt szabályozzák, hogy a CDN hogyan kommunikáljon az eredeti kiszolgálóval.

Név | Cél
-----|--------
[Maximális életben tartás kérések](#maximum-keep-alive-requests) | A Keep-Alive kapcsolatra vonatkozó kérelmek maximális számát határozza meg annak bezárása előtt.
[Proxy speciális fejlécei](#proxy-special-headers) | A POP-ról az eredeti kiszolgálóra továbbított CDN-specifikus kérelemfejlécek készletét határozza meg.

## <a name="specialty-features"></a>Speciális jellemzők

Ezek a szolgáltatások speciális funkciókat biztosítanak a tapasztalt felhasználók számára.

Név | Cél
-----|--------
[Gyorsítótárazható HTTP-metódusok](#cacheable-http-methods) | A hálózaton gyorsítótárazható további HTTP-metódusok készletét határozza meg.
[Gyorsítótárazható kérelem törzsének mérete](#cacheable-request-body-size) | Azt a küszöbértéket határozza meg, hogy a POST-válasz gyorsítótárazható-e.
[Felhasználói változó](#user-variable) | Csak belső használatra.

## <a name="url-features"></a>URL-funkciók

Ezek a funkciók lehetővé teszik a kérés átirányításának vagy átírását egy másik URL-re.

Név | Cél
-----|--------
[Átirányítások követése](#follow-redirects) | Azt határozza meg, hogy a kérelmek átirányíthatók-e az ügyfél forráskiszolgálója által visszaadott Hely fejlécben definiált állomásnévre.
[URL-átirányítás](#url-redirect) | Átirányítja a kérelmeket a Hely fejlécen keresztül.
[URL-átírás](#url-rewrite)  | Átírja a kérelem URL-címét.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a>Az Azure CDN a Verizon Premium szabályai motorjának jellemzőire vonatkozó

---

### <a name="age-response-header"></a>Korválasz fejléce

**Cél**: Azt határozza meg, hogy a kérelmezőnek küldött válasz tartalmaz-e Kor válaszfejlécet.

Érték|Eredmény
--|--
Engedélyezve | Az Age válasz fejléce szerepel a kérelmezőnek küldött válaszban.
Letiltva | Az Age válasz fejléce ki van zárva a kérelmezőnek küldött válaszból.

**Alapértelmezett viselkedés**: Letiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-parameters"></a>Sávszélesség-paraméterek

**Cél:** Azt határozza meg, hogy a sávszélesség-szabályozási paraméterek (például ec_rate és ec_prebuf) aktívak-e.

A sávszélesség-szabályozási paraméterek határozzák meg, hogy az ügyfél kérésének adatátviteli sebessége egyéni sebességre korlátozódik-e.

Érték|Eredmény
--|--
Engedélyezve|Lehetővé teszi, hogy a POP-ok tiszteletben tartsák a sávszélesség-szabályozási kérelmeket.
Letiltva|Hatására a POP-ok figyelmen kívül hagyják a sávszélesség-szabályozási paramétereket. A kért tartalom kézbesítése a szokásos módon történik (azaz sávszélesség-szabályozás nélkül).

**Alapértelmezett viselkedés:** Engedélyezve.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-throttling"></a>Sávszélesség-szabályozás

**Cél:** Szabályozza a POP-ok által nyújtott válasz sávszélességét.

A sávszélesség-szabályozás megfelelő beállításához mindkét alábbi beállítást meg kell határozni.

Beállítás|Leírás
--|--
Kbyte másodpercenként|Állítsa ezt a beállítást a válasz kézbesítéséhez használható maximális sávszélességre (Kb/s).
Prebuf másodperc|Állítsa be ezt a beállítást, hogy a POP-ok hány másodpercig várjanak a sávszélesség szabályozásáig. A korlátozás nélküli sávszélesség ezen időszakának célja, hogy megakadályozza, hogy a médialejátszó a sávszélesség-szabályozás miatt dadogási vagy pufferelési problémákat tapasztaljon.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bypass-cache"></a>Gyorsítótár megkerülése

**Cél:** Azt határozza meg, hogy a kérés nek meg kell-e kerülnie a gyorsítótárazást.

Érték|Eredmény
--|--
Engedélyezve|Az összes kérelmet átküldi az eredeti kiszolgálóra, még akkor is, ha a tartalom korábban pop-okon volt gyorsítótárazva.
Letiltva|Hatására a POP-ok gyorsítótárazják az eszközöket a válaszfejlécekben definiált gyorsítótár-házirendnek megfelelően.

**Alapértelmezett viselkedés:**

- **HTTP Nagy:** Tiltva

<!---
- **ADN:** Enabled

--->

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-http-methods"></a>Gyorsítótárazható HTTP-metódusok

**Cél:** A hálózaton gyorsítótárazható további HTTP-metódusok készletét határozza meg.

Legfontosabb információk:

- Ez a funkció feltételezi, hogy a GET-válaszokat mindig gyorsítótárba kell hozni. Ennek eredményeképpen a GET HTTP metódust nem kell figyelembe venni a szolgáltatás beállításakor.
- Ez a funkció csak a POST HTTP metódust támogatja. A POST-válasz gyorsítótárazásának `POST`engedélyezése a szolgáltatás beállításával.
- Alapértelmezés szerint csak a 14 Kb-nál kisebb törzsnek szóló kérelmek kerülnek a gyorsítótárba. A gyorsítótárazható kérelem törzsméret szolgáltatásával beállíthatja a kérelem törzsének maximális méretét.

**Alapértelmezett viselkedés:** A get-válaszok csak a gyorsítótárba kerülnek.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-request-body-size"></a>Gyorsítótárazható kérelem törzsének mérete

**Cél:** Azt a küszöbértéket határozza meg, hogy a POST-válasz gyorsítótárazható-e.

Ezt a küszöbértéket a kérelem törzsének maximális méretének megadásával határozzák meg. A nagyobb kéréstörzset tartalmazó kérelmek nem kerülnek a gyorsítótárba.

Legfontosabb információk:

- Ez a funkció csak akkor alkalmazható, ha a POST-válaszok gyorsítótárazásra jogosultak. A GYORSÍTÓTÁRAzolási kérelmek gyorsítótárazásának engedélyezéséhez használja a gyorsítótárazható HTTP-metódusok szolgáltatást.
- A megkereső szervet a következők esetében veszik figyelembe:
    - x-www-form-urlencoded értékek
    - Egyedi gyorsítótár-kulcs biztosítása
- A nagy kéréstörzs méretének meghatározása hatással lehet az adatkézbesítésteljesítményre.
    - **Ajánlott érték:** 14 Kb
    - **Minimális érték:** 1 Kb

**Alapértelmezett viselkedés:** 14 kb

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-control-header-treatment"></a>Gyorsítótár-vezérlés fejlécének kezelése

**Cél:** Szabályozza `Cache-Control` a fejlécek létrehozását a POP által, ha a külső max-age funkció aktív.

Az ilyen típusú konfiguráció elérésének legegyszerűbb módja, ha a külső max-age és a cache-control fejléckezelés funkciókat ugyanabban a utasításban helyezi el.

Érték|Eredmény
--|--
Felülírás|Biztosítja, hogy a következő műveletek történjenek:<br/> - Felülírja `Cache-Control` az eredeti kiszolgáló által létrehozott fejlécet. <br/>- Hozzáadja a `Cache-Control` külső max-age funkció által létrehozott fejlécet a válaszhoz.
Áthaladjon|Biztosítja, hogy `Cache-Control` a külső maxéletkorú funkció által létrehozott fejléc soha ne kerüljön a válaszba. <br/> Ha az eredeti `Cache-Control` kiszolgáló fejlécet hoz létre, az átjut a végfelhasználóhoz. <br/> Ha az eredeti kiszolgáló `Cache-Control` nem hoz létre fejlécet, akkor ez `Cache-Control` a beállítás azt eredményezheti, hogy a válaszfejléc nem tartalmaz fejlécet.
Hozzáadás, ha hiányzik|Ha `Cache-Control` a forráskiszolgálóról nem érkezett fejléc, akkor `Cache-Control` ez a beállítás hozzáadja a Külső max-kor szolgáltatás által létrehozott fejlécet. Ez a beállítás annak biztosításához hasznos, `Cache-Control` hogy minden eszközhöz fejléc legyen hozzárendelve.
Eltávolítás| Ez a beállítás `Cache-Control` biztosítja, hogy a fejlécválasz ne tartalmazzon fejlécet. Ha `Cache-Control` egy fejléc már hozzá van rendelve, akkor az törlődik a fejlécválaszból.

**Alapértelmezett viselkedés:** Felülírja.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-query-string"></a>Cache-Key lekérdezési karakterlánc

**Cél:** Azt határozza meg, hogy a gyorsítótár-kulcs tartalmazza-e vagy kizárja-e a kérelemhez társított lekérdezési karakterlánc-paramétereket.

Legfontosabb információk:

- Adjon meg egy vagy több lekérdezési karakterlánc paraméternevét, és válassza el az egyes paraméterneveket egyetlen szóközrel.
- Ez a szolgáltatás határozza meg, hogy a lekérdezési karakterlánc paraméterei szerepeljenek-e vagy ne legyenek kizárva a gyorsítótár-kulcsból. Az alábbi táblázatban további információkat talál az egyes lehetőségekről.

Típus|Leírás
--|--
 Belefoglalás|  Azt jelzi, hogy minden megadott paraméternek szerepelnie kell a gyorsítótárkulcsban. Minden olyan kérelemhez létrejön egy egyedi gyorsítótár-kulcs, amely a szolgáltatásban definiált lekérdezési karakterlánc-paraméter egyedi értékét tartalmazza.
 Az összes belefoglalása  |Azt jelzi, hogy egy egyedi lekérdezési karakterláncot tartalmazó eszköz minden egyes kéréséhez létrejön egy egyedi gyorsítótár-kulcs. Az ilyen típusú konfiguráció általában nem ajánlott, mert a gyorsítótár-találatok kis százalékához vezethet. A gyorsítótár-lekérések alacsony száma növeli a forráskiszolgáló terhelését, mivel több kérést kell kiszolgálnia. Ez a konfiguráció megkettőzi az "egyedi gyorsítótár" néven ismert gyorsítótárazási viselkedést a Query-String gyorsítótárazási oldalon.
 Exclude | Azt jelzi, hogy csak a megadott paraméter(ek) vannak kizárva a gyorsítótár-kulcsból. Az összes többi lekérdezési karakterlánc-paraméter szerepel a gyorsítótár-kulcsban.
 Az összes kizárása  |Azt jelzi, hogy a lekérdezési karakterlánc összes paramétere ki van zárva a gyorsítótár-kulcsból. Ez a konfiguráció megkettőzi a "standard-cache" alapértelmezett gyorsítótárazási viselkedést a Query-String gyorsítótárazás imát.  

A szabálymotor lehetővé teszi a lekérdezési karakterlánc-gyorsítótárazás megvalósításának testreszabását. Megadhatja például, hogy a lekérdezési karakterlánc-gyorsítótárazás csak bizonyos helyeken vagy fájltípusokon hajtson végre.

A "nincs gyorsítótárazás" lekérdezési karakterlánc-gyorsítótárazási viselkedés megkettőzéséhez hozzon létre egy szabályt, amely url-lekérdezési helyettesítő feltételt és egy gyorsítótár mellőzési szolgáltatást tartalmaz. Állítsa az URL-lekérdezés helyettesítő karakteres egyezési feltételét csillagra (*).

>[!IMPORTANT]
> Ha a fiók bármely elérési útjára engedélyezve van a tokenhitelesítés, a szabványos gyorsítótárazási mód az egyetlen mód, amely használható a lekérdezési karakterlánc-gyorsítótárazáshoz. További információkért lásd: [Az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési sztringekkel](cdn-query-string-premium.md).

#### <a name="sample-scenarios"></a>Példaforgatókönyvek

A szolgáltatás következő mintahasználata mintakérelmet és az alapértelmezett gyorsítótár-kulcsot biztosít:

- **Mintakérelem:** http://wpc.0001.&lt;Domain&gt;/800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01
- **Alapértelmezett gyorsítótár-kulcs:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Belefoglalás

Minta konfigurációja:

- **Típus:** Tartalmaz
- **Paraméter(ek):** nyelv

Az ilyen típusú konfiguráció a következő lekérdezési karakterlánc paraméter-gyorsítótár-kulcs:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Az összes belefoglalása

Minta konfigurációja:

- **Típus:** Az összes belefoglalása

Az ilyen típusú konfiguráció a következő lekérdezési karakterlánc paraméter-gyorsítótár-kulcs:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Exclude

Minta konfigurációja:

- **Típus:** Kizárása
- **Paraméter(ek):** munkamenet-kezelés használata

Az ilyen típusú konfiguráció a következő lekérdezési karakterlánc paraméter-gyorsítótár-kulcs:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Az összes kizárása

Minta konfigurációja:

- **Típus:** Az összes kizárása

Az ilyen típusú konfiguráció a következő lekérdezési karakterlánc paraméter-gyorsítótár-kulcs:

    /800001/Origin/folder/asset.htm

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-rewrite"></a>Cache-Key újraírása

**Cél:** Átírja a kérelemhez társított gyorsítótár-kulcsot.

A gyorsítótár-kulcs az a relatív elérési út, amely gyorsítótárazás céljából azonosítja az eszközt. Más szóval a kiszolgálók ellenőrzik egy eszköz gyorsítótárazott verzióját a gyorsítótár-kulcs által meghatározott elérési útnak megfelelően.

A szolgáltatás konfigurálásával mindkét alábbi beállítást meg kell határozni:

Beállítás|Leírás
--|--
Eredeti elérési út| Adja meg azoknak a kérelmeknek a relatív elérési útját, amelyek gyorsítótár-kulcsát újraírják. A relatív elérési út definiálható egy alap kezdőútvonal kiválasztásával, majd reguláris kifejezésminta meghatározásával.
Új elérési út|Adja meg az új gyorsítótár-kulcs relatív elérési útját. A relatív elérési út definiálható egy alap kezdőútvonal kiválasztásával, majd reguláris kifejezésminta meghatározásával. Ez a relatív útvonal dinamikusan alakítható ki [HTTP-változók](cdn-http-variables.md)használatával.

**Alapértelmezett viselkedés:** A kérelem gyorsítótár-kulcsát a kérelem URI-ja határozza meg.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="comment"></a>Megjegyzés

**Cél:** Lehetővé teszi egy jegyzet hozzáadását egy szabályhoz.

Ennek a funkciónak az egyik felhasználási célja, hogy további információkat nyújtson egy szabály általános céljáról, vagy arról, hogy miért adott hozzá egy adott egyezési feltételt vagy funkciót a szabályhoz.

Legfontosabb információk:

- Legfeljebb 150 karakter adható meg.
- Csak alfanumerikus karaktereket használjon.
- Ez a szolgáltatás nincs hatással a szabály viselkedésére. Ez csupán azt jelentette, hogy egy olyan terület, ahol információt adhat a jövőben, vagy hogy segíthet a hibaelhárítás a szabály.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="complete-cache-fill"></a>Teljes gyorsítótár-kitöltés

**Cél:** Azt határozza meg, hogy mi történik, ha egy kérelem részleges gyorsítótár-tévesztést eredményez a POP-on.

A részleges gyorsítótár-tévesztés egy olyan eszköz gyorsítótárának állapotát írja le, amelyet nem töltöttek le teljesen a POP-ba. Ha egy eszköz csak részben van gyorsítótárazva egy POP-on, akkor az eszközre vonatkozó következő kérés támpontként szolgál a rendszer az eredeti kiszolgálóra.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
A részleges gyorsítótár-tévesztés általában akkor következik be, amikor a felhasználó megszakítja a letöltést, vagy olyan eszközök esetében, amelyeket kizárólag HTTP-tartománykérelmek használatával kérnek. Ez a funkció olyan nagy méretű eszközök esetén a leghasznosabb, amelyek általában nem töltődnek le az elejétől a végéig (például videók). Ennek eredményeképpen ez a funkció alapértelmezés szerint engedélyezve van a HTTP Large platformon. Minden más platformon le van tiltva.

Tartsa meg a HTTP Large platform alapértelmezett konfigurációját, mert csökkenti az ügyfél származási kiszolgálójának terhelését, és növeli a tartalom letöltésének sebességét.

Érték|Eredmény
--|--
Engedélyezve|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedés az, hogy a POP-ot arra kényszeríti, hogy kezdeményezze az eszköz háttérbeolvasását az eredeti kiszolgálóról. Ezt követően az eszköz a POP helyi gyorsítótárában lesz.
Letiltva|Megakadályozza, hogy a POP háttérlekérést hajtson végre az eszközhöz. Az eredmény az, hogy az adott régióból származó eszközre vonatkozó következő kérelem hatására a POP az ügyfél forráskiszolgálójától kéri azt.

**Alapértelmezett viselkedés:** Engedélyezve.

#### <a name="compatibility"></a>Kompatibilitás

A gyorsítótár-beállítások nyomon követésének módja miatt ez a funkció nem társítható a következő egyezési feltételekkel:

- AS-szám
- Ügyfél IP-címe
- Cookie paraméter
- Cookie paraméter Regex
- Ország
- Eszköz
- Microsoft Edge Cname
- Hivatkozó tartomány
- Kérés fejlécének szeksika
- Kérelem fejléce Regex
- Helyettesítő fejléc kérése
- Kérelem módja
- Igénylési rendszer
- URL-lekérdezés literális
- URL-lekérdezés Regex
- URL-lekérdezési helyettesítő karakter
- URL-lekérdezési paraméter

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="compress-file-types"></a>Fájltípusok tömörítése

**Cél:** A kiszolgálón tömörített fájlok fájlformátumait határozza meg.

A fájlformátum az internetes adathordozó-típussal (például Tartalomtípus) is megadható. Az internetes médiatípus platformfüggetlen metaadatok, amelyek lehetővé teszik a kiszolgálók számára egy adott eszköz fájlformátumának azonosítását. Az alábbi oldalon található a gyakori internetes médiatípusok listája.

Internetes média típusa|Leírás
--|--
szöveg/egyszerű|Egyszerű szöveges fájlok
szöveg/html| HTML-fájlok
szöveg/css|Kaszkádolt stíluslapok (CSS)
alkalmazás/x-javascript|Javascript
alkalmazás/javascript|Javascript

Legfontosabb információk:

- Adjon meg több internetes médiatípust úgy, hogy mindegyiket egyetlen szóközvel határolódmeg.
- Ez a funkció csak az 1 MB-nál kisebb méretű eszközöket tömöríti. A nagyobb eszközöket a kiszolgálók nem tömörítik.
- Bizonyos típusú tartalmak, például képek, video- és audiomédia-eszközök (például JPG, MP3, MP4 stb.) már tömörítve vannak. Mivel az ilyen típusú eszközök további tömörítése nem csökkenti jelentősen a fájlméretet, ajánlott engedélyezni a tömörítést rajtuk.
- A helyettesítő karakterek, például a csillagok nem támogatottak.
- Mielőtt hozzáadja ezt a szolgáltatást egy szabályhoz, győződjön meg arról, hogy a Tömörítés letiltva beállítást a tömörítés lapon annak a platformnak a beállítására állítja be, amelyre ez a szabály vonatkozik.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="custom-log-field-1"></a>1. egyéni naplómező

**Cél:** Meghatározza a nyers naplófájl egyéni naplómezőjéhez rendelt formátumot és tartalmat.

Ez az egyéni mező lehetővé teszi annak meghatározását, hogy mely kérés- és válaszfejléc-értékek et tárolja a naplófájlok.

Alapértelmezés szerint az egyéni naplómező neve "x-ec_custom-1." A mező neve a Nyers napló beállításai lapon szabható testre.

A kérelem- és válaszfejlécek megadásának formátuma a következőképpen határozható meg:

Fejléc típusa|Formátum|Példák
-|-|-
Kérelem fejléce|`%{[RequestHeader]()}[i]()` | %{Accept-Encoding}i <br/> {Hivatkozó}i <br/> %{Engedélyezés}i
Válaszfejléc|`%{[ResponseHeader]()}[o]()`| %{Életkor}o <br/> %{Tartalomtípus}o <br/> %{Cookie}o

Legfontosabb információk:

- Az egyéni naplómezők a fejlécmezők és az egyszerű szöveg tetszőleges kombinációját tartalmazhatják.
- A mező érvényes karakterei a következők: alfanumerikus (0-9, a-z és A-Z), kötőjelek, kettőspontok, pontosvesszők, aposztrófok, vesszők, pontok, aláhúzások, egyenlőségjelek, zárójelek, zárójelek, zárójelek és szóközök. A százalékos szimbólum és a kapcsos zárójelek csak akkor engedélyezettek, ha fejlécmező megadására használják.
- Az egyes megadott fejlécmezők helyesírásának meg kell egyeznie a kívánt kérés/válasz fejlécnevével.
- Ha több fejlécet szeretne megadni, használjon elválasztót az egyes fejlécek jelzésére. Használhat például egy rövidítést az egyes fejlécekhez:
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o

**Alapértelmezett érték:** -

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Gyorsítótár-válaszok hibakeresése

**Cél:** Azt határozza meg, hogy a válasz [tartalmazhat-e X-EC-Debug válaszfejléceket,](cdn-http-debug-headers.md)amelyek a kért eszköz gyorsítótár-házirendjével kapcsolatos információkat tartalmaznak.

A hibakeresési gyorsítótár válaszfejlécei akkor jelennek meg a válaszban, ha mindkét válasz igaz:

- A Debug Cache Response Headers szolgáltatás engedélyezve van a megadott kérésben.
- A megadott kérelem határozza meg a hibakeresési gyorsítótár válaszfejléceinek készletét, amelyek szerepelni fognak a válaszban.

A hibakeresési gyorsítótár válaszfejlécei a következő fejléc és a megadott direktívák kéréssel kérhetők a kérelemben:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Példa:**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Érték|Eredmény
-|-
Engedélyezve|A hibakeresési gyorsítótár-válaszfejlécekre vonatkozó kérelmek az X-EC-Debug fejlécet tartalmazó választ adják vissza.
Letiltva|Az X-EC-Debug válasz fejléce ki lesz zárva a válaszból.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Alapértelmezett belső maximális életkor

**Cél:** A POP és az eredeti kiszolgáló gyorsítótárának újraérvényesítéséjének alapértelmezett maximális életkori intervallumát határozza meg. Más szóval a POP előtt eltöltött idő ellenőrzi, hogy a gyorsítótárazott eszköz megegyezik-e az eredeti kiszolgálón tárolt eszközzel.

Legfontosabb információk:

- Ez a művelet csak olyan kezdőkiszolgálótól érkező válaszok esetén történik, amely `Cache-Control` `Expires` nem rendelt hozzá maximális életkorú jelzést a fejlécben.
- Ez a művelet nem történik meg olyan eszközök esetében, amelyek nem tekinthetők gyorsítótárazhatónak.
- Ez a művelet nincs hatással a böngésző pop-gyorsítótár-újraérvényesítési eredményeire. Az ilyen típusú újraérvényesítéseket `Cache-Control` `Expires` a böngészőbe küldött vagy fejlécek határozzák meg, amelyek a Külső max-age funkcióval testreszabhatók.
- A művelet eredményei nem befolyásolják a válaszfejléceket és a POP-okból visszaadott tartalmat a tartalomhoz, de hatással lehet a POP-okból a forráskiszolgálóra küldött újraérvényesítési forgalom mennyiségére.
- A szolgáltatás konfigurálása:
    - Annak az állapotkódnak a kiválasztása, amelyre az alapértelmezett belső maximális életkor alkalmazható.
    - Egész érték megadása, majd a kívánt időegység kiválasztása (például másodperc, perc, óra stb.). Ez az érték határozza meg az alapértelmezett belső maximális életkorintervallumot.

- Ha az időegységet "Ki" értékre állítja, a rendszer egy alapértelmezett, 7 napos belső maximális `Cache-Control` `Expires` életkori intervallumot rendel hozzá azokhoz a kérelmekhez, amelyeknem kaptak maximális életkorjelzést a saját jukban vagy a fejlécükben.

**Alapértelmezett érték:** 7 nap

#### <a name="compatibility"></a>Kompatibilitás

A gyorsítótár-beállítások nyomon követésének módja miatt ez a funkció nem társítható a következő egyezési feltételekkel:
- AS-szám
- Ügyfél IP-címe
- Cookie paraméter
- Cookie paraméter Regex
- Ország
- Eszköz
- Szegély cname
- Hivatkozó tartomány
- Kérés fejlécének szeksika
- Kérelem fejléce Regex
- Helyettesítő fejléc kérése
- Kérelem módja
- Igénylési rendszer
- URL-lekérdezés literális
- URL-lekérdezés Regex
- URL-lekérdezési helyettesítő karakter
- URL-lekérdezési paraméter

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="deny-access-403"></a>Hozzáférés megtagadása (403)

**Cél**: Azt határozza meg, hogy minden kérést elutasít-e a 403-as Tiltott válasz.

Érték | Eredmény
------|-------
Engedélyezve| A 403-as Tiltott válaszsal elutasítminden olyan kérelmet, amely megfelel az egyeztetési feltételeknek.
Letiltva| Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedés az, hogy az eredeti kiszolgáló határozza meg a visszaadott válasz típusát.

**Alapértelmezett viselkedés**: Letiltva

> [!TIP]
   > Ennek a funkciónak az egyik lehetséges felhasználási területe a kérelemfejléc-egyezési feltételhez való társítása a tartalomhoz inline hivatkozásokat használó HTTP-hivatkozók hoz való hozzáférés letiltásához.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="expires-header-treatment"></a>Lejáratfejléc-kezelés

**Cél:** Szabályozza a `Expires` fejlécek POP általi létrehozását, ha a Külső max-Age szolgáltatás aktív.

Az ilyen típusú konfiguráció elérésének legegyszerűbb módja, ha a külső max-age és a expires fejléckezelés funkciókat ugyanabban a nyilatkozatban helyezi el.

Érték|Eredmény
--|--
Felülírás|Biztosítja, hogy a következő műveletekre kerüljön sor:<br/>- Felülírja `Expires` az eredeti kiszolgáló által létrehozott fejlécet.<br/>- Hozzáadja a `Expires` külső max-age funkció által létrehozott fejlécet a válaszhoz.
Áthaladjon|Biztosítja, hogy `Expires` a külső maxéletkorú funkció által létrehozott fejléc soha ne kerüljön a válaszba. <br/> Ha az eredeti `Expires` kiszolgáló fejlécet hoz létre, az átjut a végfelhasználóhoz. <br/>Ha az eredeti kiszolgáló `Expires` nem hoz létre fejlécet, akkor ez `Expires` a beállítás azt eredményezheti, hogy a válaszfejléc nem tartalmaz fejlécet.
Hozzáadás, ha hiányzik| Ha `Expires` nem érkezett meg egy fejléc az eredeti `Expires` kiszolgálóról, akkor ez a beállítás hozzáadja a Külső max-age szolgáltatás által létrehozott fejlécet. Ez a beállítás annak biztosításához hasznos, `Expires` hogy minden eszközhöz fejléc legyen hozzárendelve.
Eltávolítás| Biztosítja, hogy `Expires` a fejléc nem szerepeljen a fejlécválaszban. Ha `Expires` egy fejléc már hozzá van rendelve, akkor az törlődik a fejlécválaszból.

**Alapértelmezett viselkedés:** Felülírja

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>Külső max-age

**Cél:** A böngésző és a POP-gyorsítótár újraérvényesítésének maximális életkori intervallumát határozza meg. Más szóval, az az idő, amely eltelik, mielőtt a böngésző ellenőrizheti egy eszköz új verzióját egy POP-ból.

A szolgáltatás engedélyezése `Cache-Control: max-age` a `Expires` POP-okból létrehozza és fejléceket hoz létre, és elküldi azokat a HTTP-ügyfélnek. Alapértelmezés szerint ezek a fejlécek felülírják az eredeti kiszolgáló által létrehozott fejléceket. A gyorsítótár-vezérlés fejléckezelése és a lejárati fejléckezelés funkciók azonban a viselkedés módosítására használhatók.

Legfontosabb információk:

- Ez a művelet nincs hatással a POP–származási kiszolgáló gyorsítótárának újraérvényesítésére. Az ilyen típusú újraérvényesítéseket `Cache-Control` `Expires` az eredeti kiszolgálótól kapott fejlécek határozzák meg, és testreszabhatók az alapértelmezett belső max-age és a Force Internal Max-Age funkciókkal.
- Konfigurálja ezt a szolgáltatást egy egész érték megadásával és a kívánt időegység kiválasztásával (például másodperc, perc, óra stb.).
- Ha ezt a szolgáltatást negatív értékre állítja, `Expires` a POP-ok a múltban beállított `Cache-Control: no-cache` időt küldenek, és minden válasz a böngészőre. Bár egy HTTP-ügyfél nem gyorsítótárazza a választ, ez a beállítás nem befolyásolja a POP-ok azon képességét, hogy gyorsítótárazza a választ az eredeti kiszolgálóról.
- Ha az időegységet "Ki" állásra állítja, az letiltja ezt a funkciót. Az `Cache-Control` `Expires` eredeti kiszolgáló válaszával gyorsítótárazott fejlécek átjutnak a böngészőbe.

**Alapértelmezett viselkedés:** Ki

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="follow-redirects"></a>Átirányítások követése

**Cél:** Azt határozza meg, hogy a kérelmek átirányíthatók-e az ügyfél forráskiszolgálója által visszaadott Hely fejlécben definiált állomásnévre.

Legfontosabb információk:

- A kérelmek csak az ugyanannak a platformnak megfelelő peremhálózati cnames-ekre irányíthatók át.

Érték|Eredmény
-|-
Engedélyezve|A kérelmek átirányíthatók.
Letiltva|A kérelmek nem lesznek átirányítva.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="force-internal-max-age"></a>Belső max-kor kényszerítése

**Cél:** A POP és az eredeti kiszolgáló gyorsítótárának újraérvényesítésének maximális életkori intervallumát határozza meg. Más szóval az az idő, amely a POP előtt telik el, hogy ellenőrizhesse, hogy a gyorsítótárazott eszköz megegyezik-e az eredeti kiszolgálón tárolt eszközzel.

Legfontosabb információk:

- Ez a szolgáltatás felülírja az eredeti `Cache-Control` kiszolgálóban meghatározott maximális életkori intervallumot vagy `Expires` a fejlécek ből létrehozott.
- Ez a szolgáltatás nincs hatással a böngésző pop-gyorsítótár-újraérvényesítési eredményeire. Az ilyen típusú újraérvényesítéseket `Cache-Control` `Expires` a böngészőnek küldött vagy fejlécek határozzák meg.
- Ez a szolgáltatás nem rendelkezik megfigyelhető hatással a POP által a kérelmezőnek küldött válaszra. Ez azonban hatással lehet a POP-ok által az eredeti kiszolgálóra küldött újraérvényesítési forgalom mennyiségére.
- A szolgáltatás konfigurálása:
    - Annak az állapotkódnak a kiválasztása, amelyre a belső maximális életkor vonatkozik.
    - Egész érték megadása és a kívánt időegység kiválasztása (például másodperc, perc, óra stb.). Ez az érték határozza meg a kérelem maximális életkori intervallumát.

- Az időegység "Ki" beállításával letiltja ezt a funkciót. A kért eszközökhöz nem lesz belső maximális életkori intervallum. Ha az eredeti fejléc nem tartalmaz gyorsítótárazási utasításokat, akkor az eszköz gyorsítótárba kerül az Alapértelmezett belső max-age funkció aktív beállítása szerint.

**Alapértelmezett viselkedés:** Ki

#### <a name="compatibility"></a>Kompatibilitás

A gyorsítótár-beállítások nyomon követésének módja miatt ez a funkció nem társítható a következő egyezési feltételekkel:
- AS-szám
- Ügyfél IP-címe
- Cookie paraméter
- Cookie paraméter Regex
- Ország
- Eszköz
- Szegély cname
- Hivatkozó tartomány
- Kérés fejlécének szeksika
- Kérelem fejléce Regex
- Helyettesítő fejléc kérése
- Kérelem módja
- Igénylési rendszer
- URL-lekérdezés literális
- URL-lekérdezés Regex
- URL-lekérdezési helyettesítő karakter
- URL-lekérdezési paraméter

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="h264-support-http-progressive-download"></a>H.264 támogatás (HTTP Progresszív Letöltés)

**Cél:** A tartalom streameléséhez használható H.264 fájlformátumok típusát határozza meg.

Legfontosabb információk:

- Adja meg az engedélyezett H.264 fájlnévkiterjesztések tértagokkal tagolt készletét a Fájlkiterjesztések beállításban. A Fájlkiterjesztések beállítás felülírja az alapértelmezett viselkedést. Az MP4 és az F4V-támogatás karbantartásához adja meg ezeket a fájlnévkiterjesztéseket a beállítás beállításakor.
- Adjon meg egy pont, amikor megad minden fájlnévkiterjesztést (például _.mp4_, _.f4v_).

**Alapértelmezett viselkedés:** A HTTP Progressive Download alapértelmezés szerint támogatja az MP4 és F4V adathordozókat.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="honor-no-cache-request"></a>Tiszteletbeli gyorsítótár nélküli kérelem

**Cél:** Azt határozza meg, hogy a HTTP-ügyfél gyorsítótárnélküli kérelmeit a rendszer továbbítja-e az eredeti kiszolgálóra.

Gyorsítótárnélküli kérelem akkor történik, amikor a `Cache-Control: no-cache` HTTP-ügyfél a HTTP-kérelemben egy és/vagy `Pragma: no-cache` fejlécet küld.

Érték|Eredmény
--|--
Engedélyezve|Lehetővé teszi, hogy egy HTTP-ügyfél gyorsítótárnélküli kérelmeit továbbítsa a rendszer az eredeti kiszolgálóra, és az eredeti kiszolgáló visszaadja a válaszfejléceket és a törzset a POP-on keresztül a HTTP-ügyfélnek.
Letiltva|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedés az, hogy megakadályozza a gyorsítótár nélküli kérelmek átvitelét az eredeti kiszolgálóra.

Az összes éles forgalom esetén erősen ajánlott, hogy hagyja ezt a funkciót az alapértelmezett letiltott állapotban. Ellenkező esetben az eredeti kiszolgálók nem lesznek védve a végfelhasználóktól, akik véletlenül sok gyorsítótár nélküli kérést indíthatnak el a weboldalak frissítése során, vagy a sok népszerű médialejátszótól, akik kódolva vannak, hogy minden videókéréssel gyorsítótár nélküli fejlécet küldjenek. Mindazonáltal ez a funkció hasznos lehet bizonyos nem éles átmeneti vagy tesztelési könyvtárakra, hogy lehetővé tegye a friss tartalom igény szerinti lekérését az eredeti kiszolgálóról.

A szolgáltatás miatt a kiindulási kiszolgálóra továbbítható kérelem gyorsítótár-állapota `TCP_Client_Refresh_Miss`a . A gyorsítótár állapotai jelentés, amely elérhető a Core jelentési modulban, statisztikai adatokat szolgáltat a gyorsítótár állapota szerint. Ez a jelentés lehetővé teszi a szolgáltatás miatt az eredeti kiszolgálóra továbbított kérelmek számának és százalékos arányának nyomon követését.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-origin-no-cache"></a>Az Origin no-cache figyelmen kívül hagyása

**Cél:** Azt határozza meg, hogy a CDN figyelmen kívül hagyja-e az eredeti kiszolgálóról kiszolgált következő irányelveket:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Legfontosabb információk:

- Konfigurálja ezt a szolgáltatást olyan állapotkódok tértagokkal tagolt listájának definiálásával, amelyeknél a fenti direktívákat figyelmen kívül hagyja a rendszer.
- A funkció érvényes állapotkódjai a következők: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 és 505.
- Tiltsa le ezt a szolgáltatást üres értékre állításával.

**Alapértelmezett viselkedés:** Az alapértelmezett viselkedés a fenti irányelvek betartatása.

#### <a name="compatibility"></a>Kompatibilitás

A gyorsítótár-beállítások nyomon követésének módja miatt ez a funkció nem társítható a következő egyezési feltételekkel:
- AS-szám
- Ügyfél IP-címe
- Cookie paraméter
- Cookie paraméter Regex
- Ország
- Eszköz
- Szegély cname
- Hivatkozó tartomány
- Kérés fejlécének szeksika
- Kérelem fejléce Regex
- Helyettesítő fejléc kérése
- Kérelem módja
- Igénylési rendszer
- URL-lekérdezés literális
- URL-lekérdezés Regex
- URL-lekérdezési helyettesítő karakter
- URL-lekérdezési paraméter

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-unsatisfiable-ranges"></a>Unsatisfiable tartományok figyelmen kívül hagyása

**Cél:** Azt a választ határozza meg, amelyet a rendszer az ügyfeleknek küld vissza, amikor egy kérelem egy 416 kért tartomány nem satisfiable állapotkódot hoz létre.

Alapértelmezés szerint ez az állapotkód akkor jelenik meg, ha a megadott bájttartomány-kérést nem tudja teljesíteni egy POP, és nincs megadva if-range kérelem fejlécmezője.

Érték|Eredmény
-|-
Engedélyezve|Megakadályozza, hogy a POP-ok egy 416-os kért tartomány nem satisfiable állapotkóddal válaszoljanak egy érvénytelen bájttartomány-kérésre. Ehelyett a kiszolgálók kézbesítik a kért eszközt, és egy 200 OK-t adnak vissza az ügyfélnek.
Letiltva|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedés a 416 kért tartomány nem satisfiable állapotkód nak a betartatása.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="internal-max-stale"></a>Belső max-állott

**Cél:** Azt határozza meg, hogy a gyorsítótárazott eszköz mennyi ideig marad el a szokásos lejárati idő után, ha a POP nem tudja újraérvényesíteni a gyorsítótárazott eszközt az eredeti kiszolgálóval.

Normális esetben, ha egy eszköz maximális életkora lejár, a POP újraérvényesítési kérelmet küld az eredeti kiszolgálónak. Az eredeti kiszolgáló ezután egy 304-es nem módosított, hogy a POP egy friss lízing a gyorsítótárazott eszköz, vagy pedig a 200 OK, hogy a POP egy frissített változata a gyorsítótárazott eszköz.

Ha a POP nem tud kapcsolatot létesíteni az eredeti kiszolgálóval az újraérvényesítés megkísérlése közben, akkor ez a belső max-elavult szolgáltatás szabályozza, hogy a POP továbbra is kiszolgálhassa-e a már elavult eszközt, és ha igen, mennyi ideig.

Vegye figyelembe, hogy ez az időintervallum akkor kezdődik, amikor az eszköz maximális életkora lejár, nem akkor, amikor a sikertelen újraérvényesítés történik. Ezért az a maximális időszak, amely alatt egy eszköz sikeres újraérvényesítés nélkül is kiszolgálható, a max-age plusz a max-os állott kombinációja által meghatározott idő. Ha például egy eszköz gyorsítótárazása 9:00-kor, 30 perces maximális életkorral és 15 perces max-állatúval történt, akkor a 9:44-nél végrehajtott sikertelen újraérvényesítési kísérlet azt eredményezné, hogy a végfelhasználó megkapja az elavult gyorsítótárazott eszközt, míg a 9:46-os sikertelen újraérvényesítési kísérlet azt eredményezné, hogy a végfelhasználó 504-es átjáró-időoutot kap.

A szolgáltatáshoz konfigurált értékeket az `Cache-Control: must-revalidate` `Cache-Control: proxy-revalidate` eredeti kiszolgáló felváltotta vagy fejlécek felváltotta. Ha ezek közül a fejlécek bármelyike érkezik az eredeti kiszolgálóról, amikor egy eszköz először gyorsítótárazott, majd a POP nem szolgál egy elavult gyorsítótárazott eszközt. Ebben az esetben, ha a POP nem tudja újraérvényesíteni az origót, amikor az eszköz maximális korhatár-intervalluma lejárt, a POP 504 átjáróidőtúladási hibát ad vissza.

Legfontosabb információk:

- A szolgáltatás konfigurálása:
    - Annak az állapotkódnak a kiválasztása, amelyre a max-elavult lesz alkalmazva.
    - Egész érték megadása, majd a kívánt időegység kiválasztása (például másodperc, perc, óra stb.). Ez az érték határozza meg az alkalmazott belső max-elavult értéket.

- Ha az időegységet "Ki" állásra állítja, az letiltja ezt a funkciót. A gyorsítótárazott eszköz nem lesz kiszolgálva a normál lejárati időn túl.

**Alapértelmezett viselkedés:** Két perc.

#### <a name="compatibility"></a>Kompatibilitás

A gyorsítótár-beállítások nyomon követésének módja miatt ez a funkció nem társítható a következő egyezési feltételekkel:
- AS-szám
- Ügyfél IP-címe
- Cookie paraméter
- Cookie paraméter Regex
- Ország
- Eszköz
- Szegély cname
- Hivatkozó tartomány
- Kérés fejlécének szeksika
- Kérelem fejléce Regex
- Helyettesítő fejléc kérése
- Kérelem módja
- Igénylési rendszer
- URL-lekérdezés literális
- URL-lekérdezés Regex
- URL-lekérdezési helyettesítő karakter
- URL-lekérdezési paraméter

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="log-query-string"></a>Lekérdezési karakterlánc naplózása

**Cél:** Azt határozza meg, hogy a rendszer tárolja-e a lekérdezési karakterláncot a hozzáférési naplókban lévő URL-címmel együtt.

Érték|Eredmény
-|-
Engedélyezve|Lehetővé teszi a lekérdezési karakterláncok tárolását, amikor URL-címeket rögzít egy hozzáférési naplóban. Ha egy URL-cím nem tartalmaz lekérdezési karakterláncot, akkor ennek a beállításnak nincs hatása.
Letiltva|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedés a lekérdezési karakterláncok figyelmen kívül hagyása, amikor URL-címeket rögzít egy hozzáférési naplóban.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="maximum-keep-alive-requests"></a>Maximális életben tartás kérések

**Cél:** A Keep-Alive kapcsolatra vonatkozó kérelmek maximális számát határozza meg annak bezárása előtt.

A kérelmek maximális számának alacsony értékre állítása nem ajánlott, és teljesítménycsökkenést eredményezhet.

Legfontosabb információk:

- Adja meg ezt az értéket egész egész számként.
- Ne adjon meg vesszőket vagy periódusokat a megadott értékbe.

**Alapértelmezett érték:** 10 000 kérelem

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-request-header"></a>Ügyfélkérelem fejlécének módosítása

**Cél:** Minden kérelem tartalmaz egy kérelemfejlécek, amelyek leírják azt. Ez a funkció a következőket teheti:

- Fűzte hozzá vagy írja felül a kérelemfejléchez rendelt értéket. Ha a megadott kérelemfejléc nem létezik, akkor ez a szolgáltatás hozzáadja a kérelemhez.
- A kérelemfejléc törlése a kérelemből.

Az eredeti kiszolgálóra továbbított kérelmek a szolgáltatás által végrehajtott módosításokat fogják tükrözni.

A kérelem fejlécén az alábbi műveletek egyike hajtható végre:

Beállítás|Leírás|Példa
-|-|-
Hozzáfűzés|A megadott érték hozzáadódik a meglévő kérelemfejléc-érték végéhez.|**Kérelem fejlécértéke (ügyfél):**<br/>Érték1<br/>**Kérelem fejlécértéke (szabálymotor):**<br/>Érték2 <br/>**Új kérelemfejléc-érték:** <br/>Érték1Érték2
Felülírás|A kérelem fejlécének értéke a megadott értékre lesz állítva.|**Kérelem fejlécértéke (ügyfél):**<br/>Érték1<br/>**Kérelem fejlécértéke (szabálymotor):**<br/>Érték2<br/>**Új kérelemfejléc-érték:**<br/> Érték2 <br/>
Törlés|Törli a megadott kérelemfejlécet.|**Kérelem fejlécértéke (ügyfél):**<br/>Érték1<br/>**Ügyfélkérelem-fejléc konfigurációjának módosítása:**<br/>Törölje a kérdéses kérelemfejlécet.<br/>**Eredmény:**<br/>A megadott kérelemfejléc nem lesz továbbítva az eredeti kiszolgálóra.

Legfontosabb információk:

- Győződjön meg arról, hogy a Név beállításban megadott érték pontos egyezést jelent a kívánt kérelemfejléchez.
- Az esetet nem veszik figyelembe a fejléc azonosítása céljából. A `Cache-Control` fejlécnév alábbi változatai például azonosításra használhatók:
    - gyorsítótár-vezérlés
    - GYORSÍTÓTÁR-VEZÉRLÉS
    - cachE-vezérlés
- Fejlécnév megadásakor csak alfanumerikus karaktereket, kötőjeleket vagy aláhúzásjeleket használjon.
- A fejléc törlésével megakadályozhatja, hogy a POP-ok továbbítsák az eredeti kiszolgálóra.
- A következő fejlécek vannak fenntartva, és ez a szolgáltatás nem módosítható:
    - Továbbított
    - gazda
    - Via
    - figyelmeztetés
    - x-forwarded-for
    - Az "x-ec" betűvel kezdődő fejlécnevek foglalásra vannak fenntartva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-response-header"></a>Ügyfélválasz fejlécének módosítása

Minden válasz tartalmaz egy sor válasz fejlécek, amelyek leírják azt. Ez a funkció a következőket teheti:

- A válaszfejléchez rendelt érték hozzáfűzése vagy felülírása. Ha a megadott válaszfejléc nem létezik, akkor ez a szolgáltatás hozzáadja a válaszhoz.
- Válaszfejléc törlése a válaszból.

Alapértelmezés szerint a válaszfejléc-értékeket egy forráskiszolgáló és a POP-ok határozzák meg.

A válaszfejlécen az alábbi műveletek egyike hajtható végre:

Beállítás|Leírás|Példa
-|-|-
Hozzáfűzés|A megadott érték hozzáadódik a meglévő válaszfejléc-érték végéhez.|**Válaszfejléc értéke (ügyfél):**<br />Érték1<br/>**Válasz fejlécének értéke (szabálymotor):**<br/>Érték2<br/>**Új válaszfejléc-érték:**<br/>Érték1Érték2
Felülírás|A válasz fejlécének értéke a megadott értékre lesz állítva.|**Válaszfejléc értéke (ügyfél):**<br/>Érték1<br/>**Válasz fejlécének értéke (szabálymotor):**<br/>Érték2 <br/>**Új válaszfejléc-érték:**<br/>Érték2 <br/>
Törlés|Törli a megadott válaszfejlécet.|**Válaszfejléc értéke (ügyfél):**<br/>Érték1<br/>**Ügyfélválaszfejléc-konfiguráció módosítása:**<br/>Törölje a kérdéses válaszfejlécet.<br/>**Eredmény:**<br/>A megadott válaszfejléc nem lesz továbbítva a kérelmezőnek.

Legfontosabb információk:

- Győződjön meg arról, hogy a Név beállításban megadott érték pontos egyezést jelent a kívánt válaszfejléchez.
- Az esetet nem veszik figyelembe a fejléc azonosítása céljából. A `Cache-Control` fejlécnév alábbi változatai például azonosításra használhatók:
    - gyorsítótár-vezérlés
    - GYORSÍTÓTÁR-VEZÉRLÉS
    - cachE-vezérlés
- A fejléc törlése megakadályozza, hogy a fejléc et továbbítsa a kérelmezőnek.
- A következő fejlécek vannak fenntartva, és ez a szolgáltatás nem módosítható:
    - elfogadás-kódolás
    - életkor
    - kapcsolat
    - tartalomkódolás
    - tartalom hossza
    - tartalomtartomány
    - dátum
    - kiszolgáló
    - Pótkocsi
    - átvitel-kódolás
    - Frissítés
    - Változik
    - Via
    - figyelmeztetés
    - Az "x-ec" betűvel kezdődő fejlécnevek foglalásra vannak fenntartva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="partial-cache-sharing"></a>Részleges gyorsítótár-megosztás

**Cél:** Azt határozza meg, hogy egy kérelem képes-e részlegesen gyorsítótárazott tartalmat generálni.

Ez a részleges gyorsítótár ezután az adott tartalomra vonatkozó új kérelmek teljesítésére használható, amíg a kért tartalom teljes gyorsítótárba nem kerül.

Érték|Eredmény
-|-
Engedélyezve|A kérelmek részben gyorsítótárazott tartalmat generálhatnak.
Letiltva|A kérelmek csak a kért tartalom teljes gyorsítótárazott verzióját hozhatják létre.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="prevalidate-cached-content"></a>Gyorsítótárazott tartalom előzetes érvényesítése

**Cél:** Azt határozza meg, hogy a gyorsítótárazott tartalom jogosult lesz-e a TTL lejárta előtt a korai újraérvényesítésre.

Adja meg, hogy mennyi idő telt el a kért tartalom TTL-jének lejárta előtt, amely alatt jogosult lesz a korai újraérvényesítésre.

Legfontosabb információk:

- Ha a "Ki" lehetőséget választja időegységként, az újraérvényesítést a gyorsítótárazott tartalom TTL-jének lejárta után kell leadni. Az időt nem szabad megadni, és figyelmen kívül kell hagyni.

**Alapértelmezett viselkedés:** Ki. Az újraérvényesítés csak a gyorsítótárazott tartalom TTL-jének lejárta után történhet meg.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="proxy-special-headers"></a>Proxy speciális fejlécei

**Cél:** A [Verizon-specifikus HTTP-kérelemfejlécek](cdn-verizon-http-headers.md) készletét határozza meg, amelyeket a pop-ról egy forráskiszolgálóra továbbít a rendszer.

Legfontosabb információk:

- A szolgáltatásban definiált minden cdn-specifikus kérelemfejléc et a rendszer egy eredeti kiszolgálóra továbbítja. A kizárt fejlécek nem továbbítódnak.
- Ha meg szeretné akadályozni egy CDN-specifikus kérelemfejléc továbbítását, távolítsa el azt a fejléclista mezőben lévő, szóközre bontott listából.

Az alapértelmezett listában a következő HTTP-fejlécek szerepelnek:
- Via
- X-Forwarded-For
- X-Forwarded-Proto
- X-host
- X-Midgress között
- X-Gateway-lista
- X-EK-név
- Gazdagép

**Alapértelmezett viselkedés:** Az összes CDN-specifikus kérelemfejléc et a rendszer továbbítja az eredeti kiszolgálóra.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="refresh-zero-byte-cache-files"></a>Nulla bájtos gyorsítótárfájlok frissítése

**Cél:** Azt határozza meg, hogy a POP-ok hogyan kezelik a HTTP-ügyfél 0 bájtos gyorsítótár-eszközre vonatkozó kérelmét.

Az érvényes értékek a következők:

Érték|Eredmény
--|--
Engedélyezve|Hatására a POP újra beolvassa az eszközt az eredeti kiszolgálóról.
Letiltva|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedés az, hogy kérésre érvényes gyorsítótár-kellékeket jelenítsen meg.

Ez a funkció nem szükséges a megfelelő gyorsítótárazáshoz és a tartalomkézbesítéshez, de megoldásként hasznos lehet. Például az eredeti kiszolgálókon lévő dinamikus tartalomgenerátorok véletlenül 0 bájtos válaszokat küldhetnek a POP-oknak. Az ilyen típusú válaszokat általában a POP-ok gyorsítótárazza. Ha tudja, hogy a 0 bájtos válasz soha nem érvényes válasz az ilyen tartalomra, ez a funkció megakadályozhatja, hogy az ilyen típusú eszközök kiszolgálják az ügyfeleknek.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-cacheable-status-codes"></a>Gyorsítótárazható állapotkódok beállítása

**Cél:** Azállapotkódok készletét határozza meg, amelyek gyorsítótárazott tartalmat eredményezhetnek.

Alapértelmezés szerint a gyorsítótárazás csak 200 OK válasz esetén engedélyezett.

Adja meg a kívánt állapotkódok tértaggal tagolt készletét.

Legfontosabb információk:

- Engedélyezze az Origin No-Cache funkciót. Ha ez a funkció nincs engedélyezve, akkor előfordulhat, hogy a nem 200 OK-válasz nem lesz gyorsítótárazva.
- A funkció érvényes állapotkódjai a következők: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 és 505.
- Ez a szolgáltatás nem használható a 200 OK állapotkódot generáló válaszok gyorsítótárazásának letiltására.

**Alapértelmezett viselkedés:** A gyorsítótárazás csak olyan válaszokesetén engedélyezett, amelyek 200 OK állapotkódot hoznak létre.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-client-ip-custom-header"></a>Ügyfél IP-címének egyéni fejlécének beállítása

**Cél:** Hozzáad egy egyéni fejlécet, amely IP-cím szerint azonosítja a kérelmező ügyfelet a kérelemhez.

A Fejlécnév beállítás annak az egyéni kérelemfejlécnek a nevét határozza meg, amelyen az ügyfél IP-címe található.

Ez a szolgáltatás lehetővé teszi, hogy az ügyfél származási kiszolgálója egyéni kérelemfejlécen keresztül találja meg az ügyfél IP-címeit. Ha a kérés a gyorsítótárból érkezik, akkor az eredeti kiszolgáló nem kap tájékoztatást az ügyfél IP-címéről. Ezért ajánlott, hogy ezt a funkciót olyan eszközökkel használja, amelyek nincsenek gyorsítótárazva.

Győződjön meg arról, hogy a megadott fejlécnév nem egyezik meg az alábbi nevek egyikével sem:

- Szabványos kérelemfejlécek nevei. A szabványos fejlécnevek listája a [2616.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)
- Fenntartott fejlécnevek:
    - továbbított -for
    - gazda
    - Változik
    - Via
    - figyelmeztetés
    - x-forwarded-for
    - Az "x-ec" betűvel kezdődő fejlécnevek foglalásra vannak fenntartva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-content-delivery-on-error"></a>Elavult tartalomkézbesítés hiba esetén

**Cél:** Azt határozza meg, hogy a rendszer a gyorsítótár-újraérvényesítés során vagy a kért tartalom nak az ügyfél forráskiszolgálójáról történő beolvasásakor a rendszer kézbesíti-e a lejárt gyorsítótárazott tartalmat.

Érték|Eredmény
-|-
Engedélyezve|Az elavult tartalom akkor jelenik meg a kérelmező számára, ha hiba történik az eredeti kiszolgálóval való kapcsolat során.
Letiltva|Az eredeti kiszolgáló hibáját a rendszer továbbítja a kérelmezőnek.

**Alapértelmezett viselkedés:** Tiltva

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-while-revalidate"></a>Elavult, míg újraérvényesíti

**Cél:** Javítja a teljesítményt azáltal, hogy lehetővé teszi a POP-ok számára, hogy elavult tartalmat jelenítsen meg a kérelmezőszámára az újraérvényesítés során.

Legfontosabb információk:

- A funkció viselkedése a kiválasztott időegységtől függően változik.
    - **Időegység:** Adjon meg egy időtartamot, és válasszon ki egy időegységet (például Másodperc, Perc, Órák stb.) az elavult tartalomkézbesítés engedélyezéséhez. Ez a beállítástípus lehetővé teszi, hogy a CDN meghosszabbítsa azt az időtartamot, amaddig, amíg a tartalmat kézbesítheti, mielőtt a következő képlet szerinti ellenőrzésre lenne szükség: **TTL** + **elavult, míg újraérvényesíti az időt**
    - **Ki:** Válassza a "Ki" lehetőséget az újraérvényesítés megköveteléséhez, mielőtt elavult tartalomra vonatkozó kérelmet kellene kézbesíteni.
        - Ne adjon meg egy ideig, mivel nem alkalmazható, és figyelmen kívül hagyja.

**Alapértelmezett viselkedés:** Ki. A kért tartalom kézbesítése előtt újra érvényesítésre van szükség.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth"></a>Token hitelesítés

**Cél:** Azt határozza meg, hogy a rendszer alkalmazza-e a jogkivonatalapú hitelesítést egy kérelemre.

Ha a token-alapú hitelesítés engedélyezve van, akkor csak a titkosított jogkivonatot biztosító és a jogkivonatáltal meghatározott követelményeknek megfelelő kérelmek lesznek megadva.

A tokenértékek titkosításához és visszafejtéséhez használt titkosítási kulcsot az elsődleges kulcs és a token hitelesítési lapján található biztonsági másolati kulcs beállításai határozzák meg. Ne feledje, hogy a titkosítási kulcsok platformspecifikusak.

**Alapértelmezett viselkedés:** Tiltva.

Ez a funkció elsőbbséget élvez a legtöbb funkcióval szemben, kivéve az URL-újraírási funkciót.

Érték | Eredmény
------|---------
Engedélyezve | A kért tartalom védelme jogkivonatalapú hitelesítéssel. Csak az ügyfelektől érkező, érvényes jogkivonatot biztosító és a követelményeknek megfelelő kéréseket kell teljesíteni. Az FTP-tranzakciók nem tartoznak a tokenalapú hitelesítés hatálya alól.
Letiltva| Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedés az, hogy a token-alapú hitelesítési konfiguráció határozza meg, hogy a kérelem biztonságos lesz-e.

#### <a name="compatibility"></a>Kompatibilitás

Ne használja a Token Auth-ot mindig egyezési feltétellel.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-denial-code"></a>Token hitelesítési megtagadási kódja

**Cél:** Azt határozza meg, hogy milyen típusú választ kap a rendszer a felhasználónak, ha a kérést jogkivonatalapú hitelesítés miatt megtagadják.

A rendelkezésre álló válaszkódokat az alábbi táblázat tartalmazza.

Válaszkód|Válasz neve|Leírás
-------------|-------------|--------
301|Véglegesen áthelyezve|Ez az állapotkód átirányítja az illetéktelen felhasználókat a Hely fejlécében megadott URL-címre.
302|Találat|Ez az állapotkód átirányítja az illetéktelen felhasználókat a Hely fejlécében megadott URL-címre. Ez az állapotkód az átirányítás végrehajtásának szabványos ipari módszere.
307|Ideiglenes átirányítás|Ez az állapotkód átirányítja az illetéktelen felhasználókat a Hely fejlécében megadott URL-címre.
401|Nem engedélyezett|Az állapotkód és a WWW-Authenticate válaszfejléc kombinálásával kérheti a felhasználótól a hitelesítést.
403|Forbidden|Ez az üzenet a szabványos 403 Tiltott állapot üzenet, amelyet egy jogosulatlan felhasználó fog látni, amikor védett tartalomhoz próbál hozzáférni.
404|A fájl nem található|Ez az állapotkód azt jelzi, hogy a HTTP-ügyfél képes volt kommunikálni a kiszolgálóval, de a kért tartalom nem található.

#### <a name="compatibility"></a>Kompatibilitás

Ne használjon Token Auth denial code mindig egyezési feltételt. Ehelyett használja az **egyéni megtagadások kezelése** szakaszt a **Jogkivonat-hitelesítés** lap a **kezelés** portálon. További információ: [Az Azure CDN-eszközök védelme jogkivonatos hitelesítéssel.](cdn-token-auth.md)

#### <a name="url-redirection"></a>URL-átirányítás

Ez a szolgáltatás támogatja az URL-átirányítást egy felhasználó által definiált URL-re, ha úgy van beállítva, hogy 3xx állapotkódot adjon vissza. Ez a felhasználó által definiált URL a következő lépések végrehajtásával adható meg:

1. Válassza ki a 3xx válaszkódot a Token Auth denial code szolgáltatáshoz.
2. Válassza a "Hely" lehetőséget a Választható fejlécnév beállításban.
3. Állítsa a Választható fejlécérték beállítást a kívánt URL-címre.

Ha nincs megadva URL a 3xx állapotkódhoz, akkor a 3xx állapotkód szabványos válaszoldala visszakerül a felhasználóhoz.

Az URL-átirányítás csak a 3xx válaszkódokesetében alkalmazható.

A Választható fejlécérték beállítás támogatja az alfanumerikus karaktereket, az idézőjeleket és a szóközöket.

#### <a name="authentication"></a>Hitelesítés

Ez a szolgáltatás támogatja a web-hitelesítés fejléc felvételének képességét, amikor a tokenalapú hitelesítéssel védett tartalomra vonatkozó jogosulatlan kérésre válaszol. Ha a web-hitelesítés fejléc "alap" a konfigurációban, akkor a jogosulatlan felhasználó nak kell megadnia a fiók hitelesítő adatait.

A fenti konfiguráció a következő lépések végrehajtásával érhető el:

1. Válassza ki a "401", mint a válaszkód a Token Auth denial code szolgáltatás.
2. Válassza a "WWW-Authenticate" lehetőséget a Választható fejlécnév beállításban.
3. Állítsa a Választható fejlécérték beállítást "alap" értékre.

A WWW-Authenticate fejléc csak a 401-es válaszkódokra vonatkozik.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-ignore-url-case"></a>Token hitelesítése URL-eset figyelmen kívül hagyása

**Cél:** Azt határozza meg, hogy a tokenalapú hitelesítés által végzett URL-összehasonlítások nem érzékenyek-e a kis- és nagybetűkre.

A szolgáltatás által érintett paraméterek a következők:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Az érvényes értékek a következők:

Érték|Eredmény
---|----
Engedélyezve|Hatására a POP figyelmen kívül hagyja az esetet a tokenalapú hitelesítési paraméterek URL-címeinek összehasonlításakor.
Letiltva|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedés az URL-összehasonlítások token hitelesítése, hogy a kis- és nagybetűk megkülönböztetése.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-parameter"></a>Token hitelesítési paramétere

**Cél:** Azt határozza meg, hogy a tokenalapú hitelesítéslekérdezési karakterlánc paramétert át kell-e nevezni.

Legfontosabb információk:

- Az Érték beállítás határozza meg a lekérdezési karakterlánc paraméter nevét, amelyen keresztül egy token adható meg.
- Az Érték beállítás nem állítható "ec_token" értékre.
- Győződjön meg arról, hogy az Érték beállításban megadott név csak érvényes URL-karaktereket tartalmaz.

Érték|Eredmény
----|----
Engedélyezve|Az Érték beállítás határozza meg a lekérdezési karakterlánc paraméter nevét, amelyen keresztül a jogkivonatokat meg kell határozni.
Letiltva|A token megadható a kérelem URL-címében egy nem definiált lekérdezési karakterlánc-paraméterként.

**Alapértelmezett viselkedés:** Tiltva. A token megadható a kérelem URL-címében egy nem definiált lekérdezési karakterlánc-paraméterként.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-redirect"></a>URL-átirányítás

**Cél:** Átirányítja a kérelmeket a Hely fejlécen keresztül.

A szolgáltatás konfigurációja a következő beállításokat igényli:

Beállítás|Leírás
-|-
Kód|Válassza ki a válaszkódot, amelyet vissza kell adni a kérelmezőnek.
Forrás & minta| Ezek a beállítások egy kérelem URI-mintáját határozzák meg, amely azonosítja az átirányítható kérelmek típusát. Csak azokat a kérelmeket irányítja át a program, amelyek URL-címe mindkét feltételnek megfelel: <br/> <br/> **Forrás (vagy tartalomhozzáférési pont):** Jelöljön ki egy relatív elérési utat, amely azonosítja az eredeti kiszolgálót. Ez az elérési út a _/XXXX/_ szakasz és a végpont neve. <br/><br/> **Forrás (minta):** Meg kell határozni azt a mintát, amely relatív elérési út szerint azonosítja a kérelmeket. Ennek a reguláris kifejezésmintának olyan elérési utat kell definiálnia, amely közvetlenül az előzőleg kiválasztott tartalom-hozzáférési pont után indul (lásd fent). <br/> - Győződjön meg arról, hogy a kérelem URI-feltételek (azaz a Forrás & minta) korábban definiált nem ütközik a szolgáltatáshoz meghatározott egyezési feltételekkel. <br/> - Adjon meg egy mintát; ha mintaként üres értéket használ, a program az összes karakterláncot egyezteti.
Cél| Adja meg azt az URL-címet, amelyre a fenti kérelmeket átirányítja. <br/><br/> Dinamikusan hozd létre ezt az URL-t a következő használatával: <br/> - Reguláris kifejezésminta <br/>- [HTTP-változók](cdn-http-variables.md) <br/><br/> Helyettesítse a forrásmintában rögzített értékeket a célmintával a $_n_ használatával, ahol _az n_ azonosítja az értéket a rögzítés sorrendjében. Például $1 a forrásmintában rögzített első értéket, míg a 2000-et a második érték jelöli. <br/>

Erősen ajánlott abszolút URL-t használni. A relatív URL-címek használata a CDN URL-címeket érvénytelen elérési útra irányíthatja át.

**Példaforgatókönyv**

Ez a példa bemutatja, hogyan lehet átirányítani egy él CNAME URL-címet, amely feloldódik erre az alap CDN URL-címre: http:\//marketing.azureedge.net/brochures

A minősítő kérelmeket a rendszer erre az alapperemű CNAME URL-címre irányítja át: http:\//cdn.mydomain.com/resources

Ez az URL-átirányítás a következő ![konfigurációval érhető el: URL-átirányítás](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Összefoglalás:**

- Az URL-átirányítási funkció határozza meg az átirányítandó kérelem URL-címeit. Ennek eredményeképpen nincs szükség további egyezési feltételekre. Bár az egyezési feltétel "Mindig" volt definiálva, csak a "marketing" ügyfél eredetének "brosúrák" mappájára hivatkozó kérések lesznek átirányítva.
- Az összes egyező kérés átlesz irányítva a Cél beállításban megadott peremhálózati CNAME URL-címre.
    - Példa forgatókönyv #1:
        - Mintakérelem (CDN URL): http:\//marketing.azureedge.net/brochures/widgets.pdf
        - Kérelem URL-címe (átirányítás\/után): http: /cdn.mydomain.com/resources/widgets.pdf  
    - Mintaforgatókönyv #2:
        - Mintakérelem (Edge CNAME\/URL): http: /marketing.mydomain.com/brochures/widgets.pdf
        - Kérelem URL-címe (átirányítás\/után): http: /cdn.mydomain.com/resources/widgets.pdf Minta forgatókönyv
    - Példa forgatókönyv #3:
        - Mintakérelem (Edge CNAME\/URL): http: /brochures.mydomain.com/campaignA/final/productC.ppt
        - Kérelem URL-címe (átirányítás\/után): http: /cdn.mydomain.com/resources/campaignA/final/productC.ppt 
- A kérelemséma (%{séma}) változó a Cél beállításban van kihasználva, amely biztosítja, hogy a kérelem séma az átirányítás után változatlan maradjon.
- A kérelemből rögzített URL-szegmensek a "$1" segítségével kerülnek az új URL-címhez.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-rewrite"></a>URL-átírás

**Cél:** Átírja a kérelem URL-címét.

Legfontosabb információk:

- A szolgáltatás konfigurációja a következő beállításokat igényli:

Beállítás|Leírás
-|-
 Forrás & minta | Ezek a beállítások olyan kérelem URI-mintát határoznak meg, amely azonosítja az újraírható kérelmek típusát. Csak azokat a kérelmeket írja át újra, amelyek URL-címe mindkét feltételnek megfelel: <br/><br/>  - **Forrás (vagy tartalomhozzáférési pont):** Jelöljön ki egy relatív elérési utat, amely azonosítja az eredeti kiszolgálót. Ez az elérési út a _/XXXX/_ szakasz és a végpont neve. <br/><br/> - **Forrás (minta):** Meg kell határozni azt a mintát, amely relatív elérési út szerint azonosítja a kérelmeket. Ennek a reguláris kifejezésmintának olyan elérési utat kell definiálnia, amely közvetlenül az előzőleg kiválasztott tartalom-hozzáférési pont után indul (lásd fent). <br/> Ellenőrizze, hogy a korábban definiált kérelem URI-feltételei (azaz a Forrás & minta) nem ütköznek-e a szolgáltatáshoz meghatározott egyezési feltételekkel. Adjon meg egy mintát; ha mintaként üres értéket használ, a program az összes karakterláncot egyezteti.
 Cél  |Adja meg azt a relatív URL-címet, amelyre a fenti kéréseket átírja: <br/>    1. Olyan tartalom-hozzáférési pont kiválasztása, amely azonosítja az eredeti kiszolgálót. <br/>    2. Relatív útvonal meghatározása a következők alkalmazásával: <br/>        - Reguláris kifejezésminta <br/>        - [HTTP-változók](cdn-http-variables.md) <br/> <br/> Helyettesítse a forrásmintában rögzített értékeket a célmintával a $_n_ használatával, ahol _az n_ azonosítja az értéket a rögzítés sorrendjében. Például $1 a forrásmintában rögzített első értéket, míg a 2000-et a második érték jelöli.

 Ez a funkció lehetővé teszi, hogy a POP-ok átírják az URL-címet anélkül, hogy hagyományos átirányítást végezne. Ez azt illeti, a kérelmező ugyanazt a válaszkódot kapja, mintha az átírt URL-t kérték volna.

**1. példa**

Ez a példa bemutatja, hogyan lehet átirányítani egy él CNAME URL-címet, amely feloldódik erre az alap CDN URL-címre: http:\//marketing.azureedge.net/brochures/

A minősítő kérelmeket a rendszer erre az alapperemű CNAME URL-címre irányítja át: http:\//MyOrigin.azureedge.net/resources/

Ez az URL-átirányítás a következő ![konfigurációval érhető el: URL-átirányítás](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**2. példa**

Ez a példa bemutatja, hogyan lehet átirányítani egy él CNAME URL-t nagybetűsről kisbetűsre reguláris kifejezések használatával.

Ez az URL-átirányítás a következő ![konfigurációval érhető el: URL-átirányítás](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**Összefoglalás:**

- Az URL-újraírási funkció határozza meg az újraírt kérelem URL-címeit. Ennek eredményeképpen nincs szükség további egyezési feltételekre. Bár az egyezési feltétel "Mindig" volt definiálva, csak a "marketing" ügyfél eredetének "brosúrák" mappájára hivatkozó kérések lesznek újraírva.

- A kérelemből rögzített URL-szegmensek a "$1" segítségével kerülnek az új URL-címhez.

#### <a name="compatibility"></a>Kompatibilitás

Ez a szolgáltatás olyan megfeleltetési feltételeket tartalmaz, amelyeknek meg kell felelni ahhoz, hogy egy kérelemre alkalmazható legyen. Az ütköző egyezési feltételek beállításának elkerülése érdekében ez a szolgáltatás nem kompatibilis a következő egyezési feltételekkel:

- AS-szám
- CDN eredete
- Ügyfél IP-címe
- Vevő eredete
- Igénylési rendszer
- URL elérési út könyvtára
- URL-elérési út kiterjesztése
- URL elérési út fájlneve
- URL-elérési út konstans
- URL elérési útja Regex
- URL-elérési út helyettesítő karaktere
- URL-lekérdezés literális
- URL-lekérdezési paraméter
- URL-lekérdezés Regex
- URL-lekérdezési helyettesítő karakter

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="user-variable"></a>Felhasználói változó

**Cél:** Csak belső használatra.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

## <a name="next-steps"></a>További lépések

- [Szabályok motorjának hivatkozása](cdn-verizon-premium-rules-engine-reference.md)
- [Szabálymotor feltételes kifejezései](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [A szabálymotor megfelel a feltételeknek](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [A HTTP viselkedésének felülbírálása a szabálymotor használatával](cdn-verizon-premium-rules-engine.md)
- [Az Azure CDN – áttekintés](cdn-overview.md)