---
title: "Az SQL Data Warehouse táblák terjesztése |} Microsoft Docs"
description: "Első lépések az Azure SQL Data Warehouse táblák terjesztése."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.assetid: 5ed4337f-7262-4ef6-8fd6-1809ce9634fc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 12/06/2017
ms.author: barbkess
ms.openlocfilehash: 82e17e575cdb227af2fabf94f01e94df22994aac
ms.sourcegitcommit: 821b6306aab244d2feacbd722f60d99881e9d2a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/18/2017
---
# <a name="distributing-tables-in-sql-data-warehouse"></a>Az SQL Data Warehouse táblák terjesztése
> [!div class="op_single_selector"]
> * [– Áttekintés][Overview]
> * [Adattípusok][Data Types]
> * [Terjesztése][Distribute]
> * [Index][Index]
> * [Partíció][Partition]
> * [Statisztika][Statistics]
> * [Ideiglenes][Temporary]
>
>

Az SQL Data Warehouse egy nagymértékben párhuzamos feldolgozási (MPP) elosztott adatbázisrendszerre épül.  Az adatok és a feldolgozási képesség több csomópont közötti elosztásával az SQL Data Warehouse olyan rendkívüli méretezhetőséget kínál, amely messze meghaladja bármilyen különálló rendszer lehetőségeit.  A számítógépek adatait az SQL Data warehouse terjesztése egyike a legfontosabb tényezők optimális teljesítményének eléréséhez.   Az optimális teljesítmény kulcs van kis méretre adatmozgás, vagy pedig a kulcsot, minimalizálja az adatátvitelt jelölik a megfelelő terjesztési stratégia jelöl.

## <a name="understanding-data-movement"></a>Adatátvitel ismertetése
Minden tábla adatait az MPP rendszer több alapul szolgáló adatbázisok között oszlik meg.  A legtöbb optimalizált lekérdezések az MPP rendszeren is egyszerűen átadható az egyes elosztott adatbázisok között a más adatbázisok beavatkozás nélküli végrehajtásához.  Például tételezzük fel az értékesítési adatait, amely tartalmazza a két tábla, értékesítéssel és az ügyfelek olyan adatbázist.  Ha egy lekérdezést, amely a felhasználói tábla az értékesítési táblában csatlakoztatni kell, és akkor nullával az értékesítési és a felhasználói táblák mentése ügyfélszámot, mindegyik ügyfél egy külön adatbázisban, és csatlakoztassa értékesítés és az ügyfél lekérdezések megoldhatók belül nincsenek saját ismeretei az egyéb adatbázisok az adatbázishoz.  Ezzel szemben, ha az értékesítési adatok osztva számát és az ügyféladatok ügyfél száma szerint, majd a megadott adatbázis nem lesz a megfelelő adatok minden ügyfél esetében és így ha az értékesítési adatok csatlakoztatása az ügyféladatokat, kellene t beolvasása Ezután adatokat az adatbázisból minden ügyfél esetében.  A második példában adatmozgás kell, hogy a két tábla társíthatók mennek végbe annak a felhasználói adatok áthelyezése az értékesítési adatokat.  

Adatátvitel nem mindig egy rossz dolgot tegyenek, egyes esetekben szükséges a lekérdezés megoldására.  De ha ezt a kiegészítő lépést el kell kerülni, természetesen a lekérdezés fog futni gyorsabb.  Adatátvitel leggyakrabban akkor keletkezik, ha a táblák tartományhoz csatlakoztatott vagy Összesítés hajtja végre.  Gyakran kell tennie, mindkettőt, így bár bizonyára képes optimalizálni a illesztés, például egy esetben továbbra is kell adatátvitelt jelölik a forgatókönyvben például egy összesítő megoldása érdekében.  A trükk rendszer tudja, amelyek értéke kevesebb munka.  A legtöbb esetben nagy ténytáblák általában illesztett oszlopon terjesztése a csökkentése érdekében a legtöbb adatátvitelt jelölik a leghatékonyabb mód.  Az illesztési oszlopok adatok terjesztése a mint terjesztése összesítést részt vevő oszlopokat adatok adatátvitel csökkentése érdekében sokkal közös módszer.

## <a name="select-distribution-method"></a>Elosztási módszer kiválasztása
A háttérben SQL Data Warehouse felosztja az adatok 60 adatbázisok.  Minden egyes adatbázis nevezzük egy **terjesztési**.  Adatok minden tábla tölti be, amikor az SQL Data Warehouse ki tudja, hogyan kell az adatok osztja szét ezek 60 terjesztéseket.  

A telepítési módszer a tábla szintjén van meghatározva, és jelenleg két választási lehetőség:

1. **Ciklikus multiplexelés** egyenletesen, de véletlenszerűen elosztják adatokat.
2. **Elosztott kivonatoló** osztja el a kivonatolás csak egy oszlop értékeinek alapján

Alapértelmezés szerint nem határoznak meg a telepítési módszert, ha a tábla eloszlik a használatával a **ciklikus multiplexelés** elosztási módszer.  Azonban ha már kifinomultabb a megvalósításában, érdemes érdemes **elosztott kivonatoló** minimalizálása érdekében az adatmozgás, ami viszont optimalizálja táblák lekérdezési teljesítmény.

### <a name="round-robin-tables"></a>Ciklikus multiplexelés táblák
A ciklikus multiplexelés metódussal adatok elosztása túlságosan azok hogyan úgy tűnik, hogy.  Az adatok betöltése, mert minden egyes sorára egyszerűen jut hozzá a következő terjesztési.  Ez a módszer az adatforgalom elosztása fog véletlenszerűen mindig az adatok nagyon egyenletes elosztása a disztribúciók összes.  Ez azt jelenti, hogy nincs kész a ciklikus multiplexelés során, amely az adatokat helyezi rendezés.  Ciklikus multiplexelés terjesztési ezért nevezik véletlenszerű kivonatát.  Ciklikus multiplexelés elosztott tábla esetén nem szükséges adatokat.  Emiatt ciklikus multiplexelés gyakran táblázatokkal jó betöltése célokat.

Alapértelmezés szerint nincs elosztási módszer választása esetén a ciklikus multiplexelés elosztási módszer használható.  Azonban amíg ciklikus multiplexelés táblák könnyen használható, mert az adatok véletlenszerűen a rendszer, az azt jelenti, hogy a rendszer nem garantálható, hogy mely terjesztési pontjain. mindegyik sor van.  Emiatt a rendszer néha kell ahhoz, hogy feloldja egy lekérdezést, rendszerezéséhez az adatok mozgása Adatműveletek meghívni.  Ezt a kiegészítő lépést lelassíthatja a lekérdezéseket.

Érdemes lehet ciklikus multiplexelés terjesztési az a tábla a következő esetekben:

* Ha egyszerű kiindulási pontként első lépések
* Ha nincs egyértelmű csatlakozó kulcs
* Ha nem terjeszti a tábla a kivonat jó jelölt oszlop
* Ha a tábla nem osztja meg egy közös illesztési kulcs más táblák
* Ha az illesztés kevésbé fontos, mint más illesztéseket a
* Ha a tábla egy ideiglenes átmeneti tárolási tábla

A példák is létrehoz egy ciklikus multiplexelés táblát:

```SQL
-- Round Robin created by default
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
;

-- Explicitly Created Round Robin Table
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

> [!NOTE]
> Ciklikus multiplexelés pedig folyamatban explicit a DDL-t az alapértelmezett táblatípusban tekinthető az ajánlott eljárás, hogy a table elrendezést céljaira egyértelmű, hogy mások számára.
>
>

### <a name="hash-distributed-tables"></a>Elosztott táblák kivonatoló
Használja a **elosztott kivonatoló** terjeszteni a táblák algoritmus javíthatja a teljesítményt a sok forgatókönyvben csökkenti az adatátvitelt jelölik a lekérdezések során.  A kivonattáblákkal elosztott olyan táblák, amelyeknek a használatával a kivonatolási algoritmust választja, amely egyetlen oszlop elosztott adatbázis között.  A terjesztési oszlop, mi meghatározza, hogy az adatok felosztásának módját az elosztott adatbázisok között.  A kivonatoló függvényt sorok hozzárendelése terjesztéseket terjesztési oszlop használja.  A kivonatoló algoritmus és a létrejövő terjesztési nem determinisztikus.  Ez az ugyanannál az adattípusnál ugyanazt az értéket az azonos eloszlás mindenhol lesz.    

Ez a példa létrehoz egy táblát, a terjesztés azonosítója:

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

## <a name="select-distribution-column"></a>Válassza ki a terjesztési oszlop
Ha úgy dönt, hogy **kivonatoló terjesztése** tábla, akkor egyetlen terjesztési oszlop.  Jelöljön ki egy terjesztési oszlopot, amikor nincsenek három főbb tényezőket kell figyelembe venni.  

Válassza ki a csak egy oszlop, amelyek:

1. Nem frissíthető
2. Adatok, egyenletes elosztása elkerülhető az a döntés adatok
3. Adatátvitel minimalizálása érdekében

### <a name="select-distribution-column-which-will-not-be-updated"></a>Válassza ki a terjesztési oszlop, amely nem frissíthető
Terjesztési oszlopok nem frissíthető, ezért, válasszon statikus értékeket tartalmazó oszlop.  Egy oszlop kell frissíteni, esetén általában nem a megfelelő terjesztési jelölt.  Ha egy olyan esetben, ha frissítenie kell egy terjesztési oszlop, ez végezhető először a sor törlése, és ezután az új sorok beszúrásához.

### <a name="select-distribution-column-which-will-distribute-data-evenly"></a>Válassza ki a terjesztési oszlop, amely egyenletes elosztása adatok
Elosztott rendszer csak olyan gyorsan, mint a leglassabb kiosztás hajt végre, mert fontos osztja a munkahelyi egyenletesen szét a disztribúciók kiegyensúlyozott végrehajtási eléréséhez a rendszer.  A munkahelyi oszlik elosztott rendszerek módja alapján ahol él, az adatokat az egyes terjesztési.  Ennek köszönhetően nagyon fontos, hogy ki a megfelelő terjesztési oszlopot terjesztése az adatokat, hogy minden terjesztési egyenlő munkát, és a munka részét befejezéséhez azonos időt vesz igénybe.  Munkahelyi jól oszlik meg a rendszer, amikor az adatok között a megfelelő kiadásának kiegyensúlyozott.  Adatok nem egyenletesen eloszlik, amikor ez nevezzük **adatok döntés**.  

Egyenletesen megoszthatjuk az adatokat, és döntés adatok elkerüléséhez, vegye figyelembe a következőket a terjesztési oszlop kiválasztása:

1. Válasszon ki egy oszlopot, amely számos különböző értékeket tartalmazza.
2. Ne elosztása néhány különböző értékeket tartalmazó oszlopok adatokat.
3. Kerülje a NULL értékek nagy gyakorisággal oszlopokon adatok terjesztése.
4. Kerülje a dátumoszlopot adatok terjesztése.

Mivel egyes értékek kivonatolt 1-60 azokat a terjesztéseket, még akkor is, terjesztési elérése érdekében érdemes válasszon ki egy oszlopot, amely magas egyedi, és több mint 60 egyedi értékeket tartalmaz.  Mutatja be, fontolja meg egy olyan esetben, ahol egy oszlop csak rendelkezik 40 egyedi értékeket.  Ezt az oszlopot a terjesztési kulcs volt kiválasztva, ha, hogy a tábla adatai volna mindkét 40 azokat a terjesztéseket, legfeljebb 20 terjesztéseket nincsenek adatok, és a feldolgozás elvégzéséhez nem hagyja.  Ezzel szemben a más 40 terjesztéseket kellene a teendő az adatok egyenletesen lett terjednek több mint 60 azokat a terjesztéseket, amelyek több munkát.  Ebben a forgatókönyvben az adatok eltérésére egy példája.

MPP rendszerben minden egyes lekérdezés lépés megvárja-e a minden terjesztéseket a megosztást a munkája elvégzéséhez.  Ha egy terjesztési végez műveletet több mint a többi, majd az egyéb azokat a terjesztéseket, az erőforrás vannak lényegében kihasználatlan csak várakozás a foglalt terjesztési.  Ha munkahelyi nem egyenlően elosztva összes azokat a terjesztéseket, ez nevezzük **eltérésére feldolgozási**.  Feldolgozási döntés lekérdezések lassabban fut, mint ha a munkaterhelés is lehet egyenlően elosztva a terjesztési miatt.  Döntés adatok feldolgozási eltérésére irányítja.

Ne, a null értékek összes megnyílik a azonos terjesztési magas nullázható oszlopában terjesztése. A dátum oszlop terjesztése feldolgozási döntés is eredményezheti, mivel egy adott dátumhoz tartozó minden adat azonos terjesztési megnyílik. Ha több felhasználó állnak végrehajtás alatt lekérdezések összes szűrés ugyanazon a napon, akkor csak az 1 a 60 azokat a terjesztéseket mindaddig végre, az összes a munkát a megadott dátum óta csak akkor egy terjesztési. Ebben a forgatókönyvben a lekérdezések valószínűleg lassabb, mint ha az adatok lettek egyaránt eloszlatva összes a disztribúciók 60 alkalommal futtathatja.

Nem érdemes deduplikációra oszlop létezik, majd vegye figyelembe a telepítési módszer ciklikus multiplexelés az.

### <a name="select-distribution-column-which-will-minimize-data-movement"></a>Válassza ki a terjesztési oszlop, ami minimálisra csökkentheti adatátvitel
Adatátvitel minimalizálja a megfelelő terjesztési oszlop kiválasztásával egyike a legfontosabb stratégiák az SQL Data Warehouse teljesítményének optimalizálásához.  Adatátvitel leggyakrabban akkor keletkezik, ha a táblák tartományhoz csatlakoztatott vagy Összesítés hajtja végre.  Használt az oszlopok `JOIN`, `GROUP BY`, `DISTINCT`, `OVER` és `HAVING` készítése az összes záradékok **jó** terjesztési jelöltek kivonat.

Másrészről, oszlopai a `WHERE` záradék do **nem** gondoskodjon megfelelő kivonatoló oszlop jelöltek mert azok korlátozza, mely azokat a terjesztéseket részt vesz a lekérdezés feldolgozását, amely a döntés.  Egy oszlop, amely előfordulhat, hogy a terjesztéséhez tempting, de gyakran okozhat a feldolgozási eltérésére jó példa a dátum oszlop.

Általánosan fogalmazva Ha két nagy ténytáblák gyakran érintett szerepel, érheti el a legtöbb teljesítmény mindkét táblát az illesztési oszlopok egyik elosztásával.  Ha egy táblázatot, amely soha nem csatlakozik másik nagy ténytábla, majd keresse meg az oszlopokat, amelyek gyakran az az `GROUP BY` záradékban.

Van néhány főbb feltételek, amelyeknek teljesülniük kell adatátvitel során illesztés elkerülése érdekében:

1. A illesztésben részt vevő táblák kell lennie a terjesztés kivonatoló **egy** részt vesz a illesztési oszlop.
2. Az illesztési oszlopok adattípusai meg kell egyeznie a két tábla között.
3. Az oszlopok az egyenlő operátor szerinti szűrése tartományhoz kell csatlakoznia.
4. Az illesztési típus nem lehet egy `CROSS JOIN`.

## <a name="troubleshooting-data-skew"></a>Hibaelhárítási adatokat eltérésére
Amikor a táblabeli adatok a kivonat terjesztési metódussal esély van, hogy néhány terjesztési aránytalanul további adatai, mint a többire válik egyenetlenné. Túl sok adatot döntés hatással lehet a lekérdezési teljesítmény, mivel a végeredményt egy elosztott lekérdezést kell várnia, hogy a leghosszabban futó terjesztési befejezéséhez. Attól függően, hogy az adatok döntés fokának szükség lehet foglalkozik.

### <a name="identifying-skew"></a>Döntés azonosítása
Egy adjon meg egy táblát, mert válik egyenetlenné legegyszerűbb használandó `DBCC PDW_SHOWSPACEUSED`.  Ez az egy igen gyors és egyszerű úgy tudja megtekinteni az egyes az adatbázis 60 terjesztéseket tárolt tábla sorok száma.  Ne feledje, hogy a legkiegyensúlyozottabb teljesítményt, az elosztott tábla sorait kell egyenletesen a megfelelő kiadásának között.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Azonban ha lekérdezést hajt végre az Azure SQL Data Warehouse dinamikus felügyeleti nézetekkel (DMV) végezhet részletesebb elemzését.  Indíthatók el, a nézet létrehozása [dbo.vTableSizes] [ dbo.vTableSizes] megtekintéséhez használja az SQL [tábla áttekintése] [ Overview] cikk.  A nézet létrehozása után a lekérdezés futtatásával azonosíthatja a táblák több mint 10 % adatok döntés rendelkezik.

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
    )
order by two_part_name, row_count
;
```

