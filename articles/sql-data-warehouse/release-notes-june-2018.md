---
title: Azure SQL Data Warehouse kibocsátási megjegyzések 2018. június | Microsoft Docs
description: A Azure SQL Data Warehouse kibocsátási megjegyzései.
services: sql-data-warehouse
author: anumjs
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: ''
ms.date: 07/23/2018
ms.author: anjangsh
ms.reviewer: jrasnick
ms.openlocfilehash: 4348a634fd5b2b33f36d8e79f28caf659b82ccf4
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "67626152"
---
# <a name="whats-new-in-azure-sql-data-warehouse-june-2018"></a>A Azure SQL Data Warehouse újdonságai 2018. június
Azure SQL Data Warehouse folyamatosan fejleszti a fejlesztési funkciókat. Ez a cikk a 2018-es júniusban bevezetett új szolgáltatásokat és módosításokat ismerteti. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="user-defined-restore-points"></a>Felhasználó által definiált visszaállítási pontok
A SQL Data Warehouse 8 óránként automatikusan pillanatképeket küld az adattárházról, amely garantálja a nyolc órás helyreállítási időcélkitűzést (RPO). Habár az automatizált Pillanatképek megkönnyítik az adatraktár futtatásának kezelési terheit, az üzleti igényeknek megfelelően a kritikus időpontokban pillanatképeket kell készítenie. Például egy pillanatképet közvetlenül a jelentős adatterhelés előtt, vagy az új parancsfájlok az adatraktárba történő üzembe helyezése előtt, hogy egy visszaállítási pont közvetlenül a művelet előtt legyen engedélyezve. 

A SQL Data Warehouse mostantól támogatja a [felhasználó által definiált visszaállítási pontokat](https://azure.microsoft.com/blog/quick-recovery-time-with-sql-data-warehouse-using-user-defined-restore-points/) a [New-AzSqlDatabaseRestorePoint](https://docs.microsoft.com/powershell/module/az.sql/new-azsqldatabaserestorepoint) parancsmagon keresztül.

```powershell
New-AzSqlDatabaseRestorePoint
    -ResourceGroupName $ResourceGroupName
    -ServerName $ServerName
    -DatabaseName $DatabaseName
    -RestorePointLabel $RestorePointName
```

## <a name="column-level-security"></a>Oszlop szintű biztonság
Az adattárházban az adathozzáférés és a biztonság kezelése kritikus fontosságú az ügyfelekkel és partnerekkel való bizalom kiépítésekor. SQL Data Warehouse [mostantól támogatja az oszlop szintű biztonságot (CLS)](https://azure.microsoft.com/blog/column-level-security-is-now-supported-in-azure-sql-data-warehouse/) , amely lehetővé teszi a bizalmas adatok megtekintésére vonatkozó engedélyek módosítását azáltal, hogy az adattárház újratervezése nélkül korlátozza a felhasználók hozzáférését a táblák adott oszlopaihoz.

A CLS lehetővé teszi a táblázat oszlopaihoz való hozzáférés szabályozását a felhasználó végrehajtási környezete vagy a csoporttagság alapján a standard [](https://docs.microsoft.com/azure/sql-data-warehouse/column-level-security) szintű T-SQL-utasítás használatával. A hozzáférés-korlátozási logika maga az adatbázis rétegében található, nem pedig a más alkalmazásokban lévő adatoktól, így egyszerűbbé válik a teljes biztonsági implementáció.


```sql
CREATE USER Nurse WITHOUT LOGIN;   
GRANT SELECT ON Membership(MemberID, FirstName, LastName, Phone, Email) TO Nurse;   
EXECUTE AS USER = 'Nurse';
SELECT * FROM Membership;

Msg 230, Level 14, State 1, Line 12 
The SELECT permission was denied on the column 'SSN' of the object 'Membership', database 'CLS_TestDW', schema 'dbo'.
```

## <a name="object_schema_name"></a>OBJECT_SCHEMA_NAME
A [OBJECT_SCHEMA_NAME ()](https://docs.microsoft.com/sql/t-sql/functions/object-schema-name-transact-sql) függvény a séma hatókörű objektumaihoz tartozó adatbázis-séma nevét adja vissza. Ez a függvény az ETL-eszközökben az objektum sémájának érvényesítése során lett meghatározva. 

```sql
SELECT
    OBJECT_SCHEMA_NAME([object_id]) [table_schema]
    , [name]                        [table_name]
FROM
    [sys].[tables];
```

**Minták eredményei**
```
table_schema    | table_name
-----------------------------
dbo               customer
dbo               lineitem
dbo               nation
dbo               orders
```

## <a name="support-for-the-systime_zone_info-view"></a>A sys. time_zone_info nézet támogatása
A [sys. time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) nézet a Azure SQL Data Warehouseon belüli támogatott időzónákra vonatkozó adatokat adja vissza.

```sql
SELECT * FROM [sys].[time_zone_info];
```

**Minták eredményei**
```
name                            | current_utc_offset | is_currently_dst
-------------------------------------------------------------------------
Pacific Standard Time (Mexico)    -07:00               1
US Mountain Standard Time         -07:00               0
Mountain Standard Time (Mexico)   -06:00               1
Central Standard Time             -05:00               1
```

## <a name="auto-stats-operations-appear-in-sysdm_pdw_exec_requests-behavior-change"></a>Az automatikus stats műveletek megjelennek a sys. DM _pdw_exec_requests (viselkedés változása)

Az [automatikus létrehozási statisztikák](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics#automatic-creation-of-statistic)bevezetésével a Azure SQL Data Warehouse statisztikát készít a lekérdezés-végrehajtás optimalizálásához. A június 2018-os kiadással megfigyelhető, hogy mikor történik a statisztikák automatikus létrehozása azáltal, hogy hozzáad egy rekordot a [sys. DM _pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) -nézethez, amikor a [create Statistics](https://docs.microsoft.com/sql/t-sql/statements/create-statistics-transact-sql) művelet végrehajtása folyamatban van.

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
**Minták eredményei**
```
start_time                | end_time                | command
------------------------------------------------------------------------------------------------------------------------------
2018-06-06 19:06:26.173    2018-06-06 19:06:26.173    CREATE STATISTICS _WA_Sys_00000001_63D998CC ON dbo.LineItem(l_orderkey);
```

## <a name="next-steps"></a>További lépések
Most, hogy már ismeri a SQL Data Warehouset, megtudhatja, hogyan [hozhat létre gyorsan egy SQL Data Warehouse][create a SQL Data Warehouse]. Ha az Azure új felhasználója, hasznosnak találhatja az [Azure szószedetét][Azure glossary], amikor az új fogalmakkal ismerkedik. Vagy tekintsen meg néhányat a többi SQL Data Warehouse-erőforrás közül.  

* [Ügyfelek sikertörténetei]
* [Blogok]
* [Funkciókérések]
* [Videók]
* [Az ügyféltanácsadói csapat blogjai]
* [Stack Overflow-fórum]
* [Twitter]


[Blogok]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Az ügyféltanácsadói csapat blogjai]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Ügyfelek sikertörténetei]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Funkciókérések]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Stack Overflow-fórum]: https://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Videók]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md
