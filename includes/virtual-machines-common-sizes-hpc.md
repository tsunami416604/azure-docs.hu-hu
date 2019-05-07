---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: f3c1a946afb7f51a1c7543dabae7f2f42ff466be
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65199126"
---
Az Azure H-sorozatú virtuális gépek (VM) úgy tervezték, hogy a vezetői szintű teljesítményt, méretezhetőséget MPI, és optimalizálhatja a költségeket különböző valós HPC számítási feladatok esetében.

A HB sorozatú virtuális gépek azokra az alkalmazásokra vannak optimalizálva, amelyeknek a memória sávszélessége a legfontosabb, például a folyadékdinamika, az explicit végeselem-elemzés és az időjárás-modellezés területén. A HB virtuális gépek 60 AMD EPYC 7551 processzormagot és processzormagonként 4 GB RAM-ot tartalmaznak, és nem nyújtanak hiperszálkezelést. Az AMD EPYC platform több, mint 260 GB/mp memória-sávszélességet biztosít.

Hibrid kapcsolat-sorozat virtuális gépei sűrű számítások, például az implicit végeselemes elemzési, molekuláris dynamics és számítási kémia által vezérelt alkalmazásokhoz vannak optimalizálva. A HC virtuális gépek 44 Intel Xeon Platinum 8168-as processzormagot és processzormagonként 8 GB RAM-ot tartalmaznak, és nem nyújtanak hiperszálkezelést. Az Intel Xeon Platinum platform támogatja a szoftver eszközökkel, például az Intel matematikai Kernel Library széles választéka Intel.

HB és a hibrid kapcsolat virtuális gépek funkciója 100 Gb/s Mellanox EDR InfiniBand egy nem blokkoló fat a fa RDMA egységes teljesítményt konfigurációját. HB és a hibrid kapcsolat virtuális gépeket támogatja a szabványos Mellanox/OFED illesztőprogramok úgy, hogy az összes MPI-típusok és verziók, valamint RDMA-műveletek, valamint támogatottak.

H-sorozatú virtuális gépek magas CPU-használati vagy nagy méretű memóriát biztosít alapvető által vezérelt alkalmazásokhoz vannak optimalizálva. H-sorozatú virtuális gépek szolgáltatás 8 és 16 Intel Xeon E5-2667 v3 processzor-mag, 7 vagy 14 GB RAM, processzormagonként, és nem Hyper-Threading technológia. H-sorozat funkciók 56 Gb/s Mellanox FDR InfiniBand egy nem blokkoló fat a fa RDMA egységes teljesítményt konfigurációját. H-sorozatú virtuális gépek támogatják az Intel MPI 5.x és MS-MPI.

## <a name="hb-series"></a>HB sorozat

Prémium szintű Storage: A Premium Storage támogatja a gyorsítótárazást: Támogatott

| Méret | vCPU | Processzor | Memória (GB) | Memória sávszélesség GB/s | Alapszintű CPU gyakorisága (GHz) | Az összes virtuális mag gyakorisága (GHz-es, csúcsérték) | Egy mag gyakorisága (GHz-es, csúcsérték) | RDMA-teljesítmény (GB/s) | MPI-támogatás | Ideiglenes tárterület (GB) | Adatlemezek max. száma | Max Ethernet NICs |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Összes | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>HC sorozat

Prémium szintű Storage: A Premium Storage támogatja a gyorsítótárazást: Támogatott


| Méret | vCPU | Processzor | Memória (GB) | Memória sávszélesség GB/s | Alapszintű CPU gyakorisága (GHz) | Az összes virtuális mag gyakorisága (GHz-es, csúcsérték) | Egy mag gyakorisága (GHz-es, csúcsérték) | RDMA-teljesítmény (GB/s) | MPI-támogatás | Ideiglenes tárterület (GB) | Adatlemezek max. száma | Max Ethernet NICs |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Összes | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>H-sorozat

ACU: 290-300

Prémium szintű Storage:  Nem támogatott

Prémium szintű Storage gyorsítótárazást:  Nem támogatott

| Méret | vCPU | Processzor | Memória (GB) | Memória sávszélesség GB/s | Alapszintű CPU gyakorisága (GHz) | Az összes virtuális mag gyakorisága (GHz-es, csúcsérték) | Egy mag gyakorisága (GHz-es, csúcsérték) | RDMA-teljesítmény (GB/s) | MPI-támogatás | Ideiglenes tárterület (GB) | Adatlemezek max. száma | Max Ethernet NICs |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5-2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5-2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 |  56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5-2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5-2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard h16r méretű <sup>1</sup> | 16 | Intel Xeon E5-2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard h16mr méretű <sup>1</sup> | 16 | Intel Xeon E5-2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> MPI-alkalmazások, a dedikált RDMA-háttérhálózatot engedélyezve van FDR InfiniBand hálózat, amely ultra rendkívül alacsony késést és magas sávszélességet kínál.

<br>
