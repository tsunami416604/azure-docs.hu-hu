---
title: Teljesítményriasztások létrehozása az Azure Monitorhoz tárolókhoz | Microsoft dokumentumok
description: Ez a cikk ismerteti, hogyan hozhat létre egyéni riasztások naplólekérdezések memória és a CPU-használat az Azure Monitor tárolók.
ms.topic: conceptual
ms.date: 01/07/2020
ms.openlocfilehash: 5d73f4399d10683597fb2a2e8a3a2ab4ba0d1165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75730925"
---
# <a name="how-to-set-up-alerts-for-performance-problems-in-azure-monitor-for-containers"></a>Teljesítményproblémákkal kapcsolatos riasztások beállítása a tárolókhoz készült Azure Monitorban

Az Azure Monitor tárolók figyeli az Azure Container-példányok vagy az Azure Kubernetes-szolgáltatás (AKS) üzemeltetett felügyelt Kubernetes-fürtök üzembe helyezett tárolószámítási feladatok teljesítményét.

Ez a cikk azt ismerteti, hogyan engedélyezze a riasztásokat a következő helyzetekben:

- Ha a processzor- vagy memóriahasználat a fürtcsomópontokon meghaladja a küszöbértéket
- Ha a processzor- vagy memóriahasználat a vezérlő n belüli tárolókon meghaladja a küszöbértéket a megfelelő erőforráson beállított korláthoz képest.
- *NotReady* állapotcsomópont-számlálók
- *Sikertelen*, *Függőben*, *Ismeretlen*, *Futó*, vagy *Sikeres* pod-fázisok száma
- Ha a fürtcsomópontokon a szabad lemezterület túllépi a küszöbértéket 

A magas processzor- vagy memóriakihasználtság, illetve a fürtcsomópontokon kevés szabad lemezterület riasztásához használja a megadott lekérdezéseket metrikariasztás vagy metrikamérési riasztás létrehozásához. Metrikariasztások kisebb késésű, mint a naplóriasztások. A naplóriasztások azonban speciális lekérdezést és nagyobb kifinomultságot biztosítanak. A naplóriasztások lekérdezései összehasonlítják a datetime-ot a jelenvel a *most* operátor használatával, és egy órával visszatérve. (Az Azure Monitor tárolók tárolja az összes dátumot koordinált világidő (UTC) formátumban.)

Ha nem ismeri az Azure Monitor-riasztásokat, olvassa [el a Riasztások áttekintése a Microsoft Azure-ban](../platform/alerts-overview.md) indítás előtt című témakört. A naplólekérdezéseket használó riasztásokról a [Naplóriasztások az Azure Monitorban található naplózási témakörben](../platform/alerts-unified-log.md)olvashat bővebben. A metrikariasztásokról további információkért tekintse meg [a Metrikariasztások az Azure Monitorban.](../platform/alerts-metric-overview.md)

## <a name="resource-utilization-log-search-queries"></a>Erőforrás-kihasználtsági napló keresési lekérdezései

