---
title: Azure CDN a Verizon Premium Rules motor Match feltételekkel | Microsoft Docs
description: Az Azure Content Delivery Network a Verizon Premium Rules motor egyeztetési feltételeit ismertető dokumentációja.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 05/31/2019
ms.author: magattus
ms.openlocfilehash: 1660dca34b2f128ef5889145fcdeed0d2523b9bb
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "67593204"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-match-conditions"></a>Azure CDN a Verizon Premium szabályainak motorjának egyeztetési feltételeiről

Ez a cikk részletes leírást nyújt az Azure Content Delivery Network (CDN) a Verizon Premium [Rules Engine](cdn-verizon-premium-rules-engine.md)-ről elérhető egyezési feltételeiről.

A szabály második része az egyeztetési feltétel. Az egyeztetési feltétel azokat a kérelmeket azonosítja, amelyekhez a rendszer a különböző funkciókat fogja végrehajtani.

Az egyeztetési feltételt például a következőre használhatja:

- A tartalomra vonatkozó kérelmek szűrése egy adott helyen.
- Egy adott IP-címről vagy országból/régióból generált kérelmek szűrése.
- Kérelmek szűrése fejléc-információk alapján.

## <a name="always-match-condition"></a>Mindig egyező feltétel

Az Always Match feltétel a szolgáltatások alapértelmezett készletét alkalmazza az összes kérelemre.

