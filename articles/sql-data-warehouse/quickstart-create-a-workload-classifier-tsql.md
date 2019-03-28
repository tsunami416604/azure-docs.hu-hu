---
title: 'Gyors útmutató: Hozzon létre egy számítási feladat osztályozó – T-SQL |} A Microsoft Docs'
description: Hozzon létre egy számítási feladat osztályozó nagyon fontosként megjelölve a T-SQL használatával
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: workload management
ms.date: 03/13/2019
ms.author: rortloff
ms.reviewer: jrasnick
ms.openlocfilehash: 8ae9ad4d1101c3b42c46c51546ac82e5b4a134c8
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58519850"
---
# <a name="quickstart-create-a-workload-classifier-using-t-sql-preview"></a>Gyors útmutató: T-SQL (előzetes verzió) használatával munkaterhelés besorolás létrehozása

Ebben a rövid gyorsan létrehozhat egy számítási feladat osztályozó az ügyvezető Igazgatótól a szervezet számára nagyon fontosként megjelölve. A számítási feladatok osztályozó lehetővé teszi a Vezérigazgató lekérdezéseket elsőbbséget élveznek a más lekérdezések az alacsonyabb fontossági az üzenetsorban.

> [!Note]
> Az SQL Data Warehouse Gen2 munkaterhelés besorolási érhető el.

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

> [!NOTE]
> A SQL Data Warehouse létrehozása egy új számlázható szolgáltatás létrejöttét eredményezheti.  További információ: [SQL Data Warehouse díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).
>
>

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató feltételezi, hogy már rendelkezik egy SQL data warehouse-ba, és hogy van-e adatbázis VEZÉRLÉSE engedéllyel. Ha létre kell hoznia egyet, kövesse a [Létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) útmutatót egy **mySampleDataWarehouse** nevű adattárház létrehozásához.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-login-for-theceo"></a>Bejelentkezési TheCEO létrehozása

Az SQL Server-hitelesítési bejelentkezés létrehozása a `master` használatával [CREATE LOGIN](/sql/t-sql/statements/create-login-transact-sql) "TheCEO" számára.

```sql
IF NOT EXISTS (SELECT * FROM sys.sql_logins WHERE name = 'TheCEO')
BEGIN
CREATE LOGIN [TheCEO] WITH PASSWORD='<strongpassword>'
END
;
```

## <a name="create-user"></a>Felhasználó létrehozása

[Felhasználó létrehozása](/sql/t-sql/statements/create-user-transact-sql?view=azure-sqldw-latest), "TheCEO", a mySampleDataWarehouse

```sql
IF NOT EXISTS (SELECT * FROM sys.database_principals WHERE name = 'THECEO')
BEGIN
CREATE USER [TheCEO] FOR LOGIN [TheCEO]
END
;
```

## <a name="create-a-workload-classifier"></a>Számítási feladatok besorolás létrehozása

Hozzon létre egy [munkaterhelés osztályozó](/sql/t-sql/statements/create-workload-classifier-transact-sql?view=azure-sqldw-latest) a "TheCEO" nagyon fontosként megjelölve.

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO];
CREATE WORKLOAD CLASSIFIER [wgcTheCEO]
WITH (WORKLOAD_GROUP = 'xlargerc'
      ,MEMBERNAME = 'TheCEO'
      ,IMPORTANCE = HIGH);
```

## <a name="view-existing-classifiers"></a>Meglévő deklarációkkal megtekintése

```sql
SELECT * FROM sys.workload_management_workload_classifiers
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

```sql
DROP WORKLOAD CLASSIFIER [wgcTheCEO]
DROP USER [TheCEO]
;
```

Az adattárházegységek és az adatraktárban tárolt adatok alatt díjkötelesek. Ezek a számítási és tárolási erőforrások elkülönítve lesznek kiszámlázva.

- Ha szeretné az adatokat megtartani a tárolóban, a számítási erőforrásokat szüneteltetheti, amíg nem használja az adattárházat. Szüneteltetésével számítási adattárolás csak díjkötelesek. Ha készen áll az adatokkal való munka, folytathatja a számítást.
- Ha szeretné megelőzni a jövőbeli kiadásokat, az adattárházat törölheti is.

Kövesse az alábbi lépéseket az erőforrások törlése.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), jelölje be az adattárházra.

    ![Az erőforrások eltávolítása](media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. A számítás szüneteltetésére, válassza ki a **szüneteltetése** gombra. Ha az adattárház szüneteltetve van, az **Indítás** gomb látható.  Válassza ki a számítási erőforrások újraindításához **Start**.

3. A data warehouse-ba, nem számítunk fel díjat, a számítási és tárolási eltávolításához jelölje ki **törlése**.

4. Válassza ki a létrehozott SQL-kiszolgáló eltávolításához **mynewserver-20180430.database.windows.net** az előző képen, és válassza ki a **törlése**.  A törléssel bánjon óvatosan, mivel a kiszolgálóval együtt a hozzá rendelt összes adatbázis is törölve lesz.

5. Távolítsa el az erőforráscsoportot, válassza ki a **myResourceGroup**, majd válassza ki **erőforráscsoport törlése**.

## <a name="next-steps"></a>További lépések

Sikeresen létrehozott egy számítási feladat osztályozó által igénybe vett. Néhány lekérdezést futtató TheCEO megtekintéséhez, hogyan működnek. Lásd: [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) a lekérdezések és a hozzárendelt fontosságát.

Az SQL Data Warehouse számítási feladatok kezelésével kapcsolatos további információkért lásd: [SQL Data Warehouse számítási feladatok fontossági](sql-data-warehouse-workload-importance.md) és [SQL Warehouse számítási feladatok az Adatbesorolás](sql-data-warehouse-workload-classification.md).
