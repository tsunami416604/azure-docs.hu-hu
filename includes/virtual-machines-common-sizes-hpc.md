---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 006a44e42ed209b6f0e614b92f97e43ec30b99ef
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75467706"
---
Az Azure HPC-optimalizált virtuális gépek (VM-EK) úgy vannak kialakítva, hogy vezetői szintű teljesítményt, MPI-skálázhatóságot és költséghatékonyságot nyújtsanak számos valós alkalmazás számára.
 
## <a name="infiniband-networking-for-large-scale-hpc"></a>InfiniBand hálózatkezelés nagy méretű HPC-hoz
A HBv2 virtuális gépek 200 GB/s Mellanox HDR-InfiniBand rendelkeznek, míg a HB és a HC virtuális gépek 100 GB/s Mellanox EDR InfiniBand rendelkeznek. Ezen virtuálisgép-típusok mindegyike egy nem blokkoló FAT-fában csatlakozik az optimalizált és konzisztens RDMA teljesítmény érdekében.

A HBv2 virtuális gépek támogatják az adaptív útválasztást és a dinamikus csatlakoztatott átvitelt (a DCT a standard RC és UD átvitelek esetében). Ezek a funkciók növelik az alkalmazások teljesítményét, méretezhetőségét és konzisztenciáját, és használata erősen ajánlott.  

A HBv2, a HB és a HC virtuális gépek támogatják a szabványos Mellanox-/OFED-illesztőprogramokat. Így az összes RDMA-művelet és MPI-típus támogatott. Ezen virtuálisgép-típusok mindegyike támogatja az MPI-együttesek hardveres kiszervezését is az alkalmazások nagyobb teljesítményének növelése érdekében.
 
Az eredeti H-sorozatú virtuális gépek 56 GB/s Mellanox FDR InfiniBand rendelkeznek. A H-sorozatú InfiniBand felület kihasználása érdekében az ügyfeleknek az Azure piactéren az ehhez a virtuálisgép-típushoz tartozó, hivatalosan támogatott rendszerképeket kell használniuk. 


## <a name="hbv2-series"></a>HBv2 sorozat
A HBv2 sorozatú virtuális gépek a memória-sávszélességgel vezérelt alkalmazások számára vannak optimalizálva, mint például a Fluid Dynamics, a véges elemek elemzése és a tározó szimulálása. A HBv2 virtuális gépek szolgáltatás 120 AMD EPYC 7742 processzor-mag, 4 GB RAM/CPU mag, és nem egyidejű többszálú. Minden HBv2-alapú virtuális gép akár 340 GB/s memóriát, valamint akár 4 teraFLOPS FP64-számítást biztosít. 

Premium Storage: támogatott

| Méret | vCPU | Processzor | Memória (GB) | Memória sávszélessége GB/s | Alapszintű CPU-gyakoriság (GHz) | Teljes magok gyakorisága (GHz, csúcs) | Egymagos gyakoriság (GHz, csúcs) | RDMA teljesítmény (GB/s) | MPI-támogatás | Ideiglenes tárterület (GB) | Adatlemezek max. száma | Ethernet hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs | 120 | AMD EPYC 7742 | 480 | 350 | 2.45 | 2.45 | 3.4 | 200 | Mind | 480 + 960 | 8 | 1 |

<br>

## <a name="hb-series"></a>HB sorozat
A HB sorozatú virtuális gépek azokra az alkalmazásokra vannak optimalizálva, amelyeknek a memória sávszélessége a legfontosabb, például a folyadékdinamika, az explicit végeselem-elemzés és az időjárás-modellezés területén. A HB-beli virtuális gépeken 60 AMD EPYC 7551 processzor-mag, 4 GB RAM/CPU mag, egyidejű többszálúség nélkül. A HB virtuális gépek akár 260 GB/s memóriát is biztosítanak a memória sávszélességéhez.  

ACU: 199-216

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

| Méret | vCPU | Processzor | Memória (GiB) | Memória sávszélessége GiB/s | Alapszintű CPU-gyakoriság (GHz) | Teljes magok gyakorisága (GHz, csúcs) | Egymagos gyakoriság (GHz, csúcs) | RDMA teljesítmény (GiB/s) | MPI-támogatás | Ideiglenes tároló (GiB) | Adatlemezek max. száma | Ethernet hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Mind | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>HC sorozat
A HC sorozatú virtuális gépek a sűrű számítások által vezérelt alkalmazások számára vannak optimalizálva, mint például az implicit, véges elemek elemzése, a molekuláris dinamika és a számítási kémia. A HC virtuális gépek 44 Intel Xeon Platinum 8168-as processzormagot és processzormagonként 8 GB RAM-ot tartalmaznak, és nem nyújtanak hiperszálkezelést. Az Intel Xeon Platinum platform támogatja az Intel olyan szoftverek gazdag ökoszisztémáját, mint az Intel Math kernel Library. 

ACU: 297-315

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott


| Méret | vCPU | Processzor | Memória (GiB) | Memória sávszélessége GiB/s | Alapszintű CPU-gyakoriság (GHz) | Teljes magok gyakorisága (GHz, csúcs) | Egymagos gyakoriság (GHz, csúcs) | RDMA teljesítmény (GiB/s) | MPI-támogatás | Ideiglenes tároló (GiB) | Adatlemezek max. száma | Ethernet hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2,7 | 3.4 | 3.7 | 100 | Mind | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>H-sorozat
A H-sorozatú virtuális gépeket nagy CPU-gyakorisággal vagy nagy memóriával rendelkező, alapvető követelményekkel rendelkező alkalmazások számára optimalizáltuk. A H-sorozatú virtuális gépek 8 vagy 16 Intel Xeon E5 2667 v3 processzorral rendelkeznek, CPU Core-onként akár 14 GB RAM-mal, és nem feleznie. A H sorozatú virtuális gépek akár 80 GB/s memóriát is biztosítanak a memória sávszélességének eléréséhez.

ACU: 290–300

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

| Méret | vCPU | Processzor | Memória (GiB) | Memória sávszélessége GiB/s | Alapszintű CPU-gyakoriság (GHz) | Teljes magok gyakorisága (GHz, csúcs) | Egymagos gyakoriság (GHz, csúcs) | RDMA teljesítmény (GiB/s) | MPI-támogatás | Ideiglenes tároló (GiB) | Adatlemezek max. száma | Ethernet hálózati adapterek maximális száma |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 | 8 | Intel Xeon E5 2667 v3 | 56 | 40 | 3,2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16 | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3,6 |  - | Intel 5. x, MS-MPI | 2000 | 64 | 4 |
| Standard_H8m | 8 | Intel Xeon E5 2667 v3 | 112 | 40 | 3,2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 1000 | 32 | 2 |
| Standard_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3,6 | - | Intel 5. x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3,6 | 56 | Intel 5. x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> az MPI-alkalmazások esetében a dedikált RDMA háttérrendszer-hálózatot a FDR InfiniBand hálózata engedélyezte.

<br>
