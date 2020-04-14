---
title: Az Azure virtuális gépek méretei – Általános célú | Microsoft dokumentumok
description: Az Azure-ban a virtuális gépekhez elérhető különböző általános célméretek listája. A vCPU-k, adatlemezek és hálózati adapterek számával, valamint a tárolóátviteli és hálózati sávszélességgel kapcsolatos információkat sorolja fel a sorozat méreteihez.
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
ms.date: 02/20/2020
ms.author: mimckitt
ms.openlocfilehash: fc263eb6fbe6c6402aaf529229bb7025f070b8d9
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269669"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Általános célú virtuális gépméretek

Általános célú virtuális gép méretek kiegyensúlyozott CPU-memória arány. Ideális választások a teszteléshez és a fejlesztéshez, a kicsi és közepes adatbázisokhoz, illetve az alacsony és közepes forgalmú webkiszolgálókhoz. Ez a cikk az általános célú számítástechnikai ajánlatokról nyújt tájékoztatást.

- Az [Av2 sorozatú](av2-series.md) virtuális gépek számos hardvertípusra és processzorra telepíthetők. Az A sorozatú virtuális gépek processzorteljesítmény- és memóriakonfigurációval rendelkeznek, amelyek a legalkalmasabbak a belépő szintű számítási feladatokhoz, például a fejlesztéshez és a teszteléshez. A méretük a hardvernek megfelelően szabályozott, hogy egyenletes processzorteljesítményt nyújtsanak a futó példány számára, a futtató hardvertől függetlenül. Az adott méretet futtató fizikai hardver meghatározásához kérdezze le a virtuális hardvert a virtuális gépen belülről. A használati esetek közé tartoznak például a fejlesztési és tesztelési kiszolgálók, az alacsony forgalmú webkiszolgálók, a kis és közepes méretű adatbázisok, a fogalomigazolások és a kódtárolók.

  > [!NOTE]
  > Az A8 – A11-es virtuális gépek a tervek szerint 3/2021-re tervezik a nyugdíjba vonulást. További információ: [HPC Migration Guide](https://azure.microsoft.com/resources/hpc-migration-guide/).

- [B-sorozat burstable](sizes-b-series-burstable.md) A virtuális gépek ideálisak olyan számítási feladatokhoz, amelyek nem igényelnek folyamatosan a PROCESSZOR teljes teljesítményét, például webkiszolgálókat, kis adatbázisokat, valamint fejlesztési és tesztelési környezeteket. Ezek a számítási feladatok általában burstable teljesítménykövetelményekkel rendelkeznek. A B-sorozat lehetővé teszi, hogy ezek az ügyfelek egy virtuális gép méretét egy ártudatos alapteljesítmény, amely lehetővé teszi a virtuális gép példány a kreditek összeállításához, ha a virtuális gép kihasználva kevesebb, mint az alapteljesítmény. Ha a virtuális gép kreditet halmozott fel, a virtuális gép a virtuális gép alapkonfigurációja fölé robbanhat a PROCESSZOR 100%-ának használatával, ha az alkalmazás nagyobb CPU-teljesítményt igényel.

- [Dav4 és Dasv4-sorozat](dav4-dasv4-series.md) új méretű felhasználásával AMD 2.35Ghz EPYC<sup>TM</sup> 7452 processzor egy többszálas konfiguráció akár 256 MB L3 cache szenteli 8 MB, hogy az L3 cache minden 8 magot növekvő ügyfél lehetőségek futtatásához általános célú munkaterhelések. A Dav4 és Dasv4 sorozat ugyanolyan memória- és lemezkonfigurációval rendelkezik, mint a D & Dsv3 sorozat.

- A [DCv2 sorozat](dcv2-series.md) segít megvédeni az adatok és kódok titkosságát és integritását, miközben azokat a nyilvános felhőben dolgozzák fel. Ezeket a gépeket az Intel XEON E-2288G processzor legújabb generációja támogatja SGX technológiával. Az Intel Turbo Boost technológiával ezek a gépek akár 5,0 GHz-re is felmehetnek. A DCv2 sorozatpéldányok lehetővé teszik az ügyfelek számára, hogy biztonságos enklávé alapú alkalmazásokat építsenek a kódjuk és az adataik védelme érdekében, miközben használatban vannak.

- [Dv2 és Dsv2 sorozat](dv2-dsv2-series.md) Az eredeti D sorozatot követő virtuális gépek nagyobb teljesítményű PROCESSZORral és optimális CPU-memória konfigurációval rendelkeznek, így a legtöbb éles számítási feladathoz alkalmasak. A Dv2-sorozat körülbelül 35%-kal gyorsabb, mint a D-sorozat. A Dv2 sorozat az Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) vagy az Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) processzorokon fut az Intel Turbo Boost Technology 2.0 technológiával. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.

- [Dv3 és Dsv3 sorozat](dv3-dsv3-series.md) A virtuális gépek Intel® Xeon® 8171M 2,1 GHz-es (Skylake), Intel® Xeon® E5-2673 v4 2.3 GHz-es (Broadwell) vagy intel® Xeon® E5-2673 v3 2.4 GHz-es (Haswell) processzorokon futnak hiperszálas konfigurációban, ami jobb ár-érték arányt biztosít a legtöbb általános célú munkaterheléshez. A memória kibővült (~3,5 GiB/vCPU-ról 4 GiB/vCPU-ra), míg a lemez- és hálózati korlátokat magonként módosították, hogy igazodjanak a hyperthreading-ra való áttéréshez. A Dv3-sorozat már nem rendelkezik a D/Dv2 sorozat nagy memória virtuálisgép-méreteivel, azokat a memóriára optimalizált [Ev3 és Esv3 sorozatba](ev3-esv3-series.md)helyezték át.

Példa a D-sorozathasználati esetek közé tartoznak a nagyvállalati szintű alkalmazások, relációs adatbázisok, a memóriában belüli gyorsítótárazás és az analitika.

## <a name="other-sizes"></a>Egyéb méretek

- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>További lépések

További információ arról, hogy [az Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek a számítási teljesítmény összehasonlításában az Azure-sKU-k között.
