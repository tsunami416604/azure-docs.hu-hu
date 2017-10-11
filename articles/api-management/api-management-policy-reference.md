---
title: "Az Azure API Management szabályzatainak ismertetése"
description: "Ismerje meg az API Management konfigurálható házirendeket."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 9d4ac609-b221-4032-93ae-e27a1254d43d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: adc0c4415e10ddd0b4994cecef17f026546e91a1
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="azure-api-management-policy-reference"></a>Az Azure API Management szabályzatainak ismertetése
Ez a témakör az index a házirendek a [API-kezelési házirendjeihez][API Management policy reference]. Hozzáadása és házirendek konfigurálásával kapcsolatos tudnivalókat lásd: [házirendek az API Management][Policies in API Management].

A házirend-kifejezéseket attribútumértékekként vagy szövegértékekként lehet használni bármelyik API Management házirendben, hacsak a házirend másként nem rendelkezik. Egyes házirendek, például a [folyamatot szabályozhatja] [ Control flow] és [Set változó] [ Set variable] házirendek a házirend-kifejezések alapulnak. További információkért lásd: [házirendek speciális] [ Advanced policies] és [házirend-kifejezések][Policy expressions]

## <a name="policy-reference-index"></a>Szabályzati segédlet indexe
* [Hozzáférés a szoftverkorlátozó házirendek][Access restriction policies]
  * [Ellenőrizze a HTTP-fejléc] [ Check HTTP header] -érvénybe lépteti a létezését és/vagy a HTTP-fejléc értékét.
  * [Előfizetési határértéket hívás arányt a] [ Limit call rate by subscription] -megakadályozza, hogy API-használati napra hívás arány / előfizetés alapon korlátozásával.
  * [Korlát hívás arányt a kulcs](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) -megakadályozza, hogy API-használati napra hívás arány / kulcs alapon korlátozásával.
  * [A hívó IP-címek korlátozása] [ Restrict caller IPs] -szűrők (engedélyezi vagy megtagadja) hívást bizonyos IP-címeket és/vagy címtartományokat.
  * [Set memóriahasználati kvóta előfizetéssel] [ Set usage quota by subscription] -lehetővé teszi egy megújítható vagy élettartama hívás mennyiségi és/vagy a sávszélesség kvótát, egy előfizetés alapon érvényesítését.
  * [Set memóriahasználati kvóta kulcs által](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) -lehetővé teszi egy megújítható vagy élettartama hívás mennyiségi és/vagy a sávszélesség kvótát, egy kulcs alapon érvényesítését.
  * [Ellenőrizze a JWT] [ Validate JWT] -érvénybe lépteti a létezését és a jwt-t vagy a megadott HTTP-fejléc, vagy a megadott lekérdezési paraméter kinyert érvényességét.
* [Speciális házirendek][Advanced policies]
  * [Folyamat szabályozása] [ Control flow] - feltételesen alkalmazza a házirend-utasításoknál logikai értékelése eredményei alapján [kifejezések][expressions].
  * [Kérés továbbítása a] [ Forward request] -továbbítja a kérést a háttérszolgáltatáshoz.
  * [Az Event Hubs napló] [ Log to Event Hub] -üzeneteket küld egy üzenetcél határozzák meg a megadott formátumban egy [naplózó](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger) entitás.
  * [Próbálja meg újra](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) -újrapróbálkozások zárt házirend utasítás végrehajtása, ha, és amíg a feltétel nem teljesül. Végrehajtási ismételje meg a megadott időközönként, és a legfeljebb a megadott újrapróbálkozások száma.
  * [Térjen vissza a válasz](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) -megszakításainak-feldolgozási folyamat végrehajtása és a közvetlenül a hívó adott választ.
  * [Egyirányú kérés küldése](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) -kérést küld a megadott URL-cím a válaszra való várakozás nélkül.
  * [Kérés küldése](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) -kérést küld a megadott URL-cím.
  * [Kérelem módszert állítja be](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) -módosíthatja a kérelem HTTP-metódust.
  * [Állapot beállítása](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) – a HTTP-állapotkód: a megadott értékre változik.
  * [Új érték] [ Set variable] -értéket az elnevezett megőrzéséhez [környezetben] [ context] változó későbbi eléréshez.
  * [Nyomkövetési](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) -be egy karakterláncot ad a [API Inspector](api-management-howto-api-inspector.md) kimeneti.
  * [Várjon, amíg](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) -vár a zárt küldési kérelem a Get érték a gyorsítótárból, vagy vezérlési folyamata házirendek befejeződjön, mielőtt továbblép.
