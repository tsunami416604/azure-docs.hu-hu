---
title: Az Azure Kubernetes Service (AKS) állapotmonitorozás (előzetes verzió) |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan könnyen megtekintheti az AKS-tároló gyorsan áttekinthető az üzemeltetett Kubernetes környezetet, a kihasználtság teljesítményét.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/16/2018
ms.author: magoedte
ms.openlocfilehash: 1fd5ac0f9994a4dbf4365c21ac4f31ba0eccbb15
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39069151"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Figyelje az Azure Kubernetes Service (AKS) tároló állapotát (előzetes verzió)

Ez a cikk ismerteti, hogyan beállítása és használata az Azure Monitor tárolójának állapotára figyelése az Azure Kubernetes Service (AKS) az üzemeltetett Kubernetes-környezetekből üzembe helyezett munkaterhelések teljesítményét.  A Kubernetes-fürt és -tárolók monitorozása kritikus fontosságú, különösen, ha egy éles fürtöt futtat skálázható módon, több alkalmazással.

Tároló állapotának alkalmazásteljesítmény-figyelés gyűjtését memória és a tartományvezérlők, a csomópontok és a tárolók Kubernetes, a metrikák API-n keresztül a rendelkezésre álló processzor mérőszámainak lehetőségét biztosítja.  Miután engedélyezte a tároló állapotának, ezek a metrikák automatikusan gyűjti a Linuxhoz készült OMS-ügynök tárolóalapú verzióját használja, és tárolja a [Log Analytics](../log-analytics/log-analytics-overview.md) munkaterületen.  Az előre meghatározott nézetek megjelenítése az adataihoz, tárolókhoz kapcsolódó számítási feladatok és a Kubernetes-fürt teljesítménybeli állapotát mi van hatással, így az képes megérteni:  

* Milyen tárolók futnak, a csomópont, illetve azok átlagos processzor és memória kihasználtságáról, az erőforrás szűk keresztmetszetek azonosítása
* Ahol a tárolóban található egy tartományvezérlő és/vagy egy tartományvezérlő vagy a pod általános teljesítménye megtekintéséhez podok azonosítása 
* Tekintse át az erőforrás-használat, a standard szintű folyamatok a pod támogató egymástól független, a gazdagépen futó számítási feladatok
* A fürt segítségével azonosíthatja a kapacitásbeli szükségleteket, és döntse el, a legnagyobb terhelést is kiálló tárolókkal átlagos és a leggyakrabban használt terhelés alatt a viselkedés értelmezése 

Ha érdekli, figyelése és kezelése a Docker és a Windows a tárológazdagép konfiguráció megtekintése, naplózási és erőforrás-használatot, tekintse meg a [Tárolómonitorozási megoldás](../log-analytics/log-analytics-containers.md).

## <a name="requirements"></a>Követelmények 
Mielőtt hozzákezdene, tekintse át a következő adatokat, így megismerheti a támogatott előfeltételek.

- Egy új vagy meglévő AKS-fürt
- Tárolóalapú OMS-ügynök Linux-verzió esetében a microsoft / oms:ciprod04202018 és újabb verziók. A verziószám képviseli egy dátumot a következő formátumban – *mmddyyyy*.  Telepíti a rendszer automatikusan a tároló állapotának bevezetés során.  
- Egy Log Analytics-munkaterület.  Amikor engedélyezi az új AKS-fürt figyelése, vagy létrehozhat egyet keresztül hozhatók [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), vagy a [az Azure portal](../log-analytics/log-analytics-quick-create-workspace.md).
- A Log Analytics közreműködő szerepkört, és engedélyezze a tárolómonitorozási tagja.  A Log Analytics-munkaterülethez való hozzáférésének kapcsolatos további információkért lásd: [munkaterületeinek kezeléséhez](../log-analytics/log-analytics-manage-access.md).

## <a name="components"></a>Összetevők 

