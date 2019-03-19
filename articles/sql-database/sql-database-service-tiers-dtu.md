---
title: Az Azure SQL Database szolgáltatási szinteken - DTU-alapú vásárlási modell |} A Microsoft Docs
description: Ismerje meg a szolgáltatási szintekről az egyetlen vagy készletezett adatbázisok biztosít számítási és tárolási méretek esetében a DTU-alapú vásárlási modell.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: carlrab
manager: craigg
ms.date: 02/25/2019
ms.openlocfilehash: 7a8556edd793cbde47c14d2b79792dbe42f8e44b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57894291"
---
# <a name="service-tiers-in-the-dtu-based-purchase-model"></a>Szolgáltatási szinten a DTU-alapú vásárlási modell

Szolgáltatási szinten a DTU-alapú vásárlási modell által meghatározott memóriamennyiséggel rendelkeznek a csomagban foglalt tárhely, a megőrzési időszak a biztonsági mentések és a rögzített ár rögzített számítási méretű különbözteti meg. Minden szolgáltatási szinten a DTU-alapú vásárlási modell a számítási méret állásidő nélkül változó rugalmasságot biztosítanak. Önálló adatbázisok és rugalmas készletek számlázása óraszám alapján, a szolgáltatási szint és a számítási méret.

> [!IMPORTANT]
> Az SQL Database felügyelt példány nem támogatja a DTU-alapú vásárlási modell. További információkért lásd: [Azure SQL Database felügyelt példányába](sql-database-managed-instance.md).
> [!NOTE]
> További információ a Virtuálismag-alapú szolgáltatásszintek: [Virtuálismag-alapú szolgáltatásszintek](sql-database-service-tiers-vcore.md). További információ a DTU-alapú szolgáltatásszintek és a Virtuálismag-alapú szolgáltatásszintek sokoldalúbbá: [vásárlási modellek az Azure SQL Database](sql-database-purchase-models.md).

## <a name="compare-the-dtu-based-service-tiers"></a>A DTU-alapú szolgáltatásszintek összehasonlítása

Szolgáltatásszint kiválasztása elsődlegesen az üzleti folytonosság, tárolás és teljesítmény-követelmények attól függ.

||Alapszintű|Standard|Prémium|
| :-- | --: |--:| --:|
|Egyes számítási feladatokhoz|Fejlesztési és éles környezetek|Fejlesztési és éles környezetek|Fejlesztési és éles környezetek|
|SLA-ban garantált üzemidő|99.99%|99.99%|99.99%|
|Biztonsági mentés megőrzése|7 nap|35 napon belül|35 napon belül|
|CPU|Alacsony|Alacsony, közepes, nagy|Közepes, nagy|
|IO-átviteli sebesség (becsült) |2,5 dtu-k IOPS| 2,5 dtu-k IOPS | Dtu-k 48 IOPS|
|IO-késés (becsült)|5 ezredmásodperc (olvasás), 10 ms (írás)|5 ezredmásodperc (olvasás), 10 ms (írás)|2 ms (olvasás/írás)|
|Oszlopcentrikus indexelés |–|S3 vagy újabb verzió|Támogatott|
|Memóriabeli OLTP beállítása|–|–|Támogatott|
|||||

