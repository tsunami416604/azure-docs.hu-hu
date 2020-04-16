---
title: A Synapse SQL gyakorlati tanácsok
description: Javaslatok és gyakorlati tanácsok, amelyeket a Synapse SQL fejlesztése korként ismernie kell.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 3356817fbaef955e05358f9c92619f3c04f747d7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429615"
---
# <a name="development-best-practices-for-synapse-sql"></a>A Synapse SQL gyakorlati tanácsok
Ez a cikk az adatraktár-megoldás fejlesztése során útmutatást és gyakorlati tanácsokat ismerteti. 

## <a name="development-best-practices-for-synapse-sql"></a>A Synapse SQL gyakorlati tanácsok

### <a name="reduce-cost-with-pause-and-scale"></a>Költségek csökkentése felfüggesztés és méretezés által

A költségek felfüggesztéssel és skálázással való csökkentéséről további információért tekintse meg a [Számítások kezelése](../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) című szakaszt.

### <a name="maintain-statistics"></a>Statisztikák karbantartása

Győződjön meg róla, hogy naponta vagy minden betöltés után frissíti a statisztikáit.  Mindig érdemes figyelembe venni, hogyan viszonyul egymáshoz a teljesítmény és a statisztikák létrehozásának és frissítésének költségei. Ha úgy találja, hogy túl sokáig tart az összes statisztika karbantartása, legyen szelektívebb arról, hogy mely oszlopok rendelkeznek statisztikákkal, vagy mely oszlopokat kell gyakran frissíteni.  

Előfordulhat például, hogy frissíteni szeretné a dátumoszlopokat, ahol naponta új értékekadhatók hozzá. **A legnagyobb hasznot úgy nyerheti meg, ha statisztikával rendelkezik az illesztésekben részt vevő oszlopokról, a WHERE záradékban használt oszlopokról és a GROUP BY-ben található oszlopokról.**

Lásd [még: Táblastatisztika kezelése](develop-tables-statistics.md), [STATISZTIKA LÉTREHOZÁSA](develop-tables-statistics.md), STATISZTIKA [FRISSÍTÉSE][UPDATE STATISTICS].

### <a name="hash-distribute-large-tables"></a>Nagy táblák kivonatos elosztása

Alapértelmezés szerint a táblák ciklikus időszeleteléssel vannak elosztva.  Ez megkönnyíti a felhasználók számára a táblák létrehozását anélkül, hogy el kellene dönteniük, hogyan kell elosztani a táblákat.  A ciklikus multiplexelési táblázatok bizonyos munkaterhelések esetén megfelelően teljesíthetnek. De a legtöbb esetben a terjesztési oszlop kiválasztása sokkal jobban fog teljesíteni.  

A leggyakoribb példája annak, amikor egy oszloppal elosztott tábla sokkal jobb teljesítményt nyújt egy ciklikus időszeletelésűnél, amikor két nagy tábla egyesül.  Ha például van egy rendelési táblája, amelyet order_id oszt szét, és egy tranzakciótábla, amelyet order_id is eloszt, amikor a rendelési táblát a tranzakciók táblához order_id illeszti, ez a lekérdezés átadó lekérdezéssé válik. Ez azt jelenti, hogy kiküszöböljük az adatmozgatási műveleteket.  Ha kevesebb lépést kell végrehajtani, felgyorsul a lekérdezési folyamat.  A kisebb mértékű adatmozgás is gyorsabb lekérdezéseket eredményez.

Elosztott tábla betöltésekor győződjön meg arról, hogy a bejövő adatok nem az elosztási kulcs alapján vannak rendezve, mivel ez lelassítja a betöltéseket.  Az alábbi hivatkozásokban további részleteket talál arról, hogy a terjesztési oszlopok kiválasztása hogyan javíthatja a teljesítményt, valamint hogyan határozhat meg egy elosztott táblát a CREATE TABLES utasítás WITH záradékában.

