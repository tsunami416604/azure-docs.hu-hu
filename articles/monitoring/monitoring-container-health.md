---
title: Hogy figyelemmel kísérje Azure Kubernetes szolgáltatás (AKS) állapotát (előzetes verzió) |} Microsoft Docs
description: Ez a cikk ismerteti, hogyan könnyen áttekintheti a AKS tároló gyorsan megértéséhez a kihasználtsági üzemeltetett Kubernetes környezet teljesítményét.
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
ms.date: 06/19/2018
ms.author: magoedte
ms.openlocfilehash: 7c4294947cba72b1638e77c2dd8de1f5ee37b62a
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285990"
---
# <a name="monitor-azure-kubernetes-service-aks-container-health-preview"></a>Hogy figyelemmel kísérje Azure Kubernetes szolgáltatás (AKS) tároló állapotát (előzetes verzió)

Ez a cikk ismerteti beállítása és használata Azure figyelő tároló állapotát az Azure Kubernetes szolgáltatás (AKS) üzemeltetett Kubernetes környezetben üzembe helyezett munkaterhelések teljesítményének figyeléséhez.  A Kubernetes-fürt és -tárolók monitorozása kritikus fontosságú, különösen, ha egy éles fürtöt futtat skálázható módon, több alkalmazással.

Tároló állapotfigyelő teljesítményfigyelés gyűjtését memória és a tartományvezérlők, a csomópontok és a tárolók Kubernetes a metrikák API-n keresztül elérhető processzor metrikák lehetőséget biztosít.  Miután engedélyezte a tároló állapotát, következő metrikák tekintetében automatikusan gyűjtött Linux az OMS-ügynököt indexelése verzióját használja, és tárolja a [Naplóelemzési](../log-analytics/log-analytics-overview.md) munkaterületen.  Az előre meghatározott nézeteket tartalmazza a residing tároló munkaterhelések és mi érinti a Kubernetes fürt teljesítménybeli állapotát úgy tudja értelmezni megjelenítése:  

* Milyen tárolók futnak, a csomópont és az átlagos processzor és memória kihasználtsága erőforrás szűk keresztmetszetek azonosítása
* A tároló tartalmazó vezérlő és/vagy megtekintéséhez a vezérlő vagy pod általános teljesítménye három munkaállomás-csoporttal azonosítása 
* Tekintse át az erőforrás-használat a szabványos folyamatokat fogyasztanak támogató egymástól független a gazdagépen futó feladatok
* Átlagos és a leggyakrabban használt terhelés kapacitásigények azonosításához, és a legnagyobb terhelést is kiálló tárolókkal határozza meg a fürt működésének megértése 

Ha érdekli figyelése és kezelése a Docker és a Windows tároló állomások konfiguráció megtekintése, naplózási és erőforrás-használat, tekintse meg a [tároló figyelésére szolgáló megoldás](../log-analytics/log-analytics-containers.md).

## <a name="requirements"></a>Követelmények 
Megkezdése előtt tekintse át a következő adatokat, így megismerheti, hogy a támogatott előfeltételek.

- A fürt AKS következő verziói támogatottak: 1.7.7 való 1.9.6.
- Linux-verziójának indexelése OMS-ügynököt a microsoft / oms:ciprod04202018 és újabb verziók. Ez az ügynök telepítése automatikusan során érdekében, hogy a tároló állapotát.  
- Egy Log Analytics-munkaterület.  Amikor engedélyezhető a AKS új fürt, vagy létrehozhat egy keresztül is létrehozható [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), vagy a [Azure-portálon](../log-analytics/log-analytics-quick-create-workspace.md).
- Ahhoz, hogy a tároló a figyelés bekapcsolható a Log Analyticshez közreműködői szerepkör tagja.  A Naplóelemzési munkaterület való hozzáférés vezérlése érdekében hogyan további információkért lásd: [munkaterületek kezelése](../log-analytics/log-analytics-manage-access.md).

## <a name="components"></a>Összetevők 

Ez a funkció indexelése OMS-ügynököt a teljesítmény- és esemény-adatokat gyűjteni a fürt összes csomópontjának Linux támaszkodik.  Az ügynök automatikusan telepítése és regisztrálása a megadott Naplóelemzési munkaterület tároló figyelés engedélyezése után. 

