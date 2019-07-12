---
title: A Verizon prémium szintű Azure CDN-szabályok szabálymotor egyezési feltételei |} A Microsoft Docs
description: Az Azure Content Delivery Network – Verizon prémium szintű dokumentációja szabályok szabálymotor egyezési feltételei.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 1660dca34b2f128ef5889145fcdeed0d2523b9bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593204"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Szabálymotor Verizon prémium szintű Azure CDN a feltételeknek megfelelő

Ez a cikk felsorolja az elérhető egyezési feltételei a az Azure Content Delivery Network (CDN) a Verizon prémium szintű részletes leírását [szabálymotorral](cdn-verizon-premium-rules-engine.md).

A szabály második része az egyezési feltétellel. Egyeztetési feltételt azonosítja az adott típusú kérelmet szolgáltatások történik.

Ha például az egyeztetési feltételt is használhatja:

- Kérelmek szűrése a tartalom egy adott helyen.
- Kérelmek szűrése egy adott IP-cím vagy ország/régió alapján generált.
- Kérelmek szűrése a fejléc-információkat.

## <a name="always-match-condition"></a>Mindig a feltételnek megfelelő

A mindig az egyezési feltétellel alapértelmezés szerinti szolgáltatások összes kérelemre érvényes legyen.

Name (Név) | Cél
-----|--------
[Mindig](#always) | Alapértelmezett számos funkciót összes kérelemre érvényes legyen.

## <a name="device-match-condition"></a>Eszköz egyezési feltételei

Az eszköz az egyezési feltétellel kérelmek egy mobileszközzel a tulajdonságok alapján azonosítja.  

Name (Név) | Cél
-----|--------
[Device](#device) | A kérelmek egy mobileszközzel a tulajdonságok alapján azonosítja.

## <a name="location-match-conditions"></a>Hely egyezési feltételei

A hely egyezési feltételei kéréseket a kérelmező helye alapján azonosíthatja.

Name (Név) | Cél
-----|--------
[AS-szám](#as-number) | Egy adott hálózat kérelmekkel azonosítja.
[Ország](#country) | A megadott országokból/régiókból származó kérelmekkel azonosítja.

## <a name="origin-match-conditions"></a>Forrás egyezési feltételei

A forrás egyezési feltételei azonosítsa a kéréseket, amelyek a Content Delivery Network-tároló vagy egy ügyfél eredeti kiszolgálóra mutasson.

Name (Név) | Cél
-----|--------
[CDN-forrás](#cdn-origin) | Azonosítja a Content Delivery Network-storage-ban tárolt tartalomhoz.
[Ügyfél kezdőpontja](#customer-origin) | Egy adott ügyfél forrás kiszolgálón tárolt tartalomhoz azonosítja.

## <a name="request-match-conditions"></a>Kérelem egyezési feltételei

A kérelem egyezési feltételei kérelmet tulajdonságaik alapján azonosíthatja.

Name (Név) | Cél
-----|--------
[Ügyfél IP-címe](#client-ip-address) | Egy adott IP-címről kérelmekkel azonosítja.
[Cookie-k paraméter](#cookie-parameter) | Ellenőrzi a megadott értéket minden kéréshez társított cookie-kat.
[Cookie-k paramétert reguláris kifejezés](#cookie-parameter-regex) | A cookie-kat, az a megadott reguláris kifejezést az egyes kérelmekkel társított ellenőrzi.
[Edge Cname](#edge-cname) | Kérelmek, amelyek egy adott edge CNAME azonosítja.
[Hivatkozó tartomány](#referring-domain) | A megadott állomásnevekről említett kérelmek azonosítja.
[Kérelem fejléce szövegkonstans](#request-header-literal) | A megadott fejléc megadott értéke tartalmazó kérelmek azonosítja.
[Kérelem fejléce reguláris kifejezés](#request-header-regex) | A megadott fejléc értéke, amely megfelel a megadott reguláris kifejezést tartalmazó kérelmek azonosítja.
[Kérelem fejléce helyettesítő karakter](#request-header-wildcard) | A megadott fejléc értéke, amely megfelel a megadott minta tartalmazó kérelmek azonosítja.
[Kérelmi metódus](#request-method) | A HTTP-metódus azonosítja a kérelmeket.
[Kérelem séma](#request-scheme) | A HTTP protokoll alapján azonosítja a kérelmek.

## <a name="url-match-conditions"></a>URL-cím egyezési feltételei

Az URL-cím egyezési feltételei azonosítsa az URL-címeken alapuló kérelmeket.

Name (Név) | Cél
-----|--------
[URL-cím elérési út könyvtár](#url-path-directory) | Azonosítja a kérelmeket azok relatív elérési út.
[URL-cím elérési út bővítmény](#url-path-extension) | A fájlnév-kiterjesztésük alapján azonosítja a kérelmeket.
[URL-cím elérési_út fájlnév](#url-path-filename) | A fájl neve azonosítja a kérelmeket.
[URL-cím elérési út szövegkonstans](#url-path-literal) | A kérelem relatív elérési út és a megadott érték hasonlítja össze.
[URL Path Regex](#url-path-regex) | A kérelem relatív elérési útja a megadott reguláris kifejezés hasonlítja össze.
[URL-cím elérési út helyettesítő karakter](#url-path-wildcard) | A kérelem relatív elérési útja a megadott minta hasonlítja össze.
[URL-cím lekérdezési szövegkonstans](#url-query-literal) | A kérelem lekérdezési karakterláncát a megadott érték hasonlítja össze.
[URL-cím lekérdezési paraméter](#url-query-parameter) | A megadott lekérdezési karakterlánc-paraméter értéke, amely egy megadott mintának megfelelő tartalmazó kérelmek azonosítja.
[URL-cím lekérdezési reguláris kifejezés](#url-query-regex) | A megadott lekérdezési karakterlánc-paraméter értéke, amely megfelel a megadott reguláris kifejezést tartalmazó kérelmek azonosítja.
[URL-cím lekérdezési helyettesítő karakter](#url-query-wildcard) | A megadott értéket a kérelem lekérdezési karakterláncában elleni hasonlítja össze.

## <a name="reference-for-rules-engine-match-conditions"></a>Szabálymotor egyezési feltételei referenciája

---

### <a name="always"></a>Mindig

A mindig az egyezési feltétellel alapértelmezés szerinti szolgáltatások összes kérelemre érvényes legyen.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>AS-szám

Az AS-számok hálózati határozza meg az autonóm rendszer száma (ASN). 

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amelyben az AS-számot a feltételnek megfelelő feltételek teljesülése:

- **Egyezések**: Megköveteli, hogy az ASN-t, az ügyfél hálózati megegyezik a megadott ASN-ek valamelyikét. 
- **Nem felel meg**: Szükséges, hogy az ASN-t, az ügyfél hálózati nem egyezik a megadott ASN-ek valamelyikét.

Legfontosabb tudnivalókat:

- Adja meg az egy szóköz pedig külön határoló több ASN-eket. Például 64514 64515 megfelel a kérelmek, amelyek 64514 vagy 64515 érkezik.
- Előfordulhat, hogy bizonyos kérelmek nem ad vissza egy érvényes ASN. A kérdőjel (?) egyezni fog a kérelmeket, amelyhez érvényes ASN nem sikerült meghatározni.
- Adja meg a teljes ASN-t a kívánt hálózathoz. Hiányos értékek nem található, karakterként lesz.
- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
  - Complete Cache Fill
  - Alapértelmezett belső Max-Age
  - Belső Max-Age kényszerítése
  - Ignore Origin No-Cache
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>CDN-forrás

A CDN-forrás egyezés feltétel teljesül mindkét következő feltétel teljesülése esetén:

- A kért tartalom CDN storage-ból.
- A kérelem URI-t a tartalom-hozzáférési pont (például /000001), ez az egyezési feltétellel meghatározott típusú használja:
  - A CDN URL-CÍME: A kérés URI azonosítója a kiválasztott tartalom-hozzáférési pontot kell tartalmaznia.
  - Edge CNAME URL: A kiválasztott tartalom-hozzáférési pont a megfelelő edge CNAME konfigurációt kell mutatnia.
  
Legfontosabb tudnivalókat:

- A tartalom-hozzáférési pont azonosítja a szolgáltatás, amely szolgálhat a kért tartalmat.
- Ne használja az és a egy IF utasítást úgy, hogy bizonyos egyezési feltételei. Például az ügyfél kezdőpontja egyeztetési feltételt CDN Origin egyeztetési feltételt kombinálásával hozna létre-egyeztetési minta, amely sikerült nikdy existovat shoda. Ezért két CDN Origin egyezési feltételei keresztül egy és IF utasítás nelze kombinovat.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Ügyfél IP-címe

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amelyek alapján az ügyfél IP-cím a feltételnek megfelelő feltételek teljesülése:

- **Egyezések**: Megköveteli, hogy az ügyfél IP-cím megegyezik a megadott IP-címek valamelyikét. 
- **Nem felel meg**: Megköveteli, hogy az ügyfél IP-cím nem egyezik a megadott IP-címek valamelyikét. 

Legfontosabb tudnivalókat:

- Használjon CIDR-jelölésrendszerben.
- Adjon meg több IP-címek és/vagy IP-címblokkok határoló egyenként a szóköz. Példa:
  - **IPv4-példa**: 1.2.3.4 10.20.30.40 megegyezik bármilyen címről vagy 1.2.3.4 vagy 10.20.30.40 érkező kérelmeket.
  - **IPv6-alapú példa**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 megegyezik bármilyen cím 1:2:3:4:5:6:7:8 vagy 10:20:30:40:50:60:70:80 érkező kérelmeket.
- IP-Címblokk szintaxisa a kiindulási IP-cím perjellel és az előtag méretét. Példa:
  - **IPv4-példa**: 5.5.5.64/26 megegyezik bármilyen keresztül 5.5.5.127 5.5.5.64-címekről érkező kérelmeket.
  - **IPv6-alapú példa**: 1:2:3: / 48 megegyezik bármilyen származó címeket 1:2:3:0:0:0:0:0 1:2:3:ffff:ffff:ffff:ffff:ffff keresztül érkező kérelmeket.
- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
  - Complete Cache Fill
  - Alapértelmezett belső Max-Age
  - Belső Max-Age kényszerítése
  - Ignore Origin No-Cache
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Cookie-k paraméter

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt a cookie-k paraméter feltételnek megfelelő feltételek teljesülése.

- **Egyezések**: Kell tartalmaznia a megadott cookie-t, amely megfelel az ezen az egyezési feltétellel meghatározott értékek közül legalább egy értéket a kérést.
- **Nem felel meg**: Megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott cookie nem tartalmaz.
  - A megadott cookie-t tartalmaz, de az érték nem egyezik meg az értékeket, hogy ez az egyezési feltétellel vannak meghatározva.
  
Legfontosabb tudnivalókat:

- Cookie neve:
  - Helyettesítő karakteres értékek, többek között a csillag (*), nem támogatottak, ha egy cookie-nevet ad meg, mert csak pontos cookie neve egyezik jogosultak az összehasonlítást.
  - Ez az egyezési feltétellel-példányonként csak egy egyetlen cookie-nevet adható meg.
  - Cookie neve összehasonlítások megkülönböztetik a kis-és nagybetűket.
- Cookie-értéket:
  - Adja meg a cookie-k több érték határoló egyenként a szóköz.
  - A cookie-értéket kihasználhatják a [helyettesítő karakteres értékek](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Ha egy helyettesítő karakteres érték nem lett megadva, csak pontos egyezés kielégíteni ezt az egyezési feltétellel. Például adja meg a "Value" egyezni fog "Value", de nem "Érték1" vagy "Value2."
  - Használja a **esetben figyelmen kívül hagyása** -e a kis-és nagybetűket összehasonlítás a kérelem cookie-k értékkel ellenőrzési lehetőséget.
- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
  - Complete Cache Fill
  - Alapértelmezett belső Max-Age
  - Belső Max-Age kényszerítése
  - Ignore Origin No-Cache
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Cookie-k paramétert reguláris kifejezés

A cookie-k paramétert reguláris kifejezést az egyezési feltétellel határozza meg, a cookie nevét és értékét. Használhat [reguláris kifejezések](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) meghatározásához kívánt cookie-értéket.

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt a cookie-k paraméter Regex feltételnek megfelelő feltételek teljesülése.

- **Egyezések**: Egy kérelem tartalmazza a megadott reguláris kifejezésnek megfelelő értéket a megadott cookie-t igényel.
- **Nem felel meg**: Megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott cookie nem tartalmaz.
  - A megadott cookie-t tartalmaz, de az érték nem egyezik meg a meghatározott reguláris kifejezéssel.
  
Legfontosabb tudnivalókat:

- Cookie neve:
  - Reguláris kifejezések és a helyettesítő karakteres értékek, többek között a csillag (*), nem támogatottak, ha egy cookie-nevet ad meg, mert csak a pontos cookie neve egyezik jogosultak az összehasonlítást.
  - Ez az egyezési feltétellel-példányonként csak egy egyetlen cookie-nevet adható meg.
  - Cookie neve összehasonlítások megkülönböztetik a kis-és nagybetűket.
- Cookie-értéket:
  - A cookie-értéket reguláris kifejezéseket is előnyeit.
  - Használja a **esetben figyelmen kívül hagyása** -e a kis-és nagybetűket összehasonlítás a kérelem cookie-k értékkel ellenőrzési lehetőséget.
- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
  - Complete Cache Fill
  - Alapértelmezett belső Max-Age
  - Belső Max-Age kényszerítése
  - Ignore Origin No-Cache
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Country

Megadhatja, hogy egy ország keresztül az országkódot. 

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt az ország feltételnek megfelelő feltételek teljesülése:

- **Egyezések**: A kérelem tartalmazza a megadott ország kódértékek igényel. 
- **Nem felel meg**: Megköveteli, hogy a kérelem nem tartalmazza a megadott ország kódértékek.

Legfontosabb tudnivalókat:

- Adja meg az országkódok több határoló egyenként a szóköz.
- Helyettesítő karakterek nem használhatók, amikor az országkódot ad meg.
- Az "EU" és "AP" országkódok nem tartoznak bele az összes IP-címek ezekben a régiókban.
- Bizonyos kérelmek nem lehet, hogy egy érvényes országot kóddal tér vissza. A kérdőjel (?) egyezni fog a kérelmeket, amelyhez érvényes országkód nem sikerült meghatározni.
- Országhívó számokat a rendszer kis-és nagybetűket.
- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
  - Complete Cache Fill
  - Alapértelmezett belső Max-Age
  - Belső Max-Age kényszerítése
  - Ignore Origin No-Cache
  - Belső maximális – elavult

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Ország szerinti szűrés megvalósítása a rules engine használatával

Ez az egyezési feltétellel lehetővé teszi számos testreszabásokat kérést származik, amelyről helye alapján. Például az ország szerinti szűrés funkció működését replikálható keresztül a következő konfigurációt:

- URL-cím elérési út helyettesítő egyezés: Állítsa be a [URL-cím elérési út helyettesítő feltételnek megfelelő](#url-path-wildcard) azt a könyvtárat, biztonságát. 
    Fűzze hozzá a csillag, győződjön meg arról, hogy az összes gyermekre a hozzáférést a szabály által korlátozva lesznek relatív elérési út végén.

- Ország/régió egyezés Állítsa be az ország egyeztetési feltételt a kívánt készlethez országok/régiók.
  - Engedélyezése: Az ország az egyezési feltétellel beállítása **Neodpovídá** határozzák meg az URL-cím elérési út helyettesítő egyeztetési feltételt a helyen tárolt tartalmat csak a megadott országok/régiók hozzáférésének engedélyezéséhez.
  - Blokkolás: Az ország az egyezési feltétellel beállítása **egyezések** nem férhet hozzá az URL-cím elérési út helyettesítő egyezési feltételei által meghatározott helyen tárolt tartalmat a megadott országok/régiók blokkolására.

- Hozzáférés (403) szolgáltatás megtagadása: Engedélyezze a [hozzáférés megtagadása (403) funkció](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) az ország szerinti szűrés funkció engedélyezése vagy letiltása részének replikálni.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Ügyfél kezdőpontja

Legfontosabb tudnivalókat:

- Az ügyfél kezdőpontja egyezés feltétel teljesül, függetlenül attól, hogy kért CDN URL-címet vagy egy edge CNAME mutató URL-cím a kijelölt ügyfél kezdőpontja.
- A szabály által hivatkozott felhasználói forrás konfigurálása nem lehet törölni az ügyfél kezdőpontja oldalról. Mielőtt megkísérli törölni egy ügyfél forrás-konfigurációt, győződjön meg arról, hogy a következő konfigurációk nem hivatkoznak azt:
  - Egy ügyfél kezdőpontja egyezési feltételei
  - An edge CNAME configuration
- Ne használja az és a egy IF utasítást úgy, hogy bizonyos egyezési feltételei. Például kombinálása egy CDN-forrás az egyezési feltétellel rendelkező ügyfél kezdőpontja egyeztetési feltételt hozna létre, amely soha nem egyeztethető-egyeztetési minta. Ezért két ügyfél kezdőpontja egyezési feltételei keresztül egy és IF utasítás nelze kombinovat.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Eszköz

Az eszköz az egyezési feltétellel kérelmek egy mobileszközzel a tulajdonságok alapján azonosítja. Mobileszköz-felismerés a gazdafájlon keresztül [WURFL](http://wurfl.sourceforge.net/). 

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt az eszköz a feltételnek megfelelő feltételek teljesülése:

- **Egyezések**: A kérelmező eszközök a megadott értéknek egyeznie kell. 
- **Nem felel meg**: Megköveteli, hogy a kérelmező eszközök a megadott érték nem egyezik.

Legfontosabb tudnivalókat:

- Használja a **esetben figyelmen kívül hagyása** lehetőséget adja meg, hogy a megadott érték kis-és nagybetűket.
- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
  - Complete Cache Fill
  - Alapértelmezett belső Max-Age
  - Belső Max-Age kényszerítése
  - Ignore Origin No-Cache
  - Belső maximális – elavult

#### <a name="string-type"></a>Karakterlánc típus

Egy WURFL képesség általában tetszőleges számok, betűk és szimbólumok kombinációját fogad el. Ez a funkció rugalmas jellege miatt ki kell választania ezt az egyezési feltétellel társított értéket értelmezését. A következő táblázat ismerteti az elérhető beállítások készletét:

Type     | Leírás
---------|------------
Szövegkonstans  | Ezt a beállítást, hogy a legtöbb karakterek használatával kulcsszó különleges jelentéssel a felvételt a [Szövegkonstansérték](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Helyettesítő karakter | Ezzel a lehetőséggel előnyeit minden [helyettesítő karakterek] ([helyettesítő karakteres értékek](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Válassza ezt a lehetőséget a használandó [reguláris kifejezések](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Reguláris kifejezések hasznosak karaktereket a mintázat létrehozása.

#### <a name="wurfl-capabilities"></a>WURFL képességek

Egy WURFL képesség, amely ismerteti a mobil eszközök kategória hivatkozik. A kiválasztott funkció határozza meg a mobil eszköz leírása, amely a kérések azonosítására szolgál.

Az alábbi táblázat felsorolja a WURFL képességek és a rules engine azok változókat.

> [!NOTE]
> A következő változók használhatók a **ügyfél kérelem fejléce módosítsa** és **ügyfél válasz fejléce módosítsa** funkciókat.

Képesség | Változó | Leírás | Mintaértékek
-----------|----------|-------------|----------------
Márkanév | %{wurfl_cap_brand_name} | Egy karakterlánc, amely azt jelzi, hogy az eszköz márkanevét. | Samsung
Eszköz operációs rendszere | %{wurfl_cap_device_os} | Egy karakterlánc, amely azt jelzi, hogy az eszközön telepített operációs rendszer. | IOS
Eszköz operációs rendszerének verziója | %{wurfl_cap_device_os_version} | Egy karakterlánc, amely azt jelzi, hogy az eszközön telepített operációs rendszer verziószáma. | 1.0.1
Kettős tájolása | %{wurfl_cap_dual_orientation} | Egy logikai érték, amely jelzi, hogy az eszköz támogatja-e kettős tájolását. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | Egy karakterlánc, amely azt jelzi, hogy a mobil eszköz előnyben részesített dokumentum típus definíciója (DTD) HTML-tartalmakat. | Egyik sem<br/>xhtml_basic<br/>html5
Kép Inlining | %{wurfl_cap_image_inlining} | Logikai érték beolvasása, amely azt jelzi, hogy az eszköz támogatja-e a Base64 kódolású lemezképek. | false
Is Android | %{wurfl_vcap_is_android} | Egy logikai érték, amely azt jelzi, hogy az eszközt az Android operációs rendszer használja-e. | true
IOS | %{wurfl_vcap_is_ios} | Egy logikai érték, amely azt jelzi, hogy az eszköz iOS használja-e. | false
Smart TV van | %{wurfl_cap_is_smarttv} | Egy logikai érték, amely azt jelzi, hogy az eszköz egy Okostelevízió. | false
Smartphone van | %{wurfl_vcap_is_smartphone} | Egy logikai érték, amely azt jelzi, hogy az eszköz okostelefont. | true
Táblagépes van | %{wurfl_cap_is_tablet} | Egy logikai érték, amely azt jelzi, hogy az eszköz táblagépen. A leírás megadása nem operációs rendszer független. | true
Vezeték nélküli eszköz | %{wurfl_cap_is_wireless_device} | Egy logikai érték, amely azt jelzi, hogy az eszköz számít-e a vezeték nélküli eszköz. | true
Marketing neve | %{wurfl_cap_marketing_name} | Egy karakterlánc, amely azt jelzi, hogy az eszköz marketing nevét. | BlackBerry 8100-as Pearl
Mobileszköz böngészőjében | %{wurfl_cap_mobile_browser} | Egy karakterlánc, amely azt jelzi, hogy a böngésző, amellyel tartalomkérelem az eszközről. | Chrome
Mobil böngésző verziója | %{wurfl_cap_mobile_browser_version} | Egy karakterlánc, amely azt jelzi, hogy a használt böngészőtől függjön tartalom kérhet az eszköz verzióját. | 31
Modell neve | %{wurfl_cap_model_name} | Egy karakterlánc, amely azt jelzi, hogy az eszköz modellneve. | s3
Progresszív letöltés | %{wurfl_cap_progressive_download} | Egy logikai érték, amely jelzi, hogy az eszköz támogatja-e a hang és videó lejátszása, miközben továbbra is letöltése folyamatban. | true
Kiadás dátuma | %{wurfl_cap_release_date} | Egy karakterlánc, amely azt jelzi, hogy az év és hónap, amelyen az eszköz hozzáadva a WURFL adatbázishoz.<br/><br/>Formátum: `yyyy_mm` | 2013_december
Feloldási magassága | %{wurfl_cap_resolution_height} | Egész szám, amely azt jelzi, hogy az eszköz magasságát (képpontban). | 768
Feloldási szélessége | %{wurfl_cap_resolution_width} | Egész szám, amely azt jelzi, hogy az eszköz szélességét (képpontban). | 1024

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>Edge Cname

Legfontosabb tudnivalókat:

- Elérhető edge CNAME rekordok listájának e edge CNAME-rekordokat a platform, amelyen a rules engine konfigurálva van a peremhálózati CNAME rekordok lapon konfigurált korlátozódik.
- Mielőtt megkísérli törölni egy edge CNAME-konfigurációt, győződjön meg arról, hogy az Edge Cname egyeztetési feltételt nem hivatkozik. A szabályban definiált Edge CNAME-konfigurációk nem lehet törölni az Edge CNAME-rekordokat oldalról.
- Ne használja az és a egy IF utasítást úgy, hogy bizonyos egyezési feltételei. Például kombinálása egy Edge Cname az egyezési feltétellel rendelkező ügyfél kezdőpontja egyeztetési feltételt hozna létre, amely soha nem egyeztethető-egyeztetési minta. Ezért két Edge Cname egyezési feltételei keresztül egy és IF utasítás nelze kombinovat.
- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
  - Complete Cache Fill
  - Alapértelmezett belső Max-Age
  - Belső Max-Age kényszerítése
  - Ignore Origin No-Cache
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Hivatkozó tartomány

A gazdagép nevét a hivatkozó, amelyen keresztül tartalmat kért meghatározza, hogy a tartomány hivatkozó feltétel teljesül-e társítva.

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt a hivatkozó tartomány feltételnek megfelelő feltételek teljesülése:

- **Egyezések**: A megadott értékekre hivatkozó állomásnév szükséges. 
- **Nem felel meg**: Megköveteli, hogy a hivatkozó gazdagép neve nem egyezik a megadott érték.

Legfontosabb tudnivalókat:

- Adja meg az egy szóköz pedig külön határoló több állomásnevet.
- Támogatja-e az egyezési feltétellel [helyettesítő karakteres értékek](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- A megadott érték nem tartalmaz egy csillag, ha a hivatkozó névhez pontos egyezésűnek kell lennie. Például adja meg a "tartomany.com" nem felel meg a "www.mydomain.com."
- Használja a **esetben figyelmen kívül hagyása** -e a kis-és nagybetűket összehasonlítás ellenőrzési lehetőséget.
- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
  - Complete Cache Fill
  - Alapértelmezett belső Max-Age
  - Belső Max-Age kényszerítése
  - Ignore Origin No-Cache
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Kérelem fejléce szövegkonstans

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt a kérelem fejlécében szövegkonstans feltételnek megfelelő feltételek teljesülése.

- **Egyezések**: A kérelem tartalmazza a megadott fejléc igényel. Az értékét meg kell egyeznie a az egyezési feltétellel megadott.
- **Nem felel meg**: Megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott fejléc nem tartalmaz.
  - A megadott fejléc tartalmaz, de az érték nem felel meg, amely ezt az egyezési feltétellel van definiálva.
  
Legfontosabb tudnivalókat:

- Fejléc neve összehasonlítások mindig nagybetűk nincsenek megkülönböztetve. Használja a **esetben figyelmen kívül hagyása** szabályozhatja a fejléc értéke összehasonlítások Kisbetű/nagybetű megkülönböztetése lehetőséget.
- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
  - Complete Cache Fill
  - Alapértelmezett belső Max-Age
  - Belső Max-Age kényszerítése
  - Ignore Origin No-Cache
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Kérelem fejléce reguláris kifejezés

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt a kérelem fejlécében Regex feltételnek megfelelő feltételek teljesülése.

- **Egyezések**: A kérelem tartalmazza a megadott fejléc igényel. Az értékét meg kell egyeznie a megadott minta a megadott [reguláris kifejezés](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Nem felel meg**: Megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott fejléc nem tartalmaz.
  - A megadott fejléc tartalmaz, de az érték nem egyezik meg a megadott reguláris kifejezés.

Legfontosabb tudnivalókat:

- Fejléc neve:
  - Fejléc neve összehasonlítások megkülönböztetik a kis-és nagybetűket.
  - Cserélje le a fejléc neve szóközöket "% 20."
- Fejléc értéke:
  - A fejléc értéke kihasználhatják a reguláris kifejezéseket.
  - Használja a **esetben figyelmen kívül hagyása** szabályozhatja a fejléc értéke összehasonlítások Kisbetű/nagybetű megkülönböztetése lehetőséget.
  - A match feltétel nem teljesül, csak akkor, ha egy fejléc értéke pontosan egyezik a megadott minták egyikét.
- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
  - Complete Cache Fill
  - Alapértelmezett belső Max-Age
  - Belső Max-Age kényszerítése
  - Ignore Origin No-Cache
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Kérelem fejléce helyettesítő karakter

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt a kérelem fejlécében helyettesítő feltételnek megfelelő feltételek teljesülése.

- **Egyezések**: A kérelem tartalmazza a megadott fejléc igényel. Az értékét meg kell egyeznie az értékeket, amelyeket ez az egyezési feltétellel vannak definiálva legalább egyike.
- **Nem felel meg**: Megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott fejléc nem tartalmaz.
  - A megadott fejléc tartalmaz, de az érték nem egyezik a megadott értékeket.
  
Legfontosabb tudnivalókat:

- Fejléc neve:
  - Fejléc neve összehasonlítások megkülönböztetik a kis-és nagybetűket.
  - A fejléc neve szóközöket le kell cserélni "% 20." Ez az érték használatával adja meg a tárolóhelyek fejléc értékét.
- Fejléc értéke:
  - A fejléc értéke kihasználhatják a [helyettesítő karakteres értékek](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Használja a **esetben figyelmen kívül hagyása** szabályozhatja a fejléc értéke összehasonlítások Kisbetű/nagybetű megkülönböztetése lehetőséget.
  - A fejléc értéke pontosan egyezik a megadott minták egyikét, ha a egyezés feltétel teljesül.
  - Adja meg az egy szóköz pedig külön határoló több érték.
- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
  - Complete Cache Fill
  - Alapértelmezett belső Max-Age
  - Belső Max-Age kényszerítése
  - Ignore Origin No-Cache
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Kérelmi metódus

A kérelem metódusa egyezés feltétel nem teljesül, csak akkor, ha a eszközök kérik a kijelölt kérelem metódus segítségével. A kérelem elérhető módszerek a következők:

- GET
- HEAD
- POST
- BEÁLLÍTÁSOK
- PUT
- DELETE
- NYOMKÖVETÉSI
- CSATLAKOZÁS

Legfontosabb tudnivalókat:

- Alapértelmezés szerint csak a GET metódust kérelem hozhat létre gyorsítótárazott tartalom a hálózaton. Minden más kérelem módszereket használ proxyt a hálózaton keresztül.
- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
  - Complete Cache Fill
  - Alapértelmezett belső Max-Age
  - Belső Max-Age kényszerítése
  - Ignore Origin No-Cache
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>Kérelem séma

A kérelem séma egyezik feltétel nem teljesül, csak akkor, ha a eszközök kérik a kiválasztott protokollon keresztül. Az elérhető protokollok a következők:

- HTTP
- HTTPS

Legfontosabb tudnivalókat:

- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
  - Complete Cache Fill
  - Alapértelmezett belső Max-Age
  - Belső Max-Age kényszerítése
  - Ignore Origin No-Cache
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>URL-cím elérési út könyvtár

Azonosítja a kérelem relatív elérési úttal, amely nem tartalmazza a kért objektum a fájl nevét.

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt az URL-cím elérési út könyvtár feltételnek megfelelő feltételek teljesülése.

- **Egyezések**: A kérés tartalmaz egy relatív URL-cím, kivéve a a fájlnevet, amely megfelel a megadott URL-minta igényel.
- **Nem felel meg**: A kérés tartalmaz egy relatív URL-cím, kivéve a fájl neve, amely nem egyezik a megadott URL-minta igényel.

Legfontosabb tudnivalókat:

- Használja a **a skálához képest** beállítással azt adhatja meg, hogy az URL-cím összehasonlítás kezdődik-e előtt vagy után a tartalom-hozzáférési pont. A tartalom-hozzáférési pont az elérési út és a Verizon CDN gazdagépnév, illetve relatív elérési útját a kért objektumhoz (például /800001/CustomerOrigin) között megjelenő része. Kiszolgáló típusa (például a CDN-t vagy az ügyfél forrás) egy hely és az ügyfél fiók száma azonosítja.

   A következő értékek érhetők el a **a skálához képest** lehetőséget:
  - **Legfelső szintű**: Azt jelzi, hogy az URL-cím összehasonlító elkezdi közvetlenül a CDN-gazdaneve után. 

  Például: http:\//wpc.0001.&lt; tartomány&gt;/**800001/myorigin/SajátMappa**/index.htm

  - **Forrás**: Azt jelzi, hogy az URL-cím összehasonlító elkezdi a tartalom-hozzáférési pont (például /000001 vagy/800001/myorigin) után. Mivel a \*. azureedge.net CNAME képest a forrás könyvtárát, a Verizon CDN gazdagépnevét alapértelmezés szerint létrejön, a felhasználók az Azure CDN-t kell használnia a **forrás** értéket. 

  Például: https:\//&lt;végpont&gt;.azureedge.net/**SajátMappa**/index.htm 

  Az URL-cím mutat a következő Verizon CDN gazdaneve: http:\//wpc.0001.&lt; tartomány&gt;/800001/myorigin/**SajátMappa**/index.htm

- Az edge CNAME URL-cím újraírja van a CDN URL-CÍMÉT az URL-cím összehasonlítás előtt.

    Például a következő két URL egyaránt az adott objektum mutasson, és ezért tudja az ugyanazon URL-címet.
  - A CDN URL-címe: http:\//wpc.0001.&lt; tartomány&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    További információ:
  - Egyéni tartomány: https:\//my.domain.com/path/asset.htm
    
    - (Root) képest relatív URL-cím: / 800001/CustomerOrigin/path /
    
    - (Forrás) képest relatív URL-cím: /path/

- Az URL-cím összehasonlító vége előtt a fájlnév a kért objektum használt URL-cím része. Záró perjellel az ilyen típusú elérési út az utolsó karakter.

- Cserélje le az URL-cím elérési út mintája a szóközt "% 20."

- Minden egyes URL-cím elérési út mintája egy vagy több csillagot (*), ahol minden egyes csillag olyan sorozataira illeszkedik, egy vagy több karaktert tartalmazhat.

- Adja meg a több URL-cím elérési út a minta szerint határoló egyenként a szóköz.

    Például: * /sales/ * /marketing/

- URL-cím elérési utat kihasználhatják a [helyettesítő karakteres értékek](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Használja a **esetben figyelmen kívül hagyása** ellenőrzési lehetőséget egy kis-és nagybetűket összehasonlítás történik-e.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>URL-cím elérési út bővítmény

A fájl kiterjesztése a kért objektum azonosítja a kérelmeket.

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt az URL-cím elérési út bővítmény feltételnek megfelelő feltételek teljesülése.

- **Egyezések**: A kérelem tartalmazza, amely pontosan megegyezik a megadott minta fájlkiterjesztést az URL-cím szükséges.

   Például ha megadja a "htm", "htm" eszközök megfeleltetődnek, azonban nem "html" eszközök.  

- **Nem felel meg**: Az URL kérelmet tartalmaz, amely nem egyezik a megadott minta kiterjesztése igényel.

Legfontosabb tudnivalókat:

- Adja meg a kiterjesztések, a megfelelő a **érték** mezőbe. Nem tartalmaznak kezdődhetnek ponttal; Ha például használja htm .htm helyett.

- Használja a **esetben figyelmen kívül hagyása** ellenőrzési lehetőséget egy kis-és nagybetűket összehasonlítás történik-e.

- Adjon meg több fájlkiterjesztések határoló összes-bővítményt egy szóközzel. 

    Például: htm html

- Például adja meg a "htm" egyezik "htm" eszközök, de nem "html" eszközök.

#### <a name="sample-scenario"></a>Mintaforgatókönyv

A következő példa konfigurációja azt feltételezi, hogy a match feltétel teljesül-e, amikor egy kérelem megfelel a megadott bővítmények egyikének.

Specifikáció érték: az asp aspx php html

Ha úgy találja, a következő kiterjesztésű befejező URL-címek a match feltétel teljesül:

- .asp
- .aspx
- .php
- .html

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>URL-cím elérési_út fájlnév

Azonosítja a kérelmeket a kért objektum a fájl nevét. Ezt az egyezési feltétellel céljából a fájl nevét a kért objektumhoz, időszak és a fájlkiterjesztés (például index.html) nevéből áll.

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt az URL-cím elérési út fájlnév feltételnek megfelelő feltételek teljesülése.

- **Egyezések**: A kérelem a fájl nevét a hozzá tartozó URL-cím, amely megfelel a megadott minta tartalmaznia kell.
- **Nem felel meg**: A kérelem a fájl nevét a hozzá tartozó URL-cím, amely nem egyezik a megadott minta tartalmaznia kell.

Legfontosabb tudnivalókat:

- Használja a **esetben figyelmen kívül hagyása** ellenőrzési lehetőséget egy kis-és nagybetűket összehasonlítás történik-e.

- Több fájlkiterjesztések megadásához külön mindegyik bővítmény egyetlen szóközzel.

    Például: index.htm index.html

- Tárolóhelyek fájl nevének értékét cserélje le a(z) "% 20."

- Egy fájl név-érték kihasználhatják a [helyettesítő karakteres értékek](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Például egyes Fájlnévminta tartalmazhat egy vagy több csillagot (*), ahol minden egyes csillag olyan sorozataira illeszkedik, egy vagy több karakter.

- Ha helyettesítő karaktereket nem meg van adva, csak pontos egyezés kielégíteni ezt az egyezési feltétellel.

    Például adja meg a "bemutató.ppt" megfelel egy "bemutató.ppt", de nem egy elnevezett "presentation.pptx." nevű objektum

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>URL-cím elérési út szövegkonstans

A kérelem URL-cím, beleértve a fájlnevet, és a megadott érték hasonlítja össze.

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt az URL-cím elérési út szövegkonstans feltételnek megfelelő feltételek teljesülése.

- **Egyezések**: A kérés tartalmaz, amely megfelel a megadott minta egy URL-cím szükséges.
- **Nem felel meg**: A kérés tartalmaz, amely nem egyezik a megadott minta egy URL-cím szükséges.

Legfontosabb tudnivalókat:

- Használja a **a skálához képest** beállítással azt adhatja meg, hogy az URL-cím összehasonlító elkezdi előtt vagy után a tartalom-hozzáférési pont. 

    A következő értékek érhetők el a **a skálához képest** lehetőséget:
  - **Legfelső szintű**: Azt jelzi, hogy az URL-cím összehasonlító elkezdi közvetlenül a CDN-gazdaneve után.

    Például: http:\//wpc.0001.&lt; tartomány&gt;/**800001/myorigin/myfolder/index.htm**

  - **Forrás**: Azt jelzi, hogy az URL-cím összehasonlító elkezdi a tartalom-hozzáférési pont (például /000001 vagy/800001/myorigin) után. Mivel a \*. azureedge.net CNAME képest a forrás könyvtárát, a Verizon CDN gazdagépnevét alapértelmezés szerint létrejön, a felhasználók az Azure CDN-t kell használnia a **forrás** értéket. 

    Például: https:\//&lt;végpont&gt;.azureedge.net/**myfolder/index.htm**

  Az URL-cím mutat a következő Verizon CDN gazdaneve: http:\//wpc.0001.&lt; tartomány&gt;/800001/myorigin/**myfolder/index.htm**

- Az edge CNAME URL-cím URL-cím összehasonlítása előtt a CDN URL-címre van átírása.

Például a következő két URL egyaránt mutasson az adott objektum, és ezért tudja az ugyanazon URL-címet:

- A CDN URL-címe: http:\//wpc.0001.&lt; tartomány&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    További információ:
    
    - (Root) képest relatív URL-cím: /800001/CustomerOrigin/path/asset.htm
   
    - (Forrás) képest relatív URL-cím: /path/asset.htm

- Az URL-cím lekérdezési karakterláncok figyelmen kívül hagyja.
- Használja a **esetben figyelmen kívül hagyása** ellenőrzési lehetőséget egy kis-és nagybetűket összehasonlítás történik-e.
- Ez az egyezési feltétellel a rendszer összehasonlítja a relatív elérési útját a pontos, az ügyfél által kezdeményezett kérelem számára megadott érték.

- Egy adott címtárhoz intézett összes kérelem megfelelően, használja a [URL-cím elérési út Directory](#url-path-directory) vagy a [URL-cím elérési út helyettesítő](#url-path-wildcard) feltételnek megfelelő.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>URL-cím elérési út reguláris kifejezés

A kérelem URL-cím a megadott összehasonlítja [reguláris kifejezés](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt az URL-cím elérési út Regex feltételnek megfelelő feltételek teljesülése.

- **Egyezések**: A kérés tartalmaz egy URL-cím, amely megfelel a megadott reguláris kifejezést igényel.
- **Nem felel meg**: A kérés tartalmaz egy URL-cím, amely nem egyezik a megadott reguláris kifejezést igényel.

Legfontosabb tudnivalókat:

- Az edge CNAME URL-cím URL-cím összehasonlítása előtt a CDN URL-címre van átírása.

    Például mindkét URL-címeket az adott objektum mutasson, és így kell az azonos URL-címet.

     - A CDN URL-címe: http:\//wpc.0001.&lt; tartomány&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    További információ:
    
     - URL-cím: /800001/CustomerOrigin/path/asset.htm

- Az URL-cím lekérdezési karakterláncok figyelmen kívül hagyja.
    
- Használja a **esetben figyelmen kívül hagyása** ellenőrzési lehetőséget egy kis-és nagybetűket összehasonlítás történik-e.
    
- Az URL-címet a tárolóhelyek le kell cserélni "% 20."

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>URL-cím elérési út helyettesítő karakter

A kérelem relatív URL-cím elérési útját a megadott helyettesítő karakterrel hasonlítja össze.

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt az URL-cím elérési út helyettesítő feltételnek megfelelő feltételek teljesülése.

- **Egyezések**: A kérés tartalmaz egy URL-cím, amely megfelel a megadott helyettesítő karakterrel kell.
- **Nem felel meg**: A kérés tartalmaz egy URL-cím, amely nem egyezik a megadott helyettesítő karakterrel kell.

Legfontosabb tudnivalókat:

- **A skálához képest** lehetőséget: Ez a beállítás határozza meg, hogy az URL-cím összehasonlító elkezdi előtt vagy után a tartalom-hozzáférési pont.

   Ez a beállítás a következő értékeket veheti fel:
     - **Legfelső szintű**: Azt jelzi, hogy az URL-cím összehasonlító elkezdi közvetlenül a CDN-gazdaneve után.

       Például: http:\//wpc.0001.&lt; tartomány&gt;/**800001/myorigin/myfolder/index.htm**

     - **Forrás**: Azt jelzi, hogy az URL-cím összehasonlító elkezdi a tartalom-hozzáférési pont (például /000001 vagy/800001/myorigin) után. Mivel a \*. azureedge.net CNAME képest a forrás könyvtárát, a Verizon CDN gazdagépnevét alapértelmezés szerint létrejön, a felhasználók az Azure CDN-t kell használnia a **forrás** értéket. 

       Például: https:\//&lt;végpont&gt;.azureedge.net/**myfolder/index.htm**

     Az URL-cím mutat a következő Verizon CDN gazdaneve: http:\//wpc.0001.&lt; tartomány&gt;/800001/myorigin/**myfolder/index.htm**

- Az edge CNAME URL-cím URL-cím összehasonlítása előtt a CDN URL-címre van átírása.

    Például a következő két URL egyaránt mutasson az adott objektum, és ezért tudja az ugyanazon URL-címet:
     - A CDN URL-címe: http://wpc.0001.&lt ; tartomány&gt; /800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    További információ:
    
     - (Root) képest relatív URL-cím: /800001/CustomerOrigin/path/asset.htm
    
     - (Forrás) képest relatív URL-cím: /path/asset.htm
    
- Adja meg az egy szóköz pedig külön határoló több URL-cím elérési út.

   Például: /marketing/asset.* /sales/*.htm

- Az URL-cím lekérdezési karakterláncok figyelmen kívül hagyja.
    
- Használja a **esetben figyelmen kívül hagyása** ellenőrzési lehetőséget egy kis-és nagybetűket összehasonlítás történik-e.
    
- Cserélje le az URL-címet a tárolóhelyek "% 20."
    
- Egy URL-cím kihasználhatják a megadott érték [helyettesítő karakteres értékek](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Minden egyes URL-cím elérési út mintája tartalmazhat egy vagy több csillagot (*), ahol minden egyes csillag meg tudja egy vagy több karakter sorozata.

#### <a name="sample-scenarios"></a>Mintaforgatókönyvek

A minta konfigurációkat az alábbi táblázat azt feltételezik, hogy a match feltétel teljesül-e, amikor egy kérelem megfelel a megadott URL-minta:

Value                   | A viszonyítva    | Eredmény 
------------------------|----------------|-------
*/test.html */test.php  | Legfelső szintű vagy a forrás | Ez a minta egyezik "test.html" vagy "test.php" bármelyik mappájában nevű eszközök kérelmek szerint.
/80ABCD/origin/text/*   | legfelső szintű           | Ez a minta egyezik, ha a kért objektum megfelel a következő feltételeknek: <br />-Kell lennie, egy ügyfél forrás neve "origin". <br />– A relatív elérési út "szöveg." nevű mappát kell kezdődnie. A kért objektumhoz, vagy lehetnek a "text" mappában vagy egy rekurzív almappája.
*/CSS/* */js/*          | Legfelső szintű vagy a forrás | Ez a minta egyezik az összes CDN vagy edge egy css vagy js mappát tartalmazó CNAME URL-címeket.
*.jpg *.gif *.png       | Legfelső szintű vagy a forrás | Ez a minta egyezik a CDN- vagy peremtábla CNAME URL-címekhez .jpg, .gif, vagy .png végződésű. Adja meg ezt a mintát egy másik módja a [URL-cím elérési út bővítmény feltételnek megfelelő](#url-path-extension).
/ képek / * / media / *      | Forrás         | Ez a minta egyezik a CDN- vagy peremtábla CNAME URL-címek, amelyek relatív elérési út egy "rendszerképek" vagy "média" mappa kezdődik. <br />– A CDN URL-cím: http:\//wpc.0001.&lt; tartomány&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>URL-cím lekérdezési szövegkonstans

A kérelem lekérdezési karakterláncát a megadott érték hasonlítja össze.

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt az URL-cím lekérdezési szövegkonstans feltételnek megfelelő feltételek teljesülése.

- **Egyezések**: A kérés tartalmaz egy URL-cím lekérdezési karakterláncot, amely megfelel a megadott lekérdezési karakterlánc szükséges.
- **Nem felel meg**: A kérés tartalmaz egy URL-cím lekérdezési karakterláncot, amely nem egyezik a megadott lekérdezési karakterlánc szükséges.

Legfontosabb tudnivalókat:

- Csak a pontos lekérdezés karakterlánc megfelel megfelelnek-e az egyezési feltétellel.
    
- Használja a **esetben figyelmen kívül hagyása** szabályozhatja a lekérdezési karakterlánc-összehasonlítások Kisbetű/nagybetű megkülönböztetése lehetőséget.
    
- Ne vegyen fel egy vezető kérdőjelet (?) a lekérdezési karakterlánc értéke szöveget.
    
- Bizonyos karakterek megkövetelése URL-Címének kódolása. A százalékos szimbólum URL-címét használja a következő karakterek kódolása:

   Karakter | URL-Címének kódolása
   ----------|---------
   Űr     | %20
   &         | %25

- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
   - Complete Cache Fill
   - Alapértelmezett belső Max-Age
   - Belső Max-Age kényszerítése
   - Ignore Origin No-Cache
   - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>URL-cím lekérdezési paraméter

A megadott lekérdezési karakterláncot tartalmazó kérelmek azonosítja. Ez a paraméter, amely egy megadott mintának megfelelő értékre van állítva. Lekérdezési karakterlánc paramétert (például paraméter = érték) a kérés URL-címe határozza meg, hogy ez a feltétel teljesül. Ez az egyezési feltétellel annak neve azonosítja a lekérdezési karakterlánc paramétereként, és a paraméter értékeként egy vagy több értéket fogad el. 

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt az URL-cím lekérdezési paramétert a feltételnek megfelelő feltételek teljesülése.

- **Egyezések**: Kell tartalmaznia a megadott paraméter értéke megegyezik az ezen az egyezési feltétellel meghatározott értékek közül legalább egy kérelmet.
- **Nem felel meg**: Megköveteli, hogy a kérelem megfelel a következő feltételek valamelyike:
  - A megadott paraméter nem tartalmaz.
  - A megadott paraméter tartalmazza, de az érték nem egyezik meg az értékeket, hogy ez az egyezési feltétellel vannak meghatározva.

Ez az egyezési feltétellel nyújt egy egyszerű módja annak, hogy adja meg a név-érték párok paraméterkombinációk. A nagyobb rugalmasság biztosítására, ha a lekérdezési sztring paramétereként vannak egyeztetéséhez, fontolja meg a [URL-cím lekérdezési helyettesítő](#url-query-wildcard) feltételnek megfelelő.

Legfontosabb tudnivalókat:

- Ez az egyezési feltétellel-példányonként csak egy egyetlen URL-cím lekérdezési paraméter neve adható meg.
    
- Helyettesítő karakteres értékek használata nem támogatott, ha a paraméter neve meg van adva, mert csak a pontos paraméter neve egyezik jogosultak az összehasonlítást.
- Paraméter érték(ek) tartalmazhatnak [helyettesítő karakteres értékek](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Minden paraméter értéke minta tartalmazhat egy vagy több csillagot (*), ahol minden egyes csillag meg tudja egy vagy több karakter sorozata.
   - Bizonyos karakterek megkövetelése URL-Címének kódolása. A százalékos szimbólum URL-címét használja a következő karakterek kódolása:

       Karakter | URL-Címének kódolása
       ----------|---------
       Űr     | %20
       &         | %25

- Adja meg a lekérdezési karakterlánc paraméter több értéket határoló egyenként a szóköz. Amikor egy kérés a megadott név-érték párok kombinációk egyikét tartalmazza a match feltétel teljesül.

   - 1\. példa:

     - Konfigurálás:

       Érték1, érték2

     - Ez a konfiguráció megfelel a következő lekérdezési karakterlánc paraméterei:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - 2\. példa

     - Konfigurálás: 

        Value%20A Value%20B

     - Ez a konfiguráció megfelel a következő lekérdezési karakterlánc paraméterei:

       Parameter1=Value%20A

       Parameter1=Value%20B

- Csak akkor, ha van legalább egy név-érték párok kombinációja megadott lekérdezési karakterlánc pontos egyezést e egyezés feltétel teljesül.

   Például, ha az előző példában a konfigurációt használ, a paraméter név-érték párok kombináció "Parameter1 = ValueAdd" nem tekintik egyezést. Azonban a következő értékek egyikét adja meg, ha azt, hogy a név-érték párok kombináció egyezni fog:

   - Érték1, érték2 ValueAdd
   - ValueA* ValueB

- Használja a **esetben figyelmen kívül hagyása** szabályozhatja a lekérdezési karakterlánc-összehasonlítások Kisbetű/nagybetű megkülönböztetése lehetőséget.
    
- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
   - Complete Cache Fill
   - Alapértelmezett belső Max-Age
   - Belső Max-Age kényszerítése
   - Ignore Origin No-Cache
   - Belső maximális – elavult

#### <a name="sample-scenarios"></a>Használati példák

A következő példa bemutatja, hogy ez a beállítás működéséről adott helyzetekben:

Name (Név)  | Érték |  Eredmény
------|-------|--------
Felhasználó  | János   | Ez a minta egyezik a kért URL-cím lekérdezési karakterlánc esetén "? felhasználói János =."
Felhasználó  | *     | Ez a minta egyezik, ha a kért URL-cím lekérdezési karakterláncot tartalmaz egy felhasználó paramétert.
Email | János\* | Ez a minta egyezik, ha a kért URL-cím lekérdezési karakterláncot tartalmaz egy e-mailek paraméter, amely elindítja a "János".

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>URL-cím lekérdezési reguláris kifejezés

A megadott lekérdezési karakterláncot tartalmazó kérelmek azonosítja. Ez a paraméter értéke, amely megfelel a megadott érték [reguláris kifejezés](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt az URL-cím lekérdezési Regex feltételnek megfelelő feltételek teljesülése.

- **Egyezések**: Szükséges a kérelem URL-cím lekérdezési karakterláncot, amely megfelel a megadott reguláris kifejezést tartalmaz.
- **Nem felel meg**: Szükséges a kérelem URL-cím lekérdezési karakterláncot, amely nem egyezik a megadott reguláris kifejezést tartalmaz.

Legfontosabb tudnivalókat:

- Csak pontos egyezések a megadott reguláris kifejezést megfelelnek-e az egyezési feltétellel.
    
- Használja a **esetben figyelmen kívül hagyása** szabályozhatja a lekérdezési karakterlánc-összehasonlítások Kisbetű/nagybetű megkülönböztetése lehetőséget.
    
- Ez a beállítás az alkalmazásában egy lekérdezési karakterlánc kezdődik az első karakter után a kérdőjel (?) elválasztó, a lekérdezési karakterlánc.
    
- Bizonyos karakterek megkövetelése URL-Címének kódolása. A százalékos szimbólum URL-címét használja a következő karakterek kódolása:

   Karakter | URL-Címének kódolása | Value
   ----------|--------------|------
   Űr     | %20          | \%20
   &         | %25          | \%25

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`20
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   Note that percentage symbols must be escaped.

- Double-escape special regular expression characters (for example, \^$.+) to include a backslash in the regular expression.

   For example:

   Value | Interpreted As 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Wildcard

Compares the specified value(s) against the request's query string.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified wildcard value.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified wildcard value.

Key information:

- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
- Parameter values can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

     Character | URL Encoding
     ----------|---------
     Space     | %20
     &         | %25

- Specify multiple values by delimiting each one with a single space.

   For example: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Only exact matches to at least one of the specified query string patterns satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

 Name                 | Description
 ---------------------|------------
user=joe              | This pattern is matched when the query string for a requested URL is "?user=joe."
\*user=\* \*optout=\* | This pattern is matched when the CDN URL query contains either the user or optout parameter.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

## Next steps

- [Azure Content Delivery Network overview](cdn-overview.md)
- [Rules engine reference](cdn-verizon-premium-rules-engine-reference.md)
- [Rules engine conditional expressions](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Rules engine features](cdn-verizon-premium-rules-engine-reference-features.md)
- [Overriding default HTTP behavior using the rules engine](cdn-verizon-premium-rules-engine.md)\`25
title: Azure CDN from Verizon Premium rules engine match conditions | Microsoft Docs
description: Reference documentation for Azure Content Delivery Network from Verizon Premium rules engine match conditions.
services: cdn
author: mdgattuso

ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus

---

# Azure CDN from Verizon Premium rules engine match conditions

This article lists detailed descriptions of the available match conditions for the Azure Content Delivery Network (CDN) from Verizon Premium [rules engine](cdn-verizon-premium-rules-engine.md).

The second part of a rule is the match condition. A match condition identifies specific types of requests for which a set of features will be performed.

For example, you can use a match condition to:

- Filter requests for content at a particular location.
- Filter requests generated from a particular IP address or country/region.
- Filter requests by header information.

## Always match condition

The Always match condition applies a default set of features to all requests.

Name | Purpose
-----|--------
[Always](#always) | Applies a default set of features to all requests.

## Device match condition

The Device match condition identifies requests made from a mobile device based on its properties.  

Name | Purpose
-----|--------
[Device](#device) | Identifies requests made from a mobile device based on its properties.

## Location match conditions

The Location match conditions identify requests based on the requester's location.

Name | Purpose
-----|--------
[AS Number](#as-number) | Identifies requests that originate from a particular network.
[Country](#country) | Identifies requests that originate from the specified countries/regions.

## Origin match conditions

The Origin match conditions identify requests that point to Content Delivery Network storage or a customer origin server.

Name | Purpose
-----|--------
[CDN Origin](#cdn-origin) | Identifies requests for content stored in Content Delivery Network storage.
[Customer Origin](#customer-origin) | Identifies requests for content stored on a specific customer origin server.

## Request match conditions

The Request match conditions identify requests based on their properties.

Name | Purpose
-----|--------
[Client IP Address](#client-ip-address) | Identifies requests that originate from a particular IP address.
[Cookie Parameter](#cookie-parameter) | Checks the cookies associated with each request for the specified value.
[Cookie Parameter Regex](#cookie-parameter-regex) | Checks the cookies associated with each request for the specified regular expression.
[Edge Cname](#edge-cname) | Identifies requests that point to a specific edge CNAME.
[Referring Domain](#referring-domain) | Identifies requests that were referred from the specified host names.
[Request Header Literal](#request-header-literal) | Identifies requests that contain the specified header set to a specified value.
[Request Header Regex](#request-header-regex) | Identifies requests that contain the specified header set to a value that matches the specified regular expression.
[Request Header Wildcard](#request-header-wildcard) | Identifies requests that contain the specified header set to a value that matches the specified pattern.
[Request Method](#request-method) | Identifies requests by their HTTP method.
[Request Scheme](#request-scheme) | Identifies requests by their HTTP protocol.

## URL match conditions

The URL match conditions identify requests based on their URLs.

Name | Purpose
-----|--------
[URL Path Directory](#url-path-directory) | Identifies requests by their relative path.
[URL Path Extension](#url-path-extension) | Identifies requests by their file name extension.
[URL Path Filename](#url-path-filename) | Identifies requests by their file name.
[URL Path Literal](#url-path-literal) | Compares a request's relative path to the specified value.
[URL Path Regex](#url-path-regex) | Compares a request's relative path to the specified regular expression.
[URL Path Wildcard](#url-path-wildcard) | Compares a request's relative path to the specified pattern.
[URL Query Literal](#url-query-literal) | Compares a request's query string to the specified value.
[URL Query Parameter](#url-query-parameter) | Identifies requests that contain the specified query string parameter set to a value that matches a specified pattern.
[URL Query Regex](#url-query-regex) | Identifies requests that contain the specified query string parameter set to a value that matches a specified regular expression.
[URL Query Wildcard](#url-query-wildcard) | Compares the specified value against the request's query string.

## Reference for rules engine match conditions

---

### Always

The Always match condition applies a default set of features to all requests.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### AS Number

The AS Number network is defined by its autonomous system number (ASN). 

The **Matches**/**Does Not Match** option determines the conditions under which the AS Number match condition is met:

- **Matches**: Requires that the ASN of the client network matches one of the specified ASNs. 
- **Does Not Match**: Requires that the ASN of the client network does not match any of the specified ASNs.

Key information:

- Specify multiple ASNs by delimiting each one with a single space. For example, 64514 64515 matches requests that arrive from either 64514 or 64515.
- Certain requests might not return a valid ASN. A question mark (?) will match requests for which a valid ASN could not be determined.
- Specify the entire ASN for the desired network. Partial values will not be matched.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### CDN Origin

The CDN Origin match condition is met when both of the following conditions are met:

- Content from CDN storage was requested.
- The request URI uses the type of content access point (for example, /000001) that's defined in this match condition:
  - CDN URL: The request URI must contain the selected content access point.
  - Edge CNAME URL: The corresponding edge CNAME configuration must point to the selected content access point.
  
Key information:

- The content access point identifies the service that should serve the requested content.
- Don't use an AND IF statement to combine certain match conditions. For example, combining a CDN Origin match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two CDN Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Client IP Address

The **Matches**/**Does Not Match** option determines the conditions under which the Client IP Address match condition is met:

- **Matches**: Requires that the client's IP address matches one of the specified IP addresses. 
- **Does Not Match**: Requires that the client's IP address does not match any of the specified IP addresses. 

Key information:

- Use CIDR notation.
- Specify multiple IP addresses and/or IP address blocks by delimiting each one with a single space. For example:
  - **IPv4 example**: 1.2.3.4 10.20.30.40 matches any requests that arrive from either address 1.2.3.4 or 10.20.30.40.
  - **IPv6 example**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 matches any requests that arrive from either address 1:2:3:4:5:6:7:8 or 10:20:30:40:50:60:70:80.
- The syntax for an IP address block is the base IP address followed by a forward slash and the prefix size. For example:
  - **IPv4 example**: 5.5.5.64/26 matches any requests that arrive from addresses 5.5.5.64 through 5.5.5.127.
  - **IPv6 example**: 1:2:3:/48 matches any requests that arrive from addresses 1:2:3:0:0:0:0:0 through 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Cookie Parameter

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match any of the values that are defined in this match condition.
  
Key information:

- Cookie name:
  - Because wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - Specify multiple cookie values by delimiting each one with a single space.
  - A cookie value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - If a wildcard value has not been specified, then only an exact match will satisfy this match condition. For example, specifying "Value" will match "Value," but not "Value1" or "Value2."
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)
</br>

---

### Cookie Parameter Regex

The Cookie Parameter Regex match condition defines a cookie name and value. You can use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) to define the desired cookie value.

The **Matches**/**Does Not Match** option determines the conditions under which the Cookie Parameter Regex match condition is met.

- **Matches**: Requires a request to contain the specified cookie with a value that matches the specified regular expression.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified cookie.
  - It contains the specified cookie, but its value does not match the specified regular expression.
  
Key information:

- Cookie name:
  - Because regular expressions and wildcard values, including asterisks (*), are not supported when you're specifying a cookie name, only exact cookie name matches are eligible for comparison.
  - Only a single cookie name can be specified per instance of this match condition.
  - Cookie name comparisons are case-insensitive.
- Cookie value:
  - A cookie value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control whether a case-sensitive comparison is made against the request's cookie value.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Country

You can specify a country through its country code. 

The **Matches**/**Does Not Match** option determines the conditions under which the Country match condition is met:

- **Matches**: Requires the request to contain the specified country code values. 
- **Does Not Match**: Requires that the request does not contain the specified country code values.

Key information:

- Specify multiple country codes by delimiting each one with a single space.
- Wildcards are not supported when you're specifying a country code.
- The "EU" and "AP" country codes do not encompass all IP addresses in those regions.
- Certain requests might not return a valid country code. A question mark (?) will match requests for which a valid country code could not be determined.
- Country codes are case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### Implementing Country Filtering by using the rules engine

This match condition allows you to perform a multitude of customizations based on the location from which a request originated. For example, the behavior of the Country Filtering feature can be replicated through the following configuration:

- URL Path Wildcard match: Set the [URL Path Wildcard match condition](#url-path-wildcard) to the directory that will be secured. 
    Append an asterisk to the end of the relative path to ensure that access to all of its children will be restricted by this rule.

- Country match: Set the Country match condition to the desired set of countries.
  - Allow: Set the Country match condition to **Does Not Match** to allow only the specified countries access to content stored in the location defined by the URL Path Wildcard match condition.
  - Block: Set the Country match condition to **Matches** to block the specified countries from accessing content stored in the location defined by the URL Path Wildcard match condition.

- Deny Access (403) Feature: Enable the [Deny Access (403) feature](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) to replicate the allow or block portion of the Country Filtering feature.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Customer Origin

Key information:

- The Customer Origin match condition is met regardless of whether content is requested through a CDN URL or an edge CNAME URL that points to the selected customer origin.
- A customer origin configuration that's referenced by a rule cannot be deleted from the Customer Origin page. Before you attempt to delete a customer origin configuration, make sure that the following configurations do not reference it:
  - A Customer Origin match condition
  - An edge CNAME configuration
- Don't use an AND IF statement to combine certain match conditions. For example, combining a Customer Origin match condition with a CDN Origin match condition would create a match pattern that could never be matched. For this reason, two Customer Origin match conditions cannot be combined through an AND IF statement.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Device

The Device match condition identifies requests made from a mobile device based on its properties. Mobile device detection is achieved through [WURFL](http://wurfl.sourceforge.net/). 

The **Matches**/**Does Not Match** option determines the conditions under which the Device match condition is met:

- **Matches**: Requires the requester's device to match the specified value. 
- **Does Not Match**: Requires that the requester's device does not match the specified value.

Key information:

- Use the **Ignore Case** option to specify whether the specified value is case-sensitive.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

#### String Type

A WURFL capability typically accepts any combination of numbers, letters, and symbols. Due to the flexible nature of this capability, you must choose how the value associated with this match condition is interpreted. The following table describes the available set of options:

Type     | Description
---------|------------
Literal  | Select this option to prevent most characters from taking on special meaning by using their [literal value](cdn-verizon-premium-rules-engine-reference.md#literal-values).
Wildcard | Select this option to take advantage of all [wildcard characters]([wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
Regex    | Select this option to use [regular expressions](cdn-verizon-premium-rules-engine-reference.md#regular-expressions). Regular expressions are useful for defining a pattern of characters.

#### WURFL capabilities

A WURFL capability refers to a category that describes mobile devices. The selected capability determines the type of mobile device description that is used to identify requests.

The following table lists WURFL capabilities and their variables for the rules engine.

> [!NOTE]
> The following variables are supported in the **Modify Client Request Header** and **Modify Client Response Header** features.

Capability | Variable | Description | Sample values
-----------|----------|-------------|----------------
Brand Name | %{wurfl_cap_brand_name} | A string that indicates the brand name of the device. | Samsung
Device OS | %{wurfl_cap_device_os} | A string that indicates the operating system installed on the device. | IOS
Device OS Version | %{wurfl_cap_device_os_version} | A string that indicates the version number of the operating system installed on the device. | 1.0.1
Dual Orientation | %{wurfl_cap_dual_orientation} | A Boolean that indicates whether the device supports dual orientation. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | A string that indicates the mobile device's preferred document type definition (DTD) for HTML content. | none<br/>xhtml_basic<br/>html5
Image Inlining | %{wurfl_cap_image_inlining} | A Boolean that indicates whether the device supports Base64 encoded images. | false
Is Android | %{wurfl_vcap_is_android} | A Boolean that indicates whether the device uses the Android OS. | true
Is IOS | %{wurfl_vcap_is_ios} | A Boolean that indicates whether the device uses iOS. | false
Is Smart TV | %{wurfl_cap_is_smarttv} | A Boolean that indicates whether the device is a smart TV. | false
Is Smartphone | %{wurfl_vcap_is_smartphone} | A Boolean that indicates whether the device is a smartphone. | true
Is Tablet | %{wurfl_cap_is_tablet} | A Boolean that indicates whether the device is a tablet. This description is  OS-independent. | true
Is Wireless Device | %{wurfl_cap_is_wireless_device} | A Boolean that indicates whether the device is considered a wireless device. | true
Marketing Name | %{wurfl_cap_marketing_name} | A string that indicates the device's marketing name. | BlackBerry 8100 Pearl
Mobile Browser | %{wurfl_cap_mobile_browser} | A string that indicates the browser that's used to request content from the device. | Chrome
Mobile Browser Version | %{wurfl_cap_mobile_browser_version} | A string that indicates the version of the browser that's used to request content from the device. | 31
Model Name | %{wurfl_cap_model_name} | A string that indicates the device's model name. | s3
Progressive Download | %{wurfl_cap_progressive_download} | A Boolean that indicates whether the device supports the playback of audio and video while it is still being downloaded. | true
Release Date | %{wurfl_cap_release_date} | A string that indicates the year and month on which the device was added to the WURFL database.<br/><br/>Format: `yyyy_mm` | 2013_december
Resolution Height | %{wurfl_cap_resolution_height} | An integer that indicates the device's height in pixels. | 768
Resolution Width | %{wurfl_cap_resolution_width} | An integer that indicates the device's width in pixels. | 1024

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Edge Cname

Key information:

- The list of available edge CNAMEs is limited to those edge CNAMEs that have been configured on the Edge CNAMEs page for the platform on which the rules engine is being configured.
- Before you attempt to delete an edge CNAME configuration, make sure that an Edge Cname match condition does not reference it. Edge CNAME configurations that have been defined in a rule cannot be deleted from the Edge CNAMEs page.
- Don't use an AND IF statement to combine certain match conditions. For example, combining an Edge Cname match condition with a Customer Origin match condition would create a match pattern that could never be matched. For this reason, two Edge Cname match conditions cannot be combined through an AND IF statement.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Referring Domain

The host name associated with the referrer through which content was requested determines whether the Referring Domain condition is met.

The **Matches**/**Does Not Match** option determines the conditions under which the Referring Domain match condition is met:

- **Matches**: Requires the referring host name to match the specified values. 
- **Does Not Match**: Requires that the referring host name does not match the specified value.

Key information:

- Specify multiple host names by delimiting each one with a single space.
- This match condition supports [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- If the specified value does not contain an asterisk, it must be an exact match for the referrer's host name. For example, specifying "mydomain.com" would not match "www.mydomain.com."
- Use the **Ignore Case** option to control whether a case-sensitive comparison is made.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Literal

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Literal match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the one that's defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the one that's defined in this match condition.
  
Key information:

- Header name comparisons are always case-insensitive. Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---  

### Request Header Regex

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Regex match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match the pattern that's defined in the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match the specified regular expression.

Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Replace spaces in the header name with "%20."
- Header value:
  - A header value can take advantage of regular expressions.
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - The match condition is met only when a header value exactly matches at least one of the specified patterns.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Header Wildcard

The **Matches**/**Does Not Match** option determines the conditions under which the Request Header Wildcard match condition is met.

- **Matches**: Requires the request to contain the specified header. Its value must match at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified header.
  - It contains the specified header, but its value does not match any of the specified values.
  
Key information:

- Header name:
  - Header name comparisons are case-insensitive.
  - Spaces in the header name should be replaced with "%20." You can also use this value to specify spaces in a header value.
- Header value:
  - A header value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Use the **Ignore Case** option to control the case-sensitivity of header value comparisons.
  - This match condition is met when a header value exactly matches to at least one of the specified patterns.
  - Specify multiple values by delimiting each one with a single space.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Method

The Request Method match condition is met only when assets are requested through the selected request method. The available request methods are:

- GET
- HEAD
- POST
- OPTIONS
- PUT
- DELETE
- TRACE
- CONNECT

Key information:

- By default, only the GET request method can generate cached content on the network. All other request methods are proxied through the network.
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### Request Scheme

The Request Scheme match condition is met only when assets are requested through the selected protocol. The available protocols are:

- HTTP
- HTTPS

Key information:

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
  - Complete Cache Fill
  - Default Internal Max-Age
  - Force Internal Max-Age
  - Ignore Origin No-Cache
  - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Directory

Identifies a request by its relative path, which excludes the file name of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Directory match condition is met.

- **Matches**: Requires the request to contain a relative URL path, excluding the file name, that matches the specified URL pattern.
- **Does Not Match**: Requires the request to contain a relative URL path, excluding file name, that does not match the specified URL pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison starts before or after the content access point. The content access point is the portion of the path that appears between the Verizon CDN hostname and the relative path to the requested asset (for example, /800001/CustomerOrigin). It identifies a location by server type (for example, CDN or customer origin) and your customer account number.

   The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname. 

  For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder**/index.htm

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

  For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder**/index.htm 

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder**/index.htm

- An edge CNAME URL is rewritten to a CDN URL prior to the URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path.
  - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
  - Custom domain: https:\//my.domain.com/path/asset.htm
    
    - URL path (relative to root): /800001/CustomerOrigin/path/
    
    - URL path (relative to origin): /path/

- The portion of the URL that is used for the URL comparison ends just before the file name of the requested asset. A trailing forward slash is the last character in this type of path.

- Replace any spaces in the URL path pattern with "%20."

- Each URL path pattern can contain one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- Specify multiple URL paths in the pattern by delimiting each one with a single space.

    For example: */sales/ */marketing/

- A URL path specification can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Extension

Identifies requests by the file extension of the requested asset.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Extension match condition is met.

- **Matches**: Requires the URL of the request to contain a file extension that exactly matches the specified pattern.

   For example, if you specify "htm", "htm" assets are matched, but not "html" assets.  

- **Does Not Match**: Requires the URL request to contain a file extension that does not match the specified pattern.

Key information:

- Specify the file extensions to match in the **Value** box. Do not include a leading period; for example, use htm instead of .htm.

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- Specify multiple file extensions by delimiting each extension with a single space. 

    For example: htm html

- For example, specifying "htm" matches "htm" assets, but not "html" assets.

#### Sample Scenario

The following sample configuration assumes that this match condition is met when a request matches one of the specified extensions.

Value specification: asp aspx php html

This match condition is met when it finds URLs that end with the following extensions:

- .asp
- .aspx
- .php
- .html

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Filename

Identifies requests by the file name of the requested asset. For the purposes of this match condition, a file name consists of the name of the requested asset, a period, and the file extension (for example, index.html).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Filename match condition is met.

- **Matches**: Requires the request to contain a file name in its URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a file name in its URL path that does not match the specified pattern.

Key information:

- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.

- To specify multiple file extensions, separate each extension with a single space.

    For example: index.htm index.html

- Replace spaces in a file name value with "%20."

- A file name value can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). For example, each file name pattern can consist of one or more asterisks (*), where each asterisk matches a sequence of one or more characters.

- If wildcard characters are not specified, then only an exact match will satisfy this match condition.

    For example, specifying "presentation.ppt" matches an asset named "presentation.ppt," but not one named "presentation.pptx."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Literal

Compares a request's URL path, including file name, to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Literal match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified pattern.

Key information:

- Use the **Relative to** option to specify whether the URL comparison point begins before or after the content access point. 

    The following values are available for the **Relative to** option:
  - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

    For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

  - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

    For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

  This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to a URL comparison.

For example, both of the following URLs point to the same asset and therefore have the same URL path:

- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    Additional information:
    
    - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
   
    - URL path (relative to origin): /path/asset.htm

- Query strings in the URL are ignored.
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
- The value specified for this match condition is compared against the relative path of the exact request made by the client.

- To match all requests made to a particular directory, use the [URL Path Directory](#url-path-directory) or the [URL Path Wildcard](#url-path-wildcard) match condition.

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Regex

Compares a request's URL path to the specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Regex match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified regular expression.

Key information:

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both URLs point to the same asset and therefore have the same URL path.

     - CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    Additional information:
    
     - URL path: /800001/CustomerOrigin/path/asset.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Spaces in the URL path should be replaced with "%20."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Path Wildcard

Compares a request's relative URL path to the specified wildcard pattern.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Path Wildcard match condition is met.

- **Matches**: Requires the request to contain a URL path that matches the specified wildcard pattern.
- **Does Not Match**: Requires the request to contain a URL path that does not match the specified wildcard pattern.

Key information:

- **Relative to** option: This option determines whether the URL comparison point begins before or after the content access point.

   This option can have the following values:
     - **Root**: Indicates that the URL comparison point begins directly after the CDN hostname.

       For example: http:\//wpc.0001.&lt;domain&gt;/**800001/myorigin/myfolder/index.htm**

     - **Origin**: Indicates that the URL comparison point begins after the content access point (for example, /000001 or /800001/myorigin). Because the \*.azureedge.net CNAME is created relative to the origin directory on the Verizon CDN hostname by default, Azure CDN users should use the **Origin** value. 

       For example: https:\//&lt;endpoint&gt;.azureedge.net/**myfolder/index.htm**

     This URL points to the following Verizon CDN hostname: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/**myfolder/index.htm**

- An edge CNAME URL is rewritten to a CDN URL prior to URL comparison.

    For example, both of the following URLs point to the same asset and therefore have the same URL path:
     - CDN URL: http://wpc.0001.&lt;domain&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    Additional information:
    
     - URL path (relative to root): /800001/CustomerOrigin/path/asset.htm
    
     - URL path (relative to origin): /path/asset.htm
    
- Specify multiple URL paths by delimiting each one with a single space.

   For example: /marketing/asset.* /sales/*.htm

- Query strings in the URL are ignored.
    
- Use the **Ignore Case** option to control whether a case-sensitive comparison is performed.
    
- Replace spaces in the URL path with "%20."
    
- The value specified for a URL path can take advantage of [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Each URL path pattern can contain one or more asterisks (*), where each asterisk can match a sequence of one or more characters.

#### Sample Scenarios

The sample configurations in the following table assume that this match condition is met when a request matches the specified URL pattern:

Value                   | Relative to    | Result 
------------------------|----------------|-------
*/test.html */test.php  | Root or Origin | This pattern is matched by requests for assets named "test.html" or "test.php" in any folder.
/80ABCD/origin/text/*   | Root           | This pattern is matched when the requested asset meets the following criteria: <br />- It must reside on a customer origin called "origin." <br />- The relative path must start with a folder called "text." That is, the requested asset can either reside in the "text" folder or one of its recursive subfolders.
*/css/* */js/*          | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs that contain a css or js folder.
*.jpg *.gif *.png       | Root or Origin | This pattern is matched by all CDN or edge CNAME URLs ending with .jpg, .gif, or .png. An alternative way to specify this pattern is with the [URL Path Extension match condition](#url-path-extension).
/images/* /media/*      | Origin         | This pattern is matched by CDN or edge CNAME URLs whose relative path starts with an "images" or "media" folder. <br />- CDN URL: http:\//wpc.0001.&lt;domain&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Literal

Compares a request's query string to the specified value.

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Literal match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified query string.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified query string.

Key information:

- Only exact query string matches satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Do not include a leading question mark (?) in the query string value text.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding
   ----------|---------
   Space     | %20
   &         | %25

- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Parameter

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified pattern. Query string parameters (for example, parameter=value) in the request URL determine whether this condition is met. This match condition identifies a query string parameter by its name and accepts one or more values for the parameter value. 

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Parameter match condition is met.

- **Matches**: Requires a request to contain the specified parameter with a value that matches at least one of the values that are defined in this match condition.
- **Does Not Match**: Requires that the request meets either of the following criteria:
  - It does not contain the specified parameter.
  - It contains the specified parameter, but its value does not match any of the values that are defined in this match condition.

This match condition provides an easy way to specify parameter name/value combinations. For more flexibility if you are matching a query string parameter, consider using the [URL Query Wildcard](#url-query-wildcard) match condition.

Key information:

- Only a single URL query parameter name can be specified per instance of this match condition.
    
- Because wildcard values are not supported when a parameter name is specified, only exact parameter name matches are eligible for comparison.
- Parameter value(s) can include [wildcard values](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
   - Each parameter value pattern can consist of one or more asterisks (*), where each asterisk can match a sequence of one or more characters.
   - Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

       Character | URL Encoding
       ----------|---------
       Space     | %20
       &         | %25

- Specify multiple query string parameter values by delimiting each one with a single space. This match condition is met when a request contains one of the specified name/value combinations.

   - Example 1:

     - Configuration:

       ValueA ValueB

     - This configuration matches the following query string parameters:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - Example 2:

     - Configuration: 

        Value%20A Value%20B

     - This configuration matches the following query string parameters:

       Parameter1=Value%20A

       Parameter1=Value%20B

- This match condition is met only when there is an exact match to at least one of the specified query string name/value combinations.

   For example, if you use the configuration in the previous example, the parameter name/value combination "Parameter1=ValueAdd" would not be considered a match. However, if you specify either of the following values, it will match that name/value combination:

   - ValueA ValueB ValueAdd
   - ValueA* ValueB

- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- Due to the manner in which cache settings are tracked, this match condition is incompatible with the following features:
   - Complete Cache Fill
   - Default Internal Max-Age
   - Force Internal Max-Age
   - Ignore Origin No-Cache
   - Internal Max-Stale

#### Sample scenarios

The following example demonstrates how this option works in specific situations:

Name  | Value |  Result
------|-------|--------
User  | Joe   | This pattern is matched when the query string for a requested URL is "?user=joe."
User  | *     | This pattern is matched when the query string for a requested URL contains a User parameter.
Email | Joe\* | This pattern is matched when the query string for a requested URL contains an Email parameter that starts with "Joe."

[Back to top](#reference-for-rules-engine-match-conditions)

</br>

---

### URL Query Regex

Identifies requests that contain the specified query string parameter. This parameter is set to a value that matches a specified [regular expression](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

The **Matches**/**Does Not Match** option determines the conditions under which the URL Query Regex match condition is met.

- **Matches**: Requires the request to contain a URL query string that matches the specified regular expression.
- **Does Not Match**: Requires the request to contain a URL query string that does not match the specified regular expression.

Key information:

- Only exact matches to the specified regular expression satisfy this match condition.
    
- Use the **Ignore Case** option to control the case-sensitivity of query string comparisons.
    
- For the purposes of this option, a query string starts with the first character after the question mark (?) delimiter for the query string.
    
- Certain characters require URL encoding. Use the percentage symbol to URL encode the following characters:

   Character | URL Encoding | Value
   ----------|--------------|------
   Space     | %20          | \%20
   &         | %25          | \%25

   Vegye figyelembe, hogy százalékos szimbólumok escape-karakterrel.

- Speciális reguláris kifejezést Double-escape-karakter (például \^$. +) egy fordított perjel szerepeljenek a reguláris kifejezés.

   Példa:

   Érték | -Ként 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
   - Complete Cache Fill
   - Alapértelmezett belső Max-Age
   - Belső Max-Age kényszerítése
   - Ignore Origin No-Cache
   - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>URL-cím lekérdezési helyettesítő karakter

A megadott értékeket, szemben a kérelem lekérdezési karakterláncában hasonlítja össze.

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt az URL-cím lekérdezési helyettesítő feltételnek megfelelő feltételek teljesülése.

- **Egyezések**: A kérés tartalmaz egy URL-cím lekérdezési karakterláncot, amely megfelel a megadott helyettesítő karakteres érték szükséges.
- **Nem felel meg**: A kérés tartalmaz egy URL-cím lekérdezési karakterláncot, amely nem egyezik a megadott helyettesítő karakteres érték szükséges.

Legfontosabb tudnivalókat:

- Ez a beállítás az alkalmazásában egy lekérdezési karakterlánc kezdődik az első karakter után a kérdőjel (?) elválasztó, a lekérdezési karakterlánc.
- Paraméterértékek tartalmazhatnak [helyettesítő karakteres értékek](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Minden paraméter értéke minta tartalmazhat egy vagy több csillagot (*), ahol minden egyes csillag meg tudja egy vagy több karakter sorozata.
   - Bizonyos karakterek megkövetelése URL-Címének kódolása. A százalékos szimbólum URL-címét használja a következő karakterek kódolása:

     Karakter | URL-Címének kódolása
     ----------|---------
     Űr     | %20
     &         | %25

- Adja meg az egy szóköz pedig külön határoló több érték.

   Példa: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Csak pontos egyezések a megadott lekérdezési karakterlánc mintáit legalább egy megfelelnek-e az egyezési feltétellel.
    
- Használja a **esetben figyelmen kívül hagyása** szabályozhatja a lekérdezési karakterlánc-összehasonlítások Kisbetű/nagybetű megkülönböztetése lehetőséget.
    
- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
   - Complete Cache Fill
   - Alapértelmezett belső Max-Age
   - Belső Max-Age kényszerítése
   - Ignore Origin No-Cache
   - Belső maximális – elavult

#### <a name="sample-scenarios"></a>Használati példák

A következő példa bemutatja, hogy ez a beállítás működéséről adott helyzetekben:

 Name (Név)                 | Leírás
 ---------------------|------------
user=joe              | Ez a minta egyezik a kért URL-cím lekérdezési karakterlánc esetén "? felhasználói János =."
\*felhasználó =\* \*optout =\* | Ez a minta egyezik, amikor a CDN URL-lekérdezés tartalmazza a felhasználó vagy a optout paraméter.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>További lépések

- [Az Azure Content Delivery Network áttekintése](cdn-overview.md)
- [Szabálymotor-referencia](cdn-verizon-premium-rules-engine-reference.md)
- [Szabálymotor feltételes kifejezései](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Szabálymotor funkciói](cdn-verizon-premium-rules-engine-reference-features.md)
- [A rules engine használatával a HTTP alapértelmezés felülbírálása](cdn-verizon-premium-rules-engine.md)
