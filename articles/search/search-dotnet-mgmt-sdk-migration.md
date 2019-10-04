---
title: Frissítés a Azure Search .NET Management SDK 2-es verziójára – Azure Search
description: Frissítsen a Azure Search .NET Management SDK 2-es verziójára a korábbi verziókról. Ismerje meg az újdonságokat és a szükséges kód változásait.
author: brjohnstmsft
manager: nitinme
ms.author: brjohnst
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.openlocfilehash: 2a59cff7f5313b0ac5a060d698950a4c82160f67
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182254"
---
# <a name="upgrading-to-the-azure-search-net-management-sdk-version"></a>Frissítés a Azure Search .NET Management SDK verziójára 

> [!Important]
> Ez a tartalom még fejlesztés alatt áll. A Azure Search Management .NET SDK 3,0-es verziója a NuGet címen érhető el. Dolgozunk az áttelepítési útmutató frissítésén, hogy elmagyarázza, hogyan frissíthet az új verzióra. 
>

Ha a [Azure Search .net Management SDK](https://aka.ms/search-mgmt-sdk)-hoz tartozó 1.0.2 vagy régebbi verziót használja, ez a cikk segítséget nyújt az alkalmazás 2. verzióra való frissítéséhez.

A Azure Search .NET Management SDK 2. verziója a korábbi verziók néhány módosítását tartalmazza. Ezek többnyire kisebbek, ezért a kód módosítása csak minimális erőfeszítést igényelhet. Az új SDK-verzió használatára vonatkozó utasításokért lásd: a [verziófrissítés lépései](#UpgradeSteps) .

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>A 2. verzió újdonságai
A Azure Search .NET Management SDK 2. verziója az előző SDK-verziónak megfelelően a Azure Search felügyeleti REST API általánosan elérhető verzióját célozza meg, amely kifejezetten 2015-08-19. Az SDK módosításai szigorúan ügyféloldali változások az SDK használhatóságának javítása érdekében. Ezek a változások a következők:

* `Services.CreateOrUpdate`a és az aszinkron verziói mostantól automatikusan lekérdezik `SearchService` a kiépítési és a nem visszaadott állapotot, amíg a szolgáltatás kiépítés be nem fejeződik. Ezzel elmentheti, hogy az ilyen lekérdezési kódot saját kezűleg írja.
* Ha továbbra is manuálisan szeretné lekérdezni a szolgáltatás üzembe helyezését, használhatja az `Services.BeginCreateOrUpdate` új metódust vagy annak egy aszinkron verzióját.
* Új metódusok `Services.Update` és aszinkron verziók lettek hozzáadva az SDK-hoz. Ezek a módszerek a HTTP-javítás használatával támogatják a szolgáltatások növekményes frissítését. Mostantól például méretezheti a szolgáltatást úgy, hogy átadja egy `SearchService` példányát ezekhez a metódusokhoz, amelyek `partitionCount` csak `replicaCount` a kívánt és a tulajdonságokat tartalmazzák. A régi módon `Services.Get`hívható meg `Services.CreateOrUpdate` , módosítható `SearchService`a visszaadott érték, és a továbbítása továbbra is támogatott, de már nem szükséges. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>A frissítés lépései
Először frissítse a NuGet-referenciát `Microsoft.Azure.Management.Search` a NuGet csomagkezelő konzoljának használatára, vagy kattintson a jobb gombbal a projekt hivatkozásaira, és válassza a "NuGet-csomagok kezelése..." lehetőséget. a Visual Studióban.

Miután a NuGet letöltötte az új csomagokat és azok függőségeit, építse újra a projektet. A kód szerkezetének módjától függően előfordulhat, hogy az Újraépítés sikeresen megtörtént. Ha igen, készen állsz!

Ha a létrehozás sikertelen, annak oka az lehet, hogy néhány SDK-felületet implementált (például az egység tesztelése céljából), amelyek megváltoztak. Ennek megoldásához végre kell hajtania az új metódusokat, például `BeginCreateOrUpdateWithHttpMessagesAsync`:.

A felépítési hibák kijavítása után módosíthatja az alkalmazást, és igény szerint kihasználhatja az új funkciókat. Az SDK új funkciói részletesen ismertetik a [2. verzió újdonságait](#WhatsNew).

## <a name="conclusion"></a>Összegzés
Üdvözöljük az SDK-val kapcsolatos visszajelzéseit. Ha problémákba ütközik, kérjen segítséget a [Azure Search MSDN-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Ha hibát talál, a probléma az [Azure .net SDK GitHub](https://github.com/Azure/azure-sdk-for-net/issues)-tárházában is megadható. Ügyeljen arra, hogy a probléma címét "[Azure Search]" előtaggal adja meg.

Köszönjük Azure Search használatát!
