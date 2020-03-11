---
title: Azure API Management tartományok közötti házirendek | Microsoft Docs
description: Ismerkedjen meg az Azure API Managementban használható tartományok házirendjeivel.
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
ms.openlocfilehash: db64a2f64c592a62f621355047a7bc9844d66457
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375074"
---
# <a name="api-management-cross-domain-policies"></a>Az API Management tartományközi házirendjei
Ez a témakör az alábbi API Management szabályzatokra mutató hivatkozást tartalmaz. A házirendek hozzáadásával és konfigurálásával kapcsolatos információkért lásd: [szabályzatok API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CrossDomainPolicies"></a>Tartományok közötti házirendek

- Tartományok [közötti hívások engedélyezése](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – elérhetővé teszi az API-t az Adobe Flash és a Microsoft Silverlight böngésző alapú ügyfeleitől.
- [CORS](api-management-cross-domain-policies.md#CORS) – a több eredetű erőforrás-megosztási (CORS) támogatás egy művelethez vagy API-hoz, amely lehetővé teszi a tartományok közötti hívásokat a böngészőalapú ügyfelektől.
- [Jsnop támogatással](api-management-cross-domain-policies.md#JSONP) – egy művelet vagy API számára lehetővé teszi a JSON-t a kitöltési (jsnop támogatással) támogatással, hogy engedélyezze a tartományok közötti hívásokat a JavaScript böngésző alapú ügyfelektől.

## <a name="AllowCrossDomainCalls"></a>Tartományok közötti hívások engedélyezése
Az `cross-domain` házirend segítségével elérhetővé teheti az API-t az Adobe Flash és a Microsoft Silverlight böngésző alapú ügyfeleitől.

### <a name="policy-statement"></a>Szabályzati utasítás

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

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|tartományok közötti|Gyökérelem. A gyermek elemeknek meg kell felelniük az [Adobe tartományok közötti házirend fájljának specifikációjának](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Igen|

### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

- **Házirend fejezetei:** bejövő
- **Házirend-hatókörök:** az összes hatókör

## <a name="CORS"></a>CORS
A `cors` házirend egy művelethez vagy API-hoz ad hozzá több eredetű erőforrás-megosztási (CORS) támogatást, amely lehetővé teszi a tartományok közötti hívásokat a böngészőalapú ügyfelektől.

A CORS lehetővé teszi, hogy a böngésző és a kiszolgáló interakcióba lépjen, és meghatározza, hogy engedélyezi-e a különböző eltérő eredetű kérelmeket (például az XMLHttpRequest-hívásokat weblapokon a JavaScriptből más tartományokra). Ez nagyobb rugalmasságot tesz lehetővé, mint az azonos eredetű kérések engedélyezése, de biztonságosabb, mint az összes eltérő eredetű kérelem.

### <a name="policy-statement"></a>Szabályzati utasítás

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
Ez a példa azt mutatja be, hogyan lehet támogatni a repülés előtti kérelmeket, például az egyéni fejlécekkel vagy a GET és a POST metódustól eltérő módszerekkel. Az egyéni fejlécek és a további HTTP-műveletek támogatásához használja a `allowed-methods` és `allowed-headers` szakaszt az alábbi példában látható módon.

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

|Name (Név)|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|CORS|Gyökérelem.|Igen|N/A|
|engedélyezett – eredetek|Olyan `origin` elemeket tartalmaz, amelyek leírják a tartományok közötti kérelmek engedélyezett eredetét. a `allowed-origins` tartalmazhat egyetlen `origin` elemet is, amely meghatározza, hogy az egyes forrásokhoz, illetve egy vagy több `origin` URI-t tartalmazó elemhez `*` adjon meg.|Igen|N/A|
|forrás|Az érték lehet `*` az összes eredet engedélyezéséhez, vagy egy olyan URI-t, amely egyetlen forrást határoz meg. Az URI-nak tartalmaznia kell egy sémát, egy gazdagépet és egy portot.|Igen|Ha a portot kihagyja egy URI-ban, a 80-es portot használja a rendszer a HTTP protokollhoz, és a 443-es portot használja a HTTPS protokollhoz.|
|engedélyezett – metódusok|Ez az elem akkor szükséges, ha a GET vagy a POST metódustól eltérő módszerek engedélyezettek. A támogatott HTTP-műveleteket megadó `method` elemeket tartalmazza.|Nem|Ha ez a szakasz nem létezik, a GET és a POST is támogatott.|
|method|HTTP-műveletet ad meg.|Legalább egy `method` elemet meg kell adni, ha a `allowed-methods` szakasz megtalálható.|N/A|
|engedélyezett – fejlécek|Ez az elem `header` elemeket tartalmaz, amelyek megadják a kérésben szereplő fejlécek nevét.|Nem|N/A|
|fejlécek közzététele|Ez az elem `header` elemeket tartalmaz, amelyek megadják az ügyfél által elérhető fejlécek nevét.|Nem|N/A|
|header|Megadja a fejléc nevét.|Legalább egy `header` elemet meg kell adni `allowed-headers` vagy `expose-headers`, ha a szakasz jelen van.|N/A|

### <a name="attributes"></a>Attribútumok

|Name (Név)|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|hitelesítő adatok engedélyezése|Az elővizsgálati válasz `Access-Control-Allow-Credentials` fejléce az attribútum értékére lesz állítva, és hatással van arra, hogy az ügyfél képes legyen hitelesítő adatokat küldeni a tartományok közötti kérelmekben.|Nem|false|
|Elővizsgálat – eredmény-Max-Age|Az elővizsgálati válasz `Access-Control-Max-Age` fejléce az attribútum értékére lesz állítva, és hatással van a felhasználói ügynöknek a repülés előtti válasz gyorsítótárazására.|Nem|0|

### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

- **Házirend fejezetei:** bejövő
- **Házirend-hatókörök:** az összes hatókör

## <a name="JSONP"></a>JSNOP támogatással
A `jsonp` szabályzat a JSON-t (JSNOP támogatással) támogatja egy művelet vagy egy API számára, amely lehetővé teszi a tartományok közötti hívásokat a JavaScript böngésző alapú ügyfelektől. A JSNOP támogatással egy JavaScript-programokban használt metódus, amely egy másik tartományban lévő kiszolgálótól kér le adatait. A JSNOP támogatással megkerüli a legtöbb webböngésző által kényszerített korlátozásokat, ahol a weblapokhoz való hozzáférésnek ugyanabban a tartományban kell lennie.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Példa

```xml
<jsonp callback-parameter-name="cb" />
```

Ha a metódust a visszahívási paraméter nélkül hívja meg? CB = XXX – egyszerű JSON-t ad vissza (függvényhívás burkolója nélkül).

Ha hozzáadja a visszahívási paramétert `?cb=XXX` visszaadja a JSNOP támogatással eredményét, becsomagolva az eredeti JSON-eredményeket a visszahívási függvény köré, például `XYZ('<json result goes here>');`

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|jsnop támogatással|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Name (Név)|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|visszahívás – paraméter – név|A tartományok közötti JavaScript-függvény hívása a teljes tartománynévvel, ahol a függvény található.|Igen|N/A|

### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

- **Házirend fejezetei:** kimenő
- **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>Következő lépések

További információ a házirendek használatáról:

+ [Szabályzatok API Management](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ Házirend- [hivatkozás](api-management-policy-reference.md) a szabályzat-utasítások és azok beállításainak teljes listájához
+ [Házirend-minták](policy-samples.md)
