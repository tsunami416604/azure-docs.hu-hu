---
title: Azure CDN a Verizon Premium Rules Engine funkcióival | Microsoft Docs
description: A Verizon Premium Rules Engine funkcióinak Azure CDN dokumentációja.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 9177ac544c83305ae95ad681d3dc9f84ac64ea36
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381824"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN a Verizon Premium Rules Engine funkcióival

Ez a cikk az Azure Content Delivery Network (CDN) [szabályok motorjának](cdn-verizon-premium-rules-engine.md)elérhető szolgáltatásainak részletes leírását tartalmazza.

A szabály harmadik része a szolgáltatás. A szolgáltatás határozza meg, hogy milyen típusú műveletet alkalmaz a rendszer az egyeztetési feltételek alapján azonosított kérelem típusára.

## <a name="access-features"></a>Hozzáférési funkciók

Ezek a funkciók a tartalmakhoz való hozzáférés szabályozására szolgálnak.

Name (Név) | Cél
-----|--------
[Hozzáférés megtagadása (403)](#deny-access-403) | Meghatározza, hogy az összes kérés visszautasítva van-e egy 403 Tiltott válasz esetén.
[Jogkivonat-hitelesítés](#token-auth) | Meghatározza, hogy a rendszer a jogkivonat-alapú hitelesítést alkalmazza-e a kérelemre.
[Jogkivonat-hitelesítési elutasítási kód](#token-auth-denial-code) | Meghatározza, hogy a rendszer milyen típusú választ ad vissza a felhasználónak, ha jogkivonat-alapú hitelesítés miatt megtagadja a kérelmet.
[Jogkivonat-hitelesítés figyelmen kívül hagyása URL-eset](#token-auth-ignore-url-case) | Meghatározza, hogy a jogkivonat-alapú hitelesítés által végzett URL-összehasonlítás megkülönbözteti-e a kis-és nagybetűket.
[Jogkivonat-hitelesítési paraméter](#token-auth-parameter) | Meghatározza, hogy a jogkivonat-alapú hitelesítés lekérdezési karakterláncának paraméterét át kell-e átnevezni.

## <a name="caching-features"></a>Gyorsítótárazási funkciók

Ezek a funkciók úgy vannak kialakítva, hogy testre szabják a tartalom gyorsítótárazásának idejét és módját.

Name (Név) | Cél
-----|--------
[Sávszélesség-paraméterek](#bandwidth-parameters) | Meghatározza, hogy aktív-e a sávszélesség-szabályozási paraméterek (például ec_rate és ec_prebuf).
[Sávszélesség-szabályozás](#bandwidth-throttling) | A sávszélesség szabályozása a jelenléti pont (POP) által biztosított válasz számára.
[Gyorsítótár megkerülése](#bypass-cache) | Meghatározza, hogy a kérés kikerülje-e a gyorsítótárazást.
[Cache-Control fejléc kezelése](#cache-control-header-treatment) | `Cache-Control` fejlécek létrehozásának szabályozása a POP-ban, ha a külső Max-Age funkció aktív.
[Gyorsítótár – kulcs lekérdezési karakterlánca](#cache-key-query-string) | Meghatározza, hogy a gyorsítótár-kulcs tartalmazza-e a kérelemhez társított lekérdezési karakterlánc paramétereit.
[Gyorsítótár – kulcs újraírása](#cache-key-rewrite) | A kérelemhez társított gyorsítótár-kulcs újraírása.
[Gyorsítótár kitöltésének befejezése](#complete-cache-fill) | Meghatározza, hogy mi történjen, ha egy kérés részleges gyorsítótárat eredményez egy POP-on.
[Fájltípusok tömörítése](#compress-file-types) | A kiszolgálón tömörített fájlok fájlformátumait határozza meg.
[Alapértelmezett belső max. Age](#default-internal-max-age) | Meghatározza a POP-hoz a forrásként szolgáló kiszolgáló gyorsítótárának újraérvényesítésére vonatkozó alapértelmezett maximális életkori időközt.
[A fejléc kezelése lejár](#expires-header-treatment) | Azt szabályozza, hogy a külső Max-Age funkció aktív-e a `Expires` fejlécek előállításához a POP használatával.
[Külső Max-Age](#external-max-age) | Meghatározza a böngészőnek a POP-gyorsítótár újraérvényesítésére vonatkozó maximális élettartamának intervallumát.
[A belső Max-Age kényszerítése](#force-internal-max-age) | Meghatározza a POP-a forrásként szolgáló kiszolgáló gyorsítótár-újraérvényesítésének maximális életkori intervallumát.
[H. 264 támogatás (HTTP Progressive letöltés)](#h264-support-http-progressive-download) | Meghatározza a tartalom továbbítására használható H. 264 fájlformátumok típusait.
[A no-cache-kérelem tiszteletben tartása](#honor-no-cache-request) | Meghatározza, hogy a rendszer továbbítsa-e a HTTP-ügyfél nem gyorsítótárazott kérelmeit a forráskiszolgáló felé.
[Kihagyott forrás – gyorsítótár](#ignore-origin-no-cache) | Meghatározza, hogy a CDN figyelmen kívül hagyja-e a forráskiszolgáló által kiszolgált bizonyos irányelveket.
[Unsatisfiable tartományok figyelmen kívül hagyása](#ignore-unsatisfiable-ranges) | Meghatározza az ügyfeleknek visszaadott választ, amikor egy kérelem létrehoz egy 416 kért tartományt, amely nem teljesíthető.
[Belső maximális – elavult](#internal-max-stale) | Azt határozza meg, hogy mennyi ideig tart a lejárati idő, amikor egy gyorsítótárazott eszköz kiszolgálható egy POP-ból, ha a POP nem tudja újraérvényesíteni a gyorsítótárazott eszközt a forrás-kiszolgálóval.
[Részleges gyorsítótár megosztása](#partial-cache-sharing) | Meghatározza, hogy egy kérelem képes-e részlegesen gyorsítótárazott tartalom előállítására.
[Gyorsítótárazott tartalom előérvényesítése](#prevalidate-cached-content) | Meghatározza, hogy a gyorsítótárazott tartalom jogosult-e a korai újraérvényesítésre, mielőtt lejár az ÉLETTARTAMa.
[Nulla bájtos gyorsítótár fájljainak frissítése](#refresh-zero-byte-cache-files) | Meghatározza, hogy a rendszer hogyan kezeli a HTTP-ügyfél egy 0 bájtos gyorsítótárazási eszközre vonatkozó kérelmét.
[Gyorsítótárazható állapotkódok beállítása](#set-cacheable-status-codes) | Meghatározza a gyorsítótárazott tartalmat okozó állapotkódok készletét.
[Elavult tartalom kézbesítése hiba esetén](#stale-content-delivery-on-error) | Meghatározza, hogy a rendszer a lejárt gyorsítótárazott tartalmat akkor adja-e meg, ha hiba történik a gyorsítótár újraérvényesítése során vagy a kért tartalom ügyfél-kiszolgálóról való beolvasásakor.
[Elavult az újraellenőrzés során](#stale-while-revalidate) | Javítja a teljesítményt azáltal, hogy lehetővé teszi a POP-nak az elavult ügyfél kiszolgálását a kérelmezőnek az újraérvényesítés során.

## <a name="comment-feature"></a>Megjegyzés funkció

Ez a szolgáltatás úgy lett kialakítva, hogy a szabályon belül további információkat szolgáltasson.

Name (Név) | Cél
-----|--------
[Megjegyzés](#comment) | Lehetővé teszi egy Megjegyzés hozzáadását egy szabályon belül.

## <a name="header-features"></a>Fejléc-funkciók

Ezek a funkciók a kérelemből vagy válaszból származó fejlécek hozzáadására, módosítására és törlésére szolgálnak.

Name (Név) | Cél
-----|--------
[Életkor válaszának fejléce](#age-response-header) | Meghatározza, hogy a rendszer a kérelmezőnek küldött válaszban tartalmazza-e az életkor válaszának fejlécét.
[Hibakeresési gyorsítótár válaszának fejlécei](#debug-cache-response-headers) | Meghatározza, hogy a válasz tartalmazhatja-e az X-EC-debug válasz fejlécét, amely információt nyújt a kért eszközhöz tartozó gyorsítótár-házirendről.
[Ügyfél-kérelem fejlécének módosítása](#modify-client-request-header) | Felülírja, hozzáfűzi vagy törli egy fejlécet egy kérelemből.
[Ügyfél-válasz fejlécének módosítása](#modify-client-response-header) | Egy válasz fejlécének felülírása, hozzáfűzése vagy törlése.
[Ügyfél IP-címének egyéni fejlécének beállítása](#set-client-ip-custom-header) | Lehetővé teszi, hogy a kérelmező ügyfél IP-címe a kérelemhez egyéni kérelem fejlécként legyen hozzáadva.

## <a name="logging-features"></a>Naplózási funkciók

Ezek a funkciók úgy vannak kialakítva, hogy testre szabják a nyers naplófájlokban tárolt adatfájlokat.

Name (Név) | Cél
-----|--------
[Egyéni napló mezője 1](#custom-log-field-1) | Meghatározza a nyers naplófájl egyéni napló mezőjéhez hozzárendelt formátumot és tartalmat.
[Napló lekérdezési karakterlánca](#log-query-string) | Meghatározza, hogy a rendszer a lekérdezési karakterláncot a hozzáférési naplók URL-címével együtt tárolja-e.


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

## <a name="origin-features"></a>Forrás jellemzői

Ezek a funkciók úgy vannak kialakítva, hogy a CDN hogyan kommunikáljon a forrás-kiszolgálóval.

Name (Név) | Cél
-----|--------
[Életben tartási kérelmek maximális száma](#maximum-keep-alive-requests) | A Keep-Alive kapcsolatra vonatkozó kérelmek maximális számát határozza meg a bezárás előtt.
[Proxy speciális fejlécei](#proxy-special-headers) | Meghatározza azon CDN-specifikus kérelmek fejléceit, amelyek a POP-ból a forrás-kiszolgálóra továbbítódnak.

## <a name="specialty-features"></a>Speciális funkciók

Ezek a funkciók speciális funkciókat biztosítanak a speciális felhasználók számára.

Name (Név) | Cél
-----|--------
[Gyorsítótárazható HTTP-metódusok](#cacheable-http-methods) | Meghatározza a hálózaton gyorsítótárazható további HTTP-metódusok készletét.
[Gyorsítótárazható kérelem törzsének mérete](#cacheable-request-body-size) | Meghatározza azt a küszöbértéket, amely meghatározza, hogy a POST válasz gyorsítótárazható-e.
[Felhasználói változó](#user-variable) | Kizárólag belső használatra

## <a name="url-features"></a>URL-funkciók

Ezek a funkciók lehetővé teszik a kérés átirányítását vagy átírását egy másik URL-címre.

Name (Név) | Cél
-----|--------
[Átirányítások követése](#follow-redirects) | Meghatározza, hogy a kérések átirányíthatók-e az ügyfél-kiszolgáló által visszaadott Location fejlécben megadott állomásnévre.
[URL-átirányítás](#url-redirect) | Átirányítja a kéréseket a Location fejléc használatával.
[URL-újraírás](#url-rewrite)  | A kérelem URL-címének újraírása.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a>Azure CDN a Verizon Premium Rules Engine funkcióinak ismertetése

---

### <a name="age-response-header"></a>Életkor válaszának fejléce

**Cél**: azt határozza meg, hogy a rendszer a kérelmezőnek küldött válaszban tartalmazza-e az életkor válaszának fejlécét.

Érték|Eredmény
--|--
Engedélyezve | Az életkor-válasz fejléce a kérelmezőnek küldött válaszban szerepel.
Letiltva | Az életkor válaszának fejléce ki van zárva a kérelmezőnek küldött válaszból.

**Alapértelmezett viselkedés**: letiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-parameters"></a>Sávszélesség-paraméterek

**Cél:** Meghatározza, hogy aktív-e a sávszélesség-szabályozási paraméterek (például ec_rate és ec_prebuf).

A sávszélesség-szabályozási paraméterek határozzák meg, hogy az ügyfél kérelmének adatátviteli sebessége az egyéni díjszabásra korlátozódik-e.

Érték|Eredmény
--|--
Engedélyezve|Lehetővé teszi a kiugró sávszélesség-szabályozási kérelmek betartását.
Letiltva|Hatására a rendszer figyelmen kívül hagyja a sávszélesség-szabályozási paramétereket. A kért tartalom a szokásos módon szolgál (azaz a sávszélesség szabályozása nélkül).

**Alapértelmezett viselkedés:** Engedélyezve.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bandwidth-throttling"></a>Sávszélesség-szabályozás

**Cél:** Szabályozza a kapcsolódási pont által megadott válasz sávszélességét.

A sávszélesség-szabályozás megfelelő beállításához mindkét alábbi beállítást meg kell határozni.

Beállítás|Leírás
--|--
Kilobájt/másodperc|Ezt a beállítást a válasz továbbítására használható maximális sávszélesség (kb/s) értékre állíthatja.
Prebuf másodpercben|Ezzel a beállítással megadhatja, hogy a rendszer hány másodpercig várjon, amíg a sávszélesség le nem szabályozva lesz. A nem korlátozott sávszélesség ezen időszakának célja annak megakadályozása, hogy a Media Player a sávszélesség szabályozása miatt akadozó vagy pufferelési problémákat tapasztaljon.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="bypass-cache"></a>Gyorsítótár megkerülése

**Cél:** Meghatározza, hogy a kérés kikerülje-e a gyorsítótárazást.

Érték|Eredmény
--|--
Engedélyezve|Azt eredményezi, hogy az összes kérelem átesni fog a forráskiszolgálón, még akkor is, ha a tartalom korábban a pop-on lett gyorsítótárazva.
Letiltva|A a válasz fejlécében meghatározott gyorsítótárazási házirendnek megfelelően a POP-ok gyorsítótárazását okozza.

**Alapértelmezett viselkedés:**

- **Http nagyméretű:** Tiltva

<!---
- **ADN:** Enabled

--->

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-http-methods"></a>Gyorsítótárazható HTTP-metódusok

**Cél:** Meghatározza a hálózaton gyorsítótárazható további HTTP-metódusok készletét.

Legfontosabb információk:

- Ez a funkció azt feltételezi, hogy a GET válaszok mindig gyorsítótárazva lesznek. Ennek eredményeképpen a beolvasás HTTP-metódust nem szabad belefoglalni a funkció beállításakor.
- Ez a funkció csak a HTTP POST metódust támogatja. A válasz utáni gyorsítótárazás engedélyezése a szolgáltatás `POST`re való beállításával.
- Alapértelmezés szerint csak azok a kérelmek vannak gyorsítótárazva, amelyeknek a törzse 14 KB-nál kisebb. A kérések maximális méretének beállításához használja a gyorsítótárazható kérelem törzse funkciót.

**Alapértelmezett viselkedés:** Csak a GET válaszok vannak gyorsítótárazva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cacheable-request-body-size"></a>Gyorsítótárazható kérelem törzsének mérete

**Cél:** Meghatározza azt a küszöbértéket, amely meghatározza, hogy a POST válasz gyorsítótárazható-e.

Ezt a küszöbértéket a kérelem törzsének maximális méretének megadásával határozzák meg. A nagyobb kérelem törzsét tartalmazó kérelmeket nem gyorsítótárazza a rendszer.

Legfontosabb információk:

- Ez a funkció csak akkor alkalmazható, ha a POST válasz jogosult a gyorsítótárazásra. A gyorsítótárazható HTTP-metódusok szolgáltatással engedélyezheti a POST kérések gyorsítótárazását.
- A kérelem törzsét a következő szempontok alapján kell figyelembe venni:
    - x-www-Form-urlencoded értékek
    - Egyedi gyorsítótár-kulcs biztosítása
- A nagy méretű kérések törzsének meghatározása hatással lehet az adatkézbesítési teljesítményre.
    - **Javasolt érték:** 14 kb
    - **Minimális érték:** 1 kb

**Alapértelmezett viselkedés:** 14 kb

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-control-header-treatment"></a>Cache-Control fejléc kezelése

**Cél:** `Cache-Control` fejlécek létrehozásának szabályozása a POP-ban, ha a külső Max-Age funkció aktív.

Az ilyen típusú konfiguráció elérésének legegyszerűbb módja a külső Max-Age és a Cache-Control fejléc kezelési funkcióinak elhelyezése ugyanabban az utasításban.

Érték|Eredmény
--|--
Írja felül|Biztosítja a következő műveletek elvégzését:<br/> – Felülírja a forráskiszolgáló által generált `Cache-Control` fejlécet. <br/>– Hozzáadja a külső Max-Age funkció által előállított `Cache-Control` fejlécet a válaszhoz.
Továbbítás|Biztosítja, hogy a külső Max-Age szolgáltatás által létrehozott `Cache-Control` fejléc soha ne legyen hozzáadva a válaszhoz. <br/> Ha a forráskiszolgáló `Cache-Control` fejlécet hoz létre, a rendszer átadja a végfelhasználónak. <br/> Ha a forráskiszolgáló nem hoz létre `Cache-Control` fejlécet, akkor ez a beállítás azt eredményezheti, hogy a válasz fejléce nem tartalmaz `Cache-Control`-fejlécet.
Hozzáadás, ha hiányzik|Ha a forráskiszolgáló `Cache-Control` fejléce nem érkezett meg, akkor ez a beállítás hozzáadja a külső Max-Age szolgáltatás által létrehozott `Cache-Control`-fejlécet. Ez a beállítás akkor hasznos, ha biztosítani szeretné, hogy minden eszközhöz hozzá legyen rendelve egy `Cache-Control` fejléc.
Eltávolítás| Ezzel a beállítással biztosíthatja, hogy a fejléc válasza ne tartalmazza a `Cache-Control` fejlécét. Ha már hozzá van rendelve egy `Cache-Control` fejléc, a rendszer eltávolítja a fejléc válaszáról.

**Alapértelmezett viselkedés:** Felülírja.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-query-string"></a>Gyorsítótár – kulcs lekérdezési karakterlánca

**Cél:** Meghatározza, hogy a gyorsítótár-kulcs tartalmazza-e a kérelemhez társított lekérdezési karakterlánc paramétereit.

Legfontosabb információk:

- Adjon meg egy vagy több lekérdezési karakterlánc-paraméter nevét, és válassza el az egyes paraméterek nevét egyetlen szóközzel.
- Ez a szolgáltatás határozza meg, hogy a lekérdezési karakterlánc paraméterei szerepelnek-e a gyorsítótár-kulcsban. Az alábbi táblázatban található minden egyes lehetőséghez további információk is megtalálhatók.

Típus|Leírás
--|--
 Tartalmazza|  Azt jelzi, hogy minden megadott paraméternek szerepelnie kell a gyorsítótár-kulcsban. A rendszer egyedi gyorsítótár-kulcsot hoz létre minden olyan kérelemhez, amely egyedi értéket tartalmaz a funkcióban definiált lekérdezési karakterlánc-paraméterhez.
 Összes belefoglalása  |Azt jelzi, hogy minden kérelemhez egyedi gyorsítótár-kulcsot hoz létre egy olyan objektumhoz, amely egyedi lekérdezési karakterláncot tartalmaz. Ez a típusú konfiguráció általában nem ajánlott, mert a gyorsítótár-találatok kis hányadát okozhatják. A gyorsítótárbeli találatok alacsony száma növeli a forráskiszolgáló terhelését, mert több kérést kell kiszolgálnia. Ez a konfiguráció a lekérdezési karakterlánc gyorsítótárazása lapon a "Unique-cache" néven ismert gyorsítótárazási viselkedést duplikálja.
 Exclude | Azt jelzi, hogy csak a megadott paraméter (ek) ki van zárva a gyorsítótár-kulcsból. Az összes többi lekérdezési karakterlánc paraméter a gyorsítótár-kulcs részét képezi.
 Az összes kizárása  |Azt jelzi, hogy az összes lekérdezési karakterlánc paraméter ki van zárva a gyorsítótár-kulcsból. Ez a konfiguráció a "standard-cache" alapértelmezett gyorsítótárazási viselkedését ismétli meg a lekérdezés-karakterlánc gyorsítótárazási oldalon.  

A szabályok motor segítségével testre szabhatja a lekérdezési karakterlánc gyorsítótárazásának megvalósítási módját. Megadhatja például, hogy a lekérdezési karakterlánc gyorsítótárazása csak bizonyos helyszíneken vagy fájltípusokon történjen.

Ha a "no-cache" lekérdezési karakterlánc gyorsítótárazási viselkedését szeretné duplikálni a lekérdezési karakterlánc gyorsítótárazása lapon, hozzon létre egy szabályt, amely tartalmaz egy URL-lekérdezési helyettesítő karaktert, valamint egy megkerülési gyorsítótár-funkciót. Állítsa be az URL-lekérdezés helyettesítő karakterének megfeleltetése feltételt egy csillag (*) értékre.

>[!IMPORTANT]
> Ha a jogkivonat-hitelesítés engedélyezve van a fiók bármely elérési útjához, a szabványos gyorsítótárazási mód az egyetlen mód, amely a lekérdezési karakterláncok gyorsítótárazásához használható. További információkért lásd: [Az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési sztringekkel](cdn-query-string-premium.md).

#### <a name="sample-scenarios"></a>Példák a forgatókönyvekre

A szolgáltatás következő példája egy minta kérést és az alapértelmezett gyorsítótár-kulcsot tartalmaz:

- **Példa a kérelemre:** http://wpc.0001.&lt;D omain&gt;/800001/Origin/Folder/Asset.htm? munkamenet = 1234 & language = EN & userid = 01
- **Alapértelmezett gyorsítótár – kulcs:** /800001/Origin/Folder/Asset.htm

##### <a name="include"></a>Tartalmazza

Példa konfiguráció:

- **Írja be a következőt:** Tartalmazza
- **Paraméter (ek):** nyelv

Ez a típusú konfiguráció a következő lekérdezési karakterlánc paraméterének gyorsítótár-kulcsát állítja elő:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Összes belefoglalása

Példa konfiguráció:

- **Írja be a következőt:** Összes belefoglalása

Ez a típusú konfiguráció a következő lekérdezési karakterlánc paraméterének gyorsítótár-kulcsát állítja elő:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Exclude

Példa konfiguráció:

- **Írja be a következőt:** Kizárása
- **Paraméter (ek):** munkamenetben lévő felhasználóazonosító

Ez a típusú konfiguráció a következő lekérdezési karakterlánc paraméterének gyorsítótár-kulcsát állítja elő:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Az összes kizárása

Példa konfiguráció:

- **Írja be a következőt:** Az összes kizárása

Ez a típusú konfiguráció a következő lekérdezési karakterlánc paraméterének gyorsítótár-kulcsát állítja elő:

    /800001/Origin/folder/asset.htm

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="cache-key-rewrite"></a>Gyorsítótár – kulcs újraírása

**Cél:** A kérelemhez társított gyorsítótár-kulcs újraírása.

A cache-Key a relatív elérési út, amely azonosítja az eszközt a gyorsítótárazás céljára. Más szóval a kiszolgálók a gyorsítótár-kulcs által meghatározott elérési útnak megfelelően keresik meg egy eszköz gyorsítótárazott verzióját.

Konfigurálja a funkciót úgy, hogy meghatározza a következő lehetőségek egyikét:

Beállítás|Leírás
--|--
Eredeti elérési út| Adja meg azon kérelmek típusának relatív elérési útját, amelyek gyorsítótárazási kulcsát újra kell írni. Relatív elérési út lehet definiálni egy alapforrás elérési útjának kiválasztásával, majd egy reguláris kifejezési minta definiálásával.
Új elérési út|Adja meg az új gyorsítótár-kulcs relatív elérési útját. Relatív elérési út lehet definiálni egy alapforrás elérési útjának kiválasztásával, majd egy reguláris kifejezési minta definiálásával. Ez a relatív elérési út dinamikusan felépíthető a [http-változók](cdn-http-variables.md)használatával.

**Alapértelmezett viselkedés:** A kérések gyorsítótár-kulcsát a kérelem URI-ja határozza meg.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="comment"></a>Megjegyzés

**Cél:** Lehetővé teszi egy Megjegyzés hozzáadását egy szabályon belül.

Ennek a funkciónak az egyik funkciója, hogy további információkat szolgáltasson egy szabály általános céljáról, vagy hogy miért lett hozzáadva egy adott egyeztetési feltétel vagy szolgáltatás a szabályhoz.

Legfontosabb információk:

- Legfeljebb 150 karakter adható meg.
- Csak alfanumerikus karaktereket használjon.
- Ez a funkció nem befolyásolja a szabály viselkedését. Pusztán egy olyan térséget kell megadnia, ahol információkat adhat meg a jövőbeli referenciához, vagy amelyek segíthetnek a szabály hibáinak elhárításában.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="complete-cache-fill"></a>Gyorsítótár kitöltésének befejezése

**Cél:** Meghatározza, hogy mi történjen, ha egy kérés részleges gyorsítótárat eredményez egy POP-on.

A részleges gyorsítótár-kihagyás egy olyan eszköz gyorsítótár-állapotát ismerteti, amely nem lett teljesen letöltve a POP-ra. Ha egy adott eszköz csak részben van gyorsítótárazva egy POP-on, akkor a következő kérelmet a rendszer ismét továbbítja a forráskiszolgálón.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
A részleges gyorsítótár-kihagyás általában akkor következik be, amikor a felhasználó megszakítja a letöltést vagy a kizárólag HTTP-tartományra vonatkozó kérelmeket használó eszközökön. Ez a funkció olyan nagyméretű eszközök esetében hasznos, amelyek általában nem tölthetők le az elejétől a végéig (például videók). Ennek eredményeképpen ez a funkció alapértelmezés szerint engedélyezve van a HTTP nagyméretű platformon. Minden más platformon le van tiltva.

Tartsa meg az alapértelmezett konfigurációt a HTTP nagyméretű platformon, mert csökkenti az ügyfél-forráskiszolgáló terhelését, és növeli a sebességet, amellyel az ügyfelek letöltik a tartalmat.

Érték|Eredmény
--|--
Engedélyezve|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedés az, hogy kényszerítse a POP-ot, hogy kezdeményezzen háttérbeli beolvasást az eszközről a forrás-kiszolgálóról. Ezt követően az eszköz a POP helyi gyorsítótárában lesz.
Letiltva|Megakadályozza, hogy a POP-ból háttér-lekérést végezzenek az eszközön. Ennek az az oka, hogy az adott régióhoz tartozó következő kérelem egy POP-t kér az ügyfél-kiszolgálótól.

**Alapértelmezett viselkedés:** Engedélyezve.

#### <a name="compatibility"></a>Kompatibilitás

A gyorsítótár-beállítások nyomon követésének módja miatt ez a funkció nem rendelhető hozzá a következő egyeztetési feltételekhez:

- AS szám
- Ügyfél IP-címe
- Cookie paraméter
- Cookie-paraméterek Regexje
- Ország
- Eszköz
- Microsoft Edge CNAME
- Hivatkozó tartomány
- Kérelem fejléce – literál
- Kérelem fejlécének Regexje
- Kérelem fejlécének helyettesítő karaktere
- Kérési módszer
- Kérési séma
- URL-lekérdezési literál
- URL-lekérdezés regex
- URL-lekérdezés helyettesítő karaktere
- URL-lekérdezési paraméter

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="compress-file-types"></a>Fájltípusok tömörítése

**Cél:** A kiszolgálón tömörített fájlok fájlformátumait határozza meg.

A fájlformátum megadható a saját internetes adathordozó-típusa (például Content-Type) használatával. Az internetes adathordozó típusa platformtól független metaadatokat tartalmaz, amelyek lehetővé teszik, hogy a kiszolgálók azonosítsák egy adott eszköz fájlformátumát. Az alábbi lista tartalmazza a gyakori internetes adathordozók típusait.

Internetes adathordozó típusa|Leírás
--|--
szöveg/egyszerű|Egyszerű szövegfájlok
szöveg/html| HTML-fájlok
szöveg/CSS|Egymásra épülő stíluslapok (CSS)
application/x-javascript|Javascript
alkalmazás/JavaScript|Javascript

Legfontosabb információk:

- Több internetes adathordozó-típust is megadhat, ha mindegyiket egyetlen szóközzel kell elválasztani.
- Ez a funkció csak az 1 MB-nál kisebb méretű eszközöket tömöríti. A kiszolgálók nem tömörítik a nagyobb erőforrásokat.
- Bizonyos típusú tartalmak, például képek, videók és hanganyagok (például JPG, MP3, MP4 stb.) már tömörítve vannak. Mivel az ilyen típusú eszközök további tömörítése nem csökkenti jelentősen a fájlméretet, nem ajánlott engedélyezni a tömörítést.
- A helyettesítő karakterek (például a csillagok) nem támogatottak.
- Mielőtt hozzáadja ezt a szolgáltatást egy szabályhoz, győződjön meg arról, hogy a tömörítési letiltott beállítás be van állítva azon platform tömörítési lapján, amelyre ez a szabály vonatkozik.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="custom-log-field-1"></a>Egyéni napló mezője 1

**Cél:** Meghatározza a nyers naplófájl egyéni napló mezőjéhez hozzárendelni kívánt formátumot és tartalmat.

Ez az egyéni mező lehetővé teszi annak meghatározását, hogy a rendszer mely kérelmek és válaszok fejléceit tárolja a naplófájlokban.

Alapértelmezés szerint az egyéni napló mező neve "x-ec_custom-1". A mező neve testreszabható a nyers napló beállításai lapról.

A kérelem és válasz fejlécek megadásának formátuma az alábbiak szerint van meghatározva:

Fejléc típusa|Formátum|Példák
-|-|-
Kérelem fejléce|`%{[RequestHeader]()}[i]()` | %{Accept-Encoding}i <br/> {Referrer} i <br/> %{Authorization}i
Válasz fejléce|`%{[ResponseHeader]()}[o]()`| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

Legfontosabb információk:

- Az egyéni napló mező a fejléc mezőinek és egyszerű szövegének tetszőleges kombinációját tartalmazhatja.
- A mezőhöz érvényes karakterek a következők: alfanumerikus (0-9, a-z, és A-Z), kötőjel, kettőspont, félig kettőspont, aposztróf, vessző, pont, aláhúzás, egyenlőségjel, zárójel, szögletes zárójel és szóközök. A százalékos szimbólum és a kapcsos zárójel csak akkor engedélyezett, ha a fejléc mező megadására használatos.
- Az egyes megadott fejléc-mezők helyesírásának meg kell egyeznie a kívánt kérelem/válasz fejléc nevével.
- Ha több fejlécet szeretne megadni, használjon elválasztót az egyes fejlécek jelzéséhez. Használhat például egy rövidítést az egyes fejlécekhez:
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o

**Alapértelmezett érték:**  -

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Hibakeresési gyorsítótár válaszának fejlécei

**Cél:** Meghatározza, hogy a válasz tartalmazhat [-e X-EC-debug válasz fejléceket](cdn-http-debug-headers.md), amelyek információt biztosítanak a kért eszköz gyorsítótár-házirendjéről.

A rendszer a válaszban a hibakeresési gyorsítótár válaszának fejléceit fogja tartalmazni, ha a következők mindegyike teljesül:

- A hibakeresési gyorsítótár válaszának fejlécei szolgáltatás engedélyezve van a megadott kérelemben.
- A megadott kérelem meghatározza a hibakeresési gyorsítótár válaszának azon fejléceit, amelyek szerepelni fognak a válaszban.

A hibakeresési gyorsítótár válaszának fejléceit a következő fejléc és a kérésben megadott irányelvek alapján kérheti le:

`X-EC-Debug: _&lt;Directive1&gt;_,_&lt;Directive2&gt;_,_&lt;DirectiveN&gt;_`

**Példa**

X-EC-Debug: x-EC-cache, x-EC-pipa-cache-t, x-EC-cache-Key, x-EC-cache-State

Érték|Eredmény
-|-
Engedélyezve|A hibakeresési gyorsítótár válaszának fejlécére vonatkozó kérelmek olyan választ adnak vissza, amely tartalmazza az X-EC-debug fejlécet.
Letiltva|Az X-EC-debug válasz fejléce ki lesz zárva a válaszból.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Alapértelmezett belső max. Age

**Cél:** Meghatározza a POP-hoz a forrásként szolgáló kiszolgáló gyorsítótárának újraérvényesítésére vonatkozó alapértelmezett maximális életkori időközt. Ez azt jelenti, hogy a POP előtt eltelt idő mennyisége azt jelzi, hogy a gyorsítótárazott eszköz megfelel-e a forrás-kiszolgálón tárolt eszköznek.

Legfontosabb információk:

- Ez a művelet csak olyan forráskiszolgáló válaszait veszi figyelembe, amely nem rendelt Max-Age jelölést a `Cache-Control` vagy `Expires` fejlécben.
- Ez a művelet nem kerül sor olyan eszközökre, amelyek nem tekinthetők gyorsítótárazható.
- Ez a művelet nem érinti a böngészőt a POP-gyorsítótár újraérvényesítésére. Ezeket a típusokat a böngészőnek eljuttatott `Cache-Control` vagy `Expires` fejléc határozza meg, amely testreszabható a külső Max-Age szolgáltatással.
- Ennek a műveletnek az eredményei nem rendelkeznek megfigyelhető hatással a válasz fejlécére és a tartalomhoz a pop-ból visszaadott tartalomra, de ez hatással lehet a pop-ból a forrás-kiszolgálóra küldött újraérvényesítési forgalom mennyiségére.
- A szolgáltatás konfigurálása a következővel:
    - Válassza ki azt az állapotkódot, amelynek alapértelmezett belső maximális élettartama alkalmazható.
    - Adja meg az egész értéket, majd válassza ki a kívánt időegységet (például másodperc, perc, óra stb.). Ez az érték határozza meg az alapértelmezett belső Max-Age intervallumot.

- Ha az időegységet "off" értékre állítja, akkor a rendszer az alapértelmezett belső maximális időtartamot (7 nap) rendeli hozzá azokhoz a kérelmekhez, amelyeket nem rendeltek hozzá a `Cache-Control` vagy `Expires` fejlécben.

**Alapértelmezett érték:** 7 nap

#### <a name="compatibility"></a>Kompatibilitás

A gyorsítótár-beállítások nyomon követésének módja miatt ez a funkció nem rendelhető hozzá a következő egyeztetési feltételekhez:
- AS szám
- Ügyfél IP-címe
- Cookie paraméter
- Cookie-paraméterek Regexje
- Ország
- Eszköz
- Edge CNAME
- Hivatkozó tartomány
- Kérelem fejléce – literál
- Kérelem fejlécének Regexje
- Kérelem fejlécének helyettesítő karaktere
- Kérési módszer
- Kérési séma
- URL-lekérdezési literál
- URL-lekérdezés regex
- URL-lekérdezés helyettesítő karaktere
- URL-lekérdezési paraméter

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="deny-access-403"></a>Hozzáférés megtagadása (403)

**Cél**: azt határozza meg, hogy az összes kérés visszautasítva van-e egy 403 Tiltott válasz esetén.

Érték | Eredmény
------|-------
Engedélyezve| Az összes olyan kérést okoz, amely megfelel a megfeleltetés feltételeinek, és 403 Tiltott választ kér.
Letiltva| Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedés az, hogy lehetővé tegye a forráskiszolgáló számára a visszaadott válasz típusának meghatározását.

**Alapértelmezett viselkedés**: letiltva

> [!TIP]
   > A funkció egyik lehetséges funkciója, hogy társítsa azt egy kérelem-fejléc egyeztetési feltétellel, hogy letiltsa a tartalomhoz beágyazott hivatkozásokat használó HTTP-hivatkozók elérését.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="expires-header-treatment"></a>A fejléc kezelése lejár

**Cél:** Azt szabályozza, hogy a külső Max-Age funkció aktív-e a `Expires` fejlécek előállításához a POP használatával.

Az ilyen típusú konfiguráció elérésének legegyszerűbb módja a külső Max-Age és a lejáró fejléc-kezelési funkciók elhelyezése ugyanabban az utasításban.

Érték|Eredmény
--|--
Írja felül|Biztosítja, hogy a következő műveletek elvégzése megtörténjen:<br/>– Felülírja a forráskiszolgáló által generált `Expires` fejlécet.<br/>– Hozzáadja a külső Max-Age funkció által előállított `Expires` fejlécet a válaszhoz.
Továbbítás|Biztosítja, hogy a külső Max-Age szolgáltatás által létrehozott `Expires` fejléc soha ne legyen hozzáadva a válaszhoz. <br/> Ha a forráskiszolgáló `Expires` fejlécet hoz létre, a rendszer továbbítja a felhasználót a végfelhasználónak. <br/>Ha a forráskiszolgáló nem hoz létre `Expires` fejlécet, akkor ez a beállítás azt eredményezheti, hogy a válasz fejléce nem tartalmaz `Expires`-fejlécet.
Hozzáadás, ha hiányzik| Ha a forráskiszolgáló `Expires` fejléce nem érkezett meg, akkor ez a beállítás hozzáadja a külső Max-Age szolgáltatás által létrehozott `Expires`-fejlécet. Ez a beállítás akkor hasznos, ha biztosítani szeretné, hogy minden eszközhöz hozzá legyen rendelve egy `Expires` fejléc.
Eltávolítás| Biztosítja, hogy a fejléc válaszában ne szerepeljen `Expires` fejléc. Ha már hozzá van rendelve egy `Expires` fejléc, akkor a rendszer eltávolítja az adott fejléc-válaszból.

**Alapértelmezett viselkedés:** Felülírja

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>Külső Max-Age

**Cél:** Meghatározza a böngészőnek a POP-gyorsítótár újraérvényesítésére vonatkozó maximális élettartamának intervallumát. Más szóval azt az időtartamot adja meg, ameddig egy böngésző megkeresi az eszköz új verzióját egy POP-ból.

A funkció engedélyezésével `Cache-Control: max-age` és `Expires` fejléceket fog előállítani a pop-ból, és elküldheti őket a HTTP-ügyfélnek. Alapértelmezés szerint ezek a fejlécek felül fogják írni a forráskiszolgáló által létrehozott fejléceket. Ennek a viselkedésnek a megváltoztatásához azonban a Cache-Control fejléc kezelése és a lejárati fejléc kezelési funkciói is használhatók.

Legfontosabb információk:

- Ez a művelet nem érinti a POP-ról a forrás-kiszolgáló gyorsítótárának újraérvényesítését. Az ilyen típusú újraérvényesítéseket a forrás-kiszolgálótól kapott `Cache-Control` és `Expires` fejlécek határozzák meg, és testreszabhatók az alapértelmezett belső Max-Age és a Force belső Max-Age funkciók használatával.
- A szolgáltatás konfigurálásához egész értéket kell megadnia, és ki kell választania a kívánt időegységet (például másodperc, perc, óra stb.).
- Ha ezt a funkciót negatív értékre állítja, akkor a durranás egy `Cache-Control: no-cache` és egy `Expires` időt küld, amely a böngészőre adott válaszokkal együtt a múltban van beállítva. Bár a HTTP-ügyfél nem gyorsítótárazza a választ, ez a beállítás nem érinti a "pop" lehetőséget a forráskiszolgáló válaszának gyorsítótárazására.
- Ha az időegységet "off" értékre állítja, akkor letiltja ezt a funkciót. A forráskiszolgáló válaszával gyorsítótárazott `Cache-Control` és `Expires` fejlécek továbbítva lesznek a böngészőnek.

**Alapértelmezett viselkedés:** Kikapcsolása

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="follow-redirects"></a>Átirányítások követése

**Cél:** Meghatározza, hogy a kérések átirányíthatók-e az ügyfél-kiszolgáló által visszaadott Location fejlécben megadott állomásnévre.

Legfontosabb információk:

- A kérelmeket csak olyan peremhálózati CNAME-re lehet átirányítani, amelyek ugyanahhoz a platformhoz tartoznak.

Érték|Eredmény
-|-
Engedélyezve|A kérések átirányíthatók.
Letiltva|A rendszer nem irányítja át a kérelmeket.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="force-internal-max-age"></a>A belső Max-Age kényszerítése

**Cél:** Meghatározza a POP-a forrásként szolgáló kiszolgáló gyorsítótár-újraérvényesítésének maximális életkori intervallumát. Más szóval azt az időtartamot adja meg, amelyet a rendszer a POP előtt továbbít, hogy a gyorsítótárazott eszköz megfelel-e a forrás-kiszolgálón tárolt eszköznek.

Legfontosabb információk:

- Ez a szolgáltatás felülbírálja `Cache-Control` vagy `Expires` a forrás-kiszolgálóról generált fejlécek maximális életkori intervallumát.
- Ez a funkció nem érinti a böngészőt a POP-gyorsítótár újraérvényesítésére. Az ilyen típusú újraérvényesítések meghatározása a böngészőben eljuttatott `Cache-Control` vagy `Expires` fejlécek alapján történik.
- Ez a funkció nem rendelkezik észlelhető hatással a kérőhöz tartozó POP által küldött válaszra. Előfordulhat azonban, hogy a pop-ból a forrás-kiszolgálóra érkező újraérvényesítési forgalom mennyiségére hatással lehet.
- A szolgáltatás konfigurálása a következővel:
    - Válassza ki azt az állapotkódot, amelyre a rendszer a belső maximális kort alkalmazza.
    - Egész érték megadása és a kívánt időegység kiválasztása (például másodperc, perc, óra stb.). Ez az érték határozza meg a kérelem maximális élettartamának intervallumát.

- Ha az időegységet "off" értékre állítja, az letiltja ezt a funkciót. A rendszer a kért eszközökhöz nem rendel hozzá egy belső maximális életkorú időközt. Ha az eredeti fejléc nem tartalmaz gyorsítótárazási utasításokat, az eszköz az alapértelmezett belső Max-Age funkció aktív beállításának megfelelően lesz gyorsítótárazva.

**Alapértelmezett viselkedés:** Kikapcsolása

#### <a name="compatibility"></a>Kompatibilitás

A gyorsítótár-beállítások nyomon követésének módja miatt ez a funkció nem rendelhető hozzá a következő egyeztetési feltételekhez:
- AS szám
- Ügyfél IP-címe
- Cookie paraméter
- Cookie-paraméterek Regexje
- Ország
- Eszköz
- Edge CNAME
- Hivatkozó tartomány
- Kérelem fejléce – literál
- Kérelem fejlécének Regexje
- Kérelem fejlécének helyettesítő karaktere
- Kérési módszer
- Kérési séma
- URL-lekérdezési literál
- URL-lekérdezés regex
- URL-lekérdezés helyettesítő karaktere
- URL-lekérdezési paraméter

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="h264-support-http-progressive-download"></a>H. 264 támogatás (HTTP Progressive letöltés)

**Cél:** Meghatározza a tartalom továbbítására használható H. 264 fájlformátumok típusait.

Legfontosabb információk:

- Adja meg az engedélyezett H. 264 filename kiterjesztések szóközzel tagolt készletét a fájlkiterjesztések beállításban. A fájlkiterjesztések beállítás felülbírálja az alapértelmezett viselkedést. Ha ezt a beállítást választja, az MP4 és a F4V támogatását is megtarthatja a fájlnév-kiterjesztések használatával.
- Adjon meg egy pontot, amikor megadja az egyes fájlnevek kiterjesztését (például _. mp4_, _. F4V_).

**Alapértelmezett viselkedés:** A HTTP Progressive letöltése alapértelmezés szerint támogatja az MP4 és a F4V adathordozót.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="honor-no-cache-request"></a>A no-cache-kérelem tiszteletben tartása

**Cél:** Meghatározza, hogy a rendszer továbbítsa-e a HTTP-ügyfél nem gyorsítótárazott kérelmeit a forráskiszolgáló felé.

Ha a http-ügyfél `Cache-Control: no-cache` és/vagy `Pragma: no-cache` fejlécet küld a HTTP-kérelemben, a nem gyorsítótár-kérelem történik.

Érték|Eredmény
--|--
Engedélyezve|Lehetővé teszi, hogy a HTTP-ügyfél nem gyorsítótárazott kérelmei továbbítva legyenek a forráskiszolgáló számára, és a forráskiszolgáló visszaküldi a válasz fejléceit és a törzsét a HTTP-ügyfélnek.
Letiltva|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedés az, hogy megakadályozza a nem gyorsítótárbeli kérelmek továbbítását a forráskiszolgáló felé.

Az összes éles forgalom esetében erősen ajánlott a funkciót az alapértelmezett letiltott állapotban hagyni. Ellenkező esetben a rendszer nem védi a forrás-kiszolgálókat a végfelhasználók számára, akik véletlenül sok sikertelen gyorsítótár-kérést indíthatnak a weblapok frissítésekor, vagy a nem gyorsítótár-fejlécet tartalmazó számos népszerű multimédia-lejátszóról. Ez a funkció azonban hasznos lehet bizonyos nem éles üzemi vagy tesztelési címtárakban való alkalmazásra, hogy lehetővé váljon a friss tartalmak igény szerinti lekérése a forrás-kiszolgálóról.

Az a gyorsítótár állapota, amelyet a rendszer a következő szolgáltatás miatt továbbíthat a forráskiszolgáló számára a `TCP_Client_Refresh_Miss`. A gyorsítótár állapota jelentés, amely az alapszintű jelentési modulban elérhető, statisztikai adatokat biztosít a gyorsítótár állapota alapján. Ez a jelentés lehetővé teszi, hogy nyomon kövesse a forrásként továbbított kérelmek számát és százalékos arányát a szolgáltatás miatt.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-origin-no-cache"></a>Kihagyott forrás – gyorsítótár

**Cél:** Meghatározza, hogy a CDN figyelmen kívül hagyja-e a forrás-kiszolgálóról kiszolgált következő irányelveket:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Legfontosabb információk:

- Konfigurálja ezt a funkciót úgy, hogy meghatározza az állapotkódok szóközzel tagolt listáját, amelyek esetében a fenti irányelvek figyelmen kívül lesznek hagyva.
- A szolgáltatáshoz tartozó érvényes állapotkódok a következők: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414 és 415.
- Tiltsa le ezt a funkciót üres értékre állítva.

**Alapértelmezett viselkedés:** Az alapértelmezett viselkedés a fenti irányelvek tiszteletben tartása.

#### <a name="compatibility"></a>Kompatibilitás

A gyorsítótár-beállítások nyomon követésének módja miatt ez a funkció nem rendelhető hozzá a következő egyeztetési feltételekhez:
- AS szám
- Ügyfél IP-címe
- Cookie paraméter
- Cookie-paraméterek Regexje
- Ország
- Eszköz
- Edge CNAME
- Hivatkozó tartomány
- Kérelem fejléce – literál
- Kérelem fejlécének Regexje
- Kérelem fejlécének helyettesítő karaktere
- Kérési módszer
- Kérési séma
- URL-lekérdezési literál
- URL-lekérdezés regex
- URL-lekérdezés helyettesítő karaktere
- URL-lekérdezési paraméter

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="ignore-unsatisfiable-ranges"></a>Unsatisfiable tartományok figyelmen kívül hagyása

**Cél:** Meghatározza azt a választ, amelyet a rendszer az ügyfeleknek küld, amikor egy kérelem létrehoz egy 416 kért tartományt, amely nem teljesíthető.

Alapértelmezés szerint a rendszer ezt az állapotkódot adja vissza, ha a megadott bájtos tartományra vonatkozó kérés nem teljesíthető egy POP-nal, és nincs megadva az IF-Range kérelem fejlécének mezője.

Érték|Eredmény
-|-
Engedélyezve|Megakadályozza, hogy a pop válaszoljon egy érvénytelen bájt-tartományra irányuló kérelemre egy 416 kért tartománnyal, amely nem teljesíthető. Ehelyett a kiszolgálók kézbesítik a kért objektumot, és egy 200 OK értéket adnak vissza az ügyfélnek.
Letiltva|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedés az 416 kért tartomány, amely nem teljesíthető.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="internal-max-stale"></a>Belső maximális – elavult

**Cél:** Azt határozza meg, hogy mennyi ideig tart a lejárati idő, amikor egy gyorsítótárazott eszköz kiszolgálható egy POP-ból, ha a POP nem tudja újraérvényesíteni a gyorsítótárazott eszközt a forrás-kiszolgálóval.

Általában, ha egy adott eszköz maximális élettartama lejár, a POP egy újraérvényesítési kérelmet küld a forráskiszolgálón. A forráskiszolgáló ezt követően a 304-as vagy újabb verzióra válaszol, hogy a POP egy új bérletet adjon a gyorsítótárazott objektumon, vagy ha a 200 OK értékkel rendelkezik, a megadhatja a POP-t a gyorsítótárazott eszköz frissített verziójával.

Ha a POP nem tud kapcsolatot létesíteni a forrás-kiszolgálóval egy ilyen újraérvényesítési kísérlet során, akkor ez a belső, Max-elavult szolgáltatás azt szabályozza, hogy a POP és mennyi ideig folytathatja a már elavult eszköz kiszolgálása.

Vegye figyelembe, hogy ez az időtartam akkor kezdődik, amikor az eszköz Max-Age lejárata lejár, nem pedig a sikertelen újraérvényesítés esetén. Ezért az a maximális időtartam, ameddig egy eszköz sikeres újraérvényesítés nélkül kézbesíthető, a Max-Age és a Max-elavult kombináció kombinációja által meghatározott idő. Ha például egy adategységet a 9:00-es számú, 30 perces maximális életkorú, 15 perces maximálisan elavult értékkel gyorsítótárazott, akkor a 9:44-es sikertelen újraérvényesítési kísérlet eredményeképpen egy végfelhasználó kapja meg az elavult gyorsítótárazott objektumot, míg a 9:46-as sikertelen újraérvényesítési kísérlet eredményeképpen az en az 504-átjáró időtúllépését fogadó felhasználó.

A szolgáltatáshoz konfigurált bármely értéket felülírja `Cache-Control: must-revalidate` vagy `Cache-Control: proxy-revalidate` a forrás-kiszolgálótól érkezett fejlécek. Ha ezek a fejlécek a forrás-kiszolgálóról érkeznek, amikor egy eszköz kezdetben gyorsítótárazva van, akkor a POP nem fogja kiszolgálni az elavult gyorsítótárazott eszközt. Ebben az esetben, ha a POP nem tudja újraérvényesíteni a forrást, amikor az eszköz Max-Age intervalluma lejárt, a POP 504 átjáró időtúllépési hibát ad vissza.

Legfontosabb információk:

- A szolgáltatás konfigurálása a következővel:
    - Válassza ki azt az állapotkódot, amelynél a rendszer a maximálisan elavultat alkalmazza.
    - Adja meg az egész értéket, majd válassza ki a kívánt időegységet (például másodperc, perc, óra stb.). Ez az érték határozza meg az alkalmazni kívánt belső Max-elavult értéket.

- Ha az időegységet "off" értékre állítja, akkor letiltja ezt a funkciót. A gyorsítótárazott eszközök nem lesznek kézbesítve a normál lejárati idő után.

**Alapértelmezett viselkedés:** Két perc

#### <a name="compatibility"></a>Kompatibilitás

A gyorsítótár-beállítások nyomon követésének módja miatt ez a funkció nem rendelhető hozzá a következő egyeztetési feltételekhez:
- AS szám
- Ügyfél IP-címe
- Cookie paraméter
- Cookie-paraméterek Regexje
- Ország
- Eszköz
- Edge CNAME
- Hivatkozó tartomány
- Kérelem fejléce – literál
- Kérelem fejlécének Regexje
- Kérelem fejlécének helyettesítő karaktere
- Kérési módszer
- Kérési séma
- URL-lekérdezési literál
- URL-lekérdezés regex
- URL-lekérdezés helyettesítő karaktere
- URL-lekérdezési paraméter

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="log-query-string"></a>Napló lekérdezési karakterlánca

**Cél:** Meghatározza, hogy a rendszer a lekérdezési karakterláncot a hozzáférési naplók URL-címével együtt tárolja-e.

Érték|Eredmény
-|-
Engedélyezve|Lehetővé teszi a lekérdezési karakterláncok tárolását egy hozzáférési naplóban lévő URL-címek rögzítésekor. Ha egy URL-cím nem tartalmaz lekérdezési karakterláncot, akkor ez a beállítás nem lesz hatással.
Letiltva|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedés a lekérdezési karakterláncok figyelmen kívül hagyása az URL-címek egy hozzáférési naplóban való rögzítésekor.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="maximum-keep-alive-requests"></a>Maximum Keep-Alive Requests

**Cél:** A Keep-Alive kapcsolatra vonatkozó kérelmek maximális számát határozza meg a bezárás előtt.

Az alacsony értékre irányuló kérések maximális számának beállítása nem ajánlott, és a teljesítmény romlását okozhatja.

Legfontosabb információk:

- Ez az érték egész egész számként adható meg.
- A megadott értékben ne szerepeljenek vesszők vagy időszakok.

**Alapértelmezett érték:** 10 000 kérelem

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-request-header"></a>Ügyfél-kérelem fejlécének módosítása

**Cél:** Minden kérelem tartalmazza a kérelem fejlécét, amely leírja azt. A szolgáltatás a következőket teheti:

- A kérelem fejlécéhez rendelt érték hozzáfűzése vagy felülírása. Ha a megadott kérelem fejléce nem létezik, akkor ez a szolgáltatás hozzáadja a kéréshez.
- A kérelem fejlécének törlése.

A forráskiszolgálón továbbított kérelmek tükrözik a szolgáltatás által végrehajtott módosításokat.

A kérelem fejlécében a következő műveletek egyikét végezheti el:

Beállítás|Leírás|Példa
-|-|-
Hozzáfűzés|A megadott érték a meglévő kérelem fejlécének végéhez lesz hozzáadva.|**Kérelem fejlécének értéke (ügyfél):**<br/>Érték1<br/>**Kérelem fejlécének értéke (szabályok motorja):**<br/>Érték2 <br/>**Új kérelem fejlécének értéke:** <br/>Value1Value2
Írja felül|A kérelem fejlécének értéke a megadott értékre lesz állítva.|**Kérelem fejlécének értéke (ügyfél):**<br/>Érték1<br/>**Kérelem fejlécének értéke (szabályok motorja):**<br/>Érték2<br/>**Új kérelem fejlécének értéke:**<br/> Érték2 <br/>
Törlés|Törli a megadott kérelem fejlécét.|**Kérelem fejlécének értéke (ügyfél):**<br/>Érték1<br/>**Ügyfél-kérelmi fejléc konfigurációjának módosítása:**<br/>Törölje a kérdéses kérelem fejlécét.<br/>**Találat**<br/>A megadott kérés fejléce nem lesz továbbítva a forráskiszolgáló számára.

Legfontosabb információk:

- Győződjön meg arról, hogy a név beállításban megadott érték pontosan egyezik a kívánt kérelem fejlécébe.
- A rendszer nem veszi figyelembe a fejléc azonosítására szolgáló esetet. Például a `Cache-Control` fejléc nevének következő variációi használhatók a azonosításához:
    - gyorsítótár-vezérlés
    - GYORSÍTÓTÁR-VEZÉRLÉS
    - cachE-Control
- A fejléc nevének megadásakor csak alfanumerikus karaktereket, kötőjeleket vagy aláhúzást használjon.
- A fejlécek törlésével megakadályozható, hogy a rendszer a pop-ból továbbítsa a forrás-kiszolgálóra.
- A következő fejlécek vannak fenntartva, és ez a funkció nem módosítható:
    - továbbított
    - host
    - Keresztül
    - figyelmeztetés
    - x – továbbított – a következőhöz:
    - Az "x-EC" kezdetű fejlécek nevei le vannak foglalva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="modify-client-response-header"></a>Ügyfél-válasz fejlécének módosítása

Minden válasz tartalmazza a válasz fejléceit, amelyek leírják azt. A szolgáltatás a következőket teheti:

- Fűzze hozzá vagy írja felül a válasz fejlécéhez rendelt értéket. Ha a megadott válasz fejléce nem létezik, akkor ez a funkció hozzáadja a válaszhoz.
- Válasz fejlécének törlése a válaszból.

Alapértelmezés szerint a válasz fejlécének értékeit a forrás-és a pop-azonosítók határozzák meg.

A következő műveletek egyike végezhető el a válasz fejlécén:

Beállítás|Leírás|Példa
-|-|-
Hozzáfűzés|A megadott érték a meglévő válasz fejléc értékének végéhez lesz hozzáadva.|**Válasz fejlécének értéke (ügyfél):**<br />Érték1<br/>**Válasz fejlécének értéke (szabályok motorja):**<br/>Érték2<br/>**Új válasz fejlécének értéke:**<br/>Value1Value2
Írja felül|A válasz fejlécének értéke a megadott értékre lesz állítva.|**Válasz fejlécének értéke (ügyfél):**<br/>Érték1<br/>**Válasz fejlécének értéke (szabályok motorja):**<br/>Érték2 <br/>**Új válasz fejlécének értéke:**<br/>Érték2 <br/>
Törlés|A megadott válasz fejlécének törlése.|**Válasz fejlécének értéke (ügyfél):**<br/>Érték1<br/>**Ügyfél-válasz fejlécének módosítása:**<br/>Törölje a kérdéses válasz fejlécét.<br/>**Találat**<br/>A megadott válasz fejléce nem lesz továbbítva a kérelmezőnek.

Legfontosabb információk:

- Győződjön meg arról, hogy a név beállításban megadott érték pontosan egyezik a kívánt válasz fejlécével.
- A rendszer nem veszi figyelembe a fejléc azonosítására szolgáló esetet. Például a `Cache-Control` fejléc nevének következő variációi használhatók a azonosításához:
    - gyorsítótár-vezérlés
    - GYORSÍTÓTÁR-VEZÉRLÉS
    - cachE-Control
- Egy fejléc törlésével megakadályozható, hogy a rendszer továbbítsa azt a kérelmezőnek.
- A következő fejlécek vannak fenntartva, és ez a funkció nem módosítható:
    - elfogadás – kódolás
    - kor
    - kapcsolat
    - Content-Encoding
    - Content-Length
    - Content-Range
    - dátum
    - kiszolgáló
    - bemutató
    - átvitel – kódolás
    - upgrade
    - változhat
    - Keresztül
    - figyelmeztetés
    - Az "x-EC" kezdetű fejlécek nevei le vannak foglalva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="partial-cache-sharing"></a>Részleges gyorsítótár megosztása

**Cél:** Meghatározza, hogy egy kérelem képes-e részlegesen gyorsítótárazott tartalom előállítására.

Ezt a részleges gyorsítótárat Ezután felhasználhatja az adott tartalomra vonatkozó új kérések teljesítésére, amíg a kért tartalom teljes mértékben gyorsítótárazva nem lesz.

Érték|Eredmény
-|-
Engedélyezve|A kérések részlegesen gyorsítótárazott tartalmat hozhatnak elő.
Letiltva|A kérések csak a kért tartalom teljes mértékben gyorsítótárazott verzióját tudják előállítani.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="prevalidate-cached-content"></a>Gyorsítótárazott tartalom előérvényesítése

**Cél:** Meghatározza, hogy a gyorsítótárazott tartalom jogosult-e a korai újraérvényesítésre az ÉLETTARTAMa lejárta előtt.

Meghatározza azt az időtartamot, ameddig a kért tartalom ÉLETTARTAMa lejár, amely alatt a rendszer jogosult lesz a korai újraérvényesítésre.

Legfontosabb információk:

- Ha a "Kikapcsolva" lehetőséget választja, az időegységnek újra kell végeznie a gyorsítótárazott tartalom ÉLETTARTAMának lejárta után. Az időt nem szabad megadni, és a rendszer figyelmen kívül hagyja.

**Alapértelmezett viselkedés:** Kikapcsolása. Az újraérvényesítés csak akkor végezhető el, ha a gyorsítótárazott tartalom ÉLETTARTAMa lejárt.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="proxy-special-headers"></a>Proxy speciális fejlécei

**Cél:** Meghatározza azon [Verizon-specifikus HTTP-kérelmek fejléceit](cdn-verizon-http-headers.md) , amelyek a pop-ból a forrás-kiszolgálóra lesznek továbbítva.

Legfontosabb információk:

- A szolgáltatásban definiált CDN-specifikus kérelmek fejlécét a rendszer továbbítja a forrás-kiszolgálónak. A kizárt fejlécek nem továbbítódnak.
- Ha meg szeretné akadályozni, hogy a CDN-specifikus kérések fejléce ne legyen továbbítva, távolítsa el a mezőt a fejlécek listájából.

Az alapértelmezett lista a következő HTTP-fejléceket tartalmazza:
- Keresztül
- X – továbbított – a következőhöz:
- X-Forwarded-Proto
- X-Host
- X – Midgress
- X-Gateway-List
- X-EC-Name
- Gazdagép

**Alapértelmezett viselkedés:** A rendszer az összes CDN-specifikus kérelem fejlécét továbbítja a forráskiszolgálón.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="refresh-zero-byte-cache-files"></a>Nulla bájtos gyorsítótár fájljainak frissítése

**Cél:** Meghatározza, hogy a rendszer hogyan kezeli a HTTP-ügyfél egy 0 bájtos gyorsítótárazási eszközre vonatkozó kérelmét.

Érvényes értékek a következők:

Érték|Eredmény
--|--
Engedélyezve|Hatására a POP újra beolvassa az eszközt a forrás-kiszolgálóról.
Letiltva|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedés a gyorsítótár-adategységek igény szerinti kiszolgálása.

Ez a funkció nem szükséges a megfelelő gyorsítótárazáshoz és tartalom-továbbításhoz, de megkerülő megoldásként hasznos lehet. A forrásként szolgáló kiszolgálók dinamikus tartalmi generátorai például véletlenül 0 bájtos válaszokat kapnak a pop-ra. Az ilyen típusú válaszokat jellemzően a POPs gyorsítótárazza. Ha tudja, hogy egy 0 bájtos válasz soha nem érvényes válasz az ilyen tartalmakra, ez a funkció megakadályozhatja az ilyen típusú eszközök kiszolgálását az ügyfeleknek.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-cacheable-status-codes"></a>Gyorsítótárazható állapotkódok beállítása

**Cél:** Meghatározza a gyorsítótárazott tartalmat okozó állapotkódok készletét.

Alapértelmezés szerint a gyorsítótárazás csak az 200-es OK-válaszok esetében engedélyezett.

Adja meg a kívánt állapotkódok szóközzel tagolt készletét.

Legfontosabb információk:

- Engedélyezze a nem engedélyezett forrás-gyorsítótár figyelmen kívül hagyása funkciót. Ha ez a szolgáltatás nincs engedélyezve, akkor a nem 200 OK válaszok nem lehetnek gyorsítótárazva.
- A szolgáltatáshoz tartozó érvényes állapotkódok a következők: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 és 505.
- Ez a funkció nem használható olyan válaszok gyorsítótárazásának letiltására, amelyek 200 OK állapotkódot hoznak.

**Alapértelmezett viselkedés:** A gyorsítótárazás csak olyan válaszok esetén engedélyezett, amelyek 200 OK állapotkódot hoznak.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="set-client-ip-custom-header"></a>Ügyfél IP-címének egyéni fejlécének beállítása

**Cél:** Egy egyéni fejlécet hoz létre, amely azonosítja a kérelmező ügyfelet IP-cím alapján a kérelemhez.

A fejléc neve beállítás határozza meg az egyéni kérelem fejlécének nevét, ahol az ügyfél IP-címe tárolva van.

Ez a funkció lehetővé teszi, hogy az ügyfél-forráskiszolgáló az ügyfél IP-címeit egy egyéni kérelem fejlécén keresztül találja meg. Ha a kérést a gyorsítótárból kézbesítik, akkor a rendszer nem fogja tájékoztatni az ügyfél IP-címéről. Ezért javasoljuk, hogy ezt a szolgáltatást olyan eszközökön használja, amelyek nincsenek gyorsítótárazva.

Győződjön meg arról, hogy a megadott fejléc neve nem felel meg a következő nevek egyikének sem:

- Szabványos kérelmek fejlécének nevei A szabványos fejléc-nevek listája a [2616-as számú RFC-dokumentumban](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html)található.
- Fenntartott fejlécek nevei:
    - továbbítva – a következőhöz:
    - host
    - változhat
    - Keresztül
    - figyelmeztetés
    - x – továbbított – a következőhöz:
    - Az "x-EC" kezdetű fejlécek nevei le vannak foglalva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-content-delivery-on-error"></a>Elavult tartalom kézbesítése hiba esetén

**Cél:** Meghatározza, hogy a rendszer a lejárt gyorsítótárazott tartalmat fogja-e továbbítani, ha hiba történik a gyorsítótár-ismétlődés során, vagy amikor a kért tartalmat az ügyfél-kiszolgálóról kéri le.

Érték|Eredmény
-|-
Engedélyezve|Az elavult tartalmat a kérelmező kézbesíti, ha hiba lép fel a forrás-kiszolgálóval létesített csatlakozás során.
Letiltva|A rendszer továbbítja a forráskiszolgáló hibáját a kérelmezőnek.

**Alapértelmezett viselkedés:** Tiltva

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="stale-while-revalidate"></a>Elavult az újraellenőrzés során

**Cél:** Javítja a teljesítményt azáltal, hogy lehetővé teszi, hogy a kiugró tartalom elavult tartalmat szolgáltasson a kérelmezőnek, amíg az újraérvényesítés megtörténik.

Legfontosabb információk:

- A funkció működése a kiválasztott időegységtől függően változik.
    - **Időegység:** Adja meg az időtartamot, és válasszon ki egy időegységet (például másodperc, perc, óra stb.) az elavult tartalmak kézbesítésének engedélyezéséhez. Ez a telepítési típus lehetővé teszi a CDN számára, hogy meghosszabbítsa a tartalom továbbításához szükséges időt, mielőtt az alábbi képletnek megfelelően érvényesíti az érvényesítést: a **TTL** + **elavult, miközben újraérvényesíti az időt**
    - **Kikapcsolva:** A "ki" érték kiválasztásával megkövetelheti, hogy a rendszer az elavult tartalomra vonatkozó kérelem elvégzése előtt újraérvényesítést kérjen.
        - Ne határozzon meg hosszabb időt, mert nem alkalmazható, és a rendszer figyelmen kívül hagyja.

**Alapértelmezett viselkedés:** Kikapcsolása. A kért tartalom kiszolgálása előtt el kell végezni az újraérvényesítést.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth"></a>Jogkivonat-hitelesítés

**Cél:** Meghatározza, hogy a rendszer a jogkivonat-alapú hitelesítést alkalmazza-e a kérelemre.

Ha a jogkivonat-alapú hitelesítés engedélyezve van, akkor csak a titkosított jogkivonatot biztosító kérelmeket kell megtartani, és meg kell felelnie a jogkivonatban megadott követelményeknek.

A jogkivonat-értékek titkosításához és visszafejtéséhez használt titkosítási kulcsot az elsődleges kulcs, a jogkivonat-hitelesítés lapon pedig a biztonsági mentési kulcs beállításai határozzák meg. Ne feledje, hogy a titkosítási kulcsok platform-specifikusak.

**Alapértelmezett viselkedés:** Tiltva.

Ez a funkció elsőbbséget élvez a legtöbb funkcióval szemben az URL-átírás funkció kivételével.

Érték | Eredmény
------|---------
Engedélyezve | A kért tartalmat jogkivonat-alapú hitelesítéssel védi. A rendszer csak olyan ügyfelektől érkező kérelmeket fogad el, amelyek érvényes jogkivonatot biztosítanak, és megfelelnek a követelményeinek. Az FTP-tranzakciók ki vannak zárva a jogkivonat-alapú hitelesítésből.
Letiltva| Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedés a jogkivonat-alapú hitelesítési konfiguráció lehetővé tétele annak megállapítására, hogy a kérések biztonságban lesznek-e.

#### <a name="compatibility"></a>Kompatibilitás

Ne használjon jogkivonat-hitelesítést mindig egyező feltétellel.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-denial-code"></a>Jogkivonat-hitelesítési elutasítási kód

**Cél:** Meghatározza, hogy a rendszer milyen típusú választ adjon vissza a felhasználónak, ha a rendszer jogkivonat-alapú hitelesítés miatt megtagadja a kérelmet.

Az elérhető válasz kódokat a következő táblázat tartalmazza.

Válasz kódja|Válasz neve|Leírás
-------------|-------------|--------
301|Véglegesen áthelyezve|Ez az állapotkód átirányítja a jogosulatlan felhasználókat a hely fejlécében megadott URL-címre.
302|Található|Ez az állapotkód átirányítja a jogosulatlan felhasználókat a hely fejlécében megadott URL-címre. Ez az állapotkód az iparági szabványnak megfelelő módszer az átirányítás végrehajtásához.
307|Ideiglenes átirányítás|Ez az állapotkód átirányítja a jogosulatlan felhasználókat a hely fejlécében megadott URL-címre.
401|Nem engedélyezett|Az állapotkód és a WWW-Authentication válasz fejlécének együttes használata lehetővé teszi a felhasználó számára a hitelesítés megadását.
403|Forbidden|Ez az üzenet a szabványos 403 Tiltott állapotjelző üzenet, amelyet jogosulatlan felhasználó fog látni a védett tartalom elérésére tett kísérlet során.
404|A fájl nem található|Ez az állapotkód azt jelzi, hogy a HTTP-ügyfél képes volt kommunikálni a kiszolgálóval, de a kért tartalom nem található.

#### <a name="compatibility"></a>Kompatibilitás

Ne használjon jogkivonat-hitelesítési elutasítási kódot egy mindig egyező feltétellel. Ehelyett használja a **felügyelet** portál **jogkivonat-hitelesítés** lapján a **Custom tagadás kezelése** szakaszt. További információ: Azure CDN- [eszközök biztonságossá tétele jogkivonat-hitelesítéssel](cdn-token-auth.md).

#### <a name="url-redirection"></a>URL-átirányítás

Ez a funkció támogatja az URL-átirányítást egy felhasználó által definiált URL-címre, ha úgy van beállítva, hogy 3xx-állapotkódot ad vissza. Ez a felhasználó által definiált URL-cím a következő lépések végrehajtásával adható meg:

1. Válasszon ki egy 3xx a jogkivonat-hitelesítési elutasítási kód funkcióhoz.
2. Válassza a "location" elemet a választható fejléc neve beállításnál.
3. Adja meg a választható fejléc-érték beállítást a kívánt URL-címhez.

Ha nincs megadva URL-cím egy 3xx, akkor a rendszer a 3xx-állapotkód szabványos válasz lapját adja vissza a felhasználónak.

Az URL-átirányítás csak 3xx-válasz kódok esetén alkalmazható.

A nem kötelező fejléc-érték beállítás az alfanumerikus karaktereket, az idézőjeleket és a szóközöket is támogatja.

#### <a name="authentication"></a>Authentication

Ez a funkció támogatja a WWW-hitelesítés fejlécének használatát, ha a jogkivonat-alapú hitelesítéssel védett tartalmak jogosulatlan kérelmére válaszol. Ha a WWW-Authenticate fejléc "Basic" értékre van állítva a konfigurációban, akkor a rendszer a jogosulatlan felhasználót fogja kérni a fiók hitelesítő adatainak megadásához.

A fenti konfiguráció a következő lépések végrehajtásával érhető el:

1. Válassza a "401" lehetőséget a token Auth-elutasítási kód funkciójának a válasz kódjában.
2. Válassza a "WWW-hitelesítés" elemet a választható fejléc neve beállításban.
3. Adja meg a nem kötelező fejléc-érték beállítást az "alapszintű" értékre.

A WWW-Authenticate fejléc csak a 401-es válasz kódok esetében érvényes.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-ignore-url-case"></a>Jogkivonat-hitelesítés figyelmen kívül hagyása URL-eset

**Cél:** Meghatározza, hogy a jogkivonat-alapú hitelesítés által végzett URL-összehasonlítás megkülönbözteti-e a kis-és nagybetűket.

A funkció által érintett paraméterek a következők:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Érvényes értékek a következők:

Érték|Eredmény
---|----
Engedélyezve|Hatására a POP figyelmen kívül hagyja az esetet a jogkivonat-alapú hitelesítési paraméterek URL-címeinek összehasonlításakor.
Letiltva|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedés a tokenek hitelesítésének kis-és nagybetűket megkülönböztető URL-összehasonlítása.

**Alapértelmezett viselkedés:** Tiltva.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="token-auth-parameter"></a>Jogkivonat-hitelesítési paraméter

**Cél:** Meghatározza, hogy a jogkivonat-alapú hitelesítés lekérdezési karakterláncának paraméterét át kell-e átnevezni.

Legfontosabb információk:

- Az érték beállítás határozza meg a lekérdezési karakterlánc paraméterének a nevét, amelyen a jogkivonat megadható.
- Az érték beállítás értéke nem lehet "ec_token".
- Győződjön meg arról, hogy az érték beállításban megadott név csak érvényes URL-karaktereket tartalmaz.

Érték|Eredmény
----|----
Engedélyezve|Az érték beállítás határozza meg a lekérdezési karakterlánc paraméterének nevét, amely alapján meg kell határozni a jogkivonatokat.
Letiltva|Egy jogkivonat meghatározatlan lekérdezési karakterláncként adható meg a kérelem URL-címében.

**Alapértelmezett viselkedés:** Tiltva. Egy jogkivonat meghatározatlan lekérdezési karakterláncként adható meg a kérelem URL-címében.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-redirect"></a>URL-átirányítás

**Cél:** Átirányítja a kéréseket a Location fejléc használatával.

A funkció konfigurálásához a következő beállításokat kell megadni:

Beállítás|Leírás
-|-
Kód|Válassza ki a kérelmezőnek visszaadott válasz kódját.
Forrás & minta| Ezek a beállítások egy kérelem URI-mintáját határozzák meg, amely az átirányítható kérelmek típusát azonosítja. Csak azok a kérelmek lesznek átirányítva, amelyek URL-címe megfelel az alábbi feltételeknek: <br/> <br/> **Forrás (vagy tartalom-hozzáférési pont):** Válasszon egy relatív elérési utat, amely azonosítja a forrás-kiszolgálót. Ez az elérési út a _/XXXX/_ szakasz és a végpont neve. <br/><br/> **Forrás (minta):** Meg kell adni egy mintát, amely a relatív elérési út alapján azonosítja a kérelmeket. Ez a reguláris kifejezési minta olyan elérési utat határoz meg, amely közvetlenül a korábban kiválasztott tartalom-hozzáférési pont után indul el (lásd fent). <br/> – Győződjön meg arról, hogy a korábban definiált kérelem URI-feltételei (azaz a forrás & mintája) nem ütköznek a szolgáltatáshoz megadott egyezési feltételekkel. <br/> -Minta meghatározása; Ha üres értéket használ mintázatként, a rendszer az összes karakterláncot egyezteti.
Cél| Adja meg azt az URL-címet, amelyre a fenti kérelmek átirányítva lesznek. <br/><br/> Dinamikusan hozza létre ezt az URL-címet a következő használatával: <br/> – Reguláris kifejezés mintája <br/>[http-változók](cdn-http-variables.md) -  <br/><br/> Helyettesítse be a forrás mintájában rögzített értékeket a cél mintába a $_n_ értékkel, ahol _n_ a rögzített sorrend szerint azonosít egy értéket. Például a $1 a forrás mintában rögzített első értéket jelöli, míg a $2 a második értéket jelöli. <br/>

Erősen ajánlott abszolút URL-címet használni. A relatív URL-cím használata a CDN URL-címek érvénytelen elérési útra való átirányítására használható.

**Példa a forgatókönyvre**

Ez a példa azt mutatja be, hogyan lehet átirányítani egy olyan peremhálózati CNAME URL-címet, amely feloldja ezt az alap CDN URL-címet: http:\//marketing.azureedge.net/brochures

A rendszer átirányítja a minősítési kérelmeket az alapszintű CNAME URL-címre: http:\//cdn.mydomain.com/resources

Az URL-átirányítás a következő konfiguráción keresztül érhető el: ![URL-átirányítás](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Legfontosabb pontok:**

- Az URL-átirányítás funkció az átirányított kérelmek URL-címeit határozza meg. Ennek eredményeképpen további egyeztetési feltételek nem szükségesek. Bár az egyeztetési feltétel "mindig", csak a "marketing" ügyfél-eredet "prospektusok" mappájára mutató kérések lesznek átirányítva.
- Az összes egyező kérést a rendszer átirányítja a cél beállításban meghatározott peremhálózati CNAME URL-címre.
    - Példa #1i forgatókönyvre:
        - Minta kérés (CDN URL-cím): http:\//marketing.azureedge.net/brochures/widgets.pdf
        - Kérelem URL-címe (átirányítás után): http:\//cdn.mydomain.com/resources/widgets.pdf  
    - Példa #2i forgatókönyvre:
        - Minta kérése (Edge CNAME URL-cím): http:\//marketing.mydomain.com/brochures/widgets.pdf
        - Kérelem URL-címe (átirányítás után): http:\//cdn.mydomain.com/resources/widgets.pdf minta forgatókönyv
    - Példa #3i forgatókönyvre:
        - Minta kérése (Edge CNAME URL-cím): http:\//brochures.mydomain.com/campaignA/final/productC.ppt
        - Kérelem URL-címe (átirányítás után): http:\//cdn.mydomain.com/resources/campaignA/final/productC.ppt 
- A (z) (% {Scheme}) változó a cél beállításban van kihasználva, amely biztosítja, hogy a kérelem sémája változatlan maradjon az átirányítás után.
- A kérelemből rögzített URL-szegmensek hozzáfűzése az új URL-címhez a "$1" használatával történik.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="url-rewrite"></a>URL-átírás

**Cél:** A kérelem URL-címének újraírása.

Legfontosabb információk:

- A funkció konfigurálásához a következő beállításokat kell megadni:

Beállítás|Leírás
-|-
 Forrás & minta | Ezek a beállítások egy kérelem URI-mintáját határozzák meg, amely az esetlegesen újraírható kérelmek típusát azonosítja. Csak azok a kérelmek lesznek újraírva, amelyek URL-címe megfelel az alábbi feltételeknek: <br/><br/>  - **forrás (vagy tartalom-hozzáférési pont):** válasszon ki egy relatív elérési utat, amely azonosítja a forráskiszolgáló-kiszolgálót. Ez az elérési út a _/XXXX/_ szakasz és a végpont neve. <br/><br/> - **forrás (minta):** meg kell adni egy mintát, amely a relatív elérési út alapján azonosítja a kérelmeket. Ez a reguláris kifejezési minta olyan elérési utat határoz meg, amely közvetlenül a korábban kiválasztott tartalom-hozzáférési pont után indul el (lásd fent). <br/> Ellenőrizze, hogy a korábban definiált kérelem URI-feltételei (azaz a forrás & mintája) nem ütköznek-e a szolgáltatáshoz megadott egyezési feltételekkel. Határozza meg a mintát; Ha üres értéket használ mintázatként, a rendszer az összes karakterláncot egyezteti.
 Cél  |Adja meg azt a relatív URL-címet, amelyre a fenti kérelmeket át kívánja írni: <br/>    1. Válassza ki a forrás-kiszolgálót azonosító tartalom-hozzáférési pontot. <br/>    2. relatív elérési út definiálása a használatával: <br/>        – Reguláris kifejezés mintája <br/>        [http-változók](cdn-http-variables.md) -  <br/> <br/> Helyettesítse be a forrás mintájában rögzített értékeket a cél mintába a $_n_ értékkel, ahol _n_ a rögzített sorrend szerint azonosít egy értéket. Például a $1 a forrás mintában rögzített első értéket jelöli, míg a $2 a második értéket jelöli.

 Ez a funkció lehetővé teszi, hogy a pop-ban újra lehessen írni az URL-címet anélkül, hogy hagyományos átirányítás lenne. A kérelmező ugyanazt a hibakódot kapja, mint ha az újramegírt URL-címet kérték.

**1. példa**

Ez a példa azt szemlélteti, hogyan lehet átirányítani egy olyan peremhálózati CNAME URL-címet, amely feloldja az alap CDN URL-címét: http:\//marketing.azureedge.net/brochures/

A rendszer átirányítja a minősítési kérelmeket az alapszintű CNAME URL-címre: http:\//MyOrigin.azureedge.net/resources/

Az URL-átirányítás a következő konfiguráción keresztül érhető el: ![URL-átirányítás](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**2. minta példa**

Ebből a példából megtudhatja, hogyan irányíthatja át a peremhálózati CNAME URL-címet a kisbetűsről a kisbetűsre reguláris kifejezések használatával.

Az URL-átirányítás a következő konfiguráción keresztül érhető el: ![URL-átirányítás](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**Legfontosabb pontok:**

- Az URL-cím újraírása szolgáltatás meghatározza a kérelem URL-címeit, amelyeket újra kell írni. Ennek eredményeképpen további egyeztetési feltételek nem szükségesek. Bár az egyeztetési feltétel "mindig", csak a "marketing" ügyfél-forrás "prospektusok" mappájára mutató kérelmek lesznek átírva.

- A kérelemből rögzített URL-szegmensek hozzáfűzése az új URL-címhez a "$1" használatával történik.

#### <a name="compatibility"></a>Kompatibilitás

Ez a szolgáltatás olyan egyeztetési feltételeket tartalmaz, amelyeknek teljesülniük kell ahhoz, hogy alkalmazni lehessen a kérelmekre. Az ütköző egyeztetési feltételek beállításának megakadályozása érdekében ez a funkció nem kompatibilis a következő egyeztetési feltételekkel:

- AS szám
- CDN-forrás
- Ügyfél IP-címe
- Ügyfél forrása
- Kérési séma
- URL elérési útja könyvtár
- URL-elérési út kiterjesztése
- URL elérési útja fájlnév
- URL elérési útja literál
- URL-cím elérési útja – regex
- URL elérési útja helyettesítő karakter
- URL-lekérdezési literál
- URL-lekérdezési paraméter
- URL-lekérdezés regex
- URL-lekérdezés helyettesítő karaktere

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="user-variable"></a>Felhasználói változó

**Cél:** Csak belső használatra.

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

## <a name="next-steps"></a>Következő lépések

- [Szabálymotor-referencia](cdn-verizon-premium-rules-engine-reference.md)
- [Szabálymotor feltételes kifejezései](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Szabálymotor egyezési feltételei](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [HTTP-viselkedés felülbírálása a szabályok motor használatával](cdn-verizon-premium-rules-engine.md)
- [Azure CDN áttekintése](cdn-overview.md)