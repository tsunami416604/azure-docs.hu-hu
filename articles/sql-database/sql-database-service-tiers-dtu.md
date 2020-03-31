---
title: Szolgáltatási szintek – DTU-alapú vásárlási modell
description: Ismerje meg a szolgáltatási szintek a DTU-alapú vásárlási modell egy- és készletalapú adatbázisok számítási és tárolási méretek biztosítása.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 11/26/2019
ms.openlocfilehash: 2f316e57e407a0588e77f56d6e1fbe8c19ba5fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75562119"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>A DTU-alapú vásárlási modell szolgáltatásszintjei

A DTU-alapú vásárlási modell szolgáltatásszintjeit a számítási méretek széles skálája különbözteti meg, rögzített mennyiségű beépített tárhellyel, rögzített biztonsági mentési időszakkal és rögzített árral. A DTU-alapú vásárlási modell összes szolgáltatási szintje rugalmasságot biztosít a számítási méretek minimális [állásidővel](https://azure.microsoft.com/support/legal/sla/sql-database/v1_2/)történő módosításához; azonban van egy kapcsoló időszakon keresztül, ahol a kapcsolat megszakad az adatbázishoz egy rövid ideig, amely az újrapróbálkozási logika használatával mérséklődhet. Az egyes adatbázisok és a rugalmas készletek számlázása óránként történik a szolgáltatási szint és a számítási méret alapján.

> [!IMPORTANT]
> Az SQL Database felügyelt példánya nem támogatja a DTU-alapú vásárlási modellt. További információ: [Azure SQL Database Managed Instance](sql-database-managed-instance.md).
> [!NOTE]
> A virtuálismag-alapú szolgáltatásszintekről a [virtuálismag-alapú szolgáltatásszintek](sql-database-service-tiers-vcore.md)című témakörben talál további információt. A DTU-alapú szolgáltatási szintek és a virtuálismag-alapú szolgáltatási szintek differenciálásáról az [Azure SQL Database vásárlási modelljei](sql-database-purchase-models.md)című témakörben talál további információt.

## <a name="compare-the-dtu-based-service-tiers"></a>A DTU-alapú szolgáltatásszintek összehasonlítása

A szolgáltatási szint kiválasztása elsősorban az üzletmenet folytonosságát, a tárolás és a teljesítmény követelményeitől függ.

||Basic|Standard|Prémium|
| :-- | --: |--:| --:|
|Megcélzott munkaterhelés|Fejlesztés és termelés|Fejlesztés és termelés|Fejlesztés és termelés|
|Uptime SLA|99.99%|99.99%|99.99%|
|A biztonsági mentés maximális megőrzése|7 nap|35 nap|35 nap|
|CPU|Alacsony|Alacsony, Közepes, Magas|Közepes, Magas|
|Io átviteli -fonás (hozzávetőleges) |1-5 IOPS DTU-nként| 1-5 IOPS DTU-nként | 25 IOPS DTU-nként|
|IO késés (hozzávetőleges)|5 ms (olvasás), 10 ms (írás)|5 ms (olvasás), 10 ms (írás)|2 ms (olvasás/írás)|
|Oszlopcentrikus indexelés |N/A|S3 és újabb|Támogatott|
|Memórián belüli OLTP|N/A|N/A|Támogatott|
|||||

> [!IMPORTANT]
> Az alapszintű, standard s0, s1 és s2 szolgáltatási szintek kevesebb, mint egy virtuális mag (CPU).  Cpu-igényes számítási feladatok esetén az S3 vagy nagyobb szolgáltatási szint ajánlott. 
>
>Az adattárolás t illeti, az alapszintű, a standard S0 és az S1 szolgáltatásszintek normál lapblobokon vannak elhelyezve. A szabványos lapblobok merevlemez-alapú (HDD)alapú adathordozót használnak, és a legalkalmasabbak a fejlesztésre, tesztelésre és más, ritkán használt számítási feladatokra, amelyek kevésbé érzékenyek a teljesítmény változékonyságára.
>

> [!NOTE]
> Ingyenes Azure SQL-adatbázist kaphat az alapszintű szolgáltatási szinten egy Azure-beli ingyenes fiókkal együtt az Azure felfedezéséhez. További információ: [Felügyelt felhőalapú adatbázis létrehozása az Ingyenes Azure-fiókkal című témakörben.](https://azure.microsoft.com/free/services/sql-database/)

## <a name="single-database-dtu-and-storage-limits"></a>Egyetlen adatbázis DTU és tárolási korlátok

A számítási méretek az egyes adatbázisok adatbázis-tranzakciós egységeiben (DT-k) és a rugalmas adatbázis-tranzakciós egységekben (eDTO-k) vannak kifejezve rugalmas készletekhez. A DTU-król és az eDTU-król a [DTU-alapú vásárlási modell](sql-database-purchase-models.md#dtu-based-purchasing-model)ben található további információk.

||Basic|Standard|Prémium|
| :-- | --: | --: | --: |
| Maximális tárolási méret | 2 GB | 1 TB | 4 TB  |
| Maximális DTUs | 5 | 3000 | 4000 | 
|||||

> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy a fel nem használt terület visszaszerzéséhez össze kell adnia egy adatbázist. További információt a [Fájlterület kezelése az Azure SQL Database-ben című témakörben talál.](sql-database-file-space-management.md)

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Rugalmas készlet eDTU, tárolás és készletezésű adatbázis-korlátok

| | **Basic** | **Standard** | **Prémium** |
| :-- | --: | --: | --: |
| Maximális tárhely adatbázisonként  | 2 GB | 1 TB | 1 TB |
| Maximális tárhelykészletenként | 156 GB | 4 TB | 4 TB |
| Maximális eD-TUs adatbázisonként | 5 | 3000 | 4000 |
| Maximális eD-TUs készletenként | 1600 | 3000 | 4000 |
| Adatbázisok maximális száma készletenként | 500  | 500 | 100 |
|||||

> [!IMPORTANT]
> A prémium szint több mint 1 TB tárhelye jelenleg minden régióban elérhető, kivéve: Kína keleti, Észak-Kína, Németország központi, északkeleti régiója, USA nyugati középső régiói, az USA DoD régiói és az Egyesült Államok központi kormánya. Ezekben a régiókban a prémium szint maximális tárháza 1 TB-ra korlátozódik.  További információ: [P11-P15 jelenlegi korlátozások](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb).  
> [!IMPORTANT]
> Bizonyos körülmények között előfordulhat, hogy a fel nem használt terület visszaszerzéséhez össze kell adnia egy adatbázist. További információt az [Azure SQL Database fájlterületének kezelése című témakörben talál.](sql-database-file-space-management.md)

## <a name="dtu-benchmark"></a>DTU Benchmark

Az egyes DTU-mérhető ekhez társított fizikai jellemzők (CPU, memória, IO) kalibrálása egy olyan teljesítményteszt használatával történik, amely valós adatbázis-munkaterhelést szimulál.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>A teljesítményteszt eredményeinek a valós adatbázis teljesítményével való összevetése

Fontos megérteni, hogy minden referenciamutató csak reprezentatív és tájékoztató jellegű. A referenciamutató-alkalmazással elért tranzakciós díjak nem lesznek megegyeznek azokkal, amelyek más alkalmazásokkal elérhetők. A teljesítményteszt különböző tranzakciótípusok gyűjteményéből áll, amelyek táblák és adattípusok tartományát tartalmazó sémán futnak. Míg a benchmark ugyanazokat az alapvető műveleteket gyakorolja, amelyek az összes OLTP számítási feladatok, nem képvisel semmilyen adott adatbázis- vagy alkalmazásosztály. A referenciamutató célja, hogy ésszerű útmutatót nyújtson egy adatbázis relatív teljesítményéhez, amely a számítási méretek közötti fel- vagy leskálázás során várható. A valóságban az adatbázisok különböző méretűek és összetettek, a munkaterhelések különböző keverékeivel találkoznak, és különböző módokon reagálnak. Például egy i/o-igényes alkalmazás hamarabb megkaphatja az IO-küszöbértékeket, vagy egy CPU-igényes alkalmazás hamarabb eltalálhatja a CPU-korlátokat. Nincs garancia arra, hogy egy adott adatbázis a növekvő terhelés alatt a referenciamutatóval azonos módon méreteződik.

A referenciamutatót és módszertanát az alábbiakban részletesebben ismertetjük.

### <a name="benchmark-summary"></a>A teljesítményteszt összefoglalása

A teljesítményteszt az online tranzakciófeldolgozási (OLTP) munkaterhelésekben leggyakrabban előforduló alapvető adatbázis-műveletek kombinációjának teljesítményét méri. Bár a teljesítményteszt a felhőalapú számítástechnikát szem előtt tartva készült, az adatbázisséma, az adatsokaktakadozás és a tranzakciók úgy lettek kialakítva, hogy széles körben reprezentatívak legyenek az OLTP-munkaterhelésekben leggyakrabban használt alapvető elemekre.

### <a name="schema"></a>Séma

A séma úgy van kialakítva, hogy elegendő változatossággal és összetettséggel rendelkezik a műveletek széles körének támogatásához. A referenciamutató egy hat táblából álló adatbázissal szemben fut. A táblázatok három kategóriába sorolhatók: rögzített méret, méretezés és növekvő. Két rögzített méretű táblázat létezik; három méretezési táblázat; és egy növekvő asztal. A rögzített méretű táblák sorainak állandó száma van. A méretezési táblák számossága arányos az adatbázis teljesítményével, de nem változik a teljesítményteszt során. A növekvő tábla méretezett, mint egy skálázási tábla a kezdeti terhelés, de majd a számosság változások során a teljesítményteszt sorok beszúrása és törlése során.

A séma adattípusok keverékét tartalmazza, beleértve az egész, numerikus, karakter és dátum/idő típusokat. A séma tartalmazza az elsődleges és másodlagos kulcsokat, de az idegen kulcsokat nem – azaz nincsenek hivatkozási integritási megkötések a táblák között.

Egy adatgeneráló program hozza létre a kezdeti adatbázis adatait. Az egész és a numerikus adatok különböző stratégiákkal jönnek létre. Bizonyos esetekben az értékek véletlenszerűen vannak elosztva egy tartományban. Más esetekben egy értékhalmaz véletlenszerűen permutálódik, hogy egy adott eloszlás megmaradjon. A szövegmezők a szavak súlyozott listájából jönnek létre, hogy reálisnak tűnő adatokat hozzanak létre.

Az adatbázis mérete "léptéktényező" alapján történik. A skálázási tényező (rövidítve SF) határozza meg a skálázási és növekvő táblázatok számosságát. Az alábbiakban leírtak szerint a Felhasználók és az ütemezés című szakaszban az adatbázis mérete, a felhasználók száma és a maximális teljesítmény egymással arányosan méreteződnek.

### <a name="transactions"></a>Tranzakciók

A munkaterhelés kilenc tranzakciótípusból áll, az alábbi táblázat ban látható módon. Minden tranzakció célja, hogy kiemelje az adatbázismotor és a rendszerhardver rendszerjellemzőinek egy adott készletét, nagy kontraszttal a többi tranzakcióval. Ez a megközelítés megkönnyíti a különböző összetevők általános teljesítményre gyakorolt hatásának értékelését. Például a tranzakció "Read Heavy" termel jelentős számú olvasási műveletek lemezről.

| Transaction Type (Tranzakció típusa) | Leírás |
| --- | --- |
| Olvassa Lite |VÁLASSZA KI; memóriában; írásvédett |
| Adathordozó olvasása |VÁLASSZA KI; többnyire a memóriában; írásvédett |
| Olvassa nehéz |VÁLASSZA KI; többnyire nem a memóriában; írásvédett |
| Lite frissítése |FRISSÍTÉS; memóriában; olvasás-írás |
| Nagy frissítés |FRISSÍTÉS; többnyire nem a memóriában; olvasás-írás |
| Lite beszúrása |BESZÚRÁS; memóriában; olvasás-írás |
| Nehéz beszúrás |BESZÚRÁS; többnyire nem a memóriában; olvasás-írás |
| Törlés |TÖRLÉS; a memóriában lévő konfektés nem a memóriában; olvasás-írás |
| CPU nehéz |VÁLASSZA KI; memóriában; viszonylag nehéz CPU-terhelés; írásvédett |

### <a name="workload-mix"></a>Munkaterhelés-összetétel

A tranzakciókat véletlenszerűen választják ki egy súlyozott eloszlásból a következő teljes összetétellel. A teljes mix olvasási/írási aránya körülbelül 2:1.

| Transaction Type (Tranzakció típusa) | Mix %-a |
| --- | --- |
| Olvassa Lite |35 |
| Adathordozó olvasása |20 |
| Olvassa nehéz |5 |
| Lite frissítése |20 |
| Nagy frissítés |3 |
| Lite beszúrása |3 |
| Nehéz beszúrás |2 |
| Törlés |2 |
| CPU nehéz |10 |

### <a name="users-and-pacing"></a>Felhasználók és ütemezés

A teljesítményteszt számítási feladata egy olyan eszköz, amely tranzakciókat küld egy kapcsolatcsoporton keresztül, hogy szimulálja számos egyidejű felhasználó viselkedését. Bár az összes kapcsolat és tranzakció gép által generált, az egyszerűség kedvéért hivatkozunk ezek a kapcsolatok a "felhasználók". Bár minden felhasználó az összes többi felhasználótól függetlenül működik, minden felhasználó ugyanazt a lépésciklust hajtja végre:

1. Adatbázis-kapcsolat létrehozása.
2. Ismételje ezt addig, amíg a kilépésig nem jelez:
   - Válasszon ki egy tranzakciót véletlenszerűen (súlyozott eloszlásból).
   - Végezze el a kijelölt tranzakciót, és mérje a válaszidőt.
   - Várjon a késleltetésre.
3. Zárja be az adatbázis-kapcsolatot.
4. Kilépés.

Az ütemezési késleltetés (a 2c lépésben) véletlenszerűen van kiválasztva, de átlagosan 1,0 másodperces eloszlással. Így minden felhasználó átlagosan másodpercenként legbőlegegy tranzakciót generálhat.

### <a name="scaling-rules"></a>Szabályok méretezése

A felhasználók számát az adatbázis mérete határozza meg (léptéktényező-egységekben). Minden öt léptéktényezős egységhez egy felhasználó tartozik. Az ütemezési késleltetés miatt egy felhasználó átlagosan másodpercenként legbőlegelhet egy tranzakciót.

Egy 500-as (SF=500) méretű adatbázis például 100 felhasználóval rendelkezik, és legfeljebb 100 TPS-t érhet el. A magasabb TPS-sebesség eléréséhez több felhasználóra és nagyobb adatbázisra van szükség.

### <a name="measurement-duration"></a>Mérés időtartama

Egy érvényes teljesítményteszt-futtatás hoz legalább egy órás állandósult állapotú mérési időtartamot igényel.

### <a name="metrics"></a>Mérőszámok

A fő metrikák a teljesítmény-átviteli sebesség és a válaszidő.

- Az átviteli teljesítmény a teljesítménymérő alapvető mértéke. Az átviteli áteresztőerő az időegységenkénti tranzakciókban történik, és az összes tranzakciótípust megszámolja.
- A válaszidő a teljesítmény kiszámíthatóságának mérőszáma. A válaszidő-megkötés a szolgáltatási osztálytól függ, és a magasabb szolgáltatási osztályok szigorúbb válaszidőre van szükség, ahogy az alábbiakban látható.

| Szolgáltatási osztály | Átviteli intézkedés | Válaszidő-követelmény |
| --- | --- | --- |
| Prémium |Tranzakciók másodpercenként |95. percentilis 0,5 másodpercnél |
| Standard |Tranzakciók percenként |90. percentilis 1,0 másodperc nél |
| Basic |Óránkénti tranzakciók |80. percentilis 2,0 másodpercnél |

## <a name="next-steps"></a>További lépések

- Az egyes adatbázisokhoz rendelkezésre álló konkrét számítási méretekkel és tárolási méretválasztékkal kapcsolatos részleteket az [SQL Database DTU-alapú erőforráskorlátok az egyes adatbázisokhoz című témakörben találja.](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)
- A rugalmas készletekhez rendelkezésre álló számítási méretekkel és tárolási méretválasztékkal kapcsolatos részleteket az [SQL Database DTU-alapú erőforráskorlátok](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes)című témakörben találja.
