---
title: Az Azure Functions és az Azure SignalR Service valós idejű alkalmazások készítése
description: Áttekintés az Azure SignalR Service segítségével a kiszolgáló nélküli alkalmazásokat.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: c06203cfd7537dd4c9bc35c75a3f21d12bf64d26
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554987"
---
# <a name="build-real-time-apps-with-azure-functions-and-azure-signalr-service"></a>Az Azure Functions és az Azure SignalR Service valós idejű alkalmazások készítése

Mivel az Azure SignalR szolgáltatás és az Azure Functions teljeskörűen felügyelt, nagymértékben skálázható szolgáltatások, amelyek lehetővé teszik, hogy az infrastruktúra-kezelés helyett az alkalmazások létrehozására összpontosítson, gyakran használják együtt őket, hogy valós idejű kommunikációt biztosítsanak a [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) környezetekben.

## <a name="integrate-real-time-communications-with-azure-services"></a>Valós idejű kommunikáció integrálása Azure-szolgáltatásokkal

Az Azure Functions lehetővé teszi, hogy [több nyelven](../azure-functions/supported-languages.md) (például JavaScript, C# és Java nyelven) írjon olyan kódokat, amelyek akkor aktiválódnak, ha események történnek a felhőben. Ilyen események például a következők:

* HTTP- és webhookkérések
* Rendszeres időzítők
* Azure-szolgáltatások eseményei, például:
    - Event Grid
    - Event Hubs
    - Service Bus
    - Cosmos DB-változáscsatorna
    - Storage – blobok és üzenetsorok
    - Logic Apps-összekötők, például a Salesforce és az SQL Server

Ha az Azure Functionst használja az események Azure SignalR szolgáltatással való integrálására, akkor több ezer ügyfelet értesíthet a bekövetkező eseményekről.

Néhány gyakori forgatókönyv az Azure Functions és a SignalR szolgáltatással megvalósítható valós idejű, kiszolgáló nélküli üzenetküldésre:

* IoT-eszköztelemetria vizualizációja egy valós idejű irányítópulton vagy térképen
* Adatok frissítése egy alkalmazásban, amikor a dokumentumok frissülnek a Cosmos DB-ben
* Alkalmazásbeli értesítések küldése, ha új megrendelések jönnek létre a Salesforce-ban

## <a name="signalr-service-bindings-for-azure-functions"></a>SignalR-szolgáltatáskötések az Azure Functionshöz

A SignalR szolgáltatás Azure Functionshöz készült kötései lehetővé teszik az Azure-függvényalkalmazásoknak, hogy üzeneteket küldjenek a SignalR szolgáltatáshoz csatlakoztatott ügyfeleknek. Az ügyfelek egy SignalR ügyféloldali SDK-val csatlakozhatnak a szolgáltatáshoz, amely jelenleg .NET, JavaScript és Java nyelven (és hamarosan további nyelveken) érhető el.

### <a name="an-example-scenario"></a>Példaforgatókönyv

A SignalR szolgáltatáskötések használatának egyik példája az Azure Functions használata az Azure Cosmos DB-vel és a SignalR szolgáltatással való integrációra, hogy valós idejű üzeneteket lehessen küldeni, amikor új események jelennek meg a Cosmos DB-változáscsatornákon.

![Cosmos DB, Azure Functions, SignalR szolgáltatás](media/signalr-concept-azure-functions/signalr-cosmosdb-functions.png)

1. Módosítás történik egy Cosmos DB-gyűjteményben
2. A rendszer továbbítja a módosítási eseményt a Cosmos DB-változáscsatornának
3. A módosítási esemény aktiválja az Azure Functionst a Cosmos DB-eseményindítóval
4. A SignalR szolgáltatás kimeneti kötése közzétesz egy üzenetet a SignalR szolgáltatásban
5. A SignalR szolgáltatás elküldi az üzenet az összes csatlakoztatott ügyfélnek

### <a name="authentication-and-users"></a>Hitelesítés és felhasználók

A SignalR szolgáltatás lehetővé teszi, hogy üzeneteket továbbítson az összes ügyfélnek, vagy csak egy részüknek, például egy adott felhasználóhoz tartózó ügyfeleknek. A SignalR szolgáltatás Azure Functionshöz készült kötéseit kombinálni lehet az App Service-hitelesítéssel az olyan szolgáltatókkal rendelkező ügyfelek hitelesítésére, mint az Azure Active Directory, a Facebook és a Twitter. Ezután közvetlenül küldhet üzeneteket ezeknek a hitelesített felhasználóknak.

## <a name="next-steps"></a>További lépések

Ez a cikk áttekintést nyújtott arról, hogyan használható az Azure Functions és a SignalR szolgáltatás számos különböző kiszolgáló nélküli, valós idejű üzenetküldési forgatókönyv engedélyezésére.

További részletek az Azure Functions és a SignalR Service együttes használatával olvassa el az alábbi útmutatóban.

* [Az Azure Functions fejlesztői és SignalR Service-konfiguráció](signalr-concept-serverless-development-config.md)

További információért kövesse az alábbi rövid útmutatók egyikét.

* [Kiszolgáló nélküli Azure SignalR – rövid útmutató – C#](signalr-quickstart-azure-functions-csharp.md)
* [Kiszolgáló nélküli Azure SignalR – rövid útmutató – JavaScript](signalr-quickstart-azure-functions-javascript.md)