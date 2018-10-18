---
title: Az Azure CDN szabálymotor funkciói |} A Microsoft Docs
description: Az Azure CDN dokumentációja szabályok adatbázismotor-funkciók.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: d5be292c66a07f43b40d12e10e4939d9d91559e1
ms.sourcegitcommit: b4a46897fa52b1e04dd31e30677023a29d9ee0d9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49395241"
---
# <a name="azure-cdn-rules-engine-features"></a>Az Azure CDN szabálymotor funkciói
Ez a cikk felsorolja a részletes leírását az elérhető funkciók az Azure Content Delivery Network (CDN) [Szabálymotorral](cdn-rules-engine.md).

A harmadik részében egy szabályt a funkciója. A funkció a alkalmazni a kérelemtípus egyezési feltételei által azonosított művelet típusát határozza meg.

## <a name="access-features"></a>Hozzáférési funkcióival

Ezek a funkciók lettek kialakítva tartalomhoz való hozzáférést.

Name (Név) | Cél
-----|--------
[Engedélyezi a hozzáférést (403)](#deny-access-403) | Azt határozza meg, hogy összes kérelem egy 403 Tiltott válasz a rendszer elutasítja.
[Jogkivonat-hitelesítés](#token-auth) | Meghatározza, hogy e Jogkivonatos hitelesítési kérelem vonatkozik.
[Jogkivonat hitelesítési elutasítási kód](#token-auth-denial-code) | Határozza meg a választ, amely egy felhasználó kapja, amikor egy kérelem jogkivonat-alapú hitelesítés miatt megtagadva.
[Jogkivonat hitelesítési URL-cím egyezőek](#token-auth-ignore-url-case) | Meghatározza, hogy által Jogkivonatos hitelesítési URL-cím összehasonlítások kis-és nagybetűket.
[Hitelesítési jogkivonat paraméterének](#token-auth-parameter) | Azt határozza meg, hogy a hitelesítési jogkivonat-alapú lekérdezési karakterlánc paramétereként kell nevezhető.


## <a name="caching-features"></a>Gyorsítótárazási szolgáltatások

Testre szabhatja, mikor és hogyan a tartalom gyorsítótárazva van-e ezek a funkciók lettek kialakítva.

Name (Név) | Cél
-----|--------
[A sávszélesség-paraméterek](#bandwidth-parameters) | Azt határozza meg, hogy aktívak-e a sávszélességet szabályozó paraméterek (például ec_rate és ec_prebuf).
[Sávszélesség-szabályozás](#bandwidth-throttling) | A válasz a--jelenléti pontok (POP) által nyújtott sávszélesség szabályozza.
[Gyorsítótár megkerülése](#bypass-cache) | Azt határozza meg, hogy a kérelem kell gyorsítótárazásának megkerülése.
[A Cache-Control fejléc kezelése](#cache-control-header-treatment) | Azt szabályozza, a következő generációja `Cache-Control` fejlécek alapján a POP külső Max-Age a szolgáltatás aktív esetén.
[Gyorsítótár-kulcsot a lekérdezési karakterláncban](#cache-key-query-string) | Meghatározza, hogy a gyorsítótár-kulcsot tartalmaz, vagy nem tartalmazza a lekérdezési karakterlánc paraméterei a kéréshez társított.
[Gyorsítótár-kulcs átírás](#cache-key-rewrite) | A gyorsítótár-kulcsot a kéréshez társított újraírja.
[Végezze el a gyorsítótár kitöltése](#complete-cache-fill) | Meghatározza, hogy mi történik, ha a kérés eredményeket részleges gyorsítótár-tévesztés a csatlakozási pont.
[Fájlok tömörítése](#compress-file-types) | Meghatározza formátumú fájlokat a tömörített fájlok a kiszolgálón.
[Alapértelmezett belső Max-Age](#default-internal-max-age) | Meghatározza, hogy az alapértelmezett max-age időköz POP forrás kiszolgáló gyorsítótár újbóli ellenőrzése.
[Fejléc kezelés lejár](#expires-header-treatment) | Azt szabályozza, a következő generációja `Expires` fejlécek, ha a külső Max-Age funkció aktív POP által.
[Külső Max-Age](#external-max-age) | Határozza meg a böngészőben a POP gyorsítótárban újraérvényesítésre max-age intervallumát.
[Belső Max-Age kényszerítése](#force-internal-max-age) | Meghatározza, hogy a max-age időköz POP forrás kiszolgáló gyorsítótár újbóli ellenőrzése.
[H.264 támogatási (HTTP progresszív letöltés)](#h264-support-http-progressive-download) | Meghatározza, hogy a tartalmak streamelésére használható H.264 fájlformátumok típusú.
[Honor No-Cache kérelem](#honor-no-cache-request) | Azt határozza meg, hogy egy HTTP-alapú no-cache kérelmeket a rendszer az eredeti kiszolgálóra továbbítja.
[Forrás No-Cache mellőzése](#ignore-origin-no-cache) | Határozza meg, hogy a CDN figyelmen kívül hagyja a bizonyos irányelvek egy forráskiszolgálóról szolgálja ki.
[Hagyja figyelmen kívül Unsatisfiable tartományok](#ignore-unsatisfiable-ranges) | Meghatározza, hogy az ügyfelek számára megjelenített, amikor kérést hoz létre egy 416 kért tartomány nem teljesíthető állapotkód: válasz.
[Belső maximális – elavult](#internal-max-stale) | Vezérlőelemek mennyi ideig a normál lejárati ideje elmúlt egy gyorsítótárazott eszköz kiszolgálása az internetszolgáltatójuk POP a Ha a jelenléti pont nem tud kísérelje meg újra érvényesítését a gyorsítótárazott objektumhoz, a forrás-kiszolgálóval.
[Részleges gyorsítótárának megosztása](#partial-cache-sharing) | Határozza meg, hogy egy kérelem hozhat létre részben a gyorsítótárazott tartalmat.
[Gyorsítótárazott tartalom prevalidate](#prevalidate-cached-content) | Meghatározza, hogy a gyorsítótárazott tartalmat jogosult korai újraérvényesítésre vár a TTL lejárata előtt.
[Nulla bájt méretű gyorsítótár fájlok frissítése](#refresh-zero-byte-cache-files) | Azt határozza meg, hogyan 0 bájtos gyorsítótár eszközök egy HTTP-alapú kérelem kre kezeli.
[Állítsa be a gyorsítótárazható állapotkódok](#set-cacheable-status-codes) | Határozza meg, a gyorsítótárazott tartalmat is előfordulhat, hogy állapotkódok.
[Hiba történt a régi Tartalomkézbesítési](#stale-content-delivery-on-error) | Meghatározza, hogy lejárt-e a gyorsítótárazott tartalmat kézbesíti a rendszer hiba esetén gyorsítótár újbóli ellenőrzése során, vagy az ügyfél forráskiszolgálóról a lekért tartalom lekérésekor.
[Elavult Revalidate közben](#stale-while-revalidate) | Javítja a teljesítményt azáltal, hogy a POP-Okba annak a kérelmező elavult ügyfél szolgálják a újraérvényesítésre végrehajtása közben.

## <a name="comment-feature"></a>Megjegyzés szolgáltatást

Ez a szolgáltatás egy szabály belül további információkkal szolgál.

Name (Név) | Cél
-----|--------
[Megjegyzés](#comment) | Lehetővé teszi, hogy a szabály belül hozzáadandó megjegyzés.
 
## <a name="header-features"></a>Fejléc-funkciók

Ezek a Funkciók hozzáadása, módosítása vagy törlése a fejlécek a kérelem vagy válasz lettek kialakítva.

Name (Név) | Cél
-----|--------
[Kor válaszfejléc](#age-response-header) | Meghatározza, hogy egy kora válasz fejléce szerepel-e a válasz a kérelmezőnek küldött.
[Gyorsítótár válaszfejlécek hibakeresése](#debug-cache-response-headers) | Azt határozza meg, hogy választ előfordulhat, hogy tartalmazzák az X-EK-Debug válaszfejléc, amely információt nyújt a gyorsítótár-házirendet a kért objektumhoz.
[Ügyfél-kérelem fejléce módosítsa](#modify-client-request-header) | Felülírja, fűzi hozzá, vagy egy fejléc töröl egy kérelmet.
[Ügyfél válasz fejléce módosítsa](#modify-client-response-header) | Felülírja, fűzi hozzá, vagy egy fejléc töröl egy válasz.
[Ügyfél IP-egyéni fejléc beállítása](#set-client-ip-custom-header) | Lehetővé teszi egyéni fejléc kérésre adni a kérelmet küldő ügyfél IP-címét.


## <a name="logging-features"></a>Naplózási szolgáltatások

Ezek a szolgáltatások testre szabhatja a nyers log-fájlokban tárolt adatok lettek kialakítva.

Name (Név) | Cél
-----|--------
[1. egyéni mező](#custom-log-field-1) | Meghatározza, hogy a formátum és a tartalom, amely az egyéni napló mező egy nyers naplófájlban van rendelve.
[Napló lekérdezési karakterlánc](#log-query-string) | Meghatározza, hogy e egy lekérdezési karakterláncot az URL-cím, a naplók elérése és tárolja.


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

Ezek a funkciók vezérelheti, hogyan kommunikál a CDN a forráskiszolgáló lettek kialakítva.

Name (Név) | Cél
-----|--------
[Életben tartási kérelmek maximális száma](#maximum-keep-alive-requests) | Életben tartási kapcsolat kérelmek maximális száma határozza meg, mielőtt le van zárva.
[Proxy speciális fejlécekkel](#proxy-special-headers) | Határozza meg, a CDN-specifikus kérelemfejlécek, amely az eredeti kiszolgálóra továbbítja a kapcsolódási pont.


## <a name="specialty-features"></a>Speciális funkciók

Ezek a szolgáltatások összetettebb funkciók tapasztalt felhasználók számára adja meg.

Name (Név) | Cél
-----|--------
[Gyorsítótárazható HTTP-metódusok](#cacheable-http-methods) | Meghatározza, hogy a hálózat gyorsítótárazható további HTTP-metódusok készletét.
[Gyorsítótárazható kérelem törzse mérete](#cacheable-request-body-size) | Határozza meg a meghatározásához a POST választ is lehet gyorsítótárba kerüljenek-e a küszöbértéket.
[Felhasználói változó](#user-variable) | Csak belső használatra.

 
## <a name="url-features"></a>URL-cím-funkciók

Ezek a funkciók lehetővé teszik a kérést átirányítja vagy egy másik URL-címet átírása.

Name (Név) | Cél
-----|--------
[Átirányítások követése](#follow-redirects) | Meghatározza, hogy e kérések átirányíthatók egy ügyfél forráskiszolgáló által visszaadott helyfejlécnek meghatározott gazdagépnevét.
[Átirányítási URL-címe](#url-redirect) | Átirányítja a kéréseket a Location fejlécet keresztül.
[URL-átírás](#url-rewrite)  | Átírja a kérelem URL-CÍMÉT.



## <a name="azure-cdn-rules-engine-features-reference"></a>Az Azure CDN szabálymotor-referencia funkciók

---
### <a name="age-response-header"></a>Kor válaszfejléc
**Célú**: azt határozza meg, hogy egy kora válasz fejléce szerepel-e a válasz a kérelmezőnek küldött.
Érték|Eredmény
--|--
Engedélyezve | A válasz a kérelmezőnek küldött az életkor válasz fejléce tartalmazza.
Letiltva | A válasz a kérelmezőnek küldött az életkor válaszfejléc nem tartozik.

**Alapértelmezett viselkedés**: letiltva.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-parameters"></a>A sávszélesség-paraméterek
**Cél:** határozza meg, hogy aktívak-e a sávszélességet szabályozó paraméterek (például ec_rate és ec_prebuf).

Sávszélesség-szabályozási paraméterek határozza meg, hogy az ügyfél által kért adatátviteli sebesség korlátozva, egy egyéni sebessége.

Érték|Eredmény
--|--
Engedélyezve|Lehetővé teszi, hogy tartsa tiszteletben a sávszélesség-szabályozási kérelmek kre.
Letiltva|A kapcsolódási pontokat figyelmen kívül hagyja a sávszélesség-szabályozási paraméterek okoz. A kért tartalomkézbesítés általában (vagyis a sávszélesség szabályozása nélkül).

**Alapértelmezett viselkedés:** engedélyezve van.
 
[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-throttling"></a>Sávszélesség-szabályozás
**Cél:** szabályozza a sávszélességet, a válasz-kre által biztosított.

A következő beállításokat is megfelelően a sávszélesség-szabályozás beállítása kell meghatározni.

Beállítás|Leírás
--|--
Kilobájt / másodperc|Ehhez a beállításhoz a maximális sávszélesség (Kb / s), előfordulhat, hogy lehet a választ.
Prebuf másodperc|Ehhez a beállításhoz a POP-Okba annak Várjon, amíg folyamatban van a sávszélesség a másodpercek számát. Ebben az időszakban nem korlátozott sávszélesség az a célja, hogy megakadályozza, hogy a media player a sávszélesség-szabályozás miatt szaggatott vagy pufferelési problémákat tapasztal, amikor.

**Alapértelmezett viselkedés:** letiltva.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bypass-cache"></a>Gyorsítótár megkerülése
**Cél:** határozza meg, hogy a kérelem kell gyorsítótárazásának megkerülése.

Érték|Eredmény
--|--
Engedélyezve|Akkor is, ha a tartalom volt előzőleg gyorsítótárazott POP hatására a térhet a forráskiszolgáló minden kérelemhez.
Letiltva|POP hatására a gyorsítótár üzletszabályzata előírja a válaszfejlécek meghatározott gyorsítótár-eszközökhöz.

**Alapértelmezett viselkedés:**

- **Nagy HTTP:** letiltva

<!---
- **ADN:** Enabled

--->

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-http-methods"></a>Gyorsítótárazható HTTP-metódusok
**Cél:** határozza meg a hálózat gyorsítótárazható további HTTP-metódusok.

Legfontosabb tudnivalókat:

- Ez a funkció azt feltételezi, hogy GET válaszok mindig gyorsítótárazza. Ennek eredményeképpen a GET HTTP-metódus nem kell kiválasztani beállításakor ezt a szolgáltatást.
- Ez a funkció csak POST HTTP-metódus támogatja. Hozzászólás a válaszok gyorsítótárazását úgy, hogy ez a funkció engedélyezése `POST`.
- Alapértelmezés szerint csak a kérelmek amelynek a törzse 14 Kb-nál kisebb lettek gyorsítótárazva. A kérelem maximális szervezet méretének beállításához használja a gyorsítótárazható kérelem törzse mérete funkció.

**Alapértelmezett viselkedés:** csak GET-válaszok lettek gyorsítótárazva.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-request-body-size"></a>Gyorsítótárazható kérelem törzse mérete
**Cél:** küszöbértéke meghatározása a gyorsítótárazható, hogy POST választ határozza meg.

A küszöbérték megadása egy kérelem maximális mérete határozza meg. Nagyobb kéréstörzs tartalmazó kérelmek nem lettek gyorsítótárazva.

Legfontosabb tudnivalókat:

- Ez a funkció csak akkor alkalmazható, ha a POST válaszok gyorsítótárazásának jogosultak. A gyorsítótárazható HTTP-metódusok funkció használatával engedélyezhető a POST-kérések gyorsítótárazása.
- A kérelem törzsében van kell figyelembe venni:
    - x-www-form-urlencoded értékek
    - Egyedi gyorsítótár-kulcs biztosítva
- Egy nagy méretű kérések maximális mérete meghatározása hatással lehetnek az adatok kézbesítési teljesítményére.
    - **Javasolt érték:** 14 Kb
    - **Minimális érték:** 1 KB-os

**Alapértelmezett viselkedés:** 14 Kb

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-control-header-treatment"></a>A Cache-Control fejléc kezelése
**Cél:** szabályozza a generációját `Cache-Control` fejlécek alapján a jelenléti pont, ha a külső Max-Age funkció aktív.

Ezt a konfigurációtípust elérése a legegyszerűbb módja, hogy helyezze el a külső Max-Age és a Cache-Control fejléc-kezelés szolgáltatások ugyanabban az utasításban.

Érték|Eredmény
--|--
Felülírás|Biztosítja, hogy a következő műveletek történnek:<br/> -Felülírja a `Cache-Control` a forráskiszolgáló által generált fejléc. <br/>-Hozzáadja a `Cache-Control` a külső Max-Age szolgáltatást, hogy a válasz által előállított fejléc.
Átengedéses|Biztosítja, hogy a `Cache-Control` fejléc a külső Max-Age funkció által előállított soha nem kerül a választ. <br/> Ha a forráskiszolgáló eredményez olyan `Cache-Control` fejléc haladnak keresztül a végfelhasználók számára. <br/> Ha a forráskiszolgáló nem eredményez olyan `Cache-Control` fejléc, akkor ez a beállítás okozhat a válaszfejléc nem tartalmazza egy `Cache-Control` fejléc.
Adja hozzá, ha hiányzik|Ha egy `Cache-Control` fejléc nem érkezett meg a forráskiszolgálóról, akkor ez a beállítás a `Cache-Control` a külső Max-Age funkció által előállított fejléc. Ez a beállítás akkor hasznos ahhoz, hogy az összes eszköz legyenek-e rendelve egy `Cache-Control` fejléc.
Eltávolítás| Ez a beállítás biztosítja, hogy egy `Cache-Control` fejléc a lehetőség nem része a a fejlécek válaszát. Ha egy `Cache-Control` fejléc már hozzá van rendelve, akkor a rendszer eltávolítja a fejlécek válaszát.

**Alapértelmezett viselkedés:** felülírása.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-query-string"></a>Gyorsítótár-kulcsot a lekérdezési karakterláncban
**Cél:** határozza meg, hogy a gyorsítótár-kulcsot tartalmaz, vagy nem tartalmazza a lekérdezési karakterlánc paraméterei a kéréshez társított.

Legfontosabb tudnivalókat:

- Adjon meg egy vagy több lekérdezési karakterlánc paraméterek nevei, és minden egyes paraméternév elválasztás egy szóköz.
- Ez a funkció határozza meg, hogy lekérdezési karakterlánc paraméterei foglalt vagy zárva a gyorsítótár-kulcsot. További információ a következő táblázat az egyes beállítások.

Típus|Leírás
--|--
 Belefoglalás|  Azt jelzi, hogy minden egyes megadott paraméter szerepelnie kell a gyorsítótár-kulcsot. Egyedi gyorsítótár-kulcs jön létre mindegyik kéréshez, amely ezt a szolgáltatást a megadott lekérdezési karakterlánc paramétereként egyedi értéket tartalmaz. 
 Tartalmazza az összes  |Azt jelzi, hogy egyedi gyorsítótár-kulcs jön létre mindegyik kéréshez egy objektumba, amely tartalmazza az egyedi lekérdezési karakterláncot. Ezt a konfigurációtípust általában nem ajánlott, mert egy kis csoportja találatot eredményező gyorsítótárbeli kereséseinek vezethet. Találatot eredményező gyorsítótárbeli kereséseinek száma kevés növeli a forráskiszolgáló terhelése, mivel a további kérések pedig idézést kell benyújtania. Ez a konfiguráció duplikálja a gyorsítótárazási viselkedés, más néven "egyedi gyorsítótárazási" a lekérdezési karakterlánc gyorsítótárazási oldalon. 
 Kizárás | Azt jelzi, hogy csak a megadott pedig be van zárva a gyorsítótár-kulcsot. Más lekérdezési karakterlánc paraméterei szerepelnek a gyorsítótár-kulcsot. 
 Az összes kihagyása  |Azt jelzi, hogy az összes lekérdezési karakterlánc paraméterei nem tartoznak a gyorsítótár-kulcsot. Ez a konfiguráció lehet azonos a "standard-gyorsítótár" alapértelmezett gyorsítótárazási viselkedés a lekérdezési karakterlánc gyorsítótárazási oldalon.  

A rules engine lehetővé teszi, hogy testre szabhatja a módon, amelyben lekérdezési karakterláncok gyorsítótárazása valósul meg. Megadhatja például, hogy csak az egyes helyek vagy fájltípusok lekérdezési karakterláncok gyorsítótárazása történik.

A "no-cache" lekérdezési karakterlánc gyorsítótárazási viselkedése a lekérdezési karakterlánc gyorsítótárazási oldalon duplikálásához hozzon létre egy szabályt, amely tartalmazza az URL-cím lekérdezési helyettesítő egyeztetési feltételt és egy Mellőzés gyorsítótár-szolgáltatás. Állítsa be az URL-cím lekérdezési helyettesítő az egyezési feltétellel csillagot (*).

>[!IMPORTANT] 
> Token használata engedélyezve van a bármilyen útvonalat ehhez a fiókhoz, standard-gyorsítótáras üzemmód-e a mód, amely a lekérdezési karakterláncok gyorsítótárazása is használható. További információkért lásd: [Az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési sztringekkel](cdn-query-string-premium.md).

#### <a name="sample-scenarios"></a>Mintaforgatókönyvek

Ennek a funkciónak a következő példa az alapértelmezett gyorsítótár-kulcs és a egy mintakérelmet biztosítja:

- **Mintakérelem:** http://wpc.0001.&lt; tartomány&gt;/800001/Origin/folder/asset.htm?sessionid=1234 és nyelv = EN & userid = 01
- **Alapértelmezett gyorsítótár-kulcs:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Belefoglalás

Minta-konfiguráció:

- **Típus:** belefoglalása
- **Parametry:** nyelv

Ezt a konfigurációtípust hoz létre a következő lekérdezési karakterlánc paraméter gyorsítótár-kulcs:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Tartalmazza az összes

Minta-konfiguráció:

- **Típus:** is tartalmaznak

Ezt a konfigurációtípust hoz létre a következő lekérdezési karakterlánc paraméter gyorsítótár-kulcs:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Kizárás

Minta-konfiguráció:

- **Típus:** kizárása
- **Parametry:** sessionid felhasználói azonosító

Ezt a konfigurációtípust hoz létre a következő lekérdezési karakterlánc paraméter gyorsítótár-kulcs:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Az összes kihagyása

Minta-konfiguráció:

- **Típus:** az összes kihagyása

Ezt a konfigurációtípust hoz létre a következő lekérdezési karakterlánc paraméter gyorsítótár-kulcs:

    /800001/Origin/folder/asset.htm

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-rewrite"></a>Gyorsítótár-kulcs átírás
**Cél:** átírja a kéréshez társított gyorsítótár-kulcsot.

A gyorsítótár-kulcs a relatív elérési útját, amely az eszköz azonosítja a gyorsítótárazás céljára. Más szóval a kiszolgálók ellenőrizze, hogy egy eszköz, az elérési út alapján egy gyorsítótárazott verziója a gyorsítótár-kulcs által meghatározott módon.

Ez a funkció konfigurálása egyaránt a következő beállítások meghatározásához:

Beállítás|Leírás
--|--
Eredeti elérési útja| A kérelmek, amelynek gyorsítótár-kulcs áll feladatátvitelt típusú relatív elérési utat határozza meg. Relatív elérési út egy alap forrás elérési útja kiválasztásával, és egy Reguláriskifejezés-mintának majd meghatározása lehet definiálni.
Új elérési utat|Adja meg az új gyorsítótár-kulcsot a relatív elérési utat. Relatív elérési út egy alap forrás elérési útja kiválasztásával, és egy Reguláriskifejezés-mintának majd meghatározása lehet definiálni. A relatív elérési út használatával dinamikusan gyűjteménynévmintája [HTTP változók](cdn-http-variables.md).
**Alapértelmezett viselkedés:** kérelmet a gyorsítótár-kulcs határozza meg a kérés URI azonosítója.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="comment"></a>Megjegyzés
**Cél:** lehetővé teszi, hogy a szabály belül hozzáadandó megjegyzés.

A funkció használatát, hogy ezek miért egy adott feltételnek megfelelő vagy a szolgáltatás a szabály hozzá lett adva egy szabály vagy az általános célú további információt.

Legfontosabb tudnivalókat:

- Legfeljebb 150 karakter adható meg.
- Csak alfanumerikus karaktereket használjon.
- Ez a funkció nem befolyásolja a szabály viselkedését. Adjon meg egy adott területre, ahol megadhatja a szabály hibaelhárítási segíthet, adatokat a későbbi felhasználás céljából, vagy csupán hivatott.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="complete-cache-fill"></a>Végezze el a gyorsítótár kitöltése
**Cél:** határozza meg, mi történik, ha egy kérelmet a POP részleges gyorsítótár-tévesztés eredményez.

Részleges gyorsítótár-tévesztés azt ismerteti, hogy a gyorsítótár állapotát, az eszközökre, amelyek nem teljesen töltődött le, a jelenléti pontra irányíthatja. Ha egy eszköz csak részben gyorsítótárazva, a csatlakozási pont, majd az adott eszköz a következő kérelmet továbbítja újra az eredeti kiszolgálóra.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
Részleges gyorsítótár-tévesztés általában akkor fordul elő, miután egy felhasználó egy letöltés megszakítása vagy az eszközök kizárólag kért tartomány HTTP-kérelmek használatával. Ez a funkció akkor a leghasznosabb nagy eszközök, amelyek nem általában letöltve (például videók) le. Ennek eredményeképpen a funkció alapértelmezés szerint engedélyezve van a HTTP nagy platformon. Le van tiltva, más platformokon.

Tartsa meg az alapértelmezett konfiguráció, a HTTP nagy platformon, mert ezzel csökkenti a vásárlói forráskiszolgálóig terhelését, és növeli a sebességet, amellyel az ügyfelek a tartalom letöltése.

Érték|Eredmény
--|--
Engedélyezve|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedést, hogy kényszerített kezdeményezéséhez egy háttérbeli adatbeolvasás az eszköz a forráskiszolgálóról a jelenléti pont. Amely után az eszköz lesz a helyi gyorsítótárban a jelenléti pont.
Letiltva|Megakadályozza, hogy a POP hajt végre egy háttérbeli adatbeolvasás az eszközre. Az eredménye, hogy a következő kérelmet az adott eszköz régió hatására POP kérhet, az ügyfél eredeti kiszolgálóra.

**Alapértelmezett viselkedés:** engedélyezve van.

#### <a name="compatibility"></a>Kompatibilitás
A módját, mely gyorsítótárban beállítások nyomon követi, mert ez a funkció nem rendelhető hozzá a következő egyezési feltételei: 
- AS-szám
- Ügyfél IP-címe
- Cookie-k paraméter
- Cookie-k paramétert reguláris kifejezés
- Ország
- Eszköz
- Edge Cname
- Hivatkozó tartomány
- Kérelem fejléce szövegkonstans
- Kérelem fejléce reguláris kifejezés
- Kérelem fejléce helyettesítő karakter
- Kérelmi metódus
- Kérelem séma
- URL-cím lekérdezési szövegkonstans
- URL-cím lekérdezési reguláris kifejezés
- URL-cím lekérdezési helyettesítő karakter
- URL-cím lekérdezési paraméter

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="compress-file-types"></a>Fájlok tömörítése
**Cél:** meghatározza formátumú fájlokat a tömörített fájlok a kiszolgálón.

Fájlformátum adható meg az Internet médiatípusa (például Content-Type) használatával. Internetes media típus platformfüggetlen metaadatok, amely lehetővé teszi a kiszolgálók egy adott eszköz a fájlformátum azonosításához. Az Internet közös adathordozó-típusok listáját lejjebb találja.

Internetes média típusa|Leírás
--|--
egyszerű szöveg|Egyszerű szöveges fájlok
text/html| HTML-fájlok
Text/css|Lépcsőzetes stílus lapok (CSS)
application/x-javascript|Javascript
alkalmazás/javascript|Javascript
Legfontosabb tudnivalókat:

- Adja meg az egy szóköz pedig külön határoló több adathordozó-típusnak. 
- Ez a funkció csak tömöríti az eszközök, amelyek mérete 1 MB-nál kisebb. Nagyobb eszközök nem tömöríti a kiszolgálókat.
- Bizonyos típusú tartalmak, például képek, videó, és hang médiatartalmak (például JPG, MP3, MP4 stb.), már tömörített. További tömörítési ilyen típusú eszközök ne jelentősen csökkentse a fájlméretet, mert javasoljuk, hogy nem engedélyezi a tömörítést a őket.
- A helyettesítő karaktereket, például csillagok, nem támogatottak.
- Előtt a szolgáltatás hozzá egy szabályt, győződjön meg arról, hogy be tömörítési letiltva a platform, amelyeken ez a szabály érvényben van a tömörítés oldalon.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="custom-log-field-1"></a>1. egyéni mező
**Cél:** határozza meg a formátum és a tartalom, amely hozzá lesz rendelve a nyers log fájl az egyéni napló mezője.

Az egyéni mező határozza meg, mely kérés-válasz fejléce értékek vannak tárolva a naplófájlok teszi lehetővé.

Alapértelmezés szerint az egyéni mező neve "x-ec_custom – 1." Ez a mező nevére testre szabható a nyers Log beállítások lapon.

Adja meg a kérelmek és válaszfejlécek formátuma a következőképpen van meghatározva:

Fejléc típusa|Formátum|Példák
-|-|-
Kérelem fejléce|`%{[RequestHeader]()}[i]()` | %{Accept-Encoding}i <br/> {Hivatkozó} i <br/> %{Authorization}i
Válaszfejléc|`%{[ResponseHeader]()}[o]()`| %{Age}o <br/> %{Content-Type}o <br/> A(z) % {cookie-k} o

Legfontosabb tudnivalókat:

- Egy egyéni mezőt bármely üzenetfejlécének mezői és az egyszerű szöveg kombinációját tartalmazhatja.
- Ebben a mezőben karakterek a következők: alfanumerikus karakterek (0-9, a – z, és A – Z), kötőjeleket, kettőspontokat, pontosvesszővel elválasztva, aposztrófok, vesszővel válassza el egymástól, időszakok, aláhúzásjeleket, Egyenlőségjelek, zárójelek, zárójeleket és tárolóhelyek. A százalék szimbólummal, és kapcsos zárójelek vannak csak engedélyezett, ha egy fejléc a mezőben adható meg.
- Mezők a megadott fejléc írásmódját egyeznie kell a kívánt kérés/válasz fejléc neve.
- Ha meg szeretné több a fejléceket, minden fejléc jelzi az elválasztó használatával. Például használhatja meg minden fejléc rövidítése:
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o 

**Alapértelmezett érték:** -

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Gyorsítótár válaszfejlécek hibakeresése
**Cél:** határozza meg, hogy a válasz tartalmazhat [X-EK-Debug válaszfejlécek](cdn-http-debug-headers.md), amely információt nyújt a gyorsítótár-házirendet a kért objektumhoz.

Hibakeresési gyorsítótár válasz fejlécei fog szerepelni a válasz a következő két teljesülése esetén:

- A gyorsítótár válaszfejlécek hibakeresési funkció engedélyezve van a megadott kérésre.
- A megadott kérelem hibakeresési gyorsítótár válaszfejlécek, melyeket fog szerepelni a válasz határozza meg.

Hibakeresési fejlécek kérheti többek között a következő fejlécére, és a megadott irányelveket a kérésben gyorsítótár választ:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Példa**

X-EK-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Érték|Eredmény
-|-
Engedélyezve|Hibakeresési gyorsítótár válaszfejlécek kérelmek választ küld, amely tartalmazza az X-EK-Debug fejléc.
Letiltva|Az X-EK-Debug válaszfejléc nem kerülnek bele a választ.

**Alapértelmezett viselkedés:** letiltva.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Alapértelmezett belső Max-Age
**Cél:** meghatározza, hogy az alapértelmezett max-age időköz POP forrás kiszolgáló gyorsítótár újbóli ellenőrzése. Más szóval mennyi ideig POP előtt adja meg, hogy ellenőrzi, hogy a gyorsítótárazott objektum megegyezik-e az eszközintelligencia, a forrás kiszolgálón tárolt.

Legfontosabb tudnivalókat:

- Ez a művelet csak történik jelöli egy forráskiszolgálóról, amely nem társít egy max-age jelzés a `Cache-Control` vagy `Expires` fejléc.
- Ez a művelet nem kerül sor az eszközök, amelyek nem minősülnek gyorsítótárazható.
- Ez a művelet nem befolyásolja a böngészőben a POP gyorsítótárban revalidations. Az ilyen típusú revalidations határozza meg a `Cache-Control` vagy `Expires` a böngészőre, amely testre szabható a külső Max-Age funkcióval küldött fejlécek.
- Ez a művelet eredményei nem rendelkezik megfigyelhető hatással lesz a válaszfejlécek, és tartalmat a POP-tartalomra vonatkozó ad vissza, de, előfordulhat, hogy hatással a forráskiszolgálóra POP küldött újraérvényesítésére forgalom mennyisége.
- Ez a funkció szerint konfigurálja:
    - Válassza az állapotkódot, amelynek egy alapértelmezett belső max-age is alkalmazható.
    - Adjon meg egy egész számot, és válassza a kívánt időegység (például másodperc, perc, óra, stb.). Ez az érték határozza meg az alapértelmezett belső max-age időköz.

- Az időegység "Kikapcsolva" beállítás hozzárendelése egy alapértelmezett belső max-age gyakoriság 7 nap a kéréseket, amely nincs hozzárendelve a max-age jelzéssel ellátott azok `Cache-Control` vagy `Expires` fejléc.

**Alapértelmezett érték:** 7 nap

#### <a name="compatibility"></a>Kompatibilitás
A módját, mely gyorsítótárban beállítások nyomon követi, mert ez a funkció nem rendelhető hozzá a következő egyezési feltételei: 
- AS-szám
- Ügyfél IP-címe
- Cookie-k paraméter
- Cookie-k paramétert reguláris kifejezés
- Ország
- Eszköz
- Edge Cname
- Hivatkozó tartomány
- Kérelem fejléce szövegkonstans
- Kérelem fejléce reguláris kifejezés
- Kérelem fejléce helyettesítő karakter
- Kérelmi metódus
- Kérelem séma
- URL-cím lekérdezési szövegkonstans
- URL-cím lekérdezési reguláris kifejezés
- URL-cím lekérdezési helyettesítő karakter
- URL-cím lekérdezési paraméter

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="deny-access-403"></a>Engedélyezi a hozzáférést (403)
**Célú**: azt határozza meg, hogy összes kérelem egy 403 Tiltott válasz a rendszer elutasítja.

Érték | Eredmény
------|-------
Engedélyezve| Hatására az összes kérelem, amelyek megfelelnek a megfelelési feltételek, a rendszer elutasítja a 403 Tiltott válasz.
Letiltva| Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedést, hogy a forrás-kiszolgáló meghatározni a visszaadott válasz.

**Alapértelmezett viselkedés**: letiltva

> [!TIP]
   > Ez a funkció egy lehetséges felhasználása, hogy társítsa a kérés fejlécében lévő egyeztetési feltételt HTTP hivatkozók által használt beágyazott hivatkozások a tartalomhoz való hozzáférés letiltását.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="expires-header-treatment"></a>Fejléc kezelés lejár
**Cél:** szabályozza a generációját `Expires` fejlécek, ha a külső Max-Age funkció aktív POP által.

Ezt a konfigurációtípust elérése a legegyszerűbb módja, hogy helyezze el a külső Max-Age és a fejléc-kezelés lejár szolgáltatások ugyanabban az utasításban.

Érték|Eredmény
--|--
Felülírás|Biztosítja, hogy a következő műveletek kerül sor:<br/>-Felülírja a `Expires` a forráskiszolgáló által generált fejléc.<br/>-Hozzáadja a `Expires` a külső Max-Age szolgáltatást, hogy a válasz által előállított fejléc.
Átengedéses|Biztosítja, hogy a `Expires` fejléc a külső Max-Age funkció által előállított soha nem kerül a választ. <br/> Ha a forráskiszolgáló hoz létre egy `Expires` fejléc, akkor továbbítja a végfelhasználók számára. <br/>Ha a forráskiszolgáló nem eredményez olyan `Expires` fejléc, akkor ez a beállítás okozhat a válaszfejléc nem tartalmazza egy `Expires` fejléc.
Adja hozzá, ha hiányzik| Ha egy `Expires` fejléc nem érkezett meg a forráskiszolgálóról, akkor ez a beállítás a `Expires` a külső Max-Age funkció által előállított fejléc. Ez a beállítás akkor hasznos ahhoz, hogy az összes eszköz hozzá lesz rendelve egy `Expires` fejléc.
Eltávolítás| Biztosítja, hogy egy `Expires` fejléc a lehetőség nem része a a fejlécek válaszát. Ha egy `Expires` fejléc már hozzá van rendelve, akkor a rendszer eltávolítja a fejlécek válaszát.

**Alapértelmezett viselkedés:** felülírása

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="external-max-age"></a>Külső Max-Age
**Cél:** határozza meg a böngészőben a POP gyorsítótárban újraérvényesítésre max-age intervallumát. Más szóval mennyi ideig fogja továbbítani, mielőtt egy böngészőben ellenőrizheti a POP-eszköz új verziója.

A funkció engedélyezése generál `Cache-Control: max-age` és `Expires` kre fejléceinek és küldje el a HTTP-alapú. Alapértelmezés szerint ezek a fejlécek felülírja ezeket a fejléceket, a forráskiszolgáló által létrehozott. Azonban a Cache-Control fejléc kezelés és a fejléc-kezelés lejár szolgáltatásai használhatók ezen viselkedés.

Legfontosabb tudnivalókat:

- Ez a művelet nem befolyásolja a jelenléti pontra Irányíthatja forrás kiszolgáló gyorsítótár revalidations. Az ilyen típusú revalidations határozza meg a `Cache-Control` és `Expires` fejlécek a forráskiszolgáló kapott, és testre szabható az alapértelmezett belső Max-Age és a kényszerített belső Max-Age szolgáltatásokkal.
- Ez a funkció konfigurálásához adjon meg egy egész számot, és kiválasztja a kívánt időegység (például másodperc, perc, óra, stb.).
- Ez a funkció beállítása egy negatív érték hatására a POP-Okba annak küldése egy `Cache-Control: no-cache` és a egy `Expires` minden a böngészőnek adott válaszban a korábban beállított idő. Egy HTTP-alapú rendszer gyorsítótárazza a választ, bár ez a beállítás nem érinti a kapcsolódási pontokat képes gyorsítótárazza a választ a forráskiszolgálóról.
- Az időegység "Kikapcsolva" letiltja ezt a szolgáltatást. A `Cache-Control` és `Expires` fejlécek gyorsítótárazza a választ az eredeti kiszolgálóra, és továbbítja a böngészőben.

**Alapértelmezett viselkedés:** kikapcsolása

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="follow-redirects"></a>Átirányítások követése
**Cél:** meghatározza, hogy e kérések átirányíthatók egy ügyfél forráskiszolgáló által visszaadott helyfejlécnek meghatározott gazdagépnevét.

Legfontosabb tudnivalókat:

- Kérelmek csak átirányíthatóak edge CNAME-rekordokat, amelyek megfelelnek a ugyanarra a platformra.

Érték|Eredmény
-|-
Engedélyezve|Átirányítható a kérelmeket.
Letiltva|Kérelmek nem irányítja át.

**Alapértelmezett viselkedés:** letiltva.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="force-internal-max-age"></a>Belső Max-Age kényszerítése
**Cél:** meghatározza, hogy a max-age időköz POP forrás kiszolgáló gyorsítótár újbóli ellenőrzése. Más szóval az, hogy mennyi ideig továbbítja a kapcsolódási pont ellenőrizheti, hogy a gyorsítótárazott objektum megegyezik-e az eszközintelligencia, a forrás kiszolgálón tárolt előtt.

Legfontosabb tudnivalókat:

- Ez a funkció felülírják a max-age időköz meghatározott `Cache-Control` vagy `Expires` fejlécek egy forráskiszolgálóról jön létre.
- Ez a funkció nem befolyásolja a böngészőben a POP gyorsítótárban revalidations. Az ilyen típusú revalidations határozza meg a `Cache-Control` vagy `Expires` a böngésző kap, fejlécek.
- Ez a szolgáltatás nem rendelkezik a válasz a kérelmezőnek egy felugró kézbesítette megfigyelhető hatással lesz. Azonban még rendelkezik kre az eredeti kiszolgálóra küldött újraérvényesítésre forgalom mennyisége hatással lesz.
- Ez a funkció szerint konfigurálja:
    - Válassza az állapotkódot, amelynek a belső max-age lépnek érvénybe.
    - Adjon meg egy egész számot, és kiválasztja a kívánt időegység (például másodperc, perc, óra, stb.). Ez az érték határozza meg a kérelem max-age időköz.

- Az időegység "Kikapcsolva" letiltja ezt a szolgáltatást. Egy belső max-age időköz nem lehet hozzárendelni kívánt eszközök. Ha az eredeti fejléc nem tartalmaz gyorsítótárazási utasításait, majd az eszköz a gyorsítótárban tartja az alapértelmezett belső Max-Age funkció az aktív beállításnak megfelelően.

**Alapértelmezett viselkedés:** kikapcsolása

#### <a name="compatibility"></a>Kompatibilitás
A módját, mely gyorsítótárban beállítások nyomon követi, mert ez a funkció nem rendelhető hozzá a következő egyezési feltételei: 
- AS-szám
- Ügyfél IP-címe
- Cookie-k paraméter
- Cookie-k paramétert reguláris kifejezés
- Ország
- Eszköz
- Edge Cname
- Hivatkozó tartomány
- Kérelem fejléce szövegkonstans
- Kérelem fejléce reguláris kifejezés
- Kérelem fejléce helyettesítő karakter
- Kérelmi metódus
- Kérelem séma
- URL-cím lekérdezési szövegkonstans
- URL-cím lekérdezési reguláris kifejezés
- URL-cím lekérdezési helyettesítő karakter
- URL-cím lekérdezési paraméter

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="h264-support-http-progressive-download"></a>H.264 támogatási (HTTP progresszív letöltés)
**Cél:** meghatározza, hogy a tartalmak streamelésére használható H.264 fájlformátumok típusú.

Legfontosabb tudnivalókat:

- Meghatároz egy szóközzel elválasztott olyan engedélyezett H.264 fájlnév-kiterjesztések fájlkiterjesztések változatban. A fájlkiterjesztések beállítás felülírja az alapértelmezett viselkedést. MP4 és F4V terméktámogatási karbantartásához beleértve azokat a fájlkiterjesztéseket, ha ezt a beállítást. 
- Tartalmazza az adott időszakban egyes fájlnév-kiterjesztés megadása (például _.mp4_, _.f4v_).

**Alapértelmezett viselkedés:** alapértelmezés szerint HTTP progresszív letöltés MP4 és F4V media támogatja.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="honor-no-cache-request"></a>Honor No-Cache kérelem
**Cél:** határozza meg, hogy egy HTTP-ügyfelének a no-cache kérelmek továbbítani kell-e az eredeti kiszolgálóra.

No-cache kérelem akkor történik, ha a HTTP ügyfél küld egy `Cache-Control: no-cache` és/vagy `Pragma: no-cache` a HTTP-kérelem fejléce.

Érték|Eredmény
--|--
Engedélyezve|Lehetővé teszi, hogy egy HTTP-alapú no-cache az eredeti kiszolgálóra továbbítja a kéréseket, és a forráskiszolgáló adja vissza a válaszfejlécek, és a szervezet a jelenléti pontra Irányíthatja át a HTTP-ügyfélnek.
Letiltva|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedést, hogy megakadályozza, hogy a no-cache kérelmeket az eredeti kiszolgálóra továbbítva.

Minden éles forgalom azt javasoljuk, hagyja üresen ezt a szolgáltatást le van tiltva alapértelmezett állapotában. Ellenkező esetben szerverek fog védelme nem biztosítható felhasználók, akik véletlenül indíthat sok no-cache kérelem weblapok frissítésekor vagy a számos népszerű médialejátszókhoz, amely kódolása a no-cache fejlécet minden videó kérelem küldése. Ez a funkció azonban bizonyos nem éles átmeneti, vagy a tesztelés könyvtárak, annak érdekében, hogy friss tartalom igény szerinti lekérése a forráskiszolgálóról a alkalmazni hasznos lehet.

A gyorsítótár állapotát egy kérelmet, amely továbbítható egy forráskiszolgáló miatt ez a funkció nem jelentett `TCP_Client_Refresh_Miss`. A gyorsítótárak Allapota jelentést, amely elérhető a Core-modul reporting, a gyorsítótár állapot szerint statisztikai adatokat biztosít. Ez a jelentés lehetővé teszi az eredeti kiszolgálóra miatt ez a funkció számát és százalékos arányát továbbított kérések nyomon követésére.

**Alapértelmezett viselkedés:** letiltva.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-origin-no-cache"></a>Forrás No-Cache mellőzése
**Cél:** határozza meg, akár a CDN figyelmen kívül hagyja a következő irányelvek egy forráskiszolgálóról szolgálja ki:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Legfontosabb tudnivalókat:

- Ez a funkció konfigurálásához meghatározása, amelynek a fenti irányelvek figyelmen kívül állapotkódok szóközzel elválasztott listáját.
- Ez a funkció érvényes állapotkódjai halmaza vannak: 200, 203, 300, 301, 302, 305, 307, 400-as, 401-es, 402, 403-as, 404-es, 405-ös, 406, 407-es, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500-as, 501, 502-es, 503-as, 504, és 505-ös.
- Ez a funkció letiltása üres értékre állításával.

**Alapértelmezett viselkedés:** az alapértelmezett viselkedést, hogy tartsa tiszteletben a fenti irányelvek.

#### <a name="compatibility"></a>Kompatibilitás
A módját, mely gyorsítótárban beállítások nyomon követi, mert ez a funkció nem rendelhető hozzá a következő egyezési feltételei: 
- AS-szám
- Ügyfél IP-címe
- Cookie-k paraméter
- Cookie-k paramétert reguláris kifejezés
- Ország
- Eszköz
- Edge Cname
- Hivatkozó tartomány
- Kérelem fejléce szövegkonstans
- Kérelem fejléce reguláris kifejezés
- Kérelem fejléce helyettesítő karakter
- Kérelmi metódus
- Kérelem séma
- URL-cím lekérdezési szövegkonstans
- URL-cím lekérdezési reguláris kifejezés
- URL-cím lekérdezési helyettesítő karakter
- URL-cím lekérdezési paraméter

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-unsatisfiable-ranges"></a>Hagyja figyelmen kívül Unsatisfiable tartományok 
**Cél:** határozza meg a kérést hoz létre egy 416 kért tartomány nem teljesíthető állapotkód: Ha az ügyfelek számára visszaadott válasz.

Alapértelmezés szerint a megadott bájttartomány-kérés csatlakozási pont nem tud teljesíteni, és a egy If-tartomány kérelem fejléce mező nincs meghatározva az állapotkódot adott vissza.

Érték|Eredmény
-|-
Engedélyezve|Megakadályozza, hogy a POP egy 416 kért tartomány nem teljesíthető állapotkód: Érvénytelen bájttartomány irányuló válaszol. Ehelyett a kiszolgálók nyújthat a kért objektumhoz, és térjen vissza a 200-as rendben az ügyfél.
Letiltva|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedést, hogy tartsa tiszteletben a 416 kért tartomány nem teljesíthető állapotkód:.

**Alapértelmezett viselkedés:** letiltva.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="internal-max-stale"></a>Belső maximális – elavult
**Cél:** vezérlőelemek mennyi ideig korábbi a normál lejárati időt, a gyorsítótárazott objektumot kiszolgálása az internetszolgáltatójuk POP a Ha a jelenléti pont nem tud kísérelje meg újra érvényesítését a gyorsítótárazott objektumhoz, a forrás-kiszolgálóval.

Normális esetben, ha egy eszköz max-age időtartam lejár, a jelenléti pont egy újraérvényesítésre kérelmet küld a forráskiszolgáló. A forrás törlődnek majd vagy a 304 válaszol nem módosította a jelenléti pont friss biztosíthat bérletbe a gyorsítótárazott objektum – a 200-as rendben adja meg a jelenléti pontra Irányíthatja a gyorsítótárazott objektum frissített verzióra.

Ha a jelenléti pont nem tud egy újraérvényesítésre kísérlet során a forrás-kiszolgálóval kapcsolatot létesíteni, majd a belső Max-elavult funkció szabályozza e, és mennyi ideig, a jelenléti pont továbbra is szolgálja ki a most – elavult eszköz.

Vegye figyelembe, hogy ez alatt az időtartam kezdődik, amikor az eszköz max-age lejár, nem a sikertelen újraérvényesítésre bekövetkezésekor. A leghosszabb, amely során egy eszköz sikeres újraérvényesítésre nélkül kiszolgálható ezért max-age és max-elavult kombinációja által megadott idő. Például ha egy eszköz gyorsítótárazva lett a max-age 30 perc és 15 perc maximális elavult 9:00-kor, majd egy sikertelen újraérvényesítésére kísérlet a 9:44 eredményez a végfelhasználó fogadása a elavult gyorsítótárazott objektumhoz, amíg egy sikertelen újraérvényesítésére kísérlet a 9:46 eredményez az en d felhasználó fogad egy 504 időtúllépés az átjárón.

Ezt a szolgáltatást felváltotta beállított értéket `Cache-Control: must-revalidate` vagy `Cache-Control: proxy-revalidate` fejlécek, a forrás-kiszolgálótól kapott. Ha ezek a fejlécek fogadásakor a forráskiszolgálóról, ha egy eszköz először a rendszer gyorsítótárazza, majd a jelenléti pont nem szolgálja ki egy elavult gyorsítótárazott objektumot. Ebben az esetben ha a jelenléti pont nem tudja kísérelje meg újra érvényesítését a forrás-Ha az eszköz max-age intervalluma érvényessége lejárt, a jelenléti pont hibát ad vissza egy 504 átjáró időtúllépése.

Legfontosabb tudnivalókat:

- Ez a funkció szerint konfigurálja:
    - Válassza az állapotkódot, amelynek a maximális elavult lépnek érvénybe.
    - Adjon meg egy egész számot, és válassza a kívánt időegység (például másodperc, perc, óra, stb.). Ez az érték határozza meg, hogy a belső max-elavult rendszer alkalmazni fogja őket.

- Az időegység "Kikapcsolva" letiltja ezt a szolgáltatást. A gyorsítótárazott objektumot nem a normál lejárati ideje meghaladja kell kézbesíteni.

**Alapértelmezett viselkedés:** két perc

#### <a name="compatibility"></a>Kompatibilitás
A módját, mely gyorsítótárban beállítások nyomon követi, mert ez a funkció nem rendelhető hozzá a következő egyezési feltételei: 
- AS-szám
- Ügyfél IP-címe
- Cookie-k paraméter
- Cookie-k paramétert reguláris kifejezés
- Ország
- Eszköz
- Edge Cname
- Hivatkozó tartomány
- Kérelem fejléce szövegkonstans
- Kérelem fejléce reguláris kifejezés
- Kérelem fejléce helyettesítő karakter
- Kérelmi metódus
- Kérelem séma
- URL-cím lekérdezési szövegkonstans
- URL-cím lekérdezési reguláris kifejezés
- URL-cím lekérdezési helyettesítő karakter
- URL-cím lekérdezési paraméter

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="log-query-string"></a>Napló lekérdezési karakterlánc
**Cél:** meghatározza, hogy e egy lekérdezési karakterláncot az URL-cím, a naplók elérése és tárolni.

Érték|Eredmény
-|-
Engedélyezve|Egy hozzáférési napló URL-címek dokumentálásakor lehetővé teszi, hogy a lekérdezési karakterláncok tárolására. Ha egy URL-cím nem tartalmaz egy lekérdezési karakterláncot, majd ezt a beállítást nem lesz egy hatása.
Letiltva|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedést, hogy egy hozzáférési napló URL-címek rögzítésekor a lekérdezési karakterláncok kihagyása.

**Alapértelmezett viselkedés:** letiltva.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="maximum-keep-alive-requests"></a>Életben tartási kérelmek maximális száma
**Cél:** életben tartási kapcsolat kérelmek maximális száma határozza meg, mielőtt le van zárva.

Kérelmek maximális számának beállítása alacsony értékre nem ajánlott, és a teljesítményromlást eredményezhet.

Legfontosabb tudnivalókat:

- Adja meg ennek az értéknek egész számként.
- Vegyen fel vesszővel vagy időszakokban a megadott értéket.

**Alapértelmezett érték:** 10 000 kérelem

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-request-header"></a>Ügyfél-kérelem fejléce módosítsa
**Cél:** minden kérelmet tartalmaz, amelyek bemutatják, hogy kérelemfejlécek. Ez a szolgáltatás közül választhat:

- Hozzáfűzése, vagy egy kérés fejlécéhez hozzárendelt érték felülírása. Ha a megadott kérelem fejléce nem létezik, majd ezt a szolgáltatást hozzáadhatja a kérelmet.
- Fejléc törlése a kérelemből.

Forrás kiszolgálóhoz továbbított kérések jelenik meg ez a szolgáltatás által végzett módosítások.

Az alábbi műveletek egyikét is elvégezhető a kérelem fejléce:

Beállítás|Leírás|Példa
-|-|-
Hozzáfűzés|A megadott érték hozzáadódik a meglévő kérelem fejléce érték végét.|**Kérelem fejlécének értéke (ügyfél):**<br/>Érték1<br/>**Kérelem fejlécének értéke (szabálymotorral):**<br/>Value2 <br/>**Új kérelem fejléc értéke:** <br/>Value1Value2
Felülírás|A kérelem fejlécében értéke lesz a megadott érték.|**Kérelem fejlécének értéke (ügyfél):**<br/>Érték1<br/>**Kérelem fejlécének értéke (szabálymotorral):**<br/>Value2<br/>**Új kérelem fejléc értéke:**<br/> Value2 <br/>
Törlés|Törli a megadott kérelem fejléce.|**Kérelem fejlécének értéke (ügyfél):**<br/>Érték1<br/>**Ügyfél kérelem fejléce konfigurációjának módosítása:**<br/>Törölje a szóban forgó kérelem fejléce.<br/>**Eredmény:**<br/>A megadott kérelem fejléce nem fogja továbbítani az eredeti kiszolgálóra.

Legfontosabb tudnivalókat:

- Győződjön meg arról, hogy a beállítás a megadott érték a kívánt kérés fejlécéhez pontos egyezést.
- Eset nem figyelembe fejléc azonosítása céljából. Például, a következő lehetőségek bármelyikét a `Cache-Control` fejlécnév segítségével azonosítható:
    - a cache-control
    - A CACHE-CONTROL
    - a cachE-Control
- Adjon meg egy fejlécet, használatakor csak alfanumerikus karaktereket, kötőjeleket és aláhúzást tartalmazhatnak.
- Fejléc törlése megakadályozza azt a POP-k által az eredeti kiszolgálóra történő továbbítását.
- A következő fejléceket fenntartott és a funkció által nem módosítható:
    - továbbított
    - gazdagép
    - -n keresztül
    - figyelmeztetés
    - x-továbbított-számára
    - "X-EK" kezdődő összes fejlécnevek vannak fenntartva.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-response-header"></a>Ügyfél válasz fejléce módosítsa
Minden válasz válaszfejlécek, amelyek bemutatják, hogy egy készletét tartalmazza. Ez a szolgáltatás közül választhat:

- Hozzáfűzés, vagy egy válaszfejléc rendelt érték felülírása. Ha a megadott válasz fejléce nem létezik, majd ezt a szolgáltatást hozzáadhatja a választ.
- Törölje a válasz fejléce a választ.

Alapértelmezés szerint válasz fejléce értékek meg vannak határozva a kapcsolódási pontokat és a forráskiszolgáló által.

Az alábbi műveletek egyikét a válaszfejléc hajtható végre:

Beállítás|Leírás|Példa
-|-|-
Hozzáfűzés|A megadott érték hozzáadódik a meglévő válasz fejléce érték végét.|**Válasz fejléc értéke (ügyfél):**<br />Érték1<br/>**Válasz fejléc értéke (szabálymotorral):**<br/>Value2<br/>**Új válasz fejléc értéke:**<br/>Value1Value2
Felülírás|A válasz fejléc értéke lesz a megadott érték.|**Válasz fejléc értéke (ügyfél):**<br/>Érték1<br/>**Válasz fejléc értéke (szabálymotorral):**<br/>Value2 <br/>**Új válasz fejléc értéke:**<br/>Value2 <br/>
Törlés|Törli a megadott válasz fejléce.|**Válasz fejléc értéke (ügyfél):**<br/>Érték1<br/>**Ügyfél válasz fejléce konfigurációjának módosítása:**<br/>Törölje a szóban forgó válaszfejléc.<br/>**Eredmény:**<br/>A megadott válasz fejléce nem lesznek továbbítva a kérelmezőnek.

Legfontosabb tudnivalókat:

- Győződjön meg arról, hogy a beállítás a megadott érték a kívánt válasz fejléce a pontos egyezést. 
- Eset nem figyelembe fejléc azonosítása céljából. Például, a következő lehetőségek bármelyikét a `Cache-Control` fejlécnév segítségével azonosítható:
    - a cache-control
    - A CACHE-CONTROL
    - a cachE-Control
- Fejléc törlése megakadályozza a kérelmező történő továbbítását.
- A következő fejléceket fenntartott és a funkció által nem módosítható:
    - fogadja el-kódolás
    - Kor
    - kapcsolat
    - tartalom kódolása
    - tartalom-hossza
    - tartalom-tartomány
    - dátum
    - kiszolgáló
    - bemutató
    - átviteli kódolás
    - frissítés
    - változnak
    - -n keresztül
    - figyelmeztetés
    - "X-EK" kezdődő összes fejlécnevek vannak fenntartva.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="partial-cache-sharing"></a>Részleges gyorsítótárának megosztása
**Cél:** határozza meg, hogy egy kérelem hozhat létre részben a gyorsítótárazott tartalmat.

A részleges gyorsítótár majd használhatók az adott tartalomhoz új kérések teljesítéséhez, mindaddig, amíg a rendszer teljes mértékben gyorsítótárazza a kért tartalmat.

Érték|Eredmény
-|-
Engedélyezve|Kérelmek hozhat létre részben a gyorsítótárazott tartalmat.
Letiltva|Kérelmek csak hozhat létre egy teljes körűen gyorsítótárazott verziója a kért tartalmat.

**Alapértelmezett viselkedés:** letiltva.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="prevalidate-cached-content"></a>Gyorsítótárazott tartalom prevalidate
**Cél:** határozza meg, hogy a gyorsítótárazott tartalmat lesz jogosult korai újraérvényesítésre vár a TTL lejárata előtt.

Adja meg az idő során, ami korai újraérvényesítésére jogosultak lesznek a lekért tartalom élettartam lejárta előtt.

Legfontosabb tudnivalókat:

- Az időegység megköveteli a gyorsítótárazott tartalom követően kerül sor újraérvényesítésre "Off" kiválasztásával Élettartama lejárt. Idő nem adható meg, és a rendszer figyelmen kívül hagyja.

**Alapértelmezett viselkedés:** ki. Újraérvényesítésre csak kerülhet sor, a gyorsítótárazott tartalom TTL lejárata után.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="proxy-special-headers"></a>Proxy speciális fejlécekkel
**Cél:** határozza meg, [Verizon-specifikus HTTP-kérelmek fejléceinek](cdn-verizon-http-headers.md) , hogy a rendszer továbbítja a POP-forráskiszolgáló.

Legfontosabb tudnivalókat:

- A forráskiszolgáló minden egyes CDN-specifikus kérelemfejlécből, ez a funkció meghatározott lesznek továbbítva. Kizárt fejléceket a rendszer nem továbbítja.
- Akadályozni, hogy a CDN-specifikus fejléc továbbítva, távolítsa el azt a fejléc lista mezőben szóközzel elválasztott lista.

Az alapértelmezett listát a következő HTTP-fejléceket tartalmazza:
- Ezzel
- X-továbbított-számára
- X továbbított protokollverzió
- X-gazdagép
- X-Midgress
- X-átjáró-List
- X-EK-neve
- Gazdagép

**Alapértelmezett viselkedés:** az összes CDN-specifikus kérelemfejlécek a rendszer az eredeti kiszolgálóra továbbítja.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="refresh-zero-byte-cache-files"></a>Nulla bájt méretű gyorsítótár fájlok frissítése
**Cél:** határozza meg, hogyan 0 bájtos gyorsítótár eszközök egy HTTP-alapú kérelem kre kezeli.

Érvényes értékek a következők:

Érték|Eredmény
--|--
Engedélyezve|Az eszköz refetch a forráskiszolgálóról a POP okoz.
Letiltva|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedést, hogy érvényes gyorsítótár eszközök kérésre kiszolgálására.
Ez a funkció a megfelelő gyorsítótárazási és a tartalomkézbesítési nem kötelező, de hasznos megoldás lehet. Például a dinamikus tartalom generátorok a szerverek véletlenül eredményezhet 0 bájtos válaszok küldött-kre történő propagálása. Az ilyen típusú válaszok a rendszer jellemzően gyorsítótárzza a POP-k által. Ha tudja, hogy, hogy a 0 bájtos válasz soha nem egy érvényes válasz 

az ilyen tartalmat majd ez a funkció tudja megakadályozni az ilyen típusú eszközök kiszolgált az ügyfelek számára.

**Alapértelmezett viselkedés:** letiltva.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-cacheable-status-codes"></a>Állítsa be a gyorsítótárazható állapotkódok
**Cél:** határozza meg, a gyorsítótárazott tartalmat is előfordulhat, hogy állapotkódok.

Alapértelmezés szerint gyorsítótárazás csak engedélyezve van a 200 OK válasz.

Adja meg a kívánt állapotkódok szóközzel elválasztott csoportját.

Legfontosabb tudnivalókat:

- Engedélyezze a forrás No-Cache figyelmen kívül hagyja a szolgáltatást. Ha ez a funkció nincs engedélyezve, majd 200 OK válasz előfordulhat, hogy nincsenek gyorsítótárazva.
- Ez a funkció érvényes állapotkódjai halmaza vannak: 203, 300, 301, 302, 305, a 307, 400-as, 401-es, 402, 403-as, 404-es, 405-ös, 406, 407-es, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500-as, 501, 502-es, 503-as, 504, és 505-ös.
- Ez a funkció nem használható egy 200 OK állapotkód eredményező válaszok gyorsítótárazásának letiltása.

**Alapértelmezett viselkedés:** gyorsítótárazás csak 200 OK állapotkódot eredményező válaszok esetében engedélyezve van.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-client-ip-custom-header"></a>Ügyfél IP-egyéni fejléc beállítása
**Cél:** ad hozzá egy egyéni fejlécet, amely azonosítja a kérés IP-címet a kérést küldő ügyfélnek.

A fejléc neve beállítás határozza meg az egyéni kérelemfejlécből, az ügyfél IP-cím tároló nevét.

Ez a funkció lehetővé teszi, hogy egy ügyfél egyéni fejléc keresztül címek ismerje meg, ügyfél IP-Címét a forráskiszolgáló. Ha a kérés gyorsítótár szolgáltatja, majd a forráskiszolgáló nem értesülnek az ügyfél IP-cím. Ezért ajánlott, hogy ez a szolgáltatás használható-e az eszközöket, amelyek nincsenek gyorsítótárazva.

Győződjön meg arról, hogy a megadott fejléc neve nem egyezik a következő neveket bármelyikét:

- A fejlécnevek standard kérelmet. Standard fejlécnevek listája található [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Fejléc fenntartott nevek:
    - továbbított számára
    - gazdagép
    - változnak
    - -n keresztül
    - figyelmeztetés
    - x-továbbított-számára
    - "X-EK" kezdődő összes fejlécnevek vannak fenntartva.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-content-delivery-on-error"></a>Hiba történt a régi Tartalomkézbesítési
**Cél:** határozza meg, hogy lejárt gyorsítótárazott tartalmat kézbesíti a rendszer hiba esetén gyorsítótár újbóli ellenőrzése során, vagy az ügyfél forráskiszolgálóról a lekért tartalom lekérésekor.

Érték|Eredmény
-|-
Engedélyezve|Elavult tartalomkézbesítés a kérelmezőnek egy-a forrás-kiszolgálóhoz való csatlakozás során hiba esetén.
Letiltva|A forráskiszolgáló hiba történt a kérelmező lesznek továbbítva.

**Alapértelmezett viselkedés:** letiltva

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-while-revalidate"></a>Elavult Revalidate közben
**Cél:** javítja a teljesítményt azáltal, hogy a POP-Okba annak elavult tartalom továbbítása a kérelmezőnek újraérvényesítésre végrehajtása közben.

Legfontosabb tudnivalókat:

- A szolgáltatás működését a kiválasztott időegység függően változik.
    - **Időszakonkénti egység:** adjon meg egy bizonyos hosszúságú időszeletet, és válassza ki, hogy az elavult tartalomkézbesítési időegységet (például másodperc, perc, óra, stb.). Az ilyen típusú telepítés lehetővé teszi, hogy a CDN-t meghosszabbodhat az az idő, előfordulhat, hogy nyújthatnak, amelyek tartalom érvényesítése a következő képlet alapján megkövetelése előtt: **TTL** + **elavult közben kísérelje meg újra érvényesítését idő** 
    - **OFF:** válassza "Kikapcsolva", elavult tartalom kiszolgálása az internetszolgáltatójuk megkövetelése előtt a kérelem újbóli ellenőrzése.
        - Ne adjon meg egy bizonyos hosszúságú időszeletet, mivel nem vonatkozik, és figyelmen kívül hagyja.

**Alapértelmezett viselkedés:** ki. Újraérvényesítésre szükséges, mielőtt a lekért tartalom kiszolgálható.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth"></a>Jogkivonat-hitelesítés
**Cél:** határozza meg, hogy Jogkivonatos hitelesítési kérelmet érvényesek.

Ha a jogkivonat-alapú hitelesítés engedélyezve van, csak a kérelmek, amelyek egy titkosított jogkivonatot, és a token által meghatározott követelményeknek lesz érvényes.

A token értékeinek titkosítására és visszafejtésére használt titkosítási kulcs az elsődleges kulcsot, és a biztonsági mentés kulcs beállításokat a hitelesítési Token oldalon határozza meg. Ne feledje, hogy a titkosítási kulcsok platformspecifikus.

**Alapértelmezett viselkedés:** letiltva.

Ez a funkció élvez a URL-újraíró funkció kivételével a legtöbb funkciót.

Érték | Eredmény
------|---------
Engedélyezve | A kért tartalmat a jogkivonat-alapú hitelesítés védi. Csak olyan ügyfelektől, amelyek meg egy érvényes jogkivonatot, és megfeleljenek a kérelmeket fogja figyelembe. FTP-tranzakciók jogkivonat-alapú hitelesítés nem tartoznak.
Letiltva| Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedést, hogy a jogkivonat-alapú hitelesítés konfiguráció meghatározásához, hogy egy kérés biztonságát engedélyezése.

#### <a name="compatibility"></a>Kompatibilitás
Ne használja a hitelesítési Token egy mindig az egyezési feltétellel rendelkező. 

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-denial-code"></a>Jogkivonat hitelesítési elutasítási kód
**Cél:** határozza meg a választ, amely egy felhasználónak vissza kell, amikor egy kérelem jogkivonat-alapú hitelesítés miatt megtagadva.

A rendelkezésre álló válaszkódot az alábbi táblázatban láthatók.

Válaszkód|Válasz neve|Leírás
-------------|-------------|--------
301|Véglegesen áthelyezése|Ezzel az állapotkóddal jogosulatlan felhasználók átirányítja a helyfejléc megadott URL-cím.
302|Sikeres keresés|Ezzel az állapotkóddal jogosulatlan felhasználók átirányítja a helyfejléc megadott URL-cím. Ezzel az állapotkóddal a az iparági szabványos módszer végez átirányítást.
307|Ideiglenes átirányítási|Ezzel az állapotkóddal jogosulatlan felhasználók átirányítja a helyfejléc megadott URL-cím.
401|Nem engedélyezett|Ezzel az állapotkóddal és a válasz WWW-Authenticate fejléc egyesítésével lehetővé teszi a felhasználótól a hitelesítéshez.
403|Tiltott|Ez az üzenet a standard 403 Tiltott állapotüzenet, amely egy jogosulatlan felhasználó megjelenik a védett tartalom elérésére tett kísérlet során.
404|Nem található a fájl|Ezzel az állapotkóddal azt jelzi, hogy a HTTP-ügyfél a kiszolgálóval való kommunikációra képes volt, de a kért tartalomhoz nem található.

#### <a name="compatibility"></a>Kompatibilitás
Ne használjon egy mindig az egyezési feltétellel rendelkező hitelesítés megtagadása Tokenkódot. Inkább a **egyéni elutasítási kezelése** című rész a **jogkivonat hitelesítési** lapján a **kezelés** portálon. További információkért lásd: [az eszközjogkivonattal történő hitelesítés biztonságossá tétele az Azure CDN eszközök](cdn-token-auth.md).

#### <a name="url-redirection"></a>URL-átirányítás

Ez a funkció támogatja az URL-cím átirányítása egy felhasználó által megadott URL-címet, ha erre van konfigurálva egy 3xx állapotkódot adja vissza. A felhasználó által megadott URL-cím adható meg a következő lépések végrehajtásával:

1. Válassza ki a hitelesítés elutasítva Tokenkódot szolgáltatásának 3xx válaszkódot.
2. Válassza ki a "Hely" közül a fejléc nem kötelező neve.
3. A Fejlécérték nem kötelező beállítás a kívánt URL-címre.

Ha egy URL-címe nincs definiálva a 3xx állapotkódot, majd 3xx állapotkódot szabványos válasz lapjának visszatér a felhasználó.

Átirányítási URL-cím csak akkor érvényes, a 3xx válaszkódot.

A nem kötelező fejléc értéke alfanumerikus karaktereket, idézőjelek között, és a tárolóhelyek támogatja.

#### <a name="authentication"></a>Hitelesítés

Ez a funkció támogatja a funkció tartalmazza a WWW-Authenticate fejléc, amikor válaszol azokra a jogkivonat-alapú hitelesítés által védett tartalom irányuló jogosulatlan kérelem. Ha a WWW-Authenticate fejléc a konfigurációban az "alapszintű" van beállítva, majd a jogosulatlan felhasználó bekéri fiók hitelesítő adatait.

A fenti konfigurációban érhető el a következő lépések végrehajtásával:

1. Válassza ki a "401-es" a hitelesítés elutasítva Tokenkódot szolgáltatásának a válaszkód szerint.
2. Válassza ki a "WWW-Authenticate" közül a fejléc nem kötelező neve.
3. A nem kötelező fejléc értéke a beállítást, az "alapszintű".

A WWW-Authenticate fejléc csak akkor érvényes, a 401-es válaszkódot.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-ignore-url-case"></a>Jogkivonat hitelesítési URL-cím egyezőek
**Cél:** meghatározza, hogy által Jogkivonatos hitelesítési URL-cím összehasonlítások kis-és nagybetűket.

Ez a funkció által érintett paraméterei a következők:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Érvényes értékek a következők:

Érték|Eredmény
---|----
Engedélyezve|Hatására a POP egyezőek URL-címek Jogkivonatos hitelesítési paraméterek összehasonlításakor.
Letiltva|Visszaállítja az alapértelmezett viselkedést. Alapértelmezés szerint olyan URL-cím összehasonlítások jogkivonat-hitelesítés lehet a kis-és nagybetűket.

**Alapértelmezett viselkedés:** letiltva.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-parameter"></a>Hitelesítési jogkivonat paraméterének
**Cél:** határozza meg, hogy a hitelesítési jogkivonat-alapú lekérdezési karakterlánc paramétereként kell nevezhető át.

Legfontosabb tudnivalókat:

- Az érték a beállítás határozza meg a lekérdezési karakterlánc paraméterének neve, amelyen keresztül egy jogkivonatot lehet megadni.
- Az érték a beállítás nem állítható be "ec_token."
- Ellenőrizze, hogy a meghatározott érték változatban neve csak érvényes URL-karaktereket.

Érték|Eredmény
----|----
Engedélyezve|Az érték a beállítás határozza meg a lekérdezési karakterlánc paraméterének neve, amelyen keresztül a jogkivonatok meg kell határozni.
Letiltva|A jogkivonat a kérelem URL-címében nem definiált lekérdezési karakterlánc paraméterként adhatók meg.

**Alapértelmezett viselkedés:** letiltva. A jogkivonat a kérelem URL-címében nem definiált lekérdezési karakterlánc paraméterként adhatók meg.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-redirect"></a>Átirányítási URL-címe
**Cél:** átirányítja a kéréseket a Location fejlécet keresztül.

Ez a szolgáltatás konfigurációját igényli beállítása a következő beállításokat:

Beállítás|Leírás
-|-
Kód|Válassza ki a válaszkód, amely a kérelmező ad vissza.
Forrás & minta| Ezek a beállítások megadása a kérelem URI mintát, amely a típusa, előfordulhat, hogy irányítható kérelmek. Egyetlen kérelem URL-cím megfelel a következő feltételek közül mind át lesz irányítva: <br/> <br/> **Forrás (vagy a tartalom-hozzáférési pont):** válassza ki a relatív elérési utat, amely azonosítja az eredeti kiszolgálóra. Ez az elérési út a _/XXXX/_ szakaszt, és a végpont neve. <br/><br/> **Forrás (minta):** olyan minta, amely azonosítja a kérések relatív elérési utat meg kell határozni. Ezt reguláris kifejezési mintája meg kell határoznia egy elérési út indítása közvetlenül után a korábban kiválasztott tartalom-hozzáférési pont (lásd fent). <br/> – Győződjön meg arról, hogy a kérelem URI-t (azaz forrás & minta) korábban megadott feltételek bármely ennek a funkciónak meghatározott egyezési feltételei nem ütközik. <br/> -Adjon meg egy minta; a minta egy üres értéket használja, ha az összes karakterláncok egyeztetését.
Cél| Adja meg az URL-címet, amelyre át lesz irányítva a fenti kérelmeket. <br/><br/> Dinamikusan hozhat létre az URL-cím használatával: <br/> -A reguláris kifejezési mintája <br/>- [HTTP-változók](cdn-http-variables.md) <br/><br/> Helyettesítse az értékeket, rögzítse a forrás a mintában az a cél-minta használatával $_n_ ahol _n_ értéket a sorrendet, amelyben rögzítésének azonosítja. Ha például 1 USD közben 2 USD a második értéket képvisel a forrás mintában a rögzített első értékét jelöli. <br/> 
Azt javasoljuk, használjon abszolút URL-CÍMNEK. Egy relatív URL-cím használatát előfordulhat, hogy a CDN URL-címek átirányítása elérési út érvénytelen.

**Mintaforgatókönyv**

Ebben a példában azt ismerteti, hogyan legyenek átirányítva az edge CNAME URL-cím, amelyet a CDN alap URL-cím: http:\//marketing.azureedge.net/brochures

Feltételeknek megfelelő kérelmek átirányítjuk az alap edge CNAME URL-cím: http:\//cdn.mydomain.com/resources

Ezen URL-cím átirányítása a következő konfigurációt keresztül lehet elérni: ![átirányítási URL-címe](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Alapvető szempontokat:**

- Az átirányítási URL-cím szolgáltatás határozza meg a kérelem URL-címeket, át lesz irányítva. Ennek eredményeképpen további egyezési feltételei nem szükségesek. Bár az egyezési feltétellel "Always" típusúként volt definiálva, csak az ügyfél "marketing" forrás "brosúrák" mappára mutasson kérelmek irányítja. 
- Minden egyező kérelmet a rendszer átirányítja az Edge-ben a cél lehetőség meghatározott CNAME URL-címe. 
    - Minta #1. forgatókönyv: 
        - Mintakérelem (a CDN URL): http:\//marketing.azureedge.net/brochures/widgets.pdf 
        - Kérelem URL-címe (után átirányítási): http:\//cdn.mydomain.com/resources/widgets.pdf  
    - Minta #2. forgatókönyv: 
        - Mintakérelem (Edge CNAME URL): http:\//marketing.mydomain.com/brochures/widgets.pdf 
        - Kérelem URL-címe (után átirányítási): http:\//cdn.mydomain.com/resources/widgets.pdf
    - Minta #3. forgatókönyv: 
        - Mintakérelem (Edge CNAME URL): http:\//brochures.mydomain.com/campaignA/final/productC.ppt 
        - Kérelem URL-címe (után átirányítási): http:\//cdn.mydomain.com/resources/campaignA/final/productC.ppt  
- A kérelem séma (% {scheme}) változó-t használja a cél beállítás, amely biztosítja, hogy a kérelem sémát az átirányítást követően változatlan marad.
- Az URL-szegmensek, amelyek a kérelem lettek rögzítve lesz hozzáfűzve az új URL-cím használatával: $1."

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-rewrite"></a>URL-átírás
**Cél:** átírja a kérelem URL-CÍMÉT.

Legfontosabb tudnivalókat:

- Ez a szolgáltatás konfigurációját igényli beállítása a következő beállításokat:

Beállítás|Leírás
-|-
 Forrás & minta | Ezek a beállítások megadása a kérelem URI mintát, amely a típusa, a kérelmek, előfordulhat, hogy be kell írni. Egyetlen kérelem URL-cím megfelel a következő feltételek közül mind felülíródik: <br/><br/>  - **Forrás (vagy a tartalom-hozzáférési pont):** válassza ki a relatív elérési utat, amely azonosítja az eredeti kiszolgálóra. Ez az elérési út a _/XXXX/_ szakaszt, és a végpont neve. <br/><br/> - **Forrás (minta):** olyan minta, amely azonosítja a kérések relatív elérési utat meg kell határozni. Ezt reguláris kifejezési mintája meg kell határoznia egy elérési út indítása közvetlenül után a korábban kiválasztott tartalom-hozzáférési pont (lásd fent). <br/> Győződjön meg arról, hogy a kérelem URI-t (azaz forrás & minta) korábban megadott feltételek nem ütközik az egyezés feltételek bármelyike meghatározott ennek a funkciónak. Adjon meg egy minta; a minta egy üres értéket használja, ha az összes karakterláncok egyeztetését. 
 Cél  |Adja meg a relatív URL-cím, amelyre a fenti kérelmek felülíródik szerint: <br/>    1. A tartalom-hozzáférési pont, amely azonosítja a forráskiszolgáló kijelölése. <br/>    2. Meghatározása egy relatív elérési út használatával: <br/>        -A reguláris kifejezési mintája <br/>        - [HTTP-változók](cdn-http-variables.md) <br/> <br/> Helyettesítse az értékeket, rögzítse a forrás a mintában az a cél-minta használatával $_n_ ahol _n_ értéket a sorrendet, amelyben rögzítésének azonosítja. Ha például 1 USD közben 2 USD a második értéket képvisel a forrás mintában a rögzített első értékét jelöli. 
 Ez a funkció lehetővé teszi, hogy a POP-Okba annak az URL-cím újraírása hagyományos átirányítási végrehajtása nélkül. Azt jelenti a kérelmező kap válasz ugyanazt a kódot, mintha kért kellett az Átdolgozta URL-cím.

**1. példa forgatókönyv**

Ez a példa bemutatja, hogyan legyenek átirányítva az edge CNAME URL-cím, amelyet a CDN alap URL-cím: http:\//marketing.azureedge.net/brochures/

Feltételeknek megfelelő kérelmek átirányítjuk az alap edge CNAME URL-cím: http:\//MyOrigin.azureedge.net/resources/

Ezen URL-cím átirányítása a következő konfigurációt keresztül lehet elérni: ![átirányítási URL-címe](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Mintaforgatókönyv 2**

Ez a példa bemutatja, hogyan legyenek átirányítva az edge kisbetűssé reguláris kifejezésekkel nagybetűs CNAME URL-CÍMÉT.

Ezen URL-cím átirányítása a következő konfigurációt keresztül lehet elérni: ![átirányítási URL-címe](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)


**Alapvető szempontokat:**

- Az URL-újraíró szolgáltatás határozza meg a kérelem URL-címeket, az felülíródik. Ennek eredményeképpen további egyezési feltételei nem szükségesek. Bár az egyezési feltétellel "Always" típusúként volt definiálva, csak az ügyfél "marketing" forrás "brosúrák" mappára mutasson kérelmek felülíródik.

- Az URL-szegmensek, amelyek a kérelem lettek rögzítve lesz hozzáfűzve az új URL-cím használatával: $1."

#### <a name="compatibility"></a>Kompatibilitás
Ez a funkció tartalmazza a megfelelő feltételek, amelyeknek teljesülniük kell ahhoz a kérésekre alkalmazható. Annak érdekében, hogy ütköző egyezési feltételek beállítása megelőzése érdekében ez a funkció nem kompatibilis a a következő egyezési feltételei:

- AS-szám
- CDN-forrás
- Ügyfél IP-címe
- Ügyfél kezdőpontja
- Kérelem séma
- URL-cím elérési út könyvtár
- URL-cím elérési út bővítmény
- URL-cím elérési_út fájlnév
- URL-cím elérési út szövegkonstans
- URL-cím elérési út reguláris kifejezés
- URL-cím elérési út helyettesítő karakter
- URL-cím lekérdezési szövegkonstans
- URL-cím lekérdezési paraméter
- URL-cím lekérdezési reguláris kifejezés
- URL-cím lekérdezési helyettesítő karakter

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

---
### <a name="user-variable"></a>Felhasználói változó
**Cél:** csak belső használatra.

[Vissza a tetejére](#azure-cdn-rules-engine-features)

</br>

## <a name="next-steps"></a>További lépések
* [Szabálymotor-referencia](cdn-rules-engine-reference.md)
* [Szabálymotor feltételes kifejezései](cdn-rules-engine-reference-conditional-expressions.md)
* [Szabálymotor egyezési feltételei](cdn-rules-engine-reference-match-conditions.md)
* [A rules engine használatával HTTP a működés felülbírálása](cdn-rules-engine.md)
* [Az Azure CDN áttekintése](cdn-overview.md)