>[!NOTE] 
>Ha már telepített egy AKS fürt, akkor engedélyezhető, ahogyan az a cikk későbbi részében, a megadott Azure Resource Manager sablon használatával. Nem használhat `kubectl` frissítése, törlése, hozza létre újra, vagy telepítse az ügynököt.  
>

## <a name="sign-in-to-azure-portal"></a>Bejelentkezés az Azure Portalra
Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen. 

## <a name="enable-container-health-monitoring-for-a-new-cluster"></a>Állapotfigyelés tárolót, ha az új fürt engedélyezése
Csak akkor tudja engedélyezni a AKS fürt figyelése az Azure portálról telepítésekor.  A gyors üzembe helyezés cikkben található lépéseket követve [fürt üzembe helyezése Azure Kubernetes szolgáltatás (AKS)](../aks/kubernetes-walkthrough-portal.md).  Amikor a rendszer a **figyelés** lapon jelölje be **Igen** beállítás **figyelés engedélyezése** engedélyezéséhez, majd válasszon egy meglévő, vagy hozzon létre egy új Naplóelemzési munkaterület.  

Figyelés engedélyezése után minden konfigurációs feladat sikeresen befejeződött, a fürt két módszer egyikével teljesítmény figyelésére:

1. Közvetlenül a fürtből AKS kiválasztásával **állapotfigyelő** a bal oldali ablaktáblán.<br><br> 
2. Kattintson a **tároló állapotának figyeléséhez** csempe a kijelölt fürt AKS fürt lapján.  Azure-figyelő, válassza ki **állapotfigyelő** a bal oldali ablaktáblán.  

![Jelölje be a tároló állapotát a AKS beállítások](./media/monitoring-container-health/container-performance-and-health-select-01.png)

Figyelés engedélyezése után körülbelül 15 perc elteltével megtekintheti a fürt működési adatokat is igénybe vehet.  

## <a name="enable-container-health-monitoring-for-existing-managed-clusters"></a>Állapotfigyelés tárolót, ha a meglévő felügyelt fürtöt engedélyezése
A már telepített AKS tároló figyelés engedélyezése valósítható meg az Azure portálról vagy a PowerShell-parancsmaggal megadott Azure Resource Manager sablonnal **New-AzureRmResourceGroupDeployment** vagy Az Azure parancssori felület.  


### <a name="enable-from-azure-portal"></a>Azure-portálon engedélyezése
A következő lépésekkel használatával engedélyezi a AKS tároló Azure-portálról.

1. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Írja be az erőforrások listájához, **tárolók**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza ki **Kubernetes szolgáltatások**.<br><br> ![Azure Portal](./media/monitoring-container-health/azure-portal-01.png)<br><br>  
2. A tárolók, jelölje ki a tároló.
3. A tároló – áttekintés lapon válassza az **tároló állapotának figyeléséhez** és a **tároló állapotát és a naplók bevezetési** lap jelenik meg.
4. Az a **tároló állapotát és a naplók bevezetési** lapon, ha egy meglévő Naplóelemzési munkaterület ugyanazt az előfizetést, mint a fürtöt, jelölje ki a legördülő listából.  A lista preselects alapértelmezett munkaterület, és a AKS tároló helyét a rendszer az előfizetést. Másik lehetőségként válasszon **hozzon létre új** , és adja meg az új munkaterületet ugyanahhoz az előfizetéshez.<br><br> ![Engedélyezze a AKS tároló állapotfigyelés](./media/monitoring-container-health/container-health-enable-brownfield.png) 

    Ha **hozzon létre új**, a **hozzon létre új munkaterületet** ablaktáblán jelenik meg. A **régió** a régió alapértelmezés szerint a tároló-erőforrás jön létre, és fogadja el az alapértelmezett, vagy válasszon ki egy másik régiót, és adja meg a munkaterület nevét.  Kattintson a **létrehozása** a kiválasztott fogadásához.<br><br> ![Adja meg a tároló monintoring munkaterülete](./media/monitoring-container-health/create-new-workspace-01.png)  

    >[!NOTE]
    >Nem hozható létre egy új munkaterület nyugati középső Régiójában a régióban jelenleg csak választhat egy már meglévő munkaterület az adott régióban.  Annak ellenére, hogy a régiót a listából, a telepítés indul el, de nem sikerül röviddel utána.  
    >
 
