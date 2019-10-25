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
ms.openlocfilehash: b81060bc3d8409a9ccb74ba85c85e6f779650ffd
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72792420"
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

* a `Services.CreateOrUpdate` és az aszinkron verziói mostantól automatikusan lekérdezik a kiépítési `SearchService`, és a szolgáltatás kiépítés befejeződéséig nem térnek vissza. Ezzel elmentheti, hogy az ilyen lekérdezési kódot saját kezűleg írja.
* Ha továbbra is manuálisan szeretné lekérdezni a szolgáltatás kiépítési lehetőségeit, használhatja az új `Services.BeginCreateOrUpdate` metódust vagy annak egy aszinkron verzióját.
* A rendszer új metódusokat `Services.Update` és aszinkron verzióit adta hozzá az SDK-hoz. Ezek a módszerek a HTTP-javítás használatával támogatják a szolgáltatások növekményes frissítését. Mostantól például méretezheti a szolgáltatást úgy, hogy átadja a `SearchService` példányát ezekkel a metódusokkal, amelyek csak a kívánt `partitionCount` és `replicaCount` tulajdonságokat tartalmazzák. A `Services.Get`meghívásának régi módja, a visszaadott `SearchService`módosítása és a `Services.CreateOrUpdate`re való átadása továbbra is támogatott, de már nem szükséges. 

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>A frissítés lépései
Először frissítse a `Microsoft.Azure.Management.Search` NuGet-hivatkozását a NuGet csomagkezelő konzol használatával, vagy kattintson a jobb gombbal a projekt hivatkozásaira, és válassza a "NuGet-csomagok kezelése..." lehetőséget. a Visual Studióban.

Miután a NuGet letöltötte az új csomagokat és azok függőségeit, építse újra a projektet. A kód szerkezetének módjától függően előfordulhat, hogy az Újraépítés sikeresen megtörtént. Ha igen, készen állsz!

Ha a létrehozás sikertelen, annak oka az lehet, hogy néhány SDK-felületet implementált (például az egység tesztelése céljából), amelyek megváltoztak. Ennek megoldásához végre kell hajtania az új metódusokat, például a `BeginCreateOrUpdateWithHttpMessagesAsync`.

A felépítési hibák kijavítása után módosíthatja az alkalmazást, és igény szerint kihasználhatja az új funkciókat. Az SDK új funkciói részletesen ismertetik a [2. verzió újdonságait](#WhatsNew).

## <a name="conclusion"></a>Összegzés
Üdvözöljük az SDK-val kapcsolatos visszajelzéseit. Ha problémákba ütközik, kérjen segítséget a [Azure Search MSDN-fórumon](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch). Ha hibát talál, a probléma az [Azure .net SDK GitHub-tárházában](https://github.com/Azure/azure-sdk-for-net/issues)is megadható. Ügyeljen arra, hogy a probléma címét "[Azure Search]" előtaggal adja meg.

Köszönjük Azure Search használatát!
