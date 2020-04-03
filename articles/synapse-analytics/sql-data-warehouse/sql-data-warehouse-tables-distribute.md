---
title: Elosztott táblák tervezési útmutatója
description: Javaslatok kivonatelosztott és ciklikus multiplexelésű elosztott táblák synapse SQL-készletben tervezése.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 8a93f3ada8e56853b78321bdc7d99a667cee6158
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583506"
---
# <a name="guidance-for-designing-distributed-tables-in-synapse-sql-pool"></a>Útmutató az elosztott táblák synapse SQL-készletben történő tervezéséhez

Javaslatok kivonatelosztott és ciklikus multiplexelésű elosztott táblák synapse SQL-készletek tervezése.

Ez a cikk feltételezi, hogy ismeri az adatok terjesztését és az adatok mozgatási fogalmait a Synapse SQL-készletben.További információ: [Azure Synapse Analytics massively párhuzamos feldolgozási (MPP) architektúra.](massively-parallel-processing-mpp-architecture.md) 

## <a name="what-is-a-distributed-table"></a>Mi az elosztott tábla?

Az elosztott tábla egyetlen táblaként jelenik meg, de a sorok ténylegesen 60 felosztásközött vannak tárolva. A sorok terjesztése kivonatoló vagy ciklikus multiplexelési algoritmussal történik.  

**A kivonatoló elosztott táblák** javítják a lekérdezési teljesítményt nagy ténytáblákon, és a cikk középpontjában állnak. **A ciklikus multiplexelési táblázatok** hasznosak a betöltési sebesség növeléséhez. Ezek a tervezési lehetőségek jelentős hatással vannak a lekérdezési és betöltési teljesítmény javítására.

Egy másik táblatárolási lehetőség egy kis tábla replikálása az összes számítási csomóponton. További információt a [tervútmutató a replikált táblákhoz című témakörben talál.](design-guidance-for-replicated-tables.md) A három lehetőség gyors kiválasztásához olvassa el az Elosztott táblázatok a [táblák áttekintése című témakört.](sql-data-warehouse-tables-overview.md) 

A táblatervezés részeként a lehető legnagyobb mértékben ismerje meg az adatokat és az adatok lekérdezésének módját.Vegyük például a következő kérdéseket:

- Mekkora az asztal?   
- Milyen gyakran frissül a táblázat?   
- Vannak tény- és dimenziótáblák a Szinapszis SQL-készletében?   


### <a name="hash-distributed"></a>Elosztott kivonat

