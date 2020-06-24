---
title: PowerShell-szkript az Azure Cosmos Table API-táblázat erőforrás-zárolásának létrehozásához
description: Erőforrás-zárolás létrehozása az Azure Cosmos Table API táblázathoz
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 06/12/2020
ms.openlocfilehash: ddb72cd7928b15220ff14a6cac801a363a4d778a
ms.sourcegitcommit: 398fecceba133d90aa8f6f1f2af58899f613d1e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2020
ms.locfileid: "85126368"
---
# <a name="create-a-resource-lock-for-azure-cosmos-table-api-table-using-azure-powershell"></a>Erőforrás-zárolás létrehozása az Azure Cosmos Table API Table Azure PowerShell használatával

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-PowerShell-install](../../../../../includes/sample-PowerShell-install-no-ssh.md)]

> [!IMPORTANT]
> Az erőforrás-zárolások nem működnek a felhasználók által a Cosmos DB SDK-val összekapcsolt módosítások, a fiókok kulcsain keresztül csatlakozó eszközök vagy az Azure Portal használatával, kivéve, ha a Cosmos DB fiókot a rendszer először zárolja az `disableKeyBasedMetadataWriteAccess` engedélyezett tulajdonsággal. Ha többet szeretne megtudni a tulajdonság engedélyezéséről, tekintse meg az SDK-k [módosításainak megelőzését](../../../role-based-access-control.md#preventing-changes-from-cosmos-sdk)ismertető témakört.

## <a name="sample-script"></a>Példaszkript

[!code-PowerShell[main](../../../../../PowerShell_scripts/cosmosdb/table/ps-table-lock.ps1 "Create, list, and remove resource locks")]

## <a name="clean-up-deployment"></a>Az üzemelő példány eltávolítása

A példaszkript futtatása után a következő paranccsal távolítható el az erőforráscsoport és az összes ahhoz kapcsolódó erőforrás.

```PowerShell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Szkript ismertetése

A szkript a következő parancsokat használja. A táblázatban lévő összes parancs a hozzá tartozó dokumentációra hivatkozik.

| Parancs | Jegyzetek |
|---|---|
|**Azure-erőforrás**| |
| [Új – AzResourceLock](https://docs.microsoft.com/PowerShell/module/az.resources/new-azresourcelock) | Erőforrás-zárolást hoz létre. |
| [Get-AzResourceLock](https://docs.microsoft.com/PowerShell/module/az.resources/get-azresourcelock) | Lekérdezi egy erőforrás zárolását, vagy listázza az erőforrás-zárolásokat. |
| [Remove-AzResourceLock](https://docs.microsoft.com/PowerShell/module/az.resources/remove-azresourcelock) | Eltávolít egy erőforrás-zárolást. |
|||

## <a name="next-steps"></a>További lépések

További információ a Azure PowerShellről: [Azure PowerShell dokumentáció](https://docs.microsoft.com/PowerShell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../../../PowerShell-samples.md) találhat.