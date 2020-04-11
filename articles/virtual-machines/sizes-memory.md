---
title: Az Azure virtuális gépek méretei – Memória | Microsoft dokumentumok
description: Az Azure-ban a virtuális gépekhez elérhető különböző memóriaoptimalizált méretek. A vCPU-k, adatlemezek és hálózati adapterek számával, valamint a tárolóátviteli és hálózati sávszélességgel kapcsolatos információkat sorolja fel a sorozat méreteihez.
services: virtual-machines
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
keywords: Virtuális gép elkülönítése,izolált virtuális gép,izoláció,izolált
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 0936e3049b2bc23e2f98244f29c962f6db65b8df
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/10/2020
ms.locfileid: "81115257"
---
# <a name="memory-optimized-virtual-machine-sizes"></a>Memóriaoptimalizált virtuális gépméretek

A memóriaoptimalizált virtuálisgép-méretek magas memória-és CPU-arányt kínálnak, amely kiválóan alkalmas relációs adatbázis-kiszolgálók, közepes és nagy gyorsítótárak és memóriabeli elemzések hez. Ez a cikk a csoportosítás egyes méreteihez a vCPU-k, adatlemezek és hálózati adapterek, valamint a tárolási átviteli és hálózati sávszélesség számáról tartalmaz tájékoztatást.

- [Dv2 és DSv2-sorozat](dv2-dsv2-series-memory.md), a follow-on, hogy az eredeti D-sorozat, tartalmaz egy erősebb CPU. A Dv2-sorozat körülbelül 35%-kal gyorsabb, mint a D-sorozat. Ez fut az&reg; Intel&reg; Xeon 8171M 2.1 GHz&reg; (Skylake) vagy az Intel Xeon&reg; E5-2673 v4 2.3 GHz (Broadwell) vagy az Intel&reg; Xeon&reg; E5-2673 v3 2.4 GHz (Haswell) processzorok, és az Intel Turbo Boost Technology 2.0. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.

    A Dv2 és DSv2 sorozat ú alkalmazások gyorsabb vCPU-k, jobb ideiglenes tárolási teljesítmény vagy nagyobb memóriaigény esetén ideálisak. Nagyon hatékony kombinációt kínálnak számos nagyvállalati szintű alkalmazáshoz.

- Az [Eav4 és Easv4 sorozat](eav4-easv4-series.md) az AMD 2,35 GHz-es EPYC<sup>TM</sup> 7452 processzorát használja többszálas konfigurációban, akár 256 MB L3 gyorsítótárral, növelve a legtöbb memóriaoptimalizált munkaterhelés futtatásának lehetőségeit. Az Eav4 és Easv4 sorozat ugyanolyan memória- és lemezkonfigurációval rendelkezik, mint az Ev3 & Esv3 sorozat.

- Az [Ev3 és esv3 sorozatú](ev3-esv3-series.md) &reg; Intel Xeon&reg; 8171M 2.1&reg; GHz&reg; (Skylake) vagy az Intel Xeon E5-2673 v4 2.3 GHz (Broadwell) processzor egy hiper-szálas konfigurációban, amely jobb értéket nyújt a legtöbb általános célú számítási munkához, és az Ev3-at a legtöbb más felhő általános célú virtuális gépeihez igazítja. A memória bővült (7 GiB/vCPU-ról 8 GiB/vCPU-ra), míg a lemez- és hálózati korlátokat magonként módosították, hogy igazodjanak a hiperszálas működésre való áttéréshez. Az Ev3 a D/Dv2 családok nagy memóriában lévő virtuálisgép-méreteinek nyomon követése.

- Az M sorozat magas [vCPU-számot](m-series.md) (akár 128 vCPU-t) és nagy mennyiségű memóriát (akár 3,8 TiB) kínál. Emellett rendkívül nagy adatbázisokhoz vagy más alkalmazásokhoz is ideális, amelyek nagy vCPU-számmal és nagy mennyiségű memóriával járnak.

- Az Mv2 sorozat a felhőben lévő virtuális gépek legnagyobb vCPU-számát (akár 416 vCPU-t) és legnagyobb memóriát (akár 11,4 TiB).The [Mv2-series](mv2-series.md) offers the highest vCPU count (up to 416 vCPU) and largest memory (up to 11.4 TiB) of any VM in the cloud. Ideális a rendkívül nagy adatbázisokhoz vagy más alkalmazásokhoz, amelyek nagy vCPU-számmal és nagy mennyiségű memóriával járnak.

Az Azure Compute olyan virtuálisgép-méreteket kínál, amelyek egy adott hardvertípusra vannak elkülönítve, és egyetlen ügyfélnek vannak szentelve. Ezek a virtuális gépméretek a legalkalmasabbak olyan számítási feladatokhoz, amelyek nagyfokú elkülönítést igényelnek más ügyfelektől olyan számítási feladatokhoz, amelyek olyan elemeket tartalmaznak, mint a megfelelőségi és szabályozási követelmények. Az ügyfelek dönthetnek úgy is, hogy tovább osztják az elkülönített virtuális gépek erőforrásait a [beágyazott virtuális gépek Azure-támogatásával.](https://azure.microsoft.com/blog/nested-virtualization-in-azure/) Tekintse meg az alábbi virtuálisgép-családok lapjait az elkülönített virtuálisgép-beállításokért.

## <a name="other-sizes"></a>Egyéb méretek

- [Általános célú](sizes-general.md)
- [Számításra optimalizált](sizes-compute.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy [az Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.