Figyelés engedélyezése után körülbelül 15 perc elteltével megtekintheti a fürt működési adatokat is igénybe vehet. 

### <a name="enable-using-azure-resource-manager-template"></a>Engedélyezze az Azure Resource Manager-sablonnal
Ez a módszer két JSON sablonokkal, egy sablon meghatározza a konfigurációját, és engedélyezze a megfigyelést és a más JSON-sablon tartalmaz megadta, hogy adja meg a következő paraméter értéke:

* AKS tároló erőforrás-azonosító 
* A fürt erőforráscsoportban van üzembe helyezve 
* A Naplóelemzési munkaterület és a régióban legyen, a munkaterület létrehozása 

A Naplóelemzési munkaterület rendelkezik manuálisan kell létrehozni.  A munkaterület elkészítésére, hogy be tudjon egyet állítani keresztül [Azure Resource Manager](../log-analytics/log-analytics-template-workspace-configuration.md), [PowerShell](https://docs.microsoft.com/azure/log-analytics/scripts/log-analytics-powershell-sample-create-workspace?toc=%2fpowershell%2fmodule%2ftoc.json), az a [Azure-portálon](../log-analytics/log-analytics-quick-create-workspace.md).

Ha nem ismeri a sablon használatával a PowerShell-lel erőforrásokat üzembe helyezi a fogalmait, tekintse meg a [erőforrások a Resource Manager-sablonok és Azure PowerShell telepítése](../azure-resource-manager/resource-group-template-deploy.md)vagy Azure CLI találhat [rendelkező erőforrások telepítése Resource Manager-sablonok és az Azure parancssori felület](../azure-resource-manager/resource-group-template-deploy-cli.md).

Ha úgy döntött, hogy az Azure parancssori felület használja, először telepítéséhez és használatához a CLI helyileg.  Szükség rá, hogy futnak-e az Azure parancssori felület 2.0.27 verzió vagy újabb. Futtatás `az --version` azonosítja a verzióját. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli). 

#### <a name="create-and-execute-template"></a>Hozzon létre, majd hajtsa végre a sablon

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

4. Az érték szerkesztése **aksResourceId**, **aksResourceLocation** az értékekkel, amely a a **AKS áttekintése** a AKS fürt lapján.  A következő **workspaceResourceId** a teljes erőforrás-azonosító a Naplóelemzési munkaterület, beleértve a munkaterület neve.  Továbbá adja meg a helyet, a munkaterület van a **workspaceRegion**.    
5. Mentse a fájlt **existingClusterParam.json** egy helyi mappába.
6. Készen áll a sablon üzembe helyezésére. 

    * Használja a következő PowerShell-parancsokat a sablont tartalmazó mappájában:

        ```powershell
        New-AzureRmResourceGroupDeployment -Name OnboardCluster -ResourceGroupName ClusterResourceGroupName -TemplateFile .\existingClusterOnboarding.json -TemplateParameterFile .\existingClusterParam.json
        ```
        A konfiguráció módosítását is igénybe vehet néhány percet. A Befejezés után, amely tartalmazza az eredmény az alábbihoz hasonló üzenet jelenik meg:

        ```powershell
        provisioningState       : Succeeded
        ```

    * A futtassa a következő parancs az Azure CLI Linux rendszeren:
    
        ```azurecli
        az login
        az account set --subscription "Subscription Name"
        az group deployment create --resource-group <ResourceGroupName> --template-file ./existingClusterOnboarding.json --parameters @./existingClusterParam.json
        ```

        A konfiguráció módosítását is igénybe vehet néhány percet. A Befejezés után, amely tartalmazza az eredmény az alábbihoz hasonló üzenet jelenik meg:

        ```azurecli
        provisioningState       : Succeeded
        ```
