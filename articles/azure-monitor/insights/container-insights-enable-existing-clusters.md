---
title: Az üzembe helyezett Azure Kubernetes Service (AKS)-fürt monitorozására |} A Microsoft Docs
description: Ismerje meg, hogyan engedélyezze a monitorozást az Azure Kubernetes Service (AKS)-fürt és az Azure Monitor az tárolók már üzembe helyezte az előfizetésében.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: ae340fb11d422b7516cc315e78be974d22239503
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65074680"
---
# <a name="enable-monitoring-of-azure-kubernetes-service-aks-cluster-already-deployed"></a>Már üzembe helyezte az Azure Kubernetes Service (AKS)-fürt figyelés engedélyezése

Ez a cikk bemutatja, hogyan állítható be az Azure Monitor for containers szolgáltatásban felügyelt lévő üzemeltetett Kubernetes-fürt monitorozásához [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/) , amelyek már telepítették az előfizetésében.

Engedélyezheti a figyelését egy AKS-fürtöt, amely már üzemel, a támogatott módszerek egyikének használatával:

* Azure CLI
* Terraform
* [Az Azure Monitor](#enable-from-azure-monitor-in-the-portal) vagy [közvetlenül az AKS-fürtöt a](#enable-directly-from-aks-cluster-in-the-portal) az Azure Portalon 
* Az a [Azure Resource Manager-sablon megadott](#enable-using-an-azure-resource-manager-template) Azure PowerShell-parancsmag használatával `New-AzResourceGroupDeployment` vagy az Azure CLI használatával. 

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

## <a name="enable-using-azure-cli"></a>Engedélyezze az Azure CLI használatával

A következő lépés lehetővé teszi, hogy az AKS-fürt Azure CLI-vel figyelését. Ebben a példában, nem kell egy létrehozása, vagy adjon meg egy meglévő munkaterületet. Ez a parancs leegyszerűsíti a folyamatot, egy alapértelmezett munkaterületet hoz létre az AKS-fürt előfizetés az alapértelmezett erőforráscsoportot, ha egy még nem létezik a régióban.  Az alapértelmezett munkaterületen létrehozott hasonló formátumát *Alapértelmezettmunkaterület -\<GUID azonosítója >-\<régió >* .  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

A kimenet az alábbihoz hasonló lesz:

```azurecli
provisioningState       : Succeeded
```

Ha szeretné inkább integrálása egy meglévő munkaterületet, a következő parancs használatával adja meg a munkaterület.

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG --workspace-resource-id <ExistingWorkspaceResourceID> 
```

A kimenet az alábbihoz hasonló lesz:

```azurecli
provisioningState       : Succeeded
```

## <a name="enable-using-terraform"></a>Engedélyezze a Terraform használatával

1. Adja hozzá a **oms_agent** bővítmény profilt a már meglévő [azurerm_kubernetes_cluster erőforrás](https://www.terraform.io/docs/providers/azurerm/d/kubernetes_cluster.html#addon_profile)

   ```
   addon_profile {
    oms_agent {
      enabled                    = true
      log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
     }
   }
   ```

2. Adja hozzá a [azurerm_log_analytics_solution](https://www.terraform.io/docs/providers/azurerm/r/log_analytics_solution.html) a Terraform dokumentációjában leírt lépések végrehajtásával.

## <a name="enable-from-azure-monitor-in-the-portal"></a>Az Azure Monitor engedélyezése a portálon 

Ahhoz, hogy az AKS-fürt az Azure Portalon az Azure Monitor figyelését, tegye a következőket:

1. Az Azure Portalon válassza ki a **figyelő**. 
2. Válassza ki **tárolók** a listából.
3. Az a **figyelője – tárolók** lapon jelölje be **a nem felügyelt fürtöket**.
4. Nem figyelt fürtök a listában keresse meg a tárolót a listában, és kattintson **engedélyezése**.   
5. Az a **üzembe helyezése az Azure Monitor-tárolókhoz** lapon, ha rendelkezik egy meglévő Log Analytics munkaterület ugyanabban az előfizetésben a fürttel, válassza a legördülő listából.  
    A lista preselects az alapértelmezett munkaterületre és a helyre, amely az AKS-tároló üzembe helyezése az előfizetésben. 

    ![AKS-tárolók elemzés, monitorozás engedélyezése](./media/container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Ha azt szeretné, a fürtből a figyelési adatok tárolására szolgáló új Log Analytics-munkaterület létrehozásához kövesse a [hozzon létre egy Log Analytics-munkaterület](../../azure-monitor/learn/quick-create-workspace.md). Győződjön meg arról, a munkaterület ugyanabban az előfizetésben üzembe helyezett az AKS-tároló létrehozásához. 
 
Miután engedélyezte a figyelés, a fürt mérőszámok megtekintéséhez nagyjából 15 percet igénybe vehet. 

## <a name="enable-directly-from-aks-cluster-in-the-portal"></a>AKS-fürtöt a portálon közvetlenül a engedélyezése

Ahhoz, hogy közvetlenül egy az AKS-fürtök az Azure Portalon a figyelés, tegye a következőket:

1. Az Azure Portalon válassza a **Minden szolgáltatás** elemet. 
2. Az erőforrások listájába írja be a szöveget **tárolók**.  
    A szűrők megjelenítése a bemenetet a következők alapján. 
3. Válassza ki **Kubernetes-szolgáltatás**.  

    ![A Kubernetes-szolgáltatások hivatkozás](./media/container-insights-onboard/portal-search-containers-01.png)

4. A tárolók listájában válassza ki a tárolót.
5. A tároló áttekintés oldalán válassza **tárolók figyelése**.  
6. Az a **üzembe helyezése az Azure Monitor-tárolókhoz** lapon, ha rendelkezik egy meglévő Log Analytics munkaterület ugyanabban az előfizetésben a fürttel, válassza a legördülő listában.  
    A lista preselects az alapértelmezett munkaterületre és a helyre, amely az AKS-tároló üzembe helyezése az előfizetésben. 

    ![AKS-tároló állapotfigyelésének engedélyezése](./media/container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Ha azt szeretné, a fürtből a figyelési adatok tárolására szolgáló új Log Analytics-munkaterület létrehozásához kövesse a [hozzon létre egy Log Analytics-munkaterület](../../azure-monitor/learn/quick-create-workspace.md). Győződjön meg arról, a munkaterület ugyanabban az előfizetésben üzembe helyezett az AKS-tároló létrehozásához. 
 
Miután engedélyezte a figyelés, körülbelül 15 percet, mielőtt megtekintheti a fürt működési adatokat vehet igénybe. 

## <a name="enable-using-an-azure-resource-manager-template"></a>Engedélyezze az Azure Resource Manager-sablon használatával

Ez a módszer két JSON-sablont tartalmaz. Egy sablon határozza meg, a konfigurációt a figyelés, és a másik paraméterértékeket, hogy adja meg az alábbiakat tartalmazza:

* Az AKS tároló erőforrás-azonosítója. 
* Az erőforráscsoport, amely a fürt üzemel.

>[!NOTE]
>A sablonhoz telepíteni szeretné ugyanabban az erőforráscsoportban a fürttel.
>

A Log Analytics-munkaterülethez van létrehozni a figyelést az Azure PowerShell vagy parancssori felület használatával engedélyezése előtt. A munkaterület létrehozásához, beállíthatja azt keresztül [Azure Resource Manager](../../azure-monitor/platform/template-workspace-configuration.md)segítségével, [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), vagy a [az Azure portal](../../azure-monitor/learn/quick-create-workspace.md).

Ha ismeri a sablon segítségével üzembe helyezni erőforrásokat fogalmát, lásd:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../../azure-resource-manager/resource-group-template-deploy.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../../azure-resource-manager/resource-group-template-deploy-cli.md)

Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Az Azure CLI 2.0.59 verzióját kell futtatnia vagy újabb. A verzió azonosításához futtassa `az --version`. Ha telepíteni vagy frissíteni szeretné az Azure CLI, lásd: kell [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-a-template"></a>Hozzon létre, és hajtsa végre a sablon

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

2. Mentse a fájlt **existingClusterOnboarding.json** egy helyi mappába.
3. A következő JSON-szintaxist illessze be a fájlba:

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
       }  
     }
    }
    ```

4. Értékeinek szerkesztéséhez **aksResourceId** és **aksResourceLocation** az értékekkel a **az AKS áttekintése** oldala az AKS-fürtöt. Az érték **workspaceResourceId** a Log Analytics-munkaterületet, amely tartalmazza a következő munkaterület nevének teljes erőforrás-Azonosítójára van. 
5. Mentse a fájlt **existingClusterParam.json** egy helyi mappába.
6. Készen áll a sablon üzembe helyezésére. 

   * Üzembe helyezése az Azure PowerShell-lel, használja az alábbi parancsokat a sablont tartalmazó mappában:

       ```powershell
       New-AzResourceGroupDeployment -Name OnboardCluster -ResourceGroupName <ResourceGroupName> -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
       ```
       A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, megjelenik egy üzenet, amely a következő példához hasonló, és az eredmény tartalmazza:

       ```powershell
       provisioningState       : Succeeded
       ```

   * Az Azure CLI-vel üzembe helyezéséhez futtassa a következő parancsokat:
    
       ```azurecli
       az login
       az account set --subscription "Subscription Name"
       az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
       ```

       A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, megjelenik egy üzenet, amely a következő példához hasonló, és az eredmény tartalmazza:

       ```azurecli
       provisioningState       : Succeeded
       ```
     Miután engedélyezte a figyelés, a fürt mérőszámok megtekintéséhez nagyjából 15 percet igénybe vehet. 

## <a name="verify-agent-and-solution-deployment"></a>Az ügynök és a megoldás a telepítés ellenőrzése

Az ügynök verziója *06072018* vagy később ellenőrizheti, hogy az ügynök és a megoldás is sikeresen lettek telepítve. Az ügynök korábbi verzióival csak az ügynök telepítésének ellenőrzéséhez.

### <a name="agent-version-06072018-or-later"></a>Az ügynök 06072018 vagy újabb verziójára

A következő paranccsal ellenőrizheti, hogy az ügynök telepítése sikeresen megtörtént. 

```
kubectl get ds omsagent --namespace=kube-system
```

A kimenet a következő, amely azt jelzi, hogy megfelelően lett-e telepítve kell hasonlítania:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

A megoldás üzembe helyezése ellenőrzéséhez futtassa a következő parancsot:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A kimenet a következő, amely azt jelzi, hogy megfelelően lett-e telepítve kell hasonlítania:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>06072018-nél korábbi ügynökverzió

Ellenőrizze, hogy a Log Analytics-ügynök verziója előtt kiadott *06072018* megfelelően üzemel, a következő parancsot:  

```
kubectl get ds omsagent --namespace=kube-system
```

A kimenet a következő, amely azt jelzi, hogy megfelelően lett-e telepítve kell hasonlítania:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-configuration-with-cli"></a>Konfiguráció megtekintése a CLI-vel

Használja a `aks show` paranccsal beolvasása – részletek ilyen Ez a megoldás engedélyezve van-e, mi az a Log Analytics munkaterület erőforrás-azonosító és az összefoglaló a fürttel kapcsolatos információk.  

```azurecli
az aks show -g <resourceGroupofAKSCluster> -n <nameofAksCluster>
```

Néhány perc múlva a parancs befejeződik, és a megoldás kapcsolatos adatokat JSON formátumban adja vissza.  A parancs eredményét a monitorozási bővítményt profilt kell megjelennie, és hasonlít az alábbi példa kimenetében:

```
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

* Ha a megoldás való felvétele közben problémákat tapasztal, tekintse át a [hibaelhárítási útmutató](container-insights-troubleshoot.md)

* A figyelés engedélyezve van az AKS-fürt csomópontok és a podok mérőszámok rögzítéséhez, ezek mérőszámok az Azure Portalon érhető el. Tárolók az Azure Monitor használatával kapcsolatban lásd: [megtekintése az Azure Kubernetes Service health](container-insights-analyze.md).
