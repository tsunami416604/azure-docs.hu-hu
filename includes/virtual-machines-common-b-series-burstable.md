---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: ayshakeen
ms.service: virtual-machines
ms.topic: include
ms.date: 06/25/2019
ms.author: azcspmt;ayshak;cynthn
ms.custom: include file
ms.openlocfilehash: 6a3e2034792fdc0a4a8fed7885c7d5ad78ea24d9
ms.sourcegitcommit: ac1cfe497341429cf62eb934e87f3b5f3c79948e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67501242"
---
A B sorozat Virtuálisgép-család lehetővé teszi, hogy válassza ki, melyik Virtuálisgép-méretet biztosít a számítási feladatok számára szükséges alapszinten teljesítményének a CPU-teljesítmény akár 100 %-a-Intel® Broadwell E5-2673 v4-eszközcsomag lehetővé teszi 2,3 GHz-es és a egy Intel® Haswell 2.4 GHz E5-2673 v3 processzor vCPU.

A B sorozatú virtuális gépek ideálisak a nagy számítási feladatokhoz, nincs szükség a CPU teljes teljesítményének folyamatosan, például a webkiszolgálók, megvalósíthatósági fogalmak, kis méretű adatbázisokhoz és fejlesztési összeállítási környezetekhez. Ezeket a feladatokat jellemzően rendelkeznek – adatlöket-kezelés teljesítményre vonatkozó követelmények. A B sorozat hogy is vásároljon a virtuális gép méretét, az alapszintű teljesítményt nyújtja, és a Virtuálisgép-példány épül fel kreditek, kevesebb, mint az alapterv használata esetén. A virtuális gép kredit keletkezett, amikor a a virtuális Géphez is megnövelheti arra 100 %-át megtakaríthatja a virtuális processzor használatát, amikor az alkalmazás magasabb CPU-teljesítményre van szükség az alapkonfiguráció fent.

A B sorozat a következő Virtuálisgép-méretek érhető el:

| Size             | vCPU  | Memória: GiB | Ideiglenes tárterület (SSD) GiB | A VM alap CPU-teljesítménye | A VM maximális CPU-teljesítménye | Kezdeti kreditek | Elkülönített kreditek óránként | Elkülönített kreditek max. száma | Adatlemezek max. száma | Gyorsítótárazott és ideiglenes tárolás max. átviteli sebessége IOPS / MBps | Max. gyorsítótárazás nélküli lemezteljesítmény: IOPS / MBps | Hálózati adapterek maximális száma |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5%                   | 100%                   | 30                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 30                   | 6                  | 144            | 2                        | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 30                   | 12                 | 288           | 2                         | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 60                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 60                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 120                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 240                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |
| Standard_B12ms | 12           | 48             | 96                         | 202%                  | 1200%                   | 360                   | 121                 | 2909           | 16                                     | 6480 / 75                                 | 4320 / 50                                  | 6  |
| Standard_B16ms | 16           | 64             | 128                         | 270%                  | 1600%                   | 480                   | 162                 | 3888           | 32                                     | 8640 / 100                                 | 4320 / 50                                 | 8  |
| Standard_B20ms | 20           | 80             | 160                         | 337%                  | 2000%                   | 600                   | 203                 | 4860           | 32                                     | 10800 / 125                                 | 4320 / 50                                 | 8  |

<sup>1</sup> B1ls csak a Linux támogatott

## <a name="workload-example"></a>Példa munkaterhelés

Fontolja meg egy office-/ kivétel az alkalmazást. Az alkalmazásnak kell CPU adatlöketekkel munkaidő alatt, de nem nagy számítási teljesítményt során csúcsidőn kívüli órákra. Ebben a példában a számítási feladatok hatékony munkájához elengedhetetlen RAM 64GiB 16vCPU virtuális gép van szükség.

A táblázat az óránkénti forgalmi adatok és a diagram vizuálisan, hogy a forgalom.

B16 jellemzők:

Maximális CPU-teljesítmény: 16vCPU * 100% = 1600%

Alapkonfiguráció: 270%

![Óránként forgalmi adatok diagramja](./media/virtual-machines-common-b-series-burstable/office-workload.png)

