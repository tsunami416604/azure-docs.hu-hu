---
title: Azure API Management gyorsítótárazási szabályzatok | Microsoft dokumentumok
description: Ismerje meg az Azure API Managementben használható gyorsítótárazási szabályzatokat.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2018
ms.author: apimpm
ms.openlocfilehash: 06c4ede12f939e48973d3e0b502d90b848d199bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280299"
---
# <a name="api-management-caching-policies"></a>API Management – Gyorsítótárazási szabályzatok
Ez a témakör a következő API Management-házirendek hivatkozási alapként szolgál. A házirendek hozzáadásáról és konfigurálásáról az [API-kezelés házirendjei](https://go.microsoft.com/fwlink/?LinkID=398186)című témakörben talál további információt.

## <a name="caching-policies"></a><a name="CachingPolicies"></a>Gyorsítótárazási házirendek

- Válaszgyorsítótárazási házirendek
    - [Beolvasás a gyorsítótárból](api-management-caching-policies.md#GetFromCache) – Gyorsítótár-felolvasás tanus, és érvényes gyorsítótárazott válaszok at ad vissza, ha elérhetők.
    - [Tároló a gyorsítótárba](api-management-caching-policies.md#StoreToCache) – Gyorsítótárazások válaszok a megadott gyorsítótár-vezérlő konfigurációnak megfelelően.
- Érték-gyorsítótárazási házirendek
    - [Érték beolvasása a gyorsítótárból](#GetFromCacheByKey) – Gyorsítótárazott elem lekérése kulcs onként.
    - [Tárolóérték a gyorsítótárban](#StoreToCacheByKey) – Elem tárolása a gyorsítótárban kulcs szerint.
    - [Érték eltávolítása a gyorsítótárból](#RemoveCacheByKey) – Elem eltávolítása a gyorsítótárból kulcs szerint.

## <a name="get-from-cache"></a><a name="GetFromCache"></a>Beolvasás a gyorsítótárból
A `cache-lookup` házirend segítségével gyorsítótár-felolvasást hajtson végre, és érvényes gyorsítótárazott választ adjon vissza, ha az elérhető. Ez a házirend olyan esetekben alkalmazható, amikor a választartalom statikus marad egy adott időszakban. A válaszgyorsítótárazás csökkenti a háttérkiszolgálóra vonatkozó sávszélesség- és feldolgozási követelményeket, és csökkenti az API-fogyasztók által érzékelt késést.

> [!NOTE]
> Ennek a házirendnek rendelkeznie kell egy megfelelő [Tárolóval a gyorsítótár-házirendhez.](api-management-caching-policies.md#StoreToCache)

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" caching-type="prefer-external | external | internal" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">
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
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true" caching-type="internal" >
            <vary-by-query-parameter>version</vary-by-query-parameter>
        </cache-lookup>
    </inbound>
    <outbound>
        <cache-store duration="seconds" />
        <base />
    </outbound>
</policies>
```

#### <a name="example-using-policy-expressions"></a>Példa házirend-kifejezések használatára
Ez a példa bemutatja, hogyan konfigurálhatja az API Management válaszgyorsítótárazás időtartamát, amely megfelel a `Cache-Control` háttérszolgáltatás a háttérszolgáltatás direktíva által meghatározott válasz-gyorsítótárazásának. A szabályzat konfigurálásának és használatának bemutatását lásd: [Cloud Cover Episode 177: További API-kezelési funkciók Vlad Vinogradsky-val](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és gyors előretekerés 25:25-ig.

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

További információt a [Házirend-kifejezések](api-management-policy-expressions.md) és [a Környezeti változó című témakörben talál.](api-management-policy-expressions.md#ContextVariables)

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|gyorsítótár-keresgés|Gyökérelem.|Igen|
|eltérő fejlécenként|Indítsa el a gyorsítótárazási válaszokat a megadott fejléc értékénként, például Elfogadás, Elfogadás-charset, Elfogadás-kódolás, Elfogadási nyelv, Engedélyezés, Várható, Feladó, Állomás, Ha-egyezés.|Nem|
|eltérő lekérdezési paraméter|Indítsa el a gyorsítótárazási válaszokat a megadott lekérdezési paraméterek értékénként. Adjon meg egy vagy több paramétert. Pontosvessző használata elválasztóként. Ha nincs megadva, a rendszer az összes lekérdezési paramétert használja.|Nem|

### <a name="attributes"></a>Attribútumok

| Név                           | Leírás                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett           |
|--------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| engedélyezés-magán-válasz-gyorsítótárazás | Ha `true`a beállítás a , lehetővé teszi az engedélyezési fejlécet tartalmazó kérelmek gyorsítótárazását.                                                                                                                                                                                                                                                                        | Nem       | hamis             |
| gyorsítótárazási típus               | Válasszon az attribútum következő értékei közül:<br />- `internal`a beépített API Management gyorsítótár használatához,<br />- `external`a külső gyorsítótár használata a [Külső Azure-gyorsítótár használata a Redis hez](api-management-howto-cache-external.md)az Azure API Management alkalmazásban című részben leírtak szerint,<br />- `prefer-external`külső gyorsítótár használata, ha konfigurálva van, vagy a belső gyorsítótár más módon. | Nem       | `prefer-external` |
| tovább-gyorsítótárazási típusú        | Ezt az attribútumot az alábbi értékek egyikére kell beállítani.<br /><br /> - nincs - a későbbi gyorsítótárazás nem engedélyezett.<br />- privát - későbbi privát gyorsítótárazás megengedett.<br />- nyilvános - privát és megosztott alsóbb rétegbeli gyorsítótárazás megengedett.                                                                                                          | Nem       | Nincs              |
| újra kell validálni                | Ha az alsóbb rétegbeli gyorsítótárazás engedélyezve van, ez az attribútum be- vagy kikapcsolja a gyorsítótár-vezérlőt az `must-revalidate` átjáróválaszokban.                                                                                                                                                                                                                      | Nem       | igaz              |
| eltérő fejlesztőnként              | Állítsa `true` be a gyorsítótár válaszok [előfizetési kulcsonként](https://docs.microsoft.com/azure/api-management/api-management-subscriptions).                                                                                                                                                                                                                                                                                                         | Igen      |         False (Hamis)          |
| eltérő fejlesztői csoportok       | Állítsa `true` be a felhasználói csoportonkénti gyorsítótáras [válaszokra.](https://docs.microsoft.com/azure/api-management/api-management-howto-create-groups)                                                                                                                                                                                                                                                                                                             | Igen      |       False (Hamis)            |

### <a name="usage"></a>Használat
Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

- **Házirendszakaszok:** bejövő
- **Házirend-hatókörök:** az összes hatókör

## <a name="store-to-cache"></a><a name="StoreToCache"></a>Tároló a gyorsítótárba
A `cache-store` házirend a megadott gyorsítótár-beállításoknak megfelelően gyorsítótárazja a válaszokat. Ez a házirend olyan esetekben alkalmazható, amikor a választartalom statikus marad egy adott időszakban. A válaszgyorsítótárazás csökkenti a háttérkiszolgálóra vonatkozó sávszélesség- és feldolgozási követelményeket, és csökkenti az API-fogyasztók által érzékelt késést.

> [!NOTE]
> Ennek a házirendnek rendelkeznie kell egy megfelelő [get from cache](api-management-caching-policies.md#GetFromCache) házirenddel.

### <a name="policy-statement"></a>Politikai nyilatkozat

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

#### <a name="example-using-policy-expressions"></a>Példa házirend-kifejezések használatára
Ez a példa bemutatja, hogyan konfigurálhatja az API Management válaszgyorsítótárazás időtartamát, amely megfelel a `Cache-Control` háttérszolgáltatás a háttérszolgáltatás direktíva által meghatározott válasz-gyorsítótárazásának. A szabályzat konfigurálásának és használatának bemutatását lásd: [Cloud Cover Episode 177: További API-kezelési funkciók Vlad Vinogradsky-val](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) és gyors előretekerés 25:25-ig.

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

További információt a [Házirend-kifejezések](api-management-policy-expressions.md) és [a Környezeti változó című témakörben talál.](api-management-policy-expressions.md#ContextVariables)

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|gyorsítótár-tároló|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

| Név             | Leírás                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| duration         | A gyorsítótárazott bejegyzések megélési ideje másodpercben megadva.                                                                                                                                                                                                                                                                                                   | Igen      | N/A               |

### <a name="usage"></a>Használat
Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

- **Házirendszakaszok:** kimenő
- **Házirend-hatókörök:** az összes hatókör

## <a name="get-value-from-cache"></a><a name="GetFromCacheByKey"></a>Érték beolvasása a gyorsítótárból
A `cache-lookup-value` házirend segítségével kulcs szerint végezhet gyorsítótár-keresést, és gyorsítótárazott értéket ad vissza. A kulcs tetszőleges karakterlánc-értékkel rendelkezhet, és általában házirend-kifejezéssel van megadva.

> [!NOTE]
> Ennek a házirendnek rendelkeznie kell egy megfelelő [áruházértékkel a gyorsítótár-házirendben.](#StoreToCacheByKey)

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<cache-lookup-value key="cache key value"
    default-value="value to use if cache lookup resulted in a miss"
    variable-name="name of a variable looked up value is assigned to"
    caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Példa
A szabályzattal kapcsolatos további információkért és példákért az [Egyéni gyorsítótárazás az Azure API-kezelésben](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/)című témakörben talál.

```xml
<cache-lookup-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    variable-name="userprofile" />

```

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|gyorsítótár-keresendő érték|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

| Név             | Leírás                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| gyorsítótárazási típus | Válasszon az attribútum következő értékei közül:<br />- `internal`a beépített API Management gyorsítótár használatához,<br />- `external`a külső gyorsítótár használata a [Külső Azure-gyorsítótár használata a Redis hez](api-management-howto-cache-external.md)az Azure API Management alkalmazásban című részben leírtak szerint,<br />- `prefer-external`külső gyorsítótár használata, ha konfigurálva van, vagy a belső gyorsítótár más módon. | Nem       | `prefer-external` |
| alapértelmezett érték    | Olyan érték, amely akkor lesz hozzárendelve a változóhoz, ha a gyorsítótárkulcs-keresve hiányzik. Ha ez az attribútum nincs `null` megadva, akkor hozzá van rendelve.                                                                                                                                                                                                           | Nem       | `null`            |
| kulcs              | Gyorsítótárkulcs-érték a keresménél.                                                                                                                                                                                                                                                                                                                       | Igen      | N/A               |
| változónév    | Annak a [környezeti változónak](api-management-policy-expressions.md#ContextVariables) a neve, amelyhez a keresett érték lesz hozzárendelve, ha a kérdés sikeres. Ha a keresés hiányt eredményez, a változó hoz `default-value` létre az `null`attribútum `default-value` értékét, vagy ha az attribútum nincs megadva.                                       | Igen      | N/A               |

### <a name="usage"></a>Használat
Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

- **Házirendszakaszok:** bejövő, kimenő, háttérrendszer, hiba
- **Házirend-hatókörök:** az összes hatókör

## <a name="store-value-in-cache"></a><a name="StoreToCacheByKey"></a>A gyorsítótárban tárolt érték
A `cache-store-value` gyorsítótár-tárolást kulcs szerint hajtja végre. A kulcs tetszőleges karakterlánc-értékkel rendelkezhet, és általában házirend-kifejezéssel van megadva.

> [!NOTE]
> Ennek a házirendnek rendelkeznie kell egy megfelelő [get értékkel a gyorsítótár-házirendből.](#GetFromCacheByKey)

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<cache-store-value key="cache key value" value="value to cache" duration="seconds" caching-type="prefer-external | external | internal" />
```

### <a name="example"></a>Példa
A szabályzattal kapcsolatos további információkért és példákért az [Egyéni gyorsítótárazás az Azure API-kezelésben](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/)című témakörben talál.

```xml
<cache-store-value
    key="@("userprofile-" + context.Variables["enduserid"])"
    value="@((string)context.Variables["userprofile"])" duration="100000" />

```

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|gyorsítótár-tároló-érték|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

| Név             | Leírás                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| gyorsítótárazási típus | Válasszon az attribútum következő értékei közül:<br />- `internal`a beépített API Management gyorsítótár használatához,<br />- `external`a külső gyorsítótár használata a [Külső Azure-gyorsítótár használata a Redis hez](api-management-howto-cache-external.md)az Azure API Management alkalmazásban című részben leírtak szerint,<br />- `prefer-external`külső gyorsítótár használata, ha konfigurálva van, vagy a belső gyorsítótár más módon. | Nem       | `prefer-external` |
| duration         | A megadott időtartam értékéhez a rendszer gyorsítótárba helyezi az értéket másodpercben.                                                                                                                                                                                                                                                                                 | Igen      | N/A               |
| kulcs              | Az érték alatt tárolt gyorsítótárkulcs lesz tárolva.                                                                                                                                                                                                                                                                                                                   | Igen      | N/A               |
| érték            | A gyorsítótárazandó érték.                                                                                                                                                                                                                                                                                                                                     | Igen      | N/A               |
### <a name="usage"></a>Használat
Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

- **Házirendszakaszok:** bejövő, kimenő, háttérrendszer, hiba
- **Házirend-hatókörök:** az összes hatókör

### <a name="remove-value-from-cache"></a><a name="RemoveCacheByKey"></a>Érték eltávolítása a gyorsítótárból
A `cache-remove-value` kulcs által azonosított gyorsítótárazott elem törlése. A kulcs tetszőleges karakterlánc-értékkel rendelkezhet, és általában házirend-kifejezéssel van megadva.

#### <a name="policy-statement"></a>Politikai nyilatkozat

```xml

<cache-remove-value key="cache key value" caching-type="prefer-external | external | internal"  />

```

#### <a name="example"></a>Példa

```xml

<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>

```

#### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|gyorsítótár-eltávolítás-érték|Gyökérelem.|Igen|

#### <a name="attributes"></a>Attribútumok

| Név             | Leírás                                                                                                                                                                                                                                                                                                                                                 | Kötelező | Alapértelmezett           |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------|-------------------|
| gyorsítótárazási típus | Válasszon az attribútum következő értékei közül:<br />- `internal`a beépített API Management gyorsítótár használatához,<br />- `external`a külső gyorsítótár használata a [Külső Azure-gyorsítótár használata a Redis hez](api-management-howto-cache-external.md)az Azure API Management alkalmazásban című részben leírtak szerint,<br />- `prefer-external`külső gyorsítótár használata, ha konfigurálva van, vagy a belső gyorsítótár más módon. | Nem       | `prefer-external` |
| kulcs              | A gyorsítótárból eltávolítandó, korábban gyorsítótárazott érték kulcsa.                                                                                                                                                                                                                                                                                        | Igen      | N/A               |

#### <a name="usage"></a>Használat
Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) használható.

- **Házirendszakaszok:** bejövő, kimenő, háttérrendszer, hiba
- **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>További lépések

A házirendekkel kapcsolatos további információkért lásd:

+ [Szabályzatok az API Managementben](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ [Házirend-útmutató](api-management-policy-reference.md) a házirend-utasítások és beállításaik teljes listájához
+ [Házirendminták](policy-samples.md)