Figyelés engedélyezése után körülbelül 15 perc elteltével megtekintheti a fürt működési adatokat is igénybe vehet.  

## <a name="verify-agent-deployed-successfully"></a>Ellenőrizze a sikeresen telepített ügynök
A megfelelő telepítés OMS-ügynököt ellenőrzéséhez futtassa a következő parancsot: ` kubectl get ds omsagent --namespace=kube-system`.

A kimenet a következő jelezve volt megfelelően telepíteni kell hasonlítania:

```
User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system 
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
```  

## <a name="view-performance-utilization"></a>Nézet teljesítmény kihasználtsága
Nyissa meg a tároló állapotát, amikor az oldal azonnal megadja a teljesítmény-felhasználást a fürtcsomópontok közül.  Megtekintheti a AKS fürt adatait meg van osztva három szempontok:

- Csomópontok 
- Vezérlők  
- Containers

A sor hierarchia modelljébe Kubernetes objektum kezdődő, és a fürt egyik csomópontja.  Bontsa ki a csomópontot és egy vagy több három munkaállomás-csoporttal a csomópontokon futó látja, és ha egynél több tároló egy pod szerint vannak csoportosítva, mint a hierarchiában lévő utolsó sorának megtekinthető.<br><br> ![A teljesítmény nézetben példahierarchia Kubernetes csomópont](./media/monitoring-container-health/container-performance-and-health-view-03.png)

Válassza ki a lap tetején tartományvezérlők vagy a tárolókat, és tekintse át azokat az objektumokat a állapot- és erőforrás-felhasználást.  A képernyő tetején a legördülő listák segítségével szűrés névtér, a szolgáltatás és a csomópont alapján. Ha ehelyett meg szeretné tekinteni memóriafelhasználás a a **metrika** legördülő listában válassza ki **memória RSS** vagy **memória munkakészlet**.  **Memória RSS** csak Kubernetes 1,8 és újabb verziók esetében támogatott. Ellenkező esetben tekintse meg az értékeket **AVG %** azt jelzi, *NaN %*, amely egy nem definiált vagy unrepresentable értékét képviselő numerikus típus értéke. 

![Tároló teljesítmény csomópontok teljesítmény nézet](./media/monitoring-container-health/container-performance-and-health-view-04.png)

Alapértelmezés szerint a teljesítményadatokat az elmúlt hat órán át alapul, de módosíthatja az ablakot a **időtartomány** legördülő listából válassza ki a lap jobb felső sarkában található. Ilyenkor a lapon nem automatikus frissítése, így manuálisan frissítenie kell. 

A következő példában láthatja a csomópont *aks-agentpool-3402399-0*, értéke **tárolók** érték 10., amely egy összegző telepített tárolók teljes száma.<br><br> ![Egy csomópont példa tároló összegzése](./media/monitoring-container-health/container-performance-and-health-view-07.png)<br><br> Gyors azonosítását, ha még nem rendelkezik a megfelelő tárolók a fürtben található csomópontok közötti egyensúly segíthet.  

A következő táblázat ismerteti a csomópontok megtekintésekor megjelenő adatok.

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A gazdagép neve |
| status | A csomópont állapota Kubernetes ábrázolása |
| ÁTLAGOS % | Átlagos csomópont százaléka alapján kijelölt mértéket a kijelölt időtartamot. |
| ÁTLAG | Átlagos csomópontokon tényleges érték alapján kijelölt mértéket a kijelölt időtartamot.  A átlagos a program méri a Processzor/memória korlát beállítása egy csomópont; három munkaállomás-csoporttal és a tárolók a gazdagép által jelentett átlagos értéket esetén. |
| Containers | A tárolók száma. |
| Hasznos üzemidő | Azt az időszakot jelöli, mivel egy csomópont elindítva, vagy indult újra. |
| Pod | Csak az tárolókat. Azt mutatja, amely pods, amelynek helye. |
| Vezérlők | Csak a tárolók és három munkaállomás-csoporttal. Azt illusztrálja, mely azt lakóhelye vezérlő. Nem minden három munkaállomás-csoporttal lesz a vezérlő így néhány lehetséges, hogy megjelenítése alkalmazható. | 
| Trend AVG % | A tároló és a csomópont átlagos metrika % alapján oszlopdiagramot trend. |


