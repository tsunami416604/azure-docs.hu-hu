---
title: Naplók lekérdezése az Azure Monitorból tárolókhoz | Microsoft dokumentumok
description: Az Azure Monitor tárolók hoz metrikák és naplóadatok, és ez a cikk ismerteti a rekordokat, és mintalekérdezéseket tartalmaz.
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: ff7cbff708b794847d8be69ca8f829e622d7c7ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80333474"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>Naplók lekérdezése az Azure Monitorból tárolókhoz

Az Azure Monitor tárolók hoz teljesítménymetrikák, készletadatok és állapotadatok at tárológazdak és tárolók, és továbbítja a Log Analytics munkaterület az Azure Monitorban. Az adatokat hárompercenként gyűjtjük. Ezek az adatok az Azure Monitor [lekérdezéséhez](../../azure-monitor/log-query/log-query-overview.md) érhetők el. Ezeket az adatokat olyan esetekre alkalmazhatja, amelyek tartalmazzák az áttelepítés tervezését, a kapacitáselemzést, a felderítést és az igény szerinti teljesítményhibaelhárítást.

## <a name="container-records"></a>Tárolórekordok

Példák az Azure Monitor által a tárolókhoz gyűjtött rekordokra és a naplókeresési eredményekben megjelenő adattípusokra az alábbi táblázatban jelennek meg:

| Adattípus | Adattípus a naplókeresésben | Mezők |
| --- | --- | --- |
| Teljesítmény gazdagépekhez és tárolókhoz | `Perf` | Számítógép, Objektumnév, CounterName &#40;%Processzoridő, Lemezolvasások MB, Lemezírások MB, Memóriahasználat MB, Hálózati fogadási bájtok, Hálózati küldési bájtok, Processzorhasználat másodperc, Hálózati&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem |
| Konténerkészlet | `ContainerInventory` | TimeGenerált, Számítógép, tároló név, ContainerHostname, Kép, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Tárolónapló | `ContainerLog` | TimeGenerált, Számítógép, lemezképazonosító, tároló név, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Tárolócsomópont készlete | `ContainerNodeInventory`| TimeGenerated, Számítógép, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, Forrásrendszer|
| Podok leltára egy Kubernetes-fürtben | `KubePodInventory` | TimeGenerált, Számítógép, Fürtazonosító, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartCount, PodStartTime, ContainerStartTime, ServiceName, ControllerKind, ControllerName, ContainerStatus,  ContainerStatusReason, ContainerID, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, PodIp, SourceSystem |
| Kubernetes-fürt részét vevő csomópontok leltára | `KubeNodeInventory` | TimeGenerated, Számítógép, Fürtnév, ClusterId, LastTransitionTimeReady, Címkék, Állapot, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes események | `KubeEvents` | TimeGenerated, Számítógép, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, Üzenet, Forrásrendszer | 
| Szolgáltatások a Kubernetes-fürtben | `KubeServices` | TimeGenerált, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| A Kubernetes-fürt részét leadó csomópontok teljesítménymutatói | Perf &#124; ahol ObjectName == "K8SNode" | Számítógép, ObjectName, CounterName &#40;cpuAllocatableBytes, memoryIslocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRsSBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| A Kubernetes-fürt részét tartalmazó tárolók teljesítménymutatói | Perf &#124; ahol ObjectName == "K8SContainer" | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, CounterValue, TimeGenerated, CounterPath, SourceSystem | 
| Egyéni mutatók |`InsightsMetrics` | Számítógép, Név, Névtér, Eredet, Forrásrendszer, Címkék<sup>1</sup>, TimeGenerated, Típus, Va, _ResourceId | 

