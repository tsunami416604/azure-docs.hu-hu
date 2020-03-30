---
title: Erőforráscsoportok kezelése – Azure PowerShell
description: Az Azure PowerShell használatával kezelheti erőforráscsoportjait az Azure Resource Manager en keresztül. Erőforráscsoportok létrehozása, listázása és törlése.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 25e001927d5c06b4a7e5639faaa054ae18b12bb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248358"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Az Azure Resource Manager erőforráscsoportok kezelése az Azure PowerShell használatával

Ismerje meg, hogyan használhatja az Azure PowerShellt az [Azure Resource Managerrel](overview.md) az Azure-erőforráscsoportok kezeléséhez. Az Azure-erőforrások kezeléséről az [Azure-erőforrások kezelése az Azure PowerShell használatával.](manage-resources-powershell.md)

További cikkek az erőforráscsoportok kezeléséről:

- [Azure-erőforráscsoportok kezelése az Azure Portal használatával](manage-resources-portal.md)
- [Azure-erőforráscsoportok kezelése az Azure CLI használatával](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Mi az erőforráscsoport?

Az erőforráscsoport egy tároló, amely Azure-megoldásokhoz kapcsolódó erőforrásokat tárol. Az erőforráscsoport tartalmazhatja a megoldás összes erőforrását, vagy csak azokat az erőforrásokat, amelyeket Ön egy csoportként szeretne kezelni. A szervezet számára legideálisabb elosztás alapján eldöntheti, hogyan szeretné elosztani az erőforrásokat az erőforráscsoportok között. Általában adja hozzá az azonos életciklusú erőforrásokat ugyanahhoz az erőforráscsoporthoz, így könnyen telepítheti, frissítheti és törölheti őket csoportként.

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Ezért ha az erőforráscsoport számára megad egy helyet, akkor a metaadatok tárolási helyét adja meg. Megfelelőségi okokból szükség lehet arra, hogy az adatokat egy adott régióban tárolja.

Az erőforráscsoport erőforrásokra vonatkozó metaadatokat tárol. Amikor megadja az erőforráscsoport helyét, megadja a metaadatok tárolási helyét.

## <a name="create-resource-groups"></a>Erőforráscsoportok létrehozása

A következő PowerShell-parancsfájl létrehoz egy erőforráscsoportot, majd megjeleníti az erőforráscsoportot.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Erőforráscsoportok listázása

A következő PowerShell-parancsfájl felsorolja az előfizetés alatt található erőforráscsoportokat.

```azurepowershell-interactive
Get-AzResourceGroup
```

Egy erőforráscsoport bekése:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Erőforráscsoportok törlése

A következő PowerShell-parancsfájl töröl egy erőforráscsoportot:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Ha többet szeretne tudni arról, hogy az Azure Resource Manager hogyan rendeli meg az erőforrások törlését, olvassa el az [Azure Resource Manager erőforráscsoport-törlése című témakört.](delete-resource-group.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Erőforrások üzembe helyezése meglévő erőforráscsoportba

Lásd: [Erőforrások telepítése egy meglévő erőforráscsoportba](manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Az erőforráscsoport telepítésének ellenőrzéséhez olvassa el a [Test-AzResourceGroupDeployment .](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0)

## <a name="deploy-a-resource-group-and-resources"></a>Erőforráscsoport és erőforrások üzembe helyezése

Erőforrás-kezelő sablon használatával létrehozhat egy erőforráscsoportot, és erőforrásokat helyezhet a csoportba. További információ: [Erőforráscsoport létrehozása és erőforrások üzembe helyezése.](../templates/deploy-to-subscription.md#resource-group-and-resources)

## <a name="redeploy-when-deployment-fails"></a>Újratelepítés, ha az üzembe helyezés sikertelen

Ezt a szolgáltatást *hiba visszaállításának is nevezik.* További információ: [Újratelepítés, ha az üzembe helyezés sikertelen.](../templates/rollback-on-error.md)

## <a name="move-to-another-resource-group-or-subscription"></a>Áthelyezés másik erőforráscsoportra vagy -előfizetésre

A csoport erőforrásait áthelyezheti egy másik erőforráscsoportba. További információ: [Erőforrások áthelyezése új erőforráscsoportba vagy előfizetésbe](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Erőforráscsoportok zárolása

A zárolás megakadályozza, hogy a szervezet más felhasználói véletlenül kiirtsák vagy módosítsák a kritikus erőforrásokat, például az Azure-előfizetést, az erőforráscsoportot vagy az erőforrást. 

A következő parancsfájl zárol egy erőforráscsoportot, így az erőforráscsoport nem törölhető.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

A következő parancsfájl lekéri az összes zárolást egy erőforráscsoporthoz:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

További információ: [Erőforrások zárolása az Azure Resource Manager eszközzel](lock-resources.md).

## <a name="tag-resource-groups"></a>Erőforráscsoportok címkézése

Címkéket alkalmazhat az erőforráscsoportokra és erőforrásokra az eszközök logikai rendszerezéséhez. További információ: [Címkék használata az Azure-erőforrások rendszerezéséhez című témakörben.](tag-resources.md#powershell)

## <a name="export-resource-groups-to-templates"></a>Erőforráscsoportok exportálása sablonokba

Az erőforráscsoport beállítása után megtekintheti az erőforráscsoport Erőforrás-kezelő sablonját. A sablon exportálása két előnnyel jár:

- Automatizálja a megoldás jövőbeli üzembe helyezését, mert a sablon tartalmazza a teljes infrastruktúrát.
- Ismerje meg a sablon szintaxisát a megoldást képviselő JavaScript-objektumnotikát (JSON) című filmben.

Az erőforráscsoport összes erőforrásának exportálásához használja az [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) parancsmavet, és adja meg az erőforráscsoport nevét.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

A sablont helyi fájlként menti.

Az erőforráscsoport összes erőforrásának exportálása helyett kiválaszthatja, hogy mely erőforrásokat szeretné exportálni.

Egy erőforrás exportálásához adja át az erőforrás-azonosítót.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Több erőforrás exportálásához adja át az erőforrás-azonosítókat egy tömbben.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

A sablon exportálásakor megadhatja, hogy a program használja-e a paramétereket a sablonban. Alapértelmezés szerint az erőforrásnevek paraméterei szerepelnek, de nincs alapértelmezett értékük. A telepítés során át kell adnia ezt a paraméterértéket.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

Az erőforrásban a paraméter a névhez használatos.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Ha a `-IncludeParameterDefaultValue` sablon exportálásakor a paramétert használja, a sablonparaméter tartalmaz egy alapértelmezett értéket, amely az aktuális értékre van állítva. Ezt az alapértelmezett értéket használhatja, vagy felülírhatja az alapértelmezett értéket egy másik érték átadásával.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Ha a `-SkipResourceNameParameterization` sablon exportálásakor a paramétert használja, az erőforrásnevek paraméterei nem szerepelnek a sablonban. Ehelyett az erőforrás neve közvetlenül az erőforráson van beállítva az aktuális értékre. A név nem szabható testre az üzembe helyezés során.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

Az exportálási sablon funkció nem támogatja az Azure Data Factory-erőforrások exportálását. Ha többet szeretne megtudni arról, hogyan exportálhatja a Data Factory-erőforrásokat, olvassa el [az Adatgyár másolása vagy klónozása az Azure Data Factoryban című témakört.](https://aka.ms/exportTemplateViaAdf)

A klasszikus központi telepítési modellen keresztül létrehozott erőforrások exportálásához át kell [telepítenie őket az Erőforrás-kezelő telepítési modelljébe.](https://aka.ms/migrateclassicresourcetoarm)

További információ: [Egy-és többerőforrásos exportálás sablonba az Azure Portalon.](../templates/export-template-portal.md)

## <a name="manage-access-to-resource-groups"></a>Erőforráscsoportokhoz való hozzáférés kezelése

[A szerepköralapú hozzáférés-vezérlés (RBAC) az Azure-beli](../../role-based-access-control/overview.md) erőforrásokhoz való hozzáférés kezelésének módja. További információ: [Hozzáférés kezelése az RBAC és az Azure PowerShell használatával című témakörben.](../../role-based-access-control/role-assignments-powershell.md)

## <a name="next-steps"></a>További lépések

- Az Azure Resource Manager megismeréséhez olvassa el az [Azure Resource Manager áttekintése című témakört.](overview.md)
- Az Erőforrás-kezelő sablon szintaxisának megismeréséről [Az Azure Resource Manager-sablonok szerkezetének és szintaxisának megismerése című témakörben olvashat.](../templates/template-syntax.md)
- A sablonok fejlesztéséről részletesen olvashat [az oktatóanyagokról.](/azure/azure-resource-manager/)
- Az Azure Resource Manager sablonsémák megtekintéséhez olvassa el a [sablon hivatkozási .](/azure/templates/)