---
title: Teljesítményével kapcsolatos riasztások létrehozása az Azure Monitor használatával a tárolókhoz |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan-tárolókhoz az Azure Monitor használatával naplólekérdezések memória és CPU-kihasználtság alapján egyéni riasztásokat is létrehozhat.
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
ms.openlocfilehash: ebe2c2b488e3d71597dd24f5504a14dd7ce6671e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59282287"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Teljesítményproblémák-tárolókhoz az Azure Monitor riasztásainak beállítása
Az Azure Monitor for containers szolgáltatásban felügyelt Kubernetes-fürtöket üzemeltetett Azure Kubernetes Service (AKS) vagy az Azure Container Instances üzembe helyezett tárolók számítási feladatainak teljesítményét figyeli.

Ez a cikk ismerteti, hogyan engedélyezheti a riasztások a következő helyzetekben:

* Ha a fürtcsomópontok CPU és memória-felhasználás meghaladja a meghatározott küszöbértéket
* Ha egy vezérlő belül egyetlen tárolója sem CPU és memória-felhasználás meghaladja-e a meghatározott küszöbértéket, szemben a kapcsolódó erőforrás a beállított korlát
* *NotReady* állapot csomópontjánál száma
*  *Nem sikerült*, *függőben lévő*, *ismeretlen*, *futó*, vagy *sikeres* pod-fázis száma

Riasztás magas CPU-vagy memóriahasználat a fürtcsomópontokon, használja a lekérdezéseket, amelyet metrikariasztás vagy egy metrikamérési riasztási létrehozása. Metrikákhoz kapcsolódó riasztások, mint a riasztások alacsonyabb késéssel rendelkeznek. De riasztások speciális lekérdezés és a nagyobb kifinomultabbak. Naplóriasztások lekérdezések használatával összehasonlíthatja az eredményeket egy az aktuális dátuma és ideje a *most* operátor és a folyamatos biztonsági másolatot egy óra. (A tárolók az azure Monitor tárolja az összes dátum egyezményes világidő (UTC) formátumban.)

Ha még nem ismeri az Azure Monitor riasztások, tekintse meg [áttekintése a Microsoft Azure-ban riasztások](../platform/alerts-overview.md) megkezdése előtt. A riasztásokat, amelyek log lekérdezésekkel kapcsolatos további információkért lásd: [Naplóriasztások az Azure Monitor](../platform/alerts-unified-log.md). Metrikákhoz kapcsolódó riasztások kapcsolatos további információkért lásd: [metrikákhoz kapcsolódó riasztások az Azure monitorban](../platform/alerts-metric-overview.md).

## <a name="resource-utilization-log-search-queries"></a>Erőforrás kihasználtsága naplóbeli keresési lekérdezések
Ebben a szakaszban a lekérdezések támogatásához minden egyes riasztási. 7. lépését használhatók a [riasztás létrehozása](#create-an-alert-rule) című szakaszát.

A következő lekérdezést a tag csomópontok CPU-felhasználás mintavételezését percenként átlagosan átlagos processzorhasználat számítja ki.  

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

A következő lekérdezés tag csomópontok memóriahasználat percenként átlagosan átlagos memória felhasználását számítja ki.

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
>A következő lekérdezések használják a helyőrző értékeket \<saját fürt neve > és \<az tartományvezérlő neve->, amelyek a fürt és a tartományvezérlő. Cserélje le őket értékek adott környezetre jellemző riasztások beállításakor.

A következő lekérdezés kiszámítja az átlagos processzorhasználat-vezérlőben levő összes tároló CPU-felhasználás mintavételezését, minden vezérlő tárolópéldány percenként átlagosan. Egy tároló beállítása a korlát, százalékos érték.

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

A következő lekérdezést, memóriahasználata minden vezérlő tárolópéldány percenként átlagosan egy vezérlőt az összes tároló átlagos memória-felhasználását számítja ki. Egy tároló beállítása a korlát, százalékos érték.

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

A következő lekérdezés visszaadja az minden csomópontján és száma, amelyek állapota *készen* és *NotReady*.

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
A következő lekérdezés visszatér a pod fázis száma az összes fázisban alapján: *Nem sikerült*, *függőben lévő*, *ismeretlen*, *futó*, vagy *sikeres*.  

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
>Például bizonyos pod fázisok értesítenek *függőben lévő*, *sikertelen*, vagy *ismeretlen*, módosíthatja a lekérdezés utolsó sora. Például, hogy a riasztás *FailedCount* használja: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

## <a name="create-an-alert-rule"></a>Riasztási szabály létrehozása
Kövesse az alábbi lépéseket egy riasztás létrehozása az Azure monitorban a korábban megadott log search szabályok egyikének használatával.  

>[!NOTE]
>Az alábbi eljárás egy riasztási szabályt létrehozni a tároló erőforrás-használat megköveteli, hogy váltson át egy új log riasztások API leírtak szerint [váltson API szabályozó naplóriasztásokra vonatkozó](../platform/alerts-log-api-switch.md).
>

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza ki **figyelő** a bal oldali panelen. A **Insights**válassza **tárolók**.
3. Az a **figyelt fürtök** lapra, válassza ki a fürt a listából.
4. A csoportban a bal oldali ablaktáblán **figyelés**válassza **naplók** nyissa meg a Azure Monitor naplókat. Ezen a lapon írása, és hajtsa végre az Azure Log Analytics-lekérdezéseket.
5. Az a **naplók** lapon jelölje be **+ Új riasztási szabály**.
6. Az a **feltétel** szakaszban jelölje be a **minden alkalommal, amikor az egyéni log Search \<nem definiált logika >** előre meghatározott egyéni naplófeltétel. A **egyéni naplóbeli keresés** jeltípus automatikusan ki van jelölve, mert a közvetlenül az Azure Monitor-naplók oldalán a riasztási szabály létrehozása folyamatban.  
7. Illessze be az egyik a [lekérdezések](#resource-utilization-log-search-queries) korábban megadott a **keresési lekérdezés** mező.
8. A riasztás a következőképpen konfigurálja:

    1. A **Riasztás alapja** legördülő menüből válassza a **Metrikamérés** elemet. Egy metrikamérési minden objektum esetén riasztást hoz létre a lekérdezést, amely egy értéket a megadott küszöbérték felett van.
    1. A **feltétel**, jelölje be **nagyobb, mint**, és adja meg **75** egy kezdeti alaptervként **küszöbérték**. Vagy adjon meg egy másik értéket, amely megfelel a feltételeknek.
    1. Az a **eseményindító riasztás alapja** szakaszban jelölje be **egymás utáni incidensek**. A legördülő listából válassza ki a **nagyobb**, és adja meg **2**.
    1. Tároló CPU vagy a memóriahasználat, a riasztás konfigurálásához a **az összesített**válassza **ContainerName**. 
    1. Az a **alapján Evaluated** szakaszában a **időszak** értéket a következőre **60 perc**. A szabály lesz 5 percenként futtassa, és az aktuális időpont az elmúlt órában belül létrehozott rekordot ad vissza. A lehetséges adatkésleltetést széles ablak fiókok beállítást az adott időszakban. Emellett biztosítja, hogy a lekérdezés visszaadja az adatot, hogy elkerülje a téves negatív, amelyben a riasztás soha nem aktiválódik.

9. Válassza ki **kész** befejezéséhez a riasztási szabályt.
10. Írjon be egy nevet a **riasztásiszabály-névnek** mező. Adjon meg egy **leírás** , amely biztosítja, hogy a riasztás részletes adatait. És válasszon egy megfelelő súlyossági szint a megadott beállításokat.
11. A riasztási szabály azonnal aktiválni, fogadja el az alapértelmezett érték a **engedélyezése a szabály létrehozásakor**.
12. Válasszon egy meglévő **műveletcsoport** , vagy hozzon létre egy új csoportot. Ez a lépés biztosítja, hogy ugyanazokat a műveleteket minden alkalommal, egy riasztás akkor aktiválódik, amikor megnyílik. Konfigurálása alapján hogyan lehet az informatikai üzemeltetési csapat a DevOps kezeli az incidensek.
13. Válassza ki **riasztási szabály létrehozása** befejezéséhez a riasztási szabályt. Azonnal el fog indulni.

## <a name="next-steps"></a>További lépések

* Nézet [lekérdezés példák jelentkezzen](container-insights-analyze.md#search-logs-to-analyze-data) előre definiált lekérdezések és példák kiértékelése és az egyéb értesítési forgatókönyvek testreszabása ismerteti.
* Az Azure Monitor és egyéb aspektusait az AKS-fürt figyelése kapcsolatos további tudnivalókért lásd: [megtekintése az Azure Kubernetes Service health](container-insights-analyze.md).