<sup>1</sup> A *Címkék* tulajdonság a megfelelő [metrika több dimenzióját](../platform/data-platform-metrics.md#multi-dimensional-metrics) jelöli. A `InsightsMetrics` táblázatban összegyűjtött és tárolt mérőszámokkal kapcsolatos további információkért és a bejegyzés tulajdonságainak leírásáért lásd: [InsightsMetrics overview.](https://github.com/microsoft/OMS-docker/blob/vishwa/june19agentrel/docs/InsightsMetrics.md)

## <a name="search-logs-to-analyze-data"></a>Naplók keresése az adatok elemzéséhez

Az Azure Monitor Naplók segítségével megkeresheti a trendeket, diagnosztizálhatja a szűk keresztmetszeteket, előre jelezhet vagy korrelálhat olyan adatokat, amelyek segíthetnek meghatározni, hogy az aktuális fürtkonfiguráció optimálisan teljesít-e. Előre definiált naplókeresések állnak rendelkezésre, hogy azonnal elkezdhesse használni vagy testre szabhatja az információkat a kívánt módon.

A munkaterületen lévő adatok interaktív elemzését úgy végezheti el, hogy a **View Kubernetes eseménynaplók vagy** a **Tárolónaplók megtekintése** lehetőséget választja a betekintő ablaktáblában a **Nézet elemzés közben** legördülő listából. A **Naplókeresés** lap az Azure Portal-lap jobb oldalán jelenik meg, amelyen éppen volt.

![Adatok elemzése a Log Analyticsben](./media/container-insights-analyze/container-health-log-search-example.png)

A tároló naplók kimenet, amely továbbította a munkaterülets STDOUT és STDERR. Mivel az Azure Monitor figyeli az Azure által felügyelt Kubernetes (AKS), Kube-rendszer nem gyűjti ma, mert a nagy mennyiségű generált adatok. 

### <a name="example-log-search-queries"></a>Példa naplókeresési lekérdezésekre

Gyakran hasznos, ha olyan lekérdezéseket hoz létre, amelyek egy-két példával kezdődnek, majd módosítják azokat, hogy megfeleljenek az igényeinek. A speciálisabb lekérdezések létrehozásához kísérletezhet a következő mintalekérdezésekkel:

| Lekérdezés | Leírás | 
|-------|-------------|
| Konténerkészlet<br> &#124; projekt számítógép, név, kép, imagetag, containerstate, createdtime, startedtime, finishedtime<br> &#124; renderelő tábla | A tároló életciklusra vonatkozó összes információjának listázása| 
| KubeEvents_CL<br> &#124; ahol nem(üres(Namespace_s))<br> &#124; rendezése timegenerated desc szerint<br> &#124; renderelő tábla | Kubernetes-események|
| ContainerImageInventory (Konténerképkészlet)<br> &#124; összegezheti az AggregatedValue = count() képet, imagetag, futás | Képleltár | 
| **Válassza a Vonaldiagram megjelenítési beállítását:**<br> Teljesítmény<br> &#124; ahol az ObjectName == "K8SContainer" és a CounterName == "cpuUsageNanoCores" &#124; összegzi az AvgCPUUsageNanoCores = avg(CounterValue) bin(TimeGenerated, 30m), InstanceName | Konténer CPU | 
| **Válassza a Vonaldiagram megjelenítési beállítását:**<br> Teljesítmény<br> &#124; ahol az ObjectName == "K8SContainer" és a CounterName == "memoryRssBytes" &#124; összegzi az AvgUsedRssMemoryBytes = avg(CounterValue) bin(TimeGenerated, 30m), InstanceName | Tároló memória |
| InsightsMetrics<br> &#124; ahol a név == "requests_count"<br> &#124; összegezi a Val=any(Val) függvényt a TimeGenerated=bin(TimeGenerated, 1m) szerint.<br> &#124; rendezése timeGenerated asc szerint<br> &#124; projektkérelmekPerMinute = Val - prev(Val), TimeGenerated <br> &#124; renderelési sávdiagram  | Kérelmek percenként egyéni mérőszámokkal |

## <a name="query-prometheus-metrics-data"></a>Prometheus-mérőszámok adatainak lekérdezése

A következő példa egy Prometheus metrika lekérdezés, amely lemezolvasások másodpercenként lemezenként csomópontonként.

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

A Prometheus metrikák megtekintéséhez az Azure Monitor névtér szerint szűrt, adja meg a "prometheus". Itt van egy minta lekérdezés a Prometheus metrikák megtekintéséhez a `default` kubernetes névtérből.

```
InsightsMetrics 
| where Namespace == "prometheus"
| extend tags=parse_json(Tags)
| summarize count() by Name
```

A Prometheus-adatok név szerint közvetlenül is lekérdezhetők.

```
InsightsMetrics 
| where Namespace == "prometheus"
| where Name contains "some_prometheus_metric"
```

### <a name="query-config-or-scraping-errors"></a>Lekérdezési konfigurációs vagy kaparási hibák

Bármely konfigurációs vagy kaparási hiba kivizsgálásához a `KubeMonAgentEvents` következő példa lekérdezés tájékoztató eseményeket ad vissza a táblából.

```
KubeMonAgentEvents | where Level != "Info" 
```

A kimenet a következőhöz hasonló eredményeket jelenít meg:

![Tájékoztató események lekérdezési eredményeinek naplózása ügynöktől](./media/container-insights-log-search/log-query-example-kubeagent-events.png)

## <a name="next-steps"></a>További lépések

A tárolók Azure Monitor nem tartalmaz egy előre meghatározott riasztások készletét. Tekintse át a teljesítményriasztások létrehozása az [Azure Monitor tárolók,](container-insights-alerts.md) hogyan hozhat létre ajánlott riasztásokat a magas CPU és a memória kihasználtsága a DevOps vagy az operatív folyamatok és eljárások támogatása érdekében. 
