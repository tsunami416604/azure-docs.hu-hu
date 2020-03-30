---
title: BACPAC-fájl importálása adatbázis létrehozásához
description: BacPAC-fájl importálásával hozzon létre egy újAzure-SQL-adatbázist.
services: sql-database
ms.service: sql-database
ms.subservice: migration
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/20/2019
ms.openlocfilehash: 05698596f966f879da1affc58af0122d08d519ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256236"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Rövid útmutató: BACPAC-fájl importálása adatbázisba az Azure SQL Database-ben

Az SQL Server-adatbázis t az Azure SQL Database-ben egy [BACPAC-fájl](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) használatával importálhatja. Az adatokat importálhatja `BACPAC` az Azure Blob storage -ban (csak normál tárterület) vagy a helyszíni helyen lévő helyi tárolóból tárolt fájlból. Az importálási sebesség maximalizálása érdekében több és gyorsabb erőforrás biztosításával az adatbázist magasabb szolgáltatási szintre és számítási méretre méretezheti az importálási folyamat során. Ezután az importálás sikeres végrehajtása után leskálázhatja.

> [!NOTE]
> Az importált adatbázis kompatibilitási szintje a forrásadatbázis kompatibilitási szintjétől függ.

> [!IMPORTANT]
> Az adatbázis importálása után dönthet úgy, hogy az adatbázist a jelenlegi kompatibilitási szintjén (az AdventureWorks2008R2 adatbázis 100-as szintjén) vagy magasabb szinten működteti. Az adatbázis meghatározott kompatibilitási szinten való működtetésének következményeivel és lehetőségeivel kapcsolatos további információkért lásd: [ADATBÁZIS kompatibilitási szintjének MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). A kompatibilitási szintekkel kapcsolatos további adatbázisszintű beállításokról itt talál információt: [ADATBÁZISHOZ KÖTŐDŐ KONFIGURÁCIÓ MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).

## <a name="using-azure-portal"></a>Az Azure Portal használata

Ebből a videóból megtudhatja, hogy miként importálhat BACPAC-fájlból az Azure Portalon, vagy hogyan olvashat tovább az alábbiakban:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