A kivonatoló elosztott tábla a számítási csomópontok között egy determinisztikus kivonatoló függvény használatával osztja el a táblasorokat, hogy minden sort egy [disztribúcióhoz](massively-parallel-processing-mpp-architecture.md#distributions)rendeljen. 

![Elosztott tábla](./media/sql-data-warehouse-tables-distribute/hash-distributed-table.png "Elosztott tábla")  

Mivel az azonos értékek mindig azonos eloszlással rendelkeznek, az adattárház beépített ismeretekkel rendelkezik a sorhelyekről. A Synapse SQL-készletben ezt a tudást a lekérdezések során az adatok mozgásának minimalizálására használják, ami javítja a lekérdezési teljesítményt. 

A kivonatoló elosztott táblák jól működnek a csillagséma nagy ténytábláihoz. Ezek nagyon nagy számú sorok, és még mindig nagy teljesítményt. Vannak, természetesen, néhány tervezési szempontok, amelyek segítenek abban, hogy a teljesítmény az elosztott rendszer célja, hogy. A cikkben ismertetett egyik ilyen szempont a helyes terjesztési oszlop kiválasztása. 

Érdemes lehet kivonatoló táblát használni, ha:

- A lemezen lévő asztal mérete meghaladja a 2 GB-ot.
- A táblázat gyakori beszúrási, frissítési és törlési műveleteket is tartalmazza. 

### <a name="round-robin-distributed"></a>Ciklikus multiplexelt

A ciklikus multiplexelésű elosztott tábla egyenletesen osztja el a táblasorokat az összes disztribúció között. A sorok felosztáshoz rendelése véletlenszerű. A kivonatoló elosztott táblákkal ellentétben az azonos értékű sorok nem garantáltan ugyanahhoz az eloszláshoz lesznek rendelve. 

Ennek eredményeképpen a rendszernek néha meg kell hívnia egy adatmozgatási műveletet, hogy jobban rendszerezhesse az adatokat, mielőtt fel oldhatja a lekérdezést.  Ez az extra lépés lelassíthatja a lekérdezéseket. Például egy ciklikus multiplexelési tábla csatlakozása általában a sorok átkeverését igényli, ami teljesítménytalálat.

Fontolja meg a ciklikus multiplexelés eloszlása a táblához a következő esetekben:

- Amikor az első lépések, mint egy egyszerű kiindulási pont, mivel ez az alapértelmezett
- Ha nincs nyilvánvaló összekötő kulcs
- Ha nincs jó jelölt oszlop a táblázat terjesztésére szolgáló kivonathoz
- Ha a tábla nem oszt meg közös illesztési kulcsot más táblákkal
- Ha az illesztés kevésbé jelentős, mint a lekérdezésben lévő többi illesztés
- Ha a tábla ideiglenes átmeneti tábla

A New [York-i taxiadatok betöltése](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) bemutató példa az adatok ciklikus multiplexelési átmeneti táblába történő betöltésére.


## <a name="choosing-a-distribution-column"></a>Terjesztési oszlop kiválasztása
A kivonatoló elosztott tábla rendelkezik egy terjesztési oszlop, amely a kivonatkulcs. A következő kód például létrehoz egy kivonatoló elosztott táblát, amelynek terjesztési oszlopa a ProductKey.

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

A terjesztési oszlop kiválasztása fontos tervezési döntés, mivel az ebben az oszlopban szereplő értékek határozzák meg a sorok elosztásának módját. A legjobb választás több tényezőtől függ, és általában magában foglalja a kompromisszumokat. Ha azonban első alkalommal nem a legjobb oszlopot választja, a [TÁBLA létrehozása mint kiválasztása (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) segítségével újra létrehozhatja a táblát egy másik terjesztési oszloppal. 

### <a name="choose-a-distribution-column-that-does-not-require-updates"></a>Olyan terjesztési oszlop kiválasztása, amely nem igényel frissítést
Terjesztési oszlop csak akkor frissíthető, ha törli a sort, és nem szúr be egy új sort a frissített értékekkel. Ezért jelöljön ki egy statikus értékeket tartalmazó oszlopot. 

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Elosztási oszlop kiválasztása egyenletesen elosztott adatokkal

A legjobb teljesítmény érdekében az összes disztribúciónak körülbelül azonos számú sorral kell rendelkeznie. Ha egy vagy több felosztás aránytalanul sok sort tartalmaz, egyes felosztások befejezik a párhuzamos lekérdezés egy részét mások előtt. Mivel a lekérdezés nem fejezhető be, amíg az összes disztribúció feldolgozása be nem fejeződik, minden lekérdezés csak olyan gyors, mint a leglassabb eloszlás.

- Az adatdöntés azt jelenti, hogy az adatok nem egyenletesen oszlanak el a disztribúciók között
- A döntés feldolgozása azt jelenti, hogy egyes disztribúciók hosszabb időt vesznek igénybe, mint mások párhuzamos lekérdezések futtatásakor. Ez akkor fordulhat elő, ha az adatok ferdeek.
  
A párhuzamos feldolgozás egyensúlyba hozásához jelöljön ki egy terjesztési oszlopot, amely:

- **Számos egyedi érték.** Az oszlopnak lehetnéhány ismétlődő értéke. Az azonos értékű sorok azonban ugyanahhoz az eloszláshoz vannak rendelve. Mivel 60 disztribúció van, az oszlopnak legalább 60 egyedi értékvel kell rendelkeznie.  Általában az egyedi értékek száma sokkal nagyobb.
- **Nem rendelkezik NULL-okkal, vagy csak néhány NULL-ja van.** Egy szélsőséges példa esetén, ha az oszlop ban szereplő összes érték NULL, az összes sor ugyanahhoz az eloszláshoz van rendelve. Ennek eredményeképpen a lekérdezés feldolgozása egyetlen disztribúcióra van ferdítve, és nem élvezi a párhuzamos feldolgozás előnyeit. 
- **Nem dátumoszlop.** Az ugyanarra a dátumra vonatkozó összes adat ugyanabban az eloszlásban landol. Ha több felhasználó is ugyanazon a napon szűr, akkor a 60 disztribúcióból csak 1 végezze el az összes feldolgozási munkát. 

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Válassza ki az adatmozgást minimalizáló terjesztési oszlopot

A megfelelő lekérdezéseredmény-lekérdezések lekérdezése adatokat helyezhet át az egyik számítási csomópontról a másikra. Az adatáthelyezés gyakran akkor fordul elő, ha a lekérdezések összekapcsolódnak és összesítik az elosztott táblákat. A szinapszis SQL-készlet teljesítményének optimalizálására vonatkozó egyik legfontosabb stratégia az adatáthelyezés minimalizálását segítő terjesztési oszlop kiválasztása.

Az adatok mozgásának minimalizálása érdekében jelöljön ki egy terjesztési oszlopot, amely:

- A `JOIN`, `GROUP BY`, `DISTINCT` `OVER`, `HAVING` , és záradékok. Ha két nagy ténytábla gyakran csatlakozik, a lekérdezési teljesítmény javul, ha mindkét táblát az egyik illesztőoszlopon osztja.  Ha egy táblát nem használnak illesztésekben, érdemes lehet a táblát a `GROUP BY` záradékban gyakran szereplő oszlopon terjeszteni.
- *Nem* használatos `WHERE` a záradékok. Ez szűkítheti a lekérdezést, hogy ne fusson az összes disztribúción. 
- Ez *nem* dátumoszlop. A WHERE záradékok gyakran dátum szerint szűrnek.  Ha ez történik, az összes feldolgozás csak néhány disztribúción futtatható.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Mi a teendő, ha egyik oszlop sem jó terjesztési oszlop?

Ha egyik oszlop sem rendelkezik elegendő különböző értékkel egy terjesztési oszlophoz, létrehozhat egy új oszlopot egy vagy több érték összesítéseként. Az adatok adatáthelyezésének elkerülése érdekében a lekérdezések végrehajtása során használja az összetett terjesztési oszlopot illesztőoszlopként a lekérdezésekben.

Miután megtervezte a kivonatoló elosztott táblát, a következő lépés az adatok betöltése a táblába.  A terhelési útmutatóról a [Betöltés – áttekintés című témakörben olvashat.](design-elt-data-loading.md) 

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Hogyan állapíthatja meg, hogy a terjesztési oszlop jó választás-e?
Miután az adatok betöltése egy kivonatoló elosztott táblába, ellenőrizze, hogy a sorok egyenletesen oszlanak el a 60 disztribúciók között. Az eloszlásonkénti sorok akár 10%-os a teljesítményre gyakorolt észrevehető hatás nélkül változhatnak. 

### <a name="determine-if-the-table-has-data-skew"></a>Annak megállapítása, hogy a tábla rendelkezik-e adatdöntési
Az adatdöntés ellenőrzésének gyors módja a [DBCC PDW_SHOWSPACEUSED](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql)használata. A következő SQL-kód a 60 disztribúcióban tárolt táblasorok számát adja vissza. A kiegyensúlyozott teljesítmény érdekében az elosztott tábla sorait egyenletesen kell elosztani az összes disztribúció között.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Annak azonosítása, hogy mely táblák rendelkeznek 10%-nál több adatdöntési eltérésre:

1. Hozza létre a dbo.vTableSizes nézetet, amely a [Táblák áttekintése](sql-data-warehouse-tables-overview.md#table-size-queries) című cikkben látható.  
2. Futtassa az alábbi lekérdezést:

```sql
select *
from dbo.vTableSizes
where two_part_name in
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having (max(row_count * 1.000) - min(row_count * 1.000))/max(row_count * 1.000) >= .10
    )
order by two_part_name, row_count
;
```

### <a name="check-query-plans-for-data-movement"></a>Adatmozgatási lekérdezési tervek ellenőrzése
Egy jó terjesztési oszlop lehetővé teszi, hogy az illesztések és az összesítések minimális adatmozgást. Ez befolyásolja az illesztések írásmódját. Ahhoz, hogy két kivonatoló elosztott táblához minimális adatmozgást kapjon, az illesztési oszlopok egyikének a terjesztési oszlopnak kell lennie.  Ha két kivonatoló elosztott tábla csatlakozik egy azonos típusú terjesztési oszlophoz, az illesztés nem igényel adatáthelyezést. Az illesztések további oszlopokat is használhatnak adatáthelyezés nélkül.

Az adatok egyesítése soráni mozgásának elkerülése:

- Az illesztésben részt vevő tábláknak kivonatot kell osztaniuk az illesztésben részt vevő oszlopok **egyikén.**
- Az illesztési oszlopok adattípusainak mindkét tábla között egyeznek.
- Az oszlopokat egyenlő operátorral kell összeilleszteni.
- Az illesztéstípusa nem `CROSS JOIN`lehet .

Ha meg szeretné tekinteni, hogy a lekérdezések adatáthelyezést tapasztalnak-e, tekintse meg a lekérdezési tervet.  


## <a name="resolve-a-distribution-column-problem"></a>Terjesztési oszlopmal kapcsolatos probléma megoldása
Nem szükséges minden adatdöntési esetet feloldani. Az adatok terjesztése az adatok döntésének minimalizálása és az adatok mozgása közötti megfelelő egyensúly megtalálásának kérdése. Nem mindig lehetséges az adatdöntés és az adatmozgás minimalizálása. Néha a minimális adatmozgás előnye ellensúlyozhatja az adatok döntésének hatását.

Annak eldöntéséhez, hogy feloldja-e az adatdöntés feloldását egy táblában, a lehető legnagyobb mértékben meg kell értenie a számítási feladatokban lévő adatköteteket és lekérdezéseket. A [Lekérdezés figyelése](sql-data-warehouse-manage-monitor.md) című cikkben ismertetett lépésekkel figyelheti a döntés nek a lekérdezés teljesítményére gyakorolt hatását. Pontosabban keresse meg, hogy mennyi ideig tart a nagy lekérdezések befejezéséhez az egyes disztribúciók.

Mivel egy meglévő tábla terjesztési oszlopa nem módosítható, az adatdöntés feloldásának tipikus módja a tábla újbóli létrehozása egy másik terjesztési oszloppal.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>A tábla újbóli létrehozása új terjesztési oszloppal
Ez a példa [a CREATE TABLE AS SELECT segítségével](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=aps-pdw-2016-au7) hoz létre újra egy másik kivonatterjesztési oszlopot tartalmazó táblát.

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

Elosztott tábla létrehozásához használja az alábbi utasítások egyikét:

- [TÁBLA LÉTREHOZÁSA (Szinapszis SQL-készlet)](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [TÁBLA LÉTREHOZÁSA VÁLASZTÓKÉNT (Szinapszis SQL-készlet)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)


