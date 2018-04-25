---
title: 'Gyors útmutató: Számítási bővítés az Azure SQL Data Warehouse-ban – T-SQL | Microsoft Docs'
description: Számítási bővítés az Azure SQL Data Warehouse-ban T-SQL és SQL Server Management Studio (SSMS) segítségével. Bővítéssel a számítások teljesítménye növelhető, szűkítéssel a költségek csökkenthetők.
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.component: manage
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b4e123475679cf1afce09630c157377ee67b5202
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-using-t-sql"></a>Gyors útmutató: Azure SQL Data Warehouse-beli számítási bővítés T-SQL segítségével

Számítási bővítés az Azure SQL Data Warehouse-ban T-SQL és SQL Server Management Studio (SSMS) segítségével. [Bővítéssel](sql-data-warehouse-manage-compute-overview.md) a számítások teljesítménye növelhető, szűkítéssel a költségek csökkenthetők. 

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="before-you-begin"></a>Előkészületek

Töltse le és telepítse az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) (SSMS) legújabb verzióját.

Ez feltételezi, hogy elvégezte a [Létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) gyors útmutatót. A létrehozási és csatlakozási útmutató elvégzése után tudja, hogyan csatlakozzon, létrehozott egy **mySampleDataWarehouse** nevű adattárházat, és létrehozott egy tűzfalszabályt is, mely engedélyezi az ügyfélnek a kiszolgáló elérését.
 
## <a name="create-a-data-warehouse"></a>Adattárház létrehozása

Kövesse a [Létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) gyors útmutatót egy **mySampleDataWarehouse** nevű adattárház létrehozásához. Végezze el az útmutató összes lépését, hogy létrejöjjön a tűzfalszabály, és hogy csatlakozni tudjon az adattárházhoz az SQL Management Studio alkalmazásból.

## <a name="connect-to-the-server-as-server-admin"></a>Csatlakozás a kiszolgálóhoz kiszolgáló-rendszergazdaként

Ebben a részben az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms.md) használatával építjük fel a kapcsolatot az Azure SQL-kiszolgálóval.

1. Nyissa meg az SQL Server Management Studiót.

2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen adja meg a következő adatokat:

   | Beállítás       | Ajánlott érték | Leírás | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Kiszolgáló típusa | Adatbázismotor | Kötelezően megadandó érték |
   | Kiszolgálónév | A teljes kiszolgálónév | Például: **mynewserver-20171113.database.windows.net**. |
   | Hitelesítés | SQL Server-hitelesítés | Ebben az oktatóanyagban az SQL-hitelesítésen kívül más hitelesítéstípus nincs konfigurálva. |
   | Bejelentkezés | A kiszolgálói rendszergazdai fiók | Ez az a fiók, amely a kiszolgáló létrehozásakor lett megadva. |
   | Jelszó | A kiszolgálói rendszergazdai fiók jelszava | Ezt a jelszót adta meg a kiszolgáló létrehozásakor. |

    ![kapcsolódás a kiszolgálóhoz](media/load-data-from-azure-blob-storage-using-polybase/connect-to-server.png)

4. Kattintson a **Connect** (Csatlakozás) gombra. Megnyílik az Object Explorer ablak az SSMS-ben. 

5. Az Object Explorerben bontsa ki a **Databases** (Adatbázisok) elemet. Ezután bontsa ki a **mySampleDatabase** csomópontot az új adatbázisban található objektumok megtekintéséhez.

    ![adatbázis-objektumok](media/create-data-warehouse-portal/connected.png) 

## <a name="view-service-objective"></a>Szolgáltatási cél megtekintése
A szolgáltatási cél beállítása tartalmazza az adattárház adattárházegységeinek számát. 

Az adattárház jelenlegi adattárházegység-számának megtekintéséhez:

1. A **mynewserver-20171113.database.windows.net** kapcsolat alatt bontsa ki a **System Databases** (Rendszeradatbázisok) csomópontot.
2. Kattintson jobb gombbal a **master** elemre, és válassza a **New Query** (Új lekérdezés) lehetőséget. Megnyílik egy új lekérdezési ablak.
3. Futtassa a következő lekérdezést a sys.database_service_objectives dinamikus felügyeleti nézetből való választáshoz. 

    ```sql
    SELECT
        db.name [Database]
    ,   ds.edition [Edition]
    ,   ds.service_objective [Service Objective]
    FROM
        sys.database_service_objectives ds
    JOIN
        sys.databases db ON ds.database_id = db.database_id
    WHERE 
        db.name = 'mySampleDataWarehouse'
    ```

4. Az eredményben láthatja, hogy a **mySampleDataWarehouse** adattárház szolgáltatási célja „DW400”. 

    ![Aktuális adattárházegységek megtekintése](media/quickstart-scale-compute-tsql/view-current-dwu.png)


## <a name="scale-compute"></a>Számítások méretezése
Az SQL Data Warehouse-ban növelheti vagy csökkentheti a számítási erőforrásokat az adattárházegységek számának módosításával. A [Létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) gyorsútmutató létrehozta a **mySampleDataWarehouse** adattárházat, és inicializálta azt 400 adattárházegységgel. Az alábbi lépésekkel módosíthatja a **mySampleDataWarehouse** adattárházban az adattárházegységek számát.

Az adattárházegységek számának módosításához:

1. Kattintson jobb gombbal a **master** elemre, és válassza a **New Query** (Új lekérdezés) lehetőséget.
2. Módosítsa a szolgáltatási célt az [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) T-SQL-utasítással. Az alábbi lekérdezést futtatva például beállíthatja a DW300 szolgáltatási célt. 

```Sql
ALTER DATABASE mySampleDataWarehouse
MODIFY (SERVICE_OBJECTIVE = 'DW300')
;
```

## <a name="check-data-warehouse-state"></a>Az adattárház állapotának ellenőrzése

A szüneteltetett adattárházakhoz nem tud T-SQL-utasításokkal csatlakozni. Az adattárház jelenlegi állapotát megtekintheti egy PowerShell-parancsmag használatával. A vonatkozó példát az [Adattárház állapotának ellenőrzése – Powershell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) című cikkben találhatja meg. 

## <a name="check-operation-status"></a>Műveleti állapot ellenőrzése

Ha szeretne információt kapni az SQL-adattárházon végzett felügyeleti műveletről, futtassa a következő lekérdezést a [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) dinamikus felügyeleti nézeten. Ez a lekérdezés visszaadja a műveletet és annak állapotát, mely vagy IN_PROGRESS, vagy COMPLETED.

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND 
    major_resource_id = 'MySQLDW'
```


## <a name="next-steps"></a>További lépések
Ebből az útmutatóból megtudhatta, hogyan tudja méterezni egy adattárház számítási kapacitását. Ha bővebb információra van szüksége az Azure SQL Data Warehouse-zal kapcsolatban, folytassa az adatok betöltésével foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
>[Adatok betöltése az SQL Data Warehouse-okba](load-data-from-azure-blob-storage-using-polybase.md)
