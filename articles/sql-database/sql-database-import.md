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
ms.date: 10/15/2018
ms.openlocfilehash: f48e9656aa2f562a1475bc5e0f6e81fdcbfda66a
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49361686"
---
# <a name="import-a-bacpac-file-to-a-new-azure-sql-database"></a>Az új Azure SQL-adatbázis BACPAC-fájl importálása

Mikor kell az adatbázis importálására az archívumot, vagy egy másik platformon áttelepítésekor az adatbázis-séma és adatok importálhatja egy [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) fájlt. BACPAC-fájl egy ZIP-fájlt tartalmazó a metaadatokat és az adatokat az SQL Server-adatbázis BACPAC kiterjesztési. BACPAC-fájl importálhatók az Azure blob storage (csak standard szintű storage) vagy a helyszíni helyen a helyi tárolóból. Az importálás sebesség maximalizálása azt javasoljuk, hogy adjon meg egy magasabb szolgáltatási szintre és méretét, például egy P6 szintű számítási és majd a sikeres importálása után szükség szerint lefelé skálázhatja. Az adatbázis kompatibilitási szintjét az importálás után is, a forrás-adatbázis kompatibilitási szintje alapján.

> [!IMPORTANT]
> Miután migrálta az adatbázist az Azure SQL Database-adatbázishoz, választhat, működtetheti az adatbázist, a jelenlegi kompatibilitási szinten (100. szint az AdventureWorks2008R2 adatbázis), vagy egy magasabb szinten. Az adatbázis meghatározott kompatibilitási szinten való működtetésének következményeivel és lehetőségeivel kapcsolatos további információkért lásd: [ADATBÁZIS kompatibilitási szintjének MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level). A kompatibilitási szintekkel kapcsolatos további adatbázisszintű beállításokról itt talál információt: [ADATBÁZISHOZ KÖTŐDŐ KONFIGURÁCIÓ MÓDOSÍTÁSA](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql).

## <a name="import-from-a-bacpac-file-using-azure-portal"></a>Az Azure portal használatával BACPAC-fájlból való importálása

