---
title: Telepítési előzmények
description: Bemutatja, hogyan tekintheti meg az Azure Resource Manager üzembe helyezési műveleteit a portálon, a PowerShell, az Azure CLI és a REST API használatával.
tags: top-support-issue
ms.topic: conceptual
ms.date: 11/26/2019
ms.openlocfilehash: b0f196f86bed05094b04bfc20c7cef2248a91c65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460296"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>A telepítési előzmények megtekintése az Azure Resource Managerrel

Az Azure Resource Manager lehetővé teszi a telepítési előzmények megtekintését és a korábbi telepítések adott műveleteinek vizsgálatát. Megtekintheti az üzembe helyezett erőforrásokat, és információkat kaphat a hibákról.

Az egyes telepítési hibák elhárításával kapcsolatban olvassa el a Gyakori hibák elhárítása az Azure Resource Manager rel az [Azure-ba történő üzembe helyezésekor című témakört.](common-deployment-errors.md)

## <a name="get-deployments-and-correlation-id"></a>Központi telepítések és korrelációs azonosító beszerezni

Az Azure Portalon, a PowerShellen, az Azure CLI-n vagy a REST API-n keresztül megtekintheti a központi telepítés részleteit. Minden központi telepítés korrelációs azonosítóval rendelkezik, amely a kapcsolódó események nyomon követésére szolgál. Ez akkor lehet hasznos, ha a technikai támogatással együttműködve elhárítja a telepítést.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Jelölje ki a vizsgálni kívánt erőforráscsoportot.

1. Válassza ki a hivatkozást a **Telepítések csoportban.**

   ![Telepítési előzmények kiválasztása](./media/deployment-history/select-deployment-history.png)

1. Válassza ki az egyik központi telepítést a központi telepítési előzmények közül.

   ![Telepítés kiválasztása](./media/deployment-history/select-details.png)

1. Megjelenik a központi telepítés összegzése, beleértve a korrelációs azonosítót is.

    ![A központi telepítés összegzése](./media/deployment-history/show-correlation-id.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Egy erőforráscsoport összes központi telepítésének listázásához használja a [Get-AzResourceGroupDeployment](/powershell/module/az.resources/Get-AzResourceGroupDeployment) parancsot.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Ha egy adott központi telepítést szeretne leadni egy erőforráscsoportból, adja hozzá a **DeploymentName** paramétert.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment
```

A korrelációs azonosító lekért, használja a következőket:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment).CorrelationId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Egy erőforráscsoport központi telepítésének listázásához használja [az az telepítési csoport listáját.](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-list)

```azurecli-interactive
az deployment group list --resource-group ExampleGroup
```

Egy adott központi telepítés lelövéséhez használja az [az deployment csoport show.](/cli/azure/group/deployment?view=azure-cli-latest#az-deployment-group-show)

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment
```

A korrelációs azonosító lekért, használja a következőket:

```azurecli-interactive
az deployment group show --resource-group ExampleGroup --name ExampleDeployment --query properties.correlationId
```

# <a name="http"></a>[HTTP](#tab/http)

Egy erőforráscsoport központi telepítésének listázásához használja a következő műveletet. A kérelemben használható legújabb API-verziószámot a [Központi telepítések – Erőforráscsoport szerint lista](/rest/api/resources/deployments/listbyresourcegroup)című témakörben talál.

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/?api-version={api-version}
```

Egy adott telepítés lelövéséhez. a következő műveletet használja. A kérelemben használható legújabb API-verziószámot a [Központi telepítések – Beget című témakörben talál.](/rest/api/resources/deployments/get)

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

A válasz tartalmazza a korrelációs azonosítót.

```json
{
 ...
 "properties": {
   "mode": "Incremental",
   "provisioningState": "Failed",
   "timestamp": "2019-11-26T14:18:36.4518358Z",
   "duration": "PT26.2091817S",
   "correlationId": "47ff4228-bf2e-4ee5-a008-0b07da681230",
   ...
 }
}
```

---

## <a name="get-deployment-operations-and-error-message"></a>Telepítési műveletek és hibaüzenet ek beszerezni

Minden központi telepítés több műveletet is tartalmazhat. A központi telepítéssel kapcsolatos további részletek megtekintéséhez tekintse meg a központi telepítési műveleteket. Ha egy központi telepítés sikertelen, a központi telepítési műveletek hibaüzenetet tartalmaznak.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. A központi telepítés összegzésén válassza a **Művelet részletei**lehetőséget.

    ![Telepítési műveletek kiválasztása](./media/deployment-history/get-operation-details.png)

1. A központi telepítés ezen lépésének részleteit láthatja. Hiba esetén a részletek tartalmazzák a hibaüzenetet.

    ![Művelet részleteinek megjelenítése](./media/deployment-history/see-operation-details.png)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

Az erőforráscsoportban történő telepítés központi telepítési műveleteinek megtekintéséhez használja a [Get-AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azdeploymentoperation) parancsot.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy
```

A sikertelen műveletek megtekintéséhez szűrje **a sikertelen** állapotú műveleteket.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy).Properties | Where-Object ProvisioningState -eq Failed
```

A sikertelen műveletek állapotüzenetének levételéhez használja a következő parancsot:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeploy ).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage.error
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az erőforráscsoportra történő telepítés központi telepítési műveleteinek megtekintéséhez használja az [az telepítési csoport műveleti listájának](/cli/azure/group/deployment/operation?view=azure-cli-latest#az-deployment-group-operation-list) parancsát.

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeployment
```

