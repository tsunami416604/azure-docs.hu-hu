---
title: Azure-beli virtuális gépek méretei – memória | Microsoft Docs
description: Felsorolja az Azure-beli virtuális gépekhez elérhető különböző memória-optimalizált méreteket. A vCPU, adatlemezek és hálózati adapterek számával, valamint az ebben a sorozatban lévő méretek sávszélességével kapcsolatos információkat sorolja fel.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: VM-elkülönítés, elkülönített virtuális gép, elkülönítés, elkülönített
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 26a44cdf3e57508cebb070669a8c1f473a59df8a
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2020
ms.locfileid: "77493526"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Memória-optimalizált virtuális gépek méretei

A memóriára optimalizált virtuálisgép-méretek magas memória-CPU arányt kínálnak, amely kiválóan használható a kapcsolódó adatbázis-kiszolgálók, a közepes és a nagy gyorsítótárak, valamint a memóriabeli elemzések számára. Ez a cikk a vCPU, az adatlemezek és a hálózati adapterek számával, valamint a tárolási teljesítményével és a hálózati sávszélességgel kapcsolatos információkat tartalmaz ebben a csoportosításban.

- A [Dv2 és a DSv2 sorozat](dv2-dsv2-series-memory.md), amely az eredeti D sorozatra mutat, hatékonyabb processzorral rendelkezik. A Dv2 sorozat körülbelül 35%-kal gyorsabb a D sorozatnál. Az Intel® Xeon® 8171M 2,1 GHz-es (Skylake) vagy Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) vagy Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processzorokkal, valamint az Intel Turbo Boost Technology 2,0-mel fut. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.

    A Dv2 és a DSv2 sorozat ideális olyan alkalmazások számára, amelyek gyorsabb vCPU, jobb ideiglenes tárolási teljesítményt vagy nagyobb memóriát igényelnek. Nagyon hatékony kombinációt kínálnak számos nagyvállalati szintű alkalmazáshoz.

- A [Eav4 és a Easv4 sorozat](eav4-easv4-series.md) az AMD 2.35 GHz EPYC<sup>TM</sup> 7452 processzorát egy többszálas konfigurációban, akár 256mb L3 gyorsítótárral, a legtöbb memóriára optimalizált számítási feladatok futtatására szolgáló lehetőségek növelésével teszi elérhetővé. A Eav4-sorozat és a Easv4-sorozat ugyanazokkal a memória-és lemez-konfigurációval rendelkezik, mint a Ev3 & Esv3 sorozat.

- A [Ev3 és Esv3 sorozatú](ev3-esv3-series.md) Intel® Xeon® 8171M 2,1 GHz (Skylake), vagy az Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) processzor egy Hyper-threaded konfigurációban, jobb értékű kiosztást biztosít a legtöbb általános célú számítási feladathoz, és a Ev3 a legtöbb más felhőhöz tartozó általános célú virtuális gépekhez társítja. A memória kibontása megtörtént (7 GiB/vCPU – 8 GiB/vCPU), míg a lemez-és hálózati korlátokat a rendszer a Hyper-Threading szolgáltatáshoz való áttéréssel összehangolva kiigazította. A Ev3 a D/Dv2 családok nagy memóriabeli virtuálisgép-méretének követése.

- Az [M sorozat](m-series.md) magas vCPU (akár 128 vCPU) és nagy mennyiségű memóriát (legfeljebb 3,8 TiB) kínál. Emellett ideális a rendkívül nagy méretű adatbázisok vagy más alkalmazások számára, amelyek nagy vCPU és nagy mennyiségű memóriát foglalnak magukban.

- A [Mv2 sorozat](mv2-series.md) a legmagasabb vCPU (legfeljebb 416 vCPU) és a legnagyobb (legfeljebb 8,19 TiB) memóriával rendelkezik a felhőben. Ideális a rendkívül nagy méretű adatbázisokhoz vagy olyan egyéb alkalmazásokhoz, amelyek ki tudják használni a nagy vCPU-számot és a nagy memóriamennyiséget.

Az Azure-beli számítások olyan virtuálisgép-méreteket biztosítanak, amelyek egy adott hardvereszközhöz vannak elkülönítve, és egyetlen ügyfélhez vannak hozzárendelve. Ezek a virtuálisgép-méretek olyan számítási feladatokhoz ideálisak, amelyek nagy fokú elkülönítést igényelnek más ügyfelektől olyan munkaterhelések esetén, mint például a megfelelőségi és szabályozási követelmények. Az ügyfelek emellett dönthetnek úgy is, hogy a [beágyazott virtuális gépek Azure-támogatásának](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)használatával tovább alcsoportba helyezik az elkülönített virtuális gépek erőforrásait. Az elkülönített virtuálisgép-lehetőségekért tekintse meg az alábbi virtuális gépeket tartalmazó lapokat.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.