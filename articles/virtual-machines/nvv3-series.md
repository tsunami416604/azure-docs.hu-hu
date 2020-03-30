---
title: NVv3-sorozat – Azure virtuális gépek
description: Az NVv3 sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: d74b00a4bade956d3a511a47b0a6b0011b9fb212
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78267426"
---
# <a name="nvv3-series"></a>NVv3 sorozat

Az NVv3 sorozatú virtuális gépeket [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU-k és NVIDIA GRID technológia hajtja Intel E5-2690 v4 (Broadwell) CPU-kkal és Intel Hyper-Threading technológiával. Ezek a virtuális gépek gpu-gyorsuló grafikus alkalmazásokhoz és virtuális asztalokhoz vannak célozva, ahol az ügyfelek meg szeretnék jeleníteni az adataikat, szimulálni szeretnék az eredményeket a CAD megtekintéséhez, a CAD-hez való munkához, vagy a tartalom rendereléséhez és streameléséhez. Emellett ezek a virtuális gépek egyetlen pontosságú számítási feladatokat is futtathatnak, például kódolást és renderelést. Az NVv3 virtuális gépek támogatják a Prémium alapú tárolást, és az elődjében, az NV-sorozathoz képest kétszer annyi rendszermemóriát (RAM) kapnak.  

Az NVv3 példányokban minden GPU GRID licenccel rendelkezik. Ez a licenc rugalmasságot biztosít egy NV-példány egyetlen felhasználó virtuális munkaállomásaként való használatához, vagy 25 egyidejű felhasználó csatlakozhat a virtuális géphez egy virtuális alkalmazásforgatókönyvhöz.

Prémium szintű tárolási gyorsítótárazás: Támogatott

Élő áttelepítés: Nem támogatott

Memóriamegőrzési frissítések: Nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Maximális nem gyorsítótárazott lemezátviteli sebesség: IOPS/MBps | Hálózati adapterek maximális száma | Virtuális munkaállomások | Virtuális alkalmazások |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_NV12s_v3 |12 | 112 | 320  | 1 | 8  | 12 | 20000/200 | 4 | 1 | 25  |
| Standard_NV24s_v3 |24 | 224 | 640  | 2 | 16 | 24 | 40000/400 | 8 | 2 | 50  |
| Standard_NV48s_v3 |48 | 448 | 1280 | 4 | 32 | 32 | 80000/800 | 8 | 4 | 100 |

1 GPU = fél M60 kártya.

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
