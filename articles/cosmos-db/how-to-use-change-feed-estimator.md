---
title: A változáscsatorna-becslő használata - Azure Cosmos DB
description: További információ a változáscsatorna-becslő használatával a változáscsatorna-processzor előrehaladásának elemzéséhez
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: maquaran
ms.openlocfilehash: 0023f68400b36b9abd3b9d4a789895e79f67aa03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77585288"
---
# <a name="use-the-change-feed-estimator"></a>A változáscsatorna-becslő használata

Ez a cikk azt ismerteti, hogyan figyelheti a [változáscsatorna-processzorpéldányok](./change-feed-processor.md) előrehaladását a módosítási hírcsatorna olvasása során.

## <a name="why-is-monitoring-progress-important"></a>Miért fontos az előrehaladás nyomon követése?

A változáscsatorna-processzor mutatóként működik, amely előrehalad a [módosítási hírcsatornában,](./change-feed.md) és végrehajtja a meghatalmazotti implementáció módosításait. 

A változáscsatorna-processzor központi telepítése a rendelkezésre álló erőforrások , például a processzor, a memória, a hálózat és így tovább alapján egy adott ütemben feltudja dolgozni a módosításokat.

Ha ez az arány lassabb, mint az Azure Cosmos-tárolóban végrehajtott módosítások aránya, a processzor lemarad.

Ez a forgatókönyv azonosítása segít megérteni, ha a változáscsatorna-feldolgozó üzembe helyezését kell méreteznünk.

## <a name="implement-the-change-feed-estimator"></a>A változáscsatorna-becslő végrehajtása

A [változásadagolási processzorhoz](./change-feed-processor.md)hasonlóan a változáscsatorna-becslő is leküldéses modellként működik. A becses méri az utolsó feldolgozott cikk (a bérlettároló állapota által meghatározott) és a tároló legutóbbi módosítása közötti különbséget, és lenyomja ezt az értéket delegáltnak. A mérés idében történő mérési időköz is testreszabható 5 másodperces alapértelmezett értékkel.

Ha például a változáscsatorna-processzor a következőképpen van definiálva:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

A helyes módszer a becslő inicializálására annak mérésére, hogy a processzor a következőképpen lenne használva: `GetChangeFeedEstimatorBuilder`

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

Ha mind a feldolgozó, mind `leaseContainer` a becses ugyanaz a neve.

A másik két paraméter a delegálás, amely egy számot kap, amely azt jelzi, hogy hány módosítás van függőben a processzor által **beolvasandó,** és milyen időintervallumban szeretné végrehajtani ezt a mérést.

A becslést fogadó meghatalmazott például a következő:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

Ezt a becslést elküldheti a figyelési megoldásnak, és felhasználhatja annak megértéséhez, hogyan viselkedik a folyamat az idő múlásával.

> [!NOTE]
> A változáscsatorna-becslőt nem kell a változáscsatorna-feldolgozó részeként telepíteni, és nem is lehet ugyanannak a projektnek a része. Lehet független, és egy teljesen más példányban futtatható. Csak ugyanazt a nevet és bérletkonfigurációt kell használnia.

## <a name="additional-resources"></a>További források

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Használati minták a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [További minták a GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>További lépések

Most a következő cikkekben olvashat bővebben a hírcsatorna-feldolgozó módosításáról:

* [A módosítási hírcsatorna-processzor áttekintése](change-feed-processor.md)
* [Változáscsatorna-feldolgozó indításának időpontja](how-to-configure-change-feed-start-time.md)
