---
title: HTTP-változók az Azure CDN-szabálymotorhoz | Microsoft dokumentumok
description: A HTTP-változók lehetővé teszik a HTTP-kérelmek és válaszok metaadatainak beolvasását.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 53ad0c516547e17801bd57c2fd6b0d1704383797
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593822"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>AZ Azure CDN-szabálymotorHTTP-változói
A HTTP-változók biztosítják azokat az eszközöket, amelyekkel http-kérelem és válasz metaadatait lekérheti. Ezek a metaadatok ezután egy kérés vagy válasz dinamikus módosítására használhatók. A HTTP-változók használata a következő szabályokra korlátozódik:

- [Cache-Key újraírása](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Ügyfélkérelem fejlécének módosítása](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Ügyfélválasz fejlécének módosítása](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL-átirányítás](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL-átírás](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Meghatározások
Az alábbi táblázat a támogatott HTTP-változókat ismerteti. Üres értéket ad vissza a rendszer, ha a GEO metaadatai (például irányítószám) nem érhetők el egy adott kérelemhez.


| Név | Változó | Leírás | Mintaérték |
| ---- | -------- | ----------- | ------------ |
| ASN (kérelmező) | %{geo_asnum} | A kérelmező AS-számát jelzi. <br /><br />**Elavult:** %{virt_dst_asnum}. <br />Ez a változó a(z) %{geo_asnum} javára elavult. Bár az elavult változót használó szabály továbbra is működni fog, frissítenie kell az új változó használatához. | AS15133 |
| Város (kérelmező) | %{geo_city} | A kérelmező városát jelzi. | Los Angeles |
| Kontinens (kérelmező) | %{geo_continent} | A kérelmező kontinensét jelöli a rövidítéssel. <br />Az érvényes értékek a következők: <br />AF: Afrika<br />AS: Ázsia<br />EU: Európa<br />NA: Észak-Amerika<br />OC: Óceánia<br />SA: Dél-Amerika<br /><br />**Elavult:** %{virt_dst_continent}. <br />Ez a változó a(z) %{geo_continent} javára elavult. <br />Bár az elavult változót használó szabály továbbra is működni fog, frissítenie kell az új változó használatához.| N/A |
| Cookie-érték | %{cookie_Cookie} | A Cookie kifejezés által azonosított cookie-kulcsnak megfelelő értéket adja eredményül. | Mintahasználat: <br />%{cookie__utma}<br /><br />Mintaértéke:<br />111662281.2.10.1222100123 |
| Ország (kérelmező) | %{geo_country} | A kérelmező származási országát jelzi az országkódon keresztül. <br />**Elavult:** %{virt_dst_country}. <br /><br />Ez a változó a(z) %{geo_country} javára elavult. Bár az elavult változót használó szabály továbbra is működni fog, frissítenie kell az új változó használatához. | USA |
| Kijelölt piaci terület (kérelmező) | %{geo_dma_code} |A kérelmező médiapiacát jelzi a régiókódja szerint. <br /><br />Ez a mező csak az Egyesült Államokból származó kérelmekre vonatkozik.| 745 |
| HTTP-kérelem módja | %{request_method} | A HTTP-kérelem metódusát jelzi. | GET |
| HTTP-állapotkód | %{állapot} | A válasz HTTP-állapotkódját jelzi. | 200 |
| IP-cím (kérelmező) | %{virt_dst_addr} | A kérelmező IP-címét jelzi. | 192.168.1.1 |
| Szélesség (kérelmező) | %{geo_latitude} | A kérelmező szélességi fokát jelzi. | 34.0995 |
| Hosszúság (kérelmező) | %{geo_longitude} | A kérelmező hosszúsági fokát jelzi. | -118.4143 |
| Nagyvárosi statisztikai terület (kérelmező) | %{geo_metro_code} | A kérelmező nagyvárosi területét jelzi. <br /><br />Ez a mező csak az Egyesült Államokból származó kérelmekre vonatkozik.<br />| 745 |
| Port (kérelmező) | %{virt_dst_port} | A kérelmező ideiglenes portját jelzi. | 55885 |
| Irányítószám (kérelmező) | %{geo_postal_code} | A kérelmező irányítószámát jelzi. | 90210 |
| Található lekérdezési karakterlánc | %{is_args} | A változó értéke attól függően változik, hogy a kérelem tartalmaz-e lekérdezési karakterláncot.<br /><br />- Query String Talált: ?<br />- Nincs lekérdezési karakterlánc: NULL | ? |
| Található lekérdezési karakterlánc paramétere | %{is_amp} | A változó értéke attól függően változik, hogy a kérelem legalább egy lekérdezési karakterlánc-paramétert tartalmaz-e.<br /><br />- Paraméter található: &<br />- Nincs paraméter: NULL | & |
| Lekérdezési karakterlánc paraméterének értéke | %{arg_&lt;&gt;paraméter } | A paraméter&gt; kifejezés által azonosított lekérdezési &lt;karakterlánc paraméterének megfelelő értéket adja eredményül. | Mintahasználat: <br />%{arg_language}<br /><br />Minta lekérdezési karakterlánc-paraméter: <br />?nyelv=hu<br /><br />Mintaérték: hu |
| Lekérdezési karakterlánc értéke | %{query_string} | A kérelem URL-címében definiált teljes lekérdezési karakterlánc-értéket jelzi. |key1=val1&key2=val2&key3=val3 |
| Hivatkozó tartomány | %{referring_domain} | A Hivatkozókérés fejlécében definiált tartományt jelzi. | <www.google.com> |
| Régió (kérelmező) | %{geo_region} | A kérelmező régióját (például államot vagy tartományt) jelzi az alfanumerikus rövidítésen keresztül. | CA |
| Fejlécérték kérése | %{http_RequestHeader} | A RequestHeader kifejezés által azonosított kérelemfejlécnek megfelelő értéket adja eredményül. <br /><br />Ha a kérelemfejléc neve kötőjelet tartalmaz (például User-Agent), cserélje le aláhúzásjelre (például User_Agent).| Mintahasználat: %{http_Connection}<br /><br />Mintaérték: Keep-Alive | 
| Állomás kérése | %{állomás} | A kérelem URL-címében definiált állomást jelzi. | <www.mydomain.com> |
| Kérési protokoll | %{request_protocol} | A kérelem protokollját jelzi. | HTTP/1.1 |
| Igénylési rendszer | %{séma} | A kérelemsémát jelzi. |http |
| URI kérése (relatív) | %{request_uri} | A kérelem URI-jában definiált relatív elérési utat jelzi, beleértve a lekérdezési karakterláncot is. | /marketing/foo.js?loggedin=true |
| URI kérése (relatív lekérdezési karakterlánc nélkül) | %{uri} | A kért tartalom relatív elérési útját jelzi. <br /><br/>Legfontosabb információk:<br />- Ez a relatív elérési út kizárja a lekérdezési karakterláncot.<br />- Ez a relatív elérési út az URL-újraírásokat tükrözi. Az URL-cím átírása a következő feltételek mellett lesz átírva:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL újraírási funkció: Ez a funkció újraírja a kérelem URI-ban megadott relatív elérési utat.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Edge CNAME URL: Az ilyen típusú kérelem átírja a megfelelő CDN URL-t. |/800001/corigin/rewrittendir/foo.js |
| Kérés URI-ja | %{kérés} | A kérelem leírása. <br />Szintaxis: &lt;HTTP&gt; &lt;metódus&gt; &lt;relatív elérési útja HTTP protokoll&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Válasz fejlécének értéke | %{resp_&lt;ResponseHeader&gt;} | A &lt;ResponseHeader&gt; kifejezés által azonosított válaszfejlécnek megfelelő értéket adja eredményül. <br /><br />Ha a válaszfejléc neve kötőjelet tartalmaz (például User-Agent), cserélje le aláhúzásjelre (például User_Agent). | Mintahasználat: %{resp_Content_Length}<br /><br />Mintaérték: 100 |

## <a name="usage"></a>Használat
Az alábbi táblázat a HTTP-változó megadásának megfelelő szintaxisát ismerteti.


| Szintaxis | Példa | Leírás |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{állomás} | Ezzel a szintaxissal lejuthat a megadott &lt;HTTPVariable&gt;értéknek megfelelő teljes értékre. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{állomás,} | Ezzel a szintaxissal állíthatja be a megadott &lt;HTTPVariableDelimiter&gt;nek megfelelő teljes érték esetét. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.\.([^ :]+)/cdn.$2.$3:80} | A HTTPVariableDelimiterExpression &lt;&gt; reguláris kifejezéssel helyettesítse, törölje vagy törölje a HTTP-változó értékét. |

