---
title: Naplók lekérdezése Azure Monitorról tárolók számára | Microsoft Docs
description: A tárolók Azure Monitor a metrikákat és a naplózási adatokat gyűjtik, és ez a cikk ismerteti a rekordokat, és példákat tartalmaz a lekérdezésekre.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: ae8dd4cccb6795faa02e6705404644f6ccc24864
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71948052"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Naplók lekérdezése Azure Monitorról tárolók számára

A tárolók Azure Monitor a tároló gazdagépek és tárolók teljesítmény-metrikáit, leltározási adatait és állapotadatok adatait gyűjtik, és a Azure Monitor Log Analytics munkaterületére továbbítják. Az adatgyűjtés percen át 3 percenként történik. Ezek az adatAzure Monitor [lekérdezésekhez](../../azure-monitor/log-query/log-query-overview.md) érhetők el. Ezeket az információkat olyan forgatókönyvekre alkalmazhatja, amelyek tartalmazzák az áttelepítés megtervezését, a kapacitás elemzését, a felderítést és az igény szerinti teljesítménnyel kapcsolatos hibaelhárítást.

## <a name="container-records"></a>Tárolórekordok

Az alábbi táblázatban példák a tárolók és az adattípusok, amely a napló keresési eredmények között megjelenik az Azure Monitor által gyűjtött rekordok jelennek meg:

| Adattípus | Naplókeresési adatok típusa | Mezők |
| --- | --- | --- |
| Gazdagépek és-tárolók teljesítménye | `Perf` | Számítógép, ObjectName, CounterName &#40;processzoridő, a lemez beolvassa a MB, lemezre ír MB memória kihasználtsága (MB), hálózati fogadott bájtok, hálózati küldése memória, processzor kihasználtsága (mp), hálózati&#41;, Avg, TimeGenerated, Számláló_elérési_útja, SourceSystem |
| Tároló leltár | `ContainerInventory` | TimeGenerated, a számítógép, a tároló nevét, ContainerHostname, kép, ImageTag, ContainerState, ExitCode, EnvironmentVar, a parancs, CreatedTime, StartedTime, FinishedTime, SourceSystem, cseréjekor a Tárolóazonosító, ImageID |
| Tároló-napló | `ContainerLog` | TimeGenerated, a számítógép, a lemezkép-azonosító, a Tárolónév esetén LogEntrySource, LogEntry, SourceSystem, cseréjekor a Tárolóazonosító |
| Tárolócsomópont-készlet | `ContainerNodeInventory`| TimeGenerated, számítógép, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kubernetes-fürt podok leltára | `KubePodInventory` | TimeGenerated, Computer, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, szolgáltatásnév, ControllerKind, ControllerName, tároló állapota:,  ContainerStatusReason, ContainerID, ContainerName, name, PodLabel, névtér, PodStatus, ClusterName, képe, SourceSystem |
| Kubernetes-fürt csomópontjai részét leltára | `KubeNodeInventory` | TimeGenerated, számítógép, ClusterName, ClusterId, LastTransitionTimeReady, címkék, állapot, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes-események | `KubeEvents` | TimeGenerated, számítógép, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, üzenet, SourceSystem | 
| Kubernetes-fürtben a szolgáltatások | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| A Kubernetes-fürt csomópontok részei a teljesítmény-mérőszámok | Teljesítményoptimalizált &#124; ahol ObjectName == "K8SNode" | Számítógép, ObjectName, CounterName &#40;CpuAllocatableBytes, MemoryAllocatableBytes, CpuCapacityNanoCores, MemoryCapacityBytes, MemoryRssBytes, CpuUsageNanoCores, MemoryWorkingsetBytes, restartTimeEpoch&#41;, kártyabirtokos számlájának megterhelését, TimeGenerated, CounterPath, SourceSystem | 
| A Kubernetes-fürt része tárolók teljesítménymetrikáit | Teljesítményoptimalizált &#124; ahol ObjectName == "K8SContainer" | CounterName &#40; CpuRequestNanoCores, MemoryRequestBytes, CpuLimitNanoCores, MemoryWorkingSetBytes, RestartTimeEpoch, CpuUsageNanoCores, memoryRssBytes&#41;, kártyabirtokos számlájának megterhelését, TimeGenerated, CounterPath, SourceSystem | 
| Egyéni metrikák |`InsightsMetrics` | Számítógép, név, névtér, forrás, SourceSystem, címkék<sup>1</sup>, TimeGenerated, típus, VA, _ResourceId | 

<sup>1</sup> a *címkék* tulajdonság a megfelelő metrika [több dimenzióját](../platform/data-platform-metrics.md#multi-dimensional-metrics) jelöli. A `InsightsMetrics` táblában gyűjtött és tárolt metrikákkal és a rekordok tulajdonságainak leírásával kapcsolatos további információkért lásd: [InsightsMetrics – áttekintés](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

>[!NOTE]
>A Prometheus támogatása jelenleg a nyilvános előzetes verzióban érhető el.
>

## <a name="search-logs-to-analyze-data"></a>Keresési naplókat az adatelemzéshez

Azure Monitor naplók segítségével megkeresheti a trendeket, diagnosztizálhatja a szűk keresztmetszeteket, az előrejelzéseket, vagy korrelálhat olyan adatokkal, amelyek segítségével meghatározhatja, hogy az aktuális fürtkonfiguráció optimális teljesítményű-e. Előre definiált naplókeresések rögtön használatba, illetve adja vissza a kívánt módon szabhatja testre a biztosított.

Kiválasztásával a munkaterület az adatok interaktív elemzés céljából is végezhet a **nézet Kubernetes eseménynaplók** vagy **tárolónaplók megtekintése** lehetőség az előnézeti ablaktáblában láthatja. A **naplóbeli keresés** ablak jobb oldalán, az Azure portal oldalán, amelyen korábban volt.

![Adatok elemzése a Log Analyticsben](./media/container-insights-analyze/container-health-log-search-example.png)   

A tároló a munkaterületre továbbított kimenetet naplózza STDOUT és STDERR. Azure figyelő által figyelt Azure által felügyelt Kubernetes-(AKS), mert Kube rendszer nem gyűjti jelenleg generált adatok nagy mennyisége miatt. 

### <a name="example-log-search-queries"></a>Példa naplóbeli keresési lekérdezések

Gyakran hasznos hozhatók létre olyan lekérdezések, amelyek például vagy a kettő, és módosítsa őket az igényeinek. Annak érdekében, hogy speciális lekérdezések felépítését, kísérletezhet, és az alábbi mintalekérdezések:

| Lekérdezés | Leírás | 
|-------|-------------|
| ContainerInventory<br> &#124;Számítógép, név, kép, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime projekt<br> &#124;táblázat megjelenítése | Összes egy tároló tárolóéletciklus-adat listázása| 
| KubeEvents_CL<br> &#124;ahol not(isempty(Namespace_s))<br> &#124;Rendezés a TimeGenerated desc<br> &#124;táblázat megjelenítése | Kubernetes-események|
| ContainerImageInventory<br> &#124;summarize AggregatedValue futó = count() by ImageTag, kép | Rendszerképek leltára | 
| **Válassza ki a megjelenítendő lehetőség**:<br> Perf<br> &#124;ahol ObjectName == "K8SContainer" és a CounterName == "cpuUsageNanoCores" &#124; AvgCPUUsageNanoCores összefoglalója avg(CounterValue) a bin (TimeGenerated, 30 millió), InstanceName = | Tároló CPU | 
| **Válassza ki a megjelenítendő lehetőség**:<br> Perf<br> &#124;ahol ObjectName == "K8SContainer" és a CounterName == "memoryRssBytes" &#124; AvgUsedRssMemoryBytes összefoglalója avg(CounterValue) a bin (TimeGenerated, 30 millió), InstanceName = | Tároló memória |
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

## <a name="next-steps"></a>További lépések

A tárolók Azure Monitor nem tartalmaz előre meghatározott riasztásokat. Tekintse át a [teljesítménnyel kapcsolatos riasztások létrehozása a Azure monitor for containers](container-insights-alerts.md) szolgáltatással című témakört, amelyből megtudhatja, hogyan hozhat létre ajánlott riasztásokat magas CPU-és memóriahasználat esetén a DevOps vagy működési folyamatok és eljárások támogatásához 
