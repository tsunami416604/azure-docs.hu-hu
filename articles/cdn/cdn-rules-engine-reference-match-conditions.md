---
title: Az Azure CDN szabályok motor egyezés feltételek |} Microsoft Docs
description: Az Azure Content Delivery Network referenciadokumentációt szabályok motor egyeztetési feltételeknek.
services: cdn
documentationcenter: ''
author: Lichard
manager: akucer
editor: ''
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: rli
ms.openlocfilehash: f8dac5469e7160fae93e8251ab7f4195a383f8b4
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Az Azure CDN szabálymotor feltételek egyeznek 
Ez a cikk a rendelkezésre álló egyezés feltételek esetében az Azure Content Delivery Network (CDN) részletes leírását tartalmazza [szabálymotor](cdn-rules-engine.md).

A szabály második része a egyezés feltétel. Egy egyezés feltétel azonosítja az adott típusú kérelmet, amely funkciókat érvényesül.

Például egyezést feltételt is használhatja:
- A tartalom egy adott helyen kérelmek szűrése.
- Egy adott IP-cím vagy ország alapján generált kérelmek szűrése.
- Kérések szűrése fejléc-információ.

## <a name="always-match-condition"></a>Az állapot mindig megfelelő

A mindig egyezés feltétel alapértelmezés szerinti szolgáltatások összes kérelem vonatkozik.

