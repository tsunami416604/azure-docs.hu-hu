---
title: Hogyan lekérdezés naplók az Azure Monitor-tárolókhoz |} A Microsoft Docs
description: Tárolók az Azure Monitor mérőszámokban és naplófájlokban adatokat gyűjt, és ez a cikk ismerteti a rekordokat és mintalekérdezéseket tartalmaz.
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
ms.date: 04/17/2019
ms.author: magoedte
ms.openlocfilehash: 66fc55d8c3dbb8487d1e796d5f30b08a94f717f6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60494770"
---
# <a name="how-to-query-logs-from-azure-monitor-for-containers"></a>A tárolók az Azure Monitor naplók lekérdezése
A tárolók az Azure Monitor tárológazdagép és tárolók állapotinformációkat, teljesítmény-mérőszámok és leltári adatok gyűjt, és továbbítja azokat a Log Analytics-munkaterületet az Azure monitorban. Az adatgyűjtés percen át 3 percenként történik. Ezek az adatok érhető el [lekérdezés](../../azure-monitor/log-query/log-query-overview.md) az Azure monitorban. Ezeket az adatokat, beleértve az áttelepítés megtervezése, kapacitáselemzési, felderítési és igény szerinti teljesítménnyel kapcsolatos hibaelhárítás forgatókönyveket is alkalmazhat.

## <a name="container-records"></a>Tárolórekordok

Az alábbi táblázatban példák a tárolók és az adattípusok, amely a napló keresési eredmények között megjelenik az Azure Monitor által gyűjtött rekordok jelennek meg:

| Adattípus | Naplókeresési adatok típusa | Mezők |
| --- | --- | --- |
| Gazdagépek és-tárolók teljesítménye | `Perf` | Számítógép, ObjectName, CounterName &#40;processzoridő, a lemez beolvassa a MB, lemezre ír MB memória kihasználtsága (MB), hálózati fogadott bájtok, hálózati küldése memória, processzor kihasználtsága (mp), hálózati&#41;, Avg, TimeGenerated, Számláló_elérési_útja, SourceSystem |
| Tároló leltár | `ContainerInventory` | TimeGenerated, a számítógép, a tároló nevét, ContainerHostname, kép, ImageTag, ContainerState, ExitCode, EnvironmentVar, a parancs, CreatedTime, StartedTime, FinishedTime, SourceSystem, cseréjekor a Tárolóazonosító, ImageID |
| Tároló-napló | `ContainerLog` | TimeGenerated, a számítógép, a lemezkép-azonosító, a Tárolónév esetén LogEntrySource, LogEntry, SourceSystem, cseréjekor a Tárolóazonosító |
| Tárolócsomópont-készlet | `ContainerNodeInventory`| TimeGenerated, számítógép, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kubernetes-fürt podok leltára | `KubePodInventory` | TimeGenerated, számítógép, ClusterId, ContainerCreationTimeStamp, PodUid, PodCreationTimeStamp, ContainerRestartCount, PodRestartCount, PodStartTime, ContainerStartTime, szolgáltatásnév, ControllerKind, ControllerName, tároló,  ContainerStatusReason, cseréjekor a Tárolóazonosító, ContainerName, Name, PodLabel, Namespace, PodStatus, ClusterName, pod IP-címére, SourceSystem |
| Kubernetes-fürt csomópontjai részét leltára | `KubeNodeInventory` | TimeGenerated, számítógép, ClusterName, ClusterId, LastTransitionTimeReady, címkék, állapot, KubeletVersion, KubeProxyVersion, CreationTimeStamp, SourceSystem | 
| Kubernetes-események | `KubeEvents` | TimeGenerated, számítógép, ClusterId_s, FirstSeen_t, LastSeen_t, Count_d, ObjectKind_s, Namespace_s, Name_s, Reason_s, Type_s, TimeGenerated_s, SourceComponent_s, ClusterName_s, üzenet, SourceSystem | 
| Kubernetes-fürtben a szolgáltatások | `KubeServices` | TimeGenerated, ServiceName_s, Namespace_s, SelectorLabels_s, ClusterId_s, ClusterName_s, ClusterIP_s, ServiceType_s, SourceSystem | 
| A Kubernetes-fürt csomópontok részei a teljesítmény-mérőszámok | Teljesítményoptimalizált &#124; ahol ObjectName == "K8SNode" | Számítógép, ObjectName, CounterName &#40;cpuAllocatableBytes, memoryAllocatableBytes, cpuCapacityNanoCores, memoryCapacityBytes, memoryRssBytes, cpuUsageNanoCores, memoryWorkingsetBytes, restartTimeEpoch&#41;, Avg, TimeGenerated, Számláló_elérési_útja, SourceSystem | 
| A Kubernetes-fürt része tárolók teljesítménymetrikáit | Teljesítményoptimalizált &#124; ahol ObjectName == "K8SContainer" | CounterName &#40; cpuRequestNanoCores, memoryRequestBytes, cpuLimitNanoCores, memoryWorkingSetBytes, restartTimeEpoch, cpuUsageNanoCores, memoryRssBytes&#41;, Avg, TimeGenerated, Számláló_elérési_útja, SourceSystem | 
| InsightsMetrics | Computer, Name, Namespace, Origin, SourceSystem, Tags, TimeGenerated, Type, Value |