Ez a funkció a tárolóalapú OMS-ügynök Linux rendszeren, a teljesítmény- és esemény-adatok gyűjtésére a fürt összes csomópontja támaszkodik.  Az ügynök automatikus telepítése és regisztrálása a megadott Log Analytics-munkaterület-, miután engedélyezte a tárolók figyelését. 

>[!NOTE] 
>Ha már telepített egy AKS-fürtöt, akkor engedélyezhető egy megadott Azure Resource Manager-sablon használatával, ahogyan az a cikk későbbi részében is. Nem használhat `kubectl` frissítése, törlése, telepítse újra vagy telepítheti az ügynököt.  
>

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure Portalra
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Engedélyezze a tárolót állapotfigyelés egy új fürthöz
Engedélyezheti egy új AKS-fürt figyelése az Azure Portalról üzembe helyezés során.  Kövesse a cikk rövid [Azure Kubernetes Service (AKS)-fürt üzembe helyezése](../aks/kubernetes-walkthrough-portal.md).  Ha befejezte a a **figyelés** lapon jelölje be **Igen** beállítás **figyelés engedélyezése** engedélyezi, majd válasszon egy meglévő, vagy hozzon létre egy új Log Analytics-munkaterületet.  

Figyelés engedélyezése után minden konfigurációs feladat sikeresen befejeződött, a teljesítmény, a fürt a két módszer egyikével figyelheti:

1. Az AKS-fürtöt kiválasztásával közvetlenül a **egészségügyi** a bal oldali ablaktáblán.<br><br> 
2. Kattintson a a **tároló állapotának monitorozása** az AKS-fürt lapján a kijelölt fürt csempéjén.  Válassza ki az Azure monitorban **egészségügyi** a bal oldali ablaktáblán.  

![Válassza ki a tároló állapotának az aks-ben beállításai](./media/monitoring-container-health/container-performance-and-health-select-01.png)

Figyelés engedélyezését követően eltarthat körülbelül 15 perc elteltével megtekintheti a fürt működési adatokat.  

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Engedélyezze a tárolót állapotfigyelés a meglévő felügyelt fürtöket.
A már üzembe helyezte az Azure Portalról vagy a PowerShell-parancsmag segítségével megadott Azure Resource Manager-sablonnal egy AKS-fürt figyelése **New-AzureRmResourceGroupDeployment** vagy Azure CLI használatával.  


### <a name="enable-from-azure-portal"></a>Az Azure Portalról engedélyezése
A következő lépésekkel engedélyezze a monitorozást az AKS-tároló az Azure Portalról.

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **tárolók**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza ki **Kubernetes-szolgáltatás**.<br><br> ![Azure Portal](./media/monitoring-container-health/azure-portal-01.png)<br><br>  
2. A tárolók listájában válassza ki a tárolót.
3. A tároló áttekintés oldalán válassza **tároló állapotának monitorozása** és a **tároló állapotának és a naplók bevezetési** lap jelenik meg.
4. Az a **tároló állapotának és a naplók bevezetési** lapon, ha rendelkezik egy meglévő Log Analytics munkaterület ugyanabban az előfizetésben a fürttel, válassza a legördülő listából.  A lista preselects az alapértelmezett munkaterületre, és az AKS-tároló helye telepítve van az előfizetésben.<br><br> ![AKS-tároló állapotfigyelésének engedélyezése](./media/monitoring-container-health/container-health-enable-brownfield-02.png) 

>[!NOTE]
>Ha szeretne létrehozni egy új Log Analytics-munkaterület a fürtből a monitorozási adatok tárolásához, kövesse a [Cretae Log Analytics-munkaterület](../log-analytics/log-analytics-quick-create-workspace.md) , és ügyeljen arra, hogy ugyanahhoz az előfizetéshez tartozik, amely az AKS-tároló a munkaterület létrehozása üzembe helyezését.  
>
 
Figyelés engedélyezését követően eltarthat körülbelül 15 perc elteltével megtekintheti a fürt működési adatokat. 