### <a name="resolving-data-skew"></a>Adatok eltérésére feloldása
Nem minden eltérésére van elegendő a javítás szavatolja.  Bizonyos esetekben egy tábla bizonyos lekérdezések teljesítményét is járó eltérésére adatok kárt.  Döntse el, hogy ha oldja fel az adatok eltérésére egy tábla tisztában kell lennie az adatok mennyiségéről és lekérdezések lehetőség szerint a terhelést a.   Nézze meg a döntés hatását egyike a lépései a [lekérdezés figyelési] [ Query Monitoring] eltérésére lekérdezési teljesítményre gyakorolt hatását és a kifejezetten a hatás be, hogyan figyelheti hosszú cikket lekérdezi a befejezéséhez hajtsa végre a megfelelő az egyes terjesztéseket.

Adatok terjesztése, hogy a rendszer az egyensúlyt a minimalizálja a adatok döntés és minimalizálja az adatmozgás között. Ezek is ellentétes célok, és egyes esetekben érdemes adatmozgás csökkentése érdekében megőrizni az adatokat döntés. Például ha a terjesztési oszlop gyakran összekapcsolásokhoz és az aggregációhoz megosztott oszlopa, meg fog kell minimalizálása adatátvitelt jelölik a. A minimális adatmozgás előnyös lehet, hogy járó döntés adatok hatását.

