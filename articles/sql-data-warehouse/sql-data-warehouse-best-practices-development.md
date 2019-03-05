---
title: Fejlesztési ajánlott eljárások az Azure SQL Data Warehouse |} A Microsoft Docs
description: Javaslatok és ajánlott eljárások, amelyeket érdemes tudni az Azure SQL Data Warehouse-megoldások fejlesztésekor.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 09/04/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 0993144a3260e9a3119601db3433e4455bb39f65
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2019
ms.locfileid: "57313157"
---
# <a name="development-best-practices-for-azure-sql-data-warehouse"></a>Az Azure SQL Data Warehouse alkalmazásfejlesztési gyakorlatok
Ez a cikk ismerteti a útmutatás és ajánlott eljárások során az adattárház-megoldást. 

## <a name="reduce-cost-with-pause-and-scale"></a>Költségek csökkentése felfüggesztés és méretezés által
A költségek felfüggesztéssel és skálázással való csökkentéséről további információért tekintse meg a [Számítások kezelése](sql-data-warehouse-manage-compute-overview.md) című szakaszt. 


## <a name="maintain-statistics"></a>Statisztikák karbantartása
Győződjön meg arról, naponta, vagy minden betöltés után frissíti a statisztikákat.  Mindig érdemes figyelembe venni, hogyan viszonyul egymáshoz a teljesítmény és a statisztikák létrehozásának és frissítésének költségei. Ha úgy gondolja, hogy túl sokáig tart az összes statisztika karbantartása, lehet, hogy körültekintőbben kell kiválasztania, mely oszlopok rendelkezzenek statisztikákkal vagy melyek igényelnek gyakori frissítést.  Előfordulhat például, hogy csak a dátumoszlopokat szeretné frissíteni, amelyekbe napi rendszerességgel kerülnek új értékek. **A legnagyobb előnnyel az jár, ha az összekapcsolások részét képező, a WHERE záradékban használt és a GROUP BY elemben megtalálható oszlopok statisztikáit kéri le.**

Lásd még a [táblastatisztikák kezelésével][Manage table statistics], a [CREATE STATISTICS][CREATE STATISTICS] és az [UPDATE STATISTICS][UPDATE STATISTICS] utasítással foglalkozó témaköröket.

## <a name="hash-distribute-large-tables"></a>Nagy táblák kivonatos elosztása
Alapértelmezés szerint a táblák ciklikus időszeleteléssel vannak elosztva.  Ez megkönnyíti, hogy a felhasználók anélkül kezdhessenek neki a táblák létrehozásának, hogy dönteniük kéne az elosztás módjáról.  A ciklikus időszeletelésű táblák megfelelő megoldást jelenthetnek egye számítási feladatok esetén, a legtöbb esetben azonban egy elosztási oszlop kiválasztása megfelelőbb eredményre vezet.  A leggyakoribb példája annak, amikor egy oszloppal elosztott tábla sokkal jobb teljesítményt nyújt egy ciklikus időszeletelésűnél, amikor két nagy tábla egyesül.  Ha például van egy rendelésazonosító alapján elosztott rendelési táblája és egy, szintén a rendelésazonosító alapján elosztott tranzakciótáblája, akkor ha a rendelési táblát a tranzakciótáblával egyesíti a rendelésazonosító oszlopnál, a lekérdezés továbbított lekérdezéssé válik, így kiküszöbölhetők az adatátviteli műveletek.  Ha kevesebb lépést kell végrehajtani, felgyorsul a lekérdezési folyamat.  A kisebb mértékű adatmozgás is gyorsabb lekérdezéseket eredményez.  Ez a magyarázat csak témakörre vonatkozó tudnivalókba. Elosztott tábla betöltésekor győződjön meg arról, hogy a bejövő adatok nem az elosztási kulcs alapján vannak rendezve, mivel ez lelassítja a betöltéseket.  Az alábbi hivatkozások további információt tartalmaznak arról, hogyan javíthatja a teljesítményt az elosztási oszlop kiválasztása, valamint arról, hogyan határozhat meg elosztott táblát a CREATE TABLES utasítás WITH záradékának használatával.

Lásd még a [táblák áttekintésével][Table overview], [a táblaelosztással][Table distribution], [a táblaelosztás kiválasztásával][Selecting table distribution], a [CREATE TABLE][CREATE TABLE] és a [CREATE TABLE AS SELECT][CREATE TABLE AS SELECT] utasítással foglalkozó témaköröket.