### <a name="enable-using-azure-resource-manager-template"></a>Engedélyezze az Azure Resource Manager-sablon használatával
Ezt a módszert tartalmaz két JSON-sablonok, egy sablon határozza meg, a konfigurációt a figyelés engedélyezése és az egyéb JSON-sablon, konfigurálja úgy, hogy adja meg a következő paraméterértékeket tartalmaz:

* AKS-tárolók erőforrás-azonosító 
* A fürt erőforráscsoport van üzembe helyezve 
* Log Analytics-munkaterületet és régióban hozhat létre a munkaterületet 

A Log Analytics-munkaterületen van, manuálisan kell létrehozni.  A munkaterület létrehozásához, beállíthat egy keresztül [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), az a [az Azure portal](../log-analytics/log-analytics-quick-create-workspace.md).

Ha nem ismeri a sablon használatával a PowerShell erőforrások üzembe helyezésének fogalmait, tekintse meg a [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/resource-group-template-deploy.md)vagy az Azure CLI-vel lásd, [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).

Ha úgy döntött, hogy az Azure parancssori felület használata, először telepítése és használata a parancssori felület helyileg.  Szükség rá, hogy futnak-e az Azure CLI 2.0.27-es vagy újabb. Futtatás `az --version` a verziójának azonosításához. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-template"></a>Hozzon létre, és hajtsa végre a sablon

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
3. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

    ```json
    {
       "$schema": "https://schema.management.azure.com/  schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
         "aksResourceId": {
           "value": "/subscriptions/<SubscroptiopnId>/resourcegroups/<ResourceGroup>/providers/Microsoft.ContainerService/managedClusters/<ResourceName>"
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

4. Az érték szerkesztése **aksResourceId**, **aksResourceLocation** azokra az értékekre, amelyeket a a **az AKS áttekintése** oldala az AKS-fürtöt.  Az érték **workspaceResourceId** a Log Analytics-munkaterületet, amely tartalmazza a következő munkaterület nevének teljes erőforrás-Azonosítójára van.  Emellett adja meg a helyet, a munkaterület megtalálható a **workspaceRegion**.    
5. Mentse a fájlt **existingClusterParam.json** egy helyi mappába.
6. Készen áll a sablon üzembe helyezésére. 

    * Használja a következő PowerShell-parancsokat a sablont tartalmazó könyvtárban:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Amikor befejeződik, megjelenik egy üzenet, amely tartalmazza az eredmény az alábbihoz hasonló:

        ```powershell
        provisioningState       : Succeeded
        ```

    * A futtassa a következő parancsot az Azure CLI-vel Linux rendszeren:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        A konfiguráció módosításának befejezése néhány percet is igénybe vehet. Amikor befejeződik, megjelenik egy üzenet, amely tartalmazza az eredmény az alábbihoz hasonló:

        ```azurecli
        provisioningState       : Succeeded
        ```
Figyelés engedélyezését követően eltarthat körülbelül 15 perc elteltével megtekintheti a fürt működési adatokat.  

## <a name="verify-agent-and-solution-deployment"></a>Az ügynök és a megoldás a telepítés ellenőrzése
Az ügynök verziója *06072018* és újabb verziója szükséges, ellenőrizheti, hogy az ügynök és a megoldás is sikeresen lettek telepítve.  Az ügynök korábbi verzióival ügynök telepítésének ellenőrzése csak is.

### <a name="agent-version-06072018-and-higher"></a>Ügynök verziója 06072018 és magasabb
A következő parancs futtatásával ellenőrizze az ügynök telepítése sikeresen megtörtént.   

```
kubectl get ds omsagent --namespace=kube-system
```

A kimenet a következő jelző megfelelően fejeződött üzembe kell hasonlítania:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

A megoldás üzembe helyezése ellenőrzéséhez futtassa a következő parancsot:

```
kubectl get deployment omsagent-rs -n=kube-system
```

A kimenet a következő jelző megfelelően fejeződött üzembe kell hasonlítania:

```
User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
omsagent   1         1         1            1            3h
```

### <a name="agent-version-earlier-than-06072018"></a>06072018-nél korábbi ügynökverzió

Ellenőrizze az OMS-ügynök előtt kiadott verzióját *06072018* megfelelően üzemel, a következő parancsot:  

```
kubectl get ds omsagent --namespace=kube-system
```

A kimenet a következő jelző megfelelően fejeződött üzembe kell hasonlítania:  

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>Nézet teljesítmény kihasználtsága
Amikor megnyitja a tároló állapotának, az oldal azonnal megadja az egész fürt teljesítményének kihasználását.  Az AKS-fürt adatainak megtekintése a következő szakaszokba négy szempont:

- Fürt
- Csomópontok 
- Vezérlők  
- Containers

A fürt lapon a sor teljesítménydiagramok megjelenítése a fürt fő teljesítménymutatói.  

![Példa teljesítménydiagramok fürt lapján](./media/monitoring-container-health/container-health-cluster-perfview.png)

A következő információkat a bemutatott teljesítmény-mérőszámait:

- Processzor kihasználtsága (%) - csomópont a diagram jelöli egy összesített szempontjából gyűjtik a CPU-kihasználtság az egész fürt számára.  Kijelölésével szűrheti az eredményeket a időtartományban *átlagos*, *Min*, *maximális*, *50*, *90*, és *95* a percentilisei választó a diagram felett, vagy külön-külön vagy kombinált. 
- Csomópont memória kihasználtsági % - a diagram jelöli egy összesített szempontjából gyűjtik a memóriahasználat, az egész fürt számára.  Kijelölésével szűrheti az eredményeket a időtartományban *átlagos*, *Min*, *maximális*, *50*, *90*, és *95* a percentilisei választó a diagram felett, vagy külön-külön vagy kombinált. 
- Csomópontok száma – Ez a diagram jelöli a csomópontok száma és az állapot a Kubernetes.  A fürtcsomópontok jelölt állapot *összes*, *készen*, és *nem áll készen* és szűrt külön-külön vagy összegzéséből származik a választó a diagram felett.    
- Tevékenységnapló-pod száma – ezen a diagramon jelöli a podok száma és az állapot a Kubernetes.  A podok jelölt állapotát is *összes*, *függőben lévő*, *futó*, és *ismeretlen* és szűrt külön-külön vagy összegzéséből származik a a diagram felett választó.  

A csomópontok lapra vált, a sor hierarchia követi a Kubernetes hálózatiobjektum-modellt a fürtben egy csomópont kezdve.  Bontsa ki a csomópontot, és láthatja, hogy a csomóponton futó egy vagy több podok, ha egynél több tároló podot szerint vannak csoportosítva, mint a hierarchiában lévő utolsó sora láthatók. Ön is láthatja, hány nem pod kapcsolódó számítási feladatok futnak a gazdagépen, abban az esetben a processzor vagy a rendelkezésre álló memória mennyisége a gazdagép rendelkezik.

![Kubernetes-csomópontot példahierarchia a teljesítmény nézet](./media/monitoring-container-health/container-health-nodes-view.png)

Válassza ki a lap tetején tartományvezérlők vagy a tárolókat, és tekintse át az állapot- és erőforrás-felhasználást azokat az objektumokat.  Névtér, a szolgáltatás és a csomópont által szűréséhez használja a képernyő tetején a legördülő mezőkben. Ha ehelyett meg szeretné tekinteni a memóriahasználat, az a **metrika** legördülő listában válassza ki **memória RSS** vagy **memória-munkakészlet**.  **Memória RSS** csak a Kubernetes 1.8-as és újabb verziók esetében támogatott. Jelenik meg az értékeket **minimális %** azt jelzi, hogy *NaN %*, amely egy nem definiált vagy ábrázolható értéket jelölő numerikus adatokat típusú érték. 

![Tároló csomópontok teljesítmény nézet](./media/monitoring-container-health/container-health-node-metric-dropdown.png)

Alapértelmezés szerint a teljesítményadatokat az elmúlt hat órán alapul, de az ablakban módosíthatja a **időtartomány** legördülő listából válassza ki a lap jobb felső sarkában található. Ilyenkor a lapon nem automatikus frissítés, így azt manuálisan frissítenie kell. Kiválasztásával is szűrheti az eredményeket időtartományban *átlagos*, *Min*, *maximális*, *50*, *90*, és *95* származó a PERCENTILIS választó. 

![Az adatok szűrésének. percentilis kiválasztása](./media/monitoring-container-health/container-health-metric-percentile-filter.png)

A következő példában láthatja, hogy a csomópont *aks-nodepool-3977305*, értéke **tárolók** érték 5, amely egy összegző üzembe helyezett tárolókat teljes száma.

![A tárolók száma példája összegzése](./media/monitoring-container-health/container-health-nodes-containerstotal.png)

Ez segíthet gyorsan azonosíthatja, hogy nem kell a tárolókat a fürt csomópontjai közötti megfelelő egyensúly.  

A következő táblázat ismerteti a csomópontok megtekintésekor megjelenő információkat.

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A gazdagép neve |
| status | A csomópont állapota Kubernetes nézete |
| ÁTLAGOS %, A MIN %, A MAXIMÁLIS SZÁMA: %, A 50 %, A 90 % | A csomópont átlagos százalékos során kiválasztott töltött idő százalékos érték alapján. |
| AVG, MIN, MAX, 50, 90 | Csomópontok átlagos tényleges értéket során kiválasztott töltött idő százalékos érték alapján.  Az átlagos érték mérése történik egy csomópont; beállítása CPU/memória felső korlátja podok és tárolók az értéke az avg a gazdagép által jelentett. |
| Containers | A tárolók száma. |
| Hasznos üzemidő | Mivel a csomópont elindult, és újra lett indítva a idejét jelzi. |
| Vezérlők | Csak a tárolók és a podokat. Melyik, a hozzá tartozó tartományvezérlő jeleníti meg. Nem minden podok szerepelni fog egy vezérlőt, ezért néhány valószínűleg nincs. | 
| Átlagos %, a MIN %, a maximális száma: %, a 50 %, a 90 % trend | Oszlopdiagram trend PERCENTILIS mérőszám %-a vezérlő bemutatására. |


Válassza ki a választó **tartományvezérlők**.

![Kijelölés vezérlők megjelenítése](./media/monitoring-container-health/container-health-controllers-tab.png)

Itt láthatja a tartományvezérlők teljesítménybeli állapotát.

![< név > tartományvezérlők teljesítmény nézet](./media/monitoring-container-health/container-health-controllers-view.png)

A sor hierarchia vezérlő kezdődik, és a vezérlő bővül, és a egy, vagy egy vagy több tárolók láthatja.  Bontsa ki a pod, és az utolsó sor megjelenítése a tárolót a pod szerint vannak csoportosítva.  

A következő táblázat ismerteti a tartományvezérlők megtekintésekor megjelenő információkat.

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A vezérlő neve|
| status | A tárolók, hogy végzett futó állapotú, mint például állapotát összesítő *OK*, *kilépett*, *sikertelen* *leállítva*, vagy  *Felfüggesztve*. Ha a tároló fut-e, de a állapota volt, vagy nem megfelelően jelenik meg vagy volt nem dolgozza fel az ügynök és a 30 percnél hosszabb ideig nem válaszolt, az állapot értéke *ismeretlen*. További részletek a állapot ikon az alábbi táblázatban szerepelnek.|
| ÁTLAGOS %, A MIN %, A MAXIMÁLIS SZÁMA: %, A 50 %, A 90 % | Minden entitás, a kiválasztott metrika és PERCENTILIS átlagos % átlagos összesítő. |
| AVG, MIN, MAX, 50, 90  | Az átlagos CPU millicore vagy a memória teljesítményét a kiválasztott PERCENTILIS tárolója összesítő.  Az átlagos érték podot beállított CPU/memória felső korlátja mérése történik. |
| Containers | A vezérlő vagy a pod tárolók száma összesen. |
| Újraindul | Összesítő újraindítás száma, a tárolók. |
| Hasznos üzemidő | Egy tároló indítása óta idejét jelzi. |
| Csomópont | Csak a tárolók és a podokat. Melyik, a hozzá tartozó tartományvezérlő jeleníti meg. | 
| Átlagos %, a MIN %, a maximális száma: %, a 50 %, a 90 % trend| Oszlopdiagram trend jelölő PERCENTILIS mérőszám, a vezérlő. |

Az ikonok az állapot mezőben a tárolók online állapotát jelzi:
 
| Ikon | status | 
|--------|-------------|
| ![Készen áll, futó állapot ikon](./media/monitoring-container-health/container-health-ready-icon.png) | Fut (kész)|
| ![Várakozás vagy szüneteltetett állapot ikon](./media/monitoring-container-health/container-health-waiting-icon.png) | Várakozás vagy fel van függesztve|
| ![Utolsó jelentett futó állapotikon](./media/monitoring-container-health/container-health-grey-icon.png) | Utolsó jelentett fut, de 30 percnél hosszabb ideig nem válaszolt.|
| ![Elbocsátott állapotikon](./media/monitoring-container-health/container-health-green-icon.png) | Sikeresen leállt vagy nem sikerült leállítani a|

Az állapotjelző ikon alapján a pod biztosít számát mutatja. Azt mutatja, az rosszabb kétállapotú, és ha az egérmutatót az állapot, minden podok állapota egy összesítése megjelenít a tárolóban.  Ha nincs kész állapotú, az állapot érték jelenik-e meg a **(0)**.  

Válassza ki a választó **tárolók**.

![Válassza ki a tárolók megtekintése](./media/monitoring-container-health/container-health-containers-tab.png)

Itt látható a tárolókat teljesítménybeli állapotát.

![< név > tartományvezérlők teljesítmény nézet](./media/monitoring-container-health/container-health-containers-view.png)

A következő táblázat ismerteti a tárolók megtekintésekor megjelenő információkat.

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A vezérlő neve|
| status | A tárolók, ha van ilyen állapotát. További részletek a állapot ikon az alábbi táblázatban szerepelnek.|
| ÁTLAGOS %, A MIN %, A MAXIMÁLIS SZÁMA: %, A 50 %, A 90 % | Minden entitás, a kiválasztott metrika és PERCENTILIS átlagos % átlagos összesítő. |
| AVG, MIN, MAX, 50, 90  | Az átlagos CPU millicore vagy a memória teljesítményét a kiválasztott PERCENTILIS tárolója összesítő.  Az átlagos érték podot beállított CPU/memória felső korlátja mérése történik. |
| Pod | A pod tartalmazó tároló.| 
| Csomópont |  Csomópont, amelyben a tároló található. | 
| Újraindul | Egy tároló indítása óta idejét jelzi. |
| Hasznos üzemidő | Mivel egy tároló volt elindítva vagy újraindítva idejét jelzi. |
| Átlagos %, a MIN %, a maximális száma: %, a 50 %, a 90 % trend | Oszlopdiagram trend átlagos metrika %-át a tárolót. |

Az ikonok az állapot mezőben a podok online állapotát jelzi:
 
| Ikon | status | 
|--------|-------------|
| ![Készen áll, futó állapot ikon](./media/monitoring-container-health/container-health-ready-icon.png) | Fut (kész)|
| ![Várakozás vagy szüneteltetett állapot ikon](./media/monitoring-container-health/container-health-waiting-icon.png) | Várakozás vagy fel van függesztve|
| ![Utolsó jelentett futó állapotikon](./media/monitoring-container-health/container-health-grey-icon.png) | Utolsó jelentett fut, de 30 percnél hosszabb ideig nem válaszolt.|
| ![Elbocsátott állapotikon](./media/monitoring-container-health/container-health-terminated-icon.png) | Sikeresen leállt vagy nem sikerült leállítani a|
| ![Sikertelen állapotikon](./media/monitoring-container-health/container-health-failed-icon.png) | Hibás állapotban |

## <a name="container-data-collection-details"></a>Tároló adatainak gyűjtése részletei
Tároló állapotának tároló-gazdagépek és -tárolók különböző mérőszámokban és naplófájlokban teljesítményadatokat gyűjt. Az adatgyűjtés percen át 3 percenként történik.

### <a name="container-records"></a>Tárolórekordok

Az alábbi táblázat a tároló állapotának és az adattípusok, amely a napló keresési eredmények között megjelenik által összegyűjtött rekordokkal példái láthatók.

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
A log Analytics segítségével keresése trendek, diagnosztizálhatja a szűk keresztmetszeteket, előrejelzési vagy összevetését adatokat, amelyek segítségével határozza meg, hogy optimális működik-e az aktuális fürtbeállításokat.  Előre definiált naplókeresések biztosított rögtön használatba vagy biztosítása érdekében az adatokat a kívánt módon szabhatja testre. 

Válassza a munkaterület az adatok interaktív elemzés céljából is végezhet a **napló megtekintése** beállítás érhető el, a jobb szélen bontsa ki a vezérlő vagy a tároló.  **Naplóbeli keresés** a lap jobb fent az oldalra, amelyen korábban volt a portálon.

![Adatok elemzése a Log Analytics az](./media/monitoring-container-health/container-health-view-logs.png)   

A tároló naplóinak kimenete a Log Analyticsnek továbbítják az STDOUT és STDERR. Tároló health által monitorozott Azure felügyelt Kubernetes (AKS), mert Kube rendszer nem gyűjti ma létrehozott adatok nagy mennyisége miatt.     

### <a name="example-log-search-queries"></a>Példa naplóbeli keresési lekérdezések
Gyakran hasznos építhetők fel lekérdezések például vagy két kezdődő és annak módosításával őket az igényeinek. Az alábbi mintalekérdezések segíteni bonyolultabb lekérdezéseket is kipróbálhat.

| Lekérdezés | Leírás | 
|-------|-------------|
| ContainerInventory<br> &#124;Számítógép, név, kép, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime projekt<br> &#124;táblázat megjelenítése | Összes tároló életciklus-információinak listázása| 
| KubeEvents_CL<br> &#124;ahol not(isempty(Namespace_s))<br> &#124;Rendezés a TimeGenerated desc<br> &#124;táblázat megjelenítése | Kubernetes-események|
| ContainerImageInventory<br> &#124;summarize AggregatedValue futó = count() by ImageTag, kép | Rendszerképek leltára | 
| **A speciális analitika, válassza ki a vonaldiagramok**:<br> Teljesítményoptimalizált<br> &#124;ahol ObjectName == "Container" és a CounterName == "processzoridő"<br> &#124;Összegzés AvgCPUPercent avg(CounterValue) a bin (TimeGenerated, 30 millió), InstanceName = | Tároló CPU | 
| **A speciális analitika, válassza ki a vonaldiagramok**:<br> Teljesítményoptimalizált &#124; ahol ObjectName == "Container" és a CounterName == "Memória kihasználtsága MB"<br> &#124;Összegzés AvgUsedMemory avg(CounterValue) a bin (TimeGenerated, 30 millió), InstanceName = | Tároló memória |

## <a name="how-to-stop-monitoring-with-container-health"></a>Előfordulhat, hogy a tároló állapotának figyelése
Az AKS-tároló monitoringja engedélyezése után úgy döntene, hogy már nem szeretné, hogy megfigyeli azt, akkor is *kikapcsolhatja az újat* a megadott Azure Resource Manager-sablonok használata a PowerShell-parancsmag  **Új AzureRmResourceGroupDeployment** vagy az Azure CLI használatával.  Egy JSON-sablon meghatározza a konfigurációt a *kikapcsolhatja az újat* és az egyéb JSON-sablont, konfigurálja úgy, hogy adja meg az AKS fürterőforrás Azonosítót és erőforrás-csoport a fürt üzembe lesz helyezve, a paraméterértékeket tartalmaz.  Ha nem ismeri a sablon használatával a PowerShell erőforrások üzembe helyezésének fogalmait, tekintse meg a [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure PowerShell-lel](../azure-resource-manager/resource-group-template-deploy.md) vagy az Azure CLI-vel lásd, [erőforrások üzembe helyezése Resource Manager-sablonokkal és az Azure CLI](../azure-resource-manager/resource-group-template-deploy-cli.md).

Ha úgy döntött, hogy az Azure parancssori felület használata, először telepítése és használata a parancssori felület helyileg.  Szükség rá, hogy futnak-e az Azure CLI 2.0.27-es vagy újabb. Futtatás `az --version` a verziójának azonosításához. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli). 

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
              "config": {
                "logAnalyticsWorkspaceResourceID": null
              }
            }
           }
         }
       }
      ]
    }
    ```

