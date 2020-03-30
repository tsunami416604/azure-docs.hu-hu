---
title: Azure API-kezelési házirendek | Microsoft dokumentumok
description: Ismerkedés az Azure API Management szolgáltatásban elérhető szabályzatokkal.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 2b6e056fbfb134f0b1218b4281b9f971a0e24202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71219469"
---
# <a name="api-management-policies"></a>API Management házirendek
Ez a szakasz a következő API Management-házirendek hivatkozási. A házirendek hozzáadásáról és konfigurálásáról az [API-kezelés házirendjei](api-management-howto-policies.md)című témakörben talál további információt.  
  
 A házirendek a rendszer hatékony képessége, amely lehetővé teszi a közzétevő számára az API működését a konfiguráción keresztül. A szabályzatok olyan utasítások gyűjteményei, amelyek egy API kérésére vagy válaszára egymás után kerülnek végrehajtásra. A népszerű kivonatok közé tartozik az XML-ről JSON-ra történő formátumátalakítás és a hívási sebesség korlátozása a fejlesztőtől érkező bejövő hívások számának korlátozása érdekében. Sokkal több szabályzat érhető el a dobozból.  
  
 A házirend-kifejezéseket attribútumértékekként vagy szövegértékekként lehet használni bármelyik API Management házirendben, hacsak a házirend másként nem rendelkezik. Néhány házirend, például a [Vezérlés folyamata](api-management-advanced-policies.md#choose) és a [Változó beállítása](api-management-advanced-policies.md#set-variable) házirend-kifejezéseken alapul. További információ: [Speciális szabályzatok](api-management-advanced-policies.md#AdvancedPolicies) és [Szabályzatkifejezések](api-management-policy-expressions.md).  
  
##  <a name="policies"></a><a name="ProxyPolicies"></a> Házirendek  
  
-   [Hozzáférés-korlátozási szabályzatok](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
    -   [Http-fejléc ellenőrzése](api-management-access-restriction-policies.md#CheckHTTPHeader) – A HTTP-fejléc létezésének és/vagy értékének kényszerítése.  
    -   [Hívási sebesség korlátozása előfizetésenként](api-management-access-restriction-policies.md#LimitCallRate) – Megakadályozza az API-használat kiugrásait a hívási sebesség korlátozásával, előfizetésenként.  
    -   [Hívási sebesség korlátozása kulcs](api-management-access-restriction-policies.md#LimitCallRateByKey) szerint – Megakadályozza az API-használat kiugrásait a hívási sebesség korlátozásával, kulcsalapon.  
    -   [Hívó IP-címének korlátozása](api-management-access-restriction-policies.md#RestrictCallerIPs) – szűrők (lehetővé teszi/lehetővé teszi) a hívásokat adott IP-címekről és/vagy címtartományokból.  
    -   [Használati kvóta beállítása előfizetés enként](api-management-access-restriction-policies.md#SetUsageQuota) – Lehetővé teszi a megújuló vagy élettartamra szóló hívás mennyiségének és/vagy sávszélesség-kvótájának kényszerítése előfizetésenként.  
    -   [Használati kvóta kulcs szerint történő beállítása](api-management-access-restriction-policies.md#SetUsageQuotaByKey) – Lehetővé teszi a megújuló vagy élettartamra szóló hívás mennyiségének és/vagy sávszélesség-kvótájának kulcsonkénti kényszerítése.  
    -   [JWT érvényesítése](api-management-access-restriction-policies.md#ValidateJWT) – Egy adott HTTP-fejlécből vagy egy megadott lekérdezési paraméterből kinyert JWT meglétének és érvényességének kényszerítése.  
-   [Speciális házirendek](api-management-advanced-policies.md#AdvancedPolicies)  
    -   [Vezérlőfolyamat](api-management-advanced-policies.md#choose) – feltételesen alkalmazza a logikai kifejezések kiértékelése alapján kiadott házirend-utasítások.  
    -   [Kérés továbbítása](api-management-advanced-policies.md#ForwardRequest) – továbbítja a kérelmet a háttérszolgáltatás.
    -   [Egyidejűség korlátozása](api-management-advanced-policies.md#LimitConcurrency) – Megakadályozza, hogy a zárt házirendek egyszerre több kérést hajtsanak végre.
    -   [Napló az Event Hub -](api-management-advanced-policies.md#log-to-eventhub) üzenetekküldése a megadott formátumban egy üzenet cél által meghatározott naplózó entitás.
    -   [Mock response](api-management-advanced-policies.md#mock-response) - Megszakítja a folyamat végrehajtását, és egy kigúnyolt választ ad vissza közvetlenül a hívónak.
    -   [Újrapróbálkozás](api-management-advanced-policies.md#Retry) – A mellékelt házirend-utasítások végrehajtásának újrapróbálkozása, ha és amíg a feltétel nem teljesül. A végrehajtás a megadott időközönként és a megadott újrapróbálkozások számáig ismétlődik.  
    -   [Válasz-](api-management-advanced-policies.md#ReturnResponse) Megszakítja a folyamat végrehajtását, és a megadott választ közvetlenül a hívónak adja vissza.  
    -   [Egyirányú kérelem küldése](api-management-advanced-policies.md#SendOneWayRequest) – Kérés küldése a megadott URL-címre anélkül, hogy választ várna.  
    -   [Küldési kérelem](api-management-advanced-policies.md#SendRequest) – Kérés küldése a megadott URL-címre.
    -   [HTTP-proxy beállítása](api-management-advanced-policies.md#SetHttpProxy) – Lehetővé teszi a továbbított kérelmek http-proxyn keresztültörténő továbbítását.
    -   [Változó beállítása](api-management-advanced-policies.md#set-variable) – Egy névvel ellátott környezeti változóban lévő érték megőrzése későbbi hozzáférésesetén.  
    -   [Kérelem metódusának beállítása](api-management-advanced-policies.md#SetRequestMethod) – Lehetővé teszi a kérelem HTTP-metódusának módosítását.  
    -   [Állapotkód beállítása](api-management-advanced-policies.md#SetStatus) – A HTTP-állapotkódot a megadott értékre módosítja.  
    -   [Trace –](api-management-advanced-policies.md#Trace) egyéni nyomkövetéseket ad hozzá az [API-felügyelő](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) kimenetéhez, az Application Insights telemetriesés diagnosztikai naplók.  
    -   [Várakozás](api-management-advanced-policies.md#Wait) – A zárt [küldési kérelemre,](api-management-advanced-policies.md#SendRequest) [az érték gyorsítótárból való beolvasása](api-management-caching-policies.md#GetFromCacheByKey)vagy a folytatás előtt végrehajtandó [vezérlőfolyamat-házirendek](api-management-advanced-policies.md#choose) befejezésére vár.  
-   [Hitelesítési szabályzatok](api-management-authentication-policies.md#AuthenticationPolicies)  
    -   [Hitelesítés alapszintű](api-management-authentication-policies.md#Basic) - hitelesítés háttérszolgáltatással egyszerű hitelesítéssel.  
    -   [Hitelesítés ügyféltanúsítvánnyal](api-management-authentication-policies.md#ClientCertificate) – Hitelesítés háttérszolgáltatással az ügyféltanúsítványokkal.  
    -   [Hitelesítés felügyelt identitással](api-management-authentication-policies.md#ManagedIdentity) – Hitelesítés háttérszolgáltatással [felügyelt identitás](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)használatával.  
-   [Gyorsítótárazási házirendek](api-management-caching-policies.md#CachingPolicies)  
    -   [Beolvasás a gyorsítótárból](api-management-caching-policies.md#GetFromCache) – Gyorsítótár-felolvasás végrehajtása, és érvényes gyorsítótárazott válasz visszaadása, ha elérhető.  
    -   [Tároló a gyorsítótárba](api-management-caching-policies.md#StoreToCache) – Gyorsítótáraszta a választ a megadott gyorsítótár-vezérlő konfigurációnak megfelelően.  
    -   [Érték beolvasása a gyorsítótárból](api-management-caching-policies.md#GetFromCacheByKey) – Gyorsítótárazott elem lekérése kulcs onként.  
    -   [Tárolóérték a gyorsítótárban](api-management-caching-policies.md#StoreToCacheByKey) – Elem tárolása a gyorsítótárban kulcs szerint.  
    -   [Érték eltávolítása a gyorsítótárból](api-management-caching-policies.md#RemoveCacheByKey) – Elem eltávolítása a gyorsítótárból kulcs szerint.  
-   [Tartományközi házirendek](api-management-cross-domain-policies.md#CrossDomainPolicies)  
    -   Tartományok közötti hívások engedélyezése – Elérhetővé teszi az [API-t](api-management-cross-domain-policies.md#AllowCrossDomainCalls) az Adobe Flash és a Microsoft Silverlight böngészőalapú ügyfelektől.  
    -   [CORS](api-management-cross-domain-policies.md#CORS) – Több forrásból származó erőforrás-megosztási (CORS) támogatást ad egy művelethez vagy egy API-hoz, hogy lehetővé tegye a tartományok közötti hívásokat a böngészőalapú ügyfelektől.  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) – JSON-t ad hozzá egy művelethez vagy EGY API-hoz, amely lehetővé teszi a JavaScript böngészőalapú ügyfelek tartományok közötti hívásait.  
-   [Átalakítási szabályzatok](api-management-transformation-policies.md#TransformationPolicies)  
    -   [A JSON konvertálása XML formátumba](api-management-transformation-policies.md#ConvertJSONtoXML) – A kérelem- vagy választörzs et JSON-ból XML formátumba konvertálja.  
    -   [XML konvertálása JSON-](api-management-transformation-policies.md#ConvertXMLtoJSON) A kérelem- vagy választörzset XML-ről JSON-ra konvertálja.  
    -   [Karakterlánc keresése és cseréje a törzsben](api-management-transformation-policies.md#Findandreplacestringinbody) – Megkeresi a kérelem- vagy válaszkarakterlánc-részkarakterláncot, és egy másik részkarakterláncra cseréli.  
    -   [Maszk URL-címek a tartalomban](api-management-transformation-policies.md#MaskURLSContent) - Újraírja (maszkok) linkeket a választörzsben, hogy azok pont az egyenértékű linket az átjárón keresztül.  
    -   [Háttérszolgáltatás beállítása](api-management-transformation-policies.md#SetBackendService) – módosítja a háttérszolgáltatás egy bejövő kérelemhez.  
    -   [Készlet beállítása](api-management-transformation-policies.md#SetBody) – Beállítja az üzenet törzsét a bejövő és kimenő kérelmekhez.  
    -   [HTTP-fejléc beállítása](api-management-transformation-policies.md#SetHTTPheader) – Értéket rendel egy meglévő válasz- és/vagy kérelemfejléchez, vagy új válasz- és/vagy kérelemfejlécet ad hozzá.  
    -   [Lekérdezési karakterlánc paraméter beállítása](api-management-transformation-policies.md#SetQueryStringParameter) – Hozzáadja, lecseréli vagy törli a kérelem lekérdezési karakterlánc-paraméterét.  
    -   [URL-cím újraírása](api-management-transformation-policies.md#RewriteURL) – A kérelem URL-címét a nyilvános űrlapról a webszolgáltatás által elvárt űrlapra konvertálja.  
    -   [XML átalakítása XSLT használatával](api-management-transformation-policies.md#XSLTransform) – XSL-átalakításalkalmazása XML-re a kérelem- vagy választörzsben.  



## <a name="next-steps"></a>További lépések
A házirendekkel kapcsolatos további információkért lásd:

+ [Szabályzatok az API Managementben](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ [Házirendminták](policy-samples.md)   
