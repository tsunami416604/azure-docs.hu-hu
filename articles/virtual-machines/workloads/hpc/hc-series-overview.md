---
title: A HC sorozatú virtuális gépek áttekintése – Azure Virtual Machines | Microsoft Docs
description: Ismerje meg az Azure-beli HC sorozatú virtuális gépek előzetes verziójának támogatását.
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 672175d5bb92c8ab6dc347ece5ad6b14d8232ef4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96024078"
---
# <a name="hc-series-virtual-machine-overview"></a>A HC sorozatú virtuális gépek áttekintése

A HPC-alkalmazások teljesítményének maximalizálása az Intel Xeon skálázható processzorok esetében átgondolt megközelítést igényel az új architektúrán való elhelyezés feldolgozásához. Itt az Azure HC-sorozatú virtuális gépeken a HPC-alkalmazások megvalósítását vázoljuk. A "pNUMA" kifejezést fogjuk használni egy fizikai NUMA-tartományra, és a "vNUMA" kifejezésre hivatkozva egy virtualizált NUMA-tartományra. Hasonlóképpen a "pCore" kifejezést fogjuk használni a fizikai CPU-magok és a "virtuális mag" kifejezésre, hogy a virtualizált CPU-magokra hivatkozzon.

Fizikailag a [HC sorozatú](../../hc-series.md) kiszolgáló 2 * 24 Magos Intel Xeon Platinum 8168 processzorral rendelkezik, összesen 48 fizikai maggal. Mindegyik CPU egyetlen pNUMA tartomány, és a hat csatornához is rendelkezik egyesített hozzáféréssel. Az Intel Xeon Platinum processzorok egy 4x-es nagyobb L2 gyorsítótárral rendelkeznek, mint az előző generációkban (256 KB/Core – > 1 MB/mag), miközben az előző Intel CPU-hoz képest az L3 gyorsítótárat is csökkentik (2,5 MB/Core-> 1,375 MB/Core).

A fenti topológia a HC-sorozat hypervisor-konfigurációját is végrehajtja. Ha az Azure Hypervisort úgy kívánja biztosítani, hogy a virtuális gép beavatkozása nélkül működjön, a 0-1-es és a 24-25-es pCores (azaz az első 2 pCores) fenntartjuk a helyet. Ezután a pNUMA-tartományokat a virtuális géphez rendeli hozzá a többi magot. Így a virtuális gép a következőket fogja látni:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` magok száma virtuális gépenként

A virtuális gépnek nincs tudomása arról, hogy a pCores 0-1 és a 24-25 nem adta meg. Így minden vNUMA elérhetővé válik, mintha natívan 22 maggal rendelkezett volna.

Az Intel Xeon Platinum, Gold és Silver processzorok a CPU-szoftvercsatornán belüli és kívüli kommunikációhoz is bevezetnek egy on-Die 2D Mesh hálózatot. Erősen ajánlott az optimális teljesítmény és konzisztencia érdekében feldolgozni a rögzítést. A folyamat rögzítése a HC sorozatú virtuális gépeken fog működni, mivel a mögöttes szilícium a vendég virtuális géphez van kitéve.

Az alábbi ábrán az Azure Hypervisort és a HC-sorozatú virtuális gép számára fenntartott magok elkülönítése látható.

![Az Azure hypervisor és a HC sorozatú virtuális gép számára fenntartott magok elkülönítése](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Hardverspecifikációk

| Hardver specifikációi          | HC sorozatú virtuális gép                     |
|----------------------------------|----------------------------------|
| Cores                            | 44 (HT letiltva)                 |
| CPU                              | Intel Xeon Platinum 8168         |
| CPU-gyakoriság (nem AVX)          | 3,7 GHz (Single Core), 2.7-3.4 GHz (minden mag) |
| Memória                           | 8 GB/mag (352 összesen)            |
| Helyi lemez                       | 700 GB SSD                       |
| InfiniBand                       | 100 GB EDR Mellanox ConnectX-5   |
| Network (Hálózat)                          | 50 GB Ethernet (40 GB használható) Azure második Gen SmartNIC    |

## <a name="software-specifications"></a>Szoftverek specifikációi

| Szoftverek specifikációi     |HC sorozatú virtuális gép           |
|-----------------------------|-----------------------|
| MPI-feladatok maximális mérete            | 13200 mag (300 virtuális gép egyetlen virtuálisgép-méretezési készletben, singlePlacementGroup = true)  |
| MPI-támogatás                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, platform MPI  |
| További keretrendszerek       | Egyesített kommunikáció X, libfabric, PGAS |
| Azure Storage-támogatás       | Standard és prémium szintű lemezek (legfeljebb 4 lemez) |
| Operációs rendszer támogatása a CSATOLÓJÁHOZ nincs SR RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Orchestrator-támogatás        | CycleCloud, köteg  |

## <a name="next-steps"></a>További lépések

- További információ az [Intel Xeon SP architektúráról](https://bit.ly/2RCYkiE).
- Olvassa el a legújabb bejelentéseket és néhány HPC-példát, valamint az eredményeket az [Azure számítási technikai Közösség blogjában](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- A HPC-munkaterhelések futtatásának magasabb szintű építészeti áttekintését lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.
