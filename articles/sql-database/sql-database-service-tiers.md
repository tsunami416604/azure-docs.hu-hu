---
title: Vásárlási modellek az Azure SQL Database |} A Microsoft Docs
description: Ismerje meg az Azure SQL Database-modell megvásárlása.
services: sql-database
author: CarlRabeler
ms.service: sql-database
ms.custom: DBs & servers
ms.topic: conceptual
ms.date: 07/19/2018
manager: craigg
ms.author: carlrab
ms.openlocfilehash: 33d23de2cd0ddae95a34c2c9f7acabdc7315cd36
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2018
ms.locfileid: "39160035"
---
# <a name="azure-sql-database-purchasing-models-and-resources"></a>Az Azure SQL Database vásárlási modellek és erőforrások 

A logikai kiszolgálók [Azure SQL Database](sql-database-technical-overview.md) számítási, tárolási és i/o-erőforrások két vásárlási modellt kínál: a DTU-alapú vásárlási modell és a egy Virtuálismag-alapú vásárlási modell. 

> [!NOTE]
> [Felügyelt példányok](sql-database-managed-instance.md) az Azure SQL Database kínálnak a Virtuálismag-alapú vásárlási modell.

Az alábbi táblázatos és hasonlítsa össze két vásárlási modell.

> [!IMPORTANT]
> Virtuálismag-alapú vásárlási modell, lásd: [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md)

