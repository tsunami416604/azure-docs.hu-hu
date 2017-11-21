---
title: "Figyelés és a tárolók a Windows Azure diagnosztikai Service Fabric |} Microsoft Docs"
description: "Figyelés beállítása, és az Azure Service Fabric összehangolva diagnosztika a Windows-tárolót."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2017
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 69a59ea9fb93f6e9f3f3eea66b1a9e973b1b4eea
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2017
---
# <a name="monitor-windows-containers-on-service-fabric-using-oms"></a>A Service Fabric használatával OMS Windows tárolók figyelése

Ez annak az oktatóanyagnak három, és bemutatja, hogyan figyelheti a Windows-tárolók összehangolva. a Service Fabric OMS beállítása.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A Service Fabric-fürt OMS konfigurálása
> * Az OMS-munkaterület segítségével megtekintheti, és a tárolók és a csomópontok naplók lekérdezése
> * A tároló és a csomópont metrikák átvételéhez OMS-ügynököt konfigurálása

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag elkezdéséhez a következőket:
- Az Azure, a fürt vagy [hozzon létre egyet az oktatóanyaghoz](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
- [Azt a tárolóalapú alkalmazás központi telepítése](service-fabric-host-app-in-a-container.md)

## <a name="setting-up-oms-with-your-cluster-in-the-resource-manager-template"></a>A fürthöz, a Resource Manager sablon OMS beállítása

Abban az esetben használt a [sablon](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/Tutorial) Ez az oktatóanyag első részében, az alábbi kiegészítésekkel általános Service Fabric Azure Resource Manager sablonhoz szerepelnie kell benne. Abban az esetben az eset, hogy rendelkezik-e a saját, amely egy fürt csak a keresett OMS tárolók figyelés beállításához:
* A következő módosításokat a Resource Manager sablonhoz.
* Frissítse a fürt által a PowerShell használatával telepítheti [a sablon telepítése](https://docs.microsoft.com/en-us/azure/service-fabric/service-fabric-cluster-creation-via-arm). Az Azure Resource Manager valósít meg, hogy az erőforrás létezik, így fog megkezdik az frissítéseként.

### <a name="adding-oms-to-your-cluster-template"></a>A fürt sablon OMS hozzáadása

A következő módosításokat a *template.json*:

1. Adja hozzá az OMS-munkaterület helyét, és a nevet a *paraméterek* szakasz:
    
    ```json
    "omsWorkspacename": {
      "type": "string",
      "defaultValue": "[toLower(concat('sf',uniqueString(resourceGroup().id)))]",
      "metadata": {
        "description": "Name of your OMS Log Analytics Workspace"
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
        "description": "Specify the Azure Region for your OMS workspace"
      }
    }
    ```

    Módosítsa az értéket, vagy a adja hozzá azonos paraméterekkel a *template.parameters.json* és ott használt értékeit.

2. Adja hozzá a megoldás neve és a megoldás a *változók*: 
    
    ```json
    "omsSolutionName": "[Concat('ServiceFabric', '(', parameters('omsWorkspacename'), ')')]",
    "omsSolution": "ServiceFabric"
    ```

3. Adja hozzá az OMS Microsoft Monitoring Agent egy virtuálisgép-bővítményt. Talált virtuálisgép-méretezési készletet erőforrás: *erőforrások* > *"apiVersion": "[variables('vmssApiVersion')]"*. Az a *tulajdonságok* > *virtualMachineProfile* > *extensionProfile* > *bővítmények*, adja hozzá a következő kiterjesztés leírása alapján a *ServiceFabricNode* bővítmény: 
    
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

4. Adja hozzá az OMS-munkaterület egyedi erőforrás. A *erőforrások*, miután a virtuálisgép-méretezési beállítja erőforrás, adja hozzá a következő:
    
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

[Itt](https://github.com/ChackDan/Service-Fabric/blob/master/ARM%20Templates/Tutorial/azuredeploy.json) egy minta sablon (a használt része egy Ez az oktatóanyag), amely rendelkezik az összes ezek a változások, melyeket referenciaként használhat igény szerint. Ezeket a módosításokat az OMS Naplóelemzési munkaterület adja hozzá az erőforráscsoport. A munkaterület lesz konfigurálva a Service Fabric platform események konfigurált tárolási táblákból átvételéhez a [Windows Azure diagnosztikai](service-fabric-diagnostics-event-aggregation-wad.md) ügynök. Az OMS-ügynököt (Microsoft Monitoring Agent) is bővült a virtuális gép bővítményként - fürt minden csomópontja Ez azt jelenti, hogy méretezni a fürtön, mert az ügynök automatikusan konfigurálva a minden egyes számítógépen, és csatlakoztatnia kell ugyanazon a munkaterületen.

Az aktuális fürt frissítése az új módosításokkal a sablon telepítéséhez. Meg kell jelennie az OMS-erőforrások az erőforráscsoportban ennek befejeződése után. Amikor készen áll a fürt, akkor a tárolóalapú alkalmazást telepíti. A következő lépésben azt állítja be a tárolók figyelése.

## <a name="add-the-container-monitoring-solution-to-your-oms-workspace"></a>A tároló figyelésére szolgáló megoldás hozzáadása az OMS-munkaterület

A munkaterület beállítása a tároló megoldást, keresse meg *tároló figyelésére szolgáló megoldás* , és hozzon létre egy tároló-erőforrás (alatt a figyelés és a felügyeleti kategória).

![Tárolók megoldás hozzáadása](./media/service-fabric-tutorial-monitoring-wincontainers/containers-solution.png)

Ha a rendszer kéri a *OMS-munkaterület*, válassza ki a munkaterületen létrehozott az erőforráscsoportban, és kattintson **létrehozása**. Ezzel hozzáad egy *tároló figyelésére szolgáló megoldás* a munkaterületet, automatikusan, akkor az OMS-ügynököt a sablon által telepített elindítani a docker naplók és a statisztikák összegyűjtése. 

Lépjen vissza a *erőforráscsoport*, ahol meg kell jelennie az újonnan hozzáadott figyelési megoldást igényelnek. Ha bele gombra kattint, a kezdőlapja kell is láthat tároló lemezképek számát futtatása. 

*Vegye figyelembe, hogy a fabrikam tárolót 5 példányai futtattam [rész](service-fabric-host-app-in-a-container.md) az oktatóanyag*

![Tároló megoldás kezdőlapja](./media/service-fabric-tutorial-monitoring-wincontainers/solution-landing.png)

Kattintson a **tároló figyelő megoldás** léphet vissza, amely lehetővé teszi több információs panel is végiggörgethetnek, valamint a lekérdezések futtatása a Naplóelemzési részletes irányítópult. 

*Vegye figyelembe, hogy frissítésétől 2017. szeptember, a megoldás megy keresztül egyes frissítések - figyelmen kívül hagyja ki a hibákat kaphat Kubernetes eseményekről, a több orchestrators integrálása ugyanahhoz a megoldáshoz dolgozunk.*

Mivel az ügynök docker naplók van elindítását, alapértelmezés szerint a megjelenítő *stdout* és *stderr*. Görgessen jobbra, látják tároló kép készlet, állapot, metrikákat és a lekérdezés, amely futtatható a további hasznos adatok eléréséhez. 

![Tároló megoldás irányítópultja](./media/service-fabric-tutorial-monitoring-wincontainers/container-metrics.png)

Minden e panelek gombra kattintva léphet a Log Analytics-lekérdezés, hogy a megjelenített érték. Módosítsa a lekérdezést úgy  *\**  -naplók alatt átveszik különböző típusú megjelenítéséhez. Itt lekérdezés vagy szűrő a tároló teljesítmény, a naplókat, vagy nézze meg a Service Fabric platform események. Az ügynökök is folyamatosan van minden csomópontja, vessen egy pillantást győződjön meg arról, hogy az adatokat továbbra is alatt gyűjtött összes gép, ha a fürt konfigurációját, megváltozik a szívverést kibocsátó.   

![Tároló lekérdezés](./media/service-fabric-tutorial-monitoring-wincontainers/query-sample.png)

## <a name="configure-oms-agent-to-pick-up-performance-counters"></a>Teljesítményszámlálók átvételéhez OMS-ügynököt konfigurálása

Egy másik előnye, hogy az OMS-ügynök használatával megváltoztathatja az OMS felhasználói felületi élmény keresztül átvételéhez kívánt teljesítményszámlálók ahelyett, nem kell az Azure diagnostics ügynök konfigurálását és a Resource Manager sablon alapú frissítést minden alkalommal, amikor. Ehhez kattintson a **OMS-portálon** a kezdőlapja a tároló figyelése (vagy a Service Fabric) megoldás.

![OMS-portál](./media/service-fabric-tutorial-monitoring-wincontainers/oms-portal.png)

Ez fogja léphet a munkaterület az OMS-portálon, ahol megtekintheti a megoldások, hozzon létre egyéni irányítópultok, valamint az OMS-ügynök konfigurálása. 
* Kattintson a a **fogaskerékre kerék** meg szeretné nyitni a képernyő jobb felső sarkában a *beállítások* menü.
* Kattintson a **csatlakoztatott források** > **Windows kiszolgálók** ellenőrzése, hogy *5 Windows-számítógépek kapcsolódnak*.
* Kattintson a **adatok** > **Windows teljesítményszámlálók** keresését, és adja hozzá az új teljesítményszámlálói. Itt látni fogja a javaslatok listája az OMS Szolgáltatáshoz a Teljesítményfigyelő számlálók, illetve a keresse meg a többi számláló tudta összegyűjteni. Kattintson a **adja hozzá a kijelölt teljesítményszámlálók** való kezdi meg a javasolt metrikákat.

    ![Teljesítményszámlálói](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters.png)

Vissza az Azure-portálon **frissítése** néhány percet, és a tároló figyelésére szolgáló megoldás kell kezdődnie, hogy *számítógép teljesítményét* érkező adatokat. Ez segít megérteni, hogy az erőforrások felhasználását. A metrikák megfelelő döntéseket a fürt méretezésével kapcsolatos, illetve erősítse meg, ha a fürt van terheléselosztás a várt módon használhatja.

*Megjegyzés: Ellenőrizze, hogy a idő szűrő megfelelően van-e beállítva, hogy ezeket a mérési felhasználását.* 

![2 teljesítményszámlálói](./media/service-fabric-tutorial-monitoring-wincontainers/perf-counters2.png)


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A Service Fabric-fürt OMS konfigurálása
> * Az OMS-munkaterület segítségével megtekintheti, és a tárolók és a csomópontok naplók lekérdezése
> * A tároló és a csomópont metrikák átvételéhez OMS-ügynököt konfigurálása

Most, hogy állította be a tárolóalapú alkalmazás figyelését, próbálja meg a következőket:

* Állítsa be az OMS Linux fürt esetén, a fenti hasonló lépésekkel. Hivatkozás [sablon](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux) a Resource Manager-sablon a módosításokat.
* Konfigurálja az OMS beállítása [riasztás automatikus](../log-analytics/log-analytics-alerts.md) észlelésére és diagnosztika.
* A Service Fabric listájának tallózása [teljesítményszámlálók ajánlott](service-fabric-diagnostics-event-generation-perf.md) a fürtök konfigurálása.
* Az beszerzése familiarized a [naplófájl keresési és lekérdezése](../log-analytics/log-analytics-log-searches.md) szolgáltatásai által kínált Naplóelemzési.