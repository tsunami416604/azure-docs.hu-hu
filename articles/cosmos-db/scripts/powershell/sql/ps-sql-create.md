---
title: PowerShell-parancsfájl az Azure Cosmos DB SQL API-adatbázis és -tároló létrehozásához
description: Azure PowerShell-parancsfájl – Az Azure Cosmos DB SQL API-adatbázist és -tárolót hoz létre
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 03/17/2020
ms.author: mjbrown
ms.openlocfilehash: 719e2cd831a982c62ab965cd7dc8a37c4cb41265
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365620"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-api"></a>Adatbázis és tároló létrehozása az Azure Cosmos DB - SQL API számára

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

Ez a parancsfájl létrehoz egy Cosmos-fiókot az SQL (Core) API-hoz két régióban munkamenetszintű konzisztenciával, egy adatbázist és egy partíciós kulccsal rendelkező tárolót, `multipleWriteLocations=true`egyéni indexelési házirendet, egyedi kulcsházirendet, TTL-t, dedikált átviteli mertét és az utolsó író tnyerte meg az ütközésfeloldási házirendet egy egyéni ütközésfeloldási útvonallal, amelyet a rendszer a .

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
| [Új-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Új Cosmos DB-fiókot hoz létre. |
| [Set-AzCosmosDBSqlDatabase](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqldatabase) | Új at hoz létre, vagy frissíti a meglévő Cosmos DB SQL-adatbázis. |
| [Új-AzCosmosDBSqlUniqueKey](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekey) | Új Cosmos DB SQL UniqueKey objektumot hoz létre. |
| [Új-AzCosmosDBSqlUniqueKeyPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekeypolicy) | Új Cosmos DB SQL UniqueKeyPolicy objektumot hoz létre. |
| [Új-AzCosmosDBSqlIncludedPathIndex](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpathindex) | Létrehoz egy új PSIndexes típusú objektumot, amely a Set-AzCosmosDBSqlIncludedPath paramétereként szolgál. |
| [Új-AzCosmosDBSqlIncludedPath](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpath) | Létrehoz egy PSIncludedPath típusú új objektumot, amely a New-AzCosmosDBSqlIndexingPolicy paramétereként szolgál. |
| [Új-AzCosmosDBSqlIndexingPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Létrehoz egy pssqlindexingpolicy típusú új objektumot, amely a Set-AzCosmosDBSqlContainer paramétereként szolgál. |
| [New-AzCosmosDBSqlConflictResolutionPolicy](https://docs.microsoft.com/powershell/module/az.cosmosdb/new-azcosmosdbsqlconflictresolutionpolicy) | Létrehoz egy PSSqlConflictResolutionPolicy típusú új objektumot, amely a Set-AzCosmosDBSqlContainer paramétereként szolgál. |
| [Set-AzCosmosDBSqlContainer](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbsqlcontainer) | Új at hoz létre, vagy frissíti a meglévő Cosmos DB SQL Container. |
|**Azure erőforráscsoportok**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../../../powershell-samples.md) találhat.
