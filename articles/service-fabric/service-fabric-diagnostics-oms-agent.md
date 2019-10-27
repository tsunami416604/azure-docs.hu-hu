---
title: Azure Service Fabric – Teljesítményfigyelés Azure Monitor naplókkal | Microsoft Docs
description: Ismerje meg, hogyan állíthatja be a Log Analytics ügynököt az Azure Service Fabric-fürtökhöz tartozó tárolók és teljesítményszámlálók figyeléséhez.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 9ec68712c898eefc37a8f7b2fe2dbfdb119592de
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72934006"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Teljesítményfigyelés Azure Monitor naplókkal

Ez a cikk a Log Analytics-ügynök virtuálisgép-méretezési csoportként való hozzáadásának lépéseit ismerteti a fürthöz, és a meglévő Azure Log Analytics-munkaterülethez csatlakoztatható. Ez lehetővé teszi a tárolók, alkalmazások és Teljesítményfigyelés diagnosztikai adatainak gyűjtését. Ha hozzáad egy bővítményt a virtuálisgép-méretezési csoport erőforrásához, Azure Resource Manager biztosítja, hogy az minden csomóponton telepítve legyen, még a fürt skálázásakor is.

> [!NOTE]
> Ez a cikk azt feltételezi, hogy már be van állítva egy Azure Log Analytics-munkaterület. Ha nem, a [Azure monitor naplók beállításához](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Az ügynök bővítmény hozzáadása az Azure CLI-n keresztül

Az Log Analytics-ügynök fürthöz való hozzáadásának legjobb módja az Azure CLI-vel elérhető virtuálisgép-méretezési csoport API-k használatával. Ha még nem állította be az Azure CLI-t, látogasson el Azure Portalre, és nyisson meg egy [Cloud Shell](../cloud-shell/overview.md) példányt, vagy [telepítse az Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)-t.

1. A Cloud Shell kérése után ellenőrizze, hogy az erőforrással megegyező előfizetésben dolgozik-e. Ellenőrizze ezt a `az account show`, és győződjön meg arról, hogy a "Name" érték megegyezik a fürt előfizetésével.

2. A portálon navigáljon ahhoz az erőforráscsoporthoz, ahol a Log Analytics munkaterület található. Kattintson a log Analytics-erőforrásra (az erőforrás típusa Log Analytics munkaterület lesz). Az erőforrás-Áttekintés lapon kattintson a **Speciális beállítások** elemre a bal oldali menüben a beállítások szakaszban.

    ![Log Analytics-Tulajdonságok lap](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Ha **Windows-fürtöt vagy Linux** -fürtöt hoz létre, kattintson a **Windows-kiszolgálók** lehetőségre. Ezen az oldalon megtekintheti a `workspace ID` és `workspace key` (a portálon elsődleges kulcsként jelenik meg). A következő lépéshez mindkettőre szüksége lesz.

4. Futtassa a parancsot a Log Analytics-ügynök a fürtre való telepítéséhez a Cloud Shell `vmss extension set` API használatával:

    Windows-fürt esetén:

    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Linux-fürtök esetén:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Íme egy példa arra, hogy a Windows-fürthöz milyen Log Analytics-ügynök van hozzáadva.

    ![Log Analytics Agent CLI-parancs](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. Ennek elvégzéséhez kevesebb, mint 15 percnek kell lennie ahhoz, hogy sikeresen hozzáadja az ügynököt a csomópontokhoz. A `az vmss extension list` API használatával ellenőrizheti, hogy az ügynökök hozzá lettek-e adva:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Az ügynök hozzáadása a Resource Manager-sablon használatával

Az Azure Log Analytics-munkaterületet üzembe helyező Resource Manager-sablonok, valamint az egyes csomópontokhoz tartozó ügynökök [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) vagy [Linux](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS)rendszeren is elérhetők.

A sablont letöltheti és módosíthatja úgy, hogy olyan fürtöt helyezzen üzembe, amely megfelel az igényeinek.

## <a name="view-performance-counters"></a>Teljesítményszámlálók megtekintése

Most, hogy hozzáadta a Log Analytics-ügynököt, a Log Analytics portálra bejelentkezve kiválaszthatja, hogy mely teljesítményszámlálókat szeretné összegyűjteni.

1. A Azure Portal keresse meg azt az erőforráscsoportot, amelyben létrehozta a Service Fabric Analytics megoldást. Válassza a **ServiceFabric\<NameOfLog AnalyticsWorkspace\>** lehetőséget.

2. Kattintson a **Log Analytics** elemre.

3. Kattintson a **Speciális beállítások**elemre.

4. Kattintson **az adat**, majd a **Windows-vagy Linux-teljesítményszámlálók**elemre. Az alapértelmezett számlálók listája lehetővé teszi az engedélyezést, és beállíthatja a gyűjtemény intervallumát is. [További teljesítményszámlálókat](service-fabric-diagnostics-event-generation-perf.md) is hozzáadhat a gyűjtéshez. Ennek a [cikknek](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx)a megfelelő formátumra hivatkozik.

5. Kattintson a **Mentés**, majd **az OK**gombra.

6. A speciális beállítások panel bezárásához.

7. Az általános fejléc alatt kattintson a **munkaterület összegzése**lehetőségre.

8. A csempék egy gráf formájában jelennek meg az összes engedélyezett megoldáshoz, beleértve az egyiket a Service Fabric. A Service Fabric Analytics megoldás folytatásához kattintson a **Service Fabric** gráfra.

9. Az operatív csatornán és a megbízható szolgáltatások eseményein grafikonokkal ellátott csempék jelennek meg. A kiválasztott számlálóhoz tartozó adatok grafikus ábrázolása a csomópont Metrikái alatt fog megjelenni.

10. A további részletek megtekintéséhez kattintson a Container metrika gráfra. A teljesítményszámláló adataihoz hasonlóan is lekérdezheti a fürt eseményeinek adatait, és szűrheti a csomópontok, a teljesítményszámláló nevét és az értékeket a Kusto lekérdezési nyelv használatával.

![Log Analytics teljesítményszámláló-lekérdezés](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Következő lépések

* A megfelelő [teljesítményszámlálók](service-fabric-diagnostics-event-generation-perf.md)összegyűjtése. Ha a Log Analytics-ügynököt egy adott teljesítményszámlálók gyűjtésére szeretné beállítani, tekintse át az [adatforrások konfigurálását ismertető témakört](../azure-monitor/platform/agent-data-sources.md#configuring-data-sources)
* Azure Monitor naplók konfigurálása az [automatizált riasztások](../log-analytics/log-analytics-alerts.md) beállításához az észlelés és a diagnosztika támogatásához
* Alternatív megoldásként összegyűjtheti a teljesítményszámlálók [Azure Diagnostics bővítmény használatával, és elküldheti őket Application Insights](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
