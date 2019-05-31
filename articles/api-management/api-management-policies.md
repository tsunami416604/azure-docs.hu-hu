---
title: Az Azure API Management szabályzata |} A Microsoft Docs
description: Ismerkedés az Azure API Management szolgáltatásban elérhető szabályzatokkal.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: e27829fe5ebf57552ef4e97a2bfc7b6aefd81dc8
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2019
ms.locfileid: "66254400"
---
# <a name="api-management-policies"></a>API Management házirendek
Ebben a szakaszban egy hivatkozást kínál a következő az API Management házirendek. Hozzáadása és házirendek konfigurálásával kapcsolatos tudnivalókat lásd: [az API Management házirendek](api-management-howto-policies.md).  
  
 Szabályzatok olyan egy hatékony képesség, a rendszer, amely lehetővé teszi a közzétevő, az API konfigurálással változtathatja meg. Szabályzatok olyan kérelmei végrehajtott utasítások gyűjteményei, vagy az API-válaszban. A népszerű utasítások közé tartozik a formátumátalakítás XML-ről JSON-ná, és a hívások sebességének korlátozása a fejlesztőktől érkező hívások mennyiségének korlátozásához. Számos további házirendeket beépített érhetők el.  
  
 A házirend-kifejezéseket attribútumértékekként vagy szövegértékekként lehet használni bármelyik API Management házirendben, hacsak a házirend másként nem rendelkezik. Néhány házirend, például a [Vezérlés folyamata](api-management-advanced-policies.md#choose) és a [Változó beállítása](api-management-advanced-policies.md#set-variable) házirend-kifejezéseken alapul. További információ: [Speciális szabályzatok](api-management-advanced-policies.md#AdvancedPolicies) és [Szabályzatkifejezések](api-management-policy-expressions.md).  
  
##  <a name="ProxyPolicies"></a> Szabályzatok  
  
-   [Hozzáférés-korlátozási házirendek](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Ellenőrizze a HTTP-fejléc](api-management-access-restriction-policies.md#CheckHTTPHeader) -kikényszeríti a létezik-e és/vagy a HTTP-fejléc értékét.  
    -   [A hívások sebességének korlátozása előfizetés](api-management-access-restriction-policies.md#LimitCallRate) – megakadályozza, hogy API-használat hirtelen megugró kihasználtság, a hívások sebességének, az minden előfizetés korlátozásával.  
    -   [A hívások sebességének korlátozása a kulcs által](api-management-access-restriction-policies.md#LimitCallRateByKey) – megakadályozza, hogy API-használat hirtelen megugró kihasználtság, a hívások sebességének per kulcs alapon korlátozásával.  
    -   [A hívó IP-címek korlátozása](api-management-access-restriction-policies.md#RestrictCallerIPs) – meghatározott IP-címek és/vagy címtartományokat (engedélyezi vagy megtagadja) szűrők hívásait.  
    -   [Előfizetés-használati kvóta beállítása](api-management-access-restriction-policies.md#SetUsageQuota) – lehetővé teszi a megújuló vagy élettartama hívás kötet és/vagy a sávszélesség kvóta, minden előfizetés érvényesítését.  
    -   [Használati kvóta beállítása kulcs által](api-management-access-restriction-policies.md#SetUsageQuotaByKey) – lehetővé teszi a megújuló vagy élettartama hívás kötet és/vagy a sávszélesség kvóta, per kulcs alapon kikényszerítésére.  
    -   [Ellenőrizze a JWT](api-management-access-restriction-policies.md#ValidateJWT) -kényszerít létezését és a jwt-t, vagy egy adott HTTP-fejléc, vagy egy megadott lekérdezési paraméter kinyert érvényességét.  
-   [Speciális házirendek](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Átvitelvezérlés](api-management-advanced-policies.md#choose) – feltételesen kiértékelésének eredménye logikai kifejezésen alapuló házirend-utasítások vonatkozik.  
    -   [Kérés továbbítása](api-management-advanced-policies.md#ForwardRequest) -továbbítja a kérést a háttérszolgáltatáshoz.
    -   [Korlátozza az egyidejűség](api-management-advanced-policies.md#LimitConcurrency) -megakadályozza, hogy egyszerre több, mint a megadott számú kérelem által végrehajtása az szabályzatok idézőjelek között.
    -   [Naplózás az Eseményközpontba](api-management-advanced-policies.md#log-to-eventhub) -üzeneteket küld egy naplózó entitás által meghatározott üzenetet célként a megadott formátumban.
    -   [Válaszok utánzása](api-management-advanced-policies.md#mock-response) -megszakításainak folyamat-végrehajtás és közvetlenül a hívó számára utánzott válaszokat adjanak vissza.
    -   [Ismételje meg](api-management-advanced-policies.md#Retry) -újrapróbálkozik a mellékelt házirend-utasítások végrehajtása, ha, és amíg a feltétel nem teljesül. Végrehajtási fog a megadott időközönként ismételje meg, és legfeljebb a megadott újrapróbálkozások száma.  
    -   [Választ adja vissza](api-management-advanced-policies.md#ReturnResponse) -megszakításainak folyamat-végrehajtás és a közvetlenül a hívó adott választ adja vissza.  
    -   [Az egyirányú kérést küldhet](api-management-advanced-policies.md#SendOneWayRequest) -kérést küld a megadott URL-cím válaszra való várakozás nélkül.  
    -   [Kérés küldése a](api-management-advanced-policies.md#SendRequest) -kérést küld a megadott URL-cím.
    -   [Állítsa be a HTTP-proxy](api-management-advanced-policies.md#SetHttpProxy) – lehetővé teszi, hogy a HTTP-proxyn keresztül továbbított átirányíthatja a kéréseket.
    -   [Változó beállítása](api-management-advanced-policies.md#set-variable) – megőrzéséhez a későbbi eléréshez nevű környezeti változót egy értéket.  
    -   [Állítsa be a kérelmi metódust](api-management-advanced-policies.md#SetRequestMethod) -kérelem HTTP-metódus módosíthatja.  
    -   [Állítsa be az állapotkód](api-management-advanced-policies.md#SetStatus) – HTTP-állapotkód módosítja a megadott érték.  
    -   [Nyomkövetési](api-management-advanced-policies.md#Trace) -hozzáad egy karakterláncot, a [API Inspectorral](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) kimeneti.  
    -   [Várjon](api-management-advanced-policies.md#Wait) -vár az idézőjelek között [küldési kérelmek](api-management-advanced-policies.md#SendRequest), [érték lekérése a gyorsítótárból](api-management-caching-policies.md#GetFromCacheByKey), vagy [átvitelvezérlés](api-management-advanced-policies.md#choose) házirendek befejezését, mielőtt továbblépne.  
-   [Hitelesítési házirendek](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Az alapszintű hitelesítéshez](api-management-authentication-policies.md#Basic) -háttérszolgáltatás alapszintű hitelesítést használ a hitelesítéshez.  
    -   [Ügyféltanúsítvány a hitelesítéshez](api-management-authentication-policies.md#ClientCertificate) -háttérszolgáltatás ügyféltanúsítványok használatát a hitelesítéshez.  
    -   [Felügyelt identitás a hitelesítéshez](api-management-authentication-policies.md#ManagedIdentity) -hitelesítést egy háttérbeli szolgáltatás használata egy [identitás](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).  
-   [Gyorsítótárazási házirendek](api-management-caching-policies.md#CachingPolicies)  
    -   [Lekérése a gyorsítótárból](api-management-caching-policies.md#GetFromCache) – hajtsa végre a gyorsítótár kereshet meg és adja vissza egy érvényes gyorsítótárazott választ, ha elérhető.  
    -   [Store Cache](api-management-caching-policies.md#StoreToCache) -gyorsítótárazza a választ a megadott gyorsítótár-vezérlő konfigurációjának megfelelően.  
    -   [Érték lekérése a gyorsítótárból](api-management-caching-policies.md#GetFromCacheByKey) -kulcs által gyorsítótárazott elem beolvasása.  
    -   [Store értéket a gyorsítótárban](api-management-caching-policies.md#StoreToCacheByKey) -Store egy elemet a gyorsítótárban kulcs szerint.  
    -   [Érték eltávolítása a gyorsítótárból](api-management-caching-policies.md#RemoveCacheByKey) -távolítani egy elemet a gyorsítótárban kulcs szerint.  
-   [Tartományközi házirendek](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   [Lehetővé teszi a tartományok közötti hívások](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – az API-hoz elérhető lesz az Adobe Flash és a Microsoft Silverlight webböngésző-alapú ügyfelekről.  
    -   [A CORS](api-management-cross-domain-policies.md#CORS) -hozzáadja az eltérő eredetű erőforrások megosztása (CORS) támogatásával egy műveletet vagy API-t lehetővé teszi a tartományok közötti hívások webböngésző-alapú ügyfelekről.  
    -   [JSNOP](api-management-cross-domain-policies.md#JSONP) -JSON ad hozzá egy műveletet vagy API-t lehetővé teszi a tartományok közötti hívások JavaScript-ügyfelekből böngészőalapú padding (JSNOP) támogatásával.  
-   [Átalakítási házirendek](api-management-transformation-policies.md#TransformationPolicies)  
    -   [JSON átalakítása XML](api-management-transformation-policies.md#ConvertJSONtoXML) – alakíthatók át egymásba kérelem vagy válasz törzs JSON-ból XML.  
    -   [XML átalakítása JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) – alakíthatók át egymásba kérelem vagy válasz törzs XML-ről JSON-ná.  
    -   [A szervezet karakterlánc keresése és cseréje](api-management-transformation-policies.md#Findandreplacestringinbody) - kérelem vagy válasz részkarakterláncot keres, és lecseréli egy másik karakterláncrészletet.  
    -   [Tartalom URL-címek maszkolja](api-management-transformation-policies.md#MaskURLSContent) -hivatkozások (maszkok) újra ír a válaszban törzs úgy, hogy a megfelelő hivatkozásra az átjárón keresztül mutassanak.  
    -   [Állítsa be a háttérszolgáltatás](api-management-transformation-policies.md#SetBackendService) – módosítja egy bejövő kérésnek tartozó a háttérszolgáltatáshoz.  
    -   [Állítsa be a szervezet](api-management-transformation-policies.md#SetBody) – beállítja a bejövő és kimenő kéréseket az üzenet törzse.  
    -   [HTTP-fejléc beállítása](api-management-transformation-policies.md#SetHTTPheader) – értéket rendel hozzá egy meglévő válasz és/vagy a kérelem fejlécében vagy ad hozzá egy új válasz és/vagy a kérelem fejlécében.  
    -   [Állítsa be a lekérdezési sztring paramétereként](api-management-transformation-policies.md#SetQueryStringParameter) – ad hozzá, értéke váltja fel, vagy töröl a kérés lekérdezési karakterlánc paramétereként.  
    -   [URL-cím újraírása](api-management-transformation-policies.md#RewriteURL) -alakítja át a kérelem URL-cím a nyilvános űrlap az űrlap a webszolgáltatás által várt.  
    -   [Az XSLT-vel XML-átalakítás](api-management-transformation-policies.md#XSLTransform) -XSL átalakító érvényes XML-kérelem vagy válasz törzsében.  



## <a name="next-steps"></a>További lépések
Házirendek használata további információkért lásd:

+ [Az API Management házirendek](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ [A házirend-minták](policy-samples.md)   
