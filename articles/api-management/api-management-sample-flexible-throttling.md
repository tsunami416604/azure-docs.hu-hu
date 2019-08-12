---
title: Speciális kérelmek szabályozása az Azure API Management
description: Ismerje meg, hogyan hozhat létre és alkalmazhat rugalmas kvótát és díjszabást korlátozó házirendeket az Azure API Management használatával.
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
ms.openlocfilehash: f0f89275006498c0ff4883f259d7e76f723b922b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68851324"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Speciális kérelmek szabályozása az Azure API Management
A bejövő kérések szabályozása az Azure API Management kulcsfontosságú szerepe. A kérések sebességének vagy az átvitt kérelmeknek/adatoknak a szabályozásával API Management lehetővé teszi az API-szolgáltatók számára, hogy az API-kat felhasználják a visszaéléstől, és értéket hozzanak létre különböző API-termékekhez.

## <a name="product-based-throttling"></a>Termék-alapú szabályozás
Az eddigi díjszabási képességeket a rendszer a Azure Portalban definiált adott termék-előfizetésre korlátozta. Ez akkor hasznos, ha az API-szolgáltató korlátozásokat alkalmaz az API-t használó fejlesztőknek, azonban nem segít például az API egyes végfelhasználóinak szabályozásában. Lehetséges, hogy a fejlesztői alkalmazás egyetlen felhasználója használja fel a teljes kvótát, majd megakadályozza, hogy a fejlesztő más ügyfelei is használhassák az alkalmazást. Emellett a nagy mennyiségű kérést előidéző ügyfelek is korlátozhatják az alkalmi felhasználók hozzáférését.

## <a name="custom-key-based-throttling"></a>Egyéni kulcs alapú szabályozás

> MEGJEGYZÉS: A `rate-limit-by-key` és`quota-by-key` a házirendek nem érhetők el, ha az Azure API Management felhasználási szintjében van. 

Az új [díjszabás – a korlátozási kulcsok](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) és a [kvóta-](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) ellenőrzési házirendek rugalmasabb megoldást biztosítanak a forgalom szabályozására. Ezek az új házirendek lehetővé teszik kifejezések meghatározását a forgalom nyomon követéséhez használt kulcsok azonosításához. Ennek a működésnek a példája a legkönnyebb illusztrálva. 

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

Ha az interneten lévő összes ügyfél egyedi IP-címet használt, akkor ez a felhasználó általi használat korlátozásának hatékony módja lehet. Azonban valószínű, hogy több felhasználó oszt meg egyetlen nyilvános IP-címet, mert az Internet NAT-eszközön keresztül fér hozzá az internethez. Ennek ellenére a nem hitelesített hozzáférést `IpAddress` engedélyező API-k esetében a legjobb megoldás lehet.

## <a name="user-identity-throttling"></a>Felhasználói identitás szabályozása
Ha egy végfelhasználó hitelesítése megtörtént, a rendszer egy szabályozási kulcsot generálhat olyan információk alapján, amelyek egyedileg azonosítják az adott felhasználót.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Ez a példa azt mutatja be, hogyan lehet kibontani az `JWT` engedélyezési fejlécet, alakítsa át az objektumra, és a token tárgya alapján azonosítsa a felhasználót, és használja az értéket a kulcs korlátozására. Ha a felhasználói identitást a más jogcímek egyikében tárolják `JWT` , akkor ez az érték a helyén is felhasználható.

## <a name="combined-policies"></a>Kombinált házirendek
Bár az új szabályozási szabályzatok nagyobb szabályozást biztosítanak, mint a meglévő szabályozási szabályzatok, a két funkció együttes használata továbbra is fennáll. A termék előfizetési kulcsának szabályozása (a hívások számának[korlátozása előfizetés alapján](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) , [használati kvóta beállítása](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)előfizetéssel) nagyszerű lehetőség arra, hogy a használati szintek alapján megszámolja az API-k bevételeit. A felhasználó általi szabályozáshoz szükséges finomabb szabályozás kiegészíthető, és meggátolja, hogy az egyik felhasználó viselkedése ne legyen egy másik felhasználói felületének romlása. 

## <a name="client-driven-throttling"></a>Ügyfél-vezérelt szabályozás
Ha a szabályozási kulcs egy [házirend](/azure/api-management/api-management-policy-expressions)-kifejezéssel van definiálva, akkor ez az API-szolgáltató, amely kiválasztja a szabályozás hatókörét. A fejlesztőknek azonban érdemes megszabni, hogyan korlátozzák a saját ügyfeleiket. Ezt az API-szolgáltató engedélyezheti egy egyéni fejléc bevezetésével, amely lehetővé teszi a fejlesztői ügyfélalkalmazás számára, hogy az API-val kommunikáljon a kulccsal.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Ez lehetővé teszi a fejlesztői ügyfélalkalmazás számára, hogy kiválassza, hogyan szeretné létrehozni a ráta-korlátozó kulcsot. Az ügyfél fejlesztői létrehozhatják a saját díjszabási szintjeiket a kulcsok készletének a felhasználók számára való kiosztásával és a kulcshasználat elforgatásával.

## <a name="summary"></a>Összegzés
Az Azure API Management díjszabást és árajánlatot biztosít a védelemhez, és értéket adhat hozzá az API-szolgáltatáshoz. Az új szabályozási szabályzatok egyéni hatóköri szabályokkal lehetővé teszik, hogy az ügyfelek még jobb alkalmazásokat hozzanak létre. A jelen cikkben szereplő példák a kulcsok az ügyfél IP-címeivel, a felhasználói identitással és az ügyfél által generált értékekkel való korlátozásával mutatják be az új szabályzatok használatát. Azonban az üzenet számos más része használható, például a felhasználói ügynök, az URL-cím elérési útja, az üzenet mérete.

## <a name="next-steps"></a>További lépések
Küldjön visszajelzést a témakör Disqus-száláról. Érdemes meghallgatni azokat a lehetséges kulcsfontosságú értékeket, amelyek a forgatókönyvekben logikus választásnak bizonyultak.

