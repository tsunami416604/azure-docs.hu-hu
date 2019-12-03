---
title: BACPAC-fájl importálása adatbázis létrehozásához
description: Hozzon létre egy newAzure SQL-adatbázist egy BACPAC-fájl importálásával.
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
ms.openlocfilehash: 4d6997475099420319c52abbbce34b2756e215ed
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74687667"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Gyors útmutató: BACPAC-fájl importálása Azure SQL Database-adatbázisba

[BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) -fájl használatával importálhat egy SQL Server-adatbázist Azure SQL Database adatbázisába. Az adatok importálhatók az Azure Blob Storage-ban (csak standard Storage) vagy helyi tárolóban tárolt `BACPAC` fájlból egy helyszíni helyen. Az importálási sebesség maximalizálása érdekében több és gyorsabb erőforrást biztosít, az adatbázist az importálási folyamat során az adatbázis magasabb szolgáltatási szintjére és számítási méretére méretezheti. Ezután az importálás sikeressége után le is méretezheti a méretezést.

> [!NOTE]
> Az importált adatbázis kompatibilitási szintje a forrás adatbázis kompatibilitási szintjén alapul.

> [!IMPORTANT]
> Az adatbázis importálása után úgy is dönthet, hogy az adatbázist az aktuális kompatibilitási szinten (a AdventureWorks2008R2-adatbázis 100-as szintjén) vagy magasabb szinten szeretné használni. Az adatbázis meghatározott kompatibilitási szinten való működtetésének következményeivel és lehetőségeivel kapcsolatos további információkért lásd: [ADATBÁZIS kompatibilitási szintjének MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). A kompatibilitási szintekkel kapcsolatos további adatbázisszintű beállításokról itt talál információt: [ADATBÁZISHOZ KÖTŐDŐ KONFIGURÁCIÓ MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).

## <a name="using-azure-portal"></a>Az Azure Portal használata

Tekintse meg ezt a videót, amelyből megtudhatja, hogyan importálhat egy BACPAC-fájlból a Azure Portal vagy folytathatja az alábbi olvasást:

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

