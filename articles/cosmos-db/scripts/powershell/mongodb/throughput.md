---
title: PowerShell-parancsfájlok átviteli sebesség (RU/s) műveletekhez az Azure Cosmos DBs API MongoDB
description: PowerShell-parancsfájlok átviteli sebesség (RU/s) műveletekhez az Azure Cosmos DBs API MongoDB
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 10/07/2020
ms.author: mjbrown
ms.openlocfilehash: c6c7f285816f290c70c4a5008adcabec02988e7e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097379"
---
# <a name="throughput-rus-operations-with-powershell-for-a-database-or-collection-for-azure-cosmos-db-api-for-mongodb"></a>Átviteli sebesség (RU/s) műveletek a PowerShell-lel a MongoDB Azure Cosmos DB API-hoz tartozó adatbázishoz vagy gyűjteményhez
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="get-throughput"></a>Átviteli sebesség lekérdezése

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-ru-get.ps1 "Get throughput on a database or collection for Azure Cosmos DB API for MongoDB")]

## <a name="update-throughput"></a>Frissítési sebesség

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-ru-update.ps1 "Update throughput on a database or collection for Azure Cosmos DB API for MongoDB")]

## <a name="migrate-throughput"></a>Átviteli sebesség áttelepíteni

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/mongodb/ps-mongodb-ru-migrate.ps1 "Migrate between standard and autoscale throughput on a database or collection for Azure Cosmos DB API for MongoDB")]

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
| [Get-AzCosmosDBMongoDBDatabaseThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbmongodbdatabasethroughput) | Lekéri a megadott Azure Cosmos DB API MongoDB-adatbázis átviteli sebességét. |
| [Get-AzCosmosDBMongoDBCollectionThroughput](/powershell/module/az.cosmosdb/get-azcosmosdbmongodbcollectionthroughput) | Lekéri a megadott Azure Cosmos DB API MongoDB-gyűjteményhez tartozó átviteli sebességét. |
| [Frissítés – AzCosmosDBMongoDBDatabaseThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbmongodbdatabasethroughput) | Frissíti a Azure Cosmos DB API MongoDB-adatbázis átviteli értékét. |
| [Frissítés – AzCosmosDBMongoDBCollectionThroughput](/powershell/module/az.cosmosdb/update-azcosmosdbmongodbcollectionthroughput) | Frissíti a MongoDB-gyűjteményhez tartozó Azure Cosmos DB API átviteli sebesség értékét. |
| [Meghívás – AzCosmosDBMongoDBDatabaseThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbmongodbdatabasethroughputmigration) | Áttelepíthet egy Azure Cosmos DB API átviteli sebességét a MongoDB-gyűjteményhez. |
| [Meghívás – AzCosmosDBMongoDBCollectionThroughputMigration](/powershell/module/az.cosmosdb/invoke-azcosmosdbmongodbcollectionthroughputmigration) | Áttelepíthet egy Azure Cosmos DB API átviteli sebességét a MongoDB-gyűjteményhez. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).