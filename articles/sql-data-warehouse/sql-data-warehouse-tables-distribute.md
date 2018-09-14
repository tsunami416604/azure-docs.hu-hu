---
title: Elosztott táblák tervezési útmutató – Azure SQL Data Warehouse |} A Microsoft Docs
description: Javaslatok az Azure SQL Data Warehouse kivonatoló elosztott és a Ciklikus időszeleteléses elosztott tábla tervezéséhez.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 36db91cd7c4dad3c28c0c110ee837ca6d1284959
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45575382"
---
# <a name="guidance-for-designing-distributed-tables-in-azure-sql-data-warehouse"></a>Útmutató az Azure SQL Data Warehouse elosztott táblák tervezése
Javaslatok az Azure SQL Data Warehouse kivonatoló elosztott és a Ciklikus időszeleteléses elosztott tábla tervezéséhez.

Ez a cikk feltételezi, hogy ismeri az adatok terjesztési és az SQL Data Warehouse mozgását – fogalmak.  További információkért lásd: [Azure SQL Data Warehouse - nagymértékben párhuzamos feldolgozási (MPP) architektúra](massively-parallel-processing-mpp-architecture.md). 

## <a name="what-is-a-distributed-table"></a>Mi az elosztott tábla?
Elosztott tábla egyetlen táblázatban jelennek meg, de a sorok között 60 elosztás ténylegesen tárolódnak. Kivonatoló vagy Ciklikus időszeleteléses algoritmus a sorok oszlanak meg.  

**Kivonatoló elosztott táblák** a ténytáblák nagy lekérdezési teljesítmény javításához, és ez a cikk a fókuszt. **Ciklikus időszeleteléses táblák** hasznosak a betöltési sebesség javítása. Ezek a tervezési döntések jelentős hatást a lekérdezés javítása és a teljesítmény betöltése.

Egy másik tábla tárolási lehetőség, hogy a kis táblák replikálhatja a számítási csomópontokon. További információkért lásd: [tervezési útmutató a replikált táblák](design-guidance-for-replicated-tables.md). A következő három lehetőség közül választhat gyorsan, lásd: elosztott táblák a [táblák áttekintése](sql-data-warehouse-tables-overview.md). 

Táblatervezés részeként megismerheti a lehető legnagyobb mértékben az adatokat, és hogyan van lekéri az adatokat.  Például érdemes lehet ezeket a kérdéseket:

- Milyen méretű legyen a táblázat?   
- Milyen gyakran frissül a tábla?   
- Vannak tény- és dimenziótáblákból az adattárház?   


