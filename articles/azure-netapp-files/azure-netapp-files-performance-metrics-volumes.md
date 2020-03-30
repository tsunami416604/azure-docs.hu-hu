---
title: Ajánlott teljesítményteszt-tesztek – Azure NetApp-fájlok
description: Ismerje meg a teljesítményteljesítményre vonatkozó teljesítménytesztelési javaslatokat és a metrikákat az Azure NetApp-fájlok használatával.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: 8f354152c23dd7ad0413f27585d724f8070ca003
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551522"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Az Azure NetApp Files teljesítménytesztjével kapcsolatos javaslatok

Ez a cikk teljesítménytesztelési javaslatokat tartalmaz a kötet teljesítményéhez és az Azure NetApp-fájlok használatával kapcsolatos metrikákhoz.

## <a name="overview"></a>Áttekintés

Az Azure NetApp Files kötetek teljesítményjellemzőinek megértéséhez a nyílt forráskódú [FIO](https://github.com/axboe/fio) eszköz segítségével számos számítási feladatot szimulálhat. A FIO linuxos és Windows alapú operációs rendszerekre is telepíthető.  Ez egy kiváló eszköz, hogy egy gyors pillanatképet mind iops és átviteli egy kötet.

### <a name="vm-instance-sizing"></a>Virtuálisgép-példány méretezése

A legjobb eredmény érdekében győződjön meg arról, hogy egy virtuális gép (VM) példányt használ, amely megfelelő méretű a tesztek végrehajtásához. A következő példák Standard_D32s_v3 példányt használnak. A virtuálisgép-példányok méretéről további információt a Windows-alapú Virtuális [gépek Azure-beli virtuális gépek mérete,](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) valamint a Linux-alapú virtuális gépek [Azure-beli virtuális gépeken található méretek című témakörben](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) talál.

### <a name="azure-netapp-files-volume-sizing"></a>Az Azure NetApp Files kötet méretezése

Győződjön meg arról, hogy a várt teljesítményszintnek megfelelő szolgáltatási szintet és kötetkvóta-méretet választotta. További [információ: Az Azure NetApp-fájlok szolgáltatási szintjei.](azure-netapp-files-service-levels.md)

### <a name="virtual-network-vnet-recommendations"></a>Virtuális hálózat (VNet) ajánlásai

A teljesítményteszttesztelést ugyanabban a virtuális hálózatban kell elvégeznie, mint az Azure NetApp-fájlok. Az alábbi példa a következő ajánlást mutatja be:

![Virtuális hálózat javaslatok](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>A FIO telepítése

Fio elérhető bináris formátumban mind a Linux és a Windows. Kövesse a BINÁRIS csomagok [szakaszfio](https://github.com/axboe/fio) telepíteni a platform az Ön által kiválasztott.

## <a name="fio-examples-for-iops"></a>FIO-példák az IOPS-hoz 

Az ebben a szakaszban található FIO-példák a következő beállításokat használják:
* Virtuálisgép-példány mérete: D32s_v3
* Kapacitásmedence szolgáltatási szintje és mérete: Premium / 50 TiB
* A kvóta mennyisége: 48 TiB

A következő példák a FIO véletlenszerű olvasási és írási.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8k blokk mérete 100% véletlenszerű olvasás

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Kimenet: 68k olvasási IOPS jelenik meg

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8k blokk mérete 100%-os véletlenszerű írja

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Kimenet: 73k write IOPS jelenik meg

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>FIO példák a sávszélességre

Ebben a szakaszban a példák a FIO szekvenciális olvasási és írási.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64k blokk mérete 100% szekvenciális olvasás

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Kimenet: 11,8 Gbit/s átviteli teljesítmény

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64k blokk mérete 100%szekvenciális írások

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Kimenet: 12,2 Gbit/s átviteli teljesítmény

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Mennyiségi mutatók

Az Azure NetApp-fájlok teljesítményadatai az Azure Monitor számlálóin keresztül érhetők el. A számlálók az Azure Portalon és a REST API GET-kérelmeken keresztül érhetők el. 

A korábbi adatokat a következő információk hozhatja meg:
* Átlagos olvasási késleltetés 
* Átlagos írási késés 
* IOPS olvasása (átlag)
* ÍrásIOPS (átlag)
* Kötet logikai mérete (átlag)
* Kötet pillanatképének mérete (átlag)

### <a name="using-azure-monitor"></a>Az Azure Monitor használata 

Az Azure NetApp-fájlok számlálóit kötetenként érheti el a Metrikák oldalon, az alábbiak szerint:

![Az Azure Monitor metrikák](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Irányítópultot is létrehozhat az Azure NetApp-fájlokhoz az Azure Monitor for Azure NetApp-fájlokban, ha megkeresi a Metrikák lapot, szűri a NetApp-ot, és megadja a fontos kötetszámlálókat: 

![Az Azure Monitor irányítópultja](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure Monitor API-hozzáférés

Az Azure NetApp-fájlok számlálói REST API-hívások használatával érhetők el. Lásd: [Támogatott metrikák az Azure Monitor: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) a számlálók kapacitáskészletek és kötetek.

A következő példa egy GET URL-címet mutat be a logikai kötetméret megtekintéséhez:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>További lépések

- [Az Azure NetApp Files szolgáltatásszintjei](azure-netapp-files-service-levels.md)
- [Az Azure NetApp Files teljesítményével kapcsolatos mérőszámok](azure-netapp-files-performance-benchmarks.md)