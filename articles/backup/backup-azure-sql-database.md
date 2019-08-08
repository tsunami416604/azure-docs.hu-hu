---
title: SQL Server adatbázisok biztonsági mentése az Azure-ba
description: Ez az oktatóanyag a SQL Server Azure-ba történő biztonsági mentését ismerteti. A cikk a SQL Server helyreállítást is ismerteti.
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: dacurwin
ms.openlocfilehash: 7312821320084c766f5b3357fe64c061df83673b
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68827648"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Információk az Azure-beli virtuális gépeken futó SQL Server Backupról

SQL Server adatbázisok olyan kritikus fontosságú munkaterhelések, amelyek alacsony helyreállítási időcélkitűzést (RPO) és hosszú távú adatmegőrzést igényelnek. Azure-beli virtuális gépeken futó SQL Server-adatbázisok biztonsági mentését [Azure Backup](backup-overview.md)használatával végezheti el.

## <a name="backup-process"></a>Biztonsági mentési folyamat

Ez a megoldás kihasználja az SQL natív API-kat az SQL-adatbázisok biztonsági mentésének elvégzéséhez.

* Miután megadta a védelemmel ellátni kívánt SQL Server VMt, és lekérdezi a benne lévő adatbázisokat, Azure Backup szolgáltatás a számítási feladatok biztonsági mentési bővítményét a virtuális `AzureBackupWindowsWorkload`gépen telepíti a  fájlnévkiterjesztés alapján.
* Ez a bővítmény egy koordinátorból és egy SQL-beépülő modulból áll. Míg a koordinátor felelős a munkafolyamatok különböző műveletekhez, például a biztonsági mentés konfigurálásához, a biztonsági mentéshez és a visszaállításhoz, a beépülő modul felelős a tényleges adatforgalomért.
* Ahhoz, hogy fel tudja deríteni a virtuális gép adatbázisait, Azure Backup létrehozza `NT SERVICE\AzureWLBackupPluginSvc`a fiókot. Ez a fiók használható a biztonsági mentéshez és a visszaállításhoz, és SQL sysadmin engedélyekre van szükség. Azure Backup kihasználja `NT AUTHORITY\SYSTEM`az adatbázis-felderítési/-lekérdezési fiókot, ezért ennek a fióknak nyilvános bejelentkezéssel kell rendelkeznie az SQL-ben. Ha nem hozta létre a SQL Server VM az Azure piactéren, hibaüzenetet kaphat a **UserErrorSQLNoSysadminMembership**. Ha ez történik, [kövesse ezeket az utasításokat](backup-azure-sql-database.md).
* Miután elindította a védelem konfigurálását a kiválasztott adatbázisokon, a Backup szolgáltatás beállítja a koordinátort a biztonsági mentési ütemtervekkel és egyéb házirend-adatokkal, amelyeket a bővítmény a virtuális gépen helyileg gyorsítótáraz.
* Az ütemezett időpontban a koordinátor kommunikál a beépülő modullal, és elindítja a biztonsági mentési adatok továbbítását az SQL Serverről a VDI használatával.  
* A beépülő modul közvetlenül a Recovery Services-tárolóba küldi az adatokat, így nincs szükség átmeneti helyre. Az adattitkosítás és a Azure Backup szolgáltatás tárolja a Storage-fiókokban.
* Az adatátvitel befejezésekor a koordinátor ellenőrzi a véglegesítést a biztonsági mentési szolgáltatással.

  ![SQL biztonsági mentési architektúra](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Előkészületek

Mielőtt elkezdené, ellenőrizze az alábbiakat:

1. Győződjön meg arról, hogy rendelkezik az Azure-ban futó SQL Server-példánnyal. [Gyorsan létrehozhat egy SQL Server példányt](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) a piactéren.
2. Tekintse át a [szolgáltatás megfontolási](#feature-consideration-and-limitations) és [forgatókönyv](#scenario-support)-támogatását.
3. Tekintse át a forgatókönyvre vonatkozó [gyakori kérdéseket](faq-backup-sql-server.md) .

## <a name="scenario-support"></a>Forgatókönyvek támogatása

**Támogatás** | **Részletek**
--- | ---
**Támogatott központi telepítések** | Az SQL Marketplace Azure-beli virtuális gépek és a nem piactér (SQL Server manuálisan telepített) virtuális gépek támogatottak.
**Támogatott térségek** | Délkelet-Ausztrália, Kelet-Ausztrália (AE) <br> Dél-Brazília (BRS)<br> Közép-Kanada (CNC), Kelet-Kanada (CE)<br> Dél-Kelet-Ázsia (tenger), Kelet-Ázsia (EA) <br> USA keleti régiója (EUS), USA 2. keleti régiója (EUS2), USA nyugati középső régiója (WCUS), USA nyugati régiója (WUS); USA 2. nyugati régiója (WUS 2), USA északi középső régiója (NCUS), USA középső régiója (ke), USA déli középső régiója (SCUS) <br> India Central (INC), Dél-India (INS) <br> Kelet-Japán (JPE), Nyugat-Japán (JPW) <br> Korea középső régiója (KRC), Dél-Korea (KRS) <br> Észak-Európa (NE), Nyugat-Európa <br> Egyesült Királyság déli régiója (UKS), Egyesült Királyság nyugati régiója (UKW) <br> US Gov Arizona, US Gov Virginia, US Gov Texas, US DoD – középső régió, US DoD – keleti régió
**Támogatott operációs rendszerek** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012<br/><br/> A Linux jelenleg nem támogatott.
**Támogatott SQL Server verziók** | SQL Server 2017-as részletes leírást [itt](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 és az SPS részletesen [itt](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**Támogatott .NET-verziók** | A .NET-keretrendszer 4.5.2-es és újabb verziójának telepítése a virtuális gépen

### <a name="support-for-sql-server-2008-and-sql-server-2008-r2"></a>A SQL Server 2008 és SQL Server 2008 R2 támogatása

Azure Backup nemrég bejelentette a következőt: [EOS SQL Servers](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-2008-eos-extend-support) -SQL Server 2008 és SQL Server 2008 R2. A megoldás jelenleg előzetes verzióban érhető el az EOS SQL Serverban, és a következő konfigurációt támogatja:

1. SQL Server 2008 és SQL Server 2008 R2 fut Windows 2008 R2 SP1 rendszeren
2. A .NET-keretrendszer 4.5.2-es vagy újabb verzióját a virtuális gépre kell telepíteni
3. Az és a tükrözött adatbázisok biztonsági mentése nem támogatott

A szolgáltatás a felhasználók számára nem számít fel díjat az általánosan elérhető időpontig. Az összes többi szolgáltatásra vonatkozó [szempont és korlátozás](#feature-consideration-and-limitations) ezekre a verzióra is érvényes. Kérjük, tekintse [](backup-sql-server-database-azure-vms.md#prerequisites) át az előfeltételeket, mielőtt konfigurálja a védelmet a 2008-es és 2008 R2-es SQL-kiszolgálókon, amelyek tartalmazzák a [beállításkulcs](backup-sql-server-database-azure-vms.md#add-registry-key-to-enable-registration) beállítását (ez a lépés nem szükséges, ha a szolgáltatás általánosan elérhető).


## <a name="feature-consideration-and-limitations"></a>Funkciókkal kapcsolatos szempontok és korlátozások

- SQL Server biztonsági mentés konfigurálható a Azure Portal vagy a **PowerShellben**. Nem támogatjuk a CLI-t.
- A megoldás mindkét típusú [üzembe helyezést](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) támogatja – Azure Resource Manager virtuális gépeket és klasszikus virtuális gépeket.
- A SQL Server rendszert futtató virtuális gépek internetkapcsolatot igényelnek az Azure nyilvános IP-címeinek eléréséhez.
- SQL Server **feladatátvevő fürt példánya (a (z)** és a (z) SQL Server always on feladatátvevő fürt példánya nem támogatott.
- A tükrözési adatbázisok és az adatbázis-Pillanatképek biztonsági mentésére és visszaállítására vonatkozó műveletek nem támogatottak.
- Ha több biztonsági mentési megoldást használ a különálló SQL Server példány vagy az SQL always on rendelkezésre állási csoport biztonsági mentéséhez, a biztonsági mentés sikertelen lehet; ne tegye meg.
- A rendelkezésre állási csoport két csomópontjának különálló, azonos vagy eltérő megoldásokkal történő biztonsági mentése a biztonsági mentés sikertelenségét is eredményezheti.
- A Azure Backup csak a teljes és csak a csak **olvasható** adatbázisok teljes biztonsági mentési típusait támogatja
- A nagy mennyiségű fájllal rendelkező adatbázisok védelme nem biztosítható. A támogatott fájlok maximális száma **~ 1000**.  
- Egy tárolóban akár **2000** SQL Server adatbázisra is készíthet biztonsági mentést. Több tárolót is létrehozhat abban az esetben, ha nagyobb számú adatbázisra van szüksége.
- A biztonsági mentést akár **50** adatbázisra is konfigurálhatja egy menet közben. Ez a korlátozás segít optimalizálni a biztonsági mentési terheléseket.
- Az adatbázisok akár **2TB** méretekben is támogatottak; ennél nagyobb méretek esetén a teljesítménnyel kapcsolatos problémák merülhetnek fel.
- Ahhoz, hogy a kiszolgálókon hány adatbázis védhető, meg kell fontolni az olyan tényezőket, mint a sávszélesség, a virtuális gépek mérete, a biztonsági mentés gyakorisága, az adatbázis mérete stb. [Töltse le](http://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) az erőforrás-tervezőt, amely a virtuálisgép-erőforrások és a biztonsági mentési szabályzat alapján a kiszolgálónként elérhető adatbázisok hozzávetőleges számát adja meg.
- Rendelkezésre állási csoportok esetén a biztonsági mentések a különböző csomópontokból származnak, néhány tényező alapján. A rendelkezésre állási csoport biztonsági mentési viselkedését az alábbiakban összegzi.

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Az Always On rendelkezésre állási csoportok esetén a működés biztonsági mentése

Azt javasoljuk, hogy a biztonsági mentés az AG egyetlen csomópontján legyen konfigurálva. A biztonsági mentést mindig ugyanabban a régióban kell konfigurálni, mint az elsődleges csomópontot. Más szóval mindig szüksége lesz az elsődleges csomópontra abban a régióban, ahol a biztonsági mentést konfigurálja. Ha az AG összes csomópontja ugyanabban a régióban van, ahol a biztonsági mentés be van állítva, akkor nincs probléma.

**Régiók közötti AG**
- A biztonsági mentéstől függetlenül a biztonsági mentések nem azon a csomópontokon történnek, amelyek nem ugyanabban a régióban találhatók, mint ahol a biztonsági mentés be van állítva. Ennek az az oka, hogy a régiók közötti biztonsági másolatok nem támogatottak. Ha csak 2 csomóponttal rendelkezik, és a másodlagos csomópont a másik régióban található; Ebben az esetben a biztonsági mentések továbbra is az elsődleges csomópontról történnek (kivéve, ha a biztonsági mentési beállítások "másodlagosak").
- Ha egy feladatátvétel a biztonsági mentést tartalmazó régión kívül esik, a biztonsági mentések sikertelenek lesznek a feladatátvételi régió csomópontjain.

A biztonsági mentési beállítások és a biztonsági másolatok típusaitól függően (teljes/különbözeti/napló/csak másolás) a biztonsági másolatok egy adott csomópontból (elsődleges/másodlagos) származnak.

- **Biztonsági mentési beállítások: Elsődleges**

**Biztonsági mentés típusa** | **Node**
    --- | ---
    Teljes | Elsődleges
    Differenciál | Elsődleges
    napló |  Elsődleges
    Csak másolás – teljes |  Elsődleges

- **Biztonsági mentési beállítások: Csak másodlagos**

**Biztonsági mentés típusa** | **Node**
--- | ---
Teljes | Elsődleges
Differenciál | Elsődleges
napló |  Másodlagos
Csak másolás – teljes |  Másodlagos

- **Biztonsági mentési beállítások: Másodlagos**

**Biztonsági mentés típusa** | **Node**
--- | ---
Teljes | Elsődleges
Differenciál | Elsődleges
napló |  Másodlagos
Csak másolás – teljes |  Másodlagos

- **Nincs biztonsági mentési beállítás**

**Biztonsági mentés típusa** | **Node**
--- | ---
Teljes | Elsődleges
Differenciál | Elsődleges
napló |  Másodlagos
Csak másolás – teljes |  Másodlagos

## <a name="set-vm-permissions"></a>VM-engedélyek beállítása

  Ha a felderítést SQL Serveron futtatja, a Azure Backup a következő műveleteket végzi el:

* Hozzáadja a AzureBackupWindowsWorkload bővítményt.
* Egy NT SERVICE\AzureWLBackupPluginSvc-fiókot hoz létre a virtuális gépen lévő adatbázisok felderítéséhez. Ez a fiók biztonsági mentésre és helyreállításra szolgál, és SQL sysadmin engedélyeket igényel.
* Egy virtuális gépen futó adatbázisok felfedése, Azure Backup az NT AUTHORITY\SYSTEM fiókot használja. Ennek a fióknak nyilvános bejelentkezéssel kell rendelkeznie az SQL-ben.

Ha nem hozta létre a SQL Server VM az Azure piactéren, vagy ha SQL 2008-es és 2008 R2-es verzióval rendelkezik, előfordulhat, hogy **UserErrorSQLNoSysadminMembership** hibaüzenetet kap.

Ha a Windows 2008 R2 rendszeren futó **SQL 2008** és **2008 R2** esetén ad meg engedélyeket, tekintse meg a következőt:. [](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2)

Az összes többi verzió esetében javítsa ki az engedélyeket a következő lépésekkel:

  1. SQL Server sysadmin engedélyekkel rendelkező fiókkal jelentkezhet be SQL Server Management Studio (SSMS) szolgáltatásba. Ha speciális engedélyekre van szüksége, a Windows-hitelesítésnek működnie kell.
  2. A SQL Server nyissa meg a **Biztonság/bejelentkezések** mappát.

      ![A fiókok megjelenítéséhez nyissa meg a biztonság/bejelentkezések mappát](./media/backup-azure-sql-database/security-login-list.png)

  3. Kattintson a jobb gombbal a bejelentkezések mappára, és válassza az **új bejelentkezés**lehetőséget. A **Bejelentkezés – új**területen válassza a **Keresés**lehetőséget.

      ![A bejelentkezés – új párbeszédpanelen válassza a keresés lehetőséget.](./media/backup-azure-sql-database/new-login-search.png)

  4. A virtuális gép regisztrálása és az SQL-felderítési fázis során létrejött a Windows Virtual Service Account **NT SERVICE\AzureWLBackupPluginSvc** . Adja meg a fiók nevét az **adja meg a kijelölendő objektum nevét mezőben**látható módon. Válassza a Névellenőrzés lehetőséget a név feloldásához. Kattintson az **OK** gombra.

      ![Az ismeretlen szolgáltatásnév feloldásához válassza a Névellenőrzés lehetőséget](./media/backup-azure-sql-database/check-name.png)

  5. A **kiszolgálói szerepkörök**területen ellenőrizze, hogy ki van-e választva a **sysadmin (rendszergazda** ) szerepkör. Kattintson az **OK** gombra. A szükséges engedélyeknek már léteznie kell.

      ![Győződjön meg arról, hogy a sysadmin kiszolgálói szerepkör van kiválasztva](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Most társítsa az adatbázist a Recovery Services-tárolóhoz. A Azure Portal a **védett kiszolgálók** listában kattintson a jobb gombbal arra a kiszolgálóra, amely hibás állapotban van > az **adatbázisok**újbóli felderítése elemre.

      ![Ellenőrizze, hogy a kiszolgáló rendelkezik-e megfelelő engedélyekkel](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Az **értesítések** területén lévő előrehaladás ellenőrzéséhez. Ha a kiválasztott adatbázisok találhatók, megjelenik egy sikert jelző üzenet.

      ![Sikeres üzembe helyezési üzenet](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Ha a SQL Server a SQL Server több példánya is telepítve van, akkor minden SQL-példányhoz rendszergazdai jogosultságot kell hozzáadnia az **NT Service\AzureWLBackupPluginSvc** -fiókhoz.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>SQL sysadmin-engedélyek megadása az SQL 2008 és az SQL 2008 R2 rendszerhez

Adja hozzá az **NT AUTHORITY\SYSTEM** és **NT Service\AzureWLBackupPluginSvc** -bejelentkezéseket az SQL Server-példányhoz:

1. Lépjen a SQL Server példányra az Object Explorerben.
2. Lépjen a biztonság > bejelentkezések lehetőségre
3. Kattintson a jobb gombbal a bejelentkezések elemre, majd kattintson az *új bejelentkezés...* lehetőségre.

    ![Új bejelentkezés a SSMS használatával](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Nyissa meg az általános lapot, és adja meg a bejelentkezési név **NT AUTHORITY\SYSTEM** .

    ![a SSMS bejelentkezési neve](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Lépjen a *kiszolgálói szerepkörök* elemre, és válassza ki a *nyilvános* és a *sysadmin* szerepkört.

    ![szerepkörök kiválasztása a SSMS-ben](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Ugrás az *állapotra*. Engedély *megadása* az adatbázismotor és a bejelentkezés *engedélyezettként*való bekapcsolásához.

    ![Engedélyek megadása a SSMS-ben](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Kattintson az OK gombra.
8. Ismételje meg ugyanezeket a lépéseket (1-7 fent) a SQL Server-példányhoz tartozó NT Service\AzureWLBackupPluginSvc-bejelentkezés hozzáadásához. Ha a bejelentkezés már létezik, győződjön meg róla, hogy a rendszergazda kiszolgálói szerepkörrel rendelkezik, és az állapota területen a jogosultsággal rendelkezik az adatbázismotor és a bejelentkezés engedélyezettként való bekapcsolásához.
9. Az engedély megadását követően a portálon **újra fel kell deríteni az adatbázisok** : Tár biztonsági mentési **->** infrastruktúrájának számítási feladatai az Azure-beli virtuális gépen: **->**

    ![A Azure Portalban lévő adatbázisok újbóli felderítése](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Azt is megteheti, hogy a következő PowerShell-parancsok rendszergazdai módban történő futtatásával automatizálja az engedélyeket. A példány neve alapértelmezés szerint az MSSQLSERVER értékre van állítva. Módosítsa a példány neve argumentumot a parancsfájlban, ha szükséges:

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

* [](backup-sql-server-database-azure-vms.md) Tudnivalók SQL Server adatbázisok biztonsági mentéséről.
* [Tudnivalók a](restore-sql-database-azure-vm.md) SQL Server adatbázisok biztonsági másolatának visszaállításáról.
* [Tudnivalók a](manage-monitor-sql-database-backup.md) SQL Server adatbázisok biztonsági másolatának kezeléséről.