|**Beszerzési modell**|**Leírás**|**A legjobb**|
|---|---|---|
|DTU-alapú modell|Ez a modell csomagolt méri, számítási, tárolási és i/o-erőforrások alapján. A teljesítményszintek különálló adatbázisok esetében DTU-k (adatbázis-tranzakciós egységek), rugalmas készletek esetében pedig rugalmas DTU-k formájában vannak meghatározva. További információ a dtu-król és Edtu-: [Mik a dtu-król és edtu-k](sql-database-service-tiers.md#what-are-database-transaction-units-dtus)?|A legjobb erőforrás egyszerű, előre konfigurált beállítások használni.| 
|vCore-alapú modell|Ez a modell lehetővé teszi, hogy egymástól függetlenül méretezheti a számítási és tárolási erőforrások. Lehetővé teszi az SQL Server Azure Hybrid Benefit használatával költséget takaríthat meg.|Legjobb ügyfelek esetében, akik a rugalmasságot, a szabályozhatóság, és értékét.|
||||  

![díjszabási modell](./media/sql-database-service-tiers/pricing-model.png)

## <a name="vcore-based-purchasing-model"></a>Virtuálismag-alapú vásárlási modell 

Egy virtuális magot és generációk hardver lehetőség elérhető logikai CPU jelöli. A Virtuálismag-alapú vásárlási modell biztosítja a rugalmasságot, a vezérlő, az egyéni erőforrás-használat átláthatósága és közvetlen módon lefordítani a helyszíni tevékenységprofil követelményeinek a felhőben. Ez a modell lehetővé teszi méretezheti a számítási, memória és a tárolási számítási feladatok igényeik alapján. A Virtuálismag-alapú vásárlási modell, az ügyfelek választhatnak, hogy általános célú és üzleti kritikus szolgáltatási szintet is [önálló adatbázisok](sql-database-single-database-scale.md) és [rugalmas készletek](sql-database-elastic-pool.md). 

A Virtuálismag-alapú vásárlási modell lehetővé teszi, hogy egymástól függetlenül méretezheti a számítási és tárolási erőforrások, a helyszíni teljesítmény és optimalizálás ár. Ha az adatbázis vagy a rugalmas készlet dtu-k több mint 300 átalakítása virtuális mag fogyaszt csökkentheti költségeit. A választott API-val vagy az Azure Portallal, leállás nélkül konvertálhatja. Azonban átalakítás, nem szükséges. Ha a DTU-vásárlási modell felel meg a teljesítmény- és üzleti követelményeinek, akkor továbbra is használja azt. Ha úgy dönt, hogy a DTU-modellből átalakítása virtuális mag – modell, választhat a teljesítményszintet, használja a következő tapasztalatok: minden egyes 100 DTU standard szintű csomag szükséges általános célú csomagban; legalább 1 virtuális mag minden egyes 125 DTU prémium szintű üzletileg kritikus legalább 1 virtuális mag szükséges.

A Virtuálismag-alapú vásárlási modell, az ügyfeleknek kell fizetniük:
- COMPUTE (szolgáltatási szint + virtuális mag + hardvertől száma) *
- Típus és az adat- és naplófájlok tárterület mérete 
- IOS-es ** száma
- Biztonsági másolatot készíteni tárolás (RA-GRS) ** 

\* A kezdeti nyilvános előzetes verzióban érhető el a Gen 4 logikai CPU alapján Intel E5-2673 v3 (Haswell) 2,4 GHz-es processzorral.

\*\* Az előzetes időszakban a biztonsági másolatok és az IOs 7 napnyi használata ingyenes.

> [!IMPORTANT]
> Számítási, IOs-, adat, és a naplók tárolásához díját adatbázis vagy a rugalmas készletet. Biztonsági másolatok tárolási számlázása az egyes adatbázisokhoz. További részleteket a felügyelt példány díjak, [Azure SQL Database felügyelt példányába](sql-database-managed-instance.md).
> **Régió korlátozások:** a Virtuálismag-alapú vásárlási modell még nem áll rendelkezésre a következő régiókban: Nyugat-Európa, közép-Franciaország, Egyesült Királyság déli régiója, Egyesült Királyság nyugati régiója és Délkelet-Ausztrália.

## <a name="dtu-based-purchasing-model"></a>DTU-alapú vásárlási modell

Az adatbázis tranzakciós egység (DTU) a CPU összesített mérésén jelöli, memória, olvas és ír. A DTU-alapú vásárlási modell előre konfigurált kötegek számítási erőforrások olyan készletét kínálja, és belefoglalt tárterület teljesítményszintek különböző alkalmazásteljesítmény. Ügyfelek, akik fizetésével, rögzített nagyságú befizetések és a egy előre konfigurált csomaggal havonta, előfordulhat, hogy keresse meg a DTU-alapú modell jobban megfelel az igényeiknek. A DTU-alapú vásárlási modell, az ügyfelek választhatnak, hogy **alapszintű**, **Standard**, és **prémium** szolgáltatásszintek egyaránt [önálló adatbázisok](sql-database-single-database-scale.md) és [rugalmas készletek](sql-database-elastic-pool.md). 

### <a name="what-are-database-transaction-units-dtus"></a>Mik azok az adatbázis-tranzakciós egységek (Dtu)?
Egy adott szinten belül egyetlen Azure SQL-adatbázishoz egy [szolgáltatásszint](sql-database-single-database-scale.md), a Microsoft garantálja az adatbázishoz (függetlenül a minden más adatbázis az Azure-felhőben) erőforrások bizonyos szintű biztosít egy kiszámítható teljesítmény szintje. Erőforrás-mennyiséget egy adatbázis-tranzakciós egységek vagy a dtu-k számát számítjuk, és a egy csomagolt számítási, tárolási és i/o-erőforrások mértékegysége. E három erőforrás aránya eredetileg által meghatározott egy [OLTP-számításifeladat módszertanával](sql-database-benchmark-overview.md), tervezett, amely valós OLTP számítási feladatok. Ha a számítási feladat a következő erőforrások mennyisége meghaladja, az átviteli sebesség, szabályozott – a lassabb teljesítménye és időtúllépések eredményül kapott. A számítási feladatok által használt erőforrások nem érinti a többi SQL-adatbázisok Azure-felhőszolgáltatás számára elérhető erőforrások és egyéb számítási feladatok által használt erőforrások nem érinti az SQL-adatbázis számára elérhető erőforrások.

![határolókeret](./media/sql-database-what-is-a-dtu/bounding-box.png)

Dtu-k leghasznosabbak megértéséhez a relatív, különböző teljesítményszintek az Azure SQL Database és a szolgáltatási szintek közötti erőforrás-mennyiséggel. Ha például a Dtu megduplázása egy adatbázis teljesítményszintjének növelésével-adatbázisnak felel meg az adatbázis számára elérhető erőforrások készletét kétszeresére. Például egy 1750 DTU-val rendelkező Prémium P11 adatbázis 350x több DTU számítási teljesítményt nyújt, mint egy 5 DTU-val rendelkező Alapszintű adatbázis.  

Mélyebb betekintést nyerhet a (DTU) erőforrás-használat, a számítási feladat, használja a [Azure SQL Database lekérdezési Terheléselemző](sql-database-query-performance.md) való:

- A leggyakoribb lekérdezések azonosíthatja a CPU/időtartama/végrehajtások száma, amely potenciálisan hangolásával a jobb teljesítmény érdekében. Például az intenzív i/o-lekérdezések használata előnyös lehet [memórián belüli optimalizálási technikákat](sql-database-in-memory.md) , hogy jobban kihasználja a rendelkezésre álló memória bizonyos szolgáltatási szinten szolgáltatásszintet és teljesítményszintet.
- A lekérdezés részleteinek feltárásához, megtekintheti az szöveg- és erőforrás-használat előzményeit.
- Hozzáférés teljesítmény-finomhangolási ajánlásait, amely által végrehajtott műveletek megjelenítése [az SQL Database Advisor](sql-database-advisor.md).

Módosíthatja [DTU szolgáltatásszintek](sql-database-service-tiers-dtu.md) minimális állásideje (általában átlagosan kevesebb mint négy másodperc) az alkalmazás tetszőleges időpontban. Számos vállalkozás és alkalmazás számára elegendő az, ha adatbázisokat tudnak létrehozni, majd azok teljesítményét szükség szerint felfelé és lefelé tudják skálázni – különösen akkor, ha a használati minták viszonylag jól jelezhetők előre. Azonban előre nem látható használati minták esetén nehézségekbe ütközhet a költségek és az üzleti modell kezelése. Ebben a forgatókönyvben egy rugalmas készlet és a egy bizonyos számú edtu-t, amely a készlet több adatbázis között vannak megosztva használja.

![Bevezetés az SQL Database-be: önálló adatbázis DTU-k rétegek és szintek szerint](./media/sql-database-what-is-a-dtu/single_db_dtus.png)

### <a name="what-are-elastic-database-transaction-units-edtus"></a>Mik azok a rugalmas Database Transaction Unitok (Edtu)?
Helyett adja meg a dedikált erőforráskészletek (dtu-k), előfordulhat, hogy nem mindig lehet szükséges, amely mindig elérhető SQL-adatbázishoz, mint helyezheti őket egy [rugalmas készlet](sql-database-elastic-pool.md) egy SQL Database-kiszolgálón, amely erőforráskészletekbe szervezhető között megosztja Ezeket az adatbázisokat. A rugalmas készletek megosztott erőforrásainak rugalmas adatbázis-tranzakciós egységek vagy edtu-k vannak megadva. Rugalmas készletek több adatbázis, amelyek felhasználási módja nagy mértékben és kiszámíthatatlanul teljesítménybeli céljainak kezelésére egy egyszerű és költséghatékony megoldást kínálnak. Rugalmas készlet garantálja, hogy a készletben egy adatbázis nem felhasznált erőforrásokat, amíg a készletben lévő minden egyes adatbázis biztosítása minden esetben rendelkezik egy minimálisan szükséges erőforrások rendelkezésre. 

![Bevezetés az SQL Database-be: eDTU-k rétegek és szintek szerint](./media/sql-database-what-is-a-dtu/sqldb_elastic_pools.png)

A készlethez adott számú edtu-k rögzített áron. A rugalmas készleten belül az önálló adatbázisok az automatikus méretezés rugalmasságával rendelkeznek a konfigurált határok között. Egy adatbázis nagyobb terhelés alatt további edtu-t, igény szerint fog felhasználni. Világosabb terhelések adatbázisoknak kevesebb edtu-k. Semmilyen terhelést az adatbázisok edtu-k nem használnak fel. Az erőforrásoknak a teljes készlet számára, helyett adatbázisonként, felügyeleti feladatok egyszerűsítettek, így kiszámítható költségekkel a készlet.

A meglévő készletekhez további eDTU-k is hozzáadhatók anélkül, hogy a készlet adatbázisai leállnának, vagy bármilyen hatás érné őket. Ugyanígy ha az eDTU-kra már nincs szükség, bármikor el is távolíthatók a meglévő készletekből. Adhat hozzá vagy kivonása a készlethez adatbázisok vagy korlátot az edtu-k egy adatbázis mennyiségét használatával nagy terhelés alatt fenntartott edtu-kat más adatbázisok számára. Ha egy adatbázist a kiszámítható erőforrásokat, kiveheti a készlet szabadon azt konfigurálja a szükséges erőforrások kiszámítható mennyiségű egy önálló adatbázist.

### <a name="how-can-i-determine-the-number-of-dtus-needed-by-my-workload"></a>Hogyan állapíthatom meg a számítási feladataimhoz szükséges DTU-k számát?
Ha meglévő helyszíni vagy SQL-kiszolgálói virtuális gépeken futó számítási feladatokat kíván áthelyezni az Azure SQL Database-be, a [DTU-kalkulátor](http://dtucalculator.azurewebsites.net/) segítségével megbecsülheti a szükséges DTU-k számát. Használhat meglévő Azure SQL Database számítási feladat [SQL Database lekérdezési Terheléselemző](sql-database-query-performance.md) tudni, hogy az adatbázis erőforrás-használat (dtu-k) mélyebb betekintést a számítási feladatok optimalizálásához. Is használhatja a [sys.dm_db_ resource_stats](https://msdn.microsoft.com/library/dn800981.aspx) DMV megtekintése az erőforrás-használat az elmúlt egy óra. Másik lehetőségként a katalógusnézet [sys.resource_stats](http://msdn.microsoft.com/library/dn269979.aspx) erőforrás-használat az elmúlt 14 napra vonatkozó, de átlagosan öt perces átlagolással jeleníti meg.

### <a name="how-do-i-know-if-i-could-benefit-from-an-elastic-pool-of-resources"></a>Honnan tudom, hogy hasznát venném-e egy rugalmas erőforráskészletnek?
A készleteket nagy számú, speciális felhasználási mintákkal rendelkező adatbázishoz tervezték. Egy adott adatbázis esetében ez a minta egy viszonylag rendszertelen kiugró kihasználtság jellemzi az alacsony kihasználtságú átlagos jellemzi. Az SQL Database automatikusan kiértékeli az SQL Database-kiszolgálók adatbázisainak erőforrás-használati előzményeit, és felajánlja a megfelelő készletkonfigurációt az Azure Portalon. További információkért lásd: [Mikor érdemes rugalmas készletet használni?](sql-database-elastic-pool.md)

### <a name="what-happens-when-i-hit-my-maximum-dtus"></a>Mi történik, ha saját maximális dtu-k?
Teljesítményszintek korlátokon belül és utasítva, hogy biztosítsák az adatbázis futtatásához a kiválasztott szolgáltatás/teljesítményszint számára engedélyezett maximális legfeljebb szükséges erőforrásokat. A számítási feladat eléri a Processzor/adatátvitel IO és naplózási IO-korlátok bármelyikét, ha továbbra is megkapják azt a maximális szintet az erőforrások megengedett, de valószínűleg is tapasztalható nagyobb lekérdezési késéseket. Ezek a korlátok nem okoznak hibákat, csak a számítási feladat elvégzésének lassulását. Egyes esetekben azonban a lassulás olyan jelentős lehet, hogy a lekérések időtúllépés miatt meghiúsulnak. Ha eléri a maximális engedélyezett egyidejű felhasználói munkamenetek és kérések (munkaszálak), látni fogja a explicit hibákat. Lásd: [Azure SQL Database erőforrás-korlátozások]( sql-database-resource-limits.md#what-happens-when-database-resource-limits-are-reached) nem kapcsolódik a CPU, memória, adat IO vagy tranzakció naplózási IO erőforráskorlátok tájékoztatást.

### <a name="correlating-benchmark-results-to-real-world-database-performance"></a>Számításiteljesítmény-mérési eredmények naplókezelője valós adatbázis teljesítménye
Fontos megérteni, hogy minden a referenciaértékek reprezentatív és tájékoztató csak. A tranzakciós díjakat biztosítanak a teljesítményteszt alkalmazással elért nem ugyanazok, mint, előfordulhat, hogy más alkalmazásokkal érhető el. A teljesítményteszt típusok futtatásához egy sémát tartalmazó táblák és adattípusok számos különböző tranzakció gyűjteményét foglalja magában. A teljesítményteszt gyakorol az ugyanazon alapvető műveleteket, amelyek közösek az összes OLTP-munkaterhelések, amíg nem tartozik adatbázis vagy az alkalmazás bármely adott osztály. A teljesítményteszt az a célja, hogy adjon meg egy ésszerű útmutató, amellyel egy adatbázis, amely a várható vertikális felfelé vagy lefelé teljesítményszintek között egymáshoz viszonyított teljesítményébe. A valóságban ez adatbázisok különböző méretekre és összetettséget, számítási feladatok különböző keverékei észlel, és különböző módokon válaszol. Például i/o-igényes alkalmazások i/o-küszöbértékek hamarabb ütközhet, vagy a CPU-igényes alkalmazások CPU korlátok hamarabb tapasztalhat. Nem biztos, hogy bármely adott adatbázis lesz skálázva a ugyanúgy, mint a teljesítményteszt növekvő terhelés alatt van.

A teljesítményteszt és a módszereket ismerteti részletesebben az alábbi.

### <a name="benchmark-summary"></a>Számításiteljesítmény-mérési összefoglaló
ASDB többféle, az online tranzakciófeldolgozási (OLTP) munkaterhelések leggyakrabban előforduló alapszintű adatbázis-műveletek teljesítményének méri. Bár a teljesítményteszt a felhő-számítástechnika a szem előtt, az adatbázis-séma, az adatokkal való feltöltés célja, és a tranzakciók széles körben reprezentatívnak a leggyakrabban használt OLTP számítási feladatokat a alapszintű elemek is úgy lett kialakítva.

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
   * Válasszon ki egy tranzakciót, véletlenszerű (az egy súlyozott elosztás).
   * A kijelölt tranzakció végrehajtása, és a válaszidő mérése.
   * Várjon, amíg pacing késést.
3. Zárja be az adatbázis-kapcsolat.
4. Kilépés.

Véletlenszerűen kiválasztott a pacing késleltetése (2/c. lépés), de terjesztési az 1.0-ás másodpercenként átlagosan, amely rendelkezik. Így minden felhasználó, az átlagos generálhat legfeljebb egy-egy tranzakció / másodperc.

### <a name="scaling-rules"></a>Skálázási szabályokkal
A felhasználók száma (a mértéktényező egység) az adatbázis mérete határozza meg. Nincs minden öt mértéktényező egységet egy felhasználót. A pacing késedelem miatt egy felhasználói hozhat létre legfeljebb egy-egy tranzakció, másodpercenként átlagosan.

Például egy méretezési csoport tényezős az 500-as (SF = 500) adatbázis 100 felhasználóval fog rendelkezni, amely is 100 TPS maximális száma. Egy újabb TPS meghajtó arány szükséges további felhasználók és a egy nagyobb adatbázist.

Az alábbi táblázat a ténylegesen hosszabb ideig minden szolgáltatási szint és a teljesítmény szint felhasználók számát jeleníti meg.

| Szolgáltatási szint (teljesítmény szint) | Felhasználók | Adatbázisméret |
| --- | --- | --- |
| Alapszintű |5 |720 MB |
| Standard (S0) |10 |1 GB |
| Standard (S1) |20 |2.1 GB |
| Standard (S2) |50 |7.1 GB-OS |
| Prémium szintű (P1) |100 |14 GB |
| Prémium verzió (P2) |200 |28 GB |
| Prémium szintű (P6) |800 |114 GB |

### <a name="measurement-duration"></a>Mérési időtartama
Egy érvényes teljesítményteszt futtatása egyenletes mérési időtartama: legalább egy órát igényel.

### <a name="metrics"></a>Mérőszámok
A legfontosabb metrikákat, a teljesítményteszt átviteli sebességet és válaszidőt.

* Átviteli sebesség a teljesítményteszt alapvető teljesítményének mérték. Átviteli sebesség tranzakció /-időegység, minden tranzakciótípusok számbavételi jelenti.
* Válaszidő azt méri, teljesítmény kiszámíthatóságot nyújtanak. A válasz ideje korlátozás függ-szolgáltatás, amelynek szigorúbb válasz ideje követelmény, hogy ahogy az alábbi szolgáltatás magasabb osztályok osztályát.

| A szolgáltatás osztályban | Átviteli sebesség mérték | Válasz ideje követelmény |
| --- | --- | --- |
| Prémium |Másodpercenkénti tranzakciók |0,5 másodperc, a 95. percentilis |
| Standard |Percenkénti tranzakciók |90. százalékértékre, 1.0-ás másodperc |
| Alapszintű |Óránkénti tranzakciók |80. százalékos érték a 2.0-s másodperc |

## <a name="next-steps"></a>További lépések

- Virtuálismag-alapú vásárlási modell, lásd: [Virtuálismag-alapú vásárlási modell](sql-database-service-tiers-vcore.md)
- A DTU-alapú vásárlási modell, lásd: [DTU-alapú vásárlási modell](sql-database-service-tiers-dtu.md).
