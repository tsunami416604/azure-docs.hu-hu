---
title: HC-sorozatú virtuális gépek áttekintése – Azure virtuális gépek| Microsoft dokumentumok
description: Ismerje meg a HC-sorozatú virtuális gépek azure-beli méretének előzetes támogatását.
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
ms.openlocfilehash: a4cd74c9c85ee7413cde9f0fb4cf3ffb54c9b3d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76906744"
---
# <a name="hc-series-virtual-machine-overview"></a>HC-sorozatú virtuális gépek áttekintése

Az Intel Xeon méretezhető processzorok HPC alkalmazásteljesítményének maximalizálása átgondolt megközelítést igényel az új architektúra elhelyezésének feldolgozásához. Itt ismertetjük annak megvalósítását az Azure HC-sorozatú Virtuális gépeken A HPC-alkalmazásokhoz. A "pNUMA" kifejezést egy fizikai NUMA tartományra, a "vNUMA" szót pedig egy virtualizált NUMA tartományra fogjuk használni. Hasonlóképpen a "pCore" kifejezést használjuk a fizikai CPU magokra való hivatkozáshoz, a "virtuális mag" pedig a virtualizált CPU magokra.

Fizikailag a HC-kiszolgáló 2 * 24 magos Intel Xeon Platinum 8168 PROCESSZOR, összesen 48 fizikai maggal. Minden CPU egyetlen pNUMA tartomány, és egységes hozzáféréssel rendelkezik a DRAM hat csatornájához. Az Intel Xeon Platinum CPU-k 4-szeres nagyobb L2 gyorsítótárral rendelkeznek, mint a korábbi generációk (256 KB/core -> 1 MB/core), miközben csökkentik az L3 gyorsítótárat a korábbi Intel CPU-khoz képest (2,5 MB/core -> 1,375 MB/mag).

A fenti topológia átviszi a HC-sorozatú hipervizor konfigurációt is. Annak érdekében, hogy az Azure hipervizor a virtuális gép beavatkozása nélkül működjön, 0-1 és 24-25 pCore-okat (azaz az első 2 pCore-t foglaljuk le minden szoftvercsatornán). Ezután hozzárendeljük a pNUMA-tartományokat az összes fennmaradó maghoz a virtuális géphez. Így a virtuális gép a következőket fogja látni:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44`magok virtuális gépenként

A virtuális gép nem ismeri, hogy a 0-1 és a 24-25 pM-ek nem kaptak hozzá. Így minden egyes vNUMA-t úgy tárul fel, mintha natívmódon 22 maggal lenne.

Az Intel Xeon Platinum, Gold és Silver CPU-k egy 2D hálós hálózatot is bevezetnek a CPU-aljzaton belüli és kívüli kommunikációhoz. Az optimális teljesítmény és konzisztencia érdekében javasoljuk a folyamatrögzítést. A folyamatrögzítés működik a HC-sorozatú virtuális gépeken, mert az alapul szolgáló szilícium a vendég virtuális gép számára is elérhető. További információt az [Intel Xeon SP architektúra .](https://bit.ly/2RCYkiE)

Az alábbi ábrán az Azure Hypervisor és a HC-sorozatú virtuális gép számára fenntartott magok elkülönítése látható.

![Az Azure Hypervisor és a HC-sorozatú virtuális gép számára fenntartott magok elkülönítése](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Hardverspecifikációk

| Hardverspecifikációk          | HC-sorozatú virtuális gép                     |
|----------------------------------|----------------------------------|
| Cores                            | 44 (HT letiltva)                 |
| CPU                              | Intel Xeon Platinum 8168*        |
| CPU-frekvencia (nem AVX)          | 3,7 GHz (egymagos), 2,7-3,4 GHz (minden mag) |
| Memory (Memória)                           | 8 GB/mag (összesen 352)            |
| Helyi lemez                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb-os EDR Mellanox ConnectX-5** |
| Network (Hálózat)                          | 50 Gb-os Ethernet (40 Gb használható) Azure második Generációs SmartNIC*** |

## <a name="software-specifications"></a>Szoftverspecifikációk

| Szoftverspecifikációk     | HC-sorozatú virtuális gép          |
|-----------------------------|-----------------------|
| Mpi-feladat maximális mérete            | 13200 mag (300 virtuális gép egyetlen VMSS-ben singlePlacementGroup=true) |
| MPI-támogatás                 | MVAPICH2, OpenMPI, MPICH, Platform MPI, Intel MPI  |
| További keretrendszerek       | Egységes kommunikáció X, libfabric, PGAS |
| Azure Storage-támogatás       | STD + Premium (max. 4 lemez) |
| Os támogatás SRIOV RDMA-hoz   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+ |
| Azure CycleCloud támogatás    | Igen                         |
| Azure batch támogatás         | Igen                         |

## <a name="next-steps"></a>További lépések

* További információ a [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) [és](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) Windows HPC virtuálisgép-méreteiről az Azure-ban.

* További információ a [HPC-ről](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) az Azure-ban.
