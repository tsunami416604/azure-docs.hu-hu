---
title: A kötet teljesítmény és a metrikák az Azure Files-NetApp használatával tesztelési referenciaalap |} A Microsoft Docs
description: Javaslatok kötet teljesítmény- és mérőszámok az Azure Files-NetApp használatával, a teljesítményteszt biztosít.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: b-juche
ms.openlocfilehash: 12ae9e313655924f11799152b5e58b77776c135c
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67478809"
---
# <a name="benchmark-testing-for-volume-performance-and-metrics-using-azure-netapp-files"></a>Kötetteljesítmény és metrikák teljesítménytesztelése az Azure NetApp Files segítségével

Ez a cikk ismerteti a teljesítményteszt javaslatok, kötet teljesítmény- és mérőszámok az Azure Files-NetApp használatával.

## <a name="overview"></a>Áttekintés

Megismerheti az Azure Files-NetApp kötet teljesítménybeli jellemzői, használhatja a nyílt forráskódú eszköz [FIO](https://github.com/axboe/fio) referenciaalapok szimulálása a különböző számítási feladatok egy sorozatát futtatásához. FIO mindkét Linux rendszeren is telepíthető, és a Windows-alapú operációs rendszerek.  Egy kiváló eszköz a pillanatképben IOPS és átviteli sebesség beolvasni egy kötet.

### <a name="vm-instance-sizing"></a>Virtuálisgép-példány méretezése

A legjobb eredmények érdekében győződjön meg arról, hogy a megfelelő méretű a tesztek végrehajtásához (VM) virtuálisgép-példányt használ. Az alábbi példák Standard_D32s_v3 példányt használ. Virtuálisgép-példányméretek kapcsolatos további információkért lásd: [méretek a Windows virtuális gépek az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) Windows-alapú virtuális gépekhez, és [az Azure-ban Linux rendszerű virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Linux-alapú virtuális gépek számára.

### <a name="azure-netapp-files-volume-sizing"></a>Az Azure NetApp Files-kötet méretezése

Győződjön meg arról, hogy a megfelelő szolgáltatási szintet és a kötet kvótájának a várt szinteken kiválasztani. Lásd: [szolgáltatásszintek Azure NetApp fájlok](azure-netapp-files-service-levels.md) további információt.

### <a name="virtual-network-vnet-recommendations"></a>Virtuális hálózat (VNet) vonatkozó javaslatok

Végre kell hajtania a teljesítményteszt tesztelés ugyanabban a vnetben NetApp Azure-fájlok formájában. Az alábbi példa bemutatja a javaslat:

![Virtuális hálózati javaslatok](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>FIO telepítése

A Linux és a Windows bináris formátumú FIO érhető el. Kövesse a bináris csomagok szakaszában [FIO](https://github.com/axboe/fio) telepítéséhez a választott platformra.

## <a name="fio-examples-for-iops"></a>Iops FIO példák 

Ebben a szakaszban szereplő FIO példák használja a következő beállítás:
* Virtuálisgép-példány mérete: D32s_v3
* A kapacitás a készlet szolgáltatási szint és mérete: Prémium szintű 50 Tib-ra
* Kvóta mérete: 48 TiB

Az alábbi példák bemutatják a FIO véletlenszerű olvasási és írási.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: a 8k mérete 100 %-os véletlenszerű olvasást letiltása

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Kimenet: 68-as k olvasási iops-érték jelenik meg

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: a 8k mérete 100 %-os véletlenszerű írási műveletek letiltása

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Kimenet: 73k írási iops-érték jelenik meg

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>A sávszélesség-FIO példák

Ez a szakasz show szekvenciális FIO szereplő példák olvassa be és ír.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64 KB a mérete 100 %-os Szekvenciális olvasási letiltása

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Kimenet: 11,8 Gbit/s átviteli sebesség jelenik meg

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64 KB a mérete 100 %-os szekvenciális írási műveletek letiltása

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Kimenet: 12.2 Gbit/s átviteli sebesség jelenik meg

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Kötet-metrikák

Azure NetApp fájlok ügynökteljesítmény-adatokat az Azure Monitor számlálók érhető el. A számlálók keresztül érhető el az Azure portal és a REST API-GET-kéréseket. 

Előzményadatok a következő információkat tekintheti meg:
* Átlagos olvasási késés 
* Az átlagos írási késés 
* Olvasási IOPS (átlag)
* Az írási IOPS (átlag)
* Kötet logikai mérete (átlag)
* Kötet pillanatkép mérete (átlag)

### <a name="using-azure-monitor"></a>Az Azure Monitor használata 

Alább látható módon érhető el az Azure Files-NetApp számlálók kötetenkénti történik a metrikák lapon:

![Az Azure Monitor-metrikák](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Is létrehozhat egy irányítópultot az Azure monitorban NetApp Azure-fájlok is lesz a metrikák lapon, a NetApp szűréssel és a lényeges a kötet számlálók megadása: 

![Az Azure Monitor irányítópultja](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Az Azure Monitor API-hozzáférés

Elérheti az Azure Files-NetApp számlálók REST API-hívás használatával. Lásd: [az Azure monitorban támogatott mérőszámok: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftnetappnetappaccountscapacitypoolsvolumes) számlálók kapacitás készletek és köteteket.

Az alábbi példa bemutatja egy GET URL-cím logikai kötet méretének megtekintéséhez:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>További lépések

- [Szolgáltatási szintek a NetApp Azure-fájlok](azure-netapp-files-service-levels.md)
- [TELJESÍTMÉNYMÉRÉSEK NetApp Azure-fájlok](azure-netapp-files-performance-benchmarks.md)