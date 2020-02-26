---
title: NDv2 sorozat – Azure Virtual Machines
description: A NDv2 sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 6c002838d5a4f515a594d61e5137196c4d391795
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605438"
---
# <a name="updated-ndv2-series-preview"></a>Frissített NDv2 sorozat (előzetes verzió)

A NDv2 sorozatú virtuális gép a GPU-család új, a legigényesebb GPU-gyorsított mesterséges intelligenciához, a gépi tanuláshoz, a szimulációs és a HPC számítási feladatokhoz készült.

A NDv2 8 NVIDIA Tesla V100 NVLINK csatlakoztatott GPU-val rendelkezik, amelyek mindegyike 32 GB GPU memóriával rendelkezik. Az egyes NDv2 virtuális gépekhez 40 nem hiperszálas Intel Xeon Platinum 8168 (Skylake) mag és 672 GiB a rendszermemória.

A NDv2-példányok kiváló teljesítményt nyújtanak a HPC-és AI-munkaterhelésekhez, amelyek a CUDA GPU-val optimalizált számítási kerneleket, valamint a GPU-gyorsítást támogató számos mesterséges intelligencia, ML és analitikai eszközt támogatják, például a TensorFlow, a Pytorch, a Cafe, a RAPIDS és egyéb szolgáltatásokat. keretek.

Kritikus fontosságú, hogy a NDv2 mind a számítási szempontból intenzív felskálázás (8 GPU-s virtuális gép kihasználása), mind a Kibővítés (több virtuális gép a közös munkával együtt) munkaterhelések használatával készült. A NDv2 sorozat mostantól a 100-Gigabit InfiniBand EDR háttér-hálózatkezelést támogatja, hasonlóan a HB-sorozathoz elérhető HPC virtuális gépekhez, így lehetővé teszi a nagy teljesítményű fürtözést párhuzamos forgatókönyvek esetén, beleértve az AI és a ML elosztott képzését is. Ez a háttérrendszer támogatja az összes jelentősebb InfiniBand-protokollt, beleértve az NVIDIA NCCL2-könyvtárainak használatát is, ami lehetővé teszi a GPU-k zökkenőmentes fürtözését.


> [!NOTE]
> Ha [engedélyezi a InfiniBand](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) a ND40rs_v2 virtuális gépen, használja a 4.7-1.0.0.1 Mellanox OFED-illesztőprogramot.
>
> A GPU-memória megnövekedett mérete miatt az új ND40rs_v2 virtuális gépen a [2. generációs virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) és a Piactéri rendszerképek használata szükséges. 
>
> [Regisztráljon, hogy korai hozzáférést Kérjen a NDv2 virtuális gép előzetes verziójához.](https://aka.ms/AzureNDrv2Preview)
>
> Megjegyzés: a GPU memóriával rendelkező ND40s_v2 a már nem érhető el az előzetes verzióhoz, és a frissített ND40rs_v2 felülírt.

<br>

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

InfiniBand: támogatott

| Méret | vCPU | Memória: GiB | Temp Storage (SSD): GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Maximális hálózati sávszélesség | Hálózati adapterek maximális száma |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 16 | 32 | 80000/800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

Az Azure N sorozatú virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az NVIDIA GPU-illesztőprogramokat.

Az [NVIDIA GPU illesztőprogram-bővítmény](/extensions/hpccompute-gpu-windows.md) a megfelelő NVIDIA CUDA-vagy Grid-illesztőprogramokat telepíti egy N sorozatú virtuális gépen. A bővítmény telepítése vagy kezelése a Azure Portal vagy eszközök, például Azure PowerShell vagy Azure Resource Manager sablonok használatával. Tekintse meg az [NVIDIA GPU illesztőprogram-bővítmény dokumentációját](/extensions/hpccompute-gpu-windows.md) a támogatott operációs rendszerekhez és üzembe helyezési lépésekhez. A virtuálisgép-bővítményekkel kapcsolatos általános információkért lásd: [Azure-beli virtuális gépek bővítményei és szolgáltatásai](/extensions/overview.md).

Ha manuálisan telepíti az NVIDIA GPU-illesztőprogramokat, tekintse meg a következőt: [n-sorozat GPU-illesztőprogram beállítása Windows](/windows/n-series-driver-setup.md) vagy [N sorozatú GPU-illesztőhöz Linux](/linux/n-series-driver-setup) rendszeren támogatott operációs rendszerek, illesztőprogramok, telepítési és ellenőrzési lépések.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
