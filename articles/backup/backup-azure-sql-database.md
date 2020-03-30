---
title: SQL Server-adatbázisok biztonsági mentése az Azure-ba
description: Ez a cikk bemutatja, hogyan lehet biztonsági másolatot az SQL Server az Azure-ba. A cikk az SQL Server helyreállítását is ismerteti.
ms.topic: conceptual
ms.date: 06/18/2019
ms.openlocfilehash: 537257733d7693598fd8007da6ce12c28fbeb02a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408760"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Információk az Azure-beli virtuális gépeken futó SQL Server Backupról

[Az Azure Backup](backup-overview.md) adatfolyam-alapú, speciális megoldást kínál az Azure virtuális gépeken futó SQL Server biztonsági mentéséhez. Ez a megoldás igazodik az Azure Backup nulla infrastruktúra biztonsági mentés, a hosszú távú megőrzés és a központi felügyelet előnyeihez. Emellett a következő előnyöket biztosítja kifejezetten az SQL Server számára:

1. Munkaterhelés-tudatos biztonsági mentések, amelyek támogatják az összes biztonsági mentési típust - teljes, különbözeti és napló
2. 15 perces RPO (helyreállítási pont célja) gyakori naplóbiztonsági mentésekkel
3. Időponthoz kötött helyreállítás egy másodpercig
4. Egyéni adatbázisszintű biztonsági mentés és visszaállítás

