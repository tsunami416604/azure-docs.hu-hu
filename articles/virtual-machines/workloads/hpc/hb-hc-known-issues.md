---
title: A HB-sorozattal és a HC-sorozatú virtuális gépekkel kapcsolatos ismert problémák – Azure Virtual Machines | Microsoft Docs
description: Ismerje meg az Azure-beli HB-sorozatú virtuálisgép-méretek ismert problémáit.
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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/27/2020
ms.locfileid: "67707784"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>A HB és a HC sorozatú virtuális gépek ismert problémái

Ez a cikk a leggyakoribb problémákat és megoldásokat ismerteti a HB és a HC sorozatú virtuális gépek használatakor.

## <a name="dram-on-hb-series"></a>DRAM on HB sorozat

A HB sorozatú virtuális gépek jelenleg csak 228 GB RAM-ot tudnak kiszolgálni a vendég virtuális gépeknek. Ennek az az oka, hogy az Azure hypervisor ismert korlátozása miatt a lapok nem rendelhetők hozzá a vendég virtuális géphez fenntartott AMD CCX (NUMA-tartományok) helyi DRAM-hoz.

## <a name="accelerated-networking"></a>Gyorsított hálózatkezelés

Az Azure gyorsított hálózatkezelés jelenleg nincs engedélyezve, de az előzetes verzió ideje alatt is halad. Ha ezt a funkciót támogatja, értesítjük az ügyfeleket.

## <a name="qp0-access-restriction"></a>qp0 hozzáférési korlátozás

Ha meg szeretné akadályozni, hogy az alacsony szintű hardveres hozzáférés biztonsági réseket eredményezhet, a várólista-párok 0 nem érhetőek el a vendég virtuális gépek számára. Ez csak a ConnectX-5 NIC felügyeletéhez kapcsolódó műveleteket befolyásolja, és bizonyos InfiniBand-diagnosztika, például a ibdiagnet, de nem végfelhasználói alkalmazások futtatására is hatással van.

## <a name="ud-transport"></a>UD szállítás

Indításkor a HB és a HC sorozat nem támogatja a dinamikusan csatlakoztatott átvitelt (DCT). A DCT támogatása idővel fog megvalósulni. A megbízható kapcsolatok (RC) és a megbízhatatlan datagram (UD) átvitele támogatott.

## <a name="gss-proxy"></a>GSS proxy

A GSS proxy egy ismert hibával rendelkezik a CentOS/RHEL 7,5-ben, amely az NFS-sel való használat során jelentős teljesítmény-és reagálási szankcióként is megnyilvánulhat. Ezt a következővel lehet enyhíteni:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Gyorsítótár tisztítása

A HPC-rendszereken gyakran hasznos a memória megtisztítása a feladatok befejeződése után, mielőtt a következő felhasználó hozzá lett rendelve ugyanahhoz a csomóponthoz. A Linuxon futó alkalmazások futtatása után előfordulhat, hogy a rendelkezésre álló memória csökkenti a puffer memóriájának növekedését, és nem futtat alkalmazásokat.

![A parancssor képernyőképe](./media/known-issues/cache-cleaning-1.png)

A `numactl -H` (z) használatával megtudhatja, hogy a memória mely NUMAnode (ek) ba van pufferelt (valószínűleg az összes). A Linux rendszerben a felhasználók háromféleképpen tudják megtisztítani a gyorsítótárat, hogy a pufferelt vagy gyorsítótárazott memóriát "ingyenes" értékre állítsa vissza. A root vagy a sudo engedélyekkel kell rendelkeznie.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![A parancssor képernyőképe](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Kernel-figyelmeztetések

A következő kernel figyelmeztető üzenetek jelenhetnek meg a HB sorozatú virtuális gépek Linux rendszerű indításakor.

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

Ezt a figyelmeztetést figyelmen kívül hagyhatja. Ennek az az oka, hogy az Azure hypervisor egy ismert korlátozása, amely az idő múlásával lesz kezelve.

## <a name="next-steps"></a>További lépések

További információ a [nagy teljesítményű számítástechnikai](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) szolgáltatásokról az Azure-ban.
