---
title: Ajánlott eljárások az SQL Analytics szolgáltatáshoz az Azure szinapszis Analyticsben (korábban SQL DW)
description: Javaslatok és ajánlott eljárások az SQL Analytics-megoldások fejlesztéséhez az Azure szinapszis Analyticsben (korábban SQL DW).
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.openlocfilehash: 6a9d7c4ed020f447e9512c7bc2edd0caecf3400d
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646175"
---
# <a name="best-practices-for-sql-analytics-in-azure-synapse-analytics-formerly-sql-dw"></a>Ajánlott eljárások az SQL Analytics szolgáltatáshoz az Azure szinapszis Analyticsben (korábban SQL DW)

Ez a cikk az ajánlott eljárások gyűjteménye, amelyekkel optimális teljesítményt érhet el az [SQL Analytics](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse) -telepítésből.  A cikk célja, hogy alapszintű útmutatást nyújtson, és kiemelje a hangsúly fontos területeit.  Az egyes szakaszok egy koncepciót vezetnek be, majd a részletesebb cikkek részletes cikkeire mutatnak. A témakörök sorrendje fontossági sorrendben történik. 

## <a name="reduce-cost-with-pause-and-scale"></a>Költségek csökkentése felfüggesztés és méretezés által

A költségek felfüggesztéssel és skálázással való csökkentéséről további információért tekintse meg a [Számítások kezelése](sql-data-warehouse-manage-compute-overview.md) című szakaszt. 

## <a name="maintain-statistics"></a>Statisztikák karbantartása
A Azure SQL Data Warehouse konfigurálható az oszlopok statisztikáinak automatikus észleléséhez és létrehozásához.  Az optimalizáló által létrehozott lekérdezési tervek csak a rendelkezésre álló statisztikáknak megfelelőek.  Javasoljuk, hogy engedélyezze a AUTO_CREATE_STATISTICS az adatbázisokhoz, és naponta frissítse a statisztikát, vagy az egyes terhelések után ellenőrizze, hogy a lekérdezésekben használt oszlopokra vonatkozó statisztikák mindig naprakészek-e. 

Ha úgy találja, hogy túl sokáig tart az összes statisztika frissítése során, érdemes lehet több szelektíven kipróbálni, hogy mely oszlopokra van szükség a gyakori statisztikai frissítésekhez. Előfordulhat például, hogy csak a dátumoszlopokat szeretné frissíteni, amelyekbe napi rendszerességgel kerülnek új értékek. **A legtöbb előnyt úgy kapja meg, hogy az összekapcsolásokban részt vevő oszlopokra vonatkozó frissített statisztikát, a WHERE záradékban használt oszlopokat és a GROUP BY utasításban található oszlopokat használja.**

Lásd még a [táblastatisztikák kezelésével][Manage table statistics], a [CREATE STATISTICS][CREATE STATISTICS] és az [UPDATE STATISTICS][UPDATE STATISTICS] utasítással foglalkozó témaköröket.

## <a name="use-dmvs-to-monitor-and-optimize-your-queries"></a>A lekérdezések megfigyelése és optimalizálása DMV-kkel
Az SQL Analytics több DMV is használható, amelyek a lekérdezések végrehajtásának figyelésére szolgálnak.  A megfigyeléssel foglalkozó alábbi témakör részletes útmutatót szolgáltat ahhoz, hogyan nézheti meg egy futtatott lekérdezés részleteit.  Ha gyorsan szeretne lekérdezéseket kikeresni a DMV-kben, segíthet, ha a lekérdezéseknél használja a LABEL beállítást.

Lásd még a [számítási feladatok DMV-vel végzett megfigyelésével][Monitor your workload using DMVs], valamint a [LABEL][LABEL], [OPTION][OPTION], [sys.dm_exec_sessions][sys.dm_exec_sessions], [sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests], [sys.dm_pdw_request_steps][sys.dm_pdw_request_steps], [sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests], [sys.dm_pdw_dms_workers], [DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] és [sys.dm_pdw_waits][sys.dm_pdw_waits] elemekkel foglalkozó témaköröket.