> [!NOTE]
> Egy ingyenes Azure SQL database az alapszintű szolgáltatásszinten együtt ingyenes Azure-fiókra, Fedezze fel az Azure kérheti le. További információ: [felügyelt felhőbeli adatbázis létrehozása az ingyenes Azure-fiók](https://azure.microsoft.com/free/services/sql-database/).

## <a name="single-database-dtu-and-storage-limits"></a>Önálló adatbázis DTU- és tárterületi korlátozásai

A számítási méret az önálló adatbázisok adatbázis-tranzakciós egységek (dtu-k) és a rugalmas Database Transaction Unitok (Edtu) rugalmas készletek vannak kifejezve. További információ a dtu-król és Edtu-: [DTU-alapú vásárlási modell](sql-database-purchase-models.md#dtu-based-purchasing-model)?

||Alapszintű|Standard|Prémium|
| :-- | --: | --: | --: |
| Maximális tárterület méretét | 2 GB | 1 TB | 4 TB  |
| Maximális dtu-k | 5 | 3000 | 4000 | 
|||||

> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

## <a name="elastic-pool-edtu-storage-and-pooled-database-limits"></a>Rugalmas készlet edtu-k, a storage és a készletezett adatbázis korlátok

| | **Basic** | **Standard** | **Prémium** |
| :-- | --: | --: | --: |
| Az adatbázisonkénti maximális mérete  | 2 GB | 1 TB | 1 TB |
| Készletenkénti maximális mérete | 156 GB | 4 TB | 4 TB |
| Maximális Edtu adatbázisonként | 5 | 3000 | 4000 |
| Maximális edtu-k száma készletenként | 1600 | 3000 | 4000 |
| Készletenkénti adatbázisok maximális száma | 500  | 500 | 100 |
|||||

> [!IMPORTANT]
> Jelenleg több mint 1 TB tárterület egységára prémium szinten érhető el minden régióban, kivéve: Kelet-Kína, Észak-Kína, közép-Németország, Északkelet-Németország, USA nyugati középső Régiója, USA védelmi Minisztériuma régiók és US Government központi. Ezekben a régiókban a prémium szinthez tartozó tárterület maximuma 1 TB.  További információkért lásd: [P11 – P15 – aktuális korlátozások](sql-database-single-database-scale.md#dtu-based-purchasing-model-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
> [!IMPORTANT]
> Bizonyos körülmények között szükség lehet az adatbázis nem használt terület felszabadítását zsugorítani. További információkért lásd: [kezelése az Azure SQL Database területe](sql-database-file-space-management.md).

## <a name="dtu-benchmark"></a>DTU-teljesítményteszt

Fizikai tulajdonságait (Processzor, memória, IO) társított minden egyes DTU mérték, amely szimulálja a valós számítási feladatokkal a teljesítményteszt használatával vannak korlátokon belül.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Számításiteljesítmény-mérési eredmények naplókezelője valós adatbázis teljesítménye

Fontos megérteni, hogy minden a referenciaértékek reprezentatív és tájékoztató csak. A tranzakciós díjakat biztosítanak a teljesítményteszt alkalmazással elért nem ugyanazok, mint, előfordulhat, hogy más alkalmazásokkal érhető el. A teljesítményteszt típusok futtatásához egy sémát tartalmazó táblák és adattípusok számos különböző tranzakció gyűjteményét foglalja magában. A teljesítményteszt gyakorol az ugyanazon alapvető műveleteket, amelyek közösek az összes OLTP-munkaterhelések, amíg nem tartozik adatbázis vagy az alkalmazás bármely adott osztály. A teljesítményteszt az a célja, hogy adjon meg egy ésszerű útmutató, amellyel egy adatbázist, amikor a számítási méret között felfelé és lefelé skálázás várható egymáshoz viszonyított teljesítményébe. A valóságban ez adatbázisok különböző méretekre és összetettséget, számítási feladatok különböző keverékei észlel, és különböző módokon válaszol. Például i/o-igényes alkalmazások i/o-küszöbértékek hamarabb ütközhet, vagy a CPU-igényes alkalmazások CPU korlátok hamarabb tapasztalhat. Nem biztos, hogy bármely adott adatbázis lesz skálázva a ugyanúgy, mint a teljesítményteszt növekvő terhelés alatt van.

A teljesítményteszt és a módszereket ismerteti részletesebben az alábbi.

### <a name="benchmark-summary"></a>Számításiteljesítmény-mérési összefoglaló

A teljesítményteszt többféle, az online tranzakciófeldolgozási (OLTP) munkaterhelések leggyakrabban előforduló alapszintű adatbázis-műveletek teljesítményének méri. Bár a teljesítményteszt a felhő-számítástechnika a szem előtt, az adatbázis-séma, az adatokkal való feltöltés célja, és a tranzakciók széles körben reprezentatívnak a leggyakrabban használt OLTP számítási feladatokat a alapszintű elemek is úgy lett kialakítva.

### <a name="schema"></a>Séma

A séma van úgy tervezték, hogy elegendő különböző és összetettsége műveletek széles körét támogatja. A teljesítményteszt fut egy adatbázis-csoportból áll, hat táblára. A táblák három kategóriába sorolhatók: rögzített méretű, skálázás, és nő. Két a rögzített méretű táblák; három méretezési táblázatokban; és a egy egyre bővülő tábla. Rögzített méretű táblák sorok számának állandó rendelkeznek. Méretezési táblák egy, az adatbázis teljesítménye arányos, de nem módosítja a teljesítményteszt során Számosság rendelkeznek. A növekvő tábla van például a kezdeti betöltés, de a számosság módosítások képviselőinkkel sort beszúrni, és törölni a teljesítményteszt futó méretezési táblázat méretezni.

A séma tartalmaz vegyesen adattípusok alapján, beleértve az egész szám, dátum és idő, numerikus és karakter. A séma tartalmazza az elsődleges és másodlagos kulcsok, de nem idegen kulcsokkal – a, hogy nincs hivatkozási integritási megkötéseket táblák között.

Adatok generációs program állít elő, a kezdeti adatbázis adatait. Egész szám és numerikus adatokat a különböző stratégiák jön létre. Bizonyos esetekben a értékek véletlenszerűen vannak elosztva egy tartományt. Más esetekben értékek egy halmazát van véletlenszerűen a(z) %u kell biztosítják, hogy egy adott terjesztési. Súlyozott listájából, valósághű, akik adatokat a keresett szavakat szöveges mezők jönnek létre.

Az adatbázis méretezése alapján "nagyságrendi tényezőt." A méretezési tényező (SF rövidítése) a méretezés és a táblák növekvő számossága határozza meg. Szakaszban leírtak szerint alatt a felhasználók és Pacing, az adatbázis méretét a felhasználók számára, és az összes arányosan méretezése a maximális teljesítmény.

### <a name="transactions"></a>Tranzakciók

A számítási feladatok kilenc tranzakciótípusok áll, az alábbi táblázatban látható módon. Minden tranzakció célja jelöljön ki egy meghatározott adatbázis motor és a rendszer hardver, kontrasztos megjelenítés a más tranzakciók a rendszer jellemzőit. Ez a megközelítés megkönnyíti a különböző összetevőket az általános teljesítmény hatásainak kiértékelését. Például az "Olvasás (nagy erőforrásigényű)" tranzakció lemez olvasási műveletek jelentős számú hoz létre.

| Tranzakció típusa | Leírás |
| --- | --- |
| Olvassa el a Lite |VÁLASSZA KI A; a memóriában; csak olvasható |
| Olvassa el közepes |VÁLASSZA KI A; elsősorban memóriabeli; csak olvasható |
| Olvasás (nagy erőforrásigényű) |VÁLASSZA KI A; többnyire nincs memórián belüli; csak olvasható |
| Lite Update |FRISSÍTÉS; a memóriában; olvasási és írási |
| Frissítés (nagy erőforrásigényű) |FRISSÍTÉS; többnyire nincs memórián belüli; olvasási és írási |
| Lite beszúrása |HELYEZZE BE; a memóriában; olvasási és írási |
| (Nagy erőforrásigényű) beszúrása |HELYEZZE BE; többnyire nincs memórián belüli; olvasási és írási |
| Törlés |TÖRLÉSE; a memóriában, és nem a memóriában; olvasási és írási |
| Processzor (nagy erőforrásigényű) |VÁLASSZA KI A; a memóriában; viszonylag nagy CPU-terhelés; csak olvasható |

### <a name="workload-mix"></a>Vegyes számítási feladatok

Tranzakciók véletlenszerűen kiválasztott egy súlyozott terjesztési az a következő általános vegyesen. A teljes vegyesen tartalmaz egy olvasási/írási arány körülbelül 2:1.

| Tranzakció típusa | %-a vegyes |
| --- | --- |
| Olvassa el a Lite |35 |
| Olvassa el közepes |20 |
| Olvasás (nagy erőforrásigényű) |5 |
| Lite Update |20 |
| Frissítés (nagy erőforrásigényű) |3 |
| Lite beszúrása |3 |
| (Nagy erőforrásigényű) beszúrása |2 |
| Törlés |2 |
| Processzor (nagy erőforrásigényű) |10 |

### <a name="users-and-pacing"></a>Felhasználók és pacing

A javasolt számítási feladatok az alkalmazások egy eszközt, amely elküldi a tranzakciók több kapcsolatok szimulálásához egy egyidejű felhasználók száma. Bár a kapcsolatok és a tranzakció összes gép jön létre, az egyszerűség kedvéért nevezzük ezeket a kapcsolatokat felhasználóként"." Bár az egyes felhasználók működése független a többi felhasználó, minden felhasználó az azonos ciklus az alábbi lépéseket hajtsa végre:

1. Adatbázis-kapcsolat létesítéséhez.
2. Mindaddig, amíg kilép jelzést ismételje meg:
   - Válasszon ki egy tranzakciót, véletlenszerű (az egy súlyozott elosztás).
   - A kijelölt tranzakció végrehajtása, és a válaszidő mérése.
   - Várjon, amíg pacing késést.
3. Zárja be az adatbázis-kapcsolat.
4. Kilépés.

Véletlenszerűen kiválasztott a pacing késleltetése (2/c. lépés), de terjesztési az 1.0-ás másodpercenként átlagosan, amely rendelkezik. Így minden felhasználó, az átlagos generálhat legfeljebb egy-egy tranzakció / másodperc.

### <a name="scaling-rules"></a>Skálázási szabályokkal

A felhasználók száma (a mértéktényező egység) az adatbázis mérete határozza meg. Nincs minden öt mértéktényező egységet egy felhasználót. A pacing késedelem miatt egy felhasználói hozhat létre legfeljebb egy-egy tranzakció, másodpercenként átlagosan.

Például egy méretezési csoport tényezős az 500-as (SF = 500) adatbázis 100 felhasználóval fog rendelkezni, amely is 100 TPS maximális száma. Egy újabb TPS meghajtó arány szükséges további felhasználók és a egy nagyobb adatbázist.

### <a name="measurement-duration"></a>Mérési időtartama

Egy érvényes teljesítményteszt futtatása egyenletes mérési időtartama: legalább egy órát igényel.

### <a name="metrics"></a>Mérőszámok

A legfontosabb metrikákat, a teljesítményteszt átviteli sebességet és válaszidőt.

- Átviteli sebesség a teljesítményteszt alapvető teljesítményének mérték. Átviteli sebesség tranzakció /-időegység, minden tranzakciótípusok számbavételi jelenti.
- Válaszidő azt méri, teljesítmény kiszámíthatóságot nyújtanak. A válasz ideje korlátozás függ-szolgáltatás, amelynek szigorúbb válasz ideje követelmény, hogy ahogy az alábbi szolgáltatás magasabb osztályok osztályát.

| A szolgáltatás osztályban | Átviteli sebesség mérték | Válasz ideje követelmény |
| --- | --- | --- |
| Prémium |Másodpercenkénti tranzakciók |0,5 másodperc, a 95. percentilis |
| Standard |Percenkénti tranzakciók |90. százalékértékre, 1.0-ás másodperc |
| Alapszintű |Óránkénti tranzakciók |80. százalékos érték a 2.0-s másodperc |

## <a name="next-steps"></a>További lépések

- Részletek az adott számítási méretek és a tároló mérete lehetőségek az önálló adatbázisok számára elérhető, lásd: [SQL adatbázis DTU-alapú erőforráskorlátok az önálló adatbázisok](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes).
- Részletek az adott számítási méretek és a tároló mérete választható rugalmas készletek számára elérhető, lásd: [SQL Database DTU-alapú erőforráskorlátok](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
