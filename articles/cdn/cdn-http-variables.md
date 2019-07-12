---
title: Az Azure CDN szabálymotorral HTTP változók |} A Microsoft Docs
description: HTTP-változók lehetővé teszik a HTTP kérés- és metaadatok lekérése céljából.
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
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593822"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Az Azure CDN szabálymotorral HTTP változói
HTTP-változók adja meg az eszközöket, amelyekkel HTTP kérés- és metaadatok lehet lekérdezni. A metaadatok majd dinamikusan a kérelem vagy válasz módosításához használható. HTTP-változók használata a következő szabályok adatbázismotor-funkciók korlátozva:

- [Gyorsítótár-kulcs átírás](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Ügyfél-kérelem fejléce módosítsa](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Ügyfél válasz fejléce módosítsa](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL Redirect](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL-átírás](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## <a name="definitions"></a>Meghatározások
A következő táblázat ismerteti a támogatott HTTP-változókat. Üres érték van adja vissza, ha Georedundáns metaadatokat (például postai irányítószám) egy adott kérés nem érhető el.


| Name (Név) | Változó | Leírás | Mintaérték |
| ---- | -------- | ----------- | ------------ |
| Az ASN (kérelmező) | %{geo_asnum} | Azt jelzi, hogy a kérelmező AS-szám. <br /><br />**Elavult:** a(z) % {virt_dst_asnum}. <br />Ez a változó értéke: % {geo_asnum} érvénytelenítve lett. Bár ez a változó elavult használó szabály továbbra is működni fog, frissítenie kell, hogy az új változót használja. | AS15133 |
| Város (kérelmező) | %{geo_city} | Azt jelzi, hogy a kérelmező város. | Los Angeles |
| Kontinens (kérelmező) | %{geo_continent} | Azt jelzi, hogy a kérelmező kontinens keresztül annak rövidítése. <br />Érvényes értékek a következők: <br />AF: Afrika<br />AS: Ázsia<br />EU: Európa<br />NA: Észak-Amerika<br />C: Óceánia<br />SA: Dél-Amerika<br /><br />**Elavult:** a(z) % {virt_dst_continent}. <br />Ez a változó értéke: % {geo_continent} érvénytelenítve lett. <br />Bár ez a változó elavult használó szabály továbbra is működni fog, frissítenie kell, hogy az új változót használja.| – |
| Cookie-értéket | %{cookie_Cookie} | A cookie-kulcs a cookie-k kifejezés által azonosított megfelelő értéket adja vissza. | Példa: <br />%{cookie__utma}<br /><br />Mintaérték:<br />111662281.2.10.1222100123 |
| Ország (kérelmező) | a(z) % {geo_country} | Azt jelzi, hogy a kérelmező származási keresztül az országkódot. <br />**Elavult:** a(z) % {virt_dst_country}. <br /><br />Ez a változó értéke: % {geo_country} érvénytelenítve lett. Bár ez a változó elavult használó szabály továbbra is működni fog, frissítenie kell, hogy az új változót használja. | USA |
| Kijelölt piaci területen (kérelmező) | %{geo_dma_code} |Azt jelzi, hogy a kérelmező media piac által a régiókód. <br /><br />Ez a mező csak akkor érvényes, a kérelmekre, amely az Egyesült Államokból származnak.| 745 |
| HTTP-kérési metódus | %{request_method} | Azt jelzi, hogy a HTTP-kérési metódust. | GET |
| HTTP-állapotkód | %{status} | Azt jelzi, hogy a válasz HTTP-állapotkódot. | 200 |
| IP-cím (kérelmező) | %{virt_dst_addr} | Azt jelzi, hogy a kérelmező IP-címet. | 192.168.1.1 |
| Szélesség (kérelmező) | %{geo_latitude} | Azt jelzi, hogy a kérelmező szélességi. | 34.0995 |
| Longitude (Requester) | %{geo_longitude} | Azt jelzi, hogy a kérelmező hosszúság. | -118.4143 |
| Statisztikai nagyvárosi (kérelmező) | %{geo_metro_code} | Azt jelzi, hogy a kérelmező nagyvárosi körzetében. <br /><br />Ez a mező csak akkor érvényes, a kérelmekre, amely az Egyesült Államokból származnak.<br />| 745 |
| Port (kérelmező) | %{virt_dst_port} | Azt jelzi, hogy az igénylő a rövid élettartamú port. | 55885 |
| Irányítószám (kérelmező) | %{geo_postal_code} | Azt jelzi, hogy a kérelmező irányítószámot. | 90210 |
| Lekérdezési karakterlánc | a(z) % {is_args} | Ez a változó értéke megfelelően, hogy a kérelem tartalmaz-e a lekérdezési karakterlánc változik.<br /><br />-Lekérdezési karakterlánc:?<br />– Nincs lekérdezési karakterlánc: NULL | ? |
| Lekérdezési karakterlánc paramétereként található | %{is_amp} | Ez a változó értéke megfelelően, hogy a kérés tartalmaz-e legalább egy lekérdezési karakterlánc paramétereként változik.<br /><br />-Paraméter találhatók: &<br />-Nincsenek paraméterei: NULL | & |
| Lekérdezési karakterlánc paraméter értéke | a(z) % {arg_&lt;paraméter&gt;} | A lekérdezési karakterlánc paraméter által azonosított megfelelő értéket adja vissza a &lt;paraméter&gt; kifejezés. | Példa: <br />%{arg_language}<br /><br />Minta lekérdezésikarakterlánc-paramétert: <br />?language=en<br /><br />Érték példa: en |
| Lekérdezési karakterlánc értéke | a(z) % {query_string} | A kérelem URL-címben meghatározott teljes lekérdezési karakterlánc értékét jelöli. |1\. kulcs = 1. érték & 2. kulcs = érték2 & key3 = val3 |
| Hivatkozó tartomány | %{referring_domain} | Azt jelzi, hogy a tartományt a hivatkozó kérés fejlécében. | <www.google.com> |
| Régió (kérelmező) | a(z) % {geo_region} | Azt jelzi, hogy az igénylő a régióban (például állam/megye) annak alfanumerikus rövidítése. | CA |
| Kérelem fejlécének értéke | %{http_RequestHeader} | A kérelem fejlécében RequestHeader kifejezés által azonosított megfelelő értéket adja vissza. <br /><br />Ha a kérelem fejlécében neve tartalmaz egy kötőjellel (például felhasználói ügynök), cserélje le az aláhúzás (például User_Agent).| Példa: % {http_Connection}<br /><br />Mintaérték: Keep-Alive | 
| Request Host | %{host} | Azt jelzi, hogy a gazdagépen definiált a kérelem URL-CÍMÉT. | <www.mydomain.com> |
| Kérelem protokoll | %{request_protocol} | Azt jelzi, hogy a kérelem protokollt. | HTTP/1.1-ES |
| Kérelem séma | a(z) % {scheme} | Azt jelzi, hogy a kérelem sémát. |http |
| Kérés URI-ja (relatív) | %{request_uri} | Azt jelzi, hogy a relatív elérési utat, beleértve a lekérdezési karakterláncot, a kérés URI Azonosítójában megadott. | /marketing/foo.js?loggedin=true |
| Kérés URI-ja (relatív lekérdezési karakterlánc nélkül) | a(z) % {URI-t} | Azt jelzi, hogy a relatív elérési útját a kért tartalmat. <br /><br/>Legfontosabb tudnivalókat:<br />– A relatív elérési út nem tartalmazza a lekérdezési karakterlánc.<br />– A relatív elérési út tartalmazza az URL-cím újraírások. Egy URL-címet fog kell írni a következő feltételek:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-URL Újraírási szolgáltatás: Ez a funkció átírja a kérés URI Azonosítójában megadott relatív elérési útját.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Edge CNAME URL-címe: Az ilyen típusú kérelmet a megfelelő CDN URL-címre van átírása. |/800001/corigin/rewrittendir/foo.js |
| Kérés URI-ja | a(z) % {kérelem} | A kérelem ismerteti. <br />Szintaxis: &lt;HTTP-metódus&gt; &lt;relatív elérési út&gt; &lt;HTTP-protokoll&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Válasz fejléc értéke | %{resp_&lt;ResponseHeader&gt;} | A válasz fejléce által azonosított megfelelő értéket adja vissza a &lt;ResponseHeader&gt; kifejezés. <br /><br />Ha a válasz fejléce neve tartalmaz egy kötőjellel (például felhasználói ügynök), cserélje le az aláhúzás (például User_Agent). | Példa: % {resp_Content_Length}<br /><br />Mintaérték: 100 |

## <a name="usage"></a>Használat
A következő táblázat ismerteti a megfelelő szintaxist egy HTTP-változó megadásához.


| Szintaxis | Példa | Leírás |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Ez a szintaxis használatával a teljes értéket a megadott megfelelő &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | a(z) % {host} | Ez a szintaxis használatával állítsa be az esetben az egész érték a megadott megfelelő &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | A reguláris kifejezés használata &lt;HTTPVariableDelimiterExpression&gt; helyett, törlése, módosítására vagy egy HTTP-változó értékét. |

HTTP-változó neve csak akkor támogatott, alfabetikus karaktereket és aláhúzásjeleket tartalmazhat. Nem támogatott karaktereket átalakítása aláhúzásjeleket tartalmazhat.

## <a name="delimiter-reference"></a>Elválasztó referencia
Egy elválasztó karakter után egy HTTP-változó eléréséhez a következő effektusokat adható meg:

- Alakítsa át a változó társított értéket.

     Példa: A teljes érték átalakítása kisbetűvé.

- Törölje a társított a változó értékét.

- Módosíthatja a változó társított értéket.

     Példa: Reguláris kifejezések használata társított a HTTP-változó értékének módosításához.

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
| ; | A HTTP-változó kisbetűssé hozzárendelt érték konvertálása. |
| ^ | A HTTP-változó nagybetűvé hozzárendelt érték konvertálása. |
| ,, | Társított kisbetűssé HTTP változó értéke a megadott karakter minden előfordulását konvertálni. |
| ^^ | Társított nagybetűvé HTTP változó értéke a megadott karakter minden előfordulását konvertálni. |

## <a name="exceptions"></a>Kivételek
A következő táblázat ismerteti a körülmények között, amely alatt a megadott szöveg egy HTTP-változó nem számít.

| Állapot | Leírás | Példa |
| --------- | ----------- | --------|
| Escape-karaktersorozat % szimbólum | Százalék szimbólummal is kell megjelölni egy fordított perjel használatával. <br />A jobb Mintaérték Szövegkonstansérték, nem pedig egy HTTP-változó lesznek kezelve.| \%{host} |
| Unknown variables | An empty string is always returned for unknown variables. | %{unknown_variable} |
| Invalid characters or syntax | Variables that contain invalid characters or syntax are treated as literal values. <br /><br />Example #1: The specified value contains an invalid character (for example, -). <br /><br />Example #2: The specified value contains a double set of curly braces. <br /><br />Example #3: The specified value is missing a closing curly brace.<br /> | Example #1: %{resp_user-agent} <br /><br />Example #2: %{{host}} <br /><br />Example #3: %{host |
| Missing variable name | A NULL value is always returned when a variable is not specified. | %{} |
| Trailing characters | Characters that trail a variable are treated as literal values. <br />The sample value to the right contains a trailing curly brace that will be treated as a literal value. | %{host}} |

