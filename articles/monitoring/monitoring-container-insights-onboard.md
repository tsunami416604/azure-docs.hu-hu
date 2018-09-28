---
title: Hogyan előkészítése az Azure Monitor-tárolókhoz |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan fogunk előkészíteni és az Azure Monitor konfigurálása tárolókhoz, így az képes megérteni, hogyan működik-e a tároló és a teljesítménnyel kapcsolatos problémákat azonosítottuk.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: magoedte
ms.openlocfilehash: df145ebe6276c911ef3064e3f8ff7a23a2faa870
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423034"
---
# <a name="how-to-onboard-azure-monitor-for-containers"></a>Hogyan előkészítése az Azure Monitor for containers szolgáltatásban
Ez a cikk bemutatja, hogyan állítható be az Azure Monitor for containers szolgáltatásban üzembe helyezett Kubernetes-környezetben és lévő üzemeltetett számítási feladatok teljesítményének figyeléséhez [Azure Kubernetes Service](https://docs.microsoft.com/azure/aks/).

## <a name="prerequisites"></a>Előfeltételek 
Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Egy új vagy meglévő AKS-fürtöt.
- A Linux-verzió tárolóalapú Log Analytics-ügynököket a microsoft / oms:ciprod04202018 vagy újabb. A verziószám képviseli egy dátumot a következő formátumban: *mmddyyyy*. Az ügynök automatikusan telepítve van a szolgáltatás az előkészítés során. 
- Egy Log Analytics-munkaterület. Létrehozhat, engedélyezze a monitorozást az új AKS-fürt, vagy lehetővé teszik az előkészítési folyamatot, hozzon létre egy alapértelmezett munkaterületet az AKS-fürt előfizetés alapértelmezett az erőforráscsoportban. Ha úgy döntött, hogy saját maga létrehozni, azt a létrehozhat [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)segítségével, [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), vagy a [az Azure portal](../log-analytics/log-analytics-quick-create-workspace.md).
- A Log Analytics közreműködő szerepkört, a figyelési szint. A Log Analytics-munkaterülethez való hozzáférésének kapcsolatos további információkért lásd: [munkaterületeinek kezeléséhez](../log-analytics/log-analytics-manage-access.md).

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Összetevők 

A teljesítmény monitorozását teszi lehetővé a teljesítmény- és esemény-adatokat gyűjti össze a fürt összes csomópontja Linux rendszeren tárolóalapú Log Analytics-ügynököket támaszkodik. Az ügynök automatikus telepítése és regisztrálása a megadott Log Analytics-munkaterület-, miután engedélyezte a tárolók figyelését. 

>[!NOTE] 
>Ha már telepített egy AKS-fürtöt, akkor engedélyeznie figyelése Azure CLI vagy a megadott Azure Resource Manager-sablon használatával, ahogyan az a cikk későbbi részében is. Nem használhat `kubectl` frissítése, törlése, telepítse újra vagy telepítheti az ügynököt. 
>

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 

## <a name="enable-monitoring-for-a-new-cluster"></a>Engedélyezze a monitorozást az új fürt
A telepítés során engedélyezheti a figyelését egy új AKS-fürtöt az Azure Portalon vagy az Azure CLI használatával. Kövesse a cikk rövid [Azure Kubernetes Service (AKS)-fürt üzembe helyezése](../aks/kubernetes-walkthrough-portal.md) szeretné engedélyezni a portálról, ha. Az a **figyelés** lapon a a **figyelés engedélyezése** beállításnál válassza **Igen**, majd válassza ki egy meglévő Log Analytics-munkaterületet, vagy hozzon létre egy újat. 

Egy új Azure CLI használatával létrehozott AKS-fürt figyelése engedélyezéséhez kövesse a lépés a rövid útmutató a cikkben a szakaszában [létrehozása AKS-fürt](../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Az Azure CLI 2.0.43 verzióját kell futtatnia vagy újabb. A verzió azonosításához futtassa `az --version`. Ha telepíteni vagy frissíteni szeretné az Azure CLI, lásd: kell [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

Miután engedélyezte a figyelés, és minden konfigurációs feladat sikeresen befejeződött, a teljesítmény, a fürt két módon figyelheti:

* Az AKS-fürtöt kiválasztásával közvetlenül a **egészségügyi** a bal oldali panelen.
* Válassza a **figyelő Container insights** az AKS-fürt lapján a kijelölt fürt csempéjén. Az Azure Monitor, a bal oldali ablaktáblán válassza **egészségügyi**. 

  ![Válassza ki az Azure Monitor az aks-ben tárolók lehetőségei](./media/monitoring-container-insights-onboard/kubernetes-select-monitoring-01.png)

Miután engedélyezte a figyelés, a fürt mérőszámok megtekintéséhez nagyjából 15 percet igénybe vehet. 

## <a name="enable-monitoring-for-existing-managed-clusters"></a>A meglévő felügyelt fürtöket figyelés engedélyezése
Az AKS-fürt vagy az Azure CLI használatával a portálról, vagy a megadott Azure Resource Manager-sablonnal a PowerShell-parancsmag használatával már telepített figyelése `New-AzureRmResourceGroupDeployment`. 

### <a name="enable-monitoring-using-azure-cli"></a>Engedélyezze a monitorozást az Azure CLI használatával
A következő lépés lehetővé teszi, hogy az AKS-fürt Azure CLI-vel figyelését. Ebben a példában, nem kell egy létrehozása, vagy adjon meg egy meglévő munkaterületet. Ez a parancs leegyszerűsíti a folyamatot, egy alapértelmezett munkaterületet hoz létre az AKS-fürt előfizetés az alapértelmezett erőforráscsoportot, ha egy még nem létezik a régióban.  Az alapértelmezett munkaterületen létrehozott hasonló formátumát *Alapértelmezettmunkaterület -<GUID>-<Region>*.  

```azurecli
az aks enable-addons -a monitoring -n MyExistingManagedCluster -g MyExistingManagedClusterRG  
```

A kimenet az alábbihoz hasonló lesz:

```azurecli
provisioningState       : Succeeded
```

### <a name="enable-monitoring-from-azure-monitor"></a>Az Azure Monitor figyelés engedélyezése
Ahhoz, hogy az AKS-fürt az Azure Portalon az Azure Monitor figyelését, tegye a következőket:

1. Az Azure Portalon válassza ki a **figyelő**. 
2. Válassza ki **tárolók (előzetes verzió)** a listából.
3. Az a **figyelője – tárolók (előzetes verzió)** lapon jelölje be **a nem felügyelt fürtöket**.
4. Nem figyelt fürtök a listában keresse meg a tárolót a listában, és kattintson **engedélyezése**.   
5. Az a **tároló állapotának és a naplók bevezetési** lapon, ha rendelkezik egy meglévő Log Analytics munkaterület ugyanabban az előfizetésben a fürttel, válassza a legördülő listából.  
    A lista preselects az alapértelmezett munkaterületre és a helyre, amely az AKS-tároló üzembe helyezése az előfizetésben. 

    ![AKS-tárolók elemzés, monitorozás engedélyezése](./media/monitoring-container-insights-onboard/kubernetes-onboard-brownfield-01.png)

    >[!NOTE]
    >Ha azt szeretné, a fürtből a figyelési adatok tárolására szolgáló új Log Analytics-munkaterület létrehozásához kövesse a [hozzon létre egy Log Analytics-munkaterület](../log-analytics/log-analytics-quick-create-workspace.md). Győződjön meg arról, a munkaterület ugyanabban az előfizetésben üzembe helyezett az AKS-tároló létrehozásához. 
 
Miután engedélyezte a figyelés, a fürt mérőszámok megtekintéséhez nagyjából 15 percet igénybe vehet. 

### <a name="enable-monitoring-from-aks-cluster-in-the-portal"></a>Engedélyezze a monitorozást az AKS-fürtöt a portálon
Az AKS-tároló az Azure Portal figyelés engedélyezése, tegye a következőket:

1. Az Azure Portalon válassza ki a **minden szolgáltatás**. 
2. Az erőforrások listájába írja be a szöveget **tárolók**.  
    A szűrők megjelenítése a bemenetet a következők alapján. 
3. Válassza ki **Kubernetes-szolgáltatás**.  

    ![A Kubernetes-szolgáltatások hivatkozás](./media/monitoring-container-insights-onboard/portal-search-containers-01.png)

4. A tárolók listájában válassza ki a tárolót.
5. A tároló áttekintés oldalán válassza **tároló állapotának monitorozása**.  
6. Az a **bevezetési tároló állapotának és a naplók** lapon, ha rendelkezik egy meglévő Log Analytics munkaterület ugyanabban az előfizetésben a fürttel, válassza a legördülő listában.  
    A lista preselects az alapértelmezett munkaterületre és a helyre, amely az AKS-tároló üzembe helyezése az előfizetésben. 

    ![AKS-tároló állapotfigyelésének engedélyezése](./media/monitoring-container-insights-onboard/kubernetes-onboard-brownfield-02.png)

    >[!NOTE]
    >Ha azt szeretné, a fürtből a figyelési adatok tárolására szolgáló új Log Analytics-munkaterület létrehozásához kövesse a [hozzon létre egy Log Analytics-munkaterület](../log-analytics/log-analytics-quick-create-workspace.md). Győződjön meg arról, a munkaterület ugyanabban az előfizetésben üzembe helyezett az AKS-tároló létrehozásához. 
 
Miután engedélyezte a figyelés, körülbelül 15 percet, mielőtt megtekintheti a fürt működési adatokat vehet igénybe. 

### <a name="enable-monitoring-by-using-an-azure-resource-manager-template"></a>Engedélyezze a monitorozást az Azure Resource Manager-sablon használatával
Ez a módszer két JSON-sablont tartalmaz. Egy sablon határozza meg, a konfigurációt a figyelés, és a másik paraméterértékeket, hogy adja meg az alábbiakat tartalmazza:

* Az AKS tároló erőforrás-azonosítója. 
* Az erőforráscsoport, amely a fürt üzemel.
* A Log Analytics-munkaterületet és régióban hozhat létre a munkaterületén. 

A Log Analytics-munkaterületen van, manuálisan kell létrehozni. A munkaterület létrehozásához, beállíthatja azt keresztül [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md)segítségével, [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), vagy a [az Azure portal](../log-analytics/log-analytics-quick-create-workspace.md).

Ha ismeri a sablon segítségével üzembe helyezni erőforrásokat fogalmát, lásd:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/resource-group-template-deploy.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)

Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Kell futnia az Azure CLI 2.0.27-es vagy újabb. A verzió azonosításához futtassa `az --version`. Ha telepíteni vagy frissíteni szeretné az Azure CLI, lásd: kell [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-a-template"></a>Hozzon létre, és hajtsa végre a sablon

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
    },
    "workspaceRegion": {
    "type": "string",
    "metadata": {
       "description": "Azure Monitor Log Analytics workspace region"
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
     },
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

2. Mentse a fájlt **existingClusterOnboarding.json** egy helyi mappába.
3. A következő JSON-szintaxist illessze be a fájlba:

    ```json
    {
       "$schema": "https://schema.management.azure.com/  schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscriptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "<aksClusterLocation>"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "<workspaceLocation>"
       }
     }
    }
    ```

4. Értékeinek szerkesztéséhez **aksResourceId** és **aksResourceLocation** értékek a segítségével a **az AKS áttekintése** oldala az AKS-fürtöt. Az érték **workspaceResourceId** a Log Analytics-munkaterületet, amely tartalmazza a következő munkaterület nevének teljes erőforrás-Azonosítójára van. Emellett adja meg a helyet, a munkaterület a **workspaceRegion**. 
5. Mentse a fájlt **existingClusterParam.json** egy helyi mappába.
6. Készen áll a sablon üzembe helyezésére. 

    * Használja a következő PowerShell-parancsokat a sablont tartalmazó mappában:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ClusterResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, megjelenik egy üzenet, amely a következő példához hasonló, és az eredmény tartalmazza:

        ```powershell
        provisioningState       : Succeeded
        ```

    * A következő parancs futtatása az Azure CLI-vel:
    
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
az aks show -g <resoourceGroupofAKSCluster> -n <nameofAksCluster>
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

* Ha a megoldás való felvétele közben problémákat tapasztal, tekintse át a [hibaelhárítási útmutató](monitoring-container-insights-troubleshoot.md)

* A figyelés engedélyezve van az AKS-fürt csomópontok és a podok mérőszámok rögzítéséhez, ezek mérőszámok az Azure Portalon érhető el. Tárolók az Azure Monitor használatával kapcsolatban lásd: [megtekintése az Azure Kubernetes Service health](monitoring-container-insights-analyze.md).