A HTTP-változónevek csak az alfabetikus karaktereket és aláhúzásjeleket támogatják. A nem támogatott karaktereket aláhúzássá alakíthatja.

## <a name="delimiter-reference"></a>Határoló hivatkozás
A HTTP-változó után egy határolójel adható meg az alábbi hatások bármelyikének eléréséhez:

- Alakítsa át a változóhoz társított értéket.

     Példa: A teljes érték átalakítása kisbetűssé.

- Törölje a változóhoz társított értéket.

- A változóhoz társított érték módosítása.

     Példa: Reguláris kifejezések használatával módosítsa a HTTP-változóhoz társított értéket.

A határolók leírása az alábbi táblázatban található.

| Elválasztó | Leírás |
| --------- | ----------- |
| := | Azt jelzi, hogy egy alapértelmezett érték lesz hozzárendelve a változóhoz, ha az: <br />- Eltűnt <br />- Null értékre van állítva. |
| :+ | Azt jelzi, hogy egy alapértelmezett érték lesz hozzárendelve a változóhoz, ha egy érték hozzá van rendelve. |
| : | Azt jelzi, hogy a változóhoz rendelt érték részkarakterlánca ki lesz bontva. |
| # | Azt jelzi, hogy a határolójel után megadott mintát törölni kell, ha a változóhoz társított érték elején található. |
| % | Azt jelzi, hogy a határolójel után megadott mintát törölni kell, ha a változóhoz társított érték végén található. <br />Ez a meghatározás csak akkor alkalmazható, ha a % szimbólumot határolójelként használják. |
| / | HTTP-változót vagy mintát határolja. |
| // | Keresse meg és cserélje le a megadott minta összes példányát. |
| /= | Keresse meg, másolja és írja át a megadott minta összes előfordulását. |
| , | Konvertálja a HTTP változóhoz társított értéket kisbetűssé. |
| ^ | Konvertálja a HTTP változóhoz társított értéket nagybetűssé. |
| ,, | A HTTP-változóhoz társított érték ben megadott karakter összes példányát kisbetűssé konvertálja. |
| ^^ | A HTTP-változóhoz társított érték megadott karakterének összes példányát nagybetűssé konvertálja. |

