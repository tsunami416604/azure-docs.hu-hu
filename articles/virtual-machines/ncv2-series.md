---
title: NCv2 sorozat - Azure virtuális gépek
description: Az NCv2 sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 3643fbabef08d890ce121d41a9bc1eb40c88459d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273917"
---
# <a name="ncv2-series"></a>NCv2 sorozat

Az NCv2 sorozatú virtuális gépeket [NVIDIA Tesla P100](https://www.nvidia.com/data-center/tesla-p100/) GPU-k működtetik. Ezek a GPU-k az NC-sorozat számítási teljesítményének több mint 2-szeresét biztosítják. Az ügyfelek kihasználhatják ezeket a frissített GPU-kat a hagyományos HPC-számítási feladatokhoz, például a tározómodellezéshez, a DNS-szekvenáláshoz, a fehérjeelemzéshez, a Monte Carlo-szimulációkhoz és másokhoz. A GPU-k mellett az NCv2 sorozatú virtuális gépeket Intel Xeon E5-2690 v4 (Broadwell) CPU-k is működtetik.

Az NC24rs v2 konfiguráció alacsony késleltetésű, nagy átviteli sebességű hálózati illesztőt biztosít a szorosan összekapcsolt párhuzamos számítási feladatokhoz.

Prémium szintű tárhely: Támogatott

Prémium szintű tárolási gyorsítótárazás: Támogatott

Élő áttelepítés: Nem támogatott

Memóriamegőrzési frissítések: Nem támogatott

> [!IMPORTANT]
> Ehhez a virtuális gépsorozathoz az előfizetésvCPU-kvótája kezdetben 0-ra van állítva az egyes régiókban. [VCPU-kvótanövelésének kérése](../azure-supportability/resource-manager-core-quotas-request.md) ehhez a sorozathoz egy [elérhető régióban.](https://azure.microsoft.com/regions/services/)
>
| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Maximális nem gyorsítótárazott lemezátviteli sebesség: IOPS/MBps | Hálózati adapterek maximális száma |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v2    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v2   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = egy P100 kártya.

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
