---
title: HB-sorozat - Azure virtuális gépek
description: A HB sorozatú virtuális gépekre vonatkozó előírások.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 600f10e81742e9bb66c800b747fd7b2dc062754d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164831"
---
# <a name="hb-series"></a>HB-sorozat

A HB sorozatú virtuális gépek a memória sávszélessége által vezérelt alkalmazásokhoz vannak optimalizálva, például a folyadékdinamikához, az explicit véges elemek elemzéséhez és az időjárás modellezéséhez. A HB virtuális gépek 60 AMD EPYC 7551 processzormagot, 4 GB RAM-ot cpu magonként és egyidejű többszálas működés nélkül tartalmaznak. A HB virtuális gép akár 260 GB/s memóriasávszélességet is biztosít.

ACU: 199-216

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

Élő áttelepítés: Nem támogatott

Memóriamegőrzési frissítések: Nem támogatott

| Méret | vCPU | Processzor | Memória (GB) | Memória sávszélessége GB/s | Alap CPU-frekvencia (GHz) | All-cores frekvencia (GHz, csúcs) | Egymagos frekvencia (GHz, csúcs) | RDMA teljesítmény (Gb/s) | MPI-támogatás | Ideiglenes tárterület (GB) | Adatlemezek max. száma | Maximális Ethernet hálózati adapterek |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Összes | 700 | 4 | 1 |

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