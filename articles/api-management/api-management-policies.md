---
title: "Az Azure API-felügyeleti házirendek |} Microsoft Docs"
description: "További tudnivalók a házirendek az Azure API Management használható."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 1cc460cb-8e67-41aa-bc76-bbafc1892798
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 89d76c5d00f8a438cfec7fd1568d4735f0a65327
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="api-management-policies"></a>API Management házirendek
Ez a szakasz a következő API-felügyeleti házirendek nyújt. Hozzáadása és házirendek konfigurálásával kapcsolatos tudnivalókat lásd: [házirendek az API Management](api-management-howto-policies.md).  
  
 Házirendek olyan egy hatékony képesség, a rendszer, amelyek lehetővé teszik a közzétevőt úgy, hogy az API-t konfigurálással működésének módosításához. Házirendek olyan utasításokat, amelyek egymás után, ha a kérés végrehajtása gyűjteménye vagy egy API-t adott válaszokat. Népszerű utasítások JSON az XML-formátumú átalakítás tartalmazza, és hívja meg egy fejlesztő a bejövő hívások mennyiségének korlátozásához sebességével. Számos további házirendeket nem érhetők el.  
  
 A házirend-kifejezéseket attribútumértékekként vagy szövegértékekként lehet használni bármelyik API Management házirendben, hacsak a házirend másként nem rendelkezik. Néhány házirend, például a [Vezérlés folyamata](api-management-advanced-policies.md#choose) és a [Változó beállítása](api-management-advanced-policies.md#set-variable) házirend-kifejezéseken alapul. További információkért lásd: [házirendek speciális](api-management-advanced-policies.md#AdvancedPolicies) és [házirend-kifejezések](api-management-policy-expressions.md).  
  
##  <a name="ProxyPolicies"></a>Házirendek  
  
-   [Hozzáférés-korlátozási házirendek](api-management-access-restriction-policies.md#AccessRestrictionPolicies)  
  
    -   [Ellenőrizze a HTTP-fejléc](api-management-access-restriction-policies.md#CheckHTTPHeader) -érvénybe lépteti a létezését és/vagy a HTTP-fejléc értékét.  
  
    -   [Előfizetési határértéket hívás arányt a](api-management-access-restriction-policies.md#LimitCallRate) -megakadályozza, hogy API-használati napra hívás arány / előfizetés alapon korlátozásával.  
  
    -   [Korlát hívás arányt a kulcs](api-management-access-restriction-policies.md#LimitCallRateByKey) -megakadályozza, hogy API-használati napra hívás arány / kulcs alapon korlátozásával.  
  
    -   [A hívó IP-címek korlátozása](api-management-access-restriction-policies.md#RestrictCallerIPs) -szűrők (engedélyezi vagy megtagadja) hívást bizonyos IP-címeket és/vagy címtartományokat.  
  
    -   [Set memóriahasználati kvóta előfizetéssel](api-management-access-restriction-policies.md#SetUsageQuota) -lehetővé teszi egy megújítható vagy élettartama hívás mennyiségi és/vagy a sávszélesség kvótát, egy előfizetés alapon érvényesítését.  
  
    -   [Set memóriahasználati kvóta kulcs által](api-management-access-restriction-policies.md#SetUsageQuotaByKey) -lehetővé teszi egy megújítható vagy élettartama hívás mennyiségi és/vagy a sávszélesség kvótát, egy kulcs alapon érvényesítését.  
  
    -   [Ellenőrizze a JWT](api-management-access-restriction-policies.md#ValidateJWT) -érvénybe lépteti a létezését és a jwt-t vagy a megadott HTTP-fejléc, vagy a megadott lekérdezési paraméter kinyert érvényességét.  
  
-   [Speciális házirendek](api-management-advanced-policies.md#AdvancedPolicies)  
  
    -   [Folyamat szabályozása](api-management-advanced-policies.md#choose) - feltételesen alkalmazza a házirend-utasításoknál logikai kifejezésen kiértékelésén alapul.  
  
    -   [Kérés továbbítása a](api-management-advanced-policies.md#ForwardRequest) -továbbítja a kérést a háttérszolgáltatáshoz.  
  
    -   [Az Event Hubs napló](api-management-advanced-policies.md#log-to-eventhub) -üzeneteket küld egy határozzák meg a tranzakciónaplókat tartalmazó entitás üzenetcél a megadott formátumban.  
  
    -   [Próbálja meg újra](api-management-advanced-policies.md#Retry) -újrapróbálkozások zárt házirend utasítás végrehajtása, ha, és amíg a feltétel nem teljesül. Végrehajtási ismételje meg a megadott időközönként, és a legfeljebb a megadott újrapróbálkozások száma.  
  
    -   [Térjen vissza a válasz](api-management-advanced-policies.md#ReturnResponse) -megszakításainak-feldolgozási folyamat végrehajtása és a közvetlenül a hívó adott választ.  
  
    -   [Egyirányú kérés küldése](api-management-advanced-policies.md#SendOneWayRequest) -kérést küld a megadott URL-cím a válaszra való várakozás nélkül.  
  
    -   [Kérés küldése](api-management-advanced-policies.md#SendRequest) -kérést küld a megadott URL-cím.  
  
    -   [Új érték](api-management-advanced-policies.md#set-variable) -megőrzéséhez a későbbi eléréshez elnevezett környezeti változóban értéket.  
  
    -   [Kérelem módszert állítja be](api-management-advanced-policies.md#SetRequestMethod) -módosíthatja a kérelem HTTP-metódust.  
  
    -   [Állítsa be. állapotkód:](api-management-advanced-policies.md#SetStatus) – a HTTP-állapotkód: a megadott értékre változik.  
  
    -   [Nyomkövetési](api-management-advanced-policies.md#Trace) -be egy karakterláncot ad a [API Inspector](https://azure.microsoft.com/en-us/documentation/articles/api-management-howto-api-inspector/) kimeneti.  
  
    -   [Várjon, amíg](api-management-advanced-policies.md#Wait) -megvárja-e a zárójelek között [küldési kérelmek](api-management-advanced-policies.md#SendRequest), [lehet értéket kiolvasni a gyorsítótár](api-management-caching-policies.md#GetFromCacheByKey), vagy [folyamatot szabályozhatja](api-management-advanced-policies.md#choose) házirendeket befejeződjön, mielőtt továbblép.  
  
-   [Hitelesítési házirendek](api-management-authentication-policies.md#AuthenticationPolicies)  
  
    -   [Basic hitelesítés](api-management-authentication-policies.md#Basic) -alapszintű hitelesítést használó háttérszolgáltatás a hitelesítést.  
  
    -   [Az ügyféltanúsítvány hitelesítéséhez](api-management-authentication-policies.md#ClientCertificate) -az ügyféltanúsítványok háttérszolgáltatás a hitelesítést.  
  
-   [Gyorsítótárazási házirendek](api-management-caching-policies.md#CachingPolicies)  
  
    -   [Gyorsítótár beszerezni](api-management-caching-policies.md#GetFromCache) -hajtsa végre a gyorsítótár kereshet, és térjen vissza egy érvényes gyorsítótárazott választ, ha elérhető.  
  
    -   [Gyorsítótárazandó tároló](api-management-caching-policies.md#StoreToCache) -gyorsítótárazza a választ megadott gyorsítótár-vezérlő konfigurációjának megfelelően.  
  
    -   [Lehet értéket kiolvasni a gyorsítótár](api-management-caching-policies.md#GetFromCacheByKey) -kulcs által gyorsítótárazott elem beolvasása.  
  
    -   [A gyorsítótárban tárolja a érték](api-management-caching-policies.md#StoreToCacheByKey) -tárolja egy elemet a gyorsítótár gombot.  
  
    -   [Távolítsa el az értéket a gyorsítótárból](api-management-caching-policies.md#RemoveCacheByKey) -a gyorsítótárban, kulcs által távolítani egy elemet.  
  
-   [Tartományközi házirendek](api-management-cross-domain-policies.md#CrossDomainPolicies)  
  
    -   [Lehetővé teszi a tartományok közötti hívások](api-management-cross-domain-policies.md#AllowCrossDomainCalls) -elérhetővé válnak az API-t az Adobe Flash és a Microsoft Silverlight webböngésző-alapú ügyfelektől.  
  
    -   [A CORS](api-management-cross-domain-policies.md#CORS) -hozzáadja az eltérő eredetű erőforrások megosztása (CORS) a támogatási művelet vagy API lehetővé teszi a tartományok közötti hívások webböngésző-alapú ügyfelekről.  
  
    -   [JSONP](api-management-cross-domain-policies.md#JSONP) -JSON ad hozzá egy műveletet, vagy lehetővé teszi a tartományok közötti hívások JavaScript-ügyfelekből webböngésző-alapú API kitöltési (JSONP) támogatással.  
  
-   [Átalakítási házirendek](api-management-transformation-policies.md#TransformationPolicies)  
  
    -   [JSON átalakítása XML](api-management-transformation-policies.md#ConvertJSONtoXML) - konvertálja kérés vagy válasz body JSON formátumból az XML.  
  
    -   [XML konvertálása JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) - konvertálja kérés vagy válasz body az XML-JSON.  
  
    -   [Keresés és csere törzsében karakterlánc](api-management-transformation-policies.md#Findandreplacestringinbody) - kérés vagy válasz karakterláncrész keresése, és lecseréli egy másik karakterláncrészletet.  
  
    -   [URL-címek maszkolja a tartalom](api-management-transformation-policies.md#MaskURLSContent) -hivatkozások átírja (maszkok) a válaszban szereplő body, hogy azok az átjárón keresztül a megfelelő hivatkozásra mutat.  
  
    -   [Állítsa be háttérszolgáltatás](api-management-transformation-policies.md#SetBackendService) -módosítja a háttérszolgáltatáshoz egy bejövő kérelemhez.  
  
    -   [Állítsa be a szervezet](api-management-transformation-policies.md#SetBody) -beállítja az üzenettörzs, a bejövő és kimenő kérelmek.  
  
    -   [Set HTTP-fejléc](api-management-transformation-policies.md#SetHTTPheader) - hozzárendel egy értéket egy meglévő válasz és/vagy a kérelem fejlécében vagy ad hozzá egy új válasz és/vagy a kérelem fejlécében.  
  
    -   [Állítsa be a lekérdezési karakterlánc paraméter](api-management-transformation-policies.md#SetQueryStringParameter) - ad hozzá, értéke váltja fel, vagy törli a kérelem lekérdezési karakterláncot.  
  
    -   [URL-cím újraírása](api-management-transformation-policies.md#RewriteURL) – a kérelem URL-címe nyilvános formájukban a várt érték a webszolgáltatás által az űrlap alakítja.  
  
    -   [Átalakítás XSLT használatával XML](api-management-transformation-policies.md#XSLTransform) -XSL-átalakítás alkalmazása XML-kérés vagy válasz törzsében.  
  
## <a name="next-steps"></a>Következő lépések
Házirendek használata további információkért lásd: [házirendek az API Management](api-management-howto-policies.md).  
