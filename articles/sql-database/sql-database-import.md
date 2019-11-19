---
title: BACPAC-fájl importálása adatbázis létrehozásához
description: Hozzon létre egy newAzure SQL-adatbázis BACPAC-fájl importálásával.
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
ms.openlocfilehash: 07949f0aac1d18b88eb3ac59a6e7ad91d6865fcd
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166306"
---
# <a name="quickstart-import-a-bacpac-file-to-a-database-in-azure-sql-database"></a>Gyors útmutató: BACPAC-fájl importálása Azure SQL Database-adatbázisba

[BACPAC](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/data-tier-applications#bacpac) -fájl használatával importálhat egy SQL Server-adatbázist Azure SQL Database adatbázisába. Az adatok importálhatók az Azure Blob Storage-ban (csak standard Storage) vagy helyi tárolóban tárolt `BACPAC` fájlból egy helyszíni helyen. Az importálási sebesség maximalizálása érdekében több és gyorsabb erőforrást biztosít, az adatbázist az importálási folyamat során az adatbázis magasabb szolgáltatási szintjére és számítási méretére méretezheti. Ezután az importálás sikeressége után le is méretezheti a méretezést.

> [!NOTE]
> Az importált adatbázis kompatibilitási szintjét a forrás-adatbázis-kompatibilitási szint alapján történik.
> [!IMPORTANT]
> Az adatbázis az importálás után kiválaszthatja, működtetheti az adatbázist, a jelenlegi kompatibilitási szinten (100. szint az AdventureWorks2008R2 adatbázis), vagy egy magasabb szinten. Az adatbázis meghatározott kompatibilitási szinten való működtetésének következményeivel és lehetőségeivel kapcsolatos további információkért lásd: [ADATBÁZIS kompatibilitási szintjének MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). A kompatibilitási szintekkel kapcsolatos további adatbázisszintű beállításokról itt talál információt: [ADATBÁZISHOZ KÖTŐDŐ KONFIGURÁCIÓ MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).

## <a name="import-from-a-bacpac-file-in-the-azure-portal"></a>Az Azure Portalon BACPAC-fájlból való importálása

Tekintse meg ezt a videót, amelyből megtudhatja, hogyan importálhat egy BACPAC-fájlból a Azure Portal vagy folytathatja az alábbi olvasást:

> [!VIDEO hhttps://channel9.msdn.com/Shows/Data-Exposed/Its-just-SQL-Restoring-a-database-to-Azure-SQL-DB-from-backup/player?WT.mc_id=dataexposed-c9-niner]

A [Azure Portal](https://portal.azure.com) *csak* az Azure Blob Storage-ban tárolt BACPAC *-* fájlokból hoz létre egyetlen adatbázist a Azure SQL Databaseban.

Az adatbázis áttelepítése [felügyelt példányra](sql-database-managed-instance.md) egy BACPAC-fájlból Azure PowerShell használatával jelenleg nem támogatott. Ehelyett használjon SQL Server Management Studio vagy SQLPackage.

> [!NOTE]
> A Azure Portalon vagy PowerShellen keresztül küldött importálási/exportálási kérelmeket feldolgozó gépeknek a BACPAC-fájlt, valamint az adatrétegbeli alkalmazás-keretrendszer (DacFX) által generált ideiglenes fájlokat kell tárolniuk. A szükséges lemezterület jelentősen eltér az azonos méretű adatbázisok között, és az adatbázis méretének háromszorosára lehet szükség. Az importálási/exportálási kérelmet futtató gépek csak 450GB helyi lemezterülettel rendelkeznek. Ennek eredményeképpen előfordulhat, hogy egyes kérések meghiúsulnak a `There is not enough space on the disk`hibával. Ebben az esetben a megkerülő megoldás a sqlpackage. exe futtatása egy olyan gépen, amelyen elegendő helyi lemezterület található. A probléma elkerülése érdekében javasoljuk, hogy a 150GB-nál nagyobb adatbázisok importálására/exportálására [SqlPackage](#import-from-a-bacpac-file-using-sqlpackage) használjon.
 
1. Ha a Azure Portal használatával szeretne importálni egy BACPAC-fájlból egy új önálló adatbázisba, nyissa meg a megfelelő adatbázis-kiszolgáló lapot, majd az eszköztáron válassza az **adatbázis importálása**lehetőséget.  

   ![Adatbázis import1](./media/sql-database-import/import1.png)

2. Válassza ki a Storage-fiókot és a tárolót a BACPAC fájlhoz, majd válassza ki azt a BACPAC-fájlt, amelyből importálni kívánja.
3. Adja meg az új adatbázis mérete (általában ugyanaz, mint forrás), és adja meg a cél SQL-kiszolgáló hitelesítő adatai. Az új Azure SQL Database-adatbázisok lehetséges értékeinek listáját az [adatbázis létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current)című témakörben tekintheti meg.

   ![Adatbázis import2](./media/sql-database-import/import2.png)

4. Kattintson az **OK** gombra.

5. Az importálás előrehaladásának figyeléséhez nyissa meg az adatbázis kiszolgáló lapját, és a **Beállítások**területen válassza az **Importálás/exportálás előzmények**elemet. Ha sikeres, az importálás rendelkezik egy **befejezve** állapotát.

   ![Adatbázis-importálás állapota](./media/sql-database-import/import-status.png)

6. Annak ellenőrzéséhez, hogy az adatbázis él-e az adatbázis-kiszolgálón, válassza az **SQL-adatbázisok** lehetőséget, és ellenőrizze, hogy az új adatbázis **online állapotú**

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>SqlPackage használatával BACPAC-fájlból való importálása

Ha SQL Server adatbázist szeretne importálni a [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) parancssori segédprogrammal, tekintse meg a [paraméterek és tulajdonságok importálása](https://docs.microsoft.com/sql/tools/sqlpackage#import-parameters-and-properties)című témakört. A SqlPackage a legújabb [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) és [SQL Server Data Tools a Visual studióhoz](https://msdn.microsoft.com/library/mt204009.aspx). Is letöltheti a legújabb [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) a Microsoft letöltőközpontból.

A méretezés és a teljesítmény érdekében javasoljuk, hogy a SqlPackage a legtöbb éles környezetben használja a Azure Portal használata helyett. SQL Server Customer tanácsadó csapatának blogja `BACPAC` fájlok használatával történő áttelepítéssel kapcsolatban lásd: [áttelepítés SQL Serverról Azure SQL Database BACPAC-fájlok használatával](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

A következő SqlPackage-parancs importálja a **AdventureWorks2008R2** -adatbázist a helyi tárolóból egy **mynewserver20170403**nevű Azure SQL Database-kiszolgálóra. Létrehoz egy új adatbázist nevű **myMigratedDatabase** az egy **prémium szintű** szolgáltatási szint és a egy **P6** szolgáltatási célt. Válasszon a környezetének megfelelő értékeket módosíthatja.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=<your_server_admin_account_user_id>;Password=<your_server_admin_account_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Ha egyetlen adatbázist kezelő SQL Database-kiszolgálóhoz szeretne csatlakozni egy vállalati tűzfal mögött, a tűzfalon meg kell nyitni a 1433-es portot. Felügyelt példányhoz való kapcsolódáshoz [pont – hely kapcsolat](sql-database-managed-instance-configure-p2s.md) vagy Express Route-kapcsolat szükséges.
>

Ez a példa bemutatja, hogyan importálását az Active Directory univerzális hitelesítéssel az SqlPackage használatával.

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-into-a-single-database-from-a-bacpac-file-using-powershell"></a>Importálás egyetlen adatbázisba egy BACPAC-fájlból a PowerShell használatával

> [!NOTE]
> [A felügyelt példányok](sql-database-managed-instance.md) jelenleg nem támogatják egy adatbázis példány-adatbázisba való áttelepítését egy BACPAC-fájlból Azure PowerShell használatával. Felügyelt példányba való importáláshoz használja a SQL Server Management Studio vagy a SQLPackage.

> [!NOTE]
> A portálon vagy a Powershellen keresztül küldött importálási/exportálási kérelmek feldolgozását végző gépeknek a bacpac-fájlt, valamint az adatrétegbeli alkalmazás-keretrendszer (DacFX) által generált ideiglenes fájlokat kell tárolniuk. A szükséges lemezterület jelentős mértékben változhat az azonos méretű adatbázisok között, és akár 3 alkalommal is eltarthat az adatbázis mérete. Az importálási/exportálási kérelmet futtató gépek csak 450GB helyi lemezterülettel rendelkeznek. Ennek eredményeképpen előfordulhat, hogy egyes kérések meghiúsulnak a "nincs elég hely a lemezen" hibaüzenet. Ebben az esetben a megkerülő megoldás a sqlpackage. exe futtatása egy olyan gépen, amelyen elegendő helyi lemezterület található. A 150GB-nál nagyobb adatbázisok importálásakor/exportálásakor a [SqlPackage](#import-from-a-bacpac-file-using-sqlpackage) használatával elkerülhető a probléma.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Az Azure SQL Database továbbra is támogatja a PowerShell Azure Resource Manager modult, de a jövőbeli fejlesztés az az. SQL-modulhoz készült. Ezekhez a parancsmagokhoz lásd: [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Az az modul és a AzureRm modulok parancsainak argumentumai lényegében azonosak.

A [New-AzSqlDatabaseImport](/powershell/module/az.sql/new-azsqldatabaseimport) parancsmag használatával küldje el az importálási adatbázisra vonatkozó kérelmet a Azure SQL Database szolgáltatásnak. Adatbázis mérete, attól függően az importálás eltarthat egy ideig.

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

 Az importálás folyamatát a [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) parancsmaggal tekintheti meg. Futtassa a következő parancsmagot a kérelem után azonnal általában értéket ad vissza **állapota: InProgress**. Amikor megjelenik az importálás befejeződött **állapota: sikeres**.

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

Egy adatbázis rugalmas adatbáziskészletben való importálása nem támogatott. Az adatimportálást egyetlen adatbázisba is importálhatja, majd az adatbázist egy rugalmas készletbe helyezheti át.

## <a name="import-using-wizards"></a>Importálja a varázslók használatával

Ezek a varázslók is használhatja.

- [Importálja az SQL Server Management Studio adatrétegbeli alkalmazás varázsló](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Az SQL Server importálása és exportálása varázslóban](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan csatlakozhat, és az importált SQL Database-adatbázis lekérdezéséhez, lásd: [a rövid útmutató: Azure SQL Database: az SQL Server Management Studio való csatlakozás és adatlekérdezés](sql-database-connect-query-ssms.md).
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://techcommunity.microsoft.com/t5/DataCAT/Migrating-from-SQL-Server-to-Azure-SQL-Database-using-Bacpac/ba-p/305407).
- A teljes SQL Server adatbázis áttelepítési folyamat, beleértve a teljesítménnyel kapcsolatos javaslatok, lásd: [SQL Server-adatbázis áttelepítése az Azure SQL Database](sql-database-single-database-migrate.md).
- Megtudhatja, hogyan kezelésére és megosztására tárkulcsok és közös hozzáférésű jogosultságkódok biztonságosan, lásd: [Azure Storage biztonsági útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
