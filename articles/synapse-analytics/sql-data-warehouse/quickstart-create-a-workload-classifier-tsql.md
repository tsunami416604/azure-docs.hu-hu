---
title: 'Rövid útmutató: Hozzon létre egy számítási feladatok osztályozó - T-SQL'
description: A T-SQL segítségével hozzon létre egy nagy fontosságú számítási feladatok osztályozót.
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
ms.openlocfilehash: 6354390c44c0991b0d6bf36cba8efd4272f9c73d
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583766"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql"></a>Rövid útmutató: Hozzon létre egy számítási feladatok osztályozó t-SQL használatával

Ebben a rövid útmutatóban gyorsan létrehozhat egy munkaterhelés-osztályozót, amely nagy jelentőséggel bír a szervezet vezérigazgatója számára. Ez a munkaterhelés-osztályozó lehetővé teszi, hogy a vezérigazgatói lekérdezések elsőbbséget élvezzenek más, a várólistában alacsonyabb fontossággal rendelkező lekérdezésekkel szemben.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

> [!NOTE]
> A Synapse SQL-készletpéldány létrehozása az Azure Synapse Analytics-ben egy új számlázható szolgáltatást eredményezhet.  További információ: [Azure Synapse Analytics pricing](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató feltételezi, hogy már rendelkezik SQL Data Warehouse-val, és rendelkezik CONTROL DATABASE engedélyekkel. Ha létre kell hoznia egyet, kövesse a [Létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) útmutatót egy **mySampleDataWarehouse** nevű adattárház létrehozásához.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="create-login-for-theceo"></a>Bejelentkezés létrehozása a TheCEO számára

Sql Server hitelesítési bejelentkezés `master` létrehozása az adatbázisban create [login](/sql/t-sql/statements/create-login-transact-sql) használatával a "TheCEO" számára.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Felhasználó létrehozása

[Felhasználó létrehozása](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), "TheCEO", a mySampleDataWarehouse alkalmazásban

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Számítási feladatok osztályozójának létrehozása

Hozzon létre egy nagy fontos [ságú "TheCEO" munkaterhelés-osztályozót.](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest)

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

Az adattárház-egységekért és az adatraktárban tárolt adatokért díjat számítunk fel. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha szeretné az adatokat megtartani a tárolóban, a számítási erőforrásokat szüneteltetheti, amíg nem használja az adattárházat. A számítás szüneteltetésével csak az adattárolásért kell fizetnie. Ha készen áll az adatokkal való munkára, folytassa a számítást.
- Ha szeretné megelőzni a jövőbeli kiadásokat, az adattárházat törölheti is.

Az erőforrások karbantartásához kövesse az alábbi lépéseket.

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com)válassza ki az adatraktárban.

    ![Az erőforrások eltávolítása](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítás szüneteltetéséhez kattintson a **Szünet** gombra. Ha az adattárház szüneteltetve van, az **Indítás** gomb látható.  A számítás folytatásához válassza a **Start gombot.**

3. Ha el szeretné távolítani az adatraktárt, hogy ne számítson fel díjat a számításért vagy a tárolásért, válassza a **Törlés**lehetőséget.

4. A létrehozott SQL-kiszolgáló eltávolításához jelölje **be mynewserver-20180430.database.windows.net** az előző képen, majd kattintson a **Törlés gombra.**  A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Az erőforráscsoport eltávolításához válassza a **MyResourceGroup**lehetőséget, majd az **Erőforráscsoport törlése**lehetőséget.

## <a name="next-steps"></a>További lépések

- Most létrehozott egy számítási feladatok osztályozóját. Futtasson néhány lekérdezést theceo néven, hogy lássa, hogyan teljesítenek. A lekérdezések és a hozzárendelt fontosság megtekintéséhez tekintse meg a [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) című témakört.
- A Synapse SQL-munkaterhelés-kezeléséről további információt a [Számítási feladatok fontossága](sql-data-warehouse-workload-importance.md) és [a számítási feladatok besorolása című témakörben talál.](sql-data-warehouse-workload-classification.md)
- Tekintse meg a [számítási feladatok fontosságának konfigurálása](sql-data-warehouse-how-to-configure-workload-importance.md) című útmutatócikkeket, valamint a [Munkaterhelés-kezelés kezelésének és figyelésének módját.](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
