---
title: Azure PowerShell-példaszkript – egy adatbázist egy Azure Cosmos-fiók törlése
description: Az Azure PowerShell-példaszkript – egy adatbázist egy Azure Cosmos-fiók törlése
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 05/08/2019
ms.author: mjbrown
ms.openlocfilehash: a8ecdb7456b13b21f814a3c2eda924aa9b3f48fa
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077725"
---
# <a name="delete-a-database-in-an-azure-cosmos-account-in-an-azure-cosmos-account-using-powershell"></a>Töröl egy adatbázist egy Azure Cosmos-fiók egy Azure Cosmos-fiókban, PowerShell-lel

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Példaszkript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-database-delete.ps1 "Delete a database in an Azure Cosmos account")]

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
| [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) | Egy erőforrás beolvasása. |
| [Set-AzResource](https://docs.microsoft.com/powershell/module/az.resources/set-azresource) | Frissíti egy erőforrást. |
| [Invoke-AzResourceAction](https://docs.microsoft.com/powershell/module/az.resources/invoke-azresourceaction) | Meghív egy műveletet az erőforrás. |
| [Remove-AzResource](https://docs.microsoft.com/powershell/module/az.resources/remove-azresource) | Töröl egy erőforrást. |
|**Azure-erőforráscsoportok**| |
| [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) | Létrehoz egy erőforráscsoportot, amely az összes erőforrást tárolja. |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Töröl egy erőforráscsoportot az összes beágyazott erőforrással együtt. |
|||

## <a name="next-steps"></a>További lépések

Az Azure PowerShellről további tudnivalókért tekintse meg az [Azure PowerShell dokumentációt](https://docs.microsoft.com/powershell/).

További Azure Cosmos DB PowerShell-példaszkripteket az [Azure Cosmos DB PowerShell-szkriptek között](../../../powershell-samples.md) találhat.