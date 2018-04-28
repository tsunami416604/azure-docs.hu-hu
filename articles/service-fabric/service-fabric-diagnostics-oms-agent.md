---
title: Az Azure Service Fabric - teljesítményfigyelés a Log Analyticshez |} Microsoft Docs
description: Ismerje meg, hogyan állíthat be az OMS-ügynököt figyelés tárolók és az Azure Service Fabric-fürtök teljesítményszámlálói.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: 6e1c870458f43bcc5d6d40f0e40e2b2a95bee2af
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="performance-monitoring-with-log-analytics"></a>A Naplóelemzési teljesítményfigyelés

Ez a cikk egy virtuálisgép-méretezési virtuáliskapcsoló-kiterjesztés beállítása a fürthöz hozzáadni a Naplóelemzési, más néven az OMS-ügynököt, és csatlakoztassa a meglévő Azure Log Analytics-munkaterület lépéseket ismerteti. Ez lehetővé teszi a tárolók, alkalmazások és -figyelő gyűjtését diagnosztikai adatokat. Bővítményként való hozzáadásával a virtuálisgép-méretezési készlet erőforrás, Azure Resource Manager biztosítja, hogy megkapja-e telepítve minden csomóponton, még ha méretezés a fürt.

> [!NOTE]
> Ez a cikk feltételezi, hogy rendelkezik-e az Azure Log Analytics-munkaterülethez állította be. Ha nem így tesz, látogasson el [Azure Naplóelemzés beállítása](service-fabric-diagnostics-oms-setup.md)

## <a name="add-the-agent-extension-via-azure-cli"></a>Az Azure parancssori felület használatával ügynök-bővítmény hozzáadása

A legjobb módszer az OMS-ügynököt a fürthöz hozzáadni a virtuálisgép-méretezési keresztül értéke elérhető API-kat az Azure parancssori felület segítségével. Ha az Azure parancssori felület még nem rendelkezik, látogasson el az Azure portálon, és nyissa meg a [felhő rendszerhéj](../cloud-shell/overview.md) példányt, vagy [Azure CLI 2.0 telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Miután a felhő rendszerhéj van szükség, ellenőrizze, hogy dolgozik, az erőforrás ugyanahhoz az előfizetéshez. Ellenőrizze a `az account show` , és győződjön meg arról, hogy a "name" érték megegyezik a fürt előfizetés.

2. A portálon lépjen az erőforráscsoporthoz, ahol a Naplóelemzési munkaterület. Kattintson a Log Analytics-erőforrás (az erőforrás típusa lehet Naplóelemzési) be. Ha az erőforrás áttekintése oldalon, kattintson a **speciális beállítások** a bal oldali menü Beállítások szakaszban.

    ![Napló Analytics Tulajdonságok lap](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)
 
3. Kattintson a **Windows kiszolgálók** Ha meg vannak állandó Windows fürt, és **Linux kiszolgálók** egy Linux-fürt létrehozásakor. Ezen a lapon megjelenik a `workspace ID` és `workspace key` (a listában az elsődleges kulcs a portálon). Szüksége lesz a következő lépéshez is.

4. Futtassa a parancsot a fürt települ az OMS-ügynök használatával a `vmss extension set` a felhő rendszerhéj API:

    A Windows-fürt:
    
    ```sh
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    A Linux-fürt:

    ```sh
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<OMSworkspaceId>'}" --protected-settings "{'workspaceKey':'<OMSworkspaceKey>'}"
    ```

    Íme egy példa egy Windows fürtnek való felvétel alatt OMS-ügynököt.

    ![OMS-ügynök cli parancs](media/service-fabric-diagnostics-oms-agent/cli-command.png)
 
5. Ekkor az ügynök sikeresen hozzáadása a csomópontok kisebb, mint 15 perc. Ellenőrizheti, hogy az ügynökök használatával vannak adva a `az vmss extension list` API:

    ```sh
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Az ügynök keresztül a Resource Manager-sablon hozzáadása

A minta Resource Manager-sablonok, amelyek központi telepítése egy Azure Log Analytics-munkaterület, és adja hozzá a az ügynök a csomópontok mindegyikének érhető el [Windows](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows) vagy [Linux](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Linux).

Töltse le, és módosítsa a sablon az igényeinek leginkább megfelelő a fürtök telepítése.

## <a name="view-performance-counters-in-the-log-analytics-portal"></a>A napló Analytics portál nézet teljesítményszámlálói

Most, hogy az OMS-ügynököt, head hozzáadta a kiválaszthatja, melyik teljesítményszámlálóinak Log Analytics-portálról való szeretne gyűjteni. 

1. Az Azure-portálon lépjen az erőforráscsoporthoz, amelyben létrehozta a Service Fabric elemzési megoldások. Válassza ki **ServiceFabric\<nameOfOMSWorkspace\>**  és Ugrás a – áttekintés oldalra. A lap tetején kattintson a hivatkozásra kattintva nyissa meg az OMS-portálon.

2. Miután Ön a portálon, minden a megoldásairól engedélyezve van, egy a Service Fabric megjelenik egy grafikonon formájában csempék. Ide kattintva továbbra is a Service Fabric elemzési megoldások. 

3. Most megjelenik a diagramok néhány csempék működési csatorna és megbízható szolgáltatások események. Kattintson a jobb oldali fogaskerék ikonra kattintva nyissa meg a beállítások lapra.

    ![OMS-beállítások](media/service-fabric-diagnostics-oms-agent/oms-solutions-settings.png)

4. A beállítások lapon kattintson az adatokat, és válassza a Windows vagy Linux-teljesítményszámlálókat. Dönthet úgy, hogy megfelelően alapértelmezett listáját, és a gyűjtemény intervallumát túl állíthatja be. Azt is megteheti [további teljesítményszámlálók](service-fabric-diagnostics-event-generation-perf.md) gyűjtéséhez. A megfelelő formátumú a hivatkozott [cikk](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85).aspx).

Amennyiben a számlálók vannak konfigurálva, head biztonsági megoldások lapjára, és hamarosan megjelenik adatok továbbítására a és a a grafikonon megjelenített **csomópont metrikák**. A teljesítményszámláló-adatok hasonlóan a fürthöz kapcsolódó események és a csomópontok, telj számláló neve, valamint a Kusto lekérdezési nyelv értékek szűrő is lekérheti. 

![OMS telj számláló lekérdezés](media/service-fabric-diagnostics-oms-agent/oms-perf-counter-query.png)

## <a name="next-steps"></a>További lépések

* Megfelelő gyűjtése [teljesítményszámlálók](service-fabric-diagnostics-event-generation-perf.md). Az OMS-ügynököt specifikus teljesítményszámlálók adatainak összegyűjtése konfigurálásához tekintse át a [adatforrások konfigurálása](../log-analytics/log-analytics-data-sources.md#configuring-data-sources).
* Log Analytics beállítása konfigurálása [riasztás automatikus](../log-analytics/log-analytics-alerts.md) észlelésére és diagnosztika
* Másik megoldásként a teljesítményszámlálók segítségével gyűjtheti [Azure Diagnostics bővítményt, és küldje el az Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md#add-the-ai-sink-to-the-resource-manager-template)