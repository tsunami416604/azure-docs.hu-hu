---
title: Azure CDN a Verizon Premium Rules Engine funkcióival | Microsoft Docs
description: A Verizon Premium Rules Engine funkcióinak Azure CDN dokumentációja.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/26/2020
ms.author: allensu
ms.openlocfilehash: d2d4090934a940809fe75ad70e0650eb1c9353f1
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83872717"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Azure CDN a Verizon Premium Rules Engine funkcióival

Ez a cikk az Azure Content Delivery Network (CDN) [szabályok motorjának](cdn-verizon-premium-rules-engine.md)elérhető szolgáltatásainak részletes leírását tartalmazza.

A szabály harmadik része a szolgáltatás. A szolgáltatás határozza meg, hogy milyen típusú műveletet alkalmaz a rendszer az egyeztetési feltételek alapján azonosított kérelem típusára.


A legújabb funkciókért tekintse meg a [Verizon Rules Engine dokumentációját](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions).


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
- Ez a funkció csak a HTTP POST metódust támogatja. Engedélyezze a válasz utáni gyorsítótárazást a funkció beállításával `POST` .
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

**Cél:** A fejlécek létrehozásának szabályozása a `Cache-Control` pop-ban, ha a külső Max-Age funkció aktív.

Az ilyen típusú konfiguráció elérésének legegyszerűbb módja a külső Max-Age és a Cache-Control fejléc kezelési funkcióinak elhelyezése ugyanabban az utasításban.

Érték|Eredmény
--|--
Felülírás|Biztosítja a következő műveletek elvégzését:<br/> – Felülírja a `Cache-Control` forráskiszolgáló által generált fejlécet. <br/>– Hozzáadja a `Cache-Control` külső Max-Age funkció által előállított fejlécet a válaszhoz.
Továbbítás|Biztosítja, hogy a `Cache-Control` külső Max-Age funkció által létrehozott fejléc soha ne legyen hozzáadva a válaszhoz. <br/> Ha a forráskiszolgáló létrehoz egy `Cache-Control` fejlécet, a rendszer továbbítja a felhasználót a végfelhasználónak. <br/> Ha a forráskiszolgáló nem `Cache-Control` hoz létre fejlécet, akkor ez a beállítás azt eredményezheti, hogy a válasz fejléce nem tartalmaz `Cache-Control` fejlécet.
Hozzáadás, ha hiányzik|Ha a `Cache-Control` forráskiszolgáló nem kapott fejlécet, akkor ez a beállítás hozzáadja a `Cache-Control` külső Max-Age szolgáltatás által létrehozott fejlécet. Ez a beállítás akkor hasznos, ha biztosítani szeretné, hogy minden eszközhöz hozzá legyen rendelve egy `Cache-Control` fejléc.
Eltávolítás| Ezzel a beállítással biztosítható, hogy a fejléc `Cache-Control` ne szerepeljen az élőfej válaszában. Ha `Cache-Control` már hozzá van rendelve egy fejléc, az el lesz távolítva a fejléc-válaszból.

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
 Belefoglalás|  Azt jelzi, hogy minden megadott paraméternek szerepelnie kell a gyorsítótár-kulcsban. A rendszer egyedi gyorsítótár-kulcsot hoz létre minden olyan kérelemhez, amely egyedi értéket tartalmaz a funkcióban definiált lekérdezési karakterlánc-paraméterhez.
 Összes belefoglalása  |Azt jelzi, hogy minden kérelemhez egyedi gyorsítótár-kulcsot hoz létre egy olyan objektumhoz, amely egyedi lekérdezési karakterláncot tartalmaz. Ez a típusú konfiguráció általában nem ajánlott, mert a gyorsítótár-találatok kis hányadát okozhatják. A gyorsítótárbeli találatok alacsony száma növeli a forráskiszolgáló terhelését, mert több kérést kell kiszolgálnia. Ez a konfiguráció a lekérdezési karakterlánc gyorsítótárazása lapon a "Unique-cache" néven ismert gyorsítótárazási viselkedést duplikálja.
 Exclude | Azt jelzi, hogy csak a megadott paraméter (ek) ki van zárva a gyorsítótár-kulcsból. Az összes többi lekérdezési karakterlánc paraméter a gyorsítótár-kulcs részét képezi.
 Az összes kizárása  |Azt jelzi, hogy az összes lekérdezési karakterlánc paraméter ki van zárva a gyorsítótár-kulcsból. Ez a konfiguráció a "standard-cache" alapértelmezett gyorsítótárazási viselkedését ismétli meg a lekérdezés-karakterlánc gyorsítótárazási oldalon.  

