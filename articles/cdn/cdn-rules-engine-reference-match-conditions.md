---
title: Az Azure CDN-szabályok szabálymotor egyezési feltételei |} A Microsoft Docs
description: Az Azure Content Delivery Network dokumentációja szabályok szabálymotor egyezési feltételei.
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
ms.openlocfilehash: 3ebc4d94e0bbf24895be0aeba9fef1cc7aacd6b9
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56736712"
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Az Azure CDN szabálymotorral feltételeknek megfelelő 
Ez a cikk felsorolja az elérhető egyezési feltételei számára az Azure Content Delivery Network (CDN) a részletes leírását [szabálymotorral](cdn-rules-engine.md).

A szabály második része az egyezési feltétellel. Egyeztetési feltételt azonosítja az adott típusú kérelmet szolgáltatások történik.

Ha például az egyeztetési feltételt is használhatja:
- Kérelmek szűrése a tartalom egy adott helyen.
- Kérelmek szűrése egy adott IP-cím vagy ország alapján generált.
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
[Ország](#country) | A megadott országokból kérelmekkel azonosítja.

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

[Vissza a tetejére](#main)

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

[Vissza a tetejére](#main)

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

[Vissza a tetejére](#main)

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

[Vissza a tetejére](#main)

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
  - A cookie-értéket kihasználhatják a [helyettesítő karakteres értékek](cdn-rules-engine-reference.md#wildcard-values). 
  - Ha egy helyettesítő karakteres érték nem lett megadva, csak pontos egyezés kielégíteni ezt az egyezési feltétellel. Például adja meg a "Value" egyezni fog "Value", de nem "Érték1" vagy "Value2."
  - Használja a **esetben figyelmen kívül hagyása** -e a kis-és nagybetűket összehasonlítás a kérelem cookie-k értékkel ellenőrzési lehetőséget.
- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
  - Complete Cache Fill
  - Alapértelmezett belső Max-Age
  - Belső Max-Age kényszerítése
  - Ignore Origin No-Cache
  - Belső maximális – elavult

[Vissza a tetejére](#main)

</br>

---
### <a name="cookie-parameter-regex"></a>Cookie-k paramétert reguláris kifejezés
A cookie-k paramétert reguláris kifejezést az egyezési feltétellel határozza meg, a cookie nevét és értékét. Használhat [reguláris kifejezések](cdn-rules-engine-reference.md#regular-expressions) meghatározásához kívánt cookie-értéket. 

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

[Vissza a tetejére](#main)

</br>

---
### <a name="country"></a>Ország
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

- Hozzáférés (403) szolgáltatás megtagadása: Engedélyezze a [hozzáférés megtagadása (403) funkció](cdn-rules-engine-reference-features.md#deny-access-403) az ország szerinti szűrés funkció engedélyezése vagy letiltása részének replikálni.

[Vissza a tetejére](#main)

</br>

---
### <a name="customer-origin"></a>Ügyfél kezdőpontja

Legfontosabb tudnivalókat: 
- Az ügyfél kezdőpontja egyezés feltétel teljesül, függetlenül attól, hogy kért CDN URL-címet vagy egy edge CNAME mutató URL-cím a kijelölt ügyfél kezdőpontja.
- A szabály által hivatkozott felhasználói forrás konfigurálása nem lehet törölni az ügyfél kezdőpontja oldalról. Mielőtt megkísérli törölni egy ügyfél forrás-konfigurációt, győződjön meg arról, hogy a következő konfigurációk nem hivatkoznak azt:
  - Egy ügyfél kezdőpontja egyezési feltételei
  - An edge CNAME configuration
- Ne használja az és a egy IF utasítást úgy, hogy bizonyos egyezési feltételei. Például kombinálása egy CDN-forrás az egyezési feltétellel rendelkező ügyfél kezdőpontja egyeztetési feltételt hozna létre, amely soha nem egyeztethető-egyeztetési minta. Ezért két ügyfél kezdőpontja egyezési feltételei keresztül egy és IF utasítás nelze kombinovat.

[Vissza a tetejére](#main)

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

Typo     | Leírás
---------|------------
Szövegkonstans  | Ezt a beállítást, hogy a legtöbb karakterek használatával kulcsszó különleges jelentéssel a felvételt a [Szövegkonstansérték](cdn-rules-engine-reference.md#literal-values).
Helyettesítő karakter | Ezzel a lehetőséggel előnyeit minden [helyettesítő karakterek] ([helyettesítő karakteres értékek](cdn-rules-engine-reference.md#wildcard-values).
Regex    | Válassza ezt a lehetőséget a használandó [reguláris kifejezések](cdn-rules-engine-reference.md#regular-expressions). Reguláris kifejezések hasznosak karaktereket a mintázat létrehozása.

#### <a name="wurfl-capabilities"></a>WURFL képességek
Egy WURFL képesség, amely ismerteti a mobil eszközök kategória hivatkozik. A kiválasztott funkció határozza meg a mobil eszköz leírása, amely a kérések azonosítására szolgál.

Az alábbi táblázat felsorolja a WURFL képességek és a rules engine azok változókat.
<br>
> [!NOTE] 
> A következő változók használhatók a **ügyfél kérelem fejléce módosítsa** és **ügyfél válasz fejléce módosítsa** funkciókat.

Képesség | Változó | Leírás | Mintaértékek
-----------|----------|-------------|----------------
Márkanév | %{wurfl_cap_brand_name} | Egy karakterlánc, amely azt jelzi, hogy az eszköz márkanevét. | Samsung
Eszköz operációs rendszere | %{wurfl_cap_device_os} | Egy karakterlánc, amely azt jelzi, hogy az eszközön telepített operációs rendszer. | IOS
Eszköz operációs rendszerének verziója | %{wurfl_cap_device_os_version} | Egy karakterlánc, amely azt jelzi, hogy az eszközön telepített operációs rendszer verziószáma. | 1.0.1
Kettős tájolása | %{wurfl_cap_dual_orientation} | Egy logikai érték, amely jelzi, hogy az eszköz támogatja-e kettős tájolását. | true
HTML Preferred DTD | %{wurfl_cap_html_preferred_dtd} | Egy karakterlánc, amely azt jelzi, hogy a mobil eszköz előnyben részesített dokumentum típus definíciója (DTD) HTML-tartalmakat. | nincs<br/>xhtml_basic<br/>html5
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

[Vissza a tetejére](#main)

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

[Vissza a tetejére](#main)

</br>

---
### <a name="referring-domain"></a>Hivatkozó tartomány
A gazdagép nevét a hivatkozó, amelyen keresztül tartalmat kért meghatározza, hogy a tartomány hivatkozó feltétel teljesül-e társítva. 

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt a hivatkozó tartomány feltételnek megfelelő feltételek teljesülése:
- **Egyezések**: A megadott értékekre hivatkozó állomásnév szükséges. 
- **Nem felel meg**: Megköveteli, hogy a hivatkozó gazdagép neve nem egyezik a megadott érték.

Legfontosabb tudnivalókat:
- Adja meg az egy szóköz pedig külön határoló több állomásnevet.
- Támogatja-e az egyezési feltétellel [helyettesítő karakteres értékek](cdn-rules-engine-reference.md#wildcard-values).
- A megadott érték nem tartalmaz egy csillag, ha a hivatkozó névhez pontos egyezésűnek kell lennie. Például adja meg a "tartomany.com" nem felel meg a "www.mydomain.com."
- Használja a **esetben figyelmen kívül hagyása** -e a kis-és nagybetűket összehasonlítás ellenőrzési lehetőséget.
- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
  - Complete Cache Fill
  - Alapértelmezett belső Max-Age
  - Belső Max-Age kényszerítése
  - Ignore Origin No-Cache
  - Belső maximális – elavult

[Vissza a tetejére](#main)

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

[Vissza a tetejére](#main)

</br>

---
### <a name="request-header-regex"></a>Kérelem fejléce reguláris kifejezés
A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt a kérelem fejlécében Regex feltételnek megfelelő feltételek teljesülése.
- **Egyezések**: A kérelem tartalmazza a megadott fejléc igényel. Az értékét meg kell egyeznie a megadott minta a megadott [reguláris kifejezés](cdn-rules-engine-reference.md#regular-expressions).
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

[Vissza a tetejére](#main)

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
  - A fejléc értéke kihasználhatják a [helyettesítő karakteres értékek](cdn-rules-engine-reference.md#wildcard-values).
  - Használja a **esetben figyelmen kívül hagyása** szabályozhatja a fejléc értéke összehasonlítások Kisbetű/nagybetű megkülönböztetése lehetőséget.
  - A fejléc értéke pontosan egyezik a megadott minták egyikét, ha a egyezés feltétel teljesül.
  - Adja meg az egy szóköz pedig külön határoló több érték.
- A módját, mely gyorsítótárban beállítások nyomon követi, mert ez az egyezési feltétellel nem kompatibilis a a következő funkciókat:
  - Complete Cache Fill
  - Alapértelmezett belső Max-Age
  - Belső Max-Age kényszerítése
  - Ignore Origin No-Cache
  - Belső maximális – elavult

[Vissza a tetejére](#main)

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

[Vissza a tetejére](#main)

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

[Vissza a tetejére](#main)

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

- URL-cím elérési utat kihasználhatják a [helyettesítő karakteres értékek](cdn-rules-engine-reference.md#wildcard-values).

- Használja a **esetben figyelmen kívül hagyása** ellenőrzési lehetőséget egy kis-és nagybetűket összehasonlítás történik-e.

[Vissza a tetejére](#main)

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

[Vissza a tetejére](#main)

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
    
- Egy fájl név-érték kihasználhatják a [helyettesítő karakteres értékek](cdn-rules-engine-reference.md#wildcard-values). Például egyes Fájlnévminta tartalmazhat egy vagy több csillagot (*), ahol minden egyes csillag olyan sorozataira illeszkedik, egy vagy több karakter.
    
- Ha helyettesítő karaktereket nem meg van adva, csak pontos egyezés kielégíteni ezt az egyezési feltétellel.

    Például adja meg a "bemutató.ppt" megfelel egy "bemutató.ppt", de nem egy elnevezett "presentation.pptx." nevű objektum

[Vissza a tetejére](#main)

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

[Vissza a tetejére](#main)

</br>

---
### <a name="url-path-regex"></a>URL-cím elérési út reguláris kifejezés
A kérelem URL-cím a megadott összehasonlítja [reguláris kifejezés](cdn-rules-engine-reference.md#regular-expressions).

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

[Vissza a tetejére](#main)

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
     - A CDN URL-címe: http://wpc.0001.&lt; tartomány&gt;/800001/CustomerOrigin/path/asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    További információ:
    
     - (Root) képest relatív URL-cím: /800001/CustomerOrigin/path/asset.htm
    
     - (Forrás) képest relatív URL-cím: /path/asset.htm
    
- Adja meg az egy szóköz pedig külön határoló több URL-cím elérési út.

   Például: /marketing/asset.* /sales/*.htm

- Az URL-cím lekérdezési karakterláncok figyelmen kívül hagyja.
    
- Használja a **esetben figyelmen kívül hagyása** ellenőrzési lehetőséget egy kis-és nagybetűket összehasonlítás történik-e.
    
- Cserélje le az URL-címet a tárolóhelyek "% 20."
    
- Egy URL-cím kihasználhatják a megadott érték [helyettesítő karakteres értékek](cdn-rules-engine-reference.md#wildcard-values). Minden egyes URL-cím elérési út mintája tartalmazhat egy vagy több csillagot (*), ahol minden egyes csillag meg tudja egy vagy több karakter sorozata.

#### <a name="sample-scenarios"></a>Mintaforgatókönyvek

A minta konfigurációkat az alábbi táblázat azt feltételezik, hogy a match feltétel teljesül-e, amikor egy kérelem megfelel a megadott URL-minta:

Érték                   | A viszonyítva    | Eredmény 
------------------------|----------------|-------
*/test.html */test.php  | Legfelső szintű vagy a forrás | Ez a minta egyezik "test.html" vagy "test.php" bármelyik mappájában nevű eszközök kérelmek szerint.
/80ABCD/origin/text/*   | Gyökér           | Ez a minta egyezik, ha a kért objektum megfelel a következő feltételeknek: <br />-Kell lennie, egy ügyfél forrás neve "origin". <br />– A relatív elérési út "szöveg." nevű mappát kell kezdődnie. A kért objektumhoz, vagy lehetnek a "text" mappában vagy egy rekurzív almappája.
*/CSS/* */js/*          | Legfelső szintű vagy a forrás | Ez a minta egyezik az összes CDN vagy edge egy css vagy js mappát tartalmazó CNAME URL-címeket.
*.jpg *.gif *.png       | Legfelső szintű vagy a forrás | Ez a minta egyezik a CDN- vagy peremtábla CNAME URL-címekhez .jpg, .gif, vagy .png végződésű. Adja meg ezt a mintát egy másik módja a [URL-cím elérési út bővítmény feltételnek megfelelő](#url-path-extension).
/ képek / * / media / *      | Forrás         | Ez a minta egyezik a CDN- vagy peremtábla CNAME URL-címek, amelyek relatív elérési út egy "rendszerképek" vagy "média" mappa kezdődik. <br />– A CDN URL-cím: http:\//wpc.0001.&lt; tartomány&gt;/800001/myorigin/images/sales/event1.png<br />- Sample edge CNAME URL: http:\//cdn.mydomain.com/images/sales/event1.png

[Vissza a tetejére](#main)

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

[Vissza a tetejére](#main)

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
- Paraméter érték(ek) tartalmazhatnak [helyettesítő karakteres értékek](cdn-rules-engine-reference.md#wildcard-values).
   - Minden paraméter értéke minta tartalmazhat egy vagy több csillagot (*), ahol minden egyes csillag meg tudja egy vagy több karakter sorozata.
   - Bizonyos karakterek megkövetelése URL-Címének kódolása. A százalékos szimbólum URL-címét használja a következő karakterek kódolása:

       Karakter | URL-Címének kódolása
       ----------|---------
       Űr     | %20
       &         | %25

- Adja meg a lekérdezési karakterlánc paraméter több értéket határoló egyenként a szóköz. Amikor egy kérés a megadott név-érték párok kombinációk egyikét tartalmazza a match feltétel teljesül.

   - 1. példa:

     - Konfigurálás:

       Érték1, érték2

     - Ez a konfiguráció megfelel a következő lekérdezési karakterlánc paraméterei:

       Parameter1=ValueA
    
       Parameter1=ValueB

   - 2. példa

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
E-mail | János\* | Ez a minta egyezik, ha a kért URL-cím lekérdezési karakterláncot tartalmaz egy e-mailek paraméter, amely elindítja a "János".

[Vissza a tetejére](#main)

</br>

---
### <a name="url-query-regex"></a>URL-cím lekérdezési reguláris kifejezés
A megadott lekérdezési karakterláncot tartalmazó kérelmek azonosítja. Ez a paraméter értéke, amely megfelel a megadott érték [reguláris kifejezés](cdn-rules-engine-reference.md#regular-expressions).

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt az URL-cím lekérdezési Regex feltételnek megfelelő feltételek teljesülése.
- **Egyezések**: Szükséges a kérelem URL-cím lekérdezési karakterláncot, amely megfelel a megadott reguláris kifejezést tartalmaz.
- **Nem felel meg**: Szükséges a kérelem URL-cím lekérdezési karakterláncot, amely nem egyezik a megadott reguláris kifejezést tartalmaz.

Legfontosabb tudnivalókat:
- Csak pontos egyezések a megadott reguláris kifejezést megfelelnek-e az egyezési feltétellel.
    
- Használja a **esetben figyelmen kívül hagyása** szabályozhatja a lekérdezési karakterlánc-összehasonlítások Kisbetű/nagybetű megkülönböztetése lehetőséget.
    
- Ez a beállítás az alkalmazásában egy lekérdezési karakterlánc kezdődik az első karakter után a kérdőjel (?) elválasztó, a lekérdezési karakterlánc.
    
- Bizonyos karakterek megkövetelése URL-Címének kódolása. A százalékos szimbólum URL-címét használja a következő karakterek kódolása:

   Karakter | URL-Címének kódolása | Érték
   ----------|--------------|------
   Űr     | %20          | \%20
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


[Vissza a tetejére](#main)

</br>

---
### <a name="url-query-wildcard"></a>URL-cím lekérdezési helyettesítő karakter
A megadott értékeket, szemben a kérelem lekérdezési karakterláncában hasonlítja össze.

A **egyezések**/**Neodpovídá** a beállítás határozza meg, amely alatt az URL-cím lekérdezési helyettesítő feltételnek megfelelő feltételek teljesülése.
- **Egyezések**: A kérés tartalmaz egy URL-cím lekérdezési karakterláncot, amely megfelel a megadott helyettesítő karakteres érték szükséges.
- **Nem felel meg**: A kérés tartalmaz egy URL-cím lekérdezési karakterláncot, amely nem egyezik a megadott helyettesítő karakteres érték szükséges.

Legfontosabb tudnivalókat:
- Ez a beállítás az alkalmazásában egy lekérdezési karakterlánc kezdődik az első karakter után a kérdőjel (?) elválasztó, a lekérdezési karakterlánc.
- Paraméterértékek tartalmazhatnak [helyettesítő karakteres értékek](cdn-rules-engine-reference.md#wildcard-values):
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

[Vissza a tetejére](#main)

</br>

## <a name="next-steps"></a>További lépések
* [Az Azure Content Delivery Network áttekintése](cdn-overview.md)
* [Szabálymotor-referencia](cdn-rules-engine-reference.md)
* [Szabálymotor feltételes kifejezései](cdn-rules-engine-reference-conditional-expressions.md)
* [Szabálymotor funkciói](cdn-rules-engine-reference-features.md)
* [A rules engine használatával a HTTP alapértelmezés felülbírálása](cdn-rules-engine.md)