## Setting default header values
A default value can be assigned to a header when it meets any of the following conditions:
- Missing/unset
- Set to NULL.

The following table describes how to define a default value.

| Condition | Syntax | Example | Description |
| --------- | ------ | --------| ----------- |
| Set a header to a default value when it meets any of the following conditions: <br /><br />- Missing Header <br /><br />- Header value is set to NULL.| %{Variable:=Value} | %{http_referrer:=unspecified} | The Referrer header will only be set to *unspecified* when it is either missing or set to NULL. No action will take place if it has been set. |
| Set a header to a default value when it is missing. | %{Variable=Value} | %{http_referrer=unspecified} | The Referrer header will only be set to *unspecified* when it is missing. No action will take place if it has been set. |
| Set the header to a default value when it does not meet any of the following conditions: <br /><br />- Missing<br /><br /> - Set to NULL. | %{Variable:+Value} | %{http_referrer:+unspecified} | The Referrer header will only be set to *unspecified* when a value has been assigned to it. No action will take place if it is either missing or set to NULL. |

## Manipulating variables
Variables can be manipulated in the following ways:

- Expanding substrings
- Removing patterns

### Substring expansion
By default, a variable will expand to its full value. Use the following syntax to only expand a substring of the variable's value.

`%<Variable>:<Offset>:<Length>}`

