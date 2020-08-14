---
title: Speciális kérelemszabályozás az Azure API Managementtel
description: Ismerje meg, hogyan hozhat létre és alkalmazhat rugalmas kvótát és díjszabást korlátozó házirendeket az Azure API Management használatával.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 7ef1c09b12d3c7e365f090391aa3fa8afa03749b
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214004"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Speciális kérelemszabályozás az Azure API Managementtel
A bejövő kérések szabályozása az Azure API Management kulcsfontosságú szerepe. A kérések sebességének vagy az átvitt kérelmeknek/adatoknak a szabályozásával API Management lehetővé teszi az API-szolgáltatók számára, hogy az API-kat felhasználják a visszaéléstől, és értéket hozzanak létre különböző API-termékekhez.

## <a name="product-based-throttling"></a>Termék-alapú szabályozás
Az eddigi díjszabási képességeket a rendszer a Azure Portalban definiált adott termék-előfizetésre korlátozta. Ez akkor hasznos, ha az API-szolgáltató korlátozásokat alkalmaz az API-t használó fejlesztőknek, azonban nem segít például az API egyes végfelhasználóinak szabályozásában. Lehetséges, hogy a fejlesztői alkalmazás egyetlen felhasználója használja fel a teljes kvótát, majd megakadályozza, hogy a fejlesztő más ügyfelei is használhassák az alkalmazást. Emellett a nagy mennyiségű kérést előidéző ügyfelek is korlátozhatják az alkalmi felhasználók hozzáférését.

## <a name="custom-key-based-throttling"></a>Egyéni kulcs alapú szabályozás

> [!NOTE]
> A `rate-limit-by-key` és a `quota-by-key` házirendek nem érhetők el, ha az Azure API Management felhasználási szintjében van. 

Az új [díjszabás – a korlátozási kulcsok](./api-management-access-restriction-policies.md#LimitCallRateByKey) és a [kvóta-](./api-management-access-restriction-policies.md#SetUsageQuotaByKey) ellenőrzési házirendek rugalmasabb megoldást biztosítanak a forgalom szabályozására. Ezek az új házirendek lehetővé teszik kifejezések meghatározását a forgalom nyomon követéséhez használt kulcsok azonosításához. Ennek a működésnek a példája a legkönnyebb illusztrálva. 

## <a name="ip-address-throttling"></a>IP-cím szabályozása
Az alábbi házirendek egyetlen ügyfél IP-címét korlátozzák percenként 10 hívásra, összesen 1 000 000 hívást és havi 10 000 kilobájt sávszélességet. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Ha az interneten lévő összes ügyfél egyedi IP-címet használt, akkor ez a felhasználó általi használat korlátozásának hatékony módja lehet. Azonban valószínű, hogy több felhasználó oszt meg egyetlen nyilvános IP-címet, mert az Internet NAT-eszközön keresztül fér hozzá az internethez. Ennek ellenére a nem hitelesített hozzáférést engedélyező API-k esetében a `IpAddress` legjobb megoldás lehet.

## <a name="user-identity-throttling"></a>Felhasználói identitás szabályozása
Ha egy végfelhasználó hitelesítése megtörtént, a rendszer egy szabályozási kulcsot generálhat olyan információk alapján, amelyek egyedileg azonosítják az adott felhasználót.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Ez a példa azt mutatja be, hogyan lehet kibontani az engedélyezési fejlécet, alakítsa át az `JWT` objektumra, és a token tárgya alapján azonosítsa a felhasználót, és használja az értéket a kulcs korlátozására. Ha a felhasználói identitást a `JWT` más jogcímek egyikében tárolják, akkor ez az érték a helyén is felhasználható.

## <a name="combined-policies"></a>Kombinált házirendek
Bár az új szabályozási szabályzatok nagyobb szabályozást biztosítanak, mint a meglévő szabályozási szabályzatok, a két funkció együttes használata továbbra is fennáll. A termék előfizetési kulcsának szabályozása (a hívások számának[korlátozása előfizetés alapján](./api-management-access-restriction-policies.md#LimitCallRate) , [használati kvóta beállítása előfizetéssel](./api-management-access-restriction-policies.md#SetUsageQuota)) nagyszerű lehetőség arra, hogy a használati szintek alapján megszámolja az API-k bevételeit. A felhasználó általi szabályozáshoz szükséges finomabb szabályozás kiegészíthető, és meggátolja, hogy az egyik felhasználó viselkedése ne legyen egy másik felhasználói felületének romlása. 

## <a name="client-driven-throttling"></a>Ügyfél-vezérelt szabályozás
Ha a szabályozási kulcs egy [házirend-kifejezéssel](./api-management-policy-expressions.md)van definiálva, akkor ez az API-szolgáltató, amely kiválasztja a szabályozás hatókörét. A fejlesztőknek azonban érdemes megszabni, hogyan korlátozzák a saját ügyfeleiket. Ezt az API-szolgáltató engedélyezheti egy egyéni fejléc bevezetésével, amely lehetővé teszi a fejlesztői ügyfélalkalmazás számára, hogy az API-val kommunikáljon a kulccsal.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Ez lehetővé teszi a fejlesztői ügyfélalkalmazás számára, hogy kiválassza, hogyan szeretné létrehozni a ráta-korlátozó kulcsot. Az ügyfél fejlesztői létrehozhatják a saját díjszabási szintjeiket a kulcsok készletének a felhasználók számára való kiosztásával és a kulcshasználat elforgatásával.

## <a name="summary"></a>Összefoglalás
Az Azure API Management díjszabást és árajánlatot biztosít a védelemhez, és értéket adhat hozzá az API-szolgáltatáshoz. Az új szabályozási szabályzatok egyéni hatóköri szabályokkal lehetővé teszik, hogy az ügyfelek még jobb alkalmazásokat hozzanak létre. A jelen cikkben szereplő példák a kulcsok az ügyfél IP-címeivel, a felhasználói identitással és az ügyfél által generált értékekkel való korlátozásával mutatják be az új szabályzatok használatát. Azonban az üzenet számos más része használható, például a felhasználói ügynök, az URL-cím elérési útja, az üzenet mérete.

## <a name="next-steps"></a>Következő lépések
Küldje el nekünk visszajelzését GitHub-problémaként ebben a témakörben. Érdemes meghallgatni azokat a lehetséges kulcsfontosságú értékeket, amelyek a forgatókönyvekben logikus választásnak bizonyultak.
