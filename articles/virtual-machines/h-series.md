---
title: H-sorozat – Azure Virtual Machines
description: A H-sorozatú virtuális gépek specifikációi.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: e9f876f3d20af01867283f550590b3af23dec662
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/07/2020
ms.locfileid: "87926620"
---
# <a name="h-series"></a>H-sorozat

A H-sorozatú virtuális gépeket nagy CPU-gyakorisággal vagy nagy memóriával rendelkező, alapvető követelményekkel rendelkező alkalmazások számára optimalizáltuk. A H-sorozatú virtuális gépek 8 vagy 16 Intel Xeon E5 2667 v3 processzorral rendelkeznek, CPU Core-onként akár 14 GB RAM-mal, és nem feleznie. A H-sorozat 56 GB/s Mellanox FDR InfiniBand a konzisztens RDMA teljesítményének nem blokkoló FAT-konfigurációjában. A H-sorozatú virtuális gépek támogatják az Intel MPI 5. x és az MS-MPI használatát.

ACU: 290–300

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

Élő áttelepítés: nem támogatott

Memória-megőrzési frissítések: nem támogatott

| Méret | vCPU | Processzor | Memória (GB) | Memória sávszélessége GB/s | Alapszintű CPU-gyakoriság (GHz) | Teljes magok gyakorisága (GHz, csúcs) | Egymagos gyakoriság (GHz, csúcs) | RDMA teljesítmény (GB/s) | MPI-támogatás | Ideiglenes tárterület (GB) | Adatlemezek max. száma | Lemezek max. teljesítménye: IOPS | Ethernet hálózati adapterek maximális száma |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8   | 8  | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32x500 | 2 |
| Standard_H16  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |
| Standard_H8m  | 8  | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 32x500 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |
| Standard_H16r <sup>1</sup>  | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 64x500 | 4 |

<sup>1</sup> az MPI-alkalmazások esetében a dedikált RDMA háttérrendszer-hálózatot a FDR InfiniBand hálózata engedélyezte.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

> [!NOTE]
> A [RDMA-kompatibilis virtuális gépek](sizes-hpc.md#rdma-capable-instances)között a H-sorozat nem-SR-IOV engedélyezve. Ezért a támogatott virtuálisgép- [rendszerképek](./workloads/hpc/configure.md#vm-images), a [InfiniBand-illesztőprogram](./workloads/hpc/enable-infiniband.md) követelményei és a támogatott MPI- [kódtárak](./workloads/hpc/setup-mpi.md) eltérnek az SR-IOV-kompatibilis virtuális gépektől.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

- További információ a [virtuális gépek konfigurálásáról, a](./workloads/hpc/configure.md) [InfiniBand engedélyezéséről](./workloads/hpc/enable-infiniband.md), az [MPI beállításáról](./workloads/hpc/setup-mpi.md) és az Azure-hoz készült HPC-alkalmazások optimalizálásáról a [HPC-munkaterhelések](./workloads/hpc/overview.md)esetében.
- Olvassa el a legújabb bejelentéseket és néhány HPC-példát, valamint az eredményeket az [Azure számítási technikai Közösség blogjában](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti áttekintését lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.
- További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