Key information:

- The value assigned to the Offset term determines the starting character of the substring:

     - Positive: The starting character of the substring is calculated from the first character in the string.
     - Zero: The starting character of the substring is the first character in the string.
     - Negative: The starting character of the substring is calculated from the last character in the string.

- The length of the substring is determined by the *Length* term:

     - Omitted: Omitting the Length term allows the substring to include all characters between the starting character and the end of the string.
     - Positive: Determines the length of the substring from the starting character to the right.
     - Negative: Determines the length of the substring from the starting character to the left.

#### Example:

The following example relies on the following sample request URL:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

The following string demonstrates various methods for manipulating variables:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Based on the sample request URL, the above variable manipulation will produce the following value:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### Pattern removal
Text that matches a specific pattern can be removed from either the beginning or the end of a variable's value.

| Syntax | Action |
| ------ | ------ |
| %{Variable#Pattern} | Remove text when the specified pattern is found at the beginning of a variable's value. |
| %{Variable%Pattern} | Remove text when the specified pattern is found at the end of a variable's value. |

#### Example:

In this sample scenario, the *request_uri* variable is set to:

`/800001/myorigin/marketing/product.html?language=en-US`

The following table demonstrates how this syntax works.

| Sample syntax | Results | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Because the variable starts with the pattern, it was replaced. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Because the variable doesn't end with the pattern, there was no change.|

### Find and replace
The find and replace syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/Find/Replace} | Find and replace first occurrence of the specified pattern. |
| %{Variable//Find/Replace} | Find and replace all occurrences of the specified pattern. |
| %{Variable^} |Convert the entire value to uppercase. |
| %{Variable^Find} | Convert the first occurrence of the specified pattern to uppercase. |
| %{Variable,} | Convert the entire value to lowercase. |
| %{Variable,Find} | Convert the first occurrence of the specified pattern to lowercase. |

### Find and rewrite
For a variation on find and replace, use the text that matches the specified pattern when rewriting it. The find and rewrite syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Find, copy, and rewrite all occurrences of the specified pattern. |
| %{Variable/^Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the start of the variable. |
| %{Variable/$Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the end of the variable. |
| %{Variable/Find} | Find and delete all occurrences of the specified pattern. |

Key information:

- Expand text that matches the specified pattern by specifying a dollar sign followed by a whole integer (for example, $1).

- Multiple patterns can be specified. The order in which the pattern is specified determines the integer that will be assigned to it. In the following example, the first pattern matches "www.," the second pattern matches the second-level domain, and the third pattern matches the top-level domain:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- The rewritten value can consist of any combination of text and these placeholders.

    In the previous example, the hostname is rewritten to `cdn.$2.$3:80` (for example, cdn.mydomain.com:80).

- The case of a pattern placeholder (for example, $1) can be modified through the following flags:
     - U: Uppercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Lowercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- An operator must be specified before the pattern. The specified operator determines the pattern-capturing behavior:

     - `=`: Indicates that all occurrences of the specified pattern must be captured and rewritten.
     - `^`: Indicates that only text that starts with the specified pattern will be captured.
     - `$`: Indicates that only text that ends with the specified pattern will be capture.
 
- If you omit the */Rewrite* value, the text that matches the pattern is deleted.

\`{host}le: HTTP variables for Azure CDN rules engine | Microsoft Docs
description: HTTP variables allow you to retrieve HTTP request and response metadata.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''

ms.assetid: 
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus


---
# HTTP variables for Azure CDN rules engine
HTTP variables provide the means through which you can retrieve HTTP request and response metadata. This metadata can then be used to dynamically alter a request or a response. The use of HTTP variables is restricted to the following rules engine features:

- [Cache-Key Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modify Client Request Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modify Client Response Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL Redirect](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## Definitions
The following table describes the supported HTTP variables. A blank value is returned when GEO metadata (for example, postal code) is unavailable for a particular request.


| Name | Variable | Description | Sample value |
| ---- | -------- | ----------- | ------------ |
| ASN (Requester) | %{geo_asnum} | Indicates the requester's AS number. <br /><br />**Deprecated:** %{virt_dst_asnum}. <br />This variable has been deprecated in favor of %{geo_asnum}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | AS15133 |
| City (Requester) | %{geo_city} | Indicates the requester's city. | Los Angeles |
| Continent (Requester) | %{geo_continent} | Indicates the requester's continent through its abbreviation. <br />Valid values are: <br />AF: Africa<br />AS: Asia<br />EU: Europe<br />NA: North America<br />OC: Oceania<br />SA: South America<br /><br />**Deprecated:** %{virt_dst_continent}. <br />This variable has been deprecated in favor of %{geo_continent}. <br />Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable.| N/A |
| Cookie Value | %{cookie_Cookie} | Returns the value corresponding to the cookie key identified by the Cookie term. | Sample Usage: <br />%{cookie__utma}<br /><br />Sample Value:<br />111662281.2.10.1222100123 |
| Country (Requester) | %{geo_country} | Indicates the requester's country of origin through its country code. <br />**Deprecated:** %{virt_dst_country}. <br /><br />This variable has been deprecated in favor of %{geo_country}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | US |
| Designated Market Area (Requester) | %{geo_dma_code} |Indicates the requester's media market by its region code. <br /><br />This field is only applicable to requests that originate from the United States.| 745 |
| HTTP Request Method | %{request_method} | Indicates the HTTP request method. | GET |
| HTTP Status Code | %{status} | Indicates the HTTP status code for the response. | 200 |
| IP Address (Requester) | %{virt_dst_addr} | Indicates the requester's IP address. | 192.168.1.1 |
| Latitude (Requester) | %{geo_latitude} | Indicates the requester's latitude. | 34.0995 |
| Longitude (Requester) | %{geo_longitude} | Indicates the requester's longitude. | -118.4143 |
| Metropolitan Statistical Area (Requester) | %{geo_metro_code} | Indicates the requester's metropolitan area. <br /><br />This field is only applicable to requests that originate from the United States.<br />| 745 |
| Port (Requester) | %{virt_dst_port} | Indicates the requester's ephemeral port. | 55885 |
| Postal Code (Requester) | %{geo_postal_code} | Indicates the requester's postal code. | 90210 |
| Query String Found | %{is_args} | The value for this variable varies according to whether the request contains a query string.<br /><br />- Query String Found: ?<br />- No Query String: NULL | ? |
| Query String Parameter Found | %{is_amp} | The value for this variable varies according to whether the request contains at least one query string parameter.<br /><br />- Parameter Found: &<br />- No Parameters: NULL | & |
| Query String Parameter Value | %{arg_&lt;parameter&gt;} | Returns the value corresponding to the query string parameter identified by the &lt;parameter&gt; term. | Sample Usage: <br />%{arg_language}<br /><br />Sample Query String Parameter: <br />?language=en<br /><br />Sample Value: en |
| Query String Value | %{query_string} | Indicates the entire query string value defined in the request URL. |key1=val1&key2=val2&key3=val3 |
| Referrer Domain | %{referring_domain} | Indicates the domain defined in the Referrer request header. | <www.google.com> |
| Region (Requester) | %{geo_region} | Indicates the requester's region (for example, state or province) through its alphanumeric abbreviation. | CA |
| Request Header Value | %{http_RequestHeader} | Returns the value corresponding to the request header identified by the RequestHeader term. <br /><br />If the name of the request header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent).| Sample Usage: %{http_Connection}<br /><br />Sample Value: Keep-Alive | 
| Request Host | %{host} | Indicates the host defined in the request URL. | <www.mydomain.com> |
| Request Protocol | %{request_protocol} | Indicates the request protocol. | HTTP/1.1 |
| Request Scheme | %{scheme} | Indicates the request scheme. |http |
| Request URI (Relative) | %{request_uri} | Indicates the relative path, including the query string, defined in the request URI. | /marketing/foo.js?loggedin=true |
| Request URI (Relative without query string) | %{uri} | Indicates the relative path to the requested content. <br /><br/>Key information:<br />- This relative path excludes the query string.<br />- This relative path reflects URL rewrites. A URL will be rewritten under the following conditions:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL Rewrite Feature: This feature rewrites the relative path defined in the request URI.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Edge CNAME URL: This type of request is rewritten to the corresponding CDN URL. |/800001/corigin/rewrittendir/foo.js |
| Request URI | %{request} | Describes the request. <br />Syntax: &lt;HTTP method&gt; &lt;relative path&gt; &lt;HTTP protocol&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Response Header Value | %{resp_&lt;ResponseHeader&gt;} | Returns the value corresponding to the response header identified by the &lt;ResponseHeader&gt; term. <br /><br />If the name of the response header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent). | Sample Usage: %{resp_Content_Length}<br /><br />Sample Value: 100 |

## Usage
The following table describes the proper syntax for specifying an HTTP variable.


| Syntax | Example | Description |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Use this syntax to get the entire value corresponding to the specified &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Use this syntax to set the case for the entire value corresponding to the specified  &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | Use a regular expression for &lt;HTTPVariableDelimiterExpression&gt; to replace, delete, or manipulate an HTTP variable's value. |

HTTP variable names only support alphabetic characters and underscores. Convert unsupported characters to underscores.

## Delimiter reference
A delimiter can be specified after an HTTP variable to achieve any of the following effects:

- Transform the value associated with the variable.

     Example: Convert the entire value to lowercase.

- Delete the value associated with the variable.

- Manipulate the value associated with the variable.

     Example: Use regular expressions to change the value associated with the HTTP variable.

The delimiters are described in the following table.

| Delimiter | Description |
| --------- | ----------- |
| := | Indicates that a default value will be assigned to the variable when it is either: <br />- Missing <br />- Set to NULL. |
| :+ | Indicates that a default value will be assigned to the variable when a value has been assigned to it. |
| : | Indicates that a substring of the value assigned to the variable will be expanded. |
| # | Indicates that the pattern specified after this delimiter should be deleted when it is found at the beginning of the value associated with the variable. |
| % | Indicates that the pattern specified after this delimiter should be deleted when it is found at the end of the value associated with the variable. <br />This definition is only applicable when the % symbol is used as a delimiter. |
| / | Delimits an HTTP variable or a pattern. |
| // | Find and replace all instances of the specified pattern. |
| /= | Find, copy, and rewrite all occurrences of the specified pattern. |
| , | Convert the value associated with the HTTP variable to lowercase. |
| ^ | Convert the value associated with the HTTP variable to uppercase. |
| ,, | Convert all instances of the specified character in the value associated with the HTTP variable to lowercase. |
| ^^ | Convert all instances of the specified character in the value associated with the HTTP variable to uppercase. |

## Exceptions
The following table describes circumstances under which the specified text isn't treated as an HTTP variable.

| Condition | Description | Example |
| --------- | ----------- | --------|
| Escaping % symbol | The percentage symbol can be escaped through the use of a backslash. <br />The sample value to the right will be treated as a literal value and not as an HTTP variable.| \%{host} |
| Ismeretlen változók | Üres karakterlánc mindig ismeretlen változók adja vissza. | a(z) % {unknown_variable} |
| Érvénytelen karakterek vagy szintaxis | Érvénytelen karakterek vagy szintaxis tartalmazó változókat konstans értékeket számít. <br /><br />#1. példa: A megadott érték érvénytelen karaktert tartalmaz (például-). <br /><br />#2. példa: A megadott értéket tartalmaz a dupla kapcsos zárójelek. <br /><br />#3. példa: A megadott érték hiányzik egy záró kapcsos zárójelet.<br /> | #1. példa: % {resp_user-ügynök} <br /><br />#2. példa: % {{gazdagép}} <br /><br />#3. példa: % {gazdagép |
| Hiányzik a változó neve. | NULL érték mindig adja vissza, ha a változó értéke nincs megadva. | %{} |
| Záró karakterek | Egy változó végén karaktereket konstansértékekkel kell kezelni. <br />Jobb minta érték tartalmaz egy záró kapcsos zárójelet, amely egy literálérték, lesznek kezelve. | %{host}} |

## <a name="setting-default-header-values"></a>Alapértelmezett fejléc értékeinek beállítása
Alapértelmezett érték hozzárendelheti egy fejléc a következő feltételek bármelyikének teljesülésekor:
- Missing/unset
- NULL értékű.

A következő táblázat ismerteti, hogyan adhat meg egy alapértelmezett értéket.

| Állapot | Szintaxis | Példa | Leírás |
| --------- | ------ | --------| ----------- |
| Egy alapértelmezett értékre állítva egy fejléc a következő feltételek bármelyikének teljesülésekor: <br /><br />– Hiányzik egy fejléc <br /><br />-Fejléc értéke NULL értékre van beállítva.| %{Variable:=Value} | %{http_referrer:=unspecified} | A hivatkozó fejléc csak állítja be *meghatározatlan* hiányzik vagy NULL értékre. Nincs művelet kerül sor, ha van beállítva. |
| Ha ez hiányzik, állítsa be egy fejléc egy alapértelmezett értéket. | %{Variable=Value} | %{http_referrer=unspecified} | A hivatkozó fejléc csak állítja be *meghatározatlan* megadva. Nincs művelet kerül sor, ha van beállítva. |
| Állítsa be a fejléc egy alapértelmezett értéket, ha nem teljesíti az alábbi feltételek bármelyike: <br /><br />– Hiányzik<br /><br /> -NULL értékű. | %{Variable:+Value} | %{http_referrer:+unspecified} | A hivatkozó fejléc csak állítja be *meghatározatlan* mikor értéket rendelték hozzá. Nincs művelet kerül sor, ha hiányzik vagy null. |

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
     - Zero: A kezdő karakter a karakterláncrész a karakterlánc első karaktere.
     - Negatív: A kezdő karakter a karakterláncrész kiszámítása az utolsó karakter a karakterláncban.

- A karakterláncrész hossza határozza meg a *hossza* kifejezés:

     - Nincs megadva: Hossz kifejezés felsorolhatja lehetővé teszi, hogy a keresendő karakterláncrészletet tartalmazza az összes karaktert a karakterlánc végén és a kezdő karakter között.
     - Pozitív: Meghatározza, hogy a kezdő karakter jobb karakterláncrész hossza.
     - Negatív: Meghatározza, hogy a kezdő karakter a bal oldali karakterláncrész hossza.

#### <a name="example"></a>Példa:

Az alábbi példa a következő minta kérelem URL-címe támaszkodik:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

A következő karakterláncot a változók kezelésére szolgáló különböző módszereket mutat be:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

A minta-kérelem URL-cím alapján, a fenti változó adatkezelési állítja elő a következő értéket:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Minta eltávolítása
Szöveg, amely megfelel egy adott minta elején vagy végén egy változó értékének távolíthatja el.

| Szintaxis | Action |
| ------ | ------ |
| %{Variable#Pattern} | Távolítsa el a szöveget, ha a megadott karakterlánc megtalálható a változó értékének elejére. |
| %{Variable%Pattern} | Távolítsa el a szöveg, ha a megadott karakterlánc megtalálható a változó értékét a végén. |

#### <a name="example"></a>Példa:

Minta ebben a forgatókönyvben a *request_uri* változó értéke:

`/800001/myorigin/marketing/product.html?language=en-US`

Az alábbi táblázat azt ismerteti, hogyan működik ez a szintaxis.

| Mintául szolgáló szintaxis | Results (Eredmények) | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | A változó a minta kezdődik, mert a lett cserélve. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | A változó nem végződhet a mintának, mert nem változott.|

### <a name="find-and-replace"></a>Keresés és csere
A Keresés és csere szintaxisa a következő táblázatban leírt.

| Szintaxis | Action |
| ------ | ------ |
| %{Variable/find/replace} | Keresse meg, és cserélje le a megadott minta első előfordulása. |
| %{Variable//find/replace} | Keresse meg, és cserélje le a megadott minta összes előfordulását. |
| %{Variable^} |A teljes érték átalakítása nagybetűssé. |
| %{Variable^Find} | A megadott minta a első előfordulása átalakítása nagybetűvé. |
| A(z) % {, változó} | A teljes érték átalakítása kisbetűvé. |
| A(z) % {változó, keresés} | A megadott minta a első előfordulása átalakítása kisbetűvé. |

### <a name="find-and-rewrite"></a>A Keresés és átírás
Keresés és csere egy variációja a szöveg, amely megfelel a megadott minta újraírását, amikor használja. A Keresés és újraírási szintaxis a következő táblázatban leírt.

| Szintaxis | Action |
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
     - U: A kibontott érték nagybetűk.

         Mintául szolgáló szintaxis:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: A kibontott érték kisbetűs.

         Mintául szolgáló szintaxis:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- Az operátornak meg kell adni a minta előtt. A megadott operátor határozza meg a minta-rögzítés viselkedés:

     - `=`: Azt jelzi, hogy a megadott minta összes előfordulását rögzíteni kell, és átírása.
     - `^`: Azt jelzi, hogy csak a megadott minta kezdetű szöveg lesz rögzíthetők.
     - `$`: Azt jelzi, hogy csak a megadott minta végződő szöveg lesz rögzítése.
 
- Ha kihagyja a */Újraírási* érték, amely megfelel a mintának a szöveg törlődik.


