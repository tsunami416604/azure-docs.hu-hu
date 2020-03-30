---
title: NDv2 sorozat - Azure virtuális gépek
description: Az NDv2 sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 8a2d275501816dd504130b255b73a752c5615f0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247282"
---
# <a name="updated-ndv2-series"></a>Frissítve: NDv2-series

Az NDv2 sorozatú virtuális gép a GPU-család új kiegészítése, amelyet a legigényesebb GPU-gyorsítású AI, gépi tanulás, szimuláció és HPC-munkaterhelések igényeinek megfelelően terveztek.

Az NDv2-t 8 NVIDIA Tesla V100 NVLINK-hez csatlakoztatott GPU hajtja, amelyek mindegyike 32 GB GPU memóriával rendelkezik. Minden NDv2 virtuális gép 40 nem HyperThreaded Intel Xeon Platinum 8168 (Skylake) maggal és 672 GiB rendszermemóriával is rendelkezik.

Az NDv2 példányok kiváló teljesítményt nyújtanak a CuDA GPU-ra optimalizált számítási kernelek, valamint a GPU-gyorsítást támogató GPU-gyorsítást támogató számos AI, ML és analitikai eszköz felhasználásával, mint például a TensorFlow, a Pytorch, a Caffe, a RAPIDS és más Keretek.

Kritikusan, az NDv2 épül mind a számításilag intenzív scale-up (kihasználva 8 GPU-k virtuális gépenként) és a kibővített (több virtuális gép együtt dolgozik) számítási feladatok. Az NDv2 sorozat mostantól támogatja a 100 gigabites InfiniBand EDR háttérhálózatot, hasonlóan a HPC VM HB sorozatához, hogy lehetővé tegye a nagy teljesítményű fürtözést a párhuzamos forgatókönyvekhez, beleértve az AI és a ML elosztott képzését. Ez a háttérhálózat támogatja az összes főbb InfiniBand protokollt, beleértve az NVIDIA NCCL2 kódtárai által alkalmazott protokollokat is, lehetővé téve a GPU-k zökkenőmentes fürtözését.

> Ha engedélyezi az [InfiniBand használatát](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) a ND40rs_v2 virtuális gépen, használja a 4.7-1.0.1 Mellanox OFED illesztőprogramot.
>
> A megnövekedett GPU-memória miatt az új ND40rs_v2 virtuális gép a [2.](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) 
>
> Megjegyzés: A GPU-nként16 GB memóriát tartalmazó ND40s_v2 már nem érhető el előnézetre, és a frissített ND40rs_v2 felváltotta.

<br>

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

Élő áttelepítés: Nem támogatott

Memóriamegőrzési frissítések: Nem támogatott

InfiniBand: Támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárolás (SSD): GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS/MBps | Maximális hálózati sávszélesség | Hálózati adapterek maximális száma |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000 / 800 | 24000 Mbps | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

Az Azure N sorozatú virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az NVIDIA GPU-illesztőprogramokat.

Az [NVIDIA GPU illesztőprogram-bővítmény](./extensions/hpccompute-gpu-linux.md) megfelelő NVIDIA CUDA vagy GRID illesztőprogramokat telepít egy N sorozatú Virtuális gépre. Telepítse vagy kezelje a bővítményt az Azure Portalon vagy az olyan eszközökkel, például az Azure PowerShell vagy az Azure Resource Manager-sablonok használatával, mint például az Azure PowerShell vagy az Azure Resource Manager-sablonok. A virtuálisgép-bővítményekről az [Azure virtuálisgép-bővítmények és -szolgáltatások](./extensions/overview.md)című témakörben talál általános tudnivalókat.

Ha úgy dönt, hogy manuálisan telepíti az NVIDIA GPU-illesztőprogramokat, olvassa el [az N sorozatú GPU-illesztőprogram linuxos beállítását.](./linux/n-series-driver-setup.md)

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy [az Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.
