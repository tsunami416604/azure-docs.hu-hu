---
title: Adattárházegységek (dwu-k, cDWUs) az Azure SQL Data Warehouse |} A Microsoft Docs
description: Javaslatok az adattárházegységek (dwu-k, cDWUs) ár és a teljesítmény-, és módosítása az egységek számát az ideális számot kiválasztása.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 5f6e24dfa1b5c4ea4f0748af81104edfe88ceeae
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099103"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Az Adattárházegységek (Dwu) és számítási Adattárházegység (cDWUs)
Javaslatok az adattárházegységek (dwu-k, cDWUs) ár és a teljesítmény-, és módosítása az egységek számát az ideális számot kiválasztása. 

## <a name="what-are-data-warehouse-units"></a>Mik azok az Adattárházegységek?
Az SQL Data Warehouse CPU, memória és IO vannak csoportosítva az Adattárházegységek (Dwu) nevű számítási skálázási egységet. A DWU-absztrakt, normalizált mérték számítási erőforrások és a teljesítmény jelöli. A szolgáltatási szint módosításával alter a rendszerhez, amellyel módosíthatja a viszont a teljesítmény és a költség, a rendszer kiosztott dwu-k számának. 

Kell fizetnem a magasabb teljesítmény, növelheti az adattárházegységek számát. Kisebb teljesítményt kell fizetnie, csökkentse az adattárházegységek. Adattárházegységek módosítása nem érinti a tárolási költségek, tárolási és számítási költségeit külön-külön számlázzuk.

A teljesítményt az adattárházegységek data warehouse számítási feladatok metrikák alapján:

- Milyen gyors is egy standard adattárház adatlekérdezés a sok sort, és hajtsa végre a komplex összesítést készít? Tato operace je i/o- és CPU-igényes.
- Milyen gyors az adatraktár betöltheti az adatokat az Azure Storage BLOB vagy az Azure Data Lake? Tato operace je, hálózat- és CPU-igényes. 
- Milyen gyorsan lehet a [CREATE TABLE AS SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL paranccsal tábla másolása? Ez a művelet magában foglalja az adatok beolvasása a tárfiókból, szétosztása a készülék csomópontjai között és tárfiókba történő újbóli írása. Tato operace je CPU, IO és nagy számításigényű hálózati.

A dwu-k növelése:
- Lineárisan módosítja a vizsgálatokat, összesítések és a CTAS utasítások a rendszer teljesítményét
- Növeli az olvasók és a PolyBase betöltési műveletek írók számát
- Növeli a párhuzamos lekérdezések és egyidejű hely maximális számát.

## <a name="service-level-objective"></a>Szolgáltatási szint célkitűzése
A szolgáltatási szint célkitűzést (SLO) a méretezhetőség beállítása, amely meghatározza, hogy a költség- és az adatraktár szintjét. A szolgáltatási szintek, a Gen2 számítási kiosztása az adattárházegységekkel (cDWU), például DW2000c mérése történik. A dwu-k, például DW2000 Gen1 szolgáltatási szintek mérése történik.
  > [!NOTE]
  > Az Azure SQL Data Warehouse Gen2 nemrégiben kiegészítő skálázási lehetőségeket a compute csomag akár 100 cDWU is támogatja. A korábbi adattárházak jelenleg a Gen1 igénylő, az alacsonyabb rétegek számítási most már frissítheti a régióban jelenleg elérhető, további költségektől Gen2-re.  Ha még nem támogatott a régióban továbbra is frissíthet egy támogatott régióban. További információ: [frissítés Gen2-re](upgrade-to-latest-generation.md).

A T-SQL a service_objective paraméter beállítás határozza meg, a szolgáltatási szint és az adatraktár teljesítményszintjét.

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

## <a name="performance-tiers-and-data-warehouse-units"></a>Teljesítményszintek és kiosztása az Adattárházegységekkel

Mindegyik teljesítményszint egy némileg eltérő mértékegysége az adattárházegységek használ. Ez a különbség is megjelenik a számlán a skálázási egység közvetlen számlázás fog vonatkozni fordítja le.

- Az Adattárházegységek (Dwu) Gen1 data warehouse-adattárházak mérése történik.
- Gen2 data warehouse-adattárházak mérése történik, a számítási az Adattárházegységek (cDWUs). 

A dwu-k és a cDWUs támogatja méretezésű számítások felfelé és lefelé, és nem kell használnia az adattárház felfüggesztése számítási. Ezeket a műveleteket a rendszer az összes igény szerinti. Gen2 egy helyi lemez alapú gyorsítótárat használ a számítási csomópontokon teljesítmény javítása érdekében. Méretezhető vagy szüneteltetheti a rendszer, a gyorsítótár érvénytelenné válik, és így gyorsítótár előkészítése időszak előtt el kell végezni optimális teljesítmény akkor érhető el.  

Adattárházegységek növelésével, lineárisan növeli a számítási erőforrások. Gen2 a legjobb lekérdezési teljesítmény és a legmagasabb méretezési biztosít. Ezekben a rendszerekben kell használniuk a legtöbb gyorsítótár.

### <a name="capacity-limits"></a>Kapacitási korlátok
Minden SQL-kiszolgáló (például a myserver.database.windows.net) rendelkezik egy [adatbázis tranzakciós egységek (DTU)](../sql-database/sql-database-what-is-a-dtu.md) kvóta, amely lehetővé teszi, hogy egy bizonyos számú adattárházegységek. További információkért lásd: a [számítási feladat felügyeleti kapacitáskorlátait](sql-data-warehouse-service-capacity-limits.md#workload-management).


## <a name="how-many-data-warehouse-units-do-i-need"></a>Hány adattárházegységek van szükségem?
Az ideális számot adattárházegységek nagyban függ a számítási feladatok és a rendszer betöltött adatok mennyiségét.

A legjobb DWU kereséséhez a számítási feladathoz szükséges lépéseket:

1. Először egy kisebb DWU kiválasztásával. 
2. Az alkalmazás teljesítményének figyelése tesztelése adatterhelések a rendszer megfigyelte teljesítményének képest kiválasztott dwu-k számának észlelt problémát.
3. A rendszeres tevékenységcsúcsokat azonosítsa az esetleges további követelményeket. Ha a munkaterhelés jelentős csúcsok és csatornák mutatja a tevékenység, és csak jó okkal gyakran méretezését.

Az SQL Data Warehouse kibővített rendszer építhető ki a nagy mennyiségű számítási és a lekérdezés jelentős mennyiségű adatot. Lehetőségeit méretezését, különösen nagyobb dwu-k mellett azt javasoljuk az adatkészlet skálázásra, méretezhető, gondoskodjon arról, hogy elég adatot ahhoz, hogy a CPU-hírcsatorna. Méretezési csoport teszteléséhez, azt javasoljuk, legalább 1 TB.

> [!NOTE]
>
> A lekérdezési teljesítmény csak további folyamatokkal növeli, ha a munkahelyi oszthatóak számítási csomópontok. Ha azt tapasztalja, hogy méretezés nem módosul a teljesítményt, szükség lehet a Táblatervezés és/vagy a Lekérdezések finomhangolása. A lekérdezés finomhangolási útmutató, lásd: [felhasználói lekérdezések kezelése](sql-data-warehouse-overview-manage-user-queries.md). 

## <a name="permissions"></a>Engedélyek

Az ismertetett engedélyekkel módosítja az adattárházegységek kell [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql). 

Az Azure-erőforrások, például az SQL DB Contributor és az SQL Server Közreműködője beépített szerepkörök DWU beállításait módosíthatja. 

## <a name="view-current-dwu-settings"></a>Aktuális nézet DWU-beállítást

Az aktuális DWU-beállítás megtekintése:

1. Nyissa meg az SQL Server Object Explorerben a Visual Studióban.
2. Csatlakozzon a master adatbázishoz tartozó SQL Database logikai kiszolgáló.
3. Válassza ki a sys.database_service_objectives dinamikus felügyeleti nézetből. Például: 

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
A dwu-k vagy cDWUs módosítása:

1. Nyissa meg a [az Azure portal](https://portal.azure.com), nyissa meg az adatbázishoz, és kattintson a **méretezési**.

2. A **méretezési**, a csúszka bal vagy jobb gombbal a DWU-beállítást.

3. Kattintson a **Save** (Mentés) gombra. Ekkor megjelenik egy megerősítő üzenet. Kattintson az **igen** gombra a megerősítéshez vagy a **nem** gombra az elvetéshez.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A dwu-k vagy cDWUs módosításához használja a [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell-parancsmagot. A következő példa a szolgáltatási szint célkitűzésének dw1000 értékre állítja a MyServer kiszolgáló üzemeltetett MySQLDW adatbázishoz.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

További információkért lásd: [PowerShell-parancsmagok az SQL Data warehouse-hoz](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL
T-SQL az aktuális DWU vagy cDWU-beállítások megtekintése, módosítsa a beállításokat, és ellenőrizze a folyamat állapotát. 

A dwu-k vagy cDWUs módosítása:

1. Csatlakozzon a master adatbázisban az SQL Database logikai kiszolgálóhoz társított.
2. Használja a [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) TSQL utasítást. Az alábbi példa a szolgáltatási szint célkitűzésének beállítja az adatbázis MySQLDW dw1000 értékre. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>REST API-k

A dwu-k módosításához használja a [létrehozás vagy frissítés adatbázis](/rest/api/sql/databases/createorupdate) REST API-t. A következő példa a szolgáltatási szint célkitűzésének dw1000 értékre állítja az adatbázis MySQLDW, amely a MyServer kiszolgáló üzemel. A kiszolgáló egy Azure-erőforráscsoportot ResourceGroup1 szerepel.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

REST API-val kapcsolatos további példákért lásd [REST API-k, az SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>DWU-módosítások állapotának ellenőrzése

DWU-módosítások több percet is igénybe vehet. Ha az automatikus méretezés, vegye fontolóra logikát, győződjön meg arról, hogy bizonyos műveletek befejeződtek-e egy másik művelet végrehajtása előtt. 

Az adatbázis állapotát a különböző végpontok ellenőrzése lehetővé teszi, hogy megfelelően implementálását az automation. A portál értesítési befejezését követően egy művelet és az adatbázisok aktuális állapot biztosít, de nem engedélyezi a programozott-ellenőrzési állapot. 

Az adatbázis állapotát, és az Azure portal horizontális felskálázási műveletek nem tudja ellenőrizni.

A DWU-módosítások állapotának ellenőrzéséhez:

1. Csatlakozzon a master adatbázisban az SQL Database logikai kiszolgálóhoz társított.
2. A következő lekérdezést küld az adatbázis állapotának ellenőrzéséhez.


```sql
SELECT    *
FROM      sys.databases
;
```

1. Küldje el a következő lekérdezést a művelet állapotának ellenőrzése

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

A DMV ad vissza információkat különböző felügyeleti műveleteket az SQL Data Warehouse, a művelet és az állapotát a művelet, amely kisebb, mint például a in_progress, vagy COMPLETED.

## <a name="the-scaling-workflow"></a>A méretezési munkafolyamat

A skálázási művelet kezdeményezésekor a rendszer először megszakítja minden nyitott munkamenet, konzisztens állapotú biztosításához nyílt tranzakciók visszaállítása. A méretezési műveletek méretezés csak akkor történik meg a tranzakciós visszaállítás befejezése után.  

- Vertikális felskálázás művelet esetén a rendszer kiosztja a további számítási és a tárolási réteg majd újracsatlakoztatja. 
- Skálázási művelet esetén a felesleges csomópontokat válassza le a storage-ból, és csatlakoztassa újból a többi csomópontot az.

## <a name="next-steps"></a>További lépések
A teljesítmény kezelése kapcsolatos további információkért lásd: [erőforrásosztályok számítási feladatok kezeléséhez](resource-classes-for-workload-management.md) és [memória- és egyidejűségi korlátok](memory-and-concurrency-limits.md).



