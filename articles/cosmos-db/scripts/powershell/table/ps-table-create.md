---
title: PowerShell-parancsfájl az Azure Cosmos DB Table API-ban való létrehozáshoz
description: Megtudhatja, hogy miként frissítheti egy PowerShell-parancsfájlt egy adatbázis vagy egy tároló átviteli teljesítményének frissítéséhez az Azure Cosmos DB Table API-ban
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 03/18/2020
ms.author: mjbrown
ms.openlocfilehash: 9ea03996c793c3d53e4a3657c537f9354892c647
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365589"
---
# <a name="create-a-table-for-azure-cosmos-db---table-api"></a>Tábla létrehozása az Azure Cosmos DB - Table API-hoz

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-create.ps1 "Create a table for Table API")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
|**Azure-erőforrások**| |
| [Új-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Erőforrást hoz létre. |
|**Azure Cosmos DB**| |
| [Set-AzCosmosDBTable tábla](https://docs.microsoft.com/powershell/module/az.cosmosdb/set-azcosmosdbtable) | Creates or updates a Cosmos DB Table API Table. |
|**Azure erőforráscsoportok**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../../../powershell-samples.md) találhat.