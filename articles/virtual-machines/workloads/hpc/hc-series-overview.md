---
title: Hibrid kapcsolat-sorozatú virtuális gépek áttekintése – Azure Virtual Machines |} A Microsoft Docs
description: Ismerje meg az előzetes verziós támogatást az Azure-ban a hibrid kapcsolat-sorozat Virtuálisgép-méretet.
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
ms.openlocfilehash: 6cdb539846104f70dabf684925685fb062fea8af
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797552"
---
# <a name="hc-series-virtual-machine-overview"></a>Hibrid kapcsolat-sorozatú virtuális gépek – áttekintés

Az Intel Xeon méretezhető processzorok HPC-alkalmazások teljesítményének jelentős szükséges egy folyamat elhelyezési ezen új architektúrával átgondolt megközelítést. Itt hogy szerkezeti implementáció, az Azure hibrid kapcsolat-sorozat virtuális gépei HPC-alkalmazások. Az előfizetési időszak "pNUMA" használjuk egy fizikai NUMA-tartomány és a "vNUMA" hivatkozik egy virtuális NUMA-tartomány meg. Ehhez hasonlóan "pCore" kifejezést használjuk, tekintse meg a fizikai Processzormagok, és hivatkozni kell a "virtuális" mag virtualizált Processzormagok.

Fizikailag, egy hibrid kapcsolat kiszolgálója 2 * 24-core Intel Xeon Platinum 8168 processzorok 48 fizikai magok száma összesen. Minden egyes CPU egyetlen pNUMA tartományhoz, és rendelkezik egyesített DRAM hat csatornákon való hozzáférést. Intel Xeon Platinum processzorokat a szolgáltatás egy előző generáció esetében (256 KB/core-> 1 MB/mag), is csökkenti a korábbi Intel-processzort képest L3-gyorsítótár-nál nagyobb méretű L2 gyorsítótár x 4 (2,5 MB/core 1.375 MB/core->).

A fenti topológia végrehajtott módosítások megjelennek a hibrid kapcsolat sorozat hipervizor konfiguráció is. Ahhoz, hogy a hely számára az Azure hipervizor nem avatkozik bele a virtuális gép működéséhez, fenntartjuk pCores 0 és 1 és 24-25 (azaz az első 2 pCores egyes szoftvercsatorna). Majd hozzárendeljük pNUMA tartományokat az összes többi magok a virtuális géphez. Így a virtuális gép jelenik meg:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` magok száma a virtuális gép

A virtuális gép nem rendelkezik, hogy pCores 0 és 1 és 24-25 nem adott hozzá. Ebből kifolyólag egyes vNUMA érheti el, ha a natív módon 22 magok.

Intel Xeon platina, arany és ezüst processzorok is használ egy 2D háló die a hálózaton belüli kommunikációra és külső a CPU-szoftvercsatorna. Javasoljuk, hogy az optimális teljesítmény és a konzisztencia rögzítés folyamat. A hibrid kapcsolat-sorozat virtuális gépei folyamat rögzítés működnek, mert az alapul szolgáló szilícium üzemállapottal érhető el –, hogy a Vendég virtuális Gépen. További tudnivalókért lásd: [Intel Xeon SP architektúra](https://bit.ly/2RCYkiE).

Az alábbi ábrán látható a feladatkörök magok fenntartott Azure-Hipervizorra, illetve a hibrid kapcsolat-sorozatú virtuális gépek számára.

![Az Azure-Hipervizorra, illetve a hibrid kapcsolat-sorozatú virtuális gépek számára lefoglalt magok elkülönítése](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Hardverspecifikációk

| Hardverek specifikációi          | Hibrid kapcsolat-sorozatú virtuális gépek                     |
|----------------------------------|----------------------------------|
| Processzormagok                            | 40 (HT le van tiltva)                 |
| CPU                              | Intel Xeon Platinum 8168 *        |
| CPU gyakorisága (nem AVX)          | 3,7 GHz (egymagos) 2.7-3.4 GHz-es (az összes mag) |
| Memory (Memória)                           | 8 GB és mag (összesen 352)            |
| Helyi lemez                       | 700 GB NVMe                      |
| Infiniband                       | 100 GB-os EDR Mellanox ConnectX-5 ** |
| Network (Hálózat)                          | 50 GB-os Ethernet (40 Gb használható) Azure második általános SmartNIC x |

## <a name="software-specifications"></a>Szoftverfrissítési leírások

| Szoftverfrissítési leírások     | Hibrid kapcsolat-sorozatú virtuális gépek          |
|-----------------------------|-----------------------|
| Maximális MPI feladatok mérete            | 4400 mag (100 virtual machine scale sets) 8800 mag (200 virtuálisgép-méretezési csoportok) |
| MPI-támogatás                 | MVAPICH2, OpenMPI, MPICH, MPI, az Intel MPI Platform  |
| További keretrendszerek       | Unified Communication X, libfabric, PGAS |
| Az Azure Storage-támogatás       | Standard és prémium (max. 4 lemez) |
| PORTPROFIL rdma-t az operációs rendszer támogatása   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 + |
| Az Azure CycleCloud-támogatás    | Igen                         |
| Az Azure Batch-támogatás         | Igen                         |

## <a name="next-steps"></a>További lépések

* További tudnivalók a HPC virtuális gépek méretei [Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc) és [Windows](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-hpc) az Azure-ban.

* Tudjon meg többet [HPC](https://docs.microsoft.com/azure/architecture/topics/high-performance-computing/) az Azure-ban.
