---
title: Adattárházegységek (dwu-k, cDWUs) az Azure SQL Data Warehouse |} Microsoft Docs
description: Javaslatok az adattárházegységek (dwu-k, cDWUs) ára és teljesítménye és egységek számának módosítása optimalizálása ideális számának kiválasztása.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 94791e4dc3d3c841dde4685d34d4e3fdaf7d9af7
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Adattárházegységek (dwu-k) és a számítás Adattárházegységek (cDWUs)
Javaslatok az adattárházegységek (dwu-k, cDWUs) ára és teljesítménye és egységek számának módosítása optimalizálása ideális számának kiválasztása. 

## <a name="what-are-data-warehouse-units"></a>Mik azok a Adattárházegységek?
SQL Data Warehouse Processzor, memória és I/O vannak becsomagolva egységekbe számítási skála Adattárházegységek (dwu-k) néven. A DWU egy absztrakt, normalizált mértéket számítási erőforrásokat és a teljesítmény jelöli. A szolgáltatási szint módosításával módosítható a rendszerben, ami viszont beállítja a teljesítmény és a költség, a rendszer számára kiosztott dwu-k száma. 

Nagy nagyobb teljesítményt, adattárházegységek száma növelhető. Kevesebb teljesítmény fizet, csökkentse az adattárházegységek. Tárolási és számítási költségek külön-külön számlázzuk, adattárházegységek módosítása nem befolyásolja a tárolási költségeket.

Teljesítményt az adattárházegységek alapján történik a data warehouse munkaterhelési mérőszámokat:

- Milyen gyors is szabványos vámraktározási adatlekérdezés vizsgálata nagy mennyiségű sort és ezután hajtja végre komplex összesítést készít? Ez a művelet i/o- és a CPU-intenzív esetében.
- Milyen gyors fogadására képes, az adatraktár adatait az Azure Storage blobs szolgáltatásban vagy az Azure Data Lake? Ez a művelet a hálózati és a CPU-intenzív esetében. 
- Milyen gyorsan lehet a [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL-parancsot, másolja át egy táblázatot? Ez a művelet adatolvasás a tárolóból, szétosztása a készülék csomópontjai között, és ismét rögzíti az tárolási magában foglalja. Ez a művelet CPU IO és intenzív hálózati esetében.

Dwu-k növelése:
- Lineárisan módosítja a rendszer a vizsgálatokat, összesítések és CTAS utasítások
- Az olvasók és a PolyBase-betöltési műveleteihez írók száma
- Növeli a párhuzamos lekérdezések és feldolgozási üzembe helyezési ponti maximális számát.

## <a name="service-level-objective"></a>Szolgáltatási szint célkitűzésének
A szolgáltatási szint célkitűzést (SLO), a méretezhetőség beállítás határozza meg az adatraktár költségeket és a teljesítmény szintjét. A szolgáltatási szintjeit Gen2 értékek mérési egysége a számítási adattárházegységek (cDWU), például DW2000c. Szolgáltatási szintek Gen1 értékek mérési egysége a dwu-k, például DW2000. 

T-SQL-ben a SERVICE_OBJECTIVE beállítás határozza meg, a szolgáltatási szint és az adatraktár teljesítményének rétegét.

```sql
--Gen1
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Gen2
CREATE DATABASE myComputeSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Teljesítmény szinteket, és Adattárházegységek

Minden egyes teljesítményszinttel némileg eltérő mértékegységet az adattárházegységek használ. Ezt a különbséget a számlán is megjelenik, a méretezési egység közvetlenül a számlázási rendszerhez.

- Az adatraktárak Gen1 értékek mérése Adattárházegységek (dwu-k).
- Gen2 adatok warehousesr értékek mérési egysége a számítási Adattárházegységek (cDWUs). 

Dwu-k és cDWUs egyaránt támogatja méretezési számítási felfelé vagy lefelé, és számítási felfüggesztése, ha nem szeretné használni, az adatraktár. Ezeket a műveleteket az összes igény szerinti. Gen2 a számítási csomópontok helyi lemezalapú gyorsítótárban teljesítmény javítása érdekében használja. Méretezhető, illetve a rendszer felfüggeszti a gyorsítótárat érvénytelenítik, és így gyorsítótár felmelegedés időszak kötelező, mielőtt az optimális teljesítmény akkor érhető el.  

Adattárházegységek növelésével számítási erőforrások lineárisan növekednek. Gen2 nyújtja a legjobb lekérdezési teljesítmény és a legmagasabb méretezési, de magasabb bejegyzés árat van. A teljesítmény állandó igény szerinti rendelkező vállalatok számára készült. Ezek a rendszerek kell használniuk a legtöbb a gyorsítótár. 

### <a name="capacity-limits"></a>Kapacitási korlátok
Minden egyes SQL server (például myserver.database.windows.net) rendelkezik egy [Database Transaction Unit (DTU)](../sql-database/sql-database-what-is-a-dtu.md) , amely lehetővé teszi, hogy egy bizonyos számú adattárházegységek kvótát. További információkért lásd: a [munkaterhelés felügyeleti kapacitáskorlátait](sql-data-warehouse-service-capacity-limits.md#workload-management).


## <a name="how-many-data-warehouse-units-do-i-need"></a>Hány adattárházegységek kell?
Az ideális száma adattárházegységek függ túlságosan azok a számítási feladatok és a rendszerbe betöltött adatok mennyiségét.

A legjobb DWU kereséséhez a munkaterheléshez lépéseket:

1. Először egy kisebb DWU kiválasztása. 
2. Az alkalmazások teljesítményéről, tesztelje adatbetöltés a rendszerbe betartásával erőforrásigények teljesítéséhez képest választott dwu-k számának figyelése
3. Azonosítsa a esetleges további követelményeket csúcs tevékenység rendszeres időszakokra. Ha a munkaterhelés jelentős csúcsait és csatornák megjelenő tevékenység, és egy jó ok arra, hogy gyakran méretezni.

Az SQL Data Warehouse kibővített rendszer építhető ki a számítási és a lekérdezés jelentős mennyiségű adatok óriási mennyiségben. Igaz platformképességei méretezéshez, különösen a nagyobb dwu-k, lásd: javasoljuk az adatkészlet skálázás, annak érdekében, hogy kevés az adat a hírcsatorna a processzorok méretezni. A skála vizsgálat, azt javasoljuk, legalább 1 TB.

> [!NOTE]
>
> Lekérdezési teljesítmény csak további párhuzamos folyamatkezelést biztosítja a növekszik, ha a munkahelyi oszthatók számítási csomópontjai között. Ha talál meg, hogy skálázás nem módosítja a teljesítményt, szükség lehet a Táblatervezés és/vagy a lekérdezések hangolását. A lekérdezés hangolása útmutatást, lásd: [felhasználói lekérdezések kezelése](sql-data-warehouse-overview-manage-user-queries.md). 

## <a name="permissions"></a>Engedélyek

A adattárházegységek módosításához szükséges a leírt engedélyek [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql). 

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

3. Kattintson a **Save** (Mentés) gombra. Ekkor megjelenik egy megerősítő üzenet. Kattintson az **igen** gombra a megerősítéshez vagy a **nem** gombra az elvetéshez.

### <a name="powershell"></a>PowerShell
A dwu-k vagy cDWUs módosításához használja a [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) PowerShell-parancsmagot. Az alábbi példa állítja be a szolgáltatási szint célkitűzés DW1000 MySQLDW a MyServer kiszolgáló tárolt adatbázis.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

További információkért lásd: [az SQL Data Warehouse PowerShell-parancsmagjai](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL
A T-SQL aktuális DWU vagy cDWU beállítások megtekintése, módosítsa a beállításokat, majd a folyamat állapotának ellenőrzése. 

A dwu-k vagy cDWUs módosítása:

1. A fő adatbázist az SQL Database logikai kiszolgálóhoz csatlakozni.
2. Használja a [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) TSQL utasítást. Az alábbi példa állítja be a szolgáltatási szint célkitűzés DW1000 az adatbázis MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>REST API-k

A dwu-k módosításához használja a [létrehozás vagy frissítés adatbázis](/rest/api/sql/databases/createorupdate) REST API-t. Az alábbi példa állítja be a szolgáltatási szint célkitűzés DW1000 az adatbázis MySQLDW, amely a MyServer kiszolgáló üzemelteti. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforrás-csoportban.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

További REST API-t, tekintse meg a [REST API-k, az SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

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
A teljesítmény kezelése kapcsolatos további információkért lásd: [erőforrás-osztályok a munkaterhelés felügyeleti](resource-classes-for-workload-management.md) és [memória és a feldolgozási korlátok](memory-and-concurrency-limits.md).



