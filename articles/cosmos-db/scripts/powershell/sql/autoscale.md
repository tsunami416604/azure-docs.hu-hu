---
title: PowerShell-szkript Azure Cosmos DB SQL API-adatbázis és-tároló létrehozásához az autoscale használatával
description: Azure PowerShell script-Azure Cosmos DB SQL API-adatbázis és-tároló létrehozása az autoscale használatával
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: 32ebbb43fc02a3aa3d5129ea66ebe8640d11b0cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87506537"
---
# <a name="create-a-database-and-container-with-autoscale-for-azure-cosmos-db---sql-api"></a>Adatbázis és tároló létrehozása az Azure Cosmos DB-SQL API-val való autoskálázással

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

Ez a szkript létrehoz egy Cosmos-fiókot a Core (SQL) API-hoz két régióban a munkamenet-szintű konzisztencia, egy adatbázis és egy, az automatikus skálázási átviteli sebességgel és az alapértelmezett index-házirenddel rendelkező tárolóval.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-autoscale.ps1 "Create an account, database, and container with autoscale for Core (SQL) API")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
|**Azure Cosmos DB**| |
| [Új – AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Létrehoz egy Cosmos DB fiókot. |
| [Új – AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbsqldatabase) | Létrehoz egy Cosmos DB SQL Database. |
| [Új – AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | Létrehoz egy új Cosmos DB SQL-tárolót. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../../../powershell-samples.md) találhat.
