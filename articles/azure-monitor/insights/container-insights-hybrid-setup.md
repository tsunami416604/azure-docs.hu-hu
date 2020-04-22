---
title: Hibrid Kubernetes-fürtök konfigurálása az Azure Monitor tárolókhoz való használatával | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan konfigurálhatja az Azure Monitor tárolók az Azure Stackben vagy más környezetben üzemeltetett Kubernetes-fürtök figyelésére.
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: 6c2782fdd810403a793f6ef682d1112fadc22465
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769044"
---
# <a name="configure-hybrid-kubernetes-clusters-with-azure-monitor-for-containers"></a>Hibrid Kubernetes-fürtök konfigurálása az Azure Monitor tárolókkal

Az Azure Monitor tárolók hoz biztosít gazdag figyelési élményt az Azure Kubernetes szolgáltatás (AKS) és az [AKS-motor az Azure-ban,](https://github.com/Azure/aks-engine)amely egy saját felügyeletű Kubernetes-fürt az Azure-ban üzemeltetett. Ez a cikk ismerteti, hogyan engedélyezheti az Azure-on kívül üzemeltetett Kubernetes-fürtök figyelését, és hasonló figyelési élményt érhet el.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy a következőket kapta:

* Egy Log Analytics-munkaterület.

    Az Azure Monitor tárolók támogatja a Log Analytics munkaterületet a felsorolt régiókban az [Azure-termékek régiónként.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor) Saját munkaterület létrehozásához létrehozható az [Azure Resource Manageren](../platform/template-workspace-configuration.md)keresztül a [PowerShellen](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)vagy az [Azure Portalon](../learn/quick-create-workspace.md)keresztül.

    >[!NOTE]
    >Több fürt ugyanazon fürtnévvel rendelkező fürtök figyelésének engedélyezése nem támogatott. A fürtneveknek egyedinek kell lenniük.
    >

* Ön a Log **Analytics közreműködői szerepkör** tagja a tárolófigyelés engedélyezéséhez. A Log Analytics-munkaterülethez való hozzáférés szabályozásáról a [Munkaterülethez való hozzáférés és az adatok naplózása című](../platform/manage-access.md) témakörben talál további információt.

* [HELM-ügyfél](https://helm.sh/docs/using_helm/) a megadott Kubernetes-fürt tárolók diagramjának az Azure Monitor szolgáltatásának fedélzetre.

* A következő proxy- és tűzfalkonfigurációs adatok szükségesek a Linux-naplóügynök tárolóalapú verziójához az Azure Monitorral való kommunikációhoz:

    |Ügynök erőforrása|Portok |
    |------|---------|   
    |*.ods.opinsights.azure.com |443-as port |  
    |*.oms.opinsights.azure.com |443-as port |  
    |*.blob.core.windows.net |443-as port |  
    |*.dc.services.visualstudio.com |443-as port |

* A tárolóügynök kubelet et `cAdvisor secure port: 10250` `unsecure port :10255` igényel, vagy meg kell nyitni a fürt összes csomópontján teljesítménymetrikák gyűjtéséhez. Javasoljuk, hogy `secure port: 10250` konfigurálja a Kubelet cAdvisorját, ha még nincs konfigurálva.

* A tárolóügynök megköveteli, hogy a következő környezeti változókat kell megadni a tárolón annak érdekében, hogy `KUBERNETES_SERVICE_HOST` kommunikáljon a Kubernetes API-szolgáltatással a fürtön belül a készletadatok gyűjtéséhez - és `KUBERNETES_PORT_443_TCP_PORT`.

>[!IMPORTANT]
>A hibrid Kubernetes-fürtök figyeléséhez támogatott minimális ügynökverzió a ciprod10182019 vagy újabb.

## <a name="supported-configurations"></a>Támogatott konfigurációk

Az alábbiakat hivatalosan is támogatja az Azure Monitor tárolók.

- Környezetek: Kubernetes a helyszínen, AKS-motor az Azure-ban és az Azure Stack. További információ: [AKS Engine on Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908).
- A Kubernetes és a támogatási házirend verziói megegyeznek a [támogatott AKS-verziókkal.](../../aks/supported-kubernetes-versions.md)
- Tároló futásidejű: Docker és Moby
- Linux operációs rendszer kiadása a mester és a munkás csomópontok: Ubuntu (18.04 LTS és 16.04 LTS)
- Hozzáférés-vezérlés támogatott: Kubernetes RBAC és nem RBAC

## <a name="enable-monitoring"></a>Monitorozás engedélyezése

Az Azure Monitor engedélyezése a hibrid Kubernetes-fürt tárolóihoz a következő lépések sorrendben történő végrehajtásából áll.

1. Konfigurálja a Log Analytics-munkaterületet a Container Insights-megoldással.

2. Engedélyezze az Azure Monitor tárolók HELM-diagram a Log Analytics munkaterülettel.

### <a name="how-to-add-the-azure-monitor-containers-solution"></a>Az Azure Monitor-tárolók megoldás hozzáadása

A megoldás üzembe helyezheti a megadott Azure Resource Manager sablon `New-AzResourceGroupDeployment` segítségével az Azure PowerShell-parancsmag vagy az Azure CLI használatával.

Ha nem ismeri az erőforrások sablon használatával történő üzembe helyezésének fogalmát, olvassa el az:

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)

* [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

Ha úgy dönt, hogy az Azure CLI, először telepítenie kell, és a CLI helyileg kell használni. Az Azure CLI 2.0.59-es vagy újabb verzióját kell futtatnia. A verzió azonosításához `az --version`futtassa a futtassa a futtassa a futtassa a futtassa Ha telepítenie vagy frissítenie kell az Azure CLI-t, [olvassa el az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakört.

Ez a módszer két JSON-sablont tartalmaz. Az egyik sablon a figyelés engedélyezéséhez a konfigurációt adja meg, a másik pedig a következők megadására konfigurált paraméterértékeket tartalmazza:

- **workspaceResourceId** – a Log Analytics-munkaterület teljes erőforrásazonosítója.
- **workspaceRegion** – az a régió, amelyben a munkaterület létrejön, amelyet az Azure Portalról való megtekintéskor a munkaterület tulajdonságaiban **helyként** is neveznek.

Először azonosítsa a Log Analytics-munkaterület teljes `workspaceResourceId` erőforrás-azonosítóját, amely a **containerSolutionParams.json** fájl paraméterértékéhez szükséges, hajtsa végre a következő lépéseket, majd futtassa a PowerShell-parancsmag vagy az Azure CLI parancsot a megoldás hozzáadásához.

1. Sorolja fel az összes olyan előfizetést, amelyhez hozzáférése van a következő parancs használatával:

    ```azurecli
    az account list --all -o table
    ```

    A kimenet a következőhöz hasonlít:

    ```azurecli
    Name                                  CloudName    SubscriptionId                        State    IsDefault
    ------------------------------------  -----------  ------------------------------------  -------  -----------
    Microsoft Azure                       AzureCloud   68627f8c-91fO-4905-z48q-b032a81f8vy0  Enabled  True
    ```

    A SubscriptionId értékének **másolása.**

2. Váltson át a Log Analytics-munkaterületet üzemeltető előfizetésre a következő paranccsal:

    ```azurecli
    az account set -s <subscriptionId of the workspace>
    ```

3. A következő példa az előfizetések munkaterületeinek listáját jeleníti meg az alapértelmezett JSON formátumban.

    ```
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    A kimenetben keresse meg a munkaterület nevét, majd másolja az adott Log Analytics-munkaterület teljes erőforrásazonosítóját a **mezőazonosító**alá.

4. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

    ```json
    {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceResourceId": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace Resource ID"
            }
        },
        "workspaceRegion": {
            "type": "string",
            "metadata": {
                "description": "Azure Monitor Log Analytics Workspace region"
            }
        }
    },
    "resources": [
        {
            "type": "Microsoft.Resources/deployments",
            "name": "[Concat('ContainerInsights', '-',  uniqueString(parameters('workspaceResourceId')))]",
            "apiVersion": "2017-05-10",
            "subscriptionId": "[split(parameters('workspaceResourceId'),'/')[2]]",
            "resourceGroup": "[split(parameters('workspaceResourceId'),'/')[4]]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "apiVersion": "2015-11-01-preview",
                            "type": "Microsoft.OperationsManagement/solutions",
                            "location": "[parameters('workspaceRegion')]",
                            "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                            "properties": {
                                "workspaceResourceId": "[parameters('workspaceResourceId')]"
                            },
                            "plan": {
                                "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
                                "product": "[Concat('OMSGallery/', 'ContainerInsights')]",
                                "promotionCode": "",
                                "publisher": "Microsoft"
                            }
                        }
                    ]
                },
                "parameters": {}
            }
         }
      ]
   }
    ```

5. Mentse ezt a fájlt containerSolution.json fájlként egy helyi mappába.

6. Illessze be a következő JSON-szintaxist a fájlba:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaceResourceId": {
          "value": "<workspaceResourceId>"
      },
      "workspaceRegion": {
        "value": "<workspaceRegion>"
      }
     }
    }
    ```

