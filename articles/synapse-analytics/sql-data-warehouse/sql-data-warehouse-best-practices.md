---
title: Az SQL Analytics gyakorlati tanácsok az Azure Synapse Analytics (korábbi években SQL DW) szolgáltatásában
description: Javaslatok és gyakorlati tanácsok az SQL Analytics megoldásainak fejlesztéséhez az Azure Synapse Analytics (korábbi SQL DW) szolgáltatásban.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 44dbc03a41cfde94c344ae331b21d7536778050c
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619106"
---
# <a name="best-practices-for-sql-analytics-in-azure-synapse-analytics-formerly-sql-dw"></a>Az SQL Analytics gyakorlati tanácsok az Azure Synapse Analytics (korábbi években SQL DW) szolgáltatásában

Ez a cikk az [SQL Analytics-telepítés](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse) ből származó optimális teljesítmény eléréséhez ajánlott eljárások gyűjteménye.  A cikk célja, hogy néhány alapvető útmutatást nyújtson, és kiemelje a fontos fókuszterületeket.  Minden szakasz bemutatja, hogy egy koncepció, majd rámutat, hogy részletesebb cikkeket, amelyek kiterjednek a koncepció mélyebben. A témák sorrendje fontos. 

## <a name="reduce-cost-with-pause-and-scale"></a>Költségek csökkentése felfüggesztés és méretezés által

A költségek felfüggesztéssel és skálázással való csökkentéséről további információért tekintse meg a [Számítások kezelése](sql-data-warehouse-manage-compute-overview.md) című szakaszt. 

## <a name="maintain-statistics"></a>Statisztikák karbantartása
Az Azure SQL Data Warehouse beállítható úgy, hogy automatikusan észlelje és létrehozza az oszlopok statisztikáit.  Az optimalizáló által létrehozott lekérdezési tervek csak olyan jók, mint a rendelkezésre álló statisztikák.  Azt javasoljuk, hogy engedélyezze a AUTO_CREATE_STATISTICS az adatbázisokhoz, és tartsa a statisztikákat naponta vagy minden egyes betöltés után, hogy a lekérdezésekben használt oszlopok statisztikái mindig naprakészek legyenek. 

Ha úgy találja, hogy túl sokáig tart az összes statisztika frissítése, érdemes megpróbálni szelektívebbé tenni, hogy mely oszlopoknak van szükségük gyakori statisztikai frissítésekre. Előfordulhat például, hogy csak a dátumoszlopokat szeretné frissíteni, amelyekbe napi rendszerességgel kerülnek új értékek. **A legnagyobb hasznot úgy nyerheti meg, ha frissített statisztikákat tart az illesztésekben részt vevő oszlopokról, a WHERE záradékban használt oszlopokról és a GROUP BY-ben található oszlopokról.**

Lásd még a [táblastatisztikák kezelésével][Manage table statistics], a [CREATE STATISTICS][CREATE STATISTICS] és az [UPDATE STATISTICS][UPDATE STATISTICS] utasítással foglalkozó témaköröket.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>A lekérdezések megfigyelése és optimalizálása DMV-kkel
Az SQL Analytics számos DMV-vel rendelkezik, amelyek a lekérdezések végrehajtásának figyelésére használhatók.  A megfigyeléssel foglalkozó alábbi témakör részletes útmutatót szolgáltat ahhoz, hogyan nézheti meg egy futtatott lekérdezés részleteit.  Ha gyorsan szeretne lekérdezéseket kikeresni a DMV-kben, segíthet, ha a lekérdezéseknél használja a LABEL beállítást.

Lásd még a [számítási feladatok DMV-vel végzett megfigyelésével][Monitor your workload using DMVs], valamint a [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] és [sys.dm_pdw_waits][sys.dm_pdw_waits] elemekkel foglalkozó témaköröket.