### <a name="hash-distributed"></a>Kivonatterjesztés
Egy kivonatoló elosztott tábla táblasorokat elosztja a számítási csomópontok egy determinisztikus kivonatoló függvénnyel hozzárendelése minden sor egy [terjesztési](massively-parallel-processing-mpp-architecture.md#distributions). 

![Elosztott tábla](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "elosztott tábla")  

Azonos értékek mindig ugyanazt a terjesztéshez kivonata, mivel a data warehouse beépített képessége az a sor helyek. SQL Data Warehouse használja ezt a tudást adatátvitel során lekérdezéseket, amely javítja a lekérdezési teljesítmény minimalizálása érdekében. 

Kivonatoló elosztott táblák esetén működik megfelelően nagy ténytáblák csillag sémában. Ezek nagyon nagy mennyiségű sort rendelkezik, és továbbra is a magas teljesítmény eléréséhez. Vannak, természetesen, amelyek segítenek az elosztott rendszert úgy tervezték, hogy adja meg a teljesítmény tervezési szempontokat. Egy ilyen szempontok ebben a cikkben leírt helyes elosztási oszlop kiválasztása. 

Fontolja meg egy kivonatot elosztott tábla mikor:

- A tábla lemez mérete 2 GB-nál több.
- A tábla tartalmaz a gyakori beszúrási, frissítési és törlési műveleteket. 

### <a name="round-robin-distributed"></a>Ciklikus időszeleteléses elosztott
Ciklikus időszeleteléses elosztott tábla táblasorokat egyenlően elosztja minden disztribúcióján használhatók. A hozzárendelés disztribúciók sorok egy véletlenszerű. Kivonatoló elosztott táblázatokkal ellentétben azonos értékű sort nem biztos, hogy a ugyanaz terjesztés kell hozzárendelni. 

Ennek eredményeképpen a rendszer néha kell rendszerezéséhez az adatokat, mielőtt feloldja egy lekérdezési adatok mozgását művelet elindításához.  Ez a lépés további lelassíthatja a lekérdezéseket. Például egy Ciklikus időszeleteléses tábla csatlakoztatása általában kell lennie, reshuffling sort, amely teljesítmény találat.

Vegye figyelembe, Ciklikus időszeleteléses elosztását a tábla a következő esetekben használja:

- Ha az alapértelmezett, mivel az egyszerű kiindulási pontként bevezetés
- Ha nincs egyértelmű csatlakozó kulcs
- Ha nincs terjesztése a tábla hash jó jelöltnek számít oszlop
- Ha a tábla nem oszt meg egy közös illesztési kulcsot a más táblák
- Ha az illesztés, mint a többi illesztéseket a kevésbé jelentős
- Ha a tábla-e egy átmeneti előkészítési táblába

Az oktatóanyag [terhelés New York-i taxik adatait az Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) egy példán, amelyen az adatok betöltése egy Ciklikus időszeleteléses előkészítési táblába.


## <a name="choosing-a-distribution-column"></a>Elosztási oszlop kiválasztása
A kivonatoló elosztott tábla rendelkezik, amely a kulcskivonat elosztási oszlop. Például a következő kód táblázatot hoz létre kivonatot elosztott ProductKey elosztási oszlopot.

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

Elosztási oszlop kiválasztása egy fontos tervezési döntés, mivel ebben az oszlopban szereplő értékek határozzák meg, hogyan oszlanak meg a sorokat. A legjobb választás több tényezőtől függ, és általában magában foglalja a kompromisszumot. Azonban ha nem a legjobb oszlop először, használhat [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) újra létre kell hoznia egy másik oszlopot tartalmazó tábla. 

### <a name="choose-a-distribution-column-that-does-not-require-updates"></a>A frissítést nem igénylő elosztási oszlop kiválasztása
Elosztási oszlop nem frissíthető, kivéve, ha törölni a sort, és a frissített értékeket tartalmazó új sort beszúrni. Ezért válasszon ki egy oszlopot, statikus értékekkel. 

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Adatok, amelyek egyenlően osztja el az elosztási oszlop kiválasztása

A legjobb teljesítmény érdekében a disztribúció összes körülbelül ugyanannyi sort kell rendelkeznie. Ha egy vagy több disztribúciók sorok aránytalanul nagy számú rendelkezik, néhány disztribúciók Befejezés a többi előtt a párhuzamos lekérdezés részét. A lekérdezés nem hajtható végre, mindaddig, amíg minden disztribúcióján használhatók végzett feldolgozás, mivel minden egyes lekérdezés csak akkor gyorsaságának a leglassabb terjesztési.

- Adateltérés azt jelenti, hogy az adatok nem oszlik el egyenlően a disztribúciók közötti
- Feldolgozási torzulása azt jelenti, hogy néhány disztribúciók hosszabb időt vesz igénybe, mint a többi párhuzamos lekérdezések futtatásakor. Ez akkor fordulhat elő, amikor az adatok torzítja van.
  
Kiegyenlítése a párhuzamos feldolgozást, válassza ki egy oszlopot, amely:

- **Sok egyedi értéket tartalmaz.** Az oszlop egyes duplikált értékeket veheti fel. Összes sor ugyanazzal az értékkel azonban a ugyanaz terjesztés vannak hozzárendelve. Mivel ebben az esetben 60 elosztás, az oszlop legalább 60 egyedi értékkel kell rendelkeznie.  Egyedi értékek számának általában sokkal nagyobb.
- **Nem tartalmazhat null értékeket, vagy csak néhány NULL értékeket tartalmaz.** Szélsőséges például ha az oszlopban minden érték NULL, az összes sor vannak rendelve a ugyanaz terjesztés. Ennek eredményeképpen a lekérdezés-feldolgozás van torzítja az egy terjesztési, és nem részesül párhuzamos feldolgozást. 
- **A dátum oszlop nem**. A ugyanaz terjesztés az összes adatot ugyanabban az időpontban hajtanak végre. Ha több felhasználó ugyanazon a napon erőforráspanelén, majd csak 1. a 60 elosztás tegye feldolgozási munka. 

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Adatok áthelyezése a lehető legkevesebb elosztási oszlop kiválasztása

Beolvasni a megfelelő lekérdezés eredménye lekérdezések előfordulhat, hogy áthelyezése adatokat egyetlen számítási csomópont között. Adatáthelyezés általában akkor történik, ha a lekérdezések rendelkezik összekapcsolásokhoz és az aggregációhoz elosztott táblák. Amely segít csökkenteni az adatmozgatás elosztási oszlop kiválasztása az egyik a legfontosabb stratégiák optimalizálása az SQL Data Warehouse teljesítményét.

Adatáthelyezés minimalizálása érdekében elosztási oszlop kiválasztása, amely:

- Használatos `JOIN`, `GROUP BY`, `DISTINCT`, `OVER`, és `HAVING` záradékot. Ha a két nagy ténytáblák gyakori összekapcsolásokat, a lekérdezési teljesítmény javítja a mindkét táblázatot az összekapcsolási oszlopokhoz egyik terjesztésekor.  Egy táblázat nem szerepel az illesztések, vegye figyelembe a tábla egy oszlop, amely gyakran része terjesztése a `GROUP BY` záradékban.
- Van *nem* használt `WHERE` záradékot. Ez sikerült szűkítheti a lekérdezés nem futtathatók minden disztribúcióján használhatók. 
- Van *nem* dátum oszlop. WHERE záradék gyakran dátum szerint szűrheti.  Ha ez történik, akkor minden feldolgozás csak néhány disztribúciókon futtathat.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Mi a teendő, ha az oszlopok egyike sem jó elosztási oszlop

Ha az oszlopok egyetlen sem rendelkezik, elegendő elosztási oszlop különböző értékeket, létrehozhat egy olyan új oszlop egy vagy több értéket az összetett. A lekérdezés végrehajtása során adatáthelyezés elkerüléséhez egy illesztési oszlop a lekérdezésekben az összetett elosztási oszlop használjuk.

Miután kivonatoló elosztott tábla tervez, a következő lépés az adatok betöltése a táblába.  További útmutatás a betöltéshez: [betöltés áttekintését](sql-data-warehouse-overview-load.md). 

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Hogyan állapítható meg, hogy az oszlopot a jó választás
Adatok betöltése egy kivonatot elosztott táblába, után ellenőrizze, hogy hogyan egyenlően a sorok között oszlanak meg a 60 elosztás. A sorok száma terjesztési eltérőek lehetnek, akár 10 %-os nélkül észrevehető hatása a teljesítményre. 

### <a name="determine-if-the-table-has-data-skew"></a>Ha a tábla rendelkezik-e a tevékenységdiagramon adatok meghatározása
A használandó érték Adateltérés ellenőrzés gyors módja [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql). A következő SQL-kódot a 60 elosztás mindegyike tárolt tábla sorok számát adja vissza. Az elosztott terhelésű teljesítmény érdekében az elosztott tábla sorainak kell lennie egyenletesen minden disztribúcióján használhatók.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Azonosíthatja, hogy melyik táblákhoz több mint 10 %-os Adateltérés rendelkezik:

1. A nézet dbo.vTableSizes látható létrehozása a [táblák áttekintése](sql-data-warehouse-tables-overview.md#table-size-queries) cikk.  
2. A következő lekérdezés futtatásával:

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

### <a name="check-query-plans-for-data-movement"></a>Adatok áthelyezése a lekérdezésterveket ellenőrzése
Jó elosztási oszlop lehetővé teszi az összekapcsolásokhoz és az aggregációhoz minimális adatáthelyezés rendelkeznie. Ez hatással van a módon illesztések kell írni. A két kivonatoló elosztott tábla kapcsolódás minimális adatáthelyezés lekéréséhez az illesztési oszlopok egyikének kell lennie az oszlopot.  Két kivonatoló elosztott tábla csatlakozzon az azonos típusú adatok elosztási oszlop, ha az illesztés nem igényel adatáthelyezés. Illesztések használható további oszlopok járó adatok áthelyezése nélkül.

Adatáthelyezés elkerüléséhez-hoz való csatlakozáskori:

- A táblák a JOIN részt vevő kell lennie a kivonatterjesztés **egy** részt vesz a illesztési oszlop.
- Az illesztési oszlopok adattípusát a két tábla között meg kell egyeznie.
- Az oszlopok az egyenlő operátor szerinti szűrése kell csatlakoztatni.
- A csatlakozás típusa nem lehet egy `CROSS JOIN`.

Ha a lekérdezések tapasztalt adatáthelyezés megtekintéséhez tekintse meg a lekérdezésterv.  


## <a name="resolve-a-distribution-column-problem"></a>Egy elosztási oszlop probléma megoldása
Nem kell minden esetben az adatok tevékenységdiagramon megoldásához. Adatok terjesztése pár megtalálni az egyensúlyt a közötti minimalizálja az adatok torzulása és az adatok áthelyezését. Nincs mindig minimalizálni Adateltérés és az adatok áthelyezését. Néha előnye, hogy a minimális adatáthelyezés ellensúlyozhatja a tevékenységdiagramon adateltéréssel hatását.

Dönthet arról, hogy ha oldja fel az adatok torzulása egy táblázatban, tisztában kell lennie, a lekérdezések és az adatkötetek lehetséges a számítási. Használhatja a lépések a [lekérdezések figyelése](sql-data-warehouse-manage-monitor.md) cikk torzulása lekérdezések teljesítményére gyakorolt hatásának figyelése. Pontosabban keresse meg, hogy mennyi ideig tart nagy lekérdezések végrehajtásához egyes disztribúciókon.

A meglévő tábla terjesztési oszlopa nem módosítható, mivel a tipikus Adateltérés megoldásához módja újra létre kell hoznia egy másik oszlopot tartalmazó tábla.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Hozza létre újból egy új oszlopot tartalmazó tábla
Ez a példa [CREATE TABLE AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=aps-pdw-2016-au7) újra létre kell hoznia egy másik kivonatoló terjesztési oszlopot tartalmazó táblát.

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

## <a name="next-steps"></a>További lépések

Hozzon létre egy elosztott táblát, használja ezeket az utasításokat egyikét:

- [(Az Azure SQL Data Warehouse) tábla létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [TABLE AS SELECT (Azure SQL Data Warehouse létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)