Name (Név) | Cél
-----|--------
[Mindig](#always) | Szolgáltatások alapértelmezett készlete vonatkozik minden kérelemhez.

## <a name="device-match-condition"></a>Eszköz egyezés feltétel

Az eszköz egyezés feltétel kérelmek egy mobileszközről tulajdonságai alapján azonosítja.  

Name (Név) | Cél
-----|--------
[Device](#device) | Kérelmek egy mobileszközről tulajdonságai alapján azonosítja.

## <a name="location-match-conditions"></a>Hely egyezés feltételek

A hely egyezés feltételek kérelmet a kérelmező helye alapján azonosíthatja.

Name (Név) | Cél
-----|--------
[SZÁMOT](#as-number) | Egy adott hálózati kérelmekkel azonosítja.
[Ország](#country) | A megadott országokból kérelmekkel azonosítja.

## <a name="origin-match-conditions"></a>Forrás egyezés feltételek

A forrás egyezés feltételek azonosítása kérelmek Content Delivery Network tároló- és egy ügyfél eredeti kiszolgálóra mutasson.

Name (Név) | Cél
-----|--------
[CDN-forrása](#cdn-origin) | A tartalomhoz tartalom Delivery Network storage-ban tárolt azonosítja.
[Ügyfél forrása](#customer-origin) | Egy adott ügyfélhez forrás kiszolgálón lévő tartalomhoz azonosítja.

## <a name="request-match-conditions"></a>Feltételek egyeznek kérése

A kérelem egyezés feltételek kérelmet tulajdonságaik alapján azonosíthatja.

Name (Név) | Cél
-----|--------
[Ügyfél IP-címe](#client-ip-address) | Egy adott IP-címről kérelmekkel azonosítja.
[Cookie-k paraméter](#cookie-parameter) | Ellenőrzi a megadott értéket minden kérelemhez társított cookie-kat.
[Cookie-k paraméter Regex](#cookie-parameter-regex) | A cookie-k a megadott reguláris kifejezést minden kérelemhez társított ellenőrzi.
[Peremhálózati Cname](#edge-cname) | Azonosítja a kérelmeket, amelyek egy adott peremhálózati CNAME.
[Hivatkozó tartomány](#referring-domain) | A megadott állomásnevekről említett kérelmek azonosítja.
[Kérelem fejléc szövegkonstans](#request-header-literal) | A megadott fejléc-készlettel és egy megadott értéket tartalmazó kérések azonosítja.
[Kérelem fejléc Regex](#request-header-regex) | A megadott fejléc értéke, amely megfelel a megadott reguláris kifejezést tartalmazó kérések azonosítja.
[Kérelem fejléc helyettesítő](#request-header-wildcard) | A megadott fejléc értéke, amely megfelel a megadott minta tartalmazó kérések azonosítja.
[Kérési módszer](#request-method) | A HTTP-metódus azonosítja a kérelmeket.
[Kérelem séma](#request-scheme) | A HTTP protokoll azonosítja a kérelmeket.

## <a name="url-match-conditions"></a>URL-cím egyezik feltételek

Az URL-cím egyezik feltételek kérelmet az URL-címek alapján azonosíthatja.

Name (Név) | Cél
-----|--------
[URL-cím elérési út könyvtár](#url-path-directory) | Azonosítja a kéréseket a relatív elérési útja.
[URL-cím elérési út bővítmény](#url-path-extension) | A fájlnév-kiterjesztésük alapján azonosítja a kérelmeket.
[URL-cím elérési út fájlnév](#url-path-filename) | A fájl neve azonosítja a kérelmeket.
[URL-cím elérési út szövegkonstans](#url-path-literal) | Összehasonlítja a kérelem relatív elérési út és a megadott értéket.
[URL-cím elérési út Regex](#url-path-regex) | Összehasonlítja a kérelem relatív elérési útját a megadott reguláris kifejezés.
[URL-cím elérési út helyettesítő karakter](#url-path-wildcard) | Összehasonlítja a kérelmet a megadott minta relatív elérési útja.
[Lekérdezés-szövegkonstans URL-címe](#url-query-literal) | Összehasonlítja a lekérdezési karakterlánc és a megadott értéket.
[URL-cím lekérdezési paraméter](#url-query-parameter) | A megadott lekérdezési karakterlánc-paraméter értéke, amely megfelel a megadott minta tartalmazó kérések azonosítja.
[Lekérdezés Regex URL-címe](#url-query-regex) | A megadott lekérdezési karakterlánc-paraméter értéke, amely megfelel a megadott reguláris kifejezést tartalmazó kérések azonosítja.
[Lekérdezés helyettesítő URL-címe](#url-query-wildcard) | A megadott érték szemben a lekérdezési karakterlánc hasonlítja össze.


## <a name="reference-for-rules-engine-match-conditions"></a>Útmutató a szabályok motor egyezés feltételek

---
### <a name="always"></a>Mindig

A mindig egyezés feltétel alapértelmezés szerinti szolgáltatások összes kérelem vonatkozik.

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="as-number"></a>SZÁMOT 
Az AS számot hálózati határozza meg az autonóm rendszer számát (ASN). 

A **egyező**/**nem felel meg** beállítás meghatározza, hogy teljesülnek-e a feltételek, amelyek alapján feltétel egyezik a AS száma:
- **Megfelelő**: megköveteli, hogy az ügyfél hálózati ASN egyezik a megadott ASN-eket. 
- **Nem felel meg does**: megköveteli, hogy az ügyfél hálózati az ASN nem egyezik a megadott ASN-ek bármelyikét.

Kapcsolatos információkat:
- Adjon meg több ASN-eket határoló mindegyiken akár egy szóköz. Például 64514 64515 megfelel a kérelmeket, amelyek 64514 vagy 64515 érkeznek.
- Bizonyos kérelmek esetleg vissza érvényes ASN. A kérdőjel (?) fog egyezni a kérelmeket, amelynek érvényes ASN nem határozható meg.
- Adja meg a kívánt hálózathoz teljes ASN. Hiányos értékek nem lesz megfelelő.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cdn-origin"></a>CDN-forrása
A CDN-forrása egyezés feltétel nem teljesül, az alábbi két feltétel teljesülése esetén:
- A kért tartalom CDN tárolóból.
- A kérelem URI-azonosítója a tartalom hozzáférési pont (például /000001), ez a feltétel egyezik a megadott típusú használ:
  - CDN URL-címe: A kérelem URI-azonosítója a kiválasztott tartalom-hozzáférési pontot kell tartalmaznia.
  - Peremhálózati CNAME URL-címe: A kiválasztott tartalom-hozzáférési pont a megfelelő biztonsági CNAME konfigurációs kell mutatnia.
  
Kapcsolatos információkat:
 - A tartalom-hozzáférési pont azonosítja a szolgáltatást, amely a kért tartalom szolgálhat.
 - Ne használjon az és IF utasítást bizonyos egyezés feltételek kombinálhatók. Például egy ügyfél eredeti egyezik a feltétellel CDN származási egyezés feltétel kombinálásával hozna létre egy egyező mintát, amely soha nem feleltethető. Ezért két CDN származási egyezés feltételek nem használható együtt az és IF utasítást keresztül.

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="client-ip-address"></a>Ügyfél IP-címe
A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amelyek alapján az ügyfél IP-cím egyezik feltétel teljesül:
- **Megfelelő**: megköveteli, hogy az ügyfél IP-cím megegyezik a megadott IP-címek egyikére. 
- **Nem felel meg does**: megköveteli, hogy az ügyfél IP-cím nem egyezik a megadott IP-címek valamelyikét. 

Kapcsolatos információkat:
- Használja a CIDR-formátumban.
- Adjon meg több IP-címek és/vagy az IP-címblokkok határoló mindegyiken akár egy szóköz. Példa:
  - **IPv4-alapú példa**: 1.2.3.4 10.20.30.40 megegyezik bármilyen címről vagy 1.2.3.4 vagy 10.20.30.40 érkező kérelmeket.
  - **IPv6-alapú példa**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 megegyezik a cím 1:2:3:4:5:6:7:8 vagy 10:20:30:40:50:60:70:80 érkező kérelmeket.
- Az IP-Címblokk szintaxisa a következő perjellel, majd a előtag mérete alap IP-címét. Példa:
  - **IPv4-alapú példa**: 5.5.5.64/26 megegyezik bármilyen 5.5.5.64 keresztül 5.5.5.127 címekről érkező kérelmeket.
  - **IPv6-alapú példa**: 1:2:3: / 48 megegyezik bármilyen címek 1:2:3:0:0:0:0:0 1:2:3:ffff:ffff:ffff:ffff:ffff keresztül érkező kérelmeket.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter"></a>Cookie-k paraméter
A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt a cookie-k paraméter felel meg a feltétel teljesül.
- **Megfelelő**: vonatkozó kérelmet tartalmaz, amely megfelel az egyeztetés feltétel meghatározott értékek közül legalább egy értéket a megadott cookie-t igényel.
- **Nem egyezik meg**: megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott cookie-k nem tartalmaz.
  - A megadott cookie-t tartalmaz, de az érték nem egyezik meg bármely egyezés feltétel definiált eleme.
  
Kapcsolatos információkat:
- Cookie-név: 
  - Helyettesítő értékek, beleértve a csillag (*), nem támogatottak, ha a cookie-nevet ad meg, mert csak a pontos cookie neve megegyezik jogosultak az összehasonlításhoz.
  - E egyezés feltétel példányonként csak egy egyetlen cookie neve adható meg.
  - Cookie neve összehasonlítások nem különböztetik meg.
- Cookie-értéket: 
  - Adjon meg több cookie-k érték határoló mindegyiken akár egy szóköz.
  - A cookie-értéket kihasználhatják a [helyettesítő értékek](cdn-rules-engine-reference.md#wildcard-values). 
  - Ha egy helyettesítő karakteres érték nincs megadva, csak pontos egyezést kielégítéséhez ez egyeznek az állapot. Például "Érték" meg fog egyezni a "Érték", de nem "Érték1" vagy "Érték2."
  - Használja a **esetben figyelmen kívül hagyása** lehetőséget szabályozhatja, hogy kis-és nagybetűket összehasonlítást a kérelem cookie-k értékkel.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter-regex"></a>Cookie-k paraméter Regex
A cookie-k paramétert reguláris kifejezéssel egyező feltétel határozza meg, a cookie nevét és értékét. Használhat [reguláris kifejezések](cdn-rules-engine-reference.md#regular-expressions) kívánt cookie-k értékét. 

A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt a cookie-k paraméter Regex felel meg a feltétel teljesül.
- **Megfelelő**: kérelem tartalmazza a megadott cookie-k a megadott reguláris kifejezésnek megfelelő értéket igényel.
- **Nem egyezik meg**: megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott cookie-k nem tartalmaz.
  - A megadott cookie-t tartalmaz, de az érték nem egyezik a megadott reguláris kifejezés.
  
Kapcsolatos információkat:
- Cookie-név: 
  - A reguláris kifejezések és a helyettesítő értékek, beleértve a csillag (*), nem támogatottak, ha egy cookie-nevet ad meg, mert csak a pontos cookie neve megegyezik jogosultak az összehasonlításhoz.
  - E egyezés feltétel példányonként csak egy egyetlen cookie neve adható meg.
  - Cookie neve összehasonlítások nem különböztetik meg.
- Cookie-értéket: 
  - Cookie-k érték reguláris kifejezéseket is előnyeit.
  - Használja a **esetben figyelmen kívül hagyása** lehetőséget szabályozhatja, hogy kis-és nagybetűket összehasonlítást a kérelem cookie-k értékkel.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

--- 
### <a name="country"></a>Ország
Megadhat egy ország keresztül az országhívószámot. 

A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt az ország felel meg a feltétel teljesül:
- **Egyezések**: az adott ország kód értékeket tartalmazó kérelem igényel. 
- **Nem egyezik meg**: megköveteli, hogy a kérelem nem tartalmazza az adott ország érték.

Kapcsolatos információkat:
- Adjon meg több országhívószámok határoló mindegyiken akár egy szóköz.
- Helyettesítő karakterek nem támogatottak, ha a országkódot ad meg.
- A "EU" és "AP" országhívószámok nem tartoznak bele azokban a régiókban található összes IP-címet.
- Bizonyos kérelmek esetleg vissza érvényes országkódot. A kérdőjel (?) fog egyezni a kérelmeket, amelynek érvényes országkódot nem határozható meg.
- Országhívó számokat-és nagybetűk.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Végrehajtási ország szerinti szűrés a szabályok használata
Ez a feltétel egyezik, amelyből a kérelem származási helye alapján testreszabások számos végrehajtását teszi lehetővé. Például az ország szerinti szűrés funkció működését replikálhatók a következő konfigurációs keresztül:

- URL-cím elérési út helyettesítő egyezés: állítsa be a [URL-cím elérési út helyettesítő felel meg a feltétel](#url-path-wildcard) ahhoz a könyvtárhoz, megfelelően történik. 
    A csillag karakter hozzáfűzése annak érdekében, hogy ez a szabály összes gyermekeinek lesz korlátozva relatív elérési út végéhez.

- Ország egyezik: az ország egyezés feltétel beállítása a kívánt készletet országok.
   - Engedélyezése: Állítsa be az ország felel meg az állapotot **nem felel meg** engedélyezi a megadott URL-cím elérési út helyettesítő egyezés feltétel helyen tárolt tartalmat csak a meghatározott országok eléréséhez.
   - Blokkolás: Állítsa be az ország felel meg az állapotot **egyező** letilthatja a megadott országok elérjék a tartalmat a megadott URL-cím elérési út helyettesítő egyezés feltétel helyen tárolja.

- Megtagadás Access (403-as) szolgáltatás: Engedélyezze a [hozzáférés megtagadása (403-as) szolgáltatás](cdn-rules-engine-reference-features.md#deny-access-403) replikálni az ország szerinti szűrés szolgáltatás engedélyezési vagy tiltólista része.

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="customer-origin"></a>Ügyfél forrása

Kapcsolatos információkat: 
- Az ügyfél eredeti egyezés feltétel teljesül, függetlenül attól, hogy tartalom CDN URL-címet vagy CNAME mutató URL-CÍMMEL a kijelölt ügyfél forrás él keresztül van szükség.
- A szabály által hivatkozott felhasználói származási konfigurálása az ügyfél eredeti oldalról nem törölhető. Mielőtt megkísérli az ügyfél eredeti konfiguráció törlése, győződjön meg arról, hogy a következő konfigurációk nem hivatkozni rá:
  - Egy ügyfél eredeti egyezés feltétel
  - Egy peremhálózati CNAME-konfiguráció
- Ne használjon az és IF utasítást bizonyos egyezés feltételek kombinálhatók. Például a CDN-forrása egyezik a feltétellel ügyfél eredeti egyezés feltétel kombinálásával hozna létre egy egyező mintát, amely soha nem feleltethető. Ezért két felhasználói származási egyezés feltétel nem használható együtt keresztül az és IF utasítást.

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="device"></a>Eszköz

Az eszköz egyezés feltétel kérelmek egy mobileszközről tulajdonságai alapján azonosítja. Mobil eszköz észlelési sorrendekben [WURFL](http://wurfl.sourceforge.net/). 

A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt az eszköz felel meg a feltétel teljesül:
- **Egyezések**: a megadott értéknek felel meg a kérelmező eszközt. 
- **Nem felel meg does**: megköveteli, hogy az igénylő eszköz nem egyezik a megadott érték.

Kapcsolatos információkat:

- Használja a **esetben figyelmen kívül hagyása** beállítással adhatja meg, hogy a megadott érték kis-és nagybetűket.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

#### <a name="string-type"></a>Karakterlánc típusú
Egy WURFL képesség általában tetszőleges számok, betűk és szimbólumok kombinációját fogad el. A rugalmas jellegű, ezt a képességet, mert ki kell választania a egyezés probléma társított értéket értelmezését. A következő táblázat ismerteti a rendelkezésre álló beállításokat:

Típus     | Leírás
---------|------------
Literális  | Ezzel a beállítással megakadályozhatja, hogy a legtöbb karakternél a használatával a speciális jelentéssel véve a [literálérték](cdn-rules-engine-reference.md#literal-values).
Wildcard | Válassza ezt a beállítást, minden [helyettesítő karakterek] kihasználását ([helyettesítő értékek](cdn-rules-engine-reference.md#wildcard-values).
Regex    | Ezt a beállítást használja [reguláris kifejezések](cdn-rules-engine-reference.md#regular-expressions). A reguláris kifejezések hasznosak mintázat létrehozása karaktereket.

#### <a name="wurfl-capabilities"></a>WURFL képességek
Egy WURFL képesség, amely ismerteti a mobil eszközök kategória hivatkozik. A kiválasztott funkció határozza meg a mobileszköz leírást, amely azonosítja a kérelmeket.

A következő táblázat WURFL képességek és ezek változói a szabályok motor.
<br>
> [!NOTE] 
> A következő változók használhatók a **ügyfél kérelem fejléc módosítása** és **módosítása ügyfél válaszfejléc** szolgáltatásokat.

Képesség | Változó | Leírás | Példaértékek
-----------|----------|-------------|----------------
Márka neve | %{wurfl_cap_brand_name} | Egy karakterlánc, amely az eszköz márka nevét jelöli. | Samsung
Az eszköz operációs rendszere | %{wurfl_cap_device_os} | Egy karakterlánc, amely azt jelzi, az operációs rendszer telepítve az eszközön. | IOS
Eszköz operációs rendszerének verziója | %{wurfl_cap_device_os_version} | Egy karakterlánc, amely azt jelzi, az eszköz a telepített operációs rendszer verziószámát. | 1.0.1
Kettős tájolását | %{wurfl_cap_dual_orientation} | Egy logikai érték, amely jelzi, hogy az eszköz támogatja-e kettős tájolását. | true
HTML előnyben részesített DTD | %{wurfl_cap_html_preferred_dtd} | Egy olyan karakterlánc, amely jelzi a mobil eszköz előnyben részesített dokumentumtípusdefiníció (DTD) a HTML-tartalmakat. | nincs<br/>xhtml_basic<br/>HTML5
A kép Inlining | %{wurfl_cap_image_inlining} | Olyan logikai érték, amely jelzi, hogy az eszköz támogatja-e a Base64 kódolású képek. | false
Is Android | %{wurfl_vcap_is_android} | Egy logikai érték, amely azt jelzi, hogy az eszköz az Android operációs rendszer használja-e. | true
IOS | %{wurfl_vcap_is_ios} | Egy logikai érték, amely azt jelzi, hogy az eszköz iOS használja-e. | false
Az intelligens TV | %{wurfl_cap_is_smarttv} | Egy logikai érték, amely azt jelzi, hogy az eszköz egy intelligens TV. | false
Smartphone van | %{wurfl_vcap_is_smartphone} | Egy logikai érték, amely azt jelzi, hogy az eszköz okostelefont. | true
Tábla van | %{wurfl_cap_is_tablet} | Egy logikai érték, amely azt jelzi, hogy az eszköz egy tábla. A leírás megadása az operációs rendszer független. | true
Vezeték nélküli eszköz | %{wurfl_cap_is_wireless_device} | Egy logikai érték, amely azt jelzi, hogy az eszköz vezeték nélküli eszköz nem megfelelőnek. | true
Marketing neve | %{wurfl_cap_marketing_name} | Egy karakterlánc, amely megadja, hogy az eszköz marketing néven. | BlackBerry 8100 Pearl
Mobil böngésző | %{wurfl_cap_mobile_browser} | Egy karakterlánc, amely azt jelzi, a böngésző, amelynek használatával a tartalmat igénylő az eszközről. | Chrome
Mobil böngészőverzió | %{wurfl_cap_mobile_browser_version} | A böngészőben tartalom kérhet az eszköz használt verzióját jelző karakterlánc. | 31
Modell neve | %{wurfl_cap_model_name} | Egy karakterlánc, amely azt jelzi, az eszköz modell neve. | s3
Progresszív letöltés | %{wurfl_cap_progressive_download} | Egy logikai érték, amely jelzi, hogy az eszköz támogatja-e a audió és videó lejátszását, miközben továbbra is letöltése. | true
Kiadás dátuma | %{wurfl_cap_release_date} | Egy olyan karakterlánc, amely jelzi a kiválasztott évhez és hónaphoz, amelyen az eszköz WURFL adatbázisba lett hozzáadva.<br/><br/>Formátum: `yyyy_mm` | 2013_december
Megoldási magassága | %{wurfl_cap_resolution_height} | Az eszköz magassága képpontban jelző egész számot. | 768
Megoldási szélessége | %{wurfl_cap_resolution_width} | Az eszköz szélességét képpontban jelző egész számot. | 1024

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="edge-cname"></a>Peremhálózati Cname
Kapcsolatos információkat: 
- A rendelkezésre álló biztonsági CNAME listáját ezen peremhálózati CNAME a platform, amelyen a szabálymotor konfigurálva van, a peremhálózati CNAME lapon konfigurált korlátozódik.
- Mielőtt megkísérli egy peremhálózati CNAME-konfiguráció törlése, ellenőrizze, hogy egy biztonsági Cname egyezés állapot nem hivatkozik. Peremhálózati CNAME konfigurációt a szabályban definiált nem lehet törölni, a peremhálózati CNAME lapról. 
- Ne használjon az és IF utasítást bizonyos egyezés feltételek kombinálhatók. Például egy peremhálózati Cname egyezés állapot egy ügyfél eredeti egyezik a feltétellel kombinálásával hozna létre egy egyező mintát, amely soha nem feleltethető. Ezért két széle Cname egyezés feltétel nem használható együtt keresztül az és IF utasítást.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="referring-domain"></a>Hivatkozó tartomány
Az állomásnév társított a hivatkozó, amelyen keresztül a tartalmat a kért határozza meg, hogy a tartomány utaló feltétel teljesül. 

A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt az utaló tartomány felel meg a feltétel teljesül:
- **Egyezések**: a megadott értékekre hivatkozó állomásnév szükséges. 
- **Nem felel meg does**: megköveteli, hogy a hivatkozó állomásnév nem egyezik a megadott érték.

Kapcsolatos információkat:
- Adjon meg több állomásnevet határoló mindegyiken akár egy szóköz.
- Ez a feltétel egyezik támogatja [helyettesítő értékek](cdn-rules-engine-reference.md#wildcard-values).
- Ha a megadott érték nem tartalmaz egy csillag, a hivatkozó névhez pontos egyezésűnek kell lennie. Például "tartomany.com" megadása nem megfelelő "www.mydomain.com."
- Használja a **esetben figyelmen kívül hagyása** lehetőséget annak vezérléséhez, hogy kis-és nagybetűket összehasonlítást.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-literal"></a>Kérelem fejléc szövegkonstans
A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt a kérelem fejlécében literális felel meg, a feltétel teljesül.
- **Egyezések**: a kérelem tartalmazza a megadott fejléc igényel. Az értékét meg kell egyeznie az egyeztetés feltétel definiált.
- **Nem egyezik meg**: megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott fejléc nem tartalmaz.
  - A megadott fejléc tartalmaz, de értéke nem egyezik a definiált ez egyeznek az állapot.
  
Kapcsolatos információkat:
- Mindig különböztetik meg a fejléc neve összehasonlítást. Használja a **esetben figyelmen kívül hagyása** lehetőséget a fejléc értéke összehasonlítást Kisbetű/nagybetű megkülönböztetése szabályozására.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-regex"></a>Kérelem fejléc Regex
A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt a kérelem fejlécében Regex felel meg a feltétel teljesül.
- **Egyezések**: a kérelem tartalmazza a megadott fejléc igényel. Az értéknek meg kell felelnie a megadott mintának a megadott [reguláris kifejezés](cdn-rules-engine-reference.md#regular-expressions).
- **Nem egyezik meg**: megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott fejléc nem tartalmaz.
  - A megadott fejléc tartalmaz, de az érték nem egyezik a megadott reguláris kifejezés.

Kapcsolatos információkat:
- Fejléc neve: 
  - Különböztetik meg a fejléc neve összehasonlítást.
  - Cserélje le a fejléc neve szóközöket "% 20." 
- Állomásfejléc-érték: 
  - Egy állomásfejléc-érték reguláris kifejezéseket is előnyeit.
  - Használja a **esetben figyelmen kívül hagyása** lehetőséget a fejléc értéke összehasonlítást Kisbetű/nagybetű megkülönböztetése szabályozására.
  - Csak akkor, ha a fejléc értéke pontosan egyezik a megadott minta legalább egyike a egyezés feltétel teljesül.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult 

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-header-wildcard"></a>Kérelem fejléc helyettesítő
A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt a kérelem fejlécében helyettesítő felel meg a feltétel teljesül.
- **Egyezések**: a kérelem tartalmazza a megadott fejléc igényel. Az értékét meg kell egyeznie az egyeztetés feltétel meghatározott értékek közül legalább egy.
- **Nem egyezik meg**: megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott fejléc nem tartalmaz.
  - A megadott fejléc tartalmaz, de az érték nem egyezik a megadott értékek valamelyikét.
  
Kapcsolatos információkat:
- Fejléc neve: 
  - Különböztetik meg a fejléc neve összehasonlítást.
  - A fejléc neve szóközt kell írni a "% 20." Ez az érték használatával adja meg a szóközöket a fejléc értéke.
- Állomásfejléc-érték: 
  - A fejléc értéke kihasználhatják a [helyettesítő értékek](cdn-rules-engine-reference.md#wildcard-values).
  - Használja a **esetben figyelmen kívül hagyása** lehetőséget a fejléc értéke összehasonlítást Kisbetű/nagybetű megkülönböztetése szabályozására.
  - Az egyeztetés feltétel teljesül fejléc értéke pontosan megegyezik a megadott minta legalább egyikére.
  - Adjon meg több érték határoló mindegyiken akár egy szóköz.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-method"></a>Kérési módszer
A metódus egyezés feltétel nem teljesül, csak akkor, ha a kijelölt kérelem metódussal kért eszközök. A rendelkezésre álló kérelem megoldások a következők:
- GET
- HEAD 
- POST 
- BEÁLLÍTÁSOK 
- A PUT 
- DELETE 
- NYOMKÖVETÉSI 
- CSATLAKOZÁS 

Kapcsolatos információkat:
- Alapértelmezés szerint csak a GET kérelem metódus hozhat létre a gyorsítótárazott tartalom a hálózaton. Minden más kérelem módszereket küldése a proxyn keresztül a hálózaton keresztül.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-scheme"></a>Kérelem séma
A rendszer kérelem egyezés feltétel nem teljesül, csak akkor, ha a kiválasztott protokollal kért eszközök. A rendelkezésre álló protokollok a következők: 
- HTTP
- HTTPS

Kapcsolatos információkat:
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-directory"></a>URL-cím elérési út könyvtár
Azonosítja a kérelmet a relatív elérési utat, amely nem tartalmazza a kért objektum nevét.

A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt az URL-cím elérési út Directory felel meg a feltétel teljesül.
- **Megfelelő**: szükséges lehet relatív URL-cím elérési út, kivéve a fájl nevét, a megadott URL-cím bizonyos mintázatnak megfelelő a kérelmet.
- **Nem felel meg does**: tartalmaz egy relatív URL-címe, kivéve a fájl nevét, nem egyezik a megadott URL-cím minta kérelem igényel.

Kapcsolatos információkat:
- Használja a **viszonyítva** beállítással adhatja meg, hogy az URL-cím összehasonlítás előtt vagy után a tartalom-hozzáférési pont indításakor. A tartalom-hozzáférési pont, amely a Verizon CDN állomásnév és a relatív elérési útját a kért objektum (például /800001/CustomerOrigin) elérési út része. A kiszolgáló típusa (például a CDN-t vagy az ügyfelek forrás) által és a felhasználói fiók száma azonosítja.

   A következő értékek érhetők el a **viszonyítva** lehetőséget:
   - **Legfelső szintű**: azt jelzi, hogy az URL-cím összehasonlítás elkezdi közvetlenül a CDN állomásnév után. 

     Például: http:\//wpc.0001.&lt; tartomány&gt;/**800001/myorigin/SajátMappa**/index.htm

   - **Forrás**: azt jelzi, hogy az URL-cím összehasonlítás elkezdi a tartalom-hozzáférési pont (például /000001 vagy/800001/myorigin) után. Mivel a \*. azureedge.net CNAME képest a Verizon CDN állomásnév származási könyvtárához alapértelmezés szerint létrejön, Azure CDN felhasználónak kell használnia a **származási** érték. 

     Például: https:\//&lt;végpont&gt;.azureedge.net/**SajátMappa**/index.htm 

     Az URL-cím a következő Verizon CDN állomásnév mutat: http:\//wpc.0001.&lt; tartomány&gt;/800001/myorigin/**SajátMappa**/index.htm

- CNAME URL-cím él az URL-cím összehasonlítás előtt CDN URL-címre van írni.

    Például mind a következő URL-címeket az azonos eszköz mutasson, így rendelkezik azonos URL-címet.
    - CDN URL-címe: http:\//wpc.0001.&lt; tartomány&gt;/800001/CustomerOrigin/path/asset.htm
    
    - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    További információ:
    - Az egyéni tartomány: https:\//my.domain.com/path/asset.htm
    
    - URL-címe (gyökeréhez viszonyítva): / 800001/CustomerOrigin/path /
    
    - URL-címe (forrás) viszonyítva: /path/

- A URL-cím összehasonlítás véget ér, csak a fájl nevét, a kért eszköz előtt használt URL-CÍMÉT a részét. A záró perjelet Ez a típus elérési útjának utolsó karaktere.
    
- Cserélje le az URL-cím elérési út mintája szereplő "% 20."
    
- Minden egyes URL-cím elérési út mintája tartalmazhat egy vagy több csillagot (*), ha minden csillag megegyezik a karaktersorozatot, mely egy vagy több.
    
- Megadhatja, több URL-cím elérési út a minta határoló mindegyiken akár egy szóköz.

    Például: * /sales/ * /marketing/

- URL-cím elérési utat kihasználhatják a [helyettesítő értékek](cdn-rules-engine-reference.md#wildcard-values).

- Használja a **esetben figyelmen kívül hagyása** vezérlőhöz lehetőséget, hogy egy kis-és nagybetűket összehasonlítás történik.

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-extension"></a>URL-cím elérési út bővítmény
Azonosítja a kérelmeket a kért eszköz fájl kiterjesztése.

A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt az URL-cím elérési út bővítmény felel meg a feltétel teljesül.
- **Megfelelő**: a kérelem tartalmazza a fájlnév kiterjesztését, a megadott minta egyezik az URL-címet igényel.

   Például ha megadja a "htm", "htm" eszközök teljesül, azonban nem "html" eszközök.  

- **Nem felel meg does**: az URL-kérelmet tartalmaz, amely nem egyezik a megadott minta fájlkiterjesztés igényel.

Kapcsolatos információkat:
- Adja meg a kívánt fájlkiterjesztéseket az megegyeznek a **érték** mezőbe. Nem tartalmaznak ponttal; például használni htm .htm helyett.

- Használja a **esetben figyelmen kívül hagyása** vezérlőhöz lehetőséget, hogy egy kis-és nagybetűket összehasonlítás történik.

- Adjon meg több fájlkiterjesztések határoló a egyes kiterjesztések az egy szóköz. 

    Például: htm html

- Például adja meg a "htm" megfelel "htm" eszközök, de nem a "html" eszközök.


#### <a name="sample-scenario"></a>Mintaforgatókönyv

Az alábbi minta-konfiguráció azt feltételezi, hogy ez egyezés a feltétel teljesül-e, amikor egy kérelem megfelel a megadott egyikével.

Érték megadását: asp aspx php html

Ha úgy találja, hogy a következő kiterjesztések URL-címeket a egyezés feltétel teljesül:
- .asp
- .aspx
- .php
- .html

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-filename"></a>URL-cím elérési út fájlnév
A fájlnév, a kért eszköz azonosítja a kérelmeket. Ez egyeznek az állapot céljából a fájl nevét a kért eszköz, időtartamra és a fájlnévkiterjesztés (például index.html) nevéből áll.

A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt az URL-cím elérési út fájlnév felel meg a feltétel teljesül.
- **Megfelelő**: a kérelem tartalmazza a fájl nevét az URL-címet, amely megfelel a megadott minta a szükséges.
- **Nem felel meg does**: a kérelem tartalmazza a fájl nevét az URL-címet, amely nem egyezik a megadott minta a szükséges.

Kapcsolatos információkat:
- Használja a **esetben figyelmen kívül hagyása** vezérlőhöz lehetőséget, hogy egy kis-és nagybetűket összehasonlítás történik.

- Több fájlkiterjesztések megadásához külön a egyes kiterjesztések az egy szóköz.

    Például: index.HTML index.html

- Cserélje le egy fájl név-érték szóközt "% 20."
    
- A fájl név-érték kihasználhatják a [helyettesítő értékek](cdn-rules-engine-reference.md#wildcard-values). Például minden Fájlnévminta tartalmazhat egy vagy több csillagot (*), ha minden csillag megegyezik a karaktersorozatot, mely egy vagy több.
    
- Ha a helyettesítő karakterek nincsenek megadva, csak pontos egyezést kielégítéséhez ez egyeznek az állapot.

    Például adja meg a "bemutató.ppt" megfelel egy eszköz nevű, "bemutató.ppt", de nem egy elnevezett "presentation.pptx."

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-literal"></a>URL-cím elérési út szövegkonstans
Összehasonlítja a kérelem URL-címe, beleértve a fájlnevet, és a megadott értéket.

A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt az URL-cím elérési út literális felel meg, a feltétel teljesül.
- **Megfelelő**: tartalmaz egy URL-címet, amely megfelel a megadott minta kérelem igényel.
- **Nem felel meg does**: tartalmaz egy URL-címet, amely nem egyezik a megadott minta kérelem igényel.

Kapcsolatos információkat:
- Használja a **viszonyítva** beállítással adhatja meg, hogy az URL-cím összehasonlítás elkezdi előtt vagy után a tartalom-hozzáférési pont. 

    A következő értékek érhetők el a **viszonyítva** lehetőséget:
     - **Legfelső szintű**: azt jelzi, hogy az URL-cím összehasonlítás elkezdi közvetlenül a CDN állomásnév után.

       Például: http:\//wpc.0001.&lt; tartomány&gt;/**800001/myorigin/myfolder/index.htm**

     - **Forrás**: azt jelzi, hogy az URL-cím összehasonlítás elkezdi a tartalom-hozzáférési pont (például /000001 vagy/800001/myorigin) után. Mivel a \*. azureedge.net CNAME képest a Verizon CDN állomásnév származási könyvtárához alapértelmezés szerint létrejön, Azure CDN felhasználónak kell használnia a **származási** érték. 

       Például: https:\//&lt;végpont&gt;.azureedge.net/**myfolder/index.htm**

     Az URL-cím a következő Verizon CDN állomásnév mutat: http:\//wpc.0001.&lt; tartomány&gt;/800001/myorigin/**myfolder/index.htm**

- CNAME URL-cím él egy URL-cím összehasonlítás előtt CDN URL-címre van írni.

    Például egyaránt a következő URL-címeket az azonos eszköz mutasson, és így az azonos URL-címet rendelkezik:
    - CDN URL-címe: http:\//wpc.0001.&lt; tartomány&gt;/800001/CustomerOrigin/path/asset.htm
    - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    További információ:
    
    - URL-címe (gyökeréhez viszonyítva): /800001/CustomerOrigin/path/asset.htm
   
    - URL-címe (forrás) viszonyítva: /path/asset.htm

- Lekérdezési karakterláncok a URL-címben a rendszer figyelmen kívül hagyja.
- Használja a **esetben figyelmen kívül hagyása** vezérlőhöz lehetőséget, hogy egy kis-és nagybetűket összehasonlítás történik.
- Ez a feltétel egyezik a rendszer összehasonlítja a relatív elérési útja a pontos kérelmet az ügyfél által megadott érték.

- Egy adott könyvtár intézett összes kérelem kereséséhez használja a [URL-cím elérési út Directory](#url-path-directory) vagy a [URL-cím elérési út helyettesítő](#url-path-wildcard) feltételének.

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-regex"></a>URL-cím elérési út Regex
A kérelem URL-címe a megadott összehasonlítja [reguláris kifejezés](cdn-rules-engine-reference.md#regular-expressions).

A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt az URL-cím elérési út Regex felel meg a feltétel teljesül.
- **Megfelelő**: igényel a kérelem egy URL-címet, amely megfelel a megadott reguláris kifejezést tartalmaz.
- **Nem felel meg does**: igényel a kérelem egy URL-címet, amely nem egyezik a megadott reguláris kifejezést tartalmaz.

Kapcsolatos információkat:
- CNAME URL-cím él URL-cím összehasonlítás előtt CDN URL-címre van írni. 
 
    Például mindkét URL-címeket az azonos eszköz mutasson, és így az azonos URL-címet rendelkezik.

     - CDN URL-címe: http:\//wpc.0001.&lt; tartomány&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm
    
    További információ:
    
     - URL-címe: /800001/CustomerOrigin/path/asset.htm

- Lekérdezési karakterláncok a URL-címben a rendszer figyelmen kívül hagyja.
    
- Használja a **esetben figyelmen kívül hagyása** vezérlőhöz lehetőséget, hogy egy kis-és nagybetűket összehasonlítás történik.
    
- Az URL-cím elérési út szóközöket le kell cserélni "% 20."

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-path-wildcard"></a>URL-cím elérési út helyettesítő karakter
Összehasonlítja a kérelem relatív URL-cím elérési útját a megadott helyettesítő karakterrel.

A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt az URL-cím elérési út helyettesítő karakter felel meg a feltétel teljesül.
- **Megfelelő**: igényel az egy URL-címet, amely megfelel a megadott helyettesítő karakterrel tartalmaz a kérelem.
- **Nem felel meg does**: igényel az egy URL-címet, amely nem egyezik a megadott helyettesítő karakterrel tartalmaz a kérelem.

Kapcsolatos információkat:
- **A relatív** beállítás: Ez a beállítás azt határozza meg, hogy az URL-cím összehasonlítás elkezdi előtt vagy után a tartalom-hozzáférési pont.

   Ez a beállítás a következő értékeket veheti fel:
     - **Legfelső szintű**: azt jelzi, hogy az URL-cím összehasonlítás elkezdi közvetlenül a CDN állomásnév után.

       Például: http:\//wpc.0001.&lt; tartomány&gt;/**800001/myorigin/myfolder/index.htm**

     - **Forrás**: azt jelzi, hogy az URL-cím összehasonlítás elkezdi a tartalom-hozzáférési pont (például /000001 vagy/800001/myorigin) után. Mivel a \*. azureedge.net CNAME képest a Verizon CDN állomásnév származási könyvtárához alapértelmezés szerint létrejön, Azure CDN felhasználónak kell használnia a **származási** érték. 

       Például: https:\//&lt;végpont&gt;.azureedge.net/**myfolder/index.htm**

     Az URL-cím a következő Verizon CDN állomásnév mutat: http:\//wpc.0001.&lt; tartomány&gt;/800001/myorigin/**myfolder/index.htm**

- CNAME URL-cím él URL-cím összehasonlítás előtt CDN URL-címre van írni.

    Például egyaránt a következő URL-címeket az azonos eszköz mutasson, és így az azonos URL-címet rendelkezik:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    További információ:
    
     - URL-címe (gyökeréhez viszonyítva): /800001/CustomerOrigin/path/asset.htm
    
     - URL-címe (forrás) viszonyítva: /path/asset.htm
    
- Adjon meg több URL-cím elérési út határoló mindegyiken akár egy szóköz.

   Például: /marketing/asset.* /sales/*.htm

- Lekérdezési karakterláncok a URL-címben a rendszer figyelmen kívül hagyja.
    
- Használja a **esetben figyelmen kívül hagyása** vezérlőhöz lehetőséget, hogy egy kis-és nagybetűket összehasonlítás történik.
    
- Cserélje le az URL-cím elérési út szóközöket "% 20."
    
- Egy URL-címe kihasználhatják a megadott [helyettesítő értékek](cdn-rules-engine-reference.md#wildcard-values). Minden egyes URL-cím elérési út mintája tartalmazhat egy vagy több csillagot (*), ahol minden csillag is megegyezhet, a karaktersorozatot, mely egy vagy több.

#### <a name="sample-scenarios"></a>Mintaforgatókönyvek

A minta-konfigurációja az alábbi táblázat azt feltételezik, hogy ez egyezés a feltétel teljesül-e, amikor egy kérelem megfelel a megadott URL-minta:

Érték                   | A viszonyítva    | Eredmény 
------------------------|----------------|-------
*/test.html */test.php  | A legfelső szintű vagy forrása | Ez a minta egyezik a kérelem "test.html" vagy "test.php" bármely mappában nevű eszközök.
/80ABCD/origin/text/*   | Gyökér           | Ez a minta egyezik, amikor a kért eszköz megfelel-e a következő feltételeknek: <br />-Kell lennie egy ügyfél forrás neve "origin." <br />– A relatív elérési út egy "szöveg" nevű mappát kell kezdődnie. Ez azt jelenti, hogy a kért eszköz elhelyezkedhet a "text" mappában vagy egy rekurzív almappája.
*/CSS/* */js/*          | A legfelső szintű vagy forrása | Ebben a mintában a rendszer megkeres minden CDN vagy peremhálózati egy css vagy js mappát tartalmazó CNAME URL-címeket.
*.jpg *.gif *.png       | A legfelső szintű vagy forrása | Ebben a mintában a rendszer megkeres minden CDN vagy peremhálózati CNAME URL-címek .jpg, .gif vagy .png végződő által. Adja meg ezt a mintát egy alternatív módja van a [URL-cím elérési út bővítmény felel meg a feltétel](#url-path-extension).
/ képek / * / media / *      | Forrás         | Ez a minta egyezik CDN vagy peremhálózati relatív elérési úton kezdődik-e egy "képek" vagy "media" mappa CNAME URL-címeket. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-literal"></a>Lekérdezés-szövegkonstans URL-címe
Összehasonlítja a lekérdezési karakterlánc és a megadott értéket.

A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt az URL-cím lekérdezés literális felel meg, a feltétel teljesül.
- **Megfelelő**: a kérelmet tartalmaz egy URL-lekérdezési karakterláncot, amely megfelel a megadott lekérdezési karakterlánc szükséges.
- **Nem felel meg does**: a kérelmet tartalmaz egy URL-lekérdezési karakterláncot, amely nem egyezik a megadott lekérdezési karakterlánc szükséges.

Kapcsolatos információkat:

- Csak a pontos lekérdezés karakterlánc megfelel a egyezés probléma kielégítéséhez.
    
- Használja a **esetben figyelmen kívül hagyása** lehetőséget a lekérdezési karakterláncok összehasonlítására Kisbetű/nagybetű megkülönböztetése szabályozására.
    
- A lekérdezési karakterlánc értéke szöveges bevezető kérdőjelet (?) nem tartalmaz.
    
- Bizonyos karakterek megkövetelése URL-kódolást. Használja az URL-címre százalékos szimbólum kódolás a következő karaktereket:

   Karakter | URL-kódolás
   ----------|---------
   Űr     | %20
   &         | %25

- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
   - Fejezze be a gyorsítótár kitöltés
   - Alapértelmezett belső maximális életkora
   - Kényszerített belső maximális életkora
   - Figyelmen kívül hagyja az eredeti No-Cache
   - Belső maximális elavult

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-parameter"></a>URL-cím lekérdezési paraméter
A megadott lekérdezési karakterlánc-paramétert tartalmazó kérések azonosítja. Ez a paraméter értéke egy érték, amely megegyezik a megadott mintával. Lekérdezési karakterlánc-paramétert (például paraméter = érték) a kérelem URL-CÍMÉT határozza meg, hogy ez a feltétel teljesül. Ez egyezés a feltétel azonosítja a lekérdezési karakterlánc paraméterként a neve, és egy vagy több értéket a paraméter értéke a fogad. 

A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt az URL-cím lekérdezési paraméter felel meg a feltétel teljesül.
- **Megfelelő**: vonatkozó kérelmet tartalmaz, amely megfelel az egyeztetés feltétel meghatározott értékek közül legalább egy értéket a megadott paraméter szükséges.
- **Nem egyezik meg**: megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott paraméter nem tartalmaz.
  - A megadott paraméter tartalmaz, de értéke nem egyezik meg bármely egyezés feltétel definiált eleme.

Ez a feltétel egyezik egyszerűen adja meg a név-érték paraméterkombinációk biztosít. További rugalmasságot biztosít a lekérdezési karakterlánc paraméterként megfelelő, ha fontolja meg a [URL-cím lekérdezés helyettesítő](#url-query-wildcard) feltételének.

Kapcsolatos információkat:
- Ez egyeznek az állapot-példányonként csak egy egyetlen URL-cím lekérdezési paraméter neve adható meg.
    
- Helyettesítő karakteres értékek használata nem támogatott, ha a paraméter neve meg van adva, mert csak a pontos paraméter neve megegyezik jogosultak az összehasonlításhoz.
- Paraméter értékének tartalmazhatnak [helyettesítő értékek](cdn-rules-engine-reference.md#wildcard-values).
   - Minden paraméter értéke minta egy vagy több csillagot (*), ahol minden csillag is megegyezhet, a karaktersorozatot, mely egy vagy több állhat.
   - Bizonyos karakterek megkövetelése URL-kódolást. Használja az URL-címre százalékos szimbólum kódolás a következő karaktereket:

       Karakter | URL-kódolás
       ----------|---------
       Űr     | %20
       &         | %25

- Adja meg a lekérdezési karakterlánc paraméter több értéket határoló mindegyiken akár egy szóköz. Az egyeztetés feltétel teljesül, amikor egy kérelem tartalmazza a megadott név-érték kombinációk egyikét.

   - 1. példa:

     - A konfiguráció:

       ValueA ValueB

     - Ez a konfiguráció megfelel a következő lekérdezési karakterlánc paramétereket:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - 2. példa

     - A konfiguráció: 

        % 20A érték % 20B érték

     - Ez a konfiguráció megfelel a következő lekérdezési karakterlánc paramétereket:

       Parameter1=Value%20A

       Parameter1=Value%20B

- Csak akkor van legalább egy, a megadott lekérdezési karakterlánc név-érték kombinációk pontos egyezést e egyezés feltétel teljesül.

   Például az előző példában a konfigurációt használja, ha a paraméter név-érték kombinációja "Parameter1 = ValueAdd" nem tekint egyezés. Azonban a következő értékek egyikét adja meg, ha azt fog egyezni, hogy a név/érték kombinációja:

   - ValueA ValueB ValueAdd
   - ValueA * ValueB

- Használja a **esetben figyelmen kívül hagyása** lehetőséget a lekérdezési karakterláncok összehasonlítására Kisbetű/nagybetű megkülönböztetése szabályozására.
    
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
   - Fejezze be a gyorsítótár kitöltés
   - Alapértelmezett belső maximális életkora
   - Kényszerített belső maximális életkora
   - Figyelmen kívül hagyja az eredeti No-Cache
   - Belső maximális elavult

#### <a name="sample-scenarios"></a>Használati példák
A következő példa bemutatja, hogyan működik ez a beállítás adott helyzetekben:

Name (Név)  | Érték |  Eredmény
------|-------|--------
Felhasználó  | Joe   | Ez a minta egyezik a kért URL-címhez tartozó lekérdezési karakterlánc esetén "? felhasználó = joe."
Felhasználó  | *     | Ez a minta egyezik, amikor a kért URL-címhez tartozó lekérdezési karakterlánc egy felhasználó paramétert tartalmaz.
E-mail | Joe\* | Ez a minta egyezik, amikor a kért URL-címhez tartozó lekérdezési karakterlánc a "Joe." kezdetű E-mail paramétert tartalmaz

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-regex"></a>Lekérdezés Regex URL-címe
A megadott lekérdezési karakterlánc-paramétert tartalmazó kérések azonosítja. Ez a paraméter értéke, amely megfelel a megadott érték [reguláris kifejezés](cdn-rules-engine-reference.md#regular-expressions).

A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt az URL-lekérdezés Regex felel meg a feltétel teljesül.
- **Megfelelő**: igényel a kérelem egy URL-lekérdezési karakterláncot, amely megfelel a megadott reguláris kifejezést tartalmaz.
- **Nem felel meg does**: igényel a kérelem egy URL-lekérdezési karakterláncot, amely nem egyezik a megadott reguláris kifejezést tartalmaz.

Kapcsolatos információkat:
- Csak a megadott reguláris kifejezésnek pontosan megegyezik ez egyeznek az állapot kielégítéséhez.
    
- Használja a **esetben figyelmen kívül hagyása** lehetőséget a lekérdezési karakterláncok összehasonlítására Kisbetű/nagybetű megkülönböztetése szabályozására.
    
- Ez a beállítás az alkalmazásában egy lekérdezési karakterlánc kezdődik-e az első karakter után a kérdőjel (?) elválasztó, a lekérdezési karakterlánc.
    
- Bizonyos karakterek megkövetelése URL-kódolást. Használja az URL-címre százalékos szimbólum kódolás a következő karaktereket:

   Karakter | URL-kódolás | Érték
   ----------|--------------|------
   Űr     | %20          | \%20
   &         | %25          | \%25

   Vegye figyelembe, hogy százalékos szimbólumokat kell megjelölni.

- Speciális reguláris kifejezés dupla-escape-karakterek (például \^$. +) egy fordított perjel szerepeljenek a reguláris kifejezés.

   Példa:

   Érték | Értelmezésre 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
   - Fejezze be a gyorsítótár kitöltés
   - Alapértelmezett belső maximális életkora
   - Kényszerített belső maximális életkora
   - Figyelmen kívül hagyja az eredeti No-Cache
   - Belső maximális elavult


[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="url-query-wildcard"></a>Lekérdezés helyettesítő URL-címe
A megadott értékeket, szemben a lekérdezési karakterlánc hasonlítja össze.

A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt az URL-cím lekérdezés helyettesítő felel meg a feltétel teljesül.
- **Megfelelő**: a kérés tartalmaz egy URL-lekérdezési karakterláncot, amely megfelel a megadott helyettesítő karakteres érték szükséges.
- **Nem felel meg does**: a kérés tartalmaz egy URL-lekérdezési karakterláncot, amely nem egyezik a megadott helyettesítő karakteres érték szükséges.

Kapcsolatos információkat:
- Ez a beállítás az alkalmazásában egy lekérdezési karakterlánc kezdődik-e az első karakter után a kérdőjel (?) elválasztó, a lekérdezési karakterlánc.
- A paraméterértékek tartalmazhatnak [helyettesítő értékek](cdn-rules-engine-reference.md#wildcard-values):
   - Minden paraméter értéke minta egy vagy több csillagot (*), ahol minden csillag is megegyezhet, a karaktersorozatot, mely egy vagy több állhat.
   - Bizonyos karakterek megkövetelése URL-kódolást. Használja az URL-címre százalékos szimbólum kódolás a következő karaktereket:

     Karakter | URL-kódolás
     ----------|---------
     Űr     | %20
     &         | %25

- Adjon meg több érték határoló mindegyiken akár egy szóköz.

   Például: *Parameter1 = ValueA* *ValueB* *Parameter1 = ValueC & Parameter2 = ValueD*

- Csak pontosan megegyezik a megadott lekérdezési karakterlánc minták legalább egy elégíti ki ez egyeznek az állapot.
    
- Használja a **esetben figyelmen kívül hagyása** lehetőséget a lekérdezési karakterláncok összehasonlítására Kisbetű/nagybetű megkülönböztetése szabályozására.
    
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
   - Fejezze be a gyorsítótár kitöltés
   - Alapértelmezett belső maximális életkora
   - Kényszerített belső maximális életkora
   - Figyelmen kívül hagyja az eredeti No-Cache
   - Belső maximális elavult

#### <a name="sample-scenarios"></a>Használati példák
A következő példa bemutatja, hogyan működik ez a beállítás adott helyzetekben:

 Name (Név)                 | Leírás
 ---------------------|------------
user=joe              | Ez a minta egyezik a kért URL-címhez tartozó lekérdezési karakterlánc esetén "? felhasználó = joe."
\*user=\* \*optout=\* | Ez a minta egyezik, amikor a CDN URL-lekérdezés tartalmazza a felhasználó vagy a optout paraméter.

[Lap tetejére](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

## <a name="next-steps"></a>További lépések
* [Az Azure Content Delivery Network áttekintése](cdn-overview.md)
* [Szabálymotor-referencia](cdn-rules-engine-reference.md)
* [Szabálymotor feltételes kifejezései](cdn-rules-engine-reference-conditional-expressions.md)
* [Szabálymotor funkciói](cdn-rules-engine-reference-features.md)
* [A szabályok használata alapértelmezett HTTP működés felülbírálata](cdn-rules-engine.md)