Ez a cikk ismerteti az Azure SQL database létrehozása az Azure blob storage használatával tárolt BACPAC-fájlból való vonatkozó utasításokat a [az Azure portal](https://portal.azure.com). Importálás az Azure portal használatával támogatja a BACPAC-fájl importálása az Azure blob storage-ból.

Importálását az Azure portal használatával, nyissa meg a kiszolgálót (nem az adatbázis oldal) lapját társítsa az adatbázist, majd kattintson **importálása** az eszköztáron. Adja meg a tárfiókot és tárolót, és válassza ki az importálni kívánt BACPAC-fájlba. Válassza ki az új adatbázist (általában ugyanaz, mint forrás) méretét, és adja meg a cél SQL-kiszolgáló hitelesítő adatai.  

   ![adatbázis importálása](./media/sql-database-import/import.png)

Az importálási művelet állapotának figyelése, nyissa meg az importált adatbázist tartalmazó logikai kiszolgálóhoz tartozó lapon. Görgessen le a **műveletek** majd **Import/Export** előzményei.

> [!NOTE]
> [Az Azure SQL Database felügyelt példányain](sql-database-managed-instance.md) fájlt, ez a cikk azonban biztosítja az egyéb módszerek jelenleg nem támogatott importál egy BACPAC támogatja a migrálás az Azure portal használatával.

### <a name="monitor-the-progress-of-an-import-operation"></a>Az importálási művelet állapotának figyelése

Az importálási művelet állapotának figyelése, nyissa meg, amelybe az adatbázis importálása folyamatban van a logikai kiszolgálóhoz tartozó lapon. Görgessen le a **műveletek** majd **Import/Export** előzményei.

   ![importálása](./media/sql-database-import/import-history.png) ![importálás állapota](./media/sql-database-import/import-status.png)

Annak ellenőrzéséhez, hogy az adatbázist a kiszolgálón az élő, kattintson a **SQL-adatbázisok** és ellenőrizze, hogy az új adatbázis **Online**.

## <a name="import-from-a-bacpac-file-using-sqlpackage"></a>SQLPackage használatával BACPAC-fájlból való importálása

Importálása egy SQL database-adatbázishoz a [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) parancssori segédprogram, lásd: [paramétereknek és tulajdonságoknak importálása](https://docs.microsoft.com/sql/tools/sqlpackage#-import-parameters-and-properties). A legújabb tartalmaz az SQLPackage segédprogram [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) és [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), vagy letöltheti a legújabb [SqlPackage ](https://www.microsoft.com/download/details.aspx?id=53876) közvetlenül a Microsoft letöltőközpontból.

A méretezéshez és teljesítményhez a legtöbb éles környezetben az SQLPackage segédprogram használatát javasoljuk. További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Tekintse meg az alábbi SQLPackage parancsot egy parancsfájl például importálása a **AdventureWorks2008R2** adatbázist az Azure SQL Database logikai kiszolgáló nevű a helyi tárolóból **mynewserver20170403** Ebben a példában. Ez a szkript bemutatja egy új adatbázist nevű **myMigratedDatabase**, a szolgáltatási szintben a **prémium**, és a egy szolgáltatási célja **P6**. Ezeket az értékeket, a környezetnek megfelelően módosítani.

```cmd
SqlPackage.exe /a:import /tcs:"Data Source=mynewserver20170403.database.windows.net;Initial Catalog=myMigratedDatabase;User Id=ServerAdmin;Password=<change_to_your_password>" /sf:AdventureWorks2008R2.bacpac /p:DatabaseEdition=Premium /p:DatabaseServiceObjective=P6
```

> [!IMPORTANT]
> Egy Azure SQL Database logikai kiszolgáló figyel az 1433-as porton. Ha vállalati tűzfalon belülről szeretne csatlakozni egy Azure SQL Database logikai kiszolgálóhoz, ennek a portnak nyitva kell lennie a vállalati tűzfalon a sikeres csatlakozáshoz.
>

Ez a példa bemutatja, hogyan importálását az Active Directory univerzális hitelesítéssel az SqlPackage.exe használatával:

```cmd
SqlPackage.exe /a:Import /sf:testExport.bacpac /tdn:NewDacFX /tsn:apptestserver.database.windows.net /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="import-from-a-bacpac-file-using-powershell"></a>PowerShell használatával BACPAC-fájlból való importálása

Használja a [New-AzureRmSqlDatabaseImport](/powershell/module/azurerm.sql/new-azurermsqldatabaseimport) parancsmag egy importálása az Azure SQL Database szolgáltatás adatbázis-kérelem elküldéséhez. Az adatbázis méretétől függően az importálási művelet eltarthat egy ideig.

 ```powershell
 $importRequest = New-AzureRmSqlDatabaseImport -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "MyImportSample" `
    -DatabaseMaxSizeBytes "262144000" `
    -StorageKeyType "StorageAccessKey" `
    -StorageKey $(Get-AzureRmStorageAccountKey -ResourceGroupName "myResourceGroup" -StorageAccountName $storageaccountname).Value[0] `
    -StorageUri "http://$storageaccountname.blob.core.windows.net/importsample/sample.bacpac" `
    -Edition "Standard" `
    -ServiceObjectiveName "P6" `
    -AdministratorLogin "ServerAdmin" `
    -AdministratorLoginPassword $(ConvertTo-SecureString -String "ASecureP@assw0rd" -AsPlainText -Force)

 ```

Az importálási kérelem állapotának ellenőrzéséhez használja a [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) parancsmagot. Fut ez a kérelem után azonnal általában értéket ad vissza **állapota: InProgress**. Amikor látja **állapota: sikeres** az importálás befejeződött.

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

- Egy adatbázist a rugalmas készletben való importálása nem támogatott. Adatok importálása egy önálló adatbázis, és folytassa az adatbázis egy készletbe.

## <a name="import-using-other-methods"></a>Importálás az egyéb módszerek használatával

Ezek a varázslók is használhatja:

- [Importálja az SQL Server Management Studio adatrétegbeli alkalmazás varázsló](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database#using-the-import-data-tier-application-wizard).
- [Az SQL Server importálása és exportálása varázslóban](https://docs.microsoft.com/sql/integration-services/import-export-data/start-the-sql-server-import-and-export-wizard).

## <a name="next-steps"></a>További lépések

- Megtudhatja, hogyan csatlakozhat, és az importált SQL Database-adatbázis lekérdezéséhez, lásd: [SQL Database az SQL Server Management Studióval csatlakozhat, és végezze el a T-SQL-mintalekérdezés](sql-database-connect-query-ssms.md).
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- A teljes SQL Server adatbázis áttelepítési folyamat, beleértve a teljesítménnyel kapcsolatos javaslatok, lásd: [SQL Server-adatbázis áttelepítése az Azure SQL Database](sql-database-cloud-migrate.md).
- Megtudhatja, hogyan kezelésére és megosztására tárkulcsok és közös hozzáférésű jogosultságkódok biztonságosan, lásd: [Azure Storage biztonsági útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
