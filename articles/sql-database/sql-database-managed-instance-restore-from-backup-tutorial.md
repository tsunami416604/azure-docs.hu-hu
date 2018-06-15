---
title: Visszaállítás biztonsági másolatból egy felügyelt Azure SQL Database-példányon | Microsoft Docs
description: Adatbázis biztonsági másolatának visszaállítása egy felügyelt Azure SQL Database-példányon az SSMS használatával.
keywords: oktatóanyag az SQL Database használatához, felügyelt SQL Database-példány létrehozása, visszaállítás biztonsági másolatból
services: sql-database
author: bonova
ms.reviewer: carlrab, srbozovi
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 06/07/2018
ms.author: bonova
manager: craigg
ms.openlocfilehash: c6f397c2cfcfd9fbd4a5cc8f70a99f4abc86ba21
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/08/2018
ms.locfileid: "34850678"
---
# <a name="restore-a-database-backup-to-an-azure-sql-database-managed-instance"></a>Adatbázis biztonsági másolatának visszaállítása egy felügyelt Azure SQL Database-példányon

Ez az oktatóanyag bemutatja, hogyan állíthatja vissza egy adatbázis az Azure Blob Storage-ban tárolt biztonsági másolatát a felügyelt példányon a Wide World Importers – Standard biztonságimásolat-fájllal. Ehhez a módszerhez egy ideig le kell állítani a szolgáltatást. Az Azure Database Migration Service migráláshoz való használatát a [felügyelt példány DMS használatával történő migrálását](../dms/tutorial-sql-server-to-managed-instance.md) bemutató oktatóanyag ismerteti. A különféle migrálási módszerekről lásd: [SQL Server-példány migrálása felügyelt Azure SQL Database-példányra](sql-database-managed-instance-migrate.md).

> [!div class="checklist"]
> * A Wide World Importers – Standard biztonsági mentési fájl letöltése
> * Azure Storage-fiók létrehozása és a biztonsági mentési fájl feltöltése
> * A Wide World Importers-adatbázis visszaállítása biztonsági mentési fájlból

## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag a következő oktatóanyagban létrehozott erőforrásokat használja kiindulási pontként: [Felügyelt Azure SQL Database-példány létrehozása](sql-database-managed-instance-create-tutorial-portal.md).

## <a name="download-the-wide-world-importers---standard-backup-file"></a>A Wide World Importers – Standard biztonsági mentési fájl letöltése

Az alábbi lépéseket követve töltse le a Wide World Importers – Standard biztonsági mentési fájlt.

Az Internet Explorerben írja be a https://github.com/Microsoft/sql-server-samples/releases/download/wide-world-importers-v1.0/WideWorldImporters-Standard.bak címet az URL-cím mezőjébe, majd amikor a rendszer kéri, kattintson a **Mentés** gombra, hogy a fájlt a **Letöltések** mappába mentse.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com/#create/Microsoft.SQLManagedInstance).

## <a name="create-azure-storage-account-and-upload-backup-file"></a>Azure Storage-fiók létrehozása és a biztonsági mentési fájl feltöltése

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Keressen a **Tár** kifejezésre, majd kattintson a **Tárfiók** elemre a tárfiók űrlapjának megnyitásához.

   ![tárfiók](./media/sql-database-managed-instance-tutorial/storage-account.png)

3. Adja meg a kért adatokat a tárfiók űrlapján az alábbi táblázatban szereplő információk segítségével:

   | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   |**Name (Név)**|Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Üzemi modell**|Erőforrásmodell||
   |**Fióktípus**|Blob Storage||
   |**Teljesítmény**|Standard vagy Prémium|Mágneses meghajtók vagy SSD-k|
   |**Replikáció**|Helyileg redundáns tárolás||
   |\*\*Hozzáférési szint (alapértelmezett)|Ritka vagy Gyakori||
   |**Biztonságos átvitelre van szükség**|Letiltva||
   |**Előfizetés**|Az Ön előfizetése|Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket.|
   |**Erőforráscsoport**|A korábban létrehozott erőforráscsoport|| 
   |**Hely**|Az előzőleg kiválasztott hely||
   |**Virtuális hálózatok**|Letiltva||

4. Kattintson a **Create** (Létrehozás) gombra.

   ![tárfiók részletei](./media/sql-database-managed-instance-tutorial/storage-account-details.png)

5. A tárfiók üzembe helyezését követően nyissa meg az új tárfiókot.
6. A **Beállítások** területen kattintson a **Közös hozzáférésű jogosultságkód** elemre, és nyissa meg a közös hozzáférésű jogosultságkód (SAS) űrlapját.

   ![sas-űrlap](./media/sql-database-managed-instance-tutorial/sas-form.png)

7. Az SAS-űrlapon módosítsa igény szerint az alapértelmezett értékeket. Láthatja, hogy a lejárati dátum/idő értéke alapértelmezés szerint csak 8 óra.
8. Kattintson az **SAS előállítása** gombra.

   ![sas-űrlap kitöltve](./media/sql-database-managed-instance-tutorial/sas-generate.png)

9. Másolja és mentse az **SAS-azonosítót** és a **Blobkiszolgáló SAS URL-címét**.
10. A **Beállítások** területen kattintson a **Tárolók** lehetőségre.

    ![tárolók](./media/sql-database-managed-instance-tutorial/containers.png)

