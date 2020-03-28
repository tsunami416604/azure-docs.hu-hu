---
title: Windows-tárolók figyelése és diagnosztizálása
description: Ebben az oktatóanyagban konfigurálja az Azure Monitor naplók figyelése és diagnosztika a Windows-tárolók az Azure Service Fabric.
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: eeb279892f987ed1f26ced97ab267e8140ccb20e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75614060"
---
# <a name="tutorial-monitor-windows-containers-on-service-fabric-using-azure-monitor-logs"></a>Oktatóanyag: Windows-tárolók figyelése a Service Fabric szolgáltatáson az Azure Monitor naplóinak használatával

Ez egy oktatóanyag harmadik része, és végigvezeti az Azure Monitor naplók beállításán a Service Fabric-en hangszerelt Windows-tárolók figyeléséhez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure Monitor-naplók konfigurálása a Service Fabric-fürthöz
> * Log Analytics-munkaterület használata a tárolók és csomópontok naplóinak megtekintéséhez és lekérdezéséhez
> * A Log Analytics-ügynök konfigurálása tároló- és csomópontmetrikák felvételéhez

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Rendelkeznie kell egy fürttel az Azure-on, vagy [létre kell hoznia egyet ennek az oktatóanyagnak a segítségével](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Üzembe kell helyeznie hozzá egy tárolóba helyezett alkalmazást](service-fabric-host-app-in-a-container.md)

## <a name="setting-up-azure-monitor-logs-with-your-cluster-in-the-resource-manager-template"></a>Az Azure Monitor-naplók beállítása a fürttel az Erőforráskezelő sablonban

Abban az esetben, ha az oktatóanyag első részében [megadott sablont](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) használta, tartalmaznia kell az általános Service Fabric Azure Resource Manager-sablon alábbi kiegészítéseit. Abban az esetben, ha saját fürttel rendelkezik, amelyet az Azure Monitor-naplókkal rendelkező tárolók figyelésére szeretne beállítani:

* Hajtsa végre az alábbi módosításokat a Resource Manager-sablonon.
* Helyezze üzembe a PowerShell használatával a fürt a [sablon üzembe helyezésével](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-creation-via-arm) történő frissítéséhez. Az Azure Resource Manager megállapítja, hogy az erőforrás létezik, ezután pedig frissítésként közzéteszi.

### <a name="adding-azure-monitor-logs-to-your-cluster-template"></a>Azure Monitor-naplók hozzáadása a fürtsablonhoz

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

3. Adja hozzá a Microsoft Monitoring Agentet virtuálisgép-bővítményként. Keresse meg a virtuálisgép-méretezési készletek erőforrás: *erőforrások* > *"apiVersion": "[variables('vmssApiVersion')]"*. A *properties* > *virtualMachineProfile* > *extensionS* > *kiterjesztések*csoportban adja meg a következő bővítményleírást a *ServiceFabricNode* bővítmény alatt: 
    
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

Hivatkozási célból [itt](https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/d2ffa318581fc23ac7f1b0ab2b52db1a0d7b4ba7/5-VM-Windows-OMS-UnSecure/sfclusteroms.json) található egy (az oktatóanyag első részében használt) mintasablon, amely tartalmazza ezeket a módosításokat. Ezek a módosítások egy Log Analytics-munkaterületet adnak hozzá az erőforráscsoporthoz. A munkaterület úgy lesz konfigurálva, hogy felvegye a Service Fabric-platform eseményeit azon tárolótáblákból, amelyek a [Windows Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md)-ügynökkel lettek konfigurálva. A Log Analytics-ügynök (Microsoft Monitoring Agent) szintén hozzá lett adva a fürt minden csomópontjához virtuálisgép-bővítményként – ez azt jelenti, hogy a fürt méretezése közben az ügynököt minden számítógépen automatikusan konfigurálja és ugyanahhoz a munkaterülethez csatolja a rendszer.

Helyezze üzembe a sablont az új módosításokkal az aktuális fürt frissítéséhez. Ennek befejezése után az erőforráscsoportban látnia kell a naplóelemzési erőforrásokat. Amint a fürt kész, helyezze rajta üzembe a tárolóba helyezett alkalmazást. A következő lépésben a tárolók monitorozását állítjuk be.

## <a name="add-the-container-monitoring-solution-to-your-log-analytics-workspace"></a>A tárolómonitorozási megoldás hozzáadása a Log Analytics-munkaterülethez

A tárolómegoldások beállításához a munkaterületen keresse meg a *Tárolómonitorozási megoldást*, és hozzon létre egy tároló-erőforrást (a Monitoring és felügyelet kategóriában).

