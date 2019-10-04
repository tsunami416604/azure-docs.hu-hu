---
title: Gyakori metrikák autoskálázása
description: Megtudhatja, hogy mely metrikákat használják általában a Cloud Services, Virtual Machines és Web Apps automatikus skálázásához.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/6/2016
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 9da8e5fb88ff34e561b579b760973ecd23c884a3
ms.sourcegitcommit: 13d5eb9657adf1c69cc8df12486470e66361224e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "66129732"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Általános mérőszámok automatikus skálázása Azure Monitor

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor az automatikus skálázás lehetővé teszi a futó példányok számának méretezését a telemetria-adatok (mérőszámok) alapján. Ez a dokumentum az esetlegesen használni kívánt általános mérőszámokat ismerteti. A Azure Portalban kiválaszthatja a skálázáshoz használandó erőforrás metrikáját. Más erőforrásokból is kiválaszthat bármely mérőszámot a skálázáshoz.

Azure Monitor az autoscale csak [Virtual Machine Scale sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)és [API Management szolgáltatásokra](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)vonatkozik. Más Azure-szolgáltatások eltérő skálázási módszereket használnak.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>A Resource Manager-alapú virtuális gépek számítási mérőszámai
Alapértelmezés szerint a Resource Manager-alapú Virtual Machines és Virtual Machine Scale Sets alapszintű (gazdagép szintű) mérőszámokat bocsát ki. Emellett az Azure-beli virtuális gépek és VMSS diagnosztikai adatgyűjtésének konfigurálásakor az Azure diagnosztikai bővítmény a vendég-operációsrendszer-teljesítményszámlálók (általában "Vendég-OS mérőszámok") kitöltésére is lehetőséget nyújt.  Ezeket a metrikákat az autoskálázási szabályokban használhatja.

Az `Get MetricDefinitions` API/PoSH/CLI használatával megtekintheti a VMSS-erőforráshoz elérhető metrikákat.

Ha virtuálisgép-méretezési csoportokat használ, és nem jelenik meg egy adott metrika, akkor a diagnosztika bővítmény valószínűleg *le lesz tiltva* .

Ha egy adott metrika nem mintavételezés alatt áll, vagy a kívánt gyakorisággal kerül át, akkor frissítheti a diagnosztikai konfigurációt.

Ha az előző eset értéke TRUE (igaz), akkor tekintse át a [PowerShell használata a Windows rendszerű virtuális gépeken](../../virtual-machines/extensions/diagnostics-windows.md) a PowerShell használatával történő Azure Diagnosticsának engedélyezéséhez és frissítéséhez az Azure VM Diagnostics bővítményt a metrika engedélyezéséhez. Ez a cikk egy példa diagnosztikai konfigurációs fájlt is tartalmaz.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>A Resource Manager-alapú Windows és Linux rendszerű virtuális gépek gazdagép-metrikái
Az Azure-beli virtuális gép és a VMSS esetében alapértelmezés szerint a következő gazdagép-szintű metrikák vannak kibocsátva Windows-és Linux-példányokban. Ezek a metrikák írják le az Azure-beli virtuális gépet, de az Azure VM-gazdagépről gyűjtik, nem pedig a vendég virtuális gépen telepített ügynökön keresztül. Ezeket a metrikákat az automatikus skálázási szabályokban használhatja.

