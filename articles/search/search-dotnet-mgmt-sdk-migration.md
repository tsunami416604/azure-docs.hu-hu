---
title: Frissítés az Azure Search .NET Management SDK 2-es verziójára
titleSuffix: Azure Cognitive Search
description: Frissítsen az Azure Search .NET Management SDK 2-es verziójára a korábbi verziókból. További információ az újdonságokról és a kódmódosításról.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b18e9688141ee64eb7dfcb82ce58db198e324b5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847539"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>Az Azure Search .NET Management SDK verzióinak frissítése

> [!Important]
> Ez a tartalom még fejlesztés alatt áll. Az Azure Search Management .NET SDK 3.0-s verziója elérhető a NuGet-en. Dolgozunk az áttelepítési útmutató frissítésén, hogy elmagyarázzuk, hogyan frissíthet az új verzióra. 
>

Ha az [Azure Search .NET Management SDK](https://aka.ms/search-mgmt-sdk)1.0.2-es vagy újabb verzióját használja, ez a cikk segít az alkalmazás 2-es verziójú frissítésében.

Az Azure Search .NET Management SDK 2-es verziója tartalmaz néhány módosítást a korábbi verziókhoz. Ezek többnyire kisebb, így a kód módosítása csak minimális erőfeszítést igényel. Az új SDK-verzió használatához a [frissítés lépései](#UpgradeSteps) című témakörben talál útmutatást a kód módosításához.

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>A 2-es verzió újdonságai
Az Azure Search .NET Management SDK 2-es verziója az Azure Search Management REST API általánosan elérhető verzióját célozza meg, mint a korábbi SDK-verziók, különösen a 2015-08-19. Az SDK módosításai szigorúan ügyféloldali módosítások, amelyek javítják magának az SDK-nak a használhatóságát. Ezek a változások a következők:

* `Services.CreateOrUpdate`és az aszinkron verziók mostantól automatikusan lepollálják a kiépítést, `SearchService` és nem térvissza, amíg a szolgáltatás kiépítése befejeződött. Ez megment ön -ból birtoklás -hoz ír ilyen közvélemény-kutatás kód önmaga.
* Ha továbbra is manuálisan szeretné lehallgatni a `Services.BeginCreateOrUpdate` szolgáltatáskiépítést, használhatja az új módszert vagy annak egyik aszinkron verzióját.
* Új `Services.Update` módszerek és aszinkron verziók lettek hozzáadva az SDK-hoz. Ezek a módszerek a HTTP PATCH protokollt használják egy szolgáltatás növekményes frissítésének támogatásához. Például most már skálázhat egy `SearchService` szolgáltatást úgy, hogy egy `partitionCount` `replicaCount` példányt átad ezeknek a metódusoknak, amelyek csak a kívánt és tulajdonságokat tartalmazzák. A visszaküldött `Services.Get`hívás, a `SearchService`visszaküldött állapot `Services.CreateOrUpdate` módosítása és átadása továbbra is támogatott, de már nincs szükség rá. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>A frissítés lépései
Először frissítse NuGet-referenciáját a NuGet Csomagkezelő konzol `Microsoft.Azure.Management.Search` használatával, vagy kattintson a jobb gombbal a projektreferenciáira, és válassza a "NuGet csomagok kezelése..." parancsot. a Visual Studio alkalmazásban.

Miután a NuGet letöltötte az új csomagokat és azok függőségeit, építse újra a projektet. Attól függően, hogy a kód épül, előfordulhat, hogy sikeresen újraépül. Ha igen, akkor készen áll az indulásra!

Ha a build sikertelen, annak az lehet az oka, hogy megvalósított néhány SDK-felületet (például az egység tesztelése céljából), amelyek megváltoztak. A probléma megoldásához végre kell hajtania az `BeginCreateOrUpdateWithHttpMessagesAsync`új módszereket, például a .

Miután kijavította a buildelési hibákat, módosíthatja az alkalmazást, hogy kihasználhassa az új funkciók előnyeit, ha szeretné. Az SDK új funkcióit a [2-es verzió újdonságai](#WhatsNew)részletezik.

## <a name="next-steps"></a>További lépések
Örömmel fogadjuk az SDK-val kapcsolatos visszajelzéseit. Ha problémákba ütközik, kérjük, tegye a kérdéseket [Stack túlcsordulás](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Ha hibát talál, az [Azure .NET SDK GitHub tárházban](https://github.com/Azure/azure-sdk-for-net/issues)nyújthat be problémát. Győződjön meg róla, hogy a probléma címét a "[keresés]"-re címkézi.
