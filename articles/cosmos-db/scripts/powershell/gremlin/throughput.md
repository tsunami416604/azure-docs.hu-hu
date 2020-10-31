---
title: PowerShell-parancsfájlok az átviteli sebesség (RU/s) műveleteihez Azure Cosmos DB Gremlin API-hoz
description: PowerShell-parancsfájlok az átviteli sebesség (RU/s) műveleteihez a Gremlin API-hoz
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: 53e427b77066d1f1833ce12f856e4157a51ec530
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93075534"
---
# <a name="throughput-rus-operations-with-powershell-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>Átviteli sebesség (RU/s) műveletek a PowerShell-lel a Azure Cosmos DB-Gremlin API-hoz készült adatbázishoz vagy gráfhoz
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="get-throughput"></a>Átviteli sebesség lekérdezése

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-get.ps1 "Get throughput on a database or graph for Gremlin API")]

## <a name="update-throughput"></a>Frissítési sebesség

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-update.ps1 "Update throughput on a database or graph for Gremlin API")]

## <a name="migrate-throughput"></a>Átviteli sebesség áttelepíteni

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a database or graph for Gremlin API")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
|**Azure Cosmos DB**| |
| [Get-AzCosmosDBGremlinDatabaseThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbgremlindatabasethroughput) | Lekéri a Gremlin API-adatbázis átviteli sebesség értékét. |
| [Get-AzCosmosDBGremlinGraphThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbgremlingraphthroughput) | Beolvassa a Gremlin API gráf átviteli sebesség értékét. |
| [Frissítés – AzCosmosDBGremlinDatabaseThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbgremlindatabasethroughput) | Frissíti a Gremlin API-adatbázis átviteli sebesség értékét. |
| [Frissítés – AzCosmosDBGremlinGraphThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbgremlingraphthroughput) | Frissíti a Gremlin API Graph átviteli sebesség értékét. |
| [Meghívás – AzCosmosDBGremlinDatabaseThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbgremlindatabasethroughputmigration) | Gremlin API-adatbázis átviteli sebességének migrálása. |
| [Meghívás – AzCosmosDBGremlinGraphThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbgremlingraphthroughputmigration) | Gremlin API-gráf átviteli sebességének migrálása. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).