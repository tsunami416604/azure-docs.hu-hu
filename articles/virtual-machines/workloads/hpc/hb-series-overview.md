---
title: A HB sorozatú virtuális gépek áttekintése – Azure Virtual Machines | Microsoft Docs
description: Ismerje meg az Azure-beli HB sorozatú virtuális gépek előzetes verziójának támogatását.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "67707775"
---
# <a name="hb-series-virtual-machines-overview"></a>A HB sorozatú virtuális gépek áttekintése

A nagy teljesítményű számítási (HPC-) alkalmazások teljesítményének maximalizálása az AMD-EPYC az átgondolt megközelítéssel, a memória helyi és a feldolgozásával kapcsolatos eljárás szükséges. Az alábbiakban felvázoljuk az AMD EPYC architektúráját és az Azure-ban a HPC-alkalmazások megvalósítását. A "pNUMA" kifejezést fogjuk használni egy fizikai NUMA-tartományra, és a "vNUMA" kifejezésre hivatkozva egy virtualizált NUMA-tartományra.

Fizikailag a HB sorozat 2 * 32-Core EPYC 7551 processzor, összesen 64 fizikai maggal. Ezek a 64-es magok 16 pNUMA tartományra vannak osztva (szoftvercsatornán 8), amelyek mindegyike négy mag és más néven "CPU-komplex" (vagy "CCX"). Minden CCX saját L3-gyorsítótárral rendelkezik, ami azt szemlélteti, hogy az operációs rendszer egy pNUMA/vNUMA határt fog látni. A szomszédos CCXs pár két fizikai DRAM-csatornához (32 GB, HB-sorozatú kiszolgálókon) érhető el.

Ha biztosítani szeretné, hogy az Azure hypervisor a virtuális gép beavatkozása nélkül működjön, fenntartjuk a fizikai pNUMA tartomány 0 (az első CCX). Ezután hozzárendeljük a virtuális gép 1-15-as pNUMA-tartományát (a fennmaradó CCX-egységeket). A virtuális gép a következőket fogja látni:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60`magok száma virtuális gépenként

Maga a virtuális gép nem tudja, hogy a pNUMA 0 nem lett megadva. A virtuális gép a pNUMA 1-15-as vNUMA 0-14-et ismeri, 7 vNUMA pedig a VNUMA 1 vSocket 0 és 8 VSocket. Míg ez aszimmetrikus, az operációs rendszernek rendesen kell elindulnia és működnie. Az útmutató későbbi részében megtanítjuk, hogyan futtathatnak MPI-alkalmazásokat ezen az aszimmetrikus NUMA-elrendezésen.

A folyamat rögzítése a HB sorozatú virtuális gépeken fog működni, mivel a mögöttes szilíciumot a vendég virtuális géphez tesszük elérhetővé. Erősen ajánlott az optimális teljesítmény és konzisztencia érdekében feldolgozni a rögzítést.

További információ az [AMD EPYC architektúráról](https://bit.ly/2Epv3kC) és a LinkedIn [többplatformos architektúráról](https://bit.ly/2GpQIMb) . További részletekért tekintse meg a [HPC hangolási útmutató az AMD EPYC processzorokhoz](https://bit.ly/2T3AWZ9)című témakört.

Az alábbi ábrán az Azure Hypervisorhoz és a HB sorozatú virtuális géphez fenntartott magok elkülönítése látható.

![Az Azure hypervisor és a HB sorozatú virtuális gép számára fenntartott magok elkülönítése](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Hardverspecifikációk

| HW-specifikációk                | HB sorozatú virtuális gép                     |
|----------------------------------|----------------------------------|
| Cores                            | 60 (SMT letiltva)                |
| CPU                              | AMD EPYC 7551 *                   |
| CPU-gyakoriság (nem AVX)          | ~ 2,55 GHz (egyetlen és minden mag)   |
| Memory (Memória)                           | 4 GB/mag (240 összesen)            |
| Helyi lemez                       | 700 GB NVMe                      |
| InfiniBand                       | 100 GB EDR Mellanox ConnectX-5 * * |
| Network (Hálózat)                          | 50 GB Ethernet (40 GB használható) Azure második Gen SmartNIC * * * |

## <a name="software-specifications"></a>Szoftverek specifikációi

| SW-specifikációk           |HB sorozatú virtuális gép           |
|-----------------------------|-----------------------|
| MPI-feladatok maximális mérete            | 6000 mag (100 virtuálisgép-méretezési csoportok) 12000 magok (200 virtuálisgép-méretezési készletek)  |
| MPI-támogatás                 | MVAPICH2, OpenMPI, MPICH, platform MPI, Intel MPI  |
| További keretrendszerek       | Egyesített kommunikáció X, libfabric, PGAS |
| Azure Storage-támogatás       | STD + prémium (max. 4 lemez) |
| Operációs rendszer támogatása a CSATOLÓJÁHOZ nincs SR RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Azure CycleCloud-támogatás    | Igen                         |
| Azure Batch támogatás         | Igen                         |

## <a name="next-steps"></a>Következő lépések

* További információ az Azure-beli [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) és [Windows rendszerhez](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) készült HPC VM-méretekről.

* További információ az Azure-beli [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) szolgáltatásról.
