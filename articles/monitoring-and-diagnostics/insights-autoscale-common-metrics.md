---
title: Automatikus skálázás közös metrikák
description: Ismerje meg, melyik metrikák gyakran használják az automatikus skálázást a Felhőszolgáltatásokat, a virtuális gépek és a Web Apps.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/6/2016
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 7b6f454a8d4c8794b8c56494fd9ed573f8b79852
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262239"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Az Azure a figyelő automatikus skálázás közös metrikák
Azure figyelő automatikus skálázás lehetővé teszi futó példányok méretezési felfelé vagy lefelé, telemetriai adatokat (metrikák) alapján. Ez a dokumentum ismerteti a gyakori metrikákat, amelyeket érdemes használni. Az Azure-portál a felhőalapú szolgáltatások és a kiszolgálófarmok válassza a metrika bővítse az erőforrás. Bővítse a különböző erőforrás közül azonban bármely metrika is választhat.

Az Azure a figyelő automatikus skálázás vonatkozik csak a [virtuálisgép-méretezési csoportok](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Felhőszolgáltatások](https://azure.microsoft.com/services/cloud-services/), és [App Service - webalkalmazások](https://azure.microsoft.com/services/app-service/web/). Más Azure-szolgáltatások különböző méretezési módszer használatára.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Metrikák Resource Manager-alapú virtuális gépek számítási
Alapértelmezés szerint a Resource Manager-alapú virtuális gépek és virtuálisgép-méretezési csoportok létrehozása (gazdaszintű) alapvető metrikákat. Emellett egy Azure virtuális gép és VMSS diagnosztikai adatok gyűjtésének konfigurálásakor az Azure diagnosztikai-bővítményt is megfelelően kibocsát Vendég-operációsrendszer teljesítményszámlálók (gyakran nevezik "Vendég-operációsrendszer-metrikák").  Ezeket a mérési szabályok automatikus skálázás használható.

Használhatja a `Get MetricDefinitions` API/PoSH/CLI megtekintéséhez a metrikák a VMSS erőforrás érhető el.

Ha Virtuálisgép-méretezési készlet használata és egy adott metrika felsorolt nem jelenik meg, akkor valószínűleg *le van tiltva* a diagnosztika bővítményben.

Ha egy adott metrika nem készül mintát vagy a kívánt gyakorisággal, frissítheti a diagnosztika konfigurációját.

Ha az előző mindenképpen értéke true, majd tekintse át [Windows rendszerű virtuális gépként Azure Diagnostics engedélyezéséhez használja a Powershellt](../virtual-machines/windows/ps-extensions-diagnostics.md) kapcsolatos PowerShell használatával az Azure Virtuálisgép-diagnosztika kiterjesztést a metrika engedélyezése és konfigurálása. Cikkben is egy minta diagnosztika konfigurációs fájlt.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>A Resource Manager-alapú Windows és Linux virtuális gépek gazdagép-metrikák
A következő gazdagép szintekhez tartozó metrikákat kibocsátott alapértelmezés szerint az Azure virtuális gép és VMSS Windows és Linux példányát. A metrikák írják le az Azure virtuális gép, de az Azure virtuális gép gazdagépről, nem pedig a Vendég virtuális Gépen telepített ügynök keresztül gyűjtött. Ezeket a mérési szabályok automatikus skálázás lehet használni.

- [A Resource Manager-alapú Windows és Linux virtuális gépek gazdagép-metrikák](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)
- [A Resource Manager-alapú Windows és Linux Virtuálisgép-méretezési készlet gazdagép metrikák](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>Resource Manager-alapú Windows virtuális gépek vendég operációs rendszer metrikák
Ha a virtuális gép létrehozása az Azure-ban, diagnosztika engedélyezve van a diagnosztika bővítményével. A diagnosztika bővítményt a virtuális Gépen belül vett metrikák készlete bocsát ki. Ez azt jelenti, hogy ki alapértelmezés szerint nem kibocsátott metrikák automatikus skálázás is.

A metrikák listája a következő parancsot a PowerShell használatával is létrehozhat.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

A következő mérőszámokat riasztást hozhat létre:

| Metrika neve | Unit (Egység) |
| --- | --- |
| \Processor(_Total)\% Processor Time |Százalék |
| \Processor(_Total)\% védett módú használatának aránya |Százalék |
| \Processor(_Total)\% felhasználói idő |Százalék |
| \Processor információkat (_Total) \Processor gyakorisága |Darabszám |
| \System\Processes |Darabszám |
| \Process (_Total) \Thread száma |Darabszám |
| \Process (_Total) \Handle száma |Darabszám |
| \Memory\% előjegyzett memória |Százalék |
| \Memory\Available Bytes |Bájt |
| \Memory\Committed bájt |Bájt |
| \Memory\Commit korlátot |Bájt |
| Lapozható \Memory\Pool bájt |Bájt |
| \Memory\Pool nem lapozható készlet mérete |Bájt |
| \PhysicalDisk(_Total)\% lemez idő |Százalék |
| \PhysicalDisk(_Total)\% lemez olvasási idő |Százalék |
| \PhysicalDisk(_Total)\% lemezre írási ideje |Százalék |
| \PhysicalDisk(_Total)\Disk Transfers/sec |Egység/s |
| Lemezolvasások/mp (%) (_Total) \PhysicalDisk \Disk |Egység/s |
| \PhysicalDisk (_Total) \Disk/mp |Egység/s |
| \PhysicalDisk (_Total) \Disk bájtok/s |Bájt/s |
| Olvasott bájt/mp (%) (_Total) \PhysicalDisk \Disk |Bájt/s |
| \PhysicalDisk (_Total) \Disk írt bájt/mp |Bájt/s |
| \PhysicalDisk (_Total) \Avg. Lemezvárólista hossza |Darabszám |
| \PhysicalDisk (_Total) \Avg. Olvasási Lemezvárólista hossza |Darabszám |
| \PhysicalDisk (_Total) \Avg. Írási Lemezvárólista hossza |Darabszám |
| \LogicalDisk(_Total)\% szabad területe |Százalék |
| \LogicalDisk (_Total) \Free mérete (MB) |Darabszám |

### <a name="guest-os-metrics-linux-vms"></a>Linux virtuális gépek vendég operációs rendszer metrikák
Ha a virtuális gép létrehozása az Azure-ban, diagnosztika alapértelmezés szerint engedélyezve van diagnosztika bővítmény használatával.

A metrikák listája a következő parancsot a PowerShell használatával is létrehozhat.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 A következő mérőszámokat riasztást hozhat létre:

| Metrika neve | Unit (Egység) |
| --- | --- |
| \Memory\AvailableMemory |Bájt |
| \Memory\PercentAvailableMemory |Százalék |
| \Memory\UsedMemory |Bájt |
| \Memory\PercentUsedMemory |Százalék |
| \Memory\PercentUsedByCache |Százalék |
| \Memory\PagesPerSec |Egység/s |
| \Memory\PagesReadPerSec |Egység/s |
| \Memory\PagesWrittenPerSec |Egység/s |
| \Memory\AvailableSwap |Bájt |
| \Memory\PercentAvailableSwap |Százalék |
| \Memory\UsedSwap |Bájt |
| \Memory\PercentUsedSwap |Százalék |
| \Processor\PercentIdleTime |Százalék |
| \Processor\PercentUserTime |Százalék |
| \Processor\PercentNiceTime |Százalék |
| \Processor\PercentPrivilegedTime |Százalék |
| \Processor\PercentInterruptTime |Százalék |
| \Processor\PercentDPCTime |Százalék |
| \Processor\PercentProcessorTime |Százalék |
| \Processor\PercentIOWaitTime |Százalék |
| \PhysicalDisk\BytesPerSecond |Bájt/s |
| \PhysicalDisk\ReadBytesPerSecond |Bájt/s |
| \PhysicalDisk\WriteBytesPerSecond |Bájt/s |
| \PhysicalDisk\TransfersPerSecond |Egység/s |
| \PhysicalDisk\ReadsPerSecond |Egység/s |
| \PhysicalDisk\WritesPerSecond |Egység/s |
| \PhysicalDisk\AverageReadTime |másodperc |
| \PhysicalDisk\AverageWriteTime |másodperc |
| \PhysicalDisk\AverageTransferTime |másodperc |
| \PhysicalDisk\AverageDiskQueueLength |Darabszám |
| \NetworkInterface\BytesTransmitted |Bájt |
| \NetworkInterface\BytesReceived |Bájt |
| \NetworkInterface\PacketsTransmitted |Darabszám |
| \NetworkInterface\PacketsReceived |Darabszám |
| \NetworkInterface\BytesTotal |Bájt |
| \NetworkInterface\TotalRxErrors |Darabszám |
| \NetworkInterface\TotalTxErrors |Darabszám |
| \NetworkInterface\TotalCollisions |Darabszám |

## <a name="commonly-used-web-server-farm-metrics"></a>A gyakran használt (kiszolgálófarm) webes metrikák
Például a Http-várólista hossza közös web server mérőszámok alapján automatikus skálázás is elvégezheti. Az metrika neve **HttpQueueLength**.  A következő szakasz elérhető server farm (webalkalmazások) metrikákat.

### <a name="web-apps-metrics"></a>Webes alkalmazások metrikák
A Web Apps metrikák listája a következő parancsot a PowerShell használatával is létrehozhat.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Riasztás, vagy skálázhatja által metrikákat.

| Metrika neve | Unit (Egység) |
| --- | --- |
| CpuPercentage |Százalék |
| MemoryPercentage |Százalék |
| DiskQueueLength |Darabszám |
| HttpQueueLength |Darabszám |
| BytesReceived |Bájt |
| BytesSent |Bájt |

## <a name="commonly-used-storage-metrics"></a>Tárolási általánosan használt mérőszámait
Méretezhető tárolás várólista hossza, amely a tárolási várólistában lévő üzenetek száma szerint. Tároló várólista hossza különleges metrika, és a küszöbérték példányonként üzenetek száma. Például ha két példányt, és ha a küszöb értéke 100, skálázás esetén a várólistán lévő üzenetek száma 200. Amely lehet egy példány 100 üzenetek, 120 és 80 vagy bármely más kombinációja, amely legfeljebb 200 vagy több ad hozzá.

Ez a beállítás konfigurálása az Azure-portálon a **beállítások** panelen. Virtuálisgép-méretezési készlet, frissítheti az automatikus skálázási beállítás használata a Resource Manager sablon *metricName* , *ApproximateMessageCount* , és adja át, a tároló várólista Azonosítójának  *metricResourceUri*.

Például egy klasszikus tárfiókot az automatikus skálázási beállítás metricTrigger a következők:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

(Nem klasszikus) tárfiókot a metricTrigger ilyen műveletekre:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>A Service Bus általánosan használt mérőszámait
A Service Bus-várólista hossza, amely a Service Bus várólistában lévő üzenetek száma szerint méretezheti. A Service Bus-várólista hossza különleges metrika, és a küszöbérték példányonként üzenetek száma. Például ha két példányt, és ha a küszöb értéke 100, skálázás esetén a várólistán lévő üzenetek száma 200. Amely lehet egy példány 100 üzenetek, 120 és 80 vagy bármely más kombinációja, amely legfeljebb 200 vagy több ad hozzá.

Virtuálisgép-méretezési készlet, frissítheti az automatikus skálázási beállítás használata a Resource Manager sablon *metricName* , *ApproximateMessageCount* , és adja át, a tároló várólista Azonosítójának  *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> A Service Bushoz az erőforrás-csoport fogalma nem létezik, de Azure Resource Manager létrehoz egy alapértelmezett erőforráscsoport régiónként. Az erőforráscsoport általában a "Default - Szolgáltatásbusz-[régió]" formátumban van. Például: "Alapértelmezett-Szolgáltatásbusz-EastUS", "Alapértelmezett-Szolgáltatásbusz-WestUS", "Alapértelmezett-Szolgáltatásbusz-AustraliaEast" stb.
>
>
