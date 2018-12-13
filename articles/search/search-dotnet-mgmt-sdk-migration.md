---
title: Az Azure Search .NET Management SDK 2 – Azure Search-es verzióra
description: Frissítse a korábbi verziókról az Azure Search .NET Management SDK 2-es verzió. Megtudhatja, Miben változott, és milyen kódot módosításokra szükség.
author: brjohnstmsft
manager: jlembicz
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.custom: seodec2018
ms.openlocfilehash: 369e5283d7350729e0d8cc44f94f1f2e71b133c3
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53316869"
---
# <a name="upgrading-to-the-azure-search-net-management-sdk-version-2"></a>Az Azure Search .NET Management SDK 2-es verzióra
Ha 1.0.2-es verzióját használja, vagy a régebbi a [Azure Search .NET SDK-t felügyeleti](https://aka.ms/search-mgmt-sdk), ez a cikk segít az alkalmazás használhatja a 2. verzió frissítése.

2. verzióját az Azure Search .NET SDK felügyeleti tartalmaz néhány módosítást korábbi verzióit. Ezek a leginkább kis, így a kód módosítása csak minimális erőfeszítéssel elvégzéséhez szükséges. Lásd: [frissítésére lépéseket](#UpgradeSteps) útmutatást a kód módosítása az új SDK-verzió használatához.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>2-es verzió újdonságai
2. verzióját az Azure Search .NET SDK felügyeleti célozza azonos általánosan elérhető verzióját az Azure Search felügyeleti REST API, SDK előző verziók, kifejezetten 2015-08-19. Az SDK-t a módosítások az SDK-t, a modulok használhatóságának fejlesztésében szigorúan ügyféloldali módosításai. Ezek a változások a következők:

* `Services.CreateOrUpdate` az aszinkron verziókat most már automatikusan lekérdezik a kiépítés és `SearchService` és nem ad vissza, amíg a szolgáltatás kiépítése befejeződik. Ezzel takaríthat meg, nem kell saját kezűleg ilyen lekérdezési kód írása.
* Ha továbbra is szeretné lekérdezni a manuális kiépítési szolgáltatás, akkor használhatja az új `Services.BeginCreateOrUpdate` metódus vagy egy annak aszinkron verzióit.
* Új módszerek `Services.Update` és az aszinkron verziókat lettek hozzáadva az SDK-t. Ezek a módszerek használatával HTTP-javítás támogatja a növekményes frissítést a szolgáltatás. Például, most már méretezheti a szolgáltatás átadásával egy `SearchService` példány ezen metódusok meghívásához, amely tartalmazza a csak a kívánt `partitionCount` és `replicaCount` tulajdonságait. A régi módja hívása `Services.Get`, a visszaadott módosítása `SearchService`, majd továbbítani azt `Services.CreateOrUpdate` továbbra is támogatott, de már nem szükséges. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>Frissítési lépései
Először frissítse a NuGet referenciája `Microsoft.Azure.Management.Search` vagy a NuGet Package Manager konzol segítségével vagy a jobb gombbal a projekt hivatkozásait a és a Visual Studióban válassza a "Kezelése NuGet Packages …".

Miután NuGet töltött le az új csomagok és azok függőségeit, a projekt újraépítéséhez. Attól függően, hogyan épül fel a kódot akkor előfordulhat, hogy építse újra sikeresen megtörtént. Ha igen, akkor készen!

A build sikertelen lesz, ha azt oka az lehet, hogy megvalósította a egyes SDK kapcsolatok (például az alkalmazásában egységtesztelés), amelyek megváltoztak. A probléma megoldásához lesz szüksége az új módszerek végrehajtásához `BeginCreateOrUpdateWithHttpMessagesAsync`.

Felépítési hibák már rögzített, ha módosításokat végezheti el az alkalmazást, hogy új funkciók előnyeinek kihasználása, ha szeretné. Az SDK-t az új funkciók részletes leírásuk [2-es verzió újdonságai](#WhatsNew).

## <a name="conclusion"></a>Összegzés
Szívesen fogadjuk a visszajelzéseket az SDK-val. Ha problémákat tapasztal, nyugodtan megszüntetését a Súgó a [Azure Search MSDN-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Ha a hiba, a megkereséseit is fájl a [Azure .NET SDK GitHub-adattár](https://github.com/Azure/azure-sdk-for-net/issues). Győződjön meg arról, hogy a probléma címe és az "[az Azure Search]" előtagot.

Köszönjük, hogy az Azure Search használatával!