* [Hitelesítési házirendek][Authentication policies]
  * [Basic hitelesítés] [ Authenticate with Basic] -alapszintű hitelesítést használó háttérszolgáltatás a hitelesítést.
  * [Az ügyféltanúsítvány hitelesítéséhez] [ Authenticate with client certificate] -az ügyféltanúsítványok háttérszolgáltatás a hitelesítést.
* [Házirendek gyorsítótárazása][Caching policies] 
  * [Gyorsítótár beszerezni] [ Get from cache] -hajtsa végre a gyorsítótár kereshet, és térjen vissza egy érvényes gyorsítótárazott választ, ha elérhető.
  * [Gyorsítótárazandó tároló] [ Store to cache] -gyorsítótárazza a választ megadott gyorsítótár-vezérlő konfigurációjának megfelelően.
  * [Lehet értéket kiolvasni a gyorsítótár](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) -kulcs által gyorsítótárazott elem beolvasása.
  * [A gyorsítótárban tárolja a érték](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) -tárolja egy elemet a gyorsítótár gombot.
  * [Távolítsa el az értéket a gyorsítótárból](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) -a gyorsítótárban, kulcs által távolítani egy elemet.
* [Kereszt-tartományi házirendek][Cross domain policies] 
  * [Lehetővé teszi a tartományok közötti hívások] [ Allow cross-domain calls] -elérhetővé válnak az API-t az Adobe Flash és a Microsoft Silverlight webböngésző-alapú ügyfelektől.
  * [A CORS] [ CORS] -hozzáadja az eltérő eredetű erőforrások megosztása (CORS) a támogatási művelet vagy API lehetővé teszi a tartományok közötti hívások webböngésző-alapú ügyfelekről.
  * [JSONP] [ JSONP] -JSON ad hozzá egy műveletet, vagy lehetővé teszi a tartományok közötti hívások JavaScript-ügyfelekből webböngésző-alapú API kitöltési (JSONP) támogatással.
* [Átalakítási csoportházirendek][Transformation policies] 
  * [JSON átalakítása XML] [ Convert JSON to XML] - konvertálja kérés vagy válasz body JSON formátumból az XML.
  * [XML konvertálása JSON] [ Convert XML to JSON] - konvertálja kérés vagy válasz body az XML-JSON.
  * [Keresés és csere törzsében karakterlánc] [ Find and replace string in body] - kérés vagy válasz karakterláncrész keresése, és lecseréli egy másik karakterláncrészletet.
  * [URL-címek maszkolja a tartalom] [ Mask URLs in content] -hivatkozások átírja (maszkok) a válaszban szereplő body, hogy azok az átjárón keresztül a megfelelő hivatkozásra mutat.
  * [Állítsa be háttérszolgáltatás] [ Set backend service] -módosítja a háttérszolgáltatáshoz egy bejövő kérelemhez.
  * [Állítsa be a szervezet] [ Set body] -beállítja az üzenettörzs, a bejövő és kimenő kérelmek.
  * [Set HTTP-fejléc] [ Set HTTP header] - hozzárendel egy értéket egy meglévő válasz és/vagy a kérelem fejlécében vagy ad hozzá egy új válasz és/vagy a kérelem fejlécében.
  * [Állítsa be a lekérdezési karakterlánc paraméter] [ Set query string parameter] - ad hozzá, értéke váltja fel, vagy törli a kérelem lekérdezési karakterláncot.
  * [URL-cím újraírása] [ Rewrite URL] – a kérelem URL-címe nyilvános formájukban a várt érték a webszolgáltatás által az űrlap alakítja.

## <a name="next-steps"></a>Következő lépések
Házirend-kifejezések további információkért tekintse meg a következő videó.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Access restriction policies]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Check HTTP header]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Limit call rate by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restrict caller IPs]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Set usage quota by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validate JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[context]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Forward request]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Log to Event Hub]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Authentication policies]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Authenticate with Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Authenticate with client certificate]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Get from cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Store to cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Cross domain policies]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Allow cross-domain calls]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Transformation policies]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Convert JSON to XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Convert XML to JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Find and replace string in body]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Mask URLs in content]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Set backend service]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Set body]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Set HTTP header]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Set query string parameter]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Rewrite URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Policies in API Management]: api-management-howto-policies.md
[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx


