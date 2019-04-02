---
title: Az alkalmazásteljesítmény miatti riasztások létrehozása az Azure Monitor szolgáltatással tárolókhoz |} A Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre egyéni Azure-riasztások a memória és CPU-felhasználást tárolók az Azure Monitor log-lekérdezések alapján.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: magoedte
ms.openlocfilehash: 5bb0a727adcfb35b5d840a063b6fdb478d150953
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58804824"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Teljesítményproblémák-tárolókhoz az Azure Monitor riasztásainak beállítása
A tárolók figyelők az Azure Monitor a tárolók számítási feladatainak teljesítményét vagy az Azure Container Instances szolgáltatásban üzembe helyezett, vagy az Azure Kubernetes Service (AKS) az üzemeltetett Kubernetes-fürtök felügyelt. 

Ez a cikk ismerteti, hogyan engedélyezheti a riasztások az alábbi helyzetekben:

* Ha a fürt csomópontjai a CPU és memória kihasználtsága meghaladja a meghatározott küszöbértéket.
* Ha egy vezérlő lévő tárolók valamelyik CPU és memória kihasználtsága meghaladja a meghatározott küszöbértéket, mint a korábban megszokott a kapcsolódó erőforrás a beállított korlátnál.
* **NotReady** állapot csomópontjánál száma
* Pod fázis számolja **sikertelen**, **függőben lévő**, **ismeretlen**, **futó**, vagy **sikeres**

Riasztás, ha a CPU vagy a fürt csomópontjai a magas kihasználtság memóriát, vagy hozzon létre metrikariasztás vagy egy metrikamérési riasztási szabályt, a megadott log lekérdezésekkel. Míg a metrikákhoz kapcsolódó riasztások naplóriasztások kisebb késést, egy riasztás biztosít speciális lekérdezés és kifinomultabbak, mint a metrikariasztás. Riasztások a lekérdezések egy dátum és idő a jelenlegi most operátorral történő összehasonlítására, és visszatér egy óra. Az összes-tárolókhoz az Azure Monitor által tárolt dátumok UTC formátumban.