7. A **workspaceResourceId** értékeit a [3.](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace?view=azure-cli-latest#az-monitor-log-analytics-workspace-list) **workspaceRegion** **Region**

8. Mentse ezt a fájlt containerSolutionParams.json fájlként egy helyi mappába.

9. Készen áll a sablon üzembe helyezésére.

   * Az Azure PowerShell használatával történő üzembe helyezéshez használja a következő parancsokat a sablont tartalmazó mappában:

       ```powershell
       # configure and login to the cloud of log analytics workspace.Specify the corresponding cloud environment of your workspace to below command.
       Connect-AzureRmAccount -Environment <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       ```

       ```powershell
       # set the context of the subscription of Log Analytics workspace
       Set-AzureRmContext -SubscriptionId <subscription Id of log analytics workspace>
       ```

       ```powershell
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <resource group of log analytics workspace> -TemplateFile .\containerSolution.json -TemplateParameterFile .\containerSolutionParams.json
       ```

       A konfiguráció módosítása néhány percet is igénybe vehet. Amikor elkészült, egy üzenet jelenik meg, amely hasonló a következőhöz, és tartalmazza az eredményt:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Az Azure CLI-vel történő üzembe helyezéshez futtassa a következő parancsokat:

       ```azurecli
       az login
       az account set --name <AzureCloud | AzureChinaCloud | AzureUSGovernment>
       az login
       az account set --subscription "Subscription Name"
       # execute deployment command to add container insights solution to the specified Log Analytics workspace
       az deployment group create --resource-group <resource group of log analytics workspace> --name <deployment name> --template-file  ./containerSolution.json --parameters @./containerSolutionParams.json
       ```

       A konfiguráció módosítása néhány percet is igénybe vehet. Amikor elkészült, egy üzenet jelenik meg, amely hasonló a következőhöz, és tartalmazza az eredményt:

       ```azurecli
       provisioningState       : Succeeded
       ```

       Miután engedélyezte a figyelést, körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürt állapotmetrikákat.

## <a name="install-the-chart"></a>A diagram telepítése

>[!NOTE]
>A következő parancsok csak a Helm 2-es verziójára vonatkoznak. A --name paraméter használata nem alkalmazható a Helm 3-as verziójával.

A HELM-diagram engedélyezéséhez tegye a következőket:

1. Adja hozzá az Azure-diagramtárházat a helyi listához a következő parancs futtatásával:

    ```
    helm repo add incubator https://kubernetes-charts-incubator.storage.googleapis.com/
    ````

2. Telepítse a diagramot a következő parancs futtatásával:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<my_prod_cluster> incubator/azuremonitor-containers
    ```

    Ha a Log Analytics-munkaterület az Azure China-ban található, futtassa a következő parancsot:

    ```
    $ helm install --name myrelease-1 \
     --set omsagent.domain=opinsights.azure.cn,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

    Ha a Log Analytics-munkaterület az Azure US Government szolgáltatásban található, futtassa a következő parancsot:

    ```
    $ helm install --name myrelease-1 \
    --set omsagent.domain=opinsights.azure.us,omsagent.secret.wsid=<your_workspace_id>,omsagent.secret.key=<your_workspace_key>,omsagent.env.clusterName=<your_cluster_name> incubator/azuremonitor-containers
    ```

## <a name="configure-agent-data-collection"></a>Ügynökadatgyűjtés konfigurálása

Az 1.0.0-s verziójú diagramadatkészlet-beállítások a ConfigMap-ből vannak vezérelve. Az ügynökadatgyűjtési beállításokkal kapcsolatos [dokumentációitt](container-insights-agent-config.md).

Miután sikeresen üzembe helyezte a diagramot, áttekintheti a hibrid Kubernetes-fürt adatait az Azure Monitorban az Azure Portalon lévő tárolókhoz.  

>[!NOTE]
>A betöltési késés körülbelül öt-tíz perc az ügynök véglegesítése az Azure Log Analytics-munkaterületen. A fürt állapota a **Nincs adat** vagy **ismeretlen** értéket jeleníti meg, amíg az összes szükséges figyelési adat elérhetővé nem válik az Azure Monitorban.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha hibát észlel, miközben megpróbálja engedélyezni a hibrid Kubernetes-fürt figyelését, másolja a PowerShell [TroubleshootError_nonAzureK8s.ps1](https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/Troubleshoot/TroubleshootError_nonAzureK8s.ps1) parancsfájlját, és mentse a számítógép egy mappájába. Ez a parancsfájl segít észlelni és kijavítani a felmerült problémákat. A problémák, amelyek észlelésére és a helyesbítésre szolgálnak, a következők:

* A megadott Log Analytics-munkaterület érvényes
* A Log Analytics munkaterület az Azure Monitor for Containers megoldás konfigurálva van. Ha nem, konfigurálja a munkaterületet.
* Az OmsAgent replikakészlet-podok futnak
* Az OmsAgent démonbeállító podok futnak
* Az OmsAgent állapotszolgáltatás fut
* A Log Analytics munkaterület-azonosítója és a tárolóügynöken konfigurált kulcs egyezik azzal a munkaterülettel, amelyhez az Insight konfigurálva van.
* Érvényesítse az összes Linux-feldolgozó csomópontok `kubernetes.io/role=agent` címkével rs pod ütemezéséhez. Ha nem létezik, add hozzá.
* Érvényesítse `cAdvisor secure port:10250` vagy `unsecure port: 10255` megnyílja a fürt összes csomópontján.

Az Azure PowerShell használatával történő végrehajtáshoz használja a következő parancsokat a parancsfájlt tartalmazó mappában:

```powershell
.\TroubleshootError_nonAzureK8s.ps1 - azureLogAnalyticsWorkspaceResourceId </subscriptions/<subscriptionId>/resourceGroups/<resourcegroupName>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName> -kubeConfig <kubeConfigFile> -clusterContextInKubeconfig <clusterContext>
```

## <a name="next-steps"></a>További lépések

Ha a figyelés engedélyezve van a hibrid Kubernetes-fürt és a rajtuk futó számítási feladatok állapotának és erőforrás-kihasználásának összegyűjtéséhez, megtudhatja, [hogyan használhatja az](container-insights-analyze.md) Azure Monitort tárolókhoz.
