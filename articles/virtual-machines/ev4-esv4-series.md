---
title: Ev4 és Esv4 sorozat – Azure Virtual Machines
description: A Ev4 és a Esv4 sorozatú virtuális gépek specifikációi.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: b8a6e1013c131778bacb11bd885b9280fb6c6077
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182173"
---
# <a name="ev4-and-esv4-series"></a>Ev4 és Esv4 sorozat

A Ev4 és a Esv4 sorozat az Intel &reg; Xeon &reg; Platinum 8272CL (Cascade Lake) processzorokon fut egy Hyper-threaded konfigurációban, ideális megoldás a nagy mennyiségű, nagyvállalati szintű vállalati alkalmazásokhoz és szolgáltatásokhoz akár a RAM-504GiB. A szolgáltatás a teljes Turbo órajel 3,4 GHz-es folyamatos elérését is tartalmazza.

> [!NOTE]
> A gyakori kérdésekért tekintse meg a  [helyi Temp Disk nélküli Azure](azure-vms-no-temp-disk.md)-beli virtuálisgép-méreteket.

## <a name="ev4-series"></a>Ev4 sorozat

A Ev4-sorozat méretei az Intel Xeon &reg; Platinum 8272CL (Cascade-tó) futnak. A Ev4 sorozat példányai ideálisak a nagyvállalati igényű vállalati alkalmazásokhoz. Az Ev4 sorozatú virtuális gépek Intel &reg; Hyper-Threading technológiával rendelkeznek.

A távoli adatlemezek tárterületét a rendszer külön számlázza a virtuális gépektől. A Premium Storage-lemezek használatához használja a Esv4-méreteket. A Esv4-méretek díjszabása és számlázási mérőszámai azonosak a Ev4 sorozattal.

ACU: 195 – 210

Premium Storage: nem támogatott

Premium Storage gyorsítótárazás: nem támogatott

Élő áttelepítés: támogatott

Memória megőrzésének frissítései: támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma|Várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|
| Standard_E2_v4  | 2 | 16   | Csak a távtároló | 4 | 2|1000  |
| Standard_E4_v4  | 4 | 32  | Csak a távtároló | 8 | 2|2000  |
| Standard_E8_v4  | 8 | 64 | Csak a távtároló | 16 | 4|4000 |
| Standard_E16_v4 | 16 | 128 | Csak a távtároló | 32 | 8|8000 |
| Standard_E20_v4 | 20 | 160 | Csak a távtároló | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | Csak a távtároló | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | Csak a távtároló | 32 | 8|24000 |
| Standard_E64_v4 | 64 | 504 | Csak a távtároló | 32| 8|30000 |


## <a name="esv4-series"></a>Esv4 sorozat

A Esv4-sorozat méretei az Intel &reg; Xeon &reg; Platinum 8272CL (Cascade-tó) futnak. A Esv4 sorozat példányai ideálisak a nagyvállalati igényű vállalati alkalmazásokhoz. Az Evs4 sorozatú virtuális gépek Intel &reg; Hyper-Threading technológiával rendelkeznek. A távoli adatlemezek tárterületét a rendszer külön számlázza a virtuális gépektől.

ACU: 195-210

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

Élő áttelepítés: támogatott

Memória megőrzésének frissítései: támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Adatlemezek max. száma | Gyorsítótár nélküli lemez maximális átviteli sebessége: IOPS/MBps | Hálózati adapterek maximális száma|Várt hálózati sávszélesség (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | Csak a távtároló | 4 | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | Csak a távtároló | 8 | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | Csak a távtároló | 16 | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | Csak a távtároló | 32 | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | Csak a távtároló | 32 | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | Csak a távtároló | 32 | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | Csak a távtároló | 32 | 76800/1152 | 8|24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| Csak a távtároló | 32 | 80000/1200 | 8|30000 |

<sup>1</sup> [korlátozott méretű alapméret érhető el](./constrained-vcpu.md).

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Egyéb méretek és információk

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

Árképzési kalkulátor: [árképzési kalkulátor](https://azure.microsoft.com/pricing/calculator/)

További információ a lemezek típusairól: [lemezek típusai](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>További lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
