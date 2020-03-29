---
title: HC-sorozat – Azure virtuális gépek
description: A HC-sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: cc25fb9b21d535ef07bcfae673be48216427b370
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164780"
---
# <a name="hc-series"></a>HC-sorozat

A HC-sorozatú virtuális gépek a sűrű számítással vezérelt alkalmazásokhoz vannak optimalizálva, például implicit véges elemelemzésre, molekuláris dinamikára és számítási kémiára. A HC virtuális gépek 44 Intel Xeon Platinum 8168 processzormagot, cpumagonként 8 GB RAM-ot és hyperthreading nélküli processzormagot tartalmaznak. Az Intel Xeon Platinum platform támogatja az Intel gazdag ökoszisztéma szoftver eszközök, mint például az Intel Math Kernel Library.

ACU: 297-315

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

Élő áttelepítés: Nem támogatott

Memóriamegőrzési frissítések: Nem támogatott

| Méret | vCPU | Processzor | Memória (GB) | Memória sávszélessége GB/s | Alap CPU-frekvencia (GHz) | All-cores frekvencia (GHz, csúcs) | Egymagos frekvencia (GHz, csúcs) | RDMA teljesítmény (Gb/s) | MPI-támogatás | Ideiglenes tárterület (GB) | Adatlemezek max. száma | Maximális Ethernet hálózati adapterek |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Összes | 700 | 4 | 1 |

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