Az [Azure Portal](https://portal.azure.com) *csak* egyetlen adatbázis létrehozását támogatja az Azure SQL Database-ben, és *csak* az Azure Blob storage-ban tárolt BACPAC-fájlból.

Az azure PowerShell használatával egy BACPAC-fájlból [felügyelt példányba](sql-database-managed-instance.md) való áttelepítése jelenleg nem támogatott. Használja inkább az SQL Server Management Studio-t vagy az SQLPackage-t.

> [!NOTE]
> Az Azure Portalon vagy a PowerShellen keresztül küldött importálási/exportálási kérelmeket feldolgozó gépeknek tárolniuk kell a BACPAC-fájlt, valamint az adatszintű alkalmazáskeretrendszer (DacFX) által létrehozott ideiglenes fájlokat. A szükséges lemezterület jelentősen eltér az azonos méretű adatbázisok között, és az adatbázis méretének akár háromszorosát is megkövetelheti. Az importálási/exportálási kérelmet futtató gépek nek csak 450 GB helyi lemezterületük van. Ennek eredményeképpen egyes kérelmek sikertelenek `There is not enough space on the disk`lehetnek a hibával. Ebben az esetben a megoldás az sqlpackage.exe futtatása egy olyan számítógépen, amelyen elegendő a helyi lemezterület. Javasoljuk, hogy az SqlPackage használatával importáljon/exportáljon 150 GB-nál nagyobb adatbázisokat a probléma elkerülése érdekében.

1. Ha BACPAC-fájlból szeretne importálni egy új, egyetlen adatbázisba az Azure Portalon, nyissa meg a megfelelő adatbázis-kiszolgáló lapot, majd az eszköztáron válassza az **Adatbázis importálása**lehetőséget.  

   ![Adatbázis importálása1](./media/sql-database-import/sql-server-import-database.png)

1. Jelölje ki a BACPAC-fájl tárfiókot és tárolóját, majd jelölje ki azt a BACPAC-fájlt, amelyből importálni szeretne.

1. Adja meg az új adatbázisméretet (általában megegyezik az eredetivel), és adja meg a cél SQL Server hitelesítő adatait. Az új Azure SQL-adatbázis lehetséges értékeinek listáját az Adatbázis létrehozása című témakörben [tetszetős.](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)

   ![Adatbázis importálása2](./media/sql-database-import/sql-server-import-database-settings.png)

1. Kattintson az **OK** gombra.

1. Az importálás előrehaladásának figyeléséhez nyissa meg az adatbázis kiszolgálólapját, és a **Beállítások**csoportban válassza az **Importálás/exportálási előzmények lehetőséget.** Ha sikeres, az importálás **befejezett** állapotú.

   ![Adatbázis importálási állapota](./media/sql-database-import/sql-server-import-database-history.png)

1. Ha ellenőrizni szeretné, hogy az adatbázis él-e az adatbázis-kiszolgálón, jelölje ki az **SQL-adatbázisokat,** és ellenőrizze, hogy az új adatbázis **Online-e.**

## <a name="using-sqlpackage"></a>Az SqlPackage használata

Ha SQL Server adatbázist szeretne importálni az [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) parancssori segédprogrammal, olvassa el az importálási paraméterek és tulajdonságok című [témakört.](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties) Az SqlPackage rendelkezik a legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és SQL Server Data Tools for Visual Studio [rendszerrel.](https://msdn.microsoft.com/library/mt204009.aspx) A legújabb [SqlPackage csomagot](https://www.microsoft.com/download/details.aspx?id=53876) a Microsoft letöltőközpontjából is letöltheti.

A méretezés és a teljesítmény érdekében azt javasoljuk, hogy az SqlPackage a legtöbb éles környezetben, nem pedig az Azure Portal használata. A fájlok áttelepítésével `BACPAC` kapcsolatos SQL Server Customer Advisory Team-blogról az SQL Server [rendszerről az Azure SQL Database-re bacpacfájlok használatával című](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/)témakörben talál.

A következő SqlPackage parancs importálja az **AdventureWorks2008R2** adatbázist a helyi tárolóból egy Azure SQL Database **kiszolgálóra, a mynewserver20170403-ra.** Létrehoz egy új adatbázist **myMigratedDatabase** egy **prémium szolgáltatási** szint és egy **P6** szolgáltatási cél. Módosítsa ezeket az értékeket a környezetének megfelelően.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Ha egyetlen adatbázist egy vállalati tűzfal mögül kezel egy SQL Database-kiszolgálóhoz, a tűzfalnak meg kell nyitnia az 1433-as portot. Felügyelt példányhoz való csatlakozáshoz [pont-hely kapcsolattal](sql-database-managed-instance-configure-p2s.md) vagy gyorsútvonal-kapcsolattal kell rendelkeznie.

Ez a példa bemutatja, hogyan importálhat adatbázist az SqlPackage használatával az Active Directory univerzális hitelesítéssel.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>A PowerShell használata

> [!NOTE]
> [Egy felügyelt példány](sql-database-managed-instance.md) jelenleg nem támogatja az azure PowerShell használatával BACPAC-fájlból egy példányadatbázisba való áttelepítését. Felügyelt példányba történő importáláshoz használja az SQL Server Management Studio vagy az SQLPackage alkalmazást.

> [!NOTE]
> A portálon vagy a Powershellen keresztül benyújtott import/exportálási kérelmeket feldolgozó gépeknek tárolniuk kell a bacpac fájlt, valamint a Data-Tier Application Framework (DacFX) által létrehozott ideiglenes fájlokat. A szükséges lemezterület jelentősen eltér az azonos méretű DB-k között, és az adatbázis méretének akár háromszorosát is igénybe veheti. Az importálási/exportálási kérelmet futtató gépek nek csak 450 GB helyi lemezterületük van. Ennek eredményeképpen egyes kérelmek sikertelenek lehetnek a "Nincs elég hely a lemezen" hibaüzenettel. Ebben az esetben a megoldás az sqlpackage.exe futtatása egy olyan számítógépen, amelyen elegendő a helyi lemezterület. 150 GB-nál nagyobb adatbázisok importálásakor/exportálásakor az SqlPackage segítségével kerülje el ezt a problémát.

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

> [!IMPORTANT]
> A PowerShell Azure Resource Manager (RM) modul továbbra is támogatja az Azure SQL Database, de minden jövőbeli fejlesztés az Az.Sql modul. Az AzureRM-modul legalább 2020 decemberéig továbbra is megkapja a hibajavításokat.  Az Az modulban és az AzureRm-modulokban lévő parancsok argumentumai lényegében azonosak. A kompatibilitásukról az [Új Azure PowerShell Az modul bemutatása](/powershell/azure/new-azureps-module-az)című témakörben lehet további további további információkért.

A [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) parancsmag használatával importadatbázis-kérelmet küldhet az Azure SQL Database szolgáltatásnak. Az adatbázis méretétől függően az importálás végrehajtása eltarthat egy ideig.

```powershell
$importRequest = New-AzSqlDatabaseImport -ResourceGroupName "<resourceGroupName>" `
    -ServerName "<serverName>" -DatabaseName "<databaseName>" `
    -DatabaseMaxSizeBytes "<databaseSizeInBytes>" -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzStorageAccountKey `
        -ResourceGroupName "<resourceGroupName>" -StorageAccountName "<storageAccountName>").Value[0] `
        -StorageUri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
        -Edition "Standard" -ServiceObjectiveName "P6" `
        -AdministratorLogin "<userId>" `
        -AdministratorLoginPassword $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

A [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) parancsmag segítségével ellenőrizheti az importálás előrehaladását. A parancsmag futtatása közvetlenül a `Status: InProgress`kérés után általában visszatér . Az importálás akkor fejeződik be, amikor megjelenik a . `Status: Succeeded`

```powershell
$importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink

[Console]::Write("Importing")
while ($importStatus.Status -eq "InProgress") {
    $importStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $importRequest.OperationStatusLink
    [Console]::Write(".")
    Start-Sleep -s 10
}

[Console]::WriteLine("")
$importStatus
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Az [az-sql-db-import](/cli/azure/sql/db#az-sql-db-import) paranccsal importadatbázis-kérelmet küldhet az Azure SQL Database szolgáltatásnak. Az adatbázis méretétől függően az importálás végrehajtása eltarthat egy ideig.

```azurecli
# get the storage account key
az storage account keys list --resource-group "<resourceGroup>" --account-name "<storageAccount>"

az sql db import --resource-group "<resourceGroup>" --server "<server>" --name "<database>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p "<password>"
```

* * *

> [!TIP]
> Egy másik példa parancsfájlra vonatkozó [anamnézis: Adatbázis importálása BACPAC-fájlból](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Korlátozások

Rugalmas készletben lévő adatbázisba történő importálás nem támogatott. Importálhat adatokat egyetlen adatbázisba, majd áthelyezheti az adatbázist egy rugalmas készletbe.

## <a name="import-using-wizards"></a>Importálás varázslókkal

Ezeket a varázslókat is használhatja.

- [Adatrétegalkalmazás importálása varázsló az SQL Server Management Studio programban](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server importálása és exportálása varázsló](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>További lépések

- Az importált SQL-adatbázishoz való csatlakozásról és az adatok lekérdezéséről a [Rövid útmutató: Azure SQL Database: Az SQL Server Management Studio használata az adatok csatlakoztatásához és lekérdezéséhez](sql-database-connect-query-ssms.md)című témakörben olvashat.
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- A teljes SQL Server-adatbázis-áttelepítési folyamatról, beleértve a teljesítményjavaslatokat is, az [SQL Server adatbázis-áttelepítése az Azure SQL Database rendszerbe](sql-database-single-database-migrate.md)című témakörben található.
- A tárolási kulcsok és a megosztott hozzáférési aláírások biztonságos kezeléséről és megosztásáról az [Azure Storage biztonsági útmutatójában olvashat.](https://docs.microsoft.com/azure/storage/common/storage-security-guide)
