---
title: A fejlesztés legjobb gyakorlatai
description: Javaslatok és gyakorlati tanácsok, amelyeket ismernie kell, ahogy megoldásokat fejleszt a Synapse SQL-készlethez.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/04/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: a4902d2817c98b002b9767ad55d7ac60f5cb0c93
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350710"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>A Synapse SQL-készlet fejlesztésével kapcsolatos gyakorlati tanácsok
Ez a cikk az adatraktár-megoldás fejlesztése során útmutatást és gyakorlati tanácsokat ismerteti. 

## <a name="tune-query-performance-with-new-product-enhancements"></a>A lekérdezési teljesítmény finomhangolása új termékfejlesztésekkel  
- [Teljesítmény-finomhangolás tényleges táblán alapuló nézetekkel](performance-tuning-materialized-views.md)
- [Teljesítmény-finomhangolás rendezett fürtözött oszlopcentrikus index használatával](performance-tuning-ordered-cci.md)
- [Teljesítmény-finomhangolás eredményhalmaz gyorsítótárazásával](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Költségek csökkentése felfüggesztés és méretezés által
A költségek felfüggesztéssel és skálázással való csökkentéséről további információért tekintse meg a [Számítások kezelése](sql-data-warehouse-manage-compute-overview.md) című szakaszt. 

## <a name="maintain-statistics"></a>Statisztikák karbantartása
A szinapszis SQL-készlet beállítható úgy, hogy automatikusan észlelje és létrehozza az oszlopok statisztikáit.  Az optimalizáló által létrehozott lekérdezési tervek csak olyan jók, mint a rendelkezésre álló statisztikák.  Azt javasoljuk, hogy engedélyezze a AUTO_CREATE_STATISTICS az adatbázisokhoz, és tartsa a statisztikákat naponta vagy minden egyes betöltés után, hogy a lekérdezésekben használt oszlopok statisztikái mindig naprakészek legyenek. 

Ha úgy találja, hogy túl sokáig tart az összes statisztika frissítése, érdemes megpróbálni szelektívebbé tenni, hogy mely oszlopoknak van szükségük gyakori statisztikai frissítésekre. Előfordulhat például, hogy csak a dátumoszlopokat szeretné frissíteni, amelyekbe napi rendszerességgel kerülnek új értékek. **A legnagyobb hasznot úgy nyerheti meg, ha frissített statisztikákat tart az illesztésekben részt vevő oszlopokról, a WHERE záradékban használt oszlopokról és a GROUP BY-ben található oszlopokról.**

Lásd még a [táblastatisztikák kezelésével](sql-data-warehouse-tables-statistics.md), a [CREATE STATISTICS](sql-data-warehouse-tables-statistics.md) és az [UPDATE STATISTICS](sql-data-warehouse-tables-statistics.md#updating-statistics) utasítással foglalkozó témaköröket.

## <a name="hash-distribute-large-tables"></a>Nagy táblák kivonatos elosztása
Alapértelmezés szerint a táblák ciklikus időszeleteléssel vannak elosztva.  Ez a kialakítás megkönnyíti a felhasználók számára a táblák létrehozásának megkezdését anélkül, hogy el kellene dönteniük, hogyan kell elosztani a táblákat.  A ciklikus időszeletelésű táblák megfelelő megoldást jelenthetnek egye számítási feladatok esetén, a legtöbb esetben azonban egy elosztási oszlop kiválasztása megfelelőbb eredményre vezet.  A leggyakoribb példája annak, amikor egy oszloppal elosztott tábla sokkal jobb teljesítményt nyújt egy ciklikus időszeletelésűnél, amikor két nagy tábla egyesül.  Ha például van egy rendelésazonosító alapján elosztott rendelési táblája és egy, szintén a rendelésazonosító alapján elosztott tranzakciótáblája, akkor ha a rendelési táblát a tranzakciótáblával egyesíti a rendelésazonosító oszlopnál, a lekérdezés továbbított lekérdezéssé válik, így kiküszöbölhetők az adatátviteli műveletek.  Ha kevesebb lépést kell végrehajtani, felgyorsul a lekérdezési folyamat.  A kisebb mértékű adatmozgás is gyorsabb lekérdezéseket eredményez.  Ez a magyarázat csak a felszínt karcolja meg. Elosztott tábla betöltésekor győződjön meg arról, hogy a bejövő adatok nem az elosztási kulcs alapján vannak rendezve, mivel ez lelassítja a betöltéseket.  Az alábbi hivatkozásokon további részleteket talál arról, hogy a terjesztési oszlopok kiválasztása hogyan javíthatja a teljesítményt, valamint hogyan határozhat meg egy elosztott táblát a CREATE TABLES utasítás WITH záradékában.

Lásd még a [táblák áttekintésével](sql-data-warehouse-tables-overview.md), [a táblaelosztással](sql-data-warehouse-tables-distribute.md), [a táblaelosztás kiválasztásával](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), a [CREATE TABLE](sql-data-warehouse-tables-overview.md) és a [CREATE TABLE AS SELECT](sql-data-warehouse-develop-ctas.md) utasítással foglalkozó témaköröket.

## <a name="do-not-over-partition"></a>Túl sok partíció használatának kerülése
Míg az adatok particionálása hatékony lehet az adatok partícióváltással vagy a vizsgálatok optimalizálásával a partíció megszüntetése révén történő karbantartásához, a túl sok partíció lelassulhat a lekérdezések.  Gyakran egy nagy részletességű particionálási stratégia, amely jól működik az SQL Server nem működik jól az SQL-készlet.  A túl sok partíció a fürtözött oszlopcentrikus indexek hatékonyságát is csökkentheti, ha az egyes partíciók kevesebb mint 1 millió sorral rendelkeznek.  Ne feledje, hogy a színfalak mögött az SQL-készlet 60 adatbázisba particionálja az adatokat, így ha 100 partíciót tartalmazó táblát hoz létre, az valójában 6000 partíciót eredményez.  Mindegyik számítási feladat különböző, így érdemes kísérletezni a particionálással, és kideríteni, hogy az adott számítási feladatnál melyik megoldás a célravezető.  Érdemes kisebb szintű részletességet használni, mint az SQL Serveren.  A napi partíciók helyett például érdemes lehet heti vagy havi partíciókat használni.

Lásd még a [tábla particionálásával](sql-data-warehouse-tables-partition.md) foglalkozó témakört.

## <a name="minimize-transaction-sizes"></a>Tranzakcióméretek minimalizálása
Az INSERT, UPDATE és DELETE utasítások tranzakcióban futnak, és amikor meghiúsulnak, vissza kell őket állítani.  A hosszú visszaállítások előfordulásának csökkentése érdekében mindig minimalizálja a tranzakciók méretét, ahol erre lehetősége van.  Ezt úgy teheti meg, hogy részekre osztja az INSERT, UPDATE és DELETE utasításokat.  Ha például van egy INSERT- je, amely várhatóan 1 órát vesz igénybe, ha lehetséges, bontsa fel az INSERT-et négy részre, amelyek mindegyike 15 perc alatt fog futni.  Használja ki a minimális naplózást igénylő speciális eseteket, például a CTAS, TRUNCATE, DROP TABLE vagy INSERT utasításokat a táblák kiürítéséhez, hogy csökkentse a visszaállítással kapcsolatos kockázatokat.  A visszaállítások kiküszöbölésének másik módja a csak metaadatokat használó műveletek alkalmazása, például az adatkezelés partícióváltása.  Ahelyett például, hogy DELETE utasítással törölné egy tábla összes olyan sorát, ahol az order_date 2001. október, havonta particionálhatja az adatokat, majd az adatokkal rendelkező partíciót egy másik tábla üres partíciójára cserélheti (lásd az ALTER TABLE-re vonatkozó példákat).  A nem particionált táblák esetén érdemes lehet CTAS utasítással kiírni a táblában megőrizni kívánt adatokat a DELETE használata helyett.  Ha a CTAS ugyanannyi időt vesz igénybe, sokkal biztonságosabb, mivel minimális tranzakciónaplózással rendelkezik, és szükség esetén gyorsan visszavonható.

Lásd [még: A tranzakciók ismertetése](sql-data-warehouse-develop-transactions.md), [a tranzakciók optimalizálása](sql-data-warehouse-develop-best-practices-transactions.md), [a táblaparticionálása](sql-data-warehouse-tables-partition.md), [a TÁBLA csonkítása](https://msdn.microsoft.com/library/ms177570.aspx), [a TÁBLA MÓDOSÍTÁSA](https://msdn.microsoft.com/library/ms190273.aspx), a Tábla [létrehozása kijelölésként (CTAS)](sql-data-warehouse-develop-ctas.md)című témakört.

## <a name="use-the-smallest-possible-column-size"></a>A lehető legkisebb oszlopméret használata
A DDL meghatározásakor az adatokat támogató legkisebb adattípus használata javítja a lekérdezés teljesítményét.  Ez különösen fontos a CHAR és VARCHAR oszlopok esetén.  Ha egy oszlop leghosszabb értéke 25 karakterből áll, akkor VARCHAR(25) típusként határozza meg az oszlopot.  Ne határozza meg az összes karakteroszlopot nagy alapértelmezett hosszúságértékkel.  NVARCHAR helyett VARCHAR típusként határozza meg az oszlopokat, amikor ez is elegendő.

Lásd még a [táblák áttekintésével](sql-data-warehouse-tables-overview.md), a [tábla adattípusaival](sql-data-warehouse-tables-data-types.md), valamint a [CREATE TABLE](sql-data-warehouse-tables-overview.md) utasítással foglalkozó témaköröket.

## <a name="optimize-clustered-columnstore-tables"></a>Fürtözött oszlopcentrikus táblák optimalizálása
Fürtözött oszlopcentrikus indexek az egyik leghatékonyabb módja az adatok sql készletben tárolható.  Alapértelmezés szerint az SQL-készletben lévő táblák fürtözött ColumnStore néven jönnek létre.  Annak érdekében, hogy az oszlopcentrikus táblák a lehető legjobb teljesítménnyel fussanak, fontos a jó szegmensminőség.  Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  A szegmensminőség a tömörített sorcsoportokban található sorok száma alapján mérhető fel.  A [táblaindexekkel](sql-data-warehouse-tables-index.md) foglalkozó cikk [az oszlopcentrikus indexek gyenge minőségének okait](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) ismertető szakaszában részletes utasításokat talál, amelyek a fürtözött oszlopcentrikus táblák szegmensminőségének észlelésével és javításával foglalkoznak.  Mivel a kiváló minőségű oszlopcentrikus szegmensek fontosak, célszerű olyan felhasználói azonosítókat használni, amelyek a közepes vagy nagy erőforrásosztályban találhatók az adatok betöltéséhez. Az alacsonyabb [adattárház egységek](what-is-a-data-warehouse-unit-dwu-cdwu.md) használata azt jelenti, hogy nagyobb erőforrásosztályt szeretne hozzárendelni a betöltő felhasználóhoz.

Mivel az oszlopcentrikus táblák általában nem adnak adatokat egy tömörített oszlopcentrikus szegmensbe, amíg táblaként több mint 1 millió sor van, és minden SQL-készlettábla 60 táblára van felosztva, ökölszabályként az oszlopcentrikus táblák nem részesülnek a lekérdezés számára, kivéve, ha a táblázat több mint 60 millió sort tartalmaz.  Előfordulhat, hogy a 60 milliónál kevesebb sorral rendelkező táblák esetén egyáltalán nem érdemes oszlopcentrikus indexet használni.  A használatuk azonban hátrányt sem jelent.  Ha particionálja az adatait, akkor azt is érdemes figyelembe venni, hogy minden partíciónak 1 millió sorral kell rendelkeznie a fürtözött oszlopcentrikus indexek előnyeinek kihasználása érdekében.  Ha egy tábla 100 partícióval rendelkezik, akkor legalább 6 milliárd sorral kell rendelkeznie a fürtözött oszloptárolók előnyeinek kihasználása érdekében (60 elosztás × 100 partíció × 1 millió sor).  Ha a tábla nem rendelkezik 6 milliárd sorral, akkor csökkentse a partíciók számát, vagy fontolja meg egy halomtábla használatát.  Azzal is érdemes lehet kísérletezni, hogy jobb teljesítmény érhető-e el egy másodlagos indexekkel rendelkező halomtáblával, mint egy oszlopcentrikussal.

Oszlopcentrikus tábla lekérdezésekor a lekérdezések gyorsabban futnak, ha csak a szükséges oszlopokat választja ki.  

Lásd még a [táblaindexekkel](sql-data-warehouse-tables-index.md), az [oszlopcentrikus indexek áttekintésével](https://msdn.microsoft.com/library/gg492088.aspx) és az [oszlopcentrikus indexek újjáépítésével](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality) foglalkozó témaköröket.

## <a name="next-steps"></a>További lépések
Ha nem találja, amit keres ebben a cikkben, próbálja meg a "Docs keresése" a bal oldalon ezen az oldalon az Összes Azure Synapse Analytics-dokumentumok kereséséhez.  Az [Azure SQL Data Warehouse Forum](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) egy olyan hely, ahol kérdéseket tehet fel más felhasználóknak és az SQL Data Warehouse termékcsoportnak.  Aktívan figyeljük ezt a fórumot, és gondoskodunk róla, hogy tőlünk vagy egy másik felhasználótól választ kapjon a kérdéseire.  Ha inkább a Stack Overflow-ra vonatkozó kérdéseket szeretne feltenni, van egy [Azure SQL Data Warehouse Stack Overflow fórumunk](https://stackoverflow.com/questions/tagged/azure-sqldw) is.

A szolgáltatással kapcsolatos kéréseit az [Azure SQL Data Warehouse visszajelzési](https://feedback.azure.com/forums/307516-sql-data-warehouse) oldalán küldheti be.  Ha kéréseket tesz közzé vagy más kérésekre szavaz, sokat segíthet a szolgáltatások fontossági sorrendjének megállapításában.


