---
title: Naplók lekérdezése Azure Monitorról tárolók számára | Microsoft Docs
description: A tárolók Azure Monitor a metrikákat és a naplózási adatokat gyűjtik, és ez a cikk ismerteti a rekordokat, és példákat tartalmaz a lekérdezésekre.
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: dcd1656673e549b583de26bca897d0055f389d0a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78362024"
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
| Egyéni metrikák |`InsightsMetrics` | Számítógép, név, névtér, forrás, SourceSystem, címkék<sup>1</sup>, TimeGenerated, típus, Va, _ResourceId | 

<sup>1</sup> a *címkék* tulajdonság a megfelelő metrika [több dimenzióját](../platform/data-platform-metrics.md#multi-dimensional-metrics) jelöli. A `InsightsMetrics` táblában gyűjtött és tárolt metrikákkal és a rekordok tulajdonságainak leírásával kapcsolatos további információkért lásd: [InsightsMetrics – áttekintés](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md).

>[!NOTE]
>A Prometheus támogatása jelenleg a nyilvános előzetes verzióban érhető el.
>

## <a name="search-logs-to-analyze-data"></a>Keresési naplókat az adatelemzéshez

Azure Monitor naplók segítségével megkeresheti a trendeket, diagnosztizálhatja a szűk keresztmetszeteket, az előrejelzéseket, vagy korrelálhat olyan adatokkal, amelyek segítségével meghatározhatja, hogy az aktuális fürtkonfiguráció optimális teljesítményű-e. Előre definiált naplókeresések rögtön használatba, illetve adja vissza a kívánt módon szabhatja testre a biztosított.

A munkaterületen lévő adatok interaktív elemzését a nézet Kubernetes- **eseménynaplók** megtekintése vagy a **tároló-naplók** megtekintése lehetőség kiválasztásával végezheti el az előnézet ablaktáblán, az **elemzések** legördülő listában. A **naplóbeli keresés** lap a Azure Portal lap jobb oldalán jelenik meg.

![Adatok elemzése a Log Analyticsben](./media/container-insights-analyze/container-health-log-search-example.png)   

A tároló a munkaterületre továbbított kimenetet naplózza STDOUT és STDERR. Azure figyelő által figyelt Azure által felügyelt Kubernetes-(AKS), mert Kube rendszer nem gyűjti jelenleg generált adatok nagy mennyisége miatt. 

### <a name="example-log-search-queries"></a>Példa naplóbeli keresési lekérdezések

Gyakran hasznos hozhatók létre olyan lekérdezések, amelyek például vagy a kettő, és módosítsa őket az igényeinek. Annak érdekében, hogy speciális lekérdezések felépítését, kísérletezhet, és az alábbi mintalekérdezések:

| Lekérdezés | Leírás | 
|-------|-------------|
| ContainerInventory<br> &#124;Számítógép, név, kép, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime projekt<br> &#124;táblázat megjelenítése | Összes egy tároló tárolóéletciklus-adat listázása| 
| KubeEvents_CL<br> &#124;ahol not(isempty(Namespace_s))<br> &#124;Rendezés a TimeGenerated desc<br> &#124;táblázat megjelenítése | Kubernetes-események|
| ContainerImageInventory<br> &#124;summarize AggregatedValue futó = count() by ImageTag, kép | Rendszerképek leltára | 
| **Válassza ki a diagram megjelenítési beállítását**:<br> Perf<br> &#124;ahol ObjectName == "K8SContainer" és a CounterName == "cpuUsageNanoCores" &#124; AvgCPUUsageNanoCores összefoglalója avg(CounterValue) a bin (TimeGenerated, 30 millió), InstanceName = | Tároló CPU | 
| **Válassza ki a diagram megjelenítési beállítását**:<br> Perf<br> &#124;ahol ObjectName == "K8SContainer" és a CounterName == "memoryRssBytes" &#124; AvgUsedRssMemoryBytes összefoglalója avg(CounterValue) a bin (TimeGenerated, 30 millió), InstanceName = | Tároló memória |
| InsightsMetrics<br> &#124;ahol a Name = = "requests_count"<br> &#124;összefoglalás val = any (val) by TimeGenerated = bin (TimeGenerated, 1M)<br> &#124;Rendezés TimeGenerated ASC szerint<br> &#124;projekt RequestsPerMinute = val-Prev (val), TimeGenerated <br> &#124;renderelési barchart  | Percenkénti kérések egyéni metrikákkal |

## <a name="query-prometheus-metrics-data"></a>A Prometheus-metrikai adatok lekérdezése

A következő példa egy olyan Prometheus-metrikai lekérdezés, amely percenkénti lemezeket jelenít meg másodpercenként.

```
InsightsMetrics
| where Namespace == 'container.azm.ms/diskio'
| where TimeGenerated > ago(1h)
| where Name == 'reads'
| extend Tags = todynamic(Tags)
| extend HostName = tostring(Tags.hostName), Device = Tags.name
| extend NodeDisk = strcat(Device, "/", HostName)
| order by NodeDisk asc, TimeGenerated asc
| serialize
| extend PrevVal = iif(prev(NodeDisk) != NodeDisk, 0.0, prev(Val)), PrevTimeGenerated = iif(prev(NodeDisk) != NodeDisk, datetime(null), prev(TimeGenerated))
| where isnotnull(PrevTimeGenerated) and PrevTimeGenerated != TimeGenerated
| extend Rate = iif(PrevVal > Val, Val / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1), iif(PrevVal == Val, 0.0, (Val - PrevVal) / (datetime_diff('Second', TimeGenerated, PrevTimeGenerated) * 1)))
| where isnotnull(Rate)
| project TimeGenerated, NodeDisk, Rate
| render timechart

```

A Azure Monitor névtér alapján megszűrt Prometheus-metrikák megtekintéséhez a "Prometheus" kifejezést kell megadnia. Íme egy példa egy lekérdezésre a Prometheus-metrikák megtekintéséhez a `default` kubernetes-névtérből.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

A Prometheus-adatlekérdezéseket a név alapján is közvetlenül lehet lekérdezni.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Lekérdezési konfiguráció vagy karcolási hibák

A konfigurációs vagy a leselejtezési hibák vizsgálatához a következő példában a lekérdezés a `KubeMonAgentEvents` táblázat tájékoztató eseményeit adja vissza.

```
KubeMonAgentEvents | where Level != "Info" 
```

A kimenet az alábbihoz hasonló eredményeket fog megjeleníteni:

![Az ügynöktől származó tájékoztató események lekérdezési eredményeinek naplózása](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>Következő lépések

A tárolók Azure Monitor nem tartalmaz előre meghatározott riasztásokat. Tekintse át a [teljesítménnyel kapcsolatos riasztások létrehozása a Azure monitor for containers](container-insights-alerts.md) szolgáltatással című témakört, amelyből megtudhatja, hogyan hozhat létre ajánlott riasztásokat magas CPU-és memóriahasználat esetén a DevOps vagy működési folyamatok és eljárások támogatásához 