## <a name="exceptions"></a>Kivételek
Az alábbi táblázat azokat a körülményeket ismerteti, amelyek között a megadott szöveg nem HTTP-változóként lesz kezelve.

| Állapot | Leírás | Példa |
| --------- | ----------- | --------|
| Menekülés % szimbóluma | A százalékszimbólum fordított perjel használatával kikerülhető. <br />A jobb oldali mintaértéket a rendszer literális értékként, nem PEDIG HTTP-változóként kezeli.| \%{állomás} |
| Ismeretlen változók | Az ismeretlen változókhoz mindig üres karakterlánc kerül vissza. | %{unknown_variable} |
| Érvénytelen karakterek vagy szintaxis | Az érvénytelen karaktereket vagy szintaxist tartalmazó változókat a rendszer literális értékként kezeli. <br /><br />#1 példa: A megadott érték érvénytelen karaktert tartalmaz (például -). <br /><br />#2 példa: A megadott érték kapcsos zárójelek kettős készletét tartalmazza. <br /><br />#3 példa: A megadott értékből hiányzik egy záró kapcsos zárójel.<br /> | #1 példa: %{resp_user-agent} <br /><br />#2 példa: %{{host}} <br /><br />#3 példa: %{host |
| Hiányzó változónév | Null értéket mindig visszaad, ha nincs megadva egy változó. | %{} |
| Záró karakterek | A változót követő karaktereket a függvény konstans értékként kezeli a függvény. <br />A jobb oldali mintaérték egy záró kapcsos zárójelet tartalmaz, amelyet a függvény ként kezel. | %{állomás}} |