A választó, válassza a **tartományvezérlők**.<br><br> ![Jelölje be vezérlők megtekintése](./media/monitoring-container-health/container-performance-and-health-view-08.png)

Itt láthatja a tartományvezérlőket teljesítménybeli állapotát.<br><br> ![< név > tartományvezérlői teljesítmény nézet](./media/monitoring-container-health/container-performance-and-health-view-05.png)

A sor hierarchia kezdődik-e a tartományvezérlő, és kibontja a tartományvezérlő, és egy vagy több három munkaállomás-csoporttal, vagy egy vagy több tárolóban.  Bontsa ki a pod, és az utolsó sor megjelenítése a tároló fogyasztanak szerint vannak csoportosítva.  

A következő táblázat ismerteti a tartományvezérlők megtekintésekor megjelenő adatok.

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A vezérlő neve|
| status | A tárolók, amikor befejeződött, például a futó állapotú, állapotának *kilépett*, *sikertelen* *leállítva*, vagy *felfüggesztve*. Ha a tárolóban fut, de a állapota volt, vagy nem megfelelően jelenik meg vagy az ügynök által nem kivételezett és 30 percnél hosszabb ideig nem válaszolt, az állapota lesz *ismeretlen*. |
| ÁTLAGOS % | Minden entitás, a kiválasztott metrika az átlagos % átlagát összesítő. |
| ÁTLAG | A tároló átlagos CPU millicore vagy memória teljesítményének összesítése.  Átlagos értéke a Processzor/memória korlát beállítása egy pod mérése történik. |
| Containers | A vezérlő vagy pod tárolói teljes száma. |
| Újraindul | Összesítő az újraindítás száma tárolókból. |
| Hasznos üzemidő | Azt az időszakot jelöli, a tároló elindítása óta. |
| Pod | Csak az tárolókat. Azt mutatja, amely pods, amelynek helye. |
| Csomópont | Csak a tárolók és három munkaállomás-csoporttal. Azt illusztrálja, mely azt lakóhelye vezérlő. | 
| Trend AVG % | A tároló átlagos metrika % bemutató oszlopdiagramot trend. |

A választó, válassza a **tárolók**.<br><br> ![Jelölje be tárolók megtekintése](./media/monitoring-container-health/container-performance-and-health-view-09.png)

Itt láthatja a tárolók teljesítménybeli állapotát.<br><br> ![< név > tartományvezérlői teljesítmény nézet](./media/monitoring-container-health/container-performance-and-health-view-06.png)

A következő táblázat ismerteti a tárolók megtekintésekor megjelenő adatok.

| Oszlop | Leírás | 
|--------|-------------|
| Name (Név) | A vezérlő neve|
| status | A tárolók állapotát összesítő, ha van ilyen. |
| ÁTLAGOS % | Minden entitás, a kiválasztott metrika az átlagos % átlagát összesítő. |
| ÁTLAG | A tároló átlagos CPU millicore vagy memória teljesítményének összesítése. Átlagos értéke a Processzor/memória korlát beállítása egy pod mérése történik. |
| Containers | A vezérlő tárolók teljes száma.|
| Újraindul | Azt az időszakot jelöli, a tároló elindítása óta. |
| Hasznos üzemidő | Azt az időszakot jelöli, mivel egy tároló lett elindítva, vagy újraindítása után. |
| Pod | Pod információk helyét. |
| Csomópont |  Csomópont, ahol a tárolóban található.  | 
| Trend AVG % | A tároló átlagos metrika % bemutató oszlopdiagramot trend. |

## <a name="container-data-collection-details"></a>Tároló az gyűjtemény adatait
Tároló rendszerállapot különböző metrikák és a napló teljesítményadatokat gyűjt tároló állomások és a tárolók. Három percenként összegyűjtött adatok.

### <a name="container-records"></a>Tárolórekordok

Az alábbi táblázat példákat a tároló állapotát és az adattípusok, amelyek szerepelnek a találatok között napló által gyűjtött rekordok.

