---
title: A Change feed kalkulátor használata – Azure Cosmos DB
description: Ismerje meg, hogy miként elemezheti a változási hírcsatorna-feldolgozók állapotát a Change feed kalkulátor használatával
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 08/15/2019
ms.author: maquaran
ms.openlocfilehash: 582b0a586f830659bbea4dd665be938d2895de47
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69544830"
---
# <a name="use-the-change-feed-estimator"></a>A módosítási hírcsatorna kalkulátor használata

Ez a cikk azt ismerteti, hogyan figyelheti meg a [változási hírcsatorna processzor](./change-feed-processor.md) példányainak állapotát, ahogy beolvasták a változási csatornát.

## <a name="why-is-monitoring-progress-important"></a>Miért fontos a figyelési folyamat?

A Change feed processzor olyan mutatóként működik, amely a változási [csatornán](./change-feed.md) halad előre, és a delegált implementációban végez módosításokat. 

A módosítási hírcsatorna-feldolgozó üzembe helyezése az elérhető erőforrások, például a processzor, a memória, a hálózat stb. alapján feldolgozhatja a módosításokat egy adott sebességgel.

Ha ez a sebesség lassabb, mint amikor a módosítások a Azure Cosmos DB tárolóban történnek, a processzor a lemaradás után fog megtörténni.

Ennek a forgatókönyvnek az azonosítása segít megérteni, hogy szükség van-e a Change feed processzor üzembe helyezésének skálázására.

## <a name="implement-the-change-feed-estimator"></a>A Change feed kalkulátor implementálása

A [change feed processzorhoz](./change-feed-processor.md)hasonlóan a Change feed kalkulátor leküldéses modellként működik. A kalkulátor méri a legutóbb feldolgozott elem (a címbérletek állapota által meghatározott) és a tároló legutóbbi változása közötti különbséget, és leküldi ezt az értéket egy delegált értékre. A mérés elvégzésének időköze az alapértelmezett 5 másodperces értékkel is testreszabható.

Ha például a változási csatorna processzora a következőképpen van definiálva:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

A becslések inicializálásának helyes módja, ha azt méri, hogy a processzor `GetChangeFeedEstimatorBuilder` a következőképpen fog kinézni:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

Ahol a processzor és a kalkulátor is ugyanazt `leaseContainer` a nevet használja.

A másik két paraméter a delegált, amely egy számot fog kapni, amely azt jelzi, hogy hány **módosítást kell olvasni** a processzor, valamint azt az időintervallumot, amelyen a mérést el szeretné végezni.

A becslést fogadó delegált példa:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

Ezt a becslést elküldheti a figyelési megoldásnak, és azt is megtudhatja, hogyan viselkedik az előrehaladás az idő múlásával.

> [!NOTE]
> A módosítási hírcsatorna-kalkulátor nem szükséges a módosítási csatorna processzorának részeként telepíteni, és nem lehet ugyanannak a projektnek a része. Független lehet, és egy teljesen más példányban futtatható. Csak ugyanazt a nevet és címbérleti konfigurációt kell használnia.

## <a name="additional-resources"></a>További források

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Használati minták a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/usage/changefeed)
* [További minták a GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>További lépések

Folytassa további információ a változáscsatorna az alábbi cikkeket:

* [A hírcsatorna változásának áttekintése](change-feed.md)
* [A módosítási csatorna olvasási módjai](read-change-feed.md)
* [A csatorna módosítása folyamat használata](change-feed-processor.md)