## <a name="do-not-over-partition"></a>Túl sok partíció használatának kerülése
Míg az adatok particionálása nagyon hatásos eszköz lehet az adatok partícióváltáson keresztüli megőrzéséhez vagy a vizsgálatok partíciókizárással végzett optimalizálásához, a túl sok partíció lelassíthatja a lekérdezéseket.  Az SQL Serveren jól működő részletesebb particionálási stratégia nem feltétlenül működik hasonló hatékonysággal az SQL Data Warehouse-ban.  A túl sok partíció a fürtözött oszlopcentrikus indexek hatékonyságát is csökkentheti, ha az egyes partíciók kevesebb mint 1 millió sorral rendelkeznek.  Ne feledje, hogy a háttérben az SQL Data Warehouse 60 adatbázisba particionálja az adatait, így ha 100 partícióval rendelkező táblát hoz létre, ez 6000 partíciót eredményez.  Mindegyik számítási feladat különböző, így érdemes kísérletezni a particionálással, és kideríteni, hogy az adott számítási feladatnál melyik megoldás a célravezető.  Érdemes kisebb szintű részletességet használni, mint az SQL Serveren.  A napi partíciók helyett például érdemes lehet heti vagy havi partíciókat használni.

Lásd még a [tábla particionálásával][Table partitioning] foglalkozó témakört.

## <a name="minimize-transaction-sizes"></a>Tranzakcióméretek minimalizálása
Az INSERT, UPDATE és DELETE utasítások tranzakcióban futnak, és amikor meghiúsulnak, vissza kell őket állítani.  A hosszú visszaállítások előfordulásának csökkentése érdekében mindig minimalizálja a tranzakciók méretét, ahol erre lehetősége van.  Ezt úgy teheti meg, hogy részekre osztja az INSERT, UPDATE és DELETE utasításokat.  Ha például olyan INSERT utasítása van, amelynek a futtatása várhatóan 1 órát vesz igénybe, ha megoldható, ossza 4, egyenként 15 percig futó részre az INSERT utasítást.  Használja ki a minimális naplózást igénylő speciális eseteket, például a CTAS, TRUNCATE, DROP TABLE vagy INSERT utasításokat a táblák kiürítéséhez, hogy csökkentse a visszaállítással kapcsolatos kockázatokat.  A visszaállítások kiküszöbölésének másik módja a csak metaadatokat használó műveletek alkalmazása, például az adatkezelés partícióváltása.  Ahelyett például, hogy DELETE utasítással törölné egy tábla összes olyan sorát, ahol az order_date 2001. október, havonta particionálhatja az adatokat, majd az adatokkal rendelkező partíciót egy másik tábla üres partíciójára cserélheti (lásd az ALTER TABLE-re vonatkozó példákat).  A nem particionált táblák esetén érdemes lehet CTAS utasítással kiírni a táblában megőrizni kívánt adatokat a DELETE használata helyett.  Noha egy CTAS utasítás ugyanannyi időt vesz igénybe, sokkal biztonságosabban futtatható művelet, mivel minimális tranzakciónaplózást igényel, és szükség esetén gyorsan megszakítható.

Lásd még a [tranzakciók megismerésével][Understanding transactions], a [tranzakciók optimalizálásával][Optimizing transactions], a [tábla particionálásával][Table partitioning], valamint a [TRUNCATE TABLE][TRUNCATE TABLE], [ALTER TABLE][ALTER TABLE] és [Create table as select (CTAS)][Create table as select (CTAS)] utasításokkal foglalkozó témaköröket.

## <a name="use-the-smallest-possible-column-size"></a>A lehető legkisebb oszlopméret használata
Ha a DDL meghatározásakor az adatokat támogató legkisebb adattípust használja, nagyban javíthatja a lekérdezés teljesítményét.  Ez különösen fontos a CHAR és VARCHAR oszlopok esetén.  Ha egy oszlop leghosszabb értéke 25 karakterből áll, akkor VARCHAR(25) típusként határozza meg az oszlopot.  Ne határozza meg az összes karakteroszlopot nagy alapértelmezett hosszúságértékkel.  NVARCHAR helyett VARCHAR típusként határozza meg az oszlopokat, amikor ez is elegendő.

Lásd még a [táblák áttekintésével][Table overview], a [tábla adattípusaival][Table data types], valamint a [CREATE TABLE][CREATE TABLE] utasítással foglalkozó témaköröket.

