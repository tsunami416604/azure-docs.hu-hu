---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 0e0b78d1e492e4d0fbbf0a44b57bebc2d8d3b1e7
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2019
ms.locfileid: "58763303"
---
A B sorozat Virtuálisgép-család lehetővé teszi, hogy válassza ki, melyik Virtuálisgép-méretet biztosít a számítási feladatok számára szükséges alapszinten teljesítményének a CPU-teljesítmény akár 100 %-a-Intel® Broadwell E5-2673 v4-eszközcsomag lehetővé teszi 2,3 GHz-es és a egy Intel® Haswell 2.4 GHz E5-2673 v3 processzor vCPU.

A B sorozatú virtuális gépek ideálisak a nagy számítási feladatokhoz, nincs szükség a CPU teljes teljesítményének folyamatosan, például a webkiszolgálók, megvalósíthatósági fogalmak, kis méretű adatbázisokhoz és fejlesztési összeállítási környezetekhez. Ezeket a feladatokat jellemzően rendelkeznek – adatlöket-kezelés teljesítményre vonatkozó követelmények. A B sorozat hogy is vásároljon a virtuális gép méretét, az alapszintű teljesítményt nyújtja, és a Virtuálisgép-példány épül fel kreditek, kevesebb, mint az alapterv használata esetén. A virtuális gép kredit keletkezett, amikor a a virtuális Géphez is megnövelheti arra 100 %-át megtakaríthatja a virtuális processzor használatát, amikor az alkalmazás magasabb CPU-teljesítményre van szükség az alapkonfiguráció fent.

A B sorozat a következő hat VM-méret érhető el:

| Méret             | vCPU  | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Kiinduló virtuális gép CPU-teljesítmény | Virtuális gép maximális CPU Teljesítményoptimalizált | A kreditek banki / óra | Maximális banki kreditek | Adatlemezek max. száma | Maximális gyorsítótárazott és ideiglenes tárolóteljesítmény: IOPS / MBps | Nem gyorsítótárazott lemezek max. teljesítménye: IOPS / MBps | Hálózati adapterek maximális száma |          
|---------------|-------------|----------------|----------------------------|-----------------------|--------------------|--------------------|----------------|----------------------------------------|-------------------------------------------|-------------------------------------------|----------|
| Standard_B1ls<sup>1</sup>  | 1           | 0,5              | 4                          | 5%                   | 100%                   | 3                  | 72            | 2                                      | 200 / 10                                  | 160 / 10                                  | 2  |
| Standard_B1s  | 1           | 1              | 4                          | 10%                   | 100%                   | 6                  | 144            | 2                       | 400 / 10                                  | 320 / 10                                  | 2  |
| Standard_B1ms | 1           | 2              | 4                          | 20%                   | 100%                   | 12                 | 288           | 2                        | 800 / 10                                  | 640 / 10                                  | 2  |
| Standard_B2s  | 2           | 4              | 8                          | 40%                   | 200%                   | 24                 | 576            | 4                                      | 1600 / 15                                 | 1280 / 15                                 | 3  |
| Standard_B2ms | 2           | 8              | 16                         | 60%                   | 200%                   | 36                 | 864            | 4                                      | 2400 / 22.5                               | 1920 / 22.5                               | 3  |
| Standard_B4ms | 4           | 16             | 32                         | 90%                   | 400%                   | 54                 | 1296           | 8                                      | 3600 / 35                                 | 2880 / 35                                 | 4  |
| Standard_B8ms | 8           | 32             | 64                         | 135%                  | 800%                   | 81                 | 1944           | 16                                     | 4320 / 50                                 | 4320 / 50                                 | 4  |

<sup>1</sup> B1ls csak a Linux támogatott

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


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>K: A B sorozat támogatja a Premium Storage az adatlemezeket?
**A**: Igen, minden B sorozatú méretek támogatják a Premium Storage az adatlemezeket.   
    
### <a name="q-why-is-my-remaining-credit-set-to-0-after-a-redeploy-or-a-stopstart"></a>K: Miért érdemes a fennmaradó kredit 0 értékre van állítva egy ismételt üzembe helyezése vagy leállításához/elindításához után?
**A** : Amikor egy virtuális gép "REDPLOYED" és a virtuális gép áthelyezése egy másik csomópontra, a halmozott jóváírás elvész. Ha a virtuális gép leállítása/elindítása, de továbbra is megtalálható ugyanazon a csomóponton, a virtuális gép megőrzi a halmozott kreditet. Minden alkalommal, amikor a virtuális gép elindul egy csomóponton friss, egy kezdeti kreditet kap, a Standard_B8ms 240 perc.
    
### <a name="q-what-happens-if-i-deploy-an-unsupported-os-image-on-b1ls"></a>K: Mi történik, ha egy nem támogatott operációsrendszer-lemezkép B1ls üzembe helyezhetem?
**A** : B1ls csak támogatja a Linux-rendszerképeket, és bármely másik operációsrendszer-lemezképek üzembe helyezésekor nem kaphat a tökéletes élményt nyújtanak.
    
### <a name="q-why-is-there-no-pricing-information-for-b1ls-windows"></a>K: Miért nem nincs B1ls windows díjszabási információit?
**A** : B1ls csak támogatja a Linux-rendszerképeket, és bármely más operációsrendszer-lemezkép központi telepítésekor nem kaphat a legjobb felhasználói élmény, de a felszámolt.


    

    
