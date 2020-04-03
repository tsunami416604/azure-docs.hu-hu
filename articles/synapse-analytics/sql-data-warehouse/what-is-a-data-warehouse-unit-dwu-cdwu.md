---
title: Adattárház-egységek (DW-k) az Azure Synapse Analytics (korábban SQL DW)
description: Javaslatok az adattárház egységek (DW-k) ideális számának kiválasztására az ár és a teljesítmény optimalizálása érdekében, valamint az egységek számának módosításához.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 11/22/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: ace4bc2e46d9e1926da18dedb163657d4f343979
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586314"
---
# <a name="data-warehouse-units-dwus"></a>Adattárház-egységek (DWUs-ok)

Javaslatok az adattárház egységek (DW-k) ideális számának kiválasztására az ár és a teljesítmény optimalizálása érdekében, valamint az egységek számának módosításához.

## <a name="what-are-data-warehouse-units"></a>Mik azok az adattárház-egységek?

A [szinapszis SQL-készlet](sql-data-warehouse-overview-what-is.md#synapse-sql-pool-in-azure-synapse) a kiépített analitikus erőforrások gyűjteményét jelöli. Az analitikus erőforrások a PROCESSZOR, a memória és az IO kombinációjaként vannak definiálva. Ez a három erőforrás adattárházegységeknek (DWUs) nevezett számítási méretegységekbe van csomagolva. A DWU a számítási erőforrások és teljesítmény absztrakt, normalizált mértéke. A szolgáltatási szint módosítása megváltoztatja a rendszer számára rendelkezésre álló DWUs-ok számát, ami viszont módosítja a rendszer teljesítményét és költségét.

Nagyobb teljesítmény érdekében növelheti az adattárház egységek számát. A kisebb teljesítmény érdekében csökkentse az adattárház-egységeket. A tár és a számítási feladatok költségeinek számlázása külön történik, ezért az adattárházegységek számának módosítása nem befolyásolja a tárolási költségeket.

Az adatraktár-egységek teljesítménye a következő számítási feladatok mérőszámain alapul:

- Milyen gyorsan képes egy szabványos adatraktározási lekérdezés nagy számú sort beszkapogatni, majd összetett összesítést végrehajtani. Ez a művelet I/O és CPU-igényes.
- Milyen gyorsan tudja az adattárház adatokat betöltése az Azure Storage Blobs vagy az Azure Data Lake. Ez a művelet hálózati és CPU-igényes.
- Milyen gyorsan másolhat egy táblát a [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) T-SQL parancs. Ez a művelet magában foglalja az adatok olvasása a tárolóból, terjesztése a készülék csomópontjai között, és írásban a tároló ba újra. Ez a művelet processzor-, IO- és hálózatigényes.

Növekvő DMU-k:

- Lineárisan módosítja a rendszer teljesítményét a beolvasások, összesítések és CTAS utasítások esetében
- Növeli az olvasók és írók számát a PolyBase betöltési műveletekhez
- Növeli az egyidejű lekérdezések és egyidejűségi tárolóhelyek maximális számát.

## <a name="service-level-objective"></a>Service Level Objective

A szolgáltatási szint célkitűzés (SLO) az adattárház költség- és teljesítményszintjét meghatározó méretezhetőségi beállítás. A Gen2 SQL-készlet szolgáltatási szintjeit adattárházegységekben (DWU) mérik, például DW2000c.

A T-SQL-ben a SERVICE_OBJECTIVE beállítás határozza meg az SQL-készlet szolgáltatási szintjét.

```sql
CREATE DATABASE mySQLDW
( EDITION = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="capacity-limits"></a>Kapacitási korlátok

Minden SQL-kiszolgáló (például myserver.database.windows.net) rendelkezik egy [adatbázis-tranzakciós egység (DTU)](../../sql-database/sql-database-service-tiers-dtu.md) kvótával, amely lehetővé teszi az adattárház egységek meghatározott számát. További információt a [munkaterhelés-kezelési kapacitáskorlátok](sql-data-warehouse-service-capacity-limits.md#workload-management)című témakörben talál.

## <a name="how-many-data-warehouse-units-do-i-need"></a>Hány adattárházegységre van szükségem

Az adatraktári egységek ideális száma nagymértékben függ a munkaterheléstől és a rendszerbe betöltött adatok mennyiségétől.

A számítási feladatokhoz legjobb DWU megtalálásának lépései:

1. Kezdje egy kisebb DWU kiválasztásával.
2. Figyelje az alkalmazás teljesítményét, miközben teszteli az adatok betöltését a rendszerbe, megfigyelve a kiválasztott DWUs-ok számát a megfigyelt teljesítményhez képest.
3. Határozza meg az időszakos csúcsidőszakokra vonatkozó további követelményeket. Előfordulhat, hogy a jelentős csúcsokat és vályúkat mutató munkaterheléseket gyakran kell méretezni.

Az SQL Analytics egy horizontális felskálázási rendszer, amely hatalmas mennyiségű számítási és lekérdezési mennyiségű adatot tud kiépíteni. A skálázás valódi képességeinek megtekintéséhez, különösen a nagyobb DW-k esetén, javasoljuk az adatkészlet méretezése méretezés közbeni méretezését annak érdekében, hogy elegendő adat elegendő adatáll-e a processzorok adagolásához. A skálavizsgálathoz legalább 1 TB-ot javasoljuk.

> [!NOTE]
>
> A lekérdezési teljesítmény csak akkor növekszik, ha a munka felosztható a számítási csomópontok között. Ha úgy találja, hogy a méretezés nem módosítja a teljesítményt, előfordulhat, hogy be kell hangolnia a tábla tervét és/vagy a lekérdezéseket. A lekérdezéshangolási útmutatásról a [Felhasználói lekérdezések kezelése című](cheat-sheet.md)témakörben talál.

## <a name="permissions"></a>Engedélyek

Az adattárház egységek módosításához az [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql)című mezőben leírt engedélyekszükségesek.

Az Azure-erőforrások, például az SQL DB Contributor és az SQL Server Contributor beépített szerepkörei módosíthatják a DWU-beállításokat.

## <a name="view-current-dwu-settings"></a>Az aktuális DWU-beállítások megtekintése

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

## <a name="change-data-warehouse-units"></a>Adatraktár-egységek módosítása

### <a name="azure-portal"></a>Azure Portal

DWUs módosítása:

1. Nyissa meg az [Azure Portalt,](https://portal.azure.com)nyissa meg az adatbázist, és kattintson a **Méretezés gombra.**

2. A **Méretezés csoportban**mozgassa a csúszkát balra vagy jobbra a DWU beállítás módosításához.

3. Kattintson a **Mentés** gombra. Ekkor megjelenik egy megerősítő üzenet. Kattintson az **igen** gombra a megerősítéshez vagy a **nem** gombra az elvetéshez.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

A DWUs-ok módosításához használja a [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) PowerShell parancsmag. A következő példa a szolgáltatásszint-célkitűzést DW1000c-re állítja a MyServer kiszolgálón üzemeltetett MySQLDW adatbázishoz.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

További információ: [PowerShell-parancsmagok az SQL Data Warehouse-hoz](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL

A T-SQL segítségével megtekintheti az aktuális DWU-beállításokat, módosíthatja a beállításokat, és ellenőrizheti a folyamatot.

A DWUs módosítása:

1. Csatlakozzon a logikai SQL-adatbázis-kiszolgálóhoz társított főadatbázishoz.
2. Használja az [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) TSQL utasítást. A következő példa a szolgáltatásszint-célkitűzést DW1000c-re állítja a MySQLDW adatbázishoz.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

### <a name="rest-apis"></a>REST API-k

A DWUs-ok módosításához használja az Adatbázis REST [létrehozása vagy frissítése](/rest/api/sql/databases/createorupdate) API-t. A következő példa a szolgáltatásszint-célkitűzést DW1000c-re állítja a MySQLDW adatbázishoz, amely a MyServer kiszolgálón található. A kiszolgáló egy ResourceGroup1 nevű Azure-erőforráscsoportban található.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000c
    }
}
```

További REST API-példákért az [SQL Data Warehouse REST API-k című témakörben talál.](sql-data-warehouse-manage-compute-rest-api.md)

## <a name="check-status-of-dwu-changes"></a>DWU-módosítások állapotának ellenőrzése

A DWU módosításai több percig is eltarthatnak. Ha automatikusan skálázódik, fontolja meg a logika megvalósítását annak érdekében, hogy bizonyos műveletek befejeződtek-e, mielőtt egy másik műveletet folytatna.

Az adatbázis állapotának különböző végpontokon keresztüli ellenőrzése lehetővé teszi az automatizálás helyes megvalósítását. A portál értesítést küld egy művelet befejezésekor, és az adatbázisok aktuális állapotát, de nem teszi lehetővé az állapot programozott ellenőrzését.

Az Azure Portalon nem ellenőrizheti az adatbázis-állapot horizontális felskálázási műveleteit.

A DWU-módosítások állapotának ellenőrzése:

1. Csatlakozzon a logikai SQL-adatbázis-kiszolgálóhoz társított főadatbázishoz.

1. Küldje el a következő lekérdezést az adatbázis állapotának ellenőrzéséhez.

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

## <a name="the-scaling-workflow"></a>A méretezési munkafolyamat

Amikor egy méretezési műveletet indít el, a rendszer először megöli az összes nyitott munkamenetet, és visszaállítja a nyitott tranzakciókat a konzisztens állapot biztosítása érdekében. Méretezési műveletek esetén a skálázás csak a tranzakciós visszaállítás befejezése után történik.  

- A felskálázási művelethez a rendszer leválasztja az összes számítási csomópontot, leadja a további számítási csomópontokat, majd újra csatolja a tárolási réteget.
- A lekicsinyítési művelethez a rendszer leválasztja az összes számítási csomópontot, majd csak a szükséges csomópontokat csatlakoztatja a tárolóréteghez.

## <a name="next-steps"></a>További lépések

A teljesítmény kezeléséről az [Erőforrásosztályok a munkaterhelés-kezeléshez,](resource-classes-for-workload-management.md) valamint a [memória- és egyidejűségi korlátok című témakörben olvashat bővebben.](memory-concurrency-limits.md)
