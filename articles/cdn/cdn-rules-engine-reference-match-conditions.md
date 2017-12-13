---
title: "Az Azure CDN szabályok motor egyezés feltételek |} Microsoft Docs"
description: "Az Azure CDN referenciadokumentációt szabályok motor egyezés feltételek és a szolgáltatások."
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
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 0abbcf8508cb95d0fb8a9c8e5243426752efe590
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2017
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Az Azure CDN szabálymotor feltételek egyeznek
Ez a témakör részletesen ismerteti az Azure Content Delivery Network (CDN) feltételek egyeznek a [szabálymotor](cdn-rules-engine.md).

A szabály második része a egyezés feltétel. Egy egyezés feltétel azonosítja az adott típusú kérelmet, amely funkciókat érvényesül.

Például akkor lehet, hogy használható szűrheti a kéréseket a tartalom egy adott helyen létrehozott, egy adott IP-cím vagy ország vagy a fejléc-információ kérelmek.

## <a name="always"></a>Mindig

A mindig egyezés feltétel célja, hogy a szolgáltatások alapértelmezett készlete összes kérésre alkalmazni.

## <a name="device"></a>Eszköz

Az eszköz egyezés feltétel kérelmek egy mobileszközről tulajdonságai alapján azonosítja.  Mobil eszköz észlelési sorrendekben [WURFL](http://wurfl.sourceforge.net/).  WURFL képességek és a CDN szabálymotor változók az alábbiak.
<br>
> [!NOTE] 
> Az alábbi változók használhatók a **ügyfél kérelem fejléc módosítása** és **ügyfél válaszfejléc módosítása** szolgáltatások.

Képesség | Változó | Leírás | A minta érték(ek)
-----------|----------|-------------|----------------
Márka neve | a(z) % {wurfl_cap_brand_name} | Egy karakterlánc, amely az eszköz márka nevét jelöli. | Samsung
Az eszköz operációs rendszere | a(z) % {wurfl_cap_device_os} | Egy karakterlánc, amely azt jelzi, az operációs rendszer telepítve az eszközön. | IOS
Eszköz operációs rendszerének verziója | a(z) % {wurfl_cap_device_os_version} | Egy karakterlánc, amely azt jelzi, az eszközre telepített operációs rendszer verziószámát. | 1.0.1
Kettős tájolását | a(z) % {wurfl_cap_dual_orientation} | Egy logikai érték, amely jelzi, hogy az eszköz támogatja-e kettős tájolását. | igaz
HTML előnyben részesített DTD | a(z) % {wurfl_cap_html_preferred_dtd} | Egy olyan karakterlánc, amely jelzi a mobil eszköz előnyben részesített dokumentumtípusdefiníció (DTD) a HTML-tartalmakat. | nincs<br/>xhtml_basic<br/>HTML5
A kép Inlining | a(z) % {wurfl_cap_image_inlining} | Olyan logikai érték, amely jelzi, hogy az eszköz támogatja-e a Base64 kódolású képek. | hamis
Az Android | a(z) % {wurfl_vcap_is_android} | Egy logikai érték, amely azt jelzi, hogy az eszköz az Android operációs rendszer használja-e. | igaz
IOS | a(z) % {wurfl_vcap_is_ios} | Egy logikai érték, amely azt jelzi, hogy az eszköz iOS használja-e. | hamis
Az intelligens TV | a(z) % {wurfl_cap_is_smarttv} | Egy logikai érték, amely azt jelzi, hogy az eszköz egy intelligens TV. | hamis
Smartphone van | a(z) % {wurfl_vcap_is_smartphone} | Egy logikai érték, amely azt jelzi, hogy az eszköz okostelefont. | igaz
Tábla van | a(z) % {wurfl_cap_is_tablet} | Egy logikai érték, amely azt jelzi, hogy az eszköz egy tábla. Ez az az operációs rendszer független leírást. | igaz
Vezeték nélküli eszköz | a(z) % {wurfl_cap_is_wireless_device} | Egy logikai érték, amely azt jelzi, hogy az eszköz vezeték nélküli eszköz nem megfelelőnek. | igaz
Marketing neve | a(z) % {wurfl_cap_marketing_name} | Egy karakterlánc, amely megadja, hogy az eszköz marketing néven. | BlackBerry 8100 Pearl
Mobil böngésző | a(z) % {wurfl_cap_mobile_browser} | Egy olyan karakterlánc, amely jelzi a böngészőben tartalom kérésére az eszközről. | Chrome
Mobil böngészőverzió | a(z) % {wurfl_cap_mobile_browser_version} | A tartalom kérhet az eszköz használt böngésző verzióját jelző karakterlánc. | 31
Modell neve | a(z) % {wurfl_cap_model_name} | Egy karakterlánc, amely azt jelzi, az eszköz modell neve. | S3
Progresszív letöltés | a(z) % {wurfl_cap_progressive_download} | Egy logikai érték, amely jelzi, hogy az eszköz támogatja-e a hang-és videófolyamot lejátszását, miközben továbbra is letöltése. | igaz
Kiadás dátuma | a(z) % {wurfl_cap_release_date} | Egy olyan karakterlánc, amely jelzi a kiválasztott évhez és hónaphoz, amelyen az eszköz WURFL adatbázisba lett hozzáadva.<br/><br/>Formátum:`yyyy_mm` | 2013_december
Megoldási magassága | a(z) % {wurfl_cap_resolution_height} | Az eszköz magassága képpontban jelző egész számot. | 768
Megoldási szélessége | a(z) % {wurfl_cap_resolution_width} | Az eszköz szélességét képpontban jelző egész számot. | 1024


## <a name="location"></a>Hely

Ezen feltételek úgy terveztek, hogy a kérelmező helye alapján kérelmek egyezés.

Név | Cél
-----|--------
SZÁMOT | Egy adott hálózati kérelmekkel azonosítja.
Ország | A megadott országokból kérelmekkel azonosítja.

### <a name="as-number"></a>SZÁMOT 
Ehhez a hálózathoz az autonóm rendszer számát (ASN) van meghatározva. Lehetőség van annak jelzésére, hogy ez a feltétel lesz teljesül, amikor egy ügyfél hálózati "Megfelel" vagy "Does nem egyezik meg" a megadott SZÁMOT.

**Fontos információk**
- Adjon meg több AS számok határoló mindegyiken akár egy szóköz. Például 64514 64515 megfelel 64514 vagy 64515 érkező kérelmeket.
- Bizonyos kérelmek esetleg vissza egy érvényes SZÁMOT. A kérdőjel (azaz?) fog egyezni a kérelmeket, amelynek egy érvényes SZÁMOT nem határozható meg.
- A teljes, a kívánt hálózat száma meg kell adni. Hiányos értékek nem lesz megfelelő.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

### <a name="country"></a>Ország
Egy ország keresztül az országhívó szám adható meg. Lehetőség van annak jelzésére, hogy legyen-e ez a feltétel teljesül, ha a országban, amelyből a kérelem "Megfelel" vagy "Does nem egyezik meg" a megadott értékeket.


**Fontos információk**
- Adjon meg több országhívószámok határoló mindegyiken akár egy szóköz.
- Helyettesítő karakterek nem támogatottak, amikor az országkódot.
- A "EU" és "AP" országhívószámok nem tartoznak bele azokban a régiókban található összes IP-címet.
- Bizonyos kérelmek nem adhatnak vissza egy érvényes országhívó szám. A kérdőjel (azaz?) fog egyezni a kérelmeket, amelynek érvényes országkódot nem határozható meg.
- Országhívó számokat-és nagybetűk.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

## <a name="origin"></a>Forrás

Ezen feltételek úgy terveztek, hogy egyeznek kérelmek adott pontra CDN tárolóra vagy egy ügyfél eredeti kiszolgálóra.

Név | Cél
-----|--------
CDN-forrása | A CDN-tárolón tárolt tartalmak iránti kérelmek azonosítja.
Ügyfél forrása | Egy adott ügyfélhez forrás kiszolgálón lévő tartalomhoz azonosítja.

### <a name="cdn-origin"></a>CDN-forrása
Ez egyezés a feltétel teljesül mindkét következő feltétel teljesülése esetén:
- A kért tartalom CDN tárolóból.
- A kérelem URI-azonosítója a tartalom hozzáférési pont (pl. /000001) Ez a feltétel egyezik a definiált kihasználja.
  - CDN URL-címe: A kérelem URI-azonosítója a kiválasztott tartalom-hozzáférési pontot kell tartalmaznia.
  - Peremhálózati CNAME URL-címe: A kiválasztott tartalom-hozzáférési pont a megfelelő biztonsági CNAME konfigurációs kell mutatnia.
  
*Megjegyzések:*
 - A tartalom-hozzáférési pont azonosítja a szolgáltatást, amely a kért tartalom szolgálhat.
 - Az és IF utasítást nem kombinálhatók bizonyos egyezés feltételek használandó. Például egy ügyfél eredeti egyezik a feltétellel CDN származási egyezés feltétel kombinálásával hozna létre egy egyező mintát, amely soha nem feleltethető. Nagyon azonos ezért két CDN származási egyezés feltétel nem használható együtt az és IF utasítást keresztül.
 
### <a name="customer-origin"></a>Ügyfél forrása

**Fontos információk** 
- Függetlenül attól, hogy kérik a tartalom a CDN és a CNAME mutató URL-CÍMMEL a kijelölt ügyfél forrás él használatával Ez egyezés a feltétel teljesül.
- A szabály által hivatkozott felhasználói származási konfigurálása az ügyfél eredeti oldalról nem törölhető. Ügyfél eredeti konfiguráció törlése előtt, győződjön meg arról, hogy a következő konfigurációk nem hivatkozni rá:
  - Ügyfél eredeti egyezés feltétel
  - Egy peremhálózati CNAME-konfigurációt.
- Az és IF utasítást nem kombinálhatók bizonyos egyezés feltételek használandó. Például a CDN-forrása egyezik a feltétellel ügyfél eredeti egyezés feltétel kombinálásával hozna létre egy egyező mintát, amely soha nem feleltethető. Nagyon azonos emiatt két felhasználói származási egyezés feltétel nem használható együtt az és IF utasítást keresztül.

## <a name="request"></a>Kérés

Ezen feltételek úgy terveztek, hogy kérelmet tulajdonságaik alapján egyezés.

Név | Cél
-----|--------
Ügyfél IP-címe | Egy adott IP-címről kérelmekkel azonosítja.
Cookie-k paraméter | Ellenőrzi a megadott értéket minden kérelemhez társított cookie-kat.
Cookie-k paraméter Regex | A cookie-k a megadott reguláris kifejezést minden kérelemhez társított ellenőrzi.
Peremhálózati Cname | Azonosítja a kérelmeket, amelyek egy adott peremhálózati CNAME.
Hivatkozó tartomány | A megadott hostname(s) az említett kérelmek azonosítja.
Kérelem fejléc szövegkonstans | A megadott fejléc értéke a megadott értékeket tartalmazó kérelem azonosítja.
Kérelem fejléc Regex | A megadott fejléc értéke, amely megfelel a megadott reguláris kifejezést tartalmazó kérések azonosítja.
Kérelem fejléc helyettesítő | A megadott fejléc értéke, amely megfelel a megadott minta tartalmazó kérések azonosítja.
Kérési módszer | A HTTP-metódus azonosítja a kérelmeket.
Kérelem séma | A HTTP protokoll azonosítja a kérelmeket.

### <a name="client-ip-address"></a>Ügyfél IP-címe
Lehetőség van annak jelzésére, hogy ez az állapot, amikor egy ügyfél az IP által teljesülnek cím "Megfelel" vagy "Does nem egyezik meg" a megadott IP-címeket.

**Kapcsolatos információkat:**
- Ügyeljen arra, hogy a CIDR-jelöléssel.
- Adjon meg több IP-címek és/vagy az IP-címblokkok határoló mindegyiken akár egy szóköz.
  - **IPv4-alapú példa:** 1.2.3.4 10.20.30.40 megegyezik a 1.2.3.4 vagy 10.20.30.40 érkező kéréseit.
  - **IPv6-példa:** 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 megegyezik a 1:2:3:4:5:6:7:8 vagy 10:20:30:40:50:60:70:80 érkező kéréseit.
- Az IP-Címblokk szintaxisa a következő perjellel, majd a előtag mérete alap IP-címét.
  - **IPv4-alapú példa:** 5.5.5.64/26 megegyezik a 5.5.5.64 5.5.5.127 keresztül érkező kéréseit.
  - **IPv6-példa:** 1:2:3: / 48 megfelel a 1:2:3:0:0:0:0:0 1:2:3:ffff:ffff:ffff:ffff:ffff keresztül érkező kéréseit.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

### <a name="cookie-parameter"></a>Cookie-k paraméter
A **megfelel / nem felel meg** beállítás határozza meg a feltételeket, amely ezt a felel meg a feltétel teljesül.
- **Találatok:** kérelem tartalmazza a megadott cookie ez egyeznek az állapot megadott értékek közül legalább egy megfelelő értéket igényel.
- **Nem egyezik:** megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott cookie-k nem tartalmaz.
  - A megadott cookie-t tartalmaz, de értéke nem felel meg egyik sem ez a feltétel egyezik a megadott érték beolvasása.
  
**Kapcsolatos információkat:**
- **Cookie-név:** 
  - Speciális karaktert, köztük a csillag, nem támogatottak, ha a cookie-név megadását. Ez azt jelenti, hogy csak a pontos cookie neve megegyezik az összehasonlításhoz jogosultak.
  - E egyezés feltétel példányonként csak egy egyetlen cookie neve adható meg.
  - Cookie neve összehasonlítások nem különböztetik meg.
- **Cookie-értéket:** 
  - Adjon meg több cookie-k érték határoló mindegyiken akár egy szóköz.
  - A cookie-értéket kihasználhatják a speciális karakterek. 
  - Ha nincs megadva helyettesítő karakter, csak pontos egyezést kielégítéséhez ez egyeznek az állapot. 
   - **Példa:** megadása a "Value" fog egyezni a "Érték", de nem "Érték1" vagy "Érték2."
  - A **esetben figyelmen kívül hagyása** beállítás határozza meg, hogy a kis-és nagybetűket összehasonlítása a kérelem cookie-értéket vonatkozóan fog történni.
  - Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
   - Fejezze be a gyorsítótár kitöltés
   - Alapértelmezett belső maximális életkora
   - Kényszerített belső maximális életkora
   - Figyelmen kívül hagyja az eredeti No-Cache
   - Belső maximális elavult

### <a name="cookie-parameter-regex"></a>Cookie-k paraméter Regex
Ez a feltétel egyezik a cookie-név és érték határozza meg. Reguláris kifejezések használhatók a kívánt cookie-k értékét. 

A **megfelel / nem felel meg** beállítás határozza meg a feltételeket, amely alatt ez egyezés a feltétel teljesül.
- **Találatok:** kérelem tartalmazza a megadott cookie-k a megadott reguláris kifejezésnek megfelelő értéket igényel.
- **Nem egyezik:** megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott cookie-k nem tartalmaz.
  - A megadott cookie-t tartalmaz, de az érték nem egyezik a megadott reguláris kifejezés.
  
**Kapcsolatos információkat:**
- **Cookie-név:** 
  - Reguláris kifejezések és speciális karaktert, köztük a csillag, nem támogatottak, ha a cookie-név megadását. Ez azt jelenti, hogy csak a pontos cookie neve megegyezik az összehasonlításhoz jogosultak.
  - E egyezés feltétel példányonként csak egy egyetlen cookie neve adható meg.
  - Cookie neve összehasonlítások nem különböztetik meg.
- **Cookie-értéket:** 
  - Cookie-k érték reguláris kifejezéseket is előnyeit.
  - A **esetben figyelmen kívül hagyása** beállítás határozza meg, hogy a kis-és nagybetűket összehasonlítása a kérelem cookie-értéket vonatkozóan fog történni.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

### <a name="edge-cname"></a>Peremhálózati Cname
**Fontos információk** 
- A rendelkezésre álló biztonsági CNAME listáját csak azok a platformot, amelyre HTTP szabálymotor tartozik megfelelő peremhálózati CNAME lapon konfigurált.
- Előtt egy peremhálózati CNAME-konfiguráció törlése, győződjön meg arról, hogy egy biztonsági Cname egyezik-e a feltétel nem hivatkozni rá. Peremhálózati CNAME konfigurációt a szabályban definiált nem lehet törölni, a peremhálózati CNAME lapról. 
- Az és IF utasítást nem kombinálhatók bizonyos egyezés feltételek használandó. Például egy peremhálózati Cname egyezés állapot egy ügyfél eredeti egyezik a feltétellel kombinálásával hozna létre egy egyező mintát, amely soha nem feleltethető. Nagyon azonos emiatt két széle Cname egyezés feltétel nem használható együtt az és IF utasítást keresztül.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

### <a name="referring-domain"></a>Hivatkozó tartomány
Az állomásnév társított a hivatkozó, amelyen keresztül a tartalmat a kért határozza meg, hogy ez a feltétel teljesül. Lehetőség van annak jelzésére, hogy ez a feltétel lesz teljesülnek, akkor a hivatkozó állomásnév "Megfelel" vagy "Nem felel meg" a megadott értékeket.
**Kapcsolatos információkat:**
- Adjon meg több állomásnevek határoló mindegyiken akár egy szóköz.
- Ez a feltétel egyezik támogatja a speciális karaktereket.
- Ha a megadott érték nem tartalmaz egy csillag, majd azt egy pontosan egyezniük kell a hivatkozó gazdagép. Például "tartomany.com" megadása nem megfelelő "www.mydomain.com."
- A esetben figyelmen kívül hagyja a beállítás határozza meg, hogy történik-e a kis-és nagybetűket összehasonlítása.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult
  
 ### <a name="request-header-literal"></a>Kérelem fejléc szövegkonstans
A **megfelel / nem felel meg** beállítás határozza meg a feltételeket, amely ezt a felel meg a feltétel teljesül.
- **Találatok:** van szükség a kérelem tartalmazza a megadott fejléc és a értéket meg kell egyeznie egyezés feltétel definiálva.
- **Nem egyezik:** megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott fejléc nem tartalmaz.
  - A megadott fejléc tartalmaz, de értéke nem egyezik a definiált egyezés feltétel.
  
**Kapcsolatos információkat:**
- Mindig különböztetik meg a fejléc neve összehasonlítást. A fejléc értéke összehasonlítást Kisbetű/nagybetű megkülönböztetése a esetben figyelmen kívül hagyja a beállítás határozza meg.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult
  
### <a name="request-header-regex"></a>Kérelem fejléc Regex
**Megjegyzés:** ezt a képességet alrendszernek szabályok - speciális szabályok, amelyek külön-külön kell megvásárolni. Lépjen kapcsolatba az aktiválás a CDN fiókkezelővel. 

A **megfelel / nem felel meg** beállítás határozza meg a feltételeket, amely ezt a felel meg a feltétel teljesül.
- **Találatok:** van szükség a kérelem tartalmazza a megadott fejléc és a értéket meg kell egyeznie a megadott reguláris kifejezés beállított mintát.
- **Nem egyezik:** megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott fejléc nem tartalmaz.
  - A megadott fejléc tartalmaz, de az érték nem egyezik a megadott reguláris kifejezés.

**Kapcsolatos információkat:**
- Fejléc neve: 
  - Különböztetik meg a fejléc neve összehasonlítást.
  - A fejléc neve szóközt kell írni a "% 20." 
- Állomásfejléc-érték: 
  - Egy állomásfejléc-érték reguláris kifejezéseket is előnyeit.
  - A fejléc értéke összehasonlítást Kisbetű/nagybetű megkülönböztetése a esetben figyelmen kívül hagyja a beállítás határozza meg.
  - Csak teljes fejléc értéke megegyezik a megadott minták legalább egy eleget tesz a probléma.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult 

### <a name="request-header-wildcard"></a>Kérelem fejléc helyettesítő
A **megfelel / nem felel meg** beállítás határozza meg a feltételeket, amely ezt a felel meg a feltétel teljesül.
- **Találatok:** van szükség a kérelem tartalmazza a megadott fejléc és értékének egyeznie kell legalább Ez a feltétel egyezik a megadott érték beolvasása.
- **Nem egyezik:** megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott fejléc nem tartalmaz.
  - A megadott fejléc tartalmaz, de az érték nem egyezik a megadott értékek valamelyikét.
  
**Kapcsolatos információkat:**
- Fejléc neve: 
  - Különböztetik meg a fejléc neve összehasonlítást.
  - A fejléc neve szóközt kell írni a "% 20." Ez az érték megadása a szóközöket a fejléc értéke használható.
- Állomásfejléc-érték: 
  - A fejléc értéke kihasználhatják a speciális karakterek.
  - A fejléc értéke összehasonlítást Kisbetű/nagybetű megkülönböztetése a esetben figyelmen kívül hagyja a beállítás határozza meg.
  - Csak teljes fejléc értéke megegyezik a megadott minták legalább egy eleget tesz a probléma.
  - Adjon meg több érték határoló mindegyiken akár egy szóköz.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

### <a name="request-method"></a>Kérési módszer
Csak a kijelölt kérelem metódussal igényelt eszközök eleget tesz a probléma. A rendelkezésre álló kérelem megoldások a következők:
- GET
- HEAD 
- POST 
- BEÁLLÍTÁSOK 
- A PUT 
- TÖRLÉSE 
- NYOMKÖVETÉSI 
- CSATLAKOZÁS 

**Kapcsolatos információkat:**
- Alapértelmezés szerint csak a GET kérelem metódus hozhat létre a gyorsítótárazott tartalom a hálózaton. Minden más kérelem módszereket egyszerűen küldése a proxyn keresztül a hálózaton keresztül.
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

### <a name="request-scheme"></a>Kérelem séma
Csak a kiválasztott protokollal igényelt eszközök eleget tesz a probléma. A rendelkezésre álló protokollok a következők: a HTTP és HTTPS.

**Kapcsolatos információkat:**
- Mely gyorsítótárában beállítások követi módon, mert ez egyeznek az állapot nem kompatibilis a következő szolgáltatásokat:
  - Fejezze be a gyorsítótár kitöltés
  - Alapértelmezett belső maximális életkora
  - Kényszerített belső maximális életkora
  - Figyelmen kívül hagyja az eredeti No-Cache
  - Belső maximális elavult

## <a name="url"></a>URL-cím

Ezen feltételek úgy terveztek, hogy az URL-címek alapján kérelmek egyezés.

Név | Cél
-----|--------
URL-cím elérési út könyvtár | Azonosítja a kéréseket a relatív elérési útja.
URL-cím elérési út bővítmény | A fájlnév-kiterjesztés azonosítja a kérelmeket.
URL-cím elérési út fájlnév | A fájlnév azonosítja a kérelmeket.
URL-cím elérési út szövegkonstans | Összehasonlítja a kérelem relatív elérési út és a megadott értéket.
URL-cím elérési út Regex | Összehasonlítja a kérelem relatív elérési útját a megadott reguláris kifejezés.
URL-cím elérési út helyettesítő karakter | Összehasonlítja a kérelmet a megadott minta relatív elérési útja.
Lekérdezés-szövegkonstans URL-címe | Összehasonlítja a lekérdezési karakterlánc és a megadott értéket.
URL-cím lekérdezési paraméter | A megadott lekérdezési karakterlánc-paraméter értéke, amely megfelel a megadott minta tartalmazó kérések azonosítja.
Lekérdezés Regex URL-címe | A megadott lekérdezési karakterlánc-paraméter értéke, amely megfelel a megadott reguláris kifejezést tartalmazó kérések azonosítja.
Lekérdezés helyettesítő URL-címe | A megadott értékeket, szemben a lekérdezési karakterlánc hasonlítja össze.


## <a name="next-steps"></a>Következő lépések
* [Az Azure CDN áttekintése](cdn-overview.md)
* [Szabályok motor referencia](cdn-rules-engine-reference.md)
* [Szabályok motor feltételes kifejezések](cdn-rules-engine-reference-conditional-expressions.md)
* [Szabályok adatbázismotor-szolgáltatások](cdn-rules-engine-reference-features.md)
* [A szabályok használata alapértelmezett HTTP működés felülbírálata](cdn-rules-engine.md)

