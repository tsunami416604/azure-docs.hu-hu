---
title: Ajánlott eljárások a szinapszis SQL-készlethez az Azure szinapszis Analyticsben (korábban SQL DW)
description: Javaslatok és ajánlott eljárások az SQL-készlethez kapcsolódó megoldások fejlesztéséhez az Azure szinapszis Analyticsben (korábban SQL DW).
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 6978855e9b32a3842e76d02ef543d86cf0673019
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85206648"
---
# <a name="best-practices-for-synapse-sql-pool-in-azure-synapse-analytics-formerly-sql-dw"></a>Ajánlott eljárások a szinapszis SQL-készlethez az Azure szinapszis Analyticsben (korábban SQL DW)

Ez a cikk az ajánlott eljárások gyűjteménye, amelyekkel optimális teljesítményt érhet el az [SQL-készlet](sql-data-warehouse-overview-what-is.md) üzembe helyezése során.  A cikk célja, hogy alapszintű útmutatást nyújtson, és kiemelje a hangsúly fontos területeit.  

## <a name="reduce-cost-with-pause-and-scale"></a>Költségek csökkentése felfüggesztés és méretezés által

A költségek felfüggesztéssel és skálázással való csökkentéséről további információért tekintse meg a [Számítások kezelése](sql-data-warehouse-manage-compute-overview.md) című szakaszt.

## <a name="maintain-statistics"></a>Statisztikák karbantartása

Az SQL-készlet konfigurálható úgy, hogy automatikusan felderítse és létrehozza az oszlopok statisztikáit.  Az optimalizáló által létrehozott lekérdezési tervek csak a rendelkezésre álló statisztikáknak megfelelőek.  

Javasoljuk, hogy engedélyezze az adatbázisok AUTO_CREATE_STATISTICSét, és tartsa meg a statisztikákat naponta, vagy az egyes terhelések után, hogy a lekérdezésekben használt oszlopokra vonatkozó statisztikák mindig naprakészek legyenek.

Ha úgy találja, hogy túl sokáig tart az összes statisztika frissítése során, érdemes lehet több szelektíven kipróbálni, hogy mely oszlopokra van szükség a gyakori statisztikai frissítésekhez. Előfordulhat például, hogy csak a dátumoszlopokat szeretné frissíteni, amelyekbe napi rendszerességgel kerülnek új értékek.

> [!TIP]
> A legtöbb előnyt úgy kapja meg, hogy az összekapcsolásokban részt vevő oszlopokra vonatkozó frissített statisztikát, a WHERE záradékban használt oszlopokat és a GROUP BY utasításban található oszlopokat használja.