Lásd [még: Tábla áttekintése][Table overview], [Táblaeloszlás][Table distribution], [Táblaeloszlás kiválasztása][Selecting table distribution], [TÁBLA LÉTREHOZÁSA][CREATE TABLE], [TÁBLA LÉTREHOZÁSA, AHOGY választó szöveg.][CREATE TABLE AS SELECT]

### <a name="do-not-over-partition"></a>Túl sok partíció használatának kerülése
Míg az adatok particionálása hatékony lehet az adatok partícióváltással vagy a vizsgálatok optimalizálásával a partíció megszüntetése révén történő karbantartásához, a túl sok partíció lelassulhat a lekérdezések.  Gyakran egy nagy részletességű particionálási stratégia, amely jól működik az SQL Server nem működik jól az SQL-készlet.  

A túl sok partíció a fürtözött oszlopcentrikus indexek hatékonyságát is csökkentheti, ha az egyes partíciók kevesebb mint 1 millió sorral rendelkeznek. Az SQL-készlet 60 adatbázisba particionálja az adatokat. Így ha 100 partíciót, az eredmény 6000 partíciókat.  Mindegyik számítási feladat különböző, így érdemes kísérletezni a particionálással, és kideríteni, hogy az adott számítási feladatnál melyik megoldás a célravezető.  

Az egyik lehetőség, hogy fontolja meg a részletesség, amely alacsonyabb, mint amit esetleg dolgozott az SQL Server.  A napi partíciók helyett például érdemes lehet heti vagy havi partíciókat használni.

Lásd [még: Táblaparticionálás][Table partitioning].

### <a name="minimize-transaction-sizes"></a>Tranzakcióméretek minimalizálása

Az INSERT, UPDATE és DELETE utasítások tranzakcióban futnak, és amikor meghiúsulnak, vissza kell őket állítani.  A hosszú visszaállítások előfordulásának csökkentése érdekében mindig minimalizálja a tranzakciók méretét, ahol erre lehetősége van.  Ezt úgy teheti meg, hogy részekre osztja az INSERT, UPDATE és DELETE utasításokat.  Ha például van egy INSERT, amely várhatóan 1 órát vesz igénybe, az INSERT négy részre bontható, így minden futtatást 15 percre rövidít.

Használja ki a minimális naplózást igénylő speciális eseteket, például a CTAS, TRUNCATE, DROP TABLE vagy INSERT utasításokat a táblák kiürítéséhez, hogy csökkentse a visszaállítással kapcsolatos kockázatokat.  A visszaállítások kiküszöbölésének másik módja a csak metaadatokat használó műveletek alkalmazása, például az adatkezelés partícióváltása.  Ahelyett például, hogy DELETE utasítással törölné egy tábla összes olyan sorát, ahol az order_date 2001. október, havonta particionálhatja az adatokat, majd az adatokkal rendelkező partíciót egy másik tábla üres partíciójára cserélheti (lásd az ALTER TABLE-re vonatkozó példákat).  

A nem particionált táblák esetén érdemes lehet CTAS utasítással kiírni a táblában megőrizni kívánt adatokat a DELETE használata helyett.  Noha egy CTAS utasítás ugyanannyi időt vesz igénybe, sokkal biztonságosabban futtatható művelet, mivel minimális tranzakciónaplózást igényel, és szükség esetén gyorsan megszakítható.

Lásd [még: A tranzakciók ismertetése][Understanding transactions], [a tranzakciók optimalizálása][Optimizing transactions], [a táblaparticionálása][Table partitioning], [a TÁBLA csonkítása][TRUNCATE TABLE], [a TÁBLA MÓDOSÍTÁSA][ALTER TABLE], a Tábla [létrehozása kijelölésként (CTAS)][Create table as select (CTAS)]című témakört.

### <a name="use-the-smallest-possible-column-size"></a>A lehető legkisebb oszlopméret használata

Ha a DDL meghatározásakor az adatokat támogató legkisebb adattípust használja, nagyban javíthatja a lekérdezés teljesítményét.  Ez különösen fontos a CHAR és VARCHAR oszlopok esetén.  Ha egy oszlop leghosszabb értéke 25 karakterből áll, akkor VARCHAR(25) típusként határozza meg az oszlopot.  Ne határozza meg az összes karakteroszlopot nagy alapértelmezett hosszúságértékkel.  NVARCHAR helyett VARCHAR típusként határozza meg az oszlopokat, amikor ez is elegendő.

