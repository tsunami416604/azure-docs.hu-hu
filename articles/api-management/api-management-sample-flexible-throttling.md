---
title: "Az Azure API Management-szabályozás Speciális kérelem"
description: "Megtudhatja, hogyan hozhat létre és alkalmazhat a rugalmas kvóta és korlátozza az Azure API-felügyeleti szabályzatok alapján."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 427660be92d3caf4c381cec65f49adce9808e50a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2018
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Az Azure API Management-szabályozás Speciális kérelem
Az Azure API Management kulcsfontosságú szerepet igényt bejövő kérelmek szabályozás. Lehet, hogy a kéréseket, és a teljes kérelmek/átvitt adatok mértékű vezérlése, API-kezelés lehetővé teszi a API-szolgáltatókat az API-való visszaélés elleni védelmében, és hozzon létre külön API termék rétegekhez értékét.

## <a name="product-based-throttling"></a>A termék alapú sávszélesség-szabályozás
Naprakész, a sebesség szabályozása képességekkel rendelkezik volt korlátozva, éppen hatókörű egy adott termék-előfizetéshez (lényegében egy kulcs), az Azure portálon meghatározott. Ez akkor hasznos, ha az API-szolgáltató szűkítheti a regisztráltak-e az API használatához a fejlesztőknek, azonban nem segítséget, például a szabályozás, az API-t egyéni végfelhasználók. Előfordulhat, hogy az egyetlen teljes kvóta fogadni, és más ügyfelek a fejlesztő megakadályozza az alkalmazás használata a fejlesztői alkalmazás felhasználója. Nagy mennyiségű kérést hozhat létre több felhasználókat is, korlátozhatja az alkalmi felhasználók hozzáférésének.

## <a name="custom-key-based-throttling"></a>Egyéni kulcs alapú sávszélesség-szabályozás
Az új [arány-korlát-által-kulcs](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) és [a kulcs-kvóta](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) házirendek a forgalmi ellenőrzési jóval rugalmasabb megoldást nyújt. Ezeket az új házirendeket engedélyezi annak meghatározását-kifejezések helyesen azonosítani a kulcsokhoz, a forgalom használat nyomon követésére használható. Ez a megfelelő működésének easiest látható egy példa a. 

## <a name="ip-address-throttling"></a>IP-cím sávszélesség-szabályozás
A következő házirendek korlátozása egyetlen ügyfél IP-címnek hívások csak 10 percenként 1 000 000 hívások összesen és 10 000 kilobájt havonta sávszélesség. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Ha az interneten lévő összes ügyfél egy egyedi IP-címet használ, ez lehet hatékony módszer a felhasználó által használat korlátozása. Azonban ez nagyon valószínű, hogy több felhasználó fog-e a megosztás azokat az interneten keresztül egy NAT-eszköz használata miatt egyetlen nyilvános IP-címet. Annak ellenére, hogy az API-k esetében, amelyek lehetővé teszik a nem hitelesített hozzáférést a `IpAddress` előfordulhat, hogy a legjobb lehetőség.

## <a name="user-identity-throttling"></a>Felhasználói azonosító sávszélesség-szabályozás
Ha a felhasználó hitelesítése, akkor a sávszélesség-szabályozási kulcs hozható létre, amely egyedileg azonosít egy, amely adatok alapján felhasználó.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Ebben a példában azt az Authorization fejlécet kiolvasni, alakítsa át a következőre `JWT` objektumra, és a tulajdonos a jogkivonat segítségével azonosíthatja a felhasználót, és használja, amely a sebesség korlátozása kulcs. Ha a felhasználói azonosító található a `JWT` , egy másik jogcímek majd, hogy érték helyére lesznek használva.

## <a name="combined-policies"></a>Kombinált házirendek
Az új sávszélesség-szabályozási házirendek biztosítják nagyobb mértékben vezérelheti, mint a meglévő szabályozó házirendeket, bár nincs továbbra is érték kombinálásával mindkét szolgáltatást. Termékkulcs előfizetés általi szabályozás ([előfizetési határértéket hívás arányt a](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) és [Set memóriahasználati kvóta előfizetéssel](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)) engedélyezéséhez a úgy használati szintek alapján, az API-k monetizing remek módja. Az eszközzel kombinálva felhasználóbarát nyomtatott irányítását képesek a felhasználó által szabályozás kiegészíti, és megakadályozza, hogy egy felhasználó viselkedését egy másik élmény terhelése. 

## <a name="client-driven-throttling"></a>Ügyfél-alapú sávszélesség-szabályozás
Ha a sávszélesség-szabályozási kulcs használatával van megadva egy [házirend-kifejezést](https://msdn.microsoft.com/library/azure/dn910913.aspx), akkor célszerű az API-szolgáltatót, amelyet a sávszélesség-szabályozás hatóköre hogyan van választva. Azonban a fejlesztő előfordulhat, hogy kívánják vezérelni hogyan azok értékelje korlát a saját ügyfelek. Ez sikerült engedélyezni az API-szolgáltató egy egyéni fejlécet bevezetésével ahhoz, hogy a fejlesztő ügyfél alkalmazás való kommunikációhoz az API-t a kulcsot.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Ez lehetővé teszi a fejlesztői ügyfélalkalmazás számára adja meg, hogy azok a sebesség korlátozása a kulcs létrehozásához. Egy kis nyújt az ügyfél a fejlesztők kulcsok készleteinek kiosztása a felhasználók számára, és a kulcshasználati elforgatása létrehozhat saját rétegek.

## <a name="summary"></a>Összegzés
Az Azure API Management sebessége és ajánlat használatával történő védelméhez és értékkel adja hozzá az API-szolgáltatás is biztosít. Az egyéni szabályának új sávszélesség-szabályozási szabályzatok lehetővé teszik eszközzel kombinálva felhasználóbarát nyomtatott szabályozhatják, ezek a házirendek ahhoz, hogy az ügyfelek még élvezetesebbé alkalmazásokat hozhatnak létre. Ebben a cikkben szereplő példák bemutatják, ezeket az új házirendeket használata korlátozza az ügyfél IP-címeket, a felhasználói azonosító és a generált ügyfél értékek kulcsok gyártó szerint. Van azonban az üzenetet, például a felhasználói ügynök, az URL-cím elérési út töredék, az üzenet mérete használható sok többi részével.

## <a name="next-steps"></a>További lépések
Adja meg a visszajelzést a disqus-beszélgetésben teheti szál az ebben a témakörben. Más lehetséges értékek, amelyek egy logikai választás a forgatókönyvekben lettek szóló értesítésekre nagyszerű lenne.

## <a name="watch-a-video-overview-of-these-policies"></a>A házirendek áttekintő videó megtekintése
További információ a [arány-korlát-által-kulcs](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) és [a kulcs-kvóta](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) a cikkben szereplő házirendek adja a következő videó megtekintése.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Advanced-Request-Throttling-with-Azure-API-Management/player]
> 
> 

