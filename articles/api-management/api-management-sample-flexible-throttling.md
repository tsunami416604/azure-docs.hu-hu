---
title: Speciális kérésszabályzás az Azure API Management szolgáltatással
description: Ismerje meg, hogyan hozhat létre és rugalmas kvótát és a sebesség korlátozása az Azure API Management házirendek vonatkoznak.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 22c3987121e2ab3479274c89c359c679f5f1135e
ms.sourcegitcommit: ad3e63af10cd2b24bf4ebb9cc630b998290af467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58793356"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Speciális kérésszabályzás az Azure API Management szolgáltatással
Az Azure API Management szerepet képes arra, hogy a bejövő kérelmek szabályozása. Vagy a kérések vagy a teljes kérések/átvitt adatok arányát, az API Management lehetővé teszi a API-szolgáltatókkal való visszaélés saját API-k védelme, és hozzon létre az API-termék különböző rétegek értéket.

## <a name="product-based-throttling"></a>A termék-alapú szabályozása
A dátum, a sebesség memóriaszabályozási képességekkel rendelkezik folyamatban van egy adott termék-előfizetés, az Azure Portalon meghatározott hatóköre korlátozva lett. Ez hasznos, ha a alkalmazni a korlátok a fejlesztők az API használatára regisztrált API-szolgáltató, azonban ez nem segít, például az egyes végfelhasználók számára az API-szabályozás. Előfordulhat, hogy az a teljes kvóta fogadni, és a többi ügyfél a fejlesztő megakadályozása képes használni az alkalmazást a fejlesztői alkalmazás felhasználói egyetlen. Ezenkívül számos olyan ügyfelek, akik nagy mennyiségű kérelmet generálhat, előfordulhat, hogy férjenek hozzá az alkalmi felhasználók.

## <a name="custom-key-based-throttling"></a>Egyéni kulcs alapján szabályozása
Az új [arány-limit-by-key](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) és [kvóta-by-key](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) Forgalomvezérlés rugalmasabb megoldást nyújtanak a szabályzatokat. Ezek a házirendek lehetővé teszik azonosíthatja a kulcsok forgalom használat nyomon követésére használt kifejezések meghatározása. Ez a megfelelő működésének easiest mutatja be egy példával szemlélteti. 

## <a name="ip-address-throttling"></a>IP-cím-szabályozás
A következő házirendek korlátozzák egyetlen ügyfél IP-cím csak 10 hívás percenként összesen 1 000 000 hívást és 10 000 kilobájt / hó sávszélesség. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Ha az interneten lévő ügyfelek összes egyedi IP-címet használ, ez lehet hatékony módszer a felhasználó által a használat korlátozása. Azonban valószínű, hogy több felhasználó is használja őket a NAT-eszköz használatával az Internet elérése miatt egyetlen nyilvános IP-címet. Annak ellenére, hogy ez az API-k, amelyek lehetővé teszik a nem hitelesített hozzáférést a `IpAddress` lehet, hogy a legjobb lehetőség.

## <a name="user-identity-throttling"></a>Felhasználói identitás szabályozása
Ha a felhasználó hitelesítése, majd a sávszélesség-szabályozási kulcs létrehozható, amely egyedileg azonosítja az adott felhasználói adatok alapján.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Ez a példa bemutatja, hogyan bontsa ki az engedélyezési fejléc, alakítsa át a következőre `JWT` objektumra, és azonosítani a felhasználót, és a sebesség korlátozása a kulcsot használja, amely segítségével a jogkivonat tárgyában. Ha a felhasználó identitásának tárolja a `JWT` egyéb jogcímek az egyik, majd ezt az értéket használhatja helyette.

## <a name="combined-policies"></a>Kombinált házirendek
Az új sávszélesség-szabályozási házirendek biztosítja, mint a meglévő szabályozási házirendek vezérlése, van még mindkét funkciók kombinálásával értéket. Termékkulcs-előfizetés által szabályozás ([előfizetés által a hívások sebességének korlátozása](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) és [előfizetés használati kvóta beállítása](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)) kiválóan alkalmas API-használati szintjei alapján díjszabási által ismertségre engedélyezéséhez. Ennél részletes vezérlése képes arra, hogy a felhasználó által szabályozás kiegészíti a, és a egy felhasználói viselkedés megakadályozza, hogy a felhasználói élményt, egy másik gyorsítótárazhatók. 

## <a name="client-driven-throttling"></a>Ügyfél-driven szabályozása
Ha a sávszélesség-szabályozási kulcs használatával egy [házirend kifejezés](/azure/api-management/api-management-policy-expressions), akkor az API-szolgáltatót, amelyet a megválasztása a módját a szabályozás hatókörét. Azonban a fejlesztő vezérelheti, hogyan azok Sebességkorlát saját érdemes ügyfelek. Ez sikerült engedélyezni az API-szolgáltató által bevezetésével egy egyéni fejlécet, hogy a fejlesztő ügyfélalkalmazás való kommunikációhoz a kulcsot az API-hoz.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Ez lehetővé teszi a fejlesztői ügyfélalkalmazás adja meg, hogyan azokat a sebesség korlátozása a kulcs létrehozásához. Az ügyfél létrehozhatnak saját rétegek kulcsok részhalmazához kiosztása a felhasználók számára, és a kulcshasználat elforgatása.

## <a name="summary"></a>Összegzés
Az Azure API Management biztosít arányát és a sávszélesség-szabályozási is védeni, és adjon meg értéket, az API-szolgáltatás ajánlat. Az új sávszélesség-szabályozási házirendek hatókörkezelési egyéni szabályok lehetővé teszik ennél ügyfelei számára, hogy még jobb alkalmazások készítése ahhoz, hogy ezek a házirendek részletes felett. Ebben a cikkben szereplő példák bemutatják, ezeket az új házirendeket használata korlátozza az ügyfél IP-címeket, a felhasználói identitás és a generált ügyfél értékek kulcsok gyártási átviteli sebesség. Vannak azonban az üzenetet, például a felhasználói ügynök, a URL-cím elérési út töredék, az üzenet mérete a használható számos egyéb részei.

## <a name="next-steps"></a>További lépések
Szívesen fogadjuk visszajelzését a disqus-beszélgetésben teheti szál ebben a témakörben. Örülünk, kapcsolatos egyéb esetleges kulcsérték, amelyeket egy is logikus választás az Ön forgatókönyvei lenne.

