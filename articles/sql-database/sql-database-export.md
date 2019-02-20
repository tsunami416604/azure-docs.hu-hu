---
title: Exportálás egyetlen vagy készletezett Azure SQL-adatbázis BACPAC-fájlba |} A Microsoft Docs
description: Azure SQL-adatbázis exportálása BACPAC-fájlba az Azure portal használatával
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: carlrab
manager: craigg
ms.date: 02/18/2019
ms.openlocfilehash: 757d7e039b24beb170545d8055bad16410cf7883
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56415884"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Azure SQL-adatbázis exportálása BACPAC-fájlba

Archiválás vagy áthelyezése egy másik platformon-adatbázisok exportálását van szüksége, amikor az adatbázis-séma és adatok exportálhatja egy [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) fájlt. BACPAC-fájl egy ZIP-fájlt tartalmazó a metaadatokat és az adatokat az SQL Server-adatbázis BACPAC kiterjesztési. Az Azure Blob storage-ban vagy a helyi tárban a helyszíni helyen tárolhatók egy BACPAC-fájlba, és később importált vissza az Azure SQL Database vagy SQL-kiszolgáló helyi telepítése.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Azure SQL-adatbázis exportálása szempontjai

- Az exportálási tranzakciós szempontból konzisztens, gondoskodnia kell arról, hogy nincs írási tevékenység történik az exportálás során, vagy az exportálni kívánt egy [tranzakciós szempontból konzisztens másolatot](sql-database-copy.md) az Azure SQL-adatbázis.
- Ha exportálja a blob storage, a BACPAC-fájl maximális mérete 200 GB-os. Archiválása BACPAC-fájl, exportálja a helyi tárterület.
- Ebben a cikkben ismertetett módszerek használatával az Azure premium storage egy BACPAC-fájlba exportálása nem támogatott.
- Ha az Azure SQL Database-ből az exportálási művelet meghaladja a 20 óra, előfordulhat, hogy lehet megszakítani. Az exportálás során a teljesítmény növelése érdekében a következőket teheti:

  - Ideiglenesen növelheti a számítási méret.
  - Cease összes olvasási és írási tevékenység, az exportálás során.
  - Használja a [fürtözött index](https://msdn.microsoft.com/library/ms190457.aspx) az összes nagy táblák null értékű. Fürtözött indexek nélkül exportálása meghiúsulhat, ha a 6 és 12 óránál hosszabb ideig tart. Ennek az oka az exportálási szolgáltatás el kell végeznie egy tábla beolvasásával szeretné kipróbálni az egész tábla exportálása. Határozza meg, ha a táblák optimalizált Exportálás futtatása van egy jó módszer **DBCC SHOW_STATISTICS** , és ellenőrizze, hogy a *RANGE_HI_KEY* nem null, és annak értéke helyes terjesztési. További információkért lásd: [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs használható biztonsági mentési és visszaállítási műveletek nem tartozhat. Az Azure SQL Database automatikusan létrehoz minden felhasználói adatbázis biztonsági mentését. További információkért lásd: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md) és [SQL Database biztonsági mentéseinek](sql-database-automated-backups.md).

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Az Azure portal használatával egy BACPAC-fájlba exportálása

> [!NOTE]
> [Felügyelt példány](sql-database-managed-instance.md) jelenleg nem támogatja az adatbázis exportálása BACPAC-fájlba az Azure portal használatával. A felügyelt példány egy BACPAC-fájlba exportálni, használja az SQL Server Management Studio vagy az SQLPackage.

1. Egy adatbázis használatával történő exportálás, az [az Azure portal](https://portal.azure.com), nyissa meg az adatbázishoz tartozó lap, és kattintson a **exportálása** az eszköztáron.

   ![adatbázis exportálása](./media/sql-database-export/database-export1.png)

2. Adja meg a BACPAC-fájl nevére, válassza ki egy meglévő Azure storage-fiókot és a tároló az exportáláshoz, és adja meg a megfelelő hitelesítő adatokat a forrás-adatbázishoz való hozzáférés.

    ![adatbázis exportálása](./media/sql-database-export/database-export2.png)

3. Kattintson az **OK** gombra.

4. Az exportálási művelet állapotának figyelése, nyissa meg az exportált adatbázist tartalmazó SQL Database kiszolgálóhoz tartozó lapon. Alatt a **beállítások** majd **importálási/exportálási előzmények**.

   ![Előzmények exportálása](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exportálás BACPAC-fájlba az SQLPackage segédprogram használatával

Exportálhatja a egy SQL database-adatbázishoz a [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) parancssori segédprogram, lásd: [paramétereknek és tulajdonságoknak exportálása](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). A legújabb tartalmaz az SQLPackage segédprogram [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) és [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), vagy letöltheti a legújabb [SqlPackage ](https://www.microsoft.com/download/details.aspx?id=53876) közvetlenül a Microsoft letöltőközpontból.

A méretezéshez és teljesítményhez a legtöbb éles környezetben az SQLPackage segédprogram használatát javasoljuk. További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Ebben a példában az Active Directory univerzális hitelesítéssel az SqlPackage.exe használatával-adatbázisok exportálását mutatja be:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exportálás BACPAC-fájlba az SQL Server Management Studio (SSMS) használatával

Az SQL Server Management Studio legújabb verzióiban elérhető egy varázslón Azure SQL-adatbázis exportálása BACPAC-fájlba. Tekintse meg a [egy adatrétegbeli alkalmazás exportálása](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>A PowerShell használatával BACPAC-fájlba exportálása

> [!NOTE]
> [Felügyelt példány](sql-database-managed-instance.md) jelenleg nem támogatja az adatbázis exportálása BACPAC-fájlba az Azure PowerShell használatával. A felügyelt példány egy BACPAC-fájlba exportálni, használja az SQL Server Management Studio vagy az SQLPackage.

Használja a [New-AzureRmSqlDatabaseExport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport) parancsmagot, hogy küldje el az Azure SQL Database szolgáltatás egy adatbázis exportálási kérelmet. Az adatbázis méretétől függően az exportálási művelet eltarthat egy ideig.

```powershell
$exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Az exportálási kérelem állapotának ellenőrzéséhez használja a [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) parancsmagot. Fut ez a kérelem után azonnal általában értéket ad vissza **állapota: InProgress**. Amikor látja **állapota: Sikeres** az exportálás befejeződött.

```powershell
$exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzureRmSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>További lépések

- További információ az egyes hosszú távú adatmegőrzés és készletezett adatbázisok helyett exportálja egy adatbázis archiválási célú lásd [hosszú távú adatmegőrzés](sql-database-long-term-retention.md). Használhatja az SQL Agent-feladatok ütemezése [csak másolatot adatbázis biztonsági másolatait](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) hosszú távú adatmegőrzés alternatívájaként.
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- Az SQL Server-adatbázis egy BACPAC importálásával kapcsolatos további információkért lásd: [SQL Server-adatbázis BACPAC importálása](https://msdn.microsoft.com/library/hh710052.aspx).
- SQL Server-adatbázis exportálása BACPAC kapcsolatos további információkért lásd: [egy adatrétegbeli alkalmazás exportálása](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Adatbázis áttelepítése a Data Migration Service használatával kapcsolatos további információkért lásd: [SQL Server Migrálása az Azure SQL Database-kapcsolat nélküli módban a DMS használatával](../dms/tutorial-sql-server-to-azure-sql.md).
- Ha exportálja az SQL Serverből, egy prelude áttelepítése az Azure SQL Database, [SQL Server-adatbázis áttelepítése az Azure SQL Database](sql-database-single-database-migrate.md).
- Megtudhatja, hogyan kezelésére és megosztására tárkulcsok és közös hozzáférésű jogosultságkódok biztonságosan, lásd: [Azure Storage biztonsági útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
