---
title: Az Azure CDN szabálymotorral HTTP változók |} A Microsoft Docs
description: HTTP-változók lehetővé teszik a HTTP kérés- és metaadatok lekérése céljából.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 39084fd8408a123e8152ad96fa92025fd04ed42b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092813"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Az Azure CDN szabálymotorral HTTP változói
HTTP-változók adja meg az eszközöket, amelyekkel HTTP kérés- és metaadatok lehet lekérdezni. A metaadatok majd dinamikusan a kérelem vagy válasz módosításához használható. HTTP-változók használata a következő szabályok adatbázismotor-funkciók korlátozva:

- [Gyorsítótár-kulcs átírás](cdn-rules-engine-reference-features.md#cache-key-rewrite)
- [Ügyfél-kérelem fejléce módosítsa](cdn-rules-engine-reference-features.md#modify-client-request-header)
- [Ügyfél válasz fejléce módosítsa](cdn-rules-engine-reference-features.md#modify-client-response-header)
- [Átirányítási URL-címe](cdn-rules-engine-reference-features.md#url-redirect)
- [URL-átírás](cdn-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Meghatározások
A következő táblázat ismerteti a támogatott HTTP-változókat. Üres érték van adja vissza, ha Georedundáns metaadatokat (például postai irányítószám) egy adott kérés nem érhető el.


| Name (Név) | Változó | Leírás | Mintaérték |
| ---- | -------- | ----------- | ------------ |
| Az ASN (kérelmező) | a(z) % {geo_asnum} | Azt jelzi, hogy a kérelmező AS-szám. <br /><br />**Elavult:** a(z) % {virt_dst_asnum}. <br />Ez a változó értéke: % {geo_asnum} érvénytelenítve lett. Bár ez a változó elavult használó szabály továbbra is működni fog, frissítenie kell, hogy az új változót használja. | AS15133 |
| Város (kérelmező) | a(z) % {geo_city} | Azt jelzi, hogy a kérelmező város. | Los Angeles |
| Kontinens (kérelmező) | a(z) % {geo_continent} | Azt jelzi, hogy a kérelmező kontinens keresztül annak rövidítése. <br />Érvényes értékek a következők: <br />AF: Afrika<br />AS: Ázsiában<br />Az EU: Európa<br />NA: Észak-Amerika<br />C: Óceánia<br />SA: Dél-Amerika<br /><br />**Elavult:** a(z) % {virt_dst_continent}. <ber />Ez a változó értéke: % {geo_continent} érvénytelenítve lett. <br />Bár ez a változó elavult használó szabály továbbra is működni fog, frissítenie kell, hogy az új változót használja.| – |
| Cookie-értéket | a(z) % {cookie_Cookie} | A cookie-kulcs a cookie-k kifejezés által azonosított megfelelő értéket adja vissza. | Példa: <br />a(z) % {cookie__utma}<br /><br />Mintaérték:<br />111662281.2.10.1222100123 |
| Ország (kérelmező) | a(z) % {geo_country} | Azt jelzi, hogy a kérelmező származási keresztül az országkódot. <br />**Elavult:** a(z) % {virt_dst_country}. <br /><br />Ez a változó értéke: % {geo_country} érvénytelenítve lett. Bár ez a változó elavult használó szabály továbbra is működni fog, frissítenie kell, hogy az új változót használja. | USA |
| Kijelölt piaci területen (kérelmező) | a(z) % {geo_dma_code} |Azt jelzi, hogy a kérelmező media piac által a régiókód. <br /><br />Ez a mező csak akkor érvényes, a kérelmekre, amely az Egyesült Államokból származnak.| 745 |
| HTTP-kérési metódus | a(z) % {request_method} | Azt jelzi, hogy a HTTP-kérési metódust. | GET |
| HTTP-állapotkód | a(z) % {status} | Azt jelzi, hogy a válasz HTTP-állapotkódot. | 200 |
| IP-cím (kérelmező) | a(z) % {virt_dst_addr} | Azt jelzi, hogy a kérelmező IP-címet. | 192.168.1.1 |
| Szélesség (kérelmező) | a(z) % {geo_latitude} | Azt jelzi, hogy a kérelmező szélességi. | 34.0995 |
| Hosszúsági (kérelmező) | a(z) % {geo_longitude} | Azt jelzi, hogy a kérelmező hosszúság. | -118.4143 |
| Statisztikai nagyvárosi (kérelmező) | a(z) % {geo_metro_code} | Azt jelzi, hogy a kérelmező nagyvárosi körzetében. <br /><br />Ez a mező csak akkor érvényes, a kérelmekre, amely az Egyesült Államokból származnak.<br />| 745 |
| Port (kérelmező) | a(z) % {virt_dst_port} | Azt jelzi, hogy az igénylő a rövid élettartamú port. | 55885 |
| Irányítószám (kérelmező) | a(z) % {geo_postal_code} | Azt jelzi, hogy a kérelmező irányítószámot. | 90210 |
| Lekérdezési karakterlánc | a(z) % {is_args} | Ez a változó értéke megfelelően, hogy a kérelem tartalmaz-e a lekérdezési karakterlánc változik.<br /><br />-Lekérdezési karakterlánc:?<br />– Nincs lekérdezési karakterlánc: NULL | ? |
| Lekérdezési karakterlánc paramétereként található | a(z) % {is_amp} | Ez a változó értéke megfelelően, hogy a kérés tartalmaz-e legalább egy lekérdezési karakterlánc paramétereként változik.<br /><br />-Paraméter találhatók: &<br />-Nincsenek paraméterei: NULL | & |
| Lekérdezési karakterlánc paraméter értéke | a(z) % {arg_&lt;paraméter&gt;} | A lekérdezési karakterlánc paraméter által azonosított megfelelő értéket adja vissza a &lt;paraméter&gt; kifejezés. | Példa: <br />a(z) % {arg_language}<br /><br />Minta lekérdezésikarakterlánc-paramétert: <br />? nyelvi = en<br /><br />Érték példa: en |
| Lekérdezési karakterlánc értéke | a(z) % {query_string} | A kérelem URL-címben meghatározott teljes lekérdezési karakterlánc értékét jelöli. |1. kulcs = 1. érték & 2. kulcs = érték2 & key3 = val3 |
| Hivatkozó tartomány | a(z) % {referring_domain} | Azt jelzi, hogy a tartományt a hivatkozó kérés fejlécében. | www.google.com |
| Régió (kérelmező) | a(z) % {geo_region} | Azt jelzi, hogy az igénylő a régióban (például állam/megye) annak alfanumerikus rövidítése. | CA |
| Kérelem fejlécének értéke | a(z) % {http_RequestHeader} | A kérelem fejlécében RequestHeader kifejezés által azonosított megfelelő értéket adja vissza. <br /><br />Ha a kérelem fejlécében neve tartalmaz egy kötőjellel (például felhasználói ügynök), cserélje le az aláhúzás (például User_Agent).| Példa: % {http_Connection}<br /><br />Mintaérték: Keep-Alive | 
| Kérelem gazdagép | a(z) % {host} | Azt jelzi, hogy a gazdagépen definiált a kérelem URL-CÍMÉT. | www.mydomain.com |
| Kérelem protokoll | a(z) % {request_protocol} | Azt jelzi, hogy a kérelem protokollt. | HTTP/1.1-ES |
| Kérelem séma | a(z) % {scheme} | Azt jelzi, hogy a kérelem sémát. |http |
| Kérés URI-ja (relatív) | a(z) % {request_uri} | Azt jelzi, hogy a relatív elérési utat, beleértve a lekérdezési karakterláncot, a kérés URI Azonosítójában megadott. | /marketing/foo.js?loggedin=TRUE |
| Kérés URI-ja (relatív lekérdezési karakterlánc nélkül) | a(z) % {URI-t} | Azt jelzi, hogy a relatív elérési útját a kért tartalmat. <br /><br/>Legfontosabb tudnivalókat:<br />– A relatív elérési út nem tartalmazza a lekérdezési karakterlánc.<br />– A relatív elérési út tartalmazza az URL-cím újraírások. Egy URL-címet fog kell írni a következő feltételek:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-URL Újraírási funkció: Ez a funkció átírja a kérés URI Azonosítójában megadott relatív elérési útját.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Edge CNAME URL-címe: A kérelem az ilyen típusú újraírja, a megfelelő CDN URL-címre. |/800001/corigin/rewrittendir/foo.js |
| Kérés URI-ja | a(z) % {kérelem} | A kérelem ismerteti. <br />Szintaxis: &lt;HTTP-metódus&gt; &lt;relatív elérési út&gt; &lt;HTTP-protokoll&gt; | HTTP/1.1-es /marketing/foo.js?loggedin=true beolvasása |
| Válasz fejléc értéke | a(z) % {resp_&lt;ResponseHeader&gt;} | A válasz fejléce által azonosított megfelelő értéket adja vissza a &lt;ResponseHeader&gt; kifejezés. <br /><br />Ha a válasz fejléce neve tartalmaz egy kötőjellel (például felhasználói ügynök), cserélje le az aláhúzás (például User_Agent). | Példa: % {resp_Content_Length}<br /><br />Minta értéke: 100 |

## <a name="usage"></a>Használat
A következő táblázat ismerteti a megfelelő szintaxist egy HTTP-változó megadásához.


| Szintaxis | Példa | Leírás |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | a(z) % {host} | Ez a szintaxis használatával a teljes értéket a megadott megfelelő &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | a(z) % {host} | Ez a szintaxis használatával állítsa be az esetben az egész érték a megadott megfelelő &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{Host/=^www\.([^\.] +)\.([^\.:] +) /cdn.$2.$3:80} | A reguláris kifejezés használata &lt;HTTPVariableDelimiterExpression&gt; helyett, törlése, módosítására vagy egy HTTP-változó értékét. |

HTTP-változó neve csak akkor támogatott, alfabetikus karaktereket és aláhúzásjeleket tartalmazhat. Nem támogatott karaktereket átalakítása aláhúzásjeleket tartalmazhat.

## <a name="delimiter-reference"></a>Elválasztó referencia
Egy elválasztó karakter után egy HTTP-változó eléréséhez a következő effektusokat adható meg:

- Alakítsa át a változó társított értéket.

     Példa: A teljes értéket kisbetűsre konvertálja.

- Törölje a társított a változó értékét.

- Módosíthatja a változó társított értéket.

     Példa: Reguláris kifejezésekkel társított a HTTP-változó értékének módosításához.

Az elválasztó karakterek a következő táblázat ismerteti.

| Elválasztó karakter | Leírás |
| --------- | ----------- |
| := | Azt jelzi, hogy egy alapértelmezett érték lesz hozzárendelve a változó vagy ha: <br />– Hiányzik <br />-NULL értékű. |
| :+ | Azt jelzi, hogy egy alapértelmezett értéket hozzá lesz rendelve a változó, ha egy érték van hozzárendelve. |
| : | Azt jelzi, hogy a változóhoz rendelt érték részkarakterláncot kibomlik. |
| # | Azt jelzi, hogy a határolójel utáni megadott sémának törli-e, ha a változóhoz hozzárendelt érték elején található. |
| % | Azt jelzi, hogy a határolójel utáni megadott sémának törli-e, ha a változó hozzárendelt érték végén található. <br />Ez a definíció csak akkor alkalmazható, ha a % szimbólum elválasztóként használható. |
| / | Határoló egy HTTP-változó vagy minta. |
| // | Keresse meg, és cserélje le a megadott minta összes példánya. |
| /= | Található, másolja, majd írja újra a megadott minta összes előfordulását. |
| ;  | A HTTP-változó kisbetűssé hozzárendelt érték konvertálása. |
| ^ | A HTTP-változó nagybetűvé hozzárendelt érték konvertálása. |
| ,, | Társított kisbetűssé HTTP változó értéke a megadott karakter minden előfordulását konvertálni. |
| ^^ | Társított nagybetűvé HTTP változó értéke a megadott karakter minden előfordulását konvertálni. |

## <a name="exceptions"></a>Kivételek
A következő táblázat ismerteti a körülmények között, amely alatt a megadott szöveg egy HTTP-változó nem számít.

| Állapot | Leírás | Példa |
| --------- | ----------- | --------|
| Escape-karaktersorozat % szimbólum | Százalék szimbólummal is kell megjelölni egy fordított perjel használatával. <br />A jobb Mintaérték Szövegkonstansérték, nem pedig egy HTTP-változó lesznek kezelve.| \%{gazdagép} |
| Ismeretlen változók | Üres karakterlánc mindig ismeretlen változók adja vissza. | a(z) % {unknownvariable} |
| Érvénytelen karakterek vagy szintaxis | Érvénytelen karakterek vagy szintaxis tartalmazó változókat konstans értékeket számít. <br /><br />#1. példa: A megadott érték érvénytelen karaktert tartalmaz (például-). <br /><br />#2. példa: A megadott értéket tartalmaz a dupla kapcsos zárójelek közé. <br /><br />#3. példa: A megadott érték hiányzik egy záró kapcsos zárójelet.<br /> | #1. példa: % {resp_user-ügynök} <br /><br />#2. példa: % {{gazdagép}} <br /><br />#3. példa: % {gazdagép |
| Hiányzik a változó neve. | NULL érték mindig adja vissza, ha a változó értéke nincs megadva. | %{} |
| Záró karakterek | Egy változó végén karaktereket konstansértékekkel kell kezelni. <br />Jobb minta érték tartalmaz egy záró kapcsos zárójelet, amely egy literálérték, lesznek kezelve. | a(z) % {host}} |

## <a name="setting-default-header-values"></a>Alapértelmezett fejléc értékeinek beállítása
Alapértelmezett érték hozzárendelheti egy fejléc a következő feltételek bármelyikének teljesülésekor:
- Hiányzó vagy nem beállított
- NULL értékű.

A következő táblázat ismerteti, hogyan adhat meg egy alapértelmezett értéket.

| Állapot | Szintaxis | Példa | Leírás |
| --------- | ------ | --------| ----------- |
| Egy alapértelmezett értékre állítva egy fejléc a következő feltételek bármelyikének teljesülésekor: <br /><br />– Hiányzik egy fejléc <br /><br />-Fejléc értéke NULL értékre van beállítva.| A(z) % {változó: = érték} | a(z) % {http_referer: = nincs megadva} | A Referer fejléc csak állítja be *meghatározatlan* hiányzik vagy NULL értékre. Nincs művelet kerül sor, ha van beállítva. |
| Ha ez hiányzik, állítsa be egy fejléc egy alapértelmezett értéket. | A(z) % {változó = érték} | a(z) % {http_referer = nincs megadva} | A Referer fejléc csak állítja be *meghatározatlan* megadva. Nincs művelet kerül sor, ha van beállítva. |
| Állítsa be a fejléc egy alapértelmezett értéket, ha nem teljesíti az alábbi feltételek bármelyike: <br /><br />– Hiányzik<br /><br /> -NULL értékű. | A(z) % {változó: + érték} | a(z) % {http_referer: + meghatározatlan} | A Referer fejléc csak állítja be *meghatározatlan* mikor értéket rendelték hozzá. Nincs művelet kerül sor, ha hiányzik vagy null. |

## <a name="manipulating-variables"></a>Változók módosítása
Változók a következő módon kezelhetők:

- Bővülő karakterláncrész
- Minták eltávolítása

### <a name="substring-expansion"></a>Karakterláncrészlet bővítése
Alapértelmezés szerint a teljes érték bővített változóhoz. Az alábbi szintaxissal csak bontsa ki a változó értékének egy karakterláncrészletet.

`%<Variable>:<Offset>:<Length>}`

Legfontosabb tudnivalókat:

- Hozzárendelt eltolása kifejezés érték határozza meg a kezdő karakter a karakterláncrész:

     - Pozitív: A kezdő karakter a karakterláncrész kiszámítása a karakterlánc első karaktere.
     - Nulla: A kezdő karakter a karakterláncrész a karakterlánc első karaktere.
     - Negatív: A kezdő karakter a karakterláncrész kiszámítása az utolsó karakter a karakterláncban.

- A karakterláncrész hossza határozza meg a *hossza* kifejezés:

     - Nincs megadva: Felsorolhatja hossza kifejezés lehetővé teszi a keresendő karakterláncrészletet tartalmazza az összes karaktert a karakterlánc végén és a kezdő karakter között.
     - Pozitív: Azt határozza meg a kezdő karakter jobb karakterláncrész hossza.
     - Negatív: Azt határozza meg a kezdő karakter a bal oldali karakterláncrész hossza.

#### <a name="example"></a>Példa:

Az alábbi példa a következő minta kérelem URL-címe támaszkodik:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

A következő karakterláncot a változók kezelésére szolgáló különböző módszereket mutat be:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

A minta-kérelem URL-cím alapján, a fenti változó adatkezelési állítja elő a következő értéket:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Minta eltávolítása
Szöveg, amely megfelel egy adott minta elején vagy végén egy változó értékének távolíthatja el.

| Szintaxis | Műveletek |
| ------ | ------ |
| A(z) % {#Pattern változó} | Távolítsa el a szöveget, ha a megadott karakterlánc megtalálható a változó értékének elejére. |
| A(z) % {% változót minta} | Távolítsa el a szöveg, ha a megadott karakterlánc megtalálható a változó értékét a végén. |

#### <a name="example"></a>Példa:

Minta ebben a forgatókönyvben a *request_uri* változó értéke:

`/800001/myorigin/marketing/product.html?language=en-US`

Az alábbi táblázat azt ismerteti, hogyan működik ez a szintaxis.

| Mintául szolgáló szintaxis | Results (Eredmények) |
| ------------- | ------- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/Product.HTML?Language=en-us | A változó a minta kezdődik, mert a lett cserélve. |
| a(z) % {request_uri % html} htm | /800001/myorigin/marketing/Product.HTML?Language=en-us | A változó nem végződhet a mintának, mert nem változott.|

### <a name="find-and-replace"></a>Keresés és csere
A Keresés és csere szintaxisa a következő táblázatban leírt.

| Szintaxis | Műveletek |
| ------ | ------ |
| %{Variable/find/replace} | Keresse meg, és cserélje le a megadott minta első előfordulása. |
| %{Variable//find/replace} | Keresse meg, és cserélje le a megadott minta összes előfordulását. |
| A(z) % {változó ^} |A teljes érték átalakítása nagybetűssé. |
| A(z) % {változó ^ található} | A megadott minta a első előfordulása átalakítása nagybetűvé. |
| A(z) % {, változó} | A teljes érték átalakítása kisbetűvé. |
| A(z) % {változó, keresés} | A megadott minta a első előfordulása átalakítása kisbetűvé. |

### <a name="find-and-rewrite"></a>A Keresés és átírás
Keresés és csere egy variációja a szöveg, amely megfelel a megadott minta újraírását, amikor használja. A Keresés és újraírási szintaxis a következő táblázatban leírt.

| Szintaxis | Műveletek |
| ------ | ------ |
| %{Variable/=find/Rewrite} | Található, másolja, majd írja újra a megadott minta összes előfordulását. |
| %{Variable/^find/Rewrite} | Található, másolja, majd a megadott minta újraírási elején. a változó esetén. |
| %{Variable/$find/Rewrite} | Található, másolja, majd a megadott minta újraírási változó végén esetén. |
| %{Variable/find} | Keresse meg és törölje a megadott minta összes előfordulását. |

Legfontosabb tudnivalókat:

- Bontsa ki a szöveget, amely megegyezik a megadott minta egy dollárjel követ egész számnak egész (például $1) megadásával.

- Több is megadhatók. A sorrend, amelyben a minta van megadva a hozzá rendelt egész számot határozza meg. A következő példában az első minta egyezik "www.," a második minta megegyezik a második szintű tartomány, és a harmadik minta megfelel a legfelső szintű tartományt:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- A Átdolgozta érték szöveg és a helyőrzők bármely kombinációja állhat.

    Az előző példában az állomásnevet a feladatátvitelt `cdn.$2.$3:80` (például cdn.mydomain.com:80).

- A kis-és a egy minta helyőrző (például $1) a következő jelzőket keresztül lehet módosítani:
     - U: a kibontott érték nagybetűk.

         Mintául szolgáló szintaxis:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: kisbetűs kibontott értékét.

         Mintául szolgáló szintaxis:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Az operátornak meg kell adni a minta előtt. A megadott operátor határozza meg a minta-rögzítés viselkedés:

     - `=`: Azt jelzi, hogy kell-e írni és rögzített a megadott minta összes előfordulását.
     - `^`: Azt jelzi, hogy csak a megadott minta kezdetű szöveg lesz rögzíthetők.
     - `$`: Azt jelzi, hogy csak a megadott minta végződő szöveg rögzítése.
 
- Ha kihagyja a */Újraírási* érték, amely megfelel a mintának a szöveg törlődik.


