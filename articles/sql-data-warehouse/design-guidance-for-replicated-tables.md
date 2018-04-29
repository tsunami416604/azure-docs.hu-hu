---
title: Útmutató a replikált táblák - Azure SQL Data Warehouse kialakítása |} Microsoft Docs
description: Tervezésével kapcsolatos ajánlások replikált táblák az Azure SQL Data Warehouse sémában.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/23/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 1cc796061056ff017e3d778ebb2e50e13d55a4c1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Útmutató a replikált táblák használata az Azure SQL Data Warehouse tervezése
Ez a cikk tervezése során az SQL Data Warehouse-sémát a replikált táblák ajánlásokat biztosít. Ezek a javaslatok segítségével javíthatja a lekérdezések teljesítményét adatok mozgás és a lekérdezés összetettsége csökkentésével.

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk azt feltételezi, hogy az adatok terjesztési és az SQL Data Warehouse adatátviteli – fogalmak megismerése.  További információkért lásd: a [architektúra](massively-parallel-processing-mpp-architecture.md) cikk. 

Táblatervezés részeként megérteni a lehető legnagyobb mértékben az adatokat, és hogyan lekérik az adatokat.  Tegyük fel ezeket a kérdéseket:

- Mekkora a tábla?   
- Milyen gyakran frissülnek, a tábla?   
- Van tény-és dimenziótáblák az adatraktárban?   

## <a name="what-is-a-replicated-table"></a>Mi az a replikált tábla?
A replikált tábla rendelkezzen minden számítási csomópont elérhető tábla teljes másolata. A tábla replikálása szükségtelenné join vagy összesítés előtt a számítási csomópontok közötti adattovábbításra. Mivel a tábla csak különböző példányainak, replikált táblák legjobban, ha a tábla mérete kisebb, mint 2 GB tömörített.

Az alábbi ábrán látható, a replikált tábla által elérhető minden számítási csomóponton. Az SQL Data Warehouse a replikált tábla teljes másolja a terjesztési adatbázis minden számítási csomóponton. 

![Replikált tábla](media/guidance-for-using-replicated-tables/replicated-table.png "replikált tábla")  

Replikált táblák munkahelyi csillagséma kis dimenzió táblák esetén. Dimenziótáblák általában méretű, amely megkönnyíti a valósítható meg, tárolására és fenntartására több példány van. Dimenziók adattárolásra leíró megváltoztató lassan, például a felhasználói nevét és címét és a termék részletei. Az adatok a lassan változó jellege kevesebb újraépíti a replikált tábla vezet. 

Fontolja meg a replikált tábla esetén:

- A tábla a lemezen mérete 2 GB-nál kevesebb, függetlenül a sorok száma. A tábla mérete megkereséséhez használja a [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) parancs: `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- A tábla szolgál, amelyek egyébként adatmozgás illesztésekben. Táblázatot, amely ugyanezen az oszlopon, például egy ujjlenyomat-elosztott táblát egy ciklikus multiplexelés táblához nem osztják csatlakoztatáskor adatátvitelt jelölik a lekérdezés végrehajtásához szükséges.  Ha a tábla kicsi, fontolja meg a replikált tábla. Replikált táblák helyett a legtöbb esetben ciklikus multiplexelés táblák használatát javasoljuk. A lekérdezés csomagokban az adatátviteli műveletek megtekintéséhez használja [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).  A BroadcastMoveOperation replikált tábla használatával el is távolíthatók szokványos adatátviteli művelet.  
 
Replikált táblák nem fed fel a legjobb lekérdezési teljesítmény során:

- A tábla tartalmaz a gyakori beszúrási, frissítési és törlési műveletek. Ezen adatok adatkezelési nyelv működéséhez szükségesek egy újjáépítését a replikált tábla. Újraépítése gyakran okozhat lassabb teljesítményre.
- Az adatraktár gyakori méretezett. Adatraktár skálázás módosítja a számítási csomópontokat, ami azt eredményezi azok háromszorosa egy rebuild száma.
- A tábla a nagy számú oszlopot tartalmaz, de az műveleteket általában hozzáférni csak kis számú oszlopot. Ebben a forgatókönyvben, ahelyett, hogy a teljes táblázatot lehet terjeszteni a tábla, majd hozza létre az indexet a gyakran használt oszlopokon hatékonyabb. Ha a lekérdezés adatátvitelt jelölik a szükséges, az SQL Data Warehouse csak helyezi át az adatokat a kért oszlopot. 

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Replikált táblák használata egyszerű lekérdezés predikátumok
Mielőtt terjeszteni, vagy a tábla replikálása mellett dönt, tervezze meg a lekérdezéstípusok szeretné futtatni a táblázaton. Amikor csak lehetséges –

- Az egyszerű lekérdezés predikátumok, például egyenlőség vagy egyenlőtlen Lekérdezések replikált táblák használja.
- Az összetett lekérdezések predikátumok, például a hasonló lekérdezések elosztott táblázatot használni, vagy nincs MEGELÉGEDVE.

Processzorigényes lekérdezések legjobb végre, ha a munkahelyi oszlik el minden számítási csomópont. Például egy táblázat minden egyes sorára számítások futó lekérdezések végezzen jobban, mint a replikált táblák elosztott táblákon. Mivel a replikált tábla rendszer teljes minden számítási csomóponton, processzorigényes replikált táblákon futása a teljes táblázaton minden számítási csomóponton. A további számítási lelassíthatja a lekérdezés teljesítményét.

Például ez a lekérdezés tartalmaz egy összetett predikátum.  Az elosztott táblázat helyett egy replikált tábla esetén a szállító gyorsabban futtatja. Ebben a példában a szállító ciklikus multiplexelés elosztott is lehet.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Replikált táblák meglévő ciklikus multiplexelés táblák átalakítása
Ha már rendelkezik ciklikus multiplexelés táblák, ajánlott replikált táblák azokat a cikkben ismertetett feltételekkel, amelyek megfelelnek alakítása. Replikált táblák teljesítmény javítása a ciklikus multiplexelési táblák keresztül, mert nincs szükség az adatátvitelt jelölik.  Ciklikus multiplexelés tábla mindig szükséges, adatátvitelt jelölik a táblákra. 

Ez a példa [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) a DimSalesTerritory tábla módosítása replikált táblához. Ez a példa függetlenül attól, hogy DimSalesTerritory kivonatoló elosztott vagy a ciklikus multiplexelési működik.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

--Create statistics on new table
CREATE STATISTICS [SalesTerritoryKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryKey]);
CREATE STATISTICS [SalesTerritoryAlternateKey] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryAlternateKey]);
CREATE STATISTICS [SalesTerritoryRegion] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryRegion]);
CREATE STATISTICS [SalesTerritoryCountry] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryCountry]);
CREATE STATISTICS [SalesTerritoryGroup] ON [DimSalesTerritory_REPLICATE] ([SalesTerritoryGroup]);

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
```  

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Ciklikus multiplexelés és a lekérdezési teljesítmény példa replikálása 

A replikált tábla nem szükséges minden adatátvitelt jelölik a táblákra mivel az egész tábla már létezik minden számítási csomóponton. Ha a dimenziótáblák ciklikus multiplexelés elosztott, akkor illesztés minden számítási csomópont teljesen a dimenziótáblában másolja át. Helyezze át az adatokat, hogy a lekérdezésterv tartalmaz BroadcastMoveOperation nevű művelet. Ilyen adatok adatátviteli művelet csökkenti a lekérdezés teljesítményét, és kiiktatja a replikált táblák használata. Lekérdezés terv lépéseket megtekintéséhez használja a [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) rendszer katalógusnézet használatával derítheti ki. 

Például a következő lekérdezés az AdventureWorks sémán elvégzett a ` FactInternetSales` táblát kell kivonatoló elosztott. A `DimDate` és `DimSalesTerritory` kisebb dimenzió táblák. Ez a lekérdezés adja vissza az összes értékesítés Észak-Amerika évhez 2004:
 
```sql
SELECT [TotalSalesAmount] = SUM(SalesAmount)
FROM dbo.FactInternetSales s
INNER JOIN dbo.DimDate d
  ON d.DateKey = s.OrderDateKey
INNER JOIN dbo.DimSalesTerritory t
  ON t.SalesTerritoryKey = s.SalesTerritoryKey
WHERE d.FiscalYear = 2004
  AND t.SalesTerritoryGroup = 'North America'
```
Újból létrehozza `DimDate` és `DimSalesTerritory` ciklikus multiplexelés táblák. A lekérdezés eredményeként, a következő lekérdezésterv, amelynek műveletek áthelyezése több szórás bemutatta: 
 
