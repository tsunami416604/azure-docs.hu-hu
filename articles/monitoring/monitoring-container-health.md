---
title: Az Azure Kubernetes Service (AKS) állapotmonitorozás (előzetes verzió) |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan könnyen megtekintheti az AKS-tároló gyorsan áttekinthető az üzemeltetett Kubernetes környezetet, a kihasználtság teljesítményét.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/15/2018
ms.author: magoedte
ms.openlocfilehash: caf290477a4fd4f03bb248cc89f91027dbe68f3e
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382447"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Figyelje az Azure Kubernetes Service (AKS) tároló állapotát (előzetes verzió)

Ez a cikk ismerteti, hogyan beállítása és használata az Azure Monitor tárolójának állapotára üzembe helyezett Kubernetes-környezeteket, és az Azure Kubernetes Service (AKS) üzemeltetett számítási feladatok teljesítményének figyeléséhez. A Kubernetes-fürt és -tárolók monitorozása alapvető fontosságú, különösen akkor, ha nagy mennyiségű, több alkalmazással rendelkező egy éles fürtöt futtat.

Tároló állapotának alkalmazásteljesítmény-figyelés gyűjtését memória és a tartományvezérlők, a csomópontok és a Kubernetes, a metrikák API-n keresztül a rendelkezésre álló tárolók processzor mérőszámainak lehetőségét biztosítja. Miután engedélyezte a tároló állapotának, ezek a metrikák automatikusan egy tárolóalapú a Linuxhoz készült Log Analytics-ügynök verziója az Ön számára gyűjtött és tárolt a [Log Analytics](../log-analytics/log-analytics-overview.md) munkaterületen. A csomagban foglalt előre meghatározott nézeteket megjeleníti az adataihoz, tárolókhoz kapcsolódó számítási feladatot, és milyen hatással van a Kubernetes-fürt teljesítménybeli állapotát, így is:  

* Azonosíthatja a csomópont és a processzor és memória átlagos kihasználtság a futó tárolók. A Tudásbázis segítségével erőforrás szűk keresztmetszetek azonosítása.
* Azonosítsa, amelyben a tároló található a vezérlő vagy podot. A Tudásbázis segítségével megtekintheti a vezérlő vagy a pod általános teljesítménye. 
* Tekintse át az erőforrás-használat, a gazdagépen futó számítási feladatok, amely a szabványos folyamatokat, amelyek támogatják a pod kapcsolódnak.
* Ismerje meg a fürt átlagos és a leggyakrabban használt terhelések viselkedését. Ennek segítségével azonosíthatja a kapacitásbeli szükségleteket, és döntse el, a legnagyobb terhelést, amely képes elviselni a fürt. 

Ha érdekli, figyelése és kezelése a Docker és a Windows a tárológazdagép konfiguráció megtekintése, naplózási és erőforrás-használatot, tekintse meg a [Tárolómonitorozási megoldás](../log-analytics/log-analytics-containers.md).

## <a name="prerequisites"></a>Előfeltételek 
Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Egy új vagy meglévő AKS-fürtöt.
- A Linux-verzió tárolóalapú Log Analytics-ügynököket a microsoft / oms:ciprod04202018 vagy újabb. A verziószám képviseli egy dátumot a következő formátumban: *mmddyyyy*. Az ügynök automatikusan települ a tároló állapotának az előkészítés során. 
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

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Engedélyezze a tárolót állapotfigyelés egy új fürthöz
A telepítés során engedélyezheti a figyelését egy új AKS-fürtöt az Azure Portalon vagy az Azure CLI használatával. Kövesse a cikk rövid [Azure Kubernetes Service (AKS)-fürt üzembe helyezése](../aks/kubernetes-walkthrough-portal.md) szeretné engedélyezni a portálról, ha. Az a **figyelés** lapon a a **figyelés engedélyezése** beállításnál válassza **Igen**, majd válassza ki egy meglévő Log Analytics-munkaterületet, vagy hozzon létre egy újat. 

