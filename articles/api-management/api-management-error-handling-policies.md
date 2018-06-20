---
title: Hiba történt az Azure API-felügyeleti házirendek kezelése |} Microsoft Docs
description: Ismerje meg, hogyan válaszol az Azure API Management-kérelem feldolgozása közben fellépő hibaállapotokat.
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
ms.openlocfilehash: 73609e802eceea6aa94d77cef6ca1d654264973d
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265007"
---
# <a name="error-handling-in-api-management-policies"></a>Hiba történt az API-felügyeleti házirendek kezelése

Azáltal, hogy egy `ProxyError` objektum Azure API Management lehetővé teszi, hogy a közzétevők válaszolni a hibaállapotokat, esetleg bekövetkező kérelem feldolgozása során. A `ProxyError` objektum keresztül érhető el a [környezetben. Hiba](api-management-policy-expressions.md#ContextVariables) tulajdonság, és a házirendek által használható a `on-error` házirend szakaszban. Ez a cikk egy hivatkozást a hiba kezelési képességeket biztosít az Azure API Management.  
  
## <a name="error-handling-in-api-management"></a>Hiba történt az API Management kezelése

 Az Azure API Management házirendek osztott `inbound`, `backend`, `outbound`, és `on-error` szakasz a következő példában látható módon.  
  
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
  
A kérelem feldolgozása során beépített lépéseit együtt a házirendek, amelyeket a kérelem hatókörében hajtja végre. Ha hiba lép fel, feldolgozása azonnal ugrik a `on-error` házirend szakaszban.  
A `on-error` házirend szakasz használható a hatókörben. API-közzétevők konfigurálhatja például az event hubs jelentkezik a hiba, vagy hozzon létre egy új válasz térjen vissza a hívó egyéni működését.  
  
> [!NOTE]
>  A `on-error` szakasz nincs jelen a házirendek alapértelmezés szerint. Hozzáadása a `on-error` házirend szakaszt, keresse meg a Helyicsoportházirend-szerkesztő a kívánt házirendet, és vegye fel azt. Házirendek konfigurálásával kapcsolatos további információkért lásd: [házirendek az API Management](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).  
>   
>  Ha nincs `on-error` szakaszban hívóknak kap, 400 vagy 500 HTTP-válasz üzenetek hiba akkor fordul elő.  
  
### <a name="policies-allowed-in-on-error"></a>Hiba az engedélyezett házirendek

 A következő házirendek is használhatók a `on-error` házirend szakaszban.  
  
-   [Válassza a](api-management-advanced-policies.md#choose)  
-   [Set-változó](api-management-advanced-policies.md#set-variable)  
-   [keresése és cseréje](api-management-transformation-policies.md#Findandreplacestringinbody)  
-   [visszatérési-válasz](api-management-advanced-policies.md#ReturnResponse)  
-   [set-fejléc](api-management-transformation-policies.md#SetHTTPheader)  
-   [set-módszer](api-management-advanced-policies.md#SetRequestMethod)  
-   [állapot beállítása](api-management-advanced-policies.md#SetStatus)  
-   [küldési-kérelmek](api-management-advanced-policies.md#SendRequest)  
-   [küldési-egy-módon-kérelmek](api-management-advanced-policies.md#SendOneWayRequest)  
-   [napló-eventhub](api-management-advanced-policies.md#log-to-eventhub)  
-   [JSON-xml](api-management-transformation-policies.md#ConvertJSONtoXML)  
-   [XML-json](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>LastError

 Ha hiba lép fel, és a vezérlő ugrik a `on-error` házirend szakaszban tárolódik a hiba [környezetben. Hiba](api-management-policy-expressions.md#ContextVariables) tulajdonság, amely hozzáfér a házirendek a `on-error` szakasz. Hiba a következő tulajdonságokkal rendelkezik.  
  
| Name (Név)     | Típus   | Leírás                                                                                               | Szükséges |
|----------|--------|-----------------------------------------------------------------------------------------------------------|----------|
| Forrás   | sztring | Ahol a hiba történt az elem nevét. Lehet, vagy a házirend, vagy a beépített folyamat nevét.     | Igen      |
| Ok   | sztring | Gépet-barát hibakód, amely hibakezelés lesznek használva.                                       | Nem       |
| Üzenet  | sztring | Emberek számára olvasható hiba leírása.                                                                         | Igen      |
| Hatókör    | sztring | Ha a hiba történt, ezért lehet egy "globális", "termék", "api" vagy "művelet" hatókör neve | Nem       |
| Section  | sztring | Ha hiba történt a szakasz nevét. A lehetséges értékek: "bejövő", "Háttér", "kimenő" vagy "error".       | Nem       |
| Útvonal     | sztring | Meghatározza a beágyazott házirend, például "[3] válasszon / amikor [2]".                                                        | Nem       |
| PolicyId | sztring | Az érték a `id` attribútumot, ha meg van adva, a házirend, ahol a hiba történt az ügyfél által             | Nem       |

> [!TIP]
> Az állapotkód: környezet keresztül érheti el. Response.StatusCode.  

> [!NOTE]
> Összes házirend rendelkezik egy nem kötelező `id` attribútum adható hozzá a házirend a gyökérelemhez. Ha ez az attribútum szerepel egy házirend Ha hiba történik, az attribútum értéke lehet beolvasni használatával a `context.LastError.PolicyId` tulajdonság.

## <a name="predefined-errors-for-built-in-steps"></a>A beépített lépéseket előre definiált hibák  
 Hibák a következők előre meghatározott beépített feldolgozási lépéseket kiértékelése során előforduló hiba feltételeket.  
  
| Forrás        | Állapot                                 | Ok                  | Üzenet                                                                                                                |
|---------------|-------------------------------------------|-------------------------|------------------------------------------------------------------------------------------------------------------------|
| konfiguráció | URI nem felel meg a bármely API-t vagy a művelet | OperationNotFound       | Nem lehet megfeleltetni a bejövő kérelem művelethez.                                                                      |
| Engedélyezési | Nincs megadva előfizetés-kulcs             | SubscriptionKeyNotFound | Hiányzó előfizetés kulcs miatt megtagadva. Ügyeljen arra, hogy ez az API a kérelmet benyújtó előfizetés kulcs tartalmazniuk. |
| Engedélyezési | Előfizetés kulcs értéke érvénytelen.         | SubscriptionKeyInvalid  | Érvénytelen előfizetés kulcs miatt megtagadta a hozzáférést. Ügyeljen arra, hogy adjon meg egy érvényes és aktív az előfizetése kulcs.            |
  
## <a name="predefined-errors-for-policies"></a>Előre definiált hibák házirendek  
 Hibák a következők házirend kiértékelése közben fellépő hibaállapotokat az előre.  
  
| Forrás       | Állapot                                                       | Ok                    | Üzenet                                                                                                                              |
|--------------|-----------------------------------------------------------------|---------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| a Sebességhatár   | Sávszélesség-korlátjának túllépve                                             | RateLimitExceeded         | Sávszélesség-korlátjának túllépésekor                                                                                                               |
| kvóta        | Túllépte a kvótát.                                                  | QuotaExceeded             | A csomagba foglalt lebeszélhető percek elfogytak. Kvóta lesz tehát fel kell tölteni a xx:xx:xx. - e - sávszélesség kvótát. Kvóta lesz tehát fel kell tölteni a xx:xx:xx. |
| jsonp        | A visszahívási paraméter értéke érvénytelen (helytelen karaktereket tartalmaz.) | CallbackParameterInvalid  | {Visszahívás-paraméter-neve} visszahívási paraméter értéke nem egy érvényes JavaScript-azonosító.                                          |
| ip-filter    | Nem sikerült értelmezni a kérelmet hívó IP                          | FailedToParseCallerIP     | Nem sikerült létrehozni az IP-címet a hívónak. Hozzáférés megtagadva.                                                                        |
| ip-filter    | Hívó az IP-cím nem az engedélyezettek listájához                                | CallerIpNotAllowed        | Hívó IP-cím {ip-cím} nem engedélyezett. Hozzáférés megtagadva.                                                                        |
| ip-filter    | Hívó tiltólistán szereplő IP-cím                                    | CallerIpBlocked           | Hívó IP-cím le van tiltva. Hozzáférés megtagadva.                                                                                         |
| ellenőrzés-fejléc | Nem jelenik meg a szükséges fejléc vagy -érték hiányzik               | HeaderNotFound            | {Fejlécnév} fejléc nem található a kérelemben. Hozzáférés megtagadva.                                                                    |
| ellenőrzés-fejléc | Nem jelenik meg a szükséges fejléc vagy -érték hiányzik               | HeaderValueNotAllowed     | {Fejlécérték} {fejlécnév} fejléc értéke nem engedélyezett. Hozzáférés megtagadva.                                                          |
| jwt ellenőrzése | Jwt jogkivonat nem található a kérelemben                                 | TokenNotFound             | A JWT nem található a kérelemben. Hozzáférés megtagadva.                                                                                         |
| jwt ellenőrzése | Aláírás érvényesítése nem sikerült                                     | TokenSignatureInvalid     | < jwt-könyvtárból üzenet\>. Hozzáférés megtagadva.                                                                                          |
| jwt ellenőrzése | Érvénytelen a célközönség                                                | TokenAudienceNotAllowed   | < jwt-könyvtárból üzenet\>. Hozzáférés megtagadva.                                                                                          |
| jwt ellenőrzése | Érvénytelen kibocsátót                                                  | TokenIssuerNotAllowed     | < jwt-könyvtárból üzenet\>. Hozzáférés megtagadva.                                                                                          |
| jwt ellenőrzése | Token lejárt.                                                   | TokenExpired              | < jwt-könyvtárból üzenet\>. Hozzáférés megtagadva.                                                                                          |
| jwt ellenőrzése | Aláírási kulcs nem lett feloldva-azonosító szerint                            | TokenSignatureKeyNotFound | < jwt-könyvtárból üzenet\>. Hozzáférés megtagadva.                                                                                          |
| jwt ellenőrzése | Szükséges jogcímeket a jogkivonatból hiányoznak                          | TokenClaimNotFound        | JWT jogkivonat hiányzik a következő jogcímeket: < c1\>, < c2\>,... Hozzáférés megtagadva.                                                            |
| jwt ellenőrzése | Jogcím értékek eltérés                                           | TokenClaimValueNotAllowed | {Jogcímérték} {jogcím-neve} jogcím értéke nem engedélyezett. Hozzáférés megtagadva.                                                             |
| jwt ellenőrzése | Más érvényesítési hibák                                       | JwtInvalid                | < üzenet jwt-könyvtárból\>                                                                                                          |

## <a name="example"></a>Példa

Az API-házirend beállítást:

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

és jogosulatlan kérelmet küld a következő válasz eredményezi:

![Jogosulatlan hibaválaszba](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>További lépések

Házirendek használata további információkért lásd:

+ [Az API Management házirendek](api-management-howto-policies.md)
+ [Átalakítás API-k](transform-api.md)
+ [Házirend-hivatkozás](api-management-policy-reference.md) házirend-utasításoknál és a beállítások teljes listáját
+ [Házirend-minták](policy-samples.md)   