---
title: Biztonsági másolat visszaállítása a felügyelt példányba
description: Adatbázis biztonsági másolatának visszaállítása egy felügyelt Azure SQL Database-példányon az SSMS használatával.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 37f7366d6622356017e458fb8f893b0be0851335
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73825694"
---
# <a name="quickstart-restore-a-database-to-a-managed-instance"></a>Rövid útmutató: Adatbázis visszaállítása felügyelt példányra

Ebben a rövid útmutatóban az SQL Server Management Studio (SSMS) segítségével állíthatja vissza az Azure Blob storage adatbázisát (a Wide World Importers – Standard backup file) egy Azure SQL Database [felügyelt példányba.](sql-database-managed-instance.md)

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> Az Azure Database Migration Service (DMS) használatával történő áttelepítésről a [Felügyelt példányáttelepítés DMS használatával című](../dms/tutorial-sql-server-to-managed-instance.md)témakörben talál további információt.
> A különböző áttelepítési módszerekről az [SQL Server-példányok Azure SQL Database felügyelt példányra való áttelepítése című témakörben](sql-database-managed-instance-migrate.md)talál további információt.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató:

- A Felügyelt [példány létrehozása](sql-database-managed-instance-get-started.md) rövid útmutató erőforrásait használja.
- A számítógépen telepítve van a legújabb [SQL Server Management Studio.](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms)
- SSMS-t kell használnia a felügyelt példányhoz való csatlakozáshoz. Tekintse meg a kapcsolódási útmutatót:
  - [Nyilvános végpont engedélyezése](sql-database-managed-instance-public-endpoint-configure.md) felügyelt példányon – ez az oktatóanyag ajánlott megközelítése.
  - [Csatlakozás Azure SQL Database felügyelt példányhoz egy Azure virtuális gépről](sql-database-managed-instance-configure-vm.md)
  - [Konfiguráljon egy pont-hely kapcsolatot egy Azure SQL-adatbázis felügyelt példánya a helyszíni.](sql-database-managed-instance-configure-p2s.md)

> [!NOTE]
> Az SQL Server-adatbázisok Azure Blob storage és [egy SAS-kulcs](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)használatával történő biztonsági mentésével és visszaállításával kapcsolatos további információkért lásd: [SQL Server Biztonsági másolat URL-címre.](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017)

## <a name="restore-the-database-from-a-backup-file"></a>Az adatbázis visszaállítása biztonsági másolatból

Az SSMS-ben kövesse az alábbi lépéseket a Wide World Importers adatbázis ának a felügyelt példányra való visszaállításához. Az adatbázis biztonsági másolata egy előre konfigurált Azure Blob tárfiók tárolja.

1. Nyissa meg az SMSS-t, és csatlakozzon a felügyelt példányhoz.
2. A bal oldali menüben kattintson a jobb gombbal a felügyelt példányra, és válassza az **Új lekérdezés** parancsot egy új lekérdezési ablak megnyitásához.
3. Futtassa a következő SQL-parancsfájlt, amely egy előre konfigurált tárfiókot és SAS-kulcsot használ a felügyelt példány [hitelesítő adatainak létrehozásához.](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql)

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![hitelesítő adat létrehozása](./media/sql-database-managed-instance-get-started-restore/credential.png)

4. A hitelesítő adatok ellenőrzéséhez futtassa a következő parancsfájlt, amely egy [tároló](https://azure.microsoft.com/services/container-instances/) URL-címét használja a biztonsági másolat listájának lefelvételéhez.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![fájlok listája](./media/sql-database-managed-instance-get-started-restore/file-list.png)

5. Futtassa a következő parancsfájlt a Wide World Importers adatbázis visszaállításához.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![visszaállítás](./media/sql-database-managed-instance-get-started-restore/restore.png)

6. Futtassa a következő parancsfájlt a visszaállítás állapotának nyomon követéséhez.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Amikor a visszaállítás befejeződik, tekintse meg az adatbázist az Objektumkezelőben. Ellenőrizheti, hogy az adatbázis-visszaállítás befejeződött-e a [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) nézetben.

> [!NOTE]
> Az adatbázis-visszaállítási művelet aszinkron és retriable. Előfordulhat, hogy az SQL Server Management Studio hibája, ha megszakad a kapcsolat, vagy valamilyen időtúllépés lejár. Az Azure SQL Database továbbra is megpróbálja visszaállítani az adatbázist a háttérben, és nyomon követheti a visszaállítás előrehaladását a [sys.dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) és [sys.dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) nézetek használatával.
> A visszaállítási folyamat egyes fázisaiban a rendszernézetekben a tényleges adatbázisnév helyett egyedi azonosító jelenik meg. A `RESTORE` nyilatkozatok viselkedési különbségeiről [itt](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#restore-statement)olvashat.

## <a name="next-steps"></a>További lépések

- Az URL-címekre történő biztonsági mentés hibaelhárításáról az [SQL Server biztonsági másolat url-címhez – ajánlott eljárások és hibaelhárítás című témakörben](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting)található.
- Az alkalmazáscsatlakozási beállítások áttekintését [az Alkalmazások csatlakoztatása a felügyelt példányhoz című témakörben találja.](sql-database-managed-instance-connect-app.md)
- A kedvenc eszközeivel vagy nyelveivel történő lekérdezéshez olvassa el [a Rövid útmutatók: Azure SQL Database Connect és Query című témakört.](sql-database-connect-query.md)
