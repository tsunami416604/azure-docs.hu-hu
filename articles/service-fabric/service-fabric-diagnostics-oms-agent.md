---
title: Az Azure Service Fabric - alkalmazásteljesítmény-figyelés a Log Analytics-szel |} A Microsoft Docs
description: Ismerje meg, hogyan állítsa be a Log Analytics ügynököt tárolók és a teljesítményszámlálók az Azure Service Fabric-fürtök a figyelésre.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: e440cca59bde1c845bc99ef137c18242e888ee06
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230097"
---
# <a name="performance-monitoring-with-log-analytics"></a>Alkalmazásteljesítmény-figyelés a Log Analytics használatával

Ez a cikk ismerteti a lépéseket egy virtuális gép méretezési csoport bővítményének a fürthöz hozzáadni a Log Analytics-ügynököket, és csatlakoztassa a meglévő Azure Log Analytics-munkaterületet. Ez lehetővé teszi a tárolók, alkalmazások és alkalmazásteljesítmény-figyelési gyűjtését diagnosztikai adatait. Bővítményeként való hozzáadásával a virtuálisgép-méretezési készlet erőforrás, az Azure Resource Manager biztosítja, hogy megkapja-e telepítve minden csomóponton, még ha méretezése a fürtben.

> [!NOTE]
> Ez a cikk azt feltételezi, hogy rendelkezik-e már beállított egy Azure Log Analytics-munkaterületet. Ha ezt elmulasztja, látogasson el [Azure Log Analytics beállítása](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Adja hozzá az ügynök bővítményt, az Azure CLI-n keresztül

A legjobb módszer a Log Analytics-ügynököket a fürt hozzáadása a virtuálisgép-méretezési csoport keresztül beállítása API-kat az Azure CLI használatával. Ha az Azure parancssori felület beállítása még nem rendelkezik, látogasson el az Azure Portalon, és nyissa meg a [Cloud Shell](../cloud-shell/overview.md) példány, vagy [az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Miután a Cloud Shell van szükség, győződjön meg arról, dolgozik, és az erőforrásnak ugyanabban az előfizetésben. Ennek az ellenőrzéséhez `az account show` és ellenőrizze, hogy a "name" érték megegyezik a fürt előfizetés.

2. A portálon keresse meg az erőforráscsoport, ahol a Log Analytics-munkaterület megtalálható. Kattintson az a Log Analytics-erőforrás (az erőforrás típusa, a Log Analytics lesz). Miután az erőforrás – Áttekintés lapon, kattintson a **speciális beállítások** a beállítások szakaszában a bal oldali menüben.

    ![Log Analytics-Tulajdonságok lap](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. Kattintson a **Windows kiszolgálók** rendelkezésére állnak a Windows-fürtöt, ha és **Linux-kiszolgálók** Linux-fürt létrehozásakor. Ezen a lapon megjelenik a `workspace ID` és `workspace key` (a listában az elsődleges kulcs a portálon). Szüksége lesz a következő lépéshez is.

4. Futtassa a parancsot a fürthöz, az alakzatot a Log Analytics-ügynök telepítése használatával a `vmss extension set` a Cloud shellben API:

    Egy Windows-fürt:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    A Linux-fürt:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Íme egy példa egy Windows-fürtöt ad hozzá a Log Analytics-ügynököket.

    ![Log Analytics az ügynök parancssori felületi parancsot](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. Ez az ügynök sikeresen hozzá a csomópontok kisebb, mint 15 perc vesz igénybe. Ellenőrizheti, hogy az ügynökök használatával lettek hozzáadva a `az vmss extension list` API:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Az ügynök használatával a Resource Manager-sablon hozzáadása

Mintául szolgáló Resource Manager-sablonok üzembe helyezése az Azure Log Analytics-munkaterületet, és adja hozzá az ügynök minden egyes csomópontján érhető el a [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) vagy [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Töltse le, és módosítsa a sablon üzembe helyezése egy fürtöt, amely a legjobban az igényeinek megfelelő.

## <a name="view-performance-counters"></a>Teljesítményszámlálók megtekintése

Most, hogy hozzáadta a Log Analytics-ügynököket, lépjen a átváltunk a Log Analytics-portálon választhat, mely teljesítményszámlálókat szeretné gyűjteni. 

1. Az Azure Portalon nyissa meg az erőforráscsoport, amelyben létrehozta a Service Fabric-elemzés megoldás. Válassza ki **ServiceFabric\<nameOfLog AnalyticsWorkspace\>**.

2. Kattintson a **Log Analytics** elemre.

3. Kattintson a **speciális beállítások**.

4. Kattintson a **adatok**, majd kattintson a **Windows vagy Linux-teljesítményszámlálók**. A beállítással engedélyezi alapértelmezett számlálókat tartalmazó listát, és beállíthatja a gyűjtési időköze túl. Azt is megteheti [további teljesítményszámlálók](service-fabric-diagnostics-event-generation-perf.md) gyűjtéséhez. A kiszolgálócím formátuma hivatkozik ez [cikk](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

5. Kattintson a **mentése**, majd kattintson a **OK**.

6. Zárja be a Speciális beállítások panelt.

7. Kattintson az általános cím alatt **munkaterület összefoglalás**.

8. Csempék formájában grafikon, a megoldások engedélyezve van, például egy Service Fabric az egyes jelenik meg. Kattintson a **Service Fabric** graph, hogy a Service Fabric-elemzés megoldás.

9. Műveleti csatorna és a reliable services eseményei látni fogja a gráfok néhány csempét. A kijelölt számlálók beérkező adatok grafikus ábrázolását Csomópontmetrikák alatt jelenik meg. 

10. Kattintson a tároló metrika grafikon további részletek megtekintéséhez. A hasonlóan a fürthöz kapcsolódó események és a csomópontok, teljesítményoptimalizált számláló neve és a Kusto-lekérdezési nyelv segítségével értékek alapján végezhet szűrést a teljesítményszámláló-adatokat is lekérdezhet.

![Log Analytics teljesítményoptimalizált teljesítményszámláló-lekérdezés](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>További lépések

* Releváns gyűjtése [teljesítményszámlálók](service-fabric-diagnostics-event-generation-perf.md). A Log Analytics-ügynököket gyűjtendő teljesítményszámlálókat konfigurálásához tekintse át a [adatforrások konfigurálása](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Konfigurálja a Log Analytics beállítása [automatizált riasztások](../log-analytics/log-analytics-alerts.md) , ezzel elősegítve az észlelést és a diagnosztikát
* Alternatív megoldásként a teljesítményszámlálók segítségével gyűjtheti [Azure Diagnostics bővítményt, és küldje el azokat az Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-application-insights-sink-to-the-resource-manager-template)
