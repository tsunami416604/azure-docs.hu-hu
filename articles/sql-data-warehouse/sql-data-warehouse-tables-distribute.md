---
title: "Tervezési útmutatást elosztott táblák - Azure SQL Data Warehouse |} Microsoft Docs"
description: "Javaslatok az Azure SQL Data Warehouse kivonatoló elosztott és a ciklikus multiplexelési táblák tervezése."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jenniehubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: tables
ms.date: 01/18/2018
ms.author: barbkess
ms.openlocfilehash: 3c86b89da796223336e3a0d9dd809ae140d6911e
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/01/2018
---
# <a name="guidance-for-designing-distributed-tables-in-azure-sql-data-warehouse"></a>Útmutató az Azure SQL Data Warehouse elosztott táblák tervezése

Ez a cikk javaslatok az Azure SQL Data Warehouse elosztott táblák tervezése biztosít. Kivonatoló elosztott táblák javíthatja a nagy ténytáblák a lekérdezések teljesítményét, és ez a cikk a fókuszt. Ciklikus multiplexelés táblák hasznosak betöltése sebességének javítása. Tervezési döntések magyarázatait jelentős hatást gyakorolnak lekérdezés javítása és a teljesítmény betöltésekor.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy az adatok terjesztési és az SQL Data Warehouse adatátviteli – fogalmak megismerése.  További információkért lásd: a [architektúra](massively-parallel-processing-mpp-architecture.md) cikk. 

Táblatervezés részeként megérteni a lehető legnagyobb mértékben az adatokat, és hogyan lekérik az adatokat.  Tegyük fel ezeket a kérdéseket:

- Mekkora a tábla?   
- Milyen gyakran frissülnek, a tábla?   
- Van tény-és dimenziótáblák az adatraktárban?   

## <a name="what-is-a-distributed-table"></a>Mi az az elosztott tábla?
Elosztott táblázat jelenik meg egy táblát, de a sorok ténylegesen tárolt 60 terjesztéseket. A sorok eszközzel együtt a kivonatoló vagy a ciklikus multiplexelési algoritmus. 

Egy másik tábla tárolási lehetőség egy kis tábla a számítási csomópontok közötti replikáció. További információkért lásd: [kialakítási útmutató a replikált táblák](design-guidance-for-replicated-tables.md). Gyorsan a következő három lehetőség közül választhat, tekintse meg az elosztott táblák a [táblák áttekintése](sql-data-warehouse-tables-overview.md). 


