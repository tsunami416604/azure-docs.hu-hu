---
title: Az Azure Cosmos DB módosítási hírcsatornájának használata az Azure Functions segítségével
description: Az Azure Functions használatával csatlakozhat az Azure Cosmos DB módosítási hírcsatornához. Később hozhat létre reaktív Azure-függvények, amelyek minden új esemény.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a74635551d8416bf60689b1f1403f29883e81bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851373"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Kiszolgáló nélküli eseményalapú architektúrák az Azure Cosmos DB-vel és az Azure Functions funkcióval

Az Azure Functions a legegyszerűbb módja a [módosítási hírcsatornához](change-feed.md)való csatlakozásnak. Létrehozhat kis reaktív Azure-függvényeket, amelyek automatikusan aktiválódnak az Azure Cosmos-tároló módosítási hírcsatornájában minden új eseményen.

![Kiszolgáló nélküli eseményalapú függvények, amelyek a Cosmos DB Azure Functions eseményindítójával dolgoznak](./media/change-feed-functions/functions.png)

A [Cosmos DB Azure Functions eseményindítójával](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)kihasználhatja a [Változáscsatorna-feldolgozó](./change-feed-processor.md)méretezését és a megbízható eseményészlelési funkciót anélkül, hogy bármilyen [feldolgozói infrastruktúrát](./change-feed-processor.md)kellene fenntartania. Csak összpontosítson az Azure-függvény logikájára anélkül, hogy aggódnia kellene az eseményforrás-forrásfolyamat többi része miatt. Az eseményindítót más [Azure Functions-kötésekkel is keverheti.](../azure-functions/functions-triggers-bindings.md#supported-bindings)

> [!NOTE]
> Jelenleg az Azure Functions eseményindító a Cosmos DB csak a Core (SQL) API-val használható.

## <a name="requirements"></a>Követelmények

Kiszolgáló nélküli eseményalapú folyamat megvalósításához a következőkre van szükség:

* **A figyelt tároló:** A figyelt tároló az Azure Cosmos-tároló figyelt, és tárolja az adatokat, amelyekből a változáscsatorna jön létre. A figyelt tároló minden lapka, a figyelt tároló frissítései a tároló módosítási hírcsatornájában jelennek meg.
* **A bérleti tároló:** A bérleti tároló több és dinamikus kiszolgáló nélküli Azure-függvénypéldányok állapotát tartja fenn, és lehetővé teszi a dinamikus skálázást. Ez a bérleti tároló manuálisan vagy automatikusan létrehozható az Azure Functions eseményindító a Cosmos DB. A címbérlet-tároló automatikus létrehozásához állítsa be a *CreateLeaseCollectionIfNotExists* jelzőt a [konfigurációban.](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) Particionált címbérlet-tárolók van szükség, hogy egy `/id` partíciókulcs-definíció.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Az Azure Functions eseményindító létrehozása a Cosmos DB számára

Az Azure-függvény létrehozása a Cosmos DB Azure Functions eseményindítójával mostantól az összes Azure Functions IDE- és CLI-integrációban támogatott:

* [Visual Studio-bővítmény](../azure-functions/functions-develop-vs.md) Visual Studio-felhasználók számára.
* [Visual Studio kódbővítmény](/azure/javascript/tutorial-vscode-serverless-node-01) visual studio-kód felhasználók számára.
* És végül [Core CLI szerszámozás](../azure-functions/functions-run-local.md#create-func) egy cross-platform IDE agnosztikus élményt.

## <a name="run-your-trigger-locally"></a>Az eseményindító helyi futtatása

Az [Azure-függvényt helyileg](../azure-functions/functions-develop-local.md) futtathatja az [Azure Cosmos DB-emulátorral,](./local-emulator.md) hogy azure-előfizetés nélkül hozhassa létre és fejlessze a kiszolgáló nélküli eseményalapú folyamatokat, illetve költségekkel járjon.

Ha szeretné tesztelni az élő forgatókönyvek a felhőben, [kipróbálhatja cosmos DB ingyenes](https://azure.microsoft.com/try/cosmosdb/) hitelkártya vagy Azure-előfizetés szükséges nélkül.

## <a name="next-steps"></a>További lépések

A módosítási hírcsatornáról a következő cikkekben olvashat bővebben:

* [A módosítási hírcsatorna áttekintése](change-feed.md)
* [A módosítási hírcsatorna olvasásának módjai](read-change-feed.md)
* [A módosítási hírcsatorna-kódtár használata](change-feed-processor.md)
* [A módosítási hírcsatorna-processzorkönyvtár használata](change-feed-processor.md)
* [Kiszolgáló nélküli adatbázis-számítástechnika az Azure Cosmos DB és az Azure Functions használatával](serverless-computing-database.md)
