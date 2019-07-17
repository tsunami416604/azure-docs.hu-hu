---
title: Az Azure SQL Server-adatbázisok biztonsági mentése |} A Microsoft Docs
description: Ez az oktatóanyag azt ismerteti, hogyan SQL Server biztonsági mentése az Azure-bA. A cikk azt is bemutatja, az SQL Server helyreállítása.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 06/18/2019
ms.author: raynew
ms.openlocfilehash: 2957e784540f7c6450235d26da43121db2458dd1
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68249530"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Információk az Azure-beli virtuális gépeken futó SQL Server Backupról

Az SQL Server-adatbázisok olyan alacsony a helyreállításipont-célkitűzés (RPO) és hosszú távú megőrzés igénylő kritikus fontosságú számítási feladatokhoz. Készíthet biztonsági mentést használata Azure virtuális gépeken futó SQL Server-adatbázisok [Azure Backup](backup-overview.md).

## <a name="backup-process"></a>Biztonsági mentési folyamat

Ez a megoldás az SQL natív API-k érvénybe az SQL-adatbázisok biztonsági másolatait használja.

* Az SQL Server virtuális gép, amelyeket szeretne védeni, és lekérdezéseket az adatbázisok, adja meg, ha Azure Backup szolgáltatás telepíti egy számítási feladatok biztonsági mentési bővítményt a virtuális gép neve `AzureBackupWindowsWorkload`  bővítmény.
* Ez a bővítmény koordinátor és a egy SQL-beépülő modul áll. Bár a koordinátor különféle műveletek, például konfigurálja a biztonsági mentés, biztonsági mentési és visszaállítási munkafolyamatok kiváltásáért felelős, a beépülő modul felelős tényleges adatfolyam.
* Az, hogy a virtuális gépen adatbázisok felderítéséhez, az Azure Backup hoz létre a fiók `NT SERVICE\AzureWLBackupPluginSvc`. Ez a fiók biztonsági mentési és visszaállítási használja, és az SQL-rendszergazdai engedélyekkel kell rendelkeznie. Az Azure Backup használja a `NT AUTHORITY\SYSTEM` adatbázis felderítési/lekérdezési, így ennek a fióknak kell lennie egy nyilvános bejelentkezés az SQL-fiók. Ha az SQL Server rendszerű virtuális gép az Azure Marketplace-ről nem hozott létre, akkor előfordulhat, hogy megjelenik egy hibaüzenet **UserErrorSQLNoSysadminMembership**. Ha ez történik [az alábbi lépéseket követve](backup-azure-sql-database.md).
* Eseményindító, konfigurálja a védelmet a kiválasztott adatbázisok, miután a biztonsági mentési szolgáltatás beállítja a koordinátor más házirend részletei, amelyek a bővítmény helyben gyorsítótárazza a virtuális gép és a biztonsági mentés ütemezése 
* Az ütemezett időpontban a koordinátor a beépülő modul kommunikál, és elindítja a streamelési VDI használata SQL-kiszolgálóról a biztonsági mentési adatok.  
* A beépülő modul elküldi az adatokat közvetlenül a recovery services-tároló, így kiküszöböli az előkészítési hely van szükség. Az adatok titkosítva és a storage-fiókok az Azure Backup szolgáltatás által tárolt.
* Ha az adatátvitel befejeződött, koordinátor megerősíti, hogy a biztonsági mentési szolgáltatásban a véglegesítés.

  ![SQL biztonsági mentési architektúra](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Előkészületek

A Kezdés előtt ellenőrizze az alábbi:

1. Győződjön meg arról, hogy rendelkezik Azure-ban futó SQL Server-példányt. Is [gyorsan létrehozhat egy SQL Server-példány](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) a Marketplace-en.
2. Tekintse át a [szempont funkció](#feature-consideration-and-limitations) és [forgatókönyv támogatási](#scenario-support).
3. [Tekintse át a gyakori kérdéseket](faq-backup-sql-server.md) erről a forgatókönyvről.

## <a name="scenario-support"></a>A forgatókönyv támogatása

**Támogatás** | **Részletek**
--- | ---
**Támogatott központi telepítések** | SQL piactér Azure-beli és nem Marketplace (manuálisan telepített SQL Server) virtuális gépek támogatottak.
**Támogatott helyeken** | Délkelet-Ausztrália (ASE), Kelet-Ausztrália (AE) <br> Dél-Brazília (BRS)<br> Közép-Kanada (CNC), kelet-Kanada (CE)<br> Délkelet-Ázsia (SEA), Kelet-Ázsia (EA) <br> USA keleti RÉGIÓJA (EUS), USA keleti RÉGIÓJA (EUS2), USA nyugati középső RÉGIÓJA (WCUS), USA nyugati RÉGIÓJA (WUS); 2 USA nyugati RÉGIÓJA (WUS 2) 2 USA északi középső RÉGIÓJA (NCUS) USA középső RÉGIÓJA (CUS) déli középső RÉGIÓJA (SCUS) <br> Közép-India (INC), Dél-India (INS) <br> Kelet-japán (JPE), Nyugat-japán (JPW) <br> Korea középső régiója (KRC), Korea déli régiója (KRS) <br> Észak-Európa (NE), Nyugat-Európa <br> Egyesült Királyság déli régiója (UKS), az Egyesült Királyság nyugati régiója (UKW)
**Támogatott operációs rendszerek** | A Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 rendszerben<br/><br/> Linux jelenleg nem támogatott.
**Támogatott SQL Server-verziók** | SQL Server 2017 leírtaknak megfelelően [Itt](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202017), SQL Server 2016 és a szervizcsomagok leírtaknak megfelelően [Itt](https://support.microsoft.com/lifecycle/search?alpha=SQL%20server%202016%20service%20pack), az SQL Server 2014, SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer, Express.
**Támogatott .NET-verziók** | .NET-keretrendszer 4.5.2-es verziója és az újabb verzióját a virtuális gépen

### <a name="support-for-sql-server-2008-and-sql-server-2008-r2"></a>Az SQL Server 2008 és az SQL Server 2008 R2 támogatása

Az Azure Backup nemrég jelentettük be támogatása [EOS SQL-adatbázisai](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-2008-eos-extend-support) – SQL Server 2008 és az SQL Server 2008 R2. A megoldás jelenleg előzetes verziójú EOS SQL Serverhez, és támogatja a következő konfigurációt:

1. Az SQL Server 2008 és a Windows 2008 R2 SP1 rendszert futtató SQL Server 2008 R2
2. .NET-keretrendszer 4.5.2-es vagy újabb kell telepítenie a virtuális gépen
3. Az FCI és a tükrözött adatbázisok biztonsági mentés nem támogatott

Felhasználók a szolgáltatás eddig: általánosan elérhető az idő nem kell fizetnie. Összes olyan [szempontok és korlátozások](#feature-consideration-and-limitations) , valamint ezen verziójára érvényesek. Könyvtárából tekintse meg a [Előfeltételek](backup-sql-server-database-azure-vms.md#prerequisites) SQL Server 2008 és 2008 R2, amely többek között a kiszolgálókon a védelem konfigurálása előtt a [beállításkulcs](backup-sql-server-database-azure-vms.md#add-registry-key-to-enable-registration) (Ez a lépés nem lenne szükség esetén a szolgáltatás általánosan elérhető).


## <a name="feature-consideration-and-limitations"></a>A szolgáltatás szempontok és korlátozások

- Az Azure Portalon konfigurálhatja az SQL Server biztonsági másolat vagy **PowerShell**. Nem támogatjuk a parancssori felület.
- A megoldás mindkét típusa esetén támogatott a [központi telepítések](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) – Azure Resource Manager virtuális gépek és a klasszikus virtuális gépeket.
- Az SQL Server rendszerű virtuális Gépet az Azure nyilvános IP-címek eléréséhez internetkapcsolatra van szükség.
- Az SQL Server **feladatátvételi fürtbeli példány (FCI)** és SQL Server-alapú folyamatos a Feladatátvevőfürt-példány használata nem támogatott.
- Készítsen biztonsági másolatot, és a tükrözött adatbázisok és az adatbázis-pillanatképek visszaállítási műveletek nem támogatottak.
- Egynél több biztonsági mentési megoldások biztonsági mentése az önálló SQL Server-példány vagy az SQL Always on rendelkezésre állási csoport vezethet, a biztonsági mentési hiba; így tartózkodik.
- Biztonsági másolatot egy rendelkezésre állási csoport külön-külön az azonos vagy eltérő megoldások két csomópontot, a biztonsági is vezethet.
- Csak teljes biztonsági mentés az Azure támogatja, és csak másolatot teljes biztonsági mentési típusai **csak olvasható** adatbázisok
- Adatbázisok nagy számú fájl esetén nem biztosítható. A fájlok maximális számát, amelyet támogat a **~ 1000**.  
- Akár biztonsági **nagyjából 2000** SQL Server-adatbázisok a tárolóban. Több tároló hozható létre, amennyiben rendelkezik a nagyobb adatbázisok száma.
- Konfigurálhatja a biztonsági mentés akár **50** adatbázisok egy nyissa meg; ez a korlátozás segít optimalizálni a biztonsági mentési terhelések.
- Akár támogatjuk az adatbázisok **2 TB-os** méretű; számára, hogy nagyobb méretű, a teljesítménnyel kapcsolatos problémák is kapja meg.
- Már ismeri, feltárhatja, hogy hány adatbázist kiszolgálónként lehet védeni, kell figyelembe venni a tényezőket, például sávszélesség, Virtuálisgép-méretet, biztonsági mentés gyakoriságát, adatbázis mérete, stb. [Töltse le](http://download.microsoft.com/download/A/B/5/AB5D86F0-DCB7-4DC3-9872-6155C96DE500/SQL%20Server%20in%20Azure%20VM%20Backup%20Scale%20Calculator.xlsx) az erőforrás plannert, amely lehetővé teszi az adatbázisok hozzávetőleges száma is rendelkezik kiszolgálónként alapján, a Virtuálisgép-erőforrások és a biztonsági mentési szabályzatot.
- Rendelkezésre állási csoportok esetén a biztonsági mentések néhány tényezők alapján különböző csomópontjából kerül. A rendelkezésre állási csoport biztonsági mentési viselkedés összefoglalása.

### <a name="back-up-behavior-in-case-of-always-on-availability-groups"></a>Biztonsági mentése esetén mindig viselkedés a rendelkezésre állási csoportok

Javasoljuk, hogy a biztonsági mentés konfigurálva van egy rendelkezésre állási csoport csak egyetlen csomópontján. Biztonsági mentés mindig az elsődleges csomópont ugyanabban a régióban kell konfigurálni. Más szóval mindig kell szerepelniük a régiót, amelyben a biztonsági mentés konfigurál az elsődleges csomópont. Ha a rendelkezésre állási csoport minden csomópontján ugyanabban a régióban, amelyben a biztonsági mentés van beállítva, nem minden szempont.

**Régiók közötti rendelkezésre állási csoporthoz**
- A biztonsági mentési preferenciáját, függetlenül a biztonsági mentések a csomópontot, amely nem ugyanabban a régióban, ahol a biztonsági mentés van beállítva, nem történik. Ennek az oka a régiók közötti biztonsági másolatok nem támogatottak. Ha csak 2 csomópontot, és a másodlagos csomópontra, a többi régióban; Ebben az esetben a biztonsági mentések továbbra is elsődleges csomópontról történik (kivéve, ha a biztonsági mentési preferenciáját "csak másodlagos").
- Ha feladatátvétel történik, mint a biztonsági mentés konfigurálva, amelyben egy másik régióba, a biztonsági mentés sikertelen lesz, a csomópontokon a feladatátvételi régióban.

Attól függően, a biztonsági mentési preferenciáját és a biztonsági mentések típusok (teljes és különbségi/log/csak másolatot teljes) a biztonsági mentések kerül egy adott csomópontról (elsődleges-másodlagos).

- **Biztonsági mentési preferenciáját: Elsődleges**

**Biztonsági mentés típusa** | **Node**
    --- | ---
    Korlátlan | Elsődleges
    Különbségi | Elsődleges
    napló |  Elsődleges
    Csak másolatot teljes |  Elsődleges

- **Biztonsági mentési preferenciáját: Csak másodlagos**

**Biztonsági mentés típusa** | **Node**
--- | ---
Korlátlan | Elsődleges
Különbségi | Elsődleges
napló |  Másodlagos
Csak másolatot teljes |  Másodlagos

- **Biztonsági mentési preferenciáját: Másodlagos**

**Biztonsági mentés típusa** | **Node**
--- | ---
Korlátlan | Elsődleges
Különbségi | Elsődleges
napló |  Másodlagos
Csak másolatot teljes |  Másodlagos

- **Nincs biztonsági mentés preferencia**

**Biztonsági mentés típusa** | **Node**
--- | ---
Korlátlan | Elsődleges
Különbségi | Elsődleges
napló |  Másodlagos
Csak másolatot teljes |  Másodlagos

## <a name="set-vm-permissions"></a>Virtuális gép engedélyeinek beállítása

  Amikor felderítési SQL Server-kiszolgálón futtatja, az Azure Backup a következőket teszi:

* Hozzáadja a AzureBackupWindowsWorkload bővítményt.
* Létrehoz egy NT SERVICE\AzureWLBackupPluginSvc fiókot a virtuális gépen adatbázisok felderítéséhez. Ez a fiók használatos biztonsági és visszaállítása, és SQL-rendszergazdai engedélyekkel kell rendelkeznie.
* Felderíti az adatbázisok egy virtuális gépen futó Azure Backup az NT AUTHORITY\SYSTEM fiókot használja. Ennek a fióknak kell lennie egy nyilvános jelentkezzen be az SQL.

Ha Ön hozta létre az SQL Server rendszerű virtuális gép az Azure Marketplace-en, vagy ha SQL 2008 és 2008 R2, kaphat egy **UserErrorSQLNoSysadminMembership** hiba.

Az engedélyek ajándékozás **SQL 2008** és **2008 R2** Windows 2008 R2 rendszeren futó, tekintse meg [Itt](#give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2).

Minden egyéb verziói esetén javítsa ki az engedélyeket az alábbi lépéseket követve:

  1. Az SQL Server SysAdmin (rendszergazda) engedélyekkel rendelkező fiók használatával jelentkezzen be az SQL Server Management Studio (SSMS). Ha nincs szükség speciális engedélyek, a Windows-hitelesítés működnie kell.
  2. Nyissa meg az SQL Server, a **biztonsági/bejelentkezések** mappát.

      ![Nyissa meg a biztonsági/bejelentkezések mappában a fiókok](./media/backup-azure-sql-database/security-login-list.png)

  3. Kattintson a jobb gombbal a **bejelentkezések** mappára, és válassza **új bejelentkezés**. A **bejelentkezés – új**válassza **keresési**.

      ![A bejelentkezés – új párbeszédpanel, válassza ki a keresés](./media/backup-azure-sql-database/new-login-search.png)

  4. A virtuális Windows-szolgáltatásfiók **NT SERVICE\AzureWLBackupPluginSvc** a virtuális gép regisztrációja és SQL felderítési fázis során jött létre. Adja meg a fiók nevét, ahogyan az **írja be a kijelölendő objektum nevét**. Válassza ki **Névellenőrzés** oldani a nevet. Kattintson az **OK** gombra.

      ![Válassza ki a szolgáltatás ismeretlen név Névellenőrzés](./media/backup-azure-sql-database/check-name.png)

  5. A **kiszolgálói szerepkörök**, győződjön meg arról, hogy a **SysAdmin (rendszergazda)** szerepkör van kiválasztva. Kattintson az **OK** gombra. A szükséges engedélyekkel most már léteznie kell.

      ![Győződjön meg arról, hogy a sysadmin (rendszergazda) kiszolgálói szerepkör van kiválasztva](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Az adatbázis mostantól társíthat a Recovery Services-tároló. Az Azure Portalon az a **védett kiszolgálók** listában kattintson a jobb gombbal a kiszolgáló, amely hibás állapotú > **adatbázisok újbóli felderítése**.

      ![Ellenőrizze a kiszolgáló rendelkezik-e megfelelő engedélyekkel](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Folyamat ellenőrzéséhez a **értesítések** területen. A kijelölt adatbázisok találhatók, a sikert jelző üzenet jelenik meg.

      ![Üzembe helyezés a sikert jelző üzenet](./media/backup-azure-sql-database/notifications-db-discovered.png)

> [!NOTE]
> Ha az SQL Server telepített SQL Server több példánnyal rendelkezik, akkor hozzá kell adnia a sysadmin (rendszergazda) engedély **NT Service\AzureWLBackupPluginSvc** fiók az összes SQL-példányra.

### <a name="give-sql-sysadmin-permissions-for-sql-2008-and-sql-2008-r2"></a>SQL 2008 és az SQL 2008 R2 SQL rendszergazdai jogosultságok adása

Adjon hozzá **NT AUTHORITY\SYSTEM** és **NT Service\AzureWLBackupPluginSvc** bejelentkezések, az SQL Server-példány:

1. Nyissa meg az SQL Server-példány az Object explorer.
2. Keresse meg a Security -> bejelentkezések
3. A jobb gombbal a bejelentkezések a, és kattintson a *új bejelentkezés...*

    ![Új bejelentkezés az SSMS használatával](media/backup-azure-sql-database/sql-2k8-new-login-ssms.png)

4. Nyissa meg az Általános lapot, és adja meg **NT AUTHORITY\SYSTEM** bejelentkezési nevet.

    ![bejelentkezési név az ssms használatával](media/backup-azure-sql-database/sql-2k8-nt-authority-ssms.png)

5. Lépjen a *kiszolgálói szerepkörök* válassza *nyilvános* és *SysAdmin (rendszergazda)* szerepköröket.

    ![szerepkör kiválasztása az ssms-ben](media/backup-azure-sql-database/sql-2k8-server-roles-ssms.png)

6. Lépjen a *állapot*. *Engedélyezés* kapcsolódjanak az adatbázismotor, és jelentkezzen be az engedély *engedélyezve*.

    ![Engedélyek megadása az ssms-ben](media/backup-azure-sql-database/sql-2k8-grant-permission-ssms.png)

7. Kattintson az OK gombra.
8. Ismételje meg a lépést (1-7 fent) ugyanabban a sorrendben NT Service\AzureWLBackupPluginSvc bejelentkezési hozzáadása az SQL Server-példányt. Ha a bejelentkezés már létezik, győződjön meg arról, azt a sysadmin (rendszergazda) kiszolgálói szerepkörrel rendelkezik, és az állapot Grant engedélyt az adatbázismotorhoz való csatlakozáshoz, és jelentkezzen be az engedélyezve van.
9. Engedély megadása után **adatbázisok újbóli felderítése** a portálon: Tároló **->** biztonsági mentési infrastruktúra **->** számítási feladatok Azure-beli virtuális gépen:

    ![Az Azure portal adatbázisok újbóli felderítése](media/backup-azure-sql-database/sql-rediscover-dbs.png)

Azt is megteheti automatizálhatja a engedélyeket ad adminisztrátori módban a következő PowerShell-parancsok futtatásával. A példány neve alapértelmezés szerint MSSQLSERVER van beállítva. A példány nevét a argumentum a parancsfájlt, ha változás kell lennie:

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

* [Ismerje meg](backup-sql-server-database-azure-vms.md) SQL Server-adatbázisok biztonsági mentése.
* [Ismerje meg](restore-sql-database-azure-vm.md) biztonsági másolat az SQL Server-adatbázisok visszaállítása.
* [Ismerje meg](manage-monitor-sql-database-backup.md) kezelése biztonsági mentés az SQL Server-adatbázisok.
