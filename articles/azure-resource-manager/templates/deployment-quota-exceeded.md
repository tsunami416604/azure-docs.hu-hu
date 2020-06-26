---
title: Az üzembe helyezési kvóta túllépve
description: Ismerteti, hogyan oldható fel a több mint 800 üzemelő példány hibája az erőforráscsoport előzményeiben.
ms.topic: troubleshooting
ms.date: 06/25/2020
ms.openlocfilehash: 1b0c3de6007964b487a13e71cd43bd984cd970f1
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85391179"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Hiba elhárítása, ha a központi telepítés száma meghaladja a 800

Az egyes erőforráscsoportok az üzembe helyezési előzményekben legfeljebb 800 üzemelő példányra korlátozódnak. Ez a cikk azt a hibát ismerteti, amikor egy központi telepítés meghiúsul, mert túllépi az engedélyezett 800 üzemelő példányokat. A hiba megoldásához törölje az üzemelő példányokat az erőforráscsoport előzményeiből. Egy központi telepítés az előzményekből való törlése nem befolyásolja az üzembe helyezett erőforrásokat.

> [!NOTE]
> A Azure Resource Manager hamarosan automatikusan elindítja az előzményekből való üzembe helyezések törlését a korlát közelében. Ez a hiba akkor is megjelenhet, ha az automatikus törlést választotta. További információ: [automatikus törlések az üzembe helyezési előzményekből](deployment-history-deletions.md).

## <a name="symptom"></a>Hibajelenség

Az üzembe helyezés során hibaüzenetet kap arról, hogy a jelenlegi központi telepítés túllépi a 800-es üzemelő példányok kvótáját.

## <a name="solution"></a>Megoldás

### <a name="azure-cli"></a>Azure CLI

Az az [üzembe helyezési csoport törlése](/cli/azure/group/deployment) paranccsal törölheti az előzményekből származó központi telepítéseket.

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

Az öt napnál régebbi központi telepítések törléséhez használja a következőt:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

Az aktuális szám a telepítési előzményekben a következő paranccsal kérhető le:

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

A [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) parancs használatával törölje az előzményekből a központi telepítéseket.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Az öt napnál régebbi központi telepítések törléséhez használja a következőt:

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

Az aktuális szám a telepítési előzményekben a következő paranccsal kérhető le:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Harmadik féltől származó megoldások

A következő külső megoldások specifikus forgatókönyvek:

* [Azure Logic Apps és PowerShell-megoldások](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [AzDevOps-bővítmény](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
