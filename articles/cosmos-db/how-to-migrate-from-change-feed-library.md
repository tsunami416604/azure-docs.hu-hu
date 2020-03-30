---
title: Áttelepítés a változáscsatorna-feldolgozókönyvtárról az Azure Cosmos DB .NET V3 SDK-ba
description: Ismerje meg, hogyan telepítheti át az alkalmazást a változáscsatorna-feldolgozókód-könyvtárból az Azure Cosmos DB SDK V3-ba
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: 9570a8512e3437b12ecce2ef0c708a74a8806482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77588883"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Áttelepítés a változáscsatorna-feldolgozókönyvtárról az Azure Cosmos DB .NET V3 SDK-ba

Ez a cikk azokat a szükséges lépéseket ismerteti, amelyek a [változáscsatorna-processzorkönyvtárat](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) a .NET SDK (más néven .NET V3 SDK) [módosítási hírcsatorna](change-feed.md) szolgáltatására használó meglévő alkalmazáskódjának áttelepítéséhez szükségesek.

## <a name="required-code-changes"></a>Szükséges kódmódosítások

A .NET V3 SDK számos törési módosítást hajt végre, az alkalmazás áttelepítésének legfontosabb lépései az alábbiak:

1. A `DocumentCollectionInfo` példányok `Container` átalakítása a figyelt és a lízingeléstárolók hivatkozásaivá.
1. `WithProcessorOptions` A használt testreszabásokat frissíteni `WithLeaseConfiguration` kell `WithPollInterval` a használathoz és az intervallumokhoz, `WithStartTime` [a kezdési időponthoz](how-to-configure-change-feed-start-time.md)és `WithMaxItems` a maximális cikkszám meghatározásához.
1. Állítsa `processorName` be `GetChangeFeedProcessorBuilder` a be, `ChangeFeedProcessorOptions.LeasePrefix`hogy megfeleljen `string.Empty` a konfigurált érték , vagy használja másképp.
1. A módosítások már nem `IReadOnlyList<Document>`lesznek kézbesítve, `IReadOnlyCollection<T>` hanem `T` egy olyan típus, amelyet meg kell határoznia, már nincs alap cikkosztály.
1. A módosítások kezeléséhez már nincs szükség megvalósításra, hanem meghatalmazottat kell [definiálnia.](change-feed-processor.md#implementing-the-change-feed-processor) A delegált lehet statikus függvény, vagy ha a végrehajtások között állapotot kell fenntartania, létrehozhatja saját osztályát, és átadhat egy példánymetódust delegáltként.

Ha például a változáscsatorna-processzor létrehozásának eredeti kódja a következőképpen néz ki:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

Az áttelepített kód így fog kinézni:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

És a delegált, lehet statikus módszer:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>Állami és lízingkonténer

A változáscsatorna-processzorkönyvtárhoz hasonlóan a .NET V3 SDK módosítási hírcsatorna-szolgáltatása [is egy címbérlet-tárolót](change-feed-processor.md#components-of-the-change-feed-processor) használ az állapot tárolásához. A sémák azonban eltérőek.

Az SDK V3 változáscsatorna-processzor észleli a régi könyvtárállapotokat, és az áttelepített alkalmazáskód első végrehajtásakor automatikusan áttelepíti azt az új sémába. 

Biztonságosan leállíthatja az alkalmazást a régi kód használatával, áttelepítheti a kódot az új verzióra, elindíthatja az áttelepített alkalmazást, és az alkalmazás leállítása során történt módosításokat az új verzió felveszi és feldolgozza.

> [!NOTE]
> A függvénytárat használó alkalmazásokból a .NET V3 SDK-ba való áttelepítés egyirányú, mivel az állapot (bérletek) átkerülnek az új sémába. Az áttelepítés nem visszamenőlegesen kompatibilis.


## <a name="additional-resources"></a>További források

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Használati minták a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [További minták a GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>További lépések

Most a következő cikkekben olvashat bővebben a hírcsatorna-feldolgozó módosításáról:

* [A módosítási hírcsatorna-processzor áttekintése](change-feed-processor.md)
* [A változáscsatorna-becslő használata](how-to-use-change-feed-estimator.md)
* [Változáscsatorna-feldolgozó indításának időpontja](how-to-configure-change-feed-start-time.md)
