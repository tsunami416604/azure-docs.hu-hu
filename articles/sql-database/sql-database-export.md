---
title: Egyetlen vagy készletbe adott adatbázis exportálása BACPAC-fájlba
description: Azure SQL-adatbázis exportálása BACPAC-fájlba az Azure Portal használatával
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/16/2019
ms.topic: conceptual
ms.openlocfilehash: 0bc72f0ad58829a3ff6545b5c4741ddc20916c31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061630"
---
# <a name="export-an-azure-sql-database-to-a-bacpac-file"></a>Azure SQL-adatbázis exportálása BACPAC-fájlba

Ha archiválásvagy másik platformra való áthelyezés érdekében adatbázist kell exportálnia, exportálhatja az adatbázissémát és az adatokat [BACPAC-fájlba.](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) A BACPAC fájl egy ZIP fájl kiterjesztése BACPAC tartalmazó metaadatok és adatok egy SQL Server adatbázis. A BACPAC-fájlok tárolhatók az Azure Blob storage-ban vagy a helyi tárolóban egy helyszíni helyen, és később importálhatók az Azure SQL Database-be vagy egy SQL Server helyszíni telepítésbe.

## <a name="considerations-when-exporting-an-azure-sql-database"></a>Az Azure SQL-adatbázisok exportálásának szempontjai

