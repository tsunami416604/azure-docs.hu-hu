---
title: Naplók lekérdezése Azure Monitorról tárolók számára | Microsoft Docs
description: A tárolók Azure Monitor a metrikákat és a naplózási adatokat gyűjtik, és ez a cikk ismerteti a rekordokat, és példákat tartalmaz a lekérdezésekre.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/12/2019
ms.openlocfilehash: c3a034776b32db57f70ddee960c1cd5fc96b170b
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555411"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Naplók lekérdezése Azure Monitorról tárolók számára

A tárolók Azure Monitor a tároló gazdagépek és tárolók teljesítmény-metrikáit, leltározási adatait és állapotadatok adatait gyűjtik, és a Azure Monitor Log Analytics munkaterületére továbbítják. Az adatok gyűjtése három percenként történik. Ezek az adatAzure Monitor [lekérdezésekhez](../../azure-monitor/log-query/log-query-overview.md) érhetők el. Ezeket az információkat olyan forgatókönyvekre alkalmazhatja, amelyek tartalmazzák az áttelepítés megtervezését, a kapacitás elemzését, a felderítést és az igény szerinti teljesítménnyel kapcsolatos hibaelhárítást.

## <a name="container-records"></a>Tároló rekordjai

A következő táblázatban láthatók például a tárolók Azure Monitor által összegyűjtött rekordok, valamint a naplók keresési eredményei között megjelenő adattípusok:

| Data type | Adattípus a naplóbeli keresésben | Mezők |
| --- | --- | --- |
| Gazdagépek és tárolók teljesítménye | `Perf` | Számítógép, ObjectName, CounterName &#40;%-os processzoridő, lemez olvasás MB, lemez írása MB, memóriahasználat MB, hálózati fogadási bájtok, hálózati küldési bájtok, processzor kihasználtsága (mp), hálózat&#41;, kártyabirtokos számlájának megterhelését, TimeGenerated, CounterPath, SourceSystem |
| Tároló leltározása | `ContainerInventory` | TimeGenerated, számítógép, tároló neve, ContainerHostname, rendszerkép, ImageTag, ContainerState, ExitCode, EnvironmentVar, parancs, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Tároló naplója | `ContainerLog` | TimeGenerated, számítógép, rendszerkép azonosítója, tároló neve, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Tároló-csomópont leltározása | `ContainerNodeInventory`| TimeGenerated, számítógép, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Hüvelyek leltározása egy Kubernetes-fürtben | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, szolgáltatásnév, ControllerKind, ControllerName, tároló állapota:,  ContainerStatusReason, ContainerID, ContainerName, name, PodLabel, névtér, PodStatus, ClusterName, képe, SourceSystem |
| Kubernetes-fürt csomópontjainak leltára | `KubeNodeInventory` | TimeGenerated, számítógép, ClusterName, ClusterId, LastTransitionTimeReady, címkék, állapot, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes események | `KubeEvents` | TimeGenerated, számítógép, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, üzenet, SourceSystem | 
| Szolgáltatások a Kubernetes-fürtben | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| A Kubernetes-fürt csomópontjainak teljesítmény-mérőszámai | A &#124; Perf, ahol a ObjectName = = "K8SNode" | Számítógép, ObjectName, CounterName &#40;CpuAllocatableBytes, MemoryAllocatableBytes, CpuCapacityNanoCores, MemoryCapacityBytes, MemoryRssBytes, CpuUsageNanoCores, MemoryWorkingsetBytes, restartTimeEpoch&#41;, kártyabirtokos számlájának megterhelését, TimeGenerated, CounterPath, SourceSystem | 
| A Kubernetes-fürthöz tartozó tárolók teljesítmény-mérőszámai | A &#124; Perf, ahol a ObjectName = = "K8SContainer" | CounterName &#40; CpuRequestNanoCores, MemoryRequestBytes, CpuLimitNanoCores, MemoryWorkingSetBytes, RestartTimeEpoch, CpuUsageNanoCores, memoryRssBytes&#41;, kártyabirtokos számlájának megterhelését, TimeGenerated, CounterPath, SourceSystem | 
| Egyéni metrikák |`InsightsMetrics` | Számítógép, név, névtér, forrás, SourceSystem, címkék<sup>1</sup>, TimeGenerated, típus, VA, _ResourceId | 

