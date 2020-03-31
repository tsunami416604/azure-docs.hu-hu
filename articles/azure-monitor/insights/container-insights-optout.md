---
title: Az Azure Kubernetes-szolgáltatásfürt figyelésének leállítása | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan szüntetheti meg az Azure AKS-fürt figyelését az Azure Monitor tárolók.
ms.topic: conceptual
ms.date: 08/19/2019
ms.openlocfilehash: 618a4d7e10212dd2b042724b1ea11c97920dad57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275255"
---
# <a name="how-to-stop-monitoring-your-azure-kubernetes-service-aks-with-azure-monitor-for-containers"></a>Az Azure Kubernetes Service (AKS) tárolókhoz készült Azure Monitorral való monitorozásának leállítása

Miután engedélyezte az AKS-fürt figyelését, leállíthatja a fürt figyelését, ha úgy dönt, hogy már nem kívánja figyelni. Ez a cikk bemutatja, hogyan valósíthatom meg ezt az Azure CLI vagy a megadott Azure Resource Manager-sablonok használatával.  


## <a name="azure-cli"></a>Azure CLI

Az [aks disable-addons](https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-disable-addons) paranccsal tiltsa le az Azure Monitor tárolók. A parancs eltávolítja az ügynököt a fürtcsomópontokból, nem távolítja el a megoldást vagy az Azure Monitor-erőforrásban már összegyűjtött és tárolt adatokat.  

```azurecli
az aks disable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG
```

A fürt figyelésének újbóli engedélyezéséhez olvassa [el A figyelés engedélyezése az Azure CLI használatával](container-insights-enable-new-cluster.md#enable-using-azure-cli).

## <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

Két Azure Resource Manager-sablon nal támogatja a megoldás-erőforrások konzisztens és ismételt eltávolítását az erőforráscsoportban. Az egyik egy JSON-sablon, amely megadja a figyelés leállításához szükséges konfigurációt, a másik pedig olyan paraméterértékeket tartalmaz, amelyeket konfigurál az AKS-fürt erőforrásazonosítójának és erőforráscsoportjának megadásához, amelyben a fürt telepítve van.

Ha nem ismeri az erőforrások sablon használatával történő üzembe helyezésének fogalmát, olvassa el az:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)
* [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

>[!NOTE]
>A sablont a fürt ugyanazon erőforráscsoportjában kell telepíteni. Ha a sablon használatakor kihagy más tulajdonságokat vagy bővítményeket, az azok eltávolítását eredményezheti a fürtből. Például *enableRBAC* a fürtben megvalósított RBAC-házirendekhez, vagy *az aksResourceTagValues,* ha címkék vannak megadva az AKS-fürthöz.  
>

Ha úgy dönt, hogy az Azure CLI, először telepítenie kell, és a CLI helyileg kell használni. Az Azure CLI 2.0.27-es vagy újabb verzióját kell futtatnia. A verzió azonosításához `az --version`futtassa a futtassa a futtassa a futtassa a futtassa Ha telepítenie vagy frissítenie kell az Azure CLI-t, [olvassa el az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakört.

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
       },
    "aksResourceTagValues": {
      "type": "object",
      "metadata": {
        "description": "Existing all tags on AKS Cluster Resource"
        }
      }
     },
    "resources": [
      {
        "name": "[split(parameters('aksResourceId'),'/')[8]]",
        "type": "Microsoft.ContainerService/managedClusters",
        "location": "[parameters('aksResourceLocation')]",
        "tags": "[parameters('aksResourceTagValues')]",
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

2. Mentse ezt a fájlt **OptOutTemplate.json néven** egy helyi mappába.

3. Illessze be a következő JSON-szintaxist a fájlba:

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

4. Az **aksResourceId** és az **aksResourceLocation értékeit** az AKS-fürt értékei alapján szerkesztheti, amely a kijelölt fürt **Tulajdonságok** lapján található.

    ![Tároló tulajdonságai lap](media/container-insights-optout/container-properties-page.png)

    A **Tulajdonságok** lapon másolja a **Munkaterület-erőforrás-azonosítót**is. Erre az értékre akkor van szükség, ha később úgy dönt, hogy törölni szeretné a Log Analytics-munkaterületet. A Log Analytics-munkaterület törlése nem hajtható végre a folyamat részeként.

    Az **aksResourceTagValues értékeinek** szerkesztése az AKS-fürthöz megadott meglévő címkeértékeknek megfelelően.

5. Mentse ezt a fájlt **OptOutParam.json néven** egy helyi mappába.

6. Készen áll a sablon üzembe helyezésére.

### <a name="remove-the-solution-using-azure-cli"></a>A megoldás eltávolítása az Azure CLI használatával

Hajtsa végre a következő parancsot az Azure CLI Linuxon a megoldás eltávolításához és az AKS-fürt konfigurációjának karbantartásához.

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

Hajtsa végre a következő PowerShell-parancsokat a sablont tartalmazó mappában a megoldás eltávolításához és a konfiguráció a KS-fürtből való eltávolításához.    

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

Ha a munkaterület csak a fürt figyelésének támogatására jött létre, és már nincs rá szükség, manuálisan kell törölnie. Ha nem ismeri a munkaterület törlésének módját, olvassa el [az Azure Log Analytics-munkaterület törlése az Azure Portalon című témakört.](../../log-analytics/log-analytics-manage-del-workspace.md) Ne feledkezzen meg a **4.**
