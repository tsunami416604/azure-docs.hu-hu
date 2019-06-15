---
title: Az Azure Resource Manager üzembe helyezési előzmények |} A Microsoft Docs
description: Útmutató a portal, PowerShell, az Azure CLI és REST API-t az Azure Resource Manager üzembe helyezési műveletek megtekintése.
tags: top-support-issue
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: tomfitz
ms.openlocfilehash: 58d22e3fcae5c30e5d7dcc39b317afeef4a693ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606018"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Az Azure Resource Manager üzembe helyezési előzmények megtekintése

Az Azure Resource Manager lehetővé teszi, hogy az üzembe helyezési előzmények megtekintése, és vizsgálja meg a korábbi telepítések meghatározott műveleteket. Tekintse meg az üzembe helyezett erőforrásokat, és minden olyan hibákkal kapcsolatos információk lekérése.

Segítség az adott központi telepítési hibák elhárítása: [gyakori hibák elhárításához erőforrások üzembe helyezésekor az Azure-bA az Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portál

Az üzembe helyezési előzmények beolvasni a központi telepítés részleteit.

1. Válassza ki a vizsgálni kívánt erőforráscsoportot.

1. A hivatkozásra kattintva **központi telepítések**.

   ![Válassza ki az üzembe helyezési előzmények](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Válassza ki azt a központi telepítéseket az üzembe helyezési előzmények.

   ![Válassza ki a központi telepítés](./media/resource-manager-deployment-operations/select-details.png)

1. A központi telepítés összegzését jelenik meg, beleértve a telepített erőforrások listáját.

    ![A központi telepítés összegzése](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. Az üzembe helyezéshez használt sablon megtekintéséhez válassza **sablon**. Letöltheti a sablont az ismételt használatát.

    ![Sablon megjelenítése](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. Ha a telepítés nem sikerült, hibaüzenet jelenik meg. Válassza ki a hibaüzenet további részletekért.

    ![sikertelen üzembe helyezés megtekintése](./media/resource-manager-deployment-operations/show-error.png)

1. A részletes hibaüzenet jelenik meg.

    ![Hiba részleteinek megtekintése](./media/resource-manager-deployment-operations/show-details.png)

1. A korrelációs Azonosítót a kapcsolódó események nyomon követésére szolgál, és használata a technikai támogatáshoz a központi telepítés hibaelhárítása során lehet hasznos.

    ![Korrelációs azonosító beszerzése](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. További információ a lépés sikertelen, jelölje be **művelet részletei**.

    ![Válassza ki az üzembe helyezési műveletek](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. Ezt a lépést, az üzembe helyezés részleteit láthatja.

    ![A művelet részleteinek megjelenítése](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A központi telepítés összesített állapotát lekéréséhez használja a **Get-AzResourceGroupDeployment** parancsot.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Vagy csak ezen központi telepítések, melyeket nem sikerült az eredményeket szűrheti.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

A korrelációs Azonosítót a kapcsolódó események nyomon követésére szolgál, és használata a technikai támogatáshoz a központi telepítés hibaelhárítása során lehet hasznos. A korrelációs Azonosítót használja:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

Minden üzembe helyezés több műveleteit tartalmazza. Minden művelet egy lépése az üzembe helyezési folyamat jelöli. Fedezze fel Mi történt egy központi telepítést, általában kell az üzembe helyezési műveletek részleteinek megtekintéséhez. Láthatja, hogy a műveletek állapotának **Get-AzResourceGroupDeploymentOperation**.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

Több műveletet a következő formátumban mindegyikhez ad vissza:

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

Sikertelen műveletek kapcsolatos további információért a műveletek a tulajdonságainak lekérése **sikertelen** állapota.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

A sikertelen műveletek mindegyikhez a következő formátumban ad vissza:

```powershell
provisioningOperation : Create
provisioningState     : Failed
timestamp             : 2019-05-13T21:42:40.7151512Z
duration              : PT3.1449887S
trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
statusCode            : BadRequest
statusMessage         : @{error=}
targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                       Microsoft.Network/publicIPAddresses/myPublicIP;
                       resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}
```

Vegye figyelembe a serviceRequestId és a követési azonosítót a művelethez. A serviceRequestId az használata a technikai támogatáshoz a központi telepítés hibaelhárítása során lehet hasznos. Egy adott művelet összpontosíthat a következő lépésben fogja használni a követési azonosító.

Az állapotüzenet egy adott sikertelen művelet lekéréséhez használja a következő parancsot:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

Amely értéket ad vissza:

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Minden központi telepítési műveletet az Azure-ban a kérések és válaszok tartalmát tartalmazza. A telepítés során használhat **DeploymentDebugLogLevel** paraméterrel megadhatja, hogy a kérelem és/vagy a válasz a rendszer naplózza.

Ezt az információt kérhet a naplót, és mentse helyileg a következő PowerShell-parancsok használatával:

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Azure CLI

Központi telepítés összesített állapotát lekéréséhez használja a **azure csoport deployment show** parancsot.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
A korrelációs Azonosítót a kapcsolódó események nyomon követésére szolgál, és használata a technikai támogatáshoz a központi telepítés hibaelhárítása során lehet hasznos.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

A műveletek esetében egy központi telepítést használja:

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

A telepítéssel kapcsolatos információk lekéréséhez használja a [-sablonalapú telepítéssel kapcsolatos információk lekérése](https://docs.microsoft.com/rest/api/resources/deployments) műveletet.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

A válaszban Megjegyzés különösen a **provisioningState**, **correlationId**, és **hiba** elemeket. A **correlationId** kapcsolódó események nyomon követésére szolgál, és használata a technikai támogatáshoz a központi telepítés hibaelhárítása során lehet hasznos.

```json
{ 
 ...
 "properties": {
   "provisioningState":"Failed",
   "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
   ...
   "error":{
     "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.",
     "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
   }  
 }
}
```

Információk a központi használja [összes sablon telepítési művelet listázására](https://docs.microsoft.com/rest/api/resources/deployments). 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
A válasz alapján a megadott kérelem és/vagy a válasz információkat tartalmaz a **debugSetting** tulajdonság üzembe helyezés során.

```json
{
 ...
 "properties": 
 {
   ...
   "request":{
     "content":{
       "location":"West US",
       "properties":{
         "accountType": "Standard_LRS"
       }
     }
   },
   "response":{
     "content":{
       "error":{
         "message":"Conflict","code":"Conflict"
       }
     }
   }
 }
}
```

## <a name="next-steps"></a>További lépések
* Segítség az adott központi telepítési hibák elhárítása: [gyakori hibák elhárításához erőforrások üzembe helyezésekor az Azure-bA az Azure Resource Manager](resource-manager-common-deployment-errors.md).
* A Tevékenységnaplók segítségével figyelheti a más típusú műveleteket kapcsolatos további információkért lásd: [Azure erőforrások kezeléséhez Tevékenységnaplók megtekintése](resource-group-audit.md).
* Ellenőrizze a telepítés előtt futtatnia kell, lásd: [egy erőforráscsoportot az Azure Resource Manager-sablon üzembe helyezése](resource-group-template-deploy.md).

