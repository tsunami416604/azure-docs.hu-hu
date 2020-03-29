---
title: Erőforrások kezelése – Azure PowerShell
description: Az Azure PowerShell és az Azure Resource Manager segítségével kezelheti erőforrásait. Az erőforrások üzembe helyezése és törlése.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: de6f24ea66ef41b5ee4bfdda5948de9639f10a51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485402"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Azure-erőforrások kezelése az Azure PowerShell használatával

Ismerje meg, hogyan használhatja az Azure PowerShellt az [Azure Resource Managerrel](overview.md) az Azure-erőforrások kezeléséhez. Az erőforráscsoportok kezeléséről az [Azure-erőforráscsoportok kezelése az Azure PowerShell használatával.](manage-resource-groups-powershell.md)

További cikkek az erőforrások kezeléséről:

- [Azure-erőforrások kezelése az Azure Portal használatával](manage-resources-portal.md)
- [Azure-erőforrások kezelése az Azure CLI használatával](manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Erőforrások üzembe helyezése meglévő erőforráscsoportba

Az Azure-erőforrásokat közvetlenül telepítheti az Azure PowerShell használatával, vagy üzembe helyezhet egy Resource Manager-sablont az Azure-erőforrások létrehozásához.

### <a name="deploy-a-resource"></a>Erőforrás telepítése

A következő parancsfájl létrehoz egy tárfiókot.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>Sablon telepítése

A következő parancsfájl létrehoz egy rövid útmutató sablont egy tárfiók létrehozásához. További információ: [Rövid útmutató: Azure Resource Manager-sablonok létrehozása a Visual Studio-kód használatával](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)című témakörben olvashat.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

További információ: [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure PowerShell használatával](../templates/deploy-powershell.md)című témakörben olvashat.

## <a name="deploy-a-resource-group-and-resources"></a>Erőforráscsoport és erőforrások üzembe helyezése

Létrehozhat egy erőforráscsoportot, és erőforrásokat helyezhet üzembe a csoportban. További információ: [Erőforráscsoport létrehozása és erőforrások üzembe helyezése.](../templates/deploy-to-subscription.md#resource-group-and-resources)

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Erőforrások üzembe helyezése több előfizetésre vagy erőforráscsoportra

A sablonban lévő összes erőforrást általában egyetlen erőforráscsoportra telepíti. Vannak azonban olyan esetek, amikor szeretné üzembe helyezni egy erőforrás-készletet együtt, de elhelyezni őket a különböző erőforráscsoportok vagy előfizetések. További információ: [Azure-erőforrások üzembe helyezése több előfizetésre vagy erőforráscsoportokra.](../templates/cross-resource-group-deployment.md)

## <a name="delete-resources"></a>Erőforrások törlése

A következő parancsfájl bemutatja, hogyan lehet törölni egy tárfiókot.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Ha többet szeretne tudni arról, hogy az Azure Resource Manager hogyan rendeli meg az erőforrások törlését, olvassa el az [Azure Resource Manager erőforráscsoport-törlése című témakört.](delete-resource-group.md)

## <a name="move-resources"></a>Erőforrások áthelyezése

A következő parancsfájl bemutatja, hogyan távolíthat el egy tárfiókot az egyik erőforráscsoportból egy másik erőforráscsoportba.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Erőforrások zárolása

A zárolás megakadályozza, hogy a szervezet más felhasználói véletlenül kiirtsák vagy módosítsák a kritikus erőforrásokat, például az Azure-előfizetést, az erőforráscsoportot vagy az erőforrást. 

A következő parancsfájl zárol egy tárfiókot, így a fiók nem törölhető.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

A következő parancsfájl lekéri az összes zárolást egy tárfiókhoz:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

A következő parancsfájl törli a tárfiók zárolását:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](lock-resources.md).

## <a name="tag-resources"></a>Erőforrások címkézése

A címkézés segít az erőforráscsoport és az erőforrások logikus rendszerezésben. További információ: [Címkék használata az Azure-erőforrások rendszerezéséhez című témakörben.](tag-resources.md#powershell)

## <a name="manage-access-to-resources"></a>Erőforrásokhoz való hozzáférés kezelése

[A szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-beli](../../role-based-access-control/overview.md) erőforrásokhoz való hozzáférés kezelésének módja. További információ: [Hozzáférés kezelése az RBAC és az Azure PowerShell használatával című témakörben.](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>További lépések

- Az Azure Resource Manager megismeréséhez olvassa el az [Azure Resource Manager áttekintése című témakört.](overview.md)
- Az Erőforrás-kezelő sablon szintaxisának megismeréséről [Az Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakörben olvashat.](../templates/template-syntax.md)
- A sablonok fejlesztéséről részletesen olvashat [az oktatóanyagokról.](/azure/azure-resource-manager/)
- Az Azure Resource Manager sablonsémák megtekintéséhez olvassa el a [sablon hivatkozási .](/azure/templates/)
