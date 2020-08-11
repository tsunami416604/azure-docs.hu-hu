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
ms.openlocfilehash: ab0205f05fc1d58157f4363dcc73564f3d4786b2
ms.sourcegitcommit: d8b8768d62672e9c287a04f2578383d0eb857950
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/11/2020
ms.locfileid: "88068311"
---
# <a name="ncast4_v3-series"></a>NCasT4_v3 sorozat 

Az NCasT4_v3 sorozatú virtuális gépeket [NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU-k és AMD EPYC 7V12 (Róma) processzorok működtetik. A virtuális gépek legfeljebb 4 NVIDIA T4 GPU-t, 16 GB memóriát, valamint akár 64 nem többszálú AMD EPYC 7V12 (Róma) processzort és 440 GiB rendszermemóriát is igénybe vehetik. Ezek a virtuális gépek ideálisak a CUDA-, TensorFlow-, Pytorch-, Cafe-és egyéb keretrendszereket használó, valamint az NVIDIA GRID Technology-t használó grafikus számítási feladatok futtatására.

<br>

ACU: 230-260

Premium Storage: támogatott

Premium Storage gyorsítótárazás: támogatott

Élő áttelepítés: nem támogatott

Memória-megőrzési frissítések: nem támogatott

| Méret | vCPU | Memória: GiB | Ideiglenes tárterület (SSD) GiB | GPU | GPU memória: GiB | Adatlemezek max. száma | Hálózati adapterek maximális száma |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 |
| Standard_NV8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4  |
| Standard_NV16as_T4_v3 |16 |110 |360 | 16 | 8 | 32 | 8  |
| Standard_NV64as_T4_v3 |64 |440 |2880 | 64 | 16 | 32 | 8  |


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

## <a name="next-steps"></a>További lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
