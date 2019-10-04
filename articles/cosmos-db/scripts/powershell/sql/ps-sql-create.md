---
title: Azure PowerShell script-Azure Cosmos DB SQL-(Core-) API-adatbázis és-tároló létrehozása
description: Azure PowerShell script-Azure Cosmos DB SQL-(Core-) API-adatbázis és-tároló létrehozása
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 09/20/2019
ms.author: mjbrown
ms.openlocfilehash: eee1e31808412dc5e4308dee92f3685507e771f3
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2019
ms.locfileid: "71178804"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-core-api"></a>Adatbázis és tároló létrehozása a Azure Cosmos DB-SQL (Core) API-hoz

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

Ez a szkript létrehoz egy Cosmos-fiókot az SQL (Core) API-hoz két régióban a munkamenet-szintű konzisztencia, egy megosztott átviteli sebességgel rendelkező adatbázis és egy partíciós kulccsal rendelkező tároló, egyéni indexelési házirend, egyedi kulcs házirend, TTL, dedikált átviteli sebesség és a legutóbbi író WINS használatával. ütközés-feloldási házirend egy egyéni ütközés-feloldási útvonallal, amelyet `multipleWriteLocations=true`a rendszer akkor használ, amikor.

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL (Core) API")]

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
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Létrehoz egy erőforrást. |
|**Azure-erőforráscsoportok**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../../../powershell-samples.md) találhat.