2. Mentse a fájlt **OptOutTemplate.json** egy helyi mappába.
3. Másolja és illessze be a következő JSON-szintaxist a létrehozott fájlba:

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

4. Az érték szerkesztése **aksResourceId** és **aksResourceLocation** az értékeket az AKS-fürtöt, amely a a **tulajdonságok** a kijelölt fürt lapján.

    ![Tároló tulajdonságai lap](./media/monitoring-container-health/container-properties-page.png)

    Ha a **tulajdonságok** területén is átmásolja a **munkaterület erőforrás-azonosító**.  Az értéket kötelező megadni, ha úgy dönt, hogy törli a Log Analytics-munkaterület később, amely nem történik meg a folyamat részeként.  

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

Ha a munkaterület létrejött, csak a fürt figyelésére is alkalmas, és már nincs rá szükség, akkor törölje kézzel. Ha nem ismeri a munkaterület törlése, lásd: [törlése az Azure Log Analytics-munkaterületet az Azure Portallal](../log-analytics/log-analytics-manage-del-workspace.md).  Ne felejtse el a kapcsolatos a **munkaterület erőforrás-azonosító** azt korábban a 4. lépésben másolt, szükség van rá fog.  

## <a name="troubleshooting"></a>Hibaelhárítás
Ez a témakör az adatait a tároló állapotának kapcsolatos hibaelhárítás.

