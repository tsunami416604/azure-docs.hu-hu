---
title: Gyakori metrikák az automatikus méretezés
description: Ismerje meg, mely metrikákat gyakran használják az automatikus méretezés a Cloud Services, a virtuális gépek és a Web Apps.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/6/2016
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: f17d2d1cf25f533fb37d44da2890b6ad8741be82
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53326203"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Gyakori metrikák az Azure Monitor automatikus méretezés
Az Azure Monitor automatikus méretezés lehetővé teszi futó példányok számának méretezése felfelé és lefelé, telemetriai adatok (metrikák) alapján. Ez a dokumentum ismerteti a gyakori metrikák, amelyeket érdemes használni. Az Azure Portalon válassza ki a metrika az erőforrás méretezése által. Méretezhető, hogy egy másik erőforrás közül azonban bármilyen mérőszám is választhat.

Az Azure Monitor automatikus skálázása csak érvényes [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [App Service - webalkalmazások](https://azure.microsoft.com/services/app-service/web/), és [APIManagement-szolgáltatások](https://docs.microsoft.com/azure/api-management/api-management-key-concepts). Más Azure-szolgáltatások különböző méretezési módokat kell használnia.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Metrikák COMPUTE Resource Manager-alapú virtuális gépek számára
Alapértelmezés szerint a Resource Manager-alapú virtuális gépek és a Virtual Machine Scale Sets generuje alapmetrikák (gazdagépszintű). Ezenkívül amikor konfigurál egy Azure virtuális gép és az VMSS diagnostics gyűjtésének, az Azure diagnosztikai bővítményét is bocsát ki-alapú vendég operációs rendszer rendszerteljesítmény-számlálók (gyakran más néven "-alapú vendég operációs rendszer metrikák").  Ezek a metrikák az automatikus skálázási szabályok használhatja.

Használhatja a `Get MetricDefinitions` API vagy PoSH vagy a parancssori felületen az VMSS-erőforrás rendelkezésre álló metrikák megtekintéséhez.

Virtuálisgép-méretezési csoportok használata és a egy adott mérőszám felsorolt nem jelenik meg, akkor valószínűleg *le van tiltva* a diagnosztikai bővítmény.

Ha egy adott mérőszám nincs folyamatban van a mintavétel, vagy át, a kívánt gyakoriságot, frissítheti a diagnosztikai konfigurációját.

Ha mindkét előző esetben igaz, majd tekintse át [Windows rendszerű virtuális gép az Azure Diagnostics engedélyezéséhez használja a Powershellt](../../virtual-machines/extensions/diagnostics-windows.md) PowerShell-lel konfigurálja, és frissítse az Azure virtuális gép diagnosztikai bővítmény engedélyezése a metrika kapcsolatban. A cikk emellett tartalmaz egy minta diagnosztikai konfigurációs fájl.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>A Resource Manager-alapú Windows és Linux rendszerű virtuális gépek gazdagép-metrikák
A következő állomásszintű metrikákat projektsablon által kibocsátott alapértelmezett Azure VM-et és a VMSS a Windows- és Linux-példányok. Ezek a metrikák az Azure virtuális gép leírják, de az Azure virtuális gép gazdagépről, nem pedig a Vendég virtuális Gépen telepített ügynökön keresztül gyűjtött. Ezek a metrikák az automatikus skálázási szabályok használhatja.

- [A Resource Manager-alapú Windows és Linux rendszerű virtuális gépek gazdagép-metrikák](../../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachines)
- [Gazdagép metrikáinak Resource Manager-alapú Windows és Linux rendszerű Virtuálisgép-méretezési csoportok](../../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>Resource Manager-alapú Windows virtuális gépek vendég operációs rendszer metrikák
Az Azure-beli virtuális gép létrehozásakor a diagnosztika engedélyezve van, a diagnosztikai bővítmény használatával. A diagnosztikai bővítmény belül a virtuális gép átveszi a metrikákat egy készletét bocsát ki. Ez azt jelenti, hogy az automatikus méretezés mérőszámok, amelyek nem kerülnek naplózásra alapértelmezés szerint minden is.

A mérőszámok listája a következő parancsot a PowerShell használatával is létrehozhat.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

A következő metrikák riasztást hozhat létre:

| Metrika neve | Unit (Egység) |
| --- | --- |
| \Processor(_Total)\% Processor Time |Százalék |
| \Processor(_Total)\% módú használatának aránya |Százalék |
| \Processor(_Total)\% felhasználói idő |Százalék |
| \Processor információkat (_teljes) \Processor gyakorisága |Darabszám |
| \System\Processes |Darabszám |
| \Process (aránya _teljes) \Thread száma |Darabszám |
| \Process (aránya _teljes) \Handle száma |Darabszám |
| \Memory\% előjegyzett memória kihasználtsága) |Százalék |
| \Memory\Available Bytes |Bájt |
| \Memory\Committed bájtok |Bájt |
| \Memory\Commit korlát |Bájt |
| \Memory\Pool mérete (bájt) |Bájt |
| \Memory\Pool nem lapozható készlet mérete |Bájt |
| \PhysicalDisk(_Total)\% lemez idő |Százalék |
| \PhysicalDisk(_Total)\% lemez olvasási idő |Százalék |
| \PhysicalDisk(_Total)\% lemezre írási ideje |Százalék |
| \PhysicalDisk(_Total)\Disk Transfers/sec |Egység/s |
| Lemezolvasások/mp (%) (_Total) \PhysicalDisk \Disk |Egység/s |
| Lemezírások/mp (%) (_Total) \PhysicalDisk \Disk |Egység/s |
| \PhysicalDisk (aránya _teljes) \Disk bájt/mp |Bájt/s |
| Olvasási bájt/mp (%) (_Total) \PhysicalDisk \Disk |Bájt/s |
| \PhysicalDisk (aránya _teljes) \Disk zapsané Bajty/s |Bájt/s |
| \Avg \PhysicalDisk (aránya _teljes). Lemezvárólista hossza |Darabszám |
| \Avg \PhysicalDisk (aránya _teljes). Olvasási Lemezvárólista hossza |Darabszám |
| \Avg \PhysicalDisk (aránya _teljes). Írási Lemezvárólista hossza |Darabszám |
| \LogicalDisk(_Total)\% szabad terület |Százalék |
| \LogicalDisk (aránya _teljes) \Free (MB) |Darabszám |

### <a name="guest-os-metrics-linux-vms"></a>Linux rendszerű virtuális gépek vendég operációs rendszer metrikák
Ha egy virtuális Gépet hoz létre az Azure-ban, diagnosztika alapértelmezés szerint engedélyezve van a diagnosztikai bővítmény használatával.

A mérőszámok listája a következő parancsot a PowerShell használatával is létrehozhat.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 A következő metrikák riasztást hozhat létre:

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

## <a name="commonly-used-web-server-farm-metrics"></a>A gyakran használt webes (Serverová Farma) metrikák
Automatikus méretezés, például a Http-várólista hossza gyakran web server metrikák alapján is elvégezheti. Ez a metrika neve **HttpQueueLength**.  A következő szakaszban azok rendelkezésre álló kiszolgálói farm (webalkalmazások) metrikákat.

### <a name="web-apps-metrics"></a>Web Apps-metrikák
A Web Apps-mérőszámok listája a következő parancsot a PowerShell használatával is létrehozhat.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Riasztás, vagy méretezheti metrikák alapján.

| Metrika neve | Unit (Egység) |
| --- | --- |
| CpuPercentage |Százalék |
| MemoryPercentage |Százalék |
| DiskQueueLength |Darabszám |
| HttpQueueLength |Darabszám |
| BytesReceived |Bájt |
| BytesSent |Bájt |

## <a name="commonly-used-storage-metrics"></a>A gyakran használt Storage metrics
Storage-üzenetsor hossza, ami a storage-üzenetsorban lévő üzenetek száma szerint méretezheti. Tárolási üzenetsor hossza speciális metrika, és a küszöbérték példányonként üzenetek száma. Például ha két példány, és ha a küszöb értéke 100, skálázás esetén az üzenetsorban lévő üzenetek teljes száma 200. Amely lehet, példányonként 100 üzenetek, 120 és 80 vagy bármely egyéb kombinációjáig, amely legfeljebb 200 vagy több ad hozzá.

Ez a beállítás konfigurálása az Azure Portalon a **beállítások** panelen. A Virtuálisgép-méretezési csoportok használata a Resource Manager-sablon automatikus skálázási beállítás frissítheti *metricName* , *ApproximateMessageCount* , és adja át az Azonosítót, a tárolási üzenetsor  *metricResourceUri*.

Ha például a klasszikus Tárfiókot az automatikus skálázási beállítás metricTrigger műveletekre:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

(Nem klasszikus) storage-fiókok a metricTrigger műveletekre:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>A gyakran használt Service Bus-metrikák
A Service Bus-üzenetsor hossza, ami a Service Bus-üzenetsorban lévő üzenetek száma szerint méretezheti. A Service Bus-üzenetsor hossza speciális metrika, és a küszöbérték példányonként üzenetek száma. Például ha két példány, és ha a küszöb értéke 100, skálázás esetén az üzenetsorban lévő üzenetek teljes száma 200. Amely lehet, példányonként 100 üzenetek, 120 és 80 vagy bármely egyéb kombinációjáig, amely legfeljebb 200 vagy több ad hozzá.

A Virtuálisgép-méretezési csoportok használata a Resource Manager-sablon automatikus skálázási beállítás frissítheti *metricName* , *ApproximateMessageCount* , és adja át az Azonosítót, a tárolási üzenetsor  *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> A Service Bus erőforrás-csoport fogalma nem létezik, de Azure Resource Manager létrehoz egy alapértelmezett erőforráscsoportba régiónként. Az erőforráscsoport általában a "Default - ServiceBus-[régió]" formátumban van. Például: "Alapértelmezett-ServiceBus-EastUS", 'Alapértelmezett-ServiceBus-WestUS', "Alapértelmezett-ServiceBus-Kelet-Ausztrália" stb.
>
>
