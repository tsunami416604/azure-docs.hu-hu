---
title: Az Azure Search .NET Management SDK 2-es verzió frissítése |} Microsoft Docs
description: Az Azure Search .NET Management SDK 2-es verzió frissítése
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.openlocfilehash: a6b6c01f0cc811abca90139e4c26c27b7bd7119f
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
ms.locfileid: "31790365"
---
# <a name="upgrading-to-the-azure-search-net-management-sdk-version-2"></a>Az Azure Search .NET Management SDK 2-es verzió frissítése
1.0.2-es verzióját használata vagy a régebbi a [Azure Search .NET SDK-t felügyeleti](https://aka.ms/search-mgmt-sdk), ez a cikk segít frissíteni az alkalmazást a 2-es verziója.

Az Azure Search .NET SDK felügyeleti 2-es verziója korábbi verzióihoz képest végrehajtott egyes módosításokat tartalmazza. Ezek a legtöbbször kisebb, így a kód módosítása elvégzéséhez szükséges csak csatlakozhatnak. Lásd: [frissítésének lépései](#UpgradeSteps) útmutatást a kód módosítása az új SDK-verzió használatára.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>2-es verzió újdonságai
Az Azure Search .NET SDK felügyeleti 2 verzióját célozza ugyanazon általánosan elérhető verziójának az Azure Search felügyeleti REST API SDK korábbi, mint kifejezetten 2015-08-19. A módosítások az SDK módosulnak szigorúan ügyféloldali az SDK-t a használhatóság javítása érdekében. Ezek a változások közé tartoznak a következők:

* `Services.CreateOrUpdate` az aszinkron verzióival mostantól automatikusan lekérdezik a kiépítés és `SearchService` és befejezéséig szolgáltatás kiépítése nem adják vissza. Ezáltal, nem kell saját kezűleg ilyen lekérdezési kód írása.
* Ha meg kívánja, és kérdezze le a manuális létesítési szolgáltatás, az új használható `Services.BeginCreateOrUpdate` metódus vagy az aszinkron verziók egyike.
* Új módszerek `Services.Update` és az aszinkron verzióival érhetőek el az SDK-t. Ezek a módszerek segítségével HTTP javítására, de támogatja a növekményes frissítést a szolgáltatás. Például most szolgáltatás is skálázható egy úgy, hogy egy `SearchService` ezeket a módszereket, amely tartalmazza a csak a kívánt példány `partitionCount` és `replicaCount` tulajdonságok. Hívása a régi módja `Services.Get`, a visszaadott módosítása `SearchService`, és átadja azt a `Services.CreateOrUpdate` továbbra is támogatott, de már nem szükséges. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Frissítésének lépései
Először frissítse a NuGet referencia `Microsoft.Azure.Management.Search` vagy a NuGet-Csomagkezelő konzol használatával vagy az csomagot jobb gombbal a projekt hivatkozásait, majd válassza a "Kezelése NuGet csomagjainak..." a Visual Studióban.

Miután NuGet töltött le az új csomagok és a függőségek, a projekt újraépítéséhez. Attól függően, hogy a kód hogyan épül akkor előfordulhat, hogy újraépítése sikeresen. Ha igen, most készen áll!

Ha a build meghibásodik, annak oka lehet már megvalósította a egyes SDK kapcsolatok (például alkalmazásában egységek tesztelése), amelyek megváltoztak. A probléma megoldásához, szüksége lesz az új módszerek végrehajtásához `BeginCreateOrUpdateWithHttpMessagesAsync`.

Build hibák megszüntetése után módosíthatja a új funkcióinak kihasználásához, ha az alkalmazáshoz. Az SDK-t az új funkciói részletes leírást talál [2-es verzió újdonságai](#WhatsNew).

## <a name="conclusion"></a>Összegzés
Az SDK-val szívesen fogadjuk a visszajelzéseket. Ha problémákat tapasztal, nyugodtan megkérdezi a Súgó a [Azure Search MSDN fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Ha a hiba, a problémát fájl is a [Azure .NET SDK GitHub-tárházban](https://github.com/Azure/azure-sdk-for-net/issues). Győződjön meg arról, hogy a probléma címének és a "[az Azure Search]" előtag.

Köszönjük, hogy az Azure Search használatával!
