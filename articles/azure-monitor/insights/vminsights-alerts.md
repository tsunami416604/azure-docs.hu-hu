---
title: Riasztások az Azure Monitor virtuális gépekhez
description: Bemutatja, hogyan hozhat létre riasztási szabályokat az Azure Monitor virtuális gépekhez gyűjtött teljesítményadatokból.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/23/2020
ms.openlocfilehash: 987537d8497b3d8f2728941334d8328320ec6997
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289601"
---
# <a name="how-to-create-alerts-from-azure-monitor-for-vms"></a>Értesítések létrehozása az Azure Monitor szolgáltatásból a virtuális gépekhez
[Riasztások az Azure Monitor](../platform/alerts-overview.md) ban proaktív módon értesíti Önt a figyelési adatok érdekes adatok és minták. A virtuális gépek hez nem tartalmaz előre konfigurált riasztási szabályokat, de létrehozhatja a saját adatok alapján, hogy az általa gyűjtött adatok alapján. Ez a cikk útmutatást nyújt a riasztási szabályok létrehozásához, beleértve a mintalekérdezések.


## <a name="alert-rule-types"></a>Riasztási szabálytípusok
Az Azure Monitor [különböző típusú riasztási szabályok](../platform/alerts-overview.md#what-you-can-alert-on) alapján a riasztás létrehozásához használt adatokat. Az Azure Monitor virtuális gépekhez gyűjtött összes adatot az Azure Monitor naplók, amely támogatja a [naplóriasztásokat.](../platform/alerts-log.md) Jelenleg nem [használhatja metrikariasztások](../platform/alerts-log.md) teljesítményadatokat gyűjtött az Azure Monitor virtuális gépek, mert az adatok nem gyűjtik az Azure Monitor metrikák. Metrikariasztások adatok gyűjtéséhez telepítse a Windows virtuális gépek [diagnosztikai bővítményét](../platform/diagnostics-extension-overview.md) vagy a [Telegraf ügynök](../platform/collect-custom-metrics-linux-telegraf.md) linuxos virtuális gépekhez a teljesítményadatok metrikákba való gyűjtéséhez.

Az Azure Monitorban kétféle naplóriasztás létezik:

- [Az eredmények száma riasztások](../platform/alerts-unified-log.md#number-of-results-alert-rules) hozzon létre egy riasztást, ha a lekérdezés legalább egy megadott számú rekordot ad vissza. Ezek ideálisak a nem numerikus adatokhoz, például a [Log Analytics-ügynök](../platform/log-analytics-agent.md) által gyűjtött Windows- és Syslog-eseményekhez, illetve a több számítógépen futó teljesítménytrendek elemzéséhez.
- [Metrikamérési riasztások](../platform/alerts-unified-log.md#metric-measurement-alert-rules) külön riasztást hoznak létre a lekérdezés minden olyan rekordja számára, amelynek értéke meghaladja a riasztási szabályban meghatározott küszöbértéket. Ezek a riasztási szabályok ideálisak az Azure Monitor által virtuális gépekhez gyűjtött teljesítményadatokhoz, mivel az egyes számítógépekhez egyedi riasztásokat hozhatnak létre.


## <a name="alert-rule-walkthrough"></a>Riasztási szabály forgatókönyve
Ez a szakasz bemutatja egy metrikamérési riasztási szabály létrehozását az Azure Monitor virtuális gépek teljesítményadatainak használatával. Ezt az alapvető folyamatot számos naplólekérdezéssel használhatja a különböző teljesítményszámlálók riasztásához.

Először hozzon létre egy új riasztási szabályt az Eljárást követve az [Azure Monitor használatával naplóriasztások létrehozása, megtekintése és kezelése című eljárással.](../platform/alerts-log.md) Az **erőforrás**hoz válassza ki a Log Analytics munkaterületet, amely az Azure Monitor virtuális gépek az előfizetésben. Mivel a naplóriasztási szabályok célerőforrása mindig egy Log Analytics-munkaterület, a naplólekérdezésnek tartalmaznia kell az adott virtuális gépek vagy a virtuálisgép-méretezési csoportok szűrőjét. 

A riasztási szabály **feltételéhez** használja az [alábbi szakaszban](#sample-alert-queries) található lekérdezések egyikét **keresési lekérdezésként.** A lekérdezésnek egy AggregatedValue nevű numerikus tulajdonságot kell *visszaadnia.* Össze kell foglalnia az adatokat számítógép szerint, hogy minden olyan virtuális géphez külön riasztást hozhasson létre, amely meghaladja a küszöbértéket.

A **Riasztás logika,** válassza **metrika mérés,** majd adja meg **a küszöbértéket.** Az **Eseményindító riasztás alapján mezőben**adja meg, hogy a riasztás létrehozása előtt hányszor kell túllépni a küszöbértéket. Például valószínűleg nem érdekli, ha a processzor egyszer túllépi a küszöbértéket, majd visszatér a normál értékre, de nem érdekli, ha továbbra is meghaladja a küszöbértéket több egymást követő mérés során.

A **Kiértékelés alapján** szakasz határozza meg, hogy a lekérdezés milyen gyakran fusson, és a lekérdezés időablaka. Az alábbi példában a lekérdezés 15 percenként fut, és kiértékeli az előző 15 perc során gyűjtött teljesítményértékeket.


![Metrikamérési riasztási szabály](media/vminsights-alerts/metric-measurement-alert.png)

## <a name="sample-alert-queries"></a>Mintafigyelmeztető lekérdezések
A következő lekérdezések egy metrikamérési riasztási szabály segítségével az Azure Monitor virtuális gépek teljesítményadatok használatával használható. Mindegyik összefoglalja az adatokat számítógép szerint, hogy minden olyan számítógéphez riasztás jön létre, amelynek értéke meghaladja a küszöbértéket.

### <a name="cpu-utilization"></a>Processzorhasználat

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "Processor" and Name == "UtilizationPercentage" 
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-mb"></a>Mb-ban rendelkezésre álló memória

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Memory" and Name == "AvailableMB"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="available-memory-in-percentage"></a>Rendelkezésre álló memória százalékban

```kusto
InsightsMetrics 
| where Origin == "vm.azm.ms" 
| where Namespace == "Memory" and Name == "AvailableMB" 
| extend TotalMemory = toreal(todynamic(Tags)["vm.azm.ms/memorySizeMB"])
| extend AvailableMemoryPercentage = (toreal(Val) / TotalMemory) * 100.0 
| summarize AggregatedValue = avg(AvailableMemoryPercentage) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---all-disks-on-each-computer"></a>Használt logikai lemez - az egyes számítógépek összes lemeze

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="logical-disk-used---individual-disks"></a>Használt logikai lemez - egyedi lemezek

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "LogicalDisk" and Name == "FreeSpacePercentage"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, Disk
```

### <a name="logical-disk-iops"></a>Logikai lemez IOPS

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "TransfersPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) ), Computer, _ResourceId, Disk
```

### <a name="logical-disk-data-rate"></a>Logikai lemezadat-sebesség

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms" 
| where Namespace == "LogicalDisk" and Name == "BytesPerSecond"
| extend Disk=tostring(todynamic(Tags)["vm.azm.ms/mountId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m) , Computer, _ResourceId, Disk
```

### <a name="network-interfaces-bytes-received---all-interfaces"></a>Fogadott hálózati csatolók – az összes kapcsolat

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId 
```

### <a name="network-interfaces-bytes-received---individual-interfaces"></a>Fogadott hálózati csatolók – egyedi összeköttetések

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "ReadBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="network-interfaces-bytes-sent---all-interfaces"></a>Elküldött hálózati csatolók bájtjai – az összes kapcsolat

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId
```

### <a name="network-interfaces-bytes-sent---individual-interfaces"></a>Elküldött hálózati csatolók – egyedi összeköttetések

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where Namespace == "Network" and Name == "WriteBytesPerSecond"
| extend NetworkInterface=tostring(todynamic(Tags)["vm.azm.ms/networkDeviceId"])
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), Computer, _ResourceId, NetworkInterface
```

### <a name="virtual-machine-scale-set"></a>Virtuálisgép-méretezési csoport
Módosítsa az előfizetés-azonosítóval, az erőforráscsoporttal és a virtuálisgép-méretezési csoport nevével.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/my-vm-scaleset" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="specific-virtual-machine"></a>Adott virtuális gép
Módosítsa az előfizetés-azonosítóval, az erőforráscsoporttal és a virtuális gép nevével.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId =~ "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/my-vm" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m)
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-subscription"></a>Az előfizetés összes számítási erőforrásának processzorkihasználtsága
Módosítsa az előfizetés-azonosítójával.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" and (_ResourceId contains "/providers/Microsoft.Compute/virtualMachines/" or _ResourceId contains "/providers/Microsoft.Compute/virtualMachineScaleSets/")
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId
```

### <a name="cpu-utilization-for-all-compute-resources-in-a-resource-group"></a>Egy erőforráscsoport összes számítási erőforrásának processzorkihasználtsága
Módosítás az előfizetés-azonosítóval és az erőforráscsoporttal.

```kusto
InsightsMetrics
| where Origin == "vm.azm.ms"
| where _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachines/"
or _ResourceId startswith "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.Compute/virtualMachineScaleSets/" 
| where Namespace == "Processor" and Name == "UtilizationPercentage"
| summarize AggregatedValue = avg(Val) by bin(TimeGenerated, 15m), _ResourceId

```

## <a name="next-steps"></a>További lépések

- További információ [az Azure Monitor riasztásairól.](../platform/alerts-overview.md)
- További információ a [virtuális gépekhez készült Azure Monitor adataival kapcsolatos naplólekérdezésekről.](vminsights-log-search.md)
