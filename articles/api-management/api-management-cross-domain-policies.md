---
title: "Az Azure API Management tartományközi házirendjei |} Microsoft Docs"
description: "További tudnivalók a tartományok közötti házirendek az Azure API Management használható."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 05b25ffad4a91859932cd53475d82b11bf3e43e5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="api-management-cross-domain-policies"></a>Az API Management tartományközi házirendjei
Ez a témakör egy hivatkozást a következő API-felügyeleti házirendek. Hozzáadása és házirendek konfigurálásával kapcsolatos tudnivalókat lásd: [házirendek az API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CrossDomainPolicies"></a>Kereszt-tartományi házirendek  
  
-   [Lehetővé teszi a tartományok közötti hívások](api-management-cross-domain-policies.md#AllowCrossDomainCalls) -elérhetővé válnak az API-t az Adobe Flash és a Microsoft Silverlight webböngésző-alapú ügyfelektől.  
  
-   [A CORS](api-management-cross-domain-policies.md#CORS) -hozzáadja az eltérő eredetű erőforrások megosztása (CORS) a támogatási művelet vagy API lehetővé teszi a tartományok közötti hívások webböngésző-alapú ügyfelekről.  
  
-   [JSONP](api-management-cross-domain-policies.md#JSONP) -JSON ad hozzá egy műveletet, vagy lehetővé teszi a tartományok közötti hívások JavaScript-ügyfelekből webböngésző-alapú API kitöltési (JSONP) támogatással.  
  
##  <a name="AllowCrossDomainCalls"></a>Lehetővé teszi a tartományok közötti hívások  
 Használja a `cross-domain` házirend ahhoz, hogy elérhető az API-t az Adobe Flash és a Microsoft Silverlight webböngésző-alapú ügyfelektől.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<cross-domain>  
   <!-Policy configuration is in the Adobe cross-domain policy file format,   
      see http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->  
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
  
|Név|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|tartományok közötti|A gyökérelem. Gyermekelemek meg kell felelnie a [Adobe tartományok közötti házirend specifikációnak](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Igen|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Házirend szakaszok:** bejövő  
  
-   **Házirend hatókörök:** globális  
  
##  <a name="CORS"></a>A CORS  
 A `cors` házirendnek eltérő eredetű erőforrások megosztása (CORS) a támogatási művelet vagy API lehetővé teszi a tartományok közötti hívások webböngésző-alapú ügyfelekről.  
  
 A CORS lehetővé teszi, hogy egy böngésző és a kiszolgáló működjön, és meghatározzák, hogy az adott eltérő eredetű kérések (azaz XMLHttpRequests hívásoknak JavaScript weblapon lévő más tartományokra) engedélyezése. Ez lehetővé teszi, hogy csak az azonos eredetű kérések engedélyezése-nál nagyobb rugalmasságot, de biztonságosabb, mint az összes eltérő eredetű kérések engedélyezése.  
  
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
 Ez a példa bemutatja, hogyan előtti repülési kérések, például az egyéni fejlécek vagy nem GET vagy POST metódusok támogatására. Egyéni fejlécek és további HTTP-műveletek támogatásához használja a `allowed-methods` és `allowed-headers` szakasz a következő példában látható módon.  
  
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
  
|Név|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|a cors|A gyökérelem.|Igen|N/A|  
|engedélyezett források|Tartalmaz `origin` elemek, amelyek a tartományok közötti kérelmek engedélyezett eredetet ismertetik. `allowed-origins`vagy egyetlen tartalmazhat `origin` megadó elem `*` engedélyezéséhez a forrás vagy egy vagy több `origin` egy URI-t tartalmazó elemek.|Igen|N/A|  
|Forrás|Az érték lehet `*` minden eredet vagy URI, amely meghatározza egy egyetlen forrás. Az URI tartalmaznia kell egy protokollt, a gazdagép és a port.|Igen|Ha a port nincs megadva az URI, 80-as portot használja HTTP és a 443-as portot használja HTTPS.|  
|engedélyezett módszerek|Ez az elem szükség, ha a módszerek nem GET vagy POST engedélyezettek. Tartalmaz `method` elemeit, adja meg a támogatott HTTP-műveleteket.|Nem|Ha ez a szakasz nincs jelen, a GET és POST támogatott.|  
|Módszer|Adja meg a HTTP-műveletet.|Legalább egy `method` elemet kell megadni, ha a `allowed-methods` szakaszt.|N/A|  
|engedélyezett fejlécek|Ez az elem tartalmaz `header` elemek a fejlécek, a kérelemben szereplő nevének megadása.|Nem|N/A|  
|a fejlécek elérhetővé tétele|Ez az elem tartalmaz `header` elemek a fejlécek, az ügyfél által hozzáférhető nevének megadása.|Nem|N/A|  
|header|Meghatározza a fejléc neve.|Legalább egy `header` elemnek kell szerepelnie a `allowed-headers` vagy `expose-headers` Ha a szakaszban található.|N/A|  
  
### <a name="attributes"></a>Attribútumok  
  
|Név|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|hitelesítő adatok engedélyezése|A `Access-Control-Allow-Credentials` fejléc a következő ellenőrzés válasz Ez az attribútum értékét úgy lesz beállítva, és hatással az ügyfél hitelesítő adatait, a tartományok közötti kérelmek elküldése.|Nem|hamis|  
|ellenőrzés-result-maximális-kor|A `Access-Control-Max-Age` fejléc a következő ellenőrzés válasz Ez az attribútum értékét úgy lesz beállítva, és a felhasználói ügynök képességét, gyorsítótári előtti repülési választ.|Nem|0|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Házirend szakaszok:** bejövő  
  
-   **Házirend hatókörök:** API-művelet  
  
##  <a name="JSONP"></a>JSONP  
 A `jsonp` házirend ad JSON (JSONP) kitöltési-támogatással rendelkező művelet vagy API lehetővé teszi a tartományok közötti hívások JavaScript-ügyfelekből webböngésző-alapú. JSONP módszere a JavaScript programok kérelem adatai egy kiszolgálóról egy másik tartományban. JSONP megkerüli a korlátozás kikényszeríti a legtöbb webböngészővel, ahol a weblapok elérése ugyanabban a tartományban kell lennie.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<jsonp callback-parameter-name="callback function name" />  
```  
  
### <a name="example"></a>Példa  
  
```xml  
<jsonp callback-parameter-name="cb" />  
```  
  
 A visszahívási paraméter nélkül. a metódus hívásakor? cb = XXX egyszerű JSON (nélkül függvény hívása burkoló) ad vissza.  
  
 Ha a visszahívási paraméter hozzáadása `?cb=XXX` JSONP eredményt ad vissza, az eredményeket a visszahívási függvény körül, például az eredeti JSON alkalmazásburkoló`XYZ('<json result goes here>');`  
  
### <a name="elements"></a>Elemek  
  
|Név|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|jsonp|A gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Név|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|a visszahívási paraméternév|A tartományokon átívelő JavaScript függvény hívása a teljesen minősített tartománynevét, amelyen a függvényt található előtagként.|Igen|N/A|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Házirend szakaszok:** kimenő  
  
-   **Házirend hatókörök:** globális, termék, API-művelet  
  
## <a name="next-steps"></a>Következő lépések
Házirendek használata további információkért lásd: [házirendek az API Management](api-management-howto-policies.md).  