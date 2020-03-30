---
title: Azure API-hozzáférés-korlátozási házirendek | Microsoft dokumentumok
description: Ismerje meg az Azure API Managementben használható hozzáférés-korlátozási szabályzatokat.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266116"
---
# <a name="api-management-access-restriction-policies"></a>API Management hozzáférés-korlátozási szabályzatok

Ez a témakör a következő API Management-házirendek hivatkozási alapként szolgál. A házirendek hozzáadásáról és konfigurálásáról az [API-kezelés házirendjei](https://go.microsoft.com/fwlink/?LinkID=398186)című témakörben talál további információt.

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a>Hozzáférés-korlátozási házirendek

-   [Http-fejléc ellenőrzése](api-management-access-restriction-policies.md#CheckHTTPHeader) – A HTTP-fejléc létezésének és/vagy értékének kényszerítése.
-   [Hívási sebesség korlátozása előfizetésenként](api-management-access-restriction-policies.md#LimitCallRate) – Megakadályozza az API-használat kiugrásait a hívási sebesség korlátozásával, előfizetésenként.
-   [Hívási sebesség korlátozása kulcs](#LimitCallRateByKey) szerint – Megakadályozza az API-használat kiugrásait a hívási sebesség korlátozásával, kulcsalapon.
-   [Hívó IP-címének korlátozása](api-management-access-restriction-policies.md#RestrictCallerIPs) – szűrők (lehetővé teszi/lehetővé teszi) a hívásokat adott IP-címekről és/vagy címtartományokból.
-   [Használati kvóta beállítása előfizetés enként](api-management-access-restriction-policies.md#SetUsageQuota) – Lehetővé teszi a megújuló vagy élettartamra szóló hívás mennyiségének és/vagy sávszélesség-kvótájának kényszerítése előfizetésenként.
-   [Használati kvóta kulcs szerint történő beállítása](#SetUsageQuotaByKey) – Lehetővé teszi a megújuló vagy élettartamra szóló hívás mennyiségének és/vagy sávszélesség-kvótájának kulcsonkénti kényszerítése.
-   [JWT érvényesítése](api-management-access-restriction-policies.md#ValidateJWT) – Egy adott HTTP-fejlécből vagy egy megadott lekérdezési paraméterből kinyert JWT meglétének és érvényességének kényszerítése.

> [!TIP]
> A hozzáférés-korlátozási házirendek különböző hatókörökben különböző célokra használhatók. Például biztonságossá teheti a teljes API-t AAD-hitelesítéssel az `validate-jwt` API-szintű házirend alkalmazásával, `claims` vagy alkalmazhatja azt az API-művelet szintjén, és használhatja a részletesebb vezérléshez.

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a>HTTP-fejléc ellenőrzése

A `check-header` házirend segítségével kényszerítheti, hogy egy kérelem nek van egy megadott HTTP-fejléce. Tetszés szerint ellenőrizheti, hogy a fejlécrendelkezik-e adott értékkel, vagy ellenőrizheti az engedélyezett értékek tartományát. Ha az ellenőrzés sikertelen, a házirend leállítja a kérelmek feldolgozását, és visszaadja a házirendben megadott HTTP-állapotkódot és hibaüzenetet.

### <a name="policy-statement"></a>Politikai nyilatkozat

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

| Név         | Leírás                                                                                                                                   | Kötelező |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| ellenőrző fejléc | Gyökérelem.                                                                                                                                 | Igen      |
| érték        | Engedélyezett HTTP fejlécérték. Ha több értékelem van megadva, az ellenőrzés sikeresnek minősül, ha az értékek bármelyike egyezik. | Nem       |

### <a name="attributes"></a>Attribútumok

| Név                       | Leírás                                                                                                                                                            | Kötelező | Alapértelmezett |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| nem sikerült-check-error-message | Hibaüzenet et a HTTP-válasz törzsében kell visszaadni, ha a fejléc nem létezik, vagy érvénytelen az értéke. Ennek az üzenetnek megfelelően meg kell szabadulnia a speciális karakterektől. | Igen      | N/A     |
| sikertelen-check-httpcode      | HTTP-állapotkód, amelyet vissza kell adni, ha a fejléc nem létezik, vagy érvénytelen az értéke.                                                                                        | Igen      | N/A     |
| fejléc neve                | Az ellenőrizendő HTTP-fejléc neve.                                                                                                                                  | Igen      | N/A     |
| kis- és nagybetűk figyelmen kívül hagyása                | Igaz vagy Hamis értékre állítható. Ha a rendszer a True case értéket figyelmen kívül hagyja, amikor a fejlécértéket összehasonlítja az elfogadható értékek készletével.                                    | Igen      | N/A     |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő, kimenő

-   **Házirend-hatókörök:** az összes hatókör

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a>Hívási arány korlátozása előfizetés szerint

A `rate-limit` szabályzat megakadályozza, hogy az API-használat kiugrások előfizetésenként korlátozza a hívási sebesség egy megadott számú egy adott időszakban. A házirend aktiválásakor a hívó `429 Too Many Requests` megkapja a válasz állapotkódját.

> [!IMPORTANT]
> Ez a házirend házirend-dokumentumonként csak egyszer használható.
>
> [A házirend-kifejezések](api-management-policy-expressions.md) nem használhatók a házirend egyik házirendattribútumában sem.

> [!CAUTION]
> A sávszélesség-szabályozási architektúra elosztott jellege miatt a sebességkorlátozás soha nem teljesen pontos. A konfigurált és az engedélyezett kérelmek valós száma közötti különbség a kérelem mennyiségétől és sebességétól, a háttér-késéstől és más tényezőktől függően változik.

### <a name="policy-statement"></a>Politikai nyilatkozat

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

| Név       | Leírás                                                                                                                                                                                                                                                                                              | Kötelező |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| sebességhatár | Gyökérelem.                                                                                                                                                                                                                                                                                            | Igen      |
| api-t        | Adjon hozzá egy vagy több ilyen elemet, hogy a terméken belüli API-kra hívásdíjkorlátot szabjon ki. A termék- és API-hívási sebességkorlátok egymástól függetlenül érvényesek. Az API-ra a `name` `id`vagy a . Ha mindkét attribútum `id` meg van `name` adva, a rendszer használni fogja, és figyelmen kívül hagyja.                    | Nem       |
| Művelet  | Adjon hozzá egy vagy több ilyen elemet, hogy egy API-n belüli műveletekre hívásdíjkorlátot szabjon ki. A termék-, API- és művelethívási sebességkorlátok egymástól függetlenül kerülnek alkalmazásra. A műveletre hivatkozni `name` lehet `id`a vagyon keresztül. Ha mindkét attribútum `id` meg van `name` adva, a rendszer használni fogja, és figyelmen kívül hagyja. | Nem       |

### <a name="attributes"></a>Attribútumok

| Név           | Leírás                                                                                           | Kötelező | Alapértelmezett |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| név           | Annak az API-nak a neve, amelyre a díjkorlátot alkalmazni kell.                                                | Igen      | N/A     |
| Hívások          | A megadott időintervallumban engedélyezett hívások maximális száma `renewal-period`a. | Igen      | N/A     |
| megújítási időszak | Az az időszak másodpercben, amely után a kvóta visszaáll.                                              | Igen      | N/A     |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő

-   **Házirend-hatókörök:** termék, api, működés

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a>Hívási sebesség korlátozása kulcs szerint

> [!IMPORTANT]
> Ez a funkció nem érhető el az API Management **felhasználási** szintjében.

A `rate-limit-by-key` házirend megakadályozza, hogy az API-használat csúcsok egy kulcs alapon korlátozza a hívási sebesség egy megadott szám egy adott időszakban. A kulcs tetszőleges karakterlánc-értékkel rendelkezhet, és általában házirend-kifejezéssel van megadva. Választható növekmény feltételadható annak meghatározásához, hogy mely kérelmeket kell beleszámítani a korlátba. A házirend aktiválásakor a hívó `429 Too Many Requests` megkapja a válasz állapotkódját.

A szabályzattal kapcsolatos további információkért és példákért olvassa el [a Speciális kérelmek szabályozása az Azure API Management szolgáltatással című témakört.](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/)

> [!CAUTION]
> A sávszélesség-szabályozási architektúra elosztott jellege miatt a sebességkorlátozás soha nem teljesen pontos. A konfigurált és az engedélyezett kérelmek valós száma közötti különbség a kérelem mennyiségétől és sebességétól, a háttér-késéstől és más tényezőktől függően változik.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" />

```

### <a name="example"></a>Példa

A következő példában a sebességkorlátot a hívó IP-címe határozza meg.

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

| Név              | Leírás   | Kötelező |
| ----------------- | ------------- | -------- |
| kulcs-limit-by-key | Gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Név                | Leírás                                                                                           | Kötelező | Alapértelmezett |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| Hívások               | A megadott időintervallumban engedélyezett hívások maximális száma `renewal-period`a. | Igen      | N/A     |
| ellenkulcs         | A díjkorlát-házirendhez használandó kulcs.                                                             | Igen      | N/A     |
| növekmény-feltétel | A logikai kifejezés, amely megadja, hogy a kérelmet bele kell-e számítani a kvótába (`true`).        | Nem       | N/A     |
| megújítási időszak      | Az az időszak másodpercben, amely után a kvóta visszaáll.                                              | Igen      | N/A     |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő

-   **Házirend-hatókörök:** az összes hatókör

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a>Hívó IP-azonosítójának korlátozása

A `ip-filter` házirendszűrők (lehetővé teszik/letiltjuk) a hívásokat adott IP-címekről és/vagy címtartományokból.

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>Példa

A következő példában a házirend csak az egyetlen IP-címről vagy a megadott IP-címek tartományából érkező kéréseket engedélyezi.

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>Elemek

| Név                                      | Leírás                                         | Kötelező                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-szűrő                                 | Gyökérelem.                                       | Igen                                                            |
| address                                   | Egyetlen IP-címet ad meg, amelyre szűrni kell.   | Legalább egy `address` `address-range` vagy elem szükséges. |
| címtartomány ="cím" ="cím" között | Megadja azt az IP-címtartományt, amelyre szűrni kell. | Legalább egy `address` `address-range` vagy elem szükséges. |

### <a name="attributes"></a>Attribútumok

| Név                                      | Leírás                                                                                 | Kötelező                                           | Alapértelmezett |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| címtartomány ="cím" ="cím" között | A hozzáférés engedélyezéséhez vagy megtagadásához szolgáló IP-címek tartománya.                                        | Az elem `address-range` használathoz szükséges. | N/A     |
| ip-filter action="&#124; tiltó"    | Itt adható meg, hogy a megadott IP-címekhez és -tartományokhoz engedélyezni kell-e hívásokat. | Igen                                                | N/A     |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő
-   **Házirend-hatókörök:** az összes hatókör

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a>Használati kvóta beállítása előfizetés szerint

A `quota` szabályzat előfizetésenként kényszeríti a megújuló vagy élettartamra szóló hívás mennyiséget és/vagy sávszélesség-kvótát.

> [!IMPORTANT]
> Ez a házirend házirend-dokumentumonként csak egyszer használható.
>
> [A házirend-kifejezések](api-management-policy-expressions.md) nem használhatók a házirend egyik házirendattribútumában sem.

### <a name="policy-statement"></a>Politikai nyilatkozat

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

| Név      | Leírás                                                                                                                                                                                                                                                                                  | Kötelező |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| kvóta     | Gyökérelem.                                                                                                                                                                                                                                                                                | Igen      |
| api-t       | Adjon hozzá egy vagy több ilyen elemet, hogy a terméken belüli API-kra híváskvótát szabjon ki. A termék- és API-híváskvóták egymástól függetlenül kerülnek alkalmazásra. Az API-ra a `name` `id`vagy a . Ha mindkét attribútum `id` meg van `name` adva, a rendszer használni fogja, és figyelmen kívül hagyja.                    | Nem       |
| Művelet | Adjon hozzá egy vagy több ilyen elemet az API-n belüli műveletek híváskvótájának kikényszerítéséhez. A termék-, API- és művelethívási kvóták egymástól függetlenül kerülnek alkalmazásra. A műveletre hivatkozni `name` lehet `id`a vagyon keresztül. Ha mindkét attribútum `id` meg van `name` adva, a rendszer használni fogja, és figyelmen kívül hagyja. | Nem       |

### <a name="attributes"></a>Attribútumok

| Név           | Leírás                                                                                               | Kötelező                                                         | Alapértelmezett |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| név           | Annak az API-nak vagy műveletnek a neve, amelyre a kvóta vonatkozik.                                             | Igen                                                              | N/A     |
| Sávszélesség      | A kilobájtok maximális száma a megadott időintervallumban. `renewal-period` | Vagy `calls` `bandwidth`a , vagy mindkettőt együtt kell megadni. | N/A     |
| Hívások          | A megadott időintervallumban engedélyezett hívások maximális száma `renewal-period`a.     | Vagy `calls` `bandwidth`a , vagy mindkettőt együtt kell megadni. | N/A     |
| megújítási időszak | Az az időszak másodpercben, amely után a kvóta visszaáll.                                                  | Igen                                                              | N/A     |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő
-   **Szakpolitikai hatókörök:** termék

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a>Használati kvóta beállítása kulcs szerint

> [!IMPORTANT]
> Ez a funkció nem érhető el az API Management **felhasználási** szintjében.

A `quota-by-key` házirend kulcsalapon kényszeríti a megújuló vagy élettartamra szóló hívás mennyiséget és/vagy sávszélesség-kvótát. A kulcs tetszőleges karakterlánc-értékkel rendelkezhet, és általában házirend-kifejezéssel van megadva. Választható növekmény feltétel adható meg, hogy mely kérelmeket kell beleszámítani a kvótába. Ha több házirend növelné ugyanazt a kulcsértéket, kérésenként csak egyszer növekszik. A híváskorlát elérésekor a hívó `403 Forbidden` megkapja a válasz állapotkódját.

A szabályzattal kapcsolatos további információkért és példákért olvassa el [a Speciális kérelmek szabályozása az Azure API Management szolgáltatással című témakört.](https://azure.microsoft.com/documentation/articles/api-management-sample-flexible-throttling/)

### <a name="policy-statement"></a>Politikai nyilatkozat

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>Példa

A következő példában a kvóta a hívó IP-címe van bekarikulva.

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

| Név  | Leírás   | Kötelező |
| ----- | ------------- | -------- |
| kvóta | Gyökérelem. | Igen      |

### <a name="attributes"></a>Attribútumok

| Név                | Leírás                                                                                               | Kötelező                                                         | Alapértelmezett |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| Sávszélesség           | A kilobájtok maximális száma a megadott időintervallumban. `renewal-period` | Vagy `calls` `bandwidth`a , vagy mindkettőt együtt kell megadni. | N/A     |
| Hívások               | A megadott időintervallumban engedélyezett hívások maximális száma `renewal-period`a.     | Vagy `calls` `bandwidth`a , vagy mindkettőt együtt kell megadni. | N/A     |
| ellenkulcs         | A kvótaházirendhez használandó kulcs.                                                                      | Igen                                                              | N/A     |
| növekmény-feltétel | A logikai kifejezés, amely meghatározza, hogy a kérelmet bele kell-e számítani a kvótába (`true`)             | Nem                                                               | N/A     |
| megújítási időszak      | Az az időszak másodpercben, amely után a kvóta visszaáll.                                                  | Igen                                                              | N/A     |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő
-   **Házirend-hatókörök:** az összes hatókör

## <a name="validate-jwt"></a><a name="ValidateJWT"></a>JWT érvényesítése

A `validate-jwt` házirend egy adott HTTP-fejlécből vagy egy megadott lekérdezési paraméterből kinyert JWT létezését és érvényességét kényszeríti ki.

> [!IMPORTANT]
> A `validate-jwt` házirend megköveteli, hogy a `exp` regisztrált jogcím szerepeljen a JWT-jogkivonatban, kivéve, ha `require-expiration-time` az attribútum meg van adva, és a . `false`
> A `validate-jwt` szabályzat támogatja a HS256 és RS256 aláírási algoritmusokat. A HS256 esetében a kulcsot a bázis64 kódolású űrlapon a házirenden belül kell megadni. Az RS256 esetében a kulcsot egy Open ID konfigurációs végponton keresztül kell megadni.
> A `validate-jwt` házirend támogatja a szimmetrikus kulcsokkal titkosított tokeneket az alábbi A128CBC-HS256, A192CBC-HS384, A256CBC-HS512 titkosítási algoritmusokkal.

### <a name="policy-statement"></a>Politikai nyilatkozat

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

#### <a name="simple-token-validation"></a>Egyszerű jogkivonat-érvényesítés

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

#### <a name="azure-active-directory-token-validation"></a>Az Azure Active Directory tokenérvényesítése

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

#### <a name="azure-active-directory-b2c-token-validation"></a>Az Azure Active Directory B2C tokenérvényesítése

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

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>Hozzáférés engedélyezése jogkivonatjogcímeken alapuló műveletekhez

Ez a példa bemutatja, hogyan használhatja a [JWT-házirend érvényesítése](api-management-access-restriction-policies.md#ValidateJWT) a token jogcímjog-értéken alapuló műveletekhez való hozzáférés engedélyezéséhez.

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
| érvényesít-jwt        | Gyökérelem.                                                                                                                                                                                                                                                                                                                                         | Igen      |
| Közönség           | A tokenen szereplő elfogadható közönségjogcímek listáját tartalmazza. Ha több közönségérték van jelen, akkor a rendszer minden értéket addig próbál, amíg az összes ki nem merül (ebben az esetben az érvényesítés sikertelen), vagy amíg az egyik sikeres nem lesz. Legalább egy célközönséget meg kell adni.                                                                     | Nem       |
| kibocsátó-aláíró kulcsok | Az aláírt jogkivonatok érvényesítéséhez használt Base64 kódolású biztonsági kulcsok listája. Ha több biztonsági kulcs van jelen, akkor minden kulcs kimerül, amíg az összes ki merül (ebben az esetben az érvényesítés sikertelen), vagy amíg az egyik sikeres (hasznos jogkivonat-átgörgetés). A kulcselemek `id` egy nem kötelező `kid` attribútummal rendelkeznek, amely a jogcímegyeztetéshez szolgál.               | Nem       |
| visszafejtés-kulcsok     | A tokenek visszafejtéséhez használt Base64 kódolású kulcsok listája. Ha több biztonsági kulcs van jelen, akkor a rendszer minden kulcsot addig próbál meg, amíg az összes kulcs ki nem merül (ebben az esetben az érvényesítés sikertelen), vagy amíg egy kulcs sikeres nem lesz. A kulcselemek `id` egy nem kötelező `kid` attribútummal rendelkeznek, amely a jogcímegyeztetéshez szolgál.                                                 | Nem       |
| Kibocsátók             | A jogkivonatot kibocsátó elfogadható rendszertagok listája. Ha több kiállítói érték van jelen, akkor a rendszer minden értéket addig próbál meg, amíg az összes ki nem merül (ebben az esetben az érvényesítés sikertelen), vagy amíg az egyik sikeres nem lesz.                                                                                                                                         | Nem       |
| openid-config (nyílt-konfiguráció)       | A megfelelő Open ID konfigurációs végpont megadásához használt elem, amelyből aláíró kulcsok és kibocsátó szerezhető be.                                                                                                                                                                                                                        | Nem       |
| kötelező követelések     | A jogkivonaton várhatóan jelen lévő jogcímek listáját tartalmazza, hogy érvényesnek minősüljön. Ha `match` az attribútum van `all` beállítva, hogy minden jogcímérték a szabályzatban jelen kell lennie a jogkivonatban az érvényesítés sikeres legyen. Ha `match` az attribútum `any` beállítása legalább egy jogcím jelen kell lennie a jogkivonatban az érvényesítés sikeres. | Nem       |

### <a name="attributes"></a>Attribútumok

| Név                            | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                            | Kötelező                                                                         | Alapértelmezett                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| óra-döntés                      | Időtartomány. Segítségével megadhatja a maximális várható időkülönbség a jogkivonat-kibocsátó és az API Management-példány között.                                                                                                                                                                                                                                                                                                               | Nem                                                                               | 0 másodperc                                                                         |
| sikertelen-érvényesítés-hibaüzenet | Hibaüzenet et a HTTP-válasz törzsében, ha a JWT nem felel meg az érvényesítésen. Ennek az üzenetnek megfelelően meg kell szabadulnia a speciális karakterektől.                                                                                                                                                                                                                                                                                                 | Nem                                                                               | Az alapértelmezett hibaüzenet az érvényesítési problémától függ, például a "JWT nincs jelen" |
| failed-validation-httpcode      | HTTP-állapotkód, amelyet vissza kell adni, ha a JWT nem felel meg az érvényesítésen.                                                                                                                                                                                                                                                                                                                                                                                         | Nem                                                                               | 401                                                                               |
| fejléc neve                     | A jogkivonatot tároló HTTP-fejléc neve.                                                                                                                                                                                                                                                                                                                                                                                                         | Az `header-name`egyiket meg kell adni, `query-parameter-name` vagy `token-value` meg kell adni. | N/A                                                                               |
| lekérdezés-paraméter-név            | A jogkivonatot tartalmazó lekérdezési paraméter neve.                                                                                                                                                                                                                                                                                                                                                                                                     | Az `header-name`egyiket meg kell adni, `query-parameter-name` vagy `token-value` meg kell adni. | N/A                                                                               |
| token-érték                     | JWT-tokent tartalmazó karakterláncot visszaadó kifejezés                                                                                                                                                                                                                                                                                                                                                                                                     | Az `header-name`egyiket meg kell adni, `query-parameter-name` vagy `token-value` meg kell adni. | N/A                                                                               |
| id                              | Az `id` `key` elem attribútuma lehetővé teszi, hogy megadja a `kid` feltételt, amely a jogkivonatban (ha van) egyeztetve lesz, hogy megtudja, a megfelelő kulcsot használja az aláírás érvényesítéséhez.                                                                                                                                                                                                                                           | Nem                                                                               | N/A                                                                               |
| Mérkőzés                           | Az `match` `claim` elem attribútuma azt határozza meg, hogy a házirend minden jogcímértékének jelen kell-e lennie a jogkivonatban a sikeres érvényesítéshez. Lehetséges értékek:<br /><br /> - `all`- a házirend minden jogcímértékének jelen kell lennie a jogkivonatban az érvényesítés sikeréhez.<br /><br /> - `any`- legalább egy jogcímértéknek jelen kell lennie a jogkivonatban az érvényesítés sikeréhez.                                                       | Nem                                                                               | összes                                                                               |
| lejárati idő megkövetelése         | Logikai. Itt adható meg, hogy szükség van-e lejárati jogcímre a jogkivonatban.                                                                                                                                                                                                                                                                                                                                                                               | Nem                                                                               | igaz                                                                              |
| megkövetelési rendszer                  | A tokenséma neve, pl. "bemutatóra szóló". Ha ez az attribútum be van állítva, a házirend biztosítja, hogy a megadott séma szerepeljen az Engedélyezési fejléc értékében.                                                                                                                                                                                                                                                                                    | Nem                                                                               | N/A                                                                               |
| szükséges-aláírt-tokenek           | Logikai. Itt adható meg, hogy szükség van-e jogkivonat aláírásra.                                                                                                                                                                                                                                                                                                                                                                                           | Nem                                                                               | igaz                                                                              |
| Elválasztó                       | Sztring. Megadja az elválasztót (pl. ",") egy többértékű jogcím értékkészletének kinyeréséhez.                                                                                                                                                                                                                                                                                                                                          | Nem                                                                               | N/A                                                                               |
| url                             | Nyissa meg az azonosító konfigurációs végpontURL-címét, ahonnan az Open ID konfigurációs metaadatok beszerezhetők. A válasznak az URL-címen meghatározott specifikációk szerint kell lennie:`https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`. Az Azure Active Directory esetén `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` használja a következő URL-címet: a `contoso.onmicrosoft.com`címtárbérlő nevének helyettesítése, pl. . | Igen                                                                              | N/A                                                                               |
| kimeneti token-változó-név      | Sztring. Annak a környezeti változónak a neve, [`Jwt`](api-management-policy-expressions.md) amely a jogkivonat-értéket típusobjektumként kapja meg a jogkivonat-ellenőrzés során                                                                                                                                                                                                                                                                                     | Nem                                                                               | N/A                                                                               |

### <a name="usage"></a>Használat

Ez a házirend a következő [házirendszakaszokban](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) és [hatókörekben](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes)használható.

-   **Házirendszakaszok:** bejövő
-   **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>További lépések

A házirendekkel kapcsolatos további információkért lásd:

-   [Szabályzatok az API Managementben](api-management-howto-policies.md)
-   [API-k átalakítása](transform-api.md)
-   [Házirend-útmutató](api-management-policy-reference.md) a házirend-utasítások és beállításaik teljes listájához
-   [Házirendminták](policy-samples.md)
