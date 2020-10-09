---
title: PowerShell-szkript a Azure Cosmos DB Table APIban lévő autoskálázással rendelkező tábla létrehozásához
description: PowerShell-szkript a Azure Cosmos DB Table APIban lévő autoskálázással rendelkező tábla létrehozásához
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/30/2020
ms.author: mjbrown
ms.openlocfilehash: e5ee56beb33800c4eaa8f6f171f9e636b0056615
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "87506549"
---
# <a name="create-a-table-with-autoscale-for-azure-cosmos-db---table-api"></a>Azure Cosmos DB-Table API-hoz készült autoscale-táblázat létrehozása

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-autoscale.ps1 "Create a table with autoscale for Table API")]

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
| [Új – AzCosmosDBTable](/powershell/module/az.cosmosdb/new-azcosmosdbtable) | Létrehoz egy Table API táblát. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../../../powershell-samples.md) találhat.