A ma támogatott biztonsági mentési és visszaállítási forgatókönyvek megtekintéséhez tekintse meg a [támogatási mátrixot.](sql-support-matrix.md#scenario-support)

## <a name="backup-process"></a>A biztonsági mentés folyamata

Ez a megoldás az SQL natív API-kat használja az SQL-adatbázisok biztonsági másolatainak készítéséhez.

* Miután megadta az SQL Server virtuális gép, amely védeni szeretné, és a lekérdezés az adatbázisok benne, az `AzureBackupWindowsWorkload` Azure Backup szolgáltatás telepíti a számítási feladatok biztonsági mentési bővítményt a virtuális gép a névbővítmény.
* Ez a kiterjesztés egy koordinátorból és egy SQL beépülő modulból áll. Míg a koordinátor felelős a különböző műveletek, például a biztonsági mentés konfigurálása, a biztonsági mentés és a visszaállítás munkafolyamatainak elindításáért, a beépülő modul felelős a tényleges adatfolyamért.
* Ahhoz, hogy ezen a virtuális gépen adatbázisokat `NT SERVICE\AzureWLBackupPluginSvc`deríthesse fel, az Azure Backup létrehozza a fiókot. Ez a fiók biztonsági mentésre és visszaállításra szolgál, és SQL sysadmin engedélyeket igényel. A `NT SERVICE\AzureWLBackupPluginSvc` fiók egy [virtuális szolgáltatás fiók](https://docs.microsoft.com/windows/security/identity-protection/access-control/service-accounts#virtual-accounts), ezért nem igényel jelszókezelést. Az Azure Backup `NT AUTHORITY\SYSTEM` a fiókot használja az adatbázis felderítésére/lekérdezésére, ezért ennek a fióknak nyilvános bejelentkezésnek kell lennie az SQL-en. Ha nem az Azure Marketplace-ről hozták létre az SQL Server virtuális gépét, hibaüzenet jelenhet meg **UserErrorSQLNoSysadminMembership néven.** Ebben az esetben [kövesse az alábbi utasításokat](#set-vm-permissions).
* Miután aktiválta a védelem konfigurálását a kiválasztott adatbázisokon, a biztonsági mentési szolgáltatás beállítja a koordinátort a biztonsági mentési ütemezésekkel és más házirend-részletekkel, amelyeket a bővítmény helyileg gyorsítótárazza a virtuális gépen.
* Az ütemezett időpontban a koordinátor kommunikál a beépülő modullal, és elindítja a biztonsági mentési adatok streamelését az SQL-kiszolgálóról a VDI használatával.  
* A bővítmény közvetlenül a helyreállítási szolgáltatások tárolóba küldi az adatokat, így nincs szükség átmeneti helyre. Az adatok titkosítása és tárolása az Azure Backup szolgáltatás tárfiókokban.
* Amikor az adatátvitel befejeződött, a koordinátor megerősíti a véglegesítést a biztonsági mentési szolgáltatással.

  ![SQL biztonsági mentési architektúra](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdené, ellenőrizze az alábbiakat:

1. Győződjön meg arról, hogy az Azure-ban fut egy SQL Server-példány. [Gyorsan létrehozhat egy SQL Server-példányt](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) a piactéren.
2. Tekintse át a [szolgáltatás szempontjait](sql-support-matrix.md#feature-consideration-and-limitations) és a [forgatókönyvek támogatását.](sql-support-matrix.md#scenario-support)
3. Tekintse át a forgatókönyvvel [kapcsolatos gyakori kérdéseket.](faq-backup-sql-server.md)

## <a name="set-vm-permissions"></a>Virtuálisgép-engedélyek beállítása

  Amikor felderítést futtat egy SQL Server kiszolgálón, az Azure Backup a következőket teszi:

* Hozzáadja az AzureBackupWindowsWorkload bővítményt.
* Létrehoz egy NT SERVICE\AzureWLBackupPluginSvc fiókot a virtuális gépen lévő adatbázisok felderítéséhez. Ez a fiók biztonsági mentésre és visszaállításra szolgál, és SQL sysadmin engedélyeket igényel.
* Felderíti a virtuális gépen futó adatbázisokat, az Azure Backup az NT AUTHORITY\SYSTEM fiókot használja. Ennek a fióknak nyilvános bejelentkezésnek kell lennie az SQL-en.

Ha nem az SQL Server virtuális gép az Azure Marketplace-en, vagy ha az SQL 2008 és 2008 R2, előfordulhat, hogy egy **UserErrorSQLNoSysadminMembership** hiba.

A Windows 2008 R2 rendszeren futó **SQL 2008** és **2008 R2** esetén az engedélyek megadásához [lásd itt.](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)

Az összes többi verzió esetében javítsa ki az engedélyeket a következő lépésekkel:

  1. Sql Server rendszergazdai engedélyekkel rendelkező fiókkal jelentkezzen be az SQL Server Management Studio (SSMS) szolgáltatásba. Ha nincs szüksége speciális engedélyekre, a Windows-hitelesítésnek működnie kell.
  2. Az SQL Server en nyissa meg a **Security/Logins mappát.**

      ![A fiókok megtekintéséhez nyissa meg a Biztonság/bejelentkezés mappát](./media/backup-azure-sql-database/security-login-list.png)

  3. Kattintson a jobb gombbal a **Bejelentkezések** mappára, és válassza **az Új bejelentkezés parancsot.** A **Bejelentkezés - Új**mezőben válassza a **Keresés**lehetőséget.

      ![A Bejelentkezés - Új párbeszédpanelen válassza a Keresés lehetőséget.](./media/backup-azure-sql-database/new-login-search.png)

  4. Az **NT SERVICE\AzureWLBackupPluginSvc** Windows virtuális szolgáltatásfiók a virtuális gép regisztrációs és SQL-felderítési fázisa során jött létre. Írja be a fiók nevét a **Válassza ki jelölendő objektum nevének megadása**című részben. A név feloldásához jelölje be a **Nevek ellenőrzése** jelölőnégyzetet. Kattintson az **OK** gombra.

      ![Az ismeretlen szolgáltatásnév feloldásához jelölje be a Nevek ellenőrzése jelölőnégyzetet.](./media/backup-azure-sql-database/check-name.png)

  5. A **Kiszolgálói szerepkörök**ben győződjön meg arról, hogy a **rendszergazdai** szerepkör ki van jelölve. Kattintson az **OK** gombra. A szükséges engedélyeknek most már létezniük kell.

      ![Annak biztosítása, hogy a rendszergazdai kiszolgálói szerepkör ki legyen jelölve](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Most társítsa az adatbázist a Recovery Services tárolóhoz. Az Azure Portalvédett **kiszolgálók** listájában kattintson a jobb gombbal a hibás állapotú kiszolgálóra, > **újra felfedezze a db-ket.**

      ![Annak ellenőrzése, hogy a kiszolgáló rendelkezik-e a megfelelő engedélyekkel](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Ellenőrizze az előrehaladást az **Értesítések** területen. A kijelölt adatbázisok megmentése után egy sikeres üzenet jelenik meg.

      ![A telepítés sikeressé tévő üzenete](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Ha az SQL Server több SQL Server-példányt telepített, akkor az **NT Service\AzureWLBackupPluginSvc** fiókrendszergazdai engedélyét kell hozzáadnia az összes SQL-példányhoz.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>SQL sysadmin engedélyek megadása sql 2008-hoz és SQL 2008 R2-hez

**NT AUTHORITY\SYSTEM** és **NT Service\AzureWLBackupPluginSvc** bejelentkezések hozzáadása az SQL Server-példányhoz:

1. Nyissa meg az SQL Server-példányt az Objektumkezelőben.
2. Keresse meg a Security -> Bejelentkezések
3. Kattintson a jobb gombbal a Bejelentkezések és kattintson *az új bejelentkezés ...*

    ![Új bejelentkezés SSMS használatával](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Lépjen az Általános lapra, és írja be az **NT AUTHORITY\SYSTEM nevet** bejelentkezési névként.

    ![bejelentkezési név az SSMS-hez](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Nyissa meg a *Kiszolgálói szerepkörök et,* és válassza *a nyilvános* és *rendszergazdai szerepköröket.*

    ![szerepkörök kiválasztása az SSMS-ben](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Ugrás az *Állapot gombra.* *Adja meg* az adatbázis-motorhoz való csatlakozásés a bejelentkezés *engedélyezve való csatlakozásengedélyezését.*

    ![Engedélyek megadása az SSMS-ben](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Kattintson az OK gombra.
8. Ismételje meg ugyanazokat a lépések sorozatát (1-7 felett) az NT Service\AzureWLBackupPluginSvc bejelentkezés hozzáadásához az SQL Server-példányhoz. Ha a bejelentkezés már létezik, győződjön meg arról, hogy rendelkezik a rendszergazdai kiszolgálói szerepkörrel, és az Állapot csoportban megadja az adatbázis-motorhoz való csatlakozás és a bejelentkezés engedélyezve való csatlakozásának engedélyezését.
9. Az engedély megadása után újra felfedezheti a **->** **db-ket** a portálon: Vault biztonsági mentési infrastruktúra **->** számítási feladata az Azure virtuális gépben:

    ![DB-k újbóli felderítése az Azure Portalon](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Azt is megteheti, hogy automatizálhatja az engedélyeket a következő PowerShell-parancsok futtatásával rendszergazdai módban. A példány neve alapértelmezés szerint MSSQLSERVER. Szükség esetén módosítsa a példány névargumentumát a parancsfájlban:

```powershell
param(
    [Parameter(Mandatory=$false)]
    [string] $InstanceName = "MSSQLSERVER"
)
if ($InstanceName -eq "MSSQLSERVER")
{
    $fullInstance = $env:COMPUTERNAME   # In case it is the default SQL Server Instance
}
else
{
    $fullInstance = $env:COMPUTERNAME + "\" + $InstanceName   # In case of named instance
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT Service\AzureWLBackupPluginSvc', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
try
{
    sqlcmd.exe -S $fullInstance -Q "sp_addsrvrolemember 'NT AUTHORITY\SYSTEM', 'sysadmin'" # Adds login with sysadmin permission if already not available
}
catch
{
    Write-Host "An error occurred:"
    Write-Host $_.Exception|format-list -force
}
```

## <a name="next-steps"></a>További lépések

* További információ az SQL Server-adatbázisok biztonsági [mentéséről.](backup-sql-server-database-azure-vms.md)
* [További információ az](restore-sql-database-azure-vm.md) SQL Server-adatbázisok biztonsági mentén történő visszaállításáról.
* További információ az SQL Server-adatbázisok biztonsági mentén történt [kezeléséről.](manage-monitor-sql-database-backup.md)