11. A biztonsági mentési fájl tárolására szolgáló tároló létrehozásához kattintson a **+ Tároló** elemre.
12. Adja meg a kért adatokat a tároló űrlapján az alábbi táblázatban szereplő információk segítségével:

    | Beállítás| Ajánlott érték | Leírás |
   | ------ | --------------- | ----------- |
   |**Name (Név)**|Bármely érvényes név|Az érvényes nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) ismertető cikket.|
   |**Nyilvános hozzáférés szintje**|Privát (nincs névtelen hozzáférés)||

    ![tároló részletei](./media/sql-database-managed-instance-tutorial/container-detail.png)

13. Kattintson az **OK** gombra.
14. A tároló létrehozása után nyissa meg a tárolót.

    ![tároló](./media/sql-database-managed-instance-tutorial/container.png)

15. Kattintson a **Tároló tulajdonságai** lehetőségre, és másolja a tároló URL-címét.

    ![tároló URL-címe](./media/sql-database-managed-instance-tutorial/container-url.png)

16. Kattintson a **Feltöltés** gombra a **Blob feltöltése** űrlap megnyitásához.

    ![feltöltés](./media/sql-database-managed-instance-tutorial/upload.png)

17. Navigáljon a letöltési mappához, és válassza ki a **WideWorldIimporters-Standard.bak** fájlt.

    ![feltöltés](./media/sql-database-managed-instance-tutorial/upload-bak.png)

18. Kattintson a **Feltöltés** gombra.
19. Csak akkor folytassa az eljárást, ha a feltöltés befejeződött.

    ![feltöltés kész](./media/sql-database-managed-instance-tutorial/upload-complete.png)


## <a name="restore-the-wide-world-importers-database-from-a-backup-file"></a>A Wide World Importers-adatbázis visszaállítása biztonsági mentési fájlból

Az SSMS segítségével az alábbi lépéseket követve állítsa vissza a Wide World Importers-adatbázist a felügyelt példányon a biztonsági mentési fájlból.

1. Az SSMS-ben nyisson meg egy új lekérdezési ablakot.
2. Ezután a következő szkripttel hozzon létre egy SAS-hitelesítő adatot. Adja meg a tárfiók tárolójának URL-címét és az SAS-kulcsot az ábrán látható módon.

   ```sql
   CREATE CREDENTIAL [https://<storage_account_name>.blob.core.windows.net/<container>] 
      WITH IDENTITY = 'SHARED ACCESS SIGNATURE'
      , SECRET = '<shared_access_signature_key_with_removed_first_?_symbol>' 
   ```

    ![hitelesítő adat](./media/sql-database-managed-instance-tutorial/credential.png)

3. A következő szkripttel ellenőrizze az SAS-hitelesítő adat és a biztonsági másolat érvényességét. Ehhez adja meg a biztonsági mentési fájlt tartalmazó tároló URL-címét:

   ```sql
   RESTORE FILELISTONLY FROM URL = 
      'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'
   ```

    ![fájlok listája](./media/sql-database-managed-instance-tutorial/file-list.png)

4. A következő szkripttel állítsa vissza az Adventure Works 2012-adatbázist egy biztonsági mentési fájlból. Ehhez adja meg a biztonsági mentési fájlt tartalmazó tároló URL-címét:

   ```sql
   RESTORE DATABASE [Wide World Importers] FROM URL =
     'https://<storage_account_name>.blob.core.windows.net/<container>/WideWorldImporters-Standard.bak'`
   ```

    ![visszaállítás folyamatban](./media/sql-database-managed-instance-tutorial/restore-executing.png)

5. A visszaállítás állapotának nyomon követéséhez futtassa az alábbi lekérdezést egy új lekérdezési munkamenetben:

   ```sql
   SELECT session_id as SPID, command, a.text AS Query, start_time, percent_complete
      , dateadd(second,estimated_completion_time/1000, getdate()) as estimated_completion_time 
   FROM sys.dm_exec_requests r 
   CROSS APPLY sys.dm_exec_sql_text(r.sql_handle) a 
   WHERE r.command in ('BACKUP DATABASE','RESTORE DATABASE')`
   ```

    ![visszaállítás állapota](./media/sql-database-managed-instance-tutorial/restore-percent-complete.png)

6. Ha a visszaállítás elkészült, tekintse meg az Object Explorerben. 

    ![visszaállítás kész](./media/sql-database-managed-instance-tutorial/restore-complete.png)

## <a name="next-steps"></a>További lépések

Ebből az oktatóanyagból megtanulta, hogyan állíthatja vissza egy adatbázis az Azure Blob Storage-ban tárolt biztonsági másolatát a felügyelt példányon a Wide World Importers – Standard biztonságimásolat-fájllal. Megismerte, hogyan végezheti el az alábbi műveleteket: 

> [!div class="checklist"]
> * A Wide World Importers – Standard biztonsági mentési fájl letöltése
> * Azure Storage-fiók létrehozása és a biztonsági mentési fájl feltöltése
> * A Wide World Importers-adatbázis visszaállítása biztonsági mentési fájlból

A következő oktatóanyag azt mutatja be, hogyan migrálhatja az SQL Servert a felügyelt Azure SQL Database-példányra a DMS használatával.

> [!div class="nextstepaction"]
>[SQL Server migrálása felügyelt Azure SQL Database-példányra a DMS használatával](../dms/tutorial-sql-server-to-managed-instance.md)