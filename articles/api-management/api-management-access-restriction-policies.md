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
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 3ba620d66b84e6724751b2024059e8ecd66888cd
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79266116"
---
# <a name="api-management-access-restriction-policies"></a>Hozzáférés-korlátozási szabályzatok API Management

Ez a témakör az alábbi API Management szabályzatokra mutató hivatkozást tartalmaz. A házirendek hozzáadásával és konfigurálásával kapcsolatos információkért lásd: [szabályzatok API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

## <a name="AccessRestrictionPolicies"></a>Hozzáférés-korlátozási szabályzatok

-   [Http](api-management-access-restriction-policies.md#CheckHTTPHeader) -fejléc keresése – egy HTTP-fejléc létezésének és/vagy értékének betartatása.
-   A [hívások sebességének korlátozása előfizetéssel](api-management-access-restriction-policies.md#LimitCallRate) – MEGAKADÁLYOZZA az API-használat csúcsait a hívások sebességének korlátozásával, előfizetések alapján.
-   A [hívások gyakoriságának korlátozása kulcs szerint](#LimitCallRateByKey) – MEGAKADÁLYOZZA az API-használat csúcsait a hívások sebességének korlátozásával, kulcs alapján.
-   A [hívó IP](api-management-access-restriction-policies.md#RestrictCallerIPs) -címeinek korlátozása (engedélyezi vagy megtagadja) a HÍVÁSOKAT adott IP-címekről és/vagy címtartományból.
-   [Használati kvóta beállítása előfizetéssel](api-management-access-restriction-policies.md#SetUsageQuota) – lehetővé teszi, hogy előfizetések alapján kikényszerítse a megújítható vagy az élettartam szerinti hívások mennyiségét és/vagy sávszélesség-kvótáját.
-   [Használati kvóta beállítása kulccsal](#SetUsageQuotaByKey) – lehetővé teszi a megújítható vagy élettartamos hívások mennyiségi és/vagy sávszélesség-kvótájának kikényszeríthető kulcs alapján.
-   [JWT ellenőrzése](api-management-access-restriction-policies.md#ValidateJWT) – egy adott http-fejlécből vagy egy megadott lekérdezési paraméterből kinyert JWT létezését és érvényességét kényszeríti ki.

> [!TIP]
> A hozzáférési korlátozási házirendeket különböző hatókörökben használhatja különböző célokra. Például a teljes API-t biztonságossá teheti a HRE-hitelesítéssel, ha a `validate-jwt` szabályzatot az API szintjén alkalmazza, vagy az API-művelet szintjén alkalmazhatja, és részletesebben is használhatja a `claims`.

## <a name="CheckHTTPHeader"></a>HTTP-fejléc keresése

A `check-header` házirend használatával kényszerítheti ki, hogy egy kérelemnek van egy megadott HTTP-fejléce. Ha szeretné, megtekintheti, hogy a fejléc adott értékkel rendelkezik-e, vagy hogy engedélyezett-e az értékek tartománya. Ha az ellenőrzés sikertelen, a házirend leállítja a kérelmek feldolgozását, és visszaadja a szabályzat által megadott HTTP-állapotkódot és hibaüzenetet.

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
| érték        | Engedélyezett HTTP-fejléc értéke. Ha több Value elem van megadva, az ellenőrzése sikeresnek tekintendő, ha az értékek bármelyike egyezés. | Nem       |

### <a name="attributes"></a>Attribútumok

| Name (Név)                       | Leírás                                                                                                                                                            | Kötelező | Alapértelmezett |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | A HTTP-válasz törzsében visszaadott hibaüzenet, ha a fejléc nem létezik, vagy érvénytelen értékkel rendelkezik. Az üzenetnek megfelelő speciális karakterekkel kell elmenekülnie. | Igen      | N/A     |
| Sikertelen bejelentkezés – httpcode      | HTTP-állapotkód, amely akkor tér vissza, ha a fejléc nem létezik vagy érvénytelen értékkel rendelkezik.                                                                                        | Igen      | N/A     |
| fejléc neve                | Az ellenőriznie kívánt HTTP-fejléc neve.                                                                                                                                  | Igen      | N/A     |
| Mellőzés – eset                | Értéke TRUE (igaz) vagy FALSE (hamis) lehet. Ha a True (igaz) értékre van állítva, a rendszer figyelmen kívül hagyja, ha a fejléc értékét összehasonlítja az elfogadható értékek halmazával.                                    | Igen      | N/A     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő, kimenő

-   **Házirend-hatókörök:** az összes hatókör

## <a name="LimitCallRate"></a>A hívások sebességének korlátozása előfizetés szerint

Az `rate-limit` házirend előfizetési alapon megakadályozza az API-használati tüskéket azáltal, hogy a megadott időszakra korlátozza a hívási sebességet egy megadott számra. Ha ezt a házirendet elindítja, a hívó megkapja a `429 Too Many Requests` Response állapotkódot.

> [!IMPORTANT]
> Ez a szabályzat csak egyszer használható házirend-dokumentumként.
>
> A szabályzat [kifejezései](api-management-policy-expressions.md) nem használhatók a házirend egyik házirend-attribútumában sem.

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

| Name (Név)       | Leírás                                                                                                                                                                                                                                                                                              | Kötelező |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| korlát mértéke | Gyökérelem.                                                                                                                                                                                                                                                                                            | Igen      |
| api        | Vegyen fel egy vagy több ilyen elemet a terméken belüli API-k hívási arányának korlátozására. A termék-és API-hívások díjszabását a rendszer egymástól függetlenül alkalmazza. Az API-t `name` vagy `id`használatával lehet hivatkozni. Ha mindkét attribútum meg van adva, `id` lesz használatban, és `name` figyelmen kívül lesz hagyva.                    | Nem       |
| operation  | Vegyen fel egy vagy több ilyen elemet egy API-n belüli műveletek hívási sebességének korlátozására. A termék, az API és a műveleti hívási sebesség korlátai egymástól függetlenül érvényesek. A művelet `name` vagy `id`használatával is hivatkozhat. Ha mindkét attribútum meg van adva, `id` lesz használatban, és `name` figyelmen kívül lesz hagyva. | Nem       |

### <a name="attributes"></a>Attribútumok

| Name (Név)           | Leírás                                                                                           | Kötelező | Alapértelmezett |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| név           | Annak az API-nak a neve, amelyre alkalmazni szeretné a díjszabási korlátot.                                                | Igen      | N/A     |
| hívások          | A `renewal-period`ban megadott időintervallumban engedélyezett hívások maximális száma. | Igen      | N/A     |
| megújítás – időszak | Az az időtartam másodpercben, amely után a kvóta alaphelyzetbe áll.                                              | Igen      | N/A     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő

-   **Házirend-hatókörök:** termék, API, művelet

## <a name="LimitCallRateByKey"></a>A hívások sebességének korlátozása kulcs szerint

> [!IMPORTANT]
> Ez a funkció nem érhető el a API Management **felhasználási** szintjein.

A `rate-limit-by-key` házirend az API-k használatának meggátolása a kulcs alapján, ha a hívási sebességet egy adott időtartamra korlátozza. A kulcs tetszőleges karakterlánc-értékkel rendelkezhet, és általában egy házirend-kifejezés használatával adható meg. Opcionális növekményes feltétel adható meg annak megadásához, hogy mely kérelmeket kell figyelembe venni a korláton. Ha ezt a házirendet elindítja, a hívó megkapja a `429 Too Many Requests` Response állapotkódot.

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

| Name (Név)              | Leírás   | Kötelező |
| ----------------- | ------------- | -------- |
| mérték – korlát – kulcs | Gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Name (Név)                | Leírás                                                                                           | Kötelező | Alapértelmezett |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| hívások               | A `renewal-period`ban megadott időintervallumban engedélyezett hívások maximális száma. | Igen      | N/A     |
| számláló – kulcs         | A díjszabási szabályzathoz használandó kulcs.                                                             | Igen      | N/A     |
| increment-condition | A logikai kifejezés, amely megadja, hogy a kérést a kvóta (`true`) felé kell-e számítani.        | Nem       | N/A     |
| megújítás – időszak      | Az az időtartam másodpercben, amely után a kvóta alaphelyzetbe áll.                                              | Igen      | N/A     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő

-   **Házirend-hatókörök:** az összes hatókör

## <a name="RestrictCallerIPs"></a>Hívó IP-címeinek korlátozása

A `ip-filter` házirend-szűrők (engedélyezése/megtagadása) adott IP-címekről és/vagy címtartományból érkező hívások.

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
| Cím                                   | Egyetlen IP-címet ad meg a szűréshez.   | Legalább egy `address` vagy `address-range` elem megadása kötelező. |
| címtartomány a következőből: = "címe" – = "címe" | A szűrni kívánt IP-címtartomány megadása. | Legalább egy `address` vagy `address-range` elem megadása kötelező. |

### <a name="attributes"></a>Attribútumok

| Name (Név)                                      | Leírás                                                                                 | Kötelező                                           | Alapértelmezett |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| címtartomány a következőből: = "címe" – = "címe" | IP-címek tartománya, amely engedélyezi vagy megtagadja a hozzáférést.                                        | A `address-range` elem használatakor szükséges. | N/A     |
| IP-szűrési művelet = " &#124; tiltás engedélyezése"    | Megadja, hogy a hívások engedélyezettek-e, vagy sem a megadott IP-címekhez és tartományokhoz. | Igen                                                | N/A     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő
-   **Házirend-hatókörök:** az összes hatókör

## <a name="SetUsageQuota"></a>Használati kvóta beállítása előfizetés szerint

Az `quota` szabályzat előfizetési alapon érvényesíti a megújítható vagy életprevalencia-hívások mennyiségét és/vagy sávszélesség-kvótáját.

> [!IMPORTANT]
> Ez a szabályzat csak egyszer használható házirend-dokumentumként.
>
> A szabályzat [kifejezései](api-management-policy-expressions.md) nem használhatók a házirend egyik házirend-attribútumában sem.

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
| api       | Vegyen fel egy vagy több ilyen elemet a terméken belüli API-kra vonatkozó hívási kvóta kiszabásához. A termék-és API-hívások kvótái egymástól függetlenül vannak alkalmazva. Az API-t `name` vagy `id`használatával lehet hivatkozni. Ha mindkét attribútum meg van adva, `id` lesz használatban, és `name` figyelmen kívül lesz hagyva.                    | Nem       |
| operation | Vegyen fel egy vagy több ilyen elemet egy API-n belüli műveletekre vonatkozó hívási kvóta kiszabásához. A termék-, API-és műveleti hívások kvótái egymástól függetlenül vannak alkalmazva. A művelet `name` vagy `id`használatával is hivatkozhat. Ha mindkét attribútum meg van adva, `id` lesz használatban, és `name` figyelmen kívül lesz hagyva. | Nem       |

### <a name="attributes"></a>Attribútumok

| Name (Név)           | Leírás                                                                                               | Kötelező                                                         | Alapértelmezett |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| név           | Annak az API-nak vagy műveletnek a neve, amelyre a kvóta vonatkozik.                                             | Igen                                                              | N/A     |
| sávszélesség      | A `renewal-period`ban megadott időintervallumban engedélyezett maximális számú kilobájt. | `calls`, `bandwidth`vagy mindkettőt meg kell adni. | N/A     |
| hívások          | A `renewal-period`ban megadott időintervallumban engedélyezett hívások maximális száma.     | `calls`, `bandwidth`vagy mindkettőt meg kell adni. | N/A     |
| megújítás – időszak | Az az időtartam másodpercben, amely után a kvóta alaphelyzetbe áll.                                                  | Igen                                                              | N/A     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő
-   **Házirend-hatókörök:** termék

## <a name="SetUsageQuotaByKey"></a>Használati kvóta beállítása kulcs szerint

> [!IMPORTANT]
> Ez a funkció nem érhető el a API Management **felhasználási** szintjein.

A `quota-by-key` szabályzat a megújítható vagy élettartam-hívások mennyiségi és/vagy sávszélesség-kvótáját érvényesíti a kulcs alapján. A kulcs tetszőleges karakterlánc-értékkel rendelkezhet, és általában egy házirend-kifejezés használatával adható meg. Opcionális növekményes feltétel adható meg annak megadásához, hogy mely kérelmeket kell figyelembe venni a kvóta felé. Ha több házirend is megnöveli a kulcs értékét, akkor a kérések száma csak egyszer növekszik. Ha eléri a hívási korlátot, a hívó megkapja a `403 Forbidden` Response (válasz) állapotkódot.

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
| sávszélesség           | A `renewal-period`ban megadott időintervallumban engedélyezett maximális számú kilobájt. | `calls`, `bandwidth`vagy mindkettőt meg kell adni. | N/A     |
| hívások               | A `renewal-period`ban megadott időintervallumban engedélyezett hívások maximális száma.     | `calls`, `bandwidth`vagy mindkettőt meg kell adni. | N/A     |
| számláló – kulcs         | A kvóta-házirendhez használandó kulcs.                                                                      | Igen                                                              | N/A     |
| increment-condition | A logikai kifejezés, amely megadja, hogy a kérést a kvóta felé kell-e számítani (`true`)             | Nem                                                               | N/A     |
| megújítás – időszak      | Az az időtartam másodpercben, amely után a kvóta alaphelyzetbe áll.                                                  | Igen                                                              | N/A     |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő
-   **Házirend-hatókörök:** az összes hatókör

## <a name="ValidateJWT"></a>JWT ellenőrzése

A `validate-jwt` szabályzat egy adott HTTP-fejlécből vagy egy megadott lekérdezési paraméterből kinyert JWT létezését és érvényességét kényszeríti ki.

> [!IMPORTANT]
> A `validate-jwt` házirend megköveteli, hogy a `exp` regisztrált jogcím szerepeljen az JWT-tokenben, kivéve, ha `require-expiration-time` attribútum van megadva, és a `false`értékre van állítva.
> A `validate-jwt` házirend támogatja a HS256 és a RS256 aláírási algoritmusokat. A HS256 a kulcsot a Base64 kódolású űrlapon belül kell megadni a szabályzatban. Ahhoz, hogy RS256 a kulcsot meg kell adni egy Open ID konfigurációs végponton keresztül.
> A `validate-jwt` szabályzat a szimmetrikus kulcsokkal titkosított tokeneket támogatja a következő titkosítási algoritmusok használatával: A128CBC-HS256, A192CBC-HS384, A256CBC-HS512.

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
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
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

Ez a példa azt mutatja be, hogyan használható a [JWT-érvényesítési](api-management-access-restriction-policies.md#ValidateJWT) házirend a műveletekhez való hozzáférés engedélyezésére a jogkivonat-jogcímek értéke alapján.

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

### <a name="elements"></a>Elemek

| Elem             | Leírás                                                                                                                                                                                                                                                                                                                                           | Kötelező |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| ellenőrzés – JWT        | Gyökérelem.                                                                                                                                                                                                                                                                                                                                         | Igen      |
| közönség           | A jogkivonatban található elfogadható célközönségi jogcímek listáját tartalmazza. Ha több célközönség érték van megadva, akkor minden érték az összes Kimerítés után próbálkozik (ebben az esetben az ellenőrzés meghiúsul), vagy amíg az egyik sikeres. Legalább egy célközönséget meg kell adni.                                                                     | Nem       |
| kiállító – aláíró kulcsok | Az aláírt tokenek ellenőrzéséhez használt Base64 kódolású biztonsági kulcsok listája. Ha több biztonsági kulcs is van, akkor a rendszer minden kulcsot megpróbál, amíg az összes kimerül (ebben az esetben az ellenőrzés meghiúsul), vagy amíg az egyik sikeres (a jogkivonat-átváltáshoz hasznos). A kulcsfontosságú elemekhez választható `id` attribútumot kell használni `kid` jogcímnek való megfeleléshez.               | Nem       |
| visszafejtés – kulcsok     | A jogkivonatok visszafejtéséhez használt Base64 kódolású kulcsok listája. Ha több biztonsági kulcs is van, akkor a rendszer minden kulcsot megpróbál, amíg az összes kulcs ki nem merül (ebben az esetben az ellenőrzés meghiúsul), vagy amíg a kulcs nem sikerül. A kulcsfontosságú elemekhez választható `id` attribútumot kell használni `kid` jogcímnek való megfeleléshez.                                                 | Nem       |
| kibocsátók             | A jogkivonatot kiállító elfogadható rendszerbiztonsági tag listája. Ha több kiállítói érték is létezik, akkor minden egyes értéket megpróbál a rendszer, amíg az összes ki nem fejeződik (ebben az esetben az ellenőrzés meghiúsul), vagy amíg az egyik sikeres.                                                                                                                                         | Nem       |
| OpenID – konfiguráció       | A megfelelőségi azonosító konfigurációs végpontjának megadásához használt elem, amelyből megszerezhetők az aláíró kulcsok és a kibocsátók.                                                                                                                                                                                                                        | Nem       |
| kötelező – jogcímek     | Azon jogcímek listáját tartalmazza, amelyeknek a jogkivonatban szerepelniük kell, hogy az érvényes legyen. Ha a `match` attribútum úgy van beállítva `all`, hogy a házirendben szereplő minden jogcím értéke legyen jelen a jogkivonatban, a sikeres érvényesítéshez a tokenben szerepelnie kell. Ha a `match` attribútum értéke `any`, akkor a jogkivonatban szerepelnie kell legalább egy jogcímnek, hogy az érvényesítés sikeres legyen. | Nem       |

### <a name="attributes"></a>Attribútumok

| Name (Név)                            | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                            | Kötelező                                                                         | Alapértelmezett                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| óra – döntés                      | TimeSpan. Ezzel a beállítással adható meg a jogkivonat-kiállító és a API Management-példány rendszerórája közötti maximális várható időeltérés.                                                                                                                                                                                                                                                                                                               | Nem                                                                               | 0 másodperc                                                                         |
| failed-validation-error-message | A HTTP-válasz törzsében visszaadott hibaüzenet, ha a JWT nem ad át érvényesítést. Az üzenetnek megfelelő speciális karakterekkel kell elmenekülnie.                                                                                                                                                                                                                                                                                                 | Nem                                                                               | Az alapértelmezett hibaüzenet az érvényesítési problémától függ, például "a JWT nem jelennek meg". |
| sikertelen – érvényesítés – httpcode      | HTTP-állapotkód, amely akkor tér vissza, ha a JWT nem felel meg az érvényesítésnek.                                                                                                                                                                                                                                                                                                                                                                                         | Nem                                                                               | 401                                                                               |
| fejléc neve                     | A jogkivonatot birtokló HTTP-fejléc neve.                                                                                                                                                                                                                                                                                                                                                                                                         | `header-name`, `query-parameter-name` vagy `token-value` egyikét meg kell adni. | N/A                                                                               |
| lekérdezés-paraméter-neve            | A jogkivonatot tároló lekérdezési paraméter neve.                                                                                                                                                                                                                                                                                                                                                                                                     | `header-name`, `query-parameter-name` vagy `token-value` egyikét meg kell adni. | N/A                                                                               |
| jogkivonat-érték                     | JWT tokent tartalmazó sztringet visszaadó kifejezés                                                                                                                                                                                                                                                                                                                                                                                                     | `header-name`, `query-parameter-name` vagy `token-value` egyikét meg kell adni. | N/A                                                                               |
| id                              | A `key` elem `id` attribútuma lehetővé teszi, hogy megtudja, hogy milyen karakterláncot szeretne egyeztetni a jogkivonat `kid` jogcímével (ha van), hogy megtalálja az aláírás-ellenőrzéshez használandó megfelelő kulcsot.                                                                                                                                                                                                                                           | Nem                                                                               | N/A                                                                               |
| Mérkőzés                           | A `claim` elem `match` attribútuma azt határozza meg, hogy a szabályzatban szereplő összes jogcím értékének jelen kell lennie a jogkivonatban, hogy sikeres legyen az érvényesítés. Lehetséges értékek:<br /><br /> - `all` – a házirendben szereplő összes jogcím értékének jelen kell lennie a jogkivonatban, hogy sikeres legyen az érvényesítés.<br /><br /> - `any` – legalább egy jogcím értékének szerepelnie kell a jogkivonatban ahhoz, hogy az érvényesítés sikeres legyen.                                                       | Nem                                                                               | összes                                                                               |
| require-expiration-time         | Logikai. Meghatározza, hogy szükséges-e lejárati jogcím a jogkivonatban.                                                                                                                                                                                                                                                                                                                                                                               | Nem                                                                               | true                                                                              |
| szükséges – séma                  | A jogkivonat-séma neve, például "tulajdonos". Ha ez az attribútum be van állítva, akkor a házirend biztosítja, hogy a megadott séma megtalálható legyen az engedélyezési fejléc értékében.                                                                                                                                                                                                                                                                                    | Nem                                                                               | N/A                                                                               |
| kötelező aláírású tokenek           | Logikai. Megadja, hogy szükséges-e a jogkivonat aláírása.                                                                                                                                                                                                                                                                                                                                                                                           | Nem                                                                               | true                                                                              |
| elválasztó                       | sztring elemet. Meghatározza a többértékű jogcímből származó értékek kinyeréséhez használandó elválasztó karaktert (például ",").                                                                                                                                                                                                                                                                                                                                          | Nem                                                                               | N/A                                                                               |
| url                             | Nyissa meg az azonosító konfigurációs végpontjának URL-címét, amelyből megkérheti a nyitott azonosító konfigurációs metaadatait. A válasznak az alábbi URL-címen definiált specifikációknak kell megjelennie:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`. Azure Active Directory használja a következő URL-címet: `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` a címtár-bérlő nevét, például `contoso.onmicrosoft.com`. | Igen                                                                              | N/A                                                                               |
| output-token-változó-neve      | sztring elemet. Annak a környezeti változónak a neve, amely a jogkivonat értékét [`Jwt`](api-management-policy-expressions.md) a sikeres jogkivonat-ellenőrzés során.                                                                                                                                                                                                                                                                                     | Nem                                                                               | N/A                                                                               |

### <a name="usage"></a>Használat

Ez a szabályzat a következő házirend- [részekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörökben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirend fejezetei:** bejövő
-   **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>Következő lépések

További információ a házirendek használatáról:

-   [Szabályzatok API Management](api-management-howto-policies.md)
-   [API-k átalakítása](transform-api.md)
-   Házirend- [hivatkozás](api-management-policy-reference.md) a szabályzat-utasítások és azok beállításainak teljes listájához
-   [Házirend-minták](policy-samples.md)
