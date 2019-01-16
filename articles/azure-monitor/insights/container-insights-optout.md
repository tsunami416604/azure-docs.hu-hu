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
ms.date: 12/13/2018
ms.author: magoedte
ms.openlocfilehash: b462a2baecbaf9aaf3634964ca279b320b77dc1b
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54330687"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Az Azure Kubernetes Service (AKS) tárolókat és az Azure Monitor figyelésének leállítása

Miután engedélyezte az AKS-fürt figyelése, állítsa le a fürt figyelése, ha úgy dönt, hogy már nem szeretné figyelni tudja azt. Ez a cikk bemutatja, hogyan teheti ezt meg az Azure CLI vagy a megadott Azure Resource Manager-sablonokkal.  


## <a name="azure-cli"></a>Azure CLI
Használja a [az aks disable-bővítmények](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) paranccsal tiltsa le az Azure Monitor for containers szolgáltatásban. A parancs eltávolítja az ügynököt a fürt csomópontjai, nem távolítja el a megoldás vagy az adatok már összegyűjtött és tárolt a Log Analytics-erőforrás.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

Kívánja újból engedélyezni a figyelést a fürt számára, lásd: [engedélyezze a monitorozást az Azure CLI-vel](container-insights-onboard.md#enable-monitoring-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon
Megadott van két Azure Resource Manager-sablon eltávolítása az erőforráscsoportban a megoldásokkal kapcsolatos forrásanyagok konzisztens és ismétlődő támogatásához. Egyik megadása leállíthatja a figyelést, a konfigurációs JSON-sablon, a másik, konfigurálja úgy, hogy adja meg az AKS Azonosítót és erőforrás erőforráscsoportot, amely a fürt üzembe lesz helyezve, a paraméterértékeket tartalmaz. 

Ha még nem ismeri a sablon segítségével üzembe helyezni erőforrásokat fogalma, lásd:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

>[!NOTE]
>A sablonhoz telepíteni szeretné ugyanabban az erőforráscsoportban a fürttel. Ez a sablon használata esetén a Tulajdonságok vagy bővítmények kihagyása eredményezhet azok eltávolítása a fürtből. Ha például *enableRBAC*.  
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
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
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
        }
      }
    }
    ```

4. Értékeinek szerkesztéséhez **aksResourceId** és **aksResourceLocation** értékeit az AKS-fürtöt, amely a használatával a **tulajdonságok** a kijelölt fürt lap .

    ![Tároló tulajdonságai lap](media/container-insights-optout/container-properties-page.png)

    Ha a **tulajdonságok** területén is átmásolja a **munkaterület erőforrás-azonosító**. Az értéket kötelező megadni, ha úgy dönt, hogy később törli a Log Analytics-munkaterületet. Ez a folyamat részeként a Log Analytics-munkaterület törlése nem történik. 

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

A megoldás eltávolítása és az AKS-fürt a konfiguráció tisztítása a sablont tartalmazó könyvtárban hajtsa végre a következő PowerShell-parancsokat.    

```powershell
Connect-AzureRmAccount
Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
```

A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, egy üzenet, amely tartalmazza az eredmény az alábbihoz hasonló adja vissza:

```powershell
ProvisioningState       : Succeeded
```

Ha a munkaterület létrejött, csak a fürt figyelésére is alkalmas, és már nincs rá szükség, akkor törölje kézzel. Ha nem ismeri a munkaterület törlése, lásd: [törlése az Azure Log Analytics-munkaterületet az Azure Portallal](../../log-analytics/log-analytics-manage-del-workspace.md). Ne felejtse el a kapcsolatos a **munkaterület erőforrás-azonosító** azt korábban a 4. lépésben másolt, szükség van rá fog. 