| Adattípus | A naplófájl-keresési adattípust tartalmaz | Mezők |
| --- | --- | --- |
| A gazdagépek és a tárolók teljesítmény | `Perf` | Számítógép, ObjectName, CounterName &#40;kihasználtsága (%), a lemez beolvassa MB, szabad MB memória kihasználtsága (MB), írja hálózati fogadott bájtok, hálózati küldését bájt, a processzor kihasználtsága mp, hálózati&#41;, ellenértéknek, TimeGenerated, Számláló_elérési_útja, SourceSystem |
| Tároló leltár | `ContainerInventory` | TimeGenerated, a számítógép, a tároló neve, ContainerHostname, kép, ImageTag, ContainerState, ExitCode, EnvironmentVar, a parancs, CreatedTime, StartedTime, FinishedTime, SourceSystem, Tárolóazonosító, ImageID |
| Tároló kép készlet | `ContainerImageInventory` | TimeGenerated, számítógép, kép, ImageTag, ImageSize, VirtualSize, futó, szünetel, leállítását követően nem sikerült, SourceSystem, ImageID, TotalContainer |
| Tároló-napló | `ContainerLog` | TimeGenerated, a számítógép, a lemezkép-Azonosítót, a tároló neve, LogEntrySource, LogEntry, SourceSystem, Tárolóazonosító |
| Tároló szolgáltatás bejelentkezési | `ContainerServiceLog`  | TimeGenerated, számítógép, TimeOfCommand, kép, a parancs, SourceSystem, Tárolóazonosító |
| Tároló csomópont készlet | `ContainerNodeInventory_CL`| TimeGenerated, számítógép, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Tároló folyamat | `ContainerProcess_CL` | TimeGenerated, számítógép, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Azon Kubernetes fürtben három munkaállomás-csoporttal | `KubePodInventory` | TimeGenerated, számítógép, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus, Tárolóazonosító, ContainerName, név, PodLabel, Namespace, PodStatus, fürtnév, PodIp, SourceSystem |
| Azon csomópontok Kubernetes fürthöz tartozik | `KubeNodeInventory` | TimeGenerated, számítógép, fürtnév, ClusterId, LastTransitionTimeReady, címkéket, az állapot, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes események | `KubeEvents_CL` | TimeGenerated, számítógép, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, üzenet, SourceSystem | 
| A Kubernetes fürt szolgáltatások | `KubeServices_CL` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| A csomópontok részei a Kubernetes fürt metrikák | A Teljesítményfigyelő &#124; ahol ObjectName == "K8SNode" | Számítógép, ObjectName, CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, networkRxBytes, networkTxBytes, restartTimeEpoch, networkRxBytesPerSec, networkTxBytesPerSec, cpuAllocatableNanoCores, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes&#41;, ellenértéknek, TimeGenerated, Számláló_elérési_útja, SourceSystem | 
| A Kubernetes fürt része tárolók tartozó teljesítménymutatók | A Teljesítményfigyelő &#124; ahol ObjectName == "K8SContainer" | CounterName &#40;cpuUsageNanoCores, memoryWorkingSetBytes, memoryRssBytes, restartTimeEpoch, cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryLimitBytes&#41;, ellenértéknek, TimeGenerated, Számláló_elérési_útja, SourceSystem | 

## <a name="search-logs-to-analyze-data"></a>Keresési naplókat adatok elemzéséhez
A Naplóelemzési trendek keres, a szűk keresztmetszetek diagnosztizálása, a előrejelzési vagy összefüggésbe adatokat, melyek segíthetnek megállapítani, hogy az aktuális fürtbeállításokat teljesítménye optimális.  Előre definiált napló keresések biztosított elindítására azonnal, vagy testre ahhoz, hogy hogyan kívánja az adatokat ad vissza. 

Végezheti el az adatok interaktív elemzések elvégzéséhez a munkaterületen kiválasztásával a **napló megtekintése** beállítás érhető el a jobb szélen amikor kibővít egy tárolót.  **Naplófájl-keresési** lap jelenik meg a lap a portálon korábban a felett.<br><br> ![A Naplóelemzési adatok elemzése](./media/monitoring-container-health/container-performance-and-health-view-logs-01.png)   