Lásd még: [táblák statisztikáinak kezelése](sql-data-warehouse-tables-statistics.md), [statisztikák létrehozása](/sql/t-sql/statements/create-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)és [frissítési statisztika](/sql/t-sql/statements/update-statistics-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>A lekérdezések megfigyelése és optimalizálása DMV-kkel

Az SQL-készlet több DMV is rendelkezik, amelyek a lekérdezés végrehajtásának figyelésére használhatók.  A számítási feladatok figyelése a DMV használatával részletes útmutatást nyújt a végrehajtó lekérdezés részleteinek megtekintéséhez.  

Ha gyorsan szeretne lekérdezéseket kikeresni a DMV-kben, segíthet, ha a lekérdezéseknél használja a LABEL beállítást.

Lásd még: DMV, [label](sql-data-warehouse-develop-label.md), [Option](/sql/t-sql/queries/option-clause-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_exec_sessions](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sessions-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [DBCC PDW_SHOWEXECUTIONPLAN](/sql/t-sql/database-console-commands/dbcc-pdw-showexecutionplan-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)és [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) [használatával a munkaterhelés figyelése](sql-data-warehouse-manage-monitor.md).

## <a name="tune-query-performance-with-new-product-enhancements"></a>A lekérdezési teljesítmény finomhangolása új termékek fejlesztésével

- [Teljesítmény-finomhangolás tényleges táblán alapuló nézetekkel](performance-tuning-materialized-views.md)
- [Teljesítmény-finomhangolás rendezett fürtözött oszlopcentrikus index használatával](performance-tuning-ordered-cci.md)
- [Teljesítmény-finomhangolás eredményhalmaz gyorsítótárazásával](performance-tuning-result-set-caching.md)

## <a name="group-insert-statements-into-batches"></a>INSERT utasítások csoportosítása kötegekbe

Egy kis tábla INSERT utasítással való egyszeri betöltése, vagy akár egy keresés rendszeres újratöltése is jól teljesítheti az igényeinek megfelelő utasítást, például: `INSERT INTO MyLookup VALUES (1, 'Type 1')` .  

Ha azonban egy nap során több ezer vagy több millió sort kell betöltenie, észreveheti, hogy az egyszeres INSERT utasítások nem feltétlenül bizonyulnak elegendőnek.  Ehelyett érdemes olyan folyamatokat kifejleszteni, amelyek egy fájlba írnak, amelyet egy időről időre aktiválódó másik folyamat rendszeresen betölt.

Lásd még: [Insert](/sql/t-sql/statements/insert-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Az adatok gyors betöltése és exportálása a PolyBase segítségével

Az SQL-készlet támogatja az adatok betöltését és exportálását számos eszközön, többek között a Azure Data Factory, a Base és a BCP használatával.  Kis mennyiségű adat kezelése esetén, ahol a teljesítmény nem kulcsfontosságú tényező, bármelyik eszköz megfelelhet az igényeinek.  Amikor nagy mennyiségű adatot tölt be vagy exportál, vagy gyors teljesítményre van szükség, a PolyBase a legjobb választás.  

A Base úgy lett kialakítva, hogy kihasználja a MPP (masszívan párhuzamos feldolgozási) architektúrát, és minden más eszköznél gyorsabban betöltse és exportálja az adatmennyiséget.  A PolyBase-betöltések a CTAS vagy az INSERT INTO paranccsal futtathatók.  

> [!TIP]
> A CTAS használata minimalizálja a tranzakciók naplózásának mértékét, és ez az adatok betöltésének leggyorsabb módja.

A Azure Data Factory támogatja a alapszintű terheléseket is, és a CTAS hasonló teljesítményt érhet el.  A PolyBase többféle fájlformátumot támogat, beleértve a Gzip-fájlokat is.  
  
> [!NOTE]
> A gzip szövegfájlok használatakor az átviteli sebesség maximalizálása érdekében bontsa a fájlokat 60 vagy több fájlba, hogy maximalizálja a terhelés párhuzamosságát.  A gyorsabb teljes átviteli teljesítmény érdekében érdemes lehet egy időben betölteni az adatokat.

Lásd még: [adatok betöltése](design-elt-data-loading.md), [útmutató a következőhöz: alapszintű](guidance-for-loading-data.md), [SQL Pool betöltési minták és stratégiák](https://blogs.msdn.microsoft.com/sqlcat/20../../), [adatok betöltése a Azure Data Factory]( ../../data-factory/load-azure-sql-data-warehouse.md), [adatok áthelyezése Azure Data Factory](../../data-factory/transform-data-using-machine-learning.md), [külső fájlformátum létrehozása](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), és [CREATE TABLE as Select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="load-then-query-external-tables"></a>Betöltés, majd külső táblák lekérdezése

Míg a PolyBase, más néven a külső táblák használata lehet az adatok betöltésének leggyorsabb módja, lekérdezésekhez nem ez a legjobb megoldás. A alapszintű táblák jelenleg csak az Azure Blob-fájlokat és Azure Data Lake tárolót támogatják. Ezekhez a fájlokhoz nem tartoznak külön számítási erőforrások.  

Ennek eredményeképpen az SQL-készlet nem tudja kiszervezni ezt a munkát, ezért az adatolvasáshoz be kell töltenie a teljes fájlt a tempdb-be való betöltésével.  Ezért ha több olyan lekérdezése is van, amelyek ennek a fájlnak az adatait kérik le, érdemesebb egyszer betölteni az adatokat, és úgy beállítani a lekérdezéseket, hogy a helyi táblát használják.

Lásd még: [útmutató a Base használatához](guidance-for-loading-data.md).

## <a name="hash-distribute-large-tables"></a>Nagy táblák kivonatos elosztása

Alapértelmezés szerint a táblák ciklikus időszeleteléssel vannak elosztva.  Ez megkönnyíti, hogy a felhasználók anélkül kezdhessenek neki a táblák létrehozásának, hogy dönteniük kéne az elosztás módjáról.  A ciklikus időszeletelésű táblák megfelelő megoldást jelenthetnek egye számítási feladatok esetén, a legtöbb esetben azonban egy elosztási oszlop kiválasztása megfelelőbb eredményre vezet.  

A leggyakoribb példája annak, amikor egy oszloppal elosztott tábla sokkal jobb teljesítményt nyújt egy ciklikus időszeletelésűnél, amikor két nagy tábla egyesül.  

Ha például van egy rendelésazonosító alapján elosztott rendelési táblája és egy, szintén a rendelésazonosító alapján elosztott tranzakciótáblája, akkor ha a rendelési táblát a tranzakciótáblával egyesíti a rendelésazonosító oszlopnál, a lekérdezés továbbított lekérdezéssé válik, így kiküszöbölhetők az adatátviteli műveletek.  Ha kevesebb lépést kell végrehajtani, felgyorsul a lekérdezési folyamat.  A kisebb mértékű adatmozgás is gyorsabb lekérdezéseket eredményez.  

> [!TIP]
> Elosztott tábla betöltésekor győződjön meg arról, hogy a bejövő adatok nem az elosztási kulcs alapján vannak rendezve, mivel ez lelassítja a betöltéseket.  

A terjesztési oszlopok kiválasztásával kapcsolatos további részletekért tekintse meg a következő hivatkozásokat, valamint azt, hogyan határozhat meg elosztott táblát a CREATE TABLE utasítás WITH záradékában.

Lásd még: [táblák áttekintése](sql-data-warehouse-tables-overview.md), [tábla eloszlása](sql-data-warehouse-tables-distribute.md), a [táblázatok terjesztésének kiválasztása](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) [CREATE TABLE a kiválasztás lehetőséggel](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="do-not-over-partition"></a>Túl sok partíció használatának kerülése

Míg az adatparticionálás hatékonyan kezelheti az adatait a partíciók váltásával vagy a vizsgálatok optimalizálásával a partíciók eltávolításával, a túl sok partíció lelassíthatja a lekérdezéseket.  Gyakran nagy részletességű particionálási stratégia, amely jól működik SQL Server előfordulhat, hogy nem működik megfelelően az SQL-készletben.  

A túl sok partíció a fürtözött oszlopcentrikus indexek hatékonyságát is csökkentheti, ha az egyes partíciók kevesebb mint 1 millió sorral rendelkeznek.  Ne feledje, hogy a színfalak mögött az SQL Pool a 60-adatbázisokba particionálja az adatait, így ha 100-partíciót tartalmazó táblát hoz létre, akkor ez a 6000-partíciók tényleges eredményét eredményezi.  

Mindegyik számítási feladat különböző, így érdemes kísérletezni a particionálással, és kideríteni, hogy az adott számítási feladatnál melyik megoldás a célravezető.  Érdemes kisebb szintű részletességet használni, mint az SQL Serveren.  A napi partíciók helyett például érdemes lehet heti vagy havi partíciókat használni.

Lásd még: [tábla particionálás](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Tranzakcióméretek minimalizálása

Az INSERT, UPDATE és DELETE utasítások tranzakcióban futnak, és amikor meghiúsulnak, vissza kell őket állítani.  A hosszú visszaállítások előfordulásának csökkentése érdekében mindig minimalizálja a tranzakciók méretét, ahol erre lehetősége van.  Ezt úgy teheti meg, hogy részekre osztja az INSERT, UPDATE és DELETE utasításokat.  

Ha például egy olyan BESZÚRÁSt szeretne, amely 1 órát vesz igénybe, akkor a BESZÚRÁSt négy részre kell bontania, amely 15 percen belül fut.  A visszaállítási kockázat csökkentése érdekében speciális minimális naplózási eseteket, például a CTAS, a CSONKOLT, a DROP TABLE vagy az INSERT (üres) táblázatokat használja.  

A visszaállítások kiküszöbölésének másik módja a csak metaadatokat használó műveletek alkalmazása, például az adatkezelés partícióváltása.  Például ahelyett, hogy a DELETE utasítás végrehajtása után törli egy olyan tábla összes sorát, amelyben a order_date 2001 volt, az adatokat havonta particionálhatja, majd kikapcsolhatja a partíciót egy üres partíció adataival egy másik táblából (lásd: [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) példák).  

A nem particionált táblák esetében érdemes lehet egy CTAS használni a táblákat megőrizni kívánt, a TÖRLÉSt nem használó adatbevitelre.  Ha egy CTAS ugyanannyi időt vesz igénybe, az sokkal biztonságosabb művelet, mert minimális tranzakciós naplózással rendelkezik, és szükség esetén gyorsan megszakítható.

Lásd még: [tranzakciók megismerése](sql-data-warehouse-develop-transactions.md), [tranzakciók optimalizálása](sql-data-warehouse-develop-best-practices-transactions.md), [táblák particionálása](sql-data-warehouse-tables-partition.md), [truncate Table](/sql/t-sql/statements/truncate-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [ALTER TABLE](/sql/t-sql/statements/alter-table-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)és [CREATE TABLE as Select (CTAS)](sql-data-warehouse-develop-ctas.md).

## <a name="reduce-query-result-sizes"></a>Lekérdezési eredmények méretének csökkentése

Ez a lépés segít elkerülni a nagyméretű lekérdezési eredmények által okozott ügyféloldali problémákat.  A lekérdezés szerkesztésével csökkentheti a visszaadott sorok számát. Egyes lekérdezés-létrehozási eszközök lehetővé teszik a "legfontosabb N" szintaxis hozzáadását az egyes lekérdezésekhez.  A lekérdezés eredményét egy ideiglenes táblára is CETAS, majd a viszonyítási alap exportálást használhatja az alacsonyabb szintű feldolgozáshoz.

## <a name="use-the-smallest-possible-column-size"></a>A lehető legkisebb oszlopméret használata

A DDL meghatározásakor az adatait támogató legkisebb adattípussal javíthatja a lekérdezési teljesítményt.  Ez a megközelítés különösen fontos a CHAR és a VARCHAR oszlopok esetében.  

Ha egy oszlop leghosszabb értéke 25 karakterből áll, akkor VARCHAR(25) típusként határozza meg az oszlopot.  Ne határozza meg az összes karakteroszlopot nagy alapértelmezett hosszúságértékkel.  NVARCHAR helyett VARCHAR típusként határozza meg az oszlopokat, amikor ez is elegendő.

Lásd még: [táblák áttekintése](sql-data-warehouse-tables-overview.md), [tábla adattípusok](sql-data-warehouse-tables-data-types.md), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="use-temporary-heap-tables-for-transient-data"></a>Ideiglenes halomtáblák használata átmeneti adatokhoz

Amikor átmenetileg kivezeti az adattárolást, előfordulhat, hogy egy halom tábla használatával gyorsabban fog megjelenni az általános folyamat.  Ha csak azért tölt be adatokat, hogy további átalakítások futtatása előtt előkészítse őket, sokkal gyorsabban töltheti a táblát egy halomtáblába, mint egy fürtözött oszlopcentrikus táblába.  

Azzal is tovább gyorsíthatja a folyamatot, ha az adatokat állandó tároló helyett ideiglenes táblába tölti.  Az ideiglenes táblák "#" karakterrel kezdődnek, és csak az azt létrehozó munkamenet számára érhetők el, így csak korlátozott helyzetekben működhetnek.

A halomtáblákat a CREATE TABLE utasítás WITH záradékával lehet meghatározni.  Ha ideiglenes táblát használ, ne felejtsen el rajta is statisztikákat létrehozni.

Lásd még: [ideiglenes táblák](sql-data-warehouse-tables-temporary.md), [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), [CREATE TABLE](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="optimize-clustered-columnstore-tables"></a>Fürtözött oszlopcentrikus táblák optimalizálása

A fürtözött oszlopcentrikus indexek az egyik leghatékonyabb módszer az SQL-készletben tárolt adattároláshoz.  Alapértelmezés szerint az SQL-készletben lévő táblák fürtözött Oszlopcentrikus jönnek létre.  Annak érdekében, hogy az oszlopcentrikus táblák a lehető legjobb teljesítménnyel fussanak, fontos a jó szegmensminőség.  

Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  A szegmensminőség a tömörített sorcsoportokban található sorok száma alapján mérhető fel.  A fürtözött oszlopcentrikus-táblák szegmensének minőségének észlelésével és javításával kapcsolatos részletes utasításokért tekintse meg a [gyenge oszlopcentrikus indexének okait](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) a [táblázat indexek](sql-data-warehouse-tables-index.md) című cikkben.  

Mivel a kiváló minőségű oszlopcentrikus szegmensek fontosak, hasznos lehet a közepes vagy nagy erőforrás-osztályba tartozó felhasználói azonosítók használata az betöltéshez. Az alacsonyabb [adattárház-egységek](what-is-a-data-warehouse-unit-dwu-cdwu.md) használata azt jelenti, hogy nagyobb erőforrás-osztályt szeretne hozzárendelni a betöltési felhasználóhoz.

Mivel a oszlopcentrikus-táblák általában nem küldenek le az adatlemezeket egy tömörített oszlopcentrikus, amíg a táblázat több mint 1 000 000 sort tartalmaz, és minden SQL Pool-tábla 60 táblázatba van particionálva, a oszlopcentrikus táblái nem kapnak lekérdezést, kivéve, ha a tábla több mint 60 000 000 sorral rendelkezik.  Előfordulhat, hogy a 60 milliónál kevesebb sorral rendelkező táblák esetén egyáltalán nem érdemes oszlopcentrikus indexet használni.  A használatuk azonban hátrányt sem jelent.  

Ha particionálja az adatait, akkor azt is érdemes figyelembe venni, hogy minden partíciónak 1 millió sorral kell rendelkeznie a fürtözött oszlopcentrikus indexek előnyeinek kihasználása érdekében.  Ha egy tábla 100 partíciót tartalmaz, akkor legalább 6 000 000 000 sort kell használnia a fürtözött oszlopok tárolójának kihasználása érdekében (60 distributers *100 partitions* 1 000 000 sor).  

Ha a tábla nem rendelkezik 6 milliárd sorral, akkor csökkentse a partíciók számát, vagy fontolja meg egy halomtábla használatát.  Azzal is érdemes lehet kísérletezni, hogy jobb teljesítmény érhető-e el egy másodlagos indexekkel rendelkező halomtáblával, mint egy oszlopcentrikussal.

> [!TIP]
> Oszlopcentrikus tábla lekérdezésekor a lekérdezések gyorsabban futnak, ha csak a szükséges oszlopokat választja ki.  

Lásd még a [táblaindexekkel](sql-data-warehouse-tables-index.md), az [oszlopcentrikus indexek áttekintésével](/sql/relational-databases/indexes/columnstore-indexes-overview?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) és az [oszlopcentrikus indexek újjáépítésével](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality) foglalkozó témaköröket.

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Nagyobb erőforrásosztály használata a lekérdezés teljesítményének javítása érdekében

Az SQL-készlet erőforráscsoportok használatával foglal le memóriát a lekérdezésekhez.  Az összes felhasználó hozzá van rendelve a kisméretű erőforrás osztályhoz, amely elosztás esetén 100 MB memóriát biztosít.  Mivel mindig 60 elosztás van és mindegyik elosztás minimum 100 MB-ot kap, a rendszeren belül összesen 6000 MB, vagyis majdnem 6 GB memória van lefoglalva.  

Bizonyos lekérdezéseknél, például a nagyobb egyesítéseknél vagy a fürtözött oszlopcentrikus táblákba végzett betöltésnél előnyt jelent a nagyobb memórialefoglalások használata.  Egyes lekérdezések, például a tiszta vizsgálatok, nem kapnak kedvezményt.  A nagyobb erőforrás-osztályok használata azonban csökkenti a párhuzamosságot, ezért figyelembe kell vennie ezt a hatást, mielőtt az összes felhasználót egy nagy erőforrás-osztályba helyezné.

Lásd még: [erőforrás-osztályok a számítási feladatok kezeléséhez](resource-classes-for-workload-management.md).

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Kisebb erőforrásosztály használata az egyidejűség mértékének növelése érdekében

Ha azt tapasztalja, hogy a felhasználói lekérdezések hosszú késleltetést mutatnak, előfordulhat, hogy a felhasználók nagyobb erőforrás-osztályokon futnak, és sok egyidejűségi tárolóhelyet használnak, ami más lekérdezéseket eredményez.  Ha a `SELECT * FROM sys.dm_pdw_waits` parancs futtatásakor a rendszer sorokat ad vissza, láthatja, hogy a felhasználók lekérdezései sorban állnak.

Lásd még: [erőforrás-osztályok a számítási feladatok kezeléséhez](resource-classes-for-workload-management.md), [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="other-resources"></a>Egyéb erőforrások

Lásd még az általános problémákat és megoldásokat tartalmazó, [hibaelhárítással](sql-data-warehouse-troubleshoot.md) foglalkozó témakört.

Ha nem találta meg, amit ebben a cikkben keres, próbálkozzon az oldal bal oldalán található "dokumentumok keresése" kifejezéssel az összes Azure szinapszis-dokumentum kereséséhez.  A [Microsoft Q&az Azure szinapszis-hoz kapcsolódó kérdés oldalának](https://docs.microsoft.com/answers/topics/azure-synapse-analytics.html) célja, hogy kérdéseket tegyenek fel más felhasználók és az Azure szinapszis-termékcsoportra. Aktívan figyeljük ezt a fórumot, és gondoskodunk róla, hogy tőlünk vagy egy másik felhasználótól választ kapjon a kérdéseire.  

Ha szeretne kérdéseket feltenni a Stack Overflowre, akkor egy [Azure szinapszis stack overflow fórum](https://stackoverflow.com/questions/tagged/azure-sqldw)is rendelkezésre áll.

A funkciókra vonatkozó kérések létrehozásához használja az [Azure szinapszis visszajelzési](https://feedback.azure.com/forums/307516-sql-data-warehouse) oldalát.  Ha kéréseket tesz közzé vagy más kérésekre szavaz, sokat segíthet a szolgáltatások fontossági sorrendjének megállapításában.
