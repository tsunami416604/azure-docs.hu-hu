---
author: lucygoldbergmicrosoft
ms.service: data-explorer
ms.topic: include
ms.date: 11/28/2019
ms.author: lugoldbe
ms.openlocfilehash: 9598724db630db7fc48545b58f2b0c1cad464aa4
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74667853"
---
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szükség az Azure-erőforrásokra, törölje az üzembe helyezett erőforrásokat az erőforráscsoport törlésével. 

### <a name="clean-up-resources-using-the-azure-portal"></a>Erőforrások tisztítása a Azure Portal használatával

Törölje a Azure Portal található erőforrásokat az [erőforrások tisztítása](../articles/data-explorer/create-cluster-database-portal.md#clean-up-resources)című témakör lépéseit követve.

### <a name="clean-up-resources-using-powershell"></a>Az erőforrások eltávolítása a PowerShell használatával

Ha a Cloud Shell továbbra is nyitva van, nem kell átmásolnia/futtatnia az első sort (olvasás-gazdagép).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name that you used in the last procedure"
$resourceGroupName = "${projectName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```