## <a name="search-logs-to-analyze-data"></a>Keresési naplókat az adatelemzéshez
Az Azure Monitor-naplók segíthetnek keresése trendek, diagnosztizálhatja a szűk keresztmetszeteket, előrejelzési vagy összevetését adatokat, amelyek segítségével határozza meg, hogy optimális működik-e az aktuális fürtbeállításokat. Előre definiált naplókeresések rögtön használatba, illetve adja vissza a kívánt módon szabhatja testre a biztosított. 

Kiválasztásával a munkaterület az adatok interaktív elemzés céljából is végezhet a **nézet Kubernetes eseménynaplók** vagy **tárolónaplók megtekintése** lehetőség az előnézeti ablaktáblában láthatja. A **naplóbeli keresés** ablak jobb oldalán, az Azure portal oldalán, amelyen korábban volt.

![Adatok elemzése a Log Analyticsben](./media/container-insights-analyze/container-health-log-search-example.png)   

A tároló naplóinak kimenet, amely a munkaterülethez lesznek továbbítva a STDOUT és STDERR. Azure figyelő által figyelt Azure által felügyelt Kubernetes-(AKS), mert Kube rendszer nem gyűjti jelenleg generált adatok nagy mennyisége miatt. 

### <a name="example-log-search-queries"></a>Példa naplóbeli keresési lekérdezések
Gyakran hasznos hozhatók létre olyan lekérdezések, amelyek például vagy a kettő, és módosítsa őket az igényeinek. Annak érdekében, hogy speciális lekérdezések felépítését, kísérletezhet, és az alábbi mintalekérdezések:

| Lekérdezés | Leírás | 
|-------|-------------|
| ContainerInventory<br> &#124;Számítógép, név, kép, ImageTag, ContainerState, CreatedTime, StartedTime, FinishedTime projekt<br> &#124;táblázat megjelenítése | Összes egy tároló tárolóéletciklus-adat listázása| 
| KubeEvents_CL<br> &#124;ahol not(isempty(Namespace_s))<br> &#124;Rendezés a TimeGenerated desc<br> &#124;táblázat megjelenítése | Kubernetes-események|
| ContainerImageInventory<br> &#124;summarize AggregatedValue futó = count() by ImageTag, kép | Rendszerképek leltára | 
| **Válassza ki a megjelenítendő lehetőség**:<br> Perf<br> &#124;ahol ObjectName == "K8SContainer" és a CounterName == "cpuUsageNanoCores" &#124; AvgCPUUsageNanoCores összefoglalója avg(CounterValue) a bin (TimeGenerated, 30 millió), InstanceName = | Tároló CPU | 
| **Válassza ki a megjelenítendő lehetőség**:<br> Perf<br> &#124;ahol ObjectName == "K8SContainer" és a CounterName == "memoryRssBytes" &#124; AvgUsedRssMemoryBytes összefoglalója avg(CounterValue) a bin (TimeGenerated, 30 millió), InstanceName = | Tároló memória |

## <a name="next-steps"></a>További lépések
A tárolók az Azure Monitor nem tartalmazza a riasztások az előre definiált. Tekintse át a [teljesítményével kapcsolatos riasztások létrehozása az Azure Monitor szolgáltatással tárolók](container-insights-alerts.md) megtudhatja, hogyan hozhat létre a magas CPU és memória kihasználtságáról, a DevOps és üzemeltetési folyamatokat és eljárásokat támogatásához javasolt riasztásokat. 