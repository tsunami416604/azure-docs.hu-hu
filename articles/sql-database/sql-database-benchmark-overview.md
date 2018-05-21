---
title: Az Azure SQL Database DTU teljesítménytesztek áttekintése
description: Ez a témakör ismerteti az Azure SQL adatbázis teljesítményteszt használni a Azure SQL adatbázis teljesítménye.
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: article
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: a045a545b8a0d4dda33dd404d0d206723eb822ad
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="azure-sql-database-dtu-benchmark-overview"></a>Az Azure SQL Database DTU teljesítménytesztek áttekintése
## <a name="overview"></a>Áttekintés
A DTU-alapú alapjául szolgáló vásárlási modell, a Microsoft Azure SQL Database nyújt három [szolgáltatásszintek](sql-database-service-tiers-dtu.md) különböző teljesítményszintek. Egyes tartalmaz egy növekvő erőforrásokhoz, vagy a "power", egyre nagyobb átviteli sebesség továbbít.

> [!IMPORTANT]
> Az Azure SQL Database mostantól támogatja a vCore-alapú alapjául szolgáló vásárlási modell (előzetes verzió). További információ: [alapjául szolgáló vásárlási modell vCore alapú](sql-database-service-tiers-vcore.md).

Fontos tudni számlálása hogyan fordítja le a növekvő hatványra emelésének egyes megnövekedett adatbázis teljesítménye. A Microsoft ehhez fejlesztett ki, az Azure SQL adatbázis Referenciaalap (ASDB). A javasolt gyakorolja vegyesen található összes OLTP-munkaterhelések az alapvető műveleteket. Az átviteli sebesség teljesíteni az egyes futó adatbázisok mérjük.

Az erőforrások és a tápkábelek minden egyes szolgáltatást és teljesítményszintet szint szerint van megadva, a [adatbázis-tranzakciós egységek (dtu-k)](sql-database-what-is-a-dtu.md). Dtu-k lehetővé teszik a Processzor, memória, kevert mértéke alapján teljesítményszintjének relatív kapacitását és és olvasási sebességet egyes által kínált. Így dupla a DTU besorolása adatbázis megfelel az adatbázis power így dupla. A javasolt lehetővé teszi a tényleges adatbázis-művelet gyakorló adatbázis mérete, a felhasználók számát, és az adatbázis biztosított erőforrások arányában tranzakciós díjakat biztosítanak méretezés közben által egyes által kínált növekvő power adatbázis teljesítményének gyakorolt hatásának.

Az átviteli sebessége alapszintű rétegben kifejező által használatával tranzakciók-óránként, a Standard szolgáltatási rétegben percalapú tranzakciók használatával és a prémium szolgáltatásszintet tranzakciók másodpercenkénti, egyszerűbbé teszi a teljesítmény gyorsan kapcsolódik az egyes szolgáltatásszinteken az alkalmazás követelményeinek esetleges használatával.

## <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Teljesítményteszt eredmények válaszoknak az összekapcsolása a valós életben adatbázis teljesítménye
Fontos tisztában lenni azzal, hogy ASDB, például minden referenciaalapok reprezentatív és tájékoztató csak. A tranzakciós díjakat biztosítanak a referenciaalap alkalmazással elért nem lesz ugyanazok, mint, előfordulhat, hogy érhető el, egyéb alkalmazásokkal. A javasolt típusok futtatni egy séma tartalmazó táblák és adattípusokat számos különböző tranzakció gyűjteményét tartalmazza. A javasolt gyakorolja az ugyanazon alapvető műveleteket, amelyek közösek az OLTP-munkaterhelések, amíg nem jelent bármely adatbázis vagy alkalmazás adott osztály. A javasolt célja adjon meg egy ésszerű útmutató relatív teljesítménye, egy adatbázist, amely várható amikor felfelé vagy lefelé skálázás teljesítményszintek között. A valóságban adatbázisok különböző méretű és összetettségét, munkaterhelések különböző keverékei előforduló, és különböző módokon reagál. Például egy IO-igényes alkalmazás előfordulhat, hogy hamarabb találati IO küszöbértékeket, vagy egy processzorigényes alkalmazás előfordulhat, hogy hamarabb találati CPU-korlátok. Nem biztos, hogy bármely adott adatbázis méretezni a terheléssel végezzenek alapként azonos módon van.

A referenciaalap és a módszert az alábbi részletesebben ismerteti.

