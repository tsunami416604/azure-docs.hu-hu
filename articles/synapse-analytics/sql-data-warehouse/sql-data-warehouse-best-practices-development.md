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
ms.openlocfilehash: 5857a10d0aaf0d0c37ab55a2d0d29e5315340c9f
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633653"
---
# <a name="development-best-practices-for-synapse-sql-pool"></a>A Synapse SQL-készlet fejlesztésével kapcsolatos gyakorlati tanácsok
Ez a cikk az SQL-készletmegoldás fejlesztése korútmutatást és gyakorlati tanácsokat ismerteti. 

## <a name="tune-query-performance-with-new-product-enhancements"></a>A lekérdezési teljesítmény finomhangolása új termékfejlesztésekkel  
- [Teljesítmény-finomhangolás tényleges táblán alapuló nézetekkel](performance-tuning-materialized-views.md)
- [Teljesítmény-finomhangolás rendezett fürtözött oszlopcentrikus index használatával](performance-tuning-ordered-cci.md)
- [Teljesítmény-finomhangolás eredményhalmaz gyorsítótárazásával](performance-tuning-result-set-caching.md)

## <a name="reduce-cost-with-pause-and-scale"></a>Költségek csökkentése felfüggesztés és méretezés által
A költségek szüneteltetés és méretezés révén történő csökkentéséről a [Számítási feladatok kezelése](sql-data-warehouse-manage-compute-overview.md) című cikkben olvashat bővebben. 

## <a name="maintain-statistics"></a>Statisztikák karbantartása
Az SQL-készlet beállítható úgy, hogy automatikusan észlelje és létrehozza az oszlopok statisztikáit.  Az optimalizáló által létrehozott lekérdezési tervek csak olyan jók, mint a rendelkezésre álló statisztikák.  

Azt javasoljuk, hogy engedélyezze a AUTO_CREATE_STATISTICS az adatbázisokhoz, és tartsa naprakészen a statisztikákat naponta vagy minden egyes betöltés után, hogy a lekérdezésekben használt oszlopok statisztikái mindig naprakészek legyenek. 

Ha úgy találja, hogy túl sokáig tart az összes statisztika frissítése, érdemes megpróbálni szelektívebbé tenni, hogy mely oszlopoknak van szükségük gyakori statisztikai frissítésekre. Előfordulhat például, hogy csak a dátumoszlopokat szeretné frissíteni, amelyekbe napi rendszerességgel kerülnek új értékek. 

> [!TIP]
> A legnagyobb hasznot úgy nyerheti meg, ha frissített statisztikákat tart az illesztésekben részt vevő oszlopokról, a WHERE záradékban használt oszlopokról és a GROUP BY-ben található oszlopokról.

