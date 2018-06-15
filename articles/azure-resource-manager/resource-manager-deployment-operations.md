---
title: Az Azure Resource Manager üzembe helyezési műveleteinek |} Microsoft Docs
description: Útmutató megtekintéséhez az Azure Resource Manager üzembe helyezési műveleteket a portál, a PowerShell, az Azure CLI és a REST API-t.
services: azure-resource-manager,virtual-machines
documentationcenter: ''
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-multiple
ms.workload: infrastructure
ms.date: 04/23/2018
ms.author: tomfitz
ms.openlocfilehash: 523ea3bf5d41231ab3281f9d8eb1fac8c3dfb55f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359145"
---
# <a name="view-deployment-operations-with-azure-resource-manager"></a>Az Azure Resource Manager központi telepítési műveletek megtekintése

A műveletek a központi telepítés az Azure portálon keresztül tekintheti meg. Előfordulhat, hogy tervezheti meg a műveletek megtekintése hiba beérkezése után üzembe helyezése során, ez a cikk foglalkozik, amelyek nem tudták műveletek megtekintése. A portál, amely lehetővé teszi, hogy könnyedén megtalálhatja a hibák, és határozza meg a lehetséges javítások felületet biztosít.

A központi telepítés elháríthatja a naplókat, és a telepítési műveleteket. Ez a cikk mindkét módszer bemutatja. Segítség az adott telepítési hibáinak megoldása: [gyakori hibák feloldása, amikor erőforrásokat üzembe helyezi az Azure-bA az Azure Resource Manager](resource-manager-common-deployment-errors.md).

## <a name="portal"></a>Portál
Üzembe helyezési műveleteinek megtekintéséhez tegye a következőket:

1. Az erőforráscsoport részt vesz a telepítés figyelje meg a legutóbbi központi telepítés állapotát. Ez az állapot részleteinek kiválasztása
   
    ![Központi telepítési állapota](./media/resource-manager-deployment-operations/deployment-status.png)
2. Megjelenik a legutóbbi központi telepítés előzményei. Válassza ki a telepítést, melyeknél nem sikerült.
   
    ![Központi telepítési állapota](./media/resource-manager-deployment-operations/select-deployment.png)
3. Válassza ki a hivatkozásra kattintva megtekintheti a központi telepítés sikertelenségének leírását. Az alábbi ábrán a DNS-rekord nem egyedi.  
   
    ![sikertelen központi telepítés megtekintése](./media/resource-manager-deployment-operations/view-error.png)
   
    Ez a hibaüzenet jelenik meg a hibaelhárítás elegendő kell lennie. Azonban ha kapcsolatos további részleteket kell mely feladatok befejeződtek, megtekintheti a műveleteket, ahogy az az alábbi lépéseket.
4. Megtekintheti a telepítési műveleteket. Válassza ki a bármilyen műveletet további részletek megtekintéséhez.
   
    ![műveletek megtekintése](./media/resource-manager-deployment-operations/view-operations.png)
   
    Ebben az esetben láthatja, hogy a tárfiókot, a virtuális hálózat és a rendelkezésre állási csoport sikeresen létrehozva. A nyilvános IP-címet nem sikerült, és más erőforrások nem megkísérelte.
5. Kiválasztásával megtekintheti a központi telepítés események **események**.
   
    ![események megtekintése](./media/resource-manager-deployment-operations/view-events.png)
6. A központi telepítés az eseményeket, és válassza ki a közül legalább egy további részleteket. Figyelje meg a korrelációs azonosítót. Ez az érték akkor lehet hasznos, amikor olyan központi telepítés hibaelhárítása a technikai támogatási szolgálathoz.
   
    ![események](./media/resource-manager-deployment-operations/see-all-events.png)

