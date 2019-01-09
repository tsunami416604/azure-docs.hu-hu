---
title: Az Azure Resource Manager üzembe helyezési műveletek |} A Microsoft Docs
description: Útmutató a portal, PowerShell, az Azure CLI és REST API-t az Azure Resource Manager üzembe helyezési műveletek megtekintése.
services: azure-resource-manager,virtual-machines
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 09/28/2018
ms.author: tomfitz
ms.openlocfilehash: 8fee1e29ab3a267d77e4e43beb2c42587da5314d
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54103859"
---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Üzembehelyezési műveletek megtekintése az Azure Resource Managerrel

A műveletek esetében egy üzembe helyezést az Azure Portalon tekintheti meg. Előfordulhat, hogy iránt érdeklődnek a leginkább megtekintése a műveleteket, ha üzembe helyezés során a így ez a cikk foglalkozik, amelyek nem tudták műveletek megtekintése a hibaüzenetet kapott. A portál egy felületet kínál, amely lehetővé teszi a hibák megkereséséhez és esetleges javításokat határozza meg.

A vizsgálati naplók, vagy az üzembe helyezési műveletek megnézzük háríthatóak el a központi telepítés. Ez a cikk mindkét módszert bemutatja. Segítség az adott központi telepítési hibák elhárítása: [gyakori hibák elhárításához erőforrások üzembe helyezésekor az Azure-bA az Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portál
Az üzembe helyezési műveletek megtekintéséhez használja az alábbi lépéseket:

1. Az erőforráscsoport a telepítésben érintett figyelje meg a legutóbbi üzembe helyezés állapotát. Ez az állapot részleteinek kiválasztása
   
    ![Telepítés állapota](./media/resource-manager-deployment-operations/deployment-status.png)
2. A legutóbbi üzembe helyezési előzmények láthatja. Válassza ki a telepítést, amely nem sikerült.
   
    ![Telepítés állapota](./media/resource-manager-deployment-operations/select-deployment.png)
3. Válassza ki a hivatkozásra kattintva megtekintheti az üzembe helyezés sikertelenségének leírását. Az alábbi képen a DNS-rekord nem egyedi.  
   
    ![sikertelen üzembe helyezés megtekintése](./media/resource-manager-deployment-operations/view-error.png)
   
    Ez a hibaüzenet lehet elegendő az, hogy a hibaelhárítás megkezdése. Azonban ha kapcsolatos további részleteket kell milyen feladatokat hajtottuk végre, akkor az a műveleteket, ahogyan az alábbi lépéseket.
4. Megtekintheti az összes központi telepítési műveletet. Válassza ki minden olyan művelet kiválasztásával további részleteket.
   
    ![műveletek megtekintése](./media/resource-manager-deployment-operations/view-operations.png)
   
    Ebben az esetben láthatja, hogy a tárfiókot, virtuális hálózat és a rendelkezésre állási csoport sikeresen létrehozva. A nyilvános IP-címet nem sikerült, és más erőforrások nem került sor.
5. Megtekintheti a központi telepítés események kiválasztásával **események**.
   
    ![Események megtekintése](./media/resource-manager-deployment-operations/view-events.png)
6. Tekintse meg a központi telepítés összes eseményt, és válassza ki a további részletekért egyikével sem. Figyelje meg a korrelációs azonosítók. Ezt az értéket az használata a technikai támogatáshoz a központi telepítés hibaelhárítása során lehet hasznos.
   
    ![események megtekintéséhez](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. A központi telepítés összesített állapotát lekéréséhez használja a **Get-AzureRmResourceGroupDeployment** parancsot. 

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup
  ```

   Vagy csak ezen központi telepítések, melyeket nem sikerült az eredményeket szűrheti.

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
  ```
   
1. A korrelációs Azonosítót használja:

  ```powershell
  (Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName azuredeploy).CorrelationId
  ```

1. Minden üzembe helyezés több műveleteit tartalmazza. Minden művelet egy lépése az üzembe helyezési folyamat jelöli. Fedezze fel Mi történt egy központi telepítést, általában kell az üzembe helyezési műveletek részleteinek megtekintéséhez. Láthatja, hogy a műveletek állapotának **Get-AzureRmResourceGroupDeploymentOperation**.

  ```powershell 
  Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
  ```

    Több műveletet a következő formátumban mindegyikhez ad vissza:

  ```powershell
  Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
  OperationId    : A3EB2DA598E0A780
  Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
  PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
  ```

1. Sikertelen műveletek kapcsolatos további információért a műveletek a tulajdonságainak lekérése **sikertelen** állapota.

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
  ```
   
    A sikertelen műveletek mindegyikhez a következő formátumban ad vissza:

  ```powershell
  provisioningOperation : Create
  provisioningState     : Failed
  timestamp             : 2016-06-14T21:54:55.1468068Z
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
1. Az állapotüzenet egy adott sikertelen művelet lekéréséhez használja a következő parancsot:

  ```powershell
  ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
  ```

    Amely értéket ad vissza:

  ```powershell
  code           message                                                                        details
  ----           -------                                                                        -------
  DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
  ```
1. Minden központi telepítési műveletet az Azure-ban a kérések és válaszok tartalmát tartalmazza. A kérelem tartalma az Ön által küldött az Azure-ban üzembe helyezés során (például hozzon létre egy virtuális gép operációsrendszer-lemez és az egyéb erőforrásokat). A válasz tartalma az Azure által küldött vissza a telepítési kérelemből. A telepítés során használhat **DeploymentDebugLogLevel** paraméterrel megadhatja, hogy a kérelem és/vagy a válasz megmaradnak a naplóban. 

  Ezt az információt kérhet a naplót, és mentse helyileg a következő PowerShell-parancsok használatával:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
  ```

## <a name="azure-cli"></a>Azure CLI

1. Általános állapotát a telepítésben a **azure csoport deployment show** parancsot.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment
  ```
  
1. A visszaadott értékek egyike a **correlationId**. Ez az érték a kapcsolódó események nyomon követésére szolgál, és használata a technikai támogatáshoz a központi telepítés hibaelhárítása során lehet hasznos.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
  ```

1. A műveletek esetében egy központi telepítést használja:

  ```azurecli
  az group deployment operation list -g ExampleGroup -n ExampleDeployment
  ```

## <a name="rest"></a>REST

1. A központi telepítés adatainak lekérése a [-sablonalapú telepítéssel kapcsolatos információk lekérése](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get) műveletet.

  ```http
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

2. A központi telepítések adatainak lekérése [összes sablon telepítési művelet listázására](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_List). 

  ```http
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

