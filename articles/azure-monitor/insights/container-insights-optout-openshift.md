---
title: Az Azure Red Hat OpenShift-fürt figyelésének leállítása | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan állíthatja le a Azure Monitor for containers szolgáltatással az Azure Red Hat OpenShift-fürt figyelését.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/21/2019
ms.openlocfilehash: f769749532a05260bf3c2c9f99483c5607d985a6
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384330"
---
# <a name="how-to-stop-monitoring-your-azure-red-hat-openshift-cluster-with-azure-monitor-for-containers"></a>Az Azure Red Hat OpenShift-fürt figyelésének leállítása a Azure Monitor tárolókkal

Miután engedélyezte az Azure Red Hat OpenShift-fürt figyelését, leállíthatja a fürt figyelését, ha úgy dönt, hogy már nem szeretné figyelni. Ez a cikk bemutatja, hogyan hajthatja végre ezt a megadott Azure Resource Manager-sablonok használatával.  

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

Megadott van két Azure Resource Manager-sablon eltávolítása az erőforráscsoportban a megoldásokkal kapcsolatos forrásanyagok konzisztens és ismétlődő támogatásához. Az egyik egy JSON-sablon, amely meghatározza, hogy a rendszer hogyan állítsa le a figyelést, a másik pedig olyan paramétereket tartalmaz, amelyeket úgy konfigurál, hogy megadja a fürt üzembe helyezett OpenShift-erőforrás-AZONOSÍTÓját és az Azure-régiót. 

Ha még nem ismeri a sablon segítségével üzembe helyezni erőforrásokat fogalma, lásd:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI-vel](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Az Azure CLI 2.0.65 vagy újabb verzióját kell futtatnia. A verzió azonosításához futtassa `az --version`. Ha telepítenie vagy frissítenie kell az Azure CLI-t, tekintse meg [Az Azure CLI telepítését](https://docs.microsoft.com/cli/azure/install-azure-cli)ismertető témakört. 

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

2. Mentse ezt a fájlt **OptOutTemplate. JSON** néven egy helyi mappába.

3. A következő JSON-szintaxist illessze be a fájlba:

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

4. Szerkessze a **aroResourceId** és a **aroResourceLocation** értékeit a OpenShift-fürt értékeivel, amelyet a kiválasztott fürt **Tulajdonságok** lapján talál.

    ![Tároló tulajdonságai lap](media/container-insights-optout-openshift/cluster-properties-page.png)

5. Mentse ezt a fájlt **OptOutParam. JSON** néven egy helyi mappába.

6. Készen áll a sablon üzembe helyezésére. 

### <a name="remove-the-solution-using-azure-cli"></a>Távolítsa el a megoldás az Azure CLI használatával

Futtassa a következő parancsot az Azure CLI-vel Linuxon a megoldás eltávolításához és a fürt konfigurációjának tisztításához.

```azurecli
az login   
az account set --subscription "Subscription Name" 
az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
```

A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, egy üzenet, amely tartalmazza az eredmény az alábbihoz hasonló adja vissza:

```azurecli
ProvisioningState       : Succeeded
```

### <a name="remove-the-solution-using-powershell"></a>Távolítsa el a megoldás a PowerShell használatával

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Hajtsa végre a következő PowerShell-parancsokat a sablont tartalmazó mappában, hogy eltávolítsa a megoldást, és törölje a konfigurációt a fürtből.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, egy üzenet, amely tartalmazza az eredmény az alábbihoz hasonló adja vissza:

```powershell
ProvisioningState       : Succeeded
```

## <a name="next-steps"></a>Következő lépések

Ha a munkaterület létrejött, csak a fürt figyelésére is alkalmas, és már nincs rá szükség, akkor törölje kézzel. Ha nem ismeri a munkaterület törlésének módját, tekintse meg [Az Azure log Analytics munkaterület törlése](../../log-analytics/log-analytics-manage-del-workspace.md)című témakört. 
