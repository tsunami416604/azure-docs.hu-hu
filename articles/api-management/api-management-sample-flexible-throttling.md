---
title: Speciális kérelmek szabályozása az Azure API Management szolgáltatással
description: Ismerje meg, hogyan hozhat létre és alkalmazhat rugalmas kvóta- és sebességkorlátozó szabályzatokat az Azure API Management segítségével.
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
ms.openlocfilehash: 467d9cee74567fc0d19031773415675ae7c51818
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71066753"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Speciális kérelmek szabályozása az Azure API Management szolgáltatással
A bejövő kérelmek szabályozása az Azure API Management kulcsfontosságú szerepet játszik. Az API Management lehetővé teszi az API-szolgáltatók számára, hogy megvédjék API-kat a visszaélésekkel szemben, és értéket teremtsenek a különböző API-termékszintekhez a kérelmek sebességének szabályozásával, vagy az összes továbbított kérelem/adat átvitelével.

## <a name="product-based-throttling"></a>Termékalapú szabályozás
A mai napig a sebesség szabályozási képességek csak hatóköre egy adott termék-előfizetés, az Azure Portalon meghatározott. Ez akkor hasznos, ha az API-szolgáltató korlátozásokat alkalmaz a fejlesztők, akik feliratkoztak az API-t használni, azonban ez nem segít, például az API egyes végfelhasználóinak szabályozása. Lehetséges, hogy a fejlesztői alkalmazás egyetlen felhasználója a teljes kvótát felhasználja, majd megakadályozza, hogy a fejlesztő más ügyfelei is használhassák az alkalmazást. Emellett több olyan ügyfél is korlátozhatja a hozzáférést az alkalmi felhasználók számára, akik nagy mennyiségű kérelmet generálhatnak.

## <a name="custom-key-based-throttling"></a>Egyéni kulcsalapú szabályozás

> [!NOTE]
> A `rate-limit-by-key` `quota-by-key` szabályzatok és szabályzatok nem érhetők el, ha az Azure API Management felhasználási rétegében. 

Az új [kulcs-kulcs](/azure/api-management/api-management-access-restriction-policies#LimitCallRateByKey) és [kvóta-by-key](/azure/api-management/api-management-access-restriction-policies#SetUsageQuotaByKey) politikák rugalmasabb megoldást nyújtanak a forgalomirányításra. Ezek az új házirendek lehetővé teszik, hogy kifejezéseket definiáljon a forgalom használatának nyomon követésére használt kulcsok azonosítására. Ennek a módja a legegyszerűbb illusztrált egy példával. 

## <a name="ip-address-throttling"></a>IP-cím szabályozása
A következő házirendek egyetlen ügyfél IP-címét percenként csak 10 hívásra korlátozzák, összesen 1 000 000 hívással és 10 000 kilobájt sávszélességgel havonta. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Ha az interneten minden ügyfél egyedi IP-címet használt, ez hatékony módja lehet a felhasználó általi használat korlátozásának. Azonban valószínű, hogy több felhasználó is megosztja egyetlen nyilvános IP-címét, mivel nat-eszközön keresztül férnek hozzá az internethez. Ennek ellenére a nem hitelesített hozzáférést `IpAddress` lehetővé tenni lehetővé tlehetővé tlehetővé tlehetővé tetsző API-k esetében a legjobb megoldás lehet.

## <a name="user-identity-throttling"></a>Felhasználói identitás szabályozása
Ha a végfelhasználó hitelesítve van, akkor egy szabályozási kulcs generálható az adott felhasználót egyedileg azonosító információk alapján.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

Ez a példa bemutatja, hogyan bonthatja ki az engedélyezési fejlécet, konvertálhatja objektummá, `JWT` és hogyan használhatja a jogkivonat tárgyát a felhasználó azonosítására, és ezt használhatja sebességkorlátozó kulcsként. Ha a felhasználói identitás a `JWT` többi jogcím egyikeként van tárolva, akkor ezt az értéket lehet használni a helyén.

## <a name="combined-policies"></a>Kombinált házirendek
Bár az új szabályozási házirendek több vezérlést biztosítanak, mint a meglévő szabályozási házirendek, még mindig van érték a két képességek kombinálása. Szabályozás termék-előfizetési kulcs szerint ([Hívási sebesség korlátozása előfizetés](/azure/api-management/api-management-access-restriction-policies#LimitCallRate) és [használati kvóta beállítása előfizetésenként](/azure/api-management/api-management-access-restriction-policies#SetUsageQuota)) egy nagyszerű módja annak, hogy az API-k felhasználásának szintje alapján történő töltéssel lehetővé tegye az API-k on-diktizálását. A finomabb szemcsés szabályozása, hogy képes a fojtószelep a felhasználó egymást kiegészít, és megakadályozza, hogy az egyik felhasználó viselkedése megalázó a tapasztalat egy másik. 

## <a name="client-driven-throttling"></a>Ügyfélvezérelt szabályozás
Ha a szabályozáskulcs egy [házirend-kifejezés](/azure/api-management/api-management-policy-expressions)használatával van definiálva, akkor az API-szolgáltató választja ki, hogy a szabályozás hatóköre hogyan legyen. Előfordulhat azonban, hogy a fejlesztő szabályozhatja, hogyan korlátozzák saját ügyfeleik limitelését. Ezt az API-szolgáltató engedélyezheti egy egyéni fejléc bevezetésével, amely lehetővé teszi, hogy a fejlesztő ügyfélalkalmazása kommunikálhassa a kulcsot az API-val.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Ez lehetővé teszi, hogy a fejlesztő ügyfélalkalmazása megválassza, hogyan szeretné létrehozni a sebességkorlátozó kulcsot. Az ügyfélfejlesztők létrehozhatják saját díjrétegeiket, ha kulcskészleteket osztanak ki a felhasználóknak, és elforgatják a kulcshasználatot.

## <a name="summary"></a>Összefoglalás
Az Azure API Management díj- és árajánlat-szabályozást biztosít az API-szolgáltatás védelme és hozzáadott értékének védelme érdekében. Az egyéni hatókörszabályokkal rendelkező új szabályozási házirendek lehetővé teszik, hogy az ilyen házirendek finomabb szabályozása lehetővé tegye az ügyfelek számára, hogy még jobb alkalmazásokat építsenek. Ebben a cikkben példák bemutatják ezeknek az új házirendeknek a használatát az ügyfél IP-címekkel, a felhasználói identitással és az ügyfél által generált értékekkel rendelkező kulcsok gyártási sebességkorlátozó alkalmazásával. Az üzenetnek azonban számos más része is használható, például a felhasználói ügynök, az URL-elérési út töredékei, az üzenet mérete.

## <a name="next-steps"></a>További lépések
Kérjük, adja meg visszajelzését github-problémaként ehhez a témához. Jó lenne hallani más lehetséges kulcsfontosságú értékeket, amelyek logikus választás a forgatókönyvek.