- Ahhoz, hogy az exportálás tranzakcióskonzisztens legyen, biztosítania kell, hogy az exportálás során ne történjen írási tevékenység, vagy hogy az Azure SQL-adatbázis [tranzakciósan konzisztens példányát](sql-database-copy.md) exportálja.
- Ha blob storage-ba exportál, a BACPAC-fájl maximális mérete 200 GB. Nagyobb BACPAC-fájl archiválásához exportálja a helyi tárolóba.
- BACPAC-fájl exportálása az Azure prémium szintű tárhelyére a cikkben tárgyalt módszerekkel nem támogatott.
- A tűzfal mögötti tárolás jelenleg nem támogatott.
- Ha az Azure SQL Database-ből történő exportálási művelet meghaladja a 20 órát, előfordulhat, hogy megszakad. Az exportálás során a teljesítmény növelése érdekében a következőket teheti:

  - Ideiglenesen növelje a számítási méretet.
  - Az exportálás során hagyjon fel minden olvasási és írási tevékenységgel.
  - Használjon [fürtözött indexet](https://msdn.microsoft.com/library/ms190457.aspx) nem null értékekkel minden nagy táblában. Fürtözött indexek nélkül az exportálás sikertelen lehet, ha 6–12 óránál tovább tart. Ennek az az oka, hogy az exportszolgáltatásnak egy táblavizsgálattal kell próbálkoznia a teljes tábla exportálásához. Egy jó módszer annak megállapítására, hogy a táblák exportálásra vannak-e optimalizálva, ha a **DBCC SHOW_STATISTICS** fut, és győződjön meg arról, hogy a *RANGE_HI_KEY* nem null értékű, és értéke jó eloszlással rendelkezik. További részletek: [DBCC SHOW_STATISTICS](https://msdn.microsoft.com/library/ms174384.aspx).

> [!NOTE]
> A BACPAC-ok nem biztonsági mentési és visszaállítási műveletekhez használhatók. Az Azure SQL Database automatikusan biztonsági másolatokat hoz létre minden felhasználói adatbázishoz. További információt az [üzletmenet folytonosságának áttekintése](sql-database-business-continuity.md) és az SQL Database biztonsági mentései című [témakörben talál.](sql-database-automated-backups.md)

## <a name="export-to-a-bacpac-file-using-the-azure-portal"></a>Exportálás BACPAC-fájlba az Azure Portal használatával

Jelenleg nem támogatott egy adatbázis BACPAC-exportálása az Azure PowerShell használatával felügyelt [példányból.](sql-database-managed-instance.md) Használja inkább az SQL Server Management Studio-t vagy az SQLPackage-t.

> [!NOTE]
> Az Azure Portalon vagy a PowerShellen keresztül küldött importálási/exportálási kérelmeket feldolgozó gépeknek tárolniuk kell a BACPAC-fájlt, valamint az adatszintű alkalmazáskeretrendszer (DacFX) által létrehozott ideiglenes fájlokat. A szükséges lemezterület jelentősen eltér az azonos méretű adatbázisok között, és az adatbázis méretének akár háromszorosát is megkövetelheti. Az importálási/exportálási kérelmet futtató gépek nek csak 450 GB helyi lemezterületük van. Ennek eredményeképpen egyes kérelmek sikertelenek `There is not enough space on the disk`lehetnek a hibával. Ebben az esetben a megoldás az sqlpackage.exe futtatása egy olyan számítógépen, amelyen elegendő a helyi lemezterület. Javasoljuk, hogy az [SqlPackage](#export-to-a-bacpac-file-using-the-sqlpackage-utility) használatával importáljon/exportáljon 150 GB-nál nagyobb adatbázisokat a probléma elkerülése érdekében.

1. Ha az Azure [Portal](https://portal.azure.com)használatával szeretne exportálni egy adatbázist, nyissa meg az adatbázis lapját, és kattintson az **eszköztár Exportálás** gombjára.

   ![Adatbázis exportálása](./media/sql-database-export/database-export1.png)

2. Adja meg a BACPAC-fájlnevet, válasszon ki egy meglévő Azure-tárfiókot és tárolót az exportáláshoz, majd adja meg a megfelelő hitelesítő adatokat a forrásadatbázishoz való hozzáféréshez. Az SQL **Server rendszergazdai bejelentkezésitt** van szükség, még akkor is, ha az Azure-rendszergazda, mivel az Azure-rendszergazda nem egyenlő az SQL Server felügyeleti engedélyekkel.

    ![Adatbázis exportálása](./media/sql-database-export/database-export2.png)

3. Kattintson az **OK** gombra.

4. Az exportálási művelet előrehaladásának figyeléséhez nyissa meg az exportálandó adatbázist tartalmazó SQL Database-kiszolgáló lapját. A **Beállítások csoportban** kattintson **az Előzmények importálása/exportálása gombra.**

   ![exportálási előzmények](./media/sql-database-export/export-history.png)

## <a name="export-to-a-bacpac-file-using-the-sqlpackage-utility"></a>Exportálás BACPAC-fájlba az SQLPackage segédprogrammal

Sql-adatbázis exportálása az [SqlPackage](https://docs.microsoft.com/sql/tools/sqlpackage) parancssori segédprogrammal: [Paraméterek és tulajdonságok exportálása](https://docs.microsoft.com/sql/tools/sqlpackage#export-parameters-and-properties). Az SQLPackage segédprogram az [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) és az SQL Server Data Tools for Visual [Studio](https://msdn.microsoft.com/library/mt204009.aspx)legújabb verzióival, vagy az [SqlPackage](https://www.microsoft.com/download/details.aspx?id=53876) legújabb verzióját közvetlenül a Microsoft letöltőközpontjából töltheti le.

Javasoljuk, hogy az SQLPackage segédprogram ot használja a méretezéshez és a teljesítményhez a legtöbb éles környezetben. További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).

Ez a példa bemutatja, hogyan exportálhat adatbázist az SqlPackage.exe használatával active directory univerzális hitelesítéssel:

```cmd
SqlPackage.exe /a:Export /tf:testExport.bacpac /scs:"Data Source=apptestserver.database.windows.net;Initial Catalog=MyDB;" /ua:True /tid:"apptest.onmicrosoft.com"
```

## <a name="export-to-a-bacpac-file-using-sql-server-management-studio-ssms"></a>Exportálás BACPAC-fájlba az SQL Server Management Studio (SSMS) használatával

Az SQL Server Management Studio legújabb verziói egy varázslót biztosít az Azure SQL-adatbázis BACPAC-fájlba történő exportálásához. Lásd: [Adatszintű alkalmazás exportálása](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application).

## <a name="export-to-a-bacpac-file-using-powershell"></a>Exportálás BACPAC-fájlba powershell használatával

> [!NOTE]
> [Egy felügyelt példány](sql-database-managed-instance.md) jelenleg nem támogatja az adatbázis exportálását EGY BACPAC-fájlba az Azure PowerShell használatával. Felügyelt példány BACPAC-fájlba történő exportálásához használja az SQL Server Management Studio vagy az SQLPackage programot.

A [New-AzSqlDatabaseExport](/powershell/module/az.sql/new-azsqldatabaseexport) parancsmag használatával elküldheti az exportálási adatbázis-kérelmet az Azure SQL Database szolgáltatásnak. Az adatbázis méretétől függően az exportálási művelet befejezése eltarthat egy ideig.

```powershell
$exportRequest = New-AzSqlDatabaseExport -ResourceGroupName $ResourceGroupName -ServerName $ServerName `
  -DatabaseName $DatabaseName -StorageKeytype $StorageKeytype -StorageKey $StorageKey -StorageUri $BacpacUri `
  -AdministratorLogin $creds.UserName -AdministratorLoginPassword $creds.Password
```

Az exportálási kérelem állapotának ellenőrzéséhez használja a [Get-AzSqlDatabaseImportExportStatus](/powershell/module/az.sql/get-azsqldatabaseimportexportstatus) parancsmagot. Ha ezt közvetlenül a kérés után futtatja, az általában **az Állapot: InProgress**értéket adja vissza. Ha megjelenik **az Állapot: Sikeres,** az exportálás befejeződött.

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

- Ha többet szeretne tudni az egyetlen adatbázisok és a készletbe helyezett adatbázisok hosszú távú biztonsági mentéséről az archiválási célú exportálás alternatívájaként, olvassa el a [Hosszú távú biztonsági mentés megőrzése](sql-database-long-term-retention.md)című témakört. Az SQL Agent-feladatok segítségével [ütemezheti a csak másolási adatbázis biztonsági mentéseit](https://docs.microsoft.com/sql/relational-databases/backup-restore/copy-only-backups-sql-server) a hosszú távú biztonsági mentés megőrzésének alternatívájaként.
- További információ a BACPAC-fájlokkal végzett migrálásról az SQL Server ügyféltanácsadói csapat blogján: [Migrálás SQL Serverről az Azure SQL Database-re BACPAC-fájlokkal](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/).
- A BACPAC SQL Server adatbázisba történő importálásáról a [BACPAC importálása SQL Server adatbázisba](https://msdn.microsoft.com/library/hh710052.aspx)című témakörben olvashat.
- A BACPAC SQL Server-adatbázisból történő exportálásáról az [Adatréteg-alkalmazás exportálása](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application) című témakörben olvashat.
- Ha többet szeretne tudni arról, hogy miként lehet az adatáttelepítési szolgáltatással áttelepíteni egy adatbázist, olvassa el az [SQL Server áttelepítése az Azure SQL Database-be kapcsolat nélküli módban a DMS használatával](../dms/tutorial-sql-server-to-azure-sql.md)című témakört.
- Ha az SQL Server kiszolgálóról az Azure SQL Database-be való áttelepítés előjátékaként exportál, olvassa el az [SQL Server-adatbázis áttelepítése az Azure SQL Database rendszerbe című témakört.](sql-database-single-database-migrate.md)
- A tárolási kulcsok és a megosztott hozzáférési aláírások biztonságos kezeléséről és megosztásáról az [Azure Storage biztonsági útmutatójában olvashat.](https://docs.microsoft.com/azure/storage/common/storage-security-guide)
