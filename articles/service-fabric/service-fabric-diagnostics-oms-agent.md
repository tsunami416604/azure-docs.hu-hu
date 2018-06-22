---
title: Az Azure Service Fabric - teljesítményfigyelés a Log Analyticshez |} Microsoft Docs
description: Megtudhatja, hogyan állíthatja be a Analytics ügynök tárolók és a teljesítményszámlálók az Azure Service Fabric-fürtök a figyelésre.
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
ms.openlocfilehash: a31fe62f2e81a0e39e4c314fc736e91e72bf7517
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301553"
---
# <a name="performance-monitoring-with-log-analytics"></a>A Naplóelemzési teljesítményfigyelés

Ez a cikk adható hozzá a Analytics ügynök, mert a virtuálisgép-méretezési virtuáliskapcsoló-kiterjesztés beállítása a fürthöz, és csatlakoztassa a meglévő Azure Log Analytics-munkaterület lépéseket ismerteti. Ez lehetővé teszi a tárolók, alkalmazások és -figyelő gyűjtését diagnosztikai adatokat. Bővítményként való hozzáadásával a virtuálisgép-méretezési készlet erőforrás, Azure Resource Manager biztosítja, hogy megkapja-e telepítve minden csomóponton, még ha méretezés a fürt.

> [!NOTE]
> Ez a cikk feltételezi, hogy rendelkezik-e az Azure Log Analytics-munkaterülethez állította be. Ha nem így tesz, látogasson el [Azure Naplóelemzés beállítása](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Az Azure parancssori felület használatával ügynök-bővítmény hozzáadása

A legjobb módszer a Analytics ügynök hozzáadása a fürthöz a virtuálisgép-méretezési keresztül értéke elérhető API-kat az Azure parancssori felület segítségével. Ha az Azure parancssori felület még nem rendelkezik, látogasson el az Azure portálon, és nyissa meg a [felhő rendszerhéj](../cloud-shell/overview.md) példányt, vagy [Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Miután a felhő rendszerhéj van szükség, ellenőrizze, hogy dolgozik, az erőforrás ugyanahhoz az előfizetéshez. Ellenőrizze a `az account show` , és győződjön meg arról, hogy a "name" érték megegyezik a fürt előfizetés.

2. A portálon lépjen az erőforráscsoporthoz, ahol a Naplóelemzési munkaterület. Kattintson a Log Analytics-erőforrás (az erőforrás típusa lehet Naplóelemzési) be. Ha az erőforrás áttekintése oldalon, kattintson a **speciális beállítások** a bal oldali menü Beállítások szakaszban.

    ![Napló Analytics Tulajdonságok lap](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. Kattintson a **Windows kiszolgálók** Ha meg vannak állandó Windows fürt, és **Linux kiszolgálók** egy Linux-fürt létrehozásakor. Ezen a lapon megjelenik a `workspace ID` és `workspace key` (a listában az elsődleges kulcs a portálon). Szüksége lesz a következő lépéshez is.

4. Futtassa a parancsot a fürt települ a Log Analytics-ügynök használatával a `vmss extension set` a felhő rendszerhéj API:

    A Windows-fürt:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    A Linux-fürt:

    ```sh
    az vmss extension set --name Log AnalyticsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Íme egy példa a felvenni kívánt Windows-fürt Analytics ügynök.

    ![Elemzés ügynök cli parancs naplózása](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. Ekkor az ügynök sikeresen hozzáadása a csomópontok kisebb, mint 15 perc. Ellenőrizheti, hogy az ügynökök használatával vannak adva a `az vmss extension list` API:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Az ügynök keresztül a Resource Manager-sablon hozzáadása

A minta Resource Manager-sablonok, amelyek központi telepítése egy Azure Log Analytics-munkaterület, és adja hozzá a az ügynök a csomópontok mindegyikének érhető el [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) vagy [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Töltse le, és módosítsa a sablon az igényeinek leginkább megfelelő a fürtök telepítése.

## <a name="view-performance-counters"></a>Nézet teljesítményszámlálói

Most, hogy a Naplóelemzési ügynök head hozzáadta a kiválaszthatja, melyik teljesítményszámlálóinak Log Analytics-portálról való szeretne gyűjteni. 

1. Az Azure-portálon lépjen az erőforráscsoporthoz, amelyben létrehozta a Service Fabric elemzési megoldások. Válassza ki **ServiceFabric\<nameOfLog AnalyticsWorkspace\>**.

2. Kattintson a **Log Analytics** elemre.

3. Kattintson a **speciális beállítások**.

4. Kattintson a **adatok**, majd kattintson a **Windows vagy Linux-teljesítményszámlálók**. Dönthet úgy, hogy alapértelmezett számlálók listája, és a gyűjtemény intervallumát túl állíthatja be. Azt is megteheti [további teljesítményszámlálók](service-fabric-diagnostics-event-generation-perf.md) gyűjtéséhez. A megfelelő formátumú a hivatkozott [cikk](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

5. Kattintson a **mentése**, majd kattintson a **OK**.

6. Zárja be a Speciális beállítások panelen.

7. Általános fejléc alatt kattintson **áttekintése**.

8. Az egyes a megoldásairól engedélyezve van, egy a Service Fabric csempék egy grafikonon formájában jelenik meg. Kattintson a **Service Fabric** diagramot úgy, hogy továbbra is a Service Fabric elemzési megoldások.

9. Néhány csempe található diagramokat, amelyik a műveleti csatornát és megbízható szolgáltatások események jelenik meg. A kijelölt számlálók áramló adatokat grafikus ábrázolása metrikák csomópont alatt jelenik meg. 

10. Kattintson a tároló metrika grafikon további részletek megtekintéséhez. A teljesítményszámláló-adatok hasonlóan a fürthöz kapcsolódó események és a csomópontok, telj számláló neve, valamint a Kusto lekérdezési nyelv értékek szűrő is lekérheti.

![Napló Analytics telj számláló lekérdezés](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>További lépések

* Megfelelő gyűjtése [teljesítményszámlálók](service-fabric-diagnostics-event-generation-perf.md). A Naplóelemzési ügynök specifikus teljesítményszámlálók adatainak összegyűjtése konfigurálásához tekintse át a [adatforrások konfigurálása](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Log Analytics beállítása konfigurálása [riasztás automatikus](../log-analytics/log-analytics-alerts.md) észlelésére és diagnosztika
* Másik megoldásként a teljesítményszámlálók segítségével gyűjtheti [Azure Diagnostics bővítményt, és küldje el az Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)
