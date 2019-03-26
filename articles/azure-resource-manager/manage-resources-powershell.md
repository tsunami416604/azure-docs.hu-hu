---
title: Azure-erőforrások kezelése az Azure PowerShell-lel |} A Microsoft Docs
description: Azure PowerShell és az Azure Resource Manager használatával kezelheti az erőforrásokat.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 8e6c4047182901c5282f280f59fa95eca6571ecc
ms.sourcegitcommit: 72cc94d92928c0354d9671172979759922865615
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2019
ms.locfileid: "58417921"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Azure-erőforrások kezelése az Azure PowerShell használatával

Ismerje meg, hogyan használhatja az Azure Powershellt [Azure Resource Manager](resource-group-overview.md) az Azure-erőforrások kezeléséhez. Erőforráscsoportok kezeléséhhez lásd: [kezelése Azure-erőforráscsoportok az Azure PowerShell-lel](./manage-resource-groups-powershell.md).

Egyéb erőforrások kezelésével kapcsolatos cikkek:

- [Azure-erőforrások kezelése az Azure portal használatával](./manage-resources-portal.md)
- [Azure-erőforrások kezelése az Azure CLI-vel](./manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Erőforrások üzembe helyezése egy meglévő erőforráscsoportot

Azure-erőforrások üzembe közvetlenül az Azure PowerShell használatával, vagy az Azure-erőforrások létrehozása Resource Manager-sablon üzembe helyezése.

### <a name="deploy-a-resource"></a>Erőforrások üzembe helyezése

A következő szkript létrehoz egy tárfiókot.

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

### <a name="deploy-a-template"></a>Sablon üzembe helyezése

A következő szkriptet hoz létre egy storage-fiók létrehozása gyorsindítási sablon üzembe helyezése. További információkért lásd: [a rövid útmutató: Az Azure Resource Manager-sablonok létrehozása a Visual Studio Code használatával](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

További információkért lásd: [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](./resource-group-template-deploy.md).

## <a name="deploy-a-resource-group-and-resources"></a>Egy erőforráscsoport és erőforrások üzembe helyezése

Hozzon létre egy erőforráscsoportot, és helyezheti üzembe az erőforrásokat a csoporthoz. További információkért lásd: [hozzon létre és helyezhet üzembe erőforrásokat](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Több előfizetések vagy erőforráscsoportok erőforrások üzembe helyezése

Általában végzi az üzembe helyezést összes erőforrást a sablonhoz, amelyekkel egyetlen erőforráscsoportra. Vannak azonban forgatókönyvek, ahol szeretné erőforráscsoport telepítsen együtt, de különböző erőforráscsoport vagy előfizetés helyezze el őket. További információkért lásd: [több előfizetések vagy erőforráscsoportok üzembe helyezése az Azure-erőforrások](./resource-manager-cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Erőforrások törlése

Az alábbi parancsfájl bemutatja, hogyan törölheti a tárfiókokat.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Milyen az Azure Resource Manager orders erőforrások törlését kapcsolatos további információkért lásd: [törölni az Azure Resource Manager-erőforrást csoport](./resource-group-delete.md).

## <a name="move-resources"></a>Erőforrások áthelyezése

Az alábbi parancsfájl bemutatja, hogyan távolítsa el a storage-fiók egyik erőforráscsoportból egy másik erőforráscsoportba.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

Go-oktatóanyagot, tekintse meg [oktatóanyag: Azure-erőforrások áthelyezése másik erőforráscsoportba vagy előfizetésbe](./resource-manager-tutorial-move-resources.md). 

További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](resource-group-move-resources.md).

## <a name="lock-resources"></a>Erőforrások zárolása

Zárolás megakadályozza a véletlen törlését vagy módosítását a kritikus fontosságú erőforrások, például az Azure-előfizetésre, erőforráscsoportra vagy erőforrásra a szervezet más felhasználói. 

A következő parancsfájl egy storage-fiók zárolja, így a fiók nem törölhető.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

A következő parancsfájl egy tárfiókhoz tartozó összes zárolásainak beolvasása:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

A következő parancsfájl egy storage-fiók zárolása törli:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Erőforrások címkézése

Az erőforráscsoport és erőforrások rendszerezéséhez logikailag címkézési segít. További információ: [az Azure-erőforrások rendszerezése címkék használatával](./resource-group-using-tags.md#powershell).

## <a name="manage-access-to-resources"></a>Az erőforrásokhoz való hozzáférés kezelése

A [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) az erőforrásokhoz való hozzáférés kezelésének a módja az Azure-ban. További információkért lásd: [RBAC és az Azure PowerShell-hozzáférés kezelése](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>További lépések

- Azure Resource Manager kapcsolatban lásd: [Azure Resource Manager áttekintése](./resource-group-overview.md).
- A Resource Manager-sablon szintaxisáról kapcsolatban lásd: [struktúra és az Azure Resource Manager-sablonok szintaxisát](./resource-group-authoring-templates.md).
- Ismerje meg, hogyan fejleszthet sablonokat, tekintse meg a [lépésről lépésre haladó oktatóanyagok](/azure/azure-resource-manager/).
- Az Azure Resource Manager-sablon sémák megtekintése: [sablonreferenciája](/azure/templates/).
