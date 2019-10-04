---
title: Valós idejű alkalmazások létrehozása a Azure Functions és az Azure Signaler szolgáltatással
description: Az Azure Signaler szolgáltatás kiszolgáló nélküli alkalmazásokban való használatának áttekintése.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 6a71647d452597c84e0df1beb6e9c6c3932d171b
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639927"
---
# <a name="build-real-time-apps-with-azure-functions-and-azure-signalr-service"></a>Valós idejű alkalmazások létrehozása a Azure Functions és az Azure Signaler szolgáltatással

Mivel az Azure SignalR szolgáltatás és az Azure Functions teljeskörűen felügyelt, nagymértékben skálázható szolgáltatások, amelyek lehetővé teszik, hogy az infrastruktúra-kezelés helyett az alkalmazások létrehozására összpontosítson, gyakran használják együtt őket, hogy valós idejű kommunikációt biztosítsanak a [kiszolgáló nélküli](https://azure.microsoft.com/solutions/serverless/) környezetekben.

> [!NOTE]
> Ismerkedjen meg a Signaler és a Azure Functions együttes használatával az interaktív oktatóanyagban, amely [lehetővé teszi az automatikus frissítések használatát egy webalkalmazásban a Azure functions és a signaler szolgáltatás használatával](https://docs.microsoft.com/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr).

## <a name="integrate-real-time-communications-with-azure-services"></a>Valós idejű kommunikáció integrálása Azure-szolgáltatásokkal

Azure Functions lehetővé teszi, hogy [különböző nyelveken](../azure-functions/supported-languages.md), például a JavaScript, a Python C#és a Java nyelven is írjon kódot, amely a felhőben előforduló események bekövetkezésekor aktiválódik. Ilyen események például a következők:

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

A Azure Functions és a Signaler szolgáltatás használatával kapcsolatos részletes információkért tekintse meg a következő forrásokat:

* [Azure Functions fejlesztés és konfigurálás a Signaler szolgáltatással](signalr-concept-serverless-development-config.md)
* [Automatikus frissítések engedélyezése webalkalmazásokban a Azure Functions és a Signaler szolgáltatás használatával](https://docs.microsoft.com/learn/modules/automatic-update-of-a-webapp-using-azure-functions-and-signalr)

További információért kövesse az alábbi rövid útmutatók egyikét.

* [Kiszolgáló nélküli Azure SignalR – rövid útmutató – C#](signalr-quickstart-azure-functions-csharp.md)
* [Kiszolgáló nélküli Azure SignalR – rövid útmutató – JavaScript](signalr-quickstart-azure-functions-javascript.md)