<sup>1</sup> a *címkék* tulajdonság a megfelelő metrika [több dimenzióját](../platform/data-platform-metrics.md#multi-dimensional-metrics) jelöli. A `InsightsMetrics` táblában gyűjtött és tárolt metrikákkal és a rekordok tulajdonságainak leírásával kapcsolatos további információkért lásd: [InsightsMetrics – áttekintés](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

>[!NOTE]
>A Prometheus támogatása jelenleg a nyilvános előzetes verzióban érhető el.
>

## <a name="search-logs-to-analyze-data"></a>Naplók keresése az adatelemzéshez

Azure Monitor naplók segítségével megkeresheti a trendeket, diagnosztizálhatja a szűk keresztmetszeteket, az előrejelzéseket, vagy korrelálhat olyan adatokkal, amelyek segítségével meghatározhatja, hogy az aktuális fürtkonfiguráció optimális teljesítményű-e. Az előre definiált naplók segítségével azonnal megkezdheti a használatát, vagy testre szabhatja az adatokat a kívánt módon.

A munkaterületen lévő adatok interaktív elemzését az előnézet ablaktábla **Kubernetes-Eseménynaplók megtekintése** vagy a **tároló-naplók megtekintése** lehetőség kiválasztásával végezheti el. A **naplóbeli keresés** lap a Azure Portal lap jobb oldalán jelenik meg.

![Adatok elemzése a Log Analyticsben](./media/container-insights-analyze/container-health-log-search-example.png)   

A tároló a munkaterületre továbbított kimenetet naplózza STDOUT és STDERR. Mivel Azure Monitor figyeli az Azure által felügyelt Kubernetes (ak), a Kube-System szolgáltatást a rendszer a nagy mennyiségű generált adatok miatt ma nem gyűjti. 

### <a name="example-log-search-queries"></a>Példa naplóbeli keresési lekérdezésekre

Gyakran hasznos olyan lekérdezéseket létrehozni, amelyek egy példával vagy kettővel kezdődnek, majd az igényeinek megfelelően módosítják őket. A fejlettebb lekérdezések létrehozásához a következő példa-lekérdezésekkel lehet kísérletezni:

| Lekérdezés | Leírás | 
|-------|-------------|
| ContainerInventory<br> &#124;Project számítógép, név, rendszerkép, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime<br> &#124;renderelési táblázat | Egy tároló életciklus-információinak listázása| 
| KubeEvents_CL<br> &#124;hol nem (IsEmpty (Namespace_s))<br> &#124;Rendezés TimeGenerated csökkenő sorrendben<br> &#124;renderelési táblázat | Kubernetes események|
| ContainerImageInventory<br> &#124;AggregatedValue = count () összefoglalása rendszerkép, ImageTag, futás szerint | Rendszerkép leltározása | 
| **Válassza ki a diagram megjelenítési beállítását**:<br> Perf<br> &#124;ahol a ObjectName = = "K8SContainer" és a CounterName = = " &#124; cpuUsageNanoCores" összegzi a AvgCPUUsageNanoCores = AVG (kártyabirtokos számlájának megterhelését) by bin (TimeGenerated, 30m), példánynév | Tároló PROCESSZORa | 
| **Válassza ki a diagram megjelenítési beállítását**:<br> Perf<br> &#124;ahol a ObjectName = = "K8SContainer" és a CounterName = = " &#124; memoryRssBytes" összegzi a AvgUsedRssMemoryBytes = AVG (kártyabirtokos számlájának megterhelését) by bin (TimeGenerated, 30m), példánynév | Tárolómemória |
| InsightsMetrics<br> &#124;where Name = = "requests_count"<br> &#124;összefoglalás val = any (val) by TimeGenerated = bin (TimeGenerated, 1M)<br> &#124;Rendezés TimeGenerated ASC szerint<br> &#124;projekt RequestsPerMinute = val-Prev (val), TimeGenerated <br> &#124;renderelési barchart  | Percenkénti kérések egyéni metrikákkal |

A következő példa egy Prometheus-metrikák lekérdezése. A gyűjtött mérőszámok számítanak, és annak meghatározásához, hogy hány hiba történt egy adott időszakon belül, ki kell vonni a darabszámot. Az adatkészletet a *partitionKey*particionálja, ami azt jelenti, hogy a *név*, az *állomásnév*és a *OperationType*minden egyedi halmaza egy olyan allekérdezést futtat, amely a naplókat *TimeGenerated*szerint rendeli el, amely lehetővé teszi a a ráta megállapításához keresse meg az előző *TimeGenerated* és a rögzített darabszámot.

```
let data = InsightsMetrics 
| where Namespace contains 'prometheus' 
| where Name == 'kubelet_docker_operations' or Name == 'kubelet_docker_operations_errors'    
| extend Tags = todynamic(Tags) 
| extend OperationType = tostring(Tags['operation_type']), HostName = tostring(Tags.hostName) 
| extend partitionKey = strcat(HostName, '/' , Name, '/', OperationType) 
| partition by partitionKey ( 
    order by TimeGenerated asc 
    | extend PrevVal = prev(Val, 1), PrevTimeGenerated = prev(TimeGenerated, 1) 
    | extend Rate = iif(TimeGenerated == PrevTimeGenerated, 0.0, Val - PrevVal) 
    | where isnull(Rate) == false 
) 
| project TimeGenerated, Name, HostName, OperationType, Rate; 
let operationData = data 
| where Name == 'kubelet_docker_operations' 
| project-rename OperationCount = Rate; 
let errorData = data 
| where Name == 'kubelet_docker_operations_errors' 
| project-rename ErrorCount = Rate; 
operationData 
| join kind = inner ( errorData ) on TimeGenerated, HostName, OperationType 
| project-away TimeGenerated1, Name1, HostName1, OperationType1 
| extend SuccessPercentage = iif(OperationCount == 0, 1.0, 1 - (ErrorCount / OperationCount))
```

A kimenet az alábbihoz hasonló eredményeket fog megjeleníteni:

![Adatfeldolgozási kötet lekérdezési eredményeinek naplózása](./media/container-insights-log-search/log-query-example-prometheus-metrics.png)

## <a name="next-steps"></a>Következő lépések

A tárolók Azure Monitor nem tartalmaz előre meghatározott riasztásokat. Tekintse át a [teljesítménnyel kapcsolatos riasztások létrehozása a Azure monitor for containers](container-insights-alerts.md) szolgáltatással című témakört, amelyből megtudhatja, hogyan hozhat létre ajánlott riasztásokat magas CPU-és memóriahasználat esetén a DevOps vagy működési folyamatok és eljárások támogatásához 
