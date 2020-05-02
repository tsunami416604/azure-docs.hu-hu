---
title: PowerShell-szkript Azure Cosmos DB SQL API-adatbázis és-tároló létrehozásához
description: Azure PowerShell script-Azure Cosmos DB SQL API-adatbázis és-tároló létrehozása
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/01/2020
ms.author: mjbrown
ms.openlocfilehash: 434cc6df326a0028f327c6d9e2eda0d19989ca01
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82653167"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-api"></a>Adatbázis és tároló létrehozása a Azure Cosmos DB-SQL API-hoz

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

Ez a szkript létrehoz egy Cosmos-fiókot az SQL (Core) API-hoz két régióban a munkamenet-szintű konzisztencia, egy adatbázis és egy partíciós kulccsal rendelkező tároló, egyéni indexelési házirend, egyedi kulcs házirend, TTL, dedikált átviteli sebesség és utolsó író WINS-ütközés-feloldási szabályzattal, amely a `multipleWriteLocations=true`következő esetekben lesz használatban:.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL API")]

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
| [Új – AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Új Cosmos DB-fiók létrehozása. |
| [Set-AzCosmosDBSqlDatabase](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqldatabase) | Létrehoz egy újat, vagy frissít egy meglévő Cosmos DB SQL Database. |
| [Új – AzCosmosDBSqlUniqueKey](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekey) | Létrehoz egy új Cosmos DB SQL PSSqlUniqueKey objektumot, amelyet a New-AzCosmosDBSqlUniqueKeyPolicy paraméterként használ. |
| [Új – AzCosmosDBSqlUniqueKeyPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekeypolicy) | Létrehoz egy új Cosmos DB SQL PSSqlUniqueKeyPolicy objektumot a set-AzCosmosDBSqlContainer paraméterként. |
| [Új – AzCosmosDBSqlCompositePath](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlcompositepath) | Létrehoz egy új Cosmos DB SQL PSCompositePath objektumot, amelyet a New-AzCosmosDBSqlIndexingPolicy paraméterként használ. |
| [Új – AzCosmosDBSqlIncludedPathIndex](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpathindex) | Létrehoz egy PSIndexes típusú új objektumot, amely a set-AzCosmosDBSqlIncludedPath paraméterként használható. |
| [Új – AzCosmosDBSqlIncludedPath](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpath) | Egy új, PSIncludedPath típusú objektumot hoz létre, amely a New-AzCosmosDBSqlIndexingPolicy paraméterként van használatban. |
| [Új – AzCosmosDBSqlIndexingPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Létrehoz egy PSSqlIndexingPolicy típusú új objektumot, amely a set-AzCosmosDBSqlContainer paraméterként használható. |
| [Új – AzCosmosDBSqlConflictResolutionPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlconflictresolutionpolicy) | Létrehoz egy PSSqlConflictResolutionPolicy típusú új objektumot, amely a set-AzCosmosDBSqlContainer paraméterként használható. |
| [Set-AzCosmosDBSqlContainer](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqlcontainer) | Új vagy meglévő Cosmos DB SQL-tároló frissítésének létrehozása vagy frissítése. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../../../powershell-samples.md) találhat.
