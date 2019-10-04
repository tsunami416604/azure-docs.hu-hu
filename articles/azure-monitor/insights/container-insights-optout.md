---
title: Hogyan Stop figyelése az Azure Kubernetes Service-fürthöz |} A Microsoft Docs
description: Ez a cikk ismerteti az Azure-AKS-fürt figyelése az Azure Monitor szolgáltatással tárolók elvégzésétől módját.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/19/2019
ms.author: magoedte
ms.openlocfilehash: 376259686d1668d62cc79f340e2161ef11be5e12
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624369"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Az Azure Kubernetes Service (AKS) tárolókat és az Azure Monitor figyelésének leállítása

Miután engedélyezte az AKS-fürt figyelése, állítsa le a fürt figyelése, ha úgy dönt, hogy már nem szeretné figyelni tudja azt. Ez a cikk bemutatja, hogyan teheti ezt meg az Azure CLI vagy a megadott Azure Resource Manager-sablonokkal.  


## <a name="azure-cli"></a>Azure CLI

Használja a [az aks disable-bővítmények](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) paranccsal tiltsa le az Azure Monitor for containers szolgáltatásban. A parancs eltávolítja az ügynököt a fürtcsomópontokből, nem távolítja el a megoldást, vagy a már összegyűjtött és a Azure Monitor erőforrásban tárolt adatokat.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Kívánja újból engedélyezni a figyelést a fürt számára, lásd: [engedélyezze a monitorozást az Azure CLI-vel](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

Megadott van két Azure Resource Manager-sablon eltávolítása az erőforráscsoportban a megoldásokkal kapcsolatos forrásanyagok konzisztens és ismétlődő támogatásához. Az egyik egy JSON-sablon, amely meghatározza, hogy a rendszer hogyan állítsa le a figyelést, a másik pedig a konfigurált paraméterek értékeit, amelyekkel megadhatja azt az AK-fürterőforrás-azonosítót és erőforráscsoportot, amelyben a fürt telepítve van. 

Ha még nem ismeri a sablon segítségével üzembe helyezni erőforrásokat fogalma, lásd:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

>[!NOTE]
>A sablont a fürt azonos erőforráscsoporthoz kell telepíteni. Ha a sablon használatakor kihagyja a többi tulajdonságot vagy bővítményt, akkor azt eredményezheti a fürtből való eltávolításuk. Például *enableRBAC* a fürtben MEGVALÓSÍTott RBAC-házirendekhez, vagy *aksResourceTagValues* , ha címkéket adtak meg az AK-fürthöz.  
>

Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Kell futnia az Azure CLI 2.0.27-es vagy újabb. A verzió azonosításához futtassa `az --version`. Ha telepíteni vagy frissíteni szeretné az Azure CLI, lásd: kell [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-template"></a>Sablon létrehozása

1. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
           "type": "string",
           "metadata": {
             "description": "AKS Cluster Resource ID"
           }
       },
      "aksResourceLocation": {
        "type": "string",
        "metadata": {
           "description": "Location of the AKS resource e.g. \"East US\""
         }
       }
       },
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
      }
    },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]"
        "apiVersion": "2018-03-31",
        "properties": {
          "mode": "Incremental",
          "id": "[parameters('aksResourceId')]",
          "addonProfiles": {
            "omsagent": {
              "enabled": false,
              "config": null
            }
           }
         }
       }
      ]
    }
    ```

2. Mentse a fájlt **OptOutTemplate.json** egy helyi mappába.

3. A következő JSON-szintaxist illessze be a fájlba:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterRegion>"
        },
        "aksResourceTagValues": {
          "value": {
            "<existing-tag-name1>": "<existing-tag-value1>",
            "<existing-tag-name2>": "<existing-tag-value2>",
            "<existing-tag-nameN>": "<existing-tag-valueN>"
          }
        }
      }
    }
    ```

4. Értékeinek szerkesztéséhez **aksResourceId** és **aksResourceLocation** értékeit az AKS-fürtöt, amely a használatával a **tulajdonságok** a kijelölt fürt lap .

    ![Tároló tulajdonságai lap](media/container-insights-optout/container-properties-page.png)

    Ha a **tulajdonságok** területén is átmásolja a **munkaterület erőforrás-azonosító**. Az értéket kötelező megadni, ha úgy dönt, hogy később törli a Log Analytics-munkaterületet. Ez a folyamat részeként a Log Analytics-munkaterület törlése nem történik. 

    Szerkessze a **aksResourceTagValues** értékeit, hogy azok megfeleljenek az AK-fürthöz megadott meglévő címkék értékének.

5. Mentse a fájlt **OptOutParam.json** egy helyi mappába.

6. Készen áll a sablon üzembe helyezésére. 

### <a name="remove-the-solution-using-azure-cli"></a>Távolítsa el a megoldás az Azure CLI használatával

Futtassa a következő parancsot az Azure CLI használatával távolítsa el a megoldás és az AKS-fürt a konfiguráció tisztítása Linux rendszeren.

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

A megoldás eltávolítása és az AKS-fürt a konfiguráció tisztítása a sablont tartalmazó könyvtárban hajtsa végre a következő PowerShell-parancsokat.    

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName <yourSubscriptionName>
New-AzResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, egy üzenet, amely tartalmazza az eredmény az alábbihoz hasonló adja vissza:

```powershell
ProvisioningState       : Succeeded
```

Ha a munkaterület létrejött, csak a fürt figyelésére is alkalmas, és már nincs rá szükség, akkor törölje kézzel. Ha nem ismeri a munkaterület törlése, lásd: [törlése az Azure Log Analytics-munkaterületet az Azure Portallal](../../log-analytics/log-analytics-manage-del-workspace.md). Ne felejtse el a kapcsolatos a **munkaterület erőforrás-azonosító** azt korábban a 4. lépésben másolt, szükség van rá fog. 

