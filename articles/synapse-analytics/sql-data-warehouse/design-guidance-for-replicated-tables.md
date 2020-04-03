---
title: Tervezési útmutató a replikált táblákhoz
description: Javaslatok replikált táblák synapse SQL-ben történő tervezéséhez
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/19/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 0b240c45afcb2374f41eb26e86e46b106e314e76
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582244"
---
# <a name="design-guidance-for-using-replicated-tables-in-synapse-sql"></a>Tervezési útmutató a replikált táblák synapse SQL-ben való használatához

Ez a cikk javaslatokat tartalmaz a replikált táblák tervezésére a szinapszis SQL-sémában. Ezekkel a javaslatokkal javíthatja a lekérdezési teljesítményt az adatok mozgásának és a lekérdezés összetettségének csökkentésével.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk feltételezi, hogy ismeri az adatok terjesztését és az adatok mozgatási fogalmait a Synapse SQL-ben.További információt az [architektúra](massively-parallel-processing-mpp-architecture.md) cikkben talál. 

A táblatervezés részeként a lehető legnagyobb mértékben ismerje meg az adatokat és az adatok lekérdezésének módját.Vegyük például a következő kérdéseket:

- Mekkora az asztal?   
- Milyen gyakran frissül a táblázat?   
- Vannak tény- és dimenziótáblák a Szinapszis SQL-adatbázisában?   

## <a name="what-is-a-replicated-table"></a>Mi az a replikált tábla?

A replikált tábla rendelkezik az egyes számítási csomópontokon elérhető tábla teljes másolatával. A replikált tábla esetében nincs szükség adatadásra a számítási csomópontok között az összekapcsolási vagy aggregációs művelet előtt. Mivel a tábla több példányt is készített, a replikált táblák akkor működnek a legjobban, ha a tábla mérete kisebb, mint 2 GB tömörített.  2 GB nem egy kemény határ.  Ha az adatok statikusak, és nem változnak, nagyobb táblákat replikálhat.

Az alábbi ábrán egy replikált tábla látható, amely minden számítási csomóponton elérhető. A Synapse SQL-ben a replikált tábla teljes mértékben átmásolódik egy terjesztési adatbázisba minden számítási csomóponton. 

![Replikált tábla](./media/design-guidance-for-replicated-tables/replicated-table.png "Replikált tábla")  

A replikált táblák jól működnek a csillagséma dimenziótábláihoz. A dimenziótáblák általában olyan ténytáblákhoz vannak összekötve, amelyek a dimenziótáblától eltérően vannak elosztva.  A méretek általában olyan méretűek, amely lehetővé teszi több példány tárolását és karbantartását. A dimenziók lassan változó leíró adatokat tárolnak, például a vevő nevét és címét, valamint a termék adatait. Az adatok lassan változó jellege a replikált tábla karbantartásának kevesebb megőrzését eredményezi. 

Fontolja meg egy replikált tábla használatát, ha:

- A lemezen lévő asztal mérete a sorok számától függetlenül kisebb, mint 2 GB. A táblázat méretének megkereséséhez használja a [DBCC](/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`PDW_SHOWSPACEUSED parancsot: . 
- A tábla olyan illesztésekben használatos, amelyek egyébként adatáthelyezést igényelnek. Ha nem ugyanabban az oszlopban lévő táblákat , például egy kivonatoló elosztott táblát egy ciklikus multiplexelési táblához csatlakozik, adatáthelyezésre van szükség a lekérdezés végrehajtásához.  Ha az egyik tábla kicsi, fontolja meg egy replikált táblát. A legtöbb esetben azt javasoljuk, hogy a ciklikus multiplexelési táblák helyett replikált táblákat használjon. A lekérdezési tervekben az adatmozgatási műveletek megtekintéséhez használja a [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  A BroadcastMoveOperation egy tipikus adatmozgatási művelet, amely replikált táblával kiküszöbölhető.  
 
Előfordulhat, hogy a replikált táblák nem a legjobb lekérdezési teljesítményt eredményezik, ha:

- A táblázat gyakori beszúrási, frissítési és törlési műveleteket is tartalmazza.Az adatkezelési nyelv (DML) műveletei a replikált tábla újraépítését igénylik.A gyakori újraépítés lassabb teljesítményt eredményezhet.
- A Szinapszis SQL-adatbázis méretezése gyakran. Az adatbázis méretezése módosítja a számítási csomópontok számát, ami a replikált tábla újraépítését eredményezi.
- A tábla nagy számú oszlopot tartalmaz, de az adatműveletek általában csak kis számú oszlophoz férnek hozzá. Ebben a forgatókönyvben a teljes tábla replikálása helyett hatékonyabb lehet a tábla terjesztése, majd a gyakran használt oszlopokindex létrehozása. Ha egy lekérdezés adatáthelyezést igényel, csak a kért oszlopok adatai kerülnek áthelyezésre.

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Replikált táblák használata egyszerű lekérdezési predikátumokkal

Mielőtt úgy dönt, hogy egy tábla terjesztését vagy replikálását választja, gondolja át, hogy milyen típusú lekérdezéseket kíván futtatni a táblához. Amikor csak lehetséges,

- Replikált táblák használata egyszerű lekérdezési predikátumokkal, például egyenlőség vagy egyenlőtlenség lekérdezések.
- Elosztott táblák használata összetett lekérdezési predikátumokkal rendelkező lekérdezésekhez, például A LIKE vagy a NOT LIKE.

A processzorigényes lekérdezések akkor teljesítenek a legjobban, ha a munka az összes számítási csomópontközött elvan osztva. Például a táblák minden során számításokat futtató lekérdezések jobban teljesítenek az elosztott táblákon, mint a replikált táblák. Mivel a replikált tábla teljes egészében tárolja az egyes számítási csomópontok, egy CPU-igényes lekérdezés ta-replikált tábla fut a teljes tábla minden számítási csomóponton. Az extra számítás lelassíthatja a lekérdezés teljesítményét.

Ez a lekérdezés például összetett predikátum.  Gyorsabban fut, ha az adatok egy elosztott táblában vannak, nem pedig replikált táblában. Ebben a példában az adatok kör-ciklikus multiplexelés terjeszthető.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'
       
```       
           
## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Meglévő ciklikus multiplexelési táblák átalakítása replikált táblákká
Ha már rendelkezik ciklikus multiplexeléstáblákkal, javasoljuk, hogy konvertálja őket replikált táblákká, ha azok megfelelnek a cikkben ismertetett feltételeknek. A replikált táblák javítják a ciklikus multiplexelési táblák teljesítményét, mivel nem szükséges az adatok mozgatása.  A ciklikus multiplexelési tábla mindig adatáthelyezést igényel az illesztésekhez. 

Ez a példa [ctas](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) segítségével módosítja a DimSalesTerritory táblát replikált táblává. Ez a példa attól függetlenül működik, hogy a DimSalesTerritory kivonatoló vagy ciklikus multiplexelésű.

```sql
CREATE TABLE [dbo].[DimSalesTerritory_REPLICATE]   
WITH   
  (   
    CLUSTERED COLUMNSTORE INDEX,  
    DISTRIBUTION = REPLICATE  
  )  
AS SELECT * FROM [dbo].[DimSalesTerritory]
OPTION  (LABEL  = 'CTAS : DimSalesTerritory_REPLICATE') 

-- Switch table names
RENAME OBJECT [dbo].[DimSalesTerritory] to [DimSalesTerritory_old];
RENAME OBJECT [dbo].[DimSalesTerritory_REPLICATE] TO [DimSalesTerritory];

DROP TABLE [dbo].[DimSalesTerritory_old];
``` 
    
### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Lekérdezési teljesítmény példa ciklikus multiplexelésre és replikáltra 
    
A replikált tábla nem igényel adatáthelyezést az illesztések, mert a teljes tábla már jelen van az egyes számítási csomópontokon. Ha a dimenziótáblák ciklikus multiplexelésűek, egy illesztés másolja a dimenziótáblát teljes egészében az egyes számítási csomópontokra. Az adatok áthelyezéséhez a lekérdezési terv egy BroadcastMoveOperation nevű műveletet tartalmaz. Ez a fajta adatmozgatási művelet lelassítja a lekérdezési teljesítményt, és replikált táblák használatával megszűnik. A lekérdezési terv lépéseinek megtekintéséhez használja a [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) rendszerkatalógus nézetét.  

Például a következő lekérdezés az AdventureWorks séma, a `FactInternetSales` tábla kivonat-elosztott. A `DimDate` `DimSalesTerritory` táblák kisebb dimenziótáblák. Ez a lekérdezés a 2004-es pénzügyi év észak-amerikai értékesítését adja vissza:

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
Újra létrehoztuk `DimDate` `DimSalesTerritory` és ciklikus multiplexelési asztalként. Ennek eredményeképpen a lekérdezés a következő lekérdezési tervet mutatta, amely több szórásos áthelyezési műveletet is magában látott: 
 
![Ciklikus multiplexeléses lekérdezési terv](./media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

Újra létrehoztuk `DimDate` `DimSalesTerritory` és replikált táblákként létrehoztuk, és újra futtattuk a lekérdezést. Az eredményül kapott lekérdezési terv sokkal rövidebb, és nem rendelkezik szórásos lépéseket.

![Replikált lekérdezési terv](./media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>A replikált táblák módosításával kapcsolatos teljesítményszempontok

A replikált tábla a tábla fő verziójának karbantartásával valósítható meg. A főverziót minden számítási csomóponton egy terjesztési adatbázisba másolja. Változás esetén a főtábla frissül először. Ezután az egyes számítási csomópontok on-ra a táblát újraépíti. A replikált tábla újraépítése magában foglalja a tábla másolása minden számítási csomópont, majd az indexek létrehozása.  Egy DW400 replikált táblája például 5 másolatot ad az adatokból.  Egy fő példány és egy teljes másolat minden számítási csomóponton.  Minden adatot terjesztési adatbázisokban tárolunk, hogy támogassuk a gyorsabb adatmódosítási nyilatkozatokat és a rugalmas skálázási műveleteket. 

Újraépítésre van szükség a következő után:
- Az adatok betöltése vagy módosítása
- A Synapse SQL-példány más szintre van méretezve
- A tábladefiníció frissül

Az újraépítésnem szükséges a következők után:
- Szüneteltetési művelet
- Folytatási művelet

Az újraépítés nem történik meg közvetlenül az adatok módosítása után. Ehelyett az újraépítés akkor aktiválódik, amikor egy lekérdezés először választ ki a táblából.  A lekérdezés, amely elindította az újraépítés tanév olvasás azonnal a fő verziója a tábla, miközben az adatok aszinkron módon másolt minden számítási csomópont. Amíg az adatmásolás be nem fejeződik, a további lekérdezések továbbra is a tábla főverzióját fogják használni.  Ha olyan tevékenység történik a replikált táblával szemben, amely egy másik újraépítést kényszerít, az adatmásolás érvénytelenné válik, és a következő select utasítás újra másolja az adatokat. 

### <a name="use-indexes-conservatively"></a>Indexek használata konzervatív módon

A replikált táblákra a szokásos indexelési eljárások vonatkoznak. Minden replikált táblaindex újraépül egy index-újraépítés részeként. Csak akkor használjon indexeket, ha a teljesítménynövekedés meghaladja az indexek újraépítésének költségét.  
 
### <a name="batch-data-loads"></a>Kötegadatok betöltése
Adatok replikált táblákba történő betöltésekor próbálja meg minimalizálni az újraépítéseket a terhelések együtt kötegelésével. Végezze el az összes kötegelt terhelést a select utasítások futtatása előtt.

Ez a betöltési minta például négy forrásból tölti be az adatokat, és négy újraépítést hív meg. 

        Load from source 1.
- Válassza ki az utasításeseményindítók újraépítését 1.
        Betöltés a forrás2-ből.
- Válassza ki az utasításeseményindítók újraépítését 2.
- Betöltés a 3-as forrásból.
- Válassza ki az utasításeseményindítók újraépítését 3.
- Betöltés a 4-es forrásból.
- Válassza ki az utasításeseményindítók újraépítését 4.

Ez a betöltési minta például négy forrásból tölti be az adatokat, de csak egy újraépítést hív meg.

- Betöltés az 1-es forrásból.
- Betöltés a forrás2-ből.
- Betöltés a 3-as forrásból.
- Betöltés a 4-es forrásból.
- Válassza ki az utasításeseményindítók újraépítését.

### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Replikált tábla újraépítése kötegelt terhelés után

A lekérdezések konzisztens végrehajtási idejének biztosítása érdekében fontolja meg a replikált táblák kötegelt terhelés utáni összeállításának kényszerítése. Ellenkező esetben az első lekérdezés továbbra is adatáthelyezést használ a lekérdezés befejezéséhez. 

Ez a lekérdezés a [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) DMV-t használja a módosított, de újranem épített replikált táblák listázásához.

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
 
Az újraépítés elindításához futtassa a következő utasítást az előző kimenet minden tábláján. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
```

## <a name="next-steps"></a>További lépések

Replikált tábla létrehozásához használja az alábbi utasítások egyikét:

- [TÁBLA LÉTREHOZÁSA](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [TÁBLA LÉTREHOZÁSA KIJELÖLÉSKÉNT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

Az elosztott táblák áttekintését az [elosztott táblák](sql-data-warehouse-tables-distribute.md)című témakörben találja.