A szabályok motor segítségével testre szabhatja a lekérdezési karakterlánc gyorsítótárazásának megvalósítási módját. Megadhatja például, hogy a lekérdezési karakterlánc gyorsítótárazása csak bizonyos helyszíneken vagy fájltípusokon történjen.

Ha a "no-cache" lekérdezési karakterlánc gyorsítótárazási viselkedését szeretné duplikálni a lekérdezési karakterlánc gyorsítótárazása lapon, hozzon létre egy szabályt, amely tartalmaz egy URL-lekérdezési helyettesítő karaktert, valamint egy megkerülési gyorsítótár-funkciót. Állítsa be az URL-lekérdezés helyettesítő karakterének megfeleltetése feltételt egy csillag (*) értékre.

>[!IMPORTANT]
> Ha a jogkivonat-hitelesítés engedélyezve van a fiók bármely elérési útjához, a szabványos gyorsítótárazási mód az egyetlen mód, amely a lekérdezési karakterláncok gyorsítótárazásához használható. További információkért lásd: [Az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési sztringekkel](cdn-query-string-premium.md).

#### <a name="sample-scenarios"></a>Példák a forgatókönyvekre

A szolgáltatás következő példája egy minta kérést és az alapértelmezett gyorsítótár-kulcsot tartalmaz:

- **Példa a kérelemre:** http://wpc.0001.&lt ;D omain &gt; /800001/Origin/Folder/Asset.htm? munkamenet-azonosító = 1234&language = EN&userid = 01
- **Alapértelmezett gyorsítótár – kulcs:** /800001/Origin/Folder/Asset.htm

##### <a name="include"></a>Belefoglalás

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
- Kérelem metódusa
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
szöveg/css|Egymásra épülő stíluslapok (CSS)
alkalmazás/x – JavaScript|JavaScript
alkalmazás/JavaScript|JavaScript

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
Kérelem fejléce|`%{[RequestHeader]()}[i]()` | % {Elfogadás – kódolás} i <br/> {Referrer} i <br/> % {Authorization} i
Válaszfejléc|`%{[ResponseHeader]()}[o]()`| % {Age} o <br/> % {Content-Type} o <br/> % {Cookie} o

Legfontosabb információk:

- Az egyéni napló mező a fejléc mezőinek és egyszerű szövegének tetszőleges kombinációját tartalmazhatja.
- A mezőhöz érvényes karakterek a következők: alfanumerikus (0-9, a-z, és A-Z), kötőjel, kettőspont, félig kettőspont, aposztróf, vessző, pont, aláhúzás, egyenlőségjel, zárójel, szögletes zárójel és szóközök. A százalékos szimbólum és a kapcsos zárójel csak akkor engedélyezett, ha a fejléc mező megadására használatos.
- Az egyes megadott fejléc-mezők helyesírásának meg kell egyeznie a kívánt kérelem/válasz fejléc nevével.
- Ha több fejlécet szeretne megadni, használjon elválasztót az egyes fejlécek jelzéséhez. Használhat például egy rövidítést az egyes fejlécekhez:
    - AE:% {elfogadás-kódolás} i A:% {Authorization} i CT:% {Content-Type} o

**Alapértelmezett érték:** -

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

