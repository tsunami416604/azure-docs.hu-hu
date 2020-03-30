---
title: A telepítési kvóta túllépve
description: Ez a témakör azt a hibát ismerteti, hogy miként lehet több mint 800 központi telepítést az erőforráscsoport előzményei között.
ms.topic: troubleshooting
ms.date: 10/04/2019
ms.openlocfilehash: 919cd9a3482401cd47516e2677b0bf58387488b0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245089"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Hiba elhárítása, ha a telepítésszáma meghaladja a 800-at

Minden erőforráscsoport legfeljebb 800 központi telepítést a központi telepítési előzményekben. Ez a cikk azt a hibát ismerteti, amely akkor jelenik meg, ha egy központi telepítés sikertelen, mert túllépné az engedélyezett 800 központi telepítést. A hiba elhárításához törölje a központi telepítéseket az erőforráscsoport előzményeiből. Egy központi telepítés törlése az előzményekből nincs hatással az üzembe helyezett erőforrások egyikére sem.

## <a name="symptom"></a>Hibajelenség

A központi telepítés során hibaüzenet jelenik meg arról, hogy az aktuális központi telepítés meghaladja a 800 központi telepítéskvótát.

## <a name="solution"></a>Megoldás

### <a name="azure-cli"></a>Azure CLI

Az [az üzembe helyezési csoport törlési](/cli/azure/group/deployment) parancsával törölheti a központi telepítéseket az előzményekből.

```azurecli-interactive
az deployment group delete --resource-group exampleGroup --name deploymentName
```

Az öt napnál régebbi összes központi telepítés törléséhez használja a következőket:

```azurecli-interactive
startdate=$(date +%F -d "-5days")
deployments=$(az deployment group list --resource-group exampleGroup --query "[?properties.timestamp<'$startdate'].name" --output tsv)

for deployment in $deployments
do
  az deployment group delete --resource-group exampleGroup --name $deployment
done
```

Az aktuális számot a következő paranccsal kaphatja meg a telepítési előzményekben:

```azurecli-interactive
az deployment group list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

Az [Eltávolítás-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) paranccsal törölheti a központi telepítéseket az előzményekből.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Az öt napnál régebbi összes központi telepítés törléséhez használja a következőket:

```azurepowershell-interactive
$deployments = Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup | Where-Object Timestamp -lt ((Get-Date).AddDays(-5))

foreach ($deployment in $deployments) {
  Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name $deployment.DeploymentName
}
```

Az aktuális számot a következő paranccsal kaphatja meg a telepítési előzményekben:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

## <a name="third-party-solutions"></a>Harmadik féltől származó megoldások

A következő külső megoldások konkrét forgatókönyvekkel foglalkoznak:

* [Azure Logic-alkalmazások és PowerShell-megoldások](https://devkimchi.com/2018/05/30/managing-excessive-arm-deployment-histories-with-logic-apps/)
* [AzDevOps-bővítmény](https://github.com/christianwaha/AzureDevOpsExtensionCleanRG)
