---
title: HB-sorozat
description: A HB sorozatú virtuális gépek specifikációi.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 34fbc0683ac39429193f488f3ade289996a67fb4
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87291148"
---
# <a name="hb-series"></a>HB-sorozat

A HB sorozatú virtuális gépek a memória-sávszélesség által vezérelt alkalmazások számára vannak optimalizálva, mint például a Fluid Dynamics, a explicit véges elemek elemzése és az időjárási modellezés. A HB-beli virtuális gépeken 60 AMD EPYC 7551 processzor-mag, 4 GB RAM/CPU mag, egyidejű többszálúség nélkül. A HB virtuális gépek akár 260 GB/s memóriát is biztosítanak a memória sávszélességéhez.

ACU: 199-216

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

Élő áttelepítés: nem támogatott

Memória-megőrzési frissítések: nem támogatott

| Méret | vCPU | Processzor | Memória (GB) | Memória sávszélessége GB/s | Alapszintű CPU-gyakoriság (GHz) | Teljes magok gyakorisága (GHz, csúcs) | Egymagos gyakoriság (GHz, csúcs) | RDMA teljesítmény (GB/s) | MPI-támogatás | Ideiglenes tárterület (GB) | Adatlemezek max. száma | Ethernet hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2,0 | 2.55 | 2.55 | 100 | Mind | 700 | 4 | 1 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.