**Például**

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
- Ez a művelet nem érinti a böngészőt a POP-gyorsítótár újraérvényesítésére. Az ilyen típusú újraérvényesítéseket a `Cache-Control` `Expires` böngészőnek eljuttatott vagy az azokat tartalmazó fejlécek határozzák meg, amelyek testreszabhatók a külső Max-Age szolgáltatással.
- Ennek a műveletnek az eredményei nem rendelkeznek megfigyelhető hatással a válasz fejlécére és a tartalomhoz a pop-ból visszaadott tartalomra, de ez hatással lehet a pop-ból a forrás-kiszolgálóra küldött újraérvényesítési forgalom mennyiségére.
- A szolgáltatás konfigurálása a következővel:
    - Válassza ki azt az állapotkódot, amelynek alapértelmezett belső maximális élettartama alkalmazható.
    - Adja meg az egész értéket, majd válassza ki a kívánt időegységet (például másodperc, perc, óra stb.). Ez az érték határozza meg az alapértelmezett belső Max-Age intervallumot.

- Ha az időegységet "off" értékre állítja, a rendszer egy alapértelmezett belső, legfeljebb 7 napos időtartamot rendel hozzá a kérelmekhez, amelyek nem lettek megadva a Max-Age jelöléssel a `Cache-Control` vagy a `Expires` fejlécben.

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
- Kérelem metódusa
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

**Cél:** A `Expires` fejlécek előállítását vezérli a pop-ban, ha a külső Max-Age funkció aktív.

Az ilyen típusú konfiguráció elérésének legegyszerűbb módja a külső Max-Age és a lejáró fejléc-kezelési funkciók elhelyezése ugyanabban az utasításban.

Érték|Eredmény
--|--
Felülírás|Biztosítja, hogy a következő műveletek elvégzése megtörténjen:<br/>– Felülírja a `Expires` forráskiszolgáló által generált fejlécet.<br/>– Hozzáadja a `Expires` külső Max-Age funkció által előállított fejlécet a válaszhoz.
Továbbítás|Biztosítja, hogy a `Expires` külső Max-Age funkció által létrehozott fejléc soha ne legyen hozzáadva a válaszhoz. <br/> Ha a forráskiszolgáló létrehoz egy `Expires` fejlécet, a rendszer továbbítja a felhasználót a végfelhasználónak. <br/>Ha a forráskiszolgáló nem `Expires` hoz létre fejlécet, akkor ez a beállítás azt eredményezheti, hogy a válasz fejléce nem tartalmaz `Expires` fejlécet.
Hozzáadás, ha hiányzik| Ha a `Expires` forráskiszolgáló nem kapott fejlécet, akkor ez a beállítás hozzáadja a `Expires` külső Max-Age szolgáltatás által létrehozott fejlécet. Ez a beállítás akkor hasznos, ha biztosítani szeretné, hogy minden eszköz hozzá legyen rendelve egy `Expires` fejléchez.
Eltávolítás| Gondoskodik arról, hogy `Expires` a fejléc ne szerepeljen az élőfej válaszában. Ha `Expires` már hozzá van rendelve egy fejléc, akkor a rendszer eltávolítja az adott fejléc-válaszból.

**Alapértelmezett viselkedés:** Felülírja

