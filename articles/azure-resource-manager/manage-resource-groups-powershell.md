---
title: Azure Resource Manager-csoportok kezelése az Azure PowerShell-lel |} A Microsoft Docs
description: Azure PowerShell használatával az Azure Resource Manager-csoportok kezelése.
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
ms.openlocfilehash: 6f416f1de6baca7fe79ea2a5dddfb8f8eb5f5120
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56825093"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Azure Resource Manager-erőforráscsoportok kezelése az Azure PowerShell-lel

Ismerje meg, hogyan használhatja az Azure Powershellt [Azure Resource Manager](resource-group-overview.md) az Azure-erőforráscsoportok kezelése. Azure-erőforrások kezeléséhez, lásd: [Azure-erőforrások kezelése az Azure PowerShell-lel](./manage-resources-powershell.md).

Más cikkek erőforráscsoportok kezeléséről:

- [Az Azure-erőforráscsoportok kezelése az Azure portal használatával](./manage-resources-portal.md)
- [Az Azure-erőforráscsoportok kezelése az Azure CLI-vel](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Az erőforráscsoportok ismertetése

Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A szervezet számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között. Általánosságban elmondható adjon hozzá erőforrásokat, azonos életciklussal ugyanabban az erőforráscsoportban, így könnyen telepítése, frissítése és csoportként törölheti őket.

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Ezért ha az erőforráscsoport számára megad egy helyet, akkor a metaadatok tárolási helyét adja meg. Megfelelőségi okokból szükség lehet arra, hogy az adatokat egy adott régióban tárolja.

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Ha megad egy helyet az erőforráscsoportnak, határozunk meg a metaadatok tárolására.

## <a name="create-resource-groups"></a>Erőforráscsoportok létrehozása

A következő PowerShell-szkript létrehoz egy erőforráscsoportot, és megjeleníti az erőforráscsoport.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Erőforráscsoportok listázása

A következő PowerShell-parancsfájlt az előfizetéshez tartozó erőforráscsoportok listája.

```azurepowershell-interactive
Get-AzResourceGroup
```

Egy erőforráscsoport lekérése:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Erőforráscsoport törlése

A következő PowerShell-parancsfájl töröl egy erőforráscsoportot:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Milyen az Azure Resource Manager orders erőforrások törlését kapcsolatos további információkért lásd: [törölni az Azure Resource Manager-erőforrást csoport](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Erőforrások üzembe helyezése egy meglévő erőforráscsoportot

Lásd: [helyezheti üzembe az erőforrásokat egy meglévő erőforráscsoportot](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Egy erőforráscsoport üzemelő példányához ellenőrzéséhez tekintse meg a [Test-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Egy erőforráscsoport és erőforrások üzembe helyezése

Hozzon létre egy erőforráscsoportot, és az erőforrások üzembe helyezése a csoporthoz egy Resource Manager-sablon használatával. További információkért lásd: [hozzon létre és helyezhet üzembe erőforrásokat](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="move-to-another-resource-group-or-subscription"></a>Helyezze át egy másik erőforráscsoportba vagy előfizetésbe

A csoport az erőforrásokat áthelyezheti egy másik erőforráscsoportot. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](./resource-group-move-resources.md#move-resources).

## <a name="lock-resource-groups"></a>Zárolási erőforráscsoportok

Zárolás megakadályozza a véletlen törlését vagy módosítását a kritikus fontosságú erőforrások, például az Azure-előfizetésre, erőforráscsoportra vagy erőforrásra a szervezet más felhasználói. 

A következő parancsfájl zárolja az erőforráscsoport, az erőforráscsoport nem törölhető.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

A következő parancsfájl egy erőforráscsoportot az összes zárolásainak beolvasása:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Címke erőforráscsoportok

A címkékkel erőforráscsoportok és erőforrásokhoz, hogy logikusan rendszerezhesse az eszközöket. További információ: [az Azure-erőforrások rendszerezése címkék használatával](./resource-group-using-tags.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Erőforráscsoportok exportálása sablonokhoz

Az erőforráscsoport sikeres beállítása után érdemes a Resource Manager-sablon az erőforráscsoport megtekintéséhez. A sablon exportálása két előnyöket kínál:

- Automatizálhatja a később üzembe helyezések, a megoldás, mert a sablon tartalmazza a teljes infrastruktúra.
- Ismerje meg a sablon szintaxisáról alapján, a JavaScript Object Notation (JSON), amely a megoldás jelöli.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

További információkért lásd: [exportálási erőforráscsoport](./manage-resource-groups-portal.md#export-resource-groups-to-templates).

## <a name="manage-access-to-resource-groups"></a>Erőforráscsoportok való hozzáférés kezelése

A [szerepköralapú hozzáférés-vezérlés (RBAC)](../role-based-access-control/overview.md) az erőforrásokhoz való hozzáférés kezelésének a módja az Azure-ban. További információkért lásd: [RBAC és az Azure PowerShell-hozzáférés kezelése](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>További lépések

- Azure Resource Manager kapcsolatban lásd: [Azure Resource Manager áttekintése](./resource-group-overview.md).
- A Resource Manager-sablon szintaxisáról kapcsolatban lásd: [struktúra és az Azure Resource Manager-sablonok szintaxisát](./resource-group-authoring-templates.md).
- Ismerje meg, hogyan fejleszthet sablonokat, tekintse meg a [lépésről lépésre haladó oktatóanyagok](/azure/azure-resource-manager/).
- Az Azure Resource Manager-sablon sémák megtekintése: [sablonreferenciája](/azure/templates/).