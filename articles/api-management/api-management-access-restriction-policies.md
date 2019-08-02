---
title: Azure API Management hozzáférési korlátozási szabályzatok | Microsoft Docs
description: Ismerje meg az Azure API Management használható hozzáférés-korlátozási szabályzatokat.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 034febe3-465f-4840-9fc6-c448ef520b0f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: apimpm
ms.openlocfilehash: 8ee7db3ade594958729deeb12007f528376d5179
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2019
ms.locfileid: "68442412"
---
# <a name="api-management-access-restriction-policies"></a>Hozzáférés-korlátozási szabályzatok API Management

Ez a témakör az alábbi API Management szabályzatokra mutató hivatkozást tartalmaz. A házirendek hozzáadásával és konfigurálásával kapcsolatos információkért lásd: [szabályzatok API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AccessRestrictionPolicies"></a>Hozzáférés-korlátozási szabályzatok

-   [Http](api-management-access-restriction-policies.md#CheckHTTPHeader) -fejléc keresése – egy HTTP-fejléc létezésének és/vagy értékének betartatása.
-   A [hívások sebességének korlátozása](api-management-access-restriction-policies.md#LimitCallRate) előfizetéssel – MEGAKADÁLYOZZA az API-használat csúcsait a hívások sebességének korlátozásával, előfizetések alapján.
-   A [hívások gyakoriságának korlátozása kulcs szerint](#LimitCallRateByKey) – MEGAKADÁLYOZZA az API-használat csúcsait a hívások sebességének korlátozásával, kulcs alapján.
-   A [hívó IP](api-management-access-restriction-policies.md#RestrictCallerIPs) -címeinek korlátozása (engedélyezi vagy megtagadja) a HÍVÁSOKAT adott IP-címekről és/vagy címtartományból.
-   [Használati kvóta beállítása](api-management-access-restriction-policies.md#SetUsageQuota) előfizetéssel – lehetővé teszi, hogy előfizetések alapján kikényszerítse a megújítható vagy az élettartam szerinti hívások mennyiségét és/vagy sávszélesség-kvótáját.
-   [Használati kvóta beállítása kulccsal](#SetUsageQuotaByKey) – lehetővé teszi a megújítható vagy élettartamos hívások mennyiségi és/vagy sávszélesség-kvótájának kikényszeríthető kulcs alapján.
-   [JWT ellenőrzése](api-management-access-restriction-policies.md#ValidateJWT) – egy adott http-fejlécből vagy egy megadott lekérdezési paraméterből kinyert JWT létezését és érvényességét kényszeríti ki.

## <a name="CheckHTTPHeader"></a>HTTP-fejléc keresése

A `check-header` szabályzat használatával kényszerítheti ki, hogy egy kérelemnek van egy megadott http-fejléce. Ha szeretné, megtekintheti, hogy a fejléc adott értékkel rendelkezik-e, vagy hogy engedélyezett-e az értékek tartománya. Ha az ellenőrzés sikertelen, a házirend leállítja a kérelmek feldolgozását, és visszaadja a szabályzat által megadott HTTP-állapotkódot és hibaüzenetet.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>Példa

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>Elemek

| Name (Név)         | Leírás                                                                                                                                   | Kötelező |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| fejléc bejelölése | Gyökérelem.                                                                                                                                 | Igen      |
| value        | Engedélyezett HTTP-fejléc értéke. Ha több Value elem van megadva, az ellenőrzése sikeresnek tekintendő, ha az értékek bármelyike egyezés. | Nem       |

### <a name="attributes"></a>Attribútumok

| Name (Név)                       | Leírás                                                                                                                                                            | Kötelező | Alapértelmezett |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | A HTTP-válasz törzsében visszaadott hibaüzenet, ha a fejléc nem létezik, vagy érvénytelen értékkel rendelkezik. Az üzenetnek megfelelő speciális karakterekkel kell elmenekülnie. | Igen      | –     |
| Sikertelen bejelentkezés – httpcode      | HTTP-állapotkód, amely akkor tér vissza, ha a fejléc nem létezik vagy érvénytelen értékkel rendelkezik.                                                                                        | Igen      | –     |
| fejléc neve                | Az ellenőriznie kívánt HTTP-fejléc neve.                                                                                                                                  | Igen      | –     |
| Mellőzés – eset                | Értéke TRUE (igaz) vagy FALSE (hamis) lehet. Ha a True (igaz) értékre van állítva, a rendszer figyelmen kívül hagyja, ha a fejléc értékét összehasonlítja az elfogadható értékek halmazával.                                    | Igen      | –     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő

-   **Házirend-hatókörök:** az összes hatókör

## <a name="LimitCallRate"></a>A hívások sebességének korlátozása előfizetés szerint

A `rate-limit` házirend előfizetések alapján megakadályozza az API-használati tüskéket azáltal, hogy a megadott időszakra korlátozza a hívások sebességét egy megadott számra. Ha ez a szabályzat aktiválódik, a hívó megkapja a `429 Too Many Requests` válasz állapotkódot.

> [!IMPORTANT]
> Ez a szabályzat csak egyszer használható házirend-dokumentumként.
>
> [](api-management-policy-expressions.md) A szabályzat kifejezései nem használhatók a házirend egyik házirend-attribútumában sem.

> [!CAUTION]
> A szabályozási architektúra elosztott jellege miatt a díjszabási korlátozás soha nem teljesen pontos. A konfigurált és a valós idejű kérelmek száma közötti különbség a kérés mennyisége és sebessége, a háttérbeli késés és egyéb tényezők alapján változhat.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</rate-limit>
```

### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elemek

| Name (Név)      | Leírás                                                                                                                                                                                                                                                                                              | Kötelező |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| korlát beállítása | Gyökérelem.                                                                                                                                                                                                                                                                                            | Igen      |
| api       | Vegyen fel egy vagy több ilyen elemet a terméken belüli API-k hívási arányának korlátozására. A termék-és API-hívások díjszabását a rendszer egymástól függetlenül alkalmazza. Az API-t a vagy `name` `id`a használatával lehet hivatkozni. Ha mindkét attribútum meg van adva `id` , a rendszer ezt `name` fogja használni, és figyelmen kívül hagyja.                    | Nem       |
| operation | Vegyen fel egy vagy több ilyen elemet egy API-n belüli műveletek hívási sebességének korlátozására. A termék, az API és a műveleti hívási sebesség korlátai egymástól függetlenül érvényesek. A műveletet a vagy `name` `id`a használatával lehet hivatkozni. Ha mindkét attribútum meg van adva `id` , a rendszer ezt `name` fogja használni, és figyelmen kívül hagyja. | Nem       |

### <a name="attributes"></a>Attribútumok

| Name (Név)           | Leírás                                                                                           | Kötelező | Alapértelmezett |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | Annak az API-nak a neve, amelyre alkalmazni szeretné a díjszabási korlátot.                                                | Igen      | –     |
| hívás          | A által `renewal-period`megadott időintervallumban engedélyezett hívások maximális száma. | Igen      | –     |
| megújítás – időszak | Az az időtartam másodpercben, amely után a kvóta alaphelyzetbe áll.                                              | Igen      | –     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő

-   **Házirend-hatókörök:** termék, API, művelet

## <a name="LimitCallRateByKey"></a>A hívások sebességének korlátozása kulcs szerint

> [!IMPORTANT]
> Ez a funkció nem érhető el a API Management **felhasználási** szintjein.

A `rate-limit-by-key` házirend a megadott időszakra korlátozva meggátolja a hívások sebességét egy adott számra. A kulcs tetszőleges karakterlánc-értékkel rendelkezhet, és általában egy házirend-kifejezés használatával adható meg. Opcionális növekményes feltétel adható meg annak megadásához, hogy mely kérelmeket kell figyelembe venni a korláton. Ha ez a szabályzat aktiválódik, a hívó megkapja a `429 Too Many Requests` válasz állapotkódot.

A szabályzattal kapcsolatos további információkért és Példákért lásd: [speciális kérelmek szabályozása az Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

> [!CAUTION]
> A szabályozási architektúra elosztott jellege miatt a díjszabási korlátozás soha nem teljesen pontos. A konfigurált és a valós idejű kérelmek száma közötti különbség a kérés mennyisége és sebessége, a háttérbeli késés és egyéb tényezők alapján változhat.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>Példa

A következő példában a hívó IP-címe a díjszabási korlátot.

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elemek

| Name (Név)      | Leírás   | Kötelező |
| --------- | ------------- | -------- |
| korlát beállítása | Gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Name (Név)                | Leírás                                                                                           | Kötelező | Alapértelmezett |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| hívás               | A által `renewal-period`megadott időintervallumban engedélyezett hívások maximális száma. | Igen      | –     |
| számláló – kulcs         | A díjszabási szabályzathoz használandó kulcs.                                                             | Igen      | –     |
| increment-condition | A logikai kifejezés, amely megadja, hogy a kérést a kvóta (`true`) felé kell-e számítani.        | Nem       | –     |
| megújítás – időszak      | Az az időtartam másodpercben, amely után a kvóta alaphelyzetbe áll.                                              | Igen      | –     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő

-   **Házirend-hatókörök:** az összes hatókör

## <a name="RestrictCallerIPs"></a>Hívó IP-címeinek korlátozása

A `ip-filter` szabályzat szűrői (engedélyezheti/megtagadja) a hívásokat adott IP-címekről, illetve címtartományból.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Példa

A következő példában a házirend csak az egyetlen IP-címről vagy a megadott IP-címtartományból érkező kéréseket engedélyezi

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Elemek

| Name (Név)                                      | Leírás                                         | Kötelező                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filter                                 | Gyökérelem.                                       | Igen                                                            |
| cím                                   | Egyetlen IP-címet ad meg a szűréshez.   | Legalább egy `address` `address-range` elemet kötelező megadni. |
| címtartomány a következőből: = "címe" – = "címe" | A szűrni kívánt IP-címtartomány megadása. | Legalább egy `address` `address-range` elemet kötelező megadni. |

### <a name="attributes"></a>Attribútumok

| Name (Név)                                      | Leírás                                                                                 | Kötelező                                           | Alapértelmezett |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| címtartomány a következőből: = "címe" – = "címe" | IP-címek tartománya, amely engedélyezi vagy megtagadja a hozzáférést.                                        | A `address-range` elem használatakor szükséges. | –     |
| IP-szűrési művelet = " &#124; tiltás engedélyezése"    | Megadja, hogy a hívások engedélyezettek-e, vagy sem a megadott IP-címekhez és tartományokhoz. | Igen                                                | –     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő
-   **Házirend-hatókörök:** az összes hatókör

## <a name="SetUsageQuota"></a>Használati kvóta beállítása előfizetés szerint

A `quota` szabályzat előfizetése alapján kikényszeríti a megújítható vagy életprevalencia-hívások mennyiségét és/vagy sávszélesség-kvótáját.

> [!IMPORTANT]
> Ez a szabályzat csak egyszer használható házirend-dokumentumként.
>
> [](api-management-policy-expressions.md) A szabályzat kifejezései nem használhatók a házirend egyik házirend-attribútumában sem.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>Példa

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elemek

| Name (Név)      | Leírás                                                                                                                                                                                                                                                                                  | Kötelező |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| kvóta     | Gyökérelem.                                                                                                                                                                                                                                                                                | Igen      |
| api       | Vegyen fel egy vagy több ilyen elemet a terméken belüli API-kra vonatkozó hívási kvóta kiszabásához. A termék-és API-hívások kvótái egymástól függetlenül vannak alkalmazva. Az API-t a vagy `name` `id`a használatával lehet hivatkozni. Ha mindkét attribútum meg van adva `id` , a rendszer ezt `name` fogja használni, és figyelmen kívül hagyja.                    | Nem       |
| operation | Vegyen fel egy vagy több ilyen elemet egy API-n belüli műveletekre vonatkozó hívási kvóta kiszabásához. A termék-, API-és műveleti hívások kvótái egymástól függetlenül vannak alkalmazva. A műveletet a vagy `name` `id`a használatával lehet hivatkozni. Ha mindkét attribútum meg van adva `id` , a rendszer ezt `name` fogja használni, és figyelmen kívül hagyja. | Nem       |

### <a name="attributes"></a>Attribútumok

| Name (Név)           | Leírás                                                                                               | Kötelező                                                         | Alapértelmezett |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | Annak az API-nak vagy műveletnek a neve, amelyre a kvóta vonatkozik.                                             | Igen                                                              | –     |
| Sávszélesség      | A-ben `renewal-period`megadott időintervallumban engedélyezett maximálisan megengedett kilobájtok száma. | Vagy `calls` ,`bandwidth`vagy mindkettőt meg kell adni. | –     |
| hívás          | A által `renewal-period`megadott időintervallumban engedélyezett hívások maximális száma.     | Vagy `calls` ,`bandwidth`vagy mindkettőt meg kell adni. | –     |
| megújítás – időszak | Az az időtartam másodpercben, amely után a kvóta alaphelyzetbe áll.                                                  | Igen                                                              | –     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő
-   **Házirend-hatókörök:** termék

## <a name="SetUsageQuotaByKey"></a>Használati kvóta beállítása kulcs szerint

> [!IMPORTANT]
> Ez a funkció nem érhető el a API Management **felhasználási** szintjein.

A `quota-by-key` szabályzat a megújítható vagy az élettartam szerinti hívások mennyiségét és/vagy sávszélességét kényszeríti ki a kulcs alapján. A kulcs tetszőleges karakterlánc-értékkel rendelkezhet, és általában egy házirend-kifejezés használatával adható meg. Opcionális növekményes feltétel adható meg annak megadásához, hogy mely kérelmeket kell figyelembe venni a kvóta felé. Ha több házirend is megnöveli a kulcs értékét, akkor a kérések száma csak egyszer növekszik. Ha elérte a hívási korlátot, a hívó megkapja `403 Forbidden` a válasz állapotkódot.

A szabályzattal kapcsolatos további információkért és Példákért lásd: [speciális kérelmek szabályozása az Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/).

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Példa

A következő példában a kvóta a hívó IP-címe alapján van megjelölve.

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>Elemek

| Name (Név)  | Leírás   | Kötelező |
| ----- | ------------- | -------- |
| kvóta | Gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Name (Név)                | Leírás                                                                                               | Kötelező                                                         | Alapértelmezett |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| Sávszélesség           | A-ben `renewal-period`megadott időintervallumban engedélyezett maximálisan megengedett kilobájtok száma. | Vagy `calls` ,`bandwidth`vagy mindkettőt meg kell adni. | –     |
| hívás               | A által `renewal-period`megadott időintervallumban engedélyezett hívások maximális száma.     | Vagy `calls` ,`bandwidth`vagy mindkettőt meg kell adni. | –     |
| számláló – kulcs         | A kvóta-házirendhez használandó kulcs.                                                                      | Igen                                                              | –     |
| increment-condition | A logikai kifejezés, amely megadja, hogy a kérést a kvóta felé kell-`true`e számítani ()             | Nem                                                               | –     |
| megújítás – időszak      | Az az időtartam másodpercben, amely után a kvóta alaphelyzetbe áll.                                                  | Igen                                                              | –     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő
-   **Házirend-hatókörök:** az összes hatókör

## <a name="ValidateJWT"></a>JWT ellenőrzése

A `validate-jwt` szabályzat egy adott http-fejlécből vagy egy megadott lekérdezési paraméterből kinyert JWT létezését és érvényességét kényszeríti ki.

> [!IMPORTANT]
> A `validate-jwt` szabályzat megköveteli, hogy `exp` a regisztrált jogcím szerepeljen az JWT-tokenben, `require-expiration-time` kivéve, ha az attribútum meg `false`van adva, és a értékre van állítva.
> A `validate-jwt` szabályzat támogatja a HS256 és a RS256 aláírási algoritmusokat. A HS256 a kulcsot a Base64 kódolású űrlapon belül kell megadni a szabályzatban. Ahhoz, hogy RS256 a kulcsot meg kell adni egy Open ID konfigurációs végponton keresztül.
> A `validate-jwt` házirend támogatja a szimmetrikus kulcsokkal titkosított tokeneket a következő titkosítási algoritmusok használatával: A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <zumo-master-key id="key identifier">key value</zumo-master-key>
</validate-jwt>

```

### <a name="examples"></a>Példák

#### <a name="simple-token-validation"></a>Egyszerű jogkivonat ellenőrzése

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Azure Active Directory jogkivonat ellenőrzése

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Azure Active Directory B2C jogkivonat ellenőrzése

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>A jogkivonatok jogcímein alapuló műveletekhez való hozzáférés engedélyezése

Ez a példa azt mutatja be, hogyan használható a [JWT](api-management-access-restriction-policies.md#ValidateJWT) -érvényesítési házirend a műveletekhez való hozzáférés engedélyezésére a jogkivonat-jogcímek értéke alapján.

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

#### <a name="azure-mobile-services-token-validation"></a>Azure Mobile Services jogkivonat ellenőrzése

```xml
<validate-jwt header-name="x-zumo-auth" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Supplied access token is invalid.">
    <issuers>
        <issuer>urn:microsoft:windows-azure:zumo</issuer>
    </issuers>
    <audiences>
        <audience>Facebook</audience>
    </audiences>
    <issuer-signing-keys>
        <zumo-master-key id="0">insert key here</zumo-master-key>
    </issuer-signing-keys>
</validate-jwt>
```

### <a name="elements"></a>Elemek

| Elem             | Leírás                                                                                                                                                                                                                                                                                                                                           | Kötelező |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| ellenőrzés – JWT        | Gyökérelem.                                                                                                                                                                                                                                                                                                                                         | Igen      |
| közönség           | A jogkivonatban található elfogadható célközönségi jogcímek listáját tartalmazza. Ha több célközönség érték van megadva, akkor minden érték az összes Kimerítés után próbálkozik (ebben az esetben az ellenőrzés meghiúsul), vagy amíg az egyik sikeres. Legalább egy célközönséget meg kell adni.                                                                     | Nem       |
| kiállító – aláíró kulcsok | Az aláírt tokenek ellenőrzéséhez használt Base64 kódolású biztonsági kulcsok listája. Ha több biztonsági kulcs is van, akkor a rendszer minden kulcsot megpróbál, amíg az összes kimerül (ebben az esetben az ellenőrzés meghiúsul), vagy amíg az egyik sikeres (a jogkivonat-átváltáshoz hasznos). A kulcsfontosságú elemekhez választható `id` attribútum tartozik, amely a `kid` jogcímek közötti egyezésre szolgál.               | Nem       |
| visszafejtés – kulcsok     | A jogkivonatok visszafejtéséhez használt Base64 kódolású kulcsok listája. Ha több biztonsági kulcs is van, akkor a rendszer minden kulcsot megpróbál, amíg az összes kulcs ki nem merül (ebben az esetben az ellenőrzés meghiúsul), vagy amíg a kulcs nem sikerül. A kulcsfontosságú elemekhez választható `id` attribútum tartozik, amely a `kid` jogcímek közötti egyezésre szolgál.                                                 | Nem       |
| kibocsátók             | A jogkivonatot kiállító elfogadható rendszerbiztonsági tag listája. Ha több kiállítói érték is létezik, akkor minden egyes értéket megpróbál a rendszer, amíg az összes ki nem fejeződik (ebben az esetben az ellenőrzés meghiúsul), vagy amíg az egyik sikeres.                                                                                                                                         | Nem       |
| OpenID – konfiguráció       | A megfelelőségi azonosító konfigurációs végpontjának megadásához használt elem, amelyből megszerezhetők az aláíró kulcsok és a kibocsátók.                                                                                                                                                                                                                        | Nem       |
| kötelező – jogcímek     | Azon jogcímek listáját tartalmazza, amelyeknek a jogkivonatban szerepelniük kell, hogy az érvényes legyen. Ha az `match` attribútum `all` minden jogcím értékre van beállítva, akkor a jogkivonatban jelen kell lennie ahhoz, hogy az érvényesítés sikeres legyen. Ha az `match` attribútum `any` értéke legalább egy jogcím, akkor a jogkivonatban jelen kell lennie ahhoz, hogy az érvényesítés sikeres legyen. | Nem       |
| zumo-master-key     | Az Azure Mobile Services által kiállított tokenek főkulcsa                                                                                                                                                                                                                                                                                                 | Nem       |

### <a name="attributes"></a>Attribútumok

| Name (Név)                            | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                            | Kötelező                                                                         | Alapértelmezett                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| óra – döntés                      | TimeSpan. Ezzel a beállítással adható meg a jogkivonat-kiállító és a API Management-példány rendszerórája közötti maximális várható időeltérés.                                                                                                                                                                                                                                                                                                               | Nem                                                                               | 0 másodperc                                                                         |
| failed-validation-error-message | A HTTP-válasz törzsében visszaadott hibaüzenet, ha a JWT nem ad át érvényesítést. Az üzenetnek megfelelő speciális karakterekkel kell elmenekülnie.                                                                                                                                                                                                                                                                                                 | Nem                                                                               | Az alapértelmezett hibaüzenet az érvényesítési problémától függ, például "a JWT nem jelennek meg". |
| sikertelen – érvényesítés – httpcode      | HTTP-állapotkód, amely akkor tér vissza, ha a JWT nem felel meg az érvényesítésnek.                                                                                                                                                                                                                                                                                                                                                                                         | Nem                                                                               | 401                                                                               |
| fejléc neve                     | A jogkivonatot birtokló HTTP-fejléc neve.                                                                                                                                                                                                                                                                                                                                                                                                         | Az `header-name`egyiket `query-parameter-name` , `token-value` vagy meg kell adni. | –                                                                               |
| lekérdezés-paraméter-neve            | A jogkivonatot tároló lekérdezési paraméter neve.                                                                                                                                                                                                                                                                                                                                                                                                     | Az `header-name`egyiket `query-parameter-name` , `token-value` vagy meg kell adni. | –                                                                               |
| jogkivonat-érték                     | JWT tokent tartalmazó sztringet visszaadó kifejezés                                                                                                                                                                                                                                                                                                                                                                                                     | Az `header-name`egyiket `query-parameter-name` , `token-value` vagy meg kell adni. | –                                                                               |
| id                              | Az elem attribútuma lehetővé teszi annak a karakterláncnak a megadását, amely `kid` a jogkivonatban található jogcímek között szerepel (ha van), hogy kiderítse az aláírás-ellenőrzéshez használandó megfelelő kulcsot. `id` `key`                                                                                                                                                                                                                                           | Nem                                                                               | –                                                                               |
| egyezés                           | Az `match` `claim` elem attribútuma azt határozza meg, hogy a házirendben szereplő összes jogcím értékének szerepelnie kell-e a jogkivonatban az érvényesítés sikerességéhez. Lehetséges értékek a következők:<br /><br /> - `all`– a szabályzatban szereplő összes jogcím értékének jelen kell lennie a jogkivonatban, hogy az érvényesítés sikeres legyen.<br /><br /> - `any`– legalább egy jogcím értékének szerepelnie kell a jogkivonatban, hogy sikeres legyen az érvényesítés.                                                       | Nem                                                                               | mind                                                                               |
| require-expiration-time         | Logikai. Meghatározza, hogy szükséges-e lejárati jogcím a jogkivonatban.                                                                                                                                                                                                                                                                                                                                                                               | Nem                                                                               | true                                                                              |
| szükséges – séma                  | A jogkivonat-séma neve, például: "Tulajdonos". Ha ez az attribútum be van állítva, akkor a házirend biztosítja, hogy a megadott séma megtalálható legyen az engedélyezési fejléc értékében.                                                                                                                                                                                                                                                                                    | Nem                                                                               | –                                                                               |
| kötelező aláírású tokenek           | Logikai. Megadja, hogy szükséges-e a jogkivonat aláírása.                                                                                                                                                                                                                                                                                                                                                                                           | Nem                                                                               | true                                                                              |
| elválasztó                       | Karakterlánc. Meghatározza a többértékű jogcímből származó értékek kinyeréséhez használandó elválasztó karaktert (például ",").                                                                                                                                                                                                                                                                                                                                          | Nem                                                                               | –                                                                               |
| url                             | Nyissa meg az azonosító konfigurációs végpontjának URL-címét, amelyből megkérheti a nyitott azonosító konfigurációs metaadatait. A válasznak a következő URL-címen definiált specifikációknak kell`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`megjelennie:. Azure Active Directory használja a következő URL-címet `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` : a címtár-bérlő nevének helyettesítése, `contoso.onmicrosoft.com`például. | Igen                                                                              | –                                                                               |
output-token-változó-neve|Karakterlánc. Annak a környezeti változónak a neve, amely a jogkivonat értékét a sikeres [`Jwt`](api-management-policy-expressions.md) jogkivonat-ellenőrzés után egy típusú objektumként fogja fogadni|Nem|–

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend-részekben [](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő
-   **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>További lépések

További információ a házirendek használatáról:

-   [Szabályzatok API Management](api-management-howto-policies.md)
-   [API-k átalakítása](transform-api.md)
-   Házirend- [hivatkozás](api-management-policy-reference.md) a szabályzat-utasítások és azok beállításainak teljes listájához
-   [Házirend-minták](policy-samples.md)
