---
title: 'Rövid útmutató: Számítási feladatok elkülönítésének konfigurálása – T-SQL'
description: A T-SQL segítségével konfigurálhatja a munkaterhelés elkülönítését.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: c4920b2a5b4ff0b1a94fa8fa0e83f72761802b97
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583799"
---
# <a name="quickstart-configure-workload-isolation-using-t-sql"></a>Gyorsútmutató: A számítási feladatok elkülönítésének konfigurálása a T-SQL használatával

Ebben a rövid útmutatóban gyorsan létrehozhat egy számítási feladatok csoportját és osztályozóját az erőforrások adatbetöltéshez való lefoglalásához. A számítási feladatok csoportja a rendszererőforrások 20%-át egy adatbetöltéshez rendeli hozzá.  A munkaterhelés-osztályozó kéréseket rendel az adatbetöltési számítási feladatok csoportjához.  Az adatterhelések 20%-os elkülönítésével garantált erőforrások at találnak az SL-ek számára.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

> [!NOTE]
> A Synapse SQL-készlet létrehozása az Azure Synapse Analytics-ben egy új számlázható szolgáltatást eredményezhet.  További információ: [Azure Synapse Analytics pricing](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Előfeltételek
 
Ez a rövid útmutató feltételezi, hogy már rendelkezik egy Synapse SQL-készlet az Azure Synapse, és hogy rendelkezik CONTROL DATABASE-engedélyekkel. Ha létre kell hoznia egyet, kövesse a [Létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) útmutatót egy **mySampleDataWarehouse** nevű adattárház létrehozásához.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="create-login-for-dataloads"></a>Bejelentkezés létrehozása az adatterhelésekhez

SQL Server hitelesítési bejelentkezés `master` létrehozása az adatbázisban create [login](/sql/t-sql/statements/create-login-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) használatával az "ELTLogin" használatával.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'ELTLogin')
BEGIN
CREATE LOGIN [ELTLogin] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Felhasználó létrehozása

[Felhasználó létrehozása](/sql/t-sql/statements/create-user-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest), "ELTLogin", a mySampleDataWarehouse alkalmazásban

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'ELTLogin')
BEGIN
CREATE USER [ELTLogin] FOR LOGIN [ELTLogin]
END
;
```

## <a name="create-a-workload-group"></a>Számítási feladatcsoport létrehozása
Hozzon létre egy [számítási feladatok csoportja](/sql/t-sql/statements/create-workload-group-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) dataloads 20%-os elkülönítéssel.
```sql
CREATE WORKLOAD GROUP DataLoads
WITH ( MIN_PERCENTAGE_RESOURCE = 20   
      ,CAP_PERCENTAGE_RESOURCE = 100
      ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 5) 
;
```

## <a name="create-a-workload-classifier"></a>Számítási feladatok osztályozójának létrehozása

Hozzon létre egy [számítási feladatok osztályozó](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) tle van képezve ELTLogin a DataLoads számítási feladatok csoporthoz.

```sql
CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
WITH (WORKLOAD_GROUP = 'DataLoads'
      ,MEMBERNAME = 'ELTLogin')
;
```

## <a name="view-existing-workload-groups-and-classifiers-and-run-time-values"></a>Meglévő munkaterhelés-csoportok és osztályozók és futásidejű értékek megtekintése

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

Az adattárház-egységekért és az adatraktárban tárolt adatokért díjat számítunk fel. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha meg szeretné tartani az adatokat a tárolóban, szüneteltetheti a számítást, ha nem használja az SQL-készletet. A számítás szüneteltetésével csak az adattárolásért kell fizetnie. Ha készen áll az adatokkal való munkára, folytassa a számítást.
- Ha szeretné megelőzni a jövőbeli kiadásokat, az adattárházat törölheti is.

Az erőforrások karbantartásához kövesse az alábbi lépéseket.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)válassza ki az adatraktárban.

    ![Az erőforrások eltávolítása](./media/quickstart-configure-workload-isolation-tsql/clean-up-resources.png)

2. A számítás szüneteltetéséhez kattintson a **Szünet** gombra. Ha az adattárház szüneteltetve van, az **Indítás** gomb látható.  A számítás folytatásához válassza a **Start gombot.**

3. Ha el szeretné távolítani az adatraktárt, hogy ne számítson fel díjat a számításért vagy a tárolásért, válassza a **Törlés**lehetőséget.

4. A létrehozott SQL-kiszolgáló eltávolításához jelölje **be mynewserver-20180430.database.windows.net** az előző képen, majd kattintson a **Törlés gombra.**  A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport eltávolításához válassza a **MyResourceGroup**lehetőséget, majd az **Erőforráscsoport törlése**lehetőséget.

## <a name="next-steps"></a>További lépések

- Most létrehozott egy számítási feladatok csoportját. Futtasson néhány lekérdezést ELTLogin néven, hogy lássa, hogyan teljesítenek. Tekintse meg [a sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql/?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) a lekérdezések és a hozzárendelt munkaterhelési csoport megtekintéséhez.
- A Synapse SQL-munkaterhelés-kezeléséről további információt a [Számítási feladatok kezelése](sql-data-warehouse-workload-management.md) és a munkaterhelés [elkülönítése című témakörben talál.](sql-data-warehouse-workload-isolation.md)
