---
title: Migrálás a Change feed Processor Library-ről a Azure Cosmos DB .NET v3 SDK-ra
description: Megtudhatja, hogyan migrálhatja az alkalmazást a refeed Processor Library módosítása a Azure Cosmos DB SDK v3 verzióra
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: f651beb181430f65d0b4c86f285e74958f8366eb
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588883"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migrálás a Change feed Processor Library-ről a Azure Cosmos DB .NET v3 SDK-ra

Ez a cikk azokat a szükséges lépéseket ismerteti, amelyekkel áttelepítheti a meglévő alkalmazás kódját, amely a a .NET SDK legújabb verziójában (más néven .NET v3 SDK) módosítja a [hírcsatorna-feldolgozó függvénytárának](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) módosítása funkciót a [változási](change-feed.md) hírcsatorna szolgáltatásban.

## <a name="required-code-changes"></a>Szükséges kód módosításai

A .NET v3 SDK több feltörési változást is tartalmaz, az alábbi lépésekkel telepítheti át az alkalmazást:

1. Alakítsa át a `DocumentCollectionInfo` példányokat a figyelt és a bérletekhez tartozó tárolók `Container` hivatkozásaiba.
1. Az `WithProcessorOptions`t használó testreszabásokat frissíteni kell, hogy a `WithLeaseConfiguration` és a `WithPollInterval` intervallumokat, `WithStartTime` [a kezdési időt](how-to-configure-change-feed-start-time.md), és `WithMaxItems` a maximális elemek számának meghatározásához.
1. Állítsa be a `GetChangeFeedProcessorBuilder` `processorName` a `ChangeFeedProcessorOptions.LeasePrefix`konfigurált értékének megfelelően, vagy használja a `string.Empty`.
1. A módosítások már nem `IReadOnlyList<Document>`ként lesznek továbbítva, hanem egy `IReadOnlyCollection<T>`, ahol a `T` típusnak meg kell határoznia, nincs alapelem osztálya.
1. A módosítások kezeléséhez már nincs szükség megvalósításra, hanem meg kell [adnia egy delegált](change-feed-processor.md#implementing-the-change-feed-processor). A delegált lehet statikus függvény, vagy ha meg kell őriznie az állapotot a végrehajtások között, létrehozhat egy saját osztályt, és delegált néven is átadhat egy példány-metódust.

Ha például az eredeti kód a módosítási hírcsatorna processzorának összeállítására szolgál, a következőképpen néz ki:

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="ChangeFeedProcessorLibrary":::

Az áttelepített kód a következőképpen fog kinézni:

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="ChangeFeedProcessorMigrated":::

A delegált pedig statikus metódus lehet:

:::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs" id="Delegate":::

## <a name="state-and-lease-container"></a>Állapot-és bérlet tároló

A Restore Processor Library-hez hasonlóan a .NET v3 SDK-ban a hírcsatorna módosítása szolgáltatás egy [bérlet tárolót](change-feed-processor.md#components-of-the-change-feed-processor) használ az állapot tárolásához. A sémák azonban eltérőek.

Az SDK v3 változási hírcsatorna-feldolgozó processzora észlelni fogja a régi függvénytár-állapotot, és automatikusan áttelepíti az új sémára az áttelepített alkalmazás kódjának első végrehajtásakor. 

A régi kóddal biztonságosan leállíthatja az alkalmazást, áttelepítheti a kódot az új verzióra, elindíthatja az áttelepített alkalmazást, és az alkalmazás leállításakor bekövetkezett változásokat az új verzió fogja felvenni és feldolgozni.

> [!NOTE]
> A kódtárat a .NET v3 SDK-val használó alkalmazásokból való Migrálás egyirányú, mivel az állapot (bérletek) át lesz telepítve az új sémába. Az áttelepítés visszafelé nem kompatibilis.


## <a name="additional-resources"></a>További források

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [Használati minták a GitHubon](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [További minták a GitHubon](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Következő lépések

A következő cikkekben további tudnivalókat olvashat a hírcsatorna-feldolgozó szolgáltatással kapcsolatos változásokról:

* [A hírcsatorna-feldolgozó változásának áttekintése](change-feed-processor.md)
* [A Change feed kalkulátor használata](how-to-use-change-feed-estimator.md)
* [Hírcsatorna-processzor kezdő időpontjának módosítása](how-to-configure-change-feed-start-time.md)
