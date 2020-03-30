---
title: Az Azure Red Hat OpenShift fürt figyelésének leállítása | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan állíthatja le az Azure Red Hat OpenShift fürt figyelését az Azure Monitor tárolókhoz.
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: eff5203aeedd3c7ad283b55ba12f0e45a556460d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250733"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-cluster-with-azure-monitor-for-containers"></a>Az Azure Red Hat OpenShift fürt figyelésének leállítása az Azure Monitor tárolókhoz szolgáltatásával

Miután engedélyezte az Azure Red Hat OpenShift fürt figyelését, leállíthatja a fürt figyelését, ha úgy dönt, hogy már nem szeretné figyelni. Ez a cikk bemutatja, hogyan valósíthatom meg ezt a megadott Azure Resource Manager-sablonok használatával.  

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

Két Azure Resource Manager-sablon nal támogatja a megoldás-erőforrások konzisztens és ismételt eltávolítását az erőforráscsoportban. Az egyik egy JSON-sablon, amely meghatározza a figyelés leállításához szükséges konfigurációt, a másik pedig olyan paraméterértékeket tartalmaz, amelyeket konfigurál az OpenShift fürt erőforrásazonosítójának és az Azure-régiónak a megadásához, amelyben a fürt telepítve van.

Ha nem ismeri az erőforrások sablon használatával történő üzembe helyezésének fogalmát, olvassa el az:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)
* [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

Ha úgy dönt, hogy az Azure CLI, először telepítenie kell, és a CLI helyileg kell használni. Az Azure CLI 2.0.65-ös vagy újabb verzióját kell futtatnia. A verzió azonosításához `az --version`futtassa a futtassa a futtassa a futtassa a futtassa Ha telepítenie vagy frissítenie kell az Azure CLI-t, [olvassa el az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakört.

### <a name="create-template"></a>Sablon létrehozása

1. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

    ```json
    {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aroResourceId": {
           "type": "string",
           "metadata": {
             "description": "ARO Cluster Resource ID"
          }
        },
        "aroResourceLocation": {
          "type": "string",
          "metadata": {
            "description": "Location of the aro cluster resource e.g. westcentralus"
          }
        }
      },
      "resources": [
        {
           "name": "[split(parameters('aroResourceId'),'/')[8]]",
           "type": "Microsoft.ContainerService/openShiftManagedClusters",
           "location": "[parameters('aroResourceLocation')]",
           "apiVersion": "2019-09-30-preview",
           "properties": {
             "mode": "Incremental",
             "id": "[parameters('aroResourceId')]",
             "monitorProfile": {
               "workspaceResourceID": null,
               "enabled": false
            }
          }
        }
      ]
    }
    ```

2. Mentse ezt a fájlt **OptOutTemplate.json néven** egy helyi mappába.

3. Illessze be a következő JSON-szintaxist a fájlba:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aroResourceId": {
          "value": "/subscriptions/<subscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.ContainerService/openShiftManagedClusters/<clusterName>"
        },
        "aroResourceLocation": {
          "value": "<azure region of the cluster e.g. westcentralus>"
        }
      }
    }
    ```

4. Az **aroResourceId** és az **aroResourceLocation** értékeit az OpenShift fürt értékei alapján szerkesztheti, amely a kijelölt fürt **Tulajdonságok** lapján található.

    ![Tároló tulajdonságai lap](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Mentse ezt a fájlt **OptOutParam.json néven** egy helyi mappába.

6. Készen áll a sablon üzembe helyezésére.

### <a name="remove-the-solution-using-azure-cli"></a>A megoldás eltávolítása az Azure CLI használatával

Hajtsa végre a következő parancsot az Azure CLI Linuxon a megoldás eltávolításához és a fürt konfigurációjának karbantartásához.

```azurecli
az login   
az account set --subscription "Subscription Name"
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

A konfiguráció módosítása néhány percet is igénybe vehet. Ha befejeződött, az alábbihoz hasonló üzenetet ad vissza a rendszer, amely tartalmazza az eredményt:

```output
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>A megoldás eltávolítása a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Hajtsa végre a következő PowerShell-parancsokat a sablont tartalmazó mappában a megoldás eltávolításához és a konfiguráció fürtökből való eltávolításához.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

A konfiguráció módosítása néhány percet is igénybe vehet. Ha befejeződött, az alábbihoz hasonló üzenetet ad vissza a rendszer, amely tartalmazza az eredményt:

```output
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>További lépések

Ha a munkaterület csak a fürt figyelésének támogatására jött létre, és már nincs rá szükség, manuálisan kell törölnie. Ha nem ismeri a munkaterület törlésének módját, olvassa el [az Azure Log Analytics-munkaterület törlése című témakört.](../../log-analytics/log-analytics-manage-del-workspace.md)
