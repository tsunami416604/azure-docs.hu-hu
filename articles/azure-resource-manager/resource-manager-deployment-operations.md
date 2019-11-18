---
title: Üzembe helyezési előzmények
description: Ismerteti, hogyan lehet megtekinteni Azure Resource Manager telepítési műveleteket a portál, a PowerShell, az Azure CLI és a REST API használatával.
tags: top-support-issue
ms.topic: conceptual
ms.date: 05/13/2019
ms.openlocfilehash: d8daf7191bb22f7c7057f6ef6b220a18868872cc
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149573"
---
# <a name="view-deployment-history-with-azure-resource-manager"></a>Az üzembe helyezési előzmények megtekintése Azure Resource Manager

A Azure Resource Manager segítségével megtekintheti az üzembe helyezési előzményeket, és megvizsgálhatja a korábbi üzemelő példányok adott műveleteit. Megtekintheti a telepített erőforrásokat, és információkat kaphat a hibákról.

Az egyes telepítési hibák elhárításával kapcsolatos segítségért lásd: [gyakori hibák megoldása az erőforrások Azure-ba való telepítésekor a Azure Resource Manager használatával](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portál

Egy központi telepítés részleteinek beszerzése az üzembe helyezési előzményekből.

1. Válassza ki azt az erőforráscsoportot, amelyet meg szeretne vizsgálni.

1. Válassza ki az üzemelő **példányok**alatt lévő hivatkozást.

   ![Telepítési előzmények kiválasztása](./media/resource-manager-deployment-operations/select-deployment-history.png)

1. Válasszon egyet az üzembe helyezési előzmények közül.

   ![Központi telepítés kiválasztása](./media/resource-manager-deployment-operations/select-details.png)

1. Megjelenik a központi telepítés összegzése, beleértve az üzembe helyezett erőforrások listáját is.

    ![Központi telepítés összegzése](./media/resource-manager-deployment-operations/view-deployment-summary.png)

1. A központi telepítéshez használt sablon megtekintéséhez válassza a **sablon**lehetőséget. A sablon letöltésével újra felhasználhatja azt.

    ![Sablon megjelenítése](./media/resource-manager-deployment-operations/show-template-from-history.png)

1. Ha a telepítés nem sikerült, hibaüzenet jelenik meg. További részletekért válassza a hibaüzenetet.

    ![Sikertelen központi telepítés megtekintése](./media/resource-manager-deployment-operations/show-error.png)

1. Megjelenik a részletes hibaüzenet.

    ![Hiba részleteinek megtekintése](./media/resource-manager-deployment-operations/show-details.png)

1. A korrelációs azonosító a kapcsolódó események nyomon követésére szolgál, és hasznos lehet, ha technikai támogatással dolgozik az üzemelő példányok hibakereséséhez.

    ![Korrelációs azonosító beolvasása](./media/resource-manager-deployment-operations/get-correlation-id.png)

1. Ha többet szeretne megtudni a sikertelen lépésekről, válassza a **művelet részletei**lehetőséget.

    ![Telepítési műveletek kiválasztása](./media/resource-manager-deployment-operations/select-deployment-operations.png)

1. Ekkor megjelenik a központi telepítés adott lépésének részletei.

    ![Művelet részleteinek megjelenítése](./media/resource-manager-deployment-operations/show-operation-details.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Egy központi telepítés általános állapotának lekéréséhez használja a **Get-AzResourceGroupDeployment** parancsot.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup
```

Az eredményeket csak azokra a központi telepítések esetében szűrheti, amelyek sikertelenek voltak.

```azurepowershell-interactive
Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
```

A korrelációs azonosító a kapcsolódó események nyomon követésére szolgál, és hasznos lehet, ha technikai támogatással dolgozik az üzemelő példányok hibakereséséhez. A korrelációs azonosító beszerzéséhez használja a következőt:

```azurepowershell-interactive
(Get-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
```

Mindegyik üzemelő példány több műveletet tartalmaz. Az egyes műveletek a telepítési folyamat lépéseit jelölik. A központi telepítéssel kapcsolatos hibák felderítéséhez általában az üzembe helyezési műveletek részleteit kell megtekinteni. A **Get-AzResourceGroupDeploymentOperation**használatával megtekintheti a műveletek állapotát.

```azurepowershell-interactive
Get-AzResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName azuredeploy
```

Amely több műveletet ad vissza a következő formátumban:

```powershell
Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
OperationId    : A3EB2DA598E0A780
Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2019-05-13T21:42:40.7151512Z;
                duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
```

Ha további részleteket szeretne megtudni a sikertelen műveletekről, kérje le a **sikertelen** állapotú műveletek tulajdonságait.

```azurepowershell-interactive
(Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
```

Amely az összes sikertelen műveletet visszaadja a következő formátumban:

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

Jegyezze fel a művelet serviceRequestId és trackingId. A serviceRequestId hasznos lehet, ha technikai támogatással dolgozik az üzemelő példányok hibakereséséhez. A következő lépésben a trackingId fogja használni egy adott műveletre összpontosítva.

Egy adott sikertelen művelet állapotjelző üzenetének lekéréséhez használja a következő parancsot:

```azurepowershell-interactive
((Get-AzResourceGroupDeploymentOperation -DeploymentName azuredeploy -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
```

Amely a következőket adja vissza:

```powershell
code           message                                                                        details
----           -------                                                                        -------
DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
```

Az Azure-ban minden üzembe helyezési művelet tartalmazza a kérelmek és válaszok tartalmát. Az üzembe helyezés során a **DeploymentDebugLogLevel** paraméterrel adhatja meg, hogy a kérelem és/vagy a válasz naplózva van-e.

Ezt az információt a naplóból szerezheti be, és helyileg mentheti a következő PowerShell-parancsok használatával:

```powershell
(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

(Get-AzResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
```

## <a name="azure-cli"></a>Azure CLI

Egy központi telepítés általános állapotának beszerzéséhez használja az **Azure Group Deployment show** parancsot.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment
```
  
A korrelációs azonosító a kapcsolódó események nyomon követésére szolgál, és hasznos lehet, ha technikai támogatással dolgozik az üzemelő példányok hibakereséséhez.

```azurecli-interactive
az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
```

A központi telepítés műveleteinek megtekintéséhez használja a következőt:

```azurecli-interactive
az group deployment operation list -g ExampleGroup -n ExampleDeployment
```

## <a name="rest"></a>REST

A központi telepítéssel kapcsolatos információk lekéréséhez használja a [sablon központi telepítési](https://docs.microsoft.com/rest/api/resources/deployments) műveletével kapcsolatos információkat.

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
```

A válaszban jegyezze fel különösen a **provisioningState**, a **correlationId**és a **hiba** elemeit. A **correlationId** a kapcsolódó események nyomon követésére szolgál, és hasznos lehet, ha technikai támogatással dolgozik az üzemelő példányok hibakereséséhez.

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

A központi telepítésekkel kapcsolatos információk beszerzéséhez használja az [összes sablon központi telepítési műveleteit](https://docs.microsoft.com/rest/api/resources/deployments). 

```
GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
```
   
A válasz az üzembe helyezés során a **debugSetting** tulajdonságban megadott adatokon alapuló kérelmeket és/vagy válaszokat tartalmazza.

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
* Az egyes telepítési hibák elhárításával kapcsolatos segítségért lásd: [gyakori hibák megoldása az erőforrások Azure-ba való telepítésekor a Azure Resource Manager használatával](resource-manager-common-deployment-errors.md).
* Ha további információt szeretne arról, hogyan használhatja a tevékenység naplóit más típusú műveletek figyelésére, tekintse meg a [Tevékenységnaplók megtekintése az Azure-erőforrások kezeléséhez](resource-group-audit.md)című témakört.
* Az üzembe helyezés előtti ellenőrzéshez tekintse meg az [erőforráscsoport üzembe helyezése Azure Resource Manager sablonnal](resource-group-template-deploy.md)című témakört.

