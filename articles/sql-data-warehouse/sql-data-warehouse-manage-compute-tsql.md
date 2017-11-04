---
title: "Szüneteltetése, folytatása, méretezése a T-SQL Azure SQL Data warehouse |} Microsoft Docs"
description: "Transact-SQL (T-SQL) feladatok kibővített teljesítmény dwu-k beállításával. Költségeket takaríthat vissza csúcsidőszakon kívüli időszakokban."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.assetid: a970d939-2adf-4856-8a78-d4fe8ab2cceb
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 03/30/2017
ms.author: elbutter;barbkess
ms.openlocfilehash: 9221d72ecf8ab2ba8b04e4bc97eeef7157817cca
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-t-sql"></a>Számítási teljesítményt az Azure SQL Data Warehouse (T-SQL) kezelése
> [!div class="op_single_selector"]
> * [Áttekintés](sql-data-warehouse-manage-compute-overview.md)
> * [Portál](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
>
>

<a name="current-dwu-bk"></a>

## <a name="view-current-dwu-settings"></a>Aktuális DWU beállításainak megjelenítése
Az adatbázis aktuális DWU beállításainak megtekintése:

1. Nyissa meg az SQL Server Object Explorert a Visual Studióban.
2. A master adatbázis SQL Database logikai kiszolgálóhoz rendelt kapcsolódni.
3. Válassza ki a sys.database_service_objectives dinamikus kezelési nézetet. Például: 

```sql
SELECT
    db.name [Database]
,   ds.edition [Edition]
,   ds.service_objective [Service Objective]
FROM
    sys.database_service_objectives ds
JOIN
    sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute"></a>Skála számítási
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

A dwu-k módosítása:

1. A fő adatbázist az SQL Database logikai kiszolgálóhoz csatlakozni.
2. Használja a [ALTER DATABASE] [ ALTER DATABASE] TSQL utasítást. Az alábbi példa állítja be a szolgáltatási szint célkitűzés DW1000 az adatbázis MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="check-database-state-bk"></a>

## <a name="check-database-state-and-operation-progress"></a>Ellenőrizze az adatbázis állapotát és a művelet folyamatban van

1. A fő adatbázist az SQL Database logikai kiszolgálóhoz csatlakozni.
2. Küldje el a lekérdezés adatbázis állapotának ellenőrzése

```sql
SELECT *
FROM
sys.databases
```

3. Küldje el a lekérdezési művelet állapotának ellenőrzése

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND 
    major_resource_id = 'MySQLDW'
```

Ez a DMV visszatér a különböző felügyeleti műveleteket, például a művelet és a művelet, amely lehet IN_PROGRESS vagy befejeződött állapotának az SQL Data Warehouse az információ.



<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Következő lépések
Más felügyeleti feladatokat [kezelése-áttekintés][Management overview].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Management overview]: ./sql-data-warehouse-overview-manage.md
[Manage compute power overview]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->

[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/
