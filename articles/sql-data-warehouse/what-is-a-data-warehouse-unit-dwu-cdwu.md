---
title: Mik azok a Adattárházegységek (dwu-k, cDWUs) az Azure SQL Data Warehouse? | Microsoft Docs
description: Az Azure SQL Data Warehouse képességek kibővítési teljesítményét. Horizontális felskálázás dwu-k, cDWUs, beállításával vagy és sablonok felfüggesztése és folytatása a számítási erőforrásokat költségek csökkentése érdekében.
services: sql-data-warehouse
author: sqlmojo
manager: craigg-msft
ms.topic: conceptual
ms.component: manage
ms.date: 04/09/2018
ms.author: joeyong
ms.reviewer: jrj
ms.openlocfilehash: 56d59be2074a3047ce19fde3e808354266040864
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Adattárházegységek (dwu-k) és a számítás Adattárházegységek (cDWUs)
Adattárházegységek (dwu-k) ismerteti, és az Azure SQL Data Warehouse Adattárházegységek (cDWUS) számítási. Javaslatok kiválasztásáról adattárházegységek, és azok számát módosítása ideális számát tartalmazza. 

## <a name="what-are-data-warehouse-units"></a>Mik azok a Adattárházegységek?
SQL Data Warehouse Processzor, memória és I/O vannak becsomagolva egységekbe számítási skála Adattárházegységek (dwu-k) néven. A DWU egy absztrakt, normalizált mértéket számítási erőforrásokat és a teljesítmény jelöli. A szolgáltatási szint módosításával módosítható a rendszerben, ami viszont beállítja a teljesítmény és a költség, a rendszer számára kiosztott dwu-k száma. 

Nagy nagyobb teljesítményt, adattárházegységek száma növelhető. Kevesebb teljesítmény fizet, csökkentse az adattárházegységek. Tárolási és számítási költségek külön-külön számlázzuk, adattárházegységek módosítása nem befolyásolja a tárolási költségeket.

Teljesítményt az adattárházegységek alapján történik a data warehouse munkaterhelési mérőszámokat:

- Milyen gyors is szabványos vámraktározási adatlekérdezés vizsgálata nagy mennyiségű sort és ezután hajtja végre komplex összesítést készít? Ez a művelet i/o- és a CPU-intenzív esetében.
- Milyen gyors fogadására képes, az adatraktár adatait az Azure Storage blobs szolgáltatásban vagy az Azure Data Lake? Ez a művelet a hálózati és a CPU-intenzív esetében. 
- Milyen gyorsan lehet a [CREATE TABLE AS SELECT](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL-parancsot, másolja át egy táblázatot? Ez a művelet adatolvasás a tárolóból, szétosztása a készülék csomópontjai között, és ismét rögzíti az tárolási magában foglalja. Ez a művelet CPU IO és intenzív hálózati esetében.

Dwu-k növelése:
- Lineárisan módosítja a rendszer a vizsgálatokat, összesítések és CTAS utasítások
- Az olvasók és a PolyBase-betöltési műveleteihez írók száma
- Növeli a párhuzamos lekérdezések és feldolgozási üzembe helyezési ponti maximális számát.

## <a name="service-level-objective"></a>Szolgáltatási szint célkitűzésének
A szolgáltatási szint célkitűzést (SLO), a méretezhetőség beállítás határozza meg az adatraktár költségeket és a teljesítmény szintjét. A szolgáltatási szintek, a számítási teljesítmény réteg méretezési optimalizálása a számítási adattárházegységek (cDWU), például DW2000c értékek mérése. A rugalmasság szolgáltatási szintek optimalizálása a dwu-k, például DW2000 mérik. 

T-SQL-ben a SERVICE_OBJECTIVE beállítás határozza meg, a szolgáltatási szint és az adatraktár teljesítményének rétegét.

```sql
--Optimized for Elasticity
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Optimized for Compute
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Teljesítmény szinteket, és Adattárházegységek

Minden egyes teljesítményszinttel némileg eltérő mértékegységet az adattárházegységek használ. Ezt a különbséget a számlán is megjelenik, a méretezési egység közvetlenül a számlázási rendszerhez.

- A rugalmasság teljesítményszinttel mérése Adattárházegységek (dwu-k) optimalizálva.
- Az optimalizált a számítási teljesítmény rétegben mérése számítási Adattárházegységek (cDWUs). 

Dwu-k és cDWUs egyaránt támogatja méretezési számítási felfelé vagy lefelé, és számítási felfüggesztése, ha nem szeretné használni, az adatraktár. Ezeket a műveleteket az összes igény szerinti. Az optimalizált számítás teljesítményszinttel is használ egy helyi lemezalapú gyorsítótárban a számítási csomópontok a teljesítmény javítása érdekében. Méretezhető, illetve a rendszer felfüggeszti a gyorsítótárat érvénytelenítik, és így gyorsítótár felmelegedés időszak kötelező, mielőtt az optimális teljesítmény akkor érhető el.  

Adattárházegységek növelésével számítási erőforrások lineárisan növekednek. Az optimalizált számítás teljesítményszinttel biztosítja a legjobb lekérdezési teljesítmény és a legmagasabb méretezési azonban rendelkezik egy magasabb belépési ár. A teljesítmény állandó igény szerinti rendelkező vállalatok számára készült. Ezek a rendszerek kell használniuk a legtöbb a gyorsítótár. 

### <a name="capacity-limits"></a>Kapacitási korlátok
Minden egyes SQL server (például myserver.database.windows.net) rendelkezik egy [Database Transaction Unit (DTU)](../sql-database/sql-database-what-is-a-dtu.md) , amely lehetővé teszi, hogy egy bizonyos számú adattárházegységek kvótát. További információkért lásd: a [munkaterhelés felügyeleti kapacitáskorlátait](sql-data-warehouse-service-capacity-limits.md#workload-management).


## <a name="how-many-data-warehouse-units-do-i-need"></a>Hány adattárházegységek kell?
Az ideális száma adattárházegységek függ túlságosan azok a számítási feladatok és a rendszerbe betöltött adatok mennyiségét.

A legjobb DWU kereséséhez a munkaterheléshez lépéseket:

1. Fejlesztési először egy kisebb DWU az optimalizált használ a rugalmasság teljesítményszinttel kiválasztása.  Mivel a probléma, amely ebben a szakaszban működési érvényesítési, a rugalmasság teljesítményszinttel optimalizálása beállítás ésszerű. Jó kiindulópont DW200. 
2. Az alkalmazások teljesítményéről, tesztelje adatbetöltés a rendszerbe betartásával erőforrásigények teljesítéséhez képest választott dwu-k számának figyelése
3. Azonosítsa a esetleges további követelményeket csúcs tevékenység rendszeres időszakokra. Ha a munkaterhelés látható jelentős csúcsait és csatornák tevékenységben, és egy jó ok arra, hogy gyakran méretezhető, majd a rugalmasság teljesítményszinttel a optimalizált alkalmazást.
4. Ha több mint 1000 DWU kell, majd a számítási teljesítmény rétegben optimalizálása alkalmazást, mivel így az a legjobb teljesítmény érdekében.

Az SQL Data Warehouse kibővített rendszer építhető ki a számítási és a lekérdezés jelentős mennyiségű adatok óriási mennyiségben. Igaz platformképességei méretezéshez, különösen a nagyobb dwu-k, lásd: javasoljuk az adatkészlet skálázás, annak érdekében, hogy kevés az adat a hírcsatorna a processzorok méretezni. A skála vizsgálat, azt javasoljuk, legalább 1 TB.

> [!NOTE]
>
> Lekérdezési teljesítmény csak további párhuzamos folyamatkezelést biztosítja a növekszik, ha a munkahelyi oszthatók számítási csomópontjai között. Ha talál meg, hogy skálázás nem módosítja a teljesítményt, szükség lehet a Táblatervezés és/vagy a lekérdezések hangolását. A lekérdezés hangolása útmutatást, tekintse meg a következő [teljesítmény](sql-data-warehouse-overview-manage-user-queries.md) cikkeket. 

## <a name="permissions"></a>Engedélyek

A adattárházegységek módosításához szükséges a leírt engedélyek [ALTER DATABASE][ALTER DATABASE]. 

## <a name="view-current-dwu-settings"></a>Aktuális DWU beállításainak megjelenítése

A DWU-beállítására megtekintése:

1. Nyissa meg az SQL Server Object Explorert a Visual Studióban.
2. A master adatbázis SQL Database logikai kiszolgálóhoz rendelt kapcsolódni.
3. Válassza ki a sys.database_service_objectives dinamikus kezelési nézetet. Például: 

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Adattárházegységek módosítása

### <a name="azure-portal"></a>Azure Portal
Dwu-k vagy cDWUs módosítása:

1. Nyissa meg a [Azure-portálon](https://portal.azure.com), nyissa meg az adatbázishoz, és kattintson a **méretezési**.

2. A **méretezési**, mozgassa a csúszkát bal vagy jobb gombbal módosíthatja a DWU-beállítására állnak.

3. Kattintson a **Save** (Mentés) gombra. Egy megerősítő üzenet jelenik meg. Kattintson a **Igen** megerősítéséhez vagy **nem** megszakítja a műveletet.

### <a name="powershell"></a>PowerShell
A dwu-k vagy cDWUs módosításához az [Set-AzureRmSqlDatabase] [Set-AzureRmSqlDatabase] PowerShell parancsmaggal. Az alábbi példa állítja be a szolgáltatási szint célkitűzés DW1000 MySQLDW a MyServer kiszolgáló tárolt adatbázis.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

### <a name="t-sql"></a>T-SQL
A T-SQL aktuális DWU vagy cDWU beállítások megtekintése, módosítsa a beállításokat, majd a folyamat állapotának ellenőrzése. 

A dwu-k vagy cDWUs módosítása:

1. A fő adatbázist az SQL Database logikai kiszolgálóhoz csatlakozni.
2. Használja a [ALTER DATABASE] [ ALTER DATABASE] TSQL utasítást. Az alábbi példa állítja be a szolgáltatási szint célkitűzés DW1000 az adatbázis MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>REST API-k

A dwu-k módosításához használja a [létrehozás vagy frissítés adatbázis] [létrehozás vagy frissítés adatbázis] REST API-t. Az alábbi példa állítja be a szolgáltatási szint célkitűzés DW1000 az adatbázis MySQLDW, amely a MyServer kiszolgáló üzemelteti. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforrás-csoportban.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```


## <a name="check-status-of-dwu-changes"></a>DWU módosítások állapotának ellenőrzése

DWU módosítások több percet is igénybe vehet. Ha az automatikus skálázás, vegye fontolóra logika győződjön meg arról, hogy bizonyos műveletek befejeződtek-e egy másik művelet folytatása előtt. 

Az adatbázis állapotát a különböző végpontok ellenőrzése lehetővé teszi automation megfelelően végrehajtásához. A portál értesítés létrehozása után a művelet és az adatbázis jelenlegi állapota biztosít, de nem engedélyezi a állapot ellenőrzéséhez szoftveres. 

Az adatbázis állapotát az Azure-portálon kibővített műveletek esetében nem lehet ellenőrizni.

A DWU módosítások állapotának ellenőrzéséhez:

1. A fő adatbázist az SQL Database logikai kiszolgálóhoz csatlakozni.
2. Küldje el a következő lekérdezés futtatásával ellenőrizze az adatbázis állapotát.


```sql
SELECT    *
FROM      sys.databases
;
```

3. Küldje el a következő lekérdezés futtatásával művelet állapotának ellenőrzése

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Ez a DMV különböző felügyeleti műveleteket végez a művelet és a művelet, amely vagy állapotának például az SQL Data Warehouse kell IN_PROGRESS információt ad vissza, vagy befejeződött.

## <a name="the-scaling-workflow"></a>A méretezési munkafolyamat

A méretezési művelet kezdeményezésekor akkor a rendszer először az összes megnyitott munkamenetek, konzisztens állapotú biztosításához nyitott tranzakciók visszaállítása használhatatlanná teszi. A skálázási műveletek skálázás csak akkor történik meg a tranzakciós visszaállítás befejezése után.  

- Méretezett művelet a rendszer kiosztja a további számítási és a tárolási réteg majd reattaches. 
- Lefelé méretezéshez művelet, a felesleges csomópontok válassza le a tárhelyet, és a többi csomópontot az áttelepítést csatlakoztassa újra.

## <a name="next-steps"></a>További lépések
Tekintse meg a következő cikkek segítenek megismerni néhány további legfontosabb teljesítményi fogalmak:

* [Munkaterhelés és feldolgozási kezelése][Workload and concurrency management]
* [Tábla kialakítás áttekintése][Table design overview]
* [elosztása][Table distribution]
* [Tábla indexelő][Table indexing]
* [Táblaparticionálást][Table partitioning]
* [Tábla statisztikai adatainak][Table statistics]
* [Gyakorlati tanácsok][Best practices]

<!--Image reference-->

<!--Article references-->

[capacity limits]: ./sql-data-warehouse-service-capacity-limits.md


[Check database state with T-SQL]: ./sql-data-warehouse-manage-compute-tsql.md#check-database-state-and-operation-progress
[Check database state with PowerShell]: ./sql-data-warehouse-manage-compute-powershell.md#check-database-state
[Check database state with REST APIs]: ./sql-data-warehouse-manage-compute-rest-api.md#check-database-state

[Workload and concurrency management]: ./resource-classes-for-workload-management.md
[Table design overview]: ./sql-data-warehouse-tables-overview.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Table indexing]: ./sql-data-warehouse-tables-index.md
[Table partitioning]: ./sql-data-warehouse-tables-partition.md
[Table statistics]: ./sql-data-warehouse-tables-statistics.md
[Best practices]: ./sql-data-warehouse-best-practices.md
[development overview]: ./sql-data-warehouse-overview-develop.md

[SQL DB Contributor]:../role-based-access-control/built-in-roles.md#sql-db-contributor

<!--MSDN references-->
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Azure portal]: http://portal.azure.com/
