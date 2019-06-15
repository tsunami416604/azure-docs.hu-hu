---
title: Hibakezelés az Azure API Management házirendek |} A Microsoft Docs
description: Ismerje meg, hogyan reagáljon a az Azure API Management kérelmek feldolgozása során esetlegesen fellépő hibaállapotokat.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/19/2018
ms.author: apimpm
ms.openlocfilehash: 87693caa5343e359bb3ab424de489c2270bbca62
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64704439"
---
# <a name="error-handling-in-api-management-policies"></a>Hiba történt az API Management házirendek kezelése

Azáltal, hogy egy `ProxyError` objektumot, az Azure API Management lehetővé teszi a közzétevők válaszoljanak kérések feldolgozása során esetlegesen jelentkező hibaállapotokat. A `ProxyError` objektum keresztül érhető el a [környezetben. LastError](api-management-policy-expressions.md#ContextVariables) tulajdonság és a szabályzatok által használható a `on-error` ügyfélházirend szekció. Ez a cikk egy hivatkozást a hiba a kezelési képességek az Azure API Management.  
  
## <a name="error-handling-in-api-management"></a>Hiba történt az API Management kezelése

 Az Azure API Management házirendek vannak osztva, `inbound`, `backend`, `outbound`, és `on-error` szakaszokban a következő példában látható módon.  
  
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
  
Egy kérelem feldolgozása során beépített lépések végrehajtása együtt minden olyan szabályzatokat, amelyekkel terjed ki a kérelmet. Ha hiba történik, feldolgozása azonnal ugrik a `on-error` ügyfélházirend szekció.  
A `on-error` ügyfélházirend szekció használható bármilyen hatókörben. API-kiadók konfigurálhatja például az a hiba naplózása és az event hubs, vagy térjen vissza a hívónak új válasz létrehozásakor egyéni működését.  
  
> [!NOTE]
>  A `on-error` szakasz azonban nem szerepel a szabályzatok alapértelmezés szerint. Hozzáadása a `on-error` egy házirend részen keresse meg a Helyicsoportházirend-szerkesztő a kívánt házirendet, és adja hozzá. Szabályzatok konfigurálásával kapcsolatos további információkért lásd: [az API Management házirendek](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).  
>   
>  Ha nincs `on-error` szakaszban a hívók fog kapni 400 vagy 500 HTTP-válaszüzenetek, ha hiba történik.  
  
### <a name="policies-allowed-in-on-error"></a>Hiba az engedélyezett szabályzatok

 A következő házirendek is használhatók a `on-error` ügyfélházirend szekció.  
  
-   [Válassza a](api-management-advanced-policies.md#choose)  
-   [set-variable](api-management-advanced-policies.md#set-variable)  
-   [find-and-replace](api-management-transformation-policies.md#Findandreplacestringinbody)  
-   [return-response](api-management-advanced-policies.md#ReturnResponse)  
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)  
-   [set-method](api-management-advanced-policies.md#SetRequestMethod)  
-   [set-status](api-management-advanced-policies.md#SetStatus)  
-   [send-request](api-management-advanced-policies.md#SendRequest)  
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)  
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)  
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)  
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>LastError

 Ha hiba lép fel, és a vezérlő ugrik a `on-error` házirend szakaszban tárolódik a hiba [környezetben. LastError](api-management-policy-expressions.md#ContextVariables) tulajdonság, amely a házirendek által elérhető lesz a `on-error` szakaszban. Hiba a következő tulajdonságokkal rendelkezik.  
  
| Name (Név)       | Típus   | Leírás                                                                                               | Kötelező |
|------------|--------|-----------------------------------------------------------------------------------------------------------|----------|
| `Source`   | string | Az elem, ahol a hiba történt a neveket. Házirend és a egy beépített folyamat lépés – név lehet.     | Igen      |
| `Reason`   | string | Gép mobilbarát hiba kódra, amely volt használható a hibakezelést.                                       | Nem       |
| `Message`  | string | Természetes nyelven olvasható hiba leírása.                                                                         | Igen      |
| `Scope`    | string | Ha hiba történt, és lehet egy "globális", "termék", "api" vagy "művelet" hatókör neve | Nem       |
| `Section`  | string | Ha hiba történt a szakasz nevét. A lehetséges értékek: "a bejövő", "Háttér", "kimenő" vagy "error".       | Nem       |
| `Path`     | string | Adja meg a beágyazott házirend, például "[3] válasszon / mikor: [2]".                                                        | Nem       |
| `PolicyId` | string | Az érték a `id` attribútumot, ha meg van adva, a szabályzatra, ahol a hiba történt az ügyfél által             | Nem       |

> [!TIP]
> Az állapotkód környezetben keresztül érheti el. Response.StatusCode.  

> [!NOTE]
> Minden szabályzat van egy nem kötelező `id` attribútumot, amelyet a szabályzat a legfelső szintű elemének lehet hozzáadni. Ez az attribútum szerepel a szabályzat, ha hiba lép fel, ha az attribútum értéke lehet lekérni a `context.LastError.PolicyId` tulajdonság.

## <a name="predefined-errors-for-built-in-steps"></a>A beépített lépéseket előre meghatározott hibák  
 Az hibafeltételek kiértékelése beépített feldolgozási lépések során előforduló hibák a következők előre.  
  
| Source        | Állapot                                 | Reason                  | Message                                                                                                                |
|---------------|-------------------------------------------|-------------------------|------------------------------------------------------------------------------------------------------------------------|
| konfiguráció | URI nem felel meg az API vagy művelet | OperationNotFound       | Nem egyezik meg a bejövő kérelem egy művelethez.                                                                      |
| authorization | Nincs megadva előfizetési kulcs             | SubscriptionKeyNotFound | Hozzáférés megtagadva, mert előfizetési kulcs. Ellenőrizze, hogy az előfizetési kulcsot tartalmaznak, amikor az API-kérelem indítására. |
| authorization | Előfizetési kulcs értéke érvénytelen.         | SubscriptionKeyInvalid  | A hozzáférés érvénytelen előfizetési kulcs miatt megtagadva. Ellenőrizze, hogy adja meg az aktív előfizetésre érvényes kulcs.            |
  
## <a name="predefined-errors-for-policies"></a>A szabályzatok előre meghatározott hibák  
 Az hibaállapotok szabályzat-kiértékelés során előforduló hibák a következők előre.  
  
| Source       | Állapot                                                       | Reason                    | Message                                                                                                                              |
|--------------|-----------------------------------------------------------------|---------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| Sebességkorlát-   | Túllépte a sávszélesség-korlátjának                                             | RateLimitExceeded         | Költési korlát túllépve                                                                                                               |
| kvóta        | Kvóta túllépve                                                  | QuotaExceeded             | A csomagba foglalt lebeszélhető percek elfogytak. Kvóta lesz percmennyiség a xx:xx:xx. – vagy – a sávszélesség-kvóta kívüli. Kvóta lesz percmennyiség a xx:xx:xx. |
| jsonp        | Visszahívási paraméter értéke érvénytelen (helytelen karaktereket tartalmaz) | CallbackParameterInvalid  | {Visszahívási paraméter-neve} visszahívási paraméter értéke nem egy érvényes JavaScript-azonosító.                                          |
| ip-filter    | Nem sikerült elemezni a hívó IP-kérelemből                          | FailedToParseCallerIP     | Nem sikerült létrehozni a hívó IP-címet. A hozzáférés megtagadva.                                                                        |
| ip-filter    | Hívó IP-cím nem az engedélyezettek listájához                                | CallerIpNotAllowed        | Nem engedélyezett a hívó IP-címe: {ip-cím}. A hozzáférés megtagadva.                                                                        |
| ip-filter    | Hívó tiltólista IP-címe                                    | CallerIpBlocked           | Hívó IP-cím le van tiltva. A hozzáférés megtagadva.                                                                                         |
| ellenőrzés-fejléc | Kötelező fejléc nem jelenik meg, vagy az érték nincs megadva               | HeaderNotFound            | Fejléc {fejléc neve üzemeltetett szolgáltatás} nem található a kérelemben. A hozzáférés megtagadva.                                                                    |
| ellenőrzés-fejléc | Kötelező fejléc nem jelenik meg, vagy az érték nincs megadva               | HeaderValueNotAllowed     | Fejléc {fejléc neve üzemeltetett szolgáltatás} {fejléc-value} értéke nem engedélyezett. A hozzáférés megtagadva.                                                          |
| validate-jwt | Jwt jogkivonat a kérelemben hiányzik                                 | TokenNotFound             | A JWT nem található a kérelemben. A hozzáférés megtagadva.                                                                                         |
| validate-jwt | Aláírás-ellenőrzése nem sikerült                                     | TokenSignatureInvalid     | < jwt-könyvtárból üzenet\>. A hozzáférés megtagadva.                                                                                          |
| validate-jwt | Érvénytelen célközönség                                                | TokenAudienceNotAllowed   | < jwt-könyvtárból üzenet\>. A hozzáférés megtagadva.                                                                                          |
| validate-jwt | Érvénytelen kibocsátói                                                  | TokenIssuerNotAllowed     | < jwt-könyvtárból üzenet\>. A hozzáférés megtagadva.                                                                                          |
| validate-jwt | Lejárt a jogkivonat.                                                   | TokenExpired              | < jwt-könyvtárból üzenet\>. A hozzáférés megtagadva.                                                                                          |
| validate-jwt | Aláírási kulcs nem lett feloldva azonosító alapján                            | TokenSignatureKeyNotFound | < jwt-könyvtárból üzenet\>. A hozzáférés megtagadva.                                                                                          |
| validate-jwt | Szükséges jogcímek és jogkivonat hiányoznak                          | TokenClaimNotFound        | JWT jogkivonat hiányzik a következő jogcímek: < c1\>, < c2\>,... A hozzáférés megtagadva.                                                            |
| validate-jwt | Jogcím értékei eltérés                                           | TokenClaimValueNotAllowed | Jogcím {jogcímszabály neve üzemeltetett szolgáltatás} {jogcímérték} értéke nem engedélyezett. A hozzáférés megtagadva.                                                             |
| validate-jwt | Más érvényesítési hibák                                       | JwtInvalid                | < az üzenetet a jwt-könyvtár\>                                                                                                          |

## <a name="example"></a>Példa

Egy API-házirend beállítást:

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

és a egy jogosulatlan kérés küldése a következő választ eredményez:

![Jogosulatlan hibaválaszba](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>További lépések

Házirendek használata további információkért lásd:

+ [Az API Management házirendek](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ [Házirend-referencia](api-management-policy-reference.md) házirend-utasítások és a beállítások teljes listáját
+ [A házirend-minták](policy-samples.md)   