Name (Név) | Cél
-----|--------
[Mindig](#always) | A szolgáltatások alapértelmezett készletét alkalmazza az összes kérelemre.

## <a name="device-match-condition"></a>Eszköz egyeztetési feltétele

Az eszköz egyeztetése feltétel azonosítja a mobileszköz által a tulajdonságok alapján küldött kérelmeket.  

Name (Név) | Cél
-----|--------
[Device](#device) | Azonosítja a mobileszköz által a tulajdonságok alapján küldött kérelmeket.

## <a name="location-match-conditions"></a>A hely egyeztetési feltételei

A hely egyeztetési feltételei a kérelmező helye alapján azonosítják a kérelmeket.

Name (Név) | Cél
-----|--------
[AS szám](#as-number) | Az adott hálózatból származó kérelmeket azonosítja.
[Ország](#country) | A megadott országokból/régiókból származó kérelmeket azonosítja.

## <a name="origin-match-conditions"></a>A forrás egyeztetési feltételei

A forrás egyeztetési feltételek azokat a kérelmeket azonosítják, amelyek Content Delivery Network tárolóra vagy ügyfél-forrásra mutatnak.

Name (Név) | Cél
-----|--------
[CDN-forrás](#cdn-origin) | A Content Delivery Network tárolóban tárolt tartalomra vonatkozó kérelmeket azonosítja.
[Ügyfél forrása](#customer-origin) | Az adott ügyfél-kiszolgálón tárolt tartalomra vonatkozó kérelmeket azonosítja.

## <a name="request-match-conditions"></a>Kérelem egyeztetési feltételei

A kérés egyeztetési feltételek alapján azonosítja a kérelmeket a tulajdonságaik alapján.

Name (Név) | Cél
-----|--------
[Ügyfél IP-címe](#client-ip-address) | Az adott IP-címről származó kérelmeket azonosítja.
[Cookie paraméter](#cookie-parameter) | A megadott értékre vonatkozó kérelmekhez társított cookie-k ellenőrzése.
[Cookie-paraméterek Regexje](#cookie-parameter-regex) | Ellenőrzi az egyes kérelmekhez társított cookie-kat a megadott reguláris kifejezéshez.
[Edge CNAME](#edge-cname) | A megadott peremhálózati CNAME-re mutató kérelmeket azonosítja.
[Hivatkozó tartomány](#referring-domain) | A megadott állomásnevek által hivatkozott kérelmeket azonosítja.
[Kérelem fejléce – literál](#request-header-literal) | Azokat a kérelmeket azonosítja, amelyek tartalmazzák a megadott fejlécet egy megadott értékre.
[Kérelem fejlécének Regexje](#request-header-regex) | A megadott fejlécet tartalmazó kérelmeket azonosítja olyan értékre, amely megfelel a megadott reguláris kifejezésnek.
[Kérelem fejlécének helyettesítő karaktere](#request-header-wildcard) | A megadott fejlécet tartalmazó kérelmeket azonosítja olyan értékre, amely megfelel a megadott mintának.
[Kérelem metódusa](#request-method) | A HTTP-metódussal azonosítja a kérelmeket.
[Kérési séma](#request-scheme) | A HTTP protokollal azonosítja a kérelmeket.

## <a name="url-match-conditions"></a>URL-egyeztetési feltételek

Az URL-cím egyezési feltételei a kérelmeket az URL-címek alapján azonosítják.

Name (Név) | Cél
-----|--------
[URL elérési útja könyvtár](#url-path-directory) | A kéréseket a relatív elérési úttal azonosítja.
[URL-elérési út kiterjesztése](#url-path-extension) | A kérelmeket a fájlnévkiterjesztés alapján azonosítja.
[URL elérési útja fájlnév](#url-path-filename) | A kérelmeket a fájlnév szerint azonosítja.
[URL elérési útja literál](#url-path-literal) | Összehasonlítja a kérelem relatív elérési útját a megadott értékkel.
[URL-cím elérési útja – regex](#url-path-regex) | Összehasonlítja a kérelem relatív elérési útját a megadott reguláris kifejezéssel.
[URL elérési útja helyettesítő karakter](#url-path-wildcard) | Összehasonlítja a kérelem relatív elérési útját a megadott mintával.
[URL-lekérdezési literál](#url-query-literal) | Összehasonlítja a kérelem lekérdezési karakterláncát a megadott értékkel.
[URL-lekérdezési paraméter](#url-query-parameter) | A megadott lekérdezési karakterlánc paramétert tartalmazó kérelmeket azonosítja olyan értékre, amely megfelel egy megadott mintának.
[URL-lekérdezés regex](#url-query-regex) | A megadott lekérdezési karakterlánc paramétert tartalmazó kérelmeket azonosítja olyan értékre, amely megfelel egy adott reguláris kifejezésnek.
[URL-lekérdezés helyettesítő karaktere](#url-query-wildcard) | A megadott értéket hasonlítja össze a kérelem lekérdezési karakterláncával.

## <a name="reference-for-rules-engine-match-conditions"></a>A szabályok motorjának egyeztetési feltételei

---

### <a name="always"></a>Mindig

Az Always Match feltétel a szolgáltatások alapértelmezett készletét alkalmazza az összes kérelemre.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="as-number"></a>AS szám

A AS Number hálózatot az autonóm rendszer száma (ASN) határozza meg. 

A **egyezések**/**nem egyeznek** beállítás határozza meg, hogy teljesülnek-e az as-szám egyeztetési feltételének feltételei:

- **Egyezések**: Ehhez az szükséges, hogy az ügyfél-hálózat ASN-je megfeleljen a megadott ASN egyikének. 
- **Nem egyezik**: Ehhez az szükséges, hogy az ügyfél-hálózat ASN-je ne egyezzen meg a megadott ASN.

Legfontosabb információk:

- Több ASN is megadhat, ha mindegyiket egyetlen szóközzel kell korlátozni. Például az 64514 64515 megfelel a 64514 vagy 64515 rendszertől érkező kéréseknek.
- Előfordulhat, hogy bizonyos kérelmek nem adnak vissza érvényes ASN-t. A kérdőjel (?) egyezteti azokat a kérelmeket, amelyekhez nem lehet érvényes ASN-t meghatározni.
- A kívánt hálózat teljes ASN-jét határozza meg. A részleges értékeket nem fogja egyeztetni.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez a megfeleltetési feltétel nem kompatibilis a következő funkciókkal:
  - Gyorsítótár kitöltésének befejezése
  - Alapértelmezett belső max. Age
  - A belső Max-Age kényszerítése
  - Kihagyott forrás – gyorsítótár
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cdn-origin"></a>CDN-forrás

A CDN-forrás egyeztetési feltétele akkor teljesül, ha a következő feltételek mindegyike teljesül:

- A CDN-tárolóból származó tartalmat kérték.
- A kérelem URI-ja a jelen egyeztetési feltételben definiált tartalom-hozzáférési pont (például/000001) típusát használja:
  - CDN URL-CÍME: A kérés URI azonosítójának tartalmaznia kell a kiválasztott tartalom-hozzáférési pontot.
  - Peremhálózati CNAME URL-cím: A megfelelő peremhálózati CNAME-konfigurációnak a kiválasztott tartalom-hozzáférési pontra kell mutatnia.
  
Legfontosabb információk:

- A tartalom-hozzáférési pont azonosítja azt a szolgáltatást, amely a kért tartalmat szolgálja ki.
- Bizonyos egyezési feltételek összevonásához ne használjon és IF utasítást. Például egy CDN-beli forrás egyeztetési feltételének összevonása egy ügyfél-forrás egyeztetési feltétellel olyan egyezési mintát hoz létre, amely soha nem egyeztethető össze. Ebből kifolyólag két CDN-forrás egyeztetési feltétele nem kombinálható egy és IF utasítással.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="client-ip-address"></a>Ügyfél IP-címe

A **egyezések**/**nem egyeznek** beállítás határozza meg, hogy az ügyfél IP-címének egyeztetési feltétele teljesült-e:

- **Egyezések**: Ehhez az szükséges, hogy az ügyfél IP-címe egyezzen a megadott IP-címek egyikével. 
- **Nem egyezik**: Ehhez az szükséges, hogy az ügyfél IP-címe ne egyezzen meg a megadott IP-címek egyikével sem. 

Legfontosabb információk:

- CIDR-jelölés használata.
- Több IP-címet és/vagy IP-címtartományt is megadhat, ha mindegyiket egyetlen szóközzel kell korlátozni. Példa:
  - **IPv4-példa**: a 1.2.3.4 10.20.30.40 a 1.2.3.4 vagy a 10.20.30.40 címről érkező összes kérésnek megfelel.
  - **IPv6-példa**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 a 1:2:3:4:5:6:7:8 vagy 10:20:30:40:50:60:70:80 címről érkező összes kérésnek megfelel.
- Az IP-címterület szintaxisa az alapszintű IP-cím, amelyet egy perjel és az előtag mérete követ. Példa:
  - **IPv4-példa**: a 5.5.5.64/26 minden olyan kérést egyeztet, amely a 5.5.5.64 címről érkezik a 5.5.5.127-on keresztül.
  - **IPv6-példa**: a 1:2:3:/48 a 1:2:3:0:0:0:0:0 – 1:2: 3: FFFF: FFFF: FFFF: FFFF: FFFF címen megjelenő összes kérésnek megfelel.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez a megfeleltetési feltétel nem kompatibilis a következő funkciókkal:
  - Gyorsítótár kitöltésének befejezése
  - Alapértelmezett belső max. Age
  - A belső Max-Age kényszerítése
  - Kihagyott forrás – gyorsítótár
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="cookie-parameter"></a>Cookie paraméter

A **egyezések**/**nem egyeznek** beállítás határozza meg, hogy a cookie-paraméter megfelel-e a feltételnek.

- **Egyezések**: A megadott cookie-nak egy olyan értékkel kell rendelkeznie, amely megfelel a jelen egyeztetési feltételben definiált értékek legalább egyikének.
- **Nem egyezik**: Megköveteli, hogy a kérelem megfeleljen a következő feltételek egyikének:
  - Nem tartalmazza a megadott cookie-t.
  - Tartalmazza a megadott cookie-t, de az értéke nem felel meg a jelen egyeztetési feltételben definiált értékek egyikének sem.
  
Legfontosabb információk:

- Cookie neve:
  - Mivel a helyettesítő karakterek, beleértve a csillagokat (*), nem támogatottak a cookie-nevek megadásakor, csak a pontos cookie-nevek jogosultak az összehasonlításra.
  - Ennek a megfeleltetési feltételnek a példányain csak egyetlen cookie-nevet lehet megadni.
  - A cookie-nevek összehasonlítása a kis-és nagybetűk megkülönböztetése nélkül történik.
- Cookie értéke:
  - Több cookie-értéket is megadhat, ha mindegyiket egyetlen szóközzel kell korlátozni.
  - A cookie-értékek kihasználhatják a [helyettesítő karaktereket](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Ha a helyettesítő karakter értéke nincs megadva, akkor csak a pontos egyezés felel meg ennek a megfelelési feltételnek. Például az "érték" megadásakor a "value" értéknek kell megegyeznie, de nem "érték1" vagy "érték2".
  - A kis-és nagybetűk **figyelmen kívül hagyása** beállítással szabályozhatja, hogy a rendszer a kérelem cookie-értékének kis-és nagybetűs összehasonlítását alkalmazza
- A gyorsítótár-beállítások nyomon követésének módja miatt ez a megfeleltetési feltétel nem kompatibilis a következő funkciókkal:
  - Gyorsítótár kitöltésének befejezése
  - Alapértelmezett belső max. Age
  - A belső Max-Age kényszerítése
  - Kihagyott forrás – gyorsítótár
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)
</br>

---

### <a name="cookie-parameter-regex"></a>Cookie-paraméterek Regexje

A cookie-paraméter regex-egyeztetési feltétel határozza meg a cookie nevét és értékét. A kívánt cookie-érték megadásához [reguláris kifejezéseket](cdn-verizon-premium-rules-engine-reference.md#regular-expressions) használhat.

A **egyezések**/**nem egyeznek** beállítás határozza meg, hogy a rendszer milyen feltételek teljesülése esetén tartalmazza a cookie-paramétert a regexnek.

- **Egyezések**: A megadott cookie-t tartalmazó kérést igényel, amely megfelel a megadott reguláris kifejezésnek.
- **Nem egyezik**: Megköveteli, hogy a kérelem megfeleljen a következő feltételek egyikének:
  - Nem tartalmazza a megadott cookie-t.
  - Tartalmazza a megadott cookie-t, de az értéke nem felel meg a megadott reguláris kifejezésnek.
  
Legfontosabb információk:

- Cookie neve:
  - Mivel a reguláris kifejezések és a helyettesítő karakterek, beleértve a csillagokat (*), nem támogatottak a cookie-nevek megadásakor, csak a pontos cookie-nevek felelnek meg az összehasonlításhoz.
  - Ennek a megfeleltetési feltételnek a példányain csak egyetlen cookie-nevet lehet megadni.
  - A cookie-nevek összehasonlítása a kis-és nagybetűk megkülönböztetése nélkül történik.
- Cookie értéke:
  - A cookie-értékek a reguláris kifejezések előnyeit vehetik igénybe.
  - A kis-és nagybetűk **figyelmen kívül hagyása** beállítással szabályozhatja, hogy a rendszer a kérelem cookie-értékének kis-és nagybetűs összehasonlítását alkalmazza
- A gyorsítótár-beállítások nyomon követésének módja miatt ez a megfeleltetési feltétel nem kompatibilis a következő funkciókkal:
  - Gyorsítótár kitöltésének befejezése
  - Alapértelmezett belső max. Age
  - A belső Max-Age kényszerítése
  - Kihagyott forrás – gyorsítótár
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="country"></a>Country

Országot az országkód alapján is megadhat. 

A **egyezések**/**nem egyeznek** beállítás határozza meg, hogy a rendszer milyen feltételek teljesülése esetén teljesíti az ország egyeztetési feltételét:

- **Egyezések**: A kérelemben szerepelnie kell a megadott országkód-értékeknek. 
- **Nem egyezik**: Megköveteli, hogy a kérelem ne tartalmazza a megadott országkód-értékeket.

Legfontosabb információk:

- Több országkódot is megadhat, ha mindegyiket egyetlen szóközzel kell elválasztani.
- Az országkód megadásakor a helyettesítő karakterek használata nem támogatott.
- Az "EU" és az "AP" országkódok nem foglalják magukban az összes IP-címet ezekben a régiókban.
- Előfordulhat, hogy bizonyos kérések nem adnak vissza érvényes országkódot. A kérdőjel (?) egyezteti azokat a kérelmeket, amelyekhez nem határozható meg érvényes országkód.
- Az országkódok megkülönböztetik a kis-és nagybetűket.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez a megfeleltetési feltétel nem kompatibilis a következő funkciókkal:
  - Gyorsítótár kitöltésének befejezése
  - Alapértelmezett belső max. Age
  - A belső Max-Age kényszerítése
  - Kihagyott forrás – gyorsítótár
  - Belső maximális – elavult

#### <a name="implementing-country-filtering-by-using-the-rules-engine"></a>Az országok szűrésének megvalósítása a szabályok motor használatával

Ez az egyeztetési feltétel lehetővé teszi számos testreszabási művelet végrehajtását azon hely alapján, amelyről a kérés származik. Az ország-szűrési funkció viselkedését például a következő konfigurációval lehet replikálni:

- URL-cím helyettesítő karakterének egyezése: Állítsa be az [URL-cím helyettesítő karakterének egyezési](#url-path-wildcard) feltételt a biztonságos könyvtárba. 
    Illesszen be egy csillagot a relatív elérési út végére, hogy a szabály korlátozza az összes gyermek hozzáférését.

- Ország/régió egyezés Állítsa be az ország egyeztetési feltételt a kívánt készlethez országok/régiók.
  - Lehetővé Az ország az egyezési feltétellel beállítása **Neodpovídá** határozzák meg az URL-cím elérési út helyettesítő egyeztetési feltételt a helyen tárolt tartalmat csak a megadott országok/régiók hozzáférésének engedélyezéséhez.
  - Blokk Az ország az egyezési feltétellel beállítása **egyezések** nem férhet hozzá az URL-cím elérési út helyettesítő egyezési feltételei által meghatározott helyen tárolt tartalmat a megadott országok/régiók blokkolására.

- Hozzáférés megtagadása (403) szolgáltatás: Engedélyezze a [megtagadási hozzáférés (403) szolgáltatást](cdn-verizon-premium-rules-engine-reference-features.md#deny-access-403) az ország-szűrési szolgáltatás engedélyezés vagy Letiltás részének replikálásához.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="customer-origin"></a>Ügyfél forrása

Legfontosabb információk:

- A vásárlói forrás egyeztetési feltétele teljesül, függetlenül attól, hogy a tartalmat egy CDN URL-címen vagy egy, a kiválasztott ügyfél-forrásra mutató peremhálózati CNAME URL-címen keresztül kéri-e a rendszer.
- Egy szabály által hivatkozott ügyfél-forrás konfiguráció nem törölhető az ügyfél forrásának oldaláról. Az ügyfél-eredetű konfiguráció törlésének megkísérlése előtt győződjön meg arról, hogy a következő konfigurációk nem hivatkoznak rá:
  - Az ügyfél-forrás egyeztetési feltétele
  - Peremhálózati CNAME konfiguráció
- Bizonyos egyezési feltételek összevonásához ne használjon és IF utasítást. Ha például egy ügyfél-forrás egyeztetési feltételt egy CDN-beli kiindulási feltétellel kombinálja, egy olyan egyezési mintát hoz létre, amely soha nem egyeztethető össze. Ezért két ügyfél-forrás egyeztetési feltétel nem kombinálható egy és IF utasítással.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="device"></a>Eszköz

Az eszköz egyeztetése feltétel azonosítja a mobileszköz által a tulajdonságok alapján küldött kérelmeket. A mobileszköz-észlelés a [WURFLon](http://wurfl.sourceforge.net/)keresztül érhető el. 

A **egyezések**/**nem egyeznek** beállítás határozza meg, hogy a rendszer milyen feltételek teljesülése esetén teljesíti az eszköz egyeztetési feltételét:

- **Egyezések**: A kérelmező eszközének meg kell egyeznie a megadott értékkel. 
- **Nem egyezik**: Ehhez a kérelmező eszközének nem kell megegyeznie a megadott értékkel.

Legfontosabb információk:

- Az **eset mellőzése** beállítás megadásával adhatja meg, hogy a megadott érték megkülönbözteti-e a kis-és nagybetűket
- A gyorsítótár-beállítások nyomon követésének módja miatt ez a megfeleltetési feltétel nem kompatibilis a következő funkciókkal:
  - Gyorsítótár kitöltésének befejezése
  - Alapértelmezett belső max. Age
  - A belső Max-Age kényszerítése
  - Kihagyott forrás – gyorsítótár
  - Belső maximális – elavult

#### <a name="string-type"></a>Karakterlánc típusa

A WURFL-képességek általában számok, betűk és szimbólumok tetszőleges kombinációját fogadják el. Ennek a képességnek a rugalmas jellege miatt ki kell választania, hogy a rendszer hogyan értelmezze az ehhez a megfeleltetési feltételhez társított értéket. A következő táblázat a rendelkezésre álló lehetőségeket tartalmazza:

Type     | Leírás
---------|------------
Szó  | Ezzel a beállítással megakadályozhatja, hogy a legtöbb karakter speciális jelentést adjon a [literális értékük](cdn-verizon-premium-rules-engine-reference.md#literal-values)használatával.
Helyettesítő | Ezzel a beállítással kihasználhatja az összes [helyettesítő karaktert] ([helyettesítő](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)karakter).
Regex    | Válassza ezt a lehetőséget a [reguláris kifejezések](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)használatához. A reguláris kifejezések hasznosak a karakterek mintázatának definiálásához.

#### <a name="wurfl-capabilities"></a>WURFL-képességek

A WURFL-képesség olyan kategóriára hivatkozik, amely leírja a mobileszközök listáját. A kiválasztott képesség meghatározza a mobileszköz-Leírás típusát, amely a kérelmek azonosítására szolgál.

A következő táblázat felsorolja a WURFL-képességeket és azok változóit a szabályok motorja számára.

> [!NOTE]
> A következő változók támogatottak az **ügyfél-kérelem módosítása fejlécben** , és módosíthatják az **ügyfél válaszának fejlécének** funkcióit.

Képesség | Változó | Leírás | Mintavételezési értékek
-----------|----------|-------------|----------------
Márkanév | %{wurfl_cap_brand_name} | Egy karakterlánc, amely az eszköz márkáját jelzi. | Samsung
Eszköz operációs rendszere | %{wurfl_cap_device_os} | Egy karakterlánc, amely az eszközre telepített operációs rendszert jelzi. | IOS
Eszköz operációs rendszerének verziója | %{wurfl_cap_device_os_version} | Egy karakterlánc, amely az eszközön telepített operációs rendszer verziószámát jelzi. | 1.0.1
Kettős tájolás | %{wurfl_cap_dual_orientation} | Logikai érték, amely azt jelzi, hogy az eszköz támogatja-e a kettős tájolást. | true
HTML elsődleges DTD | %{wurfl_cap_html_preferred_dtd} | Egy karakterlánc, amely megadja a mobileszköz előnyben részesített dokumentumtípus-definícióját (DTD) a HTML-tartalomhoz. | nincs<br/>xhtml_basic<br/>HTML5
Rendszerkép inbélése | %{wurfl_cap_image_inlining} | Logikai érték, amely azt jelzi, hogy az eszköz támogatja-e a Base64 kódolású lemezképeket. | false
Is Android | %{wurfl_vcap_is_android} | Logikai érték, amely azt jelzi, hogy az eszköz használja-e az Android operációs rendszert. | true
IOS | %{wurfl_vcap_is_ios} | Egy logikai érték, amely azt jelzi, hogy az eszköz használ-e iOS-t. | false
Intelligens TV | %{wurfl_cap_is_smarttv} | Logikai érték, amely azt jelzi, hogy az eszköz intelligens TV-e. | false
Okostelefon | %{wurfl_vcap_is_smartphone} | Logikai érték, amely azt jelzi, hogy az eszköz okostelefon-e. | true
Tabletta | %{wurfl_cap_is_tablet} | Logikai érték, amely azt jelzi, hogy az eszköz táblaszámítógép-e. Ez a leírás az operációs rendszertől független. | true
Vezeték nélküli eszköz | %{wurfl_cap_is_wireless_device} | Egy logikai érték, amely jelzi, hogy az eszköz vezeték nélküli eszköznek minősül-e. | true
Marketing neve | %{wurfl_cap_marketing_name} | Egy karakterlánc, amely az eszköz marketing-nevét jelzi. | BlackBerry 8100 Pearl
Mobil böngésző | %{wurfl_cap_mobile_browser} | Egy karakterlánc, amely az eszköz tartalmának kéréséhez használt böngészőt jelzi. | Chrome
Mobil böngésző verziója | %{wurfl_cap_mobile_browser_version} | Egy karakterlánc, amely az eszköz tartalmának kéréséhez használt böngésző verzióját jelzi. | 31
Modell neve | %{wurfl_cap_model_name} | Egy karakterlánc, amely az eszköz modellje nevét jelzi. | s3
Progresszív letöltés | %{wurfl_cap_progressive_download} | Logikai érték, amely azt jelzi, hogy az eszköz támogatja-e a hang-és videó lejátszását, miközben továbbra is le van töltve. | true
Kiadás dátuma | %{wurfl_cap_release_date} | Egy karakterlánc, amely azt az évet és hónapot jelzi, amikor az eszköz hozzá lett adva a WURFL-adatbázishoz.<br/><br/>Formátum: `yyyy_mm` | 2013_december
Felbontás magassága | %{wurfl_cap_resolution_height} | Egy egész szám, amely jelzi az eszköz magasságát képpontban megadva. | 768
Felbontás szélessége | %{wurfl_cap_resolution_width} | Egész szám, amely megadja az eszköz szélességét képpontban megadva. | 1024

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="edge-cname"></a>Edge CNAME

Legfontosabb információk:

- Az elérhető Edge-CNAME rekordok listája azokra a peremhálózati CNAME-re korlátozódik, amelyek a szabályok motorjának konfigurálására szolgáló platform peremhálózati CNAME-lapján vannak konfigurálva.
- A peremhálózati CNAME konfiguráció törlésének megkísérlése előtt győződjön meg arról, hogy a peremhálózati CNAME-egyeztetési feltétel nem hivatkozik rá. A szabályban definiált Edge CNAME konfigurációk nem törölhetők a peremhálózati CNAME-lapok lapról.
- Bizonyos egyezési feltételek összevonásához ne használjon és IF utasítást. Ha például egy Edge CNAME egyeztetési feltételt társít egy ügyfél-Origó egyeztetési feltétellel, akkor egy olyan egyezési mintát hoz létre, amely soha nem egyeztethető össze. Emiatt a két peremhálózati CNAME egyeztetési feltétel nem kombinálható az AND IF utasítással.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez a megfeleltetési feltétel nem kompatibilis a következő funkciókkal:
  - Gyorsítótár kitöltésének befejezése
  - Alapértelmezett belső max. Age
  - A belső Max-Age kényszerítése
  - Kihagyott forrás – gyorsítótár
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="referring-domain"></a>Hivatkozó tartomány

Annak a hivatkozó állomásnévnek a neve, amelyen keresztül a kért tartalom szerepel, meghatározza, hogy teljesül-e a hivatkozó tartomány feltétele.

A **egyezések**/**nem egyeznek** beállítás határozza meg, hogy teljesülnek-e a hivatkozó tartomány egyeztetési feltétele:

- **Egyezések**: A hivatkozó állomásnév megadását igényli a megadott értékeknek megfelelően. 
- **Nem egyezik**: Megköveteli, hogy a hivatkozó állomásnév ne egyezzen meg a megadott értékkel.

Legfontosabb információk:

- Több állomásnevet is megadhat, ha mindegyiket egyetlen szóközzel kell elválasztani.
- Ez a megfeleltetési feltétel támogatja a [helyettesítő karaktereket](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
- Ha a megadott érték nem tartalmaz csillagot, akkor pontosan egyeznie kell a hivatkozó gazdagép nevével. Például a "mydomain.com" megadása nem egyezik meg a "www.mydomain.com" értékkel.
- A kis-és nagybetűk **figyelmen kívül hagyása** beállítással szabályozhatja, hogy a rendszer a kis-és nagybetűk megkülönböztetését
- A gyorsítótár-beállítások nyomon követésének módja miatt ez a megfeleltetési feltétel nem kompatibilis a következő funkciókkal:
  - Gyorsítótár kitöltésének befejezése
  - Alapértelmezett belső max. Age
  - A belső Max-Age kényszerítése
  - Kihagyott forrás – gyorsítótár
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-literal"></a>Kérelem fejléce – literál

A **egyezések**/**nem egyeznek** beállítás határozza meg azokat a feltételeket, amelyekben a kérelem fejlécének konstans egyeztetési feltétele teljesül.

- **Egyezések**: A kérelemnek a megadott fejlécet kell tartalmaznia. Az értéknek meg kell egyeznie az ebben az egyeztetési feltételben definiált értékkel.
- **Nem egyezik**: Megköveteli, hogy a kérelem megfeleljen a következő feltételek egyikének:
  - Nem tartalmazza a megadott fejlécet.
  - Tartalmazza a megadott fejlécet, de az értéke nem egyezik az ebben az egyeztetési feltételben definiált értékkel.
  
Legfontosabb információk:

- A fejléc neve összehasonlítások mindig kis-és nagybetűket érintenek. Az **eset mellőzése** beállítással szabályozhatja a fejléc értékének összehasonlítását.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez a megfeleltetési feltétel nem kompatibilis a következő funkciókkal:
  - Gyorsítótár kitöltésének befejezése
  - Alapértelmezett belső max. Age
  - A belső Max-Age kényszerítése
  - Kihagyott forrás – gyorsítótár
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---  

### <a name="request-header-regex"></a>Kérelem fejlécének Regexje

A **egyezések**/**nem egyeznek** beállítás határozza meg, hogy a rendszer milyen feltételek teljesülése esetén teljesíti a kérelem fejlécének regex-egyeztetési feltételét.

- **Egyezések**: A kérelemnek a megadott fejlécet kell tartalmaznia. Az értéknek meg kell egyeznie a megadott [reguláris kifejezésben](cdn-verizon-premium-rules-engine-reference.md#regular-expressions)definiált mintával.
- **Nem egyezik**: Megköveteli, hogy a kérelem megfeleljen a következő feltételek egyikének:
  - Nem tartalmazza a megadott fejlécet.
  - Tartalmazza a megadott fejlécet, de az értéke nem felel meg a megadott reguláris kifejezésnek.

Legfontosabb információk:

- Fejléc neve:
  - A fejléc nevének összehasonlítása a kis-és nagybetűk megkülönböztetése nélkül történik.
  - A fejlécben lévő szóközöket cserélje le a következőre: "% 20".
- Fejléc értéke:
  - A fejléc értéke kihasználhatja a reguláris kifejezések előnyeit.
  - Az **eset mellőzése** beállítással szabályozhatja a fejléc értékének összehasonlítását.
  - Az egyeztetési feltétel csak akkor teljesül, ha egy fejléc értéke pontosan megegyezik a megadott minták legalább egyikével.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez a megfeleltetési feltétel nem kompatibilis a következő funkciókkal:
  - Gyorsítótár kitöltésének befejezése
  - Alapértelmezett belső max. Age
  - A belső Max-Age kényszerítése
  - Kihagyott forrás – gyorsítótár
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-header-wildcard"></a>Kérelem fejlécének helyettesítő karaktere

A **egyezések**/**nem egyeznek** beállítás határozza meg, hogy a kérelem fejléce milyen feltételek teljesülése esetén teljesül.

- **Egyezések**: A kérelemnek a megadott fejlécet kell tartalmaznia. Az értéknek meg kell egyeznie a jelen egyeztetési feltételben definiált értékek közül legalább az egyikkel.
- **Nem egyezik**: Megköveteli, hogy a kérelem megfeleljen a következő feltételek egyikének:
  - Nem tartalmazza a megadott fejlécet.
  - Tartalmazza a megadott fejlécet, de az értéke nem felel meg a megadott értékek egyikének sem.
  
Legfontosabb információk:

- Fejléc neve:
  - A fejléc nevének összehasonlítása a kis-és nagybetűk megkülönböztetése nélkül történik.
  - A fejlécben lévő szóközöket a (z) "% 20" kifejezéssel kell helyettesíteni. Ennek az értéknek a használatával is megadhat szóközt a fejléc értékében.
- Fejléc értéke:
  - A fejléc értéke kihasználhatja a [helyettesítő karakteres értékeket](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).
  - Az **eset mellőzése** beállítással szabályozhatja a fejléc értékének összehasonlítását.
  - Ez a megfeleltetési feltétel akkor teljesül, ha egy fejléc értéke pontosan megegyezik a megadott minták legalább egyikével.
  - Több értéket is megadhat, ha mindegyiket egyetlen szóközzel kell korlátozni.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez a megfeleltetési feltétel nem kompatibilis a következő funkciókkal:
  - Gyorsítótár kitöltésének befejezése
  - Alapértelmezett belső max. Age
  - A belső Max-Age kényszerítése
  - Kihagyott forrás – gyorsítótár
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-method"></a>Kérelem metódusa

A kérelem metódusának egyeztetési feltétele csak akkor teljesül, ha az eszközöket a kiválasztott kérési módszer alapján kérik. Az elérhető kérelmek módszerei a következők:

- GET
- HEAD
- POST
- BEÁLLÍTÁSOK
- PUT
- DELETE
- NYOMKÖVETÉSI
- CSATLAKOZNI

Legfontosabb információk:

- Alapértelmezés szerint csak a GET Request metódus tud gyorsítótárazott tartalmat készíteni a hálózaton. Minden más kérelmezési módszer a hálózaton keresztül történik.
- A gyorsítótár-beállítások nyomon követésének módja miatt ez a megfeleltetési feltétel nem kompatibilis a következő funkciókkal:
  - Gyorsítótár kitöltésének befejezése
  - Alapértelmezett belső max. Age
  - A belső Max-Age kényszerítése
  - Kihagyott forrás – gyorsítótár
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="request-scheme"></a>Kérési séma

A kérési séma egyeztetési feltétele csak akkor teljesül, ha az eszközöket a kiválasztott protokollon keresztül kérik. A rendelkezésre álló protokollok a következők:

- HTTP
- HTTPS

Legfontosabb információk:

- A gyorsítótár-beállítások nyomon követésének módja miatt ez a megfeleltetési feltétel nem kompatibilis a következő funkciókkal:
  - Gyorsítótár kitöltésének befejezése
  - Alapértelmezett belső max. Age
  - A belső Max-Age kényszerítése
  - Kihagyott forrás – gyorsítótár
  - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-directory"></a>URL elérési útja könyvtár

A kérést a relatív elérési úttal azonosítja, amely kizárja a kért eszköz fájlnevét.

A **egyezések**/**nem egyeznek** beállítás határozza meg, hogy a rendszer milyen feltételek teljesülése esetén teljesíti az URL-cím elérési útját.

- **Egyezések**: A kérésnek tartalmaznia kell egy relatív URL elérési utat, kivéve a fájlnevet, amely megfelel a megadott URL-mintának.
- **Nem egyezik**: A kérésnek tartalmaznia kell egy relatív URL elérési utat, kivéve a fájlnevet, amely nem felel meg a megadott URL-mintának.

Legfontosabb információk:

- A **relatív** beállítás megadásával adhatja meg, hogy az URL-cím összehasonlítása a tartalom-hozzáférési pont előtt vagy után kezdődik-e. A tartalom-hozzáférési pont az elérési út azon része, amely megjelenik a Verizon CDN-állomásnév és a kért eszköz relatív elérési útja között (például/800001/CustomerOrigin). Meghatározza a helyet a kiszolgáló típusa szerint (például CDN vagy Customer Origin) és az ügyfél fiókjának számát.

   A következő értékek érhetők el a **relatív** beállításhoz:
  - **Gyökér**: Azt jelzi, hogy az URL-összehasonlító pont közvetlenül a CDN-állomásnév után kezdődik. 

  Például: http:\//WPC.0001.&lt; tartomány&gt;800001/ **/myorigin/MyFolder**/index.htm

  - **Forrás**: Azt jelzi, hogy az URL-összehasonlító pont a tartalom-hozzáférési pont után kezdődik (például/000001 vagy/800001/myorigin). Mivel a \*. azureedge.net CNAME azonosító alapértelmezés szerint a Verizon CDN-gazdagépen lévő forrás könyvtárhoz képest jön létre, Azure CDN felhasználóknak a **forrás** értéket kell használniuk. 

  Például\/: https:/&lt;Endpoint&gt;. azureedge.net/**MyFolder**/index.htm 

  Ez az URL-cím a következő Verizon CDN állomásnévre mutat\/:&lt; http:/WPC.0001. tartomány&gt;/800001/myorigin/**MyFolder**/index.htm

- A peremhálózati CNAME URL-címet az URL-cím összehasonlítását megelőzően a CDN URL-re írja a rendszer.

    Például a következő URL-címek mindegyike ugyanarra az objektumra mutat, ezért ugyanaz az URL-cím elérési útja.
  - CDN URL-cím:\/http&lt; :/WPC.0001. tartomány&gt;/800001/CustomerOrigin/Path/Asset.htm
    
  - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    További információ:
  - Egyéni tartomány: https:\//My.domain.com/Path/Asset.htm
    
    - URL elérési útja (a gyökérhez képest):/800001/CustomerOrigin/path/
    
    - URL-cím elérési útja (a forráshoz viszonyítva):/Path/

- Az URL-cím összehasonlításához használt URL-cím a kért eszköz fájlneve előtt ér véget. A záró perjel az utolsó karakter az ilyen típusú elérési úton.

- A (z) "% 20" értékkel helyettesítse az URL-cím elérési útjában lévő szóközöket.

- Minden URL-cím elérési útja tartalmazhat egy vagy több csillagot (*), ahol minden csillag egy vagy több karakterből áll.

- Több URL-útvonalat is megadhat a mintában úgy, hogy mindegyiket egyetlen szóközzel korlátozza.

    Például: */Sales/*/marketing/

- Egy URL-elérésiút-specifikáció kihasználhatja a [helyettesítő karakteres értékeket](cdn-verizon-premium-rules-engine-reference.md#wildcard-values).

- A **kis** -és nagybetűk megkülönböztetése beállítás használatával szabályozhatja, hogy a rendszer a kis-és nagybetűket megkülönböztető összehasonlítást

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-extension"></a>URL-elérési út kiterjesztése

A kért eszköz fájlkiterjesztés alapján azonosítja a kérelmeket.

A **egyezések**/**nem egyeznek** beállítás határozza meg azokat a feltételeket, amelyekben az URL-cím elérési útjának egyeztetési állapota teljesül.

- **Egyezések**: A kérelem URL-címének megadásához olyan fájlkiterjesztés szükséges, amely pontosan megfelel a megadott mintának.

   Ha például megadja a "htm", a "htm" objektumokat, de nem a "HTML" típusú eszközöket.  

- **Nem egyezik**: Az URL-címre vonatkozó kérelem olyan fájlkiterjesztés használatát igényli, amely nem felel meg a megadott mintának.

Legfontosabb információk:

- A **Value (érték** ) mezőben válassza ki, hogy mely fájlkiterjesztések egyeznek meg. Ne tartalmazzon bevezető időszakot; a. htm helyett például a htm-t használja.

- A **kis** -és nagybetűk megkülönböztetése beállítás használatával szabályozhatja, hogy a rendszer a kis-és nagybetűket megkülönböztető összehasonlítást

- Több fájlkiterjesztést is megadhat az egyes bővítmények egyetlen szóközzel való korlátozásával. 

    Például: htm html

- Például a "htm" kifejezés megadásával egyezik a "htm" objektumokkal, de a "HTML" nem.

#### <a name="sample-scenario"></a>Példa a forgatókönyvre

A következő minta-konfiguráció feltételezi, hogy ez a megfeleltetési feltétel teljesül, ha egy kérelem megfelel a megadott kiterjesztések valamelyikének.

Érték meghatározása: ASP aspx php HTML

Ez a megfelelési feltétel akkor teljesül, ha a következő kiterjesztésű URL-címeket keresi:

- . asp
- .aspx
- .php
- . html

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-filename"></a>URL elérési útja fájlnév

A kért objektum fájlneve alapján azonosítja a kérelmeket. Ennek a megfelelési feltételnek az alkalmazásában a fájl neve a kért objektum neve, egy pont és a fájlkiterjesztés (például index. html).

A **egyezések**/**nem egyeznek** beállítás határozza meg, hogy a rendszer milyen feltételek teljesülése esetén teljesíti az URL-cím elérési útjának megfeleltetési állapotát.

- **Egyezések**: A kérésnek tartalmaznia kell egy fájlnevet az URL-címében, amely megfelel a megadott mintának.
- **Nem egyezik**: A kérésnek tartalmaznia kell egy fájlnevet az URL-címében, amely nem felel meg a megadott mintának.

Legfontosabb információk:

- A **kis** -és nagybetűk megkülönböztetése beállítás használatával szabályozhatja, hogy a rendszer a kis-és nagybetűket megkülönböztető összehasonlítást

- Több fájlkiterjesztés megadásához az egyes bővítményeket egyetlen szóközzel válassza el.

    Például: index. htm index. html

- A fájlnév értékében lévő szóközöket cserélje le a következőre: "% 20".

- A fájlnév érték kihasználhatja a [helyettesítő karakteres értékeket](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Például az egyes fájlnevek mintázata egy vagy több csillagból (*) állhat, ahol minden csillag egy vagy több karakterből áll.

- Ha a helyettesítő karakterek nincsenek megadva, akkor csak a pontos egyezés fogja kielégíteni ezt a megfelelési feltételt.

    Például a "Presentation. ppt" megadásával egyezik egy "Presentation. ppt" nevű objektummal, de nem egy "Presentation. pptx" névvel.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-literal"></a>URL elérési útja literál

Összehasonlítja a kérelem URL-címét, beleértve a fájlnevet a megadott értékre.

A **egyezések**/**nem egyeznek** beállítás határozza meg, hogy a rendszer milyen feltételek teljesülése esetén teljesíti az URL elérési útjának konstans egyezési feltételét.

- **Egyezések**: A kérésnek tartalmaznia kell egy URL-útvonalat, amely megfelel a megadott mintának.
- **Nem egyezik**: A kérelemnek olyan URL-útvonalat kell tartalmaznia, amely nem felel meg a megadott mintának.

Legfontosabb információk:

- A **relatív érték** beállítás megadásával adhatja meg, hogy az URL-cím összehasonlító pontja a tartalom-hozzáférési pont előtt vagy után kezdődik-e. 

    A következő értékek érhetők el a **relatív** beállításhoz:
  - **Gyökér**: Azt jelzi, hogy az URL-összehasonlító pont közvetlenül a CDN-állomásnév után kezdődik.

    Például: http:\//WPC.0001.&lt; tartományi&gt; 800001/myorigin/MyFolder/index.htm/

  - **Forrás**: Azt jelzi, hogy az URL-összehasonlító pont a tartalom-hozzáférési pont után kezdődik (például/000001 vagy/800001/myorigin). Mivel a \*. azureedge.net CNAME azonosító alapértelmezés szerint a Verizon CDN-gazdagépen lévő forrás könyvtárhoz képest jön létre, Azure CDN felhasználóknak a **forrás** értéket kell használniuk. 

    Például\/: https:/&lt;Endpoint&gt;. azureedge.net/**MyFolder/index.htm**

  Ez az URL-cím a következő Verizon CDN állomásnévre mutat\/:&lt; http:/WPC.0001. tartomány&gt;/800001/myorigin/**MyFolder/index.htm**

- A peremhálózati CNAME URL-cím egy URL-cím összehasonlítását megelőzően a CDN URL-re íródik.

Például a következő URL-címek mindegyike ugyanarra az objektumra mutat, ezért ugyanaz az URL-cím elérési útja:

- CDN URL-cím:\/http&lt; :/WPC.0001. tartomány&gt;/800001/CustomerOrigin/Path/Asset.htm
- Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm

    További információ:
    
    - URL elérési útja (a gyökérhez képest):/800001/CustomerOrigin/path/asset.htm
   
    - URL-cím elérési útja (a forráshoz képest):/path/asset.htm

- Az URL-címben szereplő lekérdezési karakterláncok figyelmen kívül lesznek hagyva.
- A **kis** -és nagybetűk megkülönböztetése beállítás használatával szabályozhatja, hogy a rendszer a kis-és nagybetűket megkülönböztető összehasonlítást
- Az ehhez a megfeleltetési feltételhez megadott értéket a rendszer összehasonlítja az ügyfél által végzett pontos kérelem relatív elérési útjával.

- Egy adott címtárra irányuló összes kérelem megfeleltetéséhez használja az [URL-cím elérési útja](#url-path-directory) vagy az [URL-cím elérési útja helyettesítő](#url-path-wildcard) feltételt.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-regex"></a>URL-cím elérési útja – regex

Összehasonlítja a kérelem URL-elérési útját a megadott [reguláris kifejezéssel](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

A **egyezések**/**nem egyeznek** beállítás határozza meg azokat a feltételeket, amelyekben az URL-cím elérési útja megfelel a feltételnek.

- **Egyezések**: A kérésnek tartalmaznia kell egy URL-útvonalat, amely megfelel a megadott reguláris kifejezésnek.
- **Nem egyezik**: A kérelemnek olyan URL-útvonalat kell tartalmaznia, amely nem felel meg a megadott reguláris kifejezésnek.

Legfontosabb információk:

- A peremhálózati CNAME URL-cím az URL-cím összehasonlítását megelőzően a CDN URL-re íródik.

    Például mindkét URL-cím ugyanarra az objektumra mutat, ezért azonos az URL-cím elérési útja.

     - CDN URL-cím:\/http&lt; :/WPC.0001. tartomány&gt;/800001/CustomerOrigin/Path/Asset.htm

     - Edge CNAME URL: http:\//my.domain.com/path/asset.htm

    További információ:
    
     - URL-cím elérési útja:/800001/CustomerOrigin/path/asset.htm

- Az URL-címben szereplő lekérdezési karakterláncok figyelmen kívül lesznek hagyva.
    
- A **kis** -és nagybetűk megkülönböztetése beállítás használatával szabályozhatja, hogy a rendszer a kis-és nagybetűket megkülönböztető összehasonlítást
    
- Az URL-cím elérési útján lévő szóközöket a következőre kell cserélni: "% 20".

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-path-wildcard"></a>URL elérési útja helyettesítő karakter

Összehasonlítja a kérelem relatív URL-elérési útját a megadott helyettesítő típussal.

A **egyezések**/**nem egyeznek** beállítás határozza meg, hogy a rendszer milyen feltételek teljesülése esetén teljesíti az URL-cím elérési útjának egyezését.

- **Egyezések**: A kérésnek tartalmaznia kell egy URL-útvonalat, amely megfelel a megadott helyettesítő mintának.
- **Nem egyezik**: A kérelem olyan URL-útvonalat tartalmaz, amely nem felel meg a megadott helyettesítő mintának.

Legfontosabb információk:

- **A** következőhöz képest: Ez a beállítás határozza meg, hogy az URL-cím összehasonlító pontja a tartalom-hozzáférési pont előtt vagy után kezdődik-e.

   Ez a beállítás a következő értékeket veheti fel:
     - **Gyökér**: Azt jelzi, hogy az URL-összehasonlító pont közvetlenül a CDN-állomásnév után kezdődik.

       Például: http:\//WPC.0001.&lt; tartományi&gt; 800001/myorigin/MyFolder/index.htm/

     - **Forrás**: Azt jelzi, hogy az URL-összehasonlító pont a tartalom-hozzáférési pont után kezdődik (például/000001 vagy/800001/myorigin). Mivel a \*. azureedge.net CNAME azonosító alapértelmezés szerint a Verizon CDN-gazdagépen lévő forrás könyvtárhoz képest jön létre, Azure CDN felhasználóknak a **forrás** értéket kell használniuk. 

       Például\/: https:/&lt;Endpoint&gt;. azureedge.net/**MyFolder/index.htm**

     Ez az URL-cím a következő Verizon CDN állomásnévre mutat\/:&lt; http:/WPC.0001. tartomány&gt;/800001/myorigin/**MyFolder/index.htm**

- A peremhálózati CNAME URL-cím az URL-cím összehasonlítását megelőzően a CDN URL-re íródik.

    Például a következő URL-címek mindegyike ugyanarra az objektumra mutat, ezért ugyanaz az URL-cím elérési útja:
     - CDN URL- http://wpc.0001.&lt címe:&gt;;d omain/800001/CustomerOrigin/Path/Asset.htm
     - Edge CNAME URL: http:\//&lt;endpoint&gt;.azureedge.net/path/asset.htm
    
    További információ:
    
     - URL elérési útja (a gyökérhez képest):/800001/CustomerOrigin/path/asset.htm
    
     - URL-cím elérési útja (a forráshoz képest):/path/asset.htm
    
- Több URL-útvonalat is megadhat, ha mindegyiket egyetlen szóközzel kell elválasztani.

   Például:/marketing/Asset. */sales/*. htm

- Az URL-címben szereplő lekérdezési karakterláncok figyelmen kívül lesznek hagyva.
    
- A **kis** -és nagybetűk megkülönböztetése beállítás használatával szabályozhatja, hogy a rendszer a kis-és nagybetűket megkülönböztető összehasonlítást
    
- Az URL-címben lévő szóközöket cserélje le a következőre: "% 20".
    
- Az URL-cím elérési útjának megadott értéke kihasználhatja a [helyettesítő karakteres értékeket](cdn-verizon-premium-rules-engine-reference.md#wildcard-values). Minden URL-cím elérési útja tartalmazhat egy vagy több csillagot (*), ahol minden csillag egy vagy több karakterből állhat.

#### <a name="sample-scenarios"></a>Példák a forgatókönyvekre

A következő táblázatban szereplő példák azt feltételezik, hogy ez a megfeleltetési feltétel teljesül, ha egy kérelem megfelel a megadott URL-mintának:

Value                   | A következőhöz képest    | Eredmény 
------------------------|----------------|-------
*/test.html */test.php  | Gyökér vagy forrás | Ezt a mintát egy "test. html" vagy "test. php" nevű eszközre vonatkozó kérések egyeznek meg bármely mappában.
/80ABCD/origin/text/*   | Gyökér           | Ez a minta akkor egyezik, ha a kért eszköz megfelel a következő feltételeknek: <br />– A "forrás" nevű ügyfél-forrásnak kell lennie. <br />– A relatív elérési útnak a "text" nevű mappával kell kezdődnie. Ez azt eredményezi, hogy a kért eszköz a "text" mappában vagy egy rekurzív almappájában található.
*/CSS/* */js/*          | Gyökér vagy forrás | Ezt a mintát minden olyan CDN vagy Edge CNAME URL-cím megegyeznek, amely egy CSS vagy js mappát tartalmaz.
*.jpg *.gif *.png       | Gyökér vagy forrás | Ezt a mintát a. jpg,. gif vagy. png végződésű összes CDN vagy Edge CNAME URL-cím egyezteti. A minta megadásának másik módja az [URL-cím elérési útjának egyeztetési](#url-path-extension)feltétele.
/images/* /media/*      | Forrás         | Ezt a mintát a CDN vagy az Edge CNAME URL-címei egyeznek meg, amelyek relatív elérési útja "images" vagy "Media" (lemezképek) vagy "média" mappa. <br />– CDN URL-cím:\/http&lt; :/WPC.0001. tartományi&gt;/800001/myorigin/images/Sales/event1.png<br />– Minta Edge CNAME URL-cím:\/http:/CDN.mydomain.com/images/Sales/event1.png

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-literal"></a>URL-lekérdezési literál

Összehasonlítja a kérelem lekérdezési karakterláncát a megadott értékkel.

A **egyezések**/**nem egyeznek** beállítás határozza meg, hogy a rendszer milyen feltételek teljesülése esetén teljesíti az URL-lekérdezési konstans egyeztetési feltételt.

- **Egyezések**: A kérésnek tartalmaznia kell egy URL-lekérdezési karakterláncot, amely megfelel a megadott lekérdezési karakterláncnak.
- **Nem egyezik**: A kérésnek tartalmaznia kell egy URL-lekérdezési karakterláncot, amely nem felel meg a megadott lekérdezési karakterláncnak.

Legfontosabb információk:

- Csak a pontos lekérdezési karakterlánc felel meg a megfelelési feltételnek.
    
- A kis-és **nagybetűk figyelmen kívül hagyása** lehetőséggel szabályozhatja a lekérdezési karakterláncok összehasonlítását.
    
- Ne tartalmazzon kezdő kérdőjelet (?) a lekérdezési karakterlánc értéke szövegben.
    
- Bizonyos karakterek URL-kódolást igényelnek. A százalékos szimbólum használata a következő karakterek URL-kódolásához:

   Karakter | URL-kódolás
   ----------|---------
   Szóköz     | %20
   &         | %25

- A gyorsítótár-beállítások nyomon követésének módja miatt ez a megfeleltetési feltétel nem kompatibilis a következő funkciókkal:
   - Gyorsítótár kitöltésének befejezése
   - Alapértelmezett belső max. Age
   - A belső Max-Age kényszerítése
   - Kihagyott forrás – gyorsítótár
   - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-parameter"></a>URL-lekérdezési paraméter

Azokat a kérelmeket azonosítja, amelyek tartalmazzák a megadott lekérdezési karakterlánc paramétert. Ez a paraméter olyan értékre van beállítva, amely megfelel egy megadott mintának. A kérelem URL-címében szereplő lekérdezési karakterlánc paraméterei (például paraméter = érték) határozzák meg, hogy ez a feltétel teljesül-e. Ez a megfeleltetési feltétel a lekérdezési karakterlánc paraméterét azonosítja a nevével, és egy vagy több értéket fogad el a paraméter értékeként. 

A **egyezések**/**nem egyeznek** beállítás határozza meg, hogy az URL-lekérdezési paraméter megfelel-e a feltételnek.

- **Egyezések**: A megadott paramétert olyan értékkel kell tartalmaznia, amely megfelel a jelen egyeztetési feltételben definiált értékek közül legalább az egyiknek.
- **Nem egyezik**: Megköveteli, hogy a kérelem megfeleljen a következő feltételek egyikének:
  - Nem tartalmazza a megadott paramétert.
  - Tartalmazza a megadott paramétert, de az értéke nem felel meg a jelen egyeztetési feltételben definiált értékeknek.

Ez a megfeleltetési feltétel egyszerű módszert kínál a paraméterek nevének és értékének kombinációinak megadására. Ha a lekérdezési karakterlánc paraméterének megfeleltetése nagyobb rugalmasságot biztosít, érdemes lehet használni az [URL-lekérdezés helyettesítő karakteres](#url-query-wildcard) egyeztetési feltételét.

Legfontosabb információk:

- Ennek a egyeztetési feltételnek a példányain csak egyetlen URL-lekérdezési paraméter neve adható meg.
    
- Mivel a helyettesítő karakterek használata nem támogatott, ha a paraméter neve meg van adva, csak a pontos paraméter neve egyezik az összehasonlításhoz.
- A paraméter értéke (i) [helyettesítő karakteres értékeket](cdn-verizon-premium-rules-engine-reference.md#wildcard-values)tartalmazhat.
   - Minden paraméter értékének mintája egy vagy több csillag (*) lehet, ahol minden csillag egy vagy több karakterből állhat.
   - Bizonyos karakterek URL-kódolást igényelnek. A százalékos szimbólum használata a következő karakterek URL-kódolásához:

       Karakter | URL-kódolás
       ----------|---------
       Szóköz     | %20
       &         | %25

- Több lekérdezési karakterlánc paraméter értékének megadásával mindegyiket egyetlen szóközzel kell megadnia. Ez a megfeleltetési feltétel akkor teljesül, ha egy kérelem a megadott név/érték kombinációk egyikét tartalmazza.

   - 1\. példa:

     - Konfigurálás:

       Valuea ValueB

     - Ez a konfiguráció a következő lekérdezési karakterlánc-paraméterekkel egyezik:

       Parameter1=ValueA
    
       Parameter1 = ValueB

   - 2\. példa

     - Konfigurálás: 

        % 20A érték% 20B

     - Ez a konfiguráció a következő lekérdezési karakterlánc-paraméterekkel egyezik:

       Parameter1=Value%20A

       Parameter1 = érték% 20B

- Ez a megfeleltetési feltétel csak akkor teljesül, ha a lekérdezési karakterlánc megadott neve/értéke legalább egyike pontosan egyezik.

   Ha például az előző példában szereplő konfigurációt használja, a "Parameter1 = ValueAdd" paraméter neve/értéke kombináció nem tekintendő egyezésnek. Ha azonban a következő értékek egyikét adja meg, akkor az megfelel a név/érték kombinációnak:

   - Valuea ValueB ValueAdd
   - Valuea * ValueB

- A kis-és **nagybetűk figyelmen kívül hagyása** lehetőséggel szabályozhatja a lekérdezési karakterláncok összehasonlítását.
    
- A gyorsítótár-beállítások nyomon követésének módja miatt ez a megfeleltetési feltétel nem kompatibilis a következő funkciókkal:
   - Gyorsítótár kitöltésének befejezése
   - Alapértelmezett belső max. Age
   - A belső Max-Age kényszerítése
   - Kihagyott forrás – gyorsítótár
   - Belső maximális – elavult

#### <a name="sample-scenarios"></a>Használati példák

Az alábbi példa bemutatja, hogyan működik ez a beállítás bizonyos helyzetekben:

Name (Név)  | Value |  Eredmény
------|-------|--------
Felhasználó  | Joe   | Ez a minta akkor egyezik, ha a kért URL-cím lekérdezési karakterlánca "? user = Joe".
Felhasználó  | *     | Ez a minta akkor egyezik, ha a kért URL-cím lekérdezési karakterlánca felhasználói paramétert tartalmaz.
Email | Joe\* | Ez a minta akkor egyezik, ha a kért URL-cím lekérdezési karakterlánca egy olyan e-mail-paramétert tartalmaz, amely "Joe" karakterlánccal kezdődik.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-regex"></a>URL-lekérdezés regex

Azokat a kérelmeket azonosítja, amelyek tartalmazzák a megadott lekérdezési karakterlánc paramétert. Ez a paraméter olyan értékre van beállítva, amely megfelel egy megadott [reguláris kifejezésnek](cdn-verizon-premium-rules-engine-reference.md#regular-expressions).

A **egyezések**/**nem egyeznek** beállítás határozza meg azokat a feltételeket, amelyekben az URL-lekérdezés regex-egyeztetési feltétele teljesül.

- **Egyezések**: A kérésnek tartalmaznia kell egy URL-lekérdezési karakterláncot, amely megfelel a megadott reguláris kifejezésnek.
- **Nem egyezik**: A kérésnek tartalmaznia kell egy URL-lekérdezési karakterláncot, amely nem felel meg a megadott reguláris kifejezésnek.

Legfontosabb információk:

- Csak a megadott reguláris kifejezés pontos egyezése megfelel ennek a megfelelési feltételnek.
    
- A kis-és **nagybetűk figyelmen kívül hagyása** lehetőséggel szabályozhatja a lekérdezési karakterláncok összehasonlítását.
    
- Ebben a beállításban egy lekérdezési sztring az első karakterrel kezdődik a lekérdezési karakterlánchoz tartozó kérdőjel (?) határolójel után.
    
- Bizonyos karakterek URL-kódolást igényelnek. A százalékos szimbólum használata a következő karakterek URL-kódolásához:

   Karakter | URL-kódolás | Value
   ----------|--------------|------
   Szóköz     | %20          | \%20
   &         | %25          | \%25

   Vegye figyelembe, hogy a százalékos szimbólumokat el kell menekülni.

- Dupla Escape speciális reguláris kifejezésű karakterek (például \^$. +), ha egy fordított perjelet szeretne felvenni a reguláris kifejezésbe.

   Példa:

   Value | Értelmezés 
   ------|---------------
   \\+    | +
   \\\\+   | \\+

- A gyorsítótár-beállítások nyomon követésének módja miatt ez a megfeleltetési feltétel nem kompatibilis a következő funkciókkal:
   - Gyorsítótár kitöltésének befejezése
   - Alapértelmezett belső max. Age
   - A belső Max-Age kényszerítése
   - Kihagyott forrás – gyorsítótár
   - Belső maximális – elavult

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

---

### <a name="url-query-wildcard"></a>URL-lekérdezés helyettesítő karaktere

A megadott érték (eke) t hasonlítja össze a kérelem lekérdezési karakterláncával.

A **egyezések**/**nem egyeznek** beállítás határozza meg, hogy a rendszer milyen feltételek teljesülése esetén teljesíti az URL-lekérdezés helyettesítő karakterét.

- **Egyezések**: A kérésnek tartalmaznia kell egy URL-lekérdezési karakterláncot, amely megfelel a megadott helyettesítő értéknek.
- **Nem egyezik**: A kérésnek tartalmaznia kell egy URL-lekérdezési karakterláncot, amely nem felel meg a megadott helyettesítő értéknek.

Legfontosabb információk:

- Ebben a beállításban egy lekérdezési sztring az első karakterrel kezdődik a lekérdezési karakterlánchoz tartozó kérdőjel (?) határolójel után.
- A paraméterek értéke tartalmazhat [helyettesítő karaktereket](cdn-verizon-premium-rules-engine-reference.md#wildcard-values):
   - Minden paraméter értékének mintája egy vagy több csillag (*) lehet, ahol minden csillag egy vagy több karakterből állhat.
   - Bizonyos karakterek URL-kódolást igényelnek. A százalékos szimbólum használata a következő karakterek URL-kódolásához:

     Karakter | URL-kódolás
     ----------|---------
     Szóköz     | %20
     &         | %25

- Több értéket is megadhat, ha mindegyiket egyetlen szóközzel kell korlátozni.

   Példa: *Parameter1=ValueA* *ValueB* *Parameter1=ValueC&Parameter2=ValueD*

- Csak a megadott lekérdezési karakterláncok legalább egyikének pontos egyezése megfelel ennek a megfelelési feltételnek.
    
- A kis-és **nagybetűk figyelmen kívül hagyása** lehetőséggel szabályozhatja a lekérdezési karakterláncok összehasonlítását.
    
- A gyorsítótár-beállítások nyomon követésének módja miatt ez a megfeleltetési feltétel nem kompatibilis a következő funkciókkal:
   - Gyorsítótár kitöltésének befejezése
   - Alapértelmezett belső max. Age
   - A belső Max-Age kényszerítése
   - Kihagyott forrás – gyorsítótár
   - Belső maximális – elavult

#### <a name="sample-scenarios"></a>Használati példák

Az alábbi példa bemutatja, hogyan működik ez a beállítás bizonyos helyzetekben:

 Name (Név)                 | Leírás
 ---------------------|------------
user=joe              | Ez a minta akkor egyezik, ha a kért URL-cím lekérdezési karakterlánca "? user = Joe".
\*felhasználó =\* \*optout =\* | Ez a minta akkor egyezik, ha a CDN URL-lekérdezés vagy a felhasználó vagy a optout paramétert tartalmazza.

[Vissza a tetejére](#reference-for-rules-engine-match-conditions)

</br>

## <a name="next-steps"></a>További lépések

- [Az Azure Content Delivery Network áttekintése](cdn-overview.md)
- [Szabálymotor-referencia](cdn-verizon-premium-rules-engine-reference.md)
- [Szabálymotor feltételes kifejezései](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Szabálymotor funkciói](cdn-verizon-premium-rules-engine-reference-features.md)
- [Az alapértelmezett HTTP-viselkedés felülbírálása a szabályok motor használatával](cdn-verizon-premium-rules-engine.md)
