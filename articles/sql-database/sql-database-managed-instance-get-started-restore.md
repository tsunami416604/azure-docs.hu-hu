---
title: Visszaállítás biztonsági másolatból egy felügyelt Azure SQL Database-példányon | Microsoft Docs
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
ms.openlocfilehash: c07daf4cf9f355e8eccfe618262dd06b4216106e
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73146391"
---
# <a name="quickstart-restore-a-database-to-a-managed-instance"></a>Rövid útmutató: adatbázis visszaállítása felügyelt példányra

Ebben a rövid útmutatóban SQL Server Management Studio (SSMS) használatával állítja vissza az adatbázist (a Wide World Importers-standard Backup fájlt) az Azure Blob Storage-ból egy Azure SQL Database [felügyelt példányba](sql-database-managed-instance.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> A Azure Database Migration Service (DMS) használatával történő áttelepítéssel kapcsolatos további információkért lásd: a [felügyelt példányok áttelepítése a DMS használatával](../dms/tutorial-sql-server-to-managed-instance.md).
> A különböző áttelepítési módszerekkel kapcsolatos további információkért lásd: [SQL Server példány áttelepítése Azure SQL Database felügyelt példányra](sql-database-managed-instance-migrate.md).

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató:

- Erőforrásokat használ a [felügyelt példány létrehozása](sql-database-managed-instance-get-started.md) rövid útmutatóból.
- A számítógépen telepítve kell lennie a legújabb [SQL Server Management Studionak](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) .
- A SSMS használata szükséges a felügyelt példányhoz való kapcsolódáshoz. A kapcsolódás menetét a következő útmutatókban tekintheti meg:
  - [Nyilvános végpont engedélyezése](sql-database-managed-instance-public-endpoint-configure.md) felügyelt példányon – ez az oktatóanyag ajánlott megközelítése.
  - [Csatlakozás Azure SQL Database felügyelt példányhoz egy Azure virtuális gépről](sql-database-managed-instance-configure-vm.md)
  - [Pont – hely kapcsolat konfigurálása egy Azure SQL Database felügyelt példányhoz a helyszínen](sql-database-managed-instance-configure-p2s.md).

> [!NOTE]
> A SQL Server-adatbázisok Azure Blob Storage használatával történő biztonsági mentéséről és visszaállításáról további [információt a](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) [SQL Server biztonsági mentés az URL-címre](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017)című témakörben talál.

## <a name="restore-the-database-from-a-backup-file"></a>Adatbázis visszaállítása biztonságimásolat-fájlból

A SSMS-ben kövesse az alábbi lépéseket a Wide World importing-adatbázis a felügyelt példányra való visszaállításához. Az adatbázis biztonságimásolat-fájlját egy előre konfigurált Azure Blob Storage-fiók tárolja.

1. Nyissa meg az SMS-ket, és kapcsolódjon a felügyelt példányhoz.
2. A bal oldali menüben kattintson a jobb gombbal a felügyelt példányra, és válassza az **Új lekérdezés** lehetőséget egy új lekérdezési ablak megnyitásához.
3. Futtassa a következő SQL-parancsfájlt, amely egy előre konfigurált Storage-fiókot és egy SAS-kulcsot használ [egy hitelesítő adat létrehozásához](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) a felügyelt példányban.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![hitelesítő adat létrehozása](./media/sql-database-managed-instance-get-started-restore/credential.png)

4. A hitelesítő adatok ellenőrzéséhez futtassa a következő parancsfájlt, amely egy [tároló](https://azure.microsoft.com/services/container-instances/) URL-címet használ a biztonságimásolat-fájlok listájának lekéréséhez.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![fájlok listája](./media/sql-database-managed-instance-get-started-restore/file-list.png)

5. Futtassa a következő szkriptet a Wide World importing-adatbázis visszaállításához.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![visszaállítás](./media/sql-database-managed-instance-get-started-restore/restore.png)

6. Futtassa a következő szkriptet a visszaállítás állapotának nyomon követéséhez.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Ha a visszaállítás befejeződött, tekintse meg az adatbázist Object Explorerban. Ellenőrizheti, hogy az adatbázis-visszaállítás a [sys. DM _operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) nézet használatával fejeződött-e be.

> [!NOTE]
> Az adatbázis-visszaállítási művelet aszinkron és újrapróbálható. Előfordulhat, hogy a rendszer bizonyos hibákat SQL Server Management Studio, ha a kapcsolatok megszakadnak, vagy bizonyos időtúllépés lejár. Azure SQL Database továbbra is megkísérli visszaállítani az adatbázist a háttérben, és nyomon követheti a visszaállítás előrehaladását a [sys. DM _exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) és a [sys. DM _operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) nézetek használatával.
> A visszaállítási folyamat egyes fázisaiban egyedi azonosítót fog látni a rendszernézetek tényleges neve helyett. További információ a `RESTORE` nyilatkozat viselkedési [eltérésekről](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#restore-statement).

## <a name="next-steps"></a>Következő lépések

- Az URL-címekre történő biztonsági mentés hibaelhárítását lásd: [SQL Server biztonsági mentés az URL-címekhez – ajánlott eljárások és hibaelhárítás](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Az alkalmazás-csatlakozási lehetőségek áttekintését lásd: [alkalmazások csatlakoztatása felügyelt példányhoz](sql-database-managed-instance-connect-app.md).
- Ha kedvenc eszközeit vagy nyelveit szeretné lekérdezni, tekintse meg a rövid útmutatók [: Azure SQL Database a kapcsolódás és a lekérdezés](sql-database-connect-query.md)című témakört.