## <a name="optimize-clustered-columnstore-tables"></a>Fürtözött oszlopcentrikus táblák optimalizálása
A fürtözött oszlopcentrikus indexek az adatok tárolásának leghatékonyabb módjai közé tartoznak az SQL Data Warehouse-ban.  Alapértelmezés szerint a táblák az SQL Data Warehouse-ban fürtözött oszlopcentrikusként jönnek létre.  Annak érdekében, hogy az oszlopcentrikus táblák a lehető legjobb teljesítménnyel fussanak, fontos a jó szegmensminőség.  Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  A szegmensminőség a tömörített sorcsoportokban található sorok száma alapján mérhető fel.  A [táblaindexekkel][Table indexes] foglalkozó cikk [az oszlopcentrikus indexek gyenge minőségének okait][Causes of poor columnstore index quality] ismertető szakaszában részletes utasításokat talál, amelyek a fürtözött oszlopcentrikus táblák szegmensminőségének észlelésével és javításával foglalkoznak.  Kiváló minőségű oszlopcentrikus szegmensek fontosak, mert célszerű használni a felhasználók azonosítók, amelyek a közepes vagy nagy erőforrásosztály az adatok betöltéséhez. Lower használatával [adattárházegységek](what-is-a-data-warehouse-unit-dwu-cdwu.md) esetén érdemes nagyobb erőforrásosztályt hozzárendelni a betöltést végző felhasználóhoz.

Mivel az oszlopcentrikus táblák általában nem küldenek adatokat tömörített oszlopcentrikus szegmensbe addig, amíg nincs több mint 1 millió sor táblánként és az SQL Data Warehouse-táblák nincsenek 60 táblává particionálva, az oszlopcentrikus táblák használata nem célravezető a lekérdezéseknél, ha a tábla kevesebb, mint 60 millió sorral rendelkezik.  Előfordulhat, hogy a 60 milliónál kevesebb sorral rendelkező táblák esetén egyáltalán nem érdemes oszlopcentrikus indexet használni.  A használatuk azonban hátrányt sem jelent.  Ha particionálja az adatait, akkor azt is érdemes figyelembe venni, hogy minden partíciónak 1 millió sorral kell rendelkeznie a fürtözött oszlopcentrikus indexek előnyeinek kihasználása érdekében.  Ha egy tábla 100 partícióval rendelkezik, akkor legalább 6 milliárd sorral kell rendelkeznie a fürtözött oszloptárolók előnyeinek kihasználása érdekében (60 elosztás × 100 partíció × 1 millió sor).  Ha a tábla nem rendelkezik 6 milliárd sorral, akkor csökkentse a partíciók számát, vagy fontolja meg egy halomtábla használatát.  Azzal is érdemes lehet kísérletezni, hogy jobb teljesítmény érhető-e el egy másodlagos indexekkel rendelkező halomtáblával, mint egy oszlopcentrikussal.

Oszlopcentrikus tábla lekérdezésekor a lekérdezések gyorsabban futnak, ha csak a szükséges oszlopokat választja ki.  

Lásd még a [táblaindexekkel][Table indexes], az [oszlopcentrikus indexek áttekintésével][Columnstore indexes guide] és az [oszlopcentrikus indexek újjáépítésével][Rebuilding columnstore indexes] foglalkozó témaköröket.

## <a name="next-steps"></a>További lépések
Ha nem találta meg a cikkben, amit keresett, próbálkozzon az oldal bal oldalán található „Dokumentumok keresése” mezővel. Az itt megadott kifejezésre a rendszer az összes Azure SQL Data Warehouse-dokumentumban rákeres.  A [Azure SQL Data Warehouse fórum] [ Azure SQL Data Warehouse MSDN Forum] helyet, hogy más felhasználók és az SQL Data Warehouse termékcsoporttal kérdéseket tehet fel.  Aktívan figyeljük ezt a fórumot, és gondoskodunk róla, hogy tőlünk vagy egy másik felhasználótól választ kapjon a kérdéseire.  Ha inkább a Stack Overflow-ra vonatkozó kérdéseket szeretne feltenni, van egy [Azure SQL Data Warehouse Stack Overflow fórumunk][Azure SQL Data Warehouse Stack Overflow Forum] is.

A szolgáltatással kapcsolatos kéréseit az [Azure SQL Data Warehouse visszajelzési][Azure SQL Data Warehouse Feedback] oldalán küldheti be.  Ha kéréseket tesz közzé vagy más kérésekre szavaz, sokat segíthet a szolgáltatások fontossági sorrendjének megállapításában.

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
[Azure SQL Data Warehouse Stack Overflow Forum]:  http://stackoverflow.com/questions/tagged/azure-sqldw
[Azure SQL Data Warehouse loading patterns and strategies]: http://blogs.msdn.microsoft.com/sqlcat/20../../azure-sql-data-warehouse-loading-patterns-and-strategies/
