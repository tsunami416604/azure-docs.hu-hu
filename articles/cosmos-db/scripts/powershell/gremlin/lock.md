---
title: PowerShell-szkript az Azure Cosmos Gremlin API-adatbázis és-gráf erőforrás-zárolásának létrehozásához
description: Erőforrás-zárolás létrehozása az Azure Cosmos Gremlin API-adatbázis és-gráf számára
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: 69b8642c84dcb360e5ad162dbe708243a31b79e2
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93085360"
---
# <a name="create-a-resource-lock-for-azure-cosmos-gremlin-api-database-and-graph-using-azure-powershell"></a>Erőforrás-zárolás létrehozása az Azure Cosmos Gremlin API-adatbázishoz és gráfhoz Azure PowerShell használatával
[!INCLUDE[appliesto-gremlin-api](../../../includes/appliesto-gremlin-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

> [!IMPORTANT]
> Az erőforrás-zárolások nem működnek a Gremlin SDK-val vagy az Azure Portalon keresztül csatlakozó felhasználók által végzett változtatások esetén, kivéve, ha a Cosmos DB fiókot először zárolták az `disableKeyBasedMetadataWriteAccess` engedélyezett tulajdonsággal. Ha többet szeretne megtudni a tulajdonság engedélyezéséről, tekintse meg az SDK-k [módosításainak megelőzését](../../../role-based-access-control.md#prevent-sdk-changes)ismertető témakört.

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Megjegyzések |
|---|---|
|**Azure-erőforrás**| |
| [Új – AzResourceLock](/powershell/module/az.resources/new-azresourcelock) | Erőforrás-zárolást hoz létre. |
| [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock) | Lekérdezi egy erőforrás zárolását, vagy listázza az erőforrás-zárolásokat. |
| [Remove-AzResourceLock](/powershell/module/az.resources/remove-azresourcelock) | Eltávolít egy erőforrás-zárolást. |
|||

## <a name="next-steps"></a>Következő lépések

További információ a Azure PowerShellről: [Azure PowerShell dokumentáció](/powershell/).