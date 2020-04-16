---
title: Az SQL-készletek ajánlott eljárásoka
description: Javaslatok és gyakorlati tanácsok, amelyeket az SQL-készletekkel való munka közben ismernie kell.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: b3dc111fe62cbae857f3369165ba29cf40e90342
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81427795"
---
# <a name="best-practices-for-sql-pools-in-azure-synapse-analytics"></a>Az SQL-készletek ajánlott eljárásokaz Azure Synapse Analytics szolgáltatásban

Ez a cikk az Azure Synapse Analytics SQL-készletek optimális teljesítményének eléréséhez ajánlott eljárások gyűjteményét tartalmazza. Az alábbiakban alapvető útmutatást és fontos területeket talál, amelyekre a megoldás elkészítése korépíthet. Minden szakasz bemutatja, hogy egy koncepció, majd rámutat, hogy részletesebb cikkeket, amelyek kiterjednek a koncepció mélyebben.

## <a name="sql-pools-loading"></a>SQL-készletek betöltése

Az SQL-készletek betöltési útmutatója az [Útmutató az adatok betöltéséhez](data-loading-best-practices.md)című témakörben található.

## <a name="reduce-cost-with-pause-and-scale"></a>Költségek csökkentése felfüggesztés és méretezés által

A költségek szüneteltetés és méretezés révén történő csökkentéséről a [Számítási feladatok kezelése című témakörben talál](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)további információt.

## <a name="maintain-statistics"></a>Statisztikák karbantartása

Míg az SQL Server automatikusan észleli és létrehozza vagy frissíti az oszlopok statisztikáit, az SQL-készletek a statisztikák manuális karbantartását igénylik. Érdemes megőrizni a statisztikákat annak érdekében, hogy az SQL készlettervek optimalizálva legyenek.  Az optimalizáló által létrehozott csomagok csak annyira jók, amennyire az elérhető statisztikák azok.

> [!TIP]
> A statisztikák megismerésének egyik legjobb módja a mintastatisztikák létrehozása minden oszlopról.  

Ugyanilyen fontos a statisztikák frissítése is, mivel az adatokban jelentős változások történhetnek.  Ennek hagyományos módszere, ha naponta vagy minden betöltés után frissíti a statisztikákat.  Mindig érdemes figyelembe venni, hogyan viszonyul egymáshoz a teljesítmény és a statisztikák létrehozásának és frissítésének költségei.

A statisztikai karbantartási idő lerövidítéséhez legyen szelektív arról, hogy mely oszlopok rendelkeznek statisztikákkal, vagy a leggyakoribb frissítésre van szükség. Előfordulhat például, hogy frissíteni szeretné azolyan dátumoszlopokat, ahol naponta új értékeket adhat hozzá. Összpontosítson arra, hogy az illesztésekben részt vevő oszlopok, a WHERE záradékban használt oszlopok és a GROUP BY oszlopban található oszlopok statisztikái legyenek.

