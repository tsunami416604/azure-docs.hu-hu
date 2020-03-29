---
title: Ismert problémák a HB-sorozatú és HC-sorozatú virtuális gépekkel – Azure virtuális gépek | Microsoft dokumentumok
description: Ismerje meg a HB sorozatú virtuálisgépek azure-beli méreteivel kapcsolatos ismert problémákat.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/07/2019
ms.author: amverma
ms.openlocfilehash: 8d4b57fb2fee3849e102868c86fe3cab465fc70d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707784"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>A HB és a HC sorozatú virtuális gépek ismert problémái

Ez a cikk a HB-sorozatú és HC-sorozatú virtuális gépek használata esetén a leggyakoribb problémákat és megoldásokat tartalmazza.

## <a name="dram-on-hb-series"></a>DRAM a HB sorozatban

A HB sorozatú virtuális gépek jelenleg csak 228 GB RAM-ot tudnak elérhetővé tenni a vendég virtuális gépeknek. Ez annak köszönhető, hogy az Azure hypervisor ismert korlátozása megakadályozza, hogy az amd CCX helyi DRAM-hoz (NUMA-tartományok) hozzárendelve a vendég virtuális gép számára.

## <a name="accelerated-networking"></a>Gyorsított hálózatkezelés

Az Azure Accelerated Networking jelenleg nincs engedélyezve, de az előzetes verzió időszakának előrehaladtával. Értesítjük az ügyfeleket, ha ez a funkció támogatott.

## <a name="qp0-access-restriction"></a>qp0 hozzáférés korlátozása

A biztonsági réseket okozó alacsony szintű hardverhozzáférés elkerülése érdekében a 0 várólistapár nem érhető el a vendég virtuális gépek számára. Ez csak a ConnectX-5 hálózati adapter adminisztrációjával és bizonyos InfiniBand-diagnosztikák, például az ibdiagnet futtatásával kapcsolatos műveletekre lehet hatással, de magukat a végfelhasználói alkalmazásokat nem.

## <a name="ud-transport"></a>UD-átvitel

Indításkor a HB- és HC-sorozat nem támogatja a dinamikusan csatlakoztatott átvitelt (DCT). A DCT támogatása idővel végrehajtásra kerül. A reliable connection (RC) és a megbízhatatlan Datagram (UD) átvitelek támogatottak.

## <a name="gss-proxy"></a>GSS-proxy

GSS Proxy egy ismert hiba CentOS / RHEL 7.5, hogy a nyilvánvaló, mint egy jelentős teljesítmény és érzékenység büntetést, ha használják nfs. Ez a következőkkel mérsékelhető:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Gyorsítótár-tisztítás

HPC-rendszereken gyakran hasznos a memória törlése a feladat befejezése után, mielőtt a következő felhasználó hozatna ugyanazt a csomópontot. Az alkalmazások Linux on futtatása után előfordulhat, hogy a rendelkezésre álló memória csökken, miközben a puffermemória növekszik, annak ellenére, hogy nem futtat semmilyen alkalmazást.

![A parancssor képernyőképe](./media/known-issues/cache-cleaning-1.png)

A `numactl -H` használata megmutatja, hogy a memória melyik NUMAnode(i)val van pufferelve (valószínűleg az összes). A Linux, a felhasználók tisztítsa meg a gyorsítótárak három módon vissza pufferelt vagy gyorsítótárazott memória "szabad". Root-nak kell lenned, vagy sudo engedélyekkel kell rendelkezned.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![A parancssor képernyőképe](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Kernel figyelmeztetések

A következő kernel figyelmeztető üzenetek jelenhetnek meg, amikor Linux alatt indít egy HB sorozatú virtuális gépindítást.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```

Ezt a figyelmeztetést figyelmen kívül hagyhatja. Ez az Azure hipervizor ismert korlátozásának köszönhető, amelyet az idő múlásával kezelnek.

## <a name="next-steps"></a>További lépések

További információ a [nagy teljesítményű azure-beli számítástechnikáról.](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/)