## <a name="setting-default-header-values"></a>Alapértelmezett fejlécértékek beállítása
Az alapértelmezett érték akkor rendelhető a fejléchez, ha az megfelel az alábbi feltételek bármelyikének:
- Hiányzó/nem beállított
- Null értékre van állítva.

Az alábbi táblázat az alapértelmezett érték meghatározását ismerteti.

| Állapot | Szintaxis | Példa | Leírás |
| --------- | ------ | --------| ----------- |
| A fejlécet akkor állítson alapértelmezett értékre, ha az megfelel az alábbi feltételek bármelyikének: <br /><br />- Hiányzó fejléc <br /><br />- A fejléc értéke NULL.| %{Változó:=Érték} | %{http_referrer:=meghatározatlan} | A Hivatkozó fejléc csak akkor lesz *meghatározatlan* értékre állítva, ha hiányzik, vagy NULL értékre van állítva. Nincs művelet, ha már be van állítva. |
| A fejlécet alapértelmezett értékre állítsa, ha az hiányzik. | %{Variable=Érték} | %{http_referrer=meghatározatlan} | A Hivatkozó fejléc csak akkor lesz *meghatározatlan,* ha hiányzik. Nincs művelet, ha már be van állítva. |
| Állítsa a fejlécet alapértelmezett értékre, ha az nem felel meg az alábbi feltételek egyikének sem: <br /><br />- Eltűnt<br /><br /> - Null értékre van állítva. | %{Változó:+Érték} | %{http_referrer:+meghatározatlan} | A Hivatkozó fejléc csak akkor lesz *meghatározatlan,* ha egy érték hozzá van rendelve. Nem történik művelet, ha hiányzik, vagy NULL értékre van állítva. |

## <a name="manipulating-variables"></a>Változók manipulálása
A változók a következő módokon kezelhetők:

- Részkarakterláncok kibontása
- Minták eltávolítása

### <a name="substring-expansion"></a>Karakterlánc-részkibontás
Alapértelmezés szerint egy változó a teljes értékére bővül. A következő szintaxissal csak a változó értékének részkarakterláncát bontsa ki.

`%<Variable>:<Offset>:<Length>}`

Legfontosabb információk:

- Az Eltolás kifejezéshez rendelt érték határozza meg a karakterlánc részkarakterének kezdő karakterét:

     - Pozitív: A karakterláncrész kezdőkaraktere a karakterlánc első karakteréből kerül kiszámításra.
     - Nulla: A karakterláncrész kezdőkaraktere a karakterlánc első karaktere.
     - Negatív: A karakterláncrész kezdőkaraktere a karakterlánc utolsó karakteréből kerül kiszámításra.

- A karakterlánc részhosszát a *Hossz* kifejezés határozza meg:

     - Kihagyás: A Hossz kifejezés elhagyása lehetővé teszi, hogy a karakterláncrész tartalmazza a kezdő karakter és a karakterlánc vége közötti összes karaktert.
     - Pozitív: A karakterlánc részhosszát határozza meg a kezdő karaktertől jobbra.
     - Negatív: A karakterlánc részhosszát határozza meg a kezdő karaktertől balra.

#### <a name="example"></a>Példa:

A következő példa a következő mintakérelem URL-címére támaszkodik:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

A következő karakterlánc bemutatja a változók manipulálásának különböző módszereit:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

