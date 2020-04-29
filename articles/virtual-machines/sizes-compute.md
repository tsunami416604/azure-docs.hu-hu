---
title: Azure-beli virtuálisgép-méretek – optimalizált számítás | Microsoft Docs
description: Felsorolja az Azure-beli virtuális gépekhez elérhető különböző számítási optimalizált méreteket. A vCPU, az adatlemezek és a hálózati adapterek számával, valamint a tárolási teljesítményével és a hálózat sávszélességével kapcsolatos információkat sorolja fel ebben a sorozatban.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: e08d593f641c42f9ad605fda013206e70a34e52f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81269635"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Számítási optimalizált virtuálisgép-méretek

A számítási optimalizált virtuálisgép-méretek magas CPU-memória aránnyal rendelkeznek. Ezek a méretek kiválóan alkalmasak a közepes forgalmú webkiszolgálók, a hálózati berendezések, a kötegelt folyamatok és az alkalmazások kiszolgálói számára. Ez a cikk a vCPU, az adatlemezek és a hálózati adapterek számáról nyújt információt. Emellett a tárolási teljesítményre és a hálózati sávszélességre vonatkozó információkat is tartalmaz ebben a csoportosításban.

A [Fsv2 sorozat](fsv2-series.md) az Intel® Xeon® Platinum 8168 processzoron alapul. A szolgáltatás egy tartós, 3,4 GHz-es, a Turbo órajelét és a 3,7 GHz-es max. Intel® AVX-512 utasítások az Intel skálázható processzorok újdonságai. Ezek az utasítások akár kétszeres teljesítmény-növekedést is biztosítanak a vektoros feldolgozási feladatokhoz egy-és dupla pontosságú lebegőpontos műveletekben. Más szóval a számítási feladatokhoz nagyon gyorsak.

A Fsv2 sorozat a legjobb ár-teljesítmény az Azure-portfólióban, amely az Azure-beli számítási egység (ACU) alapján vCPU.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.
