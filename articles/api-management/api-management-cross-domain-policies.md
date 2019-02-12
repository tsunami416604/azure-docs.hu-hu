---
title: Az Azure API Management tartományközi szabályzatai |} A Microsoft Docs
description: További információ a használható az Azure API Management tartományközi házirendek.
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
ms.openlocfilehash: ecbc1af97ce5ed158138f2bcf47f5729842c0fe9
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56098534"
---
# <a name="api-management-cross-domain-policies"></a>Az API Management tartományközi házirendjei
Ez a témakör egy hivatkozást kínál a következő az API Management házirendek. Hozzáadása és házirendek konfigurálásával kapcsolatos tudnivalókat lásd: [az API Management házirendek](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="CrossDomainPolicies"></a> Tartományközi házirendek

- [Lehetővé teszi a tartományok közötti hívások](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – az API-hoz elérhető lesz az Adobe Flash és a Microsoft Silverlight webböngésző-alapú ügyfelekről.
- [A CORS](api-management-cross-domain-policies.md#CORS) -hozzáadja az eltérő eredetű erőforrások megosztása (CORS) támogatásával egy műveletet vagy API-t lehetővé teszi a tartományok közötti hívások webböngésző-alapú ügyfelekről.
- [JSNOP](api-management-cross-domain-policies.md#JSONP) -JSON ad hozzá egy műveletet vagy API-t lehetővé teszi a tartományok közötti hívások JavaScript-ügyfelekből böngészőalapú padding (JSNOP) támogatásával.

## <a name="AllowCrossDomainCalls"></a> Lehetővé teszi a tartományok közötti hívások
Használja a `cross-domain` szabályzatba, hogy elérhető-e az API-t az Adobe Flash és a Microsoft Silverlight webböngésző-alapú ügyfelekről.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<cross-domain>
    <!-Policy configuration is in the Adobe cross-domain policy file format,
        see https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->
</cross-domain>
```

### <a name="example"></a>Példa

```xml
<cross-domain>
    <cross-domain-policy>
        <allow-http-request-headers-from domain='*' headers='*' />
    </cross-domain-policy>
</cross-domain>
```

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Szükséges|
|----------|-----------------|--------------|
|cross-domain|A gyökérelem. Gyermekelemek meg kell felelnie a [Adobe tartományok közötti házirend fájl meghatározásának](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Igen|

### <a name="usage"></a>Használat
Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **A házirend-szakaszok:** bejövő
- **A házirend-hatókörök:** globális

## <a name="CORS"></a> CORS
A `cors` házirend hozzáadja az eltérő eredetű erőforrások megosztása (CORS) támogatásával egy műveletet vagy API-t lehetővé teszi a tartományok közötti hívások webböngésző-alapú ügyfelekről.

A CORS lehetővé teszi, hogy a böngésző és a egy kiszolgálót, és annak meghatározására, hogy az adott eltérő eredetű kérelmek (azaz XMLHttpRequests hívások JavaScript-alapú webes más tartományok) engedélyezése. Ez lehetővé teszi, hogy csak az azonos eredethez kérésének engedélyezése-nál nagyobb rugalmasságot, de biztonságosabb, mint az összes eltérő eredetű kérések engedélyezése.

### <a name="policy-statement"></a>Házirendutasítás

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
Ez a példa bemutatja, hogyan repülés kérések, például az egyéni fejlécek, vagy nem GET vagy POST metódusok támogatásához. Egyéni fejlécek és további HTTP-műveletek támogatása érdekében használja a `allowed-methods` és `allowed-headers` szakaszokban a következő példában látható módon.

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

|Name (Név)|Leírás|Szükséges|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|a cors|A gyökérelem.|Igen|–|
|engedélyezett eredetek|Tartalmaz `origin` elemek, amelyek ismertetik a engedélyezett eredetek a tartományok közötti kéréseket. `allowed-origins` is tartalmazhat egyetlen `origin` megadó eleme `*` , hogy minden forrás, vagy egy vagy több `origin` egy URI-t tartalmazó elemeket.|Igen|–|
|forrás|Az érték lehet `*` , hogy az összes forrás, vagy adja meg egy egyetlen forrás URI-t. Az URI-t tartalmaznia kell a sémát, a gazdagépet és a portot.|Igen|Ha a port nincs megadva az URI-t, 80-as portot használja HTTP és HTTPS esetén 443-as port használatos.|
|Engedélyezett metódusok|Ez az elem akkor szükséges, ha a módszerek nem GET vagy POST engedélyezettek. Tartalmaz `method` elemeket, adja meg a támogatott HTTP-műveleteket.|Nem|Ha ez a szakasz nem található, a GET és POST használata támogatott.|
|method|Adja meg a HTTP-műveletet.|Legalább egy `method` elem azért szükséges, ha a `allowed-methods` szakaszban szerepel.|–|
|allowed-headers|Tento element obsahuje `header` elemeket a fejlécek, a kérelemben szereplő nevének megadása.|Nem|–|
|expose-headers|Tento element obsahuje `header` elemek megadása, amely az ügyfél által elérhető lesz a fejlécek nevét.|Nem|–|
|header|A fejléc nevét adja meg.|Legalább egy `header` elemhez kötelező megadni `allowed-headers` vagy `expose-headers` Ha a szakaszban található.|–|

### <a name="attributes"></a>Attribútumok

|Name (Név)|Leírás|Szükséges|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|allow-credentials|A `Access-Control-Allow-Credentials` a elővizsgálati válaszok fejlécének Ez az attribútum értékét állítja be, és hatással az ügyfél hitelesítő adatai a tartományok közötti kérelmek elküldéséhez.|Nem|false|
|ellenőrzés-eredmény-max-age|A `Access-Control-Max-Age` a elővizsgálati válaszok fejlécének Ez az attribútum értékét állítja be, és a felhasználói ügynök képességét, gyorsítótár repülés válasz.|Nem|0|

### <a name="usage"></a>Használat
Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **A házirend-szakaszok:** bejövő
- **A házirend-hatókörök:** globális, API-művelet

## <a name="JSONP"></a> JSNOP
A `jsonp` egy műveletet, vagy lehetővé teszi a tartományok közötti hívások JavaScript-ügyfelekből webböngésző-alapú API-házirend hozzáadja az padding (JSNOP) támogatásával JSON. JSNOP a JavaScript-programok használt adatokat kérjen egy kiszolgálóról egy másik tartományban módszer. JSNOP megkerüli a korlátozás kényszeríti ki a legtöbb webböngésző, ahol a weblapok elérése ugyanabban a tartományban kell lennie.

### <a name="policy-statement"></a>Házirendutasítás

```xml
<jsonp callback-parameter-name="callback function name" />
```

### <a name="example"></a>Példa

```xml
<jsonp callback-parameter-name="cb" />
```

Ha a visszahívási paraméter nélkül a metódust hívja? cb = XXX egyszerű JSON (nélkül egy függvény hívása burkolót) adja vissza.

Ha a visszahívási paraméter `?cb=XXX` JSNOP eredményt ad vissza, a visszahívási függvény adatokat, mint az eredeti JSON alkalmazásburkoló `XYZ('<json result goes here>');`

### <a name="elements"></a>Elemek

|Name (Név)|Leírás|Szükséges|
|----------|-----------------|--------------|
|jsonp|A gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Name (Név)|Leírás|Szükséges|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|callback-parameter-name|A tartományok közötti JavaScript függvény hívásához előtaggal van ellátva a teljesen minősített tartománynevét, amelyen a függvény található.|Igen|–|

### <a name="usage"></a>Használat
Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

- **A házirend-szakaszok:** kimenő
- **A házirend-hatókörök:** globális, termék, API-művelet

## <a name="next-steps"></a>További lépések

Házirendek használata további információkért lásd:

+ [Az API Management házirendek](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ [Házirend-referencia](api-management-policy-reference.md) házirend-utasítások és a beállítások teljes listáját
+ [A házirend-minták](policy-samples.md)   