![Ciklikus lekérdezés terv](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

Újból létrehozza `DimDate` és `DimSalesTerritory` , replikált táblák, és a lekérdezés újra futott. Az eredményül kapott lekérdezésterv sokkal rövidebb, és nem rendelkezik a küldi helyezi át.

![Replikált lekérdezésterv](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>A replikált táblák módosítását a teljesítménnyel kapcsolatos szempontok
Az SQL Data Warehouse-tábla fő verziója fölött valósítja meg a replikált tábla. Másolja a főverzió egy terjesztési adatbázis minden számítási csomóponton. Változás történik, amikor az SQL Data Warehouse először frissíti a fő tábla. Majd újjáépíti a táblázatok minden számítási csomóponton. A replikált tábla újjáépítését másolása a tábla minden számítási csomópont, és ezután a az indexek felépítése tartalmaz.  Például egy DW400 a replikált tábla rendelkezik az adatok 5 másolatát.  A fő példány és egy teljes másolatot minden számítási csomóponton.  Minden adat terjesztési adatbázisok tárolják. SQL Data Warehouse a modellt használ az adatok módosítását gyorsabb utasítások és rugalmas skálázási műveletek támogatásához. 

Újraépíti szükségesek után:
- Adatok betöltése vagy módosította
- Az adatraktár egy másik szintre méretezése
- Tábla definíciójában frissül.

Újraépíti esetén nincs szükség után:
- Szüneteltetési művelete
- A művelet folytatása

Az újjáépítést történik meg azonnal az adatok módosítása után. Ehelyett az újjáépítést lekérdezés kiválasztja azokat a tábla első alkalommal elindul.  A lekérdezés az újjáépítést kiváltó olvassa be az azonnal-tábla fő verziója közben aszinkron módon átmásolja az adatokat minden számítási csomópont. Amíg be nem fejeződik az adatok másolását, lekérdezések továbbra is a tábla fő verzióját használja.  Minden olyan tevékenységet, amely egy másik rebuild kényszeríti replikált táblázaton történik, ha az adatok másolását érvénytelenné válik, és adat másolása újra akkor indul el, a következő utasítást. 

### <a name="use-indexes-conservatively"></a>Konzervatív módon indexek használata
Standard indexelési eljárások replikált táblák vonatkozik. Az SQL Data Warehouse újraépíti az újjáépítést részeként minden replikált tábla indexe. Indexek csak akkor alkalmazza, ha a jobb teljesítménye ez fontosabb, mint az indexek újraépítése költségét.  
 
### <a name="batch-data-loads"></a>Kötegelt adatbetöltés
Ha az adatok betöltését replikált táblák, próbálja meg minimálisra csökkenteni a újraépíti által terhelések kötegelés együtt. Hajtsa végre a kötegelt terhelések select utasítás futtatása előtt.

Például a terhelés mintát négy forrásból származó adatokat tölt, és meghívja a négy újraépíti. 

- Forrás 1 betöltése.
- SELECT utasítás eseményindítók építse újra az 1.
- Forrás 2 betöltése.
- SELECT utasítás eseményindítók építse újra a 2.
- Forrás 3 betöltése.
- SELECT utasítás eseményindítók építse újra a 3.
- Adatforrás 4 betöltése.
- SELECT utasítás eseményindítók építse újra a 4.

Például a terhelés mintát négy forrásból származó adatokat tölt, de csak hív meg, egy Újraépítés.

- Forrás 1 betöltése.
- Forrás 2 betöltése.
- Forrás 3 betöltése.
- Adatforrás 4 betöltése.
- SELECT utasítás eseményindítók építse újra.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>A replikált tábla helyreállítása egy kötegelt betöltés után
Ahhoz, hogy a lekérdezés konzisztens végrehajtásának lassúságát, fontolja meg, a replikált táblák a build kényszerített egy kötegelt betöltés után. Ellenkező esetben az első lekérdezés továbbra is használni adatátvitelt jelölik a lekérdezést. 

Ez a lekérdezés használja a [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) DMV módosított, de nem úgy replikált táblák listázásához.

```sql 
SELECT [ReplicatedTable] = t.[name]
  FROM sys.tables t  
  JOIN sys.pdw_replicated_table_cache_state c  
    ON c.object_id = t.object_id 
  JOIN sys.pdw_table_distribution_properties p 
    ON p.object_id = t.object_id 
  WHERE c.[state] = 'NotReady'
    AND p.[distribution_policy_desc] = 'REPLICATE'
```
 
Egy rebuild indításához futtassa az alábbi utasítást a fenti kimenetben minden táblában. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>További lépések 
A replikált tábla létrehozása valamelyikével ezekről az utasításokról:

- [(Az Azure SQL Data Warehouse) tábla létrehozása](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [TABLE AS SELECT (Azure SQL Data Warehouse) létrehozása](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

Elosztott táblák áttekintését lásd: [táblák elosztott](sql-data-warehouse-tables-distribute.md).



