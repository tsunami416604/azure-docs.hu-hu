---
title: PowerShell-szkript a Azure Cosmos DB Table API műveletek listázásához és lekéréséhez
description: Azure PowerShell parancsfájl-Azure Cosmos DB lista és műveletek lekérése Table API
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 07/31/2020
ms.author: mjbrown
ms.openlocfilehash: ab4313f02d233b3eac33b7da202fca559bffa147
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92281925"
---
# <a name="list-and-get-tables-for-azure-cosmos-db---table-api"></a>Azure Cosmos DB-Table API táblázatok listázása és lekérése

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/table/ps-table-list-get.ps1 "List or get tables for Table API")]

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
| [Get-AzCosmosDBAccount](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbaccount) | Felsorolja Cosmos DB fiókokat, vagy lekéri a megadott Cosmos DB fiókot. |
| [Get-AzCosmosDBTable](https://docs.microsoft.com/powershell/module/az.cosmosdb/get-azcosmosdbtable) | Listázza Table API egy fiókban lévő táblákat, vagy lekéri a megadott Table API táblázatot egy fiókban. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>Következő lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).
