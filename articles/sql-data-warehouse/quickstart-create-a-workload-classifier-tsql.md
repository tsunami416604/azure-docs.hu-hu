---
title: 'Gyors útmutató: számítási feladatok besorolása – T-SQL '
description: A T-SQL használatával hozzon létre egy nagy fontosságú számítási feladatokat.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload-management
ms.date: 05/01/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 1375605b6dab385b53af9212023767003e686e60
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73646289"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Gyors útmutató: számítási feladatok besorolásának létrehozása T-SQL használatával

Ebben a rövid útmutatóban gyorsan létre fog hozni egy számítási feladatokat, amely nagy fontossággal bír a szervezet VEZÉRIGAZGATÓJA számára. Ez a számítási feladatok besorolása lehetővé teszi, hogy az adatkezelési lekérdezések elsőbbséget élvezzenek más lekérdezésekkel szemben, amelyek kisebb jelentőséggel bírnak a várólistá

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

> [!NOTE]
> A SQL Data Warehouse létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti.  További információ: [SQL Data Warehouse díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató feltételezi, hogy már rendelkezik egy SQL Data Warehouse, és rendelkezik az adatbázis-VEZÉRLÉSi engedélyekkel. Ha létre kell hoznia egyet, kövesse a [Létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) útmutatót egy **mySampleDataWarehouse** nevű adattárház létrehozásához.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>TheCEO-beli bejelentkezés létrehozása

Hozzon létre egy SQL Server hitelesítési bejelentkezési azonosítót a `master` adatbázisban az "TheCEO" [létrehozási bejelentkezés](/sql/t-sql/statements/create-login-transact-sql) használatával.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Felhasználó létrehozása

[Felhasználó létrehozása](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest)"TheCEO", mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Számítási feladatok besorolásának létrehozása

Nagy fontossággal hozza létre a "TheCEO" számítási [feladatok besorolását](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) .

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Meglévő osztályozók megtekintése

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Az adattárház-egységek és az adattárházban tárolt adatforgalomért kell fizetnie. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha szeretné az adatokat megtartani a tárolóban, a számítási erőforrásokat szüneteltetheti, amíg nem használja az adattárházat. A számítás felfüggesztésével csak az adattárolás díját számítjuk fel. Ha készen áll az adatok feldolgozására, folytassa a számítást.
- Ha szeretné megelőzni a jövőbeli kiadásokat, az adattárházat törölheti is.

Az erőforrások tisztításához kövesse az alábbi lépéseket.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és válassza ki az adattárházat.

    ![Az erőforrások eltávolítása](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítás szüneteltetéséhez kattintson a **szüneteltetés** gombra. Ha az adattárház szüneteltetve van, az **Indítás** gomb látható.  A számítás folytatásához kattintson a **Start**gombra.

3. Ha el szeretné távolítani az adattárházat, hogy ne számítsa ki a számítási és a tárolási díjat, válassza a **Törlés**lehetőséget.

4. A létrehozott SQL-kiszolgáló eltávolításához válassza a **mynewserver-20180430.database.Windows.net** lehetőséget az előző képen, majd válassza a **Törlés**lehetőséget.  A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport eltávolításához válassza a **myResourceGroup**lehetőséget, majd válassza az **erőforráscsoport törlése**lehetőséget.

## <a name="next-steps"></a>További lépések

- Ezzel létrehozta a számítási feladatok besorolását. Futtasson néhány lekérdezést a TheCEO, hogy láthassa, hogyan végzik el. A lekérdezéseket és a hozzárendelt fontosságot a [sys. DM _pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) tekintheti meg.
- További információ a Azure SQL Data Warehouse munkaterhelés-kezelésről: a számítási [feladatok fontossága](sql-data-warehouse-workload-importance.md) és a számítási [feladatok besorolása](sql-data-warehouse-workload-classification.md).
- Tekintse meg az útmutatókat a számítási [feladatok fontosságának konfigurálásához](sql-data-warehouse-how-to-configure-workload-importance.md) , valamint a számítási [feladatok felügyeletének kezeléséhez és figyeléséhez](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md).
