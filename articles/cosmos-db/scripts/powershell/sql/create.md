---
title: PowerShell-szkript Azure Cosmos DB SQL API-adatbázis és-tároló létrehozásához
description: Azure PowerShell script-Azure Cosmos DB SQL API-adatbázis és-tároló létrehozása
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 3a803ef8c85769e8995602b980ee3249f4edcce8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074573"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-api"></a>Adatbázis és tároló létrehozása a Azure Cosmos DB-SQL API-hoz
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

Ez a szkript létrehoz egy Cosmos-fiókot az SQL (Core) API-hoz két régióban a munkamenet-szintű konzisztencia, egy adatbázis és egy partíciós kulccsal rendelkező tároló, egyéni indexelési házirend, egyedi kulcs házirend, TTL, dedikált átviteli sebesség és utolsó író WINS-ütközés-feloldási szabályzattal, amely a következő esetekben lesz használatban: `multipleWriteLocations=true` .

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
| [Új – AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Létrehoz egy Cosmos DB fiókot. |
| [Új – AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbsqldatabase) | Létrehoz egy Cosmos DB SQL Database. |
| [Új – AzCosmosDBSqlUniqueKey](/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekey) | Létrehoz egy, a New-AzCosmosDBSqlUniqueKeyPolicy paraméterként használt PSSqlUniqueKey objektumot. |
| [Új – AzCosmosDBSqlUniqueKeyPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekeypolicy) | Létrehoz egy, a New-AzCosmosDBSqlContainer paraméterként használt PSSqlUniqueKeyPolicy objektumot. |
| [Új – AzCosmosDBSqlCompositePath](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcompositepath) | Létrehoz egy, a New-AzCosmosDBSqlIndexingPolicy paraméterként használt PSCompositePath objektumot. |
| [Új – AzCosmosDBSqlIncludedPathIndex](/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpathindex) | Létrehoz egy, a New-AzCosmosDBSqlIncludedPath paraméterként használt PSIndexes objektumot. |
| [Új – AzCosmosDBSqlIncludedPath](/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpath) | Létrehoz egy, a New-AzCosmosDBSqlIndexingPolicy paraméterként használt PSIncludedPath objektumot. |
| [Új – AzCosmosDBSqlIndexingPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Létrehoz egy, a New-AzCosmosDBSqlContainer paraméterként használt PSSqlIndexingPolicy objektumot. |
| [Új – AzCosmosDBSqlConflictResolutionPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlconflictresolutionpolicy) | Létrehoz egy, a New-AzCosmosDBSqlContainer paraméterként használt PSSqlConflictResolutionPolicy objektumot. |
| [Új – AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | Létrehoz egy új Cosmos DB SQL-tárolót. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).