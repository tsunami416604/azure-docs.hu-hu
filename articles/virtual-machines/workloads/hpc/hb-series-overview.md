---
title: HB sorozatú virtuális gépek áttekintése - Azure virtuális gépek | Microsoft dokumentumok
description: Ismerje meg a HB sorozatú virtuális gépek azure-beli méretének előzetes támogatását.
services: virtual-machines
documentationcenter: ''
author: vermagit
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 05/16/2019
ms.author: amverma
ms.openlocfilehash: 62e4d3dbd7357f8c98df3307c1c8fe52cbed1c5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67707775"
---
# <a name="hb-series-virtual-machines-overview"></a>HB sorozatú virtuális gépek áttekintése

Az AMD EPYC nagy teljesítményű számítási (HPC) alkalmazásteljesítményének maximalizálásához átgondolt megközelítési memória-helységre és folyamatelhelyezésre van szükség. Az alábbiakban ismertetjük az AMD EPYC architektúrát és annak megvalósítását az Azure For HPC alkalmazásokban. A "pNUMA" kifejezést egy fizikai NUMA tartományra, a "vNUMA" szót pedig egy virtualizált NUMA tartományra fogjuk használni.

Fizikailag a HB sorozat 2 * 32 magos EPYC 7551 CPU-k, összesen 64 fizikai maggal. Ezek a 64 magok vannak osztva 16 pNUMA domain (8 foglalatonként), amelyek mindegyike négy mag, és az úgynevezett "CPU Complex" (vagy "CCX"). Minden CCX saját L3 gyorsítótárral rendelkezik, így az operációs rendszer egy pNUMA/vNUMA határt fog látni. Egy pár szomszédos CCX-ek megosztják a hozzáférést két fizikai DRAM csatornához (32 GB DRAM a HB-sorozatú szerverekben).

Annak érdekében, hogy az Azure hipervizor a virtuális gép beavatkozása nélkül működjön, fizikai pNUMA domain 0-t (az első CCX-et) foglalunk fenn. Ezután hozzárendeljük a pNUMA tartományokat 1-15 -hez (a fennmaradó CCX egységekhez) a virtuális géphez. A virtuális gép a következőket fogja látni:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`magok virtuális gépenként

A VM maga nem tudja, hogy pNUMA 0 nem kapott rá. A VM a pNUMA 1-15-öt vNUMA 0-14-ként, 7 vNUMA-val a vSocket 0-n és 8 vNUMA-n a vSocket 1-en. Bár ez aszimmetrikus, az operációs rendszernek el kell indulnia és normálisan kell működnie. Az útmutató későbbi részében azt tanítjuk, hogyan lehet a legjobban mpi alkalmazásokat futtatni ezen az aszimmetrikus NUMA elrendezésen.

A folyamatrögzítés működni fog a HB-sorozatú virtuális gépeken, mert az alapul szolgáló szilíciumot a vendég virtuális gépnek tesszük elérhetővé. Az optimális teljesítmény és konzisztencia érdekében javasoljuk a folyamatrögzítést.

További információk az [AMD EPYC architektúráról](https://bit.ly/2Epv3kC) és a [többcsipes architektúrákról](https://bit.ly/2GpQIMb) a LinkedIn-en. További információt az [AMD EPYC processzorok HPC hangolási útmutatójában talál.](https://bit.ly/2T3AWZ9)

Az alábbi ábrán az Azure Hypervisor és a HB sorozatú virtuális gép számára fenntartott magok elkülönítése látható.

![Az Azure Hypervisor és a HB sorozatú virtuális gép számára fenntartott magok elkülönítése](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Hardverspecifikációk

| HW műszaki adatok                | HB sorozatú vm                     |
|----------------------------------|----------------------------------|
| Cores                            | 60 (SMT letiltva)                |
| CPU                              | AMD EPYC 7551*                   |
| CPU-frekvencia (nem AVX)          | ~2.55 GHz (egyetlen + minden mag)   |
| Memory (Memória)                           | 4 GB/mag (összesen 240)            |
| Helyi lemez                       | 700 GB NVMe                      |
| Infiniband                       | 100 Gb-os EDR Mellanox ConnectX-5** |
| Network (Hálózat)                          | 50 Gb-os Ethernet (40 Gb használható) Azure második Generációs SmartNIC*** |

## <a name="software-specifications"></a>Szoftverspecifikációk

| SW műszaki adatok           |HB sorozatú vm           |
|-----------------------------|-----------------------|
| Mpi-feladat maximális mérete            | 6000 mag (100 virtuálisgép-mérlegcsoport) 12000 mag (200 virtuálisgép-méretezési csoport)  |
| MPI-támogatás                 | MVAPICH2, OpenMPI, MPICH, Platform MPI, Intel MPI  |
| További keretrendszerek       | Egységes kommunikáció X, libfabric, PGAS |
| Azure Storage-támogatás       | STD + Premium (max. 4 lemez) |
| Os támogatás SRIOV RDMA-hoz   | CentOS/RHEL 7.6+, SLES 12 SP4+, WinServer 2016+  |
| Azure CycleCloud támogatás    | Igen                         |
| Azure batch támogatás         | Igen                         |

## <a name="next-steps"></a>További lépések

* További információ a [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) [és](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) Windows HPC virtuálisgép-méreteiről az Azure-ban.

* További információ a [HPC-ről](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) az Azure-ban.
