---
title: Az üzembe helyezési kvóta túllépve
description: Ismerteti, hogyan oldható fel a több mint 800 üzemelő példány hibája az erőforráscsoport előzményeiben.
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: 919cd9a3482401cd47516e2677b0bf58387488b0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80245089"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Hiba elhárítása, ha a központi telepítés száma meghaladja a 800

Az egyes erőforráscsoportok az üzembe helyezési előzményekben legfeljebb 800 üzemelő példányra korlátozódnak. Ez a cikk azt a hibát ismerteti, amikor egy központi telepítés meghiúsul, mert túllépi az engedélyezett 800 üzemelő példányokat. A hiba megoldásához törölje az üzemelő példányokat az erőforráscsoport előzményeiből. Egy központi telepítés az előzményekből való törlése nem befolyásolja az üzembe helyezett erőforrásokat.

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
