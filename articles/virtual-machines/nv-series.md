---
title: NV-sorozat – Azure virtuális gépek
description: Az NV-sorozatú virtuális gépek specifikációi.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 7fd9fff87564b790915acf01373c078c2012371e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273869"
---
# <a name="nv-series"></a>NV sorozat

Az NV sorozatú virtuális gépeket [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU-k és NVIDIA GRID technológia hajtja asztali gyorsított alkalmazásokhoz és virtuális asztalokhoz, ahol az ügyfelek képesek az adataik vagy szimulációik megjelenítésére. A felhasználók képesek elképzelni a nagy grafikai igényű munkafolyamataikat az NV-példányokon, hogy kiváló grafikus képességet kapjanak, és emellett egyetlen pontosságú számítási feladatokat, például kódolást és renderelést is futtathatnak. Az NV sorozatú virtuális gépeket Intel Xeon E5-2690 v3 (Haswell) PROCESSZOROK is működtetik.

Az NV-példányokban minden GPU GRID licenccel rendelkezik. Ez a licenc rugalmasságot biztosít egy NV-példány egyetlen felhasználó virtuális munkaállomásaként való használatához, vagy 25 egyidejű felhasználó csatlakozhat a virtuális géphez egy virtuális alkalmazásforgatókönyvhöz.

Prémium szintű tárhely: Nem támogatott

Prémium szintű tárolási gyorsítótárazás: Nem támogatott

Élő áttelepítés: Nem támogatott

Memóriamegőrzési frissítések: Nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma | Virtuális munkaállomások | Virtuális alkalmazások |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

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
