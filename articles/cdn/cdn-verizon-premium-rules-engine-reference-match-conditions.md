---
title: Azure CDN a Verizon Premium szabályok motor feltételeknek valómérkőzés | Microsoft dokumentumok
description: Az Azure Content Delivery Network dokumentációja a Verizon Premium szabályok motorja feltételeknek megfelelő.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: allensu
ms.openlocfilehash: e2361590118668f2cdf22c4a29534b16790b90e4
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81253441"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Az Azure CDN a Verizon Premium szabályaitól megfelel a feltételeknek

Ez a cikk részletes leírást tartalmaz az Azure Content Delivery Network (CDN) számára a Verizon Premium [szabályok motorjától](cdn-verizon-premium-rules-engine.md)elérhető egyezési feltételekről.

A szabály második része az egyezési feltétel. Az egyezési feltétel azonosítja azokat a kérelmeket, amelyekhez szolgáltatások készlete lesz végrehajtva.

Az egyezési feltétel például a következőkre használható:

- Egy adott helyen a tartalomra vonatkozó kérelmek szűrése.
- Adott IP-címről vagy országból/régióból generált kérelmek szűrése.
- A kérelmek et fejlécadatok szerint szűrheti.

## <a name="always-match-condition"></a>Mindig egyezik a feltétel

A Mindig egyezés feltétel minden kérésre alkalmazza az alapértelmezett szolgáltatáskészletet.

