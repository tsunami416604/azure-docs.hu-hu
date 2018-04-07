---
title: Azure SQL-adatbázis BACPAC fájlba exportálása |} Microsoft Docs
description: Egy Azure SQL database az Azure portál használatával BACPAC fájlba exportálása
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: load & move data
ms.date: 04/01/2018
ms.author: carlrab
ms.topic: article
ms.openlocfilehash: 7653f0953241fa0b4a1780075641fc3fe5570a23
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Azure SQL-adatbázis BACPAC fájlba exportálása

Ha archiválása vagy egy másik platform áthelyezése egy adatbázis-exportálási van szüksége, exportálhatja az adatbázisséma és az adatok egy [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) fájlt. Egy BACPAC egy ZIP-fájlt tartalmazó a metaadatokat és az SQL Server-adatbázis BACPAC kiterjesztésű fájl. Egy BACPAC-fájl az Azure blob storage vagy a helyi tároló helyszíni helyen tárolt, és később importálható vissza az Azure SQL Database vagy a helyszíni SQL Server telepítéséhez. 

> [!IMPORTANT] 
> Az Azure SQL adatbázis automatikus exportálása 2017. március 1. a lett visszavonva. Használhat [hosszú távú biztonsági másolatok megőrzésének](sql-database-long-term-retention.md
) vagy [Azure Automation](https://github.com/Microsoft/azure-docs/blob/2461f706f8fc1150e69312098640c0676206a531/articles/automation/automation-intro.md) rendszeres időközönként archiválja az SQL adatbázist a PowerShell használatával az Ön által választott ütemezés szerint. Mintát, töltse le a [PowerShell-mintaparancsfájl](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-automation-automated-export) a Githubról.
>

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Azure SQL-adatbázis exportálása szempontjai

* Az Exportálás tranzakciós úton konzisztens, gondoskodnia kell róla, hogy nincs írási tevékenység folyamatban van az exportálás során, vagy az exportálni kívánt egy [tranzakciós úton konzisztens másolat](sql-database-copy.md) az Azure SQL adatbázis.
* A blob storage exportálja, a maximális fájlméret BACPAC akkor 200 GB-os. Archiválására nagyobb BACPAC-fájl, exportálja a helyi tároló.
* Prémium szintű Azure Storage használata az ebben a cikkben ismertetett módszerek BACPAC-fájl exportálása nem támogatott.
* Ha az Azure SQL-adatbázisból az exportálási művelet meghaladja a 20 órát, előfordulhat, hogy lehet megszakítani. Az exportálás során a teljesítmény növelése érdekében a következőket teheti:
  * Ideiglenesen növelje meg a szolgáltatási szint.
  * Próbálkozást az összes olvasási és írási tevékenység az exportálás során.
  * Használja a [fürtözött index](https://msdn.microsoft.com/library/ms190457.aspx) összes nagy táblák nem null értékekkel. Fürtözött indexek nélkül exportálása meghiúsulhat, ha 6 – 12 óránál hosszabb ideig tart. Ennek az az oka az Exportálás szolgáltatásnak kell a tábla vizsgálathoz exportálásakor a teljes táblázatot. Egy jó módszer annak meghatározásához, ha a táblák vannak optimalizálva, az Exportálás futtatásához **DBCC SHOW_STATISTICS** , és győződjön meg arról, hogy a *RANGE_HI_KEY* értéke nem null, és az értéke megfelelő terjesztési. További információkért lásd: [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> BACPACs nem célja, hogy használható a biztonsági mentéshez és visszaállításhoz. Az Azure SQL Database biztonsági mentések minden felhasználói adatbázis automatikusan létrehozza. További információkért lásd: [üzleti folytonosság – áttekintés](sql-database-business-continuity.md) és [SQL-adatbázis biztonsági másolatait](sql-database-automated-backups.md).  
> 

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Az Azure portál használatával BACPAC fájlba exportálása

Egy adatbázis használatával exportálása a [Azure-portálon](https://portal.azure.com), nyissa meg az adatbázis lapját, és kattintson a **exportálása** az eszköztáron. BACPAC fájlnevet adja meg, adja meg Azure-tárfiók és tároló az exportálást, és adja meg a hitelesítő adatokat a forrás-adatbázishoz való kapcsolódáshoz.  

![Adatbázis exportálása](./media/sql-database-export/database-export.png)

Az exportálási művelet előrehaladásának figyeléséhez, nyissa meg az exportált adatbázist tartalmazó logikai kiszolgálóhoz tartozó lapon. Görgessen le a **műveletek** majd **Import/Export** előzményeit.

![exportálja az előzmények](./media/sql-database-export/export-history.png)
![exportálás előzmények állapota](./media/sql-database-export/export-history2.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>A SQLPackage segédprogrammal BACPAC fájlba exportálása

Egy SQL database segítségével exportálhatja a [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) parancssori segédprogram, lásd: [paraméterek és a Tulajdonságok exportálása](https://msdn.microsoft.com/library/hh550080.aspx#Export Parameters and Properties). A SQLPackage segédprogram érhető el a legújabb verziójú [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) és [SQL Server Data Tools for Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), vagy letöltheti a legújabb [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) közvetlenül a Microsoft letöltőközpontból.

Méretezés és teljesítmény a legtöbb éles környezetben a SQLPackage segédprogram használatát javasoljuk. További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Ez a példa bemutatja egy adatbázist SqlPackage.exe hitelesítéssel Active Directory univerzális exportálása:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS) használatával BACPAC fájlba exportálása

A legújabb SQL Server Management Studio is adjon meg egy Azure SQL-adatbázis BACPAC-fájlba exportálása varázsló. Tekintse meg a [egy adatrétegbeli alkalmazás exportálása](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>PowerShell-lel BACPAC fájlba exportálása

Használja a [New-AzureRmSqlDatabaseExport](/powershell/module/azurerm.sql/new-azurermsqldatabaseexport) parancsmagot, hogy küldje el az Exportálás adatbázis kérelmet az Azure SQL Database szolgáltatásban. Az adatbázis méretétől függően az exportálási művelet eltarthat egy ideig.

 ```powershell
 $exportRequest = New-AzureRmSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
   -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
   -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
 ```

Az exportálási kérelem állapotának ellenőrzéséhez használja a [Get-AzureRmSqlDatabaseImportExportStatus](/powershell/module/azurerm.sql/get-azurermsqldatabaseimportexportstatus) parancsmag. Fut a kérelem után azonnal általában értéket ad vissza **állapota: esetbejegyzések**. Amikor látja **állapota: sikeres** az Exportálás be nem fejeződik.

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

* Egy Azure SQL-adatbázis biztonsági másolatának hosszú távú biztonsági másolatok megőrzésének ahelyett, hogy archív célokra adatbázis exportálja, lásd: [hosszú távú biztonsági másolatok megőrzésének](sql-database-long-term-retention.md).
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
* SQL Server-adatbázishoz egy BACPAC importálásával kapcsolatos további tudnivalókért lásd: [egy BACPCAC importálása az SQL Server-adatbázishoz](https://msdn.microsoft.com/library/hh710052.aspx).
* Című témakörben olvashat a BACPAC exportálása egy SQL Server-adatbázist, [egy adatrétegbeli alkalmazás exportálása](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) és [telepítse át az első adatbázist](sql-database-migrate-your-sql-server-database.md).
* Ha exportálja az SQL Server, a prelude áttelepítése az Azure SQL Database, lásd: [egy SQL Server-adatbázis áttelepítése az Azure SQL Database](sql-database-cloud-migrate.md).