A tároló naplók kimeneti Naplóelemzési továbbított STDOUT és az STDERR. Tároló health által figyelt Azure felügyelt Kubernetes (AKS), mert Kube-rendszer nem gyűjtenek ma létrehozott adatok nagy száma miatt.     

### <a name="example-log-search-queries"></a>Példa napló keresési lekérdezések
Gyakran érdemes hozhatók létre olyan lekérdezések például vagy két és módosításával őket, hogy illeszkedjen az igényekhez. Az alábbi mintalekérdezések segíteni bonyolultabb lekérdezéseket is kísérletezhet.

| Lekérdezés | Leírás | 
|-------|-------------|
| ContainerInventory<br> &#124;Számítógép neve, kép, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime projekt<br> &#124;tábla leképezése | Az összes tároló-életciklus információk felsorolása| 
| KubeEvents_CL<br> &#124;Ha not(isempty(Namespace_s))<br> &#124;Rendezze a TimeGenerated desc<br> &#124;tábla leképezése | Kubernetes események|
| ContainerImageInventory<br> &#124;AggregatedValue összefoglalója kép, ImageTag, count() = fut. | Lemezkép-készlet | 
| **A speciális elemzés, válassza ki a vonaldiagramok**:<br> A Teljesítményfigyelő<br> &#124;Ha ObjectName == "Container" és a CounterName == "kihasználtsága (%)"<br> &#124;AvgCPUPercent összefoglalója által bin (TimeGenerated, 30 p), példánynév avg(CounterValue) = | Tároló Processzor | 
| **A speciális elemzés, válassza ki a vonaldiagramok**:<br> A Teljesítményfigyelő &#124; ahol ObjectName == "Container" és a CounterName == "Memória kihasználtsága (MB)"<br> &#124;AvgUsedMemory összefoglalója által bin (TimeGenerated, 30 p), példánynév avg(CounterValue) = | Tároló memória |

## <a name="how-to-stop-monitoring-with-container-health"></a>Tároló állapotát a Figyelés leállítása
Miután engedélyezte a AKS tároló megfigyelését döntse el, hogy már nem megfigyeli azt szeretné, akkor *nem vesznek részt* a megadott Azure Resource Manager-sablonok használatával a PowerShell-parancsmaggal  **Új AzureRmResourceGroupDeployment** vagy Azure parancssori felület.  Egy JSON-sablon meghatározza a konfiguráció *nem vesznek részt* és az egyéb JSON-sablon megadta, hogy adja meg a AKS fürt erőforrás Azonosítóját és az erőforrás a fürtön üzemel paraméter értékeket tartalmaz.  Ha nem ismeri a sablon használatával a PowerShell-lel erőforrásokat üzembe helyezi a fogalmait, tekintse meg a [erőforrások a Resource Manager-sablonok és Azure PowerShell telepítése](../azure-resource-manager/resource-group-template-deploy.md) vagy Azure CLI találhat [rendelkező erőforrások telepítése Resource Manager-sablonok és az Azure parancssori felület](../azure-resource-manager/resource-group-template-deploy-cli.md).

Ha úgy döntött, hogy az Azure parancssori felület használja, először telepítéséhez és használatához a CLI helyileg.  Szükség rá, hogy futnak-e az Azure parancssori felület 2.0.27 verzió vagy újabb. Futtatás `az --version` azonosítja a verzióját. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli). 

### <a name="create-and-execute-template"></a>Hozzon létre, majd hajtsa végre a sablon

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

4. Az érték szerkesztése **aksResourceId** és **aksResourceLocation** a értékekkel a AKS fürt, amely a a **tulajdonságok** lapon a kijelölt fürt.<br><br> ![Tároló Tulajdonságok lap](./media/monitoring-container-health/container-properties-page.png)<br>

    Ha a **tulajdonságok** lapján is másolja a **munkaterület erőforrás-azonosító**.  Ez az érték szükség, ha Ön úgy dönt, hogy szeretné a Naplóelemzési munkaterület törlése később, amely nem történik meg a folyamat részeként.  

