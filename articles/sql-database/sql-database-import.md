---
title: Hozhat létre Azure SQL-adatbázis BACPAC-fájl importálása |} A Microsoft Docs
description: Hozzon létre egy newAzure SQL-adatbázis BACPAC-fájl importálásával.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: 3eaa12b5ba735d1e2aa0e074054328942a3041eb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57900098"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Gyors útmutató: BACPAC-fájl importálása az Azure SQL Database-adatbázishoz

Az Azure SQL Database-adatbázis egy SQL Server-adatbázis importálhat egy [BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) fájlt. Importálhatja az adatokat egy `BACPAC` fájl az Azure Blob storage (csak standard szintű storage) tárolt vagy a helyszíni helyen a helyi tárolóból. Azáltal, hogy gyorsabban, és további erőforrások importálása sebesség maximalizálása az adatbázis magasabb szolgáltatási szintre, és a számítási méret az importálási folyamat során. Majd vertikális sikeres importálása után.

> [!NOTE]
> Az importált adatbázis kompatibilitási szintjét a forrás-adatbázis-kompatibilitási szint alapján történik.
> [!IMPORTANT]
> Az adatbázis az importálás után kiválaszthatja, működtetheti az adatbázist, a jelenlegi kompatibilitási szinten (100. szint az AdventureWorks2008R2 adatbázis), vagy egy magasabb szinten. Az adatbázis meghatározott kompatibilitási szinten való működtetésének következményeivel és lehetőségeivel kapcsolatos további információkért lásd: [ADATBÁZIS kompatibilitási szintjének MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). A kompatibilitási szintekkel kapcsolatos további adatbázisszintű beállításokról itt talál információt: [ADATBÁZISHOZ KÖTŐDŐ KONFIGURÁCIÓ MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Az Azure Portalon BACPAC-fájlból való importálása

A [az Azure portal](https://portal.azure.com) *csak* támogatja az önálló adatbázis létrehozása az Azure SQL Database és *csak* egy Azure Blob storage-ban tárolt BACPAC-fájlból.

> [!NOTE]
> [Felügyelt példány](sql-database-managed-instance.md) jelenleg nem támogatja az adatbázis-példány adatbázis BACPAC-fájlból az Azure portal használatával történő áttelepítése. Felügyelt példány importálni, használja az SQL Server Management Studio vagy az SQLPackage.

1. BACPAC-fájlból importálja egy új önálló adatbázis az Azure portal használatával, nyissa meg az megfelelő adatbázis-kiszolgáló oldalára, és majd az eszköztáron válassza **adatbázis importálása**.  

   ![Adatbázis import1](./media/sql-database-import/import1.png)

2. Válassza ki a tárfiókot és a tároló a BACPAC-fájlt, és válassza ki a BACPAC-fájlt, amelyből importálni.
3. Adja meg az új adatbázis mérete (általában ugyanaz, mint forrás), és adja meg a cél SQL-kiszolgáló hitelesítő adatai. Egy új Azure SQL database a lehetséges értékek listáját lásd: [Create Database](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).

   ![Adatbázis import2](./media/sql-database-import/import2.png)

4. Kattintson az **OK** gombra.

5. Az importálás folyamat állapotának monitorozásához, nyissa meg a database-kiszolgáló oldalára, majd a **beállítások**válassza **importálási/exportálási előzmények**. Ha sikeres, az importálás rendelkezik egy **befejezve** állapotát.

   ![Adatbázis-importálás állapota](./media/sql-database-import/import-status.png)

6. Annak ellenőrzéséhez, hogy az adatbázis az adatbázis-kiszolgáló az élő, válassza ki a **SQL-adatbázisok** és ellenőrizze, hogy az új adatbázis **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>SqlPackage használatával BACPAC-fájlból való importálása

Egy SQL Server adatbázis használatával importálhatja a [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) parancssori segédprogram, lásd: [paramétereknek és tulajdonságoknak importálása](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage rendelkezik a legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Is letöltheti a legújabb [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) a Microsoft letöltőközpontból.

Méretezés és teljesítmény javasoljuk, hogy a legtöbb éles környezetben az SqlPackage használatával, ahelyett hogy az Azure portal használatával. Egy SQL Server Ügyféltanácsadói csapatának blogja használatával történő áttelepítésről a `BACPAC` fájlok, lásd: [migrálás SQL Serverről az Azure SQL Database BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Méretezés és teljesítmény javasoljuk a legtöbb éles környezetben az SqlPackage használatával. További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

A következő SqlPackage parancs importálja a **AdventureWorks2008R2** adatbázis a helyi tárolóból egy Azure SQL Database-kiszolgálóhoz nevű **mynewserver20170403**. Létrehoz egy új adatbázist nevű **myMigratedDatabase** az egy **prémium szintű** szolgáltatási szint és a egy **P6** szolgáltatási célt. Válasszon a környezetének megfelelő értékeket módosíthatja.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Szeretne csatlakozni egy SQL Database-kiszolgáló kezelése a céges tűzfal mögül önálló adatbázis, a tűzfal megnyitása a 1433-as portot kell rendelkeznie. Csatlakozás egy felügyelt példányra, rendelkeznie kell egy [pont – hely kapcsolat](sql-database-managed-instance-configure-p2s.md) vagy express route-kapcsolathoz.
>

Ez a példa bemutatja, hogyan importálását az Active Directory univerzális hitelesítéssel az SqlPackage használatával.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>Önálló adatbázis PowerShell használatával BACPAC-fájlból való importálása

> [!NOTE]
> [Felügyelt példány](sql-database-managed-instance.md) jelenleg nem támogatja az adatbázis áttelepítése az Azure PowerShell használatával BACPAC-fájlból egy példány adatbázisba. Felügyelt példány importálni, használja az SQL Server Management Studio vagy az SQLPackage.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> A PowerShell Azure Resource Manager-modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés Az.Sql modul. Ezeket a parancsmagokat lásd: [azurerm.SQL-hez](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). A parancsok a Az modul, és az AzureRm-modulok argumentumainak lényegében megegyeznek.

Használja a [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) parancsmag egy importálása az Azure SQL Database szolgáltatás adatbázis-kérelem elküldéséhez. Adatbázis mérete, attól függően az importálás eltarthat egy ideig.

 ```powershell
 $importRequest = New-AzSqlDatabaseImport 
    -ResourceGroupName "<your_resource_group>" `
    -ServerName "<your_server>" `
    -DatabaseName "<your_database>" `
    -DatabaseMaxSizeBytes "<database_size_in_bytes>" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey -ResourceGroupName "<your_resource_group>" -StorageAccountName "<your_storage_account").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 Használhatja a [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) parancsmaggal ellenőrizheti az importálás folyamatban van. Futtassa a következő parancsmagot a kérelem után azonnal általában értéket ad vissza **állapota: InProgress**. Amikor megjelenik az importálás befejeződött **állapota: Sikeres**.

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
> Egy másik példa parancsfájl: [adatbázis importálása BACPAC-fájlból](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Korlátozások

Egy adatbázis rugalmas adatbáziskészletben való importálása nem támogatott. Adatok importálása egy önálló adatbázis, és ezután helyezze át az adatbázist egy rugalmas készlet.

## <a name="import-using-wizards"></a>Importálja a varázslók használatával

Ezek a varázslók is használhatja.

- [Importálja az SQL Server Management Studio adatrétegbeli alkalmazás varázsló](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Az SQL Server importálása és exportálása varázslóban](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan csatlakozhat, és az importált SQL Database-adatbázis lekérdezéséhez, lásd: [a rövid útmutató: Azure SQL Database: SQL Server Management Studio használatával csatlakozhat, és adatokat kérdezhet le](sql-database-connect-query-ssms.md).
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- A teljes SQL Server adatbázis áttelepítési folyamat, beleértve a teljesítménnyel kapcsolatos javaslatok, lásd: [SQL Server-adatbázis áttelepítése az Azure SQL Database](sql-database-single-database-migrate.md).
- Megtudhatja, hogyan kezelésére és megosztására tárkulcsok és közös hozzáférésű jogosultságkódok biztonságosan, lásd: [Azure Storage biztonsági útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