![Tárolómegoldások hozzáadása](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

Amikor a rendszer a *Log Analytics munkaterületre*kéri, jelölje ki az erőforráscsoportban létrehozott munkaterületet, és kattintson a **Létrehozás gombra.** Ezzel hozzáad egy *tárolómonitorozási megoldást* a munkaterülethez, ami miatt a sablon által üzembe helyezett Log Analytics-ügynök automatikusan elkezdi Docker-naplók és -statisztikák gyűjtését. 

Lépjen vissza az *erőforráscsoporthoz*, ahol meg kell jelennie az újonnan hozzáadott monitorozási megoldásnak. Ha a megoldásra kattint, a kezdőlapon meg kell jelennie, hogy hány tárolórendszerképet futtat.

*Az oktatóanyag [második részéből](service-fabric-host-app-in-a-container.md) származó fabrikam tároló 5 példányát futtattam*.

![Tárolómegoldás kezdőlapja](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

A **Tárolófigyelő megoldásra** kattintva egy részletesebb irányítópultra jut, amely lehetővé teszi több panel görgetését, valamint lekérdezések futtatását az Azure Monitor naplóiban.

*Vegye figyelembe, hogy 2017 szeptemberétől kezdve a megoldás folyamatosan frissül – hagyja figyelmen kívül a Kubernetes-eseményekkel kapcsolatos esetleges hibákat. Dolgozunk azon, hogy több vezérlőt integráljunk ugyanabba a megoldásba.*

Mivel az ügynök Docker-naplókat vesz fel, alapértelmezés szerint az *stdout* és az *stderr* jelenik meg. Ha jobbra görget, láthatja a tárolórendszerkép leltárát, állapotát, metrikáit, valamint példákat olyan lekérdezésekre, amelyeket több információt tartalmazó adatok eléréséhez futtathat.

![Tárolómegoldás irányítópultja](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Ha ezekre a panelekre kattint, akkor a Megjelenített értéket generáló Kusto lekérdezéshez kerül. Módosítsa a *\** lekérdezést úgy, hogy a felvett naplók különböző típusait láthassa. Innen lekérdezéseket futtathat, szűrhet a tároló teljesítményére és naplóira, valamint megtekintheti a Service Fabric-platformeseményeket. Az ügynökök is folyamatosan szívveréseket bocsátanak ki minden csomópontból, és ezek a fürt konfigurációjának módosításakor történő ellenőrzésével meggyőződhet arról, hogy a rendszer továbbra is az összes számítógépről gyűjti az adatokat.

![Tároló lekérdezése](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-log-analytics-agent-to-pick-up-performance-counters"></a>A Log Analytics-ügynök konfigurálása teljesítményszámlálók felvételéhez

A Log Analytics-ügynök használatának másik előnye, hogy módosíthatja a naplóelemzési felhasználói felületen felvenni kívánt teljesítményszámlálókat, ahelyett, hogy konfigurálnia kellene az Azure diagnosztikai ügynököt, és erőforrás-kezelősablon-alapú frissítést kellene végeznie. minden alkalommal. Ehhez a tárolómonitorozási (vagy Service Fabric-) megoldás kezdőlapján kattintson az **OMS-munkaterület** lehetőségre.

Ekkor megnyílik a Log Analytics-munkaterület, ahol megtekintheti a megoldásokat, létrehozhat egyéni irányítópultokat, és konfigurálhatja a Log Analytics-ügynököt. 
* Kattintson a **Speciális beállítások** elemre a Speciális beállítások menü megnyitásához.
* Kattintson **a Csatlakoztatott források** > **Windows-kiszolgálók** elemre annak ellenőrzéséhez, hogy *5 Csatlakoztatott Windows-számítógéppel rendelkezik-e.*
* Kattintson **az Adat** > **Windows teljesítményszámlálók** elemre új teljesítményszámlálók kereséséhez és hozzáadásához. Itt láthatja az Azure Monitor naplóiból származó javaslatok listáját a teljesítményszámlálók hoz gyűjthet, valamint a lehetőséget, hogy más számlálók keresése lehetőséget. Ellenőrizze, hogy **Processzor(_Total)\% A processzor kihasználtsága** és a **Memória(*)\Rendelkezésre álló memória (megabájt)** számlálók össze vannak-e gyűjtve.

Néhány perc múlva **frissítse** a Tárolómonitorozási megoldást, és ezután látnia kell, ahogy érkeznek a *Számítógép teljesítményére* vonatkozó adatok. Ez segít megérteni, hogyan használja a rendszer az erőforrásokat. Ezeket a metrikákat a fürt méretezésére vonatkozó, megfelelő döntések meghozásához is használhatja, vagy annak megerősítéséhez, hogy a fürt a vártnak megfelelően osztja el a terhelést.

*Megjegyzés: A metrikák felhasználásához győződjön meg arról, hogy az idő szűrői megfelelően vannak beállítva.*

![Teljesítményszámlálók 2](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure Monitor-naplók konfigurálása a Service Fabric-fürthöz
> * Log Analytics-munkaterület használata a tárolók és csomópontok naplóinak megtekintéséhez és lekérdezéséhez
> * A Log Analytics-ügynök konfigurálása tároló- és csomópontmetrikák felvételéhez

Most, hogy beállította a tárolóba helyezett alkalmazás monitorozását, megpróbálkozhat a következőkkel:

* Állítsa be az Azure Monitor naplók egy Linux-fürthöz, a fentiekhez hasonló lépéseket követve. Hivatkozzon [erre a sablonra](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS) a módosítások a Resource Manager-sablonban történő elvégzéséhez.
* Konfigurálja az Azure Monitor naplóit [az automatikus riasztásbeállításhoz,](../log-analytics/log-analytics-alerts.md) hogy segítse a észlelést és a diagnosztikát.
* Tekintse meg a Service Fabric a fürthöz konfigurálható, [ajánlott teljesítményszámlálókat](service-fabric-diagnostics-event-generation-perf.md) tartalmazó listáját.
* Ismerkedjen meg az Azure Monitor-naplók részeként kínált [naplókeresési és lekérdezési](../log-analytics/log-analytics-log-searches.md) funkciókkal.
