---
title: Előfizetések az Azure API Management |} A Microsoft Docs
description: Ismerje meg az előfizetések az Azure API Management fogalmát.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 6f577530c42952c6340a15110bcd37383a5fca57
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59526588"
---
# <a name="subscriptions-in-azure-api-management"></a>Az Azure API Management-előfizetések

Előfizetések az Azure API Management egyik fontos vonása. Azok az API Management-példány keresztül közzétett API-k eléréséhez az API-fogyasztókat leggyakoribb módja. Ez a cikk a fogalmi áttekintése.

## <a name="what-are-subscriptions"></a>Mik azok az előfizetések?

API Management szolgáltatáson keresztül API-kat tesz közzé, amelyet esetén könnyen és a közös API-kból való biztonságos hozzáférés előfizetési kulcsok használatával. Fejlesztőknek szól, akik a fogyasztói a közzétett API-k tartalmaznia kell egy érvényes előfizetési kulcsot HTTP-kérelmekre adott API-kat fejlécekben. Ellenkező esetben a hívásokat a rendszer elutasítja azonnal az API Management-átjáró által. Ezek a háttérszolgáltatások nem továbbítja.

Az első API-k eléréséhez egy előfizetési kulcsot, egy előfizetésére szükség. Egy előfizetés az előfizetés egy kulcspárra lényegében egy elnevezett tároló. Fejlesztők számára, akiknek szükség van a közzétett API-kat használhat az előfizetések kérheti le. És API-kiadók jóváhagyására nincs szükségük. API-kiadók is létrehozhat előfizetés közvetlenül az API-fogyasztókat.

> [!TIP]
> Az API Management más mechanizmusok is támogatja az API-k, többek között az alábbi példák való hozzáférés biztonságossá tétele:
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Ügyféltanúsítványok](api-management-howto-mutual-certificates-for-clients.md)
> - [IP-címei engedélyezési listára helyezhetők](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Előfizetések hatókörének

Előfizetések társíthatók különböző hatókörök: termék, az összes API-k vagy egy egyéni API-t.

### <a name="subscriptions-for-a-product"></a>A termék előfizetések

Hagyományosan, mindig lettek társítva egyetlen előfizetés az API Management [API-termékekre](api-management-terminology.md) hatókör. A fejlesztők a fejlesztői portál termékek listája található. Ezután ezek feliratkozási kérelmeket a termékek kapcsolniuk a használni kívánt elküld. Miután egy előfizetési kérést jóváhagyják, automatikusan vagy API-kiadók a fejlesztői használhatja a kulcsokat, a termék minden API-k elérésére.

![A termék-előfizetés](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> Bizonyos forgatókönyvek alapján API-kiadók érdemes API-termék közzétételét a nyilvános előfizetések anélkül. Törölje azokat a **előfizetés szükséges** beállítást a **beállítások** az Azure Portalon a termék oldalán. Ennek eredményeképpen a termék minden API-k is elérhető API-kulcs nélkül.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Minden API-k vagy egyéni API-előfizetések

Amikor bevezettük a [fogyasztás](https://aka.ms/apimconsumptionblog) szint az API Management, végeztünk néhány változtatást, hogy korszerűsítheti a kulcskezelést:
- Először két több előfizetés hatókört bővítettük: az összes API-k és a egy egyetlen API-t. Előfizetések hatókörének már nem korlátozódik API-termék. Már lehetséges, hogy hozzáférést biztosítani az API-k, vagy minden API-k az API Management példányok belül anélkül, hogy hozzon létre egy terméket, és hozzá tud adni az API-k először kulcsok létrehozásához. API Management minden példányához is, most már tartalmaz egy nem módosítható, minden-API-k előfizetés keretében. Ez az előfizetés megkönnyíti és egyszerűbb, tesztelése és hibakeresése az API-k a test-konzolon.

- Másodszor, az API Management most már lehetővé teszi, hogy **önálló** előfizetések. Előfizetések már nem kell társítani egy fejlesztői fiók nevében. Ez a funkció akkor hasznos, forgatókönyvekben, például ha több fejlesztők vagy fejlesztőcsapatok számára megosztani egy előfizetést.

- Végül, API-kiadók mostantól [előfizetések létrehozása](api-management-howto-create-subscriptions.md) közvetlenül az Azure Portalon:

    ![Rugalmas előfizetések](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>További lépések
További információ az API Management:

+ Ismerje meg, hogy más [fogalmak](api-management-terminology.md) az API Management szolgáltatásban.
+ Kövesse a [oktatóanyagok](import-and-publish.md) további információ az API Management.
+ Ellenőrizze a [gyakori kérdéseket tartalmazó oldal](api-management-faq.md) gyakori kérdéseket.