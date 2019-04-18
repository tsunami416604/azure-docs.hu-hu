---
title: Azure Cosmos DB változáscsatorna az Azure Functions használatával
description: Az Azure Cosmos DB módosításához használja a hírcsatorna az Azure Functions használatával
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/12/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 35639dac0eacd5eae04b7848bdbbc1bc30fbf214
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680774"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Az Azure Cosmos DB és az Azure Functions kiszolgáló nélküli, eseményalapú architektúrákkal

Az Azure Functions biztosítja a legegyszerűbb módja a csatlakozás a [módosításcsatornáját](). Kis reaktív Azure Functions, amely automatikusan aktiválódik az Azure Cosmos-tároló módosítási hírcsatorna szereplő minden egyes új esemény is létrehozhat.

![Kiszolgáló nélküli eseményvezérelt funkciók használata az Azure Cosmos DB-eseményindító](./media/change-feed-functions/functions.png)

Az a [Azure Cosmos DB-eseményindító](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger), használhatja a [Változáscsatorna feldolgozói](./change-feed-processor.md)a méretezés és megbízható esemény észlelési funkciók anélkül, hogy fenn kellene [feldolgozó infrastruktúra](./change-feed-processor.md#implementing-the-change-feed-processor-library). Csak az Azure-függvény logikára koncentrálhat az események forráskezelése folyamatot a többi bajlódnia. Az eseményindító más akár kombinálhatja [Azure Functions-bindings](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Jelenleg az Azure Cosmos DB-eseményindító használható együtt a Core (SQL) API-t csak támogatott.

## <a name="requirements"></a>Követelmények

Egy kiszolgáló nélküli, eseményalapú folyamat implementálásához szükségesek:

* **A figyelt tároló**: A figyelt tároló a figyelt Azure Cosmos-tárolót, és tárolja az adatokat, amelyről a módosítási hírcsatorna jön létre. Bármely beszúrások és a figyelt tárolóra (pl. CRUD) módosításait a módosítási hírcsatorna a tároló is megjelennek.
* **A bérlet tároló**: A bérlet tároló az állapot több tartja karban, és dinamikus kiszolgáló nélküli Azure-függvény példányt, és lehetővé teszi a dinamikus méretezést. A bérlet tároló manuálisan vagy automatikusan létrehozhat az Azure Cosmos DB Trigger.To automatikusan hozza létre a bérlet tárolót, állítsa be a *CreateLeaseCollectionIfNotExists* a jelzőt a [konfigurációs](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Particionált címbérlet-tárolók szükség, hogy egy `/id` kulcsdefiníció particionálásához.

## <a name="create-your-azure-cosmos-db-trigger"></a>Az Azure Cosmos DB-eseményindító létrehozása

Az Azure-függvény létrehozása az Azure Cosmos DB-eseményindító mostantól támogatott az Azure Functions IDE, és a CLI Integrációk:

* [A Visual Studio-bővítmény](../azure-functions/functions-develop-vs.md) Visual Studio-felhasználók számára.
* [A Visual Studio-Core bővítmény](https://code.visualstudio.com/tutorials/functions-extension/create-function) Visual Studio Code-felhasználók számára.
* Végül [Core parancssori eszközök](../azure-functions/functions-run-local.md#create-func) többplatformos IDE független biztosítása érdekében.

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>Az Azure Cosmos DB-eseményindító helyileg történő futtatása

Futtathatja a [helyileg az Azure-függvény](../azure-functions/functions-develop-local.md) együtt a [Azure Cosmos DB Emulatort](./local-emulator.md) hozhat létre és fejleszthet egy Azure-előfizetésre vagy szolgáltatásért nélkül a kiszolgáló nélküli eseményvezérelt folyamatok.

Ha az szeretne élő forgatókönyvek tesztelése a felhőben, [Cosmos DB ingyenes kipróbálása](https://azure.microsoft.com/try/cosmosdb/) hitelkártya és Azure-előfizetés szükséges nélkül.

## <a name="next-steps"></a>További lépések

További információ a következő cikkekben változáscsatorna most már folytathatja:

* [Módosítási hírcsatorna áttekintése](change-feed.md)
* [A módosítási hírcsatornáról olvasási módjai](read-change-feed.md)
* [Feldolgozói kódtára használatával módosítási hírcsatorna](change-feed-processor.md)
* [Hogyan használható a módosítási hírcsatorna feldolgozói kódtára](change-feed-processor.md)
* [Azure Cosmos DB és az Azure Functions használatával kiszolgáló nélküli adatbázis-használat](serverless-computing-database.md)