Lásd még a [táblák áttekintésével][Table overview], a [tábla adattípusaival](develop-tables-data-types.md), valamint a [CREATE TABLE][CREATE TABLE] utasítással foglalkozó témaköröket.

### <a name="optimize-clustered-columnstore-tables"></a>Fürtözött oszlopcentrikus táblák optimalizálása

Fürtözött oszlopcentrikus indexek az egyik leghatékonyabb módja az adatok sql készletben tárolható.  Alapértelmezés szerint az SQL-készletben lévő táblák fürtözött ColumnStore néven jönnek létre.  Annak érdekében, hogy az oszlopcentrikus táblák a lehető legjobb teljesítménnyel fussanak, fontos a jó szegmensminőség.  Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  

A szegmensminőség a tömörített sorcsoportokban található sorok száma alapján mérhető fel.  A [táblaindexekkel][Table indexes] foglalkozó cikk [az oszlopcentrikus indexek gyenge minőségének okait][Causes of poor columnstore index quality] ismertető szakaszában részletes utasításokat talál, amelyek a fürtözött oszlopcentrikus táblák szegmensminőségének észlelésével és javításával foglalkoznak.  Mivel a kiváló minőségű oszlopcentrikus szegmensek fontosak, célszerű olyan felhasználói azonosítókat használni, amelyek a közepes vagy nagy erőforrásosztályban találhatók az adatok betöltéséhez. Az alacsonyabb [adattárház egységek](azure -synapse-resource-consumption-models.md) használata azt jelenti, hogy nagyobb erőforrásosztályt szeretne hozzárendelni a betöltő felhasználóhoz.

Mivel az oszlopcentrikus táblák általában nem adnak le adatokat egy tömörített oszlopcentrikus szegmensbe, amíg táblaként több mint 1 millió sor nem történik, és minden SQL-készlettábla 60 táblára van felosztva, az oszlopcentrikus táblák csak akkor részesülnek a lekérdezés számára, ha a tábla több mint 60 millió sort tartalmaz.  A 60 millió nál kevesebb sort betartó táblák esetében a columstore index nem feltétlenül az optimális megoldás.  

Ha particionálja az adatait, akkor azt is érdemes figyelembe venni, hogy minden partíciónak 1 millió sorral kell rendelkeznie a fürtözött oszlopcentrikus indexek előnyeinek kihasználása érdekében.  Ha egy tábla 100 partícióval rendelkezik, akkor legalább 6 milliárd sorral kell rendelkeznie ahhoz, hogy egy fürtözött oszlopok tárolója részesüljön (60 *disztribúció 100 partíció* 1 millió sor).  

Ha a tábla nem rendelkezik 6 milliárd sorral, vagy csökkentse a partíciók számát, vagy fontolja meg egy halommemória-tábla használata helyett.  Érdemes lehet kísérletezni, hogy jobb teljesítményt lehet-e szerezni egy halommemória-tábla másodlagos indexek helyett egy oszlopcentrikus tábla használatával.

Oszlopcentrikus tábla lekérdezésekor a lekérdezések gyorsabban futnak, ha csak a szükséges oszlopokat választja ki.  

Lásd [még: Táblaindexek][Table indexes], [Oszlopcentrikus indexek útmutató][Columnstore indexes guide], [Oszlopcentrikus indexek újraépítése][Rebuilding columnstore indexes].

### <a name="next-steps"></a>További lépések

Ha nem találta meg, amit keresett ebben a cikkben, próbálja meg a "Docs keresése" a lap bal oldalán az Azure SQL-készlet dokumentumainak kereséséhez.  Az [Azure SQL pool fórum][Azure SQL pool MSDN Forum] egy olyan hely, ahol kérdéseket tehet fel más felhasználóknak és az SQL pool termékcsoportnak.  