5. Mentse a fájlt **OptOutParam.json** egy helyi mappába.
6. Készen áll a sablon üzembe helyezésére. 

    * A következő PowerShell-parancsok használata a sablont tartalmazó mappából:

        ```powershell
        Connect-AzureRmAccount
        Select-AzureRmSubscription -SubscriptionName <yourSubscriptionName>
        New-AzureRmResourceGroupDeployment -Name opt-out -ResourceGroupName <ResourceGroupName> -TemplateFile .\OptOutTemplate.json -TemplateParameterFile .\OptOutParam.json
        ```

        A konfiguráció módosítását is igénybe vehet néhány percet. Ha elkészült, a, amely tartalmazza az eredmény az alábbihoz hasonló üzenetet kapja:

        ```powershell
        ProvisioningState       : Succeeded
        ```

    * A futtassa a következő parancs az Azure CLI Linux rendszeren:

        ```azurecli
        az login   
        az account set --subscription "Subscription Name" 
        az group deployment create --resource-group <ResourceGroupName> --template-file ./OptOutTemplate.json --parameters @./OptOutParam.json  
        ```

        A konfiguráció módosítását is igénybe vehet néhány percet. Ha elkészült, a, amely tartalmazza az eredmény az alábbihoz hasonló üzenetet kapja:

        ```azurecli
        ProvisioningState       : Succeeded
        ```

Ha a munkaterületet hoztak létre csak a fürt figyelésére alkalmas, és már nem szükséges, akkor manuálisan törölni. Ha nem ismeri a munkaterület törlése, lásd: [és az Azure portál egy Azure Naplóelemzési munkaterület törlése](../log-analytics/log-analytics-manage-del-workspace.md).  Ne feledje kapcsolatos a **munkaterület erőforrás-azonosító** azt korábban a 4. lépésben másolt, szükség van rá fog.  

## <a name="troubleshooting"></a>Hibaelhárítás
Ez a szakasz bővebb információt talál a tároló állapotát elhárítása.

Ha tároló állapota volt sikeresen engedélyezve és konfigurálva van, de nem érzékelhető bármely állapotinformáció vagy Naplóelemzési, ha egy napló keresést hajt végre eredményez, végezheti az alábbi lépéseket a probléma diagnosztizálása érdekében van.   

1. Ellenőrizze az ügynök állapotát a következő parancs futtatásával: `kubectl get ds omsagent --namespace=kube-system`

    A kimeneti kell hasonlítania az alábbi, amely jelzi, az ügynök fut-e a fürt összes csomópontján.  Például a fürt két csomóponttal rendelkezik, és a csomópontok száma egyenlő értéket kell látnia.  

    ```
    User@aksuser:~$ kubectl get ds omsagent --namespace=kube-system
    NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR                 AGE
    omsagent   2         2         2         2            2           beta.kubernetes.io/os=linux   1d
    ```
2. A állapotának ellenőrzése, hogy fut-e fogyasztanak vagy nem a következő parancs futtatásával: `kubectl get pods --namespace=kube-system`

    A kimenet a következőre kell hasonlítania állapotú *futtató* a omsagent számára:

    ```
    User@aksuser:~$ kubectl get pods --namespace=kube-system 
    NAME                                READY     STATUS    RESTARTS   AGE 
    aks-ssh-139866255-5n7k5             1/1       Running   0          8d 
    azure-vote-back-4149398501-7skz0    1/1       Running   0          22d 
    azure-vote-front-3826909965-30n62   1/1       Running   0          22d 
    omsagent-484hw                      1/1       Running   0          1d 
    omsagent-fkq7g                      1/1       Running   0          1d 
    ```

3. Ellenőrizze az ügynök bejegyzéseit. A tárolóalapú ügynök telepítésekor, futtatja a Gyorsellenőrzés OMI parancsok futtatásával, és az ügynök és a Docker szolgáltató verzióját jeleníti meg. Ha látni szeretné, hogy az ügynök sikeresen lett előkészítve, futtassa a következő parancsot: `kubectl logs omsagent-484hw --namespace=kube-system`

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

[Naplók keresése](../log-analytics/log-analytics-log-search.md) részletes tároló állapotát és az alkalmazás teljesítményadatok megtekintéséhez.  
