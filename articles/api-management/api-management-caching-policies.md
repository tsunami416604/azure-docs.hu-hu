---
title: "Gyorsítótárazás házirendek az Azure API Management |} Microsoft Docs"
description: "A gyorsítótárazási házirend használható az Azure API Management megismerése."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 488a4c4b7daf5c07ca5f6b6bb72464279658d372
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2017
---
# <a name="api-management-caching-policies"></a>Az API Management gyorsítótárazási házirendek
Ez a témakör egy hivatkozást a következő API-felügyeleti házirendek. Hozzáadása és házirendek konfigurálásával kapcsolatos tudnivalókat lásd: [házirendek az API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CachingPolicies"></a>Házirendek gyorsítótárazása  
  
-   A válasz gyorsítótár házirendek  
  
    -   [Gyorsítótár beszerezni](api-management-caching-policies.md#GetFromCache) -hajtsa végre a gyorsítótár kereshet, és ha elérhető egy érvényes gyorsítótárazott választ adjon vissza.  
    -   [Gyorsítótárazandó tároló](api-management-caching-policies.md#StoreToCache) -gyorsítótárazza a válaszokat a gyorsítótármappa megadott vezérlő konfigurációjának megfelelően.  
  
-   Házirendek gyorsítótárazás érték  

    -   [Lehet értéket kiolvasni a gyorsítótár](#GetFromCacheByKey) -kulcs által gyorsítótárazott elem beolvasása. 
    -   [A gyorsítótárban tárolja a érték](#StoreToCacheByKey) -tárolja egy elemet a gyorsítótár gombot. 
    -   [Távolítsa el az értéket a gyorsítótárból](#RemoveCacheByKey) -a gyorsítótárban, kulcs által távolítani egy elemet.  
  
##  <a name="GetFromCache"></a>Gyorsítótár beszerzése  
 Használja a `cache-lookup` házirend gyorsítótár végrehajtásához kereshet, és térjen vissza egy érvényes gyorsítótárazott választ, ha elérhető. Ez a házirend azokban az esetekben, ahol válasz tartalma statikus maradjon egy meghatározott időtartamra vonatkozóan lehet alkalmazni. Válasz gyorsítótárazás csökkenti a sávszélesség, és a háttér web server, és csökkenti a várakozási API fogyasztó érzékelt kivetett szemben támasztott feldolgozási igényeket.  
  
> [!NOTE]
>  Ez a házirend rendelkeznie kell egy megfelelő [tároló, a gyorsítótár](api-management-caching-policies.md#StoreToCache) házirend.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">  
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
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true">  
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
 Ez a példa bemutatja, hogyan konfigurálhatja az API Management válasz gyorsítótár, amely megfelel a háttérszolgáltatáshoz, mint a válasz gyorsítótárazás időtartama adni a biztonsági másolat szolgáltatás `Cache-Control` direktívát. A házirenddel és konfigurálása a bemutatója, lásd: [felhő fedik le a epizód 177: több API a felügyeleti funkcióinak Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és a 25:25 előretekerés.  
  
```xml  
<!-- The following cache policy snippets demonstrate how to control API Management reponse cache duration with Cache-Control headers sent by the backend service. -->  
  
<!-- Copy this snippet into the inbound section -->  
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >  
  <vary-by-header>Accept</vary-by-header>  
  <vary-by-header>Accept-Charset</vary-by-header>  
</cache-lookup>  
  
<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the deafult value of 5 min if none is found  -->  
<cache-store duration="@{  
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");  
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;  
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;  
  }"  
 />  
```  
  
 További információkért lásd: [házirend-kifejezések](api-management-policy-expressions.md) és [környezeti változó](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elemek  
  
|Név|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|gyorsítótár-keresés|A gyökérelem.|Igen|  
|eltérő-által-fejléc|Kezdjék el gyorsítótárazni a válaszok száma érték a megadott fejléc, például elfogadás, Accept-Charset, elfogadás-kódolás, elfogadás-nyelv, hitelesítés, a várt, a gazdagép, If-Match.|Nem|  
|eltérő-által-lekérdezési-paraméter|Kezdjék el gyorsítótárazni a válaszok száma a megadott lekérdezési paraméterek értékének. Adjon meg egy vagy több paramétert. Használjon pontosvessző. Ha nincs megadva, az összes lekérdezési paraméterek használhatók.|Nem|  
  
### <a name="attributes"></a>Attribútumok  
  
|Név|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|lehetővé teszik privát-válasz-gyorsítótárazás|Ha beállítása `true`, lehetővé teszi, hogy egy Authorization fejlécet tartalmazó kérelmek gyorsítótárazását.|Nem|hamis|  
|alsóbb rétegbeli gyorsítótárazás típusa|Ez az attribútum a következő értékek egyikére kell beállítani.<br /><br /> -none - alárendelt gyorsítótárazás nem engedélyezett.<br />-titkos - alsóbb rétegbeli személyes engedélyezve van a gyorsítótárazás.<br />-nyilvános - titkos és megosztott alárendelt engedélyezve van a gyorsítótárazás.|Nem|nincs|  
|must-revalidate érték|Ha az alárendelt gyorsítótár engedélyezve van ez az attribútum be- vagy kikapcsolja a `must-revalidate` cache-control direktíva átjáró válaszokban.|Nem|igaz|  
|eltérő-által-fejlesztőknek|Beállítása `true` a gyorsítótár-válaszok fejlesztői kulcs száma.|Igen||  
|eltérő-által-developer-csoportok|Beállítása `true` gyorsítótár-válaszok száma felhasználói szerepkör számára.|Igen||  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Házirend szakaszok:** bejövő  
-   **Házirend hatókörök:** API, a művelet, a termék  
  
##  <a name="StoreToCache"></a>Gyorsítótárazandó tárolásához  
 A `cache-store` házirend gyorsítótárazza a választ a gyorsítótármappa megadott beállítások szerint. Ez a házirend azokban az esetekben, ahol válasz tartalma statikus maradjon egy meghatározott időtartamra vonatkozóan lehet alkalmazni. Válasz gyorsítótárazás csökkenti a sávszélesség, és a háttér web server, és csökkenti a várakozási API fogyasztó érzékelt kivetett szemben támasztott feldolgozási igényeket.  
  
> [!NOTE]
>  Ez a házirend rendelkeznie kell egy megfelelő [lekérni a gyorsítótár](api-management-caching-policies.md#GetFromCache) házirend.  
  
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
 Ez a példa bemutatja, hogyan konfigurálhatja az API Management válasz gyorsítótár, amely megfelel a háttérszolgáltatáshoz, mint a válasz gyorsítótárazás időtartama adni a biztonsági másolat szolgáltatás `Cache-Control` direktívát. A házirenddel és konfigurálása a bemutatója, lásd: [felhő fedik le a epizód 177: több API a felügyeleti funkcióinak Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és a 25:25 előretekerés.  
  
```xml  
<!-- The following cache policy snippets demonstrate how to control API Management reponse cache duration with Cache-Control headers sent by the backend service. -->  
  
<!-- Copy this snippet into the inbound section -->  
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >  
  <vary-by-header>Accept</vary-by-header>  
  <vary-by-header>Accept-Charset</vary-by-header>  
</cache-lookup>  
  
<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the deafult value of 5 min if none is found  -->  
<cache-store duration="@{  
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");  
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;  
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;  
  }"  
 />  
```  
  
 További információkért lásd: [házirend-kifejezések](api-management-policy-expressions.md) és [környezeti változó](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elemek  
  
|Név|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|gyorsítótár|A gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Név|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|Időtartam|Másodpercben megadott idő élettartamát a gyorsítótárazott bejegyzés.|Igen|N/A|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Házirend szakaszok:** kimenő    
-   **Házirend hatókörök:** API, a művelet, a termék  
  
##  <a name="GetFromCacheByKey"></a>Lehet értéket kiolvasni a gyorsítótár  
 Használja a `cache-lookup-value` házirend gyorsítótár keresést végrehajtani a gombot, és a gyorsítótárazott érték visszaadása. A kulcs egy tetszőleges karakterlánc értéke lehet, és általában valósul meg a házirend-kifejezés használatával.  
  
> [!NOTE]
>  Ez a házirend rendelkeznie kell egy megfelelő [érték tárolása gyorsítótár](#StoreToCacheByKey) házirend.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<cache-lookup-value key="cache key value"   
    default-value="value to use if cache lookup resulted in a miss"   
    variable-name="name of a variable looked up value is assigned to" />  
```  
  
### <a name="example"></a>Példa  
 További tudnivalók és példák ezt a házirendet, lásd: [egyéni gyorsítótárazása az Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).  
  
```xml  
<cache-lookup-value  
    key="@("userprofile-" + context.Variables["enduserid"])"    
    variable-name="userprofile" />  
  
```  
  
### <a name="elements"></a>Elemek  
  
|Név|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|gyorsítótár-keresési-értéke|A gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Név|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|alapértelmezett értékű|Egy érték, amely rendeli hozzá a változó, ha a gyorsítótár kulcskeresési tévesztés eredményezett. Ha ez az attribútum nincs megadva, `null` hozzá van rendelve.|Nem|`null`|  
|kulcs|Gyorsítótár kulcsérték a keresés használatára.|Igen|N/A|  
|változó-neve|Neve a [környezeti változó](api-management-policy-expressions.md#ContextVariables) a looked be értéket rendeli hozzá, ha a keresés sikeres. Keresési tévesztés eredményez, ha a változó kap-e értékét a `default-value` attribútum vagy `null`, ha a `default-value` attribútum hiányzik.|Igen|N/A|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Házirend szakaszok:** bejövő, kimenő háttér,-hiba  
-   **Házirend hatókörök:** globális, API-t, a művelet, a termék  
  
##  <a name="StoreToCacheByKey"></a>A gyorsítótárban tárolja a érték  
 A `cache-store-value` hajtja végre a gyorsítótár tárolási gombot. A kulcs egy tetszőleges karakterlánc értéke lehet, és általában valósul meg a házirend-kifejezés használatával.  
  
> [!NOTE]
>  Ez a házirend rendelkeznie kell egy megfelelő [lehet értéket kiolvasni a gyorsítótár](#GetFromCacheByKey) házirend.  
  
### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
<cache-store-value key="cache key value" value="value to cache" duration="seconds" />  
```  
  
### <a name="example"></a>Példa  
 További tudnivalók és példák ezt a házirendet, lásd: [egyéni gyorsítótárazása az Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/).  
  
```xml  
<cache-store-value  
    key="@("userprofile-" + context.Variables["enduserid"])"  
    value="@((string)context.Variables["userprofile"])" duration="100000" />  
  
```  
  
### <a name="elements"></a>Elemek  
  
|Név|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|gyorsítótár-tároló-értéke|A gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Név|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|Időtartam|Érték a gyorsítótárba fognak kerülni a megadott típusú érték, másodpercben megadva.|Igen|N/A|  
|kulcs|A gyorsítótár kulcsának értékét tárolja.|Igen|N/A|  
|érték|A gyorsítótárazható érték.|Igen|N/A|  
  
### <a name="usage"></a>Használat  
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Házirend szakaszok:** bejövő, kimenő háttér,-hiba  
-   **Házirend hatókörök:** globális, API-t, a művelet, a termék  
  
###  <a name="RemoveCacheByKey"></a>A gyorsítótárból értékének eltávolítása  
A `cache-remove-value` egy gyorsítótárazott elem azonosítja a kulcs törlése. A kulcs egy tetszőleges karakterlánc értéke lehet, és általában valósul meg a házirend-kifejezés használatával.  
  
#### <a name="policy-statement"></a>Házirendutasítás  
  
```xml  
  
<cache-remove-value key="cache key value"/>  
  
```  
  
#### <a name="example"></a>Példa  
  
```xml  
  
<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>  
  
```  
  
#### <a name="elements"></a>Elemek  
  
|Név|Leírás|Szükséges|  
|----------|-----------------|--------------|  
|gyorsítótár-remove-értéke|A gyökérelem.|Igen|  
  
#### <a name="attributes"></a>Attribútumok  
  
|Név|Leírás|Szükséges|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|kulcs|A korábban gyorsítótárazott érték eltávolítsa őket a gyorsítótár kulcsának.|Igen|N/A|  
  
#### <a name="usage"></a>Használat  
 Ez a házirend használható a következő házirend [szakaszok](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörök](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .  
  
-   **Házirend szakaszok:** bejövő, kimenő háttér,-hiba  
-   **Házirend hatókörök:** globális, API-t, a művelet, a termék  

## <a name="next-steps"></a>Következő lépések

Házirendek használata további információkért lásd:

+ [Az API Management házirendek](api-management-howto-policies.md)
+ [Átalakítás API-k](transform-api.md)
+ [Házirend-hivatkozás](api-management-policy-reference.md) házirend-utasításoknál és a beállítások teljes listáját
+ [Házirend-minták](policy-samples.md)   
