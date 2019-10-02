---
title: Túllépte az Azure-beli üzembe helyezési kvótát
description: Ismerteti, hogyan oldható fel a több mint 800 üzemelő példány hibája az erőforráscsoport előzményeiben.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: troubleshooting
ms.date: 10/01/2019
ms.author: tomfitz
ms.openlocfilehash: f06aff74e2cf800d44115f34921825122b09a9e7
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71719434"
---
# <a name="resolve-error-when-deployment-count-exceeds-800"></a>Hiba elhárítása, ha a központi telepítés száma meghaladja a 800

Az egyes erőforráscsoportok az üzembe helyezési előzményekben legfeljebb 800 üzemelő példányra korlátozódnak. Ez a cikk azt a hibát ismerteti, amikor egy központi telepítés meghiúsul, mert túllépi az engedélyezett 800 üzemelő példányokat. A hiba megoldásához törölje az üzemelő példányokat az erőforráscsoport előzményeiből. Egy központi telepítés az előzményekből való törlése nem befolyásolja az üzembe helyezett erőforrásokat.

## <a name="symptom"></a>Jelenség

Az üzembe helyezés során hibaüzenetet kap arról, hogy a jelenlegi központi telepítés túllépi a 800-es üzemelő példányok kvótáját.

## <a name="solution"></a>Megoldás

### <a name="azure-cli"></a>Azure CLI

Az előzményekből az az [Group Deployment delete](/cli/azure/group/deployment#az-group-deployment-delete) paranccsal törölheti a központi telepítéseket.

```azurecli-interactive
az group deployment delete --resource-group exampleGroup --name deploymentName
```

Az aktuális szám a telepítési előzményekben a következő paranccsal kérhető le:

```azurecli-interactive
az group deployment list --resource-group exampleGroup --query "length(@)"
```

### <a name="azure-powershell"></a>Azure PowerShell

A [Remove-AzResourceGroupDeployment](/powershell/module/az.resources/remove-azresourcegroupdeployment) parancs használatával törölje az előzményekből a központi telepítéseket.

```azurepowershell-interactive
Remove-AzResourceGroupDeployment -ResourceGroupName exampleGroup -Name deploymentName
```

Az aktuális szám a telepítési előzményekben a következő paranccsal kérhető le:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName exampleGroup).Count
```