Ha a tároló állapotának lett sikeresen engedélyezve és konfigurálva, de Ön nem érzékelhető a bármely állapotinformációkat vagy a Log Analytics-Naplókeresés végrehajtásakor eredmények, a probléma diagnosztizálása érdekében a következő lépéseket végezheti.   

1. Ellenőrizze az ügynök állapotát a következő parancs futtatásával: 

    `kubectl get ds omsagent --namespace=kube-system`

    A kimenet a következő jelző megfelelően fejeződött üzembe kell hasonlítania:

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```  
2. Ellenőrizze a megoldás az üzemállapot-ügynök verziója *06072018* vagy újabb, a következő parancs futtatásával:

    `kubectl get deployment omsagent-rs -n=kube-system`

    A kimenet a következő jelző megfelelően fejeződött üzembe kell hasonlítania:

    ```
    User@aksuser:~$ kubectl get deployment omsagent-rs -n=kube-system 
    NAME       DESIRED   CURRENT   UP-TO-DATE   AVAILABLE    AGE
    omsagent   1         1         1            1            3h
    ```

3. A állapotának ellenőrzése, hogy fut-e a pod vagy nem a következő parancs futtatásával: `kubectl get pods --namespace=kube-system`

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

4. Ellenőrizze az ügynök bejegyzéseit. A tárolóalapú ügynök telepítésekor, egy gyors ellenőrzés futtatásával OMI a következő parancsokat, és megjeleníti az ügynök verziója fut, és 
5.  szolgáltató. Ha szeretné látni, hogy az ügynök sikeresen lett előkészítve, futtassa a következő parancsot: `kubectl logs omsagent-484hw --namespace=kube-system`

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

[Naplók keresése](../log-analytics/log-analytics-log-search.md) részletes tároló állapotát és az alkalmazás teljesítményének információk megtekintéséhez.  