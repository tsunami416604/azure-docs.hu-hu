---
title: Azure VM-méretek – általános célú | Microsoft Docs
description: Felsorolja az Azure-beli virtuális gépekhez elérhető különböző általános célú méreteket. A vCPU, az adatlemezek és a hálózati adapterek számával, valamint a tárolási teljesítményével és a hálózat sávszélességével kapcsolatos információkat sorolja fel ebben a sorozatban.
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
ms.date: 02/20/2020
ms.author: jonbeck
ms.openlocfilehash: 4b36f456f70eb79cff1f615c7c136b8fe4b1b3d4
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226714"
---
# <a name="general-purpose-virtual-machine-sizes"></a>Általános célú virtuálisgép-méretek

Az általános célú virtuálisgép-méretek kiegyensúlyozott processzor-memória arányt biztosítanak. Ideális teszteléshez és fejlesztéshez, kis-és közepes méretű adatbázisokhoz, valamint alacsony és közepes forgalmú webkiszolgálókhoz. Ez a cikk az általános célú számítástechnikai ajánlatokkal kapcsolatos információkat tartalmaz.

- A [Av2 sorozatú](av2-series.md) virtuális gépek számos különböző hardvereszközön és processzoron is üzembe helyezhetők. Az A sorozatú virtuális gépeknél a CPU-teljesítmény és a memória-konfigurációk a legmegfelelőbbek a belépési szintű munkaterhelésekhez, például a fejlesztéshez A méretük a hardvernek megfelelően szabályozott, hogy egyenletes processzorteljesítményt nyújtsanak a futó példány számára, a futtató hardvertől függetlenül. Az adott méretet futtató fizikai hardver meghatározásához kérdezze le a virtuális hardvert a virtuális gépen belülről. Ilyenek például a fejlesztési és tesztelési kiszolgálók, az alacsony forgalmú webkiszolgálók, a kis-és közepes adatbázisok, a bizonyítási fogalmak és a kódok tárházai.

  > [!NOTE]
  > Az A8 – A11-es virtuális gépek a 3/2021-es kivonulásra vannak tervezve. További információ: [HPC áttelepítési útmutató](https://azure.microsoft.com/resources/hpc-migration-guide/).

- [B sorozat – feltört](sizes-b-series-burstable.md) A virtuális gépek olyan számítási feladatokhoz ideálisak, amelyeknek nincs szükségük a CPU teljes teljesítményére, például a webkiszolgálók, a kisméretű adatbázisok és a fejlesztési és tesztelési környezetek számára. Ezek a számítási feladatok általában feltört teljesítménnyel kapcsolatos követelményekkel rendelkeznek. A B sorozat biztosítja, hogy ezek az ügyfelek a virtuálisgép-méretet olyan tudatos alapszintű teljesítmény mellett vásárolják meg, amely lehetővé teszi, hogy a virtuálisgép-példány krediteket hozzon létre, amikor a virtuális gép az alapteljesítménynél kevesebbet használ. Ha a virtuális gép felhalmozott Kredittel rendelkezik, a virtuális gép a CPU alapkonfigurációja felett akár 100%-ot is megadhat, ha az alkalmazás a nagyobb CPU-teljesítményt igényli.

- A [Dav4 és a Dasv4 sorozat](dav4-dasv4-series.md) új méretek az AMD 2.35 GHz EPYC<sup>TM</sup> 7452 processzorát egy többszálas konfigurációban, akár 256 MB L3-os gyorsítótárral, 8 GB-ot kihasználva minden 8 mag számára, amely növeli az ügyfelek számára az általános célú munkaterhelések futtatásának lehetőségét. A Dav4-sorozat és a Dasv4-sorozat ugyanazokkal a memória-és lemez-konfigurációval rendelkezik, mint a D & Dsv3 sorozat.

- A [DCv2 sorozat](dcv2-series.md) a nyilvános felhőben feldolgozott adatok és kódok titkosságának és integritásának védelmét is lehetővé teszi. Ezeket a gépeket a SGX ENKLÁVÉHOZ technológiával rendelkező Intel XEON E-2288G processzor legújabb generációja támogatja. Az Intel Turbo Boost technológiával ezek a gépek akár 5,0 GHz-re is felmehetnek. A DCv2 sorozat példányai lehetővé teszik, hogy az ügyfelek biztonságos enklávé-alapú alkalmazásokat hozzanak létre a kódok és az adataik használat közbeni védelméhez.

- [Dv2 és Dsv2 sorozat](dv2-dsv2-series.md) A virtuális gépek, amelyek az eredeti D sorozaton alapulnak, hatékonyabb CPU-és optimális CPU-memória-konfigurációt biztosítanak, így a legtöbb éles számítási feladathoz megfelelőek. A Dv2 sorozat körülbelül 35%-kal gyorsabb a D sorozatnál. A Dv2 sorozat az Intel® Xeon® 8171M 2.1 GHz-es (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) vagy Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) processzorral fut, amely az Intel Turbo Boost Technology 2,0. A Dv2-sorozat ugyanolyan memória- és lemezkonfigurációkban érhető el, mint a D-sorozat.

- [Dv3 és Dsv3 sorozat](dv3-dsv3-series.md) A virtuális gépek az Intel® Xeon® 8171M 2.1 GHz-es (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz-es (Broadwell), vagy az Intel® Xeon® E5-2673 v3 2,4 GHz-es (Haswell) processzorok egy Hyper-threaded konfigurációban, jobb értékű kiosztást biztosítanak a legtöbb általános célú munkaterheléshez. A memória ki lett bontva (~ 3,5 GiB/vCPU – 4 GiB/vCPU), míg a lemez-és hálózati korlátokat a rendszer alapszinten igazította ki, hogy az a feleznie-re legyen igazítva. A Dv3-sorozat már nem rendelkezik a D/Dv2 sorozat nagy memóriabeli virtuálisgép-méretével, ezeket a rendszer áthelyezte a memória-optimalizált [Ev3 és Esv3-sorozatba](ev3-esv3-series.md).

Példa: a D sorozat használati esetei nagyvállalati szintű alkalmazások, a kapcsolódó adatbázisok, a memórián belüli gyorsítótárazás és az elemzések.

## <a name="other-sizes"></a>Egyéb méretek

- [Számításra optimalizált](sizes-compute.md)
- [Memóriaoptimalizált](sizes-memory.md)
- [Tárolásra optimalizált](sizes-storage.md)
- [GPU-optimalizált](sizes-gpu.md)
- [Nagy teljesítményű számítás](sizes-hpc.md)
- [Előző generációk](sizes-previous-gen.md)

## <a name="next-steps"></a>Következő lépések

További információ arról, hogy az [Azure számítási egységei (ACU)](acu.md) hogyan segíthetnek az Azure SKU-ban a számítási teljesítmény összehasonlításában.