---
title: Az Azure API Management gyorsítótárazási házirendjei |} A Microsoft Docs
description: További információ a használható az Azure API Management gyorsítótárazási házirendek.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: c1fd0f462a3eb960e27b002f4f7c940a6bf978c8
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52620574"
---
# <a name="api-management-caching-policies"></a>Az API Management gyorsítótárazási házirendek
Ez a témakör egy hivatkozást kínál a következő az API Management házirendek. Hozzáadása és házirendek konfigurálásával kapcsolatos tudnivalókat lásd: [az API Management házirendek](https://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CachingPolicies"></a> Gyorsítótárazási házirendek  
  
-   Gyorsítótárazási házirendek válasz  
  
    -   [Lekérése a gyorsítótárból](api-management-caching-policies.md#GetFromCache) – hajtsa végre a gyorsítótár kereshet, és ha elérhető egy érvényes gyorsítótárazott válaszokat ad vissza.  
    -   [Store Cache](api-management-caching-policies.md#StoreToCache) -gyorsítótárazza a választ a megadott gyorsítótár-vezérlő konfigurációjának megfelelően.  
  
-   Gyorsítótárazási házirendek érték  

    -   [Érték lekérése a gyorsítótárból](#GetFromCacheByKey) -kulcs által gyorsítótárazott elem beolvasása. 
    -   [Store értéket a gyorsítótárban](#StoreToCacheByKey) -Store egy elemet a gyorsítótárban kulcs szerint. 
    -   [Érték eltávolítása a gyorsítótárból](#RemoveCacheByKey) -távolítani egy elemet a gyorsítótárban kulcs szerint.  
  
##  <a name="GetFromCache"></a> Lekérése a gyorsítótárból  
 Használja a `cache-lookup` gyorsítótár végrehajtásához házirend kereshet meg és adja vissza egy érvényes gyorsítótárazott választ, ha elérhető. Ez a szabályzat alkalmazható azokban az esetekben, ahol válasz tartalma statikus marad egy időszakon belül. Válasz-gyorsítótárazás csökkenti a sávszélesség, és a háttérrendszer webes kiszolgáló, és csökkenti a késést az API-fogyasztókat által érzékelt kirótt feldolgozási követelmények.  
  
> [!NOTE]
>  Ez a szabályzat rendelkeznie kell egy megfelelő [Store Cache](api-management-caching-policies.md#StoreToCache) házirend.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" cache-preference="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">  
  <vary-by-header>Accept</vary-by-header>  
  <!-- should be present in most cases -->  
  <vary-by-header>Accept-Charset</vary-by-header>  
  <!-- should be present in most cases -->  
  <vary-by-header>Authorization</vary-by-header>  
  <!-- should be present when allow-private-response-caching is "true"-->  
  <vary-by-header>header name</vary-by-header>  
  <!-- optional, can repeated several times -->  
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>  
  <!-- optional, can repeated several times -->  
</cache-lookup>  
```  
  
### <a name="examples"></a>Példák  
  
#### <a name="example"></a>Példa  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" cache-preference="internal" >  
            <vary-by-query-parameter>version</vary-by-query-parameter>  
        </cache-lookup>           
    </inbound>  
    <outbound>  
        <cache-store duration="seconds" />  
        <base />          
    </outbound>  
</policies>  
```  
  
#### <a name="example-using-policy-expressions"></a>Példa házirend-kifejezések használatával  
 Ez a példa bemutatja, hogyan konfigurálhatja az API Management gyorsítótárazási válaszidő, amely megfelel a háttérszolgáltatás az készült biztonsági másolat szolgáltatás által megadott válaszok gyorsítótárazását `Cache-Control` irányelv. Konfigurálása és használata a szabályzat bemutatójáért lásd: [Cloud Cover epizód 177: több API-t a felügyeleti funkcióinak Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és a 25:25 előretekerés.  
  
```xml  
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->  
  
<!-- Copy this snippet into the inbound section -->  
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >  
  <vary-by-header>Accept</vary-by-header>  
  <vary-by-header>Accept-Charset</vary-by-header>  
</cache-lookup>  
  
<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->  
<cache-store duration="@{  
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");  
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;  
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;  
  }"  
 />  
```  
  
 További információkért lásd: [házirend-kifejezések](api-management-policy-expressions.md) és [környezeti változó](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elemek  
  
|Name (Név)|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|gyorsítótár-keresés|A gyökérelem.|Igen|  
|változó-által-fejléc|Kezdjék el gyorsítótárazni a válaszok száma elfogadás, Accept-karakterkészlet, Accept-Encoding, Accept-nyelv, hitelesítés, a várt, például a megadott fejléc értékét a gazdagépről, If-Match.|Nem|  
|változó-által-lekérdezési-paraméter|Indítsa el a gyorsítótárazás a válaszok száma a megadott lekérdezési paraméterek értékét. Adjon meg egy vagy több paraméter. Az elválasztó pontosvesszőt használja. Ha nincs megadva, minden lekérdezési paramétert használja.|Nem|  
  
### <a name="attributes"></a>Attribútumok  
  
| Name (Név)                           | Leírás                                                                                                                                                                                                                                                                                                                                                 | Szükséges | Alapértelmezett           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| lehetővé teszik privát-válasz-gyorsítótárazás | Ha a beállítása `true`, lehetővé teszi, hogy engedélyeztetési fejléc tartalmazó kérelmek gyorsítótárazási.                                                                                                                                                                                                                                                                        | Nem       | false             |
| gyorsítótár-beállítás               | Az attribútum a következő értékek közül választhat:<br />- `internal` a beépített API Management-gyorsítótár használata<br />- `external` az ismertetett módon kihasználhassák a külső gyorsítótár [egy külső Redis cache az Azure API Management használata](api-management-howto-cache-external.md),<br />- `prefer-external` Ellenkező esetben használja a belső vagy külső gyorsítótár, ha konfigurálva. | Nem       | `prefer-external` |
| alsóbb rétegbeli gyorsítótárazási típus        | Ez az attribútum a következő értékek egyikére kell beállítani.<br /><br /> – Nincs – alsóbb rétegbeli gyorsítótárazás nem engedélyezett.<br />-privát – alsóbb rétegbeli privát gyorsítótárazás használata engedélyezett.<br />– nyilvános - titkos és megosztott alsóbb rétegbeli engedélyezve van a gyorsítótárazás.                                                                                                          | Nem       | nincs              |
| kell-revalidate                | Ha az alsóbb rétegbeli gyorsítótárazás engedélyezve van ez az attribútum be- vagy kikapcsolja a `must-revalidate` gyorsítótár vezérlő irányelv az átjáró válaszokat.                                                                                                                                                                                                                      | Nem       | true              |
| változó-által-fejlesztő              | Állítsa be `true` fejlesztői kulcsonként gyorsítótár által küldött válaszokhoz.                                                                                                                                                                                                                                                                                                         | Igen      |                   |
| változó-által-developer-csoportok       | Állítsa be `true` a gyorsítótár-válaszok száma felhasználói szerepkörhöz.                                                                                                                                                                                                                                                                                                             | Igen      |                   |  

### <a name="usage"></a>Használat  
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **A házirend-szakaszok:** bejövő  
-   **A házirend-hatókörök:** API, a művelet, a termék  
  
##  <a name="StoreToCache"></a> Store Cache  
 A `cache-store` házirend gyorsítótárazza a választ a megadott beállítások szerint. Ez a szabályzat alkalmazható azokban az esetekben, ahol válasz tartalma statikus marad egy időszakon belül. Válasz-gyorsítótárazás csökkenti a sávszélesség, és a háttérrendszer webes kiszolgáló, és csökkenti a késést az API-fogyasztókat által érzékelt kirótt feldolgozási követelmények.  
  
> [!NOTE]
>  Ez a szabályzat rendelkeznie kell egy megfelelő [lekérése a gyorsítótárból](api-management-caching-policies.md#GetFromCache) házirend.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<cache-store duration="seconds" />  
```  
  
### <a name="examples"></a>Példák  
  
#### <a name="example"></a>Példa  
  
```xml  
<policies>  
    <inbound>  
        <base />  
          <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">  
                <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->  
        </cache-lookup>  
    </inbound>  
    <outbound>  
        <base />  
            <cache-store duration="3600" />  
    </outbound>  
</policies>  
```  
  
#### <a name="example-using-policy-expressions"></a>Példa házirend-kifejezések használatával  
 Ez a példa bemutatja, hogyan konfigurálhatja az API Management gyorsítótárazási válaszidő, amely megfelel a háttérszolgáltatás az készült biztonsági másolat szolgáltatás által megadott válaszok gyorsítótárazását `Cache-Control` irányelv. Konfigurálása és használata a szabályzat bemutatójáért lásd: [Cloud Cover epizód 177: több API-t a felügyeleti funkcióinak Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és a 25:25 előretekerés.  
  
```xml  
<!-- The following cache policy snippets demonstrate how to control API Management response cache duration with Cache-Control headers sent by the backend service. -->  
  
<!-- Copy this snippet into the inbound section -->  
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >  
  <vary-by-header>Accept</vary-by-header>  
  <vary-by-header>Accept-Charset</vary-by-header>  
</cache-lookup>  
  
<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the default value of 5 min if none is found  -->  
<cache-store duration="@{  
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");  
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;  
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;  
  }"  
 />  
```  
  
 További információkért lásd: [házirend-kifejezések](api-management-policy-expressions.md) és [környezeti változó](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elemek  
  
|Name (Név)|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|gyorsítótár-tároló|A gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
| Name (Név)             | Leírás                                                                                                                                                                                                                                                                                                                                                 | Szükséges | Alapértelmezett           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| időtartam         | Time-to-live a gyorsítótárazott bejegyzések, másodpercben megadva.                                                                                                                                                                                                                                                                                                   | Igen      | –               |  

### <a name="usage"></a>Használat  
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **A házirend-szakaszok:** kimenő    
-   **A házirend-hatókörök:** API, a művelet, a termék  
  
##  <a name="GetFromCacheByKey"></a> Érték lekérése a gyorsítótárból  
 Használja a `cache-lookup-value` házirend végrehajtása a keresési gyorsítótár kulcs és a egy gyorsítótárazott értéket ad vissza. A kulcs egy tetszés szerinti karakterlánc értéke lehet, és általában biztosított egy házirend-kifejezés használatával.  
  
> [!NOTE]
>  Ez a szabályzat rendelkeznie kell egy megfelelő [Store értéket a gyorsítótárban](#StoreToCacheByKey) házirend.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<cache-lookup-value key="cache key value"   
    default-value="value to use if cache lookup resulted in a miss"   
    variable-name="name of a variable looked up value is assigned to"  
    cache-preference="prefer-external | external | internal" />  
```  
  
### <a name="example"></a>Példa  
 További információk és példák a szabályzat: [egyéni gyorsítótárazás az Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).  
  
```xml  
<cache-lookup-value  
    key="@("userprofile-" + context.Variables["enduserid"])"    
    variable-name="userprofile" />  
  
```  
  
### <a name="elements"></a>Elemek  
  
|Name (Név)|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|gyorsítótár-keresési-érték|A gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
| Name (Név)             | Leírás                                                                                                                                                                                                                                                                                                                                                 | Szükséges | Alapértelmezett           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| gyorsítótár-beállítás | Az attribútum a következő értékek közül választhat:<br />- `internal` a beépített API Management-gyorsítótár használata<br />- `external` az ismertetett módon kihasználhassák a külső gyorsítótár [egy külső Redis cache az Azure API Management használata](api-management-howto-cache-external.md),<br />- `prefer-external` Ellenkező esetben használja a belső vagy külső gyorsítótár, ha konfigurálva. | Nem       | `prefer-external` |
| alapértelmezett – érték    | Egy érték, amely hozzá lesz rendelve a változó, ha a gyorsítótár-keresés tévesztés eredményezett. Ha ez az attribútum nincs megadva, `null` hozzá van rendelve.                                                                                                                                                                                                           | Nem       | `null`            |
| kulcs              | Gyorsítótár-kulcs értéke a keresés használatára.                                                                                                                                                                                                                                                                                                                       | Igen      | –               |
| a változó neve    | Neve a [környezeti változó](api-management-policy-expressions.md#ContextVariables) looked mentése értékét rendeli hozzá, ha a keresés sikeres. Keresési tévesztés eredményez, ha a változó fog megegyezni értékét a `default-value` attribútum vagy `null`, ha a `default-value` attribútum hiányzik.                                       | Igen      | –               |  

### <a name="usage"></a>Használat  
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **A házirend-szakaszok:** bejövő, kimenő, háttér, – hiba  
-   **A házirend-hatókörök:** globális, API, a művelet, a termék  
  
##  <a name="StoreToCacheByKey"></a> A gyorsítótárban Store értéket  
 A `cache-store-value` kulcs végez a gyorsítótárban. A kulcs egy tetszés szerinti karakterlánc értéke lehet, és általában biztosított egy házirend-kifejezés használatával.  
  
> [!NOTE]
>  Ez a szabályzat rendelkeznie kell egy megfelelő [érték lekérése a gyorsítótárból](#GetFromCacheByKey) házirend.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<cache-store-value key="cache key value" value="value to cache" duration="seconds" cache-preference="prefer-external | external | internal" />  
```  
  
### <a name="example"></a>Példa  
 További információk és példák a szabályzat: [egyéni gyorsítótárazás az Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).  
  
```xml  
<cache-store-value  
    key="@("userprofile-" + context.Variables["enduserid"])"  
    value="@((string)context.Variables["userprofile"])" duration="100000" />  
  
```  
  
### <a name="elements"></a>Elemek  
  
|Name (Név)|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|gyorsítótár-tároló-érték|A gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
| Name (Név)             | Leírás                                                                                                                                                                                                                                                                                                                                                 | Szükséges | Alapértelmezett           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| gyorsítótár-beállítás | Az attribútum a következő értékek közül választhat:<br />- `internal` a beépített API Management-gyorsítótár használata<br />- `external` az ismertetett módon kihasználhassák a külső gyorsítótár [egy külső Redis cache az Azure API Management használata](api-management-howto-cache-external.md),<br />- `prefer-external` Ellenkező esetben használja a belső vagy külső gyorsítótár, ha konfigurálva. | Nem       | `prefer-external` |
| időtartam         | Érték a gyorsítótárba fognak a megadott időtartam érték, másodpercben megadva.                                                                                                                                                                                                                                                                                 | Igen      | –               |
| kulcs              | A gyorsítótár kulcsának értékét tárolja.                                                                                                                                                                                                                                                                                                                   | Igen      | –               |
| érték            | A gyorsítótárazható érték.                                                                                                                                                                                                                                                                                                                                     | Igen      | –               |
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **A házirend-szakaszok:** bejövő, kimenő, háttér, – hiba  
-   **A házirend-hatókörök:** globális, API, a művelet, a termék  
  
###  <a name="RemoveCacheByKey"></a> Érték eltávolítása a gyorsítótárból  
A `cache-remove-value` töröl egy gyorsítótárazott elem, a kulcs azonosítja. A kulcs egy tetszés szerinti karakterlánc értéke lehet, és általában biztosított egy házirend-kifejezés használatával.  
  
#### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
  
<cache-remove-value key="cache key value" cache-preference="prefer-external | external | internal"  />  
  
```  
  
#### <a name="example"></a>Példa  
  
```xml  
  
<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>  
  
```  
  
#### <a name="elements"></a>Elemek  
  
|Name (Név)|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|gyorsítótár-remove-value|A gyökérelem.|Igen|  
  
#### <a name="attributes"></a>Attribútumok  
  
| Name (Név)             | Leírás                                                                                                                                                                                                                                                                                                                                                 | Szükséges | Alapértelmezett           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| gyorsítótár-beállítás | Az attribútum a következő értékek közül választhat:<br />- `internal` a beépített API Management-gyorsítótár használata<br />- `external` az ismertetett módon kihasználhassák a külső gyorsítótár [egy külső Redis cache az Azure API Management használata](api-management-howto-cache-external.md),<br />- `prefer-external` Ellenkező esetben használja a belső vagy külső gyorsítótár, ha konfigurálva. | Nem       | `prefer-external` |
| kulcs              | A korábban gyorsítótárazott értéket el kell távolítani a gyorsítótárból kulcsa.                                                                                                                                                                                                                                                                                        | Igen      | –               |
  
#### <a name="usage"></a>Használat  
 Ez a házirend használható a következő szabályzatot [szakaszok](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .  
  
-   **A házirend-szakaszok:** bejövő, kimenő, háttér, – hiba  
-   **A házirend-hatókörök:** globális, API, a művelet, a termék  

## <a name="next-steps"></a>További lépések

Házirendek használata további információkért lásd:

+ [Az API Management házirendek](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ [Házirend-referencia](api-management-policy-reference.md) házirend-utasítások és a beállítások teljes listáját
+ [A házirend-minták](policy-samples.md)   