A sikertelen műveletek megtekintéséhez szűrje **a sikertelen** állapotú műveleteket.

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed']"
```

A sikertelen műveletek állapotüzenetének levételéhez használja a következő parancsot:

```azurecli-interactive
az deployment group operation list --resource-group ExampleGroup --name ExampleDeploy --query "[?properties.provisioningState=='Failed'].properties.statusMessage.error"
```

# <a name="http"></a>[HTTP](#tab/http)

Üzembe helyezési műveletek beszerezéséhez használja a következő műveletet. A kérelemben használható legújabb API-verziószámot a [Telepítési műveletek listája című](/rest/api/resources/deploymentoperations/list)témakörben talál.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```

A válasz hibaüzenetet tartalmaz.

```json
{
  "value": [
    {
      "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Resources/deployments/exampledeploy/operations/13EFD9907103D640",
      "operationId": "13EFD9907103D640",
      "properties": {
        "provisioningOperation": "Create",
        "provisioningState": "Failed",
        "timestamp": "2019-11-26T14:18:36.3177613Z",
        "duration": "PT21.0580179S",
        "trackingId": "9d3cdac4-54f8-486c-94bd-10c20867b8bc",
        "serviceRequestId": "01a9d0fe-896b-4c94-a30f-60b70a8f1ad9",
        "statusCode": "BadRequest",
        "statusMessage": {
          "error": {
            "code": "InvalidAccountType",
            "message": "The AccountType Standard_LRS1 is invalid. For more information, see - https://aka.ms/storageaccountskus"
          }
        },
        "targetResource": {
          "id": "/subscriptions/xxxx/resourceGroups/examplegroup/providers/Microsoft.Storage/storageAccounts/storageq2czadzfgizc2",
          "resourceType": "Microsoft.Storage/storageAccounts",
          "resourceName": "storageq2czadzfgizc2"
        }
      }
    },
    ...
  ]
}
```

---

## <a name="next-steps"></a>További lépések

* Az egyes telepítési hibák elhárításával kapcsolatban olvassa el a Gyakori hibák elhárítása az Azure Resource Manager rel az [Azure-ba történő üzembe helyezésekor című témakört.](common-deployment-errors.md)
* Ha többet szeretne tudni arról, hogy a tevékenységnaplók segítségével figyelheti-e a többi művelettípust, olvassa el a Tevékenységnaplók megtekintése az [Azure-erőforrások kezeléséhez című témakört.](../management/view-activity-logs.md)
* A telepítés végrehajtása előtt a telepítés érvényesítéséhez olvassa [el az Erőforráscsoport üzembe helyezése az Azure Resource Manager sablonnal (Deploy a Resource Manager) (Erőforráscsoport telepítése az Azure Resource Manager sablonnal) témakört.](deploy-powershell.md)

