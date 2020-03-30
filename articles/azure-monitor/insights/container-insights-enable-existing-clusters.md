---
title: Üzembe helyezett Azure Kubernetes-szolgáltatás (AKS) fürtjének figyelése | Microsoft dokumentumok
description: Megtudhatja, hogyan engedélyezheti egy Azure Kubernetes-szolgáltatás (AKS) fürt figyelését az Azure Monitorlal az előfizetésében már üzembe helyezett tárolókhoz.
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 8589ea71b5c7affadc61d5e4543f734a660ab543
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275450"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Az Azure Kubernetes-szolgáltatás (AKS) már üzembe helyezett fürtjének figyelésének engedélyezése

Ez a cikk ismerteti, hogyan állíthatja be az Azure Monitor a felügyelt Kubernetes-fürt az Azure Kubernetes szolgáltatás, amely már telepítve van az előfizetésben felügyelt [Kubernetes-fürt](https://docs.microsoft.com/azure/aks/) figyelésére.

Engedélyezheti a már telepített AKS-fürt figyelését a támogatott módszerek egyikével:

* Azure CLI
* Terraform
* [Az Azure Monitorból](#enable-from-azure-monitor-in-the-portal) vagy [közvetlenül az Azure Portalon lévő AKS-fürtből](#enable-directly-from-aks-cluster-in-the-portal)
* A [megadott Azure Resource Manager sablon](#enable-using-an-azure-resource-manager-template) használatával az `New-AzResourceGroupDeployment` Azure PowerShell-parancsmag vagy az Azure CLI használatával.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)

## <a name="enable-using-azure-cli"></a>Engedélyezés az Azure CLI használatával

A következő lépés lehetővé teszi az AKS-fürt figyelését az Azure CLI használatával. Ebben a példában nem szükséges létrehozni vagy megadni egy meglévő munkaterületet. Ez a parancs leegyszerűsíti a folyamatot azáltal, hogy létrehoz egy alapértelmezett munkaterületet az AKS-fürt-előfizetés alapértelmezett erőforráscsoportjában, ha még nem létezik ilyen a régióban.  A létrehozott alapértelmezett munkaterület a *DefaultWorkspace–\<GUID\<>-Region>* formátumhoz hasonlít.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

A kimenet a következőhöz hasonlít:

```output
provisioningState       : Succeeded
```

### <a name="integrate-with-an-existing-workspace"></a>Integráció meglévő munkaterülettel

Ha inkább egy meglévő munkaterülettel integrálni szeretné, hajtsa végre a következő lépéseket a `--workspace-resource-id` log analytics-munkaterület a paraméterhez szükséges teljes erőforrás-azonosító azonosításához, majd futtassa a parancsot a figyelési bővítmény adott munkaterülethez való engedélyezéséhez.  

1. Sorolja fel az összes olyan előfizetést, amelyhez hozzáférése van a következő parancs használatával:

    ```azurecli
    az account list --all -o table
    ```

    A kimenet a következőhöz hasonlít:

    ```output
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

    ```azurecli
    az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json
    ```

    A kimenetben keresse meg a munkaterület nevét, majd másolja az adott Log Analytics-munkaterület teljes erőforrásazonosítóját a **mezőazonosító**alá.

4. Futtassa a következő parancsot a figyelési `--workspace-resource-id` bővítmény engedélyezéséhez, és cserélje le a paraméter értékét. A karakterlánc értékének a dupla idézőjeleken belül kell lennie:

    ```azurecli
    az aks enable-addons -a monitoring -n ExistingManagedCluster -g ExistingManagedClusterRG --workspace-resource-id "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<WorkspaceName>"
    ```

    A kimenet a következőhöz hasonlít:

    ```output
    provisioningState       : Succeeded
    ```

## <a name="enable-using-terraform"></a>Engedélyezés a Terraform használatával

1. A **oms_agent** bővítményprofil hozzáadása a meglévő [azurerm_kubernetes_cluster erőforráshoz](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Adja hozzá a [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) a Terraform dokumentációban leírt lépéseket követve.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Engedélyezés az Azure Monitorból a portálon

Ha engedélyezni szeretné az AKS-fürt figyelését az Azure-portálon az Azure Monitorról, tegye a következőket:

1. Az Azure Portalon válassza a **Figyelő**lehetőséget.

2. Válassza a **tárolók** elemet a listából.

3. A **Figyelő - tárolók** lapon válassza a **Nem figyelt fürtök lehetőséget.**

4. A nem figyelt fürtök listájában keresse meg a tárolót a listában, és kattintson az **Engedélyezés gombra.**   

5. Az **Azure-figyelő tárolókhoz** való bevezetés lapon, ha egy meglévő Log Analytics-munkaterület teljében ugyanabban az előfizetésben, mint a fürt, válassza ki a legördülő listából.  
    A lista előre kiválasztja az alapértelmezett munkaterületet és helyet, amelyhez az AKS-tároló telepítve van az előfizetésben.

    ![AKS-tárolóelemzési figyelés engedélyezése](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Ha új Log Analytics-munkaterületet szeretne létrehozni a figyelési adatok fürtből való tárolásához, kövesse a [Log Analytics munkaterület létrehozása című](../../azure-monitor/learn/quick-create-workspace.md)részben található utasításokat. Ügyeljen arra, hogy a munkaterületet ugyanabban az előfizetésben hozza létre, amelyhez az AKS-tároló telepítve van.

Miután engedélyezte a figyelést, körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürt állapotmetrikákat.

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>Engedélyezés közvetlenül az AKS-fürtből a portálon

Ha közvetlenül az Azure Portalon lévő AKS-fürtök egyikéről szeretné engedélyezni a figyelést, tegye a következőket:

1. Az Azure Portalon válassza a **Minden szolgáltatás**lehetőséget.

2. Az erőforrások listájában kezdje el beírni a **Tárolók beírását.**  A lista a bevitel alapján szűr.

3. Válassza a **Kubernetes-szolgáltatások lehetőséget.**  

    ![A Kubernetes szolgáltatások hivatkozás](./media/container-insights-onboard/portal-search-containers-01.png)

4. A tárolók listájában válasszon ki egy tárolót.

5. A tároló áttekintése lapon válassza a **Tárolók figyelése**lehetőséget.  

6. Az **Azure-figyelő tárolókhoz** való bevezetés lapon, ha egy meglévő Log Analytics-munkaterület teljében ugyanabban az előfizetésben, mint a fürt, válassza ki a legördülő listában.  
    A lista előre kiválasztja az alapértelmezett munkaterületet és helyet, amelyhez az AKS-tároló telepítve van az előfizetésben.

    ![AKS-tároló állapotfigyelésének engedélyezése](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Ha új Log Analytics-munkaterületet szeretne létrehozni a figyelési adatok fürtből való tárolásához, kövesse a [Log Analytics munkaterület létrehozása című](../../azure-monitor/learn/quick-create-workspace.md)részben található utasításokat. Ügyeljen arra, hogy a munkaterületet ugyanabban az előfizetésben hozza létre, amelyhez az AKS-tároló telepítve van.

Miután engedélyezte a figyelést, körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürt működési adatait.

## <a name="enable-using-an-azure-resource-manager-template"></a>Engedélyezés Azure Resource Manager-sablon használatával

Ez a módszer két JSON-sablont tartalmaz. Az egyik sablon a figyelés engedélyezéséhez a konfigurációt adja meg, a másik pedig a következők megadására konfigurált paraméterértékeket tartalmazza:

* Az AKS-tároló erőforrásazonosítója.
* Az az erőforráscsoport, amelyben a fürt telepítve van.

>[!NOTE]
>A sablont ugyanabban az erőforráscsoportban kell telepíteni, mint a fürtöt.
>

A Log Analytics-munkaterületet létre kell hozni, mielőtt engedélyezne figyelést az Azure PowerShell vagy a CLI használatával. A munkaterület létrehozásához beállíthatja azt az [Azure Resource Manageren](../../azure-monitor/platform/template-workspace-configuration.md)keresztül a [PowerShellen](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)vagy az [Azure Portalon](../../azure-monitor/learn/quick-create-workspace.md)keresztül.

Ha nem ismeri az erőforrások sablon használatával történő üzembe helyezésének fogalmát, olvassa el az:

* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/templates/deploy-powershell.md)

* [Erőforrások üzembe helyezése erőforrás-kezelői sablonokkal és az Azure CLI-vel](../../azure-resource-manager/templates/deploy-cli.md)

Ha úgy dönt, hogy az Azure CLI, először telepítenie kell, és a CLI helyileg kell használni. Az Azure CLI 2.0.59-es vagy újabb verzióját kell futtatnia. A verzió azonosításához `az --version`futtassa a futtassa a futtassa a futtassa a futtassa Ha telepítenie vagy frissítenie kell az Azure CLI-t, [olvassa el az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)című témakört.

### <a name="create-and-execute-a-template"></a>Sablon létrehozása és végrehajtása

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
        },
        "workspaceResourceId": {
          "type": "string",
          "metadata": {
            "description": "Azure Monitor Log Analytics Resource ID"
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
                "enabled": true,
                "config": {
                  "logAnalyticsWorkspaceResourceID": "[parameters('workspaceResourceId')]"
                }
              }
            }
          }
        }
      ]
    }
    ```

2. Mentse ezt a fájlt **meglévőClusterOnboarding.json fájlként** egy helyi mappába.

3. Illessze be a következő JSON-szintaxist a fájlba:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "aksResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
        },
        "aksResourceLocation": {
          "value": "<aksClusterLocation>"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
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

4. Az **aksResourceId** és az **aksResourceLocation értékeit** az AKS-fürt **AKS áttekintése** lapján található értékek használatával szerkeszti. A **workspaceResourceId** értéke a Log Analytics-munkaterület teljes erőforrásazonosítója, amely tartalmazza a munkaterület nevét.

    Az **aksResourceTagValues értékeinek** szerkesztése az AKS-fürthöz megadott meglévő címkeértékeknek megfelelően.

5. Mentse ezt a fájlt **létezőClusterParam.json néven** egy helyi mappába.

6. Készen áll a sablon üzembe helyezésére.

   * Az Azure PowerShell használatával történő üzembe helyezéshez használja a következő parancsokat a sablont tartalmazó mappában:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```

       A konfiguráció módosítása néhány percet is igénybe vehet. Amikor elkészült, egy üzenet jelenik meg, amely hasonló a következőhöz, és tartalmazza az eredményt:

       ```output
       provisioningState       : Succeeded
       ```

   * Az Azure CLI-vel történő üzembe helyezéshez futtassa a következő parancsokat:

       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       A konfiguráció módosítása néhány percet is igénybe vehet. Amikor elkészült, egy üzenet jelenik meg, amely hasonló a következőhöz, és tartalmazza az eredményt:

       ```output
       provisioningState       : Succeeded
       ```

       Miután engedélyezte a figyelést, körülbelül 15 percet is igénybe vehet, mielőtt megtekintheti a fürt állapotmetrikákat.

## <a name="verify-agent-and-solution-deployment"></a>Ügynök- és megoldástelepítés ellenőrzése

A *06072018-as* vagy újabb ügynökverzióval ellenőrizheti, hogy az ügynök és a megoldás is sikeresen üzembe lett-e. Az ügynök korábbi verzióival csak az ügynök üzembe helyezését ellenőrizheti.

### <a name="agent-version-06072018-or-later"></a>Ügynök 06072018-as vagy újabb verziója

Futtassa a következő parancsot az ügynök sikeres telepítésének ellenőrzéséhez.

```
kubectl get ds omsagent --namespace=kube-system
```

A kimenetnek a következőhöz kell hasonlítania, ami azt jelzi, hogy megfelelően lett telepítve:

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

A megoldás telepítésének ellenőrzéséhez futtassa a következő parancsot:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A kimenetnek a következőhöz kell hasonlítania, ami azt jelzi, hogy megfelelően lett telepítve:

```output
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>A 06072018-nál korábbi ügynökverzió

A *06072018* előtt kiadott Log Analytics-ügynök-verzió megfelelő telepítésének ellenőrzéséhez futtassa a következő parancsot:  

```
kubectl get ds omsagent --namespace=kube-system
```

A kimenetnek a következőhöz kell hasonlítania, ami azt jelzi, hogy megfelelően lett telepítve:  

```output
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Konfiguráció megtekintése CLI-vel

A `aks show` parancs segítségével olyan részleteket kaphat, mint például a megoldás engedélyezve van-e vagy sem, mi a Log Analytics munkaterület-erőforrásazonosítója, és összegző részleteket a fürtről.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Néhány perc múlva a parancs befejezi és visszaadja a JSON-formátumú információkat a megoldásról.  A parancs eredményeinek meg kell mutatniuk a figyelési bővítmény profilt, és a következő példakimenetre hasonlítanak:

```output
"addonProfiles": {
    "omsagent": {
      "config": {
        "logAnalyticsWorkspaceResourceID": "/subscriptions/<WorkspaceSubscription>/resourceGroups/<DefaultWorkspaceRG>/providers/Microsoft.OperationalInsights/workspaces/<defaultWorkspaceName>"
      },
      "enabled": true
    }
  }
```

## <a name="next-steps"></a>További lépések

* Ha a megoldás bedeszkázása közben problémákat tapasztal, tekintse át a [hibaelhárítási útmutatót](container-insights-troubleshoot.md)

* Ha a figyelés engedélyezve van az AKS-fürt és a rajtuk futó számítási feladatok állapotának és erőforrás-kihasználtságának összegyűjtéséhez, ismerje meg, [hogyan használhatja az](container-insights-analyze.md) Azure Monitort tárolókhoz.
