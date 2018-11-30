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
ms.openlocfilehash: 45a65c7a94f3e6917b2882f27c9ad7d764ef97d7
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621835"
---
# <a name="subscriptions-in-azure-api-management"></a>Az Azure API Management-előfizetések

Előfizetések egy fontos fogalom, melyet az Azure API Management (APIM). Az APIM-példány keresztül közzétett API-k hozzáférést szerezni az API-fogyasztókat leggyakoribb módja. Ez a cikk a fogalmi áttekintése.

## <a name="what-is-subscriptions"></a>Mi az előfizetéseket

Közzé API-kat az APIM segítségével, a legegyszerűbb és leggyakrabban használt módszer a biztonságos hozzáférés az API-k esetén előfizetési kulcsok használatával. Más szóval fejlesztőknek szól, akik a fogyasztói a közzétett API-k tartalmaznia kell egy érvényes előfizetési kulcsot a HTTP-kérések API-k hívása esetén. Ellenkező esetben a hívások azonnal visszautasítja az APIM-átjáróhoz, és nem továbbítja a háttérszolgáltatások.

Szerezze be API-k eléréséhez egy előfizetési kulcsot, az előfizetés szükség. Előfizetés az előfizetés egy kulcspárra lényegében egy elnevezett tároló. Előfizetések fejlesztőknek szól, akik a fogyasztói a közzétett API-k, vagy anélkül jóváhagyási API-kiadók által olvasható be. API-kiadók is létrehozhat az előfizetések, közvetlenül az API-fogyasztókat nevében.

> [!TIP]
> API-k többek között való hozzáférés védelmét is támogatja más mechanizmusok APIM [OAuth2.0](api-management-howto-protect-backend-with-aad.md), [ügyféltanúsítványok](api-management-howto-mutual-certificates-for-clients.md), és [IP-címei engedélyezési listára helyezhetők](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Előfizetések hatókörének

Előfizetések társíthatók különböző hatókörök: termék, az összes API-k vagy egy egyéni API-t.

### <a name="subscriptions-for-a-product"></a>A termék előfizetések

Hagyományosan, mindig is egyetlen társított előfizetéseket az APIM [API-termékekre](api-management-terminology.md) hatókör. Fejlesztők lenne keresse meg a termékek listáját a fejlesztői portálon, és küldje el a feliratkozási kérelmeket a termékek szeretnék használni. Miután egy előfizetést szabadságkérésének jóváhagyásakor (automatikusan vagy API-kiadók által), a fejlesztői használatával a kulcsokat, a termék minden API-k elérésére.

![A termék-előfizetés](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> Bizonyos forgatókönyvek alapján API-kiadók érdemes API-termék közzétételét a nyilvános előfizetések anélkül. Akkor is, hogy törölje a **előfizetés szükséges** beállítást a **beállítások** az Azure Portalon a termék oldalán. Ennek eredményeképpen a termék minden API-k is elérhető API-kulcs nélkül.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Minden API-k vagy egyéni API-előfizetések

> [!NOTE]
> Ez a funkció jelenleg csak az API Management Használatalapú tarifacsomagban érhető el.

Amikor bevezettük a [fogyasztás](https://aka.ms/apimconsumptionblog) réteg az APIM, végeztünk néhány változtatást, hogy korszerűsítheti a kulcskezelést. Először is hozzáadott két további előfizetés hatókörök - az összes API-k és a egy egyetlen API-t. Előfizetések hatókörének már nem korlátozódik API-termék. Már lehetséges egy API-t (vagy az összes API-k APIM-példány belül), anélkül, termék létrehozása és hozzáadása az API-k, először való hozzáférés biztosítása a kulcsok létrehozásához. Ezen kívül minden APIM-példányra most már tartalmaz egy nem módosítható, minden-API-k előfizetés, amely egyszerűbbé és egyszerűbb, tesztelése és hibakeresése az API-k a Test-konzolon.

A második APIM most már lehetővé teszi a "különálló" előfizetések. Előfizetések már nem kell társítani egy fejlesztői fiók nevében. Ez akkor hasznos, a forgatókönyvek, például ha egy előfizetés megosztva több fejlesztők vagy fejlesztőcsapatok számára.

Végül, API-kiadók mostantól [előfizetések létrehozása](api-management-howto-create-subscriptions.md) közvetlenül az Azure Portalon:

![Rugalmas előfizetések](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>További lépések
További információ az API Management:

+ Ismerje meg, hogy más [fogalmak](api-management-terminology.md) az API Management szolgáltatásban
+ Kövesse a [oktatóanyagok](import-and-publish.md) további információ az API Management
+ Ellenőrizze a [gyakori kérdéseket tartalmazó oldal](api-management-faq.md) vonatkozó gyakori kérdések