| Forgatókönyv | Time | CPU-használat (%) | Halmozott kreditek<sup>1</sup> | Rendelkezésre álló kreditek |
| --- | --- | --- | --- | --- |
| B16ms üzembe helyezés | Környezet | Környezet  | 480-as (kezdeti Krediteket) | 480 |
| Nincs adatforgalom | 0:00 | 0 | 162 | 642 |
| Nincs adatforgalom | 1:00 | 0 | 162 | 804 |
| Nincs adatforgalom | 2:00 | 0 | 162 | 966 |
| Nincs adatforgalom | 3:00 | 0 | 162 | 1128 |
| Nincs adatforgalom | 4:00 | 0 | 162 | 1290 |
| Nincs adatforgalom | 5:00 | 0 | 162 | 1452 |
| Alacsony forgalmú | 6:00 | 270 | 0 | 1452 |
| Az alkalmazottak érkeznek az Office (az alkalmazásnak kell 80 %-os vCPU) | 7:00 | 1280 | -606 | 846 |
| Az alkalmazottak továbbra is hamarosan az Office (az alkalmazásnak kell 80 %-os vCPU) | 8:00 | 1280 | -606 | 240 |
| Alacsony forgalmú | 9:00 | 270 | 0 | 240 |
| Alacsony forgalmú | 10:00 | 100 | 102 | 342 |
| Alacsony forgalmú | 11:00 | 50 | 132 | 474 |
| Alacsony forgalmú | 12:00 | 100 | 102 | 576 |
| Alacsony forgalmú | 13:00 | 100 | 102 | 678 |
| Alacsony forgalmú | 14:00 | 50 | 132 | 810 |
| Alacsony forgalmú | 15:00 | 100 | 102 | 912 |
| Alacsony forgalmú | 16:00 | 100 | 102 | 1014 |
| Az alkalmazottak (alkalmazás igényeinek megfelelően 100 %-os vCPU) kivételkor | 17:00 | 1600 | -798 | 216 |
| Alacsony forgalmú | 18:00 | 270 | 0 | 216 |
| Alacsony forgalmú | 19:00 | 270 | 0 | 216 |
| Alacsony forgalmú | 20:00 | 50 | 132 | 348 |
| Alacsony forgalmú | 21:00 | 50 | 132 | 480 |
| Nincs adatforgalom | 22:00 | 0 | 162 | 642 |
| Nincs adatforgalom | 23:00 | 0 | 162 | 804 |

<sup>1</sup> kreditek halmozott/krediteket egy órán belül használt 03T00: `((Base CPU perf of VM - CPU Usage) / 100) * 60 minutes`.  

Egy D16s_v3, amely rendelkezik a 16 vcpu-k és 64 GB memóriát a óránkénti $0.936 / óra (havi $673.92) és a B16ms-16 virtuális processzorral és 64 GiB memóriát az arány el 0.794 $ / óra (havi $547.86). <b> Az eredmény 15 %-os megtakarítás!</b>

## <a name="q--a"></a>Kérdések és válaszok

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>K: Hogyan 135-ös % alapteljesítményének be a virtuális gép
**A**: A 135-ös: % 8 vCPU-a Virtuálisgép-méret alkotó között meg van osztva. Például ha az alkalmazás használja a kötegelt feldolgozás dolgozik 8 magos, 4, és ezek 4 vCPU mindegyike 30 %-os kihasználtságot futtatását a virtuális gép CPU-teljesítmény teljes mennyisége 120 % lenne egyenlő.  Ami azt jelenti, hogy a virtuális gép lenne kell létrehozásához kredit idő a 15 %-os különbözeti alapkonfiguráció teljesítménye alapján.  De azt is jelenti, hogy mikor van, hogy ugyanazon a virtuális gép használhat-e minden 8 vCPU 100 %-ban rendelkezésre álló kreditek megadja a virtuális Gépeket egy 800 %-os maximális CPU-teljesítményre.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>K: Hogyan figyelhetem a saját jóváírási használattal és egyenleggel
**A**: Mi lesz lesz bevezetve 2 új mérőszámok az elkövetkező hetektől a **Kredit** metrika lehetővé teszi, hogy hány kreditjeit a virtuális gép rendelkezik banki megtekintése és a **ConsumedCredit** metrika jelennek meg a virtuális gép rendelkezik hány processzorkreditek Ezek a bank az.    Fogja tudni megtekinteni ezeket a metrikákat, a metrikák panelről, a portálon vagy programozottan az Azure Monitor API-kon keresztül.

