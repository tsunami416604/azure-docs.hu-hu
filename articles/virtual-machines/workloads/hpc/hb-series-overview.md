---
title: HB sorozatú virtuális gépek áttekintése – Azure Virtual Machines |} A Microsoft Docs
description: Ismerje meg az előzetes verziós támogatást az Azure-ban HB-sorozatú virtuális gépek méretét.
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
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707775"
---
# <a name="hb-series-virtual-machines-overview"></a>HB-sorozatú virtual machines – áttekintés

Nagy teljesítményű számítási (HPC-) alkalmazás teljesítményének AMD EPYC a lehető legnagyobb átgondolt megközelítést memória helye és a folyamat elhelyezési van szükség. Az alábbiakban azt szerkezeti AMD EPYC architektúráját és az implementáció, az Azure-ban a HPC-alkalmazások. Az előfizetési időszak "pNUMA" használjuk egy fizikai NUMA-tartomány és a "vNUMA" hivatkozik egy virtuális NUMA-tartomány meg.

Fizikailag, a HB-sorozat következő 2 * 32 magos EPYC 7551 processzorok 64 fizikai magok száma összesen. Ezek 64 magos 16 pNUMA tartományok (8 száma), amelyek mindegyike négy magot osztva, és a "Processzor összetett" (vagy a "CCX") néven ismert. Minden egyes CCX rendelkezik a saját L3-gyorsítótárat, amely hogyan fogják látni az operációs rendszer pNUMA/vNUMA határ. Szomszédos CCXs párjai közös fizikai DRAM-(DRAM HB sorozat kiszolgálók 32 GB) két csatornákon való hozzáférést.

Ahhoz, hogy a hely számára az Azure hipervizor nem avatkozik bele a virtuális gép működéséhez, fenntartjuk fizikai pNUMA tartomány 0 (az első CCX). Ezután hozzárendeljük pNUMA tartomány 1 – 15 (a többi CCX egység) a virtuális gép számára. A virtuális gép jelenik meg:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` magok száma a virtuális gép

A virtuális gép, magát, nem ismert, hogy pNUMA 0 nem lett megadva hozzá. A virtuális gép, a 0. és 8 vSocket vNUMA vSocket 1 a 7 vNUMA a 0 – 14., vNUMA pNUMA 1 – 15 értelmezése. Bár ez aszimmetrikus, az operációs rendszer kell rendszerindító és megfelelően működik. Az útmutató későbbi részében hogy hogyan érdemes MPI-alkalmazások futtathatók az aszimmetrikus NUMA elrendezés utasíthatja.

Folyamat rögzítés működnek a HB-sorozat virtuális gépei, mert elérhetővé tesszük az alapul szolgáló, szilícium-, hogy a Vendég virtuális Gépen. Javasoljuk, hogy az optimális teljesítmény és a konzisztencia rögzítés folyamat.

A részletek [AMD EPYC architektúra](https://bit.ly/2Epv3kC) és [több lapka architektúrák](https://bit.ly/2GpQIMb) a Linkedinen. Részletesebb információkért lásd: a [HPC teljesítményhangolási útmutatóból AMD EPYC processzor](https://bit.ly/2T3AWZ9).

Az alábbi ábrán látható a feladatkörök magok fenntartott Azure-Hipervizorra, illetve a HB-sorozatú virtuális gépek számára.

![Az Azure-Hipervizorra, illetve HB-sorozatú virtuális gépek számára lefoglalt magok elkülönítése](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Hardverspecifikációk

| Hardver specifikációk                | HB-sorozatú virtuális gépek                     |
|----------------------------------|----------------------------------|
| Processzormagok                            | 60 (SMT le van tiltva)                |
| CPU                              | AMD EPYC 7551 *                   |
| CPU gyakorisága (nem AVX)          | ~2.55 GHz-es (egyetlen + összes mag)   |
| Memory (Memória)                           | 4 GB és mag (összesen 240)            |
| Helyi lemez                       | 700 GB NVMe                      |
| Infiniband                       | 100 GB-os EDR Mellanox ConnectX-5 ** |
| Network (Hálózat)                          | 50 GB-os Ethernet (40 Gb használható) Azure második általános SmartNIC x |

## <a name="software-specifications"></a>Szoftverfrissítési leírások

| Szoftveres specifikációk           |HB-sorozatú virtuális gépek           |
|-----------------------------|-----------------------|
| Maximális MPI feladatok mérete            | 6000 mag (100 virtuálisgép-méretezési csoportok) 12000 mag (200 virtuálisgép-méretezési csoportok)  |
| MPI-támogatás                 | MVAPICH2, OpenMPI, MPICH, MPI, az Intel MPI Platform  |
| További keretrendszerek       | Unified Communication X, libfabric, PGAS |
| Az Azure Storage-támogatás       | Standard és prémium (max. 4 lemez) |
| PORTPROFIL rdma-t az operációs rendszer támogatása   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Az Azure CycleCloud-támogatás    | Igen                         |
| Az Azure Batch-támogatás         | Igen                         |

## <a name="next-steps"></a>További lépések

* További tudnivalók a HPC virtuális gépek méretei [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) és [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) az Azure-ban.

* Tudjon meg többet [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) az Azure-ban.