Név | Cél
-----|--------
[Mindig](#always) | Az alapértelmezett szolgáltatáskészletet alkalmazza az összes kérelemre.

## <a name="device-match-condition"></a>Eszközegyezési feltétel

Az Eszköz egyezési feltétel azonosítja a mobileszközről érkező kérelmeket a tulajdonságai alapján.  

Név | Cél
-----|--------
[Eszköz](#device) | A mobileszközről érkező kérelmeket a tulajdonságai alapján azonosítja.

## <a name="location-match-conditions"></a>A hely egyeztetési feltételei

A helyegyeztetési feltételek a kérelmező tartózkodási helye alapján azonosítják a kérelmeket.

Név | Cél
-----|--------
[AS-szám](#as-number) | Azonosítja az adott hálózatról származó kérelmeket.
[Ország](#country) | Azonosítja a megadott országokból/régiókból származó kérelmeket.

## <a name="origin-match-conditions"></a>Az eredetmérkőzés feltételei

Az Origin egyezési feltételei azonosítják azokat a kéréseket, amelyek a Content Delivery Network tárhelyére vagy az ügyfél származási kiszolgálójára mutatnak.

Név | Cél
-----|--------
[CDN eredete](#cdn-origin) | A Content Delivery Network tárolójában tárolt tartalomra vonatkozó kérelmeket azonosítja.
[Vevő eredete](#customer-origin) | Egy adott ügyfél származási kiszolgálóján tárolt tartalomra vonatkozó kérelmeket azonosítja.

## <a name="request-match-conditions"></a>Kérelem egyeztetési feltételei

A kérelem megfeleltetési feltételek azonosítják a kérelmeket a tulajdonságaik alapján.

Név | Cél
-----|--------
[Ügyfél IP-címe](#client-ip-address) | Azonosítja azadott IP-címről származó kérelmeket.
[Cookie paraméter](#cookie-parameter) | Ellenőrzi a megadott értékhez tartozó egyes kérelmekhez társított cookie-kat.
[Cookie paraméter Regex](#cookie-parameter-regex) | Ellenőrzi a megadott reguláris kifejezés minden egyes kéréséhez társított cookie-kat.
[Szegély cname](#edge-cname) | Azonosítja azokat a kérelmeket, amelyek egy adott élCNAME-re mutatnak.
[Hivatkozó tartomány](#referring-domain) | A megadott állomásnevekről hivatkozott kérelmeket azonosítja.
[Kérés fejlécének szeksika](#request-header-literal) | Azonosítja azokat a kérelmeket, amelyek a megadott fejléckészletet egy megadott értékre tartalmazzák.
[Kérelem fejléce Regex](#request-header-regex) | Azonosítja azokat a kérelmeket, amelyek a megadott fejléckészletet a megadott reguláris kifejezésnek megfelelő értékre tartalmazzák.
[Helyettesítő fejléc kérése](#request-header-wildcard) | Azonosítja azokat a kérelmeket, amelyek a megadott fejléckészletet a megadott mintának megfelelő értékre tartalmazzák.
[Kérelem módja](#request-method) | A http-módszerük szerint azonosítja a kérelmeket.
[Igénylési rendszer](#request-scheme) | A HTTP protokoll által benyújtott kérelmeket azonosítja.

## <a name="url-match-conditions"></a>URL-egyezési feltételek

Az URL-cím egyeztetési feltételei az URL-címek alapján azonosítják a kérelmeket.

Név | Cél
-----|--------
[URL elérési út könyvtára](#url-path-directory) | A kérelmeket a relatív elérési útjuk szerint azonosítja.
[URL-elérési út kiterjesztése](#url-path-extension) | A kérelmeket a fájlnévkiterjesztés alapján azonosítja.
[URL elérési út fájlneve](#url-path-filename) | A kérelmeket a fájlnevük alapján azonosítja.
[URL-elérési út konstans](#url-path-literal) | Összehasonlítja a kérelem relatív elérési útját a megadott értékkel.
[URL elérési útja Regex](#url-path-regex) | Összehasonlítja a kérelem relatív elérési útját a megadott reguláris kifejezéssel.
[URL-elérési út helyettesítő karaktere](#url-path-wildcard) | Összehasonlítja a kérelem relatív elérési útját a megadott mintával.
[URL-lekérdezés literális](#url-query-literal) | Összehasonlítja a kérelem lekérdezési karakterláncát a megadott értékkel.
[URL-lekérdezési paraméter](#url-query-parameter) | Azonosítja azokat a kérelmeket, amelyek a megadott lekérdezési karakterlánc paramétert egy megadott mintának megfelelő értékre vannak beállítva.
[URL-lekérdezés Regex](#url-query-regex) | Azonosítja azokat a kérelmeket, amelyek a megadott lekérdezési karakterlánc paramétert egy megadott reguláris kifejezésnek megfelelő értékre állították be.
[URL-lekérdezési helyettesítő karakter](#url-query-wildcard) | Összehasonlítja a megadott értéket a kérelem lekérdezési karakterláncához.

## <a name="reference-for-rules-engine-match-conditions"></a>Hivatkozás a szabályokhoz motor egyezési feltételek

---

### <a name="always"></a>Mindig

A Mindig egyezés feltétel minden kérésre alkalmazza az alapértelmezett szolgáltatáskészletet.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>AS-szám

Az AS-szám hálózatot az autonóm rendszerszáma (ASN) határozza meg. 

A**Nem egyezés** **beállítás**/határozza meg azokat a feltételeket, amelyek mellett az AS-szám egyezési feltétel teljesül:

- **Egyezések**: Megköveteli, hogy az ügyfélhálózat ASN-je megegyezik a megadott ASN-ek egyikével. 
- **Nem egyezik:** Megköveteli, hogy az ügyfélhálózat ASN-je ne egyezzen meg a megadott ASN-ek egyikével sem.

Legfontosabb információk:

- Adjon meg több ASN-t úgy, hogy mindegyiket egyetlen szóközvel határolódmeg. Például a 64514 64515 megfelel a 64514 vagy 64515-ből érkező kérelmeknek.
- Előfordulhat, hogy bizonyos kérelmek nem adnak vissza érvényes ASN-t. Kérdőjel (?) megfelel azoknak a kérelmeknek, amelyeknél nem lehetett meghatározni az érvényes ASN-t.
- Adja meg a teljes ASN-t a kívánt hálózathoz. A részleges értékek nem egyeztethetők.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez az egyezési feltétel nem kompatibilis a következő szolgáltatásokkal:
  - Teljes gyorsítótár-kitöltés
  - Alapértelmezett belső maximális életkor
  - Belső max-kor kényszerítése
  - Az Origin no-cache figyelmen kívül hagyása
  - Belső max-állott

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>CDN eredete

A CDN Origin egyezési feltétel akkor teljesül, ha mindkét alábbi feltétel teljesül:

- A RENDSZER a CDN-tárolóból származó tartalmat kérte.
- A kérelem URI-ja az egyeztetési feltételben definiált tartalomhozzáférési pont típusát (például /000001) használja:
  - CDN URL-címe: A kérelem URI-jának tartalmaznia kell a kijelölt tartalom-hozzáférési pontot.
  - ÉlcNAME URL: A megfelelő élCNAME konfigurációnak a kijelölt tartalom-hozzáférési pontra kell mutatnia.
  
Legfontosabb információk:

- A tartalom-hozzáférési pont azonosítja azt a szolgáltatást, amelynek a kért tartalmat kell szolgálnia.
- Ne használjon ÉS HA utasítást bizonyos egyezési feltételek kombinálására. Például egy CDN Origin egyezési feltétel és egy Customer Origin egyezési feltétel kombinálásával olyan egyezési mintát hozlétre, amely soha nem egyeztethető. Ezért két CDN Origin egyezési feltétel nem kombinálható ÉS HA utasítással.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Ügyfél IP-címe

A **Nem**/**egyezik** beállítás határozza meg azokat a feltételeket, amelyek mellett az ügyfél IP-címegyezési feltételteljesül:

- **Egyezések**: Megköveteli, hogy az ügyfél IP-címe megegyezik a megadott IP-címek egyikével. 
- **Nem egyezik:** Megköveteli, hogy az ügyfél IP-címe ne egyezzen meg a megadott IP-címek egyikével sem. 

Legfontosabb információk:

- Cidr jelölés használata.
- Adjon meg több IP-címet és/vagy IP-címblokkot úgy, hogy mindegyiket egyetlen szóközvel hakorlátozza. Például:
  - **IPv4 példa:** 1.2.3.4 10.20.30.40 megfelel minden olyan kérésnek, amely vagy az 1.2.3.4 vagy a 10.20.30.40 címről érkezik.
  - **IPv6 példa:** 1:2:3:4:5:6:7:8 10:20:30:40:50:50:70:80 megfelel minden olyan kérésnek, amely az 1:2:3:4:5:5:6:7:8 vagy a 10:20:30:40:50:60:70:80 címről érkezik.
- Az IP-címblokk szintaxisa az alap IP-cím, amelyet egy perjel és az előtag mérete követ. Például:
  - **IPv4 példa:** Az 5.5.5.64/26 minden olyan kérésnek megfelel, amely az 5.5.5.64 és 5.5.5.127 közötti címekről érkezik.
  - **IPv6 példa:** 1:2:3:/48 megfelel minden olyan kérésnek, amely az 1:2:3:0:0:0:0:0:0:0 és 1:2:3:ffff:ffff:ffff:ffff:ffff:ffff:ffff.0:0:0:0:0:0;
- A gyorsítótár-beállítások nyomon követésének módja miatt ez az egyezési feltétel nem kompatibilis a következő szolgáltatásokkal:
  - Teljes gyorsítótár-kitöltés
  - Alapértelmezett belső maximális életkor
  - Belső max-kor kényszerítése
  - Az Origin no-cache figyelmen kívül hagyása
  - Belső max-állott

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Cookie paraméter

A **Nem**/**egyezik** beállítás határozza meg azokat a feltételeket, amelyek mellett a Cookie Parameter egyezési feltétel teljesül.

- **Egyezés:** A megadott cookie-t olyan értékkel rendelkező kérelemre van szükség, amely megfelel az egyezési feltételben meghatározott értékek közül legalább az egyiknek.
- **Nem felel meg:** Megköveteli, hogy a kérelem megfeleljen az alábbi feltételek egyikének:
  - Nem tartalmazza a megadott cookie-t.
  - Tartalmazza a megadott cookie-t, de az értéke nem felel meg az ebben az egyezési feltételben meghatározott értékek egyikének sem.
  
Legfontosabb információk:

- Cookie neve:
  - Mivel a helyettesítő karakteres értékek, beleértve a csillagokat (*) is, nem támogatottak a cookie-név megadásakor, csak a pontos cookie-névegyezések használhatók összehasonlításra.
  - Ennek az egyezési feltételnek egy példányánként csak egy cookie-név adható meg.
  - A cookie-k nevének összehasonlítása nem érzékeny a kis- és nagybetűkre.
- Cookie-érték:
  - Adjon meg több cookie-értéket úgy, hogy mindegyiket egyetlen szóközvel határolódmeg.
  - A cookie-értékek kihasználhatják a [helyettesítő karakteres értékeket.](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)
  - Ha nincs megadva helyettesítő karakterérték, akkor csak egy pontos egyezés felel meg ennek az egyezési feltételnek. Az "Érték" megadása például az "Érték" értéknek felel meg, de az "Érték1" vagy a "Érték2" értéknek nem.
  - A **Kis- és nagybetűk figyelmen kívül hagyása** beállítással szabályozhatja, hogy a kis- és nagybetűk megkülönböztetése összehasonlítást végezze-e a kérelem cookie-értékéhez.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez az egyezési feltétel nem kompatibilis a következő szolgáltatásokkal:
  - Teljes gyorsítótár-kitöltés
  - Alapértelmezett belső maximális életkor
  - Belső max-kor kényszerítése
  - Az Origin no-cache figyelmen kívül hagyása
  - Belső max-állott

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Cookie paraméter Regex

A Cookie Parameter Regex egyezési feltétel a cookie nevét és értékét határozza meg. [A reguláris kifejezések](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) segítségével megadhatja a kívánt cookie-értéket.

A **Nem**/**egyezik** beállítás határozza meg azokat a feltételeket, amelyek mellett a Cookie Parameter Regex egyezési feltétel teljesül.

- **Egyezés:** A megadott cookie-t tartalmazó, a megadott reguláris kifejezésnek megfelelő értékkel rendelkező cookie-t kell tartalmaznia.
- **Nem felel meg:** Megköveteli, hogy a kérelem megfeleljen az alábbi feltételek egyikének:
  - Nem tartalmazza a megadott cookie-t.
  - Tartalmazza a megadott cookie-t, de értéke nem felel meg a megadott reguláris kifejezésnek.
  
Legfontosabb információk:

- Cookie neve:
  - Mivel a reguláris kifejezések és a helyettesítő karakteres értékek, beleértve a csillagokat (*) is, nem támogatottak a cookie-név megadásakor, csak a pontos cookie-névegyezések használhatók összehasonlításra.
  - Ennek az egyezési feltételnek egy példányánként csak egy cookie-név adható meg.
  - A cookie-k nevének összehasonlítása nem érzékeny a kis- és nagybetűkre.
- Cookie-érték:
  - A cookie-érték kihasználhatja a reguláris kifejezések előnyeit.
  - A **Kis- és nagybetűk figyelmen kívül hagyása** beállítással szabályozhatja, hogy a kis- és nagybetűk megkülönböztetése összehasonlítást végezze-e a kérelem cookie-értékéhez.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez az egyezési feltétel nem kompatibilis a következő szolgáltatásokkal:
  - Teljes gyorsítótár-kitöltés
  - Alapértelmezett belső maximális életkor
  - Belső max-kor kényszerítése
  - Az Origin no-cache figyelmen kívül hagyása
  - Belső max-állott

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Ország

Az országot az országkódon keresztül adhatja meg. 

A**Nem egyezések** **Matches**/beállítás határozza meg azokat a feltételeket, amelyek mellett az Ország egyezési feltétel teljesül:

- **Egyezések**: A megadott országkódértékeket tartalmazó kérés szükséges. 
- **Nem egyezik:** Megköveteli, hogy a kérelem ne tartalmazza a megadott országkódértékeket.

Legfontosabb információk:

- Adjon meg több országkódot úgy, hogy mindegyiket egyetlen szóközvel határolódmeg.
- A helyettesítő karakterek nem támogatottak országkód megadásakor.
- Az "EU" és az "AP" országkódok nem terjednek ki az adott régiók összes IP-címére.
- Előfordulhat, hogy bizonyos kérések nem adnak vissza érvényes országkódot. A kérdőjel (?) megfelel azoknak a kérelmeknek, amelyek esetében nem lehetett meghatározni az érvényes országkódot.
- Az országkódokban a kis- és nagybetűket nem lehet figyelembe.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez az egyezési feltétel nem kompatibilis a következő szolgáltatásokkal:
  - Teljes gyorsítótár-kitöltés
  - Alapértelmezett belső maximális életkor
  - Belső max-kor kényszerítése
  - Az Origin no-cache figyelmen kívül hagyása
  - Belső max-állott

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Végrehajtási országszűrés a szabálymotor használatával

Ez az egyezési feltétel lehetővé teszi, hogy számos testreszabást hajtson végre azon hely alapján, ahonnan a kérelem származik. Az Országszűrés szolgáltatás viselkedése például a következő konfiguráción keresztül replikálható:

- URL-elérési út helyettesítő karaktere: Állítsa be az [URL-cím elérési karakterének egyezési feltételét](#url-path-wildcard) a biztonságos könyvtárhoz. 
    A relatív elérési út végéhez csatoljon egy csillagot, hogy ez a szabály korlátozza az összes gyermekhez való hozzáférést.

- Országmérkőzés: Állítsa az Ország meccsfeltételt a kívánt országkészletre.
  - Engedélyezés: Állítsa az Országegyezésfeltétel **nem egyezést,** hogy csak a megadott országok férhessenek hozzá az URL-cím elérési karakteres feltétele által meghatározott helyen tárolt tartalomhoz.
  - Letiltás: Állítsa az Országegyezés feltételt **egyezésre,** hogy a megadott országok ne férhessenek hozzá az URL-címelérési helyettesítő karakter feltétele által meghatározott helyen tárolt tartalomhoz.

- Hozzáférés megtagadása (403) szolgáltatás: Engedélyezze a [Hozzáférés megtagadása (403) szolgáltatást](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) az Országszűrés szolgáltatás engedélyezési vagy blokkrészének replikálásához.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Vevő eredete

Legfontosabb információk:

- Az Ügyfél eredete egyezési feltétel teljesül, függetlenül attól, hogy a tartalom kért egy CDN URL-címet, vagy egy él CNAME URL-t, amely rámutat a kiválasztott ügyfél eredete.
- A szabály által hivatkozott ügyfél származási konfigurációja nem törölhető az Ügyfél eredete oldalról. Mielőtt megpróbálna törölni egy ügyfél származási konfigurációját, győződjön meg arról, hogy a következő konfigurációk nem hivatkoznak rá:
  - Ügyfél eredetegyezési feltétele
  - ÉlcNAME konfiguráció
- Ne használjon ÉS HA utasítást bizonyos egyezési feltételek kombinálására. Például egy Customer Origin egyezési feltétel és egy CDN Origin egyezési feltétel kombinálása olyan egyezési mintát hoz létre, amely soha nem egyeztethető. Ezért két Ügyfél-eredetfeltétel nem kombinálható AND IF utasítással.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Eszköz

Az Eszköz egyezési feltétel azonosítja a mobileszközről érkező kérelmeket a tulajdonságai alapján. A mobileszközök észlelése a [WURFL-en](http://wurfl.sourceforge.net/)keresztül érhető el. 

A **Nem**/**egyezik** beállítás határozza meg azokat a feltételeket, amelyek mellett az Eszköz egyezési feltétel teljesül:

- **Egyezések**: A kérelmező eszközének meg kell egyeznie a megadott értékkel. 
- **Nem egyezik:** Megköveteli, hogy a kérelmező eszköze ne egyezzen meg a megadott értékkel.

Legfontosabb információk:

- A **Kis- és nagybetűk figyelmen kívül hagyása** beállítással megadhatja, hogy a megadott érték ne legyen-e a kis- és nagybetűk megkülönböztetése.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez az egyezési feltétel nem kompatibilis a következő szolgáltatásokkal:
  - Teljes gyorsítótár-kitöltés
  - Alapértelmezett belső maximális életkor
  - Belső max-kor kényszerítése
  - Az Origin no-cache figyelmen kívül hagyása
  - Belső max-állott

#### <a name="string-type"></a>Karakterlánc típusa

A WURFL-funkció általában számok, betűk és szimbólumok tetszőleges kombinációját fogadja el. A funkció rugalmas jellege miatt ki kell választania az egyezési feltételhez társított érték értelmezésének módját. Az alábbi táblázat a rendelkezésre álló beállításokat ismerteti:

Típus     | Leírás
---------|------------
Szó  | Akkor válassza ezt a lehetőséget, ha meg szeretné akadályozni, hogy a legtöbb karakter a [szó szerinti értékük](cdn-verizon-premium-rules-engine-reference.md#literal-values)használatával különleges jelentést vegyen fel.
Helyettesítő | Ezzel a beállítással kihasználhatja az összes [helyettesítő karakter]([helyettesítő karakterek) előnyeit.](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)
Regex    | Akkor válassza ezt a lehetőséget, ha [reguláris kifejezéseket](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)szeretne használni. A reguláris kifejezések hasznosak a karakterminta definiálásához.

#### <a name="wurfl-capabilities"></a>WURFL képességek

A WURFL-képesség olyan kategóriára utal, amely a mobileszközöket írja le. A kiválasztott funkció határozza meg a mobileszköz leírásának típusát, amely a kérelmek azonosítására szolgál.

Az alábbi táblázat a WURFL-képességeket és azok változóit sorolja fel a szabálymotorhoz.

> [!NOTE]
> Az alábbi változókat az **Ügyfélkérelem fejlécének módosítása** és az **Ügyfélválasz fejlécének módosítása** szolgáltatás támogatja.

Képesség | Változó | Leírás | Mintaértékek
-----------|----------|-------------|----------------
Márkanév | %{wurfl_cap_brand_name} | Az eszköz márkanevét jelző karakterlánc. | Samsung
Eszköz operációs rendszer | %{wurfl_cap_device_os} | Az eszközre telepített operációs rendszert jelző karakterlánc. | iOS
Eszköz operációs rendszerének verziója | %{wurfl_cap_device_os_version} | Az eszközre telepített operációs rendszer verziószámát jelző karakterlánc. | 1.0.1
Kettős tájolás | %{wurfl_cap_dual_orientation} | Logikai érték, amely azt jelzi, hogy az eszköz támogatja-e a kettős tájolást. | igaz
HTML preferált DTD | %{wurfl_cap_html_preferred_dtd} | A mobileszköz html-tartalomhoz javasolt dokumentumtípus-definícióját (DTD) jelző karakterlánc. | Nincs<br/>xhtml_basic<br/>html5
Kép belehúzása | %{wurfl_cap_image_inlining} | Logikai érték, amely azt jelzi, hogy az eszköz támogatja-e a Base64 kódolású képeket. | hamis
Android-rendszer | %{wurfl_vcap_is_android} | Logikai érték, amely azt jelzi, hogy az eszköz használja-e az Android operációs rendszert. | igaz
IOS-es | %{wurfl_vcap_is_ios} | Logikai érték, amely azt jelzi, hogy az eszköz iOS-t használ-e. | hamis
Smart TV-készülék | %{wurfl_cap_is_smarttv} | Logikai érték, amely azt jelzi, hogy az eszköz okostelevízió-e. | hamis
Az okostelefon | %{wurfl_vcap_is_smartphone} | Logikai érték, amely azt jelzi, hogy az eszköz okostelefon-e. | igaz
Táblagép | %{wurfl_cap_is_tablet} | Logikai érték, amely azt jelzi, hogy az eszköz táblagép-e. Ez a leírás operációs rendszertől független. | igaz
Vezeték nélküli eszköz | %{wurfl_cap_is_wireless_device} | Logikai érték, amely azt jelzi, hogy az eszköz vezeték nélküli eszköznek minősül-e. | igaz
Marketing neve | %{wurfl_cap_marketing_name} | Az eszköz marketingnevét jelző karakterlánc. | BlackBerry 8100 gyöngy
Mobil böngésző | %{wurfl_cap_mobile_browser} | Olyan karakterlánc, amely azt a böngészőt jelzi, amely tartalom kérésre szolgál az eszközről. | Chrome
Mobil böngésző verziója | %{wurfl_cap_mobile_browser_version} | Olyan karakterlánc, amely a böngésző nek azt a verzióját jelzi, amely a tartalom nak az eszközről történő kéréséhez használatos. | 31
Modell neve | %{wurfl_cap_model_name} | Az eszköz modellnevét jelző karakterlánc. | s3
Progresszív letöltés | %{wurfl_cap_progressive_download} | Logikai érték, amely azt jelzi, hogy az eszköz támogatja-e a hang és a videó lejátszását a letöltés közben. | igaz
Megjelenési dátum | %{wurfl_cap_release_date} | Egy karakterlánc, amely azt az évet és hónapot jelzi, amelyre az eszközt hozzáadta a WURFL-adatbázishoz.<br/><br/>Formátum:`yyyy_mm` | 2013_december
Felbontás magassága | %{wurfl_cap_resolution_height} | Egész szám, amely az eszköz magasságát jelzi képpontban. | 768
Felbontás szélessége | %{wurfl_cap_resolution_width} | Egész szám, amely az eszköz szélességét jelzi képpontban. | 1024

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>Szegély cname

Legfontosabb információk:

- A rendelkezésre álló élcnames-ek listája azokra a peremhálózati CNAMEs-ekre korlátozódik, amelyek et az Edge CNAMEs oldalon konfiguráltak ahhoz a platformhoz, amelyen a szabálymotor konfigurálva van.
- Mielőtt megpróbálna törölni egy szegély CNAME konfigurációt, győződjön meg arról, hogy egy Edge Cname egyezési feltétel nem hivatkozik rá. A szabályban definiált edge CNAME konfigurációk nem törölhetők az Edge CNAMEs lapról.
- Ne használjon ÉS HA utasítást bizonyos egyezési feltételek kombinálására. Például egy Edge Cname egyezési feltétel és egy ügyfél origin egyezési feltétel kombinálása olyan egyezési mintát hoz létre, amely soha nem egyeztethető. Emiatt két Edge Cname egyezési feltétel nem kombinálható egy ÉS HA utasítással.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez az egyezési feltétel nem kompatibilis a következő szolgáltatásokkal:
  - Teljes gyorsítótár-kitöltés
  - Alapértelmezett belső maximális életkor
  - Belső max-kor kényszerítése
  - Az Origin no-cache figyelmen kívül hagyása
  - Belső max-állott

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Hivatkozó tartomány

A tartalom kérésének alapjául tartozó hivatkozóhoz társított állomásnév határozza meg, hogy teljesül-e a hivatkozó tartomány feltétele.

A**Nem egyezés** **beállítás**/határozza meg azokat a feltételeket, amelyek mellett a hivatkozó tartomány egyezési feltétel teljesül:

- **Egyezések**: A megadott értékeknek megfelelően a hivatkozó állomásnévszükséges. 
- **Nem egyezik:** Megköveteli, hogy a hivatkozó állomásnév ne egyezzen meg a megadott értékkel.

Legfontosabb információk:

- Adjon meg több állomásnevet úgy, hogy mindegyiket egyetlen szóközvel határolódmeg.
- Ez az egyezési feltétel támogatja a [helyettesítő karakter értékeit.](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)
- Ha a megadott érték nem tartalmaz csillagot, annak pontosegyezésnek kell lennie az ajánló állomásnevéhez. A "mydomain.com" megadása például nem felel meg a "www.mydomain.com"-nak.
- A **Kis- és nagybetűk figyelmen kívül hagyása** beállítással szabályozhatja, hogy a kis- és nagybetűk megkülönböztetése történik-e.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez az egyezési feltétel nem kompatibilis a következő szolgáltatásokkal:
  - Teljes gyorsítótár-kitöltés
  - Alapértelmezett belső maximális életkor
  - Belső max-kor kényszerítése
  - Az Origin no-cache figyelmen kívül hagyása
  - Belső max-állott

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Kérés fejlécének szeksika

A **Nem**/**egyezik** beállítás határozza meg azokat a feltételeket, amelyek mellett a Kérésfejléc literális egyezési feltétele teljesül.

- **Egyezések**: A megadott fejlécet tartalmazó kérés szükséges. Az értékének meg kell egyeznie az ebben az egyezési feltételben meghatározott értékkel.
- **Nem felel meg:** Megköveteli, hogy a kérelem megfeleljen az alábbi feltételek egyikének:
  - Nem tartalmazza a megadott fejlécet.
  - Tartalmazza a megadott fejlécet, de értéke nem egyezik meg az ebben az egyezési feltételben meghatározottval.
  
Legfontosabb információk:

- A fejlécnév-összehasonlítások mindig nem idják meg a kis- és nagybetűket. A **Kis- és nagybetűk figyelmen kívül hagyása** beállítással szabályozhatja a fejlécértékek összehasonlításának kis- és nagybetűk megkülönböztetését.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez az egyezési feltétel nem kompatibilis a következő szolgáltatásokkal:
  - Teljes gyorsítótár-kitöltés
  - Alapértelmezett belső maximális életkor
  - Belső max-kor kényszerítése
  - Az Origin no-cache figyelmen kívül hagyása
  - Belső max-állott

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Kérelem fejléce Regex

A **Nem**/**egyezik** beállítás határozza meg azokat a feltételeket, amelyek mellett a Kérelemfejléc Regex egyezési feltételteljesül.

- **Egyezések**: A megadott fejlécet tartalmazó kérés szükséges. Az értékének meg kell egyeznie a megadott [reguláris kifejezésben megadott mintával.](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)
- **Nem felel meg:** Megköveteli, hogy a kérelem megfeleljen az alábbi feltételek egyikének:
  - Nem tartalmazza a megadott fejlécet.
  - Tartalmazza a megadott fejlécet, de értéke nem felel meg a megadott reguláris kifejezésnek.

Legfontosabb információk:

- Fejléc neve:
  - A fejlécnév-összehasonlítások nem tartalmaznak kis- és nagybetűket.
  - Cserélje le a fejlécnevében lévő szóközöket a következőre: "%20.".
- Fejléc értéke:
  - A fejlécérték kihasználhatja a reguláris kifejezések előnyeit.
  - A **Kis- és nagybetűk figyelmen kívül hagyása** beállítással szabályozhatja a fejlécértékek összehasonlításának kis- és nagybetűk megkülönböztetését.
  - Az egyezési feltétel csak akkor teljesül, ha a fejléc értéke pontosan megegyezik a megadott minták legalább egyikével.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez az egyezési feltétel nem kompatibilis a következő szolgáltatásokkal:
  - Teljes gyorsítótár-kitöltés
  - Alapértelmezett belső maximális életkor
  - Belső max-kor kényszerítése
  - Az Origin no-cache figyelmen kívül hagyása
  - Belső max-állott

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Helyettesítő fejléc kérése

A **Nem**/**egyezik** beállítás határozza meg azokat a feltételeket, amelyek mellett a Kérelemfejléc helyettesítő feltételteljesül.

- **Egyezések**: A megadott fejlécet tartalmazó kérés szükséges. Az értéknek meg kell egyeznie az ebben az egyezési feltételben definiált értékek közül legalább egynek.
- **Nem felel meg:** Megköveteli, hogy a kérelem megfeleljen az alábbi feltételek egyikének:
  - Nem tartalmazza a megadott fejlécet.
  - Tartalmazza a megadott fejlécet, de értéke nem egyezik meg a megadott értékek egyikével sem.
  
Legfontosabb információk:

- Fejléc neve:
  - A fejlécnév-összehasonlítások nem tartalmaznak kis- és nagybetűket.
  - A fejlécnevében lévő szóközöket a következőre kell cserélni: "%20". Ezzel az értékkel szóközöket is megadhat egy fejlécértékben.
- Fejléc értéke:
  - A fejlécértékek kihasználhatják a [helyettesítő karakter értékeit.](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)
  - A **Kis- és nagybetűk figyelmen kívül hagyása** beállítással szabályozhatja a fejlécértékek összehasonlításának kis- és nagybetűk megkülönböztetését.
  - Ez az egyezési feltétel akkor teljesül, ha egy fejlécérték pontosan megegyezik a megadott minták legalább egyikével.
  - Adjon meg több értéket úgy, hogy mindegyiket egyetlen szóközvel határolódmeg.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez az egyezési feltétel nem kompatibilis a következő szolgáltatásokkal:
  - Teljes gyorsítótár-kitöltés
  - Alapértelmezett belső maximális életkor
  - Belső max-kor kényszerítése
  - Az Origin no-cache figyelmen kívül hagyása
  - Belső max-állott

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Kérelem módja

A kérelemmódszer egyeztetési feltétele csak akkor teljesül, ha az eszközöket a kiválasztott kérelemmetóduson keresztül kérik. A rendelkezésre álló kérési módszerek a következők:

- GET
- HEAD
- POST
- Lehetőségek
- PUT
- DELETE
- Nyomkövetési
- Csatlakoztassa

Legfontosabb információk:

- Alapértelmezés szerint csak a GET kérésmetódus hozhat létre gyorsítótárazott tartalmat a hálózaton. Az összes többi kérelemmetódus a hálózaton keresztül történik.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez az egyezési feltétel nem kompatibilis a következő szolgáltatásokkal:
  - Teljes gyorsítótár-kitöltés
  - Alapértelmezett belső maximális életkor
  - Belső max-kor kényszerítése
  - Az Origin no-cache figyelmen kívül hagyása
  - Belső max-állott

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>Igénylési rendszer

A kérelemséma egyeztetési feltétele csak akkor teljesül, ha az eszközöket a kiválasztott protokollon keresztül kérik. A rendelkezésre álló protokollok a következők:

- HTTP
- HTTPS

Legfontosabb információk:

- A gyorsítótár-beállítások nyomon követésének módja miatt ez az egyezési feltétel nem kompatibilis a következő szolgáltatásokkal:
  - Teljes gyorsítótár-kitöltés
  - Alapértelmezett belső maximális életkor
  - Belső max-kor kényszerítése
  - Az Origin no-cache figyelmen kívül hagyása
  - Belső max-állott

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>URL elérési út könyvtára

A kérelmet a relatív elérési útja alapján azonosítja, amely kizárja a kért eszköz fájlnevét.

A **Nem**/**egyezik** beállítás határozza meg azokat a feltételeket, amelyek mellett az URL-elérési út címtára egyezési feltétel teljesül.

- **Egyezések**: A kérelemnek tartalmaznia kell egy relatív URL-elérési utat, kivéve a fájlnevet, amely megfelel a megadott URL-mintának.
- **Nem egyezik:** A kérelemnek tartalmaznia kell egy relatív URL-elérési utat, kivéve a fájlnevet, amely nem felel meg a megadott URL-mintának.

Legfontosabb információk:

- A **Relatív végpont** beállítással megadhatja, hogy az URL-összehasonlítás a tartalom-hozzáférési pont előtt vagy után kezdődött-e. A tartalom-hozzáférési pont az elérési útnak az a része, amely a Verizon CDN állomásnév és a kért eszköz relatív elérési útja (például /800001/CustomerOrigin) között jelenik meg. A helyet kiszolgálótípus (például CDN vagy ügyfél eredete) és az ügyfélszámlaszáma szerint azonosítja.

   A Következő értékek érhetők el a **Relatív beállításhoz:**
  - **Gyökér**: Azt jelzi, hogy az URL-összehasonlító pont közvetlenül a CDN állomásnév után kezdődik. 

  Például: http:\//wpc.0001. &lt;&gt;domain/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Azt jelzi, hogy az URL-összehasonlító pont a tartalom-hozzáférési pont után kezdődik (például /000001 vagy /800001/myorigin). Mivel \*a .azureedge.net CNAME alapértelmezés szerint a Verizon CDN állomásnév kezdőkönyvtárához viszonyítva jön létre, az Azure CDN-felhasználóknak az Origin értéket kell **használniuk.** 

  Például:\//&lt;https:&gt;endpoint .azureedge.net/**myfolder**/index.htm 

  Ez az URL a következő Verizon CDN\/állomásnévre mutat: http: /wpc.0001. &lt;tartomány&gt;/800001/myorigin/**myfolder**/index.htm

- A szélű CNAME URL-címet a CDN-URL-címre írja át az URL-összehasonlítás előtt.

    Például mindkét alábbi URL-cím ugyanarra az eszközre mutat, és ezért ugyanaz az URL-elérési út.
  - CDN URL:\/http: /wpc.0001. &lt;tartomány&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Szélű CNAME URL:http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    További információ:
  - Egyéni tartomány:\/https: /my.domain.com/path/asset.htm
    
    - URL elérési útja (a gyökérhez viszonyítva): /800001/CustomerOrigin/path/
    
    - URL elérési útja (az origóhoz viszonyítva): /path/

- Az URL-cím összehasonlításához használt URL-cím nek közvetlenül a kért eszköz fájlneve előtt ér véget. A záró perjel az ilyen típusú görbe utolsó karaktere.

- Cserélje le az URL-elérési út mintájában lévő szóközöket a következőre: "%20.".

- Minden URL-útvonalminta tartalmazhat egy vagy több csillagot (*), ahol minden csillag egy vagy több karakterből álló sorozatnak felel meg.

- Adjon meg több URL-útvonalat a mintában úgy, hogy mindegyiket egyetlen szóközvel határolódmeg.

    Például: */sales/ */marketing/

- Az URL-elérési út specifikációja kihasználhatja a [helyettesítő karakter értékeit.](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)

- A **Kis- és nagybetűk figyelmen kívül hagyása** beállítással szabályozhatja, hogy a rendszer elvégzi-e a kis- és nagybetűk megkülönböztetését.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>URL-elérési út kiterjesztése

A kért eszköz fájlkiterjesztése által érkező kérelmeket azonosítja.

A **Nem**/**egyezik** beállítás határozza meg azokat a feltételeket, amelyek mellett az URL-elérési út bővítmény egyezési feltételteljesül.

- **Egyezések**: A kérelem URL-címének olyan fájlkiterjesztést kell tartalmaznia, amely pontosan megfelel a megadott mintának.

   Ha például a "htm", a "htm" eszközöket adja meg, a "html" eszközök egyeztetése nem.  

- **Nem egyezik:** Az URL-kérelemnek olyan fájlkiterjesztést kell tartalmaznia, amely nem felel meg a megadott mintának.

Legfontosabb információk:

- Adja meg az **Érték** mezőben megegyező fájlkiterjesztéseket. Ne adjon meg kezdő időszakot; használja például a htm-et a .htm helyett.

- A **Kis- és nagybetűk figyelmen kívül hagyása** beállítással szabályozhatja, hogy a rendszer elvégzi-e a kis- és nagybetűk megkülönböztetését.

- Adjon meg több fájlkiterjesztést úgy, hogy az egyes kiterjesztéseket egyetlen szóközre határolód. 

    Például: htm html

- Például a "htm" megadása megfelel a "htm" eszközöknek, de a "html" eszközöknek nem.

#### <a name="sample-scenario"></a>Példaforgatókönyv

A következő mintakonfiguráció feltételezi, hogy ez az egyezési feltétel akkor teljesül, ha egy kérelem megfelel a megadott bővítmények egyikének.

Érték specifikáció: asp aspx php html

Ez az egyezési feltétel akkor teljesül, ha olyan URL-eket talál, amelyek a következő bővítményekkel végződnek:

- .asp
- .aspx
- .php
- .html

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>URL elérési út fájlneve

A kérelmeket a kért eszköz fájlneve alapján azonosítja. Ennek az egyezési feltételnek az alkalmazásában a fájlnév a kért eszköz nevét, egy időszakot és a fájlkiterjesztést (például index.html) foglalja magában.

A **Nem**/**egyezik** beállítás határozza meg azokat a feltételeket, amelyek mellett az URL-elérési út fájlneve egyezési feltétel teljesül.

- **Egyezés:** A kérelemnek tartalmaznia kell egy olyan fájlnevet az URL-címében, amely megfelel a megadott mintának.
- **Nem egyezik:** A kérelemnek tartalmaznia kell egy olyan fájlnevet az URL-elérési útjában, amely nem felel meg a megadott mintának.

Legfontosabb információk:

- A **Kis- és nagybetűk figyelmen kívül hagyása** beállítással szabályozhatja, hogy a rendszer elvégzi-e a kis- és nagybetűk megkülönböztetését.

- Több fájlkiterjesztés megadásához válassza el az egyes kiterjesztéseket egyetlen szóközrel.

    Például: index.htm index.html

- A fájlnév-érték szóközeit cserélje le a következőre: "%20.".

- A fájlnévértékek kihasználhatják a [helyettesítő karakter értékeket.](cdn-verizon-premium-rules-engine-reference.md#wildcard-values) Minden fájlnévminta egy vagy több csillagból (*) állhat, ahol mindegyik csillag egy vagy több karakterből álló sorozatnak felel meg.

- Ha nincs megadva helyettesítő karakter, akkor csak egy pontos egyezés felel meg ennek az egyezési feltételnek.

    A "presentation.ppt" megadása például egy "presentation.ppt" nevű eszköznek felel meg, de a "presentation.pptx" névnek nem.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>URL-elérési út konstans

Összehasonlítja a kérelem URL-címét, beleértve a fájlnevet is, a megadott értékkel.

A **Nem**/**egyezik** beállítás határozza meg azokat a feltételeket, amelyek mellett az URL-cím címkés címkés egyezési feltételteljesül.

- **Egyezések**: A kérelemnek tartalmaznia kell egy URL-útvonalat, amely megfelel a megadott mintának.
- **Nem egyezik:** A kérelemnek olyan URL-útvonalat kell tartalmaznia, amely nem felel meg a megadott mintának.

Legfontosabb információk:

- A **Relatív végpont** beállítással megadhatja, hogy az URL-összehasonlító pont a tartalom-hozzáférési pont előtt vagy után kezdődött-e. 

    A Következő értékek érhetők el a **Relatív beállításhoz:**
  - **Gyökér**: Azt jelzi, hogy az URL-összehasonlító pont közvetlenül a CDN állomásnév után kezdődik.

    Például: http:\//wpc.0001. &lt;&gt;tartomány/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Azt jelzi, hogy az URL-összehasonlító pont a tartalom-hozzáférési pont után kezdődik (például /000001 vagy /800001/myorigin). Mivel \*a .azureedge.net CNAME alapértelmezés szerint a Verizon CDN állomásnév kezdőkönyvtárához viszonyítva jön létre, az Azure CDN-felhasználóknak az Origin értéket kell **használniuk.** 

    Például:\//&lt;https:&gt;endpoint .azureedge.net/**myfolder/index.htm**

  Ez az URL a következő Verizon CDN\/állomásnévre mutat: http: /wpc.0001. &lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- A szélű CNAME URL-címet a CDN-URL-címre írja át az URL-összehasonlítás előtt.

Például mindkét alábbi URL-cím ugyanarra az elemre mutat, és ezért ugyanaz az URL-elérési út:

- CDN URL:\/http: /wpc.0001. &lt;tartomány&gt;/800001/CustomerOrigin/path/asset.htm
- Szélű CNAME URL:http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    További információ:
    
    - URL elérési útja (a gyökérhez viszonyítva): /800001/CustomerOrigin/path/asset.htm
   
    - URL elérési útja (az origóhoz viszonyítva): /path/asset.htm

- Az URL-cím lekérdezési karakterláncait a rendszer figyelmen kívül hagyja.
- A **Kis- és nagybetűk figyelmen kívül hagyása** beállítással szabályozhatja, hogy a rendszer elvégzi-e a kis- és nagybetűk megkülönböztetését.
- Az egyezési feltételhez megadott értéket a rendszer összehasonlítja az ügyfél által küldött pontos kérelem relatív elérési útjával.

- Egy adott könyvtárhoz intézett összes kérelem egyeztetéséhez használja az [URL elérési út könyvtárát](#url-path-directory) vagy az [URL-elérési út helyettesítő](#url-path-wildcard) feltételét.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>URL elérési útja Regex

Összehasonlítja a kérelem URL-címét a megadott [reguláris kifejezéssel.](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)

A **Nem**/**egyezik** beállítás határozza meg azokat a feltételeket, amelyek mellett az URL-elérési út Regex egyezési feltételteljesül.

- **Egyezés:** A kérésnek tartalmaznia kell egy URL-útvonalat, amely megfelel a megadott reguláris kifejezésnek.
- **Nem egyezik:** A kérelemnek olyan URL-útvonalat kell tartalmaznia, amely nem felel meg a megadott reguláris kifejezésnek.

Legfontosabb információk:

- A szélű CNAME URL-címet az URL-összehasonlítás előtt újraírják egy CDN URL-címre.

    Például mindkét URL ugyanarra az eszközre mutat, és ezért ugyanaz az URL-elérési út.

     - CDN URL:\/http: /wpc.0001. &lt;tartomány&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL:\/http: /my.domain.com/path/asset.htm

    További információ:
    
     - URL elérési útja: /800001/CustomerOrigin/path/asset.htm

- Az URL-cím lekérdezési karakterláncait a rendszer figyelmen kívül hagyja.
    
- A **Kis- és nagybetűk figyelmen kívül hagyása** beállítással szabályozhatja, hogy a rendszer elvégzi-e a kis- és nagybetűk megkülönböztetését.
    
- Az URL-elérési út szóközeit a következőre kell cserélni: "%20".

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>URL-elérési út helyettesítő karaktere

Összehasonlítja a kérelem relatív URL-elérési útját a megadott helyettesítő karaktermintával.

A **Nem**/**egyezik** beállítás határozza meg azokat a feltételeket, amelyek mellett az URL-címhelyettesítő karakteres egyezési feltételteljesül.

- **Egyezések**: A kérelemnek tartalmaznia kell egy URL-útvonalat, amely megfelel a megadott helyettesítő karaktermintának.
- **Nem egyezik:** A kérelemnek olyan URL-útvonalat kell tartalmaznia, amely nem felel meg a megadott helyettesítő karaktermintának.

Legfontosabb információk:

- **A beállításhoz viszonyítva:** Ez a beállítás határozza meg, hogy az URL-összehasonlító pont a tartalom-hozzáférési pont előtt vagy után kezdődik-e.

   Ez a beállítás a következő értékeket tartalmazhatja:
     - **Gyökér**: Azt jelzi, hogy az URL-összehasonlító pont közvetlenül a CDN állomásnév után kezdődik.

       Például: http:\//wpc.0001. &lt;&gt;tartomány/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Azt jelzi, hogy az URL-összehasonlító pont a tartalom-hozzáférési pont után kezdődik (például /000001 vagy /800001/myorigin). Mivel \*a .azureedge.net CNAME alapértelmezés szerint a Verizon CDN állomásnév kezdőkönyvtárához viszonyítva jön létre, az Azure CDN-felhasználóknak az Origin értéket kell **használniuk.** 

       Például:\//&lt;https:&gt;endpoint .azureedge.net/**myfolder/index.htm**

     Ez az URL a következő Verizon CDN\/állomásnévre mutat: http: /wpc.0001. &lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- A szélű CNAME URL-címet az URL-összehasonlítás előtt újraírják egy CDN URL-címre.

    Például mindkét alábbi URL-cím ugyanarra az elemre mutat, és ezért ugyanaz az URL-elérési út:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Szélű CNAME URL:http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    További információ:
    
     - URL elérési útja (a gyökérhez viszonyítva): /800001/CustomerOrigin/path/asset.htm
    
     - URL elérési útja (az origóhoz viszonyítva): /path/asset.htm
    
- Adjon meg több URL-útvonalat úgy, hogy mindegyiket egyetlen szóközvel határolódmeg.

   Például: /marketing/asset.* /sales/*.htm

- Az URL-cím lekérdezési karakterláncait a rendszer figyelmen kívül hagyja.
    
- A **Kis- és nagybetűk figyelmen kívül hagyása** beállítással szabályozhatja, hogy a rendszer elvégzi-e a kis- és nagybetűk megkülönböztetését.
    
- Cserélje le az URL-elérési út szóközeit a következőre: "%20.".
    
- Az URL-elérési úthoz megadott érték kihasználhatja a [helyettesítő karakter értékeit.](cdn-verizon-premium-rules-engine-reference.md#wildcard-values) Minden URL-útvonalminta tartalmazhat egy vagy több csillagot (*), ahol minden csillag egy vagy több karakterből álló sorozatnak felelhet meg.

#### <a name="sample-scenarios"></a>Példaforgatókönyvek

Az alábbi táblázatban szereplő mintakonfigurációk feltételezik, hogy ez az egyezési feltétel akkor teljesül, ha egy kérelem megfelel a megadott URL-mintának:

Érték                   | A következőhöz viszonyítva:    | Eredmény 
------------------------|----------------|-------
*/test.html */test.php  | Gyökér vagy eredet | Ez a minta egyezteti a "test.html" vagy a "test.php" nevű eszközökre vonatkozó kérésekkel bármely mappában.
/80ABCD/origó/szöveg/*   | Gyökér           | Ez a minta akkor egyeztetve lesz, amikor a kért eszköz megfelel az alábbi feltételeknek: <br />- Egy "eredet" nevű ügyfél származáson kell lennie. <br />- A relatív elérési útnak a "szöveg" nevű mappával kell kezdődnie. Ez azt illeti, a kért eszköz vagy a "szöveg" mappában vagy annak egyik rekurzív almappájában található.
*/css/* */js/*          | Gyökér vagy eredet | Ezt a mintát egycs vagy js mappát tartalmazó összes CDN vagy edge CNAME URL-lel párosítja.
*.jpg *.gif *.png       | Gyökér vagy eredet | Ezt a mintát a .jpg, .gif vagy .png végződésű összes CDN- vagy szegélyű CNAME URL-cím felel meg. A minta megadásának másik módja az [URL-elérési út kiterjesztése egyezési feltétel.](#url-path-extension)
/images/* /media/*      | Forrás         | Ezt a mintát cdn- vagy szegélyes CNAME URL-ek egészül ki, amelyek relatív elérési útja egy "kép" vagy "adathordozó" mappával kezdődik. <br />- CDN URL:\/http: /wpc.0001. &lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Minta él CNAME\/URL: http: /cdn.mydomain.com/images/sales/event1.png

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>URL-lekérdezés literális

Összehasonlítja a kérelem lekérdezési karakterláncát a megadott értékkel.

A **Nem**/**egyezik** beállítás határozza meg azokat a feltételeket, amelyek mellett az URL-lekérdezés konstans egyezési feltétele teljesül.

- **Egyezés:** A kérelemnek tartalmaznia kell egy URL-lekérdezési karakterláncot, amely megfelel a megadott lekérdezési karakterláncnak.
- **Nem egyezik:** A kérelemnek olyan URL-lekérdezési karakterláncot kell tartalmaznia, amely nem felel meg a megadott lekérdezési karakterláncnak.

Legfontosabb információk:

- Csak a pontos lekérdezési karakterlánc-egyezések felelnek meg ennek az egyezési feltételnek.
    
- A **Kis- és nagybetűk figyelmen kívül hagyása** beállítással szabályozhatja a lekérdezési karakterláncok összehasonlításának kis- és nagybetűk megkülönböztetését.
    
- Ne írjon be sorelső kérdőjelet (?) a lekérdezési karakterlánc értékének szövegében.
    
- Bizonyos karakterek URL-kódolást igényelnek. A százalékos szimbólummal az URL-cím a következő karaktereket kódolja:

   Karakter | URL-kódolás
   ----------|---------
   Space (Szóköz)     | %20
   &         | %25

- A gyorsítótár-beállítások nyomon követésének módja miatt ez az egyezési feltétel nem kompatibilis a következő szolgáltatásokkal:
   - Teljes gyorsítótár-kitöltés
   - Alapértelmezett belső maximális életkor
   - Belső max-kor kényszerítése
   - Az Origin no-cache figyelmen kívül hagyása
   - Belső max-állott

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>URL-lekérdezési paraméter

A megadott lekérdezési karakterlánc-paramétert tartalmazó kérelmeket azonosítja. Ez a paraméter egy megadott mintának megfelelő értékre van beállítva. A lekérdezési karakterlánc paraméterei (például parameter=value) a kérelem URL-címében határozzák meg, hogy teljesül-e ez a feltétel. Ez az egyezési feltétel a lekérdezési karakterlánc paraméterét a neve alapján azonosítja, és egy vagy több értéket fogad el a paraméterértékhez. 

A **Nem**/**egyezik** beállítás határozza meg azokat a feltételeket, amelyek mellett az URL-lekérdezési paraméter megfelel.

- **Egyezés:** A megadott paramétert olyan értékkel rendelkező kérelemre van szükség, amely megfelel az egyezési feltételben meghatározott értékek közül legalább egynek.
- **Nem felel meg:** Megköveteli, hogy a kérelem megfeleljen az alábbi feltételek egyikének:
  - Nem tartalmazza a megadott paramétert.
  - Tartalmazza a megadott paramétert, de értéke nem felel meg az ebben az egyezési feltételben definiált értékek egyikének sem.

Ez az egyezési feltétel egyszerű módot biztosít a paraméternév/értékkombinációk megadására. Ha egy lekérdezési karakterlánc-paraméternek megfelel, nagyobb rugalmasságot szeretne, fontolja meg az [URL-lekérdezési helyettesítő karakter](#url-query-wildcard) egyeztetési feltétel használatát.

Legfontosabb információk:

- Ennek az egyezési feltételnek egy példányánként csak egyetlen URL-lekérdezési paraméternév adható meg.
    
- Mivel a helyettesítő karakteres értékek nem támogatottak, ha paraméternév van megadva, csak a pontos paraméternév-egyezések jogosultak az összehasonlításra.
- A paraméterérték(ek) [helyettesítő karaktereket](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)is tartalmazhatnak.
   - Minden paraméterérték-minta egy vagy több csillagból (*) állhat, ahol minden csillag egy vagy több karakterből álló sorozatnak felelhet meg.
   - Bizonyos karakterek URL-kódolást igényelnek. A százalékos szimbólummal az URL-cím a következő karaktereket kódolja:

       Karakter | URL-kódolás
       ----------|---------
       Space (Szóköz)     | %20
       &         | %25

- Adjon meg több lekérdezési karakterlánc paraméterértékét úgy, hogy mindegyiket egyetlen szóközvel határolódmeg. Ez az egyezési feltétel akkor teljesül, ha egy kérelem a megadott név/érték kombinációk egyikét tartalmazza.

   - 1. példa:

     - Konfigurálás:

       ÉrtékA Érték B

     - Ez a konfiguráció megfelel a következő lekérdezési karakterlánc-paramétereknek:

       Paraméter1=Érték
    
       Paraméter1=B érték

   - 2. példa

     - Konfigurálás: 

        Érték%20A érték%20b

     - Ez a konfiguráció megfelel a következő lekérdezési karakterlánc-paramétereknek:

       Paraméter1=Érték%20A

       Paraméter1=Érték%20B

- Ez az egyezési feltétel csak akkor teljesül, ha a megadott lekérdezési karakterlánc-név/érték kombinációk közül legalább egypontos egyezést alkalmaz.

   Ha például az előző példában a konfigurációt használja, a "Parameter1=ValueAdd" paraméternév/értékkombináció nem tekinthető egyezésnek. Ha azonban az alábbi értékek valamelyikét adja meg, az megegyezik a név/érték kombinációval:

   - ValueA ValueB értékhozzáadása
   - ÉrtékA* Érték B

- A **Kis- és nagybetűk figyelmen kívül hagyása** beállítással szabályozhatja a lekérdezési karakterláncok összehasonlításának kis- és nagybetűk megkülönböztetését.
    
- A gyorsítótár-beállítások nyomon követésének módja miatt ez az egyezési feltétel nem kompatibilis a következő szolgáltatásokkal:
   - Teljes gyorsítótár-kitöltés
   - Alapértelmezett belső maximális életkor
   - Belső max-kor kényszerítése
   - Az Origin no-cache figyelmen kívül hagyása
   - Belső max-állott

#### <a name="sample-scenarios"></a>Mintaforgatókönyvek

A következő példa bemutatja, hogyan működik ez a lehetőség bizonyos helyzetekben:

Név  | Érték |  Eredmény
------|-------|--------
Felhasználó  | Joe   | Ez a minta akkor egyeztetve lesz, amikor a kért URL lekérdezési karakterlánca "?user=joe" lesz.
Felhasználó  | *     | Ez a minta akkor egyezteti, ha a kért URL-cím lekérdezési karakterlánca felhasználói paramétert tartalmaz.
E-mail | Joe\* | Ez a minta akkor egyezteti, amikor a kért URL-cím lekérdezési karakterlánca "Joe" kezdetű e-mail paramétert tartalmaz.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>URL-lekérdezés Regex

A megadott lekérdezési karakterlánc-paramétert tartalmazó kérelmeket azonosítja. Ez a paraméter egy megadott [reguláris kifejezésnek](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)megfelelő értékre van beállítva.

A **Nem**/**egyezik** beállítás határozza meg azokat a feltételeket, amelyek mellett az URL-lekérdezés Regex egyezési feltételteljesül.

- **Egyezés:** A kérelemnek tartalmaznia kell egy URL-lekérdezési karakterláncot, amely megfelel a megadott reguláris kifejezésnek.
- **Nem egyezik:** A kérelemnek olyan URL-lekérdezési karakterláncot kell tartalmaznia, amely nem felel meg a megadott reguláris kifejezésnek.

Legfontosabb információk:

- Csak a megadott reguláris kifejezéspontos egyezésefelel meg ennek az egyezési feltételnek.
    
- A **Kis- és nagybetűk figyelmen kívül hagyása** beállítással szabályozhatja a lekérdezési karakterláncok összehasonlításának kis- és nagybetűk megkülönböztetését.
    
- Ennek a beállításnak az alkalmazásában a lekérdezési karakterlánc a lekérdezési karakterlánc kérdőjel (?) határolója utáni első karakterrel kezdődik.
    
- Bizonyos karakterek URL-kódolást igényelnek. A százalékos szimbólummal az URL-cím a következő karaktereket kódolja:

   Karakter | URL-kódolás | Érték
   ----------|--------------|------
   Space (Szóköz)     | %20          | \%20
   &         | %25          | \%25

   Ne feledje, hogy a százalékos szimbólumokat meg kell kerülni.

- Dupla escape speciális reguláris kifejezés \^karakterek (például $.+), hogy a reguláris kifejezés ben fordított perjel szerepeljen.

   Például:

   Érték | Értelmezve 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- A gyorsítótár-beállítások nyomon követésének módja miatt ez az egyezési feltétel nem kompatibilis a következő szolgáltatásokkal:
   - Teljes gyorsítótár-kitöltés
   - Alapértelmezett belső maximális életkor
   - Belső max-kor kényszerítése
   - Az Origin no-cache figyelmen kívül hagyása
   - Belső max-állott

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>URL-lekérdezési helyettesítő karakter

Összehasonlítja a megadott értéket(oka)t a kérelem lekérdezési karakterláncával.

A **Nem**/**egyezik** beállítás határozza meg azokat a feltételeket, amelyek mellett az URL-lekérdezés helyettesítő feltétele teljesül.

- **Egyezések**: A kérelemnek tartalmaznia kell egy URL-lekérdezési karakterláncot, amely megfelel a megadott helyettesítő karakter értékének.
- **Nem egyezik:** A kérelemnek olyan URL-lekérdezési karakterláncot kell tartalmaznia, amely nem felel meg a megadott helyettesítő karakter értékének.

Legfontosabb információk:

- Ennek a beállításnak az alkalmazásában a lekérdezési karakterlánc a lekérdezési karakterlánc kérdőjel (?) határolója utáni első karakterrel kezdődik.
- A paraméterértékek tartalmazhatnak [helyettesítő karakterértékeket:](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)
   - Minden paraméterérték-minta egy vagy több csillagból (*) állhat, ahol minden csillag egy vagy több karakterből álló sorozatnak felelhet meg.
   - Bizonyos karakterek URL-kódolást igényelnek. A százalékos szimbólummal az URL-cím a következő karaktereket kódolja:

     Karakter | URL-kódolás
     ----------|---------
     Space (Szóköz)     | %20
     &         | %25

- Adjon meg több értéket úgy, hogy mindegyiket egyetlen szóközvel határolódmeg.

   Például: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Csak a megadott lekérdezési karakterlánc-minták legalább egyikének pontos egyezése felel meg ennek az egyezési feltételnek.
    
- A **Kis- és nagybetűk figyelmen kívül hagyása** beállítással szabályozhatja a lekérdezési karakterláncok összehasonlításának kis- és nagybetűk megkülönböztetését.
    
- A gyorsítótár-beállítások nyomon követésének módja miatt ez az egyezési feltétel nem kompatibilis a következő szolgáltatásokkal:
   - Teljes gyorsítótár-kitöltés
   - Alapértelmezett belső maximális életkor
   - Belső max-kor kényszerítése
   - Az Origin no-cache figyelmen kívül hagyása
   - Belső max-állott

#### <a name="sample-scenarios"></a>Mintaforgatókönyvek

A következő példa bemutatja, hogyan működik ez a lehetőség bizonyos helyzetekben:

 Név                 | Leírás
 ---------------------|------------
user=joe              | Ez a minta akkor egyeztetve lesz, amikor a kért URL lekérdezési karakterlánca "?user=joe" lesz.
\*user\* \*= optout=\* | Ez a minta akkor egyeztetve lesz, ha a CDN URL-lekérdezés a felhasználó vagy a letiltás i.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>További lépések

- [Az Azure Content Delivery Network áttekintése](cdn-overview.md)
- [Szabályok motorjának hivatkozása](cdn-verizon-premium-rules-engine-reference.md)
- [Szabálymotor feltételes kifejezései](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Szabályok motor jellemzői](cdn-verizon-premium-rules-engine-reference-features.md)
- [Az alapértelmezett HTTP-viselkedés felülbírálása a szabálymotor használatával](cdn-verizon-premium-rules-engine.md)
