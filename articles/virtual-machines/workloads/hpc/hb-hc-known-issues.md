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
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 6316bcc91bb381facb4f77b2d8dbd8b22f9ed387
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660095"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>A H- és N-sorozatú virtuális gépek ismert problémái

Ez a cikk a [H-sorozatú](../../sizes-hpc.md) és [N sorozatú](../../sizes-gpu.md) virtuális gépek használatakor leggyakrabban előforduló problémákat és megoldásokat ismerteti.

## <a name="dram-on-hb-series"></a>DRAM on HB sorozat

A HB sorozatú virtuális gépek jelenleg csak 228 GB RAM-ot tudnak kiszolgálni a vendég virtuális gépeknek. Ennek az az oka, hogy az Azure hypervisor ismert korlátozása miatt a lapok nem rendelhetők hozzá a vendég virtuális géphez fenntartott AMD CCX (NUMA-tartományok) helyi DRAM-hoz.

## <a name="accelerated-networking"></a>Gyorsított hálózatkezelés

Az Azure gyorsított hálózatkezelés jelenleg nincs engedélyezve, de az előzetes verzió ideje alatt is halad. Ha ezt a funkciót támogatja, értesítjük az ügyfeleket.

## <a name="qp0-access-restriction"></a>qp0 hozzáférési korlátozás

Ha meg szeretné akadályozni, hogy az alacsony szintű hardveres hozzáférés biztonsági réseket eredményezhet, a várólista-párok 0 nem érhetőek el a vendég virtuális gépek számára. Ez csak a ConnectX-5 NIC felügyeletéhez kapcsolódó műveleteket befolyásolja, és bizonyos InfiniBand-diagnosztika, például a ibdiagnet, de nem végfelhasználói alkalmazások futtatására is hatással van.

## <a name="gss-proxy"></a>GSS proxy

A GSS proxy egy ismert hibával rendelkezik a CentOS/RHEL 7,5-ben, amely az NFS-sel való használat során jelentős teljesítmény-és reagálási szankcióként is megnyilvánulhat. Ezt a következővel lehet enyhíteni:

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>Gyorsítótár tisztítása

A HPC-rendszereken gyakran hasznos a memória megtisztítása a feladatok befejeződése után, mielőtt a következő felhasználó hozzá lett rendelve ugyanahhoz a csomóponthoz. A Linuxon futó alkalmazások futtatása után előfordulhat, hogy a rendelkezésre álló memória csökkenti a puffer memóriájának növekedését, és nem futtat alkalmazásokat.

![A parancssor képernyőképe](./media/known-issues/cache-cleaning-1.png)

`numactl -H`A (z) használatával megtudhatja, hogy a memória mely NUMAnode (ek) ba van pufferelt (valószínűleg az összes). A Linux rendszerben a felhasználók háromféleképpen tudják megtisztítani a gyorsítótárat, hogy a pufferelt vagy gyorsítótárazott memóriát "ingyenes" értékre állítsa vissza. A root vagy a sudo engedélyekkel kell rendelkeznie.

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


## <a name="infiniband-driver-installation-on-infiniband-enabled-n-series-vm-sizes"></a>InfiniBand-illesztőprogram telepítése a InfiniBand-ben engedélyezett N sorozatú virtuálisgép-méretek

NC24r_v3 és ND40r_v2 az SR-IOV engedélyezve van, miközben a NC24r és a NC24r_v2 nem engedélyezett az SR-IOV. Néhány részlet a bifurkációs [itt](../../sizes-hpc.md#rdma-capable-instances).
A InfiniBand (IB) konfigurálható az SR-IOV-kompatibilis virtuálisgép-méretekben a OFED-illesztőprogramokkal, míg a nem SR-IOV virtuálisgép-méretekhez ND-illesztőprogramok szükségesek. Ez az IB-támogatás megfelelően elérhető a [CentOS-HPC-VMIs](configure.md). Az Ubuntu esetében tekintse meg a OFED és az ND-illesztőprogramok telepítésének [utasításait](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351) a [docs](enable-infiniband.md#vm-images-with-infiniband-drivers)részben leírtak szerint.


## <a name="next-steps"></a>További lépések

- Tekintse át a [HB-sorozat áttekintését](hb-series-overview.md) és a [HC-sorozat áttekintését](hc-series-overview.md) , amelyből megismerheti a számítási feladatok optimális konfigurálását a teljesítmény és a méretezhetőség érdekében.
- Olvassa el a legújabb bejelentéseket és néhány HPC-példát, valamint az eredményeket az [Azure számítási technikai Közösség blogjában](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti áttekintését lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.
