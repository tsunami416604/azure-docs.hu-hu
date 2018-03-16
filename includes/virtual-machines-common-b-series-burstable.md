---
title: "fájl belefoglalása"
description: "fájl belefoglalása"
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 95a78df20f5bed07213dfa3cc2c9b35e283f54e7
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2018
---
A B sorozatnak VM termékcsalád lehetővé teszi annak meghatározását, mely Virtuálisgép-méretet a megfelelő alap szint teljesítmény a munkaterhelés a lehetőséget nyújt a CPU-teljesítmény az Intel® Broadwell E5-2673 v4 legfeljebb 100 %-os kapacitásnövelés 2.3 GHz-es, vagy egy Intel® Haswell 2.4 GHz -es E5-2673 v3 processzor vCPU.

A B sorozatú virtuális gépek ideálisak munkaterhelések esetén, amelyek nem kell a Processzor teljes teljesítményének folyamatosan, például a webkiszolgálók, a kis adatbázisok és a fejlesztési és tesztelési környezetben. Az ilyen terhelések általában burstable teljesítmény követelményekkel rendelkezik. A B sorozatnak lehetőséget nyújt a beszerzési egy Virtuálisgép-méretet, a teljesítmény alapértékeiről, és kevesebb, mint az eredeti használata esetén a kreditek elkészít a Virtuálisgép-példány. A virtuális gép jóváírás keletkezett, amikor a virtuális Gépet a alapmennyiséget meghaladóan használatát az akár 100 %-a a vCPU, ha az alkalmazás által igényelt nagyobb processzorteljesítmény is kapacitásnövelés.

A B sorozatnak érkezik a következő hat Virtuálisgép-méretek:

| Méret          | vCPU meg | Memória: GiB | Ideiglenes tárterület (SSD) GiB | Kiinduló virtuális gép CPU-teljesítmény | Maximális CPU telj VM | Banki kreditek / óra | Maximális banki kreditek |
|---------------|--------|-------------|----------------|--------------------------------|---------------------------|-----------------------|--------------------|
| Standard_B1s  | 1      | 1           | 4              | 10%                            | 100%                      | 6                     | 144                |
| Standard_B1ms | 1      | 2           | 4              | 20%                            | 100%                      | 12                    | 288                |
| Standard_B2s  | 2      | 4           | 8              | 40%                            | 200%                      | 24                    | 576                |
| Standard_B2ms | 2      | 8           | 16             | 60%                            | 200%                      | 36                    | 864                |
| Standard_B4ms | 4      | 16          | 32             | 90%                            | 400%                      | 54                    | 1296               |
| Standard_B8ms | 8      | 32          | 64             | 135%                           | 800%                      | 81                    | 1944               |




## <a name="q--a"></a>Kérdések és válaszok 

### <a name="q-how-do-you-get-135-baseline-performance-from-a-vm"></a>K: hogyan 135-ös % alapterv teljesítmény eléréséhez a virtuális gép alapján
**A**: A 135-ös % 8 vCPU-a Virtuálisgép-méretet alkotó között meg van osztva. Például ha az alkalmazás által a dolgozik a köteges feldolgozás 8 magos, 4, és ezek 4 vCPU mindegyikének futnak, 30 %-os kihasználtsága a teljes rendszermemóriához viszonyítva VM CPU-teljesítmény 120 % volna egyenlő.  Ami azt jelenti, hogy a virtuális gép volna kell felépítése a 15 % különbözeti a alapteljesítményének a jóváírás időpontokat.  De azt is jelenti, hogy ha van elérhető, hogy ugyanazon VM használhatja minden 8 vCPU 100 %-os kreditek meg ezt a virtuális Gépet egy maximális CPU teljesítményt nyújtó 800 %.


### <a name="q-how-can-i-monitor-my-credit-balance-and-consumption"></a>K: hogyan követheti nyomon a saját kreditegyenlege és felhasználási
**A**: azt fogja bevezetése 2 új mérőszámok az elkövetkező hetektől a **jóváírás** metrika lehetővé teszi a virtuális gép rendelkezik banki hány kreditek megtekintése és a **ConsumedCredit** metrika megjelenik, hány Processzor a virtuális gép már felhasználta a Bank kreditek.    A metrikák panelről metrikákat megtekintheti a portálon vagy programozottan az Azure-figyelő API-k segítségével fogja.

A metrikai adatok elérésével az Azure-további információkért lásd: [áttekintése a Microsoft Azure-ban mérőszámok](../articles/monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="q-how-are-credits-accumulated"></a>K: hogyan kreditek összegzi a rendszer?
**A**: A virtuális gépi felhalmozódása és felhasználási díjszabás vannak beállítva, úgy, hogy egy virtuális Gépet, pontosan az alapszintű teljesítmény szint fog rendelkezni, sem a nettó felhalmozódása, vagy a teljesítménynövelés érdekében kreditek fogyasztását.  A virtuális gép lesz a nettó növelését kreditek amikor fut-e az alapszintű teljesítmény szint alatt, és kreditek nettó csökkenése fog rendelkezni, amikor a virtuális gép van használ, több processzor-Hozzáférést, mint az alap teljesítményszint szükséges.

**Példa**: a virtuális gépek B1ms méretét a kis munkaidő-nyilvántartási adatbázis-alkalmazás központi telepítésére. Ez a méret lehetővé teszi, hogy az alkalmazás saját / perc használata vagy a bank.2 kreditek alapterv legfeljebb 20 %-át egy vCPU használandó. 

Az alkalmazás jelenleg elfoglalt elején és az alkalmazottak munka 7:00 – 9:00-kor és 4:00 – 18:00:00 között nap végén. Az órában más 20 nap, a saját alkalmazása általában a tétlen, csak a 10 %-át a vCPU. A nem csúcsidőre I / perc 0,2 kreditek nyerjen, de csak felhasználják 0.l kreditek / perc, így a virtuális gép lesz banki ikonra.1 x 60 = 6 kreditek óránként.  A csúcsidőn kívüli vagyok 20 órában I fog banki 120 kreditek.  

Csúcsidőben alkalmazás 60 % vCPU kihasználtsági átlaga szerepel, percenként 0,2 kreditek továbbra is nyerjen, de I / perc 0,6 kreditek felhasználását, a nettó költségét.4 kreditek egy perc vagy.4 x 60 = 24 kreditek óránként. Napi használat, 4 óra van, 4 x 24 = 96 költséggel kreditek esetében a használati csúcsot.

Ha a 120 jóváírásokat I csúcsidőn kívüli érvénybe és kivonás a 96 kreditek a saját csúcsidőszak használt I banki naponta, a más felszakadásáig tevékenység használható egy további 24 kreditek.


### <a name="q-does-the-b-series-support-premium-storage-data-disks"></a>K: a B sorozatnak támogatja a prémium szintű Storage adatlemezek?
**A**: Igen, támogatja a B sorozatnak különböző méretű a prémium szintű Storage adatok lemezeket.   
    


    

    