Lásd [még: Táblastatisztika kezelése](sql-data-warehouse-tables-statistics.md), [STATISZTIKA LÉTREHOZÁSA](sql-data-warehouse-tables-statistics.md)és STATISZTIKA [FRISSÍTÉSE](sql-data-warehouse-tables-statistics.md#update-statistics).

## <a name="hash-distribute-large-tables"></a>Nagy táblák kivonatos elosztása
Alapértelmezés szerint a táblák ciklikus időszeleteléssel vannak elosztva.  Ez a kialakítás megkönnyíti a felhasználók számára a táblák létrehozásának megkezdését anélkül, hogy el kellene dönteniük, hogyan kell elosztani a táblákat.  

A ciklikus időszeletelésű táblák megfelelő megoldást jelenthetnek egye számítási feladatok esetén, a legtöbb esetben azonban egy elosztási oszlop kiválasztása megfelelőbb eredményre vezet.  A leggyakoribb példája annak, amikor egy oszloppal elosztott tábla sokkal jobb teljesítményt nyújt egy ciklikus időszeletelésűnél, amikor két nagy tábla egyesül.  

Ha például van egy rendelésazonosító alapján elosztott rendelési táblája és egy, szintén a rendelésazonosító alapján elosztott tranzakciótáblája, akkor ha a rendelési táblát a tranzakciótáblával egyesíti a rendelésazonosító oszlopnál, a lekérdezés továbbított lekérdezéssé válik, így kiküszöbölhetők az adatátviteli műveletek.  Ha kevesebb lépést kell végrehajtani, felgyorsul a lekérdezési folyamat.  A kisebb mértékű adatmozgás is gyorsabb lekérdezéseket eredményez.  

Elosztott tábla betöltésekor győződjön meg arról, hogy a bejövő adatok nem az elosztási kulcs alapján vannak rendezve, mivel ez lelassítja a betöltéseket.  Az alábbi cikkek további részleteket tartalmaznak a teljesítmény javításáról egy terjesztési oszlop kiválasztásával, és arról, hogyan definiálhat egy elosztott táblát a CREATE TABLES utasítás WITH záradékában.

Lásd [még: Tábla áttekintése](sql-data-warehouse-tables-overview.md), [Táblaeloszlás](sql-data-warehouse-tables-distribute.md), [Táblaeloszlás kiválasztása](https://blogs.msdn.microsoft.com/sqlcat/20../../choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/), [TÁBLA LÉTREHOZÁSA](sql-data-warehouse-tables-overview.md)és TÁBLA [LÉTREHOZÁSA VÁLASZTÓKÉNT](sql-data-warehouse-develop-ctas.md)

## <a name="do-not-over-partition"></a>Túl sok partíció használatának kerülése
Míg az adatok particionálása hatékony lehet az adatok partícióváltással vagy a vizsgálatok optimalizálásával a partíció megszüntetése révén történő karbantartásához, a túl sok partíció lelassulhat a lekérdezések.  

Gyakran előfordulhat, hogy az SQL Server en jól működik egy nagy részletességű particionálási stratégia, amely nem működik jól az SQL-készleten.  A túl sok partíció a fürtözött oszlopcentrikus indexek hatékonyságát is csökkentheti, ha az egyes partíciók kevesebb mint 1 millió sorral rendelkeznek.  

Ne feledje, hogy a színfalak mögött az SQL-készlet 60 adatbázisba particionálja az adatokat, így ha 100 partíciót tartalmazó táblát hoz létre, az valójában 6000 partíciót eredményez.  Mindegyik számítási feladat különböző, így érdemes kísérletezni a particionálással, és kideríteni, hogy az adott számítási feladatnál melyik megoldás a célravezető.  

> [!TIP]
> Fontolja meg egy kisebb részletesség, mint amit esetleg dolgozott az SQL Server.  A napi partíciók helyett például érdemes lehet heti vagy havi partíciókat használni.

Lásd [még: Táblaparticionálás](sql-data-warehouse-tables-partition.md).

## <a name="minimize-transaction-sizes"></a>Tranzakcióméretek minimalizálása
Az INSERT, UPDATE és DELETE utasítások tranzakcióban futnak, és amikor meghiúsulnak, vissza kell őket állítani.  A hosszú visszaállítások előfordulásának csökkentése érdekében mindig minimalizálja a tranzakciók méretét, ahol erre lehetősége van.  Ezt úgy teheti meg, hogy részekre osztja az INSERT, UPDATE és DELETE utasításokat.  

Ha például van egy INSERT- je, amely várhatóan 1 órát vesz igénybe, ha lehetséges, bontsa fel az INSERT-et négy részre, amelyek mindegyike 15 perc alatt fog futni.  A visszaállítási kockázat csökkentése érdekében használja ki a speciális minimális naplózási eseteket, például a CTAS- t, a CSONKOLÁSt, a DROP TABLE-t vagy az INSERT-et üres táblákhoz.  

A visszaállítások kiküszöbölésének másik módja a csak metaadatokat használó műveletek alkalmazása, például az adatkezelés partícióváltása.  Ahelyett például, hogy DELETE utasítással törölné egy tábla összes olyan sorát, ahol az order_date 2001. október, havonta particionálhatja az adatokat, majd az adatokkal rendelkező partíciót egy másik tábla üres partíciójára cserélheti (lásd az ALTER TABLE-re vonatkozó példákat).  

Particionálatlan táblák esetén a DELETE helyett ctas-t érdemes használni a táblában tartani kívánt adatok írásához.  Ha a CTAS ugyanannyi időt vesz igénybe, sokkal biztonságosabb, mivel minimális tranzakciónaplózással rendelkezik, és szükség esetén gyorsan visszavonható.

Lásd [még: A tranzakciók ismertetése](sql-data-warehouse-develop-transactions.md), [a tranzakciók optimalizálása](sql-data-warehouse-develop-best-practices-transactions.md), [a táblaparticionálás](sql-data-warehouse-tables-partition.md), [a TÁBLA csonkítása](https://msdn.microsoft.com/library/ms177570.aspx), [az ALTER TÁBLA](https://msdn.microsoft.com/library/ms190273.aspx)és a Create table as [select (CTAS) ismertetése.](sql-data-warehouse-develop-ctas.md)

## <a name="use-the-smallest-possible-column-size"></a>A lehető legkisebb oszlopméret használata
A DDL meghatározásakor az adatokat támogató legkisebb adattípus használata javítja a lekérdezés teljesítményét.  Ez a megközelítés különösen fontos a CHAR és VARCHAR oszlopok esetében.  

Ha egy oszlop leghosszabb értéke 25 karakterből áll, akkor VARCHAR(25) típusként határozza meg az oszlopot.  Ne határozza meg az összes karakteroszlopot nagy alapértelmezett hosszúságértékkel.  NVARCHAR helyett VARCHAR típusként határozza meg az oszlopokat, amikor ez is elegendő.

Lásd [még: Táblázat áttekintése](sql-data-warehouse-tables-overview.md), [Táblázat adattípusok](sql-data-warehouse-tables-data-types.md)és [TÁBLA LÉTREHOZÁSA](sql-data-warehouse-tables-overview.md).

## <a name="optimize-clustered-columnstore-tables"></a>Fürtözött oszlopcentrikus táblák optimalizálása
Fürtözött oszlopcentrikus indexek az egyik leghatékonyabb módja az adatok sql készletben tárolható.  Alapértelmezés szerint az SQL-készletben lévő táblák fürtözött ColumnStore néven jönnek létre.  

> [!NOTE]
> Az oszlopcentrikus táblák lekérdezéseinek optimális teljesítményének elérése érdekében fontos a jó szegmensminőség.  

Amikor a sorokat nagy memóriaterhelés mellett írja oszlopcentrikus táblákba, az oszlopcentrikus szegmens minősége gyengülhet.  A szegmensminőség a tömörített sorcsoportokban található sorok száma alapján mérhető fel.  

Tekintse meg a [gyenge oszlopcentrikus index minőségének okait](sql-data-warehouse-tables-index.md#causes-of-poor-columnstore-index-quality) a [Táblázat indexek](sql-data-warehouse-tables-index.md) cikkben a fürtözött oszlopcentrikus táblák szegmensminőségének észlelésével és javításával kapcsolatos lépésenkénti útmutatásért.  

Mivel a kiváló minőségű oszlopcentrikus szegmensek fontosak, célszerű olyan felhasználói azonosítókat használni, amelyek a közepes vagy nagy erőforrásosztályban találhatók az adatok betöltéséhez. Az alacsonyabb [adattárház egységek](what-is-a-data-warehouse-unit-dwu-cdwu.md) használata azt jelenti, hogy nagyobb erőforrásosztályt szeretne hozzárendelni a betöltő felhasználóhoz.

Mivel az oszlopcentrikus táblák általában nem adnak le adatokat egy tömörített oszlopcentrikus szegmensbe, amíg táblaként több mint 1 millió sor van, és minden SQL-készlettábla 60 táblára van felosztva, általában az oszlopcentrikus táblák nem részesülnek a lekérdezés számára, kivéve, ha a tábla több mint 60 millió sorral rendelkezik.  

Egy 60 millió nál kevesebb sorral rendelkező tábla esetén előfordulhat, hogy nincs értelme oszlopcentrikus indexszel rendelkezni.  A használatuk azonban hátrányt sem jelent.  

Ha particionálja az adatait, akkor azt is érdemes figyelembe venni, hogy minden partíciónak 1 millió sorral kell rendelkeznie a fürtözött oszlopcentrikus indexek előnyeinek kihasználása érdekében.  Ha egy tábla 100 partícióval rendelkezik, akkor legalább 6 milliárd sorral kell rendelkeznie a fürtözött oszloptárolók előnyeinek kihasználása érdekében (60 elosztás × 100 partíció × 1 millió sor).  

Ha a tábla nem rendelkezik 6 milliárd sorral, akkor csökkentse a partíciók számát, vagy fontolja meg egy halomtábla használatát.  Azzal is érdemes lehet kísérletezni, hogy jobb teljesítmény érhető-e el egy másodlagos indexekkel rendelkező halomtáblával, mint egy oszlopcentrikussal.

> [!TIP]
> Oszlopcentrikus tábla lekérdezésekor a lekérdezések gyorsabban futnak, ha csak a szükséges oszlopokat választja ki.  

Lásd [még: Táblaindexek](sql-data-warehouse-tables-index.md), [Oszlopcentrikus indexek útmutató](https://msdn.microsoft.com/library/gg492088.aspx)és[Az oszlopcentrikus indexek újraépítése](sql-data-warehouse-tables-index.md#rebuilding-indexes-to-improve-segment-quality).

## <a name="next-steps"></a>További lépések
Ha nem találja, amit keres ebben a cikkben, próbálja meg a "Docs keresése" a bal oldalon ezen az oldalon az Összes Azure Synapse dokumentumok kereséséhez.  

Az [Azure Synapse fórum](https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse) egy olyan hely, ahol kérdéseket tehet közzé más felhasználóknak és az Azure Synapse termékcsoportnak.  Aktívan figyeljük ezt a fórumot, és gondoskodunk róla, hogy tőlünk vagy egy másik felhasználótól választ kapjon a kérdéseire.  

Ha inkább a Stack Overflow-ra vonatkozó kérdéseket szeretne feltenni, van egy [Azure SQL Data Warehouse Stack Overflow fórumunk](https://stackoverflow.com/questions/tagged/azure-sqldw) is.

Az [Azure Synapse visszajelzési](https://feedback.azure.com/forums/307516-sql-data-warehouse) lap segítségével szolgáltatáskéréseket.  Ha kéréseket tesz közzé vagy más kérésekre szavaz, sokat segíthet a szolgáltatások fontossági sorrendjének megállapításában.