Mielőtt hozzákezdene, ha nem ismeri az Azure monitorban riasztásokat, lásd: [áttekintése a Microsoft Azure-ban riasztások](../platform/alerts-overview.md). Riasztások a log-lekérdezésekkel kapcsolatos további információkért lásd: [Naplóriasztások az Azure Monitor](../platform/alerts-unified-log.md). Metrikákhoz kapcsolódó riasztások kapcsolatos további információkért lásd: [metrikákhoz kapcsolódó riasztások az Azure monitorban](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Erőforrás kihasználtsága naplóbeli keresési lekérdezések
Ebben a szakaszban a lekérdezések által minden riasztási forgatókönyv támogatásához. A lekérdezések szükségesek a 7. lépés a [riasztás létrehozása](#create-alert-rule) szakaszt.  

A következő lekérdezés átlagos CPU-felhasználását számítja ki átlagban tag csomópontok CPU-kihasználtság percenként.  

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuCapacityNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```

A következő lekérdezés tag csomópontok memóriahasználat percenként átlagosan átlagos memória-felhasználását számítja ki.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryCapacityBytes';
let usageCounterName = 'memoryRssBytes';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
// cluster filter would go here if multiple clusters are reporting to the same Log Analytics workspace
| distinct ClusterName, Computer
| join hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime
  | where TimeGenerated >= startDateTime
  | where ObjectName == 'K8SNode'
  | where CounterName == capacityCounterName
  | summarize LimitValue = max(CounterValue) by Computer, CounterName, bin(TimeGenerated, trendBinSize)
  | project Computer, CapacityStartTime = TimeGenerated, CapacityEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer
| join kind=inner hint.strategy=shuffle (
  Perf
  | where TimeGenerated < endDateTime + trendBinSize
  | where TimeGenerated >= startDateTime - trendBinSize
  | where ObjectName == 'K8SNode'
  | where CounterName == usageCounterName
  | project Computer, UsageValue = CounterValue, TimeGenerated
) on Computer
| where TimeGenerated >= CapacityStartTime and TimeGenerated < CapacityEndTime
| project ClusterName, Computer, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize), ClusterName
```
>[!IMPORTANT]
>Az alábbi lekérdezéseket tartalmaz a fürt és a vezérlő nevének - karakterlánc helyőrzőértékeket < saját-fürt-neve > és < saját-tartományvezérlő-neve >. Riasztások beállítása előtt cserélje le a zárójelben adott környezetre jellemző értékekkel. 


A következő lekérdezés kiszámítja az átlagos processzorhasználat-vezérlőben levő összes tároló CPU-felhasználás mintavételezését minden tárolópéldány egy tartományvezérlő, a korlát beállítása a tároló százalékában percenként átlagosan.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'cpuLimitNanoCores';
let usageCounterName = 'cpuUsageNanoCores';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

A következő lekérdezést, memóriahasználata minden vezérlő tárolópéldány egy tároló beállítása a korlát, százalékos percenként átlagosan egy vezérlőt az összes tároló átlagos memória-felhasználását számítja ki.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let capacityCounterName = 'memoryLimitBytes';
let usageCounterName = 'memoryRssBytes';
let clusterName = '<your-cluster-name>';
let controllerName = '<your-controller-name>';
KubePodInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where ClusterName == clusterName
| where ControllerName == controllerName
| extend InstanceName = strcat(ClusterId, '/', ContainerName),
         ContainerName = strcat(controllerName, '/', tostring(split(ContainerName, '/')[1]))
| distinct Computer, InstanceName, ContainerName
| join hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ObjectName == 'K8SContainer'
    | where CounterName == capacityCounterName
    | summarize LimitValue = max(CounterValue) by Computer, InstanceName, bin(TimeGenerated, trendBinSize)
    | project Computer, InstanceName, LimitStartTime = TimeGenerated, LimitEndTime = TimeGenerated + trendBinSize, LimitValue
) on Computer, InstanceName
| join kind=inner hint.strategy=shuffle (
    Perf
    | where TimeGenerated < endDateTime + trendBinSize
    | where TimeGenerated >= startDateTime - trendBinSize
    | where ObjectName == 'K8SContainer'
    | where CounterName == usageCounterName
    | project Computer, InstanceName, UsageValue = CounterValue, TimeGenerated
) on Computer, InstanceName
| where TimeGenerated >= LimitStartTime and TimeGenerated < LimitEndTime
| project Computer, ContainerName, TimeGenerated, UsagePercent = UsageValue * 100.0 / LimitValue
| summarize AggregatedValue = avg(UsagePercent) by bin(TimeGenerated, trendBinSize) , ContainerName
```

A következő lekérdezés visszaadja az minden csomópontján és száma, az állapota **készen** és **NotReady**.

```kusto
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
let clusterName = '<your-cluster-name>';
KubeNodeInventory
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| distinct ClusterName, Computer, TimeGenerated
| summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName, Computer
| join hint.strategy=broadcast kind=inner (
    KubeNodeInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | summarize TotalCount = count(), ReadyCount = sumif(1, Status contains ('Ready'))
                by ClusterName, Computer,  bin(TimeGenerated, trendBinSize)
    | extend NotReadyCount = TotalCount - ReadyCount
) on ClusterName, Computer, TimeGenerated
| project   TimeGenerated,
            ClusterName,
            Computer,
            ReadyCount = todouble(ReadyCount) / ClusterSnapshotCount,
            NotReadyCount = todouble(NotReadyCount) / ClusterSnapshotCount
| order by ClusterName asc, Computer asc, TimeGenerated desc
```
A következő lekérdezés visszatér a pod fázis száma alapján az összes fázisban - **sikertelen**, **függőben lévő**, **ismeretlen**, **futó**, vagy **Sikeres**.  

```kusto
let endDateTime = now();
    let startDateTime = ago(1h);
    let trendBinSize = 1m;
    let clusterName = '<your-cluster-name>';
    KubePodInventory
    | where TimeGenerated < endDateTime
    | where TimeGenerated >= startDateTime
    | where ClusterName == clusterName
    | distinct ClusterName, TimeGenerated
    | summarize ClusterSnapshotCount = count() by bin(TimeGenerated, trendBinSize), ClusterName
    | join hint.strategy=broadcast (
        KubePodInventory
        | where TimeGenerated < endDateTime
        | where TimeGenerated >= startDateTime
        | distinct ClusterName, Computer, PodUid, TimeGenerated, PodStatus
        | summarize TotalCount = count(),
                    PendingCount = sumif(1, PodStatus =~ 'Pending'),
                    RunningCount = sumif(1, PodStatus =~ 'Running'),
                    SucceededCount = sumif(1, PodStatus =~ 'Succeeded'),
                    FailedCount = sumif(1, PodStatus =~ 'Failed')
                 by ClusterName, bin(TimeGenerated, trendBinSize)
    ) on ClusterName, TimeGenerated
    | extend UnknownCount = TotalCount - PendingCount - RunningCount - SucceededCount - FailedCount
    | project TimeGenerated,
              TotalCount = todouble(TotalCount) / ClusterSnapshotCount,
              PendingCount = todouble(PendingCount) / ClusterSnapshotCount,
              RunningCount = todouble(RunningCount) / ClusterSnapshotCount,
              SucceededCount = todouble(SucceededCount) / ClusterSnapshotCount,
              FailedCount = todouble(FailedCount) / ClusterSnapshotCount,
              UnknownCount = todouble(UnknownCount) / ClusterSnapshotCount
| summarize AggregatedValue = avg(PendingCount) by bin(TimeGenerated, trendBinSize)
```

>[!NOTE]
>Például bizonyos pod fázisok értesítenek **függőben lévő**, **sikertelen**, vagy **ismeretlen**, a lekérdezés utolsó sora módosítani kell. Például a riasztás *FailedCount* `| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`.  

## <a name="create-alert-rule"></a>Riasztási szabály létrehozása
A következő lépésekkel hozhat létre egy Log riasztási az Azure Monitor használatával a korábban megadott log search szabályok egyikének.  

>[!NOTE]
>Az alábbi eljárás végrehajtásához leírtak szerint válthat az új naplófájl riasztások API [váltson API szabályozó Naplóriasztásokra vonatkozó](../platform/alerts-log-api-switch.md) tároló-erőforrások kihasználtságát riasztási szabály létrehozásakor. 
>

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **figyelő** a bal oldali ablaktáblán, az Azure Portalon. Alatt a **Insights** szakaszban jelölje be **tárolók**.    
3. Az a **figyelt fürtök** lapra, válassza ki a fürt a fürt nevére kattintva a listából.
4. A bal oldali ablaktábla a **figyelés** szakaszban jelölje be **naplók** nyissa meg az Azure Monitor naplózza a oldal, amely írható, és hajtsa végre az Azure Log Analytics-lekérdezéseket.
5. Az a **naplók** kattintson **+ Új riasztási szabály**.
6. Alatt a **feltétel** területén kattintson az előre definiált egyéni napló feltétel teljesülésekor **minden alkalommal, amikor az egyéni log Search <logic undefined>** . A **egyéni naplóbeli keresés** jeltípus automatikusan ki van jelölve a számunkra, mert a Microsoft által kezdeményezett, közvetlenül az Azure Monitor-naplók oldalán a riasztási szabály létrehozása.  
7. Illessze be az egyik a [lekérdezések](#resource-utilization-log-search-queries) korábban megadott a **keresési lekérdezés** mező. 

8. Konfigurálja a riasztást az alábbi információkkal:

    a. A **Riasztás alapja** legördülő menüből válassza a **Metrikamérés** elemet. A metrikamérés egy riasztást hoz létre a lekérdezés minden egyes objektumához, amelynek értéke meghaladja a megadott küszöbértéket.  
    b. Az a **feltétel**, jelölje be **nagyobb, mint** , és adja meg **75** egy kezdeti alaptervként **küszöbérték** vagy adjon meg egy értéket, amely megfelel a feltételek.  
    c. A **eseményindító riasztás alapja** szakaszban jelölje be **egymás utáni incidensek** a legördülő listából válassza **nagyobb, mint** adjon meg egy értéket a **2**.  
    d. Tároló CPU vagy a memóriahasználat, a riasztás konfigurálásakor a **az összesített** kiválasztása **ContainerName** a legördülő listából.  
    e. A **alapján Evaluated** szakaszában, módosíthatja a **időszak** érték 60 perc. A szabály lesz 5 percenként, és az aktuális időpont az elmúlt órában belül létrehozott rekordot ad vissza. Ha az időtartamot szélesebb időközre állítja, figyelembe veheti az esetleges adatkésést, és biztosíthatja, hogy a lekérdezés adatokat ad vissza, így nem kap téves negatív választ, ha a figyelmeztetés soha nem aktiválódik. 

9. Kattintson a **Kész** gombra a riasztási szabály létrehozásának befejezéséhez.
10. Adjon meg egy nevet a riasztásból a **riasztásiszabály-névnek** mező. Adjon meg egy **leírás** részletező a riasztás-adatait, és válasszon egy megfelelő súlyossági lehetőségeket.
11. Ha a létrehozásakor azonnal aktiválni kívánja a riasztási szabályt, fogadja el a **Szabály engedélyezése a létrehozásakor** alapértelmezett értékét.
12. Az utolsó lépést, válasszon egy meglévő, vagy hozzon létre egy új **műveletcsoport**, amely biztosítja, hogy ugyanazokat a műveleteket kerül minden alkalommal, amikor egy riasztás akkor aktiválódik, és az egyes szabályokhoz határoz meg használható. Konfigurálása alapján hogyan lehet az informatikai RÉSZLEG kezeli az incidensek fejlesztési és üzemeltetési műveleteket. 
13. Kattintson a **Riasztási szabály létrehozás** gombra a riasztási szabály létrehozásának befejezéséhez. Azonnal el fog indulni.

## <a name="next-steps"></a>További lépések

* Tekintse át a [lekérdezés példák jelentkezzen](container-insights-analyze.md#search-logs-to-analyze-data) ismerje meg az előre definiált lekérdezések és a példákat, kiértékelheti és testre szabható használja az egyéb értesítési forgatókönyvek. 
* Megtudhatja, hogyan kell használni az Azure Monitor és egyéb monitorozhatja az AKS-fürt a folytatáshoz tekintse meg a [megtekintése az Azure Kubernetes Service health](container-insights-analyze.md)