A [Azure Portal](https://portal.azure.com) *csak* az Azure Blob Storage-ban tárolt BACPAC *-* fájlokból hoz létre egyetlen adatbázist a Azure SQL Databaseban.

Az adatbázis áttelepítése [felügyelt példányra](sql-database-managed-instance.md) egy BACPAC-fájlból Azure PowerShell használatával jelenleg nem támogatott. Ehelyett használjon SQL Server Management Studio vagy SQLPackage.

> [!NOTE]
> A Azure Portalon vagy PowerShellen keresztül küldött importálási/exportálási kérelmeket feldolgozó gépeknek a BACPAC-fájlt, valamint az adatrétegbeli alkalmazás-keretrendszer (DacFX) által generált ideiglenes fájlokat kell tárolniuk. A szükséges lemezterület jelentősen eltér az azonos méretű adatbázisok között, és az adatbázis méretének háromszorosára lehet szükség. Az importálási/exportálási kérelmet futtató gépek csak 450GB helyi lemezterülettel rendelkeznek. Ennek eredményeképpen előfordulhat, hogy egyes kérések meghiúsulnak a `There is not enough space on the disk`hibával. Ebben az esetben a megkerülő megoldás a sqlpackage. exe futtatása egy olyan gépen, amelyen elegendő helyi lemezterület található. A probléma elkerülése érdekében javasoljuk, hogy a 150GB-nál nagyobb adatbázisok importálására/exportálására SqlPackage használjon.

1. Ha a Azure Portal használatával szeretne importálni egy BACPAC-fájlból egy új önálló adatbázisba, nyissa meg a megfelelő adatbázis-kiszolgáló lapot, majd az eszköztáron válassza az **adatbázis importálása**lehetőséget.  

   ![Adatbázis import1](./media/sql-database-import/import1.png)

1. Válassza ki a Storage-fiókot és a tárolót a BACPAC fájlhoz, majd válassza ki azt a BACPAC-fájlt, amelyből importálni kívánja.

1. Adja meg az új adatbázis méretét (általában megegyezik a forrással), és adja meg a cél SQL Server hitelesítő adatokat. Az új Azure SQL Database-adatbázisok lehetséges értékeinek listáját az [adatbázis létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)című témakörben tekintheti meg.

   ![Adatbázis import2](./media/sql-database-import/import2.png)

1. Kattintson az **OK** gombra.

1. Az importálás előrehaladásának figyeléséhez nyissa meg az adatbázis kiszolgáló lapját, és a **Beállítások**területen válassza az **Importálás/exportálás előzmények**elemet. Ha a művelet sikeres, az importálás **Befejezve** állapotba kerül.

   ![Adatbázis-importálás állapota](./media/sql-database-import/import-status.png)

1. Annak ellenőrzéséhez, hogy az adatbázis él-e az adatbázis-kiszolgálón, válassza az **SQL-adatbázisok** lehetőséget, és ellenőrizze, hogy az új adatbázis **online állapotú**

## <a name="using-sqlpackage"></a>A SqlPackage használata

Ha SQL Server adatbázist szeretne importálni a [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) parancssori segédprogrammal, tekintse meg a [paraméterek és tulajdonságok importálása](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)című témakört. A SqlPackage a legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és [SQL Server Data Tools a Visual studióhoz](https://msdn.microsoft.com/library/mt204009.aspx). A legújabb [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) a Microsoft letöltőközpontból is letöltheti.

A méretezés és a teljesítmény érdekében javasoljuk, hogy a SqlPackage a legtöbb éles környezetben használja a Azure Portal használata helyett. SQL Server Customer tanácsadó csapatának blogja `BACPAC` fájlok használatával történő áttelepítéssel kapcsolatban lásd: [áttelepítés SQL Serverról Azure SQL Database BACPAC-fájlok használatával](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

A következő SqlPackage-parancs importálja a **AdventureWorks2008R2** -adatbázist a helyi tárolóból egy **mynewserver20170403**nevű Azure SQL Database-kiszolgálóra. Létrehoz egy **myMigratedDatabase** nevű új adatbázist egy **prémium** szintű szolgáltatási szinttel és egy **P6** szolgáltatás-célkitűzéssel. A környezetnek megfelelően módosítsa ezeket az értékeket.

```cmd
sqlpackage.exe /a:import /tcs:"Data Source=<serverName>.database.windows.net;Initial Catalog=<migratedDatabase>;User Id=<userId>;Password=<password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Ha egyetlen adatbázist kezelő SQL Database-kiszolgálóhoz szeretne csatlakozni egy vállalati tűzfal mögött, a tűzfalon meg kell nyitni a 1433-es portot. Felügyelt példányhoz való kapcsolódáshoz [pont – hely kapcsolat](sql-database-managed-instance-configure-p2s.md) vagy Express Route-kapcsolat szükséges.

Ez a példa bemutatja, hogyan importálhat egy adatbázist az SqlPackage használatával Active Directory univerzális hitelesítéssel.

```cmd
sqlpackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="using-powershell"></a>A PowerShell használata

> [!NOTE]
> [A felügyelt példányok](sql-database-managed-instance.md) jelenleg nem támogatják egy adatbázis példány-adatbázisba való áttelepítését egy BACPAC-fájlból Azure PowerShell használatával. Felügyelt példányba való importáláshoz használja a SQL Server Management Studio vagy a SQLPackage.

> [!NOTE]
> A portálon vagy a Powershellen keresztül küldött importálási/exportálási kérelmek feldolgozását végző gépeknek a bacpac-fájlt, valamint az adatrétegbeli alkalmazás-keretrendszer (DacFX) által generált ideiglenes fájlokat kell tárolniuk. A szükséges lemezterület jelentős mértékben változhat az azonos méretű adatbázisok között, és akár 3 alkalommal is eltarthat az adatbázis mérete. Az importálási/exportálási kérelmet futtató gépek csak 450GB helyi lemezterülettel rendelkeznek. Ennek eredményeképpen előfordulhat, hogy egyes kérések meghiúsulnak a "nincs elég hely a lemezen" hibaüzenet. Ebben az esetben a megkerülő megoldás a sqlpackage. exe futtatása egy olyan gépen, amelyen elegendő helyi lemezterület található. A 150GB-nál nagyobb adatbázisok importálásakor/exportálásakor a SqlPackage használatával elkerülhető a probléma.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager (RM) modult, de a jövőbeli fejlesztés az az. SQL modulhoz kapcsolódik. A AzureRM modul továbbra is megkapja a hibajavításokat, amíg legalább december 2020-ra nem kerül sor.  Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak. A kompatibilitással kapcsolatos további információkért lásd: [az új Azure PowerShell bemutatása az Module](/powershell/azure/new-azureps-module-az).

A [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) parancsmag használatával küldje el az importálási adatbázisra vonatkozó kérelmet a Azure SQL Database szolgáltatásnak. Az adatbázis méretétől függően az importálás hosszabb időt is igénybe vehet.

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

Az importálás folyamatát a [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) parancsmaggal tekintheti meg. Ha a parancsmagot közvetlenül a kérést követően futtatja, a rendszer általában `Status: InProgress`t ad vissza. Az importálás akkor fejeződik be, amikor megjelenik a `Status: Succeeded`.

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

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Az az [-SQL-db-import](/cli/azure/sql/db#az-sql-db-import) paranccsal küldje el az importálási adatbázisra vonatkozó kérelmet a Azure SQL Database szolgáltatásnak. Az adatbázis méretétől függően az importálás hosszabb időt is igénybe vehet.

```azure-cli
# get the storage account key
az storage account keys list --resource-group "<resourceGroupName>" --account-name "<storageAccountName>"

az sql db import --resource-group "<resourceGroupName>" --server "<serverName>" --name "<databaseName>" `
    --storage-key-type "StorageAccessKey" --storage-key "<storageAccountKey>" `
    --storage-uri "https://myStorageAccount.blob.core.windows.net/importsample/sample.bacpac" `
    -u "<userId>" -p $(ConvertTo-SecureString -String "<password>" -AsPlainText -Force)
```

* * *

> [!TIP]
> Egy másik parancsfájl-példa: [adatbázis importálása BACPAC-fájlból](scripts/sql-database-import-from-bacpac-powershell.md).

## <a name="limitations"></a>Korlátozások

A rugalmas készletben lévő adatbázisba való importálás nem támogatott. Az adatimportálást egyetlen adatbázisba is importálhatja, majd az adatbázist egy rugalmas készletbe helyezheti át.

## <a name="import-using-wizards"></a>Importálás varázslók használatával

Ezeket a varázslókat is használhatja.

- [Adatrétegbeli alkalmazás importálása varázsló SQL Server Management Studioban](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [SQL Server Importálás és exportálás varázsló](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Következő lépések

- Az importált SQL Databasehoz való kapcsolódással és lekérdezéssel kapcsolatos információkért lásd: gyors útmutató [: Azure SQL Database: a SQL Server Management Studio használata a kapcsolódáshoz és az adatlekérdezéshez](sql-database-connect-query-ssms.md).
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- A teljes SQL Server adatbázis-áttelepítési folyamatról, beleértve a teljesítménnyel kapcsolatos ajánlásokat is, tekintse meg a [SQL Server adatbázis áttelepítésének Azure SQL Database](sql-database-single-database-migrate.md).
- A tárolási kulcsok és a közös hozzáférési aláírások biztonságos kezelésével és megosztásával kapcsolatos további információkért lásd: az [Azure Storage biztonsági útmutatója](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