## <a name="benchmark-summary"></a>Teljesítményteszt összegzése
ASDB méri az online tranzakció-feldolgozási (OLTP) munkaterhelések leggyakrabban előforduló alapszintű adatbázis-műveletek vegyesen teljesítményét. Bár a referenciaalap úgy van kialakítva, a számítógép-használatról szem előtt, az adatbázis-séma, az adatok feltöltése a felhőalapú, és a tranzakciók készített körben jellemző az OLTP-munkaterhelések leggyakrabban használt alapvető elem lehet.

## <a name="schema"></a>Séma
A séma elég különböző és összetettsége műveletek széles körének támogatására szolgál. A javasolt fut egy adatbázist hat táblák áll. A táblák három kategóriába sorolhatók: rögzített méretű, méretezés és növekedését. Két rögzített méretű táblák; három méretezési táblák; és egy egyre bővülő tábla. Rögzített méretű sorok számának állandó kell. Méretezési azzal arányos, hogy az adatbázis teljesítményét, de nem változtatja meg a referenciaalap során számosságuk kell. A növekvő tábla mérete, például egy méretezési táblázat a kezdeti betöltés, de a cardinality változások során a referenciaalap futó sort beszúrni, és törölni.

A séma tartalmaz vegyesen adattípusok, beleértve az egész szám, numerikus, a karakter, és a dátum/idő. A séma tartalmazza az elsődleges és másodlagos kulcsok, de nem idegen kulcsokkal – Ez azt jelenti, hogy nincsenek hivatkozási integritási megkötés táblák között.

Adatok generálása program állít elő, a kezdeti adatbázis adatai. Az egész és numerikus adatokat a különböző stratégiák jön létre. Bizonyos esetekben az értékek vannak osztva véletlenszerűen tartományban. Más esetekben az értékek egy halmazát van véletlenszerűen a(z) kell biztosítják, hogy egy adott terjesztési. Szövegmező eredményezett reális kinézetű adatokat szavak súlyozott listájából jönnek létre.

Az adatbázis mérete alapján egy "Mérettényező." A méretezési tényező (KB rövidítése) számossága alapján a méretezés és táblák növekvő határozza meg. Alább részben leírt a felhasználók és Pacing, az adatbázis méretének felhasználók számát, és minden arányosan méretezni a legjobb teljesítmény.

## <a name="transactions"></a>Tranzakciók
A munkaterhelés kilenc tranzakciótípusok áll, az alábbi táblázatban látható módon. Minden tranzakció célja, hogy jelöljön ki egy meghatározott adatbázis-motor és a rendszer hardver, kontrasztos megjelenítés a más tranzakciók a rendszer jellemzőit. Ez a megközelítés megkönnyíti a különböző összetevőket az általános teljesítményt hatásának értékelése. Például a "Olvasás nehéz" tranzakció eredménye olyan jelentős lemezről az olvasási műveletek.

| Tranzakció típusa | Leírás |
| --- | --- |
| Olvassa el a Lite |VÁLASSZA KI A; a memóriában; csak olvasható |
| Olvasási közepes |VÁLASSZA KI A; legtöbbször a memóriában; csak olvasható |
| Olvasási gyakori |VÁLASSZA KI A; többnyire nincs a memóriában; csak olvasható |
| A frissítés Lite |FRISSÍTÉS; a memóriában; olvasási és írási |
| Gyakori frissítése |FRISSÍTÉS; többnyire nincs a memóriában; olvasási és írási |
| INSERT Lite |INSERT; a memóriában; olvasási és írási |
| Gyakori beszúrása |INSERT; többnyire nincs a memóriában; olvasási és írási |
| Törlés |TÖRLÉS; a memóriában, és nem memóriában; olvasási és írási |
| CPU gyakori |VÁLASSZA KI A; a memóriában; CPU-terhelés viszonylag nagy mennyiségű; csak olvasható |

## <a name="workload-mix"></a>Munkaterhelés-vegyes
Tranzakciók véletlenszerűen kiválasztott egy súlyozott terjesztési a következő általános kettőn együtt. A teljes vegyes van egy olvasási/írási arányt körülbelül 2:1.

| Tranzakció típusa | Vegyes % |
| --- | --- |
| Olvassa el a Lite |35 |
| Olvasási közepes |20 |
| Olvasási gyakori |5 |
| A frissítés Lite |20 |
| Gyakori frissítése |3 |
| INSERT Lite |3 |
| Gyakori beszúrása |2 |
| Törlés |2 |
| CPU gyakori |10 |

## <a name="users-and-pacing"></a>Felhasználók és pacing
A referenciaalap munkaterhelés célja egy eszköz, amellyel a tranzakciók közötti kapcsolatok egy egyidejű felhasználók száma szimulálásához készlete. Bár a kapcsolatok és a tranzakciók összes gép jön létre, az egyszerűség érdekében ezek a kapcsolatok lesz az "felhasználók". Bár minden felhasználó működése független a többi felhasználó, a minden felhasználó az azonos ciklus az alábbi lépéseket hajtsa végre:

1. Egy adatbázis-kapcsolat létrehozásához.
2. Ismételje meg a mindaddig, mígnem való kilépéshez jelzi:
   * Véletlenszerű (a súlyozott terjesztési) egy olyan tranzakciót választott.
   * A kijelölt tranzakció végrehajtása, és mérheti a válaszidőt.
   * Várjon, amíg pacing késést.
3. Zárja be az adatbázis-kapcsolatot.
4. Kilépés.

A pacing késleltetése (2/c. lépés) véletlenszerűen kiválasztott-e, de a terjesztési, amely rendelkezik 1.0-ás másodpercenként átlagosan. Így minden felhasználóhoz, átlagosan generálhat másodpercenként legfeljebb egy tranzakciót.

## <a name="scaling-rules"></a>Skálázási szabályok
A felhasználók száma (Mérettényező egységekben) az adatbázis mérete határozza meg. Nincs minden öt Mérettényező egységek egy felhasználó. A pacing késedelem miatt egy felhasználó hozhat létre a legfeljebb egy tranzakciót másodpercenként átlagosan.

Például egy méretezési tényezős 500 (KB = 500) adatbázis 100 felhasználót lesz, és a 100 TP-k maximális sebesség érhető el. A magasabb TP-k meghajtó arány előírja, hogy több felhasználó és egy nagyobb adatbázist.

Az alábbi táblázat az egyes szolgáltatást és teljesítményszintet ténylegesen szűk felhasználók számát jeleníti meg.

| Szolgáltatási réteg (teljesítményszint szükséges) | Felhasználók | Adatbázisméret |
| --- | --- | --- |
| Alapszintű |5 |720 MB |
| Standard (S0) |10 |1 GB |
| Standard (S1) |20 |2.1 GB |
| Standard (S2) |50 |7.1 GB |
| Prémium (P1) |100 |14 GB |
| Prémium (P2) |200 |28 GB |
| Prémium (P6) |800 |114 GB |

## <a name="measurement-duration"></a>Mérési időtartama
Egy érvényes teljesítményteszt futtatásához igényel a stabil állapot mérési időtartama legalább egy órát.

## <a name="metrics"></a>Mérőszámok
Az alapvető metrikákat a teljesítményteszt olyan átviteli sebesség és a válaszidőt.

* Az alapvető teljesítménymérés a teljesítményteszt esetén átviteli sebesség. Átviteli sebesség jelentésekről--időegység, minden tranzakciótípus számbavételi tranzakciókat.
* Válaszidő teljesítmény kiszámíthatóságot mérőszáma. A válasz időkorlát osztály szolgáltatást, magasabb osztályok szigorúbb válasz idő követelmény, hogy alább látható módon szolgáltatás függ.

| Szolgáltatás osztálya | Átviteli sebesség mérték | Válasz idő követelmény |
| --- | --- | --- |
| Prémium |Másodpercenkénti tranzakciók |95. százalékos érték 0,5 másodperc: |
| Standard |Percenkénti tranzakciók |90. százalékos érték 1.0-ás másodperc: |
| Alapszintű |Óránkénti tranzakciók |80 PERCENTILIS 2.0 másodperc: |

## <a name="conclusion"></a>Összegzés
Az Azure SQL adatbázis teljesítményteszt méri az Azure SQL Database keresztül elérhető szolgáltatásszintek és teljesítményszintek tartományán relatív teljesítménye. A javasolt alapszintű adatbázis-műveletek, az online tranzakció-feldolgozási (OLTP) munkaterhelések leggyakrabban előforduló vegyesen gyakorolja. Tényleges teljesítményének mérése, a referenciaalap biztosít a részletesebb elemzés milyen mértékben hat az átviteli sebességének módosítása teljesítményszintjét, mint ha csak az egyes szinteken, például a Processzor sebessége, a memóriaméret és a IOPS biztosított erőforrások listázása. A jövőben folytatjuk azt fejleszteni a teljesítményteszt hatókörében bővíthetők, és bontsa ki a megadott adatokat.

## <a name="resources"></a>További források
[SQL-adatbázis bemutatása](sql-database-technical-overview.md)

[DTU-alapú szolgáltatásszintjei és teljesítményszintjei](sql-database-service-tiers-dtu.md)

[vCore-alapú szolgáltatásszintjei és teljesítményszintjei](sql-database-service-tiers-vcore.md)

[Az önálló adatbázisok teljesítményének útmutató](sql-database-performance-guidance.md)
