---
title: NC-sorozat - Azure virtuális gépek
description: Az NC sorozatú virtuális gépekre vonatkozó előírások.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: eab7dfe79aa5cdf234c8bc9472387214f7df3563
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164525"
---
# <a name="nc-series"></a>NC sorozat

Az NC sorozatú virtuális gépeket az [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) kártya és az Intel Xeon E5-2690 v3 (Haswell) processzor hajtja. A felhasználók gyorsabban átrághatják az adatokat, ha a CUDA-t energiafeltárási alkalmazásokhoz, összeomlás-szimulációkhoz, sugáralapú rendereléshez, mélytanuláshoz és egyebekhez használják. Az NC24r konfiguráció alacsony késleltetésű, nagy átviteli sebességű hálózati illesztőt biztosít a szorosan összekapcsolt párhuzamos számítási feladatokhoz.

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

Élő áttelepítés: Nem támogatott

Memóriamegőrzési frissítések: Nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma |
|---|---|---|---|---|---|---|---|
| Standard_NC6    | 6  | 56  | 340  | 1 | 12 | 24 | 1 |
| Standard_NC12   | 12 | 112 | 680  | 2 | 24 | 48 | 2 |
| Standard_NC24   | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = fél K80-kártya.

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
