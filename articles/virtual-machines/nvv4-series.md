---
title: NVv4 sorozat - Azure virtuális gépek
description: Az NVv4 sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 0295ed7d44d64fcc1aeb68e1beaa37987b177edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273845"
---
# <a name="nvv4-series"></a>NVv4 sorozat 

Az NVv4 sorozatú virtuális gépeket [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU-k és AMD EPYC 7V12(Róma) CPU-k működtetik. Az NVv4 sorozatú Azure részleges GPU-kkal rendelkező virtuális gépeket vezet be. Válassza ki a megfelelő méretű virtuális gépet a GPU gyorsított grafikus alkalmazásokhoz és virtuális asztalokhoz, amelyek a 2 GiB keretpufferrel rendelkező GPU 1/8-atól indulnak, és 16 GiB keretpufferrel rendelkező teljes GPU-ra. Az NVv4 virtuális gépek jelenleg csak a Windows vendég operációs rendszerét támogatják.

<br>

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

Élő áttelepítés: Nem támogatott

Memóriamegőrzési frissítések: Nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 |

<sup>Az 1</sup> NVv4 sorozatból álló virtuális gépek AMD egyidejű többszálas technológiával rendelkeznek

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

A Windows rendszert futtató Azure NVv4 sorozatú virtuális gépek GPU-funkcióinak kihasználásához telepíteni kell az AMD GPU-illesztőprogramokat.

Az AMD GPU-illesztőprogramok manuális telepítéséhez olvassa el az [N sorozatú AMD GPU-illesztőprogram windowsos beállítását](./windows/n-series-amd-driver-setup.md) a támogatott operációs rendszerekhez, illesztőprogramokhoz, telepítéshez és ellenőrzési lépésekhez.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy [az Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.
