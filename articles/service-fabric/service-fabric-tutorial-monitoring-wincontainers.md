---
title: Windows-tárolók monitorozása és diagnosztikája a Service Fabricben az Azure-ban | Microsoft Docs
description: Ebben az oktatóanyagban konfigurálhatja a Log Analytics szolgáltatást az Azure Service Fabric Windows-tárolóinak monitorozására és diagnosztikájára.
services: service-fabric
documentationcenter: .net
author: TylerMSFT
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/08/2018
ms.author: twhitney, dekapur
ms.custom: mvc
ms.openlocfilehash: 9249c0bb559328c878d784b54af5117b785daffe
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2018
ms.locfileid: "51300625"
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-log-analytics"></a>Oktatóanyag: A Service Fabric Windows-tárolóinak monitorozása a Log Analytics használatával

Ez az oktatóanyag harmadik része, amely végigvezeti a Log Analytics beállításán a Service Fabricen vezényelt Windows-tárolók monitorozásához.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Log Analytics konfigurálása a Service Fabric-fürthöz
> * Log Analytics-munkaterület használata a tárolók és csomópontok naplóinak megtekintéséhez és lekérdezéséhez
> * A Log Analytics-ügynök konfigurálása tároló- és csomópontmetrikák felvételéhez

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Rendelkeznie kell egy fürttel az Azure-on, vagy [létre kell hoznia egyet ennek az oktatóanyagnak a segítségével](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Üzembe kell helyeznie hozzá egy tárolóba helyezett alkalmazást](service-fabric-host-app-in-a-container.md)

## <a name="setting-up-log-analytics-with-your-cluster-in-the-resource-manager-template"></a>Log Analytics beállítása a fürttel a Resource Manager-sablonban

Abban az esetben, ha az oktatóanyag első részében [megadott sablont](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) használta, tartalmaznia kell az általános Service Fabric Azure Resource Manager-sablon alábbi kiegészítéseit. Ha saját fürttel rendelkezik, amelyet a tárolók Log Analytics szolgáltatással való monitorozására szeretne használni:

* Hajtsa végre az alábbi módosításokat a Resource Manager-sablonon.
* Helyezze üzembe a PowerShell használatával a fürt a [sablon üzembe helyezésével](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) történő frissítéséhez. Az Azure Resource Manager megállapítja, hogy az erőforrás létezik, ezután pedig frissítésként közzéteszi.

### <a name="adding-log-analytics-to-your-cluster-template"></a>Log Analytics hozzáadása a fürtsablonhoz

Hajtsa végre a következő módosításokat a *template.json* fájlban:

1. Adja hozzá a Log Analytics-munkaterület helyét és nevét a *parameters* (paraméterek) szakaszhoz:

    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your Log Analytics Workspace"
      }
    },
    "omsRegion": {
      "type": "string",
      "defaultValue": "East US",
      "allowedValues": [
        "West Europe",
        "East US",
        "Southeast Asia"
      ],
      "metadata": {
        "description": "Specify the Azure Region for your Log Analytics workspace"
      }
    }
    ```

    A név vagy hely értékének módosításához adja hozzá ugyanazokat a paramétereket a *template.parameters.json* fájlhoz, és módosítsa az ott használt értékeket.

2. Adja hozzá a megoldás nevét és a megoldást a *variables* (változók) szakaszhoz:

    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Adja hozzá a Microsoft Monitoring Agentet virtuálisgép-bővítményként. Virtuálisgép-méretezési csoportok erőforrásainak keresése: *resources* > *"apiVersion": "[variables('vmssApiVersion')]"*. A *properties* > *virtualMachineProfile* > *extensionProfile* > *extensions* szakaszban adja hozzá a következő bővítményleírást a *ServiceFabricNode* bővítmény alatt: 
    
    ```json
    {
        "name": "[concat(variables('vmNodeType0Name'),'OMS')]",
        "properties": {
            "publisher": "Microsoft.EnterpriseCloud.Monitoring",
            "type": "MicrosoftMonitoringAgent",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "workspaceId": "[reference(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')), '2015-11-01-preview').customerId]"
            },
            "protectedSettings": {
                "workspaceKey": "[listKeys(resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename')),'2015-11-01-preview').primarySharedKey]"
            }
        }
    },
    ```

4. Adja hozzá a Log Analytics-munkaterületet önálló erőforrásként. A *resources* (erőforrások) szakaszban, a virtuálisgép-méretezési csoportok erőforrásait követően vegye fel az alábbiakat:

    ```json
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[parameters('omsWorkspacename')]",
        "type": "Microsoft.OperationalInsights/workspaces",
        "properties": {
            "sku": {
                "name": "Free"
            }
        },
        "resources": [
            {
                "apiVersion": "2015-11-01-preview",
                "name": "[concat(variables('applicationDiagnosticsStorageAccountName'),parameters('omsWorkspacename'))]",
                "type": "storageinsightconfigs",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]",
                    "[concat('Microsoft.Storage/storageAccounts/', variables('applicationDiagnosticsStorageAccountName'))]"
                ],
                "properties": {
                    "containers": [ ],
                    "tables": [
                        "WADServiceFabric*EventTable",
                        "WADWindowsEventLogsTable",
                        "WADETWEventTable"
                    ],
                    "storageAccount": {
                        "id": "[resourceId('Microsoft.Storage/storageaccounts/', variables('applicationDiagnosticsStorageAccountName'))]",
                        "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('applicationDiagnosticsStorageAccountName')),'2015-06-15').key1]"
                    }
                }
            },
            {
                "apiVersion": "2015-11-01-preview",
                "name": "System",
                "type": "datasources",
                "dependsOn": [
                    "[concat('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
                ],
                "kind": "WindowsEvent",
                "properties": {
                    "eventLogName": "System",
                    "eventTypes": [
                        {
                            "eventType": "Error"
                        },
                        {
                            "eventType": "Warning"
                        },
                        {
                            "eventType": "Information"
                        }
                    ]
                }
            }
        ]
    },
    {
        "apiVersion": "2015-11-01-preview",
        "location": "[parameters('omsRegion')]",
        "name": "[variables('omsSolutionName')]",
        "type": "Microsoft.OperationsManagement/solutions",
        "dependsOn": [
            "[concat('Microsoft.OperationalInsights/workspaces/', parameters('OMSWorkspacename'))]"
        ],
        "properties": {
            "workspaceResourceId": "[resourceId('Microsoft.OperationalInsights/workspaces/', parameters('omsWorkspacename'))]"
        },
        "plan": {
            "name": "[variables('omsSolutionName')]",
            "publisher": "Microsoft",
            "product": "[Concat('OMSGallery/', variables('omsSolution'))]",
            "promotionCode": ""
        }
    },
    ```

Hivatkozási célból [itt](https://github.com/ChackDan/Service-Fabric/blob/master/ARM%20Templates/Tutorial/azuredeploy.json) található egy (az oktatóanyag első részében használt) mintasablon, amely tartalmazza ezeket a módosításokat. Ezek a módosítások egy Log Analytics-munkaterületet adnak hozzá az erőforráscsoporthoz. A munkaterület úgy lesz konfigurálva, hogy felvegye a Service Fabric-platform eseményeit azon tárolótáblákból, amelyek a [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md)-ügynökkel lettek konfigurálva. A Log Analytics-ügynök (Microsoft Monitoring Agent) szintén hozzá lett adva a fürt minden csomópontjához virtuálisgép-bővítményként – ez azt jelenti, hogy a fürt méretezése közben az ügynököt minden számítógépen automatikusan konfigurálja és ugyanahhoz a munkaterülethez csatolja a rendszer.

Helyezze üzembe a sablont az új módosításokkal az aktuális fürt frissítéséhez. Miután a művelet befejeződött, a Log Analytics-erőforrásoknak megjelennek az erőforráscsoportban. Amint a fürt kész, helyezze rajta üzembe a tárolóba helyezett alkalmazást. A következő lépésben a tárolók monitorozását állítjuk be.

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>A tárolómonitorozási megoldás hozzáadása a Log Analytics-munkaterülethez

A tárolómegoldások beállításához a munkaterületen keresse meg a *Tárolómonitorozási megoldást*, és hozzon létre egy tároló-erőforrást (a Monitoring és felügyelet kategóriában).

![Tárolómegoldások hozzáadása](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

Ha a rendszer kéri a *Log Analytics-munkaterületet*, válassza ki az erőforráscsoportban létrehozott munkaterületet, majd kattintson a **Létrehozás** parancsra. Ezzel hozzáad egy *tárolómonitorozási megoldást* a munkaterülethez, ami miatt a sablon által üzembe helyezett Log Analytics-ügynök automatikusan elkezdi Docker-naplók és -statisztikák gyűjtését. 

Lépjen vissza az *erőforráscsoporthoz*, ahol meg kell jelennie az újonnan hozzáadott monitorozási megoldásnak. Ha a megoldásra kattint, a kezdőlapon meg kell jelennie, hogy hány tárolórendszerképet futtat.

*Az oktatóanyag [második részéből](service-fabric-host-app-in-a-container.md) származó fabrikam tároló 5 példányát futtattam*.

![Tárolómegoldás kezdőlapja](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

Ha a **Tárolómonitorozási megoldásra** kattint, a rendszer egy még részletesebb irányítópultra irányítja, ahol több ablaktáblán görgethet végig és lekérdezéseket futtathat a Log Analyticsben.

*Vegye figyelembe, hogy 2017 szeptemberétől kezdve a megoldás folyamatosan frissül – hagyja figyelmen kívül a Kubernetes-eseményekkel kapcsolatos esetleges hibákat. Dolgozunk azon, hogy több vezérlőt integráljunk ugyanabba a megoldásba.*

Mivel az ügynök Docker-naplókat vesz fel, alapértelmezés szerint az *stdout* és az *stderr* jelenik meg. Ha jobbra görget, láthatja a tárolórendszerkép leltárát, állapotát, metrikáit, valamint példákat olyan lekérdezésekre, amelyeket több információt tartalmazó adatok eléréséhez futtathat.

![Tárolómegoldás irányítópultja](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Ha az ablaktáblák közül bármelyikre rákattint, A rendszer átirányítja a megjelenített értéket létrehozó Log Analytics-lekérdezéshez. A felvett naplók különböző fajtáinak megtekintéséhez módosítsa a lekérdezést a következőre: *\**. Innen lekérdezéseket futtathat, szűrhet a tároló teljesítményére és naplóira, valamint megtekintheti a Service Fabric-platformeseményeket. Az ügynökök is folyamatosan szívveréseket bocsátanak ki minden csomópontból, és ezek a fürt konfigurációjának módosításakor történő ellenőrzésével meggyőződhet arról, hogy a rendszer továbbra is az összes számítógépről gyűjti az adatokat.

![Tároló lekérdezése](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-log-analytics-agent-to-pick-up-performance-counters"></a>A Log Analytics-ügynök konfigurálása teljesítményszámlálók felvételéhez

A Log Analytics-ügynök használatának egy másik előnye, hogy anélkül módosíthatók a Log Analytics felhasználói élményén keresztül a felvenni kívánt teljesítményszámlálók, hogy konfigurálni kellene az Azure Diagnostics-ügynököt, és minden alkalommal Resource Manager-sablonalapú frissítést kellene elvégeznie. Ehhez a tárolómonitorozási (vagy Service Fabric-) megoldás kezdőlapján kattintson az **OMS-munkaterület** lehetőségre.

Ekkor megnyílik a Log Analytics-munkaterület, ahol megtekintheti a megoldásokat, létrehozhat egyéni irányítópultokat, és konfigurálhatja a Log Analytics-ügynököt. 
* Kattintson a **Speciális beállítások** elemre a Speciális beállítások menü megnyitásához.
* Kattintson a **Csatlakoztatott források** > **Windows-kiszolgálók** elemre annak ellenőrzéséhez, hogy *5 Windows rendszerű számítógép van-e csatlakoztatva*.
* Kattintson az **Adatok** > **Windows-teljesítményszámlálók** elemre a teljesítményszámlálók kereséséhez és új teljesítményszámlálók hozzáadásához. Itt megjelenik a Log Analytics gyűjthető teljesítményszámlálókra vonatkozó javaslatainak listája, valamint az egyéb számlálók keresésének lehetősége. Ellenőrizze, hogy **Processzor(_Total)\% A processzor kihasználtsága** és a **Memória(*)\Rendelkezésre álló memória (megabájt)** számlálók össze vannak-e gyűjtve.

Néhány perc múlva **frissítse** a Tárolómonitorozási megoldást, és ezután látnia kell, ahogy érkeznek a *Számítógép teljesítményére* vonatkozó adatok. Ez segít megérteni, hogyan használja a rendszer az erőforrásokat. Ezeket a metrikákat a fürt méretezésére vonatkozó, megfelelő döntések meghozásához is használhatja, vagy annak megerősítéséhez, hogy a fürt a vártnak megfelelően osztja el a terhelést.

*Megjegyzés: A metrikák felhasználásához győződjön meg arról, hogy az idő szűrői megfelelően vannak beállítva.*

![Teljesítményszámlálók 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Log Analytics konfigurálása a Service Fabric-fürthöz
> * Log Analytics-munkaterület használata a tárolók és csomópontok naplóinak megtekintéséhez és lekérdezéséhez
> * A Log Analytics-ügynök konfigurálása tároló- és csomópontmetrikák felvételéhez

Most, hogy beállította a tárolóba helyezett alkalmazás monitorozását, megpróbálkozhat a következőkkel:

* Állítsa be a Log Analytics szolgáltatást egy Linux-fürthöz, a fentiekhez hasonló lépéseket követve. Hivatkozzon [erre a sablonra](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) a módosítások a Resource Manager-sablonban történő elvégzéséhez.
* Konfigurálja a Log Analytics szolgáltatást az [automatizált riasztások](../log-analytics/log-analytics-alerts.md) beállításához, ezzel elősegítve az észlelést és a diagnosztikát.
* Tekintse meg a Service Fabric a fürthöz konfigurálható, [ajánlott teljesítményszámlálókat](service-fabric-diagnostics-event-generation-perf.md) tartalmazó listáját.
* Ismerkedjen meg a Log Analytics részét képező [naplókeresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) funkcióval.