Az Azure-hoz a mérőszámadatokat elérésével további információkért lásd: [Microsoft Azure-ban mérőszámok áttekintése](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>K: Hogyan tevődik össze kreditek?
**A**: A virtuális gép felhalmozódása és a használatalapú díjszabás szerint vannak beállítva, hogy pontosan az alap teljesítményi szinten rendszerű virtuális gép fog rendelkezni, sem a nettó felhalmozódása, vagy az tartalékkapacitás kreditek felhasználását.  Virtuális gép lesz a nettó növekedés értékű kreditet kap, amikor az alapszintű teljesítmény szint alatt fut, és lesz a nettó csökkenését értékű kreditet kap, amikor a virtuális gép több a CPU, mint az alapszintű teljesítményszint okból rendszerbetöltést végrehajtani.

**Példa**:  Üzembe helyezhetem a virtuális gép használatával B1ms méretét a kis idő és a nyilvántartás adatbázis-alkalmazás. Ez a méret legfeljebb 20 %-a vCPU-használhatja saját alapterv, amely 0,2 krediteket is használhatok perc vagy banki alkalmazás lehetővé teszi. 

Az alkalmazásom elején és végén saját alkalmazottak munka nap, 7:00 – 9:00-kor és 4:00 – 18:00:00 közötti elfoglalt. A többi 20 óra a nap, az alkalmazásom van alatt általában, tétlen, csak a 10 %-a virtuális processzor. A nem csúcsidőre órában tudom percenkénti 0,2 krediteket szerezhet, de csak felhasználják az 0.l kreditek száma percenként, így a virtuális gép fog a bank 0,1 × 60 = 6 óránként krediteket.  Csúcsidőszakon kívüli vagyok 20 óra e fog banki 120 krediteket.  

Alacsony kínálat csúcsidőben alkalmazásom átlagának kiszámítása 60 %-os vCPU-kihasználtság, percenként 0,2 kreditjeit még megszerzésére, de I / perc a 0.6-os kreditek felhasználását, 0,4 havonta felhasználható kreditek nettó költségek mellett egy percig vagy 0,4 × 60 = 24-kreditet írunk jóvá / óra. A használati csúcsot naponta 4 órán keresztül van, 4 x 24 = 96 költségei kreditek a saját használati csúcsot.

Ha a 120 jóváírásokat I csúcsidőn kívüli igénybe vehet, és a saját időszakokban használtam 96 kreditek kivonása, e banki naponta más csúcsforgalomra tevékenység használható egy további 24-krediteket.

### <a name="q-how-can-i-calculate-credits-accumulated-and-used"></a>K: Hogyan kiszámíthatja a halmozott és felhasznált kreditek?
**A**: Használhatja a következő képletre: 

(A CPU-teljesítmény VM - CPU-használat alapján) / 100 = kreditek bank, vagy használjon / perc

a fenti példány például: az alapkonfiguráció 20 %, és használhatja a processzor, amelyek gyűlik 10 % (20 %-át – 10 %) / 100 = 0,1 kredit percenként.

### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>K: A B sorozat támogatja a Premium Storage az adatlemezeket?
**A**: Igen, minden B sorozatú méretek támogatják a Premium Storage az adatlemezeket.   
    
### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>K: Miért érdemes a fennmaradó kredit 0 értékre van állítva egy ismételt üzembe helyezése vagy leállításához/elindításához után?
**A** : Amikor egy virtuális gép "REDPLOYED" és a virtuális gép áthelyezése egy másik csomópontra, a halmozott jóváírás elvész. Ha a virtuális gép leállítása/elindítása, de továbbra is megtalálható ugyanazon a csomóponton, a virtuális gép megőrzi a halmozott kreditet. Minden alkalommal, amikor a virtuális gép elindul egy csomóponton friss, egy kezdeti kreditet kap, a Standard_B8ms 240 perc.
    
### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>K: Mi történik, ha egy nem támogatott operációsrendszer-lemezkép B1ls üzembe helyezhetem?
**A** : B1ls csak támogatja a Linux-rendszerképeket, és bármely másik operációsrendszer-lemezképek üzembe helyezésekor nem kaphat a tökéletes élményt nyújtanak.
