---
title: Hozhat létre Azure SQL-adatbázis BACPAC-fájl importálása |} A Microsoft Docs
description: Hozzon létre egy newAzure SQL-adatbázis BACPAC-fájl importálásával.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 12/05/2018
ms.openlocfilehash: 6753666f1747c95ad3486444ed41e3cad0b8e905
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084176"
---
# <a name="quickstart-import-a-bacpac-file-to-a-new-azure-sql-database"></a>Gyors útmutató: Az új Azure SQL-adatbázis BACPAC-fájl importálása

SQL Server-adatbázis is áttelepíthet egy Azure SQL database-adatbázishoz egy [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) fájlt (a zip-fájlt a egy `.bacpac` bővítmény, az adatbázis metaadatai és adatokat tartalmazó). BACPAC-fájl importálása az Azure blob storage-ba (csak standard szintű storage) vagy a helyszíni helyen a helyi tárolóból. Az importálás sebesség maximalizálása azt javasoljuk, hogy, adjon meg egy magasabb szolgáltatási szintre és a számítási méret (például P6), majd a sikeres importálása után. Az importált adatbázis kompatibilitási szintjét a forrás-adatbázis-kompatibilitási szint alapján történik.

> [!IMPORTANT]
> Az adatbázis az importálás után kiválaszthatja, működtetheti az adatbázist, a jelenlegi kompatibilitási szinten (100. szint az AdventureWorks2008R2 adatbázis), vagy egy magasabb szinten. Az adatbázis meghatározott kompatibilitási szinten való működtetésének következményeivel és lehetőségeivel kapcsolatos további információkért lásd: [ADATBÁZIS kompatibilitási szintjének MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). A kompatibilitási szintekkel kapcsolatos további adatbázisszintű beállításokról itt talál információt: [ADATBÁZISHOZ KÖTŐDŐ KONFIGURÁCIÓ MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Az Azure Portalon BACPAC-fájlból való importálása

Ez a szakasz bemutatja, hogyan, a a [az Azure portal](https://portal.azure.com), egy Azure SQL database létrehozása az Azure blob storage-ban tárolt BACPAC-fájlból. A portál *csak* támogatja a BACPAC-fájl importálása az Azure blob storage-ban.

> [!NOTE]
> [Az Azure SQL Database felügyelt példányain](sql-database-managed-instance.md) támogatja az ebben a cikkben az egyéb módszerek használatával BACPAC-fájlból való importálása, de jelenleg nem támogatja az Azure Portalon való migrálás.

Importálását az Azure Portalon, nyissa meg a logikai kiszolgálót, amely fogja üzemeltetni az importálás, válassza az eszköztár lapját **adatbázis importálása**.  

   ![adatbázis importálása](./media/sql-database-import/import.png)

Válassza ki a tárfiókot, a tároló és a BACPAC-fájlba importálni kívánja. Adja meg az új adatbázis mérete (általában ugyanaz, mint forrás), és adja meg a cél SQL-kiszolgáló hitelesítő adatai. 

### <a name="monitor-imports-progress"></a>Az importálási folyamat figyelése

Az importálás folyamat állapotának monitorozásához nyissa meg az importált database logikai kiszolgáló oldalára, görgessen le a **beállítások** válassza **importálási/exportálási előzmények**. Ha sikeres, az importálás rendelkezik egy **befejezve** állapotát.

Annak ellenőrzéséhez, hogy az adatbázist a kiszolgálón az élő, válassza ki a **SQL-adatbázisok** és ellenőrizze, hogy az új adatbázis **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>SqlPackage használatával BACPAC-fájlból való importálása

Importálása egy SQL database-adatbázishoz a [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) parancssori segédprogram, lásd: [paramétereknek és tulajdonságoknak importálása](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties). SqlPackage tartalmaz a legújabb [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) és [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). Is letöltheti a legújabb [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) a Microsoft letöltőközpontból.

Méretezés és teljesítmény javasoljuk a legtöbb éles környezetben az SqlPackage használatával. További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

A következő SqlPackage parancs importálja a **AdventureWorks2008R2** adatbázist az Azure SQL Database logikai kiszolgáló nevű a helyi tárolóból **mynewserver20170403**. Létrehoz egy új adatbázist nevű **myMigratedDatabase** az egy **prémium szintű** szolgáltatási szint és a egy **P6** szolgáltatási célt. Válasszon a környezetének megfelelő értékeket módosíthatja.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Egy Azure SQL Database logikai kiszolgáló figyel az 1433-as porton. A céges tűzfal mögül logikai kiszolgálóhoz csatlakozik, a tűzfal nyissa meg ezt a portot kell rendelkeznie.
>

Ez a példa bemutatja, hogyan importálását az Active Directory univerzális hitelesítéssel az SqlPackage használatával.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>PowerShell használatával BACPAC-fájlból való importálása

Használja a [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) parancsmag egy importálása az Azure SQL Database szolgáltatás adatbázis-kérelem elküldéséhez. Adatbázis mérete, attól függően az importálás eltarthat egy ideig.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport 
    -ResourceGroupName "myResourceGroup" `
    -ServerName "myLogicalServer" `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName "myStorageAccount").Value[0] `
    -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "<your_server_admin_account_user_id>" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "<your_server_admin_account_password>" -AsPlainText -Force)

 ```

 Használhatja a [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) parancsmaggal ellenőrizheti az importálás folyamatban van. Futtassa a következő parancsmagot a kérelem után azonnal általában értéket ad vissza **állapota: InProgress**. Amikor megjelenik az importálás befejeződött **állapota: sikeres**.

```powershell
$importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress")
{
    $importStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}
[Console]::WriteLine("")
$importStatus
```

> [!TIP]
Egy másik példa parancsfájl: [adatbázis importálása BACPAC-fájlból](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Korlátozások

Egy adatbázis rugalmas adatbáziskészletben való importálása nem támogatott. Adatok importálása egy önálló adatbázis, és folytassa az adatbázis egy készletbe.

## <a name="import-using-wizards"></a>Importálja a varázslók használatával

Ezek a varázslók is használhatja.

- [Importálja az SQL Server Management Studio adatrétegbeli alkalmazás varázsló](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Az SQL Server importálása és exportálása varázslóban](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan csatlakozhat, és az importált SQL Database-adatbázis lekérdezéséhez, lásd: [a rövid útmutató: Azure SQL Database: az SQL Server Management Studio való csatlakozás és adatlekérdezés](sql-database-connect-query-ssms.md).
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- A teljes SQL Server adatbázis áttelepítési folyamat, beleértve a teljesítménnyel kapcsolatos javaslatok, lásd: [SQL Server-adatbázis áttelepítése az Azure SQL Database](sql-database-cloud-migrate.md).
- Megtudhatja, hogyan kezelésére és megosztására tárkulcsok és közös hozzáférésű jogosultságkódok biztonságosan, lásd: [Azure Storage biztonsági útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
