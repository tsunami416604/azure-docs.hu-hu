---
title: A Synapse Analytics SQL-erőforrás-felhasználása
description: Ismerje meg a Synapse SQL-felhasználási modellek et az Azure Synapse Analytics szolgáltatásban.
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e078893b3bbe0ef5661cd87bad62b320f78ceb5d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424859"
---
# <a name="azure-synapse-analytics-sql-resource-consumption"></a>Az Azure Synapse Analytics SQL-erőforrás-felhasználása

Ez a cikk a Synapse SQL (előzetes verzió) erőforrás-felhasználási modelljeit ismerteti.

## <a name="sql-on-demand"></a>SQL igény szerint

SQL on-demand egy lekérdezési szolgáltatásonkénti fizetés, amely nem igényli a megfelelő méret kiválasztásához. A rendszer automatikusan alkalmazkodik az Ön igényeihez, megszabadítva önt az infrastruktúra kezelésétől és a megoldás megfelelő méretének kiválasztásától.

## <a name="sql-pool---data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>SQL készlet - Adattárház egységek (DWUs) és számítási adattárház egységek (cDWUs)

Javaslatok az adattárház egységek (DW-k) ideális számának kiválasztására az ár és a teljesítmény optimalizálása érdekében, valamint az egységek számának módosításához.

### <a name="what-are-data-warehouse-units"></a>Mik azok az adattárház-egységek?

A szinapszis SQL-készlet a kiépített analitikus erőforrások gyűjteményét jelöli. Az analitikus erőforrások a PROCESSZOR, a memória és az IO kombinációjaként vannak definiálva. Ez a három erőforrás adattárházegységeknek (DWUs) nevezett számítási méretegységekbe van csomagolva. A DWU a számítási erőforrások és teljesítmény absztrakt, normalizált mértéke. A szolgáltatási szint módosítása megváltoztatja a rendszer számára rendelkezésre álló DWUs-ok számát, ami viszont módosítja a rendszer teljesítményét és költségét.

Nagyobb teljesítmény érdekében növelheti az adattárház egységek számát. A kisebb teljesítmény érdekében csökkentse az adattárház-egységeket. A tár és a számítási feladatok költségeinek számlázása külön történik, ezért az adattárházegységek számának módosítása nem befolyásolja a tárolási költségeket.

Az adatraktár-egységek teljesítménye az adatraktári munkaterhelési metrikákon alapul:

- Milyen gyorsan képes egy szabványos adatraktározási lekérdezés nagy számú sort beszkapogatni, majd összetett összesítést végrehajtani. Ez a művelet I/O és CPU-igényes.
- Milyen gyorsan tudja az adattárház adatokat betöltése az Azure Storage Blobs vagy az Azure Data Lake. Ez a művelet hálózati és CPU-igényes.
- Milyen gyorsan másolhat egy táblát a [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL parancs. Ez a művelet magában foglalja az adatok olvasása a tárolóból, terjesztése a készülék csomópontjai között, és írásban a tároló ba újra. Ez a művelet processzor-, IO- és hálózatigényes.

Növekvő DMU-k:

- Lineárisan módosítja a rendszer teljesítményét a beolvasások, összesítések és CTAS utasítások esetében
- Növeli az olvasók és írók számát a PolyBase betöltési műveletekhez
- Növeli az egyidejű lekérdezések és egyidejűségi tárolóhelyek maximális számát.

### <a name="service-level-objective"></a>Service Level Objective

A szolgáltatási szint célkitűzés (SLO) az adattárház költség- és teljesítményszintjét meghatározó méretezhetőségi beállítás. A Gen2 szervizszintjeit számítási adattárház egységekben (cDWU) mérik, például DW2000c. A Gen1 szolgáltatásszinteket DWUs-okban mérik, például DW2000-ben.

A szolgáltatási szint célkitűzés (SLO) az adattárház költség- és teljesítményszintjét meghatározó méretezhetőségi beállítás. A Gen2 SQL-készlet szolgáltatási szintjeit adattárházegységekben (DWU) mérik, például DW2000c.

> [!NOTE]
> Az Azure SQL Data Warehouse Gen2 nemrégiben további méretezési képességekkel egészítettki a számítási szintek 100 cDWU-ig terjedő támogatásához. A Gen1-en jelenleg meglévő, alacsonyabb számítási szinteket igénylő adattárházak mostantól további költségek nélkül frissíthetnek a Gen2-re azokban a régiókban, amelyek jelenleg elérhetők.  Ha a régió még nem támogatott, továbbra is frissíthet egy támogatott régióra. További információt a [Frissítés a Gen2 rendszerre](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)című témakörben talál.

A T-SQL-ben a SERVICE_OBJECTIVE beállítás határozza meg az SQL-készlet szolgáltatási szintjét és teljesítményszintjét.

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

### <a name="performance-tiers-and-data-warehouse-units"></a>Teljesítményszintek és adattárházegységek

Minden egyes teljesítményszint kissé eltérő mértékegységet használ az adattárházegységeikhez. Ez a különbség tükröződik a számlán, mivel a skálázási egység közvetlenül fordítja a számlázás.

- A Gen1 adatraktárakat adatraktáregységekben (DWUs) mérik.
- A Gen2 adatraktárakat számítási adattárházegységekben (cDWUs) mérik.

Mind a DWUs-ok, mind a cDWUs-ok támogatják a számítási skálázást fel- vagy leskálázást, és szüneteltetik a számítást, ha nem kell használnia az adatraktárt. Ezek a műveletek mind igény szerint. A Gen2 helyi lemezalapú gyorsítótárat használ a számítási csomópontokon a teljesítmény javítása érdekében. A rendszer méretezésekor vagy szüneteltetésekor a gyorsítótár érvénytelenné válik, így az optimális teljesítmény elérése előtt szükség van a gyorsítótár felmelegedésének időszakára.  

Az adattárház-egységek növelésével lineárisan növeli a számítási erőforrásokszámát. A Gen2 biztosítja a legjobb lekérdezési teljesítményt és a legmagasabb skálát. A Gen2 rendszerek is a legtöbbet használják ki a gyorsítótárból.

#### <a name="capacity-limits"></a>Kapacitási korlátok

Minden SQL-kiszolgáló (például myserver.database.windows.net) rendelkezik egy [adatbázis-tranzakciós egység (DTU)](../../sql-database/sql-database-service-tiers-dtu.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) kvótával, amely lehetővé teszi az adattárház egységek meghatározott számát. További információt a [munkaterhelés-kezelési kapacitáskorlátok](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management)című témakörben talál.

### <a name="how-many-data-warehouse-units-do-i-need"></a>Hány adattárházegységre van szükségem

Az adatraktári egységek ideális száma nagymértékben függ a munkaterheléstől és a rendszerbe betöltött adatok mennyiségétől.

A számítási feladatokhoz legjobb DWU megtalálásának lépései:

1. Kezdje egy kisebb DWU kiválasztásával.
2. Figyelje az alkalmazás teljesítményét, miközben teszteli az adatok betöltését a rendszerbe, megfigyelve a kiválasztott DWUs-ok számát a megfigyelt teljesítményhez képest.
3. Határozza meg az időszakos csúcsidőszakokra vonatkozó további követelményeket. Előfordulhat, hogy a jelentős csúcsokat és vályúkat mutató munkaterheléseket gyakran kell méretezni.

Az SQL-készlet egy horizontális felskálázási rendszer, amely hatalmas mennyiségű számítási és lekérdezési mennyiségű adatot tud kiépíteni. A skálázás valódi képességeinek megtekintéséhez, különösen a nagyobb DW-k esetén, javasoljuk az adatkészlet méretezése méretezés közbeni méretezését annak érdekében, hogy elegendő adat elegendő adatáll-e a processzorok adagolásához. A skálavizsgálathoz legalább 1 TB-ot javasoljuk.

> [!NOTE]
>
> A lekérdezési teljesítmény csak akkor növekszik, ha a munka felosztható a számítási csomópontok között. Ha úgy találja, hogy a méretezés nem módosítja a teljesítményt, előfordulhat, hogy be kell hangolnia a tábla tervét és/vagy a lekérdezéseket. A lekérdezéshangolási útmutatásról a [Felhasználói lekérdezések kezelése című](../overview-cheat-sheet.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)témakörben talál.

### <a name="permissions"></a>Engedélyek

Az adattárház egységek módosításához az [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)című mezőben leírt engedélyekszükségesek.

Az Azure-erőforrások, például az SQL DB Contributor és az SQL Server Contributor beépített szerepkörei módosíthatják a DWU-beállításokat.

#### <a name="view-current-dwu-settings"></a>Az aktuális DWU-beállítások megtekintése

Az aktuális DWU-beállítás megtekintése:

1. Nyissa meg az SQL Server Object Explorer t a Visual Studióban.
2. Csatlakozzon a logikai SQL-adatbázis-kiszolgálóhoz társított főadatbázishoz.
3. Válasszon a sys.database_service_objectives dinamikus felügyeleti nézetből. Például:

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

### <a name="change-data-warehouse-units"></a>Adatraktár-egységek módosítása

#### <a name="azure-portal"></a>Azure Portal

DWUs módosítása:

1. Nyissa meg az [Azure Portalt,](https://portal.azure.com)nyissa meg az adatbázist, és kattintson a **Méretezés gombra.**

2. A **Méretezés csoportban**mozgassa a csúszkát balra vagy jobbra a DWU beállítás módosításához.

3. Kattintson a **Save** (Mentés) gombra. Ekkor megjelenik egy megerősítő üzenet. Kattintson az **igen** gombra a megerősítéshez vagy a **nem** gombra az elvetéshez.

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A DWUs-ok módosításához használja a [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) PowerShell parancsmag. A következő példa a szolgáltatásszint-célkitűzést DW1000-re állítja a MyServer kiszolgálón üzemeltetett MySQLDW adatbázishoz.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

További információ: [PowerShell-parancsmagok az SQL Data Warehouse-hoz](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

#### <a name="t-sql"></a>T-SQL

A T-SQL segítségével megtekintheti az aktuális DWU-beállításokat, módosíthatja a beállításokat, és ellenőrizheti a folyamatot.

A DWUs módosítása:

1. Csatlakozzon a logikai SQL-adatbázis-kiszolgálóhoz társított főadatbázishoz.
2. Használja az [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) TSQL utasítást. A következő példa a szolgáltatásszint-célkitűzést DW1000c-re állítja a MySQLDW adatbázishoz.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

#### <a name="rest-apis"></a>REST API-k

A DWUs-ok módosításához használja az Adatbázis REST [létrehozása vagy frissítése](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) API-t. A következő példa a szolgáltatásszint-célkitűzést DW1000c-re állítja a MySQLDW adatbázishoz, amely a MyServer kiszolgálón található. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforráscsoportban található.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

További REST API-példákért az [SQL Data Warehouse REST API-k című témakörben talál.](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="check-status-of-dwu-changes"></a>DWU-módosítások állapotának ellenőrzése

A DWU módosításai több percig is eltarthatnak. Ha automatikusan skálázódik, fontolja meg a logika megvalósítását annak érdekében, hogy bizonyos műveletek befejeződtek-e, mielőtt egy másik műveletet folytatna.

Az adatbázis állapotának különböző végpontokon keresztüli ellenőrzése lehetővé teszi az automatizálás helyes megvalósítását. A portál értesítést küld egy művelet befejezésekor, és az adatbázisok aktuális állapotát, de nem teszi lehetővé az állapot programozott ellenőrzését.

Az Azure Portalon nem ellenőrizheti az adatbázis-állapot horizontális felskálázási műveleteit.

A DWU-módosítások állapotának ellenőrzése:

1. Csatlakozzon a logikai SQL-adatbázis-kiszolgálóhoz társított főadatbázishoz.
2. Küldje el a következő lekérdezést az adatbázis állapotának ellenőrzéséhez.

```sql
SELECT    *
FROM      sys.databases
;
```

1. A következő lekérdezés elküldése a művelet állapotának ellenőrzéséhez

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Ez a dmv az SQL-készlet különböző felügyeleti műveleteiről ad vissza információkat, például a műveletről és a művelet állapotáról, amely IN_PROGRESS vagy befejeződött.

### <a name="the-scaling-workflow"></a>A méretezési munkafolyamat

Amikor egy méretezési műveletet indít el, a rendszer először megöli az összes nyitott munkamenetet, és visszaállítja a nyitott tranzakciókat a konzisztens állapot biztosítása érdekében. Méretezési műveletek esetén a skálázás csak a tranzakciós visszaállítás befejezése után történik.  

- A felskálázási művelethez a rendszer leválasztja az összes számítási csomópontot, leadja a további számítási csomópontokat, majd újra csatolja a tárolási réteget.
- A lekicsinyítési művelethez a rendszer leválasztja az összes számítási csomópontot, majd csak a szükséges csomópontokat csatlakoztatja a tárolóréteghez.

## <a name="next-steps"></a>További lépések

A teljesítmény kezeléséről az [Erőforrásosztályok a munkaterhelés-kezeléshez,](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) valamint a [memória- és egyidejűségi korlátok című témakörben olvashat bővebben.](../sql-data-warehouse/memory-concurrency-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
