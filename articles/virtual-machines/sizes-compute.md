---
title: Azure virtuális gépek méretei – Optimalizált számítás | Microsoft dokumentumok
description: Az Azure-ban a virtuális gépekhez elérhető különböző számítási optimalizált méretek. A vCPU-k, adatlemezek és hálózati adapterek számával, valamint a tárolóátviteli és hálózati sávszélességgel kapcsolatos információkat sorolja fel a sorozat méreteihez.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: d709d621341ef14ec158ed5af1c2df4297d66b8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77493630"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Optimalizált virtuális gépméretek számítása

A számítási optimalizált virtuálisgép-méretek nagy processzor-memória aránnyal rendelkeznek. Ezek a méretek közepes forgalmú webkiszolgálók, hálózati készülékek, kötegelt folyamatok és alkalmazáskiszolgálók esetén is megfelelőek. Ez a cikk a vCPU-k, adatlemezek és hálózati adapterek számáról tartalmaz tájékoztatást. A csoportosítás minden egyes méretére vonatkozó anamnézisre vonatkozó információkat is tartalmaz.

Az [Fsv2 sorozat](fsv2-series.md) az Intel® Xeon® Platinum 8168 processzoron alapul. Ez jellegét meghatározza egy tartós minden magtok Turbo órajel -ból 3.4 GHz és egy legfelső határ egymagos turbó frekvencia -ból 3.7 GHz. Intel® AVX-512 utasítások újak az Intel skálázható processzorok. Ezek az utasítások akár kétszeres teljesítménynövekedést biztosítanak a vektoros feldolgozási számítási feladatokhoz mind az egy- és kétszeres pontosságú lebegőpontos műveleteken. Más szóval, ezek nagyon gyors a számítási számítási feladatokhoz.

Alacsonyabb óránkénti listaáron az Fsv2-sorozat a legjobb ár-teljesítmény az Azure-portfólióban az Azure Compute Unit (ACU) vCPU-nként.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy [az Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.