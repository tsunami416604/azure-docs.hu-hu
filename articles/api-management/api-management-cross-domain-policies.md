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
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 86c61679a73f03f7e54bba746107685796ec07c9
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442320"
---
# <a name="api-management-cross-domain-policies"></a>Az API Management tartományközi házirendjei
Ez a témakör az alábbi API Management szabályzatokra mutató hivatkozást tartalmaz. A házirendek hozzáadásával és konfigurálásával kapcsolatos információkért lásd: [szabályzatok API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CrossDomainPolicies"></a>Tartományok közötti házirendek

- Tartományok [közötti hívások engedélyezése](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – elérhetővé teszi az API-t az Adobe Flash és a Microsoft Silverlight böngésző alapú ügyfeleitől.
- [CORS](api-management-cross-domain-policies.md#CORS) – a több eredetű erőforrás-megosztási (CORS) támogatás egy művelethez vagy API-hoz, amely lehetővé teszi a tartományok közötti hívásokat a böngészőalapú ügyfelektől.
- [Jsnop támogatással](api-management-cross-domain-policies.md#JSONP) – egy művelet vagy API számára lehetővé teszi a JSON-t a kitöltési (jsnop támogatással) támogatással, hogy engedélyezze a tartományok közötti hívásokat a JavaScript böngésző alapú ügyfelektől.

## <a name="AllowCrossDomainCalls"></a>Tartományok közötti hívások engedélyezése
`cross-domain` A szabályzat segítségével elérhetővé teheti az API-t az Adobe Flash és a Microsoft Silverlight böngésző alapú ügyfeleitől.

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
Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

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
Ez a példa azt mutatja be, hogyan lehet támogatni a repülés előtti kérelmeket, például az egyéni fejlécekkel vagy a GET és a POST metódustól eltérő módszerekkel. Az egyéni fejlécek és a további http-műveletek támogatásához `allowed-methods` használja `allowed-headers` a és a szakaszt az alábbi példában látható módon.

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
|CORS|Gyökérelem.|Igen|–|
|engedélyezett – eredetek|Olyan `origin` elemeket tartalmaz, amelyek leírják a tartományok közötti kérelmek engedélyezett eredetét. `allowed-origins`tartalmazhat egy `origin` olyan elemet, `*` amely engedélyezi bármely forrás használatát, vagy egy vagy több `origin` URI-t tartalmazó elemet.|Igen|–|
|forrás|Az érték `*` lehet az összes eredet engedélyezése, vagy egy URI, amely egyetlen forrást határoz meg. Az URI-nak tartalmaznia kell egy sémát, egy gazdagépet és egy portot.|Igen|Ha a portot kihagyja egy URI-ban, a 80-es portot használja a rendszer a HTTP protokollhoz, és a 443-es portot használja a HTTPS protokollhoz.|
|engedélyezett – metódusok|Ez az elem akkor szükséges, ha a GET vagy a POST metódustól eltérő módszerek engedélyezettek. A `method` támogatott http-műveleteket megadó elemeket tartalmazza.|Nem|Ha ez a szakasz nem létezik, a GET és a POST is támogatott.|
|metódus|HTTP-műveletet ad meg.|Ha a `method` `allowed-methods` szakasz jelen van, legalább egy elem megadása kötelező.|–|
|engedélyezett – fejlécek|Ez az elem `header` olyan elemeket tartalmaz, amelyek megadják a kérésben szerepeltethető fejlécek nevét.|Nem|–|
|fejlécek közzététele|Ez az elem `header` olyan elemeket tartalmaz, amelyek az ügyfél által elérhető fejlécek nevét határozzák meg.|Nem|–|
|header|Megadja a fejléc nevét.|Legalább egy `header` elemet meg kell adni, `allowed-headers` vagy `expose-headers` ha a szakasz jelen van.|–|

### <a name="attributes"></a>Attribútumok

|Name (Név)|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|hitelesítő adatok engedélyezése|Az elővizsgálati válasz fejléce az attribútum értékére lesz állítva, és hatással van arra, hogy az ügyfél képes legyen hitelesítő adatokat küldeni a tartományok közötti kérelmekben. `Access-Control-Allow-Credentials`|Nem|false|
|Elővizsgálat – eredmény-Max-Age|Az elővizsgálati válasz fejléce az attribútum értékére lesz állítva, és hatással van a felhasználói ügynöknek a repülés előtti válasz gyorsítótárazására. `Access-Control-Max-Age`|Nem|0|

### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

- **Házirend fejezetei:** bejövő
- **Házirend-hatókörök:** az összes hatókör

## <a name="JSONP"></a>JSNOP TÁMOGATÁSSAL
A `jsonp` szabályzat egy művelet vagy egy API számára lehetővé teszi a JSON-t a kitöltő (jsnop támogatással) támogatással, hogy engedélyezze a tartományok közötti hívásokat a JavaScript böngésző alapú ügyfelektől. A JSNOP támogatással egy JavaScript-programokban használt metódus, amely egy másik tartományban lévő kiszolgálótól kér le adatait. A JSNOP támogatással megkerüli a legtöbb webböngésző által kényszerített korlátozásokat, ahol a weblapokhoz való hozzáférésnek ugyanabban a tartományban kell lennie.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Példa

```xml
<jsonp callback-parameter-name="cb" />
```

Ha a metódust a visszahívási paraméter nélkül hívja meg? CB = XXX – egyszerű JSON-t ad vissza (függvényhívás burkolója nélkül).

Ha hozzáadja a visszahívási `?cb=XXX` paramétert, a visszaadja a jsnop támogatással eredményét, és becsomagolja az eredeti JSON-eredményeket a visszahívási függvény köré, például:`XYZ('<json result goes here>');`

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Kötelező|
|----------|-----------------|--------------|
|jsnop támogatással|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Name (Név)|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|visszahívás – paraméter – név|A tartományok közötti JavaScript-függvény hívása a teljes tartománynévvel, ahol a függvény található.|Igen|–|

### <a name="usage"></a>Használat
Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

- **Házirend fejezetei:** kimenő
- **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>További lépések

További információ a házirendek használatáról:

+ [Szabályzatok API Management](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ Házirend- [hivatkozás](api-management-policy-reference.md) a szabályzat-utasítások és azok beállításainak teljes listájához
+ [Házirend-minták](policy-samples.md)
