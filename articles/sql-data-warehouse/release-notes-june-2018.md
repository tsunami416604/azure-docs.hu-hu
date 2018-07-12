---
title: Az Azure SQL Data Warehouse kibocsátási megjegyzések a 2018 június |} A Microsoft Docs
description: Kibocsátási megjegyzések az Azure SQL Data warehouse-hoz.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 06/25/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 4b097568abdad5e84605856d0246b0407d881758
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723972"
---
# <a name="whats-new-in-azure-sql-data-warehouse-june-2018"></a>Mi az új Azure SQL Data warehouse? 2018. június
Az Azure SQL Data Warehouse fejlesztései folyamatosan kap. Ez a cikk ismerteti az új szolgáltatásokat és változásokat tartalmazza, amelyek a 2018 június. 

## <a name="user-defined-restore-points"></a>Felhasználó által definiált visszaállítási pontok
Az SQL Data Warehouse szolgáltatás automatikusan pillanatképeket készít az adatraktárról garantáló egy 8 órás helyreállításipont-célkitűzés (RPO) 8 óránként. Amíg ez a felügyeleti terheket, az adattárház futó automatikus pillanatfelvételek egyszerű, szükség van a pillanatfelvételt a üzleti igények alapján kritikus időpontokban. Ha például pillanatképének elkészítése közvetlenül egy jelentős az adatok betöltése vagy új parancsfájl központi telepítése előtt, közvetlenül a művelet előtt visszaállítási pont engedélyezése a data warehouse-bA. 

Az SQL Data Warehouse mostantól támogatja [felhasználói visszaállítási pontok](https://azure.microsoft.com/blog/quick-recovery-time-with-sql-data-warehouse-using-user-defined-restore-points/) keresztül a [New-AzureRmSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabaserestorepoin) parancsmagot.

```PowerShell
New-AzureRmSqlDatabaseRestorePoint
    -ResourceGroupName $ResourceGroupName
    -ServerName $ServerName
    -DatabaseName $DatabaseName
    -RestorePointLabel $RestorePointName
```

## <a name="column-level-security"></a>Oszlop biztonság
Adathozzáférés és az adattárház biztonsági kezelése fontos az ügyfelek és partnerek számára a bizalmi kapcsolat létrehozásához. Az SQL Data Warehouse [mostantól támogatja az oszlopszintű biztonsági (CLS)](https://azure.microsoft.com/blog/column-level-security-is-now-supported-in-azure-sql-data-warehouse/) , amely lehetővé teszi, ha az adattárház újratervezés nélkül korlátozza a felhasználói hozzáférést az adott oszlopok a táblázatokban lévő bizalmas adatok megtekintéséhez szükséges engedélyekkel.

CLS lehetővé teszi, hogy ki férhet hozzá a táblázat oszlopaihoz szabvány használatával a felhasználó a végrehajtási környezet vagy a csoporttagság alapján [GRANT](https://docs.microsoft.com/azure/sql-data-warehouse/column-level-security) T-SQL-utasítással. A hozzáférés korlátozási logika megtalálható az adatbázisszinten, saját maga helyett egy másik alkalmazás adatait a forrásadatok leegyszerűsíti a teljes biztonsági végrehajtására.


```sql
CREATE USER Nurse WITHOUT LOGIN;   
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO Nurse;   
EXECUTE AS USER = 'Nurse';
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12 
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="objectschemaname"></a>OBJECT_SCHEMA_NAME
A [OBJECT_SCHEMA_NAME()]() függvény séma hatókörébe tartozó objektumok sémájának nevét adja vissza. Ez a függvény vált az ETL-eszközök gyakori objektum séma érvényesítése. 

```sql
SELECT
    OBJECT_SCHEMA_NAME([object_id]) [table_schema]
    , [name]                        [table_name]
FROM
    [sys].[tables];
```

**Példa eredmények**
```
table_schema    | table_name
-----------------------------
dbo               customer
dbo               lineitem
dbo               nation
dbo               orders
```

## <a name="support-for-the-systimezoneinfo-view"></a>A sys.time_zone_info nézet támogatása
A [sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) megtekintése az Azure SQL Data Warehouse belül támogatott időzónák kapcsolatos információkat ad vissza.

```sql
SELECT * FROM [sys].[time_zone_info];
```

**Példa eredmények**
```
name                            | current_utc_offset | is_currently_dst
-------------------------------------------------------------------------
Pacific Standard Time (Mexico)    -07:00               1
US Mountain Standard Time         -07:00               0
Mountain Standard Time (Mexico)   -06:00               1
Central Standard Time             -05:00               1
```

## <a name="auto-stats-operations-appear-in-sysdmpdwexecrequests-behavior-change"></a>Az automatikus statisztikák műveletek jelennek meg sys.dm_pdw_exec_requests (viselkedésének módosítása)

Bevezetésével [automatikus Create Statistics](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistics), Azure SQL Data Warehouse optimalizálhatja a lekérdezés-végrehajtás statisztikai hoz létre. A 2018 június kiadás fájltulajdonságainak figyelheti, hogy mikor statisztikái is automatikusan létrehozott, egy rekord hozzáadásával a [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) megtekintése, amikor bármelyik [CREATE STATISTICS](https://docs.microsoft.com/sql/t-sql/statements/create-statistics-transact-sql) művelet végrehajtása.

```sql
SELECT
    [start_time]
    , [end_time]
    , [command]
FROM
    [sys].[dm_pdw_exec_requests]
WHERE
    [command] LIKE 'CREATE STATISTICS _WA_Sys%';
```
**Példa eredmények**
```
start_time                | end_time                | command
------------------------------------------------------------------------------------------------------------------------------
2018-06-06 19:06:26.173    2018-06-06 19:06:26.173    CREATE STATISTICS _WA_Sys_00000001_63D998CC ON dbo.LineItem(l_orderkey);
```