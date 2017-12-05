---
title: "Hiba történt az Azure API-felügyeleti házirendek kezelése |} Microsoft Docs"
description: "Ismerje meg, hogyan válaszol az Azure API Management-kérelem feldolgozása közben fellépő hibaállapotokat."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 6bc71c0745493d52128553a78a31c45a3bca30f8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2017
---
# <a name="error-handling-in-api-management-policies"></a>Hiba történt az API-felügyeleti házirendek kezelése
Az Azure API Management lehetővé teszi, hogy válaszoljanak a hibaállapotokat, amely jelentkezhet a kérelem feldolgozása során a proxy, adja meg a közzétevők egy `ProxyError` objektum. A `ProxyError` objektum keresztül érhető el a [környezetben. Hiba](api-management-policy-expressions.md#ContextVariables) tulajdonság, és a házirendek által használható a `on-error` házirend szakaszban. Ez a témakör egy hivatkozást a hiba kezelési képességeket biztosít az Azure API Management.  
  
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
  
 A kérelem feldolgozása során beépített lépéseit együtt a kérelem hatókörébe házirendekben hajtja végre. Ha hiba lép fel, feldolgozása azonnal ugrik a `on-error` házirend szakaszban. A `on-error` házirend szakasz használható bármely hatókörű, és API-közzétevők konfigurálhatja például az event hubs jelentkezik a hiba, vagy hozzon létre egy új válasz térjen vissza a hívó egyéni működését.  
  
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
  
## <a name="lasterror"></a>Hiba  
 Ha hiba lép fel, és a vezérlő ugrik a `on-error` házirend szakaszban tárolódik a hiba [környezetben. Hiba](api-management-policy-expressions.md#ContextVariables) tulajdonság, amely hozzáfér a házirendek a `on-error` szakaszt, és a következő tulajdonságokkal rendelkezik.  
  
|Név|Típus|Leírás|Szükséges|  
|----------|----------|-----------------|--------------|  
|Forrás|Karakterlánc|Ahol a hiba történt az elem nevét. Lehet, vagy a házirend, vagy a beépített folyamat nevét.|Igen|  
|Ok|Karakterlánc|Gépet-barát hibakód, amely hibakezelés lesznek használva.|Nem|  
|Üzenet|Karakterlánc|Emberek számára olvasható hiba leírása.|Igen|  
|Hatókör|Karakterlánc|Ha a hiba történt, ezért lehet egy "globális", "termék", "api" vagy "művelet" hatókör neve|Nem|  
|Section|Karakterlánc|A szakasz nevét, ahol hiba történt, és sikerült valamelyik "bejövő", "Háttér", "kimenő" vagy "error".|Nem|  
|Útvonal|Karakterlánc|Meghatározza a beágyazott házirend, pl. "[3] válasszon / amikor [2]".|Nem|  
|PolicyId|Karakterlánc|Az érték a `id` attribútumot, ha meg van adva, a házirend, ahol a hiba történt az ügyfél által|Nem|  
  
> [!NOTE]
>  Összes házirend rendelkezik egy nem kötelező `id` attribútum adható hozzá a házirend a gyökérelemhez. Ha ez az attribútum szerepel egy házirend Ha hiba történik, az attribútum értéke lehet beolvasni használatával a `context.LastError.PolicyId` tulajdonság.  
  
## <a name="predefined-errors-for-built-in-steps"></a>A beépített lépéseket előre definiált hibák  
 Hibák a következők előre meghatározott beépített feldolgozási lépéseket kiértékelése során előforduló hiba feltételeket.  
  
|Forrás|Feltétel|Ok|Üzenet|  
|------------|---------------|------------|-------------|  
|konfiguráció|URI nem felel meg a bármely API-t vagy a művelet|OperationNotFound|Nem lehet megfeleltetni a bejövő kérelem művelethez.|  
|Engedélyezési|Nincs megadva előfizetés-kulcs|SubscriptionKeyNotFound|Hiányzó előfizetés kulcs miatt megtagadva. Ügyeljen arra, hogy ez az API a kérelmet benyújtó előfizetés kulcs tartalmazniuk.|  
|Engedélyezési|Előfizetés kulcs értéke érvénytelen.|SubscriptionKeyInvalid|Érvénytelen előfizetés kulcs miatt megtagadta a hozzáférést. Ügyeljen arra, hogy adjon meg egy érvényes és aktív az előfizetése kulcs.|  
  
## <a name="predefined-errors-for-policies"></a>Előre definiált hibák házirendek  
 Hibák a következők házirend kiértékelése közben fellépő hibaállapotokat az előre.  
  
|Forrás|Feltétel|Ok|Üzenet|  
|------------|---------------|------------|-------------|  
|a Sebességhatár|Sávszélesség-korlátjának túllépve|RateLimitExceeded|Sávszélesség-korlátjának túllépésekor|  
|kvóta|Túllépte a kvótát.|QuotaExceeded|A csomagba foglalt lebeszélhető percek elfogytak. Kvóta lesz tehát fel kell tölteni a xx:xx:xx. - e - sávszélesség kvótát. Kvóta lesz tehát fel kell tölteni a xx:xx:xx.|  
|jsonp|A visszahívási paraméter értéke érvénytelen (helytelen karaktereket tartalmaz.)|CallbackParameterInvalid|{Visszahívás-paraméter-neve} visszahívási paraméter értéke nem egy érvényes JavaScript-azonosító.|  
|IP-szűrő|Nem sikerült értelmezni a kérelmet hívó IP|FailedToParseCallerIP|Nem sikerült létrehozni az IP-címet a hívónak. Hozzáférés megtagadva.|  
|IP-szűrő|Hívó az IP-cím nem az engedélyezettek listájához|CallerIpNotAllowed|Hívó IP-cím {ip-cím} nem engedélyezett. Hozzáférés megtagadva.|  
|IP-szűrő|Hívó tiltólistán szereplő IP-cím|CallerIpBlocked|Hívó IP-cím le van tiltva. Hozzáférés megtagadva.|  
|ellenőrzés-fejléc|Nem jelenik meg a szükséges fejléc vagy -érték hiányzik|HeaderNotFound|{Fejlécnév} fejléc nem található a kérelemben. Hozzáférés megtagadva.|  
|ellenőrzés-fejléc|Nem jelenik meg a szükséges fejléc vagy -érték hiányzik|HeaderValueNotAllowed|{Fejlécérték} {fejlécnév} fejléc értéke nem engedélyezett. Hozzáférés megtagadva.|  
|jwt ellenőrzése|Jwt jogkivonat nem található a kérelemben|TokenNotFound|A JWT nem található a kérelemben. Hozzáférés megtagadva.|  
|jwt ellenőrzése|Aláírás érvényesítése nem sikerült|TokenSignatureInvalid|< jwt-könyvtárból üzenet\>. Hozzáférés megtagadva.|  
|jwt ellenőrzése|Érvénytelen a célközönség|TokenAudienceNotAllowed|< jwt-könyvtárból üzenet\>. Hozzáférés megtagadva.|  
|jwt ellenőrzése|Érvénytelen kibocsátót|TokenIssuerNotAllowed|< jwt-könyvtárból üzenet\>. Hozzáférés megtagadva.|  
|jwt ellenőrzése|Token lejárt.|TokenExpired|< jwt-könyvtárból üzenet\>. Hozzáférés megtagadva.|  
|jwt ellenőrzése|Aláírási kulcs nem lett feloldva-azonosító szerint|TokenSignatureKeyNotFound|< jwt-könyvtárból üzenet\>. Hozzáférés megtagadva.|  
|jwt ellenőrzése|Szükséges jogcímeket a jogkivonatból hiányoznak|TokenClaimNotFound|JWT jogkivonat hiányzik a következő jogcímeket: < c1\>, < c2\>,... Hozzáférés megtagadva.|  
|jwt ellenőrzése|Jogcím értékek eltérés|TokenClaimValueNotAllowed|{Jogcímérték} {jogcím-neve} jogcím értéke nem engedélyezett. Hozzáférés megtagadva.|  
|jwt ellenőrzése|Más érvényesítési hibák|JwtInvalid|< üzenet jwt-könyvtárból\>|

## <a name="next-steps"></a>Következő lépések

Házirendek használata további információkért lásd:

+ [Az API Management házirendek](api-management-howto-policies.md)
+ [Átalakítás API-k](transform-api.md)
+ [Házirend-hivatkozás](api-management-policy-reference.md) házirend-utasításoknál és a beállítások teljes listáját
+ [Házirend-minták](policy-samples.md)   