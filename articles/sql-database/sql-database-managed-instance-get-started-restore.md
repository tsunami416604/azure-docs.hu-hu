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
ms.reviewer: carlrab, bonova
manager: craigg
ms.date: 12/14/2018
ms.openlocfilehash: e2aa9edcd53aa3881b07e31fcf2312d5173a3a6e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57903501"
---
# <a name="quickstart-restore-a-database-to-a-managed-instance"></a>Gyors útmutató: Adatbázis visszaállítása felügyelt példányra 

Ez a rövid útmutatóban fogja használni az SQL Server Management Studio (SSMS) (a Wide World Importers – Standard biztonságimásolat-fájl) adatbázis visszaállítása az Azure Blob storage-ból egy Azure SQL Database-be [felügyelt példány](sql-database-managed-instance.md). 

> [!VIDEO https://www.youtube.com/embed/RxWYojo_Y3Q]

> [!NOTE]
> - Az Azure Database Migration Service (DMS) végzett áttelepítésének további információkért lásd: [DMS használatával felügyelt példány migrálása](../dms/tutorial-sql-server-to-managed-instance.md). 
> - Különféle migrálási módszerekről további információkért lásd: [SQL Server-példány migrálása az Azure SQL Database felügyelt példányába történő](sql-database-managed-instance-migrate.md).

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató:

- Az erőforrást használ, a [létrehoz egy felügyelt példányt](sql-database-managed-instance-get-started.md) rövid.
- Rendelkeznie kell a számítógép a legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) telepítve.
- SSMS használata a felügyelt példányhoz való kapcsolódáshoz szükséges. Tekintse meg az alábbi gyorsútmutatókkal való csatlakozásról:
  - [Csatlakozás Azure SQL Database felügyelt példányhoz egy Azure virtuális gépről](sql-database-managed-instance-configure-vm.md)
  - [Pont – hely kapcsolat konfigurálása egy Azure SQL Database felügyelt példányába való helyszíni](sql-database-managed-instance-configure-p2s.md).

> [!NOTE]
> További információ a biztonsági mentése és visszaállítása egy SQL Server-adatbázis az Azure Blob storage és a egy [közös hozzáférésű Jogosultságkód (SAS) kulcs](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1), lásd: [URL-címét az SQL Server biztonsági másolat](sql-database-managed-instance-get-started-restore.md).

## <a name="restore-the-database-from-a-backup-file"></a>Állítsa vissza az adatbázist egy biztonsági mentési fájlból

Az ssms-ben kövesse az alábbi lépéseket a Wide World Importers-adatbázis visszaállítása a felügyelt példányhoz. Az adatbázis biztonsági másolatát egy előre konfigurált Azure Blob storage-fiók van tárolva.

1. Nyissa meg az SMSS, és csatlakozhat a felügyelt példányhoz.
2. A bal oldali menüben kattintson a jobb gombbal a felügyelt példány, és válassza ki **új lekérdezés** egy új lekérdezési ablak megnyitásához.
3. Futtassa a következő SQL-parancsfájlt, amely egy előre konfigurált tárfiókot és SAS-kulcsot használ [hitelesítő adatok létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-credential-transact-sql) a felügyelt példány található.

   ```sql
   CREATE CREDENTIAL [https://mitutorials.blob.core.windows.net/databases] 
   WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
   , SECRET = 'sv=2017-11-09&ss=bfqt&srt=sco&sp=rwdlacup&se=2028-09-06T02:52:55Z&st=2018-09-04T18:52:55Z&spr=https&sig=WOTiM%2FS4GVF%2FEEs9DGQR9Im0W%2BwndxW2CQ7%2B5fHd7Is%3D' 
   ```

    ![hitelesítő adat létrehozása](./media/sql-database-managed-instance-get-started-restore/credential.png)

4. A hitelesítő adatok ellenőrzéséhez futtassa a következő parancsfájlt, amely használja egy [tároló](https://azure.microsoft.com/services/container-instances/) listáját a biztonságimásolat-fájl URL-CÍMÉT.

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://mitutorials.blob.core.windows.net/databases/WideWorldImporters-Standard.bak'
   ```

    ![fájlok listája](./media/sql-database-managed-instance-get-started-restore/file-list.png)

5. Futtassa a következő szkriptet a Wide World Importers-adatbázis visszaállítása.

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

7. Ha a visszaállítás elkészült, tekintse meg az Object Explorerben. 

## <a name="next-steps"></a>További lépések

- Egy biztonsági mentést egy URL-címet a hibaelhárításhoz, tekintse meg a [SQL Server biztonsági másolat URL-cím ajánlott eljárások és hibaelhárítás](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-to-url-best-practices-and-troubleshooting).
- Az alkalmazás kapcsolati beállítások áttekintéséhez lásd: [összekötheti saját alkalmazásait a felügyelt példány](sql-database-managed-instance-connect-app.md).
- Lekérdezés a kedvenc eszközeinek vagy nyelveinek használatával, lásd: [rövid útmutatók: Az Azure SQL Database csatlakozásról és lekérdezésről](sql-database-connect-query.md).