## <a name="tune-query-performance-with-new-product-enhancements"></a>A lekérdezési teljesítmény finomhangolása új termékfejlesztésekkel
- [Teljesítmény-finomhangolás tényleges táblán alapuló nézetekkel](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [Teljesítmény-finomhangolás rendezett fürtözött oszlopcentrikus index használatával](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [Teljesítmény-finomhangolás eredményhalmaz gyorsítótárazásával](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="group-insert-statements-into-batches"></a>INSERT utasítások csoportosítása kötegekbe
A kis táblák INSERT utasítással végzett egyszeri feltöltése vagy akár egy keresés rendszeres újratöltése is kielégítheti az igényeit, ha egy, a következőhöz hasonló utasítást használ: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Ha azonban egy nap során több ezer vagy több millió sort kell betöltenie, észreveheti, hogy az egyszeres INSERT utasítások nem feltétlenül bizonyulnak elegendőnek.  Ehelyett érdemes olyan folyamatokat kifejleszteni, amelyek egy fájlba írnak, amelyet egy időről időre aktiválódó másik folyamat rendszeresen betölt.

Lásd még: [INSERT][INSERT].

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Az adatok gyors betöltése és exportálása a PolyBase segítségével

 Az SQL Analytics számos eszköz , például az Azure Data Factory, a PolyBase és a BCP használatával támogatja az adatok betöltését és exportálását.  Kis mennyiségű adat kezelése esetén, ahol a teljesítmény nem kulcsfontosságú tényező, bármelyik eszköz megfelelhet az igényeinek.  Amikor nagy mennyiségű adatot tölt be vagy exportál, vagy gyors teljesítményre van szükség, a PolyBase a legjobb választás.  
 
 PolyBase célja, hogy kihasználja az MPP (Masszívan párhuzamos feldolgozás) architektúra és betölteni és exportálni adatok magnitúdó gyorsabb, mint bármely más eszköz.  A PolyBase-betöltések a CTAS vagy az INSERT INTO paranccsal futtathatók.  **A CTAS használata minimalizálja a tranzakciók naplózásának mértékét, és ez az adatok betöltésének leggyorsabb módja.** 
 
  Az Azure Data Factory támogatja a PolyBase terheléseket is, és hasonló teljesítményt érhet el, mint a CTAS.  A PolyBase többféle fájlformátumot támogat, beleértve a Gzip-fájlokat is.  **A gzip szöveges fájlok használata kori átviteli terhelés maximalizálásához bontsa fel a fájlokat 60 vagy több fájlra, hogy maximalizálja a terhelés párhuzamosságát.**  A gyorsabb teljes átviteli teljesítmény érdekében érdemes lehet egy időben betölteni az adatokat.

Lásd még: [Adatok betöltése][Load data], [Útmutató a PolyBase használatához][Guide for using PolyBase], [SQL készletbetöltési minták és stratégiák][Azure SQL Data Warehouse loading patterns and strategies], Adatok [betöltése az Azure Data Factory segítségével,][Load Data with Azure Data Factory] [Adatok áthelyezése az Azure Data Factory segítségével,][Move data with Azure Data Factory] [HOZZon létre külső fájlformátumot,][CREATE EXTERNAL FILE FORMAT] [hozzon létre táblázatot kiválasztottként (CTAS)][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Betöltés, majd külső táblák lekérdezése
Míg a PolyBase, más néven a külső táblák használata lehet az adatok betöltésének leggyorsabb módja, lekérdezésekhez nem ez a legjobb megoldás. A Polybase-táblák jelenleg csak az Azure blobfájlokat és az Azure Data Lake-tárhelyet támogatják. Ezekhez a fájlokhoz nem tartoznak külön számítási erőforrások.  

Ennek eredményeképpen az SQL Analytics nem tudja kiszervezni ezt a munkát, ezért az adatok olvasásához a teljes fájlt be kell töltenie a tempdb-be.  Ezért ha több olyan lekérdezése is van, amelyek ennek a fájlnak az adatait kérik le, érdemesebb egyszer betölteni az adatokat, és úgy beállítani a lekérdezéseket, hogy a helyi táblát használják.

Lásd még a [PolyBase használatára vonatkozó útmutatót][Guide for using PolyBase].

## <a name="hash-distribute-large-tables"></a>Nagy táblák kivonatos elosztása
Alapértelmezés szerint a táblák ciklikus időszeleteléssel vannak elosztva.  Ez megkönnyíti, hogy a felhasználók anélkül kezdhessenek neki a táblák létrehozásának, hogy dönteniük kéne az elosztás módjáról.  A ciklikus időszeletelésű táblák megfelelő megoldást jelenthetnek egye számítási feladatok esetén, a legtöbb esetben azonban egy elosztási oszlop kiválasztása megfelelőbb eredményre vezet.  A leggyakoribb példája annak, amikor egy oszloppal elosztott tábla sokkal jobb teljesítményt nyújt egy ciklikus időszeletelésűnél, amikor két nagy tábla egyesül.  

Ha például van egy rendelésazonosító alapján elosztott rendelési táblája és egy, szintén a rendelésazonosító alapján elosztott tranzakciótáblája, akkor ha a rendelési táblát a tranzakciótáblával egyesíti a rendelésazonosító oszlopnál, a lekérdezés továbbított lekérdezéssé válik, így kiküszöbölhetők az adatátviteli műveletek.  Ha kevesebb lépést kell végrehajtani, felgyorsul a lekérdezési folyamat.  A kisebb mértékű adatmozgás is gyorsabb lekérdezéseket eredményez.  

Elosztott tábla betöltésekor győződjön meg arról, hogy a bejövő adatok nem az elosztási kulcs alapján vannak rendezve, mivel ez lelassítja a betöltéseket.  Az alábbi hivatkozásokon további részleteket talál arról, hogy a terjesztési oszlop kiválasztása hogyan javíthatja a teljesítményt, valamint hogyan határozhat meg egy elosztott táblát a CREATE TABLE utasítás WITH záradékában.

Lásd még a [táblák áttekintésével][Table overview], [a táblaelosztással][Table distribution], [a táblaelosztás kiválasztásával][Selecting table distribution], a [CREATE TABLE][CREATE TABLE] és a [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT] utasítással foglalkozó témaköröket.

## <a name="do-not-over-partition"></a>Túl sok partíció használatának kerülése
Míg az adatok particionálása hatékony lehet az adatok partícióváltással vagy a vizsgálatok optimalizálásával a partíció megszüntetése révén történő karbantartásához, a túl sok partíció lelassulhat a lekérdezések.  Gyakran egy nagy részletességű particionálási stratégia, amely jól működhet az SQL Server nem működik jól az SQL Analytics.  

A túl sok partíció a fürtözött oszlopcentrikus indexek hatékonyságát is csökkentheti, ha az egyes partíciók kevesebb mint 1 millió sorral rendelkeznek.  Ne feledje, hogy a színfalak mögött az SQL Analytics 60 adatbázisba particionálja az adatokat, így ha 100 partíciót tartalmazó táblát hoz létre, az valójában 6000 partíciót eredményez.  

Mindegyik számítási feladat különböző, így érdemes kísérletezni a particionálással, és kideríteni, hogy az adott számítási feladatnál melyik megoldás a célravezető.  Érdemes kisebb szintű részletességet használni, mint az SQL Serveren.  A napi partíciók helyett például érdemes lehet heti vagy havi partíciókat használni.

Lásd még a [tábla particionálásával][Table partitioning] foglalkozó témakört.

## <a name="minimize-transaction-sizes"></a>Tranzakcióméretek minimalizálása
Az INSERT, UPDATE és DELETE utasítások tranzakcióban futnak, és amikor meghiúsulnak, vissza kell őket állítani.  A hosszú visszaállítások előfordulásának csökkentése érdekében mindig minimalizálja a tranzakciók méretét, ahol erre lehetősége van.  Ezt úgy teheti meg, hogy részekre osztja az INSERT, UPDATE és DELETE utasításokat.  

Ha például van egy INSERT, amely várhatóan 1 órát vesz igénybe, ha lehetséges, bontsa fel az INSERT-et négy részre, amelyek mindegyike 15 perc alatt fog futni.  Használja ki a minimális naplózást igénylő speciális eseteket, például a CTAS, TRUNCATE, DROP TABLE vagy INSERT utasításokat a táblák kiürítéséhez, hogy csökkentse a visszaállítással kapcsolatos kockázatokat.  

A visszaállítások kiküszöbölésének másik módja a csak metaadatokat használó műveletek alkalmazása, például az adatkezelés partícióváltása.  Ahelyett például, hogy DELETE utasítással törölné egy tábla összes olyan sorát, ahol az order_date 2001. október, havonta particionálhatja az adatokat, majd az adatokkal rendelkező partíciót egy másik tábla üres partíciójára cserélheti (lásd az ALTER TABLE-re vonatkozó példákat).  

Particionálatlan táblák esetén a DELETE helyett ctas-t érdemes használni a táblában tartani kívánt adatok írásához.  Ha a CTAS ugyanannyi időt vesz igénybe, sokkal biztonságosabb, mivel minimális tranzakciónaplózással rendelkezik, és szükség esetén gyorsan visszavonható.

Lásd még a [tranzakciók megismerésével][Understanding transactions], a [tranzakciók optimalizálásával][Optimizing transactions], a [tábla particionálásával][Table partitioning], valamint a [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE] és [Create table as select (CTAS)][Create table as select (CTAS)] utasításokkal foglalkozó témaköröket.

## <a name="reduce-query-result-sizes"></a>A lekérdezés eredményméretének csökkentése  
Ez a lépés segít elkerülni a nagy lekérdezési eredmény által okozott ügyféloldali problémákat.  A lekérdezés szerkesztésével csökkentheti a visszaadott sorok számát. Egyes lekérdezésgeneráló eszközök lehetővé teszik, hogy "top N" szintaxist adjon az egyes lekérdezésekhez.  A CETAS a lekérdezés eredményét egy ideiglenes táblába is beviheti, majd használhatja a PolyBase exportot az alsószintű feldolgozáshoz.

## <a name="use-the-smallest-possible-column-size"></a>A lehető legkisebb oszlopméret használata
A DDL meghatározásakor az adatokat támogató legkisebb adattípus használata javítja a lekérdezés teljesítményét.  Ez különösen fontos a CHAR és VARCHAR oszlopok esetén.  Ha egy oszlop leghosszabb értéke 25 karakterből áll, akkor VARCHAR(25) típusként határozza meg az oszlopot.  Ne határozza meg az összes karakteroszlopot nagy alapértelmezett hosszúságértékkel.  NVARCHAR helyett VARCHAR típusként határozza meg az oszlopokat, amikor ez is elegendő.

Lásd még a [táblák áttekintésével][Table overview], a [tábla adattípusaival][Table data types], valamint a [CREATE TABLE][CREATE TABLE] utasítással foglalkozó témaköröket.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Ideiglenes halomtáblák használata átmeneti adatokhoz
Amikor ideiglenesen adatokat ér el, előfordulhat, hogy egy halommemória-tábla használatával gyorsabbá válik a teljes folyamat.  Ha csak azért tölt be adatokat, hogy további átalakítások futtatása előtt előkészítse őket, sokkal gyorsabban töltheti a táblát egy halomtáblába, mint egy fürtözött oszlopcentrikus táblába.  

Azzal is tovább gyorsíthatja a folyamatot, ha az adatokat állandó tároló helyett ideiglenes táblába tölti.  Az ideiglenes táblák "#" -val kezdődnek, és csak az azt létrehozó munkamenet számára érhetők el, így előfordulhat, hogy csak korlátozott esetekben működnek.   A halomtáblákat a CREATE TABLE utasítás WITH záradékával lehet meghatározni.  Ha ideiglenes táblát használ, ne felejtsen el rajta is statisztikákat létrehozni.

Lásd még az [ideiglenes táblákkal][Temporary tables], illetve a [CREATE TABLE][CREATE TABLE] és [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT] utasításokkal foglalkozó témaköröket.

## <a name="optimize-clustered-columnstore-tables"></a>Fürtözött oszlopcentrikus táblák optimalizálása
A fürtözött oszlopcentrikus indexek az egyik leghatékonyabb módja az adatok SQL Analytics-ben való tárolásának.  Alapértelmezés szerint az SQL Analytics táblái fürtözött ColumnStore-ként jönnek létre.  Annak érdekében, hogy az oszlopcentrikus táblák a lehető legjobb teljesítménnyel fussanak, fontos a jó szegmensminőség.  

Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  A szegmensminőség a tömörített sorcsoportokban található sorok száma alapján mérhető fel.  A [táblaindexekkel][Table indexes] foglalkozó cikk [az oszlopcentrikus indexek gyenge minőségének okait][Causes of poor columnstore index quality] ismertető szakaszában részletes utasításokat talál, amelyek a fürtözött oszlopcentrikus táblák szegmensminőségének észlelésével és javításával foglalkoznak.  

Mivel a kiváló minőségű oszlopcentrikus szegmensek fontosak, célszerű olyan felhasználói azonosítókat használni, amelyek a közepes vagy nagy erőforrásosztályban találhatók az adatok betöltéséhez. Az alacsonyabb [adattárház egységek](what-is-a-data-warehouse-unit-dwu-cdwu.md) használata azt jelenti, hogy nagyobb erőforrásosztályt szeretne hozzárendelni a betöltő felhasználóhoz.

Mivel az oszlopcentrikus táblák általában nem adnak le adatokat egy tömörített oszlopcentrikus szegmensbe, amíg táblaként több mint 1 millió sor van, és minden SQL Analytics-tábla 60 táblára van particionálva, ökölszabály ként az oszlopcentrikus táblák nem szolgálnak egy lekérdezésszámára, kivéve, ha a tábla több mint 60 millió sorral rendelkezik.  Előfordulhat, hogy a 60 milliónál kevesebb sorral rendelkező táblák esetén egyáltalán nem érdemes oszlopcentrikus indexet használni.  A használatuk azonban hátrányt sem jelent.  

Ha particionálja az adatait, akkor azt is érdemes figyelembe venni, hogy minden partíciónak 1 millió sorral kell rendelkeznie a fürtözött oszlopcentrikus indexek előnyeinek kihasználása érdekében.  Ha egy tábla 100 partícióval rendelkezik, akkor legalább 6 milliárd sorral kell rendelkeznie a fürtözött oszloptárolók előnyeinek kihasználása érdekében (60 elosztás × 100 partíció × 1 millió sor).  

Ha a tábla nem rendelkezik 6 milliárd sorral, akkor csökkentse a partíciók számát, vagy fontolja meg egy halomtábla használatát.  Azzal is érdemes lehet kísérletezni, hogy jobb teljesítmény érhető-e el egy másodlagos indexekkel rendelkező halomtáblával, mint egy oszlopcentrikussal.

Oszlopcentrikus tábla lekérdezésekor a lekérdezések gyorsabban futnak, ha csak a szükséges oszlopokat választja ki.  

Lásd még a [táblaindexekkel][Table indexes], az [oszlopcentrikus indexek áttekintésével][Columnstore indexes guide] és az [oszlopcentrikus indexek újjáépítésével][Rebuilding columnstore indexes] foglalkozó témaköröket.

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Nagyobb erőforrásosztály használata a lekérdezés teljesítményének javítása érdekében
Az SQL Analytics erőforráscsoportokat használ a lekérdezések memóriájának lefoglalására.  A dobozon kívül minden felhasználó a kis erőforrásosztályhoz van rendelve, amely disztribúciónként 100 MB memóriát biztosít.  Mivel mindig 60 elosztás van és mindegyik elosztás minimum 100 MB-ot kap, a rendszeren belül összesen 6000 MB, vagyis majdnem 6 GB memória van lefoglalva.  

Bizonyos lekérdezéseknél, például a nagyobb egyesítéseknél vagy a fürtözött oszlopcentrikus táblákba végzett betöltésnél előnyt jelent a nagyobb memórialefoglalások használata.  Néhány lekérdezésnél, például amelyek csak vizsgálati műveleteket végeznek, ezeknek a használata nem jár további előnnyel.  A másik oldalon a nagyobb erőforrásosztályok kihasználása csökkenti az egyidejűséget, ezért ezt a hatást figyelembe kell venni, mielőtt az összes felhasználót egy nagy erőforrásosztályba helyezne át.

Lásd még: [Erőforrásosztályok számítási feladatok kezeléséhez](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Kisebb erőforrásosztály használata az egyidejűség mértékének növelése érdekében
Ha azt szeretné, hogy a felhasználói lekérdezések hosszú késleltetéssel rendelkeznek, előfordulhat, hogy a felhasználók nagyobb erőforrásosztályokban futnak, és sok egyidejűségi tárolóhelyet használnak fel, ami más lekérdezések várólistára kerülését okozza.  Ha a `SELECT * FROM sys.dm_pdw_waits` parancs futtatásakor a rendszer sorokat ad vissza, láthatja, hogy a felhasználók lekérdezései sorban állnak.

Lásd még: [Erőforrásosztályok számítási feladatok kezeléséhez](resource-classes-for-workload-management.md), [sys.dm_pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Egyéb erőforrások
Lásd még az általános problémákat és megoldásokat tartalmazó, [hibaelhárítással][Troubleshooting] foglalkozó témakört.

Ha nem találta, amit keres ebben a cikkben, próbálja meg a "Docs keresése" a bal oldalon ezen az oldalon az Összes Azure Synapse dokumentumok kereséséhez.  Az [Azure Synapse fórum][Azure SQL Data Warehouse MSDN Forum] egy olyan hely, ahol kérdéseket tehet fel más felhasználóknak és az Azure Synapse termékcsoportnak. 

Aktívan figyeljük ezt a fórumot, és gondoskodunk róla, hogy tőlünk vagy egy másik felhasználótól választ kapjon a kérdéseire.  Ha inkább kérdéseket tesz fel a veremtúlcsordulás, mi is van egy [Azure Synapse Stack túlcsordulás fórum.][Azure SQL Data Warehouse Stack Overflow Forum]

Végül használja az [Azure Synapse visszajelzési][Azure SQL Data Warehouse Feedback] oldalt a szolgáltatáskérések.  Ha kéréseket tesz közzé vagy más kérésekre szavaz, sokat segíthet a szolgáltatások fontossági sorrendjének megállapításában.

<!--Image references-->

<!--Article references-->
[Create a support ticket]:sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]:sql-data-warehouse-develop-ctas.md
[Table overview]:sql-data-warehouse-tables-overview.md
[Table data types]:sql-data-warehouse-tables-data-types.md
[Table distribution]:sql-data-warehouse-tables-distribute.md
[Table indexes]:sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]:sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]:sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]:sql-data-warehouse-tables-partition.md
[Manage table statistics]:sql-data-warehouse-tables-statistics.md
[Temporary tables]:sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]:guidance-for-loading-data.md
[Load data]:design-elt-data-loading.md
[Move data with Azure Data Factory]: ../../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]:load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]:sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ../../sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]:sql-data-warehouse-develop-transactions.md
[Optimizing transactions]:sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]:sql-data-warehouse-troubleshoot.md
[LABEL]:sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE]: https://msdn.microsoft.com/library/ms190273.aspx
[CREATE EXTERNAL FILE FORMAT]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE STATISTICS]: https://msdn.microsoft.com/library/ms188038.aspx
[CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE TABLE AS SELECT]: https://msdn.microsoft.com/library/mt204041.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[INSERT]: https://msdn.microsoft.com/library/ms174335.aspx
[OPTION]: https://msdn.microsoft.com/library/ms190322.aspx
[TRUNCATE TABLE]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS]: https://msdn.microsoft.com/library/ms187348.aspx
[sys.dm_exec_sessions]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_waits]: https://msdn.microsoft.com/library/mt203893.aspx
[Columnstore indexes guide]: https://msdn.microsoft.com/library/gg492088.aspx

<!--Other Web references-->
[Selecting table distribution]: https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Azure SQL Data Warehouse Feedback]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Azure SQL Data Warehouse MSDN Forum]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Azure SQL Data Warehouse Stack Overflow Forum]:  https://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: https://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
