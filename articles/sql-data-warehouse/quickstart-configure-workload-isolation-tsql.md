---
title: 'Rövid útmutató: a munkaterhelés elkülönítésének konfigurálása – T-SQL'
description: A munkaterhelések elkülönítésének konfigurálása a T-SQL használatával.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: bd3ad98116b18a77a77e8f6f327689d0ebb7dd21
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200515"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Rövid útmutató: a munkaterhelés elkülönítésének konfigurálása T-SQL használatával

Ebben a rövid útmutatóban gyorsan létre fog hozni egy munkaterhelési csoportot és egy besorolást az erőforrások az adatok betöltéséhez való kiszolgálásához. A munkaterhelési csoport a rendszererőforrások 20%-át egy adatterhelésre fogja kiosztani.  A számítási feladatok besorolása a kérelmeket az adatterhelések terhelési csoportjához rendeli.  Az adatterhelések 20%-os elkülönítése esetén a rendszer garantált forrásokat biztosít a kitalált SLA-hoz.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

> [!NOTE]
> Ha SQL Analytics-példányt hoz létre az Azure szinapszis Analytics szolgáltatásban, akkor egy új számlázható szolgáltatást eredményezhet.  További információ: az [Azure szinapszis Analytics díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Előfeltételek
 
Ez a rövid útmutató azt feltételezi, hogy már rendelkezik SQL Analytics-példánnyal az Azure Szinapszisban, és hogy rendelkezik az adatbázis-VEZÉRLÉSi engedélyekkel. Ha létre kell hoznia egyet, kövesse a [Létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) útmutatót egy **mySampleDataWarehouse** nevű adattárház létrehozásához.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

## <a name="create-login-for-dataloads"></a>DataLoads-beli bejelentkezés létrehozása

Hozzon létre egy SQL Server hitelesítési bejelentkezést a `master` adatbázisban a ELTLogin' [létrehozási bejelentkezési](/sql/t-sql/statements/create-login-transact-sql) használatával.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Felhasználó létrehozása

[Felhasználó létrehozása](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest)"ELTLogin", mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Munkaterhelés-csoport létrehozása
Hozzon létre egy [munkaterhelési csoportot](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest) a DataLoads 20%-os elkülönítéssel.
```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20   
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5) 
;
```

## <a name="create-a-workload-classifier"></a>Számítási feladatok besorolásának létrehozása

Hozzon létre egy számítási feladathoz tartozó [osztályozó](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) a ELTLogin leképezéséhez a DataLoads munkaterhelés csoportjába.

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

Az erőforrások tisztításához kövesse az alábbi lépéseket.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és válassza ki az adattárházat.

    ![Az erőforrások eltávolítása](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítás szüneteltetéséhez kattintson a **szüneteltetés** gombra. Ha az adattárház szüneteltetve van, az **Indítás** gomb látható.  A számítás folytatásához kattintson a **Start**gombra.

3. Ha el szeretné távolítani az adattárházat, hogy ne számítsa ki a számítási és a tárolási díjat, válassza a **Törlés**lehetőséget.

4. A létrehozott SQL-kiszolgáló eltávolításához válassza a **mynewserver-20180430.database.Windows.net** lehetőséget az előző képen, majd válassza a **Törlés**lehetőséget.  A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport eltávolításához válassza a **myResourceGroup**lehetőséget, majd válassza az **erőforráscsoport törlése**lehetőséget.

## <a name="next-steps"></a>További lépések

- Most létrehozott egy munkaterhelés-csoportot. Futtasson néhány lekérdezést a ELTLogin, hogy láthassa, hogyan végzik el. A lekérdezéseket és a hozzárendelt munkaterhelési csoportot a [sys. dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) témakörben tekintheti meg.
- Az SQL Analytics számítási feladatok kezelésével kapcsolatos további információkért lásd: a számítási [feladatok kezelése](sql-data-warehouse-workload-management.md) és a számítási [feladatok elkülönítése](sql-data-warehouse-workload-isolation.md).