A tipikus oldható fel az adatok döntés módja újra létre kell hoznia egy másik terjesztési oszlop a tábla. Mivel nincs mód a meglévő tábla, egy tábla terjesztési módosítsa úgy a terjesztési oszlop módosítása, hogy egy [CTAS] szögletes hozza létre újra.  Az alábbiakban a két példa bemutatja, hogyan oldhatók fel az adatok eltérésére:

### <a name="example-1-re-create-the-table-with-a-new-distribution-column"></a>1. példa: Hozza létre egy új terjesztési oszlopot tartalmazó tábla
A példa [CTAS] [-] újra létre kell hoznia egy másik kivonatoló terjesztési oszlopot tartalmazó tábla.

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### <a name="example-2-re-create-the-table-using-round-robin-distribution"></a>2. példa: Hozza létre a tábla ciklikus multiplexelés eloszlás használatával
A példa [CTAS] [-] újra létre kell hoznia ciklikus multiplexelés helyett egy kivonatoló terjesztési tartalmazó tábla. Ez a változás a művelet létrehoz még adatok terjesztési megnövekedett adatmozgás használ.

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN]
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## <a name="next-steps"></a>Következő lépések
Táblatervezés kapcsolatos további tudnivalókért tekintse meg a [elosztás][Distribute], [Index][Index], [partíció] [ Partition], [Adattípusok][Data Types], [statisztika] [ Statistics] és [ideiglenes táblák] [ Temporary] cikkeket.

Ajánlott eljárások áttekintését lásd: [SQL Data Warehouse gyakorlati tanácsok][SQL Data Warehouse Best Practices].

<!--Image references-->

<!--Article references-->
[Overview]: ./sql-data-warehouse-tables-overview.md
[Data Types]: ./sql-data-warehouse-tables-data-types.md
[Distribute]: ./sql-data-warehouse-tables-distribute.md
[Index]: ./sql-data-warehouse-tables-index.md
[Partition]: ./sql-data-warehouse-tables-partition.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[Temporary]: ./sql-data-warehouse-tables-temporary.md
[SQL Data Warehouse Best Practices]: ./sql-data-warehouse-best-practices.md
[Query Monitoring]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#table-size-queries

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED()]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->
