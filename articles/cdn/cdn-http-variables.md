---
title: Az Azure CDN szabálymotor HTTP változók |} Microsoft Docs
description: HTTP változók lehetővé teszi a HTTP-kérelem-válasz metaadatok lekérése céljából.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2018
ms.author: v-deasim
ms.openlocfilehash: ea7469b1d1c3d1c20beca9b1fb3bef0d4dac9492
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Azure CDN szabálymotor HTTP változói
HTTP-változók adja meg az azt jelenti, amelyen keresztül a HTTP-kérelem-válasz metaadatok kérheti le. A metaadatok majd segítségével dinamikusan módosítható a kérés vagy válasz. A HTTP-változók használata a következő szabályok motor szolgáltatásai korlátozva:

- [Gyorsítótár-kulcs átdolgozás](cdn-rules-engine-reference-features.md#cache-key-rewrite)
- [Ügyfél fejléc módosítása](cdn-rules-engine-reference-features.md#modify-client-request-header)
- [Módosítsa az ügyfél válaszfejléc](cdn-rules-engine-reference-features.md#modify-client-response-header)
- [Az átirányítási URL-címe](cdn-rules-engine-reference-features.md#url-redirect)
- [URL-cím átdolgozás](cdn-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Meghatározások
A következő táblázat ismerteti a támogatott HTTP-változókat. Egy üres értéket adja vissza, ha földrajzi metaadatok (például irányítószámot) nem érhető el egy adott kérelem.


| Name (Név) | Változó | Leírás | Mintaérték |
| ---- | -------- | ----------- | ------------ |
| Az ASN (kérelmező) | a(z) % {geo_asnum} | Azt jelzi, a kérelmező SZÁMOT. <br /><br />**Elavult:** % {virt_dst_asnum}. <br />Ez a változó használata helyett a(z) % {geo_asnum}. Bár ez nem ajánlott a változó használó szabály továbbra is működni fog, frissítse úgy, hogy az új változót használja. | AS15133 |
| Város (kérelmező) | a(z) % {geo_city} | Azt jelzi, hogy az igénylő város. | Los Angeles |
| Kontinensen (kérelmező) | a(z) % {geo_continent} | Azt jelzi, hogy az igénylő kontinensen keresztül annak rövidítése. <br />Érvényes értékek a következők: <br />AF: Afrika<br />AS: Ázsia<br />EU: Európa<br />NA: Észak-Amerika<br />C: Óceánia<br />SA: Dél-Amerika<br /><br />**Elavult:** % {virt_dst_continent}. <ber />Ez a változó használata helyett a(z) % {geo_continent}. <br />Bár ez nem ajánlott a változó használó szabály továbbra is működni fog, frissítse úgy, hogy az új változót használja.| – |
| Cookie-értéket | a(z) % {cookie_Cookie} | A cookie-kulcs a cookie-k kifejezés által azonosított megfelelő értékét adja vissza. | Példa: <br />a(z) % {cookie__utma}<br /><br />A minta értéke:<br />111662281.2.10.1222100123 |
| Ország (kérelmező) | a(z) % {geo_country} | Azt jelzi, hogy az igénylő származási az országhívószám keresztül. <br />**Elavult:** % {virt_dst_country}. <br /><br />Ez a változó használata helyett a(z) % {geo_country}. Bár ez nem ajánlott a változó használó szabály továbbra is működni fog, frissítse úgy, hogy az új változót használja. | USA |
| Kijelölt piaci terület (kérelmező) | a(z) % {geo_dma_code} |Azt jelzi, media piaci a kérelmező által a régió kódja. <br /><br />Ez a mező tulajdonság csak az Amerikai Egyesült Államokból kérelmekkel vonatkozik.| 745 |
| HTTP-kérelem módszer | a(z) % {request_method} | Azt jelzi, hogy a HTTP-kérési metódust. | GET |
| HTTP-állapotkód | a(z) % {status} | Azt jelzi, hogy a HTTP-állapotkód: a válasz. | 200 |
| IP-cím (kérelmező) | a(z) % {virt_dst_addr} | Azt jelzi, hogy az igénylő IP-címet. | 192.168.1.1 |
| Szélesség (kérelmező) | a(z) % {geo_latitude} | Azt jelzi, hogy az igénylő szélesség. | 34.0995 |
| A hosszúság (kérelmező) | a(z) % {geo_longitude} | Azt jelzi, hogy az igénylő hosszúság. | -118.4143 |
| Statisztikai térsége (kérelmező) | a(z) % {geo_metro_code} | Azt jelzi, hogy az igénylő térsége. <br /><br />Ez a mező tulajdonság csak az Amerikai Egyesült Államokból kérelmekkel vonatkozik.<br />| 745 |
| Port (kérelmező) | a(z) % {virt_dst_port} | Azt jelzi, hogy az igénylő rövid élettartamú port. | 55885 |
| Irányítószám (kérelmező) | a(z) % {geo_postal_code} | Azt jelzi, hogy az igénylő irányítószámot. | 90210 |
| Lekérdezési karakterlánc található | a(z) % {is_args} | Ez a változó értéke megfelelően, hogy a kérelem tartalmazza a lekérdezési karakterlánc függően változik.<br /><br />-Lekérdezési található karakterlánc:?<br />-Nincs lekérdezési karakterlánc: NULL értékű | ? |
| Lekérdezési karakterlánc paraméter található | a(z) % {is_amp} | Ez a változó értéke megfelelően, hogy a kérelem tartalmaz legalább egy lekérdezési karakterlánc paraméter függően változik.<br /><br />-Paraméter található: &<br />-Nincsenek paraméterei: NULL értékű | & |
| Lekérdezési karakterlánc paraméter értéke | a(z) % {arg_&lt;paraméter&gt;} | A lekérdezési karakterlánc paraméter által azonosított megfelelő értékét adja vissza a &lt;paraméter&gt; kifejezés. | Példa: <br />a(z) % {arg_language}<br /><br />A minta lekérdezési karakterlánc paraméter: <br />? nyelvi = en<br /><br />Minta értéke: en |
| Lekérdezési karakterlánc értéke | a(z) % {query_string} | Azt jelzi, hogy a teljes lekérdezési karakterláncokra vonatkozó értéket megadva a kérelem URL-címében. |key1 = érték1 & key2 = érték2 & key3 = val3 |
| Hivatkozó tartomány | a(z) % {referring_domain} | Azt jelzi, hogy a tartományt a hivatkozó kérelem fejlécében. | www.google.com |
| A régióban (kérelmező) | a(z) % {geo_region} | Azt jelzi, hogy az igénylő régió (például állam vagy megye) keresztül annak alfanumerikus rövidítése. | CA |
| Kérelem fejléc értéke | a(z) % {http_RequestHeader} | A kérelem fejlécében RequestHeader kifejezés által azonosított megfelelő értékét adja vissza. <br /><br />Ha a kérelem fejlécében nevét (például felhasználói ügynök) kötőjellel tartalmaz, akkor cserélje le a aláhúzás karakterrel (például User_Agent).| Példa: % {http_Connection}<br /><br />A minta értéke: Életben tartási | 
| Kérelem állomás | a(z) % {állomás} | Azt jelzi, hogy a kérelem URL-címében megadva gazda. | www.mydomain.com |
| Kérelem protokoll | a(z) % {request_protocol} | Azt jelzi, hogy a kérelem protokoll. | A HTTP/1.1 |
| Kérelem séma | a(z) % {séma} | Azt jelzi, hogy a kérelem séma. |http |
| Kérelem URI-azonosítója (relatív) | a(z) % {request_uri} | Azt jelzi, hogy a relatív elérési út, beleértve a lekérdezési karakterláncot, a kérelem URI-azonosítója definiálva. | /marketing/foo.js?loggedin=TRUE |
| Kérelem URI-azonosítója (relatív lekérdezési karakterlánc nélkül) | a(z) % {uri} | Azt jelzi, hogy a relatív elérési út a kért tartalomhoz. <br /><br/>Kapcsolatos információkat:<br />– A relatív elérési út nem tartalmazza a lekérdezési karakterlánc.<br />-E relatív elérési út az URL-cím újraírások tükrözi. Egy URL-cím a következő feltételek felülíródik:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Módosítsa úgy a szolgáltatás URL: Ez a szolgáltatás újraírja a kérési URI-ban meghatározott relatív elérési út.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Edge CNAME URL-címe: A kérés ilyen típusú rendszeren, a megfelelő CDN URL-cím. |/800001/corigin/rewrittendir/foo.js |
| Kérés URI-ja | a(z) % {kérelem} | A kérelem ismerteti. <br />Szintaxis: `HTTPMethod RelativePath Protocol` | /Marketing/foo.js?loggedin=true HTTP/1.1 beolvasása |
| Válasz állomásfejléc-érték | a(z) % {resp_&lt;ResponseHeader&gt;} | A megfelelő által azonosított válaszfejléc értékét adja vissza a &lt;ResponseHeader&rt; kifejezés. <br /><br />Ha a válaszfejlécet nevét (például felhasználói ügynök) kötőjellel tartalmaz, cserélje le a aláhúzás karakterrel (például User_Agent). | Példa: % {resp_Content_Length}<br /><br />Minta értéke: 100 |

## <a name="usage"></a>Használat
A következő táblázat ismerteti a helyes szintaxisa egy HTTP-változó.


| Szintaxis | Példa | Leírás |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | a(z) % {állomás} | Ez a szintaxis használatával kaphat a teljes értéket a megadott megfelelő &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | a(z) % {állomás} | Ez a szintaxis segítségével állítsa be a teljes megfelelő a megadott értéket az esetében &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{Host/=^www\.([^\.] +)\.([^\.:] +) /cdn.$2.$3:80} | A reguláris kifejezés használata &lt;HTTPVariableDelimiterExpression&gt; cserélni, törölje, vagy kezelheti egy HTTP-változó értékét. |

HTTP változónevek csak támogatja alfabetikus karaktereket és aláhúzás karaktereket tartalmazhatnak. Nem támogatott karaktereket átalakítása aláhúzás karaktereket tartalmazhatnak.

## <a name="delimiter-reference"></a>Elválasztó referencia
Egy elválasztó karakter után egy HTTP-változó a következő hatásai vannak megvalósítására adható meg:

- Alakítsa át a változó társított értéket.

     Példa: A teljes érték kisbetűssé alakítandó.

- Törölje a társított a változó értékét.

- Kezelheti a változó társított értéket.

     Példa: Módosítsa az értéket a HTTP-változó társított reguláris kifejezéseket használ.

A következő táblázat ismerteti az elválasztó karaktert.

| Elválasztó karakter | Leírás |
| --------- | ----------- |
| := | Azt jelzi, hogy egy alapértelmezett értéket rendeli hozzá a változó amikor vagy: <br />-Hiányzik <br />-NULL értékű. |
| :+ | Azt jelzi, hogy egy alapértelmezett értéket rendeli hozzá a változó Ha értéket van rendelve. |
| : | Azt jelzi, hogy a változóhoz rendelt érték karakterláncrész kibontásra. |
| # | Azt jelzi, hogy az elválasztó karakter után megadott minta törölni kell a változóhoz rendelt érték elejére talált. |
| % | Azt jelzi, hogy az elválasztó karakter után megadott minta törli-e, ha a változó társított értéket végén található. <br />Ez a definíció értéke csak alkalmazható, ha a % szimbólum elválasztó szolgál. |
| / | Amely egy HTTP-változó vagy minta. |
| // | Keresse meg, és cserélje le a megadott minta összes példányát. |
| /= | Található, másolja, és írja át a megadott minta összes előfordulásának. |
| , | A HTTP-változó kisbetűssé társított értéket átalakítani. |
| ^ | A nagybetűssé alakítandó HTTP változó társított értéket átalakítani. |
| ,, | Alakítsa át a megadott karakter kisbetűssé HTTP változó társított értéket az összes példányát. |
| ^^ | Alakítsa át a megadott karakter nagybetűssé alakítandó HTTP változó társított értéket az összes példányát. |

## <a name="exceptions"></a>Kivételek
A következő táblázat ismerteti a körülmények között, amely alatt a megadott szöveg egy HTTP-változó nem számít.

| Feltétel | Leírás | Példa |
| --------- | ----------- | --------|
| Escape-karaktersorozat % szimbólum | A százalékos szimbólum is kell megjelölni egy fordított perjel révén. <br />A minta értéke jobb konstansérték, nem pedig egy HTTP-változó kezelik.| \%{állomás} |
| Ismeretlen változók | Ismeretlen változók mindig visszaküldött üres karakterlánc. | a(z) % {unknownvariable} |
| Érvénytelen karaktereket vagy szintaxis | Érvénytelen karaktereket vagy szintaxist tartalmaz változók literálértékek tekintendők. <br /><br />#1. példa: A megadott érték érvénytelen karaktert tartalmaz (például-). <br /><br />#2. példa: A megadott értéket tartalmaz a dupla kapcsos zárójelek. <br /><br />#3. példa: A megadott érték hiányzik egy záró kapcsos zárójelet.<br /> | #1. példa: % {resp_user-ügynök} <br /><br />#2. példa: % {{állomás}} <br /><br />#3. példa: % {állomás |
| Hiányzik a változó neve | NULL érték mindig ad vissza, ha a változó értéke nincs megadva. | %{} |
| Záró karakterek | Egy változó végén karaktereket literálértékek tekintendők. <br />A minta értéke jobb konstansérték tekinteni záró kapcsos tartalmazza. | % {állomás}} |

## <a name="setting-default-header-values"></a>A beállítás alapértelmezett fejléc értéke
Alapértelmezett érték lehet hozzárendelni egy fejléc a következő feltételek bármelyikének teljesülésekor:
- Hiányzó/beállítás törlése
- NULL értékű.

A következő táblázat ismerteti, hogyan adhat meg alapértelmezett értéket.

| Feltétel | Szintaxis | Példa | Leírás |
| --------- | ------ | --------| ----------- |
| Egy alapértelmezett értékre állítva egy fejléc a következő feltételek bármelyikének teljesülésekor: <br /><br />-Hiányzik egy fejléc <br /><br />-Fejléc értéke NULL.| A(z) % {változó: = Value} | a(z) % {http_referer: = meghatározatlan} | A hivatkozó fejléc csak úgy lesz beállítva, *meghatározatlan* amikor, vagy állítsa NULL értékre. Semmilyen műveletre akkor kerül sor, ha van beállítva. |
| Állítsa be fejléc alapértelmezett értéket, ha hiányzik. | A(z) % {változó = Value} | a(z) % {http_referer = meghatározatlan} | A hivatkozó fejléc csak úgy lesz beállítva, *meghatározatlan* megadva. Semmilyen műveletre akkor kerül sor, ha van beállítva. |
| Állítsa be a fejléc alapértelmezett értéket, amikor nem felel meg a következő feltételek: <br /><br />-Hiányzik<br /><br /> -NULL értékű. | A(z) % {változó: + érték} | a(z) % {http_referer: + meghatározatlan} | A hivatkozó fejléc csak úgy lesz beállítva, *meghatározatlan* amikor érték van hozzárendelve. Semmilyen műveletre akkor kerül sor, ha hiányzik, vagy állítsa NULL értékre. |

## <a name="manipulating-variables"></a>Változók módosítása
Változók kezelhetők a következőképpen:

- Karakterláncrészletek kibontása
- Minták eltávolítása

### <a name="substring-expansion"></a>Substring bővítése
Alapértelmezés szerint egy változó bővített teljes értéke. Az alábbi szintaxissal csak bontsa ki a változó értéke egy részét.

`%<Variable>:<Offset>:<Length>}`

Kapcsolatos információkat:

- Az eltolás kifejezés rendelt határozza meg a substring kezdő karakter:

     - Pozitív: A substring kezdő karakter kiszámítása a karakterlánc első karaktere.
     - Nulla: A substring kezdő karakter a karakterlánc első karaktere.
     - Negatív: A substring kezdő karakter kiszámítása a karakterlánc utolsó karaktere.

- A substring hosszát határozza meg a *hossza* kifejezés:

     - Nincs megadva: Hossza kifejezés kihagyása lehetővé teszi, hogy a keresendő karakterláncrészletet összes karaktereket és a karakterlánc végén a kezdő karakter között.
     - Pozitív: Meghatározza, hogy mennyi jobb a kezdő karakter karakterláncrészletet.
     - Negatív: Meghatározza, hogy mennyi balra a kezdő karakter karakterláncrészletet.

#### <a name="examples"></a>Példák:

Az alábbi példa támaszkodik a következő minta kérelem URL-címe:

`https://cdn.mydomain.com/folder/marketing/myconsultant/proposal.html`

A következő karakterlánc változók kezelésére szolgáló különböző módszerekről mutatja be:

`https://www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm`

A minta kérelem URL-címe alapján, a fenti változó adatkezelési eredményez a következő értéket:

`https://www.mydomain.com/mobile/marketing/proposal.htm`


### <a name="pattern-removal"></a>Minta eltávolítása
Egy adott bizonyos mintázatnak megfelelő szöveg elején vagy végén egy változó értéke lehet eltávolítani.

| Szintaxis | Műveletek |
| ------ | ------ |
| A(z) % {változó #Pattern} | Távolítsa el a szöveg, ha a megadott karakterlánc megtalálható egy változó értékének elejére. |
| A(z) % {% változót mintát} | Távolítsa el a szöveg, ha a megadott karakterlánc megtalálható a változó értékét a végén. |

#### <a name="example"></a>Példa:

Ez a mintaforgatókönyv request_uri változó értéke:

`/800001/myorigin/marketing/product.html?language=en-US`

Az alábbi táblázat bemutatja, hogyan működik ez a szintaxis.

| Szintaxis | Results (Eredmények) |
| ------------- | ------- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/Product.HTML?Language=en-us | A változó a mintával kezdődik, mert a lett cserélve. |
| a(z) % {request_uri % html} htm | /800001/myorigin/marketing/Product.HTML?Language=en-us | A változó nem végződhet a mintát, mert nem változott.|

### <a name="find-and-replace"></a>Keresés és csere
A Keresés és csere szintaxisa a következő táblázatban ismertetett.

| Szintaxis | Műveletek |
| ------ | ------ |
| %{Variable/find/replace} | Keresse meg, és cserélje le a első előfordulása a megadott mintának. |
| %{Variable//find/replace} | Keresse meg, és cserélje le a megadott minta összes előfordulásának. |
| A(z) % {változó ^} |A teljes érték átalakítása nagybetűk. |
| A(z) % {változó ^ található} | A megadott minta első előfordulásának átalakítása nagybetűk. |
| A(z) % {változó} | A teljes érték átalakítása kis. |
| A(z) % {változó, keresés} | A megadott minta első előfordulásának átalakítása kis. |

### <a name="find-and-rewrite"></a>Keresés és átdolgozás
A Keresés és csere változatát használja a szöveg, amely megfelel a megadott minta, amikor írja át azt. A Keresés és átdolgozás szintaxisa a következő táblázatban ismertetett.

| Szintaxis | Műveletek |
| ------ | ------ |
| %{Variable/=find/Rewrite} | Található, másolja, és írja át a megadott minta összes előfordulásának. |
| %{Variable/^find/Rewrite} | Található, másolja, és írja át a megadott minta elején a változó esetén. |
| %{Variable/$find/Rewrite} | Található, másolja, és írja át a megadott minta, a változó végén esetén. |
| %{Variable/find} | Keresse meg és törölje a megadott minta összes előfordulását. |

Kapcsolatos információkat:

- Bontsa ki a szöveget, amely megegyezik a megadott minta egy dollárjel követ egy teljes egész szám (például 1 USD) megadásával.

- Több minták adható meg. A minta van megadva határozza meg az egész szám, amely rendeli hozzá. A következő példában az első minta megfelel "www" értékűre., a második minta megfelel a második szintű tartomány, és a harmadik minta megfelel a legfelső szintű tartomány:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Egy átírt érték szöveg- és a helyőrzők kombinációja állhat.

    Az előző példában az állomásnév rendszer újraírja `cdn.$2.$3:80` (például cdn.mydomain.com:80).

- A kis-és egy minta helyőrző (például $1) a következő jelzőket keresztül lehet módosítani:
     - U: a bővített érték nagybetűk.

         Szintaxis:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: kisbetűs bővített értékét.

         Szintaxis:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- A minta előtt meg kell adni a operátor. A megadott operátor mintát rögzítése viselkedését határozza meg:

     - `=`: Azt jelzi, hogy a megadott minta összes előfordulásának rögzíteni kell, és írni.
     - `^`: Azt jelzi, hogy csak a megadott minta kezdődő szöveg lesz rögzítve.
     - `$`: Azt jelzi, hogy csak a megadott minta végződő szöveg lesz rögzítési.
 
- Ha nincs megadva a */Újraírási* érték, a szöveg, amely megfelel a mintának törlődik.