- [A Resource Manager-alapú Windows és Linux rendszerű virtuális gépek gazdagép-metrikái](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)
- [A Resource Manager-alapú Windows-és Linux-VM Scale Sets gazdagép-metrikái](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>Vendég operációs rendszer metrikái Resource Manager-alapú Windows virtuális gépek
Amikor létrehoz egy virtuális gépet az Azure-ban, a diagnosztika a diagnosztika bővítmény használatával engedélyezhető. A diagnosztikai bővítmény a virtuális gép belsejéből származó mérőszámok készletét bocsátja ki. Ez azt jelenti, hogy az alapértelmezés szerint nem kibocsátott mérőszámok autoskálázása is megtörténik.

A metrikák listáját a PowerShellben a következő parancs használatával hozhatja meg.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Riasztást a következő mérőszámokhoz hozhat létre:

| Metrika neve | Unit (Egység) |
| --- | --- |
| \Processor(_Total)\% Processor Time |Percent |
| \Processor (összesen)\% – Kiemelt idő |Percent |
| \Processor (összesen)\% felhasználói idő |Percent |
| \Processor-információ (összesen) \Processor gyakorisága |Count |
| \System\Processes |Count |
| \Process (összesen) \Thread száma |Count |
| \Process (összesen) \Handle száma |Count |
| \Memory\% előjegyzett bájtok használatban |Percent |
| \Memory\Available Bytes |Bájt |
| \Memory\Committed bájtok |Bájt |
| \Memory\Commit korlátja |Bájt |
| \Memory\Pool lapozható bájtok |Bájt |
| \Memory\Pool, nem lapozható bájtok |Bájt |
| \PhysicalDisk (összesen)\% lemez ideje |Percent |
| \PhysicalDisk (összesen)\% lemez olvasási ideje |Percent |
| \PhysicalDisk (összesen)\% lemez írási ideje |Percent |
| \PhysicalDisk(_Total)\Disk Transfers/sec |Egység/s |
| \PhysicalDisk (összesen) \ olvasás/mp |Egység/s |
| \PhysicalDisk (összesen) \ (írás/mp) |Egység/s |
| \PhysicalDisk (összesen) \ átviteli sebessége (bájt/s) |BytesPerSecond |
| \PhysicalDisk (összesen) \ olvasási sebesség (bájt/s) |BytesPerSecond |
| \PhysicalDisk (összesen) \ írási sebesség (bájt/s) |BytesPerSecond |
| \PhysicalDisk(_Total)\Avg. Lemezvárólista hossza |Count |
| \PhysicalDisk(_Total)\Avg. Lemez olvasási várólistájának hossza |Count |
| \PhysicalDisk(_Total)\Avg. Lemez írási várólistájának hossza |Count |
| \Logikai lemez (összesen)\% szabad terület |Percent |
| \Logikai lemez (összesen) \Szabad hely (MB) |Count |

### <a name="guest-os-metrics-linux-vms"></a>Vendég operációs rendszer mérőszámai Linux rendszerű virtuális gépek
Amikor létrehoz egy virtuális gépet az Azure-ban, a diagnosztika alapértelmezés szerint engedélyezve van a diagnosztikai bővítmény használatával.

A metrikák listáját a PowerShellben a következő parancs használatával hozhatja meg.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Riasztást a következő mérőszámokhoz hozhat létre:

| Metrika neve | Unit (Egység) |
| --- | --- |
| \Memory\AvailableMemory |Bájt |
| \Memory\PercentAvailableMemory |Percent |
| \Memory\UsedMemory |Bájt |
| \Memory\PercentUsedMemory |Percent |
| \Memory\PercentUsedByCache |Percent |
| \Memory\PagesPerSec |Egység/s |
| \Memory\PagesReadPerSec |Egység/s |
| \Memory\PagesWrittenPerSec |Egység/s |
| \Memory\AvailableSwap |Bájt |
| \Memory\PercentAvailableSwap |Percent |
| \Memory\UsedSwap |Bájt |
| \Memory\PercentUsedSwap |Percent |
| \Processor\PercentIdleTime |Percent |
| \Processor\PercentUserTime |Percent |
| \Processor\PercentNiceTime |Percent |
| \Processor\PercentPrivilegedTime |Percent |
| \Processor\PercentInterruptTime |Percent |
| \Processor\PercentDPCTime |Percent |
| \Processor\PercentProcessorTime |Percent |
| \Processor\PercentIOWaitTime |Percent |
| \PhysicalDisk\BytesPerSecond |BytesPerSecond |
| \PhysicalDisk\ReadBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\WriteBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\TransfersPerSecond |Egység/s |
| \PhysicalDisk\ReadsPerSecond |Egység/s |
| \PhysicalDisk\WritesPerSecond |Egység/s |
| \PhysicalDisk\AverageReadTime |Másodperc |
| \PhysicalDisk\AverageWriteTime |Másodperc |
| \PhysicalDisk\AverageTransferTime |Másodperc |
| \PhysicalDisk\AverageDiskQueueLength |Count |
| \NetworkInterface\BytesTransmitted |Bájt |
| \NetworkInterface\BytesReceived |Bájt |
| \NetworkInterface\PacketsTransmitted |Count |
| \NetworkInterface\PacketsReceived |Count |
| \NetworkInterface\BytesTotal |Bájt |
| \NetworkInterface\TotalRxErrors |Count |
| \NetworkInterface\TotalTxErrors |Count |
| \NetworkInterface\TotalCollisions |Count |

## <a name="commonly-used-web-server-farm-metrics"></a>Gyakran használt web (kiszolgálófarm) metrikák
Az autoskálázást a gyakori webkiszolgáló-metrikák, például a http-várólista hossza alapján is elvégezheti. A metrika neve **HttpQueueLength**.  A következő szakasz felsorolja a rendelkezésre álló kiszolgálófarm (Web Apps) mérőszámait.

### <a name="web-apps-metrics"></a>Web Apps metrikák
A Web Apps metrikáinak listáját a PowerShellben a következő paranccsal hozhatja meg.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Ezeket a metrikákat riasztással vagy méretezhetővé teheti.

| Metrika neve | Unit (Egység) |
| --- | --- |
| CpuPercentage |Percent |
| MemoryPercentage |Percent |
| DiskQueueLength |Count |
| HttpQueueLength |Count |
| BytesReceived |Bájt |
| BytesSent |Bájt |

## <a name="commonly-used-storage-metrics"></a>Gyakran használt tárolási metrikák
A tárolási várólista hossza méretezhető, ami a tárolási várólistában lévő üzenetek száma. A tárolási várólista hossza egy speciális metrika, a küszöbérték pedig az üzenetek száma egy példányban. Ha például két példány van megadva, és ha a küszöbérték 100 értékre van állítva, akkor a skálázás akkor fordul elő, ha a várólistán lévő üzenetek száma összesen 200. A példányok száma 100, 120 és 80, vagy bármely más kombináció, amely akár 200-et, akár többet is felvesz.

Konfigurálja ezt a beállítást a Azure Portal a **Beállítások** panelen. A virtuálisgép-méretezési csoportok esetében frissítheti a Resource Manager-sablonban található autoskálázási beállítást úgy, hogy a *MetricName* *ApproximateMessageCount* használja, és a *metricResourceUri*azonosítóját adja át.

Egy klasszikus Storage-fiókkal például a metricTrigger az autoskálázási beállítás a következőket fogja tartalmazni:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

Egy (nem klasszikus) Storage-fiók esetében a metricTrigger a következőket foglalja magában:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Gyakran használt Service Bus metrikák
Service Bus üzenetsor hosszával méretezheti a méretezést, amely a Service Bus üzenetsor üzeneteinek száma. Service Bus üzenetsor hossza egy speciális metrika, és a küszöbérték a példányok száma. Ha például két példány van megadva, és ha a küszöbérték 100 értékre van állítva, akkor a skálázás akkor fordul elő, ha a várólistán lévő üzenetek száma összesen 200. A példányok száma 100, 120 és 80, vagy bármely más kombináció, amely akár 200-et, akár többet is felvesz.

A virtuálisgép-méretezési csoportok esetében frissítheti a Resource Manager-sablonban található autoskálázási beállítást úgy, hogy a *MetricName* *ApproximateMessageCount* használja, és a *metricResourceUri*azonosítóját adja át.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Service Bus esetében az erőforráscsoport fogalma nem létezik, de a Azure Resource Manager régiónként létrehoz egy alapértelmezett erőforráscsoportot. Az erőforráscsoport általában az "alapértelmezett-ServiceBus-[region]" formátumban van. Például: "default-ServiceBus-EastUS", "default-ServiceBus-WestUS", "default-ServiceBus-AustraliaEast" stb.
>
>

