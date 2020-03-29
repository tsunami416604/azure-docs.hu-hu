---
title: Gyakori mérőszámok automatikus skálázása
description: Ismerje meg, hogy mely metrikákat használják a felhőszolgáltatások, a virtuális gépek és a webalkalmazások automatikus skálázásához.
ms.topic: conceptual
ms.date: 12/6/2016
ms.subservice: autoscale
ms.openlocfilehash: 2c335168683212337876c963a7cfdb441d0ac69a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845561"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Az Azure Monitor automatikus skálázása gyakori metrikák

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az Azure Monitor automatikus skálázás lehetővé teszi, hogy a futó példányok száma fel- vagy leskálázása telemetriai adatok (metrikák) alapján. Ez a dokumentum ismerteti a gyakori metrikákat, amelyeket érdemes lehet használni. Az Azure Portalon kiválaszthatja az erőforrás metrikáját, amely szerint skálázható. Azonban azt is kiválaszthatja a metrika egy másik erőforrás skálázása.

Az Azure Monitor automatikus skálázása csak [a virtuális gép méretezési készleteire,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [a felhőszolgáltatásokra,](https://azure.microsoft.com/services/cloud-services/) [az appszolgáltatásra – webalkalmazásokra](https://azure.microsoft.com/services/app-service/web/)és [az API-kezelési szolgáltatásokra](https://docs.microsoft.com/azure/api-management/api-management-key-concepts)vonatkozik. Más Azure-szolgáltatások különböző skálázási módszereket használnak.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Számítási mérőszámok az Erőforrás-kezelő alapú virtuális gépekhez
Alapértelmezés szerint az Erőforrás-kezelő-alapú virtuális gépek és a virtuálisgép-méretezési készletek alapszintű (állomásszintű) metrikákat bocsátanak ki. Emellett az Azure VM és VMSS diagnosztikai adatgyűjtésének konfigurálásakor az Azure diagnosztikai bővítmény vendégoperációs rendszer teljesítményszámlálókat is bocsát ki (közismert nevén "vendég-operációs rendszer metrikák").  Ezeket a metrikákat automatikus skálázási szabályokban használja.

Az `Get MetricDefinitions` API/PoSH/CLI segítségével megtekintheti a VMSS-erőforrás számára elérhető metrikákat.

Ha virtuálisgép-méretezési csoportokat használ, és nem lát egy adott metrika szerepel, majd valószínűleg *le van tiltva* a diagnosztikai bővítményben.

Ha egy adott metrika nincs mintavételezve vagy átvitele a kívánt gyakorisággal, frissítheti a diagnosztikai konfigurációt.

Ha bármelyik előző eset igaz, tekintse át [a PowerShell használatával, hogy az Azure Diagnostics egy virtuális gépen windows os PowerShell](../../virtual-machines/extensions/diagnostics-windows.md) konfigurálásához és frissítéséhez az Azure VM diagnosztikai bővítmény, hogy a metrika engedélyezéséhez. Ez a cikk egy mintadiagnosztikai konfigurációs fájlt is tartalmaz.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Az Erőforrás-kezelőn alapuló Windows- és Linux-virtuális gépek gazdagép-metrikái
A következő állomásszintű metrikák at alapértelmezés szerint az Azure VM és VMSS a Windows és a Linux-példányok. Ezek a metrikák az Azure virtuális gép, de az Azure virtuális gép gazdagép, nem pedig a vendég virtuális gépre telepített ügynök. Ezeket a metrikákat használhatja az automatikus skálázási szabályokban.

- [Az Erőforrás-kezelőn alapuló Windows- és Linux-virtuális gépek gazdagép-metrikái](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)
- [Az Erőforrás-kezelőn alapuló Windows- és Linux-méretezési készletek gazdamutatói](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-for-resource-manager-based-windows-vms"></a>Vendég operációsrendszer-mérőszámok az Erőforrás-kezelőn alapuló Windows-virtuális gépekhez
Amikor virtuális gép az Azure-ban, diagnosztika engedélyezve van a Diagnosztika bővítmény használatával. A diagnosztikai bővítmény a virtuális gép belsejéből vett metrikák készletét bocsátja ki. Ez azt jelenti, hogy az alapértelmezés szerint nem kibocsátott metrikák automatikus skálázásával is leskálázhatja.

A metrikák listáját a PowerShell következő parancsával hozhatlétre.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

A következő mutatókhoz hozhat létre riasztást:

| Metrikus neve | Unit (Egység) |
| --- | --- |
| \Processor(_Total)\% Processor Time |Százalék |
| \Processzor(_Total)\% Kiemelt idő |Százalék |
| \Processzor(_Total)\% Felhasználói idő |Százalék |
| \Processzoradatok(_Total)\Processzor gyakorisága |Darabszám |
| \Rendszer\Folyamatok |Darabszám |
| \Folyamat(_Total)\Szálak száma |Darabszám |
| \Folyamat(_Total)\Leírók száma |Darabszám |
| \Használatban lévő memóriavéglegesített\% bájtok |Százalék |
| \Memory\Available Bytes |Bájt |
| \Memória\Véglegesített bájtok |Bájt |
| \Memória\Véglegesítési korlát |Bájt |
| \Memória\Lapozható készletbájt |Bájt |
| \Memória\Nem lapozható készlet bájtjai |Bájt |
| \PhysicalDisk(_Total)\% Lemezidő |Százalék |
| \PhysicalDisk(_Total)\% lemez olvasási ideje |Százalék |
| \PhysicalDisk(_Total)\% lemez írási ideje |Százalék |
| \PhysicalDisk(_Total)\Lemezátvitel/mp |CountPerSzekszekundum |
| \PhysicalDisk(_Total)\Lemezolvasás/mp |CountPerSzekszekundum |
| \PhysicalDisk(_Total)\Lemezírás/mp |CountPerSzekszekundum |
| \PhysicalDisk(_Total)\Lemezbájt/mp |BytesPerSecond |
| \PhysicalDisk(_Total)\Lemezolvasási bájt/mp |BytesPerSecond |
| \PhysicalDisk(_Total)\Lemezírási bájtok/mp |BytesPerSecond |
| \PhysicalDisk(_Total)\Átlagos lemezvárólista hossza |Darabszám |
| \PhysicalDisk(_Total)\Átlagos lemezolvasási várólista hossza |Darabszám |
| \PhysicalDisk(_Total)\Átlagos lemezírási várólista hossza |Darabszám |
| \LogicalDisk(_Total)\% szabad terület |Százalék |
| \LogicalDisk(_Total)\Ingyenes megabájt |Darabszám |

### <a name="guest-os-metrics-linux-vms"></a>Vendég operációs rendszer linuxos virtuális gépeket mérőszámokat
Amikor virtuális gép az Azure-ban, diagnosztika alapértelmezés szerint engedélyezve van a Diagnosztikai bővítmény használatával.

A metrikák listáját a PowerShell következő parancsával hozhatlétre.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 A következő mutatókhoz hozhat létre riasztást:

| Metrikus neve | Unit (Egység) |
| --- | --- |
| \Memória\Elérhető memória |Bájt |
| \Memória\PercentAvailableMemory |Százalék |
| \Memória\Használt memória |Bájt |
| \Memória\Százalékkal használt memória |Százalék |
| \Memory\PercentUsedByCache |Százalék |
| \Memória\PagesPerSec |CountPerSzekszekundum |
| \Memória\PagesreadperSec |CountPerSzekszekundum |
| \Memória\PagesWrittenperSec |CountPerSzekszekundum |
| \Memória\Elérhető swap |Bájt |
| \Memória\PercentAvailableSwap |Százalék |
| \Memória\UsedSwap |Bájt |
| \Memória\PercentUsedSwap |Százalék |
| \Processzor\PercentIdleTime |Százalék |
| \Processzor\PercentUserTime |Százalék |
| \Processzor\PercentNiceTime |Százalék |
| \Processzor\PercentPrivilegedTime |Százalék |
| \Processzor\PercentInterruptTime |Százalék |
| \Processzor\PercentDPCTime |Százalék |
| \Processzor\PercentProcessorTime |Százalék |
| \Processzor\PercentIOWaitTime |Százalék |
| \PhysicalDisk\BytesPerSecond |BytesPerSecond |
| \PhysicalDisk\ReadBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\WriteBytesPerSecond |BytesPerSecond |
| \PhysicalDisk\TransfersPerSecond |CountPerSzekszekundum |
| \PhysicalDisk\Olvasásszekunduma |CountPerSzekszekundum |
| \PhysicalDisk\WritesPerSecond |CountPerSzekszekundum |
| \PhysicalDisk\AverageReadTime |Másodperc |
| \PhysicalDisk\AverageWriteTime |Másodperc |
| \PhysicalDisk\AverageTransferTime |Másodperc |
| \PhysicalDisk\AverageDiskQueueLength |Darabszám |
| \NetworkInterface\Bájtátvitel |Bájt |
| \NetworkInterface\Fogadott bájtok |Bájt |
| \NetworkInterface\PacketsTransmitted |Darabszám |
| \NetworkInterface\Fogadott csomagok |Darabszám |
| \NetworkInterface\BájtÖsszes |Bájt |
| \NetworkInterface\TotalRxErrors |Darabszám |
| \NetworkInterface\TotalTxErrors |Darabszám |
| \NetworkInterface\TotalCollisions |Darabszám |

## <a name="commonly-used-app-service-server-farm-metrics"></a>Általánosan használt App Service (Server Farm) mérőszámok
Automatikus skálázást is végrehajthat a közös webkiszolgáló-metrikák, például a Http-várólista hossza alapján. A metrika neve **HttpQueueLength**.  A következő szakasz felsorolja az elérhető kiszolgálófarm (App Service) metrikákat.

### <a name="web-apps-metrics"></a>Webalkalmazások mutatói
A Web Apps-metrikák listáját a PowerShell következő parancsával hozhatja létre.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Ezekre a mutatókra vonatkozó anam-es riasztást vagy méretezést.

| Metrikus neve | Unit (Egység) |
| --- | --- |
| Cpuszázalék |Százalék |
| Memóriaszázalék |Százalék |
| DiskQueueLength |Darabszám |
| HttpQueueLength között |Darabszám |
| Fogadott bájt |Bájt |
| Küldött bájt |Bájt |

## <a name="commonly-used-storage-metrics"></a>Általánosan használt tárolási metrikák
A storage-várólista hossza szerint méretezhető, amely a tárolóvárólistában lévő üzenetek száma. A tárolási várólista hossza egy speciális metrika, és a küszöbérték az üzenetek példányonkénti száma. Ha például két példány van, és a küszöbérték 100-ra van állítva, a méretezés akkor következik be, amikor a várólistában lévő üzenetek teljes száma 200. Ez lehet 100 üzenet példányonként, 120 és 80, vagy bármely más kombináció, amely összeadja a 200 vagy több.

Konfigurálja ezt a beállítást az Azure Portalon a **Beállítások** panelen. Virtuálisgép-méretezési csoportok esetén frissítheti az Erőforrás-kezelő sablon automatikus skálázási beállítását, hogy *a metricName-t* *approximateMessageCount-ként* használja, és adja át a tárolóvárólista azonosítóját *metricResourceUri*néven.

Például egy klasszikus tárfiók az automatikus skálázási beállítás metricTrigger tartalmazná:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

Egy (nem klasszikus) tárfiók esetén a metricTrigger a következőket tartalmazza:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Általánosan használt Service Bus-metrikák
A Service Bus várólista hossza szerint méretezhető, amely a Service Bus várólistában lévő üzenetek száma. A Service Bus várólista hossza egy speciális metrika, és a küszöbérték az üzenetek száma példányonként. Ha például két példány van, és a küszöbérték 100-ra van állítva, a méretezés akkor következik be, amikor a várólistában lévő üzenetek teljes száma 200. Ez lehet 100 üzenet példányonként, 120 és 80, vagy bármely más kombináció, amely összeadja a 200 vagy több.

Virtuálisgép-méretezési csoportok esetén frissítheti az Erőforrás-kezelő sablon automatikus skálázási beállítását, hogy *a metricName-t* *approximateMessageCount-ként* használja, és adja át a tárolóvárólista azonosítóját *metricResourceUri*néven.

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> A Service Bus esetében az erőforráscsoport koncepciója nem létezik, de az Azure Resource Manager régiónként létrehoz egy alapértelmezett erőforráscsoportot. Az erőforráscsoport általában "Default-ServiceBus-[region]" formátumban van. Például a "Default-ServiceBus-EastUS", a "Default-ServiceBus-WestUS", a "Default-ServiceBus-AustraliaEast" stb.
>
>

