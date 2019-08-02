---
title: Egyetlen vagy készletezett Azure SQL-adatbázis exportálása BACPAC-fájlba | Microsoft Docs
description: Azure SQL Database-adatbázis exportálása BACPAC-fájlba a Azure Portal használatával
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.openlocfilehash: 9b4770f565f256d444ab6a6f06bb369b8417eb18
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68568252"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Azure SQL Database-adatbázis exportálása BACPAC-fájlba

Ha archiválni vagy egy másik platformra szeretne áthelyezni egy adatbázist, exportálhatja az adatbázis-sémát és az [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) -fájlba. A BACPAC-fájl egy ZIP-fájl, amely egy SQL Server-adatbázisból származó metaadatokat és adatokat tartalmazó BACPAC kiterjesztését tartalmazza. A BACPAC-fájlok az Azure Blob Storage-ban vagy helyi tárolóban tárolhatók, és később importálhatók Azure SQL Databaseba vagy egy SQL Server helyszíni telepítésbe.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Az Azure SQL Database-adatbázisok exportálásának szempontjai

- Ahhoz, hogy az Exportálás tranzakciós szempontból konzisztens legyen, meg kell győződnie arról, hogy az exportálás során nem történik írási tevékenység, vagy az Azure SQL Database [tranzakciós szempontból konzisztens másolatából](sql-database-copy.md) exportálja.
- Ha blob Storage-ba exportál, a BACPAC-fájlok maximális mérete 200 GB. Egy nagyobb BACPAC-fájl archiválásához exportálja a helyi tárolóba.
- BACPAC-fájlnak az Azure Premium Storage-ba való exportálása a cikkben ismertetett módszerek használatával nem támogatott.
- A tűzfal mögötti tárterület jelenleg nem támogatott.
- Ha az exportálási művelet Azure SQL Database meghaladja a 20 órát, előfordulhat, hogy a rendszer megszakítja a műveletet. Az exportálás során a teljesítmény növeléséhez a következőket teheti:

  - Ideiglenesen növelje a számítási méretet.
  - Az exportálás során minden olvasási és írási tevékenység megszűnik.
  - Használjon nem null értékű [fürtözött indexet](https://msdn.microsoft.com/library/ms190457.aspx) az összes nagyméretű táblán. Fürtözött indexek nélkül az Exportálás sikertelen lehet, ha 6-12 óránál hosszabb időt vesz igénybe. Ennek az az oka, hogy az exportálási szolgáltatásnak egy táblázatos vizsgálatot kell végrehajtania a teljes tábla exportálásához. Egy jó módszer annak megállapítására, hogy a táblák exportálásra vannak-e optimalizálva, hogy **DBCC SHOW_STATISTICS** fusson, és győződjön meg arról, hogy a *RANGE_HI_KEY* nem null értékű, és az értéke jó eloszlású. Részletekért lásd: [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> A BACPACs nem használhatók biztonsági mentési és visszaállítási műveletekhez. Azure SQL Database automatikusan készít biztonsági másolatokat minden felhasználói adatbázishoz. Részletekért lásd: [Üzletmenet-folytonosság áttekintése](sql-database-business-continuity.md) és [SQL Database biztonsági mentések](sql-database-automated-backups.md).

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Exportálás BACPAC-fájlba a Azure Portal használatával

A felügyelt példányokból származó BACPAC exportálása [](sql-database-managed-instance.md) a Azure PowerShell használatával jelenleg nem támogatott. Ehelyett használjon SQL Server Management Studio vagy SQLPackage.

> [!NOTE]
> A Azure Portalon vagy PowerShellen keresztül küldött importálási/exportálási kérelmeket feldolgozó gépeknek a BACPAC-fájlt, valamint az adatrétegbeli alkalmazás-keretrendszer (DacFX) által generált ideiglenes fájlokat kell tárolniuk. A szükséges lemezterület jelentősen eltér az azonos méretű adatbázisok között, és az adatbázis méretének háromszorosára lehet szükség. Az importálási/exportálási kérelmet futtató gépek csak 450GB helyi lemezterülettel rendelkeznek. Ennek eredményeképpen előfordulhat, hogy néhány kérelem meghiúsul a hibával `There is not enough space on the disk`. Ebben az esetben a megkerülő megoldás a sqlpackage. exe futtatása egy olyan gépen, amelyen elegendő helyi lemezterület található. A probléma elkerülése érdekében javasoljuk, hogy a 150GB-nál nagyobb adatbázisok importálására/exportálására [SqlPackage](#export-to-a-bacpac-file-using-the-sqlpackage-utility) használjon.

1. Ha a [Azure Portal](https://portal.azure.com)segítségével szeretne exportálni egy adatbázist, nyissa meg az adatbázis lapját, és kattintson az **Exportálás** gombra az eszköztáron.

   ![Adatbázis-exportálás](./media/sql-database-export/database-export1.png)

2. Adja meg a BACPAC fájlnevét, válasszon ki egy meglévő Azure Storage-fiókot és-tárolót az exportáláshoz, majd adja meg a megfelelő hitelesítő adatokat a forrás-adatbázishoz való hozzáféréshez. Ha Ön az Azure-rendszergazda, az Azure-rendszergazda nem felel meg SQL Server rendszergazdai engedélyekkel, akkor is szükség van egy SQL **Server-rendszergazdai bejelentkezésre** .

    ![Adatbázis-exportálás](./media/sql-database-export/database-export2.png)

3. Kattintson az **OK** gombra.

4. Az exportálási művelet előrehaladásának figyeléséhez nyissa meg az exportálni kívánt adatbázist tartalmazó SQL Database-kiszolgáló lapját. A **Beállítások** területen kattintson az **Importálás/exportálás előzmények**elemre.

   ![exportálási előzmények](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exportálás BACPAC-fájlba az SQLPackage segédprogram használatával

Ha az SQL Database-adatbázist a [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) parancssori segédprogrammal szeretné exportálni, tekintse meg a [paraméterek és tulajdonságok exportálása](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties)című témakört. A SQLPackage segédprogram a [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) és a SQL Server Data Tools legújabb verzióit tartalmazza a [Visual studióhoz](https://msdn.microsoft.com/library/mt204009.aspx), vagy letöltheti a [SQLPackage](https://www.microsoft.com/download/details.aspx?id=53876) legújabb verzióját közvetlenül a Microsoft letöltőközpontból.

A legtöbb éles környezetben a SQLPackage segédprogram használatát javasoljuk a méretezéshez és a teljesítményhez. További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Ez a példa azt szemlélteti, hogyan lehet exportálni egy adatbázist az SqlPackage. exe használatával Active Directory univerzális hitelesítéssel:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exportálás BACPAC-fájlba SQL Server Management Studio (SSMS) használatával

SQL Server Management Studio legújabb verziói egy varázslót biztosítanak egy Azure SQL Database-adatbázis BACPAC-fájlba való exportálásához. Lásd: [adatrétegbeli alkalmazás exportálása](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exportálás BACPAC-fájlba a PowerShell használatával

> [!NOTE]
> [A felügyelt példányok](sql-database-managed-instance.md) jelenleg nem támogatják az adatbázisok exportálását egy BACPAC-fájlba Azure PowerShell használatával. Felügyelt példány BACPAC-fájlba való exportálásához használja a SQL Server Management Studio vagy a SQLPackage.

A [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) parancsmag használatával küldje el az exportálási adatbázisra vonatkozó kérelmet a Azure SQL Database szolgáltatásnak. Az adatbázis méretétől függően az exportálási művelet végrehajtása hosszabb időt is igénybe vehet.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Az exportálási kérelem állapotának megtekintéséhez használja a [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) parancsmagot. Ezt azonnal futtatva, miután a kérelem **általában visszaadja az állapotot: Folyamatban**van. Amikor megjelenik **az állapot:**  Az Exportálás sikeresen befejeződött.

```powershell
$exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
[Console]::Write("Exporting")
while ($exportStatus.Status -eq "InProgress")
{
    Start-Sleep -s 10
    $exportStatus = Get-AzSqlDatabaseImportExportStatus -OperationStatusLink $exportRequest.OperationStatusLink
    [Console]::Write(".")
}
[Console]::WriteLine("")
$exportStatus
```

## <a name="next-steps"></a>További lépések

- Ha szeretné megtudni, hogy egy adatbázis és a készletezett adatbázisok hosszú távú biztonsági mentése az adatbázisok archiválási célú exportálásának alternatívájaként történjen, tekintse meg a [biztonsági másolatok hosszú távú megőrzését](sql-database-long-term-retention.md)ismertető témakört. Az SQL-ügynök feladatainak használatával ütemezheti az [adatbázis-másolatok biztonsági mentését](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) a hosszú távú biztonsági mentés alternatívájaként.
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- A BACPAC SQL Server adatbázisba való importálásával kapcsolatos további tudnivalókért lásd: [BACPAC importálása SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)-adatbázisba.
- A BACPAC SQL Server adatbázisból való exportálásával kapcsolatos információkért lásd: [adatrétegbeli alkalmazás exportálása](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
- Ha szeretné megtudni, hogyan lehet az adatáttelepítési szolgáltatást egy adatbázis áttelepítésére használni, olvassa el a [SQL Server migrálás Azure SQL Database offline állapotba a DMS használatával](../dms/tutorial-sql-server-to-azure-sql.md)című témakör
- Ha az SQL Server-ból exportálja a Azure SQL Databaseba való áttelepítést, olvassa el [a SQL Server adatbázis áttelepítése Azure SQL Databasere](sql-database-single-database-migrate.md)című témakört.
- Megtudhatja, hogyan kezelésére és megosztására tárkulcsok és közös hozzáférésű jogosultságkódok biztonságosan, lásd: [Azure Storage biztonsági útmutatóját](https://docs.microsoft.com/azure/storage/common/storage-security-guide).
