---
title: Számítási műveletek méretezése az Azure Synapse Analytics szolgáltatásban – T-SQL
description: A T-SQL és az SQL Server Management Studio (SSMS) használatával az Azure Synapse Analytics segítségével skálázható a számítás. Bővítéssel a számítások teljesítménye növelhető, szűkítéssel a költségek csökkenthetők.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: ba0bcc61cbfbb16652021045a3b25bbcee72df2c
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350782"
---
# <a name="quickstart-scale-compute-in-azure-synapse-analytics-using-t-sql"></a>Rövid útmutató: A számítás méretezése az Azure Synapse Analytics szolgáltatásban a T-SQL használatával

A T-SQL és az SQL Server Management Studio (SSMS) használatával az Azure Synapse Analytics (korábbi sql DW) segítségével skálázható a számítás. [Felskálázással](sql-data-warehouse-manage-compute-overview.md) a számítások teljesítménye növelhető, leskálázással a költségek csökkenthetők. 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot, mielőtt elkezdené.

## <a name="before-you-begin"></a>Előkészületek

Töltse le és telepítse az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) legújabb verzióját.
 
## <a name="create-a-data-warehouse"></a>Adattárház létrehozása

Kövesse a [Létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) gyors útmutatót egy **mySampleDataWarehouse** nevű adattárház létrehozásához. A rövid útmutató végrehajtásával győződjön meg arról, hogy rendelkezik tűzfalszabállyal, és az SQL Server Management Studio-ból tud csatlakozni az adattárházhoz.

## <a name="connect-to-the-server-as-server-admin"></a>Csatlakozás a kiszolgálóhoz kiszolgáló-rendszergazdaként

Ebben a részben az [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) használatával építjük fel a kapcsolatot az Azure SQL-kiszolgálóval.

1. Nyissa meg az SQL Server Management Studiót.

2. A **Connect to Server** (Kapcsolódás a kiszolgálóhoz) párbeszédpanelen adja meg a következő adatokat:

   | Beállítás       | Ajánlott érték | Leírás | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Kiszolgáló típusa | Adatbázismotor | Kötelezően megadandó érték |
   | Kiszolgálónév | A teljes kiszolgálónév | Íme egy példa: **mySampleDataWarehouseservername.database.windows.net**. |
   | Hitelesítés | SQL Server-hitelesítés | Ebben az oktatóanyagban az SQL-hitelesítésen kívül más hitelesítéstípus nincs konfigurálva. |
   | Bejelentkezés | A kiszolgálói rendszergazdafiók | Az a fiók, amely a kiszolgáló létrehozásakor lett megadva. |
   | Jelszó | A kiszolgálói rendszergazdai fiók jelszava | A kiszolgáló létrehozásakor megadott jelszó. |

    ![Csatlakozás kiszolgálóhoz](./media/quickstart-scale-compute-tsql/connect-to-server.png)

3. Kattintson a **Csatlakozás** gombra. Megnyílik az Object Explorer ablak az SSMS-ben.

4. Az Object Explorerben bontsa ki a **Databases** (Adatbázisok) elemet. Ezután bontsa ki a **mySampleDataWarehouse csomópontot** az új adatbázis objektumainak megtekintéséhez.

    ![Adatbázis-objektumok](./media/quickstart-scale-compute-tsql/connected.png)

## <a name="view-service-objective"></a>Szolgáltatási cél megtekintése
A szolgáltatási cél beállítása tartalmazza az adattárház adattárházegységeinek számát. 

Az adattárház jelenlegi adattárházegység-számának megtekintéséhez:

1. A **mySampleDataWarehouseservername.database.windows.net**kapcsolatban bontsa ki a **Rendszeradatbázisok csomópontot.**
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

    ![iew-áram-dwu](./media/quickstart-scale-compute-tsql/view-current-dwu.png)

## <a name="scale-compute"></a>Számítások méretezése
Az Azure Synapse-ban növelheti vagy csökkentheti a számítási erőforrásokat az adatraktár-egységek módosításával. A [Létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) gyorsútmutató létrehozta a **mySampleDataWarehouse** adattárházat, és inicializálta azt 400 adattárházegységgel. Az alábbi lépésekkel módosíthatja a **mySampleDataWarehouse** adattárházban az adattárházegységek számát.

Az adattárházegységek számának módosításához:

1. Kattintson jobb gombbal a **master** elemre, és válassza a **New Query** (Új lekérdezés) lehetőséget.
2. Módosítsa a szolgáltatási célt az [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) T-SQL-utasítással. Az alábbi lekérdezést futtatva például beállíthatja a DW300 szolgáltatási célt. 

    ```Sql
    ALTER DATABASE mySampleDataWarehouse
    MODIFY (SERVICE_OBJECTIVE = 'DW300c');
    ```

## <a name="monitor-scale-change-request"></a>Méretmódosítási kérés monitorozása
Az előző módosítási kérés állapotának megtekintéséhez használhatja a `WAITFORDELAY` T-SQL szintaxist a sys.dm_operation_status dinamikus felügyeleti nézet (DMV) lekérdezéséhez.

A szolgáltatásobjektum módosítási állapotának lekérdezése:

1. Kattintson jobb gombbal a **master** elemre, és válassza a **New Query** (Új lekérdezés) lehetőséget.
2. Futtassa az alábbi lekérdezést a sys.dm_operation_status DMV lekérdezéséhez.

    ```sql
    WHILE 
    (
        SELECT TOP 1 state_desc
        FROM sys.dm_operation_status
        WHERE 
            1=1
            AND resource_type_desc = 'Database'
            AND major_resource_id = 'mySampleDataWarehouse'
            AND operation = 'ALTER DATABASE'
        ORDER BY
            start_time DESC
    ) = 'IN_PROGRESS'
    BEGIN
        RAISERROR('Scale operation in progress',0,0) WITH NOWAIT;
        WAITFOR DELAY '00:00:05';
    END
    PRINT 'Complete';
    ```
3. A kimenet az állapotlekérdezések naplóját jeleníti meg.

    ![Művelet állapota](./media/quickstart-scale-compute-tsql/polling-output.png)

## <a name="check-data-warehouse-state"></a>Az adattárház állapotának ellenőrzése

A szüneteltetett adattárházakhoz nem tud T-SQL-utasításokkal csatlakozni. Az adattárház jelenlegi állapotát megtekintheti egy PowerShell-parancsmag használatával. A vonatkozó példát az [Adattárház állapotának ellenőrzése – Powershell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) című cikkben találhatja meg. 

## <a name="check-operation-status"></a>Műveleti állapot ellenőrzése

Az Azure Synapse különböző felügyeleti műveleteire vonatkozó információk visszaadására futtassa a következő lekérdezést a [sys.dm_operation_status](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) DMV-n. A lekérdezés például visszaadja a műveletet és annak állapotát, amely IN_PROGRESS, vagy COMPLETED lehet.

```sql
SELECT *
FROM
    sys.dm_operation_status
WHERE
    resource_type_desc = 'Database'
AND 
    major_resource_id = 'mySampleDataWarehouse'
```


## <a name="next-steps"></a>További lépések
Ebben az útmutatóban megismerhette, hogyan skálázható egy adattárház számítási kapacitása. Ha többet szeretne megtudni az Azure Synapse, folytassa az adatbetöltési oktatóanyag.

> [!div class="nextstepaction"]
>[Adatok betöltése az Azure Synapse Analytics szolgáltatásba](load-data-from-azure-blob-storage-using-polybase.md)
