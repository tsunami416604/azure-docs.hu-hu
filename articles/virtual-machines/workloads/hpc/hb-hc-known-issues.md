---
title: Ismert problémák HB sorozatú és a hibrid kapcsolat sorozatú virtuális gépek - Azure Virtual Machines |} A Microsoft Docs
description: Ismerje meg az Azure-ban HB-sorozat virtuális gépeinek szolgáltatással kapcsolatos ismert problémák.
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
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707784"
---
# <a name="known-issues-with-hb-series-and-hc-series-vms"></a>A HB és a HC sorozatú virtuális gépek ismert problémái

Ez a cikk a leggyakoribb problémákat és megoldásokat nyújt HB sorozatú és a hibrid kapcsolat sorozatú virtuális gépek használata során.

## <a name="dram-on-hb-series"></a>A HB sorozat DRAM

HB sorozatú virtuális gépek jelenleg csak közzéteheti 228 GB RAM Memóriát a Vendég virtuális gépeket. Megakadályozza, hogy a lapok lesz hozzárendelve a helyi DRAM az AMD CCX meg (tartományok NUMA) a Vendég virtuális gép számára lefoglalt okozza az Azure hipervizor egy ismert korlátozás.

## <a name="accelerated-networking"></a>Gyorsított hálózatkezelés

Az Azure gyorsított hálózatkezelés van nincs engedélyezve, de fogja, hogy az előzetes verzió alatt. Ha ez a funkció támogatott ügyfelek értesíteni fogjuk.

## <a name="qp0-access-restriction"></a>qp0 hozzáférési korlátozás

Biztonsági rések, várólista pár is előfordulhat, hogy alacsony szintű hardver hozzáférés elkerülése érdekében 0 nem érhető el a Vendég virtuális gépeket. Ez csak érintheti műveletek általában társított felügyeleti hálózati adapter ConnectX-5 és egyes InfiniBand diagnosztikai például ibdiagnet, de nem magukat a végfelhasználói alkalmazások futtatásához.

## <a name="ud-transport"></a>UD átviteli

Indításkor a HB és a hibrid kapcsolat-sorozat nem támogatja a dinamikus csatlakoztatott átviteli (DCT). Idővel DCT támogatása hajtják végre. Megbízható kapcsolat (RC) és a nem megbízható Hálózathatáron Datagram (UD) átvitel használata támogatott.

## <a name="gss-proxy"></a>GSS Proxy

GSS-Proxy egy ismert hiba, amely jelentős teljesítmény és válaszképesség napján belül pótdíj NFS-SZEL való használatakor is manifest CentOS/RHEL 7.5 tartalmaz. Ez a mérsékelhető a:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>A gyorsítótár törlése

A HPC-rendszerek esetében hasznos gyakran karbantartása a memória, a feladat befejezése után a következő felhasználónak ugyanazon a csomóponton kiosztása előtt. A Linuxos alkalmazások futtatása után előfordulhat, hogy a rendelkezésre álló memória csökkenti a puffer memória növekszik, annak ellenére, hogy nem fut az alkalmazások közben.

![Képernyőkép a parancssor használatával](./media/known-issues/cache-cleaning-1.png)

Használatával `numactl -H` jelennek meg a memória pufferelve van-e az (esetleg minden) melyik NUMAnode(s). A Linux, a felhasználók távolíthatja a gyorsítótárakat három módon való visszatéréshez pufferelt vagy gyorsítótárazza a memóriában "ingyenes". Legfelső szintű vagy sudo engedélyekkel rendelkeznie kell.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![Képernyőkép a parancssor használatával](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>Kernel figyelmeztetések

Amikor Linux HB sorozatú virtuális gép elindítása a következő kernel figyelmeztető üzeneteket jelenhet meg.

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

Ez a figyelmeztetés figyelmen kívül hagyhatja. Az Azure hipervizor, amely az idő múlásával kibocsátásokban megtörténik egy ismert korlátozás miatt nem lehetséges.

## <a name="next-steps"></a>További lépések

Tudjon meg többet [nagy teljesítményű számítási](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) az Azure-ban.
