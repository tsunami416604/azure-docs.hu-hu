---
title: "A Content Delivery Network szabálymotor vonatkozó feltételek egyeznek |} Microsoft Docs"
description: "Az Azure Content Delivery Network referenciadokumentációt szabályok motor egyezés feltételek és a szolgáltatások."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: rli
ms.openlocfilehash: 944675f4b055c3d0c2559db53e4807a5c00bda64
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2018
---
# <a name="match-conditions-for-the-content-delivery-network-rules-engine"></a>A Content Delivery Network szabálymotor vonatkozó feltételek egyeznek
Ez a cikk az Azure Content Delivery Network szabad feltételei részletes leírását tartalmazza [szabálymotor](cdn-rules-engine.md).

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
[Eszköz](#device) | Kérelmek egy mobileszközről tulajdonságai alapján azonosítja.

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
URL-cím elérési út könyvtár | Azonosítja a kéréseket a relatív elérési útja.
URL-cím elérési út bővítmény | A fájlnév-kiterjesztésük alapján azonosítja a kérelmeket.
URL-cím elérési út fájlnév | A fájl neve azonosítja a kérelmeket.
URL-cím elérési út szövegkonstans | Összehasonlítja a kérelem relatív elérési út és a megadott értéket.
URL-cím elérési út Regex | Összehasonlítja a kérelem relatív elérési útját a megadott reguláris kifejezés.
URL-cím elérési út helyettesítő karakter | Összehasonlítja a kérelmet a megadott minta relatív elérési útja.
Lekérdezés-szövegkonstans URL-címe | Összehasonlítja a lekérdezési karakterlánc és a megadott értéket.
URL-cím lekérdezési paraméter | A megadott lekérdezési karakterlánc-paraméter értéke, amely megfelel a megadott minta tartalmazó kérések azonosítja.
Lekérdezés Regex URL-címe | A megadott lekérdezési karakterlánc-paraméter értéke, amely megfelel a megadott reguláris kifejezést tartalmazó kérések azonosítja.
Lekérdezés helyettesítő URL-címe | A megadott érték szemben a lekérdezési karakterlánc hasonlítja össze.


## <a name="reference-for-rules-engine-match-conditions"></a>Útmutató a szabályok motor egyezés feltételek

---
### <a name="always"></a>Mindig

A mindig egyezés feltétel alapértelmezés szerinti szolgáltatások összes kérelem vonatkozik.

[Lap tetejére](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="as-number"></a>SZÁMOT 
Az AS számot hálózati határozza meg az autonóm rendszer számát (ASN). Lehetőség van annak jelzésére, hogy ez az állapot, amikor egy ügyfél hálózati "Megfelel" vagy "Does nem egyezik meg" a megadott ASN teljesülnek.

Kapcsolatos információkat:
- Adjon meg több ASN-eket határoló mindegyiken akár egy szóköz. Például 64514 64515 megfelel a kérelmeket, amelyek 64514 vagy 64515 érkeznek.
- Bizonyos kérelmek esetleg vissza érvényes ASN. A kérdőjel (?) fog egyezni a kérelmeket, amelynek érvényes ASN nem határozható meg.
- Meg kell adnia a kívánt hálózathoz teljes ASN. Hiányos értékek nem lesz megfelelő.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="cdn-origin"></a>CDN-forrása
A CDN-forrása egyezés feltétel nem teljesül, az alábbi két feltétel teljesülése esetén:
- A kért tartalom Content Delivery Network tárolóból.
- A kérelem URI használ a tartalom hozzáférési pontokat (például /000001), a match feltétel van megadva.
  - A tartalom a kézbesítési hálózati URL-címe: A kérelem URI-azonosítója a kiválasztott tartalom-hozzáférési pontot kell tartalmaznia.
  - Peremhálózati CNAME URL-címe: A kiválasztott tartalom-hozzáférési pont a megfelelő biztonsági CNAME konfigurációs kell mutatnia.
  
Kapcsolatos információkat:
 - A tartalom-hozzáférési pont azonosítja a szolgáltatást, amely a kért tartalom szolgálhat.
 - Ne használjon az és IF utasítást bizonyos egyezés feltételek kombinálhatók. Például egy ügyfél eredeti egyezik a feltétellel CDN származási egyezés feltétel kombinálásával hozna létre egy egyező mintát, amely soha nem feleltethető. Ezért két CDN származási egyezés feltételek nem használható együtt az és IF utasítást keresztül.

[Lap tetejére](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="client-ip-address"></a>Ügyfél IP-címe
Lehetőség van annak jelzésére, hogy az ügyfél IP-cím feltétel lesz teljesül, amikor egy ügyfél IP-cím "Megfelel" vagy "Does nem egyezik meg" a megadott IP-címek.

Kapcsolatos információkat:
- Ügyeljen arra, hogy a CIDR-jelöléssel.
- Adjon meg több IP-címek és/vagy az IP-címblokkok határoló mindegyiken akár egy szóköz.
  - **IPv4-alapú példa**: 1.2.3.4 10.20.30.40 megegyezik bármilyen 1.2.3.4 vagy 10.20.30.40 érkező kérelmeket.
  - **IPv6-alapú példa**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 megegyezik bármilyen 1:2:3:4:5:6:7:8 vagy 10:20:30:40:50:60:70:80 érkező kérelmeket.
- Az IP-Címblokk szintaxisa a következő perjellel, majd a előtag mérete alap IP-címét.
  - **IPv4-alapú példa**: 5.5.5.64/26 megegyezik bármilyen 5.5.5.64 5.5.5.127 keresztül érkező kérelmeket.
  - **IPv6-alapú példa**: 1:2:3: / 48 megegyezik bármilyen 1:2:3:0:0:0:0:0 1:2:3:ffff:ffff:ffff:ffff:ffff keresztül érkező kérelmeket.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#azure-cdn-rules-engine-match-conditions)

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
  - Csillag, beleértve a különleges karakterek nem támogatottak, ha a cookie-nevet ad meg. Ez azt jelenti, hogy csak a pontos cookie neve megegyezik az összehasonlításhoz jogosultak.
  - E egyezés feltétel példányonként csak egy egyetlen cookie neve adható meg.
  - Cookie neve összehasonlítások nem különböztetik meg.
- Cookie-értéket: 
  - Adjon meg több cookie-k érték határoló mindegyiken akár egy szóköz.
  - A cookie-értéket kihasználhatják a speciális karakterek. 
  - Ha nincs megadva helyettesítő karaktert, csak pontos egyezést eleget tesz a egyezés probléma. Például "Érték" meg fog egyezni a "Érték", de nem "Érték1" vagy "Érték2."
  - A **esetben figyelmen kívül hagyása** beállítás határozza meg, hogy a kis-és nagybetűket összehasonlítása a kérelem cookie-értéket vonatkozóan fog történni.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="cookie-parameter-regex"></a>Cookie-k paraméter Regex
A cookie-k paramétert reguláris kifejezéssel egyező feltétel határozza meg, a cookie nevét és értékét. A reguláris kifejezések segítségével megadhatja a kívánt cookie-értéket. 

A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely ezt a felel meg a feltétel teljesül.
- **Megfelelő**: kérelem tartalmazza a megadott cookie-k a megadott reguláris kifejezésnek megfelelő értéket igényel.
- **Nem egyezik meg**: megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott cookie-k nem tartalmaz.
  - A megadott cookie-t tartalmaz, de az érték nem egyezik a megadott reguláris kifejezés.
  
Kapcsolatos információkat:
- Cookie-név: 
  - A reguláris kifejezések és speciális karaktert, köztük a csillag, nem támogatottak, ha egy cookie-nevet ad meg. Ez azt jelenti, hogy csak a pontos cookie neve megegyezik az összehasonlításhoz jogosultak.
  - E egyezés feltétel példányonként csak egy egyetlen cookie neve adható meg.
  - Cookie neve összehasonlítások nem különböztetik meg.
- Cookie-értéket: 
  - Cookie-k érték reguláris kifejezéseket is előnyeit.
  - A **esetben figyelmen kívül hagyása** beállítás határozza meg, hogy a kis-és nagybetűket összehasonlítása a kérelem cookie-értéket vonatkozóan fog történni.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#azure-cdn-rules-engine-match-conditions)

</br>

--- 
### <a name="country"></a>Ország
Megadhat egy ország keresztül az országhívószámot. Lehetőség van annak jelzésére, hogy legyen-e ez a feltétel teljesül, ha a országban, amelyből a kérelem "Megfelel" vagy "Does nem egyezik meg" a megadott értékek.

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

[Lap tetejére](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="customer-origin"></a>Ügyfél forrása

Kapcsolatos információkat: 
- Függetlenül attól, hogy tartalom tartalom kézbesítési hálózati URL-címet vagy CNAME mutató URL-CÍMMEL a kijelölt ügyfél forrás él keresztül van szükség a felhasználói származási egyezés feltétel teljesül.
- A szabály által hivatkozott felhasználói származási konfigurálása az ügyfél eredeti oldalról nem törölhető. Mielőtt megkísérli az ügyfél eredeti konfiguráció törlése, győződjön meg arról, hogy a következő konfigurációk nem hivatkozni rá:
  - Egy ügyfél eredeti egyezés feltétel
  - Egy peremhálózati CNAME-konfiguráció
- Ne használjon az és IF utasítást bizonyos egyezés feltételek kombinálhatók. Például a CDN-forrása egyezik a feltétellel ügyfél eredeti egyezés feltétel kombinálásával hozna létre egy egyező mintát, amely soha nem feleltethető. Ezért két felhasználói származási egyezés feltétel nem használható együtt keresztül az és IF utasítást.

[Lap tetejére](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="device"></a>Eszköz

Az eszköz egyezés feltétel kérelmek egy mobileszközről tulajdonságai alapján azonosítja. Mobil eszköz észlelési sorrendekben [WURFL](http://wurfl.sourceforge.net/). A következő táblázat WURFL képességek és a Content Delivery Network szabálymotor a változókat.
<br>
> [!NOTE] 
> A következő változók használhatók a **ügyfél kérelem fejléc módosítása** és **módosítása ügyfél válaszfejléc** szolgáltatásokat.

Képesség | Változó | Leírás | Példaértékek
-----------|----------|-------------|----------------
Márka neve | a(z) % {wurfl_cap_brand_name} | Egy karakterlánc, amely az eszköz márka nevét jelöli. | Samsung
Az eszköz operációs rendszere | a(z) % {wurfl_cap_device_os} | Egy karakterlánc, amely azt jelzi, az operációs rendszer telepítve az eszközön. | IOS
Eszköz operációs rendszerének verziója | a(z) % {wurfl_cap_device_os_version} | Egy karakterlánc, amely azt jelzi, az eszköz a telepített operációs rendszer verziószámát. | 1.0.1
Kettős tájolását | a(z) % {wurfl_cap_dual_orientation} | Egy logikai érték, amely jelzi, hogy az eszköz támogatja-e kettős tájolását. | igaz
HTML előnyben részesített DTD | a(z) % {wurfl_cap_html_preferred_dtd} | Egy olyan karakterlánc, amely jelzi a mobil eszköz előnyben részesített dokumentumtípusdefiníció (DTD) a HTML-tartalmakat. | nincs<br/>xhtml_basic<br/>HTML5
A kép Inlining | a(z) % {wurfl_cap_image_inlining} | Olyan logikai érték, amely jelzi, hogy az eszköz támogatja-e a Base64 kódolású képek. | false
Az Android | a(z) % {wurfl_vcap_is_android} | Egy logikai érték, amely azt jelzi, hogy az eszköz az Android operációs rendszer használja-e. | igaz
IOS | a(z) % {wurfl_vcap_is_ios} | Egy logikai érték, amely azt jelzi, hogy az eszköz iOS használja-e. | false
Az intelligens TV | a(z) % {wurfl_cap_is_smarttv} | Egy logikai érték, amely azt jelzi, hogy az eszköz egy intelligens TV. | false
Smartphone van | a(z) % {wurfl_vcap_is_smartphone} | Egy logikai érték, amely azt jelzi, hogy az eszköz okostelefont. | igaz
Tábla van | a(z) % {wurfl_cap_is_tablet} | Egy logikai érték, amely azt jelzi, hogy az eszköz egy tábla. Ez az az operációs rendszer független leírást. | igaz
Vezeték nélküli eszköz | a(z) % {wurfl_cap_is_wireless_device} | Egy logikai érték, amely azt jelzi, hogy az eszköz vezeték nélküli eszköz nem megfelelőnek. | igaz
Marketing neve | a(z) % {wurfl_cap_marketing_name} | Egy karakterlánc, amely megadja, hogy az eszköz marketing néven. | BlackBerry 8100 Pearl
Mobil böngésző | a(z) % {wurfl_cap_mobile_browser} | Egy karakterlánc, amely azt jelzi, a böngésző, amelynek használatával a tartalmat igénylő az eszközről. | Chrome
Mobil böngészőverzió | a(z) % {wurfl_cap_mobile_browser_version} | A böngészőben tartalom kérhet az eszköz használt verzióját jelző karakterlánc. | 31
Modell neve | a(z) % {wurfl_cap_model_name} | Egy karakterlánc, amely azt jelzi, az eszköz modell neve. | S3
Progresszív letöltés | a(z) % {wurfl_cap_progressive_download} | Egy logikai érték, amely jelzi, hogy az eszköz támogatja-e a audió és videó lejátszását, miközben továbbra is letöltése. | igaz
Kiadás dátuma | a(z) % {wurfl_cap_release_date} | Egy olyan karakterlánc, amely jelzi a kiválasztott évhez és hónaphoz, amelyen az eszköz WURFL adatbázisba lett hozzáadva.<br/><br/>Formátum:`yyyy_mm` | 2013_december
Megoldási magassága | a(z) % {wurfl_cap_resolution_height} | Az eszköz magassága képpontban jelző egész számot. | 768
Megoldási szélessége | a(z) % {wurfl_cap_resolution_width} | Az eszköz szélességét képpontban jelző egész számot. | 1024

[Lap tetejére](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="edge-cname"></a>Peremhálózati Cname
Kapcsolatos információkat: 
- A rendelkezésre álló biztonsági CNAME listáját csak azok, amelyek az Edge CNAME oldalon, amely megfelel a platform, amelyen HTTP szabálymotor konfigurálva vannak konfigurálva.
- Mielőtt megkísérli egy peremhálózati CNAME-konfiguráció törlése, ellenőrizze, hogy egy biztonsági Cname egyezés állapot nem hivatkozik. Peremhálózati CNAME konfigurációt a szabályban definiált nem lehet törölni, a peremhálózati CNAME lapról. 
- Ne használjon az és IF utasítást bizonyos egyezés feltételek kombinálhatók. Például egy peremhálózati Cname egyezés állapot egy ügyfél eredeti egyezik a feltétellel kombinálásával hozna létre egy egyező mintát, amely soha nem feleltethető. Ezért két széle Cname egyezés feltétel nem használható együtt keresztül az és IF utasítást.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="referring-domain"></a>Hivatkozó tartomány
Az állomásnév társított a hivatkozó, amelyen keresztül a tartalmat a kért határozza meg, hogy a tartomány utaló feltétel teljesül. Lehetőség van annak jelzésére, hogy ez az állapot, amikor a hivatkozó gazdagép nevét "Megfelel" vagy "Does nem egyezik meg" a megadott értékek teljesülnek.

Kapcsolatos információkat:
- Adjon meg több állomásnevet határoló mindegyiken akár egy szóköz.
- Ez a feltétel egyezik támogatja a speciális karaktereket.
- Ha a megadott érték nem tartalmaz egy csillag, a hivatkozó névhez pontos egyezésűnek kell lennie. Például "tartomany.com" megadása nem megfelelő "www.mydomain.com."
- A **esetben figyelmen kívül hagyása** beállítás határozza meg, hogy történik-e a kis-és nagybetűket összehasonlítása.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#azure-cdn-rules-engine-match-conditions)

</br>

---  
### <a name="request-header-literal"></a>Kérelem fejléc szövegkonstans
A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely ezt a felel meg a feltétel teljesül.
- **Egyezések**: a kérelem tartalmazza a megadott fejléc igényel. Az értékét meg kell egyeznie az egyeztetés feltétel definiált.
- **Nem egyezik meg**: megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott fejléc nem tartalmaz.
  - A megadott fejléc tartalmaz, de értéke nem egyezik a definiált ez egyeznek az állapot.
  
Kapcsolatos információkat:
- Mindig különböztetik meg a fejléc neve összehasonlítást. A **esetben figyelmen kívül hagyása** beállítás határozza meg a kis-és nagybetűk, a fejléc értéke összehasonlítást.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#azure-cdn-rules-engine-match-conditions)

</br>

---  
### <a name="request-header-regex"></a>Kérelem fejléc Regex
A **egyező**/**nem felel meg** beállítás határozza meg a feltételeket, amely alatt a kérelem fejlécében Regex felel meg a feltétel teljesül.
- **Egyezések**: a kérelem tartalmazza a megadott fejléc igényel. Az értékét meg kell egyeznie egy mintát, amely a megadott reguláris kifejezés van megadva.
- **Nem egyezik meg**: megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott fejléc nem tartalmaz.
  - A megadott fejléc tartalmaz, de az érték nem egyezik a megadott reguláris kifejezés.

Kapcsolatos információkat:
- Fejléc neve: 
  - Különböztetik meg a fejléc neve összehasonlítást.
  - A fejléc neve szóközt kell írni a "% 20." 
- Állomásfejléc-érték: 
  - Egy állomásfejléc-érték reguláris kifejezéseket is előnyeit.
  - A **esetben figyelmen kívül hagyása** beállítás határozza meg a kis-és nagybetűk, a fejléc értéke összehasonlítást.
  - Csak teljes fejléc értéke megegyezik a megadott minták legalább egy eleget tesz a probléma.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult 

[Lap tetejére](#azure-cdn-rules-engine-match-conditions)

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
  - A fejléc értéke kihasználhatják a speciális karakterek.
  - A **esetben figyelmen kívül hagyása** beállítás határozza meg a kis-és nagybetűk, a fejléc értéke összehasonlítást.
  - Csak teljes fejléc értéke megegyezik a megadott minták legalább egy eleget tesz a probléma.
  - Adjon meg több érték határoló mindegyiken akár egy szóköz.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="request-method"></a>Kérési módszer
Csak a kijelölt kérelem metódussal igényelt eszközök eleget tesz a kérési metódust a feltétel. A rendelkezésre álló kérelem megoldások a következők:
- GET
- HEAD 
- POST 
- BEÁLLÍTÁSOK 
- PUT 
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

[Lap tetejére](#azure-cdn-rules-engine-match-conditions)

</br>

---
### <a name="request-scheme"></a>Kérelem séma
Csak a kiválasztott protokollal igényelt eszközök eleget tesz a rendszer kérelem feltétel. A rendelkezésre álló protokollok a következők: a HTTP és HTTPS.

Kapcsolatos információkat:
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

[Lap tetejére](#azure-cdn-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>További lépések
* [Az Azure Content Delivery Network áttekintése](cdn-overview.md)
* [Szabályok motor referencia](cdn-rules-engine-reference.md)
* [Szabályok motor feltételes kifejezések](cdn-rules-engine-reference-conditional-expressions.md)
* [Szabályok adatbázismotor-szolgáltatások](cdn-rules-engine-reference-features.md)
* [A szabályok használata alapértelmezett HTTP működés felülbírálata](cdn-rules-engine.md)

