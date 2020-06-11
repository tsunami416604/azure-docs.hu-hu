---
title: HBv2 sorozat – Azure Virtual Machines
description: A HBv2 sorozatú virtuális gépek specifikációi.
author: vermagit
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.date: 02/03/2020
ms.author: amverma
ms.openlocfilehash: 730065333fde7431b7018ef0a6e614e12727d7ed
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84673853"
---
# <a name="hbv2-series"></a>HBv2 sorozat

A HBv2 sorozatú virtuális gépek a memória-sávszélességgel vezérelt alkalmazások számára vannak optimalizálva, mint például a Fluid Dynamics, a véges elemek elemzése és a tározó szimulálása. A HBv2 virtuális gépek szolgáltatás 120 AMD EPYC 7742 processzor-mag, 4 GB RAM/CPU mag, és nem egyidejű többszálú. Minden HBv2-alapú virtuális gép akár 340 GB/s memóriát, valamint akár 4 teraFLOPS FP64-számítást biztosít.

Premium Storage: támogatott

Élő áttelepítés: nem támogatott

Memória-megőrzési frissítések: nem támogatott

| Méret | vCPU | Processzor | Memória (GB) | Memória sávszélessége GB/s | Alapszintű CPU-gyakoriság (GHz) | Teljes magok gyakorisága (GHz, csúcs) | Egymagos gyakoriság (GHz, csúcs) | RDMA teljesítmény (GB/s) | MPI-támogatás | Ideiglenes tárterület (GB) | Adatlemezek max. száma | Ethernet hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC 7V12 | 480 | 350 | 2.45 | 3,1 | 3.3 | 200 | Mind | 480 + 960 | 8 | 1 |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.