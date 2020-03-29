---
title: HBv2 sorozat - Azure virtuális gépek
description: A HBv2 sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: eea649610ca53ccbb98b5ca361555280dcd3dafe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164797"
---
# <a name="hbv2-series"></a>HBv2 sorozat

A HBv2 sorozatú virtuális gépek a memória sávszélessége által vezérelt alkalmazásokhoz vannak optimalizálva, mint például a folyadékdinamika, a véges elemek elemzése és a tározószimuláció. A HBv2 virtuális gépek 120 AMD EPYC 7742 processzormagot, 4 GB RAM-ot cpu magonként és egyidejű többszálas működés nélkül tartalmaznak. Minden HBv2 virtuális gép akár 340 GB/s memória sávszélességet és akár 4 teraFLOPS FP64-es számítási kapacitást biztosít.

Prémium szintű tárhely: Támogatott

Élő áttelepítés: Nem támogatott

Memóriamegőrzési frissítések: Nem támogatott

| Méret | vCPU | Processzor | Memória (GB) | Memória sávszélessége GB/s | Alap CPU-frekvencia (GHz) | All-cores frekvencia (GHz, csúcs) | Egymagos frekvencia (GHz, csúcs) | RDMA teljesítmény (Gb/s) | MPI-támogatás | Ideiglenes tárterület (GB) | Adatlemezek max. száma | Maximális Ethernet hálózati adapterek |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3,1 | 3.3 | 200 | Összes | 480 + 960 | 8 | 1 |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy [az Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.