Egy új Azure CLI használatával létrehozott AKS-fürt figyelése engedélyezéséhez kövesse a lépés a rövid útmutató a cikkben a szakaszában [létrehozása AKS-fürt](../aks/kubernetes-walkthrough.md#create-aks-cluster).  

>[!NOTE]
>Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Az Azure CLI 2.0.43 verzióját kell futtatnia vagy újabb. A verzió azonosításához futtassa `az --version`. Ha telepíteni vagy frissíteni szeretné az Azure CLI, lásd: kell [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli). 
>

Miután engedélyezte a figyelés, és minden konfigurációs feladat sikeresen befejeződött, a teljesítmény, a fürt két módon figyelheti:

* Az AKS-fürtöt kiválasztásával közvetlenül a **egészségügyi** a bal oldali panelen.
* Válassza a **tároló állapotának monitorozása** az AKS-fürt lapján a kijelölt fürt csempéjén. Az Azure Monitor, a bal oldali ablaktáblán válassza **egészségügyi**. 

  ![Lehetőség kiválasztásakor a tároló állapotának az aks-ben](./media/monitoring-container-health/container-performance-and-health-select-01.png)

Miután engedélyezte a figyelés, körülbelül 15 percet, mielőtt megtekintheti a fürt működési adatokat vehet igénybe. 

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Engedélyezze a tárolót állapotfigyelés a meglévő felügyelt fürtöket.
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

### <a name="enable-monitoring-in-the-azure-portal"></a>Engedélyezze a monitorozást az Azure Portalon
Az AKS-tároló az Azure Portal figyelés engedélyezése, tegye a következőket:

1. Az Azure Portalon válassza ki a **minden szolgáltatás**. 
2. Az erőforrások listájába írja be a szöveget **tárolók**.  
    A szűrők megjelenítése a bemenetet a következők alapján. 
3. Válassza ki **Kubernetes-szolgáltatás**.  

    ![A Kubernetes-szolgáltatások hivatkozás](./media/monitoring-container-health/azure-portal-01.png)

4. A tárolók listájában válassza ki a tárolót.
5. A tároló áttekintés oldalán válassza **tároló állapotának monitorozása**.  
6. Az a **bevezetési tároló állapotának és a naplók** lapon, ha rendelkezik egy meglévő Log Analytics munkaterület ugyanabban az előfizetésben a fürttel, válassza a legördülő listában.  
    A lista preselects az alapértelmezett munkaterületre és a helyre, amely az AKS-tároló üzembe helyezése az előfizetésben. 

    ![AKS-tároló állapotfigyelésének engedélyezése](./media/monitoring-container-health/container-health-enable-brownfield-02.png)

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
        "name": "[Concat('ContainerInsights', '(', split(parameters('workspaceResourceId'),'/')[8], ')')]",
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
           "value": "/subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
       },
       "aksResourceLocation": {
         "value": "East US"
       },
       "workspaceResourceId": {
         "value": "/subscriptions/<SubscriptionId>/resourceGroups/<ResourceGroup>/providers/Microsoft.OperationalInsights/workspaces/<workspaceName>"
       },
       "workspaceRegion": {
         "value": "eastus"
       }
     }
    }
    ```

4. Értékeinek szerkesztéséhez **aksResourceId** és **aksResourceLocation** értékek a segítségével a **az AKS áttekintése** oldala az AKS-fürtöt. Az érték **workspaceResourceId** a Log Analytics-munkaterületet, amely tartalmazza a következő munkaterület nevének teljes erőforrás-Azonosítójára van. Emellett adja meg a helyet, a munkaterület a **workspaceRegion**. 
5. Mentse a fájlt **existingClusterParam.json** egy helyi mappába.
6. Készen áll a sablon üzembe helyezésére. 

    * Használja a következő PowerShell-parancsokat a sablont tartalmazó mappában:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, megjelenik egy üzenet, amely a következő példához hasonló, és az eredmény tartalmazza:

        ```powershell
        provisioningState       : Succeeded
        ```

    * A következő parancs futtatása az Azure CLI használatával Linux rendszeren:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, megjelenik egy üzenet, amely a következő példához hasonló, és az eredmény tartalmazza:

        ```azurecli
        provisioningState       : Succeeded
        ```
Miután engedélyezte a figyelés, körülbelül 15 percet, mielőtt megtekintheti a fürt működési adatokat vehet igénybe. 

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

## <a name="view-performance-utilization"></a>Nézet teljesítmény kihasználtsága
Amikor megnyitja a tároló állapotának, az oldal azonnal megadja az egész fürt teljesítményének kihasználását. Az AKS-fürt adatainak megtekintése a következő szakaszokba négy szempont:

- Fürt
- Csomópontok 
- Vezérlők  
- Containers

Az a **fürt** lapon, a negyedik sor teljesítménydiagramok megjelenítése a fürt fő teljesítménymutatói. 

![Példa teljesítménydiagramok fürt lapján](./media/monitoring-container-health/container-health-cluster-perfview.png)

A teljesítmény diagramon négy teljesítmény-mérőszámait jeleníti meg:

- **Csomópont CPU-kihasználtság&nbsp;%**: egy összesített szempontjából gyűjtik a CPU-kihasználtság az egész fürt számára. Kijelölésével szűrheti az eredményeket a időtartományban **átlagos**, **Min**, **maximális**, **50**, **90**, és **95.** percentilisei-választójában jelenítse a diagram felett vagy külön-külön vagy együtt. 
- **Csomópont memóriahasználat&nbsp;%**: egy összesített szempontjából gyűjtik a memóriahasználat, az egész fürt számára. Kijelölésével szűrheti az eredményeket a időtartományban **átlagos**, **Min**, **maximális**, **50**, **90**, és **95.** percentilisei-választójában jelenítse a diagram felett vagy külön-külön vagy együtt. 
- **Csomópontok száma**: A csomópontok száma és a Kubernetes állapota. A fürtcsomópontok jelölt állapotok a *összes*, *készen*, és *nem áll készen* és szűrt külön-külön vagy együtt a választó a diagram felett a. 
- **Tevékenységnapló-pod száma**: A podok számát és a Kubernetes állapota. A podok jelöli az állapotok a *összes*, *függőben lévő*, *futó*, és *ismeretlen* és szűrt külön-külön vagy együtt a a a diagram felett választó. 

Ha úgy vált, hogy **csomópontok**, **tartományvezérlők**, és **tárolók** lap jobb oldalán található az automatikusan megjelennek a tulajdonság panelen.  Azt mutatja, beleértve való Kubernetes-objektumokat rendszerezése címkék, kijelölt elem tulajdonságainak.  Kattintson a **>>** összekapcsolása a panelen view\hide a panelen.  

![Példa Kubernetes perspektívák tulajdonságait tartalmazó ablaktáblán](./media/monitoring-container-health/perspectives-preview-pane-01.png)

Kibontja a hierarchia az objektumok, ahogy a Tulajdonságok panelen frissítések alapján a kiválasztott objektum. A panelen is megtekintheti az előre definiált naplókeresések Kubernetes-események kattintva a **nézet Kubernetes eseménynaplók** a panel tetején lévő hivatkozásra. Kubernetes-naplóadatok megtekintésével kapcsolatos további információkért lásd: [keresni a naplókban az adatelemzéshez](#search-logs-to-analyze-data).

Váltson a **csomópontok** lapra, és a sor hierarchia követi a Kubernetes hálózatiobjektum-modellje, kezdve a fürt egyik csomópontjához. Bontsa ki a csomópontot, és a csomóponton futó egy vagy több podok is megtekintheti. Ha egynél több tároló van csoportosítva podot, jelennek meg az utolsó sorban a hierarchiában. Hány nem pod kapcsolódó számítási feladatok futnak a gazdagépen, ha a gazdagép processzort vagy a rendelkezésre álló memória mennyisége is megtekintheti.

![Kubernetes-csomópontot példahierarchia a teljesítmény nézet](./media/monitoring-container-health/container-health-nodes-view.png)

Válassza ki a tartományvezérlők vagy a lap tetején lévő tárolókat, és tekintse át az állapot- és erőforrás-felhasználást azokat az objektumokat. Névtér, a szolgáltatás és a csomópont által szűréséhez használja a legördülő listák tetején. Ha ehelyett meg szeretné tekinteni a memóriahasználat, az a **metrika** legördülő listában válassza **memória RSS** vagy **memória-munkakészlet**. **Memória RSS** csak Kubernetes 1.8-as és újabb verziók esetében támogatott. Ellenkező esetben, tekintse meg az értékeket **Min&nbsp; %**  , *NaN&nbsp;%*, azaz egy nem definiált képviselő numerikus típus értéke vagy ábrázolható érték. 

![Tároló csomópontok teljesítmény nézet](./media/monitoring-container-health/container-health-node-metric-dropdown.png)

Alapértelmezés szerint a teljesítményadatokat az elmúlt hat órán alapul, de az ablak használatával módosíthatja a **időtartomány** legördülő listából válassza ki a jobb felső sarokban. Ilyenkor a lapon nem automatikus frissítés, így azt manuálisan frissítenie kell. Kiválasztásával is szűrheti az eredményeket időtartományban **átlagos**, **Min**, **maximális**, **50**, **90**, és **95** PERCENTILIS-választójában jelenítse. 

![Az adatok szűrésének. percentilis kiválasztása](./media/monitoring-container-health/container-health-metric-percentile-filter.png)

A következő példában, vegye figyelembe a node *aks-nodepool-3977305*, értéke **tárolók** érték 5, amely egy összesítő üzembe helyezett tárolókat teljes száma.

![A tárolók példája egy összesítő](./media/monitoring-container-health/container-health-nodes-containerstotal.png)

Azt is segítenek gyorsan azonosítani, hogy van-e a tárolók a fürtben lévő csomópontok közötti megfelelő egyensúly. 

A csomópontok megtekintésekor megjelenő információkat az alábbi táblázatban olvasható:

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A gazdagép neve. |
| status | A csomópont állapota Kubernetes nézete. |
| Átlagos&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;% | A csomópont átlagos százalékos PERCENTILIS alapján a kijelölt időszakra. |
| Avg, Min, Max, 50, 90 | Csomópontok átlagos tényleges értéket során kiválasztott töltött idő százalékos érték alapján. Az átlagos érték mérése történik egy csomópont; beállítása CPU/memória felső korlátja podok és tárolók az értéke az avg a gazdagép által jelentett. |
| Containers | A tárolók száma. |
| Hasznos üzemidő | Mivel a csomópont elindult, és újra lett indítva a idejét jelzi. |
| Vezérlők | Csak a tárolók és a podokat. Azt mutatja, hogy melyik tartományvezérlő van a hozzá tartozó. Nem minden podok egy vezérlőt, vannak, ezért néhány megjelenítheti **N/A**. | 
| Átlagos trend&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;% | Oszlopdiagram trend PERCENTILIS mérőszám százalékos aránya a vezérlő bemutatására. |


Válassza ki a választó **tartományvezérlők**.

![Kijelölés vezérlők megjelenítése](./media/monitoring-container-health/container-health-controllers-tab.png)

Itt megtekintheti a vezérlő teljesítménybeli állapotát.

![< név > tartományvezérlők teljesítmény nézet](./media/monitoring-container-health/container-health-controllers-view.png)

A sor hierarchia vezérlő kezdődik, és a vezérlő bővül. Megtekintheti az egy vagy több tárolóban. Bontsa ki a pod, és az utolsó sort jeleníti meg a tároló a pod szerint vannak csoportosítva. 

A tartományvezérlők megtekintésekor megjelenő információkat az alábbi táblázatban olvasható:

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A vezérlő neve.|
| status | Összesítő állapotát, amikor befejeződött, például a futó állapotú, a tárolók *OK*, *kilépett*, *sikertelen* *leállítva*, vagy *Szüneteltetve*. Ha a tároló fut-e, de a állapota volt, vagy nem megfelelően jelenik meg, vagy volt nem dolgozza fel az ügynök és a 30 percnél hosszabb ideig nem válaszolt, az állapot értéke *ismeretlen*. További részletek a állapot ikon az alábbi táblázatban szerepelnek.|
| Átlagos&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;% | Minden entitás, a kiválasztott metrika és PERCENTILIS hányada összesítő átlaga. |
| Avg, Min, Max, 50, 90  | Összesítő az átlagos CPU millicore vagy a memória teljesítményét a kiválasztott PERCENTILIS tárolója. Az átlagos érték podot beállított CPU/memória felső korlátja mérése történik. |
| Containers | A vezérlő vagy a pod tárolók száma összesen. |
| Újraindul | Összesítő tárolókból újraindítás száma. |
| Hasznos üzemidő | Egy tároló indítása óta idejét jelzi. |
| Csomópont | Csak a tárolók és a podokat. Melyik, a hozzá tartozó tartományvezérlő jeleníti meg. | 
| Átlagos trend&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;%| Oszlopdiagram trend jelölő PERCENTILIS mérőszám, a vezérlő. |

Az ikonok az állapot mezőben a tárolók online állapotát jelzi:
 
| Ikon | status | 
|--------|-------------|
| ![Készen áll, futó állapot ikon](./media/monitoring-container-health/container-health-ready-icon.png) | Fut (kész)|
| ![Várakozás vagy szüneteltetett állapot ikon](./media/monitoring-container-health/container-health-waiting-icon.png) | Várakozás vagy fel van függesztve|
| ![Utolsó jelentett futó állapotikon](./media/monitoring-container-health/container-health-grey-icon.png) | Utolsó jelentett fut, de 30 percnél hosszabb ideig nem válaszolt.|
| ![A sikeres állapot ikon](./media/monitoring-container-health/container-health-green-icon.png) | Sikeresen leállt vagy nem sikerült leállítani a|

Az állapotjelző ikon alapján a pod biztosít számát jeleníti meg. A legrosszabb kétállapotú jeleníti meg, és ha az egérmutatót az állapot, a tárolóban megjeleníti az összes podok egy összesítő állapotát. Ha nincs kész állapotú, az állapot értékét jeleníti meg **(0)**. 

Válassza ki a választó **tárolók**.

![Válassza ki a tárolók megtekintése](./media/monitoring-container-health/container-health-containers-tab.png)

Itt megtekintheti a tárolókat teljesítménybeli állapotát.

![< név > tartományvezérlők teljesítmény nézet](./media/monitoring-container-health/container-health-containers-view.png)

A tárolók megtekintésekor megjelenő információkat az alábbi táblázatban olvasható:

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A vezérlő neve.|
| status | A tárolók, ha van ilyen állapotát. További részletek a állapot ikon a következő táblázatban találhatók.|
| Átlagos&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;% | Az összesítő az átlagos százalékos aránya a kiválasztott metrika és PERCENTILIS minden entitáshoz. |
| Avg, Min, Max, 50, 90  | Az összesítő az átlagos CPU millicore vagy a memória teljesítményét a kiválasztott PERCENTILIS tárolója. Az átlagos érték podot beállított CPU/memória felső korlátja mérése történik. |
| Pod | A pod tartalmazó tároló.| 
| Csomópont |  Csomópont, amelyben a tároló található. | 
| Újraindul | Egy tároló indítása óta idejét jelzi. |
| Hasznos üzemidő | Mivel egy tároló volt elindítva vagy újraindítva idejét jelzi. |
| Átlagos trend&nbsp;%, Min&nbsp;% Max&nbsp;%, az 50&nbsp;%, 90&nbsp;% | Oszlopdiagram a tendencia, hogy a tároló átlagos metrika százalékát jelenti. |

Az állapot mezőben az ikonok jelzi a podok, online válik, az alábbi táblázatban leírtak szerint:
 
| Ikon | status | 
|--------|-------------|
| ![Készen áll, futó állapot ikon](./media/monitoring-container-health/container-health-ready-icon.png) | Fut (kész)|
| ![Várakozás vagy szüneteltetett állapot ikon](./media/monitoring-container-health/container-health-waiting-icon.png) | Várakozás vagy fel van függesztve|
| ![Utolsó jelentett futó állapotikon](./media/monitoring-container-health/container-health-grey-icon.png) | Utolsó jelentett fut, de a 30 percnél hosszabb ideig nem válaszolt|
| ![Elbocsátott állapotikon](./media/monitoring-container-health/container-health-terminated-icon.png) | Sikeresen leállt vagy nem sikerült leállítani a|
| ![Sikertelen állapotikon](./media/monitoring-container-health/container-health-failed-icon.png) | Hibás állapotban |

## <a name="container-data-collection-details"></a>Tároló-adatokat gyűjtő részletei
Tároló állapotának tároló-gazdagépek és -tárolók különböző mérőszámokban és naplófájlokban teljesítményadatokat gyűjt. Az adatgyűjtés percen át 3 percenként történik.

### <a name="container-records"></a>Tárolórekordok

Az alábbi táblázatban példák a tároló állapotának és az adattípusok, amely a napló keresési eredmények között megjelenik által gyűjtött rekordok jelennek meg:

| Adattípus | Naplókeresési adatok típusa | Mezők |
| --- | --- | --- |
| Gazdagépek és-tárolók teljesítménye | `Perf` | Számítógép, ObjectName, CounterName &#40;processzoridő, a lemez beolvassa a MB, lemezre ír MB memória kihasználtsága (MB), hálózati fogadott bájtok, hálózati küldése memória, processzor kihasználtsága (mp), hálózati&#41;, Avg, TimeGenerated, Számláló_elérési_útja, SourceSystem |
| Tároló leltár | `ContainerInventory` | TimeGenerated, a számítógép, a tároló nevét, ContainerHostname, kép, ImageTag, ContainerState, ExitCode, EnvironmentVar, a parancs, CreatedTime, StartedTime, FinishedTime, SourceSystem, cseréjekor a Tárolóazonosító, ImageID |
| Tárolórendszerkép leltárát | `ContainerImageInventory` | TimeGenerated, számítógép, kép, ImageTag, ImageSize, VirtualSize, futó, fel van függesztve, leállítja, sikertelen, SourceSystem, ImageID, TotalContainer |
| Tároló-napló | `ContainerLog` | TimeGenerated, a számítógép, a lemezkép-azonosító, a Tárolónév esetén LogEntrySource, LogEntry, SourceSystem, cseréjekor a Tárolóazonosító |
| Container service-napló | `ContainerServiceLog`  | TimeGenerated, számítógép, TimeOfCommand, kép, a parancs, SourceSystem, cseréjekor a Tárolóazonosító |
| Tárolócsomópont-készlet | `ContainerNodeInventory_CL`| TimeGenerated, számítógép, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Tárolófolyamat | `ContainerProcess_CL` | TimeGenerated, számítógép, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Kubernetes-fürt podok leltára | `KubePodInventory` | TimeGenerated, számítógép, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, szolgáltatásnév, ControllerKind, ControllerName, tároló, Cseréjekor a Tárolóazonosító, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, pod IP-címére, SourceSystem |
| Kubernetes-fürt csomópontjai részét leltára | `KubeNodeInventory` | TimeGenerated, számítógép, ClusterName, ClusterId, LastTransitionTimeReady, címkék, állapot, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes-események | `KubeEvents_CL` | TimeGenerated, számítógép, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, üzenet, SourceSystem | 
| Kubernetes-fürtben a szolgáltatások | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| A Kubernetes-fürt csomópontok részei a teljesítmény-mérőszámok | Teljesítményoptimalizált &#124; ahol ObjectName == "K8SNode" | Számítógép, ObjectName, CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes&#41;, Avg, TimeGenerated, Számláló_elérési_útja, SourceSystem | 
| A Kubernetes-fürt része tárolók teljesítménymetrikáit | Teljesítményoptimalizált &#124; ahol ObjectName == "K8SContainer" | CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes&#41;, Avg, TimeGenerated, Számláló_elérési_útja, SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>Keresési naplókat az adatelemzéshez
A log Analytics segítségével keresése trendek, diagnosztizálhatja a szűk keresztmetszeteket, előrejelzési vagy összevetését adatokat, amelyek segítségével határozza meg, hogy optimális működik-e az aktuális fürtbeállításokat. Előre definiált naplókeresések rögtön használatba, illetve adja vissza a kívánt módon szabhatja testre a biztosított. 

Kiválasztásával a munkaterület az adatok interaktív elemzés céljából is végezhet a **nézet Kubernetes eseménynaplók** vagy **tárolónaplók megtekintése** lehetőség az előnézeti ablaktáblában láthatja. A **naplóbeli keresés** ablak jobb oldalán, az Azure portal oldalán, amelyen korábban volt.

![Adatok elemzése a Log Analytics az](./media/monitoring-container-health/container-health-log-search-example.png)   

A tároló naplók kimenete, a Log Analytics továbbíthatja a rendszer az STDOUT és STDERR. Tároló health által monitorozott Azure által felügyelt Kubernetes-(AKS), mert Kube rendszer nem gyűjti jelenleg generált adatok nagy mennyisége miatt. 

### <a name="example-log-search-queries"></a>Példa naplóbeli keresési lekérdezések
Gyakran hasznos hozhatók létre olyan lekérdezések, amelyek például vagy a kettő, és módosítsa őket az igényeinek. Annak érdekében, hogy speciális lekérdezések felépítését, kísérletezhet, és az alábbi mintalekérdezések:

| Lekérdezés | Leírás | 
|-------|-------------|
| ContainerInventory<br> &#124;Számítógép, név, kép, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime projekt<br> &#124;táblázat megjelenítése | Összes egy tároló tárolóéletciklus-adat listázása| 
| KubeEvents_CL<br> &#124;ahol not(isempty(Namespace_s))<br> &#124;Rendezés a TimeGenerated desc<br> &#124;táblázat megjelenítése | Kubernetes-események|
| ContainerImageInventory<br> &#124;summarize AggregatedValue futó = count() by ImageTag, kép | Rendszerképek leltára | 
| **A speciális analitika, válassza ki a vonaldiagramok**:<br> Perf<br> &#124;ahol ObjectName == "Container" és a CounterName == "processzoridő"<br> &#124;Összegzés AvgCPUPercent avg(CounterValue) a bin (TimeGenerated, 30 millió), InstanceName = | Tároló CPU | 
| **A speciális analitika, válassza ki a vonaldiagramok**:<br> Teljesítményoptimalizált &#124; ahol ObjectName == "Container" és a CounterName == "Memória kihasználtsága MB"<br> &#124;Összegzés AvgUsedMemory avg(CounterValue) a bin (TimeGenerated, 30 millió), InstanceName = | Tároló memória |

## <a name="how-to-stop-monitoring-with-container-health"></a>Előfordulhat, hogy a tároló állapotának figyelése
Ha engedélyezése után az AKS-tárolók figyelését, úgy döntene, hogy már nem szeretne, megfigyeli azt, akkor is *kikapcsolhatja az újat* vagy a megadott Azure Resource Manager-sablonok segítségével a PowerShell-parancsmag  **Új AzureRmResourceGroupDeployment** vagy az Azure CLI használatával. Egy JSON-sablon meghatározza a konfigurációt a *kikapcsolhatja az újat*. A másik, konfigurálja úgy, hogy adja meg az AKS Azonosítót és erőforrás erőforráscsoportot, amely a fürt üzembe lesz helyezve, a paraméterértékeket tartalmaz. 

Ha még nem ismeri a sablon segítségével üzembe helyezni erőforrásokat fogalma, lásd:
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/resource-group-template-deploy.md)
* [Erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md)

Ha az Azure CLI-vel, akkor először helyi telepítése és használata a parancssori felület. Kell futnia az Azure CLI 2.0.27-es vagy újabb. A verzió azonosításához futtassa `az --version`. Ha telepíteni vagy frissíteni szeretné az Azure CLI, lásd: kell [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-template"></a>Hozzon létre, és hajtsa végre a sablon

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

    ![Tároló tulajdonságai lap](./media/monitoring-container-health/container-properties-page.png)

    Ha a **tulajdonságok** területén is átmásolja a **munkaterület erőforrás-azonosító**. Az értéket kötelező megadni, ha úgy dönt, hogy később törli a Log Analytics-munkaterületet. Ez a folyamat részeként a Log Analytics-munkaterület törlése nem történik. 

5. Mentse a fájlt **OptOutParam.json** egy helyi mappába.
6. Készen áll a sablon üzembe helyezésére. 

    * A következő PowerShell-parancsok használata a sablont tartalmazó könyvtárban:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, egy üzenet, amely tartalmazza az eredmény az alábbihoz hasonló adja vissza:

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * A futtassa a következő parancsot az Azure CLI-vel Linux rendszeren:

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Ha elkészült, egy üzenet, amely tartalmazza az eredmény az alábbihoz hasonló adja vissza:

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Ha a munkaterület létrejött, csak a fürt figyelésére is alkalmas, és már nincs rá szükség, akkor törölje kézzel. Ha nem ismeri a munkaterület törlése, lásd: [törlése az Azure Log Analytics-munkaterületet az Azure Portallal](../log-analytics/log-analytics-manage-del-workspace.md). Ne felejtse el a kapcsolatos a **munkaterület erőforrás-azonosító** azt korábban a 4. lépésben másolt, szükség van rá fog. 

## <a name="troubleshooting"></a>Hibaelhárítás
Ez a témakör az adatait a tároló állapotának kapcsolatos hibaelhárítás.

Ha a tároló állapotának lett sikeresen engedélyezve és konfigurálva, de nem tekintheti meg állapotinformációk vagy az eredmény a Log Analytics Naplókeresés végrehajtásakor, segíthet a probléma diagnosztizálása az alábbiak szerint: 

1. Ellenőrizze az ügynök állapotát a következő parancs futtatásával: 

    `kubectl get ds omsagent --namespace=kube-system`

    A kimenet a következő, amely azt jelzi, hogy megfelelően lett-e telepítve kell hasonlítania:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Ellenőrizze a megoldás az üzemállapot-ügynök verziója *06072018* vagy újabb, a következő parancs futtatásával:

    `kubectl get deployment omsagent-rs -n=kube-system`

    A kimenet a következő, amely azt jelzi, hogy megfelelően lett-e telepítve kell hasonlítania:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. Ellenőrizze, hogy fut a futó a következő parancsot a pod állapotának ellenőrzéséhez: `kubectl get pods --namespace=kube-system`

    A kimenet a következő állapottal kell hasonlítania *futó* a omsagent számára:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

4. Ellenőrizze az ügynök bejegyzéseit. A tárolóalapú ügynök telepítésekor lekérdezi az OMI a következő parancsok futtatásával egy gyors ellenőrzés fut, és az ügynök és a szolgáltató verzióját jeleníti meg. 

5. Győződjön meg arról, hogy az ügynök sikeresen lett előkészítve, futtassa a következő parancsot: `kubectl logs omsagent-484hw --namespace=kube-system`

    Az állapot a következő kell hasonlítania:

    ```
    User@aksuser:~$ kubectl logs omsagent-484hw --namespace=kube-system
    :
    :
    instance of Container_HostInventory
    {
        [Key] InstanceID=3a4407a5-d840-4c59-b2f0-8d42e07298c2
        Computer=aks-nodepool1-39773055-0
        DockerVersion=1.13.1
        OperatingSystem=Ubuntu 16.04.3 LTS
        Volume=local
        Network=bridge host macvlan null overlay
        NodeRole=Not Orchestrated
        OrchestratorType=Kubernetes
    }
    Primary Workspace: b438b4f6-912a-46d5-9cb1-b44069212abc    Status: Onboarded(OMSAgent Running)
    omi 1.4.2.2
    omsagent 1.6.0.23
    docker-cimprov 1.0.0.31
    ```

## <a name="next-steps"></a>További lépések

Részletes tároló állapotát és az alkalmazás teljesítményének információk megtekintése: [naplók keresése](../log-analytics/log-analytics-log-search.md). 