Aktívan figyeljük ezt a fórumot, és gondoskodunk róla, hogy tőlünk vagy egy másik felhasználótól választ kapjon a kérdéseire.  Ha inkább kérdéseket szeretne feltenni a Veremtúlcsordulással kapcsolatban, akkor van egy [Azure SQL pool stack overflow fórumunk][Azure SQL pool Stack Overflow Forum]is.

Az [Azure SQL pool visszajelzési lapján][Azure SQL pool Feedback] szolgáltatáskéréseket tehet le.  Ha kéréseket tesz közzé vagy más kérésekre szavaz, sokat segíthet a szolgáltatások fontossági sorrendjének megállapításában.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ../sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Create table as select (CTAS)]: ../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Table overview]:develop-tables-overview.md
[Table distribution]: ../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Table indexes]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Causes of poor columnstore index quality]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Temporary tables]:develop-tables-temporary.md
[Guide for using PolyBase]:../sql-analytics/data-loading-best-practices.md
[Load data]: ../sql-data-warehouse/esign-elt-data-loading.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Move data with Azure Data Factory]: ../../data-factory/transform-data-using-machine-learning.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Load data with Azure Data Factory]: ../../data-factory/load-azure-sql-data-warehouse.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ../sql-data-warehouse/load-data-wideworldimportersdw.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Monitor your workload using DMVs]: ../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Pause compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#pause-compute-bk?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Resume compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#resume-compute-bk?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Scale compute resources]: ../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scale-compute?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Understanding transactions]:develop-transactions.md
[Optimizing transactions]: ../sql-data-warehouse/sql-data-warehouse-develop-best-practices-transactions.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[Troubleshooting]: ../sql-data-warehouse/sql-data-warehouse-troubleshoot.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json
[LABEL]:develop-label.md

<!--MSDN references-->
[ALTER TABLE]: /sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE EXTERNAL FILE FORMAT]: /sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE STATISTICS]: /sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE TABLE]: /sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[CREATE TABLE AS SELECT]: /sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[DBCC PDW_SHOWEXECUTIONPLAN]: /sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[INSERT]: /sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[OPTION]: /sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[TRUNCATE TABLE]: /sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[UPDATE STATISTICS]: /sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_exec_sessions]: /sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_exec_requests]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_request_steps]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_sql_requests]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_dms_workers]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[sys.dm_pdw_waits]: /sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest
[Columnstore indexes guide]: /sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL pool Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL pool MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL pool Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL pool loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/

## <a name="development-best-practices-for-sql-on-demand-preview"></a>Az SQL igény szerinti fejlesztésével kapcsolatos gyakorlati tanácsok (előzetes verzió)

### <a name="general-considerations"></a>Általános megfontolások

SQL igény szerinti lehetővé teszi, hogy az Azure storage-fiókok ban fájlok lekérdezése. Nem rendelkezik helyi tárolási vagy betöltési képességekkel, ami azt jelenti, hogy a lekérdezési célokat tartalmazó összes fájl az SQL igény szerinti külső fájlja. Ezért a fájlok tárolásból való olvasásával kapcsolatos minden hatással lehet a lekérdezés teljesítményére.

### <a name="colocate-azure-storage-account-and-sql-on-demand"></a>Az Azure Storage-fiók és az SQL igény szerinti közös áthelyezése

 
A késés minimalizálása érdekében helyezze el az Azure-tárfiókot és az SQL igény szerinti végpontját. A munkaterület létrehozása során kiépített tárfiókok és végpontok ugyanabban a régióban találhatók. 
 
Az optimális teljesítmény érdekében, ha az SQL igény szerinti más tárfiókokhoz is hozzáfér, győződjön meg arról, hogy ugyanabban a régióban vannak. Ellenkező esetben az adatok hálózati átvitele a távoli régióból a végpont régiójába nagyobb lesz.

### <a name="azure-storage-throttling"></a>Az Azure Storage szabályozása