A mintakérelem URL-címe alapján a fenti változókezelés a következő értéket eredményezi:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Minta eltávolítása
Az adott mintának megfelelő szöveg eltávolítható a változó értékének elejéről vagy végéről.

| Szintaxis | Műveletek |
| ------ | ------ |
| %{Változó#minta} | A szöveg eltávolítása, ha a megadott minta a változó értékének elején található. |
| %{Változó%minta} | A szöveg eltávolítása, ha a megadott minta a változó értékének végén található. |

#### <a name="example"></a>Példa:

Ebben a mintaforgatókönyvben a *request_uri* változó a következő:

`/800001/myorigin/marketing/product.html?language=en-US`

Az alábbi táblázat bemutatja, hogyan működik ez a szintaxis.

| Példa szintaxisa | Results (Eredmények) | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Mivel a változó a mintával kezdődik, kicserélték. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Mivel a változó nem ér véget a mintával, nem volt változás.|

### <a name="find-and-replace"></a>Keresés és csere
A keresés és a csere szintaxisát az alábbi táblázat ismerteti.

| Szintaxis | Műveletek |
| ------ | ------ |
| %{Változó/Keresés/csere} | A megadott minta első előfordulásának keresése és cseréje. |
| %{Változó/keresés/csere} | Keresse meg és cserélje le a megadott minta összes előfordulását. |
| %{^Variable^} |A teljes érték átalakítása nagybetűssé. |
| %{Változó^Keresés} | A megadott minta első előfordulásának átalakítása nagybetűssé. |
| %{Változó,} | A teljes érték átalakítása kisbetűssé. |
| %{Változó,Keresés} | A megadott minta első előfordulásának kisbetűssé alakítása. |

### <a name="find-and-rewrite"></a>Keresés és átírás
A keresés és csere változatához használja a megadott mintának megfelelő szöveget az újraíráskor. A lekeresés és az átírás szintaxisát az alábbi táblázat ismerteti.

| Szintaxis | Műveletek |
| ------ | ------ |
| %{Variable/=Keresés/Újraírás} | Keresse meg, másolja és írja át a megadott minta összes előfordulását. |
| %{Változó/^Keresés/Újraírás} | Keresse meg, másolja és írja át a megadott mintát, amikor az a változó elején történik. |
| %{Variable/$Find/Rewrite} | Keresse meg, másolja és írja át a megadott mintát, amikor az a változó végén történik. |
| %{Változó/keresés} | A megadott minta összes előfordulásának megkeresése és törlése. |

Legfontosabb információk:

- Bontsa ki a megadott mintának megfelelő szöveget egy dollárjel, majd egy egész szám (például 1 000 Dollár) megadásával.

- Több minta is megadható. A minta beállításának sorrendje határozza meg a hozzá rendelt egész számmeghatározását. A következő példában az első minta megegyezik a "www.", a második minta a második szintű tartománnyal, a harmadik pedig a legfelső szintű tartománnyal:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Az átírt érték a szöveg és a helyőrzők tetszőleges kombinációjából állhat.

    Az előző példában az állomásnév `cdn.$2.$3:80` újraírása (például cdn.mydomain.com:80).

- A minta helyőrzőjének esete (például $1) a következő jelzőkön keresztül módosítható:
     - U: Nagybetűs a bővített érték.

         Minta szintaxisa:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: A bővített érték kisbetűs.

         Minta szintaxisa:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- A minta előtt meg kell adni egy operátort. A megadott operátor határozza meg a mintarögzítési viselkedést:

     - `=`: Azt jelzi, hogy a megadott minta minden előfordulását rögzíteni és újraírni kell.
     - `^`: Azt jelzi, hogy csak a megadott mintával kezdődő szöveg lesz rögzítve.
     - `$`: Azt jelzi, hogy csak a megadott mintával végződő szöveg lesz rögzítve.
 
- Ha kihagyja a */Rewrite* értéket, a mintának megfelelő szöveg törlődik.


