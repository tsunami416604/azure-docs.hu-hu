---
title: Tervezési útmutató a replikált táblák – Azure SQL Data Warehouse |} A Microsoft Docs
description: Tervezési javaslatok replikált táblák az Azure SQL Data Warehouse-sémában. 
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 03/19/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 031abcb9133663f39375560a06b0770c89eafb27
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259567"
---
# <a name="design-guidance-for-using-replicated-tables-in-azure-sql-data-warehouse"></a>Tervezési útmutató a replikált táblák az Azure SQL Data Warehouse használata
Ez a cikk javaslatok az SQL Data Warehouse sémában replikált táblák tervezéséhez nyújt. Ezekkel az ajánlásokkal használatával javíthatja a lekérdezések teljesítményét adatok mozgását és lekérdezési összetettséget csökkentésével.

> [!VIDEO https://www.youtube.com/embed/1VS_F37GI9U]

## <a name="prerequisites"></a>Előfeltételek
Ez a cikk feltételezi, hogy ismeri az adatok terjesztési és az SQL Data Warehouse mozgását – fogalmak.  További információkért lásd: a [architektúra](massively-parallel-processing-mpp-architecture.md) cikk. 

Táblatervezés részeként megismerheti a lehető legnagyobb mértékben az adatokat, és hogyan van lekéri az adatokat.  Például érdemes lehet ezeket a kérdéseket:

- Milyen méretű legyen a táblázat?   
- Milyen gyakran frissül a tábla?   
- Vannak tény- és dimenziótáblákból az adattárház?   

## <a name="what-is-a-replicated-table"></a>Mi az, hogy egy replikált tábla?
Egy replikált tábla teljes másolatával járó elérhető-e a tábla rendelkezik minden számítási csomóponton. A tábla replikálása feleslegessé teszi a előtt egy összesítés vagy a számítási csomópontok közötti adatátvitelt. Mivel a tábla rendelkezik több példányát tárolja, a replikált táblák leghatékonyabbak, ha a tábla mérete 2 GB-nál kisebb tömörített.  2 GB-os nem rögzített korlátja.  Ha az adatok statikus és nem változik, nagyobb méretű táblák replikálhatja.

Az alábbi ábrán látható egy replikált tábla, amely a számítási csomópontokon elérhető. Az SQL Data Warehouse a replikált tábla teljes másolja egy terjesztési adatbázis minden számítási csomóponton. 

![Replikált tábla](media/guidance-for-using-replicated-tables/replicated-table.png "replikált tábla")  

Replikált táblák működik jól a dimenziótáblák csillag sémában. Dimenziótáblák általában csatlakoztatott ténytáblák, amelyek működnek, mint a dimenziótáblában.  Dimenziók rendszerint méretű, amely megkönnyíti a tárol és tart fenn több példányban is megvalósítható. Dimenziók változik lassan, például az ügyfél neve és a címet és a termékek részleteit leíró adatokat tárolhat. Az adatok lassan változó jellege kevesebb a replikált tábla karbantartása vezet. 

Fontolja meg egy replikált tábla mikor:

- A tábla lemez mérete 2 GB-nál kisebb, függetlenül a sorok száma. A tábla mérete megkereséséhez használja a [DBCC PDW_SHOWSPACEUSED](https://docs.microsoft.com/sql/t-sql/database-console-commands/dbcc-pdw-showspaceused-transact-sql) parancs: `DBCC PDW_SHOWSPACEUSED('ReplTableCandidate')`. 
- A tábla szolgál, amelyek egyébként adatáthelyezés illesztésekben. Amikor táblákat, amelyek nem osztják meg ugyanazt az oszlopot, például egy Ciklikus időszeleteléses tábla kivonatoló elosztott tábla adatáthelyezés a lekérdezés végrehajtásához szükséges.  Ha a tábla egyik kicsi, fontolja meg egy replikált tábla. A legtöbb esetben a Ciklikus időszeleteléses táblák helyett a replikált táblák használatát javasoljuk. Az adatátviteli műveletek megtekintéséhez a lekérdezésterveket használja [sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql).  A BroadcastMoveOperation a szokványos adatátviteli műveletet, amely egy replikált tábla segítségével küszöbölhető.  
 
Replikált táblák nem eredményezhet, a legjobb lekérdezési teljesítmény során:

- A tábla tartalmaz a gyakori beszúrási, frissítési és törlési műveleteket. Ezek az adatkezelési nyelvű (DML) műveletek a replikált tábla van szükség. Újraépítése gyakran okozhat a lassabb teljesítményre.
- Az adatraktár gyakori van méretezve. A számítási csomópontok számát, amelyre a replikált tábla újraépítését adattárház skálázás módosítja.
- A tábla nagy számú oszlopot tartalmaz, de Adatműveletek általában kis számú oszlopot eléréséhez. Ebben a forgatókönyvben helyett a teljes tábla replikálása hatékonyabb a tábla, és ezután lehet indexet létrehozni a gyakran használt oszlopok érdemes lehet. Ha egy lekérdezést igényel az adatmozgatás, az SQL Data Warehouse csak helyez át adatokat a kért oszlopok. 

## <a name="use-replicated-tables-with-simple-query-predicates"></a>Replikált táblák használata egyszerű lekérdezés predikátumokat
Mielőtt kívánja terjeszteni, vagy egy tábla replikálása, gondolja át szeretné futtatni a táblázaton lekérdezések típusának. Amikor csak lehetséges,

- Az egyszerű lekérdezési predikátumok, például az egyenlőség vagy egyenlótlenség lekérdezések replikált táblák használatával.
- Elosztott táblák az összetett lekérdezések predikátumok, például a hasonló lekérdezések használatával, vagy nem.

CPU-igényes lekérdezéseket akkor teljesítenek a legjobban, ha az összes számítási csomópont között oszlanak meg a munkahelyi. Például egy táblázat minden egyes sorára futó számítások lekérdezések jobban, mint a replikált táblák elosztott táblákon végrehajtani. Mivel a replikált tábla minden számítási csomóponton teljes rendszer, a CPU-igényes lekérdezést egy replikált tábla fut a táblán teljes minden számítási csomóponton. Az extra számítási lelassíthatja a lekérdezések teljesítményét.

Ez a lekérdezés például egy összetett predikátum rendelkezik.  Amikor az adatok egy elosztott tábla helyett egy replikált tábla gyorsabban azt futtatja. Az ebben a példában az adatok Ciklikus időszeleteléses elosztott lehet.

```sql

SELECT EnglishProductName 
FROM DimProduct 
WHERE EnglishDescription LIKE '%frame%comfortable%'

```

## <a name="convert-existing-round-robin-tables-to-replicated-tables"></a>Replikált táblák Ciklikus időszeleteléses meglévő táblák átalakítása
Ha már rendelkezik a táblák Ciklikus időszeleteléses, azt javasoljuk replikált táblák azokat, amelyek megfelelnek a következő cikkben ismertetett feltételeknek konvertálása. Replikált táblák teljesítmény javítása a táblák Ciklikus időszeleteléses keresztül, mert nincs szükség az adatok áthelyezését.  Ciklikus időszeleteléses tábla mindig szükséges adatáthelyezés illesztéseket. 

Ez a példa [CTAS](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) módosítani a DimSalesTerritory tábla egy replikált tábla. Ebben a példában függetlenül attól, hogy DimSalesTerritory kivonatoló elosztott vagy Ciklikus időszeleteléses működik.

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

### <a name="query-performance-example-for-round-robin-versus-replicated"></a>Lekérdezési teljesítmény példa a Ciklikus időszeleteléses és replikálása 

Egy replikált tábla nem igényel minden adatáthelyezés az illesztések, mert a teljes tábla már szerepel a számítási csomópontokon. Ha a dimenzió táblák Ciklikus időszeleteléses elosztott, illesztés másolja át a dimenziótáblában teljes minden számítási csomóponton. Az adatok áthelyezéséhez a lekérdezésterv BroadcastMoveOperation nevű műveletet tartalmaz. Az ilyen típusú adatok mozgását művelet csökkenti a lekérdezés teljesítményét, és kiiktatja a replikált táblák használatával. Lekérdezés terv lépései megtekintéséhez használja a [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) rendszer katalógusnézet. 

Például a következő lekérdezést az AdventureWorks séma a ` FactInternetSales` táblát a kivonatoló elosztott. A `DimDate` és `DimSalesTerritory` kisebb dimenzió táblák. Ez a lekérdezés visszaadja az összes értékesítést Észak-Amerikában 2004-es pénzügyi évre:
 
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
Újra létrehozza `DimDate` és `DimSalesTerritory` Ciklikus időszeleteléses táblákként. Ennek eredményeképpen a lekérdezés kimutatta, a következő lekérdezés csomagra, amely rendelkezik több adás műveleteket helyez át: 
 
![Ciklikus lekérdezési terv](media/design-guidance-for-replicated-tables/round-robin-tables-query-plan.jpg) 

Újra létrehozza `DimDate` és `DimSalesTerritory` as replikált táblákról és futott-e a lekérdezést. Az eredményül kapott lekérdezésterv sokkal rövidebb, és nem kell minden küldi el a kurzor.

![A lekérdezésterv replikált](media/design-guidance-for-replicated-tables/replicated-tables-query-plan.jpg) 


## <a name="performance-considerations-for-modifying-replicated-tables"></a>Replikált táblák módosítására a teljesítménnyel kapcsolatos szempontok
Az SQL Data Warehouse-tábla fő verziója megőrzése valósít meg egy replikált tábla. Másolja a főverzió egy terjesztési adatbázis minden számítási csomóponton. Ha változás történik, az SQL Data Warehouse első frissíti a master táblát. Ezután újjáépíti minden számítási csomóponton lévő táblákat. Egy replikált tábla újjáépítést másolása a tábla minden számítási csomóponton, és majd a az indexek létrehozása tartalmaz.  Például egy replikált tábla egy DW400 meg, hogy az adatok 5 példányát.  A fő példányt és a egy teljes másolatot minden számítási csomóponton.  Terjesztésipont-adatbázisokban tárolt összes adatot. Az SQL Data Warehouse ezt a modellt használ gyorsabb adatok módosításának utasítások és a rugalmas méretezési műveletek támogatásához. 

Újraépíteni után szükségesek:
- Adatok betöltése vagy módosítva
- Az adatraktár van méretezve, hogy egy másik szintre
- Tábla definíciójának frissítése

Újraépíteni nem szükségesek után:
- Pause művelet
- A művelet folytatása

Az újjáépítést nem megy végbe, azonnal, miután az adatok módosulnak. Az újjáépítést akkor aktiválódik, ehelyett egy lekérdezést a tábla kiválasztja az első alkalommal.  A lekérdezés, amely kiváltotta az újjáépítést azonnal beolvassa a-tábla fő verziója, amíg minden számítási csomópont aszinkron módon másolja az adatokat. Mindaddig, amíg az adatok másolása befejeződött, további lekérdezések továbbra is a tábla fő verzióját használja.  Ha minden tevékenység egy másik Újraépítés arra kényszeríti a replikált táblázaton történik, az adatok másolását érvénytelenné válik, és a következő select utasítás indítja újra másolandó adatok. 

### <a name="use-indexes-conservatively"></a>Konzervatív módon vegyen figyelembe indexek használata
Az indexelő általános gyakorlatok replikált táblák vonatkoznak. Az SQL Data Warehouse újraépíti az újjáépítést részeként minden replikált tábla indexe. Indexek csak akkor használja, ha a teljesítmény megvalósításának költsége az indexek újraépítése.  
 
### <a name="batch-data-loads"></a>A Batch adatterhelések
Amikor az adatok betöltését replikált táblák, próbálja meg minimálisra csökkenteni a újraépíteni által terhelések kötegelés együtt. Hajtsa végre a kötegelt terhelések select utasítás futtatása előtt.

Például a terhelés minta négy forrásból származó adatokat tölt, és négy újraépíteni hív meg. 

- 1. forrásból származó betölteni.
- SELECT utasítás eseményindítók építse újra az 1.
- Forrás 2 betöltése.
- SELECT utasítás eseményindítók építse újra a 2.
- 3. forrás betöltése.
- SELECT utasítás eseményindítók építse újra a 3.
- Adatforrás 4 betöltése.
- SELECT utasítás eseményindítók építse újra a 4.

Például a terhelést a minta négy forrásból származó adatokat tölt, de csak hív meg egy újraépítése.

- 1. forrásból származó betölteni.
- Forrás 2 betöltése.
- 3. forrás betöltése.
- Adatforrás 4 betöltése.
- Építse újra a SELECT utasítás eseményindítók.


### <a name="rebuild-a-replicated-table-after-a-batch-load"></a>Építse újra a replikált táblát egy kötegelt betöltés után
Ahhoz, hogy a lekérdezés konzisztens végrehajtási időpontok, fontolja meg egy kötegelt betöltés után a replikált táblák a build kényszerítése. Ellenkező esetben az első lekérdezés továbbra is használni kívánt adatok áthelyezése a lekérdezést. 

Ez a lekérdezés használ a [sys.pdw_replicated_table_cache_state](/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql) DMV, módosítás, de nem újraépítve replikált táblák felsorolása.

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
 
Újjáépítést indításához futtassa a következő utasítást a fenti kimenetben szereplő minden egyes táblán. 

```sql
SELECT TOP 1 * FROM [ReplicatedTable]
``` 
 
## <a name="next-steps"></a>További lépések 
Hozzon létre egy replikált tábla, használja ezeket az utasításokat egyikét:

- [(Az Azure SQL Data Warehouse) tábla létrehozása](/sql/t-sql/statements/create-table-azure-sql-data-warehouse)
- [TABLE AS SELECT (az Azure SQL Data Warehouse) létrehozása](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse)

Elosztott táblák áttekintését lásd: [elosztott táblákról](sql-data-warehouse-tables-distribute.md).



