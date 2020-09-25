---
title: Az Nvhs T4 v3 sorozata
description: Az Nvhs T4 v3 sorozatú virtuális gépek specifikációi.
services: virtual-machines
ms.subservice: sizes
author: vikancha-MSFT
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 08/10/2020
ms.author: vikancha
ms.openlocfilehash: 17ce5314f58a92158ff4fd187ad0ca46bb14a275
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91320166"
---
# <a name="ncast4_v3-series-in-preview"></a>NCasT4_v3 sorozat (előzetes verzió) 

Az NCasT4_v3 sorozatú virtuális gépeket [NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU-k és AMD EPYC 7V12 (Róma) processzorok működtetik. A virtuális gépek legfeljebb 4 NVIDIA T4 GPU-t, 16 GB memóriát, valamint akár 64 nem többszálú AMD EPYC 7V12 (Róma) processzort és 440 GiB rendszermemóriát is igénybe vehetik. Ezek a virtuális gépek ideálisak a mesterséges intelligencia-szolgáltatások üzembe helyezéséhez – például a felhasználók által generált kérelmek valós idejű meghívásához, vagy interaktív grafikus és vizualizációs számítási feladatokhoz az NVIDIA GRID Driver és Virtual GPU Technology használatával. A CUDA-, TensorRT-, Cafe-, ONNX-és egyéb keretrendszerek, vagy GPU-gyorsított grafikus alkalmazások (az OpenGL és a DirectX) alapján a szabványos GPU számítási feladatok gazdaságosan üzembe helyezhetők a NCasT4_v3 sorozaton belül a felhasználók közelében.

[Beküldheti](https://aka.ms/NCT4v3Preview) az előzetes program részét képező kérelmet.

<br>

ACU: 230-260

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

Élő áttelepítés: nem támogatott

Memória-megőrzési frissítések: nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Támogatott operációs rendszerek és illesztőprogramok

A Windows vagy Linux rendszerű Azure NCasT4_v3 sorozatú virtuális gépek GPU-képességeinek kihasználásához az NVIDIA GPU-illesztőprogramokat kell telepíteni.

Ha manuálisan szeretné telepíteni az NVIDIA GPU-illesztőprogramokat, tekintse meg a következő témakört: [N-sorozat GPU-illesztőprogram beállítása a Windows](./windows/n-series-driver-setup.md) rendszerhez támogatott operációs rendszerek, illesztőprogramok, telepítési és ellenőrzési lépések.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tároptimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
