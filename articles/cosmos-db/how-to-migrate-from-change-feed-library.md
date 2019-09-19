---
title: Migrálás a Change feed Processor Library-ről a Azure Cosmos DB .NET v3 SDK-ra
description: Megtudhatja, hogyan migrálhatja az alkalmazást a refeed Processor Library módosítása a Azure Cosmos DB SDK v3 verzióra
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: maquaran
ms.openlocfilehash: 9570a8512e3437b12ecce2ef0c708a74a8806482
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077554"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>Migrálás a Change feed Processor Library-ről a Azure Cosmos DB .NET v3 SDK-ra

Ez a cikk azokat a szükséges lépéseket ismerteti, amelyekkel áttelepítheti a meglévő alkalmazás kódját, amely a a .NET SDK legújabb verziójában (más néven .NET v3 SDK) módosítja a [hírcsatorna-feldolgozó függvénytárának](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) módosítása funkciót a [változási](change-feed.md) hírcsatorna szolgáltatásban.

## <a name="required-code-changes"></a>Szükséges kód módosításai

A .NET v3 SDK több feltörési változást is tartalmaz, az alábbi lépésekkel telepítheti át az alkalmazást:

1. Alakítsa át `DocumentCollectionInfo` a `Container` példányokat a figyelt és a bérleti tárolók hivatkozásaira.
1. A használatban `WithProcessorOptions` lévő testreszabásokat a használat `WithLeaseConfiguration` és `WithPollInterval` az intervallumok, `WithStartTime` a [Kezdési idő](how-to-configure-change-feed-start-time.md)és `WithMaxItems` a maximális elemek számának meghatározására kell frissíteni.
1. `ChangeFeedProcessorOptions.LeasePrefix` `string.Empty` A on érték`GetChangeFeedProcessorBuilder` megadásával egyeztetheti a vagy más módon konfigurált értéket. `processorName`
1. A módosítások már nem `IReadOnlyList<Document>`kerülnek be a rendszerbe, hanem `IReadOnlyCollection<T>` az a hely, `T` ahol meg kell határozni a kívánt típust, már nincs alapelem osztálya.
1. A módosítások kezeléséhez már nincs szükség megvalósításra, hanem meg kell [adnia egy delegált](change-feed-processor.md#implementing-the-change-feed-processor). A delegált lehet statikus függvény, vagy ha meg kell őriznie az állapotot a végrehajtások között, létrehozhat egy saját osztályt, és delegált néven is átadhat egy példány-metódust.

Ha például az eredeti kód a módosítási hírcsatorna processzorának összeállítására szolgál, a következőképpen néz ki:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

Az áttelepített kód a következőképpen fog kinézni:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

A delegált pedig statikus metódus lehet:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

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

## <a name="next-steps"></a>További lépések

A következő cikkekben további tudnivalókat olvashat a hírcsatorna-feldolgozó szolgáltatással kapcsolatos változásokról:

* [A hírcsatorna-feldolgozó változásának áttekintése](change-feed-processor.md)
* [A Change feed kalkulátor használata](how-to-use-change-feed-estimator.md)
* [Hírcsatorna-processzor kezdő időpontjának módosítása](how-to-configure-change-feed-start-time.md)