Több alkalmazás és szolgáltatás is hozzáférhet a tárfiókhoz. Ha az alkalmazások, szolgáltatások és az SQL igény szerinti munkaterhelés e számítási feladatok által generált kombinált IOPS vagy átviteli kapacitás meghaladja a tárfiók korlátait, a storage-szabályozás történik. Tárolási szabályozás esetén jelentős negatív hatással van a lekérdezés teljesítményére. 
 
A szabályozás észlelése után az SQL igény szerinti rendszer rendelkezik a forgatókönyv beépített kezelésével. SQL igény szerinti lesz, hogy a kérelmeket a tárolás lassabb ütemben, amíg a szabályozás feloldása. Azonban az optimális lekérdezés-végrehajtás, azt javasoljuk, hogy ne stresszelje a tárfiókot más számítási feladatok lekérdezés végrehajtása során.

### <a name="prepare-files-for-querying"></a>Fájlok előkészítése lekérdezésre

Ha lehetséges, előkészítheti a fájlokat a jobb teljesítmény érdekében:

- Konvertálja csv parketta - Parketta oszlopos formátumban. Mivel tömörített, kisebb fájlmérettel rendelkezik, mint az azonos adatokkal rendelkező CSV-fájlok, és az SQL igény szerinti olvasásához kevesebb időre és tárolási kérelemre lesz szüksége.
- Ha egy lekérdezés egyetlen nagy fájlt céloz meg, előnyös lesz, ha több kisebb fájlra osztja fel.
- Próbálja meg a CSV fájlméretét 10 GB alatt tartani.
- Azt szeretné, hogy azonos méretű fájlokat egy OPENROWSET elérési út vagy egy külső tábla LOCATION.
- Partisítsa az adatokat a partíciók különböző mappákba vagy fájlnevekbe való tárolásával - ellenőrizze [a fájlnév és a filepath függvények használatát adott partíciók célzásához.](#use-fileinfo-and-filepath-functions-to-target-specific-partitions)

### <a name="use-fileinfo-and-filepath-functions-to-target-specific-partitions"></a>Adott partíciók célzásához használjon fileinfo és filepath függvényeket

Az adatok gyakran partíciókba vannak rendezve. Utasíthatja az SQL-t, hogy bizonyos mappákat és fájlokat kérdezzen le. Ez csökkenti a lekérdezés olvasásához és feldolgozásához szükséges fájlok számát és adatmennyiségét. Következésképpen jobb teljesítményt fog elérni. További információért ellenőrizze [a fájlnév-](develop-storage-files-overview.md#filename-function) és [fájlelérési funkciókat,](develop-storage-files-overview.md#filepath-function) valamint példákat arra vonatkozóan, hogyan [lehet lekérdezni bizonyos fájlokat.](query-specific-files.md)

Ha a tárolóban lévő adatok nincsenek particionálva, fontolja meg a particionálást, hogy ezeket a függvényeket a fájlokat célzó lekérdezések optimalizálásához használhassa.

[Particionált Spark-táblák lekérdezésekor](develop-storage-files-spark-tables.md) az SQL igény szerint, a lekérdezés automatikusan csak a szükséges fájlokat célozza meg.

### <a name="use-cetas-to-enhance-query-performance-and-joins"></a>A CETAS használata a lekérdezési teljesítmény növeléséhez és az illesztéshez

A [CETAS](develop-tables-cetas.md) az SQL igény szerinti egyik legfontosabb szolgáltatása. A CETAS egy párhuzamos művelet, amely külső táblametaadatokat hoz létre, és a SELECT lekérdezés eredményét a tárfiókban lévő fájlok készletébe exportálja.

A CETAS segítségével a lekérdezések gyakran használt részeinek, például az illesztett referenciatábláknak egy új fájlkészletben tárolhatók. Később csatlakozhat ehhez az egyetlen külső táblához ahelyett, hogy több lekérdezésben megismételné a gyakori illesztéseket. Mivel a CETAS parattfájlokat hoz létre, a statisztika automatikusan létrejön, amikor az első lekérdezés ezt a külső táblát célozza meg, és jobb teljesítményt fog nyerni.