## <a name="tune-query-performance-with-new-product-enhancements"></a>A lekérdezési teljesítmény finomhangolása új termékek fejlesztésével
- [Teljesítmény-finomhangolás tényleges táblán alapuló nézetekkel](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-materialized-views)
- [Teljesítmény-finomhangolás rendezett fürtözött oszlopcentrikus index használatával](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-ordered-cci)
- [Teljesítmény-finomhangolás eredményhalmaz gyorsítótárazásával](https://docs.microsoft.com/azure/sql-data-warehouse/performance-tuning-result-set-caching)

## <a name="group-insert-statements-into-batches"></a>INSERT utasítások csoportosítása kötegekbe
A kis táblák INSERT utasítással végzett egyszeri feltöltése vagy akár egy keresés rendszeres újratöltése is kielégítheti az igényeit, ha egy, a következőhöz hasonló utasítást használ: `INSERT INTO MyLookup VALUES (1, 'Type 1')`.  Ha azonban egy nap során több ezer vagy több millió sort kell betöltenie, észreveheti, hogy az egyszeres INSERT utasítások nem feltétlenül bizonyulnak elegendőnek.  Ehelyett érdemes olyan folyamatokat kifejleszteni, amelyek egy fájlba írnak, amelyet egy időről időre aktiválódó másik folyamat rendszeresen betölt.

Lásd még: [INSERT][INSERT].

## <a name="use-polybase-to-load-and-export-data-quickly"></a>Az adatok gyors betöltése és exportálása a PolyBase segítségével

 Az SQL Analytics számos eszközön keresztül támogatja az adatok betöltését és exportálását, többek között a Azure Data Factory, a Base és a BCP használatával.  Kis mennyiségű adat kezelése esetén, ahol a teljesítmény nem kulcsfontosságú tényező, bármelyik eszköz megfelelhet az igényeinek.  Amikor nagy mennyiségű adatot tölt be vagy exportál, vagy gyors teljesítményre van szükség, a PolyBase a legjobb választás.  
 
 A Base úgy lett kialakítva, hogy kihasználja a MPP (masszívan párhuzamos feldolgozási) architektúrát, és minden más eszköznél gyorsabban betöltse és exportálja az adatmennyiséget.  A PolyBase-betöltések a CTAS vagy az INSERT INTO paranccsal futtathatók.  **A CTAS használata minimalizálja a tranzakciók naplózásának mértékét, és ez az adatok betöltésének leggyorsabb módja.** 
 
  A Azure Data Factory támogatja a alapszintű terheléseket is, és a CTAS hasonló teljesítményt érhet el.  A PolyBase többféle fájlformátumot támogat, beleértve a Gzip-fájlokat is.  **A gzip szövegfájlok használatakor az átviteli sebesség maximalizálása érdekében bontsa a fájlokat 60 vagy több fájlba, hogy maximalizálja a terhelés párhuzamosságát.**  A gyorsabb teljes átviteli teljesítmény érdekében érdemes lehet egy időben betölteni az adatokat.

Lásd még: [adatok betöltése][Load data], [útmutató a következőhöz: alapszintű][Guide for using PolyBase], [SQL Pool betöltési minták és stratégiák][Azure SQL Data Warehouse loading patterns and strategies], [adatok betöltése a Azure Data Factory][Load Data with Azure Data Factory], [adatok áthelyezése Azure Data Factory][Move data with Azure Data Factory], [külső fájlformátum létrehozása][CREATE EXTERNAL FILE FORMAT], [Létrehozás Select (CTAS) tábla][Create table as select (CTAS)]

## <a name="load-then-query-external-tables"></a>Betöltés, majd külső táblák lekérdezése
Míg a PolyBase, más néven a külső táblák használata lehet az adatok betöltésének leggyorsabb módja, lekérdezésekhez nem ez a legjobb megoldás. A alapszintű táblák jelenleg csak az Azure Blob-fájlokat és Azure Data Lake tárolót támogatják. Ezekhez a fájlokhoz nem tartoznak külön számítási erőforrások.  

Ennek eredményeképpen az SQL Analytics nem tudja kiszervezni ezt a munkát, ezért az adatolvasáshoz be kell töltenie a teljes fájlt a tempdb-be való betöltésével.  Ezért ha több olyan lekérdezése is van, amelyek ennek a fájlnak az adatait kérik le, érdemesebb egyszer betölteni az adatokat, és úgy beállítani a lekérdezéseket, hogy a helyi táblát használják.

Lásd még a [Útmutató a PolyBase használatához][Guide for using PolyBase].

## <a name="hash-distribute-large-tables"></a>Nagy táblák kivonatos elosztása
Alapértelmezés szerint a táblák ciklikus időszeleteléssel vannak elosztva.  Ez megkönnyíti, hogy a felhasználók anélkül kezdhessenek neki a táblák létrehozásának, hogy dönteniük kéne az elosztás módjáról.  A ciklikus időszeletelésű táblák megfelelő megoldást jelenthetnek egye számítási feladatok esetén, a legtöbb esetben azonban egy elosztási oszlop kiválasztása megfelelőbb eredményre vezet.  A leggyakoribb példája annak, amikor egy oszloppal elosztott tábla sokkal jobb teljesítményt nyújt egy ciklikus időszeletelésűnél, amikor két nagy tábla egyesül.  

Ha például van egy rendelésazonosító alapján elosztott rendelési táblája és egy, szintén a rendelésazonosító alapján elosztott tranzakciótáblája, akkor ha a rendelési táblát a tranzakciótáblával egyesíti a rendelésazonosító oszlopnál, a lekérdezés továbbított lekérdezéssé válik, így kiküszöbölhetők az adatátviteli műveletek.  Ha kevesebb lépést kell végrehajtani, felgyorsul a lekérdezési folyamat.  A kisebb mértékű adatmozgás is gyorsabb lekérdezéseket eredményez.  

Elosztott tábla betöltésekor győződjön meg arról, hogy a bejövő adatok nem az elosztási kulcs alapján vannak rendezve, mivel ez lelassítja a betöltéseket.  A terjesztési oszlopok kiválasztásával kapcsolatos további részletekért tekintse meg az alábbi hivatkozásokat, valamint azt, hogyan határozhat meg egy elosztott táblát a CREATE TABLE utasítás WITH záradékában.

Lásd még a [táblák áttekintésével][Table overview], [a táblaelosztással][Table distribution], [a táblaelosztás kiválasztásával][Selecting table distribution], a [CREATE TABLE][CREATE TABLE] és a [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT] utasítással foglalkozó témaköröket.

## <a name="do-not-over-partition"></a>Túl sok partíció használatának kerülése
Míg az adatparticionálás hatékonyan kezelheti az adatait a partíciók váltásával vagy a vizsgálatok optimalizálásával a partíciók eltávolításával, a túl sok partíció lelassíthatja a lekérdezéseket.  Gyakran nagy részletességű particionálási stratégia, amely jól működik SQL Server előfordulhat, hogy nem működik megfelelően az SQL Analyticsben.  

A túl sok partíció a fürtözött oszlopcentrikus indexek hatékonyságát is csökkentheti, ha az egyes partíciók kevesebb mint 1 millió sorral rendelkeznek.  Ne feledje, hogy a színfalak mögött az SQL Analytics az Ön adatait 60-adatbázisba particionálja, így ha 100-partíciót tartalmazó táblát hoz létre, akkor ez valójában 6000 partíciót eredményez.  

Mindegyik számítási feladat különböző, így érdemes kísérletezni a particionálással, és kideríteni, hogy az adott számítási feladatnál melyik megoldás a célravezető.  Érdemes kisebb szintű részletességet használni, mint az SQL Serveren.  A napi partíciók helyett például érdemes lehet heti vagy havi partíciókat használni.

Lásd még a [tábla particionálásával][Table partitioning] foglalkozó témakört.

## <a name="minimize-transaction-sizes"></a>Tranzakcióméretek minimalizálása
Az INSERT, UPDATE és DELETE utasítások tranzakcióban futnak, és amikor meghiúsulnak, vissza kell őket állítani.  A hosszú visszaállítások előfordulásának csökkentése érdekében mindig minimalizálja a tranzakciók méretét, ahol erre lehetősége van.  Ezt úgy teheti meg, hogy részekre osztja az INSERT, UPDATE és DELETE utasításokat.  

Ha például egy olyan BESZÚRÁSt szeretne, amely 1 órát vesz igénybe, akkor a BESZÚRÁSt négy részre kell bontania, amely 15 percen belül fut.  Használja ki a minimális naplózást igénylő speciális eseteket, például a CTAS, TRUNCATE, DROP TABLE vagy INSERT utasításokat a táblák kiürítéséhez, hogy csökkentse a visszaállítással kapcsolatos kockázatokat.  

A visszaállítások kiküszöbölésének másik módja a csak metaadatokat használó műveletek alkalmazása, például az adatkezelés partícióváltása.  Ahelyett például, hogy DELETE utasítással törölné egy tábla összes olyan sorát, ahol az order_date 2001. október, havonta particionálhatja az adatokat, majd az adatokkal rendelkező partíciót egy másik tábla üres partíciójára cserélheti (lásd az ALTER TABLE-re vonatkozó példákat).  

A nem particionált táblák esetében érdemes lehet egy CTAS használni a táblákat megőrizni kívánt, a TÖRLÉSt nem használó adatbevitelre.  Ha egy CTAS ugyanannyi időt vesz igénybe, az sokkal biztonságosabb művelet, mert minimális tranzakciós naplózással rendelkezik, és szükség esetén gyorsan megszakítható.

Lásd még a [Tranzakciók megismerése][Understanding transactions], a [Tranzakciók optimalizálása][Optimizing transactions], a [tábla particionálásával][Table partitioning], valamint a [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE] és [Create table as select (CTAS)][Create table as select (CTAS)] utasításokkal foglalkozó témaköröket.

## <a name="reduce-query-result-sizes"></a>Lekérdezési eredmények méretének csökkentése  
Ez a lépés segít elkerülni a nagyméretű lekérdezési eredmények által okozott ügyféloldali problémákat.  A lekérdezés szerkesztésével csökkentheti a visszaadott sorok számát. Egyes lekérdezés-létrehozási eszközök lehetővé teszik a "legfontosabb N" szintaxis hozzáadását az egyes lekérdezésekhez.  A lekérdezés eredményét egy ideiglenes táblára is CETAS, majd a viszonyítási alap exportálást használhatja az alacsonyabb szintű feldolgozáshoz.

## <a name="use-the-smallest-possible-column-size"></a>A lehető legkisebb oszlopméret használata
A DDL meghatározásakor az adatait támogató legkisebb adattípussal javíthatja a lekérdezési teljesítményt.  Ez különösen fontos a CHAR és VARCHAR oszlopok esetén.  Ha egy oszlop leghosszabb értéke 25 karakterből áll, akkor VARCHAR(25) típusként határozza meg az oszlopot.  Ne határozza meg az összes karakteroszlopot nagy alapértelmezett hosszúságértékkel.  NVARCHAR helyett VARCHAR típusként határozza meg az oszlopokat, amikor ez is elegendő.

Lásd még a [táblák áttekintésével][Table overview], a [tábla adattípusaival][Table data types], valamint a [CREATE TABLE][CREATE TABLE] utasítással foglalkozó témaköröket.

## <a name="use-temporary-heap-tables-for-transient-data"></a>Ideiglenes halomtáblák használata átmeneti adatokhoz
Amikor átmenetileg kivezeti az adattárolást, előfordulhat, hogy egy halom tábla használatával gyorsabban fog megjelenni az általános folyamat.  Ha csak azért tölt be adatokat, hogy további átalakítások futtatása előtt előkészítse őket, sokkal gyorsabban töltheti a táblát egy halomtáblába, mint egy fürtözött oszlopcentrikus táblába.  

Azzal is tovább gyorsíthatja a folyamatot, ha az adatokat állandó tároló helyett ideiglenes táblába tölti.  Az ideiglenes táblák "#" karakterrel kezdődnek, és csak az azt létrehozó munkamenet számára érhetők el, így csak korlátozott helyzetekben működhetnek.   A halomtáblákat a CREATE TABLE utasítás WITH záradékával lehet meghatározni.  Ha ideiglenes táblát használ, ne felejtsen el rajta is statisztikákat létrehozni.

Lásd még az [ideiglenes táblákkal][Temporary tables], illetve a [CREATE TABLE][CREATE TABLE] és [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT] utasításokkal foglalkozó témaköröket.

## <a name="optimize-clustered-columnstore-tables"></a>Fürtözött oszlopcentrikus táblák optimalizálása
A fürtözött oszlopcentrikus indexek az egyik leghatékonyabb módszer az SQL Analyticsben tárolt adatai tárolására.  Alapértelmezés szerint az SQL Analytics táblái fürtözött Oszlopcentrikus jönnek létre.  Annak érdekében, hogy az oszlopcentrikus táblák a lehető legjobb teljesítménnyel fussanak, fontos a jó szegmensminőség.  

Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  A szegmensminőség a tömörített sorcsoportokban található sorok száma alapján mérhető fel.  A [táblaindexekkel][Causes of poor columnstore index quality] foglalkozó cikk [az oszlopcentrikus indexek gyenge minőségének okait][Table indexes] ismertető szakaszában részletes utasításokat talál, amelyek a fürtözött oszlopcentrikus táblák szegmensminőségének észlelésével és javításával foglalkoznak.  

Mivel a kiváló minőségű oszlopcentrikus szegmensek fontosak, hasznos lehet a közepes vagy nagy erőforrás-osztályba tartozó felhasználói azonosítók használata az betöltéshez. Az alacsonyabb [adattárház-egységek](what-is-a-data-warehouse-unit-dwu-cdwu.md) használata azt jelenti, hogy nagyobb erőforrás-osztályt szeretne hozzárendelni a betöltési felhasználóhoz.

Mivel a oszlopcentrikus-táblák általában nem küldenek le az adatlemezeket egy tömörített oszlopcentrikus, amíg a táblázat több mint 1 000 000 sort használ, és minden SQL Analytics-tábla 60 táblázatba van particionálva, a oszlopcentrikus-táblák azonban nem kapnak lekérdezést, kivéve, ha a táblában több mint 60 000 000 sor szerepel.  Előfordulhat, hogy a 60 milliónál kevesebb sorral rendelkező táblák esetén egyáltalán nem érdemes oszlopcentrikus indexet használni.  A használatuk azonban hátrányt sem jelent.  

Ha particionálja az adatait, akkor azt is érdemes figyelembe venni, hogy minden partíciónak 1 millió sorral kell rendelkeznie a fürtözött oszlopcentrikus indexek előnyeinek kihasználása érdekében.  Ha egy tábla 100 partícióval rendelkezik, akkor legalább 6 milliárd sorral kell rendelkeznie a fürtözött oszloptárolók előnyeinek kihasználása érdekében (60 elosztás × 100 partíció × 1 millió sor).  

Ha a tábla nem rendelkezik 6 milliárd sorral, akkor csökkentse a partíciók számát, vagy fontolja meg egy halomtábla használatát.  Azzal is érdemes lehet kísérletezni, hogy jobb teljesítmény érhető-e el egy másodlagos indexekkel rendelkező halomtáblával, mint egy oszlopcentrikussal.

Oszlopcentrikus tábla lekérdezésekor a lekérdezések gyorsabban futnak, ha csak a szükséges oszlopokat választja ki.  

Lásd még a [táblaindexekkel][Table indexes], az [oszlopcentrikus indexek áttekintésével][Columnstore indexes guide] és az [oszlopcentrikus indexek újjáépítésével][Rebuilding columnstore indexes] foglalkozó témaköröket.

## <a name="use-larger-resource-class-to-improve-query-performance"></a>Nagyobb erőforrásosztály használata a lekérdezés teljesítményének javítása érdekében
Az SQL Analytics erőforráscsoportok használatával foglal le memóriát a lekérdezésekhez.  Az összes felhasználó hozzá van rendelve a kisméretű erőforrás osztályhoz, amely elosztás esetén 100 MB memóriát biztosít.  Mivel mindig 60 elosztás van és mindegyik elosztás minimum 100 MB-ot kap, a rendszeren belül összesen 6000 MB, vagyis majdnem 6 GB memória van lefoglalva.  

Bizonyos lekérdezéseknél, például a nagyobb egyesítéseknél vagy a fürtözött oszlopcentrikus táblákba végzett betöltésnél előnyt jelent a nagyobb memórialefoglalások használata.  Néhány lekérdezésnél, például amelyek csak vizsgálati műveleteket végeznek, ezeknek a használata nem jár további előnnyel.  A nagyobb erőforrás-osztályok kihasználása a flip oldalon csökkenti a párhuzamosságot, ezért figyelembe kell vennie ezt a hatást, mielőtt az összes felhasználót egy nagy erőforrás-osztályba helyezné.

Lásd még: [Erőforrásosztályok számítási feladatok kezeléséhez](resource-classes-for-workload-management.md)

## <a name="use-smaller-resource-class-to-increase-concurrency"></a>Kisebb erőforrásosztály használata az egyidejűség mértékének növelése érdekében
Ha azt tapasztalja, hogy a felhasználói lekérdezések hosszú késleltetést mutatnak, előfordulhat, hogy a felhasználók nagyobb erőforrás-osztályokon futnak, és számos egyidejűségi tárolóhelyet használnak, ami más lekérdezések várólistára helyezését okozza.  Ha a `SELECT * FROM sys.dm_pdw_waits` parancs futtatásakor a rendszer sorokat ad vissza, láthatja, hogy a felhasználók lekérdezései sorban állnak.

Lásd még: [erőforrás-osztályok a számítási feladatok kezeléséhez](resource-classes-for-workload-management.md), [sys. DM _pdw_waits][sys.dm_pdw_waits]

## <a name="other-resources"></a>Egyéb háttéranyagok
Lásd még az általános problémákat és megoldásokat tartalmazó, [hibaelhárítással][Troubleshooting] foglalkozó témakört.

Ha nem találta meg, amit ebben a cikkben keres, próbálkozzon az oldal bal oldalán található "dokumentumok keresése" kifejezéssel az összes Azure szinapszis-dokumentum kereséséhez.  Az [Azure szinapszis fórumának][Azure SQL Data Warehouse MSDN Forum] célja, hogy kérdéseket tegyen fel más felhasználók és az Azure szinapszis-termékcsoport számára. 

Aktívan figyeljük ezt a fórumot, és gondoskodunk róla, hogy tőlünk vagy egy másik felhasználótól választ kapjon a kérdéseire.  Ha szeretne kérdéseket feltenni a Stack Overflowre, akkor egy [Azure szinapszis stack overflow fórum][Azure SQL Data Warehouse Stack Overflow Forum]is rendelkezésre áll.

Végezetül kérjük, használja az [Azure szinapszis visszajelzési][Azure SQL Data Warehouse Feedback] oldalát a funkciókra vonatkozó kérések elvégzéséhez.  Ha kéréseket tesz közzé vagy más kérésekre szavaz, sokat segíthet a szolgáltatások fontossági sorrendjének megállapításában.

<!--Image references-->

<!--Article references-->
[Create a support ticket]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Create table as select (CTAS)]: ./sql-data-warehouse-develop-ctas.md
[Table overview]: ./sql-data-warehouse-tables-overview.md
[Table data types]: ./sql-data-warehouse-tables-data-types.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexes]: ./sql-data-warehouse-tables-index.md
[Causes of poor columnstore index quality]: ./sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality
[Rebuilding columnstore indexes]: ./sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Manage table statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary tables]: ./sql-data-warehouse-tables-temporary.md
[Guide for using PolyBase]: ./guidance-for-loading-data.md
[Load data]: ./design-elt-data-loading.md
[Move data with Azure Data Factory]: ../data-factory/transform-data-using-machine-learning.md
[Load data with Azure Data Factory]: ../data-factory/load-azure-sql-data-warehouse.md
[Load data with bcp]: /sql/tools/bcp-utility
[Load data with PolyBase]: ./load-data-wideworldimportersdw.md
[Monitor your workload using DMVs]: ./sql-data-warehouse-manage-monitor.md
[Pause compute resources]: ./sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Resume compute resources]: ./sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Scale compute resources]: ./sql-data-warehouse-manage-compute-overview.md#scale-compute
[Understanding transactions]: ./sql-data-warehouse-develop-transactions.md
[Optimizing transactions]: ./sql-data-warehouse-develop-best-practices-transactions.md
[Troubleshooting]: ./sql-data-warehouse-troubleshoot.md
[LABEL]: ./sql-data-warehouse-develop-label.md

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
