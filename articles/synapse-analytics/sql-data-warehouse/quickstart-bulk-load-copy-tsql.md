---
title: 'Rövid útmutató: adatok tömeges betöltése egyetlen T-SQL-utasítás használatával'
description: Adatok tömeges betöltése a COPY utasítás használatával
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 06/18/2020
ms.author: kevin
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: f82bedc6ef638714b2641003e8274c2024a86c2e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "85213006"
---
# <a name="quickstart-bulk-load-data-using-the-copy-statement"></a>Gyors útmutató: adatok tömeges betöltése a COPY utasítás használatával

Ebben a rövid útmutatóban az adatok tömeges betöltését az SQL-készletbe az egyszerű és rugalmas [másolási utasítással](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) végezheti el a nagy átviteli sebességű adatfeldolgozáshoz. A MÁSOLÁSi utasítás az ajánlott betöltési segédprogram, amely lehetővé teszi az adatok zökkenőmentes és rugalmas betöltését a következő funkciókkal:

- Az alacsonyabb jogosultságú felhasználók betöltésének engedélyezése anélkül, hogy szigorú VEZÉRLÉSi engedélyekre lenne szükség az adatraktárban
- Csak egyetlen T-SQL-utasítást használhat fel anélkül, hogy további adatbázis-objektumokat kellene létrehoznia
- Finomabb engedélyezési modell kihasználása anélkül, hogy a Storage-fiókok kulcsait a megosztási hozzáférési aláírások (SAS) használatával tegye közzé
- Másik Storage-fiók megadása a ERRORFILE helyéhez (REJECTED_ROW_LOCATION)
- Az egyes célértékek alapértelmezett értékeinek testreszabása, valamint a forrásadatok mezőinek megadása adott cél oszlopokra való betöltéshez
- Egyéni sor lezárójának megadása CSV-fájlokhoz
- A CSV-fájlokhoz tartozó Escape-karakterláncok, mezők és sorok elhatárolói
- A CSV-fájlok SQL Server formátumának kihasználása
- Helyettesítő karakterek és több fájl megadása a tárolási hely elérési útján

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató azt feltételezi, hogy már rendelkezik SQL-készlettel. Ha nem hozott létre SQL-készletet, használja a [create és a összekapcsolás-Portal](create-data-warehouse-portal.md) rövid útmutatót.

## <a name="set-up-the-required-permissions"></a>A szükséges engedélyek beállítása

```sql
-- List the permissions for your user
select  princ.name
,       princ.type_desc
,       perm.permission_name
,       perm.state_desc
,       perm.class_desc
,       object_name(perm.major_id)
from    sys.database_principals princ
left join
        sys.database_permissions perm
on      perm.grantee_principal_id = princ.principal_id
where name = '<yourusername>';

--Make sure your user has the permissions to CREATE tables in the [dbo] schema
GRANT CREATE TABLE TO <yourusername>;
GRANT ALTER ON SCHEMA::dbo TO <yourusername>;

--Make sure your user has ADMINISTER DATABASE BULK OPERATIONS permissions
GRANT ADMINISTER DATABASE BULK OPERATIONS TO <yourusername>

--Make sure your user has INSERT permissions on the target table
GRANT INSERT ON <yourtable> TO <yourusername>

```

## <a name="create-the-target-table"></a>A céltábla létrehozása

Ebben a példában az adatokat a New York-i taxi adatkészletből fogjuk betölteni. Betöltünk egy "trip" nevű táblázatot, amely egy adott évben elkészített taxis utakat jelöl. A tábla létrehozásához futtassa a következő parancsot:

```sql
CREATE TABLE [dbo].[Trip]
(
    [DateID] int NOT NULL,
    [MedallionID] int NOT NULL,
    [HackneyLicenseID] int NOT NULL,
    [PickupTimeID] int NOT NULL,
    [DropoffTimeID] int NOT NULL,
    [PickupGeographyID] int NULL,
    [DropoffGeographyID] int NULL,
    [PickupLatitude] float NULL,
    [PickupLongitude] float NULL,
    [PickupLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [DropoffLatitude] float NULL,
    [DropoffLongitude] float NULL,
    [DropoffLatLong] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [PassengerCount] int NULL,
    [TripDurationSeconds] int NULL,
    [TripDistanceMiles] float NULL,
    [PaymentType] varchar(50) COLLATE SQL_Latin1_General_CP1_CI_AS NULL,
    [FareAmount] money NULL,
    [SurchargeAmount] money NULL,
    [TaxAmount] money NULL,
    [TipAmount] money NULL,
    [TollsAmount] money NULL,
    [TotalAmount] money NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN,
    CLUSTERED COLUMNSTORE INDEX
);
```

## <a name="run-the-copy-statement"></a>A COPY utasítás futtatása

Futtassa az alábbi MÁSOLÁSi utasítást, amely az Azure Blob Storage-fiókból származó adatok betöltését az utazási táblába fogja betölteni.

```sql
COPY INTO [dbo].[Trip] FROM 'https://nytaxiblob.blob.core.windows.net/2013/Trip2013/'
WITH (
   FIELDTERMINATOR='|',
   ROWTERMINATOR='0x0A'
) OPTION (LABEL = 'COPY: dbo.trip');
```

## <a name="monitor-the-load"></a>A terhelés figyelése

A következő lekérdezés rendszeres futtatásával győződjön meg arról, hogy a terhelés folyamatban van-e:

```sql
SELECT  r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command
,       sum(bytes_processed) AS bytes_processed
,       sum(rows_processed) AS rows_processed
FROM    sys.dm_pdw_exec_requests r
              JOIN sys.dm_pdw_dms_workers w
                     ON r.[request_id] = w.request_id
WHERE [label] = 'COPY: dbo.trip' and session_id <> session_id() and type = 'WRITER'
GROUP BY r.[request_id]                           
,       r.[status]                               
,       r.resource_class                         
,       r.command;

```

## <a name="next-steps"></a>További lépések

- Az betöltéssel kapcsolatos ajánlott eljárásokért lásd: [ajánlott eljárások az betöltéshez](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/guidance-for-loading-data).
- Az adatterhelések erőforrásainak kezelésével kapcsolatos információkért lásd: [munkaterhelés elkülönítése](https://docs.microsoft.com/azure/synapse-analytics/sql-data-warehouse/quickstart-configure-workload-isolation-tsql). 
