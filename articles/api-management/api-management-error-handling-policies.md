---
title: Hibakezelés az Azure API-kezelési házirendekben | Microsoft dokumentumok
description: Ismerje meg, hogyan reagálhat a kérelmek azure API-kezelés során esetleg előforduló hibafeltételekre.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: 2c021a6d10c95b58ac444de8ea895ca01371a2b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75902461"
---
# <a name="error-handling-in-api-management-policies"></a>Hibakezelés az API Management-szabályzatokban

Egy `ProxyError` objektum biztosításával az Azure API Management lehetővé teszi a közzétevők számára, hogy válaszoljanak a hibafeltételekre, amelyek a kérelmek feldolgozása során fordulhatnak elő. Az `ProxyError` objektum a környezeten keresztül érhető [el. LastError](api-management-policy-expressions.md#ContextVariables) tulajdonság, és a `on-error` házirend szakasz házirendjei használhatják. Ez a cikk az Azure API Management hibakezelési képességeire mutató hivatkozást tartalmaz.

## <a name="error-handling-in-api-management"></a>Hibakezelés az API-kezelésben

Az Azure API Management `inbound`szabályzatai a következő példában látható módon , `backend`, `outbound`és `on-error` szakaszokra vannak osztva.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is
         forwarded to the backend service go here -->
    </backend>
    <outbound>
      <!-- statements to be applied to the response go here -->
    </outbound>
    <on-error>
        <!-- statements to be applied if there is an error
             condition go here -->
  </on-error>
</policies>
```

A kérelem feldolgozása során a beépített lépések végrehajtása a kérelem hatókörében lévő szabályzatokkal együtt történik. Hiba esetén a feldolgozás azonnal a `on-error` házirendszakaszra ugrik.
A `on-error` házirendszakasz bármely hatókörben használható. Az API-közzétevők egyéni viselkedést konfigurálhatnak, például naplózhatják a hibát az eseményközpontokba, vagy új választ hozhatnak létre a hívónak való visszatéréshez.

> [!NOTE]
> A `on-error` szakasz alapértelmezés szerint nincs jelen a házirendekben. Ha hozzá `on-error` szeretné adni a szakaszt egy házirendhez, keresse meg a kívánt házirendet a házirend-szerkesztőben, és adja hozzá. A házirendek konfigurálásáról az [API-kezelés házirendjei](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/)című témakörben talál további információt.
>
> Ha nincs `on-error` szakasz, a hívók 400 vagy 500 HTTP-válaszüzenetet kapnak, ha hiba lép fel.

### <a name="policies-allowed-in-on-error"></a>A hiba esetén engedélyezett házirendek

A következő házirendek használhatók `on-error` a házirend szakaszban.

-   [Válassza ki](api-management-advanced-policies.md#choose)
-   [beállított változó](api-management-advanced-policies.md#set-variable)
-   [keresés és csere](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [visszaadás](api-management-advanced-policies.md#ReturnResponse)
-   [set-fejléc](api-management-transformation-policies.md#SetHTTPheader)
-   [set-módszer](api-management-advanced-policies.md#SetRequestMethod)
-   [beállított állapot](api-management-advanced-policies.md#SetStatus)
-   [küldési kérelem](api-management-advanced-policies.md#SendRequest)
-   [küldés-egyirányú kérés](api-management-advanced-policies.md#SendOneWayRequest)
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)
-   [json-xml](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)

## <a name="lasterror"></a>LastError

Ha hiba történik, és a `on-error` vezérlő a házirendszakaszra ugrik, a rendszer a hibát [a környezetben tárolja. LastError](api-management-policy-expressions.md#ContextVariables) tulajdonság, amely a `on-error` szakasz házirendjei által érhető el. A LastError a következő tulajdonságokkal rendelkezik.

| Név       | Típus   | Leírás                                                                                               | Kötelező |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | sztring | Annak az elemnek a neve, ahol a hiba történt. Lehet házirend vagy beépített folyamatlépés neve.      | Igen      |
| `Reason`   | sztring | Gépbarát hibakód, amely hibakezeléshez használható.                                       | Nem       |
| `Message`  | sztring | Ember által olvasható hibaleírás.                                                                         | Igen      |
| `Scope`    | sztring | Annak a hatókörnek a neve, ahol a hiba történt, és lehet a "globális", "termék", "api" vagy "művelet" egyike | Nem       |
| `Section`  | sztring | Az a szakasz név, ahol hiba történt. Lehetséges értékek: "bejövő", "háttérrendszer", "kimenő" vagy "hiba".      | Nem       |
| `Path`     | sztring | Beágyazott házirendet ad meg, például "choose[3]/when[2]".                                                 | Nem       |
| `PolicyId` | sztring | Az `id` attribútum értéke, ha azt az ügyfél adja meg, abban a házirendben, ahol hiba történt             | Nem       |

> [!TIP]
> Az állapotkódot a környezeten keresztül érheti el. Response.StatusCode.

> [!NOTE]
> Minden házirend rendelkezik egy választható `id` attribútummal, amely hozzáadható a házirend gyökéreleméhez. Ha ez az attribútum hibaesetén szerepel egy házirendben, az attribútum értéke a `context.LastError.PolicyId` tulajdonság használatával beolvasható.

## <a name="predefined-errors-for-built-in-steps"></a>Előre definiált hibák a beépített lépésekhez

A következő hibák előre definiálva vannak a beépített feldolgozási lépések kiértékelése során előforduló hibafeltételekhez.

| Forrás        | Állapot                                 | Ok                  | Üzenet                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| konfiguráció | Az Uri nem egyezik egyetlen API-val vagy művelettel sem | A művelet nem található       | Nem lehet egyeztetni a bejövő kérést egy művelettel.                                                                      |
| engedélyezés | Az előfizetési kulcs nincs megadva             | A SubscriptionKey Nem található | A hozzáférés megtagadva a hiányzó előfizetési kulcs miatt. Győződjön meg arról, hogy az API-ra vonatkozó kérelmek kérések esetén tartalmazza az előfizetési kulcsot. |
| engedélyezés | Az előfizetési kulcs értéke érvénytelen         | Előfizetési kulcsérvénytelen  | A hozzáférés érvénytelen előfizetési kulcs miatt megtagadva. Győződjön meg arról, hogy egy aktív előfizetéshez érvényes kulcsot ad meg.            |
| multiple (többszörös) | Az ügyfél (egy ügyfél és egy API Management átjáró között) megszakadt az ügyfél, miközben a kérés függőben volt | Ügyfélkapcsolati hiba | multiple (többszörös) |
| multiple (többszörös) | Upstream kapcsolat (egy API Management átjáróról háttérszolgáltatásra) nem jött létre, vagy a háttérrendszer megszakította | HáttérrendszerConnectionFailure | multiple (többszörös) |
| multiple (többszörös) | Futásidejű kivétel történt egy adott kifejezés kiértékelése során | Kifejezésérték-értékeléssikertelen | multiple (többszörös) |

## <a name="predefined-errors-for-policies"></a>A házirendek előre definiált hibái

A következő hibák előre definiálva vannak a házirend-kiértékelés során előforduló hibafeltételekhez.

| Forrás       | Állapot                                                       | Ok                    | Üzenet                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| sebességhatár   | Túllépte a díjkorlátot                                             | RateLimitExceeded         | Túllépte a sebességkorlátot                                                                                                               |
| kvóta        | Túllépve a kvótát                                                  | Kvótatúllépve             | A csomagba foglalt lebeszélhető percek elfogytak. A kvóta feltöltése xx:xx:xx. -vagy- Elfogyott a sávszélesség-kvóta. A kvóta feltöltése xx:xx:xx. |
| Jsonp        | A visszahívási paraméter értéke érvénytelen (nem megfelelő karaktereket tartalmaz) | CallbackParameterInvalid  | A(z) {callback-parameter-name} visszahívási paraméter értéke nem érvényes JavaScript-azonosító.                                          |
| ip-szűrő    | Nem sikerült elemezni a hívó IP-címét a kérésből                          | Nem sikerültToParseCallerIP     | Nem sikerült létrehozni a hívó IP-címét. A hozzáférés megtagadva.                                                                        |
| ip-szűrő    | A hívó IP-címe nem szerepel az engedélyezett listában                                | CallerIpNem engedélyezett        | A hívó IP-címe ({ip-address} nem engedélyezett. A hozzáférés megtagadva.                                                                        |
| ip-szűrő    | A hívó IP-címe letiltott listában van                                    | CallerIpBlokkolva           | A hívó IP-címe le van tiltva. A hozzáférés megtagadva.                                                                                         |
| ellenőrző fejléc | A szükséges fejléc nincs megmutatva, vagy az érték hiányzik               | A fejléc nem található            | A(z) {header-name} fejléc nem található a kérelemben. A hozzáférés megtagadva.                                                                    |
| ellenőrző fejléc | A szükséges fejléc nincs megmutatva, vagy az érték hiányzik               | HeaderValue Nem engedélyezett     | A(z) {header-name} fejléc(header-name} értéke nem engedélyezett. A hozzáférés megtagadva.                                                          |
| érvényesít-jwt | Hiányzik a JWT token a kérelemből                                 | Token Nem található             | A JWT nem található a kérelemben. A hozzáférés megtagadva.                                                                                         |
| érvényesít-jwt | Az aláírás érvényesítése nem sikerült                                     | TokenSignatureÉrvénytelen     | <üzenetet jwt\>könyvtár . A hozzáférés megtagadva.                                                                                          |
| érvényesít-jwt | Érvénytelen közönség                                                | TokenAudience Notallowed   | <üzenetet jwt\>könyvtár . A hozzáférés megtagadva.                                                                                          |
| érvényesít-jwt | Érvénytelen kibocsátó                                                  | TokenIssuerNotAllowed     | <üzenetet jwt\>könyvtár . A hozzáférés megtagadva.                                                                                          |
| érvényesít-jwt | Lejárt a jogkivonat.                                                   | TokenExpired              | <üzenetet jwt\>könyvtár . A hozzáférés megtagadva.                                                                                          |
| érvényesít-jwt | Az aláíráskulcs nem lett feloldva az azonosítóval                            | TokenSignatureKeyNotFound | <üzenetet jwt\>könyvtár . A hozzáférés megtagadva.                                                                                          |
| érvényesít-jwt | A szükséges jogcímek hiányoznak a jogkivonatból                          | TokenClaimNotFound        | JWT token hiányzik a következő\>állítások: <\>c1, <c2 , ... A hozzáférés megtagadva.                                                            |
| érvényesít-jwt | A jogcímértékek nem egyeznek                                           | TokenClaimValueNem engedélyezett | A(z) {jogcímnév} {jogcím-név} jogcímértéke nem engedélyezett. A hozzáférés megtagadva.                                                             |
| érvényesít-jwt | Egyéb érvényesítési hibák                                       | JwtInvalid                | <üzenet jwt könyvtárból\>                                                                                                          |
| továbbítási kérelem vagy küldési kérelem | A HTTP-válasz állapotkódja és a fejlécek nem érkeztek meg a háttérrendszertől a beállított időkorláton belül | Időkorlát | multiple (többszörös) |

## <a name="example"></a>Példa

API-házirend beállítása a következőkre:

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <set-header name="ErrorSource" exists-action="override">
            <value>@(context.LastError.Source)</value>
        </set-header>
        <set-header name="ErrorReason" exists-action="override">
            <value>@(context.LastError.Reason)</value>
        </set-header>
        <set-header name="ErrorMessage" exists-action="override">
            <value>@(context.LastError.Message)</value>
        </set-header>
        <set-header name="ErrorScope" exists-action="override">
            <value>@(context.LastError.Scope)</value>
        </set-header>
        <set-header name="ErrorSection" exists-action="override">
            <value>@(context.LastError.Section)</value>
        </set-header>
        <set-header name="ErrorPath" exists-action="override">
            <value>@(context.LastError.Path)</value>
        </set-header>
        <set-header name="ErrorPolicyId" exists-action="override">
            <value>@(context.LastError.PolicyId)</value>
        </set-header>
        <set-header name="ErrorStatusCode" exists-action="override">
            <value>@(context.Response.StatusCode.ToString())</value>
        </set-header>
        <base />
    </on-error>
</policies>
```

és a jogosulatlan kérelem küldése a következő választ fogja eredményezni:

![Nem engedélyezett hibaválasz](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>További lépések

A házirendekkel kapcsolatos további információkért lásd:

-   [Szabályzatok az API Managementben](api-management-howto-policies.md)
-   [API-k átalakítása](transform-api.md)
-   [Házirend-útmutató](api-management-policy-reference.md) a házirend-utasítások és beállításaik teljes listájához
-   [Házirendminták](policy-samples.md)