[Vissza a tetejére](#azure-cdn-from-verizon-premium-rules-engine-features)

</br>

---

### <a name="external-max-age"></a>Külső Max-Age

**Cél:** Meghatározza a böngészőnek a POP-gyorsítótár újraérvényesítésére vonatkozó maximális élettartamának intervallumát. Más szóval azt az időtartamot adja meg, ameddig egy böngésző megkeresi az eszköz új verzióját egy POP-ból.

Ha engedélyezi ezt a funkciót, a rendszer létrehozza `Cache-Control: max-age` és `Expires` kitölti a pop-ket, és elküldi őket a http-ügyfélnek. Alapértelmezés szerint ezek a fejlécek felül fogják írni a forráskiszolgáló által létrehozott fejléceket. Ennek a viselkedésnek a megváltoztatásához azonban a Cache-Control fejléc kezelése és a lejárati fejléc kezelési funkciói is használhatók.

Legfontosabb információk:

- Ez a művelet nem érinti a POP-ról a forrás-kiszolgáló gyorsítótárának újraérvényesítését. Az ilyen típusú újraérvényesítéseket a `Cache-Control` `Expires` forrás-kiszolgálótól kapott és a fejlécek határozzák meg, és testreszabhatók az alapértelmezett belső Max-Age és a Force belső Max-Age funkciókkal.
- A szolgáltatás konfigurálásához egész értéket kell megadnia, és ki kell választania a kívánt időegységet (például másodperc, perc, óra stb.).
- Ha ezt a funkciót negatív értékre állítja, a a következő üzenet jelenik meg, `Cache-Control: no-cache` `Expires` amely a böngészőben megjelenő minden egyes válasznál a múltban megadott idő. Bár a HTTP-ügyfél nem gyorsítótárazza a választ, ez a beállítás nem érinti a "pop" lehetőséget a forráskiszolgáló válaszának gyorsítótárazására.
- Ha az időegységet "off" értékre állítja, akkor letiltja ezt a funkciót. A `Cache-Control` `Expires` forrás-kiszolgáló válaszával gyorsítótárazott és a fejléceket a rendszer átadja a böngészőnek.

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

- Ez a szolgáltatás felülbírálja a forrás-kiszolgáló által definiált `Cache-Control` vagy fejlécekben megadott maximális életkori intervallumot `Expires` .
- Ez a funkció nem érinti a böngészőt a POP-gyorsítótár újraérvényesítésére. Az ilyen típusú újraérvényesítéseket a `Cache-Control` `Expires` böngészőnek eljuttatott vagy fejlécek határozzák meg.
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
- Kérelem metódusa
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

A nem gyorsítótárra vonatkozó kérelem akkor következik be, amikor a HTTP-ügyfél `Cache-Control: no-cache` a http-kérelemben egy és/vagy `Pragma: no-cache` fejlécet küld.

Érték|Eredmény
--|--
Engedélyezve|Lehetővé teszi, hogy a HTTP-ügyfél nem gyorsítótárazott kérelmei továbbítva legyenek a forráskiszolgáló számára, és a forráskiszolgáló visszaküldi a válasz fejléceit és a törzsét a HTTP-ügyfélnek.
Letiltva|Visszaállítja az alapértelmezett viselkedést. Az alapértelmezett viselkedés az, hogy megakadályozza a nem gyorsítótárbeli kérelmek továbbítását a forráskiszolgáló felé.

Az összes éles forgalom esetében erősen ajánlott a funkciót az alapértelmezett letiltott állapotban hagyni. Ellenkező esetben a rendszer nem védi a forrás-kiszolgálókat a végfelhasználók számára, akik véletlenül sok sikertelen gyorsítótár-kérést indíthatnak a weblapok frissítésekor, vagy a nem gyorsítótár-fejlécet tartalmazó számos népszerű multimédia-lejátszóról. Ez a funkció azonban hasznos lehet bizonyos nem éles üzemi vagy tesztelési címtárakban való alkalmazásra, hogy lehetővé váljon a friss tartalmak igény szerinti lekérése a forrás-kiszolgálóról.

Az a gyorsítótár-állapot, amelyet a rendszer a következő szolgáltatás miatt továbbíthat egy forráskiszolgáló számára `TCP_Client_Refresh_Miss` . A gyorsítótár állapota jelentés, amely az alapszintű jelentési modulban elérhető, statisztikai adatokat biztosít a gyorsítótár állapota alapján. Ez a jelentés lehetővé teszi, hogy nyomon kövesse a forrásként továbbított kérelmek számát és százalékos arányát a szolgáltatás miatt.

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
- Kérelem metódusa
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

Vegye figyelembe, hogy ez az időtartam akkor kezdődik, amikor az eszköz Max-Age lejárata lejár, nem pedig a sikertelen újraérvényesítés esetén. Ezért az a maximális időtartam, ameddig egy eszköz sikeres újraérvényesítés nélkül kézbesíthető, a Max-Age és a Max-elavult kombináció kombinációja által meghatározott idő. Ha például egy adategységet a 9:00-es verzióban, a max. 30 percben, a max. 15 percen belül, a 9:44-es sikertelen újraérvényesítési kísérlet eredményeképpen egy végfelhasználó kapja meg az elavult gyorsítótárazott objektumot, míg a 9:46-ben meghiúsult újraérvényesítési kísérlet eredményeképpen a végfelhasználó a 504 Gateway időtúllépését fogja kapni.

A szolgáltatáshoz konfigurált bármely értéket felülírja a rendszer, `Cache-Control: must-revalidate` vagy `Cache-Control: proxy-revalidate` a forráskiszolgáló által fogadott fejléceket. Ha ezek a fejlécek a forrás-kiszolgálóról érkeznek, amikor egy eszköz kezdetben gyorsítótárazva van, akkor a POP nem fogja kiszolgálni az elavult gyorsítótárazott eszközt. Ebben az esetben, ha a POP nem tudja újraérvényesíteni a forrást, amikor az eszköz Max-Age intervalluma lejárt, a POP 504 átjáró időtúllépési hibát ad vissza.

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
- Kérelem metódusa
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

### <a name="maximum-keep-alive-requests"></a>Életben tartási kérelmek maximális száma

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
Felülírás|A kérelem fejlécének értéke a megadott értékre lesz állítva.|**Kérelem fejlécének értéke (ügyfél):**<br/>Érték1<br/>**Kérelem fejlécének értéke (szabályok motorja):**<br/>Érték2<br/>**Új kérelem fejlécének értéke:**<br/> Érték2 <br/>
Törlés|Törli a megadott kérelem fejlécét.|**Kérelem fejlécének értéke (ügyfél):**<br/>Érték1<br/>**Ügyfél-kérelmi fejléc konfigurációjának módosítása:**<br/>Törölje a kérdéses kérelem fejlécét.<br/>**Találat**<br/>A megadott kérés fejléce nem lesz továbbítva a forráskiszolgáló számára.

Legfontosabb információk:

- Győződjön meg arról, hogy a név beállításban megadott érték pontosan egyezik a kívánt kérelem fejlécébe.
- A rendszer nem veszi figyelembe a fejléc azonosítására szolgáló esetet. Például a fejléc nevének következő variációi `Cache-Control` használhatók a azonosításához:
    - gyorsítótár-vezérlés
    - GYORSÍTÓTÁR-VEZÉRLÉS
    - Gyorsítótár-vezérlés
- A fejléc nevének megadásakor csak alfanumerikus karaktereket, kötőjeleket vagy aláhúzást használjon.
- A fejlécek törlésével megakadályozható, hogy a rendszer a pop-ból továbbítsa a forrás-kiszolgálóra.
- A következő fejlécek vannak fenntartva, és ez a funkció nem módosítható:
    - továbbított
    - gazda
    - keresztül
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
Felülírás|A válasz fejlécének értéke a megadott értékre lesz állítva.|**Válasz fejlécének értéke (ügyfél):**<br/>Érték1<br/>**Válasz fejlécének értéke (szabályok motorja):**<br/>Érték2 <br/>**Új válasz fejlécének értéke:**<br/>Érték2 <br/>
Törlés|A megadott válasz fejlécének törlése.|**Válasz fejlécének értéke (ügyfél):**<br/>Érték1<br/>**Ügyfél-válasz fejlécének módosítása:**<br/>Törölje a kérdéses válasz fejlécét.<br/>**Találat**<br/>A megadott válasz fejléce nem lesz továbbítva a kérelmezőnek.

Legfontosabb információk:

- Győződjön meg arról, hogy a név beállításban megadott érték pontosan egyezik a kívánt válasz fejlécével.
- A rendszer nem veszi figyelembe a fejléc azonosítására szolgáló esetet. Például a fejléc nevének következő variációi `Cache-Control` használhatók a azonosításához:
    - gyorsítótár-vezérlés
    - GYORSÍTÓTÁR-VEZÉRLÉS
    - Gyorsítótár-vezérlés
- Egy fejléc törlésével megakadályozható, hogy a rendszer továbbítsa azt a kérelmezőnek.
- A következő fejlécek vannak fenntartva, és ez a funkció nem módosítható:
    - elfogadás – kódolás
    - életkor
    - kapcsolat
    - Content-Encoding
    - Content-Length
    - Content-Range
    - date
    - kiszolgáló
    - bemutató
    - átvitel – kódolás
    - frissítés
    - változhat
    - keresztül
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
- X – továbbított – proto
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

Az érvényes értékek a következők:

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
    - gazda
    - változhat
    - keresztül
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
    - **Időegység:** Adja meg az időtartamot, és válasszon ki egy időegységet (például másodperc, perc, óra stb.) az elavult tartalmak kézbesítésének engedélyezéséhez. Ez a telepítési típus lehetővé teszi, hogy a CDN kiterjessze azt az időtartamot, ameddig a tartalom elérhetővé válik, mielőtt az alábbi képletnek megfelelően érvényesíti az érvényesítést: a **TTL**  +  **elavult az idő újraérvényesítése során** .
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

Válaszkód|Válasz neve|Leírás
-------------|-------------|--------
301|Véglegesen áthelyezve|Ez az állapotkód átirányítja a jogosulatlan felhasználókat a hely fejlécében megadott URL-címre.
302|Találat|Ez az állapotkód átirányítja a jogosulatlan felhasználókat a hely fejlécében megadott URL-címre. Ez az állapotkód az iparági szabványnak megfelelő módszer az átirányítás végrehajtásához.
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

#### <a name="authentication"></a>Hitelesítés

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

Az érvényes értékek a következők:

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
Code|Válassza ki a kérelmezőnek visszaadott válasz kódját.
Forrás & minta| Ezek a beállítások egy kérelem URI-mintáját határozzák meg, amely az átirányítható kérelmek típusát azonosítja. Csak azok a kérelmek lesznek átirányítva, amelyek URL-címe megfelel az alábbi feltételeknek: <br/> <br/> **Forrás (vagy tartalom-hozzáférési pont):** Válasszon egy relatív elérési utat, amely azonosítja a forrás-kiszolgálót. Ez az elérési út a _/XXXX/_ szakasz és a végpont neve. <br/><br/> **Forrás (minta):** Meg kell adni egy mintát, amely a relatív elérési út alapján azonosítja a kérelmeket. Ez a reguláris kifejezési minta olyan elérési utat határoz meg, amely közvetlenül a korábban kiválasztott tartalom-hozzáférési pont után indul el (lásd fent). <br/> – Győződjön meg arról, hogy a korábban definiált kérelem URI-feltételei (azaz a forrás & mintája) nem ütköznek a szolgáltatáshoz megadott egyezési feltételekkel. <br/> -Minta meghatározása; Ha üres értéket használ mintázatként, a rendszer az összes karakterláncot egyezteti.
Cél| Adja meg azt az URL-címet, amelyre a fenti kérelmek átirányítva lesznek. <br/><br/> Dinamikusan hozza létre ezt az URL-címet a következő használatával: <br/> – Reguláris kifejezés mintája <br/>- [HTTP-változók](cdn-http-variables.md) <br/><br/> Helyettesítse be a forrás mintájában rögzített értékeket a cél mintába a $_n_ értékkel, ahol _n_ a rögzített sorrend szerint azonosít egy értéket. Például a $1 a forrás mintában rögzített első értéket jelöli, míg a $2 a második értéket jelöli. <br/>

Erősen ajánlott abszolút URL-címet használni. A relatív URL-cím használata a CDN URL-címek érvénytelen elérési útra való átirányítására használható.

**Példa a forgatókönyvre**

Ez a példa azt mutatja be, hogyan lehet átirányítani egy peremhálózati CNAME URL-címet, amely feloldja az alap CDN URL-címét: http: \/ /marketing.azureedge.net/brochures

A megfelelő kéréseket a rendszer átirányítja erre az alapszintű CNAME URL-címre: http: \/ /CDN.mydomain.com/Resources

Az URL-átirányítás a következő konfiguráción keresztül érhető el: ![ URL-átirányítás](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Összefoglalás:**

- Az URL-átirányítás funkció az átirányított kérelmek URL-címeit határozza meg. Ennek eredményeképpen további egyeztetési feltételek nem szükségesek. Bár az egyeztetési feltétel "mindig", csak a "marketing" ügyfél-eredet "prospektusok" mappájára mutató kérések lesznek átirányítva.
- Az összes egyező kérést a rendszer átirányítja a cél beállításban meghatározott peremhálózati CNAME URL-címre.
    - Példa #1i forgatókönyvre:
        - Minta kérés (CDN URL-cím): http: \/ /marketing.azureedge.net/brochures/widgets.pdf
        - Kérelem URL-címe (átirányítás után): http: \/ /CDN.mydomain.com/Resources/widgets.pdf  
    - Példa #2i forgatókönyvre:
        - Minta kérése (Edge CNAME URL-cím): http: \/ /marketing.mydomain.com/brochures/widgets.pdf
        - Kérelem URL-címe (átirányítás után): http: \/ /CDN.mydomain.com/Resources/widgets.pdf minta forgatókönyv
    - Példa #3i forgatókönyvre:
        - Minta kérése (Edge CNAME URL-cím): http: \/ /brochures.mydomain.com/campaignA/Final/productC.ppt
        - Kérelem URL-címe (átirányítás után): http: \/ /CDN.mydomain.com/Resources/campaignA/Final/productC.ppt 
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
 Forrás & minta | Ezek a beállítások egy kérelem URI-mintáját határozzák meg, amely az esetlegesen újraírható kérelmek típusát azonosítja. Csak azok a kérelmek lesznek újraírva, amelyek URL-címe megfelel az alábbi feltételeknek: <br/><br/>  - **Forrás (vagy tartalom-hozzáférési pont):** Válasszon egy relatív elérési utat, amely azonosítja a forrás-kiszolgálót. Ez az elérési út a _/XXXX/_ szakasz és a végpont neve. <br/><br/> - **Forrás (minta):** Meg kell adni egy mintát, amely a relatív elérési út alapján azonosítja a kérelmeket. Ez a reguláris kifejezési minta olyan elérési utat határoz meg, amely közvetlenül a korábban kiválasztott tartalom-hozzáférési pont után indul el (lásd fent). <br/> Ellenőrizze, hogy a korábban definiált kérelem URI-feltételei (azaz a forrás & mintája) nem ütköznek-e a szolgáltatáshoz megadott egyezési feltételekkel. Határozza meg a mintát; Ha üres értéket használ mintázatként, a rendszer az összes karakterláncot egyezteti.
 Cél  |Adja meg azt a relatív URL-címet, amelyre a fenti kérelmeket át kívánja írni: <br/>    1. Válassza ki a forrás-kiszolgálót azonosító tartalom-hozzáférési pontot. <br/>    2. relatív elérési út definiálása a használatával: <br/>        – Reguláris kifejezés mintája <br/>        - [HTTP-változók](cdn-http-variables.md) <br/> <br/> Helyettesítse be a forrás mintájában rögzített értékeket a cél mintába a $_n_ értékkel, ahol _n_ a rögzített sorrend szerint azonosít egy értéket. Például a $1 a forrás mintában rögzített első értéket jelöli, míg a $2 a második értéket jelöli.

 Ez a funkció lehetővé teszi, hogy a pop-ban újra lehessen írni az URL-címet anélkül, hogy hagyományos átirányítás lenne. A kérelmező ugyanazt a hibakódot kapja, mint ha az újramegírt URL-címet kérték.

**1. példa**

Ez a példa azt szemlélteti, hogyan lehet átirányítani egy olyan peremhálózati CNAME URL-címet, amely feloldja ezt az alap CDN URL-címet: http: \/ /marketing.azureedge.net/brochures/

A megfelelő kéréseket a rendszer átirányítja erre az alapszintű CNAME URL-címre: http: \/ /MyOrigin.azureedge.net/Resources/

Az URL-átirányítás a következő konfiguráción keresztül érhető el: ![ URL-átirányítás](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**2. minta példa**

Ebből a példából megtudhatja, hogyan irányíthatja át a peremhálózati CNAME URL-címet a kisbetűsről a kisbetűsre reguláris kifejezések használatával.

Az URL-átirányítás a következő konfiguráción keresztül érhető el: ![ URL-átirányítás](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)

**Összefoglalás:**

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
## <a name="next-steps"></a>További lépések

- [Szabályok motor referenciája](cdn-verizon-premium-rules-engine-reference.md)
- [Szabálymotor feltételes kifejezései](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Szabálymotor egyezési feltételei](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [HTTP-viselkedés felülbírálása a szabályok motor használatával](cdn-verizon-premium-rules-engine.md)
- [Azure CDN áttekintése](cdn-overview.md)
