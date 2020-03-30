---
title: ND sorozat – Azure virtuális gépek
description: Az ND sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 163dc0d6ecd4f91273a82b04ce186f7a8df868af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79465056"
---
# <a name="nd-series"></a>ND sorozat

Az ND sorozatú virtuális gépek a mesterséges intelligencia és a Deep Learning-munkaterhelésekhez tervezett GPU-család új kiegészítői. Kiváló teljesítményt nyújtanak a képzéshez és a következtetésekhez. Az ND-példányokat [nvidia Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) GPU-k és Intel Xeon E5-2690 v4 (Broadwell) CPU-k működtetik. Ezek a példányok kiváló teljesítményt nyújtanak az egypontosságú lebegőpontos műveletekhez, a Microsoft Cognitive Toolkit, a TensorFlow, a Caffe és más keretrendszereket használó AI-munkaterhelésekhez. Az ND sorozat sokkal nagyobb GPU memóriaméretet (24 GB) is kínál, amely lehetővé teszi, hogy sokkal nagyobb neurális hálózati modelleket szereljen fel. Az NC-sorozathoz hasonlóan az ND sorozat is másodlagos, alacsony késleltetésű, nagy átviteli sebességű hálózattal rendelkezik az RDMA-n és az InfiniBand-kapcsolaton keresztül, így számos GPU-n keresztül nagy méretű betanítási feladatokat futtathat.

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

Élő áttelepítés: Nem támogatott

Memóriamegőrzési frissítések: Nem támogatott

> [!IMPORTANT]
> Ehhez a virtuális gépsorozathoz a vCPU (core) kvóta régiónként az előfizetésben kezdetben 0 lesz állítva. [VCPU-kvótanövelésének kérése](../azure-supportability/resource-manager-core-quotas-request.md) ehhez a sorozathoz egy [elérhető régióban.](https://azure.microsoft.com/regions/services/)
>
| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Maximális nem gyorsítótárazott lemezátviteli sebesség: IOPS/MBps | Hálózati adapterek maximális száma |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = egy P40 kártya.

*RDMA-kompatibilis

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

Az Azure N sorozatú virtuális gépek GPU-képességeinek kihasználásához telepíteni kell az NVIDIA GPU-illesztőprogramokat.

Az [NVIDIA GPU illesztőprogram-bővítmény](./extensions/hpccompute-gpu-windows.md) megfelelő NVIDIA CUDA vagy GRID illesztőprogramokat telepít egy N sorozatú Virtuális gépre. Telepítse vagy kezelje a bővítményt az Azure Portalon vagy az olyan eszközökkel, például az Azure PowerShell vagy az Azure Resource Manager-sablonok használatával, mint például az Azure PowerShell vagy az Azure Resource Manager-sablonok. A támogatott operációs rendszereket és a telepítési lépéseket az [NVIDIA GPU-illesztőprogram-bővítmény dokumentációjában](./extensions/hpccompute-gpu-windows.md) találja. A virtuálisgép-bővítményekről az [Azure virtuálisgép-bővítmények és -szolgáltatások](./extensions/overview.md)című témakörben talál általános tudnivalókat.

Ha úgy dönt, hogy manuálisan telepíti az NVIDIA GPU-illesztőprogramokat, olvassa el az [N sorozatú GPU-illesztőprogram beállítása Windows](./windows/n-series-driver-setup.md) vagy N [sorozatú GPU-illesztőprogram linuxos beállítását](./linux/n-series-driver-setup.md) a támogatott operációs rendszerek, illesztőprogramok, telepítés és ellenőrzési lépések hez.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy [az Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.
