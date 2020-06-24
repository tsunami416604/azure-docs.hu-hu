---
title: Adatraktár-egységek (DWU-EK) az Azure szinapszis Analyticsben (korábban SQL DW)
description: Javaslatok az adatraktár-egységek (DWU) ideális számának kiválasztásához az árak és a teljesítmény optimalizálásához, valamint az egységek számának módosításához.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: e96fc9af0aa4f362256a5f0fee9465441a1c3928
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2020
ms.locfileid: "85210623"
---
# <a name="data-warehouse-units-dwus"></a>Adatraktár-egységek (DWU)

Javaslatok az adatraktár-egységek (DWU) ideális számának kiválasztásához az árak és a teljesítmény optimalizálásához, valamint az egységek számának módosításához.

## <a name="what-are-data-warehouse-units"></a>Mik az adatraktár-egységek

A [SZINAPSZIS SQL-készlet](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse) a kiépített analitikai erőforrások gyűjteményét jelöli. Az analitikai erőforrások a CPU, a memória és az IO kombinációja vannak meghatározva.

Ez a három erőforrás az adatraktár-egységek (DWU) számítási skálázási egységei között van. A DWU a számítási erőforrások és teljesítmény absztrakt, normalizált mértéke.

A szolgáltatási szint módosítása megváltoztatja a rendszer számára elérhető DWU számát, ami viszont a teljesítmény és a szolgáltatás költségeit is módosítja.

A nagyobb teljesítmény érdekében növelheti az adatraktár-egységek számát. Kevesebb teljesítmény esetén csökkentse az adatraktár-egységeket. A tár és a számítási feladatok költségeinek számlázása külön történik, ezért az adattárházegységek számának módosítása nem befolyásolja a tárolási költségeket.

Az adatraktár-egységek teljesítménye a következő adatraktár-számítási feladatok mérőszámán alapul:

- A standard SQL-készlet lekérdezésének gyorsasága nagy mennyiségű sort tud beolvasni, majd összetett összesítést hajt végre. Ez a művelet I/O-és CPU-igényes.
- Az SQL-készletnek az Azure Storage-Blobokból vagy Azure Data Lakeból való betöltésének gyorsasága. A művelet a hálózati és a CPU-igényes.
- A [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL-parancs által a tábla másolásának gyorsasága. A művelet magában foglalja az adatok tárolásból való beolvasását, a berendezés csomópontjain való terjesztését és a tárolóba való írást. Ez a művelet CPU-, IO-és hálózati igényű.

Növekvő DWU:

- A rendszer lineárisan módosítja a vizsgálatok, összesítések és CTAS utasításokhoz tartozó rendszerek teljesítményét
- Növeli az olvasók és az írók számát a kiinduló betöltési műveletekhez
- Növeli az egyidejű lekérdezések és a párhuzamossági bővítőhelyek maximális számát.

## <a name="service-level-objective"></a>Service Level Objective

A szolgáltatási szint célkitűzése (SLO) a méretezhetőségi beállítás, amely meghatározza az adattárház költségeit és teljesítményét. A Gen2 szolgáltatási szintjeit számítási adattárház-egységek (cDWU-EK) mérik, például DW2000c. A Gen1 szolgáltatási szintjei a DWU-ben vannak mérve, például DW2000.

A szolgáltatási szint célkitűzése (SLO) a méretezhetőségi beállítás, amely meghatározza az SQL-készlet költségeit és teljesítményét. A Gen2 SQL-készlet szolgáltatási szintjei az adatraktár-egységekben (DWU) mérhetőek, például DW2000c.

> [!NOTE]
> Azure SQL Data Warehouse Gen2 a közelmúltban hozzáadott további méretezési képességeket a számítási rétegek támogatásához, amely a 100-cDWU alacsony. A jelenleg a Gen1-on található meglévő adattárházak, amelyek az alacsonyabb számítási szinteket igénylik, mostantól a Gen2-ra frissíthetik azokat a régiókat, amelyek jelenleg elérhetők a további díjak nélkül.  Ha a régiója még nem támogatott, akkor továbbra is frissíthet egy támogatott régióra. További információ: [verziófrissítés a Gen2](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

A T-SQL-ben a SERVICE_OBJECTIVE beállítás határozza meg az SQL-készlet szolgáltatási szintjét és teljesítményét.

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Teljesítmény szintjei és adattárház-egységek

Minden teljesítményszint némileg eltérő mértékegységet használ az adattárház-egységek számára. Ez a különbség a számlán jelenik meg, mivel a skálázási egység közvetlenül a számlázásra van lefordítva.

- A Gen1 adattárházak mérése adatraktár-egységekben történik (DWU).
- A Gen2 adattárházak mérése számítási adattárház-egységekben történik (cDWUs).

Mind a DWU, mind a cDWUs támogatja a méretezési számítási kapacitást, és szünetelteti a számítást, ha nem kell használnia az adattárházat. Ezek a műveletek mind igény szerint használhatók. A Gen2 egy helyi lemezes gyorsítótárat használ a számítási csomópontokon a teljesítmény javítása érdekében. A rendszer skálázása vagy szüneteltetése után a gyorsítótár érvénytelenné válik, így az optimális teljesítmény elérése előtt szükség van egy gyorsítótár-felmelegedésre.  

Minden SQL-kiszolgáló (például myserver.database.windows.net) rendelkezik egy [adatbázis-tranzakciós egység (DTU)](../../azure-sql/database/service-tiers-dtu.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) kvótával, amely egy adott számú adattárház-egységet engedélyez. További információ: [munkaterhelés-kezelési kapacitás korlátai](sql-data-warehouse-service-capacity-limits.md#workload-management).

## <a name="capacity-limits"></a>Kapacitási korlátok

Minden SQL-kiszolgáló (például myserver.database.windows.net) rendelkezik egy [adatbázis-tranzakciós egység (DTU)](../../sql-database/sql-database-what-is-a-dtu.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) kvótával, amely egy adott számú adattárház-egységet engedélyez. További információ: [munkaterhelés-kezelési kapacitás korlátai](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management).

## <a name="how-many-data-warehouse-units-do-i-need"></a>Hány adatraktár-egységre van szükségem

Az adatraktár-egységek ideális száma nagy mértékben függ a munkaterheléstől és a rendszerbe betöltött adatok mennyiségétől.

A számítási feladatok legjobb DWU megkeresésének lépései:

1. Először válasszon kisebb DWU.
2. Az alkalmazások teljesítményének figyelése az adatterhelések a rendszeren való tesztelésekor, a kiválasztott DWU számának megjelölésével.
3. Azonosítsa a maximális tevékenység időszakos időszakára vonatkozó további követelményeket. Előfordulhat, hogy a jelentős csúcsokat és a tevékenységekben lévő vályúkat bemutató munkaterheléseket gyakran kell méretezni.

Az SQL Pool egy kibővíthető rendszer, amely nagy mennyiségű számítási és lekérdezési mennyiségű adatmennyiséget tud kiépíteni.

Ha szeretné megtekinteni a skálázás valódi képességeit, különösen nagyobb DWU esetében, javasoljuk, hogy az adatkészletet méretezéssel méretezheti, hogy elegendő mennyiségű adattal rendelkezzen a processzorok megadásához. A méretezési teszteléshez legalább 1 TB-ot ajánlott használni.

> [!NOTE]
>
> A lekérdezési teljesítmény csak akkor nő további párhuzamos, ha a munka felosztható a számítási csomópontok között. Ha azt tapasztalja, hogy a méretezés nem változik a teljesítményen, előfordulhat, hogy a tábla kialakítását és/vagy a lekérdezéseit is meg kell adnia. A lekérdezések hangolásával kapcsolatos útmutatásért lásd: [felhasználói lekérdezések kezelése](cheat-sheet.md).

## <a name="permissions"></a>Engedélyek

Az adatraktár-egységek módosításához az [Alter Database](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)utasításban ismertetett engedélyek szükségesek.

Az Azure-erőforrások, például az SQL-adatbázis közreműködői és SQL Server közreműködők beépített szerepkörei módosíthatják a DWU beállításait.

## <a name="view-current-dwu-settings"></a>Aktuális DWU-beállítások megtekintése

Az aktuális DWU-beállítás megtekintése:

1. Nyissa meg SQL Server Object Explorer a Visual Studióban.
2. Kapcsolódjon a logikai SQL-kiszolgálóhoz társított Master adatbázishoz.
3. Válasszon a sys. database_service_objectives dinamikus felügyeleti nézetből. Például:

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Adatraktár-egységek módosítása

### <a name="azure-portal"></a>Azure Portal

DWU módosítása:

1. Nyissa meg a [Azure Portal](https://portal.azure.com), nyissa meg az adatbázist, és kattintson a **skálázás**elemre.

2. A **skála**alatt mozgassa a csúszkát balra vagy jobbra a DWU beállítás módosításához.

3. Kattintson a **Save** (Mentés) gombra. Ekkor megjelenik egy megerősítő üzenet. Kattintson az **igen** gombra a megerősítéshez vagy a **nem** gombra az elvetéshez.

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A DWU módosításához használja a [set-AzSqlDatabase PowerShell-](/powershell/module/az.sql/set-azsqldatabase) parancsmagot. A következő példa beállítja a szolgáltatási szint célkitűzését, hogy DW1000 a Server MyServer üzemeltetett adatbázis-MySQLDW.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

További információ: PowerShell- [parancsmagok a SQL Data Warehousehoz](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="t-sql"></a>T-SQL

A T-SQL használatával megtekintheti az aktuális DWUsettings, módosíthatja a beállításokat, és megtekintheti a folyamatot.

A DWU módosítása:

1. Kapcsolódjon a kiszolgálóhoz társított Master adatbázishoz.
2. Használja az [Alter Database](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) TSQL utasítást. A következő példa a szolgáltatási szint célkitűzését állítja be a DW1000c adatbázis-MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

### <a name="rest-apis"></a>REST API-k

A DWU módosításához használja az [adatbázis létrehozása vagy frissítése](/rest/api/sql/databases/createorupdate) REST API. A következő példa a szolgáltatási szint célkitűzését állítja be a DW1000c adatbázis-MySQLDW, amely a kiszolgáló MyServer található. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforráscsoport.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

További REST API példákat a [SQL Data Warehouse REST API](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)-k című témakörben talál.

## <a name="check-status-of-dwu-changes"></a>DWU-változások állapotának ellenõrzése

A DWU módosításai több percet is igénybe vehetnek. Ha automatikusan méretezést végez, érdemes megfontolnia a logikát, hogy a művelet végrehajtása előtt bizonyos műveleteket végre lehessen hajtani.

Az adatbázis állapotának ellenőrzése különböző végpontokon keresztül lehetővé teszi az automatizálás megfelelő megvalósítását. A portál értesítést küld egy művelet és az adatbázisok aktuális állapotának befejezéséről, de nem teszi lehetővé az állapot programozott ellenőrzését.

A kibővíthető műveletekhez tartozó adatbázis-állapot nem ellenőrizhető a Azure Portal.

A DWU változásainak állapotának ellenõrzése:

1. Kapcsolódjon a kiszolgálóhoz társított Master adatbázishoz.
2. Az adatbázis állapotának vizsgálatához küldje el a következő lekérdezést.

```sql
SELECT    *
FROM      sys.databases
;
```

1. A művelet állapotának vizsgálatához küldje el a következő lekérdezést

    ```sql
    SELECT    *
    FROM      sys.dm_operation_status
    WHERE     resource_type_desc = 'Database'
    AND       major_resource_id = 'MySQLDW'
    ;
    ```

Ez a DMV az SQL-készlet különböző felügyeleti műveleteivel, például a művelettel és a művelet állapotával kapcsolatos információkat ad vissza, amely vagy IN_PROGRESS vagy kész.

## <a name="the-scaling-workflow"></a>A skálázási munkafolyamat

Amikor elindít egy méretezési műveletet, a rendszer először az összes nyitott munkamenetet megöli, és minden nyitott tranzakciót visszagörget, hogy konzisztens állapotot biztosítson. A skálázási műveletek esetében a méretezés csak a tranzakciós visszaállítás befejeződése után történik meg.  

- A skálázási műveletek esetében a rendszer leválasztja az összes számítási csomópontot, kiépíti a további számítási csomópontokat, majd újracsatlakoztatja a tárolási réteghez.
- A leskálázási művelethez a rendszer leválasztja az összes számítási csomópontot, majd csak a szükséges csomópontokat csatlakoztatja a tárolási réteghez.

## <a name="next-steps"></a>További lépések

A teljesítmény kezelésével kapcsolatos további információkért lásd: [erőforrás-osztályok a számítási feladatok kezeléséhez](resource-classes-for-workload-management.md) , valamint a [memória és a Egyidejűség korlátai](memory-concurrency-limits.md).
