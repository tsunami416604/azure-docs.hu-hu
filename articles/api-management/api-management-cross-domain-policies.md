---
title: Azure API Management tartományok közötti szabályzatok | Microsoft dokumentumok
description: Ismerje meg az Azure API Managementben használható tartományok közötti szabályzatokat.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: b72abf4e208c57987375a105865046f194460058
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265986"
---
# <a name="api-management-cross-domain-policies"></a>Az API Management tartományközi házirendjei
Ez a témakör a következő API Management-házirendek hivatkozási alapként szolgál. A házirendek hozzáadásáról és konfigurálásáról az [API-kezelés házirendjei](https://go.microsoft.com/fwlink/?LinkID=398186)című témakörben talál további információt.

## <a name="cross-domain-policies"></a><a name="CrossDomainPolicies"></a>Tartományok közötti házirendek

- Tartományok közötti hívások engedélyezése – Elérhetővé teszi az [API-t](api-management-cross-domain-policies.md#AllowCrossDomainCalls) az Adobe Flash és a Microsoft Silverlight böngészőalapú ügyfelektől.
- [CORS](api-management-cross-domain-policies.md#CORS) – Több forrásból származó erőforrás-megosztási (CORS) támogatást ad egy művelethez vagy egy API-hoz, hogy lehetővé tegye a tartományok közötti hívásokat a böngészőalapú ügyfelektől.
- [JSONP](api-management-cross-domain-policies.md#JSONP) – JSON-t ad hozzá egy művelethez vagy EGY API-hoz, amely lehetővé teszi a JavaScript böngészőalapú ügyfelek tartományok közötti hívásait.

## <a name="allow-cross-domain-calls"></a><a name="AllowCrossDomainCalls"></a>Tartományok közötti hívások engedélyezése
A `cross-domain` szabályzat segítségével elérhetővé teheti az API-t az Adobe Flash és a Microsoft Silverlight böngészőalapú ügyfelekről.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>Példa

```xml
<cross-domain>
    <cross-domain>
        <allow-http-request-headers-from domain='*' headers='*' />
    </cross-domain>
</cross-domain>
```

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|tartományok közötti|Gyökérelem. A gyermekelemeknek meg kell felelniük az [Adobe tartományok közötti házirendfájl-specifikációjának.](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html)|Igen|

### <a name="usage"></a>Használat
Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

- **Házirendszakaszok:** bejövő
- **Házirend-hatókörök:** az összes hatókör

## <a name="cors"></a><a name="CORS"></a>KOROS
A `cors` házirend hozzáadja a több forrásból származó erőforrás-megosztási (CORS) támogatást egy művelethez vagy egy API-hoz, amely lehetővé teszi a tartományok közötti hívásokat a böngészőalapú ügyfelektől.

A CORS lehetővé teszi a böngésző és a kiszolgáló számára, hogy kommunikáljanak egymással, és meghatározza, hogy engedélyezi-e az egyes kereszteredjelöléseket (azaz XMLHttpRequests hívásokat JavaScriptből egy weboldalon más tartományokba). Ez nagyobb rugalmasságot tesz lehetővé, mint az azonos eredetű kérelmek engedélyezése, de biztonságosabb, mint az összes kereszteredetű kérelem engedélyezése.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<cors allow-credentials="false|true">
    <allowed-origins>
        <origin>origin uri</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="number of seconds">
        <method>http verb</method>
    </allowed-methods>
    <allowed-headers>
        <header>header name</header>
    </allowed-headers>
    <expose-headers>
        <header>header name</header>
    </expose-headers>
</cors>
```

### <a name="example"></a>Példa
Ez a példa bemutatja, hogyan támogathatja a repülés előtti kérelmeket, például az egyéni fejlécekkel vagy a GET-től és a POST-tól eltérő módszerekkel. Az egyéni fejlécek és további HTTP-műveletek `allowed-headers` támogatásához használja a szakaszokat az `allowed-methods` alábbi példában látható módon.

```xml
<cors allow-credentials="true">
    <allowed-origins>
        <!-- Localhost useful for development -->
        <origin>http://localhost:8080/</origin>
        <origin>http://example.com/</origin>
    </allowed-origins>
    <allowed-methods preflight-result-max-age="300">
        <method>GET</method>
        <method>POST</method>
        <method>PATCH</method>
        <method>DELETE</method>
    </allowed-methods>
    <allowed-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
        <header>x-zumo-version</header>
        <header>x-zumo-auth</header>
        <header>content-type</header>
        <header>accept</header>
    </allowed-headers>
    <expose-headers>
        <!-- Examples below show Azure Mobile Services headers -->
        <header>x-zumo-installation-id</header>
        <header>x-zumo-application</header>
    </expose-headers>
</cors>
```

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|cors (korok)|Gyökérelem.|Igen|N/A|
|engedélyezett eredetű|Olyan `origin` elemeket tartalmaz, amelyek leírják a tartományok közötti kérelmek megengedett eredetét. `allowed-origins`tartalmazhat egyetlen `origin` elemet, amely `*` megadja az origó engedélyezését, vagy egy vagy több, `origin` URI-t tartalmazó elemet.|Igen|N/A|
|forrás (origin)|Az érték lehet `*` az összes eredet engedélyezéséhez, vagy egy egyetlen origót megadva URI. Az URI-nak tartalmaznia kell egy sémát, állomást és portot.|Igen|Ha a port ot kihagyja egy URI-ban, a 80-as portot a HTTP-hez, a 443-as portot pedig a HTTPS-hez használja a rendszer.|
|engedélyezett módszerek|Erre az elemre akkor van szükség, ha a GET vagy a POST metódustól eltérő módszerek is engedélyezettek. A `method` támogatott HTTP-műveleteket meghatározó elemeket tartalmazza. Az `*` érték az összes metódust jelzi.|Nem|Ha ez a szakasz nem található meg, a GET és a POST támogatott.|
|method|HTTP-műveletet ad meg.|Legalább egy `method` elemre szükség `allowed-methods` van, ha a szakasz jelen van.|N/A|
|engedélyezett fejlécek|Ez az `header` elem olyan elemeket tartalmaz, amelyek a kérelemben szereplő fejlécek nevét határozzák meg.|Nem|N/A|
|kitett fejlécek|Ez az `header` elem olyan elemeket tartalmaz, amelyek az ügyfél által elérhető fejlécek nevét határozzák meg.|Nem|N/A|
|header|Fejlécnevet ad meg.|Legalább egy `header` elem `allowed-headers` szükséges, `expose-headers` vagy ha a szakasz jelen van.|N/A|

### <a name="attributes"></a>Attribútumok

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|engedélyezés-hitelesítő adatok|Az `Access-Control-Allow-Credentials` elővizsgálati válasz fejléce ennek az attribútumnak az értékére lesz állítva, és befolyásolja az ügyfél azon képességét, hogy hitelesítő adatokat küldjön a tartományok közötti kérelmekben.|Nem|hamis|
|elővizsgálat-eredmény-max-kor|Az `Access-Control-Max-Age` elővizsgálati válasz fejléce ennek az attribútumnak az értékére lesz állítva, és befolyásolja a felhasználói ügynök képességét a repülés előtti válasz gyorsítótárazására.|Nem|0|

### <a name="usage"></a>Használat
Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

- **Házirendszakaszok:** bejövő
- **Házirend-hatókörök:** az összes hatókör

## <a name="jsonp"></a><a name="JSONP"></a>Jsonp
A `jsonp` házirend json tetszés (JSONP) támogatást ad hozzá egy művelethez vagy egy API-hoz, amely lehetővé teszi a JavaScript böngészőalapú ügyfelek tartományok közötti hívásait. A JSONP egy olyan módszer, amelyet a JavaScript-programokban használnak arra, hogy adatokat kérjenek egy másik tartományban lévő kiszolgálóról. A JSONP megkerüli a legtöbb olyan webböngésző által kikényszerített korlátozást, ahol a weboldalakhoz való hozzáférésnek ugyanabban a tartományban kell lennie.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Példa

```xml
<jsonp callback-parameter-name="cb" />
```

Ha a metódust a visszahívási paraméter nélkül hívja meg ?cb=XXX, akkor egyszerű JSON-t ad vissza (függvényhívás-burkoló nélkül).

Ha hozzáadja a `?cb=XXX` visszahívási paramétert, akkor jSONP-eredményt ad vissza, és az eredeti JSON-eredményeket a visszahívási függvény köré csomagolja, például`XYZ('<json result goes here>');`

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|Jsonp|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|visszahívás-paraméter-név|A tartományközi JavaScript függvény hívása előtaggal rendelkezik azzal a teljesen minősített tartománynévvel, ahol a függvény található.|Igen|N/A|

### <a name="usage"></a>Használat
Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

- **Házirendszakaszok:** kimenő
- **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>További lépések

A házirendekkel kapcsolatos további információkért lásd:

+ [Szabályzatok az API Managementben](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ [Házirend-útmutató](api-management-policy-reference.md) a házirend-utasítások és beállításaik teljes listájához
+ [Házirendminták](policy-samples.md)