Ebben a szakaszban a lekérdezések támogatják az egyes riasztási forgatókönyvek. A cikk [létrehozási riasztási](#create-an-alert-rule) szakaszának 7.

A következő lekérdezés kiszámítja az átlagos CPU-kihasználtság, mint a tag csomópontok CPU-kihasználtsága percenként átlagosan.  

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

A következő lekérdezés kiszámítja az átlagos memóriakihasználtságot a tagcsomópontok memóriakihasználtságának percenkénti átlagaként.

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
>A következő lekérdezések a \<> \<fürt és a vezérlő nevét> helyőrző értékeket használják. A riasztások beállításakor cserélje le őket a környezetére jellemző értékekre.

A következő lekérdezés kiszámítja az átlagos CPU-kihasználtsága az összes tárolók egy vezérlőben, mint egy átlagos CPU-kihasználtsága minden tárolópéldány egy vezérlő percenként. A mérték a tárolóhoz beállított korlát százaléka.

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

A következő lekérdezés kiszámítja a vezérlőösszes tárolójának átlagos memóriakihasználtságát, mint a vezérlő minden tárolópéldányának átlagos memóriakihasználtságát percenként. A mérték a tárolóhoz beállított korlát százaléka.

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

A következő lekérdezés visszaadja az összes olyan csomópontot és számlálót, amelynek *állapota Ready* és *NotReady.*

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
A következő lekérdezés az összes fázis alapján adja vissza a pod fázisok számát: *Sikertelen*, *Függőben,* *Ismeretlen*, *Futás*vagy *Sikeres*.  

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
>Bizonyos podfázisok, például *a Függőben lévő*, *sikertelen*vagy *ismeretlen*fázisok riasztásához módosítsa a lekérdezés utolsó sorát. Például a *FailedCount használatával* kapcsolatos riasztás: <br/>`| summarize AggregatedValue = avg(FailedCount) by bin(TimeGenerated, trendBinSize)`

A következő lekérdezés olyan fürtcsomópontok lemezeit adja vissza, amelyek meghaladják a 90%-os szabad területet. A fürtazonosító lekért e-két lekérdezést először `ClusterId` futtassa a következő lekérdezést, és másolja az értéket a tulajdonságból:

```kusto
InsightsMetrics
| extend Tags = todynamic(Tags)            
| project ClusterId = Tags['container.azm.ms/clusterId']   
| distinct tostring(ClusterId)   
``` 

```kusto
let clusterId = '<cluster-id>';
let endDateTime = now();
let startDateTime = ago(1h);
let trendBinSize = 1m;
InsightsMetrics
| where TimeGenerated < endDateTime
| where TimeGenerated >= startDateTime
| where Origin == 'container.azm.ms/telegraf'            
| where Namespace == 'container.azm.ms/disk'            
| extend Tags = todynamic(Tags)            
| project TimeGenerated, ClusterId = Tags['container.azm.ms/clusterId'], Computer = tostring(Tags.hostName), Device = tostring(Tags.device), Path = tostring(Tags.path), DiskMetricName = Name, DiskMetricValue = Val   
| where ClusterId =~ clusterId       
| where DiskMetricName == 'used_percent'
| summarize AggregatedValue = max(DiskMetricValue) by bin(TimeGenerated, trendBinSize)
| where AggregatedValue >= 90
```

## <a name="create-an-alert-rule"></a>Riasztási szabály létrehozása

Kövesse az alábbi lépéseket, hogy hozzon létre egy naplóriasztást az Azure Monitorban a korábban megadott naplókeresési szabályok egyikének használatával. ARM-sablon használatával történő létrehozásról a [Mintanapló-riasztás létrehozása az Azure Resource Template használatával című témakörben](../platform/alerts-log.md#sample-log-alert-creation-using-azure-resource-template)olvashat.

>[!NOTE]
>Az alábbi eljárás a tárolóerőforrás-kihasználtság riasztási szabályának létrehozásához új naplóriasztások API-ra kell váltania a [Naplóriasztások Hoz való Api-váltása](../platform/alerts-log-api-switch.md)című részben leírtak szerint.
>

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com)
2. Az Azure Portalon keressen és válassza **a Log Analytics-munkaterületeket.**
3. A Log Analytics-munkaterületek listájában válassza ki az Azure Monitort támogató munkaterületet a tárolókhoz. 
4. A bal oldali ablaktáblában válassza **a Naplók** lehetőséget az Azure Monitor naplók lapjának megnyitásához. Ezen a lapon azure log analytics-lekérdezéseket írhat és hajthat végre.
5. A **Naplók** lapon illessze be a korábban megadott [lekérdezések](#resource-utilization-log-search-queries) egyikét a **Keresési lekérdezés** mezőbe, majd válassza a **Futtatás** lehetőséget az eredmények érvényesítéséhez. Ha nem hajtja végre ezt a lépést, az **+Új riasztás** beállítás nem választható ki.
6. A naplóriasztás létrehozásához válassza az **+Új riasztás** lehetőséget.
7. A **Feltétel csoportban** jelölje be a **Ha az egyéni naplókeresés \<logikai>** előre definiált egyéni naplófeltétel. Az **egyéni naplókeresési** jel típusa automatikusan kiválasztásra kerül, mert közvetlenül az Azure Monitor naplók lapról hozunk létre riasztási szabályt.  
8. Illessze be a korábban megadott [lekérdezések](#resource-utilization-log-search-queries) egyikét a **Keresési lekérdezés** mezőbe.
9. Állítsa be a riasztást az alábbiak szerint:

    1. Az **Alapul** legördülő listában válassza a **Metrikus mérés**lehetőséget. A metrikamérés riasztást hoz létre a lekérdezés minden olyan objektumához, amelynek értéke meghaladja a megadott küszöbértéket.
    1. A **Feltétel mezőben**válassza a **Nagyobb, mint**lehetőséget, és írja be a **75** értéket a processzor- és memóriakihasználtsági riasztások kezdeti **alapvonal-küszöbértékeként.** Az alacsony lemezterület-riasztáshoz írja be a **90**értéket. Vagy adjon meg egy másik értéket, amely megfelel a feltételeknek.
    1. Az **Eseményindító riasztás alapján** szakaszban válassza az **Egymást követő incidensek lehetőséget.** A legördülő listában válassza a **Nagyobb mint**lehetőséget, és írja be a **2**értéket.
    1. A tároló processzorának vagy a memóriahasználatnak a riasztáskonfigurálásához **az Összesítés a területen**csoportban válassza a **ContainerName**lehetőséget. A fürtcsomópont alacsony lemezriasztásának konfigurálásához válassza a **ClusterId**lehetőséget.
    1. A **Kiértékelés alapján** szakaszban állítsa az **Időszak** értéket **60 percre.** A szabály 5 percenként fut, és az aktuális időponttól számított utolsó órában létrehozott rekordokat adja vissza. Az időszak beállítása egy széles ablakszámlák lehetséges adatkésés. Azt is biztosítja, hogy a lekérdezés adatokat ad vissza, hogy elkerüljék a hamis negatív, amelyben a riasztás soha nem aktiválódik.

10. A riasztási szabály végrehajtásához válassza a **Kész** lehetőséget.
11. Írjon be egy nevet a **Riasztási szabály neve** mezőbe. Adjon meg egy **leírást,** amely a riasztás részleteit tartalmazza. És válassza ki a megfelelő súlyossági szintet a megadott lehetőségek közül.
12. A riasztási szabály azonnali aktiválásához fogadja el az Alapértelmezett értéket az **Engedélyezés szabály létrehozáskor mezőben.**
13. Jelöljön ki egy meglévő **műveletcsoportot,** vagy hozzon létre egy új csoportot. Ez a lépés biztosítja, hogy ugyanazokat a műveleteket minden alkalommal, amikor egy riasztás aktiválódik. Konfigurálja az informatikai vagy DevOps-műveleti csapat incidensek kezelése alapján.
14. A riasztási szabály végrehajtásához válassza a **Figyelmeztetési szabály létrehozása** lehetőséget. Azonnal el fog indulni.

## <a name="next-steps"></a>További lépések

- Tekintse meg [a naplólekérdezési példákat](container-insights-log-search.md#search-logs-to-analyze-data) az előre definiált lekérdezések és példák megtekintéséhez vagy testreszabásához a fürtök riasztásához, megjelenítéséhez vagy elemzéséhez.
- Ha többet szeretne tudni az Azure Monitorról és a Kubernetes-fürt egyéb aspektusainak figyeléséről, olvassa el a [Kubernetes-fürt teljesítményének megtekintése](container-insights-analyze.md) és [a Kubernetes-fürt állapotának megtekintése](container-insights-health.md)című témakört.
