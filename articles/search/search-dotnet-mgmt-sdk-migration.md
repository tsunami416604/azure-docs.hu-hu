---
title: Frissítés a Azure Search .NET Management SDK 2-es verziójára
titleSuffix: Azure Cognitive Search
description: Frissítsen a Azure Search .NET Management SDK 2-es verziójára a korábbi verziókról. Ismerje meg az újdonságokat és a szükséges kód változásait.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: b18e9688141ee64eb7dfcb82ce58db198e324b5b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "73847539"
---
# <a name="upgrading-versions-of-the-azure-search-net-management-sdk"></a>A Azure Search .NET Management SDK verzióinak frissítése

> [!Important]
> Ez a tartalom még fejlesztés alatt áll. A Azure Search Management .NET SDK 3,0-es verziója a NuGet címen érhető el. Dolgozunk az áttelepítési útmutató frissítésén, hogy elmagyarázza, hogyan frissíthet az új verzióra. 
>

Ha a [Azure Search .net Management SDK](https://aka.ms/search-mgmt-sdk)-hoz tartozó 1.0.2 vagy régebbi verziót használja, ez a cikk segítséget nyújt az alkalmazás 2. verzióra való frissítéséhez.

A Azure Search .NET Management SDK 2. verziója a korábbi verziók néhány módosítását tartalmazza. Ezek többnyire kisebbek, ezért a kód módosítása csak minimális erőfeszítést igényelhet. Az új SDK-verzió használatára vonatkozó utasításokért lásd: a [verziófrissítés lépései](#UpgradeSteps) .

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-2"></a>A 2. verzió újdonságai
A Azure Search .NET Management SDK 2. verziója az előző SDK-verziónak megfelelően a Azure Search felügyeleti REST API általánosan elérhető verzióját célozza meg, amely kifejezetten 2015-08-19. Az SDK módosításai szigorúan ügyféloldali változások az SDK használhatóságának javítása érdekében. Ezek a változások a következők:

* `Services.CreateOrUpdate`a és az aszinkron verziói mostantól automatikusan lekérdezik a kiépítési és a nem visszaadott állapotot, `SearchService` amíg a szolgáltatás kiépítés be nem fejeződik. Ezzel elmentheti, hogy az ilyen lekérdezési kódot saját kezűleg írja.
* Ha továbbra is manuálisan szeretné lekérdezni a szolgáltatás üzembe helyezését, használhatja az új `Services.BeginCreateOrUpdate` metódust vagy annak egy aszinkron verzióját.
* Új metódusok `Services.Update` és aszinkron verziók lettek hozzáadva az SDK-hoz. Ezek a módszerek a HTTP-javítás használatával támogatják a szolgáltatások növekményes frissítését. Mostantól például méretezheti a szolgáltatást úgy, hogy átadja egy `SearchService` példányát ezekhez a metódusokhoz, amelyek csak a kívánt `partitionCount` és a `replicaCount` tulajdonságokat tartalmazzák. A régi módon hívható `Services.Get` meg, módosítható a visszaadott érték, `SearchService` és a továbbítása `Services.CreateOrUpdate` továbbra is támogatott, de már nem szükséges. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>A frissítés lépései
Először frissítse a NuGet-referenciát a `Microsoft.Azure.Management.Search` NuGet csomagkezelő konzoljának használatára, vagy kattintson a jobb gombbal a projekt hivatkozásaira, és válassza a "NuGet-csomagok kezelése..." lehetőséget. a Visual Studióban.

Miután a NuGet letöltötte az új csomagokat és azok függőségeit, építse újra a projektet. A kód szerkezetének módjától függően előfordulhat, hogy az Újraépítés sikeresen megtörtént. Ha igen, készen állsz!

Ha a létrehozás sikertelen, annak oka az lehet, hogy néhány SDK-felületet implementált (például az egység tesztelése céljából), amelyek megváltoztak. Ennek megoldásához végre kell hajtania az új metódusokat, például: `BeginCreateOrUpdateWithHttpMessagesAsync` .

A felépítési hibák kijavítása után módosíthatja az alkalmazást, és igény szerint kihasználhatja az új funkciókat. Az SDK új funkciói részletesen ismertetik a [2. verzió újdonságait](#WhatsNew).

## <a name="next-steps"></a>További lépések
Üdvözöljük az SDK-val kapcsolatos visszajelzéseit. Ha problémákba ütközik, tegye fel a kérdéseit [stack Overflowba](https://stackoverflow.com/questions/tagged/azure-cognitive-search?tab=Newest). Ha hibát talál, a probléma az [Azure .net SDK GitHub-tárházában](https://github.com/Azure/azure-sdk-for-net/issues)is megadható. Ügyeljen arra, hogy a "[Search]" címkével címkézze fel a probléma címét.
