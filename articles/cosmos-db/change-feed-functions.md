---
title: Azure Cosmos DB változási hírcsatorna használata a Azure Functions
description: Azure Cosmos DB módosítási hírcsatorna használata a Azure Functions
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: db3f1b6657ae455ae049eaffd6758fc7e6944fb9
ms.sourcegitcommit: 6b41522dae07961f141b0a6a5d46fd1a0c43e6b2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68001025"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Kiszolgáló nélküli eseményvezérelt architektúrák Azure Cosmos DB és Azure Functions

A Azure Functions biztosítja a legegyszerűbb módot a változási [csatornához](change-feed.md)való kapcsolódásra. Létrehozhat olyan kis reaktív Azure Functions, amelyek automatikusan aktiválódik az Azure Cosmos-tároló változási csatornájának minden új eseményén.

![A Azure Cosmos DB eseményindítóval működő kiszolgáló nélküli eseményvezérelt függvények](./media/change-feed-functions/functions.png)

A [Azure Cosmos db eseményindító](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger)használatával a feldolgozó [infrastruktúra](./change-feed-processor.md)karbantartásának szükségessége nélkül kihasználhatja a [változási csatorna](./change-feed-processor.md)méretezését és a megbízható események észlelési funkcióját. Csak az Azure-függvény logikáját kell összpontosítania anélkül, hogy aggódnia kellene az Event-beszerzés folyamat többi részével. Az triggert más [Azure functions kötésekkel](../azure-functions/functions-triggers-bindings.md#supported-bindings)is összekeverheti.

> [!NOTE]
> Jelenleg a Azure Cosmos DB trigger csak a Core (SQL) API-val használható.

## <a name="requirements"></a>Követelmények

Kiszolgáló nélküli eseményvezérelt folyamat megvalósításához a következők szükségesek:

* **A figyelt tároló**: A figyelt tároló a figyelt Azure Cosmos-tároló, amely a változási csatornát generáló adatok tárolására szolgál. A figyelt tárolóba tartozó lapkák és módosítások (például a szifilisz) megjelennek a tárolóban.
* **A bérlet tárolója**: A címbérleti tároló több és dinamikus kiszolgáló nélküli Azure functions-példányon tárolja az állapotot, és lehetővé teszi a dinamikus skálázást. Ez a címbérleti tároló manuálisan vagy automatikusan hozható létre a Azure Cosmos DB Trigger.To automatikusan létrehozza a címbérleti tárolót, állítsa be a *CreateLeaseCollectionIfNotExists* jelzőt a [konfigurációban](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Particionált bérletű tárolók szükségesek a `/id` partíciós kulcs definíciójának létrehozásához.

## <a name="create-your-azure-cosmos-db-trigger"></a>Azure Cosmos DB trigger létrehozása

Az Azure-függvény Azure Cosmos DB triggerrel való létrehozása mostantól támogatott az összes Azure Functions IDE és CLI-integrációban:

* Visual Studio- [bővítmény](../azure-functions/functions-develop-vs.md) a Visual Studio-felhasználók számára.
* [Visual Studio Core-bővítmény](https://code.visualstudio.com/tutorials/functions-extension/create-function) a Visual Studio Code-felhasználók számára.
* És végül a [CLI-eszközök](../azure-functions/functions-run-local.md#create-func) alapszintű, többplatformos ide-alapú felhasználói élményhez.

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>Azure Cosmos DB-trigger helyi futtatása

Az [Azure-függvényt helyileg](../azure-functions/functions-develop-local.md) is futtathatja a [Azure Cosmos db emulátor](./local-emulator.md) használatával, amely Azure-előfizetés nélkül hozhat létre és fejleszthet kiszolgáló nélküli eseményvezérelt folyamatokat, vagy költségekkel jár.

Ha élő forgatókönyveket szeretne tesztelni a felhőben, akkor [ingyenesen](https://azure.microsoft.com/try/cosmosdb/) kipróbálhatja a Cosmos DBT bankkártyás vagy Azure-előfizetés nélkül.

## <a name="next-steps"></a>További lépések

A következő cikkekben továbbra is megismerheti a hírcsatornák változását:

* [A hírcsatorna változásának áttekintése](change-feed.md)
* [A módosítási csatorna olvasási módjai](read-change-feed.md)
* [Feldolgozói kódtára használatával módosítási hírcsatorna](change-feed-processor.md)
* [A Change feed Processor Library használata](change-feed-processor.md)
* [Kiszolgáló nélküli adatbázis-számítástechnika Azure Cosmos DB és Azure Functions használatával](serverless-computing-database.md)