## <a name="powershell"></a>PowerShell
1. Ahhoz, hogy a központi telepítés összesített állapotát, használja a **Get-AzureRmResourceGroupDeployment** parancsot. 

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup
  ```

   Vagy csak a sikertelen telepítések az eredményeket szűrheti is.

  ```powershell
  Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
  ```
   
2. Minden központi telepítési több műveletet tartalmaz. Minden műveletet a telepítési folyamat azon lépése jelöli. Annak megállapításához, hogy mi a probléma telepítés, általában szüksége üzembe helyezési műveleteinek kapcsolatos részletek megtekintéséhez. Láthatja, hogy a műveletek állapotának **Get-AzureRmResourceGroupDeploymentOperation**.

  ```powershell 
  Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName vmDeployment
  ```

    Több művelet található a következő formátumban mindegyiknél visszaadó:

  ```powershell
  Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
  OperationId    : A3EB2DA598E0A780
  Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                   duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                   serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
  PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                   serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}
  ```

3. Ahhoz, hogy további információt a sikertelen műveleteket, a műveletek tulajdonságainak lekérése **sikertelen** állapotát.

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
  ```
   
    Amely adja vissza a sikertelen műveleteket a mindegyiknél a következő formátumban:

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

    Vegye figyelembe a serviceRequestId és a trackingId a művelethez. A serviceRequestId akkor lehet hasznos, amikor olyan központi telepítés hibaelhárítása a technikai támogatási szolgálathoz. Szüksége lesz a trackingId a következő lépésben egy adott művelethez összpontosíthat.
4. Ahhoz, hogy egy adott sikertelen művelettel állapotüzenetet, használja a következő parancsot:

  ```powershell
  ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
  ```

    Amely adja vissza:

  ```powershell
  code           message                                                                        details
  ----           -------                                                                        -------
  DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}
  ```
4. Minden központi telepítési műveletet az Azure-ban kérés- és tartalmát tartalmazza. A kérelem tartalma az Ön által küldött Azure üzembe helyezése során (például hozzon létre egy virtuális Gépet, az operációsrendszer-lemez és az egyéb erőforrások). A válasz tartalma az Azure által küldött vissza a központi telepítési kérelemből. A telepítés során használhatja **DeploymentDebugLogLevel** paraméterrel megadhatja, hogy a kérelem és/vagy a válasz a naplóba kerülnek. 

  Ez az információ lekérése a naplót, és mentse helyileg a következő PowerShell-parancsok használatával:

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.request | ConvertTo-Json |  Out-File -FilePath <PathToFile>

  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName "TestDeployment" -ResourceGroupName "Test-RG").Properties.response | ConvertTo-Json |  Out-File -FilePath <PathToFile>
  ```

## <a name="azure-cli"></a>Azure CLI

1. Az alkalmazáspéldány általános állapotának lekérése a **azure-csoportok telepítési megjelenítése** parancsot.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment
  ```
  
1. A visszaadott értékek közül egy a **correlationId**. Ez az érték a kapcsolódó események nyomon követésére szolgál, és hasznos lehet, amikor olyan központi telepítés hibaelhárítása a technikai támogatási szolgálathoz.

  ```azurecli
  az group deployment show -g ExampleGroup -n ExampleDeployment --query properties.correlationId
  ```

1. A központi telepítés műveletek megjelenítéséhez használja:

  ```azurecli
  az group deployment operation list -g ExampleGroup -n ExampleDeployment
  ```

## <a name="rest"></a>REST

1. A központi telepítés adatainak beolvasása a [sablon-üzembehelyezés adatainak beolvasása](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_Get) műveletet.

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
  ```

    A válaszban, vegye figyelembe különösen a **provisioningState**, **correlationId**, és **hiba** elemek. A **correlationId** kapcsolódó események nyomon követésére szolgál, és hasznos lehet, amikor olyan központi telepítés hibaelhárítása a technikai támogatási szolgálathoz.

  ```json
  { 
    ...
    "properties": {
      "provisioningState":"Failed",
      "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
      ...
      "error":{
        "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
        "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
      }  
    }
  }
  ```

2. A központi telepítések adatainak beolvasása [összes sablon telepítési művelet](https://docs.microsoft.com/rest/api/resources/deployments#Deployments_List). 

  ```http
  GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}
  ```
   
    A válasz alapján megadva a kérelem és/vagy válasz információkat tartalmaz a **debugSetting** tulajdonság üzembe helyezése során.

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
* Segítség az adott telepítési hibáinak megoldása: [gyakori hibák feloldása, amikor erőforrásokat üzembe helyezi az Azure-bA az Azure Resource Manager](resource-manager-common-deployment-errors.md).
* A tevékenység-naplók segítségével figyelheti a más típusú műveleteket, lásd: [tevékenységi naplóit Azure-erőforrások kezeléséhez megtekintése](resource-group-audit.md).
* A telepítés előtt futtatnia kell az ellenőrzéséhez tekintse meg a [Azure Resource Manager sablonnal erőforráscsoport telepítése](resource-group-template-deploy.md).