### <a name="hash-distributed"></a>Elosztott kivonata
A kivonat-elosztott tábla elosztja táblázat sorait a számítási csomópontok determinisztikus kivonatoló függvény használatával minden egyes sorára hozzárendelése egy [terjesztési](massively-parallel-processing-mpp-architecture.md#distributions). 

![Elosztott tábla](media/sql-data-warehouse-distributed-data/hash-distributed-table.png "elosztott tábla")  

Mindig azonos értékek kivonat-ugyanazt a terjesztéshez, mivel az adatraktár rendelkezik a sor helyek beépített ismerete. Az SQL Data Warehouse adatátvitel során a lekérdezések, amely azáltal növeli a lekérdezési teljesítmény minimalizálása érdekében a Tudásbázis használja. 

Kivonatoló elosztott táblák is nagy ténytábláinak csillagséma alkalmasak. Ezek nagyon nagy mennyiségű sort rendelkezik, és továbbra is a magas teljesítmény érdekében. Nincsenek, természetesen, amelyek segítenek a teljesítmény, az elosztott rendszer szolgál a tervezési szempontokat. Egy jó terjesztési oszlop kiválasztása egy ilyen fontos szempont, ebben a cikkben ismertetett rendszer. 

Érdemes lehet a kivonat-elosztott táblából:

- A tábla a lemezen mérete 2 GB-ot.
- A tábla tartalmaz a gyakori beszúrási, frissítési és törlési műveletek. 

### <a name="round-robin-distributed"></a>Ciklikus multiplexelés elosztott
Ciklikus multiplexelés elosztott tábla elosztása táblázat sorait egyenletesen valamennyi felosztást. A hozzárendelés terjesztéseket sorok véletlenszerű. Kivonatoló elosztott táblák eltérően egyenlő értéket tartalmazó sorok ismételt nem garantált hozzá kell rendelni a azonos terjesztési. 

Emiatt a rendszer néha kell ahhoz, hogy feloldja egy lekérdezést, rendszerezéséhez az adatok mozgása Adatműveletek meghívni.  Ezt a kiegészítő lépést lelassíthatja a lekérdezéseket. Például általában csatlakoztatása egy ciklikus multiplexelés tábla szükséges reshuffling a sorok, amely teljesítmény találat.

Érdemes lehet a ciklikus multiplexelés az a tábla a következő esetekben:

- Ha az első lépések egyszerű kiindulási pontként, mert ez az alapértelmezett
- Ha nincs egyértelmű csatlakozó kulcs
- Ha nem terjeszti a tábla a kivonat jó jelölt oszlop
- Ha a tábla nem osztja meg egy közös illesztési kulcs más táblák
- Ha az illesztés kevésbé fontos, mint más illesztéseket a
- Ha a tábla egy ideiglenes átmeneti tárolási tábla

Az oktatóanyag [adatok betöltése az Azure Storage-blobból](load-data-from-azure-blob-storage-using-polybase.md#load-the-data-into-your-data-warehouse) egy példán adatok betöltését a ciklikus multiplexelési átmeneti tárolási tábla.


## <a name="choosing-a-distribution-column"></a>Egy olyan terjesztési oszlop kiválasztása
A kivonatoló elosztott tábla terjesztési oszlop, amely a kivonatkulcs rendelkezik. Például az alábbi kód kivonatoló elosztott táblázat létrehozása ProductKey terjesztési oszlopként.

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

Egy fontos tervezési döntés a terjesztési oszlop kiválasztása, mivel ebben az oszlopban szereplő értékek határozzák meg, hogyan jutnak el a sorokat. A legjobb választás számos tényezőtől függ, és általában a mellékhatásokkal jár. Azonban ha nem a legjobb oszlop először, használhatja [létrehozása TABLE AS kiválasztása (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) újra létre kell hoznia egy másik terjesztési oszlopot tartalmazó tábla. 

### <a name="choose-a-distribution-column-that-does-not-require-updates"></a>Válassza ki a terjesztési oszlopot, amely nem kell frissíteni
Egy olyan terjesztési oszlop nem frissíthető, kivéve, ha a sor törlése és a frissített értékekkel új sor beszúrására. Ezért válassza ki a statikus értékeket tartalmazó oszlop. 

### <a name="choose-a-distribution-column-with-data-that-distributes-evenly"></a>Válasszon egy terjesztési oszlopot, amely egyenlően osztja el adatok

A legjobb teljesítmény érdekében a disztribúciók mindegyikét megközelítőleg azonos számú sort kell rendelkeznie. Ha egy vagy több felosztás aránytalanul nagy számú sort, néhány terjesztéseket Befejezés a többi előtt a párhuzamos lekérdezések része. A lekérdezés nem hajtható végre, amíg minden terjesztéseket végzett feldolgozás, mivel minden egyes lekérdezés csak akkor lassabbak, mint a leglassabb terjesztési.

- Adatok döntés azt jelenti, hogy az adatok nem között van elosztva egyenletesen a disztribúciók
- Feldolgozási döntés azt jelenti, hogy néhány terjesztési tovább tart, mint a többire párhuzamos lekérdezések futtatásakor. Ez akkor fordulhat elő, amikor az adatok válik egyenetlenné.
  
A párhuzamos feldolgozást végző egyensúlyba, válasszon ki egy terjesztési oszlopot, amely:

- **Sok egyedi értéket tartalmaz.** Az oszlop egyes ismétlődő értékeket veheti fel. Azonban minden sor ugyanarra az értékre ugyanazon eloszlását vannak hozzárendelve. Mivel nincsenek 60 azokat a terjesztéseket, az oszlop legalább 60 egyedi értékkel kell rendelkeznie.  Az egyedi értékek számának általában sokkal nagyobb.
- **Nem rendelkezik a null értékeket, vagy csak néhány NULL értékeket tartalmaz.** Szélsőséges példát Ha az oszlop minden értékének NULL, a sorok vannak hozzárendelve a azonos terjesztési. Ennek eredményeképpen a lekérdezés feldolgozása válik egyenetlenné egy terjesztési, és nem tudják igénybe venni párhuzamos feldolgozást. 
- **A dátum oszlop nincs**. Minden adat ugyanarra az időpontra fájljai a azonos eloszlását. Ha több felhasználó az összes szűrő ugyanazon a napon, csak az 1 a 60 azokat a terjesztéseket, tegye a feldolgozási munka. 

### <a name="choose-a-distribution-column-that-minimizes-data-movement"></a>Válassza ki, amely minimálisra csökkenti az adatátvitelt jelölik a terjesztési oszlop

A megfelelő lekérdezéssel lekérni a lekérdezések eredménye lehet, hogy tárolt adatok mozgatása egy számítási csomópont egy másikra. Adatátvitel általában akkor történik, ha a lekérdezések rendelkezik összekapcsolásokhoz és az aggregációhoz elosztott táblákon. Csökkentheti az adatátvitel terjesztési oszlopok kiválasztása az egyik a legfontosabb stratégiák az SQL Data Warehouse teljesítményének optimalizálásához.

Adatátvitel minimalizálása érdekében válasszon ki egy terjesztési oszlopot, amely:

- A használt `JOIN`, `GROUP BY`, `DISTINCT`, `OVER`, és `HAVING` záradékban. Ha két nagy ténytáblák gyakori illesztéseket, a lekérdezési teljesítmény növeli a mindkét táblát az illesztési oszlopok egyik terjesztésekor.  Egy táblázat nem szerepel csatlakozik, vegye figyelembe terjesztése gyakran része oszlopban a tábla a `GROUP BY` záradékban.
- Van *nem* használt `WHERE` záradékban. Ez a lekérdezés futtatásához nem a megfelelő kiadásának sikerült Szűkítse le. 
- Van *nem* a dátum oszlop. WHERE záradékaiban gyakran dátum szerinti szűréséhez.  Ha ez történik, a feldolgozási futtathat a csak néhány terjesztéseket.

### <a name="what-to-do-when-none-of-the-columns-are-a-good-distribution-column"></a>Mi a teendő, ha az oszlopok egyike jó terjesztési oszlop

Ha az oszlopok egyike értékűeknek elegendő terjesztési oszlop, létrehozhat egy olyan új oszlop egy vagy több értékek összetett. Több adatátvitelt jelölik a lekérdezés végrehajtása közben, használja az összetett terjesztési oszlop lekérdezésekben illesztési oszlop.

Miután alakítson ki az ujjlenyomat-elosztott tábla, a következő lépéssel lehet adatokat betölteni az a táblázat.  Útmutatás feltöltését, lásd: [betöltést áttekintő](sql-data-warehouse-overview-load.md). 

## <a name="how-to-tell-if-your-distribution-column-is-a-good-choice"></a>Hogyan állapítható meg, hogy a terjesztési oszlop jó választás
Adatok betöltése a kivonat-elosztott táblába, után ellenőrizze, hogy hogyan egyenlően elosztott a sorok a 60 terjesztéseket. A sorok) eloszlása feladatonként (10 %-kal egy észrevehető gyakorolt hatása összességében nélkül változhatnak. 

### <a name="determine-if-the-table-has-data-skew"></a>Határozza meg, ha a tábla tartalmaz-e az adatok döntés
Ellenőrizze az adatok döntés, hogy használja gyorsan [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql). A következő SQL-kódot a táblázat sorait az egyes a 60 terjesztéseket tárolt számát adja vissza. Az elosztott terhelésű teljesítmény érdekében az elosztott tábla sorait kell kell elosztva egyenletesen valamennyi felosztást.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

Azonosításához táblák rendelkezhet több mint 10 % adatok döntés:

1. A megjelenített nézet dbo.vTableSizes létrehozása a [táblák áttekintése](sql-data-warehouse-tables-overview.md#table-size-queries) cikk.  
2. Futtassa a következő lekérdezést:

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

### <a name="check-query-plans-for-data-movement"></a>Az adatátvitelt jelölik a lekérdezésterveket ellenőrzése
Egy jó terjesztési oszlop lehetővé teszi, hogy összekapcsolásokhoz és az aggregációhoz minimális adatmozgás rendelkeznie. Ez hatással van a módon illesztések kell írni. Ahhoz, hogy a minimális kapcsolatos adatmozgatás a két kivonatoló elosztott táblákon illesztés, az illesztési oszlopok egyikének kell lennie a terjesztési oszlop.  Ha két kivonatoló elosztott tábla ugyanannál az adattípusnál terjesztési oszlopon veszi, az illesztés nem igényel adatátvitelt jelölik. Illesztések használható további oszlopok nélkül adatátvitelt jelölik.

Adatátvitel során illesztés elkerülésére:

- A illesztésben részt vevő táblák kell lennie a terjesztés kivonatoló **egy** részt vesz a illesztési oszlop.
- Az illesztési oszlopok adattípusai meg kell egyeznie a két tábla között.
- Az oszlopok az egyenlő operátor szerinti szűrése tartományhoz kell csatlakoznia.
- Az illesztési típus nem lehet egy `CROSS JOIN`.

Ha a lekérdezések tapasztal adatmozgás megtekintéséhez vessen egy pillantást a lekérdezéstervben.  


## <a name="resolve-a-distribution-column-problem"></a>Egy terjesztési oszlop probléma megoldása
Nincs szükség az adatok minden esetben döntés megoldásához. Adatok terjesztése, hogy a rendszer az egyensúlyt a közötti minimalizálja a eltérésére adatok és az adatátvitelt jelölik. Nincs mindig minimalizálni adatok döntés, mind az adatátvitelt jelölik. A minimális adatmozgás előnyös néha előfordulhat, hogy járó döntés adatok hatását.

Döntse el, hogy ha oldja fel az adatok eltérésére egy tábla tisztában kell lennie az adatok mennyiségéről és lekérdezések lehetőség szerint a terhelést a. A lépéseket használhatja a [lekérdezés figyelési](sql-data-warehouse-manage-monitor.md) cikk eltérésére lekérdezési teljesítményre gyakorolt hatásának figyelése. Pontosabban keresse meg, hogy mennyi ideig tart befejezéséhez az egyes terjesztési nagy lekérdezések.

A meglévő tábla terjesztési oszlopa nem módosítható, mert a tipikus oldható fel az adatok döntés módja újra létre kell hoznia egy másik terjesztési oszlop a tábla.  

### <a name="re-create-the-table-with-a-new-distribution-column"></a>Hozza létre az új terjesztési oszlopot tartalmazó tábla
Ez a példa [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md) újra létre kell hoznia egy másik kivonatoló terjesztési oszlopot tartalmazó tábla.

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

Hozzon létre egy elosztott táblát, valamelyikével ezekről az utasításokról:

- [(Az Azure SQL Data Warehouse) tábla létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [TABLE AS SELECT (Azure SQL Data Warehouse létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)