A statisztikákkal kapcsolatos további információk a [Táblastatisztikák kezelése](develop-tables-statistics.md), a [STATISZTIKA LÉTREHOZÁSA](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és a [STATISZTIKA FRISSÍTÉSE](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) című cikkekben találhatók.

## <a name="group-insert-statements-into-batches"></a>INSERT utasítások csoportosítása kötegekbe

Egy egyszeri terhelés egy kis táblába egy `INSERT INTO MyLookup VALUES (1, 'Type 1')`INSERT utasítással, amely az igényeitől függően a legjobb megközelítés lehet. Ha azonban több ezer vagy több millió sort kell betöltenie a nap folyamán, akkor valószínű, hogy a singleton INSERTS nem optimális.

A probléma megoldásának egyik módja egy fájlba író folyamat kifejlesztése, majd egy másik folyamat a fájl rendszeres betöltéséhez. További információt az [INSERT](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) cikkben talál.

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Az adatok gyors betöltése és exportálása a PolyBase segítségével

Az SQL-készlet számos eszköz , például az Azure Data Factory, a PolyBase és a BCP használatával támogatja az adatok betöltését és exportálását.  Kis mennyiségű adat kezelése esetén, ahol a teljesítmény nem kulcsfontosságú tényező, bármelyik eszköz megfelelhet az igényeinek.  

> [!NOTE]
> A Polybase a legjobb választás nagy mennyiségű adat betöltésekor vagy exportálásakor, vagy ha gyorsabb teljesítményre van szüksége.

A PolyBase-betöltések a CTAS vagy az INSERT INTO paranccsal futtathatók. A CTAS minimalizálja a tranzakciónaplózást, és ez a leggyorsabb módja az adatok betöltésének. Az Azure Data Factory támogatja a PolyBase terheléseket is, és a CTAS-hoz hasonló teljesítményt érhet el. PolyBase támogatja a különböző fájlformátumok, beleértve a Gzip fájlokat.

A Gzip szöveges fájlok használata kori átviteli terhelés maximalizálásához bontsa fel a fájlokat 60 vagy több fájlra, hogy maximalizálja a terhelés párhuzamosságát. A gyorsabb teljes átviteli teljesítmény érdekében érdemes lehet egy időben betölteni az adatokat. Az e szakaszra vonatkozó témakörökkel kapcsolatos további információkat az alábbi cikkek tartalmazzák:

- [Adatok betöltése](data-loading-overview.md)
- [Útmutató a PolyBase használatához](data-loading-best-practices.md)
- [Azure SQL-készlet betöltési mintái és stratégiái](https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/)
- [Adatok betöltése az Azure Data Factory segítségével](../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Adatok betöltése az Azure Data Factoryvel](../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="load-then-query-external-tables"></a>Betöltés, majd külső táblák lekérdezése

A Polybase nem optimális lekérdezésekhez. Az SQL-készletek polibázis-táblái jelenleg csak az Azure blobfájlokat és az Azure Data Lake-tárhelyet támogatják. Ezek a fájlok nem rendelkeznek olyan számítási erőforrások at támogatja őket. Ennek eredményeképpen az SQL-készletek nem tudják kiszervezni ezt a munkát, és a teljes fájlt a tempdb betöltésével kell olvasniuk, hogy olvashassa az adatokat.

Ha több lekérdezése van az adatok lekérdezéséhez, akkor jobb, ha ezeket az adatokat egyszer tölti be, és a lekérdezések a helyi táblát használják. További Polibázis-útmutatás t tartalmaz a [PolyBase használatának útmutatója](data-loading-best-practices.md) cikkben.

## <a name="hash-distribute-large-tables"></a>Nagy táblák kivonatos elosztása

Alapértelmezés szerint a táblák ciklikus időszeleteléssel vannak elosztva.   Ez az alapértelmezett beállítás megkönnyíti a felhasználók számára a táblák létrehozását anélkül, hogy el kellene dönteniük, hogyan kell elosztani a táblákat. A ciklikus multiplexelési táblázatok bizonyos munkaterhelések esetén megfelelően teljesíthetnek. De a legtöbb esetben a terjesztési oszlop jobb teljesítményt nyújt.  

A ciklikus multiplexelést végző oszlopok által elosztott tábla leggyakoribb példája két nagy ténytábla összeillesztése.  

Ha például van egy rendelési táblája, amelyet order_id oszt ki, és egy tranzakciótáblát is eloszt order_id, amikor a rendelési táblát a order_id tranzakciótáblához illeszti, ez a lekérdezés átadó lekérdezéssé válik. Az adatmozgatási műveletek ezután megszűnnek. Ha kevesebb lépést kell végrehajtani, felgyorsul a lekérdezési folyamat. A kisebb mértékű adatmozgás is gyorsabb lekérdezéseket eredményez.

> [!NOTE]
> Elosztott tábla betöltésekor a bejövő adatok nem rendezhetők a terjesztési kulcson. Ha így teszel, az lelassítja a terhelést.

Az alábbi cikk hivatkozások további részleteket tartalmaznak a teljesítmény javításáról egy terjesztési oszlop kiválasztásával. A CREATE TABLE utasítás WITH záradékában is talál információt arról, hogyan definiálhat elosztott táblát:

- [Táblázat – áttekintés](develop-tables-overview.md)
- [a táblaelosztással](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [a táblaelosztás kiválasztásával](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/)
- [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="do-not-over-partition"></a>Túl sok partíció használatának kerülése

Míg az adatok particionálása hatékony lehet az adatok partícióváltással vagy a vizsgálatok optimalizálásával a partíció megszüntetése révén történő karbantartásához, a túl sok partíció lelassulhat a lekérdezések.  Gyakran egy nagy részletességű particionálási stratégia, amely jól működik az SQL Server nem működik jól az SQL-készlet.  

Ha túl sok partícióval rendelkezik, csökkentheti a fürtözött oszlopcentrikus indexek hatékonyságát, ha minden partíció 1 millió nál kevesebb sort tartalmaz. Az SQL-készletek automatikusan particionálják az adatokat 60 adatbázisra. Így ha 100 partíciót, az eredmény 6000 partíciókat. Minden számítási feladatok eltérő, ezért a legjobb tanács az, hogy kísérletezzenek particionálás, hogy mi működik a legjobban a számítási feladatokhoz.  

Az egyik lehetőség, hogy fontolja meg egy részletesség, amely alacsonyabb, mint amit az SQL Server használatával megvalósított. Érdemes például heti vagy havi partíciókat használni a napi partíciók helyett.

A particionálásról a [Table particionálás](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) című cikkben olvashat bővebben.

## <a name="minimize-transaction-sizes"></a>Tranzakcióméretek minimalizálása

Az INSERT, UPDATE és DELETE utasítások egy tranzakcióban futnak. Ha kudarcot vallanak, vissza kell őket görgetni. A hosszú visszaállítás lehetőségének csökkentése érdekében lehetőség szerint minimalizálja a tranzakcióméreteket.  A tranzakcióméretek minimalizálása az INSERT, UPDATE és DELETE utasítások részekre osztásával végezhető el. Ha például van egy INSERT, amely várhatóan 1 órát vesz igénybe, akkor az INSERT négy részre bontható. Ezután minden futás 15 percre rövidül.  

> [!TIP]
> A visszaállítási kockázat csökkentése érdekében használja ki a speciális minimális naplózási eseteket, például a CTAS, a Csonkolás, a DROP TABLE vagy az INSERT eseteket az üres táblákhoz.  

A visszaállítások kiküszöbölésének másik módja a csak metaadatokat használó műveletek alkalmazása, például az adatkezelés partícióváltása.  Ha például egy DELETE utasítást futtategy olyan tábla összes sorának törléséhez, ahol a order_date 2001 októberében volt, havonta particionálhatja az adatokat. Ezután kiválthatja a partíciót egy másik táblából származó üres partíció adataival (lásd az ALTER TABLE példákat).  

Particionálatlan táblák esetén a DELETE helyett ctas-t érdemes használni a táblában tartani kívánt adatok írásához.  Ha a CTAS ugyanannyi időt vesz igénybe, sokkal biztonságosabb futtatni, mivel minimális tranzakciónaplózással rendelkezik, és szükség esetén gyorsan visszavonható.

Az e szakaszhoz kapcsolódó tartalommal kapcsolatos további információk az alábbi cikkekben találhatók:

- [Create table as select (CTAS)](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [Tranzakciók megismerése](develop-transactions.md)
- [Tranzakciók optimalizálása](../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [tábla particionálásával](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [TÁBLÁZAT CSONKOLÁSA](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="reduce-query-result-sizes"></a>A lekérdezés eredményméretének csökkentése

A lekérdezési eredmények méretének csökkentése segít elkerülni a nagy lekérdezési eredmények által okozott ügyféloldali problémákat.  A lekérdezés szerkesztésével csökkentheti a visszaadott sorok számát. Egyes lekérdezésgeneráló eszközök lehetővé teszik, hogy "top N" szintaxist adjon az egyes lekérdezésekhez.  A CETAS a lekérdezés eredményét egy ideiglenes táblába is beviheti, majd használhatja a PolyBase exportot az alsószintű feldolgozáshoz.

## <a name="use-the-smallest-possible-column-size"></a>A lehető legkisebb oszlopméret használata

A DDL meghatározásakor használja a legkisebb adattípust, amely támogatja az adatokat, mivel ezzel javítja a lekérdezés teljesítményét.  Ez az ajánlás különösen fontos a CHAR és VARCHAR oszlopok esetében.  Ha egy oszlop leghosszabb értéke 25 karakterből áll, akkor VARCHAR(25) típusként határozza meg az oszlopot.  Ne határozza meg az összes karakteroszlopot nagy alapértelmezett hosszúságértékkel.  Ezenkívül az oszlopokat VARCHAR-ként is definiálhatja, ha az NVARCHAR használata helyett csak ennyire van szükség.

A fenti információkszempontjából releváns alapvető fogalmak részletesebb áttekintését a [Táblázat áttekintése](develop-tables-overview.md), [Táblaadattípusok](develop-tables-data-types.md)és [A TABLE LÉTREHOZÁSA](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) cikkek ben találja.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Ideiglenes halomtáblák használata átmeneti adatokhoz

Ha ideiglenesen adatokat hoz végre az SQL-készleteken, a halommemória-táblák általában gyorsabbá teszik a teljes folyamatot.  Ha csak a további átalakítások futtatása előtt tölti be az adatokat, a tábla halommemória-táblába való betöltése gyorsabb lesz, mint az adatok betöltése egy fürtözött oszlopcentrikus táblába.  

Az adatok ideiglenes táblába való betöltése is sokkal gyorsabban töltődik be, mint egy tábla állandó tárolóba töltése.  Az ideiglenes táblák "#" kezdetűk, és csak az azt létrehozó munkamenet számára érhetők el. Következésképpen csak korlátozott esetben működhetnek. A halomtáblákat a CREATE TABLE utasítás WITH záradékával lehet meghatározni.  Ha ideiglenes táblát használ, ne felejtsen el rajta is statisztikákat létrehozni.

További útmutatásért olvassa el az [Ideiglenes táblák](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és TÁBLA LÉTREHOZÁSA [MINT VÁLASZTÓ](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) cikkeket.

## <a name="optimize-clustered-columnstore-tables"></a>Fürtözött oszlopcentrikus táblák optimalizálása

Fürtözött oszlopcentrikus indexek az egyik leghatékonyabb módja az adatok sql készletben tárolható.  Alapértelmezés szerint az SQL-készletben lévő táblák fürtözött ColumnStore néven jönnek létre.  Annak érdekében, hogy az oszlopcentrikus táblák a lehető legjobb teljesítménnyel fussanak, fontos a jó szegmensminőség.  Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  

A szegmensek minősége a tömörített sorcsoport sorainak számával mérhető. Tekintse meg a [gyenge oszlopcentrikus index minőségének okait](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality) a [Táblázat indexek](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) cikkben a fürtözött oszlopcentrikus táblák szegmensminőségének észlelésével és javításával kapcsolatos lépésenkénti útmutatásért.  

Mivel a kiváló minőségű oszlopcentrikus szegmensek fontosak, célszerű olyan felhasználói azonosítókat használni, amelyek a közepes vagy nagy erőforrásosztályban találhatók az adatok betöltéséhez. Az alacsonyabb [adattárház egységek](resource-consumption-models.md) használata azt jelenti, hogy nagyobb erőforrásosztályt szeretne hozzárendelni a betöltő felhasználóhoz.

Az oszlopcentrikus táblák általában nem adnak adatokat egy tömörített oszlopcentrikus szegmensbe, amíg táblázatonként több mint 1 millió sor nem lesz. Minden SQL-készlettábla particionált 60 táblák. Így az oszlopcentrikus táblák csak akkor részesülnek a lekérdezés számára, ha a tábla több mint 60 millió sort tartalmaz.  

> [!TIP]
> A 60 millió nál kevesebb sort tartalmazó táblák esetében előfordulhat, hogy az oszlopcentrikus index nem az optimális megoldás.  

Ha particionálja az adatokat, minden partíciónak 1 millió sorral kell rendelkeznie a fürtözött oszlopcentrikus index előnyeinek kihasználása érdekében.  Egy 100 partíciót tartalmazó tábla esetén legalább 6 milliárd sorral kell rendelkeznie ahhoz, hogy egy fürtözött oszlopok tárolója részesüljön (60 *disztribúció 100 partíció* 1 millió sor).  

Ha a táblázat nem rendelkezik 6 milliárd sorral, akkor két fő lehetősége van. Csökkentse a partíciók számát, vagy fontolja meg egy halommemória-tábla használatát.  Érdemes lehet kísérletezni, hogy jobb teljesítményt lehet-e szerezni egy halommemória-tábla másodlagos indexek helyett egy oszlopcentrikus tábla használatával.

Oszlopcentrikus tábla lekérdezésekor a lekérdezések gyorsabban futnak, ha csak a szükséges oszlopokat választja ki.  További információ a tábla- és oszlopcentrikus indexek, és megtalálható a [tábla indexek](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json), [Oszlopcentrikus indexek útmutató,](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)és [újraépítése oszlopcentrikus indexek](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rebuilding-indexes-to-improve-segment-quality) cikkek.

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Nagyobb erőforrásosztály használata a lekérdezés teljesítményének javítása érdekében

Az SQL-készletek erőforráscsoportokat használnak a lekérdezések memóriájának lefoglalására. Kezdetben minden felhasználó a kis erőforrásosztályhoz van rendelve, amely disztribúciónként 100 MB memóriát biztosít.  Mindig van 60 disztribúció. Minden disztribúció legalább 100 MB-ot kap. A teljes rendszerszintű memóriafoglalás 6000 MB, azaz alig 6 GB alatt van.  

Bizonyos lekérdezéseknél, például a nagyobb egyesítéseknél vagy a fürtözött oszlopcentrikus táblákba végzett betöltésnél előnyt jelent a nagyobb memórialefoglalások használata.  Egyes lekérdezések, például a tiszta vizsgálatok, nem fognak hasznos akt> számára. A nagyobb erőforrásosztályok felhasználása hatással van az egyidejűségre. Ezért érdemes szem előtt tartaniezeket a tényeket, mielőtt az összes felhasználót egy nagy erőforrás-osztályba helyezne át.

Az erőforrásosztályokról további információt a [számítási feladatok kezeléséhez erőforrásosztályok](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) című cikkben talál.

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Kisebb erőforrásosztály használata az egyidejűség növeléséhez

Ha azt veszi észre, hogy a felhasználói lekérdezések hosszú késést észlelnek, előfordulhat, hogy a felhasználók nagyobb erőforrásosztályokban futnak. Ez a forgatókönyv elősegíti az egyidejűségi tárolóhelyek felhasználását, ami más lekérdezések várólistára kerülését okozhatja.  Annak megállapításához, hogy a `SELECT * FROM sys.dm_pdw_waits` felhasználói lekérdezések várólistára kerülnek-e, futtassa le, hogy lássa, a rendszer visszaadja-e a sorokat.

A számítási feladatok kezeléséhez és a [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) cikkek [erőforrás-osztályok](../sql-data-warehouse/resource-classes-for-workload-management.md) további információkat nyújtanak.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>A lekérdezések megfigyelése és optimalizálása DMV-kkel

Az SQL-készletek több DMV-vel rendelkeznek, amelyek a lekérdezések végrehajtásának figyelésére használhatók.  Az alábbi figyelési cikk részletesen bemutatja, hogyan tekintheti meg a végrehajtó lekérdezés részleteit.  Ha gyorsan szeretne lekérdezéseket kikeresni a DMV-kben, segíthet, ha a lekérdezéseknél használja a LABEL beállítást. További részletes információkért kérjük, olvassa el az alábbi listában szereplő cikkeket:

- [Monitor your workload using DMVs](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

- [LABEL](develop-label.md)
- [Lehetőség](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="next-steps"></a>További lépések

A gyakori problémákat és megoldásokat a [Hibaelhárítás](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) című cikkben találja.

Ha a cikkben nem szereplő információkra van szüksége, a lap bal oldalán található "Docs keresése" segítségével keressen az összes SQL-készletdokumentumban.  Az [SQL pool fórum](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) egy olyan hely, ahol kérdéseket tehet fel más felhasználóknak és az SQL pool termékcsoportnak.  

Aktívan figyeljük ezt a fórumot, és gondoskodunk róla, hogy tőlünk vagy egy másik felhasználótól választ kapjon a kérdéseire.  Ha inkább kérdéseket szeretne feltenni a Veremtúlcsordulással kapcsolatban, akkor van egy [Azure SQL pool stack overflow fórumunk](https://stackoverflow.com/questions/tagged/azure-sqldw)is.

Szolgáltatáskérések esetén használja az [Azure SQL pool visszajelzési lapját.](https://feedback.azure.com/forums/307516-sql-data-warehouse)  A kérések hozzáadása vagy más kérések felszavazása segít abban, hogy a legkeresettebb funkciókra összpontosítsunk.
