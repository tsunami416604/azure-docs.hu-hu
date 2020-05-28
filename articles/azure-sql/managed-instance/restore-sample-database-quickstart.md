---
title: 'Gyors útmutató: biztonsági másolat visszaállítása (SSMS)'
titleSuffix: Azure SQL SQL Managed Instance
description: Ebből a rövid útmutatóból megtudhatja, hogyan állíthatja vissza az adatbázis biztonsági másolatát egy felügyelt Azure SQL SQL-példányra SQL Server-felügyelet (SSMS) használatával.
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, carlrab, bonova
ms.date: 12/14/2018
ms.openlocfilehash: 74975c050ef77c02e8912ce6cdd9404ac5bba019
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84054730"
---
# <a name="quickstart-restore-a-database-to-an-azure-sql-managed-instance-with-ssms"></a>Gyors útmutató: adatbázis visszaállítása egy felügyelt Azure SQL-példányra a SSMS használatával
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Ebben a rövid útmutatóban SQL Server Management Studio (SSMS) használatával állítja vissza az adatbázist (a Wide World Importers-standard Backup fájlt) az Azure Blob Storage-ból egy [felügyelt Azure SQL-példányba](sql-managed-instance-paas-overview.md).

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> A Azure Database Migration Service (DMS) használatával történő áttelepítéssel kapcsolatos további információkért lásd: [SQL felügyelt példányok áttelepítése a DMS használatával](../../dms/tutorial-sql-server-to-managed-instance.md).
> A különböző áttelepítési módszerekkel kapcsolatos további információkért lásd: [SQL Server Migrálás az Azure SQL felügyelt példányára](migrate-to-instance-from-sql-server.md).

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató:

- Erőforrásokat használ a [felügyelt SQL-példányok létrehozása](instance-create-quickstart.md) című rövid útmutatóban.
- A [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) legújabb verzióját kell telepíteni.
- A használatához a SSMS használata szükséges az SQL felügyelt példányhoz való kapcsolódáshoz. A kapcsolódás menetét a következő útmutatókban tekintheti meg:
  - [Nyilvános végpont engedélyezése](public-endpoint-configure.md) az SQL felügyelt példányain – ez az oktatóanyag ajánlott megközelítése.
  - [Kapcsolódás SQL felügyelt példányhoz egy Azure-beli virtuális gépen](connect-vm-instance-configure.md)
  - [Pont – hely kapcsolat konfigurálása egy SQL felügyelt példányhoz a helyszínen](point-to-site-p2s-configure.md).

> [!NOTE]
> A SQL Server-adatbázisok Azure Blob Storage használatával történő biztonsági mentéséről és visszaállításáról további [információt a](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) [SQL Server biztonsági mentés az URL-címre](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url?view=sql-server-2017)című témakörben talál.

## <a name="restore-from-a-backup-file"></a>Visszaállítás biztonságimásolat-fájlból

A SQL Server Management Studio (SSMS) alkalmazásban az alábbi lépéseket követve állítsa vissza a Wide World importőr-adatbázist az SQL felügyelt példányára. Az adatbázis biztonságimásolat-fájlját egy előre konfigurált Azure Blob Storage-fiók tárolja.

1. Nyissa meg a SSMS, és kapcsolódjon a felügyelt SQL-példányhoz.
2. A **Object Explorerban**kattintson a jobb gombbal az SQL felügyelt példányára, és válassza az **Új lekérdezés** lehetőséget egy új lekérdezési ablak megnyitásához.
3. Futtassa a következő SQL-parancsfájlt, amely egy előre konfigurált Storage-fiókot és egy SAS-kulcsot használ a [hitelesítő adatok létrehozásához](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) az SQL felügyelt példányában.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases]
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D'
   ```

    ![hitelesítő adat létrehozása](./media/restore-sample-database-quickstart/credential.png)

4. A hitelesítő adatok ellenőrzéséhez futtassa a következő parancsfájlt, amely egy [tároló](https://azure.microsoft.com/services/container-instances/) URL-címet használ a biztonságimásolat-fájlok listájának lekéréséhez.

   ```sql
   RESTORE FILELISTONLY FROM URL =
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![fájlok listája](./media/restore-sample-database-quickstart/file-list.png)

5. Futtassa a következő szkriptet a Wide World importing-adatbázis visszaállításához.

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![visszaállítás](./media/restore-sample-database-quickstart/restore.png)

6. Futtassa a következő szkriptet a visszaállítás állapotának nyomon követéséhez.

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time
   FROM sys.dm_exec_requests r
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')
   ```

7. Ha a visszaállítás befejeződött, tekintse meg az adatbázist Object Explorerban. A [sys. dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) nézet használatával ellenőrizheti, hogy az adatbázis-visszaállítás befejeződött-e.

> [!NOTE]
> Az adatbázis-visszaállítási művelet aszinkron és újrapróbálható. Előfordulhat, hogy a rendszer bizonyos hibákat SQL Server Management Studio, ha a kapcsolatok megszakadnak, vagy bizonyos időtúllépés lejár. Azure SQL Database továbbra is megkísérli visszaállítani az adatbázist a háttérben, és nyomon követheti a visszaállítás előrehaladását a [sys. dm_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql) és a [sys. dm_operation_status](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) nézetek használatával.
> A visszaállítási folyamat egyes fázisaiban egyedi azonosítót fog látni a rendszernézetek tényleges neve helyett. Ismerje meg az `RESTORE` utasítások viselkedésével kapcsolatos [here](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-transact-sql-information#restore-statement)eltéréseket.

## <a name="next-steps"></a>További lépések

- Az URL-címekre történő biztonsági mentés hibaelhárítását lásd: [SQL Server biztonsági mentés az URL-címekhez – ajánlott eljárások és hibaelhárítás](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Az alkalmazás-csatlakozási lehetőségek áttekintését lásd: [alkalmazások csatlakoztatása SQL felügyelt példányhoz](connect-application-instance.md).
- Ha kedvenc eszközeit vagy nyelveit szeretné lekérdezni, tekintse meg a rövid útmutatók [: Azure SQL Database a kapcsolódás és a lekérdezés](../database/connect-query-content-reference-guide.md)című témakört.
