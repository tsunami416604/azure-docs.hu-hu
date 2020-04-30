---
title: 'Rövid útmutató: a munkaterhelés elkülönítésének konfigurálása – T-SQL'
description: A munkaterhelések elkülönítésének konfigurálása a T-SQL használatával.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/27/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 99c64e703158c40c2cc110a18be7b8c8d3800ff0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82207802"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Rövid útmutató: a munkaterhelés elkülönítésének konfigurálása T-SQL használatával

Ebben a rövid útmutatóban gyorsan létre fog hozni egy munkaterhelési csoportot és egy besorolást az erőforrások az adatok betöltéséhez való kiszolgálásához. A munkaterhelési csoport a rendszererőforrások 20%-át egy adatterhelésre fogja kiosztani.  A számítási feladatok besorolása a kérelmeket az adatterhelések terhelési csoportjához rendeli.  Az adatterhelések 20%-os elkülönítése esetén a rendszer garantált forrásokat biztosít a kitalált SLA-hoz.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

> [!NOTE]
> Ha SQL Analytics-példányt hoz létre az Azure szinapszis Analytics szolgáltatásban, akkor egy új számlázható szolgáltatást eredményezhet.  További információ: az [Azure szinapszis Analytics díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató azt feltételezi, hogy már rendelkezik SQL Analytics-példánnyal az Azure Szinapszisban, és hogy rendelkezik az adatbázis-VEZÉRLÉSi engedélyekkel. Ha létre kell hoznia egyet, kövesse a [Létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) útmutatót egy **mySampleDataWarehouse** nevű adattárház létrehozásához.

## <a name="create-login-for-dataloads"></a>DataLoads-beli bejelentkezés létrehozása

Hozzon létre egy SQL Server hitelesítési bejelentkezést az adatbázisban az `master` "ELTLogin'" [létrehozási bejelentkezés](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) használatával.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Felhasználó létrehozása

[Felhasználó létrehozása](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)"ELTLogin", mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Munkaterhelés-csoport létrehozása

Hozzon létre egy [munkaterhelési csoportot](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) a DataLoads 20%-os elkülönítéssel.

```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5)
;
```

## <a name="create-a-workload-classifier"></a>Számítási feladatok besorolásának létrehozása

Hozzon létre egy számítási feladathoz tartozó [osztályozó](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) a ELTLogin leképezéséhez a DataLoads munkaterhelés csoportjába.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>A meglévő munkaterhelés-csoportok, besorolások és futásidejű értékek megtekintése

```sql
--Workload groups
SELECT * FROM
sys.workload_management_workload_groups

--Workload classifiers
SELECT * FROM
sys.workload_management_workload_classifiers

--Run-time values
SELECT * FROM
sys.dm_workload_management_workload_groups_stats
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

```sql
DROP WORKLOAD CLASSIFIER [wgcELTLogin]
DROP WORKLOAD GROUP [DataLoads]
DROP USER [ELTLogin]
;
```

Az adattárház-egységek és az adattárházban tárolt adatforgalomért kell fizetnie. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha meg szeretné őrizni az adatok tárolását, szüneteltetheti a számítást, ha nem használja az SQL-készletet. A számítás felfüggesztésével csak az adattárolás díját számítjuk fel. Ha készen áll az adatok feldolgozására, folytassa a számítást.
- Ha szeretné megelőzni a jövőbeli kiadásokat, az adattárházat törölheti is.

## <a name="next-steps"></a>További lépések

- Most létrehozott egy munkaterhelés-csoportot. Futtasson néhány lekérdezést a ELTLogin, hogy láthassa, hogyan végzik el. A lekérdezéseket és a hozzárendelt munkaterhelési csoportot a [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) témakörben tekintheti meg.
- További információ az SQL-alapú számítási feladatok kezeléséről: [munkaterhelés-kezelés](sql-data-warehouse-workload-management.md) és munkaterhelés- [elkülönítés](sql-data-warehouse-workload-isolation.md).
