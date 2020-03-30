---
title: Előfizetések az Azure API Managementben | Microsoft dokumentumok
description: Ismerje meg az előfizetések fogalmát az Azure API Managementben.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 9144af131e1427d0b3226655c871921ac1d91665
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70073419"
---
# <a name="subscriptions-in-azure-api-management"></a>Előfizetések az Azure API Managementben

Az előfizetések fontos koncepciót jelentenek az Azure API Managementben. Ezek a leggyakoribb módja az API-fogyasztók számára, hogy hozzáférjenek az API Management-példányon keresztül közzétett API-khoz. Ez a cikk áttekintést nyújt a koncepcióról.

## <a name="what-are-subscriptions"></a>Mik azok az előfizetések?

Ha API-n keresztül teszi közzé az API-kat, az előfizetési kulcsok használatával egyszerűen és általánosan biztosíthatja az API-khoz való hozzáférést. Azoknak a fejlesztőknek, akiknek fel kell használniuk a közzétett API-kat, érvényes előfizetési kulcsot kell tartalmazniuk a HTTP-kérelmekben, amikor hívásokat kezdeményeznek ezekhez az API-khoz. Ellenkező esetben a hívásokat az API Management átjáró azonnal elutasítja. Nem továbbítják őket a háttérszolgáltatásoknak.

Az API-k eléréséhez előfizetési kulcs lekérni, előfizetés szükséges. Az előfizetés lényegében egy elnevezett tároló egy pár előfizetési kulcsok. Azok a fejlesztők, akiknek fel kell használniuk a közzétett API-kat, előfizetéseket kaphatnak. És nem kell jóváhagyást API-közzétevők. Az API-közzétevők közvetlenül is létrehozhatnak előfizetéseket az API-fogyasztók számára.

> [!TIP]
> Az API Management más api-khoz való hozzáférés biztosítására szolgáló mechanizmusokat is támogat, többek között a következő példákat:
> - [OAuth2.0](api-management-howto-protect-backend-with-aad.md)
> - [Ügyféltanúsítványok](api-management-howto-mutual-certificates-for-clients.md)
> - [IP-engedélyezési lista](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Az előfizetések hatóköre

Az előfizetések különböző hatókörökhöz társíthatók: termék, az összes API vagy egy egyedi API.

### <a name="subscriptions-for-a-product"></a>Termék előfizetései

Hagyományosan az API Management előfizetései mindig egyetlen [API-termékkörhöz](api-management-terminology.md) voltak társítva. A fejlesztők megtalálták a termékek listáját a fejlesztői portálon. Ezután előfizetési kérelmeket nyújtottak be a használni kívánt termékekre vonatkozóan. Az előfizetési kérelem jóváhagyása után, akár automatikusan, akár az API-közzétevők által, a fejlesztő használhatja a benne lévő kulcsokat a termék összes API-jának eléréséhez. Jelenleg a fejlesztői portál csak a felhasználói profil szakaszban lévő termékkör-előfizetéseket jeleníti meg. 

![Termék-előfizetések](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> Bizonyos esetekben az API-közzétevők előfordulhat, hogy az előfizetések követelménye nélkül szeretnének közzétenni egy API-terméket a nyilvánosság számára. Az Azure Portalon a **termék Beállítások** lapján törölje a jelet az **előfizetés megkövetelése** jelölőnégyzetből. Ennek eredményeképpen a termék alatt található összes API API API-k API-kulcs nélkül érhetők el.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Előfizetések az összes API-hoz vagy egy egyedi API-hoz

Amikor bevezettük az API Management [felhasználási](https://aka.ms/apimconsumptionblog) szintjét, néhány változtatást eszközöltünk a kulcskezelés egyszerűsítése érdekében:
- Először is hozzáadtunk még két előfizetési hatókört: az összes API-t és egy API-t. Az előfizetések hatóköre már nem korlátozódik egy API-termékre. Most már lehetséges, hogy hozzon létre olyan kulcsokat, amelyek hozzáférést biztosítanak egy API-hoz, vagy az API-felügyeleti példányon belüli összes API-hoz anélkül, hogy létre kellene hoznia egy terméket, és először hozzá kellene adnia az API-kat. Emellett minden API Management-példány most egy nem módosítható, minden API-előfizetéssel érkezik. Ez az előfizetés megkönnyíti és egyszerűbbé teszi az API-k tesztelését és hibakeresési api-jait a tesztkonzolon belül.

- Másodszor, az API Management mostantól lehetővé teszi **az önálló** előfizetéseket. Az előfizetések már nem szükségesek fejlesztői fiókhoz társítva. Ez a funkció olyan esetekben hasznos, például amikor több fejlesztő vagy csapat osztozik egy előfizetésen.

- Végül az API-közzétevők mostantól közvetlenül az Azure Portalon [hozhatnak létre előfizetéseket:](api-management-howto-create-subscriptions.md)

    ![Rugalmas előfizetések](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>További lépések
További információ az API Managementről:

+ Ismerje meg az API Management egyéb [fogalmait.](api-management-terminology.md)
+ Kövesse [oktatóanyagainkat,](import-and-publish.md) hogy többet tudjon meg az API Managementről.
+ A gyakori kérdésekért tekintse meg [gyik oldalunkat.](api-